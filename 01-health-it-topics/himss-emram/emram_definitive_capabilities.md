# EMRAM Definitive Capabilities

Each stage is defined by a specific set of capabilities that did not exist in the prior stage. These are the capabilities that HIMSS evaluators look for when scoring a hospital. Everything else is inherited from the stage below.

------

## Stage 0 — No Clinical Information Systems

**Defining condition:** No clinical software is operational at the point of care.

There are no definitive digital capabilities at this stage. It is the baseline against which all adoption is measured.

------

## Stage 1 — Ancillary Systems Online

**Defining condition:** At least three ancillary departments (lab, radiology, pharmacy) have operational departmental software.

- Lab staff can enter, process, and retrieve test results within a digital lab information system (LIS)
- Radiology staff can capture, manage, and retrieve images and reports within a departmental system
- Pharmacy staff can manage medication inventory and dispensing records within a digital pharmacy system

> These systems are isolated. No data crosses departmental boundaries.

------

## Stage 2 — Clinical Data Repository and Controlled Vocabulary

**Defining condition:** A central Clinical Data Repository (CDR) aggregates data from ancillary systems using a controlled medical vocabulary. Basic HIE connectivity is established.

- Clinicians can view consolidated patient results — lab, radiology reports, and basic demographics — from a single workstation outside the originating department
- Patient data uses standardized clinical terminology (e.g., LOINC for labs, RxNorm for medications), meaning the same concept resolves consistently regardless of which department generated it
- Clinicians can access limited patient data from external facilities for the first time via Health Information Exchange (HIE)

> The record is read-only and aggregated. No clinical documentation or order entry exists yet.

------

## Stage 3 — Nursing Documentation and eMAR

**Defining condition:** Nurses document clinical care digitally. The paper Medication Administration Record (MAR) is replaced by an electronic MAR (eMAR).

- Nurses can document vital signs, assessments, and care notes directly into the EMR
- Every medication administration is recorded electronically in the eMAR with an automatic timestamp, creating a real-time, auditable medication record
- Nurses can view a patient's complete eMAR history within the current encounter
- Nurses can receive electronic task alerts and care reminders from the system
- Physicians and other clinicians can view nursing documentation and eMAR entries from their own workstation

> The eMAR is the definitive Stage 3 capability. It replaces paper as the legal medication administration record.

------

## Stage 4 — Computerized Physician Order Entry (CPOE) with Clinical Decision Support (CDS)

**Defining condition:** Physicians enter all orders directly into the EMR. The system performs automated safety checks at the moment of order entry.

- Physicians can enter medication, lab, and imaging orders directly into the EMR — no paper or verbal order transcription required
- The system automatically checks every medication order for drug-drug interactions, patient allergy conflicts, and dosing errors before the order is committed
- Physicians receive real-time safety alerts at the point of ordering — the first time the system actively intervenes in clinical decision-making
- Nurses receive physician orders electronically and can act on them immediately, without transcription
- Pharmacy staff receive medication orders electronically and can review, verify, and queue them for dispensing within the system
- Radiology staff receive imaging orders electronically and manage their worklist from CPOE-generated requests
- Lab staff receive electronic lab orders and route results back to the ordering physician's EMR view

> CPOE with CDS is the definitive Stage 4 capability and the most consequential adoption event in the EMRAM model. It is where the system first prevents harm.

------

## Stage 5 — Full Clinician Documentation and DICOM Integration

**Defining condition:** All clinical documentation is structured and discrete. DICOM imaging is fully integrated into the EMR workflow.

- Physicians can document clinical encounters using structured templates with discrete, coded fields — not free-text narrative — making the record computable and queryable
- Physicians can view DICOM radiology images directly within the EMR without launching a separate PACS viewer
- Clinical decision support rules can now fire against structured discrete data in the patient record, enabling richer and more precise safety alerts (e.g., a CDS rule that fires when a specific lab value conflicts with an active medication order)
- Pharmacy staff can perform indication checking — validating that an ordered medication is clinically appropriate for the patient's coded diagnoses
- The structured record is queryable for quality reporting, population analytics, and regulatory submissions for the first time

> Structured documentation is the definitive Stage 5 capability. It transforms the EMR from a documentation tool into a computable data asset.

------

## Stage 6 — Closed-Loop Medication Administration

**Defining condition:** Every step of the medication cycle — from physician order to bedside administration — is electronically verified using barcode or RFID scanning.

- Nurses can scan the patient's wristband and the medication barcode at the bedside before every administration, verifying the five rights: right patient, right drug, right dose, right route, right time
- The system generates an immediate electronic alert and blocks administration if any element of the verification fails
- Every administration is automatically timestamped and logged to the eMAR at the point of scanning — no manual entry required
- Pharmacy staff can audit the complete medication trail from physician order through dispensing cabinet to bedside administration within a single electronic record
- Automated dispensing cabinets (e.g., Omnicell, Pyxis) are integrated with the EMR, and any discrepancy between dispensed and administered medication generates an automatic alert

> Closed-loop medication administration is the definitive Stage 6 capability. It closes the last manual gap in the medication safety chain.

------

## Stage 7 — Complete EMR Environment

**Defining condition:** The hospital operates as a data-generating, continuously learning system. The EMR is fully integrated internally and externally, and clinical data actively drives analytics, quality improvement, and algorithmic decision support.

- Clinicians can access predictive clinical decision support driven by population-level analytics — including early warning scores, deterioration alerts, sepsis flags, and readmission risk — generated from the live patient record
- Physicians can view longitudinal patient data aggregated from multiple facilities via FHIR-based external exchange
- Clinical data feeds continuous quality improvement loops, and clinicians receive outcome-linked feedback on their practice patterns benchmarked against peers
- The hospital can demonstrate, with data, that its digital systems are improving measurable patient outcomes
- Hospital administrators can generate regulatory compliance reports, submit data to external registries and payers, and run real-time operational dashboards directly from the EMR data
- Radiology data contributes to AI-assisted diagnostic pipelines
- Lab result trends feed predictive deterioration models
- Paper is operationally eliminated, including downtime procedures

> The definitive Stage 7 condition is not a single feature — it is the organizational ability to close the loop between clinical data and improved outcomes at a population level.