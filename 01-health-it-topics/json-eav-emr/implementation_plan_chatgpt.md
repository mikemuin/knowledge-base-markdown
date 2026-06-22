# ChatGPT Implementation Plan

Below is a consolidated review and implementation plan that merges your original architecture, the Claude review, and the attached schema design.

# Consolidated Review and Implementation Plan

## Clinical Documentation Module for Hospital-Wide EMR

## Executive verdict

The approach is **architecturally sound and worth proceeding with**, provided the projection layer is treated as a governed clinical data product, not just an ETL convenience.

The strongest pattern is:

```text
Clinical form UI
  → Canonical JSON clinical document
  → Versioned projection engine
  → Typed EAV clinical fact store
  → Concept instances / grouped clinical events
  → Operational read models
  → BI / AI marts
```

This gives you flexibility at documentation time, semantic structure at ingestion time, and performance at operational and analytical time.

The attached schema correctly emphasizes four major principles: typed EAV instead of generic key-value storage, concept anchoring, bitemporal modeling, and JSON as the source of truth rather than the EAV projection itself. The additional review also correctly identifies the major risks: schema governance, query complexity, late operational table design, missing temporal/audit discipline, and undefined ETL reliability.

My recommendation: **proceed, but tighten the model in five areas**:

1. Make metadata governance mandatory before form go-live.
2. Add `concept_instance_id` to every projected observation row.
3. Define operational table contracts before forms are finalized.
4. Make the projection pipeline observable, replayable, and reconciled.
5. Build BI/AI marts as separate curated products, not direct EAV pivots.

------

# 1. Final architecture

## 1.1 Layered model

| Layer                    | Purpose                                           | Persistence                        |
| ------------------------ | ------------------------------------------------- | ---------------------------------- |
| Presentation layer       | Metadata-driven clinical forms                    | UI/form engine                     |
| Canonical document layer | Full-fidelity medico-legal record                 | `clinical_document.canonical_json` |
| Metadata registry        | Defines forms, fields, concepts, projection rules | normalized metadata tables         |
| Projection engine        | Converts JSON into clinical facts                 | application/ETL service            |
| Typed EAV layer          | Flexible, queryable clinical fact ledger          | typed `obs_*` tables               |
| Concept instance layer   | Groups facts into clinical events                 | `concept_instance`                 |
| Operational read models  | Fast EMR module usage                             | normalized domain tables           |
| BI / AI layer            | Flattened, longitudinal, point-in-time datasets   | marts, feature tables              |

The right mental model is:

```text
JSON preserves the document.
EAV preserves reusable clinical facts.
Concept instances preserve clinical meaning.
Operational tables serve EMR workflows.
Analytical marts serve BI and AI.
```

------

# 2. Consolidated review

## 2.1 What is strong

### A. Canonical JSON is the right source layer

This is appropriate for hospital-wide documentation because clinical forms are sparse, nested, evolving, and specialty-specific. JSON allows form evolution without constantly changing the physical database schema.

However, the JSON must be **canonical but not arbitrary**. Every document should include:

```text
document_id
patient_id
encounter_id
form_code
form_version
json_schema_hash
authored_at
submitted_at
created_by
document_status
canonical_json
json_hash
```

The attached schema’s use of `json_schema_hash`, `json_hash`, `projection_status`, and form versioning is the right governance direction.

