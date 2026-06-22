# EAV Schema — MySQL 8.0 Implementation

**Clinical Documentation Module — Ingestion and Domain Modeling Layer**
*MySQL 8.0 port of the PostgreSQL schema (May 2026)*
*Minimum MySQL version: 8.0.16 (required for enforced CHECK constraints)*

---

## MySQL-Specific Design Decisions

Before the DDL, these are the explicit decisions made for the MySQL port and the reasoning behind each.

**Primary keys — `BINARY(16)` with swap-flag UUIDs.** `UUID_TO_BIN(UUID(), 1)` stores UUIDs in a byte order that groups time-related UUIDs together in the B-tree index, reducing page splits on sequential inserts. Read them back with `BIN_TO_UUID(col, 1)`. All application code should use these helper functions; do not insert raw `UUID()` strings into `BINARY(16)` columns.

**Timestamps — `DATETIME(6)` everywhere, UTC enforced at application layer.** MySQL `TIMESTAMP` has a 2038 horizon and auto-converts to the session timezone — both are unsuitable for a clinical record system. `DATETIME(6)` stores the value as given with microsecond precision and no conversion. The application must always write UTC. Set `time_zone = '+00:00'` on every connection to enforce this, or configure it as the global default.

**`dv_class` — inline `ENUM` on `attribute_catalog`.** MySQL has no `CREATE TYPE`. An `ENUM` column is the closest equivalent and enforces valid values at the storage engine level. The set of DV classes is stable enough that `ENUM` is appropriate here.

**`JSON` column for `canonical_json`.** MySQL 8.0's `JSON` type stores a binary DOM (similar to PostgreSQL `JSONB`). `JSON_EXTRACT()` / `->` / `->>` operators work for path extraction. Specific paths that are frequently queried are indexed via generated stored columns. Full-document GIN-style indexing is not available — ad-hoc JSON path queries on unprojected fields will require full-table scans unless a generated column index exists.

**Partitioning vs. foreign keys.** MySQL's InnoDB storage engine does not support foreign keys on partitioned tables. The resolution per table:
- Metadata tables (`form_version`, `attribute_catalog`, `concept_definition`, `concept_attribute_map`): **no partitioning, FK constraints active.** These tables are small and change infrequently.
- `clinical_document`: **no partitioning, FK active** on `form_version_id`. Document volume is large but secondary to EAV volume; the `patient_id` + date indexes provide sufficient query performance. Partition if row count exceeds ~50M.
- EAV fact tables (`obs_quantity`, `obs_coded`, `obs_ordinal`, `obs_text`, `obs_datetime`, `obs_boolean`, `obs_proportion`, `concept_instance`): **partitioned by `obs_date` (RANGE on `TO_DAYS()`), FK constraints dropped.** These tables will accumulate the vast majority of rows. Referential integrity to `clinical_document` and `attribute_catalog` is enforced at the application/projection engine layer.

**`MATERIALIZED VIEW` — does not exist in MySQL.** Replaced by a regular table `projection_manifest_cache` with a stored procedure `refresh_projection_manifest()` that truncates and repopulates it. Call this procedure on any schema change or as a scheduled event.

**`RETURNS TABLE` functions — not supported in MySQL.** The point-in-time `as_of` pattern is implemented as a view with a `WHERE` clause parameterized by session variables, or as a stored procedure that writes to a temporary table. Both patterns are provided.

**Stored procedures — `plpgsql` syntax replaced with MySQL `BEGIN...END` procedure syntax.**

**`json_path_scope` as concept instance discriminator.** Forms contain two structural patterns that produce multiple concept instances within a single document: repeating array groups (multiple BP readings, multiple medication entries) and parallel named sections (left eye / right eye, pre-op / post-op). The original `UNIQUE KEY uq_ci_doc_concept (document_id, concept_id, obs_date)` assumes one concept occurrence per document, which is wrong for both patterns.

`concept_instance` now carries a `json_path_scope` column — the JSONPath to the root of the repeating group or named section. Uniqueness is `(document_id, concept_id, json_path_scope, obs_date)`. Two medication entries in the same document get two `concept_instance` rows because `$.medications[0]` ≠ `$.medications[1]`. Left and right eye assessments get two rows because `$.eye_exam.left` ≠ `$.eye_exam.right`.

Individual EAV rows retain `source_json_path` — the full leaf path to the specific field (e.g. `$.medications[0].dose`). The relationship is: `json_path_scope` on `concept_instance` is the group root; `source_json_path` on each EAV row is the leaf within that group. Together they give both grouping identity and precise field lineage. The `attribute_catalog.json_path` column stores the *template* path (e.g. `$.medications[*].dose`) — the wildcard or index-free pattern the projection engine uses to locate fields. At projection time the engine resolves the template to actual indexed paths and stores the resolved path in `source_json_path`.

---

## 1. Utility: UUID Helper Reminder

```sql
-- Insert a new BINARY(16) primary key:
--   UUID_TO_BIN(UUID(), 1)
--
-- Read it back as a string:
--   BIN_TO_UUID(col, 1)
--
-- The swap flag (1) reorders bytes for B-tree locality.
-- Always use both functions consistently. Mixing swapped and
-- un-swapped UUIDs in the same column produces silently wrong joins.
```

---

## 2. Metadata Foundation

These tables keep FK constraints and are not partitioned.

### 2.1 `form_version`

```sql
CREATE TABLE form_version (
    form_version_id     BINARY(16)      NOT NULL DEFAULT (UUID_TO_BIN(UUID(), 1)),
    form_code           VARCHAR(100)    NOT NULL        COMMENT 'Stable identifier, e.g. VITALS, SOAP_NOTE',
    version_number      INT UNSIGNED    NOT NULL,
    semver              VARCHAR(20)     NOT NULL        COMMENT 'e.g. 2.1.0',
    json_schema_hash    CHAR(64)        NOT NULL        COMMENT 'SHA-256 of the canonical JSON schema file',
    status              ENUM('DRAFT','ACTIVE','DEPRECATED')
                                        NOT NULL DEFAULT 'DRAFT',
    effective_from      DATETIME(6)     NOT NULL        COMMENT 'UTC',
    effective_to        DATETIME(6)     NULL            COMMENT 'NULL = currently active. UTC.',
    created_at          DATETIME(6)     NOT NULL DEFAULT NOW(6) COMMENT 'UTC',
    created_by          BINARY(16)      NOT NULL        COMMENT 'user_id ref — enforced at app layer',

    PRIMARY KEY (form_version_id),
    UNIQUE KEY uq_form_version (form_code, version_number)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci
  COMMENT='All datetime columns store UTC. Use DATETIME(6) throughout; never TIMESTAMP.';
```

---

### 2.2 `attribute_catalog`

