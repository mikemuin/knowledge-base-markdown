# Impact of YAKAP to TECH

For a tech company looking to work with Local Government Units (LGUs) to implement the PhilHealth Konsulta program, understanding the operational landscape, technological requirements, and existing challenges is crucial. PhilHealth's Konsulta benefit package, especially with the introduction of PhilHealth GAMOT, aims to provide comprehensive outpatient care and medication coverage to all Filipinos.

Here are the key things a tech company needs to know:

### 1. Understanding the PhilHealth Konsulta Program and its Scope

- **Universal Eligibility**: All Filipinos are **automatically included** in the National Health Insurance Program (NHIP) and are **immediately eligible** for PhilHealth benefits, regardless of their premium payment status. This policy decouples entitlement from the ability to pay premiums.
- **Konsulta Package Providers (KPPs)**: Services are delivered through PhilHealth-accredited KPPs, which are primary care providers. These can be public or private facilities that manage a "catchment population" – registered patients or clients within their area of responsibility.
- Covered Services:
  - **Primary Care Services**: Include consultations, individual health education, preventive health services (based on life stage guarantees), assistance in accessing services from subcontracted or partner facilities, and referrals to specialty/higher levels of care.
  - **Select Diagnostic/Laboratory Examinations**: Covers 15 tests, including Complete Blood Count, Lipid Profile, Fasting Blood Sugar, Oral Glucose Tolerance Test, Glycosylated Hemoglobin, Creatinine, Chest X-Ray, Sputum Microscopy, Electrocardiogram (ECG), Urinalysis, Pap smear, Fecalysis, and Fecal Occult Blood Test. **Mammogram and Ultrasound (specifically for breast, upper abdomen, and pelvic areas)** have been added to the covered services.
  - **Medications**: The **21 specific drugs and medicines** previously covered under Konsulta (e.g., antimicrobials, anti-asthma, anti-diabetics, anti-hypertensives, anti-thrombotics, and anti-histamines) are now **subsumed under PhilHealth GAMOT** for their dispensing and payment. PhilHealth GAMOT covers a broader "GAMOT List" of essential medications from the Philippine National Formulary, with a fixed fee schedule. Each beneficiary has an **Annual Benefit Limit (ABL) of Twenty Thousand Pesos (PHP 20,000.00)** for these outpatient drugs.

### 2. Operational Processes and Data Management Needs

- Registration and Identification:
  - Beneficiaries must **register with a specific KPP** to avail Konsulta benefits.
  - While not a legal precondition, a **PhilHealth Identification Number (PIN) remains operationally critical** for providers to file claims, verify eligibility, and receive reimbursements. The law mandates harmonizing PhilHealth’s database with the Philippine Identification System (PhilSys).
  - **Documentation barriers** persist, as patients often lack required documents for registration, leading to delays. PhilHealth accepts a joint affidavit from two disinterested persons if official documents are unavailable, but this raises concerns about data reliability.
  - A **photograph of the patient is now required** at the KPP for benefit availment, primarily for post-audit monitoring purposes.
- Verification at Point of Service:
  - Health care facilities must **verify a patient's PhilHealth status** (PIN and membership category) in the PhilHealth system. This is typically done through the Health Care Institution (HCI) Portal to generate a PhilHealth Benefit Eligibility Form (PBEF).
  - **Current IT infrastructure struggles** with seamless, real-time verification due to unreliable internet connectivity, especially in remote areas. This often necessitates **manual verification** (e.g., phone calls to LHIOs), leading to delays, especially during weekends and holidays when PhilHealth offices are closed.
  - **Data duplication** can occur when facilities create new records for existing members due to verification failures, compromising data integrity.
- Medication Dispensing (PhilHealth GAMOT):
  - **Authorized physicians** use the **GAMOT App** to prescribe medications, which generates a **Unique Prescription Security Code (UPSC)**.
  - **GAMOT Facilities** (drugstores, hospital pharmacies, or primary care facilities with dispensing capacity) use the GAMOT App to dispense the prescribed medications and record transactions.
  - An **Annual Benefit Limit (ABL) of PHP 20,000.00** applies to PhilHealth GAMOT, which resets annually. Beneficiaries receive a **GAMOT Availment Slip (GAS)** showing their remaining ABL balance.
- Reporting and Claims Processing:
  - KPPs must **encode all consultation data** and services rendered (diagnostics, dispensed medicines) and submit XML files regularly (preferably daily) through the HCI Portal or Local Health Insurance Offices (LHIOs).
  - The **Electronic Konsulta Availment Slip (EKAS)** and **Electronic Prescription Slip (EPRESS)** are key documents for reporting services provided and medicines prescribed. Hard copies can be used if system-generated versions are unavailable.
  - **Delayed contribution posting** and issues with system validation can affect timely benefit eligibility determination and payments.

### 3. Financial Mechanisms for KPPs

- **Capitation Rate**: Effective January 1, 2024, the maximum annual capitation rate paid to KPPs increased to **PHP 1,700.00** per beneficiary for both government and private facilities.
- Payment Tranches:
  - **40% (First Tranche)**: Paid based on the number of registered members with a "First Patient Encounter" (FPE). Subsequent years for retained beneficiaries do not require a new FPE if a medical consultation was conducted in the previous year.
  - **60% (Second Tranche)**: Based on the number of registered members with FPE and their **achievement of performance targets** (e.g., utilization rates of primary care consultations, laboratory services, and medicine dispensing).
- **Co-payment/Balance Billing**: **Private KPPs are allowed to charge a co-payment cap of up to PHP 900.00** per annum per beneficiary. Public KPPs are subject to the No Balance Billing (NBB) policy.

### 4. Opportunities and Challenges for a Tech Company

- Strengthening IT Infrastructure and Data Management:
  - There is a critical need for **real-time verification** capabilities, including support for weekends and after-hours, and offline verification protocols during system downtimes.
  - Developing an **integrated IT system** accessible to all facility types (including primary care) with **automated key-process mechanisms, document scanning, and digital form processing** is a high priority.
  - **Interoperability with national databases** (PhilSys, DSWD, PSA, BIR) is essential to reduce duplication and ensure accurate beneficiary information.
  - Enhancing **digital payment platforms** for informal sector workers and streamlining processes for government subsidies for indirect contributors would address financial sustainability concerns.
- Streamlining Registration and Verification:
  - Implementing **automated matching systems** to reduce duplicate records and a **unified verification system** accessible across all health care facilities would significantly improve efficiency and data integrity.
  - Designing **emergency verification procedures** for urgent cases is vital to ensure continuous service delivery.
- Building Capacity at Facility Level:
  - The tech company can provide solutions that are user-friendly and offer **continuous capacity building** and **technical support** for KPP staff, especially in primary care settings where resources and digital literacy may be limited.
  - Developing clear, **standardized implementation guidelines** integrated into the technology would help ensure consistent policy application.
- Enhancing Communication and Awareness:
  - Developing digital tools for community-based awareness programs could help beneficiaries understand their entitlements and how to access them seamlessly.
- Addressing the "Value Gap":
  - While not directly a tech function, understanding that Filipinos still incur substantial out-of-pocket expenses despite coverage means that technology solutions which enable **better monitoring of service utilization and outcomes** can support PhilHealth in designing more impactful and financially protective benefit packages.

By focusing on these areas, a tech company can provide valuable solutions to PhilHealth and LGUs, bridging the gap between policy intent and operational reality, and ultimately improving access to health services for all Filipinos.