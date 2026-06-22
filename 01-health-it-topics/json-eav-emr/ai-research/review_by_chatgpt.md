# JSON-EAV model Review

> I want a review of this approach to build a Clinical Documentation Module/Application for hospital-wide EMR.
>
> - There is a Presentation and Canonical JSON Storage layer. This layer allows multi-dimensional clinical forms and saves them to JSON
> - There is a second Ingestion and Domain Modeling layer. This is a projection layer of the canonical JSON.
> - Initial ingestion is through EAV tables. The EAV tables are data-typed according to the different DV classes of OpenEHR.
> - Not all canonical JSON document fields are projected. Only select the important ones, especially those that need to be tracked, computed, and retrieved in other modules.
> - These projected data in EAV tables are grouped or anchored as concepts, so that they can be extracted as a group
> - A secondary set of tables in the ingestion layer is for operational use. They are normalized tables projected directly from JSON, but probably more from the EAV tables. These secondary sets are defined after the forms are developed.

Overall: **this is a strong architecture**, and I’d consider it a practical middle ground between flexible clinical documentation and downstream EMR usability.

The key is to treat the layers differently:

| Layer                         | Role                                                     |
| ----------------------------- | -------------------------------------------------------- |
| Canonical JSON                | Legal/source clinical document and full-fidelity capture |
| EAV ingestion                 | Queryable clinical fact/event store                      |
| Concept/group anchors         | Clinical meaning and context preservation                |
| Normalized operational tables | Fast EMR workflows and module integration                |
| BI/AI marts                   | Flattened, longitudinal, analysis-ready datasets         |

My main recommendation: **make the projection layer highly governed and deterministic**, otherwise the system can become a pile of form-specific JSON plus inconsistent EAV extracts.

------

## 1. Presentation + Canonical JSON layer

This is the right starting point.

For hospital-wide documentation, forms will evolve constantly. JSON gives you flexibility for:

- specialty-specific forms
- multi-dimensional sections
- repeating groups
- conditional fields
- changing documentation templates
- partial or sparse data
- full clinical note preservation

But the JSON must not be “free-form JSON.” It should be **metadata-driven JSON**.

Each captured field should have stable metadata behind it:

```text
template_id
template_version
field_id
clinical_path
concept_id
display_label
data_type
unit
terminology_binding
cardinality
validation_rules
```

For example, a BP field should not just be:

```json
{
  "systolic": 120,
  "diastolic": 80
}
```

It should be traceable to something like:

```json
{
  "field_id": "vital.bp.systolic",
  "concept_id": "systolic_bp",
  "path": "/vitals/blood_pressure/systolic",
  "value": 120,
  "unit": "mmHg",
  "dv_type": "DV_QUANTITY"
}
```

The canonical JSON should also preserve:

- patient
- encounter
- author
- department/location
- document type
- form/template version
- clinical effective time
- recorded time
- amendment status
- signature/finalization state

That makes the JSON usable as both a clinical document and a replayable source for future projections.

------

## 2. Ingestion and domain modeling layer

This is the most important part of your design.

The projection layer should be treated as a **clinical fact extraction layer**, not just an ETL convenience.

A good flow would be:

```text
Clinical Form UI
   ↓
Canonical JSON Document
   ↓
Projection Rules / Mapping Engine
   ↓
Typed EAV Clinical Fact Tables
   ↓
Operational Tables / BI Marts / AI Feature Tables
```

The projection rules need to be versioned. For example:

```text
projection_rule_id
source_template_id
source_template_version
source_json_path
target_concept_id
target_dv_type
target_unit
target_grouping_rule
active_from
active_to
```

This is important because the same clinical idea may appear in several forms:

```text
ER triage BP
Nursing vitals BP
ICU flowsheet BP
Anesthesia intra-op BP
OPD assessment BP
```

All of those may map to the same domain concept:

```text
blood_pressure_panel
  - systolic_bp
  - diastolic_bp
  - mean_arterial_pressure
```