```sql
CREATE TABLE attribute_catalog (
    attribute_id        BINARY(16)      NOT NULL DEFAULT (UUID_TO_BIN(UUID(), 1)),
    form_version_id     BINARY(16)      NOT NULL,
    attribute_code      VARCHAR(150)    NOT NULL        COMMENT 'Stable code, e.g. SYSTOLIC_BP',
    attribute_label     VARCHAR(255)    NOT NULL,
    json_path           VARCHAR(500)    NOT NULL        COMMENT 'Template JSONPath, e.g. $.medications[*].dose. Projection engine resolves to actual indexed paths at runtime.',
    dv_class            ENUM(
                            'DV_QUANTITY',
                            'DV_CODED_TEXT',
                            'DV_TEXT',
                            'DV_DATE_TIME',
                            'DV_BOOLEAN',
                            'DV_ORDINAL',
                            'DV_PROPORTION',
                            'DV_IDENTIFIER',
                            'DV_DURATION'
                        )               NOT NULL,
    unit_of_measure     VARCHAR(50)     NULL            COMMENT 'UCUM unit code. DV_QUANTITY only.',
    terminology_system  VARCHAR(100)    NULL            COMMENT 'e.g. SNOMED-CT, ICD-10, LOINC',
    loinc_code          VARCHAR(20)     NULL,
    is_mandatory        TINYINT(1)      NOT NULL DEFAULT 0,
    is_projected        TINYINT(1)      NOT NULL DEFAULT 1
                                                        COMMENT '0 = stored in JSON only; not projected to EAV',
    projection_rationale TEXT           NULL            COMMENT 'Why this field is in the projection',
    effective_from      DATE            NOT NULL,
    effective_to        DATE            NULL,
    created_at          DATETIME(6)     NOT NULL DEFAULT NOW(6),
    created_by          BINARY(16)      NOT NULL,

    PRIMARY KEY (attribute_id),
    UNIQUE KEY uq_attr_code (form_version_id, attribute_code),
    KEY idx_attr_form_version (form_version_id),
    KEY idx_attr_dv_class (dv_class),

    CONSTRAINT fk_attr_form_version
        FOREIGN KEY (form_version_id) REFERENCES form_version (form_version_id)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;
```

---

### 2.3 `concept_definition`

```sql
CREATE TABLE concept_definition (
    concept_id          BINARY(16)      NOT NULL DEFAULT (UUID_TO_BIN(UUID(), 1)),
    concept_code        VARCHAR(100)    NOT NULL        COMMENT 'e.g. BLOOD_PRESSURE, GCS',
    concept_label       VARCHAR(255)    NOT NULL,
    concept_version     INT UNSIGNED    NOT NULL DEFAULT 1,
    archetype_ref       VARCHAR(200)    NULL            COMMENT 'e.g. openEHR-EHR-OBSERVATION.blood_pressure.v2',
    clinical_domain     VARCHAR(100)    NULL            COMMENT 'e.g. CARDIOLOGY, GENERAL',
    description         TEXT            NULL,
    is_extractable      TINYINT(1)      NOT NULL DEFAULT 1
                                                        COMMENT '1 = eligible for ML feature engineering',
    effective_from      DATE            NOT NULL,
    effective_to        DATE            NULL,
    created_at          DATETIME(6)     NOT NULL DEFAULT NOW(6),

    PRIMARY KEY (concept_id),
    UNIQUE KEY uq_concept_version (concept_code, concept_version)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;
```

---

### 2.4 `concept_attribute_map`

```sql
CREATE TABLE concept_attribute_map (
    map_id                  BINARY(16)  NOT NULL DEFAULT (UUID_TO_BIN(UUID(), 1)),
    concept_id              BINARY(16)  NOT NULL,
    attribute_id            BINARY(16)  NOT NULL,
    is_anchor_attribute     TINYINT(1)  NOT NULL DEFAULT 0
                                                    COMMENT 'Exactly 1 per concept. Signals concept was captured.',
    is_required_for_concept TINYINT(1)  NOT NULL DEFAULT 0
                                                    COMMENT 'concept_instance.is_complete depends on these',
    display_order           INT         NOT NULL DEFAULT 0,
    effective_from          DATE        NOT NULL,
    effective_to            DATE        NULL,

    PRIMARY KEY (map_id),
    UNIQUE KEY uq_concept_attr (concept_id, attribute_id),

    CONSTRAINT fk_cam_concept
        FOREIGN KEY (concept_id) REFERENCES concept_definition (concept_id),
    CONSTRAINT fk_cam_attribute
        FOREIGN KEY (attribute_id) REFERENCES attribute_catalog (attribute_id)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;
```

---

## 3. Clinical Document Store

Not partitioned. FK on `form_version_id` active. Generated stored columns index the most-queried JSON paths.

```sql
CREATE TABLE clinical_document (
    document_id             BINARY(16)      NOT NULL DEFAULT (UUID_TO_BIN(UUID(), 1)),
    patient_id              BINARY(16)      NOT NULL,
    encounter_id            BINARY(16)      NOT NULL,
    form_version_id         BINARY(16)      NOT NULL,

    -- Bitemporal document lifecycle (all UTC)
    authored_at             DATETIME(6)     NOT NULL    COMMENT 'When clinician signed. UTC.',
    submitted_at            DATETIME(6)     NOT NULL    COMMENT 'When document reached the server. UTC.',
    last_amended_at         DATETIME(6)     NULL        COMMENT 'UTC.',

    -- Document status
    document_status         ENUM('DRAFT','SIGNED','AMENDED','VOIDED')
                                            NOT NULL DEFAULT 'DRAFT',
    void_reason             TEXT            NULL,

    -- Canonical JSON (source of truth)
    canonical_json          JSON            NOT NULL,
    json_hash               CHAR(64)        NOT NULL    COMMENT 'SHA-256 of canonical_json at save time',

    -- Generated columns for frequently queried JSON paths
    -- These enable indexed lookups without full JSON scans.
    -- Extend as additional paths require indexing.
    _doc_patient_id         VARCHAR(36)     GENERATED ALWAYS AS
                                (canonical_json ->> '$.patient_id') STORED,
    _doc_encounter_id       VARCHAR(36)     GENERATED ALWAYS AS
                                (canonical_json ->> '$.encounter_id') STORED,
    _doc_clinical_datetime  DATETIME(6)     GENERATED ALWAYS AS
                                (CAST(canonical_json ->> '$.clinical_datetime' AS DATETIME(6))) STORED
                                            COMMENT 'Clinical effective time extracted from JSON',

    -- Projection pipeline tracking
    projection_status       ENUM('PENDING','PROJECTED','FAILED','STALE')
                                            NOT NULL DEFAULT 'PENDING',
    projected_at            DATETIME(6)     NULL,
    projection_error        TEXT            NULL
                                            COMMENT 'Last error message if projection_status = FAILED',

    -- Audit
    created_by              BINARY(16)      NOT NULL,
    created_at              DATETIME(6)     NOT NULL DEFAULT NOW(6),

    PRIMARY KEY (document_id),
    KEY idx_doc_patient     (patient_id),
    KEY idx_doc_encounter   (encounter_id),
    KEY idx_doc_status      (projection_status),
    KEY idx_doc_authored    (authored_at),
    KEY idx_doc_gen_patient (_doc_patient_id),
    KEY idx_doc_gen_enc     (_doc_encounter_id),
    KEY idx_doc_gen_cdt     (_doc_clinical_datetime),

    CONSTRAINT fk_doc_form_version
        FOREIGN KEY (form_version_id) REFERENCES form_version (form_version_id)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci
  COMMENT='canonical_json is immutable after SIGNED. All datetimes UTC.';
```

