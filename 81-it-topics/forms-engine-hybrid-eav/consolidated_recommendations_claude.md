# Clinical Registry & EMR Architecture Assessment

## Forms Engine and EAV Component Design

------

## Strategic Context

Building a system analogous to OpenMRS or OpenClinica requires a carefully balanced architecture that reconciles **schema flexibility** (the EAV promise) with **query performance** (the relational reality). The following assessment addresses each layer with technical precision and implementation-grade guidance.

------

## Presentation Layer

### Forms Rendering

The rendering engine is your user-facing contract. It must be dynamic, offline-capable, and clinically safe. The two dominant paradigms are **schema-driven rendering** (where a JSON/XML schema defines the form) and **widget-based composition** (where components are assembled at runtime).

For a global health context, you must prioritize low-bandwidth resilience. React-based renderers like **react-jsonschema-form**, **Enketo** (used in OpenMRS), or a custom renderer built on **Angular Reactive Forms** are all viable, but Enketo's XForms compatibility gives it an edge in offline, resource-limited settings. The renderer must handle skip logic, validation rules, calculated fields, and multi-language label resolution at render time, not at submission time.

### Forms JSON Schema

The schema is the intellectual core of your forms engine. A production-grade schema for a clinical registry should extend JSON Schema Draft 7 or later, layering clinical semantics on top. A representative structure looks like this:

```json
{
  "formId": "hts-001",
  "version": "2.3.1",
  "encounterType": "HIV_TESTING",
  "locale": "en",
  "pages": [
    {
      "pageId": "demographics",
      "label": "Patient Demographics",
      "sections": [
        {
          "sectionId": "id-block",
          "questions": [
            {
              "id": "q_dob",
              "type": "date",
              "label": "Date of Birth",
              "concept": "CIEL:1533",
              "required": true,
              "validators": [
                { "type": "date", "allowFutureDates": false }
              ],
              "historicalExpression": "patient.birthdate"
            },
            {
              "id": "q_hiv_result",
              "type": "select",
              "label": "HIV Test Result",
              "concept": "CIEL:159427",
              "answerConcepts": [
                { "label": "Positive", "value": "CIEL:703" },
                { "label": "Negative", "value": "CIEL:664" },
                { "label": "Indeterminate", "value": "CIEL:1138" }
              ],
              "hide": {
                "hideWhenExpression": "isEmpty(q_consent_given) || q_consent_given !== 'CIEL:1065'"
              }
            }
          ]
        }
      ]
    }
  ]
}
```

The key design principles here are concept binding (every question maps to a terminology concept), expression-based skip logic, and versioned schema identity. Schema versioning is non-negotiable for longitudinal registries — you must know which form version generated which data record.

### Forms Data Model — Nested Forms

Nested forms represent the most architecturally consequential decision in your forms engine. They correspond to repeating groups in clinical data: multiple medication entries, multiple lab results, multiple family history entries. The data model must distinguish between a **parent encounter** and its **child observations**.

```
FormSchema (1)
    └── Page (N)
          └── Section (N)
                └── Question (N)
                      ├── AnswerOption (N)      ← for select types
                      └── ChildFormSchema (0..1) ← for nested/repeating groups
```

The critical implementation risk is infinite nesting. Enforce a maximum nesting depth of 3 at the schema validation layer. Beyond that, you are modeling a relational domain problem, not a forms problem, and should use a linked encounter pattern instead.

------

## Storage Layer

### Terminology Services

Terminology is the semantic foundation. Without it, your EAV store becomes an uninterpretable bag of values. You need a **local concept dictionary** that mirrors or extends established vocabularies: SNOMED CT, LOINC, ICD-10/11, and CIEL for the OpenMRS context.

The terminology service must expose three capabilities: concept lookup by code and system, hierarchy traversal (parent/child/ancestor), and mapping resolution (e.g., map a local code to SNOMED). A microservice backed by PostgreSQL with the **pg_trgm** extension for fuzzy search, or a dedicated terminology server like **HAPI FHIR's terminology module** or **Snowstorm**, is the right tool here.

