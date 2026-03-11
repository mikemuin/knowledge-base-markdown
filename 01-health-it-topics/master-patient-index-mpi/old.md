# Master Patient Index (MPI): A Technical and Operational Treatise
**Target Audience:** Health IT Analysts, Architects, and Developers

---

## 1. Introduction: The Identity Hypothesis
In healthcare, patient identity is not a static fact; it is a resolvable, auditable hypothesis. Clinical systems (EMRs, LIS, RIS) are designed to manage **records**—sets of clinical observations, orders, and billing events tied to a local identifier (MRN). However, these systems are notoriously poor at managing **identity**—the mapping of multiple disparate records to a single human being.

The fundamental challenge of a Master Patient Index (MPI) is not just "finding duplicates," but managing the lifecycle of identity resolution without compromising clinical safety or legal integrity. This treatise outlines a reference architecture and implementation strategy that treats patient records as immutable facts while providing a flexible, reversible layer for identity management.

### The Golden Rule of MPI
> **Patient IDs represent records. Person IDs represent reality.**

---

## 2. The Reference Architecture: Three-Layer Separation
Successful MPIs—whether homegrown or commercial (e.g., IBM, Epic, Cerner)—rely on a fundamental separation of concerns. Collapsing identity truth into a search index is the most common cause of MPI failure.

### Layer 1: The Identity Anchor (`person`)
The `person` table is the stable, long-lived anchor. It contains no clinical data and rarely changes. It serves as the enterprise-wide "Source of Truth" for human identity.

### Layer 2: The Identity Assertion (`person_patient_xref`)
This is the most critical layer. It maps `person_id` to various `patient_id`s across different systems. This layer records the **judgment** that "Record A and Record B belong to Person X." It must be auditable, reversible, and preserve history.

### Layer 3: The Search Engine (`mpi_patient_index`)
This is the **perception** layer. It contains denormalized, cleaned, and phonetic versions of demographics. It is optimized for speed and recall. It identifies *candidates*, but it does not assert *truth*.

---

## 3. Technical Implementation: The Schema

### Database Design (MySQL 8+ Example)
The following schema enforces transactional safety and clear ownership boundaries.

```sql
-- Identity Anchor
CREATE TABLE person (
  person_id   CHAR(36) PRIMARY KEY,
  status      ENUM('active','retired','merged','error') NOT NULL DEFAULT 'active',
  created_at  TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP
);

-- Cross-Reference (The Assertion Layer)
CREATE TABLE person_patient_xref (
  person_id     CHAR(36) NOT NULL,
  patient_id    VARCHAR(32) NOT NULL,
  source_system VARCHAR(20) NOT NULL,
  link_reason   ENUM('registration_new','registration_selected','mpi_auto','mpi_manual','system_import') NOT NULL,
  status        ENUM('active','superseded','revoked') NOT NULL DEFAULT 'active',
  created_at    TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP,
  PRIMARY KEY (person_id, patient_id),
  FOREIGN KEY (person_id) REFERENCES person(person_id)
);

-- Search Index (The Perception Layer)
CREATE TABLE mpi_patient_index (
  patient_id          VARCHAR(32) PRIMARY KEY,
  first_canon         VARCHAR(100),
  last_canon          VARCHAR(100),
  full_name_norm      VARCHAR(300), -- "COLLAPSEDNAME"
  alt_name_keys       JSON,         -- ["LASTFIRST", "FIRSTLAST"]
  phonetic_code       VARCHAR(50),  -- Double Metaphone or Soundex
  dob                 DATE,
  phone_normalized    VARCHAR(30),
  updated_at          TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP
);
```

### The "Why" of Semantic States
Avoid the generic `inactive` status. In healthcare identity, you must know **why** a state changed to allow for safe unmerging and auditing.
- **`retired` vs `merged`**: Distinguishes between a record that is simply no longer used and one that was explicitly combined into another identity.
- **`revoked` (Xref)**: Critical for legal defensibility. It indicates a link was explicitly undone because it was incorrect.

---

## 4. Name Handling: The Five Representations
Matching fails when developers use raw display names for comparison. A robust MPI maintains five distinct representations of a patient's name:

1.  **RAW**: Exactly what was typed (Dr. John O'Malley). Used for display and legal truth.
2.  **CANONICAL**: Cleaned and consistent (JOHN OMALLEY). Used for UI consistency and simple filtering.
3.  **NORMALIZED**: Collapsed strings (JOHNOMALLEY). Used for fast, deterministic B-tree indexing.
4.  **ALTERNATE ORDER**: Defense against field swaps (OMALLEYJOHN). Essential for East Asian names or inconsistent entry.
5.  **PHONETIC**: Algorithm-generated codes (JNMAL). Catches typos and transliteration variances.

---

## 5. Matching Theory: Fellegi-Sunter and Beyond
Modern MPI matching is rooted in **Fellegi-Sunter (F-S) Theory**, a statistical framework for probabilistic record linkage.

### The Scoring Mechanism
Instead of binary Yes/No matches, F-S produces a **Total Score** based on weighted evidence:
- **M-probability**: Probability that a field agrees given the records are a match (e.g., SSN has high M-prob).
- **U-probability**: Probability that a field agrees given the records are *not* a match (e.g., "John" has a high U-prob in some populations).

### Decision Thresholds
- **High Score**: Auto-link (Deterministic).
- **Medium Score**: Manual Review (Stewardship Queue).
- **Low Score**: Distinct Identities (No Action).

---

## 6. Operational Workflows & Safety

### Ownership Boundaries
- **EMR Owns**: `patient_id`, clinical demographics (facts), visits, and clinical truth.
- **MPI Owns**: `person_id`, matching logic, duplicate detection, and identity resolution (hypotheses).

### The Merge/Unmerge Lifecycle
The "blast radius" of an identity decision must be minimized.
- **Merging**: To merge, we re-point the `person_id` in the `person_patient_xref` table and mark the "losing" person as `merged`. **We never touch clinical tables or delete patient records.**
- **Unmerging**: To unmerge, we simply create a new `person` anchor and move the specific `patient_id` link back. Because clinical data was never moved, the "unmerge" is instantaneous and risk-free.

### Registration-Time Best Practices
MPI should **assist**, not block.
- Show possible duplicates grouped by `person_id`.
- Always allow the registrar to "Create New" (False negatives are safer than false positives at the front desk).
- Post-registration batch processes handle the deep probabilistic matching that registration systems are too fast to execute.

---

## 7. Discussion: Why Homegrown MPIs Fail
Most failed identity projects share a common flaw: they treat identity as a one-way database update rather than a long-lived governance process.

1.  **Destructive Merges**: Systems that delete records or rewrite MRNs in clinical tables make unmerging impossible and destroy audit trails.
2.  **The "Good Algorithm" Trap**: Teams spend 90% of their time on matching math and 10% on workflow. In production, **undoing a wrong match safely is harder than making a right match.**
3.  **Lack of Governance**: Without clear rules on who can merge and how "Source System Trust" is weighted, the data will eventually rot.

### Conclusion
A scalable MPI is a combination of a high-performance search engine and a rigorous, auditable identity layer. By separating clinical facts from identity hypotheses, Health IT organizations can provide a longitudinal view of the patient while maintaining the absolute integrity of the legal medical record.

> **Indexes find candidates. Links assert truth. Persons preserve reality.**