---

## 4. Typed EAV Fact Tables

**Partitioned. FK constraints dropped. Referential integrity enforced at application layer.**

All EAV tables share the same header column set. Partition strategy: `RANGE` on `TO_DAYS(obs_date)`. Monthly partitions shown — automate creation with an `EVENT` scheduler or application job.

The pattern below is shown in full for `obs_quantity`. Subsequent tables show only the type-specific value columns; the header and partition clauses follow the same structure.

---

### 4.1 `concept_instance`

The concept anchor table. One row per concept occurrence per document **per JSON path scope**. All EAV rows reference `instance_id` to group related facts. The `json_path_scope` column is what makes two medication entries in the same document two distinct instances rather than a collision.

```sql
CREATE TABLE concept_instance (
    instance_id                 BINARY(16)      NOT NULL DEFAULT (UUID_TO_BIN(UUID(), 1)),
    document_id                 BINARY(16)      NOT NULL    COMMENT 'FK to clinical_document — app enforced',
    concept_id                  BINARY(16)      NOT NULL    COMMENT 'FK to concept_definition — app enforced',
    patient_id                  BINARY(16)      NOT NULL,
    encounter_id                BINARY(16)      NOT NULL,

    -- Path discriminator: root path of the repeating group or named section
    -- Examples:
    --   Single occurrence  : $.vitals.blood_pressure
    --   Repeating array    : $.medications[0], $.medications[1]
    --   Parallel sections  : $.eye_exam.left, $.eye_exam.right
    -- This is the scope key that distinguishes multiple instances of the
    -- same concept within one document. NOT the leaf attribute path.
    json_path_scope             VARCHAR(500)    NOT NULL
                                                COMMENT 'Group root path from canonical JSON. Discriminator.',

    observation_datetime        DATETIME(6)     NOT NULL    COMMENT 'Clinical event time. UTC.',
    recorded_at                 DATETIME(6)     NOT NULL    COMMENT 'Entry time. UTC.',

    -- Completeness tracking
    expected_attribute_count    SMALLINT UNSIGNED NOT NULL,
    projected_attribute_count   SMALLINT UNSIGNED NOT NULL DEFAULT 0,
    is_complete                 TINYINT(1)      GENERATED ALWAYS AS
                                    (projected_attribute_count >= expected_attribute_count) STORED,

    -- Bitemporal validity
    valid_from                  DATETIME(6)     NOT NULL DEFAULT NOW(6),
    valid_to                    DATETIME(6)     NULL        COMMENT 'NULL = current row',
    row_status                  ENUM('ACTIVE','SUPERSEDED','VOIDED')
                                                NOT NULL DEFAULT 'ACTIVE',
    superseded_by               BINARY(16)      NULL        COMMENT 'FK to concept_instance — app enforced',

    created_by                  BINARY(16)      NOT NULL,
    created_at                  DATETIME(6)     NOT NULL DEFAULT NOW(6),
    obs_date                    DATE            GENERATED ALWAYS AS (DATE(observation_datetime)) STORED,

    PRIMARY KEY (instance_id, obs_date),
    -- json_path_scope is part of uniqueness: multiple instances of the
    -- same concept in the same document are distinguished by their scope path
    UNIQUE KEY uq_ci_doc_concept_path (document_id, concept_id, json_path_scope, obs_date),
    KEY idx_ci_patient_concept  (patient_id, concept_id, observation_datetime),
    KEY idx_ci_encounter        (encounter_id, concept_id),
    KEY idx_ci_path_scope       (document_id, json_path_scope),
    KEY idx_ci_status           (row_status)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci
  COMMENT='One row per concept occurrence per document per json_path_scope. No FK constraints — partitioned.'
  PARTITION BY RANGE (TO_DAYS(obs_date)) (
    PARTITION p_before_2025 VALUES LESS THAN (TO_DAYS('2025-01-01')),
    PARTITION p_2025_01     VALUES LESS THAN (TO_DAYS('2025-02-01')),
    PARTITION p_2025_02     VALUES LESS THAN (TO_DAYS('2025-03-01')),
    -- ... continue monthly partitions ...
    PARTITION p_future      VALUES LESS THAN MAXVALUE
);
```

> **Partition maintenance:** Add new monthly partitions by reorganizing `p_future` before it is reached. Example:
> ```sql
> ALTER TABLE concept_instance REORGANIZE PARTITION p_future INTO (
>     PARTITION p_2026_06 VALUES LESS THAN (TO_DAYS('2026-07-01')),
>     PARTITION p_future  VALUES LESS THAN MAXVALUE
> );
> ```
> Automate this with a MySQL `EVENT` that runs on the 1st of each month, or from an application scheduler.

---

### 4.1a Path Model: Template, Scope, and Leaf

Three distinct path values work together across the schema. Each answers a different question:

| Column | Lives on | Example | Answers |
|---|---|---|---|
| `attribute_catalog.json_path` | Metadata | `$.medications[*].dose` | What template path does this attribute follow in any document? |
| `concept_instance.json_path_scope` | Anchor row | `$.medications[0]` | Which specific group occurrence is this instance? |
| `obs_*.source_json_path` | Each EAV row | `$.medications[0].dose` | Which exact leaf in the JSON produced this value? |

**How the projection engine uses them:**

1. For each `attribute_catalog` row with `is_projected = 1`, take the `json_path` template.
2. Resolve it against the document's `canonical_json` — a wildcard path like `$.medications[*].dose` yields one result per array element.
3. For each resolved result, derive the scope root by stripping the leaf segment: `$.medications[0].dose` → scope = `$.medications[0]`.
4. Look up or create a `concept_instance` row keyed on `(document_id, concept_id, json_path_scope, obs_date)`. This is the anchor for this group occurrence.
5. Write the EAV row with `instance_id` pointing to that anchor and `source_json_path` holding the full resolved leaf path.

**Named section example** — parallel `$.eye_exam.left` and `$.eye_exam.right`:

```
concept_instance row 1:  concept=VISUAL_ACUITY, json_path_scope=$.eye_exam.left
concept_instance row 2:  concept=VISUAL_ACUITY, json_path_scope=$.eye_exam.right

obs_quantity row A:  instance_id→row1,  source_json_path=$.eye_exam.left.visual_acuity
obs_quantity row B:  instance_id→row2,  source_json_path=$.eye_exam.right.visual_acuity
```

**Repeating array example** — two BP readings in one encounter:

```
concept_instance row 1:  concept=BLOOD_PRESSURE, json_path_scope=$.vitals.bp[0]
concept_instance row 2:  concept=BLOOD_PRESSURE, json_path_scope=$.vitals.bp[1]

obs_quantity row A:  instance_id→row1,  source_json_path=$.vitals.bp[0].systolic
obs_quantity row B:  instance_id→row1,  source_json_path=$.vitals.bp[0].diastolic
obs_quantity row C:  instance_id→row2,  source_json_path=$.vitals.bp[1].systolic
obs_quantity row D:  instance_id→row2,  source_json_path=$.vitals.bp[1].diastolic
```