```sql
CREATE TABLE concept (
    concept_id      UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    concept_code    VARCHAR(50) NOT NULL,
    vocabulary_id   VARCHAR(30) NOT NULL,  -- 'CIEL', 'SNOMED', 'LOINC'
    display_name    TEXT NOT NULL,
    data_type       VARCHAR(20),           -- 'Numeric', 'Coded', 'Text', 'Date'
    retired         BOOLEAN DEFAULT FALSE,
    UNIQUE (concept_code, vocabulary_id)
);

CREATE TABLE concept_answer (
    concept_id      UUID REFERENCES concept(concept_id),
    answer_concept_id UUID REFERENCES concept(concept_id),
    sort_weight     NUMERIC(5,2),
    PRIMARY KEY (concept_id, answer_concept_id)
);

CREATE TABLE concept_name (
    concept_name_id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    concept_id      UUID REFERENCES concept(concept_id),
    name            TEXT NOT NULL,
    locale          VARCHAR(10) NOT NULL,
    locale_preferred BOOLEAN DEFAULT FALSE,
    name_type       VARCHAR(20)  -- 'FULLY_SPECIFIED', 'SHORT', 'SYNONYM'
);
```

### EAV Metadata

The EAV metadata layer defines what can be stored before anything is stored. This is your schema-on-write enforcement point. Each attribute must be typed, concept-bound, and belong to a form version context.

```sql
CREATE TABLE attribute_type (
    attribute_type_id   UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    concept_id          UUID REFERENCES concept(concept_id),
    datatype            VARCHAR(30) NOT NULL,
    -- 'Boolean','Coded','Text','Numeric','DateTime','Complex'
    min_occurs          INT DEFAULT 0,
    max_occurs          INT DEFAULT 1,  -- NULL = unbounded (repeating)
    form_schema_id      UUID,
    form_schema_version VARCHAR(20),
    created_at          TIMESTAMPTZ DEFAULT now()
);
```

### Hybrid EAV Tables

Pure EAV collapses under analytical load. The hybrid pattern partitions storage by datatype, preserving type safety and enabling partial indexing strategies. This is the pattern used effectively by OpenMRS and is the correct approach for any production registry.

```sql
-- Core encounter anchor
CREATE TABLE encounter (
    encounter_id    UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    patient_id      UUID NOT NULL REFERENCES patient(patient_id),
    encounter_type  UUID NOT NULL REFERENCES concept(concept_id),
    form_schema_id  UUID,
    location_id     UUID,
    encounter_date  DATE NOT NULL,
    voided          BOOLEAN DEFAULT FALSE,
    created_by      UUID,
    created_at      TIMESTAMPTZ DEFAULT now()
);

-- Typed observation partitions
CREATE TABLE obs_numeric (
    obs_id          UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    encounter_id    UUID REFERENCES encounter(encounter_id),
    concept_id      UUID REFERENCES concept(concept_id),
    value_numeric   NUMERIC(20,5),
    units           VARCHAR(20),
    obs_date        TIMESTAMPTZ,
    voided          BOOLEAN DEFAULT FALSE
);

CREATE TABLE obs_coded (
    obs_id          UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    encounter_id    UUID REFERENCES encounter(encounter_id),
    concept_id      UUID REFERENCES concept(concept_id),
    value_concept_id UUID REFERENCES concept(concept_id),
    obs_date        TIMESTAMPTZ,
    voided          BOOLEAN DEFAULT FALSE
);

CREATE TABLE obs_text (
    obs_id          UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    encounter_id    UUID REFERENCES encounter(encounter_id),
    concept_id      UUID REFERENCES concept(concept_id),
    value_text      TEXT,
    obs_date        TIMESTAMPTZ,
    voided          BOOLEAN DEFAULT FALSE
);

-- Nested/repeating group anchor
CREATE TABLE obs_group (
    obs_group_id    UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    encounter_id    UUID REFERENCES encounter(encounter_id),
    group_concept_id UUID REFERENCES concept(concept_id),
    parent_group_id UUID REFERENCES obs_group(obs_group_id), -- self-ref for nesting
    sort_weight     INT,
    voided          BOOLEAN DEFAULT FALSE
);

-- Link member observations to their group
ALTER TABLE obs_numeric ADD COLUMN obs_group_id UUID REFERENCES obs_group(obs_group_id);
ALTER TABLE obs_coded   ADD COLUMN obs_group_id UUID REFERENCES obs_group(obs_group_id);
ALTER TABLE obs_text    ADD COLUMN obs_group_id UUID REFERENCES obs_group(obs_group_id);
```

Critical indexing strategy for the EAV layer:

```sql
CREATE INDEX idx_obs_numeric_concept ON obs_numeric(concept_id) WHERE NOT voided;
CREATE INDEX idx_obs_coded_value     ON obs_coded(value_concept_id) WHERE NOT voided;
CREATE INDEX idx_obs_encounter       ON obs_numeric(encounter_id);
-- Partial indexes on high-cardinality concepts used in cohort queries
CREATE INDEX idx_hiv_result ON obs_coded(encounter_id, value_concept_id)
    WHERE concept_id = 'your-hiv-result-concept-uuid' AND NOT voided;
```