For MySQL, use `JSON` rather than PostgreSQL `JSONB`. MySQL JSON columns cannot be indexed directly; index important JSON paths through generated columns when needed. ([dev.mysql.com](https://dev.mysql.com/doc/refman/8.4/en/create-table.html?utm_source=chatgpt.com))

------

### B. Typed EAV is the correct ingestion model

Traditional EAV fails because everything becomes loosely typed. Your approach avoids this by splitting observation values according to openEHR-style DV classes:

```text
obs_quantity
obs_coded
obs_text
obs_ordinal
obs_datetime
obs_boolean
obs_proportion
obs_identifier
obs_duration
```

That is much better than:

```text
entity_id | attribute | value_text
```

The attached schema’s design of separate tables per DV class preserves type fidelity, avoids a single generic `TEXT` value, and keeps units, code systems, ordinal scales, and timestamps explicit.

------

### C. Concept anchoring is essential

This is one of the most important design choices.

A blood pressure reading is not just two rows:

```text
SYSTOLIC_BP = 120
DIASTOLIC_BP = 80
```

It is one clinical event:

```text
BLOOD_PRESSURE instance
  - systolic
  - diastolic
  - body position
  - cuff site
  - method
  - observation time
```

The attached schema correctly introduces `concept_definition`, `concept_attribute_map`, and `concept_instance` to make this grouping explicit.

------

### D. Selective projection is correct

Not all JSON fields should become EAV rows.

Use this tiering:

| Tier   | Storage                   | Example                                      |
| ------ | ------------------------- | -------------------------------------------- |
| Tier 0 | JSON only                 | layout fields, rarely queried comments       |
| Tier 1 | indexed document metadata | chief complaint, document type               |
| Tier 2 | typed EAV                 | BP, pain score, GCS, SpO2                    |
| Tier 3 | operational tables        | latest vitals, allergy list, active problems |
| Tier 4 | BI/AI marts               | daily patient features, longitudinal vitals  |

The attached schema’s `is_projected` flag and `projection_rationale` are important. They turn “we only project important fields” into a governed, auditable decision.

------

### E. Bitemporal modeling is mandatory

This should be non-negotiable.

Every clinical fact needs at least:

```text
observation_datetime  -- when the clinical event happened
recorded_at           -- when it was entered
valid_from            -- when this row version became valid
valid_to              -- when superseded or voided
row_status            -- ACTIVE / SUPERSEDED / VOIDED
```

This is critical for audit, correction handling, longitudinal analysis, and AI model training. The Claude review correctly flags this as a major gap if it is not explicit, and the attached schema fills that gap with temporal columns and correction patterns.

For MySQL, prefer `DATETIME(6)` stored in UTC plus optional timezone metadata, instead of copying PostgreSQL `TIMESTAMPTZ`. MySQL converts `TIMESTAMP` values between session time zone and UTC, while `DATETIME` does not behave the same way. ([dev.mysql.com](https://dev.mysql.com/doc/refman/en/datetime.html?utm_source=chatgpt.com))

------

# 3. Critical design correction

## Add `concept_instance_id` to every EAV row

The attached schema gives EAV rows a `concept_id` and `document_id`, and the query joins observations to `concept_instance` using those fields. That is not enough for hospital documentation.

Why? A single document can contain multiple instances of the same concept.

Example:

```text
Nursing flowsheet document
  - BP at 08:00
  - BP at 10:00
  - BP at 12:00
```

If `concept_instance` has:

```sql
UNIQUE (document_id, concept_id)
```

then the model cannot safely represent repeated concept instances within one document.

## Recommended change

Use:

```text
concept_instance_id
```

as the actual grouping key.

Each `obs_*` table should include:

```sql
concept_instance_id BINARY(16) NOT NULL
```

Then one document can contain many instances of the same concept:

```text
document_id = D1
concept = BLOOD_PRESSURE

concept_instance_id = BP1 at 08:00
concept_instance_id = BP2 at 10:00
concept_instance_id = BP3 at 12:00
```

Replace:

```sql
UNIQUE (document_id, concept_id)
```

with something like:

```sql
UNIQUE (document_id, concept_id, source_group_path, group_ordinal)
```

or:

```sql
UNIQUE (document_id, concept_id, observation_datetime, group_ordinal)
```

depending on how repeated groups are represented in JSON.

This is probably the most important schema adjustment before implementation.

------

# 4. MySQL implementation adjustments

The attached schema is written in PostgreSQL style. For MySQL, translate it deliberately.

| PostgreSQL-style item                              | MySQL direction                                           |
| -------------------------------------------------- | --------------------------------------------------------- |
| `UUID`                                             | `BINARY(16)` preferred, or `CHAR(36)` for simplicity      |
| `JSONB`                                            | `JSON`                                                    |
| `TIMESTAMPTZ`                                      | `DATETIME(6)` UTC + timezone metadata if needed           |
| partial indexes with `WHERE row_status = 'ACTIVE'` | composite indexes including `row_status`                  |
| generated date partition columns                   | supported, but design carefully                           |
| `CHECK` constraints                                | supported in modern MySQL 8.x; use for simple validations |
| partitioned tables with foreign keys               | avoid this combination in MySQL/InnoDB                    |

A major MySQL-specific issue: partitioned InnoDB tables do not support foreign keys, so you must choose between physical FK enforcement and native partitioning for very large observation tables. ([dev.mysql.com](https://dev.mysql.com/doc/en/partitioning-limitations.html?utm_source=chatgpt.com))

Recommended practical approach:

| Table type                 | Recommendation                                               |
| -------------------------- | ------------------------------------------------------------ |
| Metadata tables            | Use full foreign keys                                        |
| `clinical_document`        | Use FK to metadata if not partitioned                        |
| High-volume `obs_*` tables | Consider no physical FK if partitioning is required; enforce referential integrity in projection service |
| Operational read models    | Use indexes and constraints optimized for workload           |
| BI/AI marts                | Usually no FK required; generated from governed pipelines    |

For JSON indexing, use generated columns for frequently filtered JSON paths, because MySQL does not index JSON columns directly. ([dev.mysql.com](https://dev.mysql.com/doc/refman/8.4/en/create-table.html?utm_source=chatgpt.com))

------

# 5. Recommended core MySQL table groups

## 5.1 Metadata registry

Minimum tables:

```text
form_version
form_field
attribute_catalog
clinical_concept
concept_attribute_map
projection_rule
value_set
unit_catalog
terminology_map
```

Purpose:

```text
Defines what can be captured,
what can be projected,
how it maps to concepts,
and how it should be interpreted.
```

This must be built before serious form development.

------

## 5.2 Canonical document tables

```text
clinical_document
clinical_document_version
clinical_document_audit
projection_run
projection_error
```

Recommended `clinical_document` fields:

```text
document_id
patient_id
encounter_id
form_version_id
document_status
authored_at_utc
submitted_at_utc
canonical_json
json_hash
projection_status
projected_at_utc
projection_error
created_by
created_at_utc
```

The attached schema’s `projection_status` concept is very important because it makes failed projections visible, alertable, and replayable.

------

## 5.3 Concept instance table

This table is the event anchor.

```text
concept_instance
```

Recommended fields:

```text
concept_instance_id
document_id
patient_id
encounter_id
concept_id
concept_version
source_group_path
group_ordinal
observation_datetime_utc
recorded_at_utc
valid_from_utc
valid_to_utc
row_status
expected_attribute_count
projected_attribute_count
is_complete
created_at_utc
```

This becomes the parent for all grouped observations.

------

## 5.4 Typed EAV observation tables

Minimum set:

```text
obs_quantity
obs_coded
obs_text
obs_ordinal
obs_datetime
obs_boolean
obs_proportion
```

Every table should include the same header:

```text
obs_id
concept_instance_id
document_id
patient_id
encounter_id
attribute_id
concept_id
observation_datetime_utc
recorded_at_utc
valid_from_utc
valid_to_utc
row_status
data_quality_flag
source_json_path
created_at_utc
created_by
```

Then each table adds type-specific columns.

For example:

```text
obs_quantity
  - magnitude
  - magnitude_status
  - unit_code
  - normalized_magnitude
  - normalized_unit_code
  - normal_range_low
  - normal_range_high

obs_coded
  - code_value
  - code_display
  - terminology_system
  - terminology_version
  - preferred_term

obs_ordinal
  - ordinal_value
  - ordinal_label
  - scale_name
  - scale_min
  - scale_max
```

------

# 6. Operational read model strategy

Do not use EAV directly for common EMR screens.

Use EAV as the **clinical fact ledger**, then project into operational tables.

Examples:

```text
patient_latest_vital
patient_vital_bp
patient_vital_sign_event
patient_allergy
patient_problem
patient_diagnosis
patient_score_result
patient_flowsheet_observation
encounter_clinical_summary
```

## Important governance change

Do not wait until after forms are fully developed to think about operational tables.

Instead, define **operational query contracts** during form design.

Example for vitals:

```text
The vitals module must answer:
- latest BP per patient
- last 50 BP readings
- BP trend by encounter
- abnormal BP alerts
- vitals captured in ER triage
- vitals captured by nursing flowsheet
```

Then decide which operational tables are needed.

This resolves the weakness identified in the attached review: if operational tables are designed too late, teams will start querying EAV directly for workloads it is not optimized to serve.

------

# 7. Projection pipeline design

The projection pipeline should be a first-class subsystem.

## 7.1 Trigger model

Recommended:

```text
1. Clinician saves/signs document.
2. Document is stored as canonical JSON.
3. clinical_document.projection_status = PENDING.
4. Projection worker reads PENDING documents.
5. Worker validates form version and schema hash.
6. Worker applies projection rules.
7. Worker creates concept_instance rows.
8. Worker creates typed obs_* rows.
9. Worker updates operational read models.
10. Worker marks document PROJECTED or FAILED.
```

## 7.2 Projection states

Use:

```text
PENDING
PROJECTING
PROJECTED
FAILED
STALE
REPROJECTED
```

## 7.3 Required controls

| Control               | Purpose                                         |
| --------------------- | ----------------------------------------------- |
| projection manifest   | shows what fields are projected and why         |
| projection run log    | records each projection attempt                 |
| dead-letter queue     | stores failed documents                         |
| replay function       | reprocesses old JSON after rule changes         |
| reconciliation report | compares JSON document count vs projected facts |
| idempotency key       | prevents duplicate projection rows              |
| schema hash check     | prevents wrong rule/version mapping             |

The attached schema’s projection manifest is a good governance tool because it exposes projected and non-projected fields, their rationale, concept mapping, and effective dates.

------

# 8. BI and AI readiness plan

Do not make data scientists pivot raw EAV repeatedly.

Create curated analytical structures.

## 8.1 Analytical layers

| Layer                   | Shape                                      | Use                     |
| ----------------------- | ------------------------------------------ | ----------------------- |
| observation long table  | one row per patient-concept-attribute-time | longitudinal research   |
| concept event table     | one row per concept instance               | event sequence modeling |
| vitals wide table       | one row per vital-sign event               | dashboards, reporting   |
| daily feature table     | one row per patient per day                | ML feature engineering  |
| encounter summary table | one row per encounter                      | operational analytics   |
| cohort tables           | study-specific                             | registry and research   |

## 8.2 Point-in-time correctness

AI feature tables must support:

```text
as_of_datetime
feature_observation_window
feature_available_datetime
source_document_id
source_concept_instance_id
```

This prevents leakage, especially when corrected or late-entered data exists.

Example:

```text
For a sepsis model at 10:00,
do not include a lab result observed at 09:30
if it was only recorded or available at 11:15.
```

That is why `observation_datetime`, `recorded_at`, `valid_from`, and `valid_to` matter.

------

# 9. Implementation roadmap

## Phase 0 — Architecture decisions

Finalize these before building tables:

```text
- MySQL version and deployment model
- UUID strategy: BINARY(16) vs CHAR(36)
- UTC datetime convention
- JSON schema governance
- terminology systems to support
- unit normalization standard
- partitioning vs physical FK enforcement
- operational read model ownership
```

Deliverables:

```text
Architecture decision record
Clinical data model standards
Naming conventions
Datetime and timezone standard
Terminology and unit policy
```

------

## Phase 1 — Metadata foundation

Build:

```text
form_version
form_field
attribute_catalog
clinical_concept
concept_attribute_map
projection_rule
value_set
unit_catalog
terminology_map
```

Acceptance criteria:

```text
- Every form field has a stable field code.
- Every projectable field has a JSON path.
- Every projected field has a DV class.
- Every projected field has a projection rationale.
- Every concept has a version.
- Every concept defines required and optional attributes.
- Every concept defines whether repeat instances are allowed.
```

------

## Phase 2 — Canonical JSON document store

Build:

```text
clinical_document
clinical_document_version
clinical_document_audit
```

Acceptance criteria:

```text
- JSON is stored in full.
- JSON hash is generated.
- Form version and schema hash are stored.
- Document lifecycle is explicit: DRAFT, SIGNED, AMENDED, VOIDED.
- Projection status is explicit.
- Old document versions are not lost.
```

------

## Phase 3 — Projection engine

Build the application service or ETL worker that converts JSON to typed facts.

Acceptance criteria:

```text
- Projection is idempotent.
- Projection is rule-versioned.
- Projection failure is visible.
- Failed documents are replayable.
- Projection output is traceable to JSON path.
- Projection creates concept instances first, then obs rows.
- Reprojection can be run for old documents after new rules are approved.
```

------

## Phase 4 — Typed EAV clinical fact store

Build:

```text
concept_instance
obs_quantity
obs_coded
obs_text
obs_ordinal
obs_datetime
obs_boolean
obs_proportion
obs_correction_audit
```

Critical implementation rule:

```text
Every obs_* row must reference concept_instance_id.
```

Acceptance criteria:

```text
- Typed values are not stored as generic strings.
- Units are normalized.
- Codes preserve terminology system and version.
- All facts carry observation time and recorded time.
- Corrections supersede rows instead of overwriting them.
- Active rows are queryable efficiently by patient, concept, attribute, and time.
```

------

## Phase 5 — Operational read models

Start with high-value domains:

```text
Vitals
Allergies
Problems
Diagnoses
Scores
Nursing flowsheets
Clinical summaries
```

For vitals, build:

```text
patient_vital_event
patient_vital_bp
patient_latest_vital
```

Acceptance criteria:

```text
- EMR screens do not need complex EAV pivots.
- Latest values are fast.
- Last N readings are fast.
- Operational tables reference source concept_instance_id.
- Operational tables can be regenerated from EAV.
```

------

## Phase 6 — Governance and observability

Build:

```text
projection_manifest
projection_run_log
projection_error_log
data_quality_issue
data_reconciliation_report
metadata_change_log
```

Monitor:

```text
- documents pending projection
- projection failures
- stale projections
- incomplete concept instances
- missing required attributes
- invalid units
- invalid codes
- abnormal volume changes by form/concept
```

Acceptance criteria:

```text
- No silent projection failures.
- Clinical informatics can see what is and is not projected.
- Data engineering can replay failed or stale projections.
- Audit can trace every EAV row back to source JSON.
```

------

## Phase 7 — BI and AI marts

Build:

```text
mart_observation_long
mart_concept_event
mart_vitals_wide
mart_encounter_summary
mart_patient_daily_features
mart_patient_timeline_event
```

Acceptance criteria:

```text
- BI users do not query raw EAV.
- AI users get point-in-time correct datasets.
- Feature tables preserve source lineage.
- Corrections are handled by valid_from / valid_to logic.
- Concepts become reusable feature groups.
```

------

# 10. Priority risk register

| Risk                                  | Severity    | Mitigation                                        |
| ------------------------------------- | ----------- | ------------------------------------------------- |
| JSON schema drift                     | High        | schema hash, form versioning, validation          |
| EAV query complexity                  | High        | concept instances, operational read models, marts |
| repeated concept instances mishandled | High        | add `concept_instance_id` to obs rows             |
| operational tables delayed            | High        | define query contracts during form design         |
| silent projection failures            | High        | projection status, dead-letter queue, replay      |
| weak temporal modeling                | High        | observation, recorded, valid-from, valid-to       |
| unit inconsistency                    | High        | unit catalog and normalization                    |
| terminology inconsistency             | Medium/High | terminology map and versioning                    |
| overprojection                        | Medium      | projection rationale and governance               |
| underprojection                       | Medium      | replay from canonical JSON                        |
| MySQL partition/FK conflict           | Medium/High | choose FK enforcement or partitioning per table   |

------

# 11. Recommended first implementation slice

Start with **Vitals**, especially Blood Pressure, because it exercises almost every pattern:

```text
- quantity values
- grouped concept instance
- repeated observations
- operational latest-value table
- longitudinal trend
- unit normalization
- correction handling
- BI/AI usefulness
```

Minimum pilot scope:

```text
Forms:
- Nursing Vitals
- ER Triage

Concepts:
- BLOOD_PRESSURE
- HEART_RATE
- RESPIRATORY_RATE
- TEMPERATURE
- SPO2
- PAIN_SCORE

Operational tables:
- patient_vital_event
- patient_vital_bp
- patient_latest_vital

Marts:
- mart_vitals_long
- mart_vitals_wide
```

This pilot will validate whether the architecture works before expanding to more complex domains like neurological exam, intake/output, obstetrics, oncology, or surgical documentation.

------

# Final recommendation

Adopt the architecture, but formalize it as:

```text
Canonical JSON document store
+ governed metadata registry
+ versioned projection engine
+ typed EAV clinical fact ledger
+ concept-instance anchoring
+ operational read models
+ BI/AI marts
```

The main design change I would make before implementation is:

```text
Do not group EAV rows only by document_id + concept_id.
Use concept_instance_id everywhere.
```

That one adjustment makes the model safe for repeated measurements, flowsheets, serial assessments, and longitudinal AI use.