The ingestion layer is where you harmonize that.

------

## 3. Typed EAV using openEHR DV-style classes

This is a good design choice.

The biggest weakness of traditional EAV is that everything becomes a string. That destroys type safety, query performance, validation, and analytics.

Using data-typed EAV tables is much better.

Instead of one generic table like this:

```text
patient_id | attribute | value
```

Use typed clinical fact tables, for example:

```text
clinical_fact_quantity
clinical_fact_coded_text
clinical_fact_boolean
clinical_fact_datetime
clinical_fact_text
clinical_fact_count
clinical_fact_proportion
clinical_fact_ordinal
```

A simplified MySQL-style quantity table might look like:

```sql
CREATE TABLE clinical_fact_quantity (
    fact_id BIGINT PRIMARY KEY AUTO_INCREMENT,
    patient_id BIGINT NOT NULL,
    encounter_id BIGINT NULL,
    document_id BIGINT NOT NULL,
    concept_id BIGINT NOT NULL,
    group_instance_id BIGINT NULL,

    value_decimal DECIMAL(18,6) NOT NULL,
    unit_code VARCHAR(50) NOT NULL,

    clinical_time DATETIME NOT NULL,
    recorded_time DATETIME NOT NULL,

    source_template_id VARCHAR(100) NOT NULL,
    source_template_version VARCHAR(50) NOT NULL,
    source_json_path VARCHAR(500) NOT NULL,

    status ENUM('preliminary','final','amended','entered_in_error') DEFAULT 'final',

    INDEX idx_patient_concept_time (patient_id, concept_id, clinical_time),
    INDEX idx_encounter_concept (encounter_id, concept_id),
    INDEX idx_group_instance (group_instance_id),
    INDEX idx_document (document_id)
);
```

That gives you flexibility while keeping the data queryable.

For coded values:

```sql
CREATE TABLE clinical_fact_coded_text (
    fact_id BIGINT PRIMARY KEY AUTO_INCREMENT,
    patient_id BIGINT NOT NULL,
    encounter_id BIGINT NULL,
    document_id BIGINT NOT NULL,
    concept_id BIGINT NOT NULL,
    group_instance_id BIGINT NULL,

    code_system VARCHAR(100) NOT NULL,
    code_value VARCHAR(100) NOT NULL,
    display_text VARCHAR(255) NOT NULL,

    clinical_time DATETIME NOT NULL,
    recorded_time DATETIME NOT NULL,

    source_template_id VARCHAR(100) NOT NULL,
    source_template_version VARCHAR(50) NOT NULL,
    source_json_path VARCHAR(500) NOT NULL,

    INDEX idx_patient_concept_time (patient_id, concept_id, clinical_time),
    INDEX idx_code (code_system, code_value)
);
```

This is much safer than generic EAV.

------

## 4. Selective projection is correct

You should not project every JSON field into EAV.

That will make the ingestion layer bloated, noisy, and expensive.

A better rule is to classify fields into tiers:

| Tier   | Meaning                       | Example                                          |
| ------ | ----------------------------- | ------------------------------------------------ |
| Tier 0 | Stored only in canonical JSON | narrative comments, layout-only fields           |
| Tier 1 | Search/index fields           | chief complaint, diagnosis text, author, service |
| Tier 2 | Clinical facts                | vitals, scores, symptoms, assessments            |
| Tier 3 | Operational domain tables     | allergy list, problem list, medication orders    |
| Tier 4 | BI/AI curated features        | latest BP, BP trend, sepsis indicators           |

So your principle is right: **only project what needs to be tracked, computed, retrieved, trended, or reused.**

But make sure the unprojected fields remain backfillable later. Since the full JSON is stored, you can later add a projection rule and replay old documents.

That is a major advantage of your design.

------

## 5. Anchoring projected EAV data as concepts is essential

This is probably the most important part of the whole architecture.

