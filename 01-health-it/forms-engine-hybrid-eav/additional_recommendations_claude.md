# Additional Critical Considerations

There are several important dimensions worth adding that would significantly strengthen the architecture.

---

### 1. Patient Identity Management

This is arguably the most consequential gap in most registry implementations. Without a robust identity layer, everything downstream is compromised by duplicate records and missed linkages.

You need a dedicated **Master Patient Index (MPI)** with probabilistic matching. The core components are a **golden record** strategy, a **match score threshold** table, and a **candidate duplicate queue** for human review. OpenEMPI and HAPI FHIR's Patient $match operation are the reference implementations here. In low-resource settings where national IDs are unreliable, you need a composite matching algorithm that uses demographic attributes weighted by their discriminating power in that population.

```sql
CREATE TABLE patient_identity (
    identity_id     UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    patient_id      UUID REFERENCES patient(patient_id),
    identifier_type VARCHAR(50),  -- 'NATIONAL_ID','ART_NUMBER','FINGERPRINT_REF'
    identifier_value VARCHAR(100),
    is_golden       BOOLEAN DEFAULT FALSE,
    source_system   VARCHAR(50),
    assigning_facility UUID,
    voided          BOOLEAN DEFAULT FALSE
);

CREATE TABLE patient_merge_audit (
    merge_id        UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    winning_id      UUID,
    losing_id       UUID,
    match_score     NUMERIC(5,4),
    merged_by       UUID,
    merged_at       TIMESTAMPTZ DEFAULT now(),
    reason          TEXT
);
```

------

### 2. Multi-Tenancy and Facility Hierarchy

A registry serving multiple facilities, districts, or countries needs structural multi-tenancy from day one, not retrofitted later. The location hierarchy must be modeled explicitly because it drives both data partitioning and aggregate reporting boundaries.

```sql
CREATE TABLE location (
    location_id     UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    name            TEXT NOT NULL,
    location_type   VARCHAR(30),  -- 'COUNTRY','REGION','DISTRICT','FACILITY','WARD'
    parent_id       UUID REFERENCES location(location_id),
    mfl_code        VARCHAR(20),  -- Master Facility List code
    latitude        NUMERIC(9,6),
    longitude       NUMERIC(9,6),
    active          BOOLEAN DEFAULT TRUE
);
```

Row-level security in PostgreSQL is the right enforcement mechanism, ensuring that a facility-level user can never inadvertently query another facility's data, even through a misconfigured application query.

```sql
ALTER TABLE encounter ENABLE ROW LEVEL SECURITY;

CREATE POLICY encounter_facility_isolation ON encounter
    USING (location_id = ANY(
        current_setting('app.accessible_locations')::UUID[]
    ));
```

------

### 3. FHIR Interoperability Layer

Your registry must speak FHIR R4 at its boundary. This is not optional for any system that will exchange data with national health information exchanges, DHIS2, or international donor reporting systems. The architecture decision is whether to use a **FHIR façade** over your existing schema or maintain a **native FHIR resource store** in parallel.

The façade pattern is almost always the right answer for existing registries. You map your internal encounter/obs model to FHIR resources at the API boundary without restructuring your storage. Key mappings are Patient → Patient, Encounter → Encounter, obs_coded/obs_numeric → Observation, and your concept dictionary entries → CodeSystem/ValueSet resources.

HAPI FHIR Server as a standalone JPA server, or a custom FHIR endpoint built with the **fhir.resources** Python library or **firely-net-sdk**, are the two practical implementation paths.

------

### 4. Data Quality Framework

Raw EAV data is notoriously difficult to quality-assure because errors are invisible without concept-aware validation. You need a dedicated data quality layer with three tiers.

**Completeness checks** validate that required concepts are present for a given encounter type. **Plausibility checks** validate that values are within clinically expected ranges for a given patient context — a CD4 count of 50,000 is technically numeric but clinically impossible. **Consistency checks** validate cross-observation logic — a patient marked as deceased should not have encounters after the date of death.

```sql
CREATE TABLE dq_rule (
    rule_id         UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    rule_name       TEXT NOT NULL,
    rule_type       VARCHAR(20),  -- 'COMPLETENESS','PLAUSIBILITY','CONSISTENCY'
    encounter_type  UUID REFERENCES concept(concept_id),
    rule_expression TEXT,         -- SQL or JSONLogic expression
    severity        VARCHAR(10),  -- 'ERROR','WARNING','INFO'
    active          BOOLEAN DEFAULT TRUE
);

CREATE TABLE dq_violation (
    violation_id    UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    rule_id         UUID REFERENCES dq_rule(rule_id),
    encounter_id    UUID,
    patient_id      UUID,
    detected_at     TIMESTAMPTZ DEFAULT now(),
    resolved        BOOLEAN DEFAULT FALSE,
    resolved_by     UUID,
    resolved_at     TIMESTAMPTZ
);
```

------

### 5. Program Enrollment and Care Cascades

Most clinical registries in global health are program-centric — HIV, TB, MCH, Malaria. The concept of **program enrollment** is architecturally distinct from an encounter. A patient is enrolled in a program once, has a current program state that transitions over time, and may have program-specific attributes that are not relevant outside that program context.

```sql
CREATE TABLE program (
    program_id      UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    name            TEXT NOT NULL,
    concept_id      UUID REFERENCES concept(concept_id)
);

CREATE TABLE patient_program (
    patient_program_id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    patient_id      UUID REFERENCES patient(patient_id),
    program_id      UUID REFERENCES program(program_id),
    enrolled_date   DATE NOT NULL,
    completed_date  DATE,
    location_id     UUID,
    outcome_concept UUID  -- 'TRANSFERRED_OUT','DIED','COMPLETED'
);

CREATE TABLE patient_state (
    state_id        UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    patient_program_id UUID REFERENCES patient_program(patient_program_id),
    state_concept   UUID REFERENCES concept(concept_id),
    -- 'ACTIVE_ON_ART','LOST_TO_FOLLOWUP','VIRALLY_SUPPRESSED'
    start_date      DATE NOT NULL,
    end_date        DATE,
    voided          BOOLEAN DEFAULT FALSE
);
```

This state machine model enables accurate cascade analysis — the 95-95-95 HIV targets, TB treatment outcomes, and antenatal care completion rates all depend on accurate state transition tracking.

------

### 6. Offline Synchronization Architecture

In most global health deployment contexts, connectivity is intermittent. Your architecture must treat offline-first as a first-class design constraint, not an edge case. This means every mobile or facility-level client needs a local data store that can operate fully independently and synchronize bidirectionally when connectivity is restored.

The key challenges are **conflict resolution** (two facilities updated the same record while offline), **sync ordering** (foreign key dependencies must be resolved in the correct sequence), and **bandwidth efficiency** (only changed records should transmit). CouchDB/PouchDB replication and a custom implementation using vector clocks and a sync log table are the two architectural patterns. OpenMRS's Sync 2.0 module and the newer OpenMRS 3.x offline architecture are worth studying closely as reference implementations.

------

### 7. Consent and Data Governance

Particularly for registries handling HIV, TB, mental health, or reproductive health data, consent must be modeled explicitly in the data layer, not just enforced at the application layer.

```sql
CREATE TABLE patient_consent (
    consent_id      UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    patient_id      UUID REFERENCES patient(patient_id),
    consent_type    VARCHAR(50),  -- 'TREATMENT','DATA_SHARING','RESEARCH'
    granted         BOOLEAN NOT NULL,
    granted_date    DATE,
    expiry_date     DATE,
    witness_id      UUID,
    form_version    VARCHAR(20),
    revoked         BOOLEAN DEFAULT FALSE,
    revoked_date    DATE
);
```

Data-sharing agreements between facilities and national systems must be modeled at the location hierarchy level, with the consent record serving as the legal anchor for cross-facility data access.

------

## Revised Architecture Completeness Map

| Domain             | Original Assessment | Added Now                     |
| ------------------ | ------------------- | ----------------------------- |
| Forms Engine       | Covered             | —                             |
| EAV Storage        | Covered             | DQ Framework                  |
| Terminology        | Covered             | FHIR CodeSystem mapping       |
| Analytics          | Covered             | —                             |
| Patient Identity   | Missing             | MPI, probabilistic matching   |
| Multi-Tenancy      | Missing             | Location hierarchy, RLS       |
| Interoperability   | Missing             | FHIR R4 façade layer          |
| Program Logic      | Missing             | Enrollment, state machines    |
| Offline Sync       | Missing             | Conflict resolution, sync log |
| Consent/Governance | Missing             | Consent model, data sharing   |

The original assessment was architecturally sound at the component level. These additions address the **systemic and operational realities** that determine whether a registry succeeds in deployment rather than just in design.