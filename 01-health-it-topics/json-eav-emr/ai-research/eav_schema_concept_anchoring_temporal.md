# EAV Schema Design: Concept Anchoring and Temporal Modeling

**Clinical Documentation Module — Ingestion and Domain Modeling Layer**
*Builds on: Clinical Registry and EMR Architecture (Feb 2026)*

---

## 1. Design Principles

This schema is the typed projection layer that sits between the canonical JSON store and all downstream consumers — operational tables, BI materialized views, and AI feature pipelines. Four principles govern every design decision:

1. **Type fidelity over flexibility.** The EAV tables are not a generic key-value store. Each value type maps to a specific openEHR DV class with its own column set. Avoid the temptation of a single `value TEXT` column.
2. **Concepts are first-class entities.** A concept is an anchored, versioned group of semantically related attributes. It is the unit of extraction, the unit of clinical meaning, and the unit of ML feature engineering.
3. **All data is bitemporal.** Every projected row carries both the time of clinical observation and the time of data entry. Corrections are versioned, not overwritten.
4. **The JSON is the source of truth.** The EAV projection is a derivative. Any field in the projection must be traceable back to a JSON document path. The projection is designed for query performance, not for independent authority.

---

## 2. Metadata Foundation

These tables define what *can* be projected. They are populated at form-design time, before any clinical data arrives.

### 2.1 `form_version`

Tracks every deployed version of every clinical form. Projection rules are always tied to a specific form version.

```sql
CREATE TABLE form_version (
    form_version_id     UUID            PRIMARY KEY DEFAULT gen_random_uuid(),
    form_code           VARCHAR(100)    NOT NULL,           -- stable identifier e.g. 'VITALS', 'SOAP_NOTE'
    version_number      INTEGER         NOT NULL,
    semver              VARCHAR(20)     NOT NULL,           -- e.g. '2.1.0'
    json_schema_hash    CHAR(64)        NOT NULL,           -- SHA-256 of the canonical JSON schema
    status              VARCHAR(20)     NOT NULL            -- 'DRAFT' | 'ACTIVE' | 'DEPRECATED'
                        CHECK (status IN ('DRAFT','ACTIVE','DEPRECATED')),
    effective_from      TIMESTAMPTZ     NOT NULL,
    effective_to        TIMESTAMPTZ,                        -- NULL = currently active
    created_at          TIMESTAMPTZ     NOT NULL DEFAULT now(),
    created_by          UUID            NOT NULL,

    UNIQUE (form_code, version_number)
);
```

> **Why SHA-256 the schema?** If the JSON schema file ever diverges from what is registered here, the hash check fails. This enforces that projection rules are always evaluated against the exact schema version they were written for.

---

### 2.2 `attribute_catalog`

Every field that *may* be projected from JSON into EAV is registered here. This is the single source of truth for what the EAV layer knows about.

```sql
CREATE TYPE dv_class AS ENUM (
    'DV_QUANTITY',          -- numeric with units: weight, BP, temperature
    'DV_CODED_TEXT',        -- coded value from a terminology: ICD-10, SNOMED, local code
    'DV_TEXT',              -- free text, unstructured
    'DV_DATE_TIME',         -- a point in time
    'DV_BOOLEAN',           -- true/false flags
    'DV_ORDINAL',           -- ordered scale: 0-4, mild/moderate/severe
    'DV_PROPORTION',        -- ratio or percentage: SpO2, ejection fraction
    'DV_IDENTIFIER',        -- external identifier: MRN, accession number
    'DV_DURATION'           -- time span: length of stay, symptom duration
);

CREATE TABLE attribute_catalog (
    attribute_id            UUID            PRIMARY KEY DEFAULT gen_random_uuid(),
    form_version_id         UUID            NOT NULL REFERENCES form_version(form_version_id),
    attribute_code          VARCHAR(150)    NOT NULL,           -- stable code, e.g. 'SYSTOLIC_BP'
    attribute_label         VARCHAR(255)    NOT NULL,
    json_path               VARCHAR(500)    NOT NULL,           -- JSONPath to the source field
    dv_class                dv_class        NOT NULL,
    unit_of_measure         VARCHAR(50),                        -- for DV_QUANTITY only
    terminology_system      VARCHAR(100),                       -- e.g. 'SNOMED-CT', 'ICD-10', 'LOINC'
    loinc_code              VARCHAR(20),                        -- LOINC code if mappable
    is_mandatory            BOOLEAN         NOT NULL DEFAULT FALSE,
    is_projected            BOOLEAN         NOT NULL DEFAULT TRUE,  -- FALSE = stored in JSON only
    projection_rationale    TEXT,                               -- why this field is projected
    effective_from          DATE            NOT NULL,
    effective_to            DATE,
    created_at              TIMESTAMPTZ     NOT NULL DEFAULT now(),
    created_by              UUID            NOT NULL,

    UNIQUE (form_version_id, attribute_code)
);
```