------

## Analytics Layer

The EAV storage layer is deliberately normalized for write integrity. The analytics layer must denormalize deliberately for read performance. These are separate concerns that should never be conflated.

### Indexed Data Marts

Data marts are patient-centric, wide tables that flatten the EAV store for a specific clinical domain. They are the primary interface for reporting, cohort queries, and dashboard aggregation.

```sql
-- HIV Testing Registry Data Mart
CREATE TABLE mart_hiv_testing (
    patient_id          UUID,
    encounter_id        UUID,
    encounter_date      DATE,
    location_id         UUID,
    age_at_encounter    INT,
    sex                 VARCHAR(10),
    test_result         VARCHAR(20),   -- denormalized concept name
    test_result_code    VARCHAR(50),
    cd4_count           NUMERIC,
    on_art              BOOLEAN,
    linked_to_care      BOOLEAN,
    reporting_period    VARCHAR(7),    -- 'YYYY-MM' for partitioning
    created_at          TIMESTAMPTZ DEFAULT now(),
    PRIMARY KEY (patient_id, encounter_id)
) PARTITION BY RANGE (encounter_date);

CREATE INDEX idx_mart_hiv_period   ON mart_hiv_testing(reporting_period);
CREATE INDEX idx_mart_hiv_result   ON mart_hiv_testing(test_result_code);
CREATE INDEX idx_mart_hiv_location ON mart_hiv_testing(location_id, encounter_date);
```

### Generated Columns

Generated columns provide a safe, zero-cost computation layer within the storage schema itself. They are ideal for clinical calculations that must be consistent regardless of which application layer writes the data.

```sql
ALTER TABLE patient ADD COLUMN birth_date DATE NOT NULL;

ALTER TABLE patient ADD COLUMN age_years INT
    GENERATED ALWAYS AS (
        EXTRACT(YEAR FROM AGE(CURRENT_DATE, birth_date))::INT
    ) STORED;

-- In obs_numeric, flag abnormal values inline
ALTER TABLE obs_numeric ADD COLUMN reference_range_low  NUMERIC;
ALTER TABLE obs_numeric ADD COLUMN reference_range_high NUMERIC;
ALTER TABLE obs_numeric ADD COLUMN is_abnormal BOOLEAN
    GENERATED ALWAYS AS (
        value_numeric < reference_range_low OR value_numeric > reference_range_high
    ) STORED;
```

### Generated Tables and Views

Materialized views are the workhorses of the analytics layer. They should be refreshed on a defined cadence — nightly for operational dashboards, weekly for aggregate indicator reports.

```sql
-- Patient-level cohort summary
CREATE MATERIALIZED VIEW mv_patient_hiv_summary AS
SELECT
    e.patient_id,
    MAX(e.encounter_date)                           AS last_encounter_date,
    COUNT(DISTINCT e.encounter_id)                  AS total_encounters,
    MAX(n.value_numeric) FILTER (
        WHERE n.concept_id = 'cd4-count-concept-uuid'
    )                                               AS latest_cd4,
    BOOL_OR(c.value_concept_id = 'on-art-yes-concept-uuid') AS ever_on_art
FROM encounter e
LEFT JOIN obs_numeric n ON n.encounter_id = e.encounter_id AND NOT n.voided
LEFT JOIN obs_coded   c ON c.encounter_id = e.encounter_id AND NOT c.voided
WHERE NOT e.voided
GROUP BY e.patient_id
WITH DATA;

CREATE UNIQUE INDEX ON mv_patient_hiv_summary(patient_id);

-- Facility-level monthly aggregate
CREATE MATERIALIZED VIEW mv_facility_monthly_indicators AS
SELECT
    location_id,
    reporting_period,
    COUNT(*)                                        AS total_tests,
    COUNT(*) FILTER (WHERE test_result_code = 'CIEL:703') AS positive_tests,
    ROUND(
        COUNT(*) FILTER (WHERE test_result_code = 'CIEL:703') * 100.0
        / NULLIF(COUNT(*), 0), 2
    )                                               AS positivity_rate
FROM mart_hiv_testing
GROUP BY location_id, reporting_period
WITH DATA;
```

### Triggers and Stored Procedures

Triggers provide write-time automation for audit trails, data mart population, and referential integrity enforcement that cannot be expressed in constraints alone. They must be used judiciously — over-triggering creates opaque, hard-to-debug data pipelines.

**Appropriate uses:**

```sql
-- Audit trail trigger: capture every obs change
CREATE TABLE obs_audit_log (
    log_id          UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    obs_id          UUID NOT NULL,
    obs_table       VARCHAR(30) NOT NULL,
    operation       CHAR(1) NOT NULL,   -- 'I', 'U', 'D'
    old_value       JSONB,
    new_value       JSONB,
    changed_by      UUID,
    changed_at      TIMESTAMPTZ DEFAULT now()
);

CREATE OR REPLACE FUNCTION fn_obs_audit()
RETURNS TRIGGER LANGUAGE plpgsql AS $$
BEGIN
    INSERT INTO obs_audit_log(obs_id, obs_table, operation, old_value, new_value, changed_by)
    VALUES (
        COALESCE(NEW.obs_id, OLD.obs_id),
        TG_TABLE_NAME,
        LEFT(TG_OP, 1),
        CASE WHEN TG_OP != 'INSERT' THEN to_jsonb(OLD) END,
        CASE WHEN TG_OP != 'DELETE' THEN to_jsonb(NEW) END,
        current_setting('app.current_user_id', true)::UUID
    );
    RETURN NEW;
END;
$$;

CREATE TRIGGER trg_obs_numeric_audit
AFTER INSERT OR UPDATE OR DELETE ON obs_numeric
FOR EACH ROW EXECUTE FUNCTION fn_obs_audit();
-- Stored procedure: refresh data mart on demand or via pg_cron
CREATE OR REPLACE PROCEDURE sp_refresh_hiv_mart(p_from_date DATE DEFAULT NULL)
LANGUAGE plpgsql AS $$
DECLARE
    v_from DATE := COALESCE(p_from_date, CURRENT_DATE - INTERVAL '7 days');
BEGIN
    -- Incremental delete and reinsert for the affected period
    DELETE FROM mart_hiv_testing
    WHERE encounter_date >= v_from;

    INSERT INTO mart_hiv_testing (
        patient_id, encounter_id, encounter_date, location_id,
        test_result_code, test_result, on_art, reporting_period
    )
    SELECT
        e.patient_id,
        e.encounter_id,
        e.encounter_date,
        e.location_id,
        c_result.concept_code,
        cn_result.name,
        EXISTS (
            SELECT 1 FROM obs_coded oc2
            WHERE oc2.encounter_id = e.encounter_id
              AND oc2.concept_id = 'on-art-concept-uuid'
              AND oc2.value_concept_id = 'yes-concept-uuid'
        ),
        TO_CHAR(e.encounter_date, 'YYYY-MM')
    FROM encounter e
    JOIN obs_coded oc ON oc.encounter_id = e.encounter_id
        AND oc.concept_id = 'hiv-result-concept-uuid'
        AND NOT oc.voided
    JOIN concept c_result ON c_result.concept_id = oc.value_concept_id
    JOIN concept_name cn_result ON cn_result.concept_id = c_result.concept_id
        AND cn_result.locale_preferred = TRUE
    WHERE e.encounter_date >= v_from AND NOT e.voided;

    RAISE NOTICE 'HIV mart refreshed from %', v_from;
END;
$$;
```

------

## Architecture Risk Assessment Summary

| Layer             | Primary Risk                    | Mitigation                                                   |
| ----------------- | ------------------------------- | ------------------------------------------------------------ |
| Forms Engine      | Schema drift across versions    | Immutable versioned schemas, never mutate deployed versions  |
| EAV Storage       | Query performance degradation   | Typed partitioning, partial indexes, hybrid mart pattern     |
| Terminology       | Concept fragmentation           | Single concept dictionary, CIEL as the master vocabulary     |
| Data Marts        | Stale analytics                 | Scheduled refresh via pg_cron, incremental procedures        |
| Triggers          | Hidden complexity and deadlocks | Restrict triggers to audit and notifications only; no business logic |
| Stored Procedures | Vendor lock-in                  | Document all procedures, maintain equivalent ORM fallbacks   |

------

## Strategic Recommendation

Build the **storage and terminology layers first** — they are the hardest to migrate later. Adopt PostgreSQL as your primary engine for its native JSONB, generated columns, partitioning, and procedural language support. Design the forms schema as an **immutable, versioned artifact** from day one. Architect your analytics layer as a **separate read replica** fed by the mart refresh procedures, keeping your OLTP and OLAP concerns cleanly separated. This architecture scales from a single-facility deployment in a resource-limited setting to a national aggregate registry with disciplined infrastructure growth, which is precisely the sustainability mandate that global health informatics demands.