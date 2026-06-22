# Clinical Documentation Module — Consolidated Review and Implementation Plan

*Synthesized from: Claude architectural review (May 2026) and ChatGPT architectural review*
*Relates to: EAV Schema Design — MySQL 8.0 (May 2026) and prior Clinical Registry and EMR Architecture (Feb 2026)*
*Database platform confirmed: MySQL 8.0*

---

## Executive Summary

Both reviews independently arrive at the same verdict: **the architecture is sound and appropriate for a hospital-wide EMR.** The core design — canonical JSON as the legal source of record, typed EAV as the clinical fact ledger, concept anchoring as the grouping mechanism, and normalized operational tables as the serving model — is a mature, defensible pattern. No fundamental redesign is required.

What both reviews also agree on is that the architecture's success is entirely dependent on governance discipline, not technical cleverness. The tables and DDL are the easy part. The hard part is the metadata registry, the projection rule engine, and the institutional processes that keep them consistent over time.

This document consolidates both reviews into a single reference, resolves their differences, and provides a phased implementation plan.

---

## Part 1 — Consolidated Architecture Assessment

### 1.1 Layer Role Definitions (Reconciled)

Both reviews propose the same fundamental layer model with slightly different naming. The reconciled definition is:

| Layer | Role | Source of truth for |
|---|---|---|
| Metadata registry | Defines what can be captured, projected, and grouped | Form schemas, concept definitions, projection rules |
| Presentation layer | Renders forms from metadata; captures user input | Nothing — it is a renderer |
| Canonical JSON store | Immutable, full-fidelity clinical document | The legal and clinical record |
| Typed EAV fact store | Queryable projection of selected clinical facts | Longitudinal clinical events |
| Concept instance layer | Groups related EAV facts into clinical units | Clinical meaning and context |
| Operational domain tables | Domain-driven, normalized read models | Fast EMR workflow queries |
| BI / AI marts | Flattened, curated analytical datasets | Longitudinal analysis and ML features |

The key framing that both reviews converge on — and which should be internalized by the entire development team — is:

> Forms are capture instruments. JSON preserves the clinical document. EAV captures reusable clinical facts. Concept anchors preserve meaning and context. Operational tables serve workflows. Analytical marts serve BI and AI.

---

### 1.2 Agreements Across Both Reviews

The following points are raised by both reviews independently. Their convergence elevates them from recommendations to requirements.

**Canonical JSON must be metadata-driven, not free-form.** Every field in the JSON must carry stable metadata: a `field_id`, a `clinical_path`, a `concept_id`, a `dv_type`, a `unit`, and `terminology_binding`. A raw `{"systolic": 120}` is not acceptable. The JSON must be self-describing and replayable. This is a prerequisite for deterministic projection.

**Projection rules must be versioned, deterministic, and replayable.** The same clinical concept (blood pressure, SpO2, GCS) will appear across multiple forms from multiple departments. The projection layer must harmonize these into the same concept. A central concept registry and a versioned projection rule table are not optional. Without them, the EAV layer becomes a per-form artifact dump rather than a clinical fact ledger.

**Typed EAV tables per DV class are the right choice.** Both reviews explicitly reject the generic single-table `attribute | value TEXT` pattern. Using separate tables for `obs_quantity`, `obs_coded`, `obs_ordinal`, etc. preserves type safety, improves index selectivity, and prevents the type drift that makes generic EAV analytically unusable over time.

**Concept/group anchoring is mandatory, not optional.** Clinical values are not meaningful in isolation. Systolic BP without diastolic BP, position, site, and method is incomplete. SpO2 without O2 delivery context is ambiguous. The concept instance (or `group_instance_id` in the ChatGPT framing) is the structural mechanism that preserves clinical meaning. It must be enforced at projection time, not inferred at query time.

**Operational tables must be domain-driven, not form-driven.** This is the sharpest recommendation in both reviews. The original architecture specifies that secondary normalized tables are "defined after the forms are developed" — both reviews flag this as a governance risk. The revised principle: define operational tables from stable clinical domain requirements, then map one or more forms to them. Blood pressure is a domain concept, not a form field.