> **`is_projected` flag:** This is how you make the "selective projection" policy explicit and auditable. Every field in the form is registered, but only fields with `is_projected = TRUE` appear in the EAV tables. The gap between the full catalog and the projected subset is always visible to governance.

---

### 2.3 `concept_definition`

A concept is a named, versioned group of semantically related attributes that can be extracted together as a clinical unit. A concept corresponds roughly to an openEHR CLUSTER or OBSERVATION archetype.

```sql
CREATE TABLE concept_definition (
    concept_id          UUID            PRIMARY KEY DEFAULT gen_random_uuid(),
    concept_code        VARCHAR(100)    NOT NULL,           -- e.g. 'BLOOD_PRESSURE', 'NEUROLOGICAL_EXAM'
    concept_label       VARCHAR(255)    NOT NULL,
    concept_version     INTEGER         NOT NULL DEFAULT 1,
    archetype_ref       VARCHAR(200),                       -- e.g. 'openEHR-EHR-OBSERVATION.blood_pressure.v2'
    clinical_domain     VARCHAR(100),                       -- e.g. 'CARDIOLOGY', 'NEUROLOGY', 'GENERAL'
    description         TEXT,
    is_extractable      BOOLEAN         NOT NULL DEFAULT TRUE,  -- eligible for feature engineering
    effective_from      DATE            NOT NULL,
    effective_to        DATE,
    created_at          TIMESTAMPTZ     NOT NULL DEFAULT now(),

    UNIQUE (concept_code, concept_version)
);
```

---

### 2.4 `concept_attribute_map`

The bridge between concept definitions and the attribute catalog. This is the anchoring relationship.

```sql
CREATE TABLE concept_attribute_map (
    map_id                  UUID        PRIMARY KEY DEFAULT gen_random_uuid(),
    concept_id              UUID        NOT NULL REFERENCES concept_definition(concept_id),
    attribute_id            UUID        NOT NULL REFERENCES attribute_catalog(attribute_id),
    is_anchor_attribute     BOOLEAN     NOT NULL DEFAULT FALSE,  -- the "key" attribute of the concept
    is_required_for_concept BOOLEAN     NOT NULL DEFAULT FALSE,  -- concept incomplete without this
    display_order           INTEGER     NOT NULL DEFAULT 0,
    effective_from          DATE        NOT NULL,
    effective_to            DATE,

    UNIQUE (concept_id, attribute_id)
);
```

> **Anchor attribute:** Each concept has exactly one anchor attribute — the field whose presence signals that the concept was captured. For `BLOOD_PRESSURE`, the anchor is `SYSTOLIC_BP`. For `GLASGOW_COMA_SCALE`, the anchor is `GCS_TOTAL`. The anchor is what you query to know "was this concept recorded for this encounter?"

---

## 3. Clinical Document Header

Every canonical JSON document that enters the system is registered here. All EAV rows reference back to this header. This is the parent record for the entire projection.

```sql
CREATE TABLE clinical_document (
    document_id             UUID            PRIMARY KEY DEFAULT gen_random_uuid(),
    patient_id              UUID            NOT NULL,
    encounter_id            UUID            NOT NULL,
    form_version_id         UUID            NOT NULL REFERENCES form_version(form_version_id),

    -- Bitemporal: document lifecycle
    authored_at             TIMESTAMPTZ     NOT NULL,       -- when the clinician completed/signed
    submitted_at            TIMESTAMPTZ     NOT NULL,       -- when the document hit the server
    last_amended_at         TIMESTAMPTZ,

    -- Document status
    document_status         VARCHAR(30)     NOT NULL DEFAULT 'DRAFT'
                            CHECK (document_status IN ('DRAFT','SIGNED','AMENDED','VOIDED')),
    void_reason             TEXT,

    -- Source JSON (the canonical record)
    canonical_json          JSONB           NOT NULL,
    json_hash               CHAR(64)        NOT NULL,       -- SHA-256 of canonical_json

    -- Projection tracking
    projection_status       VARCHAR(20)     NOT NULL DEFAULT 'PENDING'
                            CHECK (projection_status IN ('PENDING','PROJECTED','FAILED','STALE')),
    projected_at            TIMESTAMPTZ,
    projection_error        TEXT,

    -- Audit
    created_by              UUID            NOT NULL,
    created_at              TIMESTAMPTZ     NOT NULL DEFAULT now(),

    -- Partitioning key (for large deployments)
    authored_date           DATE            GENERATED ALWAYS AS (authored_at::DATE) STORED
) PARTITION BY RANGE (authored_date);

-- Monthly partitions (example — automate in production)
CREATE TABLE clinical_document_2025_01
    PARTITION OF clinical_document
    FOR VALUES FROM ('2025-01-01') TO ('2025-02-01');
```

