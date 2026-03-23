# Master Patient Index (MPI) Project Summary

This folder contains a comprehensive body of knowledge regarding the architecture, technical implementation, and operational governance of a Master Patient Index (MPI). The materials emphasize that in healthcare, patient identity is a "resolvable hypothesis" rather than a static fact, requiring a system that is auditable, reversible, and safe.

## 1. Core Architectural Philosophy
The documentation advocates for a **Three-Layer Separation of Concerns** to ensure clinical safety and data integrity:

*   **Layer 1: The Identity Anchor (`person`)**: A stable, long-lived record representing a unique human being. It contains no clinical data.
*   **Layer 2: The Identity Assertion (`person_patient_xref`)**: The mapping layer that links a `person_id` to various `patient_id`s across different systems (EMR, LIS, RIS). This is where "truth" is asserted and where merges/unmerges are managed.
*   **Layer 3: The Search Engine (`mpi_patient_index`)**: A denormalized, performance-tuned "perception" layer containing cleaned and phonetic demographics used to identify match candidates.

## 2. Key Technical Components

### Database Schema & Implementation
*   **MySQL 8+ Reference Scripts**: Detailed SQL scripts are provided for the three-layer architecture using InnoDB for transactional safety.
*   **Status Management**: Heavy emphasis on using explicit ENUMs (e.g., `active`, `retired`, `merged`, `superseded`, `revoked`) rather than a vague `inactive` status to preserve audit trails and intent.
*   **Name Handling**: Implementation of "The Five Representations" to handle global name variances:
    1. **Raw**: Original input (Legal truth).
    2. **Canonical**: Cleaned/Uppercase (UI consistency).
    3. **Normalized**: Collapsed (Fast B-tree indexing).
    4. **Alternate Order**: Swap-defense (First/Last name swaps).
    5. **Phonetic**: Sound-based codes (Typo resilience).

### Matching & Merging Logic
*   **Fellegi-Sunter (F-S) Theory**: The probabilistic math used to score match confidence based on M-probabilities (agreement likelihood in matches) and U-probabilities (chance agreement in non-matches).
*   **Safe Merging/Unmerging**: A core principle is that **merging identity is reversible; merging records is not.** The documentation provides step-by-step guides for re-pointing cross-references without ever deleting clinical data or touching original MRNs.

## 3. Operational Governance
*   **Ownership Boundaries**: EMRs own clinical facts and local IDs; the MPI owns identity resolution and cross-system links.
*   **Registration Best Practices**: The MPI should assist registrars by suggesting candidates but should never block registration or perform destructive auto-merges at the front desk.
*   **Interoperability**: Guidance on mapping identity links to **HL7 FHIR `Patient.link`** and using HL7 v2 `MRG` segments for downstream synchronization.

## 4. Documentation Index

| File | Description |
| :--- | :--- |
| `mpi_consolidated_treatise.md` | The foundational technical and operational guide for the project. |
| `mpi_reference_architecture.md` | Conceptual diagrams and ownership boundary definitions. |
| `mpi_mysql_script_and_mergeunmerge.md` | Complete SQL schema and procedural logic for identity lifecycle. |
| `mpi_status_enums.md` | Deep dive into lifecycle states and why semantic clarity is required. |
| `mpi_the_concept.md` | Philosophical breakdown of "Search Engine vs. Identity Authority." |
| `patient_identity_mpi_consolidated_guidance.md` | A high-level "cheat sheet" of core MPI principles. |

---
*Summary generated on March 12, 2026.*
