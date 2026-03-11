# Master Patient Index (MPI): A Technical and Operational Treatise
**Target Audience:** Health IT Analysts, Architects, and Developers

---

## 1. Introduction: The Identity Hypothesis
In healthcare, patient identity is not a static fact; it is a resolvable, auditable hypothesis. Clinical systems (EMRs, LIS, RIS) are designed to manage **records**—sets of clinical observations, orders, and billing events—using a local identifier (MRN). However, these systems are notoriously poor at managing **identity**—the mapping of multiple disparate records to a single human being.

The fundamental challenge of a Master Patient Index (MPI) is not just "finding duplicates," but managing the lifecycle of identity resolution without compromising clinical safety, legal integrity, or global health equity.

### The Golden Rule of MPI
> **Patient IDs represent records. Person IDs represent reality.**

---

## 2. The Reference Architecture: Three-Layer Separation
Successful MPIs rely on a fundamental separation of concerns. Collapsing identity truth into a search index is the most common cause of MPI failure.

### Layer 1: The Identity Anchor (`person`)
The `person` table is the stable, long-lived anchor. It contains no clinical data and rarely changes. It serves as the enterprise-wide "Source of Truth" for human identity.

### Layer 3: The Identity Assertion (`person_patient_xref`)
This layer maps `person_id` to various `patient_id`s across different systems. It records the **judgment** that "Record A and Record B belong to Person X." It must be auditable, reversible, and account for **Source System Trust**—weighting identifiers from national registries higher than self-registration portals.

### Layer 3: The Search Engine (`mpi_patient_index`)
The **perception** layer. It contains denormalized, cleaned, and phonetic versions of demographics. It is optimized for speed and recall, identifying *candidates* for review.

---

## 3. Technical Implementation: The Schema

### Database Design (MySQL 8+ Example)
The following schema enforces transactional safety and clear ownership boundaries.

```sql
-- Identity Anchor
CREATE TABLE person (
  person_id   CHAR(36) PRIMARY KEY, -- Use UUIDs for future HIE federation
  status      ENUM('active','retired','merged','error') NOT NULL DEFAULT 'active',
  created_at  TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP
);

-- Cross-Reference (The Assertion Layer)
CREATE TABLE person_patient_xref (
  person_id     CHAR(36) NOT NULL,
  patient_id    VARCHAR(32) NOT NULL,
  source_system VARCHAR(20) NOT NULL,
  source_trust  INT DEFAULT 50, -- 0-100 weighting
  link_reason   ENUM('registration_new','registration_selected','mpi_auto','mpi_manual','system_import') NOT NULL,
  status        ENUM('active','superseded','revoked') NOT NULL DEFAULT 'active',
  created_at    TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP,
  PRIMARY KEY (person_id, patient_id),
  FOREIGN KEY (person_id) REFERENCES person(person_id)
);

-- Search Index (The Perception Layer)
CREATE TABLE mpi_patient_index (
  patient_id          VARCHAR(32) PRIMARY KEY,
  name_1_canon        VARCHAR(100), -- Global-friendly (e.g. Given)
  name_2_canon        VARCHAR(100), -- Global-friendly (e.g. Family)
  full_name_norm      VARCHAR(300), 
  alt_name_keys       JSON,         
  phonetic_code       VARCHAR(50),  
  dob                 DATE,
  updated_at          TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP
);
```

---

## 4. Global Name Handling: The Five Representations
In global contexts, "First/Middle/Last" structures often fail. A robust MPI uses generic name components (Name 1, Name 2, etc.) to handle patronymics, double surnames, or mononymous patients.

1.  **RAW**: Exactly what was typed (Dr. John O'Malley).
2.  **CANONICAL**: Cleaned for human review (JOHN OMALLEY).
3.  **NORMALIZED**: Collapsed strings (JOHNOMALLEY) for fast B-tree indexing.
4.  **ALTERNATE ORDER**: Defense against field swaps (OMALLEYJOHN).
5.  **PHONETIC**: Algorithm-generated codes (JNMAL). Must use language-appropriate algorithms (e.g., Metaphone for English, custom for Arabic/Chinese).

---

## 5. Matching Theory: Fellegi-Sunter & Source Trust
MPI matching uses **Fellegi-Sunter (F-S) Theory** to produce a **Total Score** based on weighted evidence.

- **M-probability**: Likelihood fields agree given a match (e.g., National ID).
- **U-probability**: Likelihood fields agree by chance (e.g., common surnames).
- **Source Trust Weighting**: Identity decisions are weighted by the source system's reliability. A link asserted by a National Identity Registry carries more weight than one from a legacy clinic spreadsheet.

---

## 6. Interoperability & Standards (HL7/FHIR)
Internal identity decisions must propagate using international standards to maintain semantic interoperability.

- **FHIR**: Map internal links to `Patient.link`. Use `replaced-by` for merges and `seealso` for high-confidence candidates.
- **HL7 v2**: Use the `MRG` (Merge Patient) segment in `ADT` messages to inform downstream systems (LIS, PACS) of identity resolution.

---

## 7. Practical Implementation: The "In-App" Enterprise Pattern
When a separate MPI service is not feasible, the MPI can be built directly within the enterprise database using a dedicated schema to maintain logical separation.

### The Architectural Strategy
```sql
CREATE SCHEMA identity_mpi;

CREATE TABLE identity_mpi.persons (
  person_id       UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  canonical_mrn   VARCHAR(64) UNIQUE,
  global_status   ENUM('active', 'merged', 'suppressed') DEFAULT 'active'
);

-- Link existing EMR records to the Anchor
ALTER TABLE public.patients 
ADD COLUMN enterprise_person_id UUID REFERENCES identity_mpi.persons(person_id);
```

### Key Advantages
1.  **Transactional Integrity**: Wrap merge/unmerge in a single ACID transaction.
2.  **Zero Latency**: Perform direct `JOIN`s for real-time duplicate detection.
3.  **Atomic Backups**: Clinical and identity truth stay perfectly synchronized in snapshots.

---

## 8. Bootstrapping Identity: From Monolith to MPI
For organizations migrating from a monolithic `patients` table to an MPI-enabled structure, the transition must be managed to ensure zero data loss and immediate operational continuity.

### The Recommended Migration Strategy (Phase-Based)

#### Phase 1: The "1:1 Anchor" Initialization
The safest starting point is to assume every existing patient record is a unique human until proven otherwise. This avoids "accidental merges" during the migration process.

```sql
-- 1. Add the identity anchor to the existing patients table
ALTER TABLE public.patients ADD COLUMN enterprise_person_id UUID;

-- 2. Generate a unique identity for every record
UPDATE public.patients SET enterprise_person_id = gen_random_uuid();

-- 3. Populate the 'persons' table from the newly generated IDs
INSERT INTO identity_mpi.persons (person_id, global_status)
SELECT DISTINCT enterprise_person_id, 'active' FROM public.patients;

-- 4. Initialize the cross-reference table
INSERT INTO identity_mpi.person_patient_xref (person_id, patient_id, source_system, link_reason)
SELECT enterprise_person_id, patient_id, 'LEGACY_SYSTEM', 'system_import'
FROM public.patients;
```

#### Phase 2: Duplicate Discovery
Once the 1:1 mapping is established, run the **Fellegi-Sunter matching algorithm** across the entire `patients` table. 
- **Goal**: Identify pairs or groups of `patient_id`s that likely belong to the same human.
- **Output**: A list of "High Confidence" merge candidates.

#### Phase 3: The "Soft Merge"
For high-confidence candidates, execute a "Soft Merge" by updating the `person_patient_xref` table.

```sql
-- Example: Merging Patient B into Patient A's Identity
BEGIN;
  -- 1. Re-point Patient B to Patient A's Person ID
  UPDATE identity_mpi.person_patient_xref 
  SET person_id = (SELECT person_id FROM identity_mpi.person_patient_xref WHERE patient_id = 'PATIENT_A'),
      status = 'superseded'
  WHERE patient_id = 'PATIENT_B';

  -- 2. Retire the redundant Person ID
  UPDATE identity_mpi.persons SET global_status = 'merged' WHERE person_id = 'PERSON_B_ID';
COMMIT;
```

### Critical Success Factors
1.  **Do Not Batch-Merge at Migration**: Never perform automated merges during the initial data load. Establish the structure first, then deduplicate iteratively.
2.  **Maintain the Legacy Key**: Keep the original `patient_id` (MRN) as the primary key for clinical data. The `person_id` is for identity resolution, not for database joins in clinical modules.
3.  **Audit the Genesis**: Every record in the `person_patient_xref` table should have a `link_reason` of `system_import` for the bootstrap phase to distinguish legacy data from new, registration-time assertions.

---

## 9. Operational Stewardship
Even the best algorithm requires a human **Data Steward**.
- **Stewardship Workflow**: Provide a UI for adjudicating "Medium Score" candidates.
- **The "Blast Radius" Principle**: Merging and unmerging should only affect the `xref` layer. **Never delete clinical history or merge records in clinical tables.**

---

## 9. Conclusion
A scalable MPI is a combination of a high-performance search engine and a rigorous identity layer. By treating identity as a resolvable hypothesis and adhering to global standards, Health IT organizations can ensure patient safety and data continuity across diverse ecosystems.

> **Indexes find candidates. Links assert truth. Persons preserve reality.**