> **`projection_status`:** This is the dead-letter mechanism for the ETL pipeline. A document with `projection_status = 'FAILED'` is visible, alertable, and replayable. Silent projection failures — the most dangerous kind — become impossible.

---

## 4. Typed EAV Tables

Rather than one monolithic `obs` table, the projection layer uses a table per openEHR DV class. This is the key architectural choice that preserves type safety without sacrificing flexibility.

### 4.1 Design of the Common Header Columns

Every EAV table carries this common set. All type-specific tables extend it:

| Column | Type | Purpose |
|---|---|---|
| `obs_id` | UUID PK | Row identity |
| `document_id` | UUID FK | Parent document |
| `attribute_id` | UUID FK | What field this is |
| `concept_id` | UUID FK | Which concept anchors this row |
| `patient_id` | UUID | Denormalized for partition pruning |
| `encounter_id` | UUID | Denormalized for join performance |
| `observation_datetime` | TIMESTAMPTZ | When the clinical event occurred |
| `recorded_at` | TIMESTAMPTZ | When the value was entered into the system |
| `valid_from` | TIMESTAMPTZ | Bitemporal: row validity start |
| `valid_to` | TIMESTAMPTZ | Bitemporal: row validity end (NULL = current) |
| `row_status` | VARCHAR | `'ACTIVE'` \| `'SUPERSEDED'` \| `'VOIDED'` |
| `superseded_by` | UUID | FK to replacement row (for corrections) |
| `data_quality_flag` | VARCHAR | `'VALID'` \| `'IMPLAUSIBLE'` \| `'UNVERIFIED'` |
| `source_json_path` | VARCHAR | Exact JSONPath that produced this row |
| `created_by` | UUID | Who or what system projected this row |
| `created_at` | TIMESTAMPTZ | Projection timestamp |

---

### 4.2 `obs_quantity` — DV_QUANTITY

For all numeric measurements with units: vital signs, laboratory results, anthropometrics.

```sql
CREATE TABLE obs_quantity (
    obs_id                  UUID            PRIMARY KEY DEFAULT gen_random_uuid(),
    document_id             UUID            NOT NULL REFERENCES clinical_document(document_id),
    attribute_id            UUID            NOT NULL REFERENCES attribute_catalog(attribute_id),
    concept_id              UUID            NOT NULL REFERENCES concept_definition(concept_id),
    patient_id              UUID            NOT NULL,
    encounter_id            UUID            NOT NULL,

    -- The value
    magnitude               NUMERIC(18, 6)  NOT NULL,
    magnitude_status        VARCHAR(20)     DEFAULT 'EQUAL'
                            CHECK (magnitude_status IN ('EQUAL','LESS_THAN','GREATER_THAN',
                                                        'LESS_THAN_OR_EQUAL','GREATER_THAN_OR_EQUAL')),
    units                   VARCHAR(50)     NOT NULL,           -- UCUM unit code preferred
    normal_range_low        NUMERIC(18, 6),
    normal_range_high       NUMERIC(18, 6),
    normal_range_units      VARCHAR(50),

    -- Temporal (bitemporal)
    observation_datetime    TIMESTAMPTZ     NOT NULL,
    recorded_at             TIMESTAMPTZ     NOT NULL DEFAULT now(),
    valid_from              TIMESTAMPTZ     NOT NULL DEFAULT now(),
    valid_to                TIMESTAMPTZ,

    -- Lineage and quality
    row_status              VARCHAR(20)     NOT NULL DEFAULT 'ACTIVE'
                            CHECK (row_status IN ('ACTIVE','SUPERSEDED','VOIDED')),
    superseded_by           UUID            REFERENCES obs_quantity(obs_id),
    data_quality_flag       VARCHAR(20)     NOT NULL DEFAULT 'UNVERIFIED'
                            CHECK (data_quality_flag IN ('VALID','IMPLAUSIBLE','UNVERIFIED','CORRECTED')),
    source_json_path        VARCHAR(500)    NOT NULL,
    created_by              UUID            NOT NULL,
    created_at              TIMESTAMPTZ     NOT NULL DEFAULT now(),

    -- Partition key
    obs_date                DATE            GENERATED ALWAYS AS (observation_datetime::DATE) STORED
) PARTITION BY RANGE (obs_date);

CREATE INDEX idx_obsq_patient_attr_time
    ON obs_quantity (patient_id, attribute_id, observation_datetime DESC)
    WHERE row_status = 'ACTIVE';

CREATE INDEX idx_obsq_concept_encounter
    ON obs_quantity (concept_id, encounter_id)
    WHERE row_status = 'ACTIVE';

CREATE INDEX idx_obsq_document
    ON obs_quantity (document_id);
```

---

### 4.3 `obs_coded` — DV_CODED_TEXT

For all terminology-bound values: diagnoses, findings, procedure codes, drug names.

```sql
CREATE TABLE obs_coded (
    obs_id                  UUID            PRIMARY KEY DEFAULT gen_random_uuid(),
    document_id             UUID            NOT NULL REFERENCES clinical_document(document_id),
    attribute_id            UUID            NOT NULL REFERENCES attribute_catalog(attribute_id),
    concept_id              UUID            NOT NULL REFERENCES concept_definition(concept_id),
    patient_id              UUID            NOT NULL,
    encounter_id            UUID            NOT NULL,

    -- The value
    code_value              VARCHAR(100)    NOT NULL,           -- e.g. 'I10', '73211009'
    code_display            VARCHAR(500)    NOT NULL,
    terminology_system      VARCHAR(100)    NOT NULL,           -- 'ICD-10', 'SNOMED-CT', 'LOCAL'
    terminology_version     VARCHAR(50),
    local_code              VARCHAR(100),                       -- internal code if mapped
    preferred_term          VARCHAR(500),                       -- canonical display term

    -- Temporal (bitemporal)
    observation_datetime    TIMESTAMPTZ     NOT NULL,
    recorded_at             TIMESTAMPTZ     NOT NULL DEFAULT now(),
    valid_from              TIMESTAMPTZ     NOT NULL DEFAULT now(),
    valid_to                TIMESTAMPTZ,

    -- Lineage and quality
    row_status              VARCHAR(20)     NOT NULL DEFAULT 'ACTIVE'
                            CHECK (row_status IN ('ACTIVE','SUPERSEDED','VOIDED')),
    superseded_by           UUID            REFERENCES obs_coded(obs_id),
    data_quality_flag       VARCHAR(20)     NOT NULL DEFAULT 'UNVERIFIED'
                            CHECK (data_quality_flag IN ('VALID','IMPLAUSIBLE','UNVERIFIED','CORRECTED')),
    source_json_path        VARCHAR(500)    NOT NULL,
    created_by              UUID            NOT NULL,
    created_at              TIMESTAMPTZ     NOT NULL DEFAULT now(),

    obs_date                DATE            GENERATED ALWAYS AS (observation_datetime::DATE) STORED
) PARTITION BY RANGE (obs_date);

CREATE INDEX idx_obscoded_patient_attr
    ON obs_coded (patient_id, attribute_id, observation_datetime DESC)
    WHERE row_status = 'ACTIVE';
```

---

### 4.4 `obs_text` — DV_TEXT

For unstructured narrative fields: clinical notes, impression text, comments. These are projected selectively — most free-text stays in JSON.

```sql
CREATE TABLE obs_text (
    obs_id                  UUID            PRIMARY KEY DEFAULT gen_random_uuid(),
    document_id             UUID            NOT NULL REFERENCES clinical_document(document_id),
    attribute_id            UUID            NOT NULL REFERENCES attribute_catalog(attribute_id),
    concept_id              UUID            NOT NULL REFERENCES concept_definition(concept_id),
    patient_id              UUID            NOT NULL,
    encounter_id            UUID            NOT NULL,

    text_value              TEXT            NOT NULL,
    character_count         INTEGER         GENERATED ALWAYS AS (char_length(text_value)) STORED,
    language_code           CHAR(5)         NOT NULL DEFAULT 'en-PH',

    observation_datetime    TIMESTAMPTZ     NOT NULL,
    recorded_at             TIMESTAMPTZ     NOT NULL DEFAULT now(),
    valid_from              TIMESTAMPTZ     NOT NULL DEFAULT now(),
    valid_to                TIMESTAMPTZ,

    row_status              VARCHAR(20)     NOT NULL DEFAULT 'ACTIVE'
                            CHECK (row_status IN ('ACTIVE','SUPERSEDED','VOIDED')),
    superseded_by           UUID            REFERENCES obs_text(obs_id),
    data_quality_flag       VARCHAR(20)     NOT NULL DEFAULT 'UNVERIFIED',
    source_json_path        VARCHAR(500)    NOT NULL,
    created_by              UUID            NOT NULL,
    created_at              TIMESTAMPTZ     NOT NULL DEFAULT now(),

    obs_date                DATE            GENERATED ALWAYS AS (observation_datetime::DATE) STORED
) PARTITION BY RANGE (obs_date);
```

---

### 4.5 `obs_ordinal` — DV_ORDINAL

For ordered scales: GCS, pain scales, severity ratings, Apgar scores. Stores both the numeric ordinal value and the display label.