**All temporal dimensions must be explicit.** Four distinct timestamps are required on every projected clinical fact: `clinical_time` (when the event occurred), `recorded_time` (when it was entered), `document_time` (when the document was authored/signed), and a row-validity window (`valid_from` / `valid_to`) for corrections. Conflating these — particularly relying on document creation time as a proxy for clinical time — will produce incorrect longitudinal analyses and biased ML features.

**The full JSON must be stored and remain replayable.** Because projection is selective by design, fields not projected today may be needed for analysis tomorrow. The ability to add a projection rule and replay historical documents is one of the most important properties of this architecture. It must not be compromised by destructive updates to the JSON store.

---

### 1.3 Differences Between the Two Reviews

The two reviews have two substantive differences, both of which require a resolution decision.

**Difference 1 — Database platform**

The ChatGPT review presents DDL in MySQL (`BIGINT AUTO_INCREMENT`, `ENUM`, `DATETIME`). The Claude review originally used PostgreSQL (`UUID`, `TIMESTAMPTZ`, `GENERATED ALWAYS AS`, `PARTITION BY RANGE`, `JSONB`).

*Resolution:* **MySQL 8.0 is the confirmed platform.** The schema has been fully ported to MySQL 8.0 (see: EAV Schema — MySQL 8.0). The following constraints are accepted as part of this decision and must be understood by the entire development team:

- **`BINARY(16)` UUIDs with swap flag.** Primary keys use `UUID_TO_BIN(UUID(), 1)` on insert and `BIN_TO_UUID(col, 1)` on read. The swap flag reorders time-high bytes for B-tree locality. This must be applied consistently — mixing swapped and un-swapped UUIDs in the same column produces silently wrong joins.
- **`DATETIME(6)` throughout, UTC enforced at application layer.** MySQL `TIMESTAMP` is unsuitable (2038 limit, implicit timezone conversion). All datetime columns use `DATETIME(6)`. UTC discipline is the application's responsibility; set `time_zone = '+00:00'` on every connection.
- **No foreign keys on partitioned tables.** MySQL InnoDB does not support FK constraints on partitioned tables. High-volume EAV tables (`obs_quantity`, `obs_coded`, `obs_ordinal`, `obs_text`, `obs_datetime`, `obs_boolean`, `obs_proportion`, `concept_instance`) are partitioned by `obs_date` with FK enforcement dropped. **Referential integrity on these tables is enforced at the application and projection engine layer** — this is a standing architectural responsibility, not a temporary workaround.
- **Explicit `obs_date` parameter required for partition pruning.** MySQL can only prune partitions when the partition key column appears literally in the `WHERE` clause. The correction stored procedure and the point-in-time as-of query both require `obs_date` as an explicit parameter. Application code must always supply it.
- **No `MATERIALIZED VIEW`.** Replaced by `projection_manifest_cache` table populated by `refresh_projection_manifest()` stored procedure. Refresh must be wired into the deployment pipeline and triggered on every form version or attribute catalog change.
- **`FULLTEXT` indexes incompatible with partitioning.** If full-text search on `obs_text.text_value` is needed, a separate non-partitioned shadow table or an external search index is required. This must be a deliberate design decision before `obs_text` goes to production.
- **`CHECK` constraints enforced from MySQL 8.0.16.** Confirm the exact patch version of the deployment target. `ENUM` column types enforce valid values regardless of patch version and are used throughout for status and flag columns.

**Difference 2 — Hierarchy depth of concept anchoring**

The ChatGPT review proposes a four-level anchor hierarchy: `document_id → section_instance_id → clinical_event_instance_id → panel/group_instance_id`. The Claude review uses a two-level model: `document_id → concept_instance_id`.

*Resolution:* **The two-level model is preferred for initial implementation**, with the option to extend. The four-level hierarchy adds modeling power but also adds join complexity, governance overhead, and risk of inconsistent section/event boundary definitions across forms. The `concept_instance` table already handles the grouping function of `panel/group_instance_id`. The `section_instance` and `clinical_event_instance` levels can be added later as operational table materialization needs become clear. Do not build hierarchy depth that you cannot fill with governed definitions from day one.

---

### 1.4 Gaps Not Fully Addressed by Either Review

These items appear in neither review or only partially — they are included here because they are material to implementation.