Retrieving "all BP readings for this document" is a single join on `concept_id` + `document_id` to `concept_instance`, then join to EAV tables on `instance_id`. No path parsing at query time.

---

```sql
CREATE TABLE obs_quantity (
    obs_id                  BINARY(16)      NOT NULL DEFAULT (UUID_TO_BIN(UUID(), 1)),

    -- Parent references (app-enforced, no FK on partitioned table)
    document_id             BINARY(16)      NOT NULL    COMMENT 'FK → clinical_document',
    instance_id             BINARY(16)      NOT NULL    COMMENT 'FK → concept_instance (the anchor)',
    attribute_id            BINARY(16)      NOT NULL    COMMENT 'FK → attribute_catalog',
    concept_id              BINARY(16)      NOT NULL    COMMENT 'FK → concept_definition',
    patient_id              BINARY(16)      NOT NULL,
    encounter_id            BINARY(16)      NOT NULL,

    -- Type-specific value columns
    magnitude               DECIMAL(18,6)   NOT NULL,
    magnitude_status        ENUM('EQUAL','LESS_THAN','GREATER_THAN',
                                 'LESS_THAN_OR_EQUAL','GREATER_THAN_OR_EQUAL')
                                            NOT NULL DEFAULT 'EQUAL',
    units                   VARCHAR(50)     NOT NULL    COMMENT 'Original unit as entered',
    normalized_value        DECIMAL(18,6)   NULL        COMMENT 'Value in normalized UCUM unit',
    normalized_units        VARCHAR(50)     NULL        COMMENT 'Normalized UCUM unit code',
    normal_range_low        DECIMAL(18,6)   NULL,
    normal_range_high       DECIMAL(18,6)   NULL,
    normal_range_units      VARCHAR(50)     NULL,

    -- Temporal (bitemporal — all UTC)
    observation_datetime    DATETIME(6)     NOT NULL    COMMENT 'When clinical event occurred. UTC.',
    recorded_at             DATETIME(6)     NOT NULL    COMMENT 'When value was entered. UTC.',
    valid_from              DATETIME(6)     NOT NULL DEFAULT NOW(6) COMMENT 'Correction lifecycle start. UTC.',
    valid_to                DATETIME(6)     NULL        COMMENT 'NULL = current row. UTC.',

    -- Lineage and quality
    row_status              ENUM('ACTIVE','SUPERSEDED','VOIDED')
                                            NOT NULL DEFAULT 'ACTIVE',
    superseded_by           BINARY(16)      NULL        COMMENT 'obs_id of corrected row — app enforced',
    data_quality_flag       ENUM('VALID','IMPLAUSIBLE','UNVERIFIED','CORRECTED')
                                            NOT NULL DEFAULT 'UNVERIFIED',
    source_json_path        VARCHAR(500)    NOT NULL
                                            COMMENT 'Full resolved leaf path, e.g. $.medications[0].dose. Scope root is on concept_instance.json_path_scope.',
    created_by              BINARY(16)      NOT NULL,
    created_at              DATETIME(6)     NOT NULL DEFAULT NOW(6),

    -- Partition key (generated stored)
    obs_date                DATE            GENERATED ALWAYS AS (DATE(observation_datetime)) STORED,

    PRIMARY KEY (obs_id, obs_date),
    KEY idx_obsq_patient_attr  (patient_id, attribute_id, observation_datetime),
    KEY idx_obsq_instance      (instance_id),
    KEY idx_obsq_doc           (document_id),
    KEY idx_obsq_status        (row_status, valid_to),
    KEY idx_obsq_concept_enc   (concept_id, encounter_id)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci
  COMMENT='DV_QUANTITY facts. No FK constraints — partitioned. All datetimes UTC.'
  PARTITION BY RANGE (TO_DAYS(obs_date)) (
    PARTITION p_before_2025 VALUES LESS THAN (TO_DAYS('2025-01-01')),
    PARTITION p_2025_01     VALUES LESS THAN (TO_DAYS('2025-02-01')),
    PARTITION p_2025_02     VALUES LESS THAN (TO_DAYS('2025-03-01')),
    PARTITION p_future      VALUES LESS THAN MAXVALUE
);
```

---

### 4.3 `obs_coded` — DV_CODED_TEXT

```sql
CREATE TABLE obs_coded (
    obs_id                  BINARY(16)      NOT NULL DEFAULT (UUID_TO_BIN(UUID(), 1)),
    document_id             BINARY(16)      NOT NULL,
    instance_id             BINARY(16)      NOT NULL,
    attribute_id            BINARY(16)      NOT NULL,
    concept_id              BINARY(16)      NOT NULL,
    patient_id              BINARY(16)      NOT NULL,
    encounter_id            BINARY(16)      NOT NULL,

    -- Type-specific
    code_value              VARCHAR(100)    NOT NULL,
    code_display            VARCHAR(500)    NOT NULL,
    terminology_system      VARCHAR(100)    NOT NULL    COMMENT 'ICD-10, SNOMED-CT, LOINC, LOCAL',
    terminology_version     VARCHAR(50)     NULL,
    local_code              VARCHAR(100)    NULL        COMMENT 'Internal mapped code if applicable',
    preferred_term          VARCHAR(500)    NULL,

    -- Temporal
    observation_datetime    DATETIME(6)     NOT NULL,
    recorded_at             DATETIME(6)     NOT NULL,
    valid_from              DATETIME(6)     NOT NULL DEFAULT NOW(6),
    valid_to                DATETIME(6)     NULL,

    -- Lineage
    row_status              ENUM('ACTIVE','SUPERSEDED','VOIDED') NOT NULL DEFAULT 'ACTIVE',
    superseded_by           BINARY(16)      NULL,
    data_quality_flag       ENUM('VALID','IMPLAUSIBLE','UNVERIFIED','CORRECTED')
                                            NOT NULL DEFAULT 'UNVERIFIED',
    source_json_path        VARCHAR(500)    NOT NULL,
    created_by              BINARY(16)      NOT NULL,
    created_at              DATETIME(6)     NOT NULL DEFAULT NOW(6),
    obs_date                DATE            GENERATED ALWAYS AS (DATE(observation_datetime)) STORED,

    PRIMARY KEY (obs_id, obs_date),
    KEY idx_obscoded_patient_attr  (patient_id, attribute_id, observation_datetime),
    KEY idx_obscoded_instance      (instance_id),
    KEY idx_obscoded_code          (terminology_system, code_value),
    KEY idx_obscoded_status        (row_status, valid_to)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci
  COMMENT='DV_CODED_TEXT facts. No FK constraints — partitioned. All datetimes UTC.'
  PARTITION BY RANGE (TO_DAYS(obs_date)) (
    PARTITION p_before_2025 VALUES LESS THAN (TO_DAYS('2025-01-01')),
    PARTITION p_2025_01     VALUES LESS THAN (TO_DAYS('2025-02-01')),
    PARTITION p_2025_02     VALUES LESS THAN (TO_DAYS('2025-03-01')),
    PARTITION p_future      VALUES LESS THAN MAXVALUE
);
```

---

### 4.4 `obs_ordinal` — DV_ORDINAL