```sql
CREATE TABLE obs_ordinal (
    obs_id                  UUID            PRIMARY KEY DEFAULT gen_random_uuid(),
    document_id             UUID            NOT NULL REFERENCES clinical_document(document_id),
    attribute_id            UUID            NOT NULL REFERENCES attribute_catalog(attribute_id),
    concept_id              UUID            NOT NULL REFERENCES concept_definition(concept_id),
    patient_id              UUID            NOT NULL,
    encounter_id            UUID            NOT NULL,

    ordinal_value           SMALLINT        NOT NULL,           -- numeric rank (0, 1, 2, 3...)
    ordinal_symbol          VARCHAR(50),                        -- '1+', 'MODERATE', etc.
    ordinal_label           VARCHAR(200)    NOT NULL,           -- 'Eye opening to pain'
    scale_min               SMALLINT        NOT NULL,
    scale_max               SMALLINT        NOT NULL,
    scale_name              VARCHAR(100),                       -- 'Glasgow Coma Scale'

    observation_datetime    TIMESTAMPTZ     NOT NULL,
    recorded_at             TIMESTAMPTZ     NOT NULL DEFAULT now(),
    valid_from              TIMESTAMPTZ     NOT NULL DEFAULT now(),
    valid_to                TIMESTAMPTZ,

    row_status              VARCHAR(20)     NOT NULL DEFAULT 'ACTIVE'
                            CHECK (row_status IN ('ACTIVE','SUPERSEDED','VOIDED')),
    superseded_by           UUID            REFERENCES obs_ordinal(obs_id),
    data_quality_flag       VARCHAR(20)     NOT NULL DEFAULT 'UNVERIFIED',
    source_json_path        VARCHAR(500)    NOT NULL,
    created_by              UUID            NOT NULL,
    created_at              TIMESTAMPTZ     NOT NULL DEFAULT now(),

    obs_date                DATE            GENERATED ALWAYS AS (observation_datetime::DATE) STORED
) PARTITION BY RANGE (obs_date);
```

---

### 4.6 `obs_datetime` — DV_DATE_TIME

For clinical time points that are *values*, not metadata: date of onset, date of last menstrual period, date of procedure.

```sql
CREATE TABLE obs_datetime (
    obs_id                  UUID            PRIMARY KEY DEFAULT gen_random_uuid(),
    document_id             UUID            NOT NULL REFERENCES clinical_document(document_id),
    attribute_id            UUID            NOT NULL REFERENCES attribute_catalog(attribute_id),
    concept_id              UUID            NOT NULL REFERENCES concept_definition(concept_id),
    patient_id              UUID            NOT NULL,
    encounter_id            UUID            NOT NULL,

    datetime_value          TIMESTAMPTZ     NOT NULL,
    datetime_precision      VARCHAR(20)     NOT NULL DEFAULT 'DATETIME'
                            CHECK (datetime_precision IN ('YEAR','MONTH','DATE','DATETIME')),
    -- e.g. a patient recalls year of onset but not month

    observation_datetime    TIMESTAMPTZ     NOT NULL,
    recorded_at             TIMESTAMPTZ     NOT NULL DEFAULT now(),
    valid_from              TIMESTAMPTZ     NOT NULL DEFAULT now(),
    valid_to                TIMESTAMPTZ,

    row_status              VARCHAR(20)     NOT NULL DEFAULT 'ACTIVE'
                            CHECK (row_status IN ('ACTIVE','SUPERSEDED','VOIDED')),
    superseded_by           UUID            REFERENCES obs_datetime(obs_id),
    data_quality_flag       VARCHAR(20)     NOT NULL DEFAULT 'UNVERIFIED',
    source_json_path        VARCHAR(500)    NOT NULL,
    created_by              UUID            NOT NULL,
    created_at              TIMESTAMPTZ     NOT NULL DEFAULT now(),

    obs_date                DATE            GENERATED ALWAYS AS (observation_datetime::DATE) STORED
) PARTITION BY RANGE (obs_date);
```

---

### 4.7 `obs_boolean` and `obs_proportion`

Abbreviated for clarity — same header columns, type-specific value columns:

```sql
-- DV_BOOLEAN: flags and yes/no findings
CREATE TABLE obs_boolean (
    obs_id                  UUID            PRIMARY KEY DEFAULT gen_random_uuid(),
    document_id             UUID            NOT NULL REFERENCES clinical_document(document_id),
    attribute_id            UUID            NOT NULL REFERENCES attribute_catalog(attribute_id),
    concept_id              UUID            NOT NULL REFERENCES concept_definition(concept_id),
    patient_id              UUID            NOT NULL,
    encounter_id            UUID            NOT NULL,
    bool_value              BOOLEAN         NOT NULL,
    observation_datetime    TIMESTAMPTZ     NOT NULL,
    recorded_at             TIMESTAMPTZ     NOT NULL DEFAULT now(),
    valid_from              TIMESTAMPTZ     NOT NULL DEFAULT now(),
    valid_to                TIMESTAMPTZ,
    row_status              VARCHAR(20)     NOT NULL DEFAULT 'ACTIVE',
    superseded_by           UUID            REFERENCES obs_boolean(obs_id),
    data_quality_flag       VARCHAR(20)     NOT NULL DEFAULT 'UNVERIFIED',
    source_json_path        VARCHAR(500)    NOT NULL,
    created_by              UUID            NOT NULL,
    created_at              TIMESTAMPTZ     NOT NULL DEFAULT now(),
    obs_date                DATE            GENERATED ALWAYS AS (observation_datetime::DATE) STORED
) PARTITION BY RANGE (obs_date);

-- DV_PROPORTION: percentages, ratios, fractions
CREATE TABLE obs_proportion (
    obs_id                  UUID            PRIMARY KEY DEFAULT gen_random_uuid(),
    document_id             UUID            NOT NULL REFERENCES clinical_document(document_id),
    attribute_id            UUID            NOT NULL REFERENCES attribute_catalog(attribute_id),
    concept_id              UUID            NOT NULL REFERENCES concept_definition(concept_id),
    patient_id              UUID            NOT NULL,
    encounter_id            UUID            NOT NULL,
    numerator               NUMERIC(10, 4)  NOT NULL,
    denominator             NUMERIC(10, 4)  NOT NULL DEFAULT 100,
    proportion_type         VARCHAR(20)     NOT NULL DEFAULT 'PERCENT'
                            CHECK (proportion_type IN ('PERCENT','RATIO','FRACTION','UNITARY')),
    computed_value          NUMERIC(10, 6)  GENERATED ALWAYS AS (numerator / NULLIF(denominator, 0)) STORED,
    observation_datetime    TIMESTAMPTZ     NOT NULL,
    recorded_at             TIMESTAMPTZ     NOT NULL DEFAULT now(),
    valid_from              TIMESTAMPTZ     NOT NULL DEFAULT now(),
    valid_to                TIMESTAMPTZ,
    row_status              VARCHAR(20)     NOT NULL DEFAULT 'ACTIVE',
    superseded_by           UUID            REFERENCES obs_proportion(obs_id),
    data_quality_flag       VARCHAR(20)     NOT NULL DEFAULT 'UNVERIFIED',
    source_json_path        VARCHAR(500)    NOT NULL,
    created_by              UUID            NOT NULL,
    created_at              TIMESTAMPTZ     NOT NULL DEFAULT now(),
    obs_date                DATE            GENERATED ALWAYS AS (observation_datetime::DATE) STORED
) PARTITION BY RANGE (obs_date);
```

---

## 5. Concept Instance Table

This is the concept anchoring mechanism. When the ETL projects a JSON document, it creates one `concept_instance` row per concept present in the document. The instance row is the grouping key for all EAV attribute rows belonging to that concept occurrence.

```sql
CREATE TABLE concept_instance (
    instance_id             UUID            PRIMARY KEY DEFAULT gen_random_uuid(),
    document_id             UUID            NOT NULL REFERENCES clinical_document(document_id),
    concept_id              UUID            NOT NULL REFERENCES concept_definition(concept_id),
    patient_id              UUID            NOT NULL,
    encounter_id            UUID            NOT NULL,

    -- When was this concept captured?
    observation_datetime    TIMESTAMPTZ     NOT NULL,
    recorded_at             TIMESTAMPTZ     NOT NULL DEFAULT now(),

    -- Completeness: how many required attributes were projected?
    expected_attribute_count    INTEGER     NOT NULL,
    projected_attribute_count   INTEGER     NOT NULL DEFAULT 0,
    is_complete                 BOOLEAN     GENERATED ALWAYS AS
                                    (projected_attribute_count >= expected_attribute_count) STORED,

    -- Bitemporal
    valid_from              TIMESTAMPTZ     NOT NULL DEFAULT now(),
    valid_to                TIMESTAMPTZ,

    row_status              VARCHAR(20)     NOT NULL DEFAULT 'ACTIVE'
                            CHECK (row_status IN ('ACTIVE','SUPERSEDED','VOIDED')),
    superseded_by           UUID            REFERENCES concept_instance(instance_id),

    created_by              UUID            NOT NULL,
    created_at              TIMESTAMPTZ     NOT NULL DEFAULT now(),

    obs_date                DATE            GENERATED ALWAYS AS (observation_datetime::DATE) STORED,

    UNIQUE (document_id, concept_id)  -- one instance per concept per document
) PARTITION BY RANGE (obs_date);

CREATE INDEX idx_ci_patient_concept_time
    ON concept_instance (patient_id, concept_id, observation_datetime DESC)
    WHERE row_status = 'ACTIVE';

CREATE INDEX idx_ci_encounter_concept
    ON concept_instance (encounter_id, concept_id)
    WHERE row_status = 'ACTIVE';
```

