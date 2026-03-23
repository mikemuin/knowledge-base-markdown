# MPI Implementation Notes

## 1. The Core Problem: Identity vs. Records
In healthcare, clinical systems (such as EMRs, LIS, and RIS) are designed to manage **records**—sets of clinical observations, orders, and billing events—using local identifiers (like MRNs or patient IDs). However, these systems inherently fail at managing **identity**, which is the mapping of multiple disparate records across different systems to a single human being.

Because of this limitation, healthcare organizations frequently create **multiple patient records for the same human**. These duplicate records are silent, cumulative, and dangerous to patient safety, as clinicians may make medical decisions based on fragmented or incomplete clinical history.

You cannot safely fix this problem by simply deleting or merging patient records in place, as doing so rewrites clinical history, risks data loss, and breaks references in the source systems.

The fundamental challenge of a Master Patient Index (MPI) is managing the identity-resolution lifecycle without compromising clinical safety, legal integrity, or interoperability. This requires recognizing the overarching principle of identity management:
> **Patient IDs represent clinical records. Person IDs represent human reality.**

## 2. What is a Master Patient Index (MPI)?
A Master Patient Index is an enterprise-wide system, external to any single EMR, that maps multiple clinical records to a single human being. It ensures that disparate healthcare systems have a unified, reliable view of patient identity without altering local clinical records.

A robust MPI must treat patient records as immutable facts and identity as a resolvable hypothesis. This means that an MPI does not just identify candidate duplicates; it actively manages decisions about identity (merges, unmerges, and source-system trust) over time.

## 3. The Reference Architecture: Three-Layer Separation
The most common cause of homegrown MPI failures is collapsing the "search" function and the "identity truth" into a single layer. Successful commercial MPIs rely on a fundamental, three-layer separation of concerns:

### Layer 1: The Identity Anchor (`person`)
The `person` table is the stable, long-lived anchor of the system.
- **In plain language, this is:** The definitive roster of actual human beings, completely separate from their clinical information.
- It contains **no clinical data** and **no demographics**.
- It rarely changes once established.
- It provides a single permanent identifier (e.g., UUID) that represents the patient in reality, making it safe for analytics and global Health Information Exchange (HIE).

### Layer 2: The Identity Assertion (`person_patient_xref`)
This is the **judgment** layer where truth, auditability, and safety live.
- **In plain language, this is:** The mapping table that explicitly states, "Hospital Record A and Clinic Record B both belong to Human X."
- It maps the `person_id` (anchor) to various `patient_id`s across source systems.
- It explicitly records the **decision** that "Record A and Record B belong to the same Person X."
- It manages merges and unmerges. An incorrect merge can be safely revoked here by altering the mapping, changing the identity state *without* rewriting clinical history or data in the EMR.
- It handles provenance, tracking *why* a link was made and grading the trust level of the source system.

### Layer 3: The Search Engine (`mpi_patient_index`)
This is the **perception** layer. It powers speed, recall, and duplicate detection.
- **In plain language, this is:** A fast, heavily-indexed search engine used solely to find records that look similar to each other.
- This layer denormalizes, cleans, and standardizes demographic data (names, DOBs).
- It calculates phonetic codes and alternate structures to quickly find matching candidates during patient registration.
- It provides the input for probabilistic scoring algorithms (like Fellegi-Sunter).
- **Crucially:** The search engine identifies candidates; it does *not* possess the authority to declare identity truth. Truth is deferred to Layer 2.

## 4. Production System Architecture (Beyond the Data Layers)
While the three data layers (`person`, `person_patient_xref`, `mpi_patient_index`) form the core *database* architecture of an MPI, a production-ready system requires several operational layers built around this core to function safely and effectively:

1. **Data Ingestion & Integration Layer:**
   - **In plain language, this is:** The front door that receives, cleans, and standardizes messy data before the system attempts to match it.
   - Receives patient demographic data from diverse source systems (EHRs, Billing, LIS).
   - Standardizes and normalizes incoming data (e.g., uppercasing, removing punctuation) before it hits the search index.
2. **Matching Logic Engine:**
   - **In plain language, this is:** The calculator that computes the statistical probability that two records actually represent the same human.
   - The algorithmic layer that applies Fellegi-Sunter or hybrid matching theories against the `mpi_patient_index` to generate candidate scores.
3. **Data Governance & Stewardship Layer:**
   - **In plain language, this is:** The human review queue where tricky, uncertain matches are sent for manual approval or rejection by trained staff.
   - A critical workflow UI for human Data Stewards. It provides side-by-side comparisons of "middle-tier" algorithmic matches, allowing human judgment to securely approve merges or unmerges.
   - Enforces merge authority policies. **No governance → no automation.**
4. **Interoperability / API Layer:**
   - **In plain language, this is:** The broadcaster that securely shares the MPI's identity decisions back to the rest of the hospital's software suite.
   - Exposes the identity mappings back to the enterprise using standards such as FHIR (Patient.link) or HL7 (ADT with MRG segments), so that source systems respect the MPI's identity decisions.

## 5. Technical Implementation: Conceptual Data Dictionary
*Note: This data dictionary is not intended to be a complete, production-ready schema. Its purpose is to communicate the core concepts—highlighting only the essential tables and columns required for the architectural layers (Anchor, Assertion, Perception) to function successfully.*

The goal of this simplified schema is to enforce the separation of concerns between identity representation, human assertions, and fast search.

### Table: `person` (The Identity Anchor)
**Purpose:** Acts as the single permanent identifier for a human being across the enterprise.
**Key Columns:**
- `person_id` (Primary Key): The enterprise-wide, stable identifier (e.g., UUID). It never changes.
- `status` (`active`, `retired`, `merged`): Indicates if this identity is active or if it has been retired/merged into a different `person_id`.

### Table: `person_patient_xref` (The Assertion Layer)
**Purpose:** Records the human or algorithmic judgment that a specific local clinical record belongs to a specific enterprise person.
**Key Columns:**
- `person_id` (Foreign Key): The enterprise identity anchor.
- `patient_id` (Foreign Key): The local identifier from the source system (e.g., MRN).
- `source_system`: Defines where the clinical record lives (e.g., "Main EMR", "Lab System").
- `source_trust` (Score): A weighting system (0-100) based on the reliability of the source system. (e.g., A national identity registry has a higher trust score than a self-registration portal).
- `link_reason` (`system_import`, `mpi_auto`, `mpi_manual`): Provides provenance on *why* this link was established.
- `status` (`active`, `superseded`, `revoked`): Critical for reversibility. An incorrect merge is undone by changing this status to `revoked`, safely detaching the local record from the enterprise identity without deleting data.

### Table: `mpi_patient_index` (The Search Engine)
**Purpose:** The fast, heavily indexed table used for probabilistic matching and patient registration lookups. Demographics live here, not in the `person` table.
**Key Columns:**
- `patient_id` (Foreign Key): The local identifier linking back to the source system record.
- `name_1_canon` / `name_2_canon`: Generic canonical name components (e.g., Given Name, Family Name) cleaned and uppercased for consistency in human review.
- `full_name_norm`: The normalized, collapsed string (e.g., "JOHNOMALLEY") used for blazing-fast deterministic matching and B-tree indexing.
- `alt_name_keys`: Alternate arrangements of the name elements (e.g., swapping first and last name) used to defend against data entry errors.
- `phonetic_code`: Algorithmically generated code (e.g., Metaphone logic) representing how a name *sounds*, utilized to catch typos.
- `dob`: Date of birth, typically highly weighted in matching algorithms.