**Projection pipeline as a first-class component.** Both reviews describe the projection layer conceptually but neither defines its operational behavior: what triggers it (event-driven vs. scheduled), how it handles partial failures, what the retry and dead-letter strategy is, and how replay is initiated. A document that fails projection silently will not appear in operational queries — in a clinical context, this can affect patient safety workflows. The `projection_status` column on `clinical_document` addresses visibility, but the pipeline behavior must be explicitly designed.

**Unit normalization at projection time.** The ChatGPT review raises unit inconsistency as a risk (weight in kg vs. lb, temperature in °C vs. °F). The resolution is to store both `original_value / original_unit` and `normalized_value / normalized_unit` in `obs_quantity`. Normalization rules (UCUM unit mappings) must be part of the metadata registry, not hardcoded in the projection engine.

**Data quality flagging.** The `data_quality_flag` column (`VALID`, `IMPLAUSIBLE`, `UNVERIFIED`, `CORRECTED`) is present in the schema but neither review addresses the rules that set it. Implausibility rules (e.g., systolic BP > 300 mmHg, weight < 0.5 kg) should be defined in the attribute catalog and applied at projection time. Only rows with `data_quality_flag = 'VALID'` should feed into BI/AI marts.

**AI data leakage prevention.** The `obs_quantity_as_of()` function in the schema provides point-in-time correct queries, but this mechanism must be prominently documented as the required access pattern for any model training pipeline. Ad hoc queries against `v_obs_quantity_active` will produce data leakage in retrospective study designs.

**Projection manifest governance.** The `mv_projection_manifest` view surfaces which fields are projected and which are not. This view should be reviewed by clinical informatics and the data science team at every form release cycle — not just at initial deployment.

---

## Part 2 — Revised Architecture with Reconciled Components

```
┌─────────────────────────────────────────────────────────────┐
│  METADATA REGISTRY                                          │
│  form_version · attribute_catalog · concept_definition      │
│  concept_attribute_map · projection_rule · value_set        │
│  unit_normalization_map                                     │
└───────────────────────────┬─────────────────────────────────┘
                            │ governs
┌───────────────────────────▼─────────────────────────────────┐
│  PRESENTATION LAYER                                         │
│  Metadata-driven form renderer                              │
│  Validates using attribute_catalog rules                    │
│  Saves self-describing canonical JSON                       │
└───────────────────────────┬─────────────────────────────────┘
                            │ writes
┌───────────────────────────▼─────────────────────────────────┐
│  CANONICAL JSON STORE  (clinical_document)                  │
│  Immutable · versioned · full-fidelity                      │
│  projection_status tracks pipeline state                    │
└──────────┬──────────────────────────────┬───────────────────┘
           │ projection engine reads       │ ad-hoc JSON access
           │ (event-driven or scheduled)  │ (unprojected fields)
┌──────────▼──────────────────────────────▼───────────────────┐
│  TYPED EAV CLINICAL FACT STORE                              │
│  concept_instance (anchor)                                  │
│  obs_quantity · obs_coded · obs_ordinal                     │
│  obs_text · obs_datetime · obs_boolean · obs_proportion     │
│  Bitemporal · append-only · quality-flagged                 │
└──────────┬──────────────────────────────┬───────────────────┘
           │                              │
┌──────────▼────────────┐   ┌─────────────▼───────────────────┐
│  OPERATIONAL DOMAIN   │   │  BI / AI ANALYTICAL LAYER       │
│  TABLES               │   │                                 │
│  Domain-driven        │   │  Materialized views             │
│  Not form-driven      │   │  Feature marts                  │
│  Fast read models     │   │  Longitudinal tables            │
│  Module integration   │   │  Point-in-time correct queries  │
└───────────────────────┘   └─────────────────────────────────┘
```

---

## Part 3 — Selective Projection Tiers (Reconciled)

The ChatGPT review introduces a five-tier projection classification that is directly compatible with the `is_projected` flag and `projection_rationale` column in `attribute_catalog`. Adopt this as the formal governance vocabulary:

| Tier | Label | Projected to EAV | Example fields |
|---|---|---|---|
| 0 | Document-only | No | Narrative comments, layout-only fields, signatures |
| 1 | Search / index | No (JSON index) | Chief complaint text, author, service, document type |
| 2 | Clinical facts | Yes — EAV tables | Vitals, scores, symptoms, coded assessments |
| 3 | Operational domain | Yes — operational tables | Active diagnoses, medication orders, allergy list |
| 4 | BI / AI features | Yes — mart tables | Trended BP, sepsis indicators, LKV summaries |

Tier 0 and Tier 1 fields live in the attribute catalog (`is_projected = FALSE`) but remain fully queryable via `canonical_json` JSONB operators. Tier 1 fields may additionally warrant a GIN index on the JSONB column for full-text or key-value search.

---

## Part 4 — Temporal Model: Four Required Timestamps

Both reviews independently flag weak temporal modeling as the highest-risk gap. The four-timestamp model is non-negotiable:

| Timestamp | Column name | Who sets it | Meaning |
|---|---|---|---|
| Clinical time | `observation_datetime` | Clinician | When the clinical event occurred |
| Recorded time | `recorded_at` | System | When the value was entered into the system |
| Document time | `authored_at` on `clinical_document` | Clinician | When the document was signed/finalized |
| Row validity | `valid_from` / `valid_to` | System | When this version of the row is/was current |

**Under no circumstances should `recorded_at` be used as a proxy for `observation_datetime` in longitudinal queries.** The gap between these two can span hours (retrospective nursing charting) or days (delayed procedure documentation). For AI feature extraction, all time-series windows must be anchored to `observation_datetime`.

---

## Part 5 — Minimum Table Set (Reconciled)

This is the complete, reconciled table inventory. It merges the ChatGPT minimum table list with the PostgreSQL DDL from the Claude schema document.

### Metadata registry

```
form_version
attribute_catalog
concept_definition
concept_attribute_map
projection_rule            ← add: versioned mapping from JSON path to concept
value_set                  ← add: controlled vocabulary for coded attributes
unit_normalization_map     ← add: original → UCUM normalized unit mappings
```

### Clinical document store

```
clinical_document          (with projection_status, canonical_json JSONB)
clinical_document_version  ← add: amendment history for mutable documents
```

### Typed EAV clinical fact store