---

Every EAV row (`obs_quantity`, `obs_coded`, etc.) carries a `concept_id` foreign key. The concept instance table is the hub that lets you extract all attributes of a concept in a single join rather than a dynamic pivot. The query pattern becomes:

```sql
-- Extract all attributes for the BLOOD_PRESSURE concept for a patient
SELECT
    ci.instance_id,
    ci.observation_datetime,
    q.attribute_id,
    ac.attribute_code,
    q.magnitude,
    q.units
FROM concept_instance ci
JOIN obs_quantity q
    ON q.concept_id = ci.concept_id
    AND q.document_id = ci.document_id
    AND q.row_status = 'ACTIVE'
JOIN attribute_catalog ac
    ON ac.attribute_id = q.attribute_id
WHERE ci.patient_id = :patient_id
  AND ci.concept_id = (
      SELECT concept_id FROM concept_definition
      WHERE concept_code = 'BLOOD_PRESSURE' AND effective_to IS NULL
  )
  AND ci.row_status = 'ACTIVE'
ORDER BY ci.observation_datetime DESC;
```

---

## 6. Temporal Modeling: The Correction Pattern

Clinical data corrections are common and legally significant. The schema enforces a strict append-only correction model — no row is ever modified after projection. Corrections create new rows and close old ones.

### 6.1 Correction Procedure

```sql
-- Correct a DV_QUANTITY observation (same pattern for all obs_ tables)
CREATE OR REPLACE PROCEDURE correct_obs_quantity(
    p_original_obs_id   UUID,
    p_new_magnitude     NUMERIC,
    p_new_units         VARCHAR,
    p_corrected_by      UUID,
    p_correction_reason TEXT
)
LANGUAGE plpgsql AS $$
DECLARE
    v_new_obs_id UUID := gen_random_uuid();
    v_original   obs_quantity%ROWTYPE;
BEGIN
    -- Lock and fetch original
    SELECT * INTO v_original
    FROM obs_quantity
    WHERE obs_id = p_original_obs_id
      AND row_status = 'ACTIVE'
    FOR UPDATE;

    IF NOT FOUND THEN
        RAISE EXCEPTION 'obs_id % not found or already superseded', p_original_obs_id;
    END IF;

    -- Close the original row
    UPDATE obs_quantity
    SET
        valid_to        = now(),
        row_status      = 'SUPERSEDED',
        superseded_by   = v_new_obs_id
    WHERE obs_id = p_original_obs_id;

    -- Insert corrected row, inheriting all provenance from original
    INSERT INTO obs_quantity (
        obs_id, document_id, attribute_id, concept_id,
        patient_id, encounter_id,
        magnitude, magnitude_status, units,
        normal_range_low, normal_range_high, normal_range_units,
        observation_datetime,           -- clinical time is UNCHANGED
        recorded_at,                    -- original entry time is UNCHANGED
        valid_from,                     -- new validity starts now
        valid_to,
        row_status,
        data_quality_flag,
        source_json_path,
        created_by, created_at
    ) VALUES (
        v_new_obs_id,
        v_original.document_id, v_original.attribute_id, v_original.concept_id,
        v_original.patient_id, v_original.encounter_id,
        p_new_magnitude, v_original.magnitude_status, p_new_units,
        v_original.normal_range_low, v_original.normal_range_high, v_original.normal_range_units,
        v_original.observation_datetime,    -- preserved: when the event happened
        v_original.recorded_at,             -- preserved: when originally entered
        now(),                              -- valid_from: this correction is current from now
        NULL,
        'ACTIVE',
        'CORRECTED',
        v_original.source_json_path,
        p_corrected_by, now()
    );

    -- Log in correction audit table
    INSERT INTO obs_correction_audit (
        original_obs_id, new_obs_id, obs_table,
        corrected_by, corrected_at, correction_reason,
        old_value_json, new_value_json
    ) VALUES (
        p_original_obs_id, v_new_obs_id, 'obs_quantity',
        p_corrected_by, now(), p_correction_reason,
        jsonb_build_object('magnitude', v_original.magnitude, 'units', v_original.units),
        jsonb_build_object('magnitude', p_new_magnitude, 'units', p_new_units)
    );
END;
$$;
```

### 6.2 Correction Audit Table

```sql
CREATE TABLE obs_correction_audit (
    audit_id            UUID            PRIMARY KEY DEFAULT gen_random_uuid(),
    original_obs_id     UUID            NOT NULL,
    new_obs_id          UUID            NOT NULL,
    obs_table           VARCHAR(50)     NOT NULL,           -- 'obs_quantity', 'obs_coded', etc.
    corrected_by        UUID            NOT NULL,
    corrected_at        TIMESTAMPTZ     NOT NULL DEFAULT now(),
    correction_reason   TEXT            NOT NULL,
    old_value_json      JSONB           NOT NULL,
    new_value_json      JSONB           NOT NULL
);

CREATE INDEX idx_oca_original ON obs_correction_audit (original_obs_id);
CREATE INDEX idx_oca_corrected_by ON obs_correction_audit (corrected_by, corrected_at DESC);
```

---

## 7. Point-in-Time Correct Views

For longitudinal analysis and AI feature extraction, the most important temporal concept is **as-of queries**: "what did we know about this patient as of date X, based only on data that was valid at that time?" This prevents data leakage in predictive models.

```sql
-- Active observations as of NOW (standard operational view)
CREATE VIEW v_obs_quantity_active AS
SELECT *
FROM obs_quantity
WHERE row_status = 'ACTIVE'
  AND valid_to IS NULL;

-- Point-in-time correct: what was valid at a given timestamp?
-- Usage: pass as a parameterized query or wrap in a set-returning function
CREATE OR REPLACE FUNCTION obs_quantity_as_of(
    p_patient_id    UUID,
    p_as_of         TIMESTAMPTZ
)
RETURNS TABLE (LIKE obs_quantity)
LANGUAGE sql STABLE AS $$
    SELECT *
    FROM obs_quantity
    WHERE patient_id = p_patient_id
      AND valid_from <= p_as_of
      AND (valid_to IS NULL OR valid_to > p_as_of)
      AND observation_datetime <= p_as_of
      AND row_status != 'VOIDED';
$$;
```

---

## 8. Projection Manifest View

This view closes the governance loop — it shows every attribute in the catalog, whether it is projected, and the most recent projected row count. Operations and clinical informatics can monitor this regularly.

```sql
CREATE MATERIALIZED VIEW mv_projection_manifest AS
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
    ac.effective_to
FROM attribute_catalog ac
JOIN form_version fv ON fv.form_version_id = ac.form_version_id
LEFT JOIN concept_attribute_map cam ON cam.attribute_id = ac.attribute_id
LEFT JOIN concept_definition cd ON cd.concept_id = cam.concept_id
ORDER BY fv.form_code, cd.concept_code, cam.display_order;

REFRESH MATERIALIZED VIEW mv_projection_manifest;  -- run on schema changes
```

---

## 9. Schema Summary

### Entity relationships

```
form_version
  └── attribute_catalog (1:many)
        └── concept_attribute_map (many:many bridge)
              └── concept_definition

clinical_document (1 per JSON document)
  ├── concept_instance (1 per concept per document)
  ├── obs_quantity     (1 per DV_QUANTITY field projected)
  ├── obs_coded        (1 per DV_CODED_TEXT field projected)
  ├── obs_text         (1 per DV_TEXT field projected)
  ├── obs_ordinal      (1 per DV_ORDINAL field projected)
  ├── obs_datetime     (1 per DV_DATE_TIME field projected)
  ├── obs_boolean      (1 per DV_BOOLEAN field projected)
  └── obs_proportion   (1 per DV_PROPORTION field projected)
```

### Key temporal columns and their meaning

| Column | Meaning | Who controls it |
|---|---|---|
| `observation_datetime` | When the clinical event occurred | Clinician (clinical truth) |
| `recorded_at` | When the value was entered into the system | System (entry truth) |
| `valid_from` | When this row became the current version | System (correction lifecycle) |
| `valid_to` | When this row was superseded or voided | System (correction lifecycle) |
| `authored_at` (document) | When the document was signed | Clinician |
| `submitted_at` (document) | When the document hit the server | System |
| `projected_at` (document) | When the EAV projection ran | ETL pipeline |

---

## 10. Implementation Sequence

1. **Metadata tables first** — `form_version`, `attribute_catalog`, `concept_definition`, `concept_attribute_map`. Populate these before any form goes live.
2. **`clinical_document` next** — this is the parent anchor. No EAV row can exist without it.
3. **`concept_instance` with the EAV tables** — deploy together; `concept_id` FK on EAV tables requires concept_instance to exist first.
4. **Correction infrastructure** — `obs_correction_audit` and the correction procedures before any clinical data is entered.
5. **Views and functions** — `v_obs_quantity_active`, `obs_quantity_as_of()`, `mv_projection_manifest` after core tables are stable.
6. **Partitioning strategy** — automate monthly partition creation via `pg_partman` before go-live; do not manage partitions manually.