```sql
CREATE TABLE obs_ordinal (
    obs_id                  BINARY(16)      NOT NULL DEFAULT (UUID_TO_BIN(UUID(), 1)),
    document_id             BINARY(16)      NOT NULL,
    instance_id             BINARY(16)      NOT NULL,
    attribute_id            BINARY(16)      NOT NULL,
    concept_id              BINARY(16)      NOT NULL,
    patient_id              BINARY(16)      NOT NULL,
    encounter_id            BINARY(16)      NOT NULL,

    -- Type-specific
    ordinal_value           SMALLINT        NOT NULL    COMMENT 'Numeric rank on the scale',
    ordinal_symbol          VARCHAR(50)     NULL        COMMENT 'e.g. 1+, T, MODERATE',
    ordinal_label           VARCHAR(200)    NOT NULL    COMMENT 'e.g. Eye opening to pain',
    scale_min               SMALLINT        NOT NULL,
    scale_max               SMALLINT        NOT NULL,
    scale_name              VARCHAR(100)    NULL        COMMENT 'e.g. Glasgow Coma Scale',

    observation_datetime    DATETIME(6)     NOT NULL,
    recorded_at             DATETIME(6)     NOT NULL,
    valid_from              DATETIME(6)     NOT NULL DEFAULT NOW(6),
    valid_to                DATETIME(6)     NULL,

    row_status              ENUM('ACTIVE','SUPERSEDED','VOIDED') NOT NULL DEFAULT 'ACTIVE',
    superseded_by           BINARY(16)      NULL,
    data_quality_flag       ENUM('VALID','IMPLAUSIBLE','UNVERIFIED','CORRECTED')
                                            NOT NULL DEFAULT 'UNVERIFIED',
    source_json_path        VARCHAR(500)    NOT NULL,
    created_by              BINARY(16)      NOT NULL,
    created_at              DATETIME(6)     NOT NULL DEFAULT NOW(6),
    obs_date                DATE            GENERATED ALWAYS AS (DATE(observation_datetime)) STORED,

    PRIMARY KEY (obs_id, obs_date),
    KEY idx_obsord_patient_attr  (patient_id, attribute_id, observation_datetime),
    KEY idx_obsord_instance      (instance_id),
    KEY idx_obsord_status        (row_status, valid_to)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci
  COMMENT='DV_ORDINAL facts — scored scales. No FK constraints — partitioned.'
  PARTITION BY RANGE (TO_DAYS(obs_date)) (
    PARTITION p_before_2025 VALUES LESS THAN (TO_DAYS('2025-01-01')),
    PARTITION p_2025_01     VALUES LESS THAN (TO_DAYS('2025-02-01')),
    PARTITION p_2025_02     VALUES LESS THAN (TO_DAYS('2025-03-01')),
    PARTITION p_future      VALUES LESS THAN MAXVALUE
);
```

---

### 4.5 `obs_text` — DV_TEXT

```sql
CREATE TABLE obs_text (
    obs_id                  BINARY(16)      NOT NULL DEFAULT (UUID_TO_BIN(UUID(), 1)),
    document_id             BINARY(16)      NOT NULL,
    instance_id             BINARY(16)      NOT NULL,
    attribute_id            BINARY(16)      NOT NULL,
    concept_id              BINARY(16)      NOT NULL,
    patient_id              BINARY(16)      NOT NULL,
    encounter_id            BINARY(16)      NOT NULL,

    -- Type-specific
    text_value              TEXT            NOT NULL,
    language_code           CHAR(5)         NOT NULL DEFAULT 'en-PH',

    observation_datetime    DATETIME(6)     NOT NULL,
    recorded_at             DATETIME(6)     NOT NULL,
    valid_from              DATETIME(6)     NOT NULL DEFAULT NOW(6),
    valid_to                DATETIME(6)     NULL,

    row_status              ENUM('ACTIVE','SUPERSEDED','VOIDED') NOT NULL DEFAULT 'ACTIVE',
    superseded_by           BINARY(16)      NULL,
    data_quality_flag       ENUM('VALID','IMPLAUSIBLE','UNVERIFIED','CORRECTED')
                                            NOT NULL DEFAULT 'UNVERIFIED',
    source_json_path        VARCHAR(500)    NOT NULL,
    created_by              BINARY(16)      NOT NULL,
    created_at              DATETIME(6)     NOT NULL DEFAULT NOW(6),
    obs_date                DATE            GENERATED ALWAYS AS (DATE(observation_datetime)) STORED,

    PRIMARY KEY (obs_id, obs_date),
    KEY idx_obstxt_patient_attr  (patient_id, attribute_id, observation_datetime),
    KEY idx_obstxt_instance      (instance_id),
    KEY idx_obstxt_status        (row_status, valid_to),
    FULLTEXT KEY ft_text_value   (text_value)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci
  COMMENT='DV_TEXT facts — projected free-text only. No FK constraints — partitioned.'
  PARTITION BY RANGE (TO_DAYS(obs_date)) (
    PARTITION p_before_2025 VALUES LESS THAN (TO_DAYS('2025-01-01')),
    PARTITION p_2025_01     VALUES LESS THAN (TO_DAYS('2025-02-01')),
    PARTITION p_2025_02     VALUES LESS THAN (TO_DAYS('2025-03-01')),
    PARTITION p_future      VALUES LESS THAN MAXVALUE
);
```

> **Note on FULLTEXT:** MySQL does not support `FULLTEXT` indexes on partitioned tables in 8.0. If full-text search on `text_value` is required, maintain a separate non-partitioned mirror table for `obs_text` rows flagged for search, or use an external search engine (Elasticsearch, Meilisearch). The `FULLTEXT` index above is shown for the non-partitioned fallback configuration.

---

### 4.6 `obs_datetime` — DV_DATE_TIME

```sql
CREATE TABLE obs_datetime (
    obs_id                  BINARY(16)      NOT NULL DEFAULT (UUID_TO_BIN(UUID(), 1)),
    document_id             BINARY(16)      NOT NULL,
    instance_id             BINARY(16)      NOT NULL,
    attribute_id            BINARY(16)      NOT NULL,
    concept_id              BINARY(16)      NOT NULL,
    patient_id              BINARY(16)      NOT NULL,
    encounter_id            BINARY(16)      NOT NULL,

    -- Type-specific (a clinical datetime that IS the value, e.g. date of onset)
    datetime_value          DATETIME(6)     NOT NULL,
    datetime_precision      ENUM('YEAR','MONTH','DATE','DATETIME')
                                            NOT NULL DEFAULT 'DATETIME'
                                            COMMENT 'Patient may recall year only — do not assume full precision',

    observation_datetime    DATETIME(6)     NOT NULL,
    recorded_at             DATETIME(6)     NOT NULL,
    valid_from              DATETIME(6)     NOT NULL DEFAULT NOW(6),
    valid_to                DATETIME(6)     NULL,

    row_status              ENUM('ACTIVE','SUPERSEDED','VOIDED') NOT NULL DEFAULT 'ACTIVE',
    superseded_by           BINARY(16)      NULL,
    data_quality_flag       ENUM('VALID','IMPLAUSIBLE','UNVERIFIED','CORRECTED')
                                            NOT NULL DEFAULT 'UNVERIFIED',
    source_json_path        VARCHAR(500)    NOT NULL,
    created_by              BINARY(16)      NOT NULL,
    created_at              DATETIME(6)     NOT NULL DEFAULT NOW(6),
    obs_date                DATE            GENERATED ALWAYS AS (DATE(observation_datetime)) STORED,

    PRIMARY KEY (obs_id, obs_date),
    KEY idx_obsdt_patient_attr  (patient_id, attribute_id, observation_datetime),
    KEY idx_obsdt_instance      (instance_id),
    KEY idx_obsdt_status        (row_status, valid_to)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci
  PARTITION BY RANGE (TO_DAYS(obs_date)) (
    PARTITION p_before_2025 VALUES LESS THAN (TO_DAYS('2025-01-01')),
    PARTITION p_2025_01     VALUES LESS THAN (TO_DAYS('2025-02-01')),
    PARTITION p_2025_02     VALUES LESS THAN (TO_DAYS('2025-03-01')),
    PARTITION p_future      VALUES LESS THAN MAXVALUE
);
```

---

### 4.7 `obs_boolean` and `obs_proportion`

```sql
-- DV_BOOLEAN: yes/no clinical flags
CREATE TABLE obs_boolean (
    obs_id                  BINARY(16)      NOT NULL DEFAULT (UUID_TO_BIN(UUID(), 1)),
    document_id             BINARY(16)      NOT NULL,
    instance_id             BINARY(16)      NOT NULL,
    attribute_id            BINARY(16)      NOT NULL,
    concept_id              BINARY(16)      NOT NULL,
    patient_id              BINARY(16)      NOT NULL,
    encounter_id            BINARY(16)      NOT NULL,

    bool_value              TINYINT(1)      NOT NULL COMMENT '1 = TRUE, 0 = FALSE',

    observation_datetime    DATETIME(6)     NOT NULL,
    recorded_at             DATETIME(6)     NOT NULL,
    valid_from              DATETIME(6)     NOT NULL DEFAULT NOW(6),
    valid_to                DATETIME(6)     NULL,
    row_status              ENUM('ACTIVE','SUPERSEDED','VOIDED') NOT NULL DEFAULT 'ACTIVE',
    superseded_by           BINARY(16)      NULL,
    data_quality_flag       ENUM('VALID','IMPLAUSIBLE','UNVERIFIED','CORRECTED')
                                            NOT NULL DEFAULT 'UNVERIFIED',
    source_json_path        VARCHAR(500)    NOT NULL,
    created_by              BINARY(16)      NOT NULL,
    created_at              DATETIME(6)     NOT NULL DEFAULT NOW(6),
    obs_date                DATE            GENERATED ALWAYS AS (DATE(observation_datetime)) STORED,

    PRIMARY KEY (obs_id, obs_date),
    KEY idx_obsbool_patient_attr (patient_id, attribute_id, observation_datetime),
    KEY idx_obsbool_instance     (instance_id)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci
  PARTITION BY RANGE (TO_DAYS(obs_date)) (
    PARTITION p_before_2025 VALUES LESS THAN (TO_DAYS('2025-01-01')),
    PARTITION p_2025_01     VALUES LESS THAN (TO_DAYS('2025-02-01')),
    PARTITION p_2025_02     VALUES LESS THAN (TO_DAYS('2025-03-01')),
    PARTITION p_future      VALUES LESS THAN MAXVALUE
);

-- DV_PROPORTION: percentages, ratios, fractions
CREATE TABLE obs_proportion (
    obs_id                  BINARY(16)      NOT NULL DEFAULT (UUID_TO_BIN(UUID(), 1)),
    document_id             BINARY(16)      NOT NULL,
    instance_id             BINARY(16)      NOT NULL,
    attribute_id            BINARY(16)      NOT NULL,
    concept_id              BINARY(16)      NOT NULL,
    patient_id              BINARY(16)      NOT NULL,
    encounter_id            BINARY(16)      NOT NULL,

    numerator               DECIMAL(10,4)   NOT NULL,
    denominator             DECIMAL(10,4)   NOT NULL DEFAULT 100,
    proportion_type         ENUM('PERCENT','RATIO','FRACTION','UNITARY')
                                            NOT NULL DEFAULT 'PERCENT',
    computed_value          DECIMAL(10,6)   GENERATED ALWAYS AS
                                (numerator / NULLIF(denominator, 0)) STORED,

    observation_datetime    DATETIME(6)     NOT NULL,
    recorded_at             DATETIME(6)     NOT NULL,
    valid_from              DATETIME(6)     NOT NULL DEFAULT NOW(6),
    valid_to                DATETIME(6)     NULL,
    row_status              ENUM('ACTIVE','SUPERSEDED','VOIDED') NOT NULL DEFAULT 'ACTIVE',
    superseded_by           BINARY(16)      NULL,
    data_quality_flag       ENUM('VALID','IMPLAUSIBLE','UNVERIFIED','CORRECTED')
                                            NOT NULL DEFAULT 'UNVERIFIED',
    source_json_path        VARCHAR(500)    NOT NULL,
    created_by              BINARY(16)      NOT NULL,
    created_at              DATETIME(6)     NOT NULL DEFAULT NOW(6),
    obs_date                DATE            GENERATED ALWAYS AS (DATE(observation_datetime)) STORED,

    PRIMARY KEY (obs_id, obs_date),
    KEY idx_obsprop_patient_attr (patient_id, attribute_id, observation_datetime),
    KEY idx_obsprop_instance     (instance_id)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci
  PARTITION BY RANGE (TO_DAYS(obs_date)) (
    PARTITION p_before_2025 VALUES LESS THAN (TO_DAYS('2025-01-01')),
    PARTITION p_2025_01     VALUES LESS THAN (TO_DAYS('2025-02-01')),
    PARTITION p_2025_02     VALUES LESS THAN (TO_DAYS('2025-03-01')),
    PARTITION p_future      VALUES LESS THAN MAXVALUE
);
```

---

## 5. Correction Infrastructure

### 5.1 `obs_correction_audit`

Not partitioned. Small volume, FK constraints active.

```sql
CREATE TABLE obs_correction_audit (
    audit_id            BINARY(16)      NOT NULL DEFAULT (UUID_TO_BIN(UUID(), 1)),
    original_obs_id     BINARY(16)      NOT NULL    COMMENT 'The obs_id that was superseded',
    new_obs_id          BINARY(16)      NOT NULL    COMMENT 'The replacement obs_id',
    obs_table           VARCHAR(50)     NOT NULL    COMMENT 'obs_quantity | obs_coded | obs_ordinal etc.',
    corrected_by        BINARY(16)      NOT NULL,
    corrected_at        DATETIME(6)     NOT NULL DEFAULT NOW(6),
    correction_reason   TEXT            NOT NULL,
    old_value_json      JSON            NOT NULL    COMMENT 'Snapshot of changed columns before correction',
    new_value_json      JSON            NOT NULL    COMMENT 'Snapshot of changed columns after correction',

    PRIMARY KEY (audit_id),
    KEY idx_oca_original    (original_obs_id),
    KEY idx_oca_corrector   (corrected_by, corrected_at)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;
```

---

### 5.2 Correction Stored Procedure — `correct_obs_quantity`

```sql
DELIMITER $$

CREATE PROCEDURE correct_obs_quantity(
    IN  p_original_obs_id   BINARY(16),
    IN  p_obs_date          DATE,           -- required for partition pruning
    IN  p_new_magnitude     DECIMAL(18,6),
    IN  p_new_units         VARCHAR(50),
    IN  p_corrected_by      BINARY(16),
    IN  p_correction_reason TEXT
)
BEGIN
    DECLARE v_new_obs_id        BINARY(16) DEFAULT UUID_TO_BIN(UUID(), 1);
    DECLARE v_doc_id            BINARY(16);
    DECLARE v_instance_id       BINARY(16);
    DECLARE v_attribute_id      BINARY(16);
    DECLARE v_concept_id        BINARY(16);
    DECLARE v_patient_id        BINARY(16);
    DECLARE v_encounter_id      BINARY(16);
    DECLARE v_magnitude         DECIMAL(18,6);
    DECLARE v_mag_status        VARCHAR(30);
    DECLARE v_units             VARCHAR(50);
    DECLARE v_norm_value        DECIMAL(18,6);
    DECLARE v_norm_units        VARCHAR(50);
    DECLARE v_range_low         DECIMAL(18,6);
    DECLARE v_range_high        DECIMAL(18,6);
    DECLARE v_range_units       VARCHAR(50);
    DECLARE v_obs_datetime      DATETIME(6);
    DECLARE v_recorded_at       DATETIME(6);
    DECLARE v_json_path         VARCHAR(500);
    DECLARE v_found             INT DEFAULT 0;

    -- Fetch original row (partition-pruned via p_obs_date)
    SELECT
        document_id, instance_id, attribute_id, concept_id,
        patient_id, encounter_id,
        magnitude, magnitude_status, units,
        normalized_value, normalized_units,
        normal_range_low, normal_range_high, normal_range_units,
        observation_datetime, recorded_at,
        source_json_path,
        1
    INTO
        v_doc_id, v_instance_id, v_attribute_id, v_concept_id,
        v_patient_id, v_encounter_id,
        v_magnitude, v_mag_status, v_units,
        v_norm_value, v_norm_units,
        v_range_low, v_range_high, v_range_units,
        v_obs_datetime, v_recorded_at,
        v_json_path,
        v_found
    FROM obs_quantity
    WHERE obs_id = p_original_obs_id
      AND obs_date = p_obs_date          -- partition pruning
      AND row_status = 'ACTIVE'
    LIMIT 1;

    IF v_found = 0 THEN
        SIGNAL SQLSTATE '45000'
            SET MESSAGE_TEXT = 'obs_id not found or already superseded';
    END IF;

    -- Close the original row
    UPDATE obs_quantity
    SET
        valid_to      = NOW(6),
        row_status    = 'SUPERSEDED',
        superseded_by = v_new_obs_id
    WHERE obs_id    = p_original_obs_id
      AND obs_date  = p_obs_date;

    -- Insert corrected row
    -- observation_datetime and recorded_at are PRESERVED (clinical truth unchanged)
    -- valid_from is NOW (this correction is current from this moment)
    INSERT INTO obs_quantity (
        obs_id,
        document_id, instance_id, attribute_id, concept_id,
        patient_id, encounter_id,
        magnitude, magnitude_status, units,
        normalized_value, normalized_units,
        normal_range_low, normal_range_high, normal_range_units,
        observation_datetime,
        recorded_at,
        valid_from, valid_to,
        row_status, data_quality_flag,
        source_json_path,
        created_by, created_at
    ) VALUES (
        v_new_obs_id,
        v_doc_id, v_instance_id, v_attribute_id, v_concept_id,
        v_patient_id, v_encounter_id,
        p_new_magnitude, v_mag_status, p_new_units,
        v_norm_value, v_norm_units,         -- caller should update normalized cols separately if needed
        v_range_low, v_range_high, v_range_units,
        v_obs_datetime,                     -- UNCHANGED: when the event happened
        v_recorded_at,                      -- UNCHANGED: when originally entered
        NOW(6), NULL,
        'ACTIVE', 'CORRECTED',
        v_json_path,
        p_corrected_by, NOW(6)
    );

    -- Audit log
    INSERT INTO obs_correction_audit (
        original_obs_id, new_obs_id, obs_table,
        corrected_by, corrected_at, correction_reason,
        old_value_json, new_value_json
    ) VALUES (
        p_original_obs_id, v_new_obs_id, 'obs_quantity',
        p_corrected_by, NOW(6), p_correction_reason,
        JSON_OBJECT('magnitude', v_magnitude, 'units', v_units),
        JSON_OBJECT('magnitude', p_new_magnitude, 'units', p_new_units)
    );

END$$

DELIMITER ;
```

> **Why `p_obs_date` is a required parameter:** MySQL can only prune partitions when the partition key column appears literally in the `WHERE` clause. Since `obs_date` is a generated column derived from `observation_datetime`, the optimizer cannot always infer it from `obs_id` alone. Passing `p_obs_date` explicitly guarantees the update and select hit only the correct partition rather than scanning all partitions.

---

## 6. Point-in-Time Correct Queries

MySQL has no `RETURNS TABLE` functions. Two patterns are provided.

### 6.1 Active-only view (operational use)

```sql
CREATE OR REPLACE VIEW v_obs_quantity_active AS
SELECT *
FROM obs_quantity
WHERE row_status = 'ACTIVE'
  AND valid_to IS NULL;
```

---

### 6.2 Point-in-time stored procedure (AI / longitudinal use)

```sql
DELIMITER $$

CREATE PROCEDURE obs_quantity_as_of(
    IN  p_patient_id    BINARY(16),
    IN  p_as_of         DATETIME(6),
    IN  p_date_from     DATE,           -- partition range start for pruning
    IN  p_date_to       DATE            -- partition range end for pruning
)
BEGIN
    -- Returns all obs_quantity rows valid at p_as_of for this patient.
    -- p_date_from / p_date_to narrow the partition scan to the relevant window.
    -- For model training: p_as_of = the prediction timestamp.
    -- Rows with valid_from > p_as_of have not yet been created at that moment.
    -- Rows with valid_to <= p_as_of were superseded before that moment.
    SELECT *
    FROM obs_quantity
    WHERE patient_id            = p_patient_id
      AND obs_date             BETWEEN p_date_from AND p_date_to  -- partition pruning
      AND valid_from           <= p_as_of
      AND (valid_to IS NULL OR valid_to > p_as_of)
      AND observation_datetime <= p_as_of
      AND row_status           != 'VOIDED';
END$$

DELIMITER ;
```

---

## 7. Projection Manifest (MySQL equivalent of the materialized view)

```sql
-- Cache table (replaces PostgreSQL MATERIALIZED VIEW)
CREATE TABLE projection_manifest_cache (
    cache_id            INT UNSIGNED    NOT NULL AUTO_INCREMENT,
    form_code           VARCHAR(100)    NOT NULL,
    semver              VARCHAR(20)     NOT NULL,
    attribute_code      VARCHAR(150)    NOT NULL,
    attribute_label     VARCHAR(255)    NOT NULL,
    dv_class            VARCHAR(30)     NOT NULL,
    is_projected        TINYINT(1)      NOT NULL,
    projection_rationale TEXT           NULL,
    concept_code        VARCHAR(100)    NULL,
    is_anchor_attribute TINYINT(1)      NULL,
    is_required         TINYINT(1)      NULL,
    effective_from      DATE            NOT NULL,
    effective_to        DATE            NULL,
    refreshed_at        DATETIME(6)     NOT NULL,

    PRIMARY KEY (cache_id),
    KEY idx_pmc_form    (form_code),
    KEY idx_pmc_concept (concept_code)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;

-- Refresh procedure (call on schema changes or on a scheduled EVENT)
DELIMITER $$

CREATE PROCEDURE refresh_projection_manifest()
BEGIN
    TRUNCATE TABLE projection_manifest_cache;

    INSERT INTO projection_manifest_cache (
        form_code, semver,
        attribute_code, attribute_label, dv_class,
        is_projected, projection_rationale,
        concept_code,
        is_anchor_attribute, is_required,
        effective_from, effective_to,
        refreshed_at
    )
    SELECT
        fv.form_code,
        fv.semver,
        ac.attribute_code,
        ac.attribute_label,
        ac.dv_class,
        ac.is_projected,
        ac.projection_rationale,
        cd.concept_code,
        cam.is_anchor_attribute,
        cam.is_required_for_concept,
        ac.effective_from,
        ac.effective_to,
        NOW(6)
    FROM attribute_catalog ac
    JOIN form_version fv
        ON fv.form_version_id = ac.form_version_id
    LEFT JOIN concept_attribute_map cam
        ON cam.attribute_id = ac.attribute_id
    LEFT JOIN concept_definition cd
        ON cd.concept_id = cam.concept_id
    ORDER BY fv.form_code, cd.concept_code, cam.display_order;
END$$

DELIMITER ;

-- Optional: schedule automatic refresh daily
CREATE EVENT IF NOT EXISTS evt_refresh_projection_manifest
    ON SCHEDULE EVERY 1 DAY
    STARTS (TIMESTAMP(CURRENT_DATE) + INTERVAL 1 HOUR)
    DO CALL refresh_projection_manifest();
```

---

## 8. Schema Summary

### Partition and FK strategy at a glance

| Table | Partitioned | FK constraints | Reasoning |
|---|---|---|---|
| `form_version` | No | — | Small; referenced by others |
| `attribute_catalog` | No | Yes → `form_version` | Small; governance table |
| `concept_definition` | No | — | Small |
| `concept_attribute_map` | No | Yes → both | Small bridge table |
| `clinical_document` | No | Yes → `form_version` | Large but secondary to EAV; partition if >50M rows |
| `concept_instance` | Yes, by `obs_date` | No (app-enforced) | High volume; core anchor |
| `obs_quantity` | Yes, by `obs_date` | No (app-enforced) | Highest volume table |
| `obs_coded` | Yes, by `obs_date` | No (app-enforced) | High volume |
| `obs_ordinal` | Yes, by `obs_date` | No (app-enforced) | Medium volume |
| `obs_text` | Yes, by `obs_date` | No (app-enforced) | Medium volume |
| `obs_datetime` | Yes, by `obs_date` | No (app-enforced) | Lower volume |
| `obs_boolean` | Yes, by `obs_date` | No (app-enforced) | Lower volume |
| `obs_proportion` | Yes, by `obs_date` | No (app-enforced) | Lower volume |
| `obs_correction_audit` | No | No (cross-table) | Low volume; audit trail |
| `projection_manifest_cache` | No | No | Reporting cache |

### Temporal columns and their meaning

| Column | Meaning | Who sets it |
|---|---|---|
| `observation_datetime` | When the clinical event occurred | Clinician |
| `recorded_at` | When the value was entered into the system | System |
| `valid_from` | When this row version became current | System |
| `valid_to` | When this row version was superseded (NULL = current) | System (correction procedure) |
| `authored_at` on `clinical_document` | When the document was signed | Clinician |
| `submitted_at` on `clinical_document` | When the document reached the server | System |
| `projected_at` on `clinical_document` | When the EAV projection ran | ETL pipeline |

### Path columns and their meaning

| Column | Lives on | Scope | Example |
|---|---|---|---|
| `attribute_catalog.json_path` | Metadata | Template — pattern used by projection engine to locate fields | `$.medications[*].dose` |
| `concept_instance.json_path_scope` | Anchor | Group root — discriminates multiple instances of same concept in one document | `$.medications[0]` |
| `obs_*.source_json_path` | EAV row | Leaf — exact resolved path that produced this individual value | `$.medications[0].dose` |

### UUID usage pattern

```sql
-- Insert
INSERT INTO form_version (form_version_id, ...) VALUES (UUID_TO_BIN(UUID(), 1), ...);

-- Select (readable)
SELECT BIN_TO_UUID(form_version_id, 1) AS form_version_id FROM form_version;

-- WHERE clause lookup from application string
WHERE form_version_id = UUID_TO_BIN('your-uuid-string-here', 1)
```

---

## 9. Implementation Notes Specific to MySQL 8.0

**`DEFAULT (UUID_TO_BIN(UUID(), 1))` requires MySQL 8.0.13+.** The parentheses around the expression are required for function-based defaults.

**`json_path_scope` uniqueness key length.** The `UNIQUE KEY uq_ci_doc_concept_path (document_id, concept_id, json_path_scope, obs_date)` index includes a `VARCHAR(500)` column. MySQL InnoDB has an index key prefix limit of 3072 bytes (utf8mb4 = 4 bytes/char). A `VARCHAR(500)` contributes up to 2000 bytes. Combined with two `BINARY(16)` columns (32 bytes) and a `DATE` (3 bytes), the total is well within limits. If paths in practice exceed 400 characters, reduce the `VARCHAR` size or use a generated stored column containing a `SHA2(json_path_scope, 256)` hash as the uniqueness key instead.

**Generated columns and `ALTER TABLE`.** Adding a new generated stored column to a partitioned table in MySQL 8.0 requires a full table rebuild. Plan generated columns at design time; retrofitting them is expensive on large tables.

**`FULLTEXT` + partitioning.** MySQL 8.0 does not support `FULLTEXT` indexes on partitioned tables. If full-text search on `obs_text.text_value` is needed, the options are: (a) a separate non-partitioned shadow table populated via trigger, (b) an external search index, or (c) dropping partitioning on `obs_text` if its volume is manageable.

**`CHECK` constraints enforced from 8.0.16.** If your deployment is on an earlier 8.0 patch, the `ENUM` columns on `row_status`, `data_quality_flag`, `document_status`, and `projection_status` enforce valid values at the storage level, but the explicit `CHECK` constraints in the PostgreSQL version have no MySQL equivalent before 8.0.16.

**Partition pruning requires explicit date in WHERE.** The `p_obs_date` parameter in `correct_obs_quantity` and the `p_date_from / p_date_to` parameters in `obs_quantity_as_of` are mandatory — they are not optional convenience parameters. Without a literal `obs_date` condition in the query, MySQL will scan all partitions.