```
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

### Operational domain tables (domain-driven, not form-driven)

```
patient_vital_sign         (general vitals: HR, RR, temperature, SpO2)
patient_vital_bp           (blood pressure panel with context)
patient_latest_vital       (denormalized latest values per concept)
patient_problem            (active problem list)
patient_allergy            (active allergy list)
patient_score_result       (scored assessments: GCS, pain, APGAR, etc.)
patient_flowsheet_row      (nursing flowsheet entries)
patient_io_event           (intake/output)
```

### BI / AI analytical layer

```
mart_patient_observation_long   (longitudinal EAV-to-long format)
mart_patient_vitals_wide        (pre-pivoted vitals per encounter)
mart_encounter_summary          (encounter-level aggregates)
mart_patient_daily_features     (daily feature snapshots for ML)
mart_patient_timeline_event     (ordered clinical event timeline)
```

---

## Part 6 — Implementation Plan

### Phase 0 — Foundations (Weeks 1–4)
*Nothing can be built correctly without these. This phase has no deliverable forms.*

- [ ] Finalize database platform selection (PostgreSQL — see Section 1.3)
- [ ] Deploy metadata registry tables: `form_version`, `attribute_catalog`, `concept_definition`, `concept_attribute_map`
- [ ] Define initial concept vocabulary for the first five clinical domains (vitals, diagnoses, allergies, medications, assessments) — these are the anchor concepts that all forms will map to
- [ ] Establish projection tier governance: every field on every form must be classified Tier 0–4 before the form is deployed
- [ ] Define the four-timestamp data contract: all form schemas must carry `observation_datetime` as a required capture field, distinct from system timestamps
- [ ] Define unit normalization rules for all DV_QUANTITY fields in scope

**Gate: no form goes to development until its concept mappings and projection tier classifications are registered in the metadata registry.**

---

### Phase 1 — Core EAV Infrastructure (Weeks 5–8)
*Deploy the central EAV store and document pipeline.*

- [ ] Deploy `clinical_document` with `projection_status` column and JSONB canonical store
- [ ] Deploy `concept_instance` and all typed EAV tables (`obs_quantity`, `obs_coded`, `obs_ordinal`, `obs_text`, `obs_datetime`, `obs_boolean`, `obs_proportion`)
- [ ] Deploy `obs_correction_audit` and correction stored procedures
- [ ] Implement projection engine (event-triggered on document submission):
  - reads `canonical_json`
  - walks `attribute_catalog` for `is_projected = TRUE` fields
  - applies unit normalization
  - writes concept instances and EAV rows
  - updates `projection_status` to `PROJECTED` or `FAILED`
- [ ] Implement dead-letter alerting on `projection_status = 'FAILED'`
- [ ] Implement JSON replay capability (re-run projection for a document given a new projection rule version)
- [ ] Deploy `v_obs_quantity_active` and `obs_quantity_as_of()` function
- [ ] Deploy `mv_projection_manifest` as a governance monitoring view

**Gate: projection pipeline must achieve 100% projection success rate on synthetic test documents before any clinical form goes live.**

---

### Phase 2 — First Clinical Forms and Operational Tables (Weeks 9–16)
*Deploy the first forms and the operational tables that serve them.*

- [ ] Deploy vitals documentation form (first form — simplest concept set, highest query volume)
- [ ] Validate concept instance projection for `BLOOD_PRESSURE`, `HEART_RATE`, `RESPIRATORY_RATE`, `TEMPERATURE`, `SPO2` panels
- [ ] Deploy `patient_vital_sign`, `patient_vital_bp`, `patient_latest_vital` operational tables
- [ ] Implement operational table refresh triggers (from concept_instance, not from form directly)
- [ ] Deploy nursing assessment form (second form — introduces DV_ORDINAL and DV_CODED_TEXT types)
- [ ] Deploy clinical scores form: GCS, pain scale, APGAR as needed
- [ ] Deploy `patient_score_result` operational table
- [ ] Conduct first projection manifest review with clinical informatics team

**Gate: operational table queries for latest vitals and scores must return results within 200ms on a patient with 24 months of data.**

---

### Phase 3 — Domain Expansion and BI Layer (Weeks 17–24)
*Expand to remaining clinical domains. Begin building analytical layer.*

- [ ] Deploy problem list, allergy, and medication documentation forms
- [ ] Deploy `patient_problem`, `patient_allergy` operational tables
- [ ] Begin BI mart design: `mart_patient_vitals_wide`, `mart_encounter_summary`
- [ ] Implement materialized view refresh pipeline for mart tables
- [ ] Define data quality rule set: implausibility thresholds for all DV_QUANTITY concepts
- [ ] Activate `data_quality_flag` auto-assignment at projection time
- [ ] Deploy `mart_patient_observation_long` as the first longitudinal dataset
- [ ] Document `obs_quantity_as_of()` as the required access pattern for all ML feature extraction

---

### Phase 4 — AI Readiness and Longitudinal Analysis (Weeks 25–32)
*Prepare the analytical layer for data science consumption.*

- [ ] Deploy `mart_patient_daily_features` with first feature set (vitals trends, missingness indicators, latest coded values per concept domain)
- [ ] Deploy `mart_patient_timeline_event` for sequence modeling
- [ ] Validate point-in-time correctness: confirm no future data leakage in retrospective feature extraction
- [ ] Conduct data quality audit: percentage of `data_quality_flag = 'VALID'` rows per concept
- [ ] Define and register feature definitions in a `feature_catalog` table (name, source concept, derivation logic, temporal window)
- [ ] Establish mart refresh SLA and monitoring

---

## Part 7 — Governance Requirements

These are not implementation tasks — they are standing processes that must be established before Phase 1 and maintained indefinitely.

**Concept registry ownership.** One named person or team owns the concept registry. All new projection mappings require their approval. The specific risk being managed: four departments call the same measurement "BP Systolic", "Systolic BP", "SBP", and "Blood Pressure - S" — all must map to the same `concept_id`, and only the concept registry owner can enforce that.

**Projection manifest review cadence.** At every form release, `projection_manifest_cache` must be refreshed (via `CALL refresh_projection_manifest()`) and reviewed by clinical informatics. The review confirms: (a) all new fields are classified to a projection tier, (b) all Tier 2+ fields have concept mappings, (c) no existing projection mappings are broken by the new form version.

**Correction policy.** Corrections are append-only. No EAV row is ever modified after projection. Corrections use the stored procedure, which creates a new row, closes the old one, and writes to `obs_correction_audit`. This policy must be enforced at the application layer and documented in the system's data governance charter.

**Operational table domain ownership.** Operational tables are domain-driven. Each operational table has a named clinical domain owner (e.g., nursing owns `patient_vital_sign`, pharmacy owns the medication operational table). New form-to-operational-table mappings require domain owner approval. This prevents the "each form generates its own schema" failure mode.

**AI access pattern enforcement.** Any data extraction for model training or retrospective study must use the `obs_quantity_as_of()` stored procedure or equivalent point-in-time queries on other DV class tables, not the active-only views. The `p_obs_date` / `p_date_from` / `p_date_to` partition parameters must always be supplied — omitting them causes full partition scans. This rule prevents data leakage and must be included in the data science team's access agreement.

---

## Part 8 — Risk Register

| Risk | Likelihood | Impact | Mitigation |
|---|---|---|---|
| Projection inconsistency across forms | High | High | Central concept registry with mandatory mapping review at every form release |
| Silent projection failures | Medium | High | `projection_status` column + dead-letter alerting; 100% projection success gate before go-live |
| Temporal conflation (recorded ≠ clinical time) | High | High | `observation_datetime` as required capture field; validated at form submission |
| Operational tables become form-specific | High | Medium | Domain-ownership model; operational table contracts defined before form development |
| EAV query performance degradation | Medium | Medium | Indexes on `(patient_id, attribute_id, observation_datetime)`; partition pruning requires explicit `obs_date` in all queries; operational tables as first-level serving model; EAV not exposed directly to UI |
| Unit drift across forms | Medium | High | Unit normalization map in metadata registry; `normalized_value` + `normalized_unit` stored at projection time |
| Context loss (SpO2 without O2 delivery method) | Medium | High | Concept completeness check on `concept_instance.is_complete`; required-attribute enforcement in `concept_attribute_map` |
| Data leakage in ML features | Medium | High | Point-in-time access functions documented and enforced; `valid_from`/`valid_to` always respected in feature extraction |
| JSON schema drift without version registration | Low | High | SHA-256 hash on `form_version`; projection engine rejects documents whose schema hash does not match a registered version |
| FK integrity violation on partitioned EAV tables | Medium | High | Application and projection engine enforce referential integrity; integration tests validate document_id, attribute_id, concept_id on every insert path |
| Partition scan due to missing `obs_date` parameter | High | Medium | Code review gate: all queries on partitioned tables must include explicit `obs_date` / date range predicate; linted in ORM layer |
| Timezone corruption from `TIMESTAMP` misuse | Low | High | `DATETIME(6)` enforced schema-wide; `time_zone = '+00:00'` set on every connection; flagged in code review checklist |
| Partition table becoming unmanageable without maintenance | Medium | Medium | MySQL `EVENT` scheduler automates monthly partition creation; monitored in ops runbook; at least 3 months of future partitions maintained at all times |

---

## Summary

The architecture requires no fundamental redesign. The implementation sequence is:

1. **Metadata registry first** — before any form is designed
2. **Concept vocabulary before operational tables** — domains drive tables, not forms
3. **Projection pipeline as a reliability-critical component** — not an ETL afterthought
4. **Bitemporality as a structural requirement** — four timestamps, always
5. **Governance processes running in parallel with development** — concept ownership, manifest review, correction policy

The two reviews are in agreement on every material point. The one platform difference (MySQL vs. PostgreSQL) is now resolved: **MySQL 8.0** is confirmed. The schema has been fully ported. The two-level concept anchor hierarchy (`document_id → concept_instance`) stands as the starting model, with the option to extend to deeper hierarchy as operational needs become clear.

The primary MySQL-specific obligations that must be embedded in development standards from day one: UTC-only `DATETIME(6)`, swap-flag `BINARY(16)` UUIDs applied consistently, explicit `obs_date` parameters on all partitioned table queries, and application-layer referential integrity enforcement on all EAV tables.
