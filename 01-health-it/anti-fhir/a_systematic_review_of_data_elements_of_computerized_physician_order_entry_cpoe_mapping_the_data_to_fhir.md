# A systematic review of data elements of computerized physician order entry (CPOE): mapping the data to FHIR

The paper "A systematic review of data elements of computerized physician order entry (CPOE): mapping the data to FHIR" identifies 270 unique data elements used in CPOE systems. These are grouped into two categories based on whether they were successfully mapped to HL7 FHIR resources or not.

------

**Mapped Data Elements (199 total)**
These data elements were successfully mapped to one of the 26 FHIR resources identified in the study. These include resources such as Patient, Observation, Condition, Procedure, ServiceRequest, MedicationRequest, etc.

**Patient Resource:**

- Name
- Family
- Identifier
- Birth Date
- Gender
- Marital Status
- Address
- Phone Number
- Birth Place
- Race
- National ID
- Ethnicity
- Education
- Job
- Organ Donation

**Practitioner Resource:**

- Practitioner Name
- Signature Type

**RelatedPerson Resource:**

- Father's Name

**Organization Resource:**

- Name
- Type
- Address

**Encounter Resource:**

- Type
- Address
- Class
- Service Type
- Discharge Destination
- Physical Type
- Time Period
- Date
- Transported by Land Ambulance
- Transported by Air Ambulance

**Condition Resource:**

- Diagnosis Code
- Current Diagnosis
- Primary
- Discharge
- Other
- Problem List
- Clinical Status
- Cause of Death
- Place of Death
- Other Causes of Death
- Condition of Discharge or Transfer
- External Cause of Death
- Frequency of Fall in Previous Month
- Injury Date
- Injury Time
- Primary Cause of ESRD

**Procedure Resource:**

- Date of Surgery
- Start of Operation
- End of Operation
- Kind of Anesthesia
- Start of Anesthesia
- Anesthesia Time
- Anesthesia Risk Level
- Anesthesia Medication

**Observation Resource:**

- Signs
- Symptoms
- Blood Pressure
- Weight
- Height
- BMI
- Pregnancy Status
- Other Physical Exam Findings
- Smoking Status
- Blood Group
- Head Circumference
- Body Surface Area
- Growth Chart (Children)
- Chief Complaint
- Blood Flow Rate
- Fluid Intake or Output
- Medical History
- Hypertension
- Diabetes
- Arrhythmia
- Ischemic Cardiomyopathy
- Dementia
- COPD
- Stroke
- Chronic Kidney Disease

**DiagnosticReport Resource:**

- Laboratory Results
- Imaging Study Findings
- Diagnostic Test Results
- Identifier
- Category
- Issued Effective

**ServiceRequest Resource:**

- Referral Request
- Referral Request Type
- Ordering Location
- Date Occurrence
- Time
- Reason
- Service Description
- Para Clinic Tests Results
- Hemodialysis Adequacy
- Start of Dialysis
- Dialyzer Type
- Hemodialysis
- Para Clinic Result Limit
- Para Clinic Report
- Blood Request
- Blood Unit
- Blood Type
- Request Rehabilitation
- Type of Rehabilitation

**Medication Resource:**

- Medication Name
- Medication Code
- Medication Dosage Instruction
- Medication Status
- Medication Form
- Stop Date
- Medication Unit
- Medication Frequency
- Medication Value
- Medication References
- Medication Dosage Range
- Daily Maximum Range
- Daily Dosage
- Side Effect
- Medication Instruction of Discharge

**MedicationAdministration Resource:**

- Medication Administration
- Route
- Dose

**Specimen Resource:**

- Specimen Collection Date
- Specimen Type

**CarePlan Resource:**

- Nutrition Order

**Other Resources Implemented But Not Detailed:**

- FamilyMemberHistory
- Communication
- Immunization
- Questionnaire
- QuestionnaireResponse
- ChargeItem
- ExplanationOfBenefit
- Claim
- Account
- Contract
- Task
- DeviceRequest
- ImagingStudy
- EpisodeOfCare
- Goal
- Consent

------

**Unmapped Data Elements (71 total)**
These elements could not be mapped directly to any existing FHIR resource and were considered for use as extensions. Examples of unmapped data elements include:

- Breastfeeding
- Pregnancy Category
- Pregnancy Risk
- AV access
- Hemodialysis Adequacy
- Type of Rehabilitation
- Para Clinic Result Limit
- Para Clinic Report
- Blood Pack Serial Number
- Job
- Education
- Organ Donation
- Signature Type
- Father’s Name
- Patient Image
- Transported by Land Ambulance
- Transported by Air Ambulance
- Physical Type (location hierarchy)
- Daily Maximum Range
- Dissolvent Name
- Dissolvent Dosage Range
- Dissolvent Dosage Unit
- Medication Preferences

These elements often represented:

- Localized data requirements
- Detailed administrative or logistical information
- Compound clinical indicators not explicitly defined in FHIR

They account for approximately 26% of the total dataset and indicate areas where FHIR could be extended or evolved to better support real-world CPOE needs.

------

This classification highlights the variability and gaps in current CPOE data standardization and supports the recommendation that both CPOE systems and the FHIR standard evolve toward greater alignment.