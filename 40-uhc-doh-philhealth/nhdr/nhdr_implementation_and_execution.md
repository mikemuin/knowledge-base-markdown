# NHDR Implementation and Execution

The implementation and execution of the National Health Data Repository (NHDR) involves a multi-phased, highly structured digital transformation of the entire national health ecosystem, focusing heavily on mandatory standardization and rigorous validation of local data systems before information can be securely submitted to the central repository established and maintained by PhilHealth.

The pathway for data flow, from the healthcare provider (HCP) to the NHDR, relies on a modular, phased approach called the Integrated Health Information System Implementation Model (iHISIM).

### Technical Flow: From Healthcare Provider to Repository

The execution of the NHDR is based on the mandated submission of health and health-related data by all public and private health-related entities to PhilHealth through the NHDR.

#### 1. Data Generation at the Source (Healthcare Provider/Entity)

1. **Mandatory System Implementation:** All HCPs and insurers are required to **implement and maintain an Integrated Health Information System (iHIS)**. This iHIS must consist of core components such as enterprise resource planning, human resource information systems, **electronic health records (EHR)**, and an electronic prescription log.
2. **Modular System Rollout:** The implementation follows a phased plan, the iHISIM, where core modules are deployed cumulatively over about 36 months:
   - **Stage 1 (1–18 months):** Clinical modules like Electronic Health/Medical Record, Referral System Management, Telemedicine, Laboratory & Diagnostics, Electronic Prescription & Dispensing, Human Resource Information, Clinical Decision Support, and PhilHealth Electronic Claims Processing & Provider Payment.
   - **Stage 2 (19–33 months):** Non-clinical modules like Enterprise Resource Planning, Supply Chain Management, Financial & Capital Asset Management, and Quality Management System.
3. **Data Entry:** All patient or individual-based data entries must be done through **validated Electronic Medical Record (EMR) systems** (such as the DOH-developed *iClinicSys*) or other validated information systems available in the respective reporting units. This data relates to individual and population health and well-being, including administrative, public health, medical, pharmaceutical, and health financing data.

#### 2. Standardization and Validation (Readiness for Submission)

1. **Adoption of Standards:** All implementing systems (iHIS/EMR) must mandatorily adopt and use the **National Health Data Standards (NHDS)** for interoperability, which are a set of standardized terminology, definition, and structure. The full, updated list of standards is published in the DOH-maintained **National Health Data Dictionary (NHDD)**.
2. **Standards Conformance Validation (SCIV):** Regardless of how the system was developed (in-house, outsourced, or DOH-provided), the application software must undergo and **pass the Standards Conformance and Interoperability Validation (SCIV)** by the DOH, PhilHealth, and the Department of Information and Communications Technology (DICT). This validation confirms conformance with the NHDS and compliance with rules on data processing and submission. The successful passing of SCIV is a **minimum requirement for licensing and accreditation** starting October 2022.
3. **Data Format:** The data collected by the EMR systems will be exported to the repository system, often in an **aggregated format**, via an **Application Programming Interface (API)** for national reporting, such as for the Routine Information and Statistics for Enhancement of Public Health (RISE PH) system.

#### 3. Submission to the NHDR (The Interoperability Layer)

1. **Single Point of Submission:** Health and health-related data must be submitted to PhilHealth through the NHDR, which is designated as the **single point-of-submission** and the **Single Source of Truth (SSOT) for Health**.
2. **Data Flow Models:** Submission can occur via two models:
   - **Centralized Model:** Entities submit data directly to the NHDR.
   - **Decentralized Model:** Health-related data must undergo processing at national or local government units before submission (e.g., PSA data on birth/death).
3. **Interoperability Layer Functions:** The transmission passes through the **Interoperability Layer**, which serves as the single point of entry and performs crucial processing tasks before storage:
   - **Validation:** Checking compliance with mandatory **data codes** and **data quality rules**.
   - **Security/Privacy Checks:** Verifying compliance with **data privacy, confidentiality, and security rules**.
   - **Routing and Transformation:** Handling data routing, transformation, messaging, queuing, and staging of the data.
   - **Identification:** Identifying and authenticating users and checking their access rights and privileges.

