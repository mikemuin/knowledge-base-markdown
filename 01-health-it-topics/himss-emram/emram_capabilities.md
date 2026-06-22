# EMRAM Stages — What Staff Can and Cannot Do

------

## Stage 0 — No Clinical Information Systems

All clinical work is paper-based. No staff role has access to digital clinical data.

- **Nurses**
  - Can do:
    - Document patient observations on paper charts
    - Administer medications using paper MAR
    - Communicate orders verbally or via handwritten notes
    - Retrieve patient history from physical paper records
  - Cannot do:
    - Access any digital patient record
    - Receive electronic alerts or notifications
    - Log medication administration electronically
    - Look up a patient's allergy list or medication history digitally
    - Track patient bed assignment or location electronically
- **Doctors/Physicians**
  - Can do:
    - Write orders on paper
    - Review paper-based lab results and imaging reports delivered physically
    - Access patient history from physical records
  - Cannot do:
    - Enter orders into any system
    - Receive electronic clinical decision support
    - View results digitally at the point of care
    - Look up a patient's medication history or allergy list digitally
- **Lab Staff**
  - Can do:
    - Run tests and produce results on paper or locally printed reports
    - Track specimens manually using paper logs
  - Cannot do:
    - Transmit results electronically to other departments or clinicians
    - Flag or route critical values electronically
    - Track specimen status digitally
- **Radiology Staff**
  - Can do:
    - Produce films and printed reports
    - Schedule imaging studies using paper-based appointment books
  - Cannot do:
    - Share images or reports digitally with other departments
    - Receive imaging orders electronically
    - Track study status or worklist digitally
- **Pharmacy Staff**
  - Can do:
    - Dispense medications based on handwritten or verbally communicated orders
    - Maintain manual inventory logs
  - Cannot do:
    - Receive orders electronically
    - Check for drug-drug interactions or allergy conflicts using any system
    - Track dispensing records digitally

------

## Stage 1 — Ancillary Department Systems Installed

Departmental software exists in the lab, radiology, and pharmacy, but systems are isolated. No cross-department data sharing.

- **Nurses**
  - Can do:
    - Receive printed lab results or verbal relay from ancillary departments
    - Administer medications using paper MAR
  - Cannot do:
    - View lab or radiology results directly from a nursing workstation
    - Receive electronic medication orders
    - Track patient admissions, discharges, or transfers (ADT) electronically
    - Receive electronic alerts or notifications from any department
- **Doctors/Physicians**
  - Can do:
    - Request lab or radiology results verbally or via paper requisition
    - Review printed result reports
  - Cannot do:
    - View results electronically outside the originating department
    - Enter orders into any system
    - Receive electronic notification when a result is ready
    - Track order status electronically
- **Lab Staff**
  - Can do:
    - Enter, process, and view test results within the lab information system (LIS)
    - Print result reports for distribution
    - Track specimen status digitally within the LIS
  - Cannot do:
    - Send results electronically to nursing stations, physician workstations, or any central record
    - Flag or route critical values electronically to the responsible clinician
    - Receive electronic order requests from physicians
- **Radiology Staff**
  - Can do:
    - Capture and manage images within the radiology department system
    - Produce printed reports
    - Manage an internal digital worklist within the radiology system
  - Cannot do:
    - Share images or reports electronically outside the radiology department
    - Receive imaging orders electronically from physicians
    - Schedule studies from a centrally visible system
- **Pharmacy Staff**
  - Can do:
    - Manage medication inventory and dispensing records within the pharmacy system
    - Track dispensing history within the pharmacy system
  - Cannot do:
    - Receive electronic orders from physicians
    - Electronically verify orders against patient allergy or medication history
    - Check for drug-drug interactions using patient-linked data

------

## Stage 2 — Clinical Data Repository, Controlled Vocabulary, HIE Connectivity

A central Clinical Data Repository (CDR) aggregates results from ancillary systems. Standardized clinical vocabulary is in place. Basic Health Information Exchange (HIE) connectivity begins.

- **Nurses**
  - Can do:
    - View aggregated lab and radiology results from a central system
    - Access a basic patient data summary across departments
    - View patient allergy and demographic data consolidated in the CDR
    - Access limited data from external facilities for patients transferred in via HIE
  - Cannot do:
    - Document nursing assessments or care notes electronically
    - Log medication administration electronically
    - Receive electronic alerts or task reminders
    - Communicate electronically with other departments through the system
- **Doctors/Physicians**
  - Can do:
    - View consolidated patient results from the CDR at a workstation
    - Access limited data from external facilities via HIE
    - View aggregated allergy and medication history in the CDR
  - Cannot do:
    - Enter orders electronically
    - Receive clinical decision support at the point of care
    - Document encounter notes in the system
    - Receive electronic notification when a result is abnormal or critical
- **Lab Staff**
  - Can do:
    - Transmit results electronically to the CDR using standardized codes
    - Have results accessible outside the department for the first time
  - Cannot do:
    - Receive electronic order requests from physicians
    - Interact with pharmacy or nursing systems directly
    - Trigger clinical alerts based on result values
- **Radiology Staff**
  - Can do:
    - Transmit structured radiology reports to the CDR
    - Have reports accessible to other departments via the CDR for the first time
    - View requesting physician and patient demographic data from the CDR
  - Cannot do:
    - Share DICOM images directly with physician or nursing workstations
    - Receive electronic imaging orders
    - Have images viewable inside a clinical workflow outside radiology
- **Pharmacy Staff**
  - Can do:
    - View patient allergy and basic medication history aggregated in the CDR
    - Cross-reference dispensing records against CDR patient data
  - Cannot do:
    - Receive electronic orders from physicians
    - Perform real-time drug interaction checking linked to the patient record
    - Transmit dispensing confirmations back to the CDR

------

## Stage 3 — Nursing/Clinical Documentation and eMAR

Nurses document digitally. The electronic Medication Administration Record (eMAR) replaces the paper MAR.

- **Nurses**
  - Can do:
    - Document vital signs, assessments, and care notes electronically
    - Record medication administration in the eMAR with a timestamp
    - View the eMAR history for a patient
    - Receive electronic care alerts and task reminders
    - View patient allergy list electronically during medication preparation
    - Access nursing documentation from prior shifts within the same encounter
  - Cannot do:
    - View physician orders entered electronically (CPOE is not yet in place)
    - Receive automated drug interaction alerts at the point of administration
    - Scan barcodes to verify medications at the bedside
    - Receive orders electronically — verbal or paper order transcription is still required
- **Doctors/Physicians**
  - Can do:
    - View nursing documentation and eMAR entries
    - Review aggregated patient data in the CDR
    - View structured nursing assessments completed during the current encounter
  - Cannot do:
    - Enter orders electronically (CPOE is not yet in place)
    - Receive clinical decision support prompts
    - See real-time alerts tied to nursing findings or vital sign thresholds
    - Document clinical encounter notes in a structured electronic format
- **Lab Staff**
  - Can do:
    - Receive results requests through existing workflows
    - Transmit results to the CDR
  - Cannot do:
    - Receive electronic orders from physicians via the EMR
    - Have critical value alerts routed automatically to the responsible nurse or physician
- **Radiology Staff**
  - Can do:
    - Transmit reports to the CDR as in Stage 2
  - Cannot do:
    - Receive electronic imaging orders from physicians
    - Have images viewable within the clinical EMR workflow
- **Pharmacy Staff**
  - Can do:
    - View eMAR entries to reconcile administered medications against dispense records
    - Manage dispensing within the pharmacy system
  - Cannot do:
    - Receive electronic medication orders from physicians
    - Perform electronic verification of orders against patient allergy profiles in real time
    - Close the medication loop — no bedside verification exists yet

------

## Stage 4 — CPOE with Clinical Decision Support

Physicians enter all orders directly into the EMR. The system checks for drug interactions, allergy conflicts, and dosing errors at the moment of order entry.

- **Nurses**
  - Can do:
    - Receive physician orders electronically and act on them immediately without transcription
    - View pending and completed order queues in real time
    - Document against active electronic orders
    - Flag order discrepancies or clarifications electronically back to the physician
    - Receive electronic critical lab value alerts routed from the lab system
  - Cannot do:
    - Verify medication administration at the bedside via barcode scanning
    - Receive automated alerts during the physical act of administering a medication
    - Access structured physician documentation (notes remain free-text or dictated)
- **Doctors/Physicians**
  - Can do:
    - Enter medication, lab, and imaging orders directly into the EMR
    - Receive real-time alerts for drug-drug interactions, allergy conflicts, and dosing errors at order entry
    - View order history and status
    - Receive electronic critical lab value alerts
    - Query order status and results without leaving the EMR
  - Cannot do:
    - Rely on system-generated structured clinical documentation (physician notes are still largely free-text or dictated)
    - Access fully integrated DICOM imaging within the EMR workflow
    - Receive CDS based on structured discrete data in the patient record (structured documentation does not yet exist)
- **Pharmacy Staff**
  - Can do:
    - Receive physician orders electronically
    - Review and verify orders within the system before dispensing
    - View patient allergy data at order review
    - Route dispensing exceptions and clarification requests back through the system to the ordering physician
  - Cannot do:
    - Close the loop on physical medication administration — bedside verification is still not electronic
    - Perform indication checking (structured diagnoses do not yet exist in the record)
- **Lab Staff**
  - Can do:
    - Receive electronic lab order requests originating from CPOE
    - Transmit results back to the ordering physician's EMR view
    - Route critical value alerts electronically to the ordering clinician
  - Cannot do:
    - Trigger automated CDS rules in the EMR based on result values
    - Have results feed into population-level analytics
- **Radiology Staff**
  - Can do:
    - Receive electronic imaging orders from CPOE for the first time
    - Manage a digitally populated imaging worklist from physician orders
    - Transmit completed reports back to the ordering physician's EMR view
  - Cannot do:
    - Have DICOM images viewable within the physician EMR workflow (full PACS integration comes at Stage 5)
    - Receive CDS prompts related to imaging appropriateness

------

## Stage 5 — Full Clinician Documentation and Structured Data

All physician documentation is structured and discrete. Imaging (DICOM) is fully integrated into the EMR workflow.

- **Nurses**
  - Can do:
    - Document against structured nursing templates with discrete fields
    - View integrated radiology images and reports alongside other patient data
    - Benefit from CDS rules that fire against structured nursing data (e.g., fall risk scores, pressure injury alerts)
    - View structured physician notes and problem lists within the same EMR workflow
    - Contribute to structured care plans linked to discrete diagnoses and orders
  - Cannot do:
    - Verify medication administration at the bedside via barcode or RFID scanning (closed-loop is not yet in place)
    - Receive automated alerts specifically tied to the act of physical medication administration
- **Doctors/Physicians**
  - Can do:
    - Document clinical encounters using structured templates with discrete, codified fields
    - View DICOM radiology images directly within the EMR without switching to a separate PACS viewer
    - Benefit from richer CDS rules running against structured discrete data in the patient record
    - Have documentation queryable for analytics and quality reporting
    - Maintain structured problem lists, medication lists, and allergy lists as discrete coded data
    - Generate referral letters and clinical summaries drawn from structured record data
  - Cannot do:
    - Rely on the system to electronically verify that ordered medications were correctly administered at the bedside
    - Access population-level analytics or benchmarking dashboards (that capability is Stage 7)
- **Radiology Staff**
  - Can do:
    - Deliver DICOM images directly into the physician EMR workflow via full PACS integration
    - Receive electronic imaging orders from CPOE and close the order loop on completion
    - Have structured report findings contribute to CDS rules in the EMR
  - Cannot do:
    - Contribute imaging data to population-level analytics pipelines (that capability is Stage 7)
- **Pharmacy Staff**
  - Can do:
    - Verify orders against a richer, structured patient record including coded diagnoses and documented allergies
    - Perform indication checking — validating that the ordered medication is appropriate for the patient's coded diagnoses
    - Access structured medication reconciliation data across the encounter
  - Cannot do:
    - Complete the closed medication loop — bedside barcode verification is not yet in place
- **Lab Staff**
  - Can do:
    - Have results feed into CDS rules that fire against the structured patient record (e.g., a critical potassium value triggering an alert against an active diuretic order)
    - Transmit results in discrete coded format that populates structured flowsheets and trend views
  - Cannot do:
    - Have results feed into population-level analytics or predictive model pipelines (that capability is Stage 7)
    - Trigger closed-loop medication safety checks at the bedside

------

## Stage 6 — Closed-Loop Medication Administration

Every step of the medication cycle is electronically verified. Bedside barcode or RFID scanning confirms the right patient, right drug, right dose, and right time before administration.

- **Nurses**
  - Can do:
    - Scan patient wristband and medication barcode at the bedside before every administration
    - Receive an immediate electronic alert if any element of the five rights fails verification
    - Have all administrations automatically timestamped and logged in the eMAR
    - Receive real-time dispensing cabinet alerts for discrepancies between dispensed and ordered medications
    - Reconcile automated dispensing cabinet stock against eMAR records electronically
  - Cannot do:
    - Override a failed barcode check without generating a tracked exception event (overrides are logged and reviewable by pharmacy and quality teams)
    - Access population-level analytics or quality dashboards (that capability is Stage 7)
- **Doctors/Physicians**
  - Can do:
    - View confirmed medication administration records with timestamps in real time
    - Receive downstream alerts if a medication was not administered within the expected window
    - Query real-time medication administration status per patient from within the EMR
  - Cannot do:
    - Access population-level prescribing analytics or outcome-linked feedback on ordering patterns (that capability is Stage 7)
- **Pharmacy Staff**
  - Can do:
    - Electronically verify and dispense medications with the full medication loop closed
    - Receive alerts when a dispensed medication does not match the scanned administration record
    - Manage automated dispensing cabinets (e.g., Omnicell, Pyxis) integrated with the EMR
    - Audit the complete medication administration trail from order to bedside administration electronically
  - Cannot do:
    - Access population-level medication adherence data or outcomes analytics (that capability is Stage 7)
- **Lab Staff**
  - Can do:
    - Have critical value alerts routed directly to the administering nurse and ordering physician simultaneously, linked to active medication orders
    - Confirm that critical result notifications are acknowledged electronically by the responsible clinician
  - Cannot do:
    - Contribute to predictive analytics pipelines (that capability is Stage 7)
- **Radiology Staff**
  - Can do:
    - Confirm electronic completion of imaging orders with full closed-loop status tracking from order to report delivery
    - Have imaging report delivery acknowledged electronically by the ordering clinician
  - Cannot do:
    - Contribute imaging data to AI-assisted diagnostic pipelines or population health analytics (that capability is Stage 7)

------

## Stage 7 — Complete EMR Environment

Paper is eliminated. The EMR is fully integrated internally and externally. Clinical data actively drives population health analytics, quality improvement, and algorithmic decision support.

- **Nurses**
  - Can do:
    - Access predictive alerts generated from analytics models (e.g., early sepsis warnings, deterioration scores, readmission risk flags)
    - Document and retrieve data across care settings via external HIE
    - Operate in a fully paper-free environment including downtime procedures that use offline digital copies
    - Generate digitally tracked patient education materials and discharge instructions drawn from structured record data
    - Contribute nursing documentation to learning health system loops that refine CDS rules over time
  - Cannot do:
    - (No workflow capability gaps remain relative to the model's scope; limitations shift to policy, governance, and model accuracy)
- **Doctors/Physicians**
  - Can do:
    - Access AI- or analytics-driven clinical decision support informed by population-level data
    - View longitudinal patient data aggregated from multiple facilities via FHIR-based exchange
    - Contribute to and benefit from continuous quality improvement loops driven by structured EMR data
    - Receive outcome-linked feedback on their ordering patterns benchmarked against peers
    - Submit clinical data to research registries and participate in learning health system initiatives
    - Access real-time public health surveillance data integrated via HIE
  - Cannot do:
    - (No workflow capability gaps remain within the model's scope; limitations are organizational, regulatory, or related to model performance)
- **Pharmacy Staff**
  - Can do:
    - Participate in closed-loop, analytics-informed medication management
    - Access population-level medication adherence and outcomes data for formulary and safety review
    - Contribute to pharmacovigilance reporting pipelines drawing on structured dispensing and administration data
  - Cannot do:
    - (No workflow capability gaps remain within the model's scope)
- **Lab Staff**
  - Can do:
    - Have results feed directly into analytics pipelines and population health dashboards
    - Trigger downstream CDS rules based on result value trends across patient populations
    - Contribute structured lab data to predictive deterioration models and early warning systems
  - Cannot do:
    - (No workflow capability gaps remain within the model's scope)
- **Radiology Staff**
  - Can do:
    - Contribute imaging data to AI-assisted diagnostic analysis pipelines
    - Have imaging findings feed population health and epidemiological analytics
    - Access AI-generated preliminary reads or triage prioritization within the EMR workflow
  - Cannot do:
    - (No workflow capability gaps remain within the model's scope)
- **Hospital Administrators / Quality Teams**
  - Can do:
    - Run continuous quality and outcomes reporting directly from the EMR data
    - Submit data to external registries and payers electronically
    - Benchmark clinical performance against external datasets via HIE-connected analytics
    - Generate regulatory compliance reports automatically from structured EMR data
    - Monitor real-time operational dashboards for bed utilization, throughput, and safety event rates
  - Cannot do:
    - (Limitations at this stage are organizational and regulatory, not system capability gaps)