Clinical values rarely make sense alone.

For example, systolic and diastolic BP should not just be independent rows:

```text
patient_id | concept       | value
1          | systolic_bp   | 120
1          | diastolic_bp  | 80
```

They need a shared anchor:

```text
blood_pressure_panel_instance_id = 98765
```

Then:

```text
group_instance_id | concept       | value
98765             | systolic_bp   | 120
98765             | diastolic_bp  | 80
98765             | body_position | sitting
98765             | cuff_site     | left arm
98765             | method        | manual
```

This lets you retrieve blood pressure as a clinical event, not as loose values.

You likely need several anchor levels:

```text
document_id
  → section_instance_id
    → clinical_event_instance_id
      → panel/group_instance_id
        → individual facts
```

For example:

```text
Encounter
  └── Nursing Vitals Document
        └── Vitals Section
              └── BP Measurement Instance
                    ├── Systolic
                    ├── Diastolic
                    ├── Position
                    ├── Arm
                    └── Method
```

This is what makes EAV usable for longitudinal analysis.

Without group anchors, EAV becomes analytically dangerous.

------

## 6. Secondary normalized operational tables are necessary

This is also the right idea.

EAV is good for flexible ingestion. It is not ideal for every operational workflow.

Hospital modules need fast, predictable tables for things like:

- latest vital signs
- active allergies
- problem list
- active diagnoses
- medication reconciliation
- nursing flowsheets
- intake/output
- clinical scores
- procedure summaries
- discharge summaries
- referral tracking

For these, normalized projection tables are appropriate.

Example:

```sql
CREATE TABLE patient_vital_bp (
    bp_id BIGINT PRIMARY KEY AUTO_INCREMENT,
    patient_id BIGINT NOT NULL,
    encounter_id BIGINT NULL,
    source_group_instance_id BIGINT NOT NULL,

    systolic DECIMAL(6,2) NOT NULL,
    diastolic DECIMAL(6,2) NOT NULL,
    mean_arterial_pressure DECIMAL(6,2) NULL,

    position_code VARCHAR(50) NULL,
    site_code VARCHAR(50) NULL,
    method_code VARCHAR(50) NULL,

    clinical_time DATETIME NOT NULL,
    recorded_time DATETIME NOT NULL,

    source_document_id BIGINT NOT NULL,

    INDEX idx_patient_time (patient_id, clinical_time),
    INDEX idx_encounter_time (encounter_id, clinical_time)
);
```

Then another table for latest values:

```sql
CREATE TABLE patient_latest_vital (
    patient_id BIGINT NOT NULL,
    concept_group_code VARCHAR(100) NOT NULL,
    source_group_instance_id BIGINT NOT NULL,
    clinical_time DATETIME NOT NULL,

    PRIMARY KEY (patient_id, concept_group_code)
);
```

This avoids expensive EAV queries for common EMR screens.

------

## One thing I would change

You said:

> These secondary sets are defined after the forms are developed.

That is okay for early development, but risky hospital-wide.

I would revise it to:

> Secondary operational tables are defined from stable clinical domain requirements, then mapped from one or more forms.

Forms should not be the main driver of operational tables.

Clinical domains should be.

For example, blood pressure may come from many forms. The operational table should not be tied to one BP form. It should represent the hospital-wide BP domain.

Better mental model:

```text
Forms are capture instruments.
JSON is the source document.
EAV is the clinical fact ledger.
Operational tables are domain projections.
BI/AI tables are analytical projections.
```

Do not let each form generate its own operational schema unless the form is truly isolated.

------

## Main risks

### 1. Projection inconsistency

Different forms may map similar fields differently.

Example:

```text
"BP Systolic"
"Systolic BP"
"SBP"
"Blood Pressure - S"
```

All must map to the same `concept_id`.

You need a central concept registry.

------

### 2. Weak time modeling

Do not rely only on document creation time.

Every clinical fact should distinguish:

```text
clinical_time      -- when the measurement/event happened
recorded_time      -- when it was entered
document_time      -- when the document was created
verified_time      -- when it was signed/validated
```

For AI and longitudinal analysis, `clinical_time` is critical.

------

### 3. Loss of context

A value without context can be misleading.

Example:

```text
SpO2 = 92%
```

You need to know:

```text
room air or oxygen?
oxygen flow rate?
device?
patient position?
clinical setting?
```

Your concept/group anchor design helps solve this, but it needs to be enforced.

------

### 4. EAV query performance

EAV can become slow if every screen queries vertical rows directly.

Mitigation:

- index by `(patient_id, concept_id, clinical_time)`
- index by `group_instance_id`
- partition large fact tables by date or facility if needed
- use generated summary tables
- use materialized operational tables
- avoid joining 10 EAV aliases in live UI screens
- use EAV mostly as ingestion/fact ledger, not always as the final serving model

------

### 5. Type drift and unit inconsistency

For quantities, enforce normalized units.

Example:

```text
weight: kg
temperature: °C
BP: mmHg
height: cm
```

Store both if needed:

```text
original_value
original_unit
normalized_value
normalized_unit
```

This matters for analytics and AI.

------

## Recommended refined architecture

I would structure it like this:

```text
1. Metadata Registry
   - forms
   - templates
   - fields
   - concepts
   - value sets
   - units
   - projection rules

2. Clinical Documentation UI
   - renders forms from metadata
   - validates using metadata
   - saves full canonical JSON

3. Canonical Document Store
   - immutable or versioned JSON documents
   - medico-legal source
   - full fidelity

4. Projection Engine
   - reads JSON
   - applies versioned mapping rules
   - emits clinical facts

5. Typed EAV Clinical Fact Store
   - quantity facts
   - coded facts
   - boolean facts
   - text facts
   - datetime facts
   - grouped by clinical event/concept instance

6. Operational Domain Tables
   - latest vitals
   - active problems
   - allergies
   - scores
   - flowsheet summaries
   - module-specific read models

7. BI / AI Layer
   - flattened wide tables
   - longitudinal event tables
   - feature marts
   - cohort-ready datasets
```

That gives you both flexibility and analytical discipline.

------

## A practical MySQL table set

At minimum, I’d expect these:

```text
clinical_document
clinical_document_version
form_template
form_field
clinical_concept
concept_relationship
projection_rule
clinical_group_instance
clinical_fact_quantity
clinical_fact_coded_text
clinical_fact_boolean
clinical_fact_datetime
clinical_fact_text
clinical_fact_audit
```

Then operational projections:

```text
patient_vital_bp
patient_vital_sign
patient_latest_vital
patient_problem
patient_allergy
patient_score_result
patient_flowsheet_row
```

Then BI/AI projections:

```text
mart_patient_observation_long
mart_patient_vitals_wide
mart_encounter_summary
mart_patient_daily_features
mart_patient_timeline_event
```

------

## Final assessment

Your approach is sound. I would rate it as **architecturally viable and appropriate for a hospital-wide EMR**, with these conditions:

1. **Canonical JSON must be governed by metadata**, not arbitrary form JSON.
2. **Projection rules must be versioned, deterministic, and replayable.**
3. **Typed EAV is a good ingestion model**, but should not be the only serving model.
4. **Concept/group anchors are mandatory**, especially for panels like BP, GCS, APGAR, intake/output, pain scores, and assessments.
5. **Operational normalized tables should be domain-driven**, not form-driven.
6. **BI/AI datasets should be separate curated projections**, not direct ad hoc queries from raw EAV.
7. **Clinical time, context, units, terminology, and provenance must be first-class fields.**

The design is strongest if you frame it this way:

> JSON preserves the clinical document.
> EAV captures reusable clinical facts.
> Concept anchors preserve meaning and context.
> Normalized tables serve operational workflows.
> Analytical marts serve BI and AI.