#### 4. The Repository Infrastructure (NHDR Components)

The NHDR, maintained by PhilHealth, is a central database system composed of three architectural components:

1. **Data Storage Component:** This is the physical storage, which includes an **Operational Database** (for recording incoming transactional data using OLTP) and a **Data Warehouse** (for storing critical historical data extracted, transformed, and formatted for analytical use using OLAP). The NHDR adopts a cloud computing approach as the preferred ICT deployment strategy.
2. **Compute Services Component:** These are the resources that ensure the NHDR is available and functional. This component manages security, user accounts (identification and authentication), standard registries (like the National Health Workforce Registry), operational database integrity, and **Business Intelligence and Analytics (BIA) management**. This component handles the Extract-Transform-Load (ETL) process necessary to integrate, cleanse, and load raw data into the Data Warehouse for analysis.
3. **Client Services Component (Philippine Health Services & Data Access System):** These are the systems clients interact with, including the **Dataset Submission** system, a **Business Intelligence and Analytics** platform for unified data analysis and reporting, a **Data Access Request (DAR)** platform, and an **Open Data (OD)** platform.

Once data is processed and stored, PhilHealth must provide the DOH access to all submitted data via the NHDR, requiring a formal service/operational level agreement (SLA) for data sharing.

------

### Brutally Honest Gaps in Implementation

While the framework and policy documentation provide an exhaustive blueprint, the sources reveal significant gaps, particularly regarding the readiness and functional maturity of implementation on the ground:

| Area of Concern                                | Specific Gap/Issue                                           | Supporting Evidence                                          |
| ---------------------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| **Functional Interoperability (EHR Maturity)** | The critical step of having validated EMR systems that can link primary care providers to secondary and tertiary care providers (Level 3 functional requirement) is highly immature. **Only 8%** of UHC implementation sites had achieved this functional level as of late 2023. | Only **8%** have a validated EMR system that links PCPN to secondary and tertiary care providers within P/CWHS. |
| **System Adoption Rate**                       | While foundational system implementation is high for rural health units (94.25% adopted EMRs) and public hospitals (68.30% adopted iHOMIS), the critical step of *validation* and *interoperability* remains low. | **Only 31%** of P/CWHS facilities reported a functional EMR system. |
| **Policy/Operational Detail Gaps**             | Although policy issuances mandate the establishment of the NHDR and its components, many crucial operational and technical details must still be formalized. | Operational policies still need to be formulated by DOH and PhilHealth, including detailed **Data Classification**, the **SCIV testing methodology and Toolkit**, SOPs on **data submission to the NHDR**, and **unified operational guidelines** defining specific procedures and quality standards. Action points identify the need to finalize priorities of health and health-related data for Phase 1 and 2 implementations. |
| **Long Implementation Timeline**               | The process is projected to take nearly a decade to fully integrate and scale up, suggesting that achieving the "single source of truth" across all data domains is a very long-term goal. | Phase 1 began in 2022, and the full scope of Phase 2 (scaling up the model for health and health-related data) is scheduled to run continuously until at least **2029**. |
| **Data Consistency Challenge**                 | A major objective of the framework is to address recurring problems with *poor quality* and *untimely generation and reporting* of data due to inconsistent standards adoption. The success of the NHDR hinges entirely on the mandatory adoption of NHDS, which is historically a weak point across the fragmented health system. | At present, there is **inconsistent adoption of health data standards** across the healthcare service delivery chain. |
| **Infrastructure and Resource Gap**            | While PhilHealth is tasked with providing the system, HCPs must still allot counterpart resources for **human resources, server and hardware, connectivity, and infrastructure** to utilize the system, which poses a significant hurdle, especially for lower-resourced facilities. | Health care providers and insurers must ensure availability of needed investments and support mechanisms to fully implement and maintain an integrated HIS such as funding, human resources, hardware, infrastructure and other qualifying requirements. |