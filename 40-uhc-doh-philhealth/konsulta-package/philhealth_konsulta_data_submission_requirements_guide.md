# PhilHealth Konsulta Data Submission Requirements Guide

## Executive Summary

This comprehensive guide outlines the data submission requirements for PhilHealth Konsulta Package Providers (KPPs) based on PhilHealth Circular No. 2024-0013 and associated implementing documents. The guide covers mandatory datasets, submission triggers, deadlines, and complete data element specifications required for compliance with Philippine healthcare regulations.

------

## 1. MAJOR DATASETS REQUIRED FOR SUBMISSION

### 1.1 XML Consultation Data Files

**Primary Dataset for Capitation Payment Processing**

**Purpose:** Core transaction data for first and second tranche payments **Submission Method:**

- HCI Portal (online submission)
- Physical submission via USB to LHIO
- Direct EMR integration

**Data Storage:** PhilHealth Konsulta Database (centralized)

### 1.2 Electronic Konsulta Availment Slip (EKAS)

**Service Documentation Dataset**

**Purpose:** Documentation of healthcare services provided to patients **Submission Method:**

- System-generated through eKonsulta
- Manual hard copy (dual copy system)

### 1.3 Electronic Prescription Slip (EPRESS)

**Medication Documentation Dataset**

**Purpose:** Documentation of medications prescribed and dispensed **Submission Method:**

- System-generated through eKonsulta
- Manual hard copy (dual copy system)

### 1.4 First Patient Encounter (FPE) Forms

**Health Screening Dataset**

**Purpose:** Initial comprehensive health assessment data **Submission Method:** Integrated within XML consultation data

### 1.5 Provider Registration and Accreditation Data

**Administrative Dataset**

**Purpose:** KPP credentialing and network participation **Submission Method:** PhilHealth Konsulta Registration Form (PKRF)

------

## 2. SUBMISSION SCENARIOS AND TRIGGERS

### 2.1 First Tranche (40%) Payment Submissions

**Trigger Events:**

- Completion of First Patient Encounter (FPE)
- Medical consultation provided
- Laboratory services completed
- Medication dispensed

**Submission Requirements:**

- Daily basis preferred
- Monthly deadline: 11:59 PM on 7th calendar day of succeeding month
- Any submissions after deadline processed in next SAP1 cycle

**Processing Timeline:**

- SAP1 generation: 8th day of succeeding month
- Payment release: Within 60 days after 8th calendar day

### 2.2 Second Tranche (60%) Payment Submissions

**Trigger Events:**

- Annual performance evaluation
- Achievement of performance targets
- FPE completion status verification

**Submission Requirements:**

- Annual submission deadline: 11:59 PM on 7th calendar day of January (following year)
- Complete consultation data for entire year
- Performance metrics documentation

**Processing Timeline:**

- SAP2 generation: 8th day of January
- Payment release: Within 60 days after 8th calendar day of January

### 2.3 Registration and Transfer Scenarios

**New Patient Registration:**

- PKRF completion
- Photo consent form
- PIN verification
- KPP selection (1st and 2nd choice)

**Patient Transfer Between KPPs:**

- Previous KPP documentation
- New KPP selection
- Transfer justification
- Updated PKRF

### 2.4 Administrative Reporting

**Provider Data Updates:**

- Structure changes
- Service capability modifications
- Accreditation renewals
- Network participation changes

------

## 3. COMPLETE DATA ELEMENTS SPECIFICATION

### 3.1 XML Consultation Data Elements

#### Patient Demographics

- **PhilHealth Identification Number (PIN):** Mandatory unique identifier
- **Patient Name:** Last, First, Middle, Extension
- **Date of Birth:** MM/DD/YYYY format
- **Age:** Calculated or entered
- **Sex:** Male/Female designation
- **Contact Number:** Primary phone contact
- **Address:** Complete residential address
- **Membership Category:** Member/Dependent classification
- **Membership Type:** Specific membership classification
- **Client Type:** Patient classification

#### Transaction Details

- **Case Number:** Unique case identifier per provider
- **HCI Accreditation Number:** Provider accreditation ID
- **Transaction Number:** Unique transaction identifier
- **Health Screening Date:** MM/DD/YYYY format
- **Authorization Transaction Code (ATC):** System-generated code
- **Registration Number:** Patient registration reference
- **Date Registered:** MM/DD/YYYY format

#### Clinical Information

- **Chief Complaint:** Detailed patient complaint description

- Review of Systems:

Comprehensive symptom checklist

  - Constitutional symptoms (appetite, sleep, weight loss, depression, fever, headache, memory loss, vision, hearing)
  - Respiratory symptoms (cough, chest pain, palpitations, breathing difficulty)
  - Gastrointestinal symptoms (abdominal pain, vomiting, bowel changes, bleeding)
  - Genitourinary symptoms (urination frequency, pain, discharge)
  - Musculoskeletal symptoms (spasms, weakness, joint pain, stiffness)

#### Physical Examination Findings

- Vital Signs:
  - Blood Pressure (mmHg)
  - Heart Rate (/min)
  - Respiratory Rate (/min)
  - Temperature (°C)
- Anthropometric Measurements:
  - Height (cm/inches)
  - Weight (kg/lb)
  - BMI (calculated)
  - Body Circumferences (waist, hip, limbs)
  - Middle Upper Arm Circumference (MUAC)
- Pediatric-Specific (0-24 months):
  - Length (cm)
  - Head Circumference (cm)
  - Skinfold Thickness (cm)
- Additional Assessments:
  - Visual Acuity
  - Blood Type (if available)
  - General Survey (alertness status)

#### Medical History

- Past Medical History Checklist:
  - Cancer
  - Allergies
  - Diabetes Mellitus
  - Hypertension
  - Heart Disease
  - Stroke
  - Bronchial Asthma
  - COPD/Emphysema/Bronchitis
  - Tuberculosis
  - Other conditions (specify)

#### Social History

- Smoking History:
  - Current status (Yes/No)
  - Duration (years)
  - Type (cigarette, e-cigarette, vape, other)
- Alcohol History:
  - Current status (Yes/No)
  - Duration (years)

#### Female-Specific Data

- Menstrual History:
  - Last Menstrual Period (MM/DD/YYYY)
  - First Menstrual Period (MM/DD/YYYY)
  - Number of Pregnancies

### 3.2 EKAS (Electronic Konsulta Availment Slip) Data Elements

#### Service Documentation

- **Laboratory Services Performed:**
  - Complete Blood Count (CBC) with platelet count
  - Lipid Profile (Total Cholesterol, Triglycerides, HDL, LDL)
  - Fasting Blood Sugar (FBS)
  - Oral Glucose Tolerance Test
  - Glycosylated Hemoglobin (HbA1c)
  - Creatinine
  - Urinalysis
  - Fecalysis
  - Fecal Occult Blood Test
- **Diagnostic Services:**
  - Chest X-Ray
  - Sputum Microscopy
  - Electrocardiogram (ECG)
  - Pap Smear
  - Mammogram
  - Ultrasound (breast, upper abdominal, pelvic)

#### Service Delivery Details

- **Date Performed:** MM/DD/YYYY for each service
- **Performed By:** Healthcare provider identification
- **Service Status:** Completed/Pending indicator

#### Patient Feedback Section

- **Service Receipt Confirmation:** Yes/No
- **Satisfaction Rating:** Happy/Neutral/Sad scale
- **Comments/Suggestions:** Free text field
- **Complaints:** Free text field

### 3.3 EPRESS (Electronic Prescription Slip) Data Elements

#### Prescription Information

- **Medication List:** Generic names required
- **Dosage:** Specific dosage instructions
- **Frequency:** Administration schedule
- **Duration:** Treatment period
- **Next Dispensing Date:** MM/DD/YYYY format

#### Prescriber Information

- **Physician Name:** Full name
- **PRC License Number:** Professional license ID
- **PTR Number:** Professional tax receipt
- **S2 Number:** Narcotic license (if applicable)

#### Patient Feedback Section

- **Medication Receipt Confirmation:** Yes/No
- **Satisfaction Rating:** Happy/Neutral/Sad scale
- **Comments/Suggestions:** Free text field

### 3.4 Provider Registration Data Elements (PKRF)

#### Beneficiary Information

- **Member/Dependent Status:** Classification
- **PIN:** PhilHealth ID number
- **Full Name:** Complete name
- **Address:** Residential address
- **Date of Birth:** MM/DD/YYYY
- **Contact Number:** Primary contact

#### KPP Selection

- 1st Choice KPP:

Primary provider selection

  - Name
  - Address

- 2nd Choice KPP:

Alternative provider

  - Name
  - Address

#### Transfer Information (if applicable)

- **Previous KPP:** Former provider details
- **Transfer Reason:** Justification for change

#### Dependent Registration

- **Minor Dependents:** Complete list
- **Guardian Information:** For dependents under 21

#### Consent and Declarations

- **FPE Certification:** Statement of non-duplication
- **Data Privacy Consent:** RA 10173 compliance
- **Signature:** Legal authorization

### 3.5 Provider Data Record Elements

#### Network Information

- **Provider Name:** Official facility name
- **Network Type:** Public/Private/Mixed designation
- **Mailing Address:** Official correspondence address
- Contact Information:
  - Phone number
  - Email address
  - Fax number

#### Authorized Representative

- **Name:** Last, First, Middle, Extension
- **Position/Designation:** Official title
- Contact Information:
  - Email address
  - Fax number

#### Health Facilities List

- **Facility Name:** Each facility in network
- **Type of Health Facility:** Classification
- **Services Offered:** Capability listing
- **License/Certification:** Accreditation details
- **Validity Period:** License expiration dates

### 3.6 Photo Consent Form Elements

#### Patient Information

- **Name:** Full legal name
- **Address:** Complete residential address
- **Signature:** Legal authorization
- **Date:** MM/DD/YYYY of consent

#### Photo Specifications

- **Size:** Minimum 35mm width x 45mm height
- **Format:** Colored photograph
- **Composition:** Full face, front view, eyes open
- **Quality:** Clear, no shadows, centered

#### Provider Information

- **KPP Name:** Facility name
- **Optional:** Facility logo/branding

------

## 4. SUBMISSION DEADLINES AND COMPLIANCE REQUIREMENTS

### 4.1 Critical Deadlines

#### Monthly Submissions (First Tranche)

- **Preferred:** Daily submission
- **Required:** By 11:59 PM on 7th calendar day of succeeding month
- **Processing:** SAP1 generation on 8th day
- **Payment:** Within 60 days of 8th day

#### Annual Submissions (Second Tranche)

- **Deadline:** 11:59 PM on 7th calendar day of January (following year)
- **Processing:** SAP2 generation on 8th day of January
- **Payment:** Within 60 days of 8th day of January

### 4.2 Submission Methods by Capability

#### High-Capability Providers (eKonsulta + Internet)

- Extract XML files from system
- Upload directly through HCI Portal
- Daily submission recommended

#### Medium-Capability Providers (Online EMR, No eKonsulta)

- Direct data upload capability
- System integration required
- Daily submission possible

#### Low-Capability Providers (No eKonsulta, No Online EMR)

- Manual XML file extraction
- Physical submission via USB to LHIO
- Email submission to LHIO
- Hard copy EKAS/EPRESS completion required

### 4.3 Validation and Processing Requirements

#### System Validation Criteria

- Complete data element population
- Format compliance (dates, codes, identifiers)
- Referential integrity checks
- Duplicate record detection

#### Data Quality Standards

- Mandatory field completion
- Standardized coding systems
- Date format consistency (MM/DD/YYYY)
- Proper use of generic medication names

#### Error Handling

- Incomplete submissions rejected
- System-generated error reports
- Resubmission required for failed validations
- No payment processing for invalid data

------

## 5. REGULATORY COMPLIANCE CONSIDERATIONS

### 5.1 Data Privacy (RA 10173) Compliance

- Patient consent requirements
- Data minimization principles
- Secure transmission protocols
- Audit trail maintenance

### 5.2 PhilHealth Regulations

- Circular No. 2024-0013 compliance
- Universal Health Care (UHC) Law adherence
- DOH implementing rules integration
- Professional licensing requirements

### 5.3 Quality Assurance

- Post-audit monitoring requirements
- Performance target achievement
- Service delivery validation
- Patient satisfaction monitoring

------

## 6. TECHNICAL IMPLEMENTATION NOTES

### 6.1 System Integration Requirements

- PhilHealth-certified application software
- XML file generation capability
- HCI Portal connectivity
- Data encryption for transmission

### 6.2 Backup and Recovery

- USB backup for offline submission
- LHIO coordination for technical issues
- Service provider support channels
- Manual form completion as fallback

### 6.3 Performance Monitoring

- Real-time submission tracking
- SAP generation monitoring
- Payment processing verification
- Error rate analysis

------

## 7. CONCLUSION

The PhilHealth Konsulta data submission framework represents a comprehensive approach to primary healthcare data management in the Philippines. Success depends on understanding the intricate relationships between different datasets, maintaining strict compliance with submission deadlines, and ensuring complete, accurate data element population across all required forms and systems.

Healthcare IT professionals must maintain awareness of the dual-track submission system (first and second tranche), the critical importance of the 7th calendar day deadlines, and the necessity of having robust backup procedures for providers with limited technical capabilities. The framework's emphasis on patient-centered data collection, combined with rigorous performance monitoring, reflects the Philippine healthcare system's commitment to quality primary care delivery through the Universal Health Care initiative.

Regular review of PhilHealth circulars and maintaining current knowledge of system updates remains essential for sustained compliance and optimal reimbursement outcomes for healthcare providers participating in the Konsulta program.