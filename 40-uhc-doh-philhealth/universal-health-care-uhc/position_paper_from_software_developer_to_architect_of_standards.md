## Position Paper: Shifting the Paradigm—The National Government’s Role in Digital Health Must Transition from Software Developer to Architect of Standards

### I. Introduction and Thesis

The Universal Health Care (UHC) Act (Republic Act No. 11223) rightly identifies a robust and integrated Health Information System (HIS) as fundamental to achieving equitable access to quality healthcare in the Philippines. While the law mandates that the Department of Health (DOH) and the Philippine Health Insurance Corporation (PhilHealth) fund and provide HIS software, such as iClinicSys and iHOMIS, "at no cost" to resource-constrained entities, this strategy has created a fundamental misalignment between legal mandate and operational efficiency.

This position paper asserts that the national government’s commitment to providing free electronic medical record (EMR) software, while intended to solve resource gaps, actively **hinders the realization of UHC objectives** by stifling market innovation, burdening frontline workers with inefficient tools, and diverting critical national resources away from the primary necessity: **establishing and enforcing mandatory, interoperable national standards.** The DOH must urgently cease its role as the software *carpenter* and embrace its indispensable function as the digital *road map architect*.

### II. Foundational Arguments: The Conflict of Roles

#### A. Operational Deficiencies Undermine UHC Goals

The goal of the UHC Act is an integrated system providing seamless continuity of care. However, the UHC-IS assessment reveals that existing DOH-provided EMRs are critically perceived as prioritizing top-down reporting over essential frontline operations, imposing a significant burden on health workers.

1. **Reporting over Care Delivery:** DOH EMRs were often designed with the "statistician's requirement at the national level" in mind, rather than the needs of the health provider or clinician. This focus turns the software into merely a "data gathering and reporting" tool, creating an "added burden" that regional operations staff find "very frustrated" to implement.
2. **Fragmentation and Inefficiency:** The poor operational functionality of government systems contributes directly to the fragmentation they were meant to resolve, forcing healthcare workers into the burden of **double or triple encoding** patient information across multiple, non-interoperable platforms (such as local EMRs, DOH systems, and PhilHealth's claims systems).
3. **Unmet Capacity Requirements:** While the DOH is mandated to develop and finance HIS, providing resources only for government facilities, the national government has been unable to support the essential accompanying operational needs, particularly sufficient IT human resources (HRH) at the local level due to contractual limitations and Personal Services (PS) caps. This systemic failure to provide necessary complementary resources (staff, hardware, training) means the government can only effectively service a fraction of the public health sector.

#### B. The Mandate Requires Architecture, Not Application Development

The core responsibility of the DOH, as the "overall leader of the health sector", is defined by setting the **national policy direction, plan, technical standards, and guidelines**. The shift needed is recognized within the Enterprise Architecture review:

1. **Software Safety and Regulation Gap:** The government, in its role as a software publisher (for systems like iHOMIS or iClinicSys), **does not presently employ ISO-13485 conformant practices** regarding eHealth software development. This weak internal quality-control, despite the software acting as a potential "medical device" (Software as a Medical Device, or SAMD), constitutes a patient safety risk. The DOH's capacity should focus on regulation, ensuring EMR software is "patient-safe" and adopting appropriate certification and market-wide regulation.
2. **Stifled Competition:** Because the private sector is not imposed upon to exclusively use DOH software, it is driven by **market-based forces**. Private hospitals utilizing commercial EMRs have reported satisfaction with the performance and support of these third-party systems, leading to a challenge in mandating the use of a single government HIS. The DOH's continuing effort to mandate or promote its internally developed EMRs (which were historically targeted to "unseat all other EMR solutions") acts as a deterrent to external innovation, restricting the availability of more suitable operational tools for public health delivery.

### III. Core Positions

#### Position 1: The National Government Must Focus Exclusively on Standards, Policy, and Regulation.

The DOH's energy and budget must be **strategically redirected from coding and maintenance to architecture and enforcement**. The true UHC mandate for HIS is the creation of a **national interoperable digital health ecosystem**, which requires the DOH to act as the "road map architect".

- **Policy Imperative:** The DOH must prioritize foundational governance and technical standards. This means focusing on the **Standards Conformance and Interoperability Validation (SCIV) framework** to ensure systems can seamlessly exchange data.
- **Architectural Mandate:** The DOH's role must be centered on the **OpenHIE framework**, prioritizing the investment and deployment of **foundational registries**—including terminology services, client registry, health facility registry, and FHIR messaging standards—as the single source of truth.

#### Position 2: If the DOH Cannot Service the Entire Public Health Sector, It Must Open the Market.

The current system of non-transferrable, under-supported government software does a disservice to the public health sector. If the government cannot ensure the necessary funding (including dedicated budget lines for HIS) and the full HRH complement to implement and support its own EMRs across all Local Government Units (LGUs), it should immediately step aside and let the specialized Health IT market service public health facilities.

- LGUs that opt to acquire or internally develop their systems must **shoulder all costs**. However, the DOH must ensure a marketplace where all third-party vendors are rigorously tested against national standards and incentivized by PhilHealth reimbursements to deploy fully functional, interoperable systems. This fosters competition focused on quality and compliance, rather than competing with a free, yet insufficient, government alternative.

#### Position 3: Leverage Private Sector Innovation through Strict Standards and Incentives.

The lack of imposition of DOH software on the private sector has allowed commercial systems to reach a satisfactory performance level driven by market demand. This market capability must be leveraged for public health.

- **Incentivization through PhilHealth:** PhilHealth contracts must be used to **incentivize the incorporation of health information systems**, automation, and improved data quality. We must embed incentives and disincentives that **reward real-time, standards-compliant EMR use** and **require exclusive use of interoperable systems**. This shift allows the market to compete on quality while ensuring conformance to national architectural needs (like the National Health Data Repository—NHDR).

### IV. Recommendations: The Architect’s Roadmap

To fulfill the true objectives of UHC, the following phased actions, largely supported by the UHC-IS assessment, must be implemented immediately:

| Priority                      | Action                                                       | Responsibility   | Basis in Sources |
| ----------------------------- | ------------------------------------------------------------ | ---------------- | ---------------- |
| **I. Immediate Transition**   | **1. Shift to Managed Services:** Fast-track the transition of government EMRs (iClinicSys, iHOMIS) to a **managed service model** to outsource system development and support, thereby addressing endemic usability issues and redirecting DOH resources. | DOH (KMITS)      |                  |
|                               | **2. Discontinue Interim Systems:** PhilHealth must establish a firm timeline to **discontinue the interim eKonsulta solution** and accelerate the accreditation of standards-compliant EMR providers. | PhilHealth       |                  |
| **II. Focus on Architecture** | **3. Prioritize Foundational Registries:** DOH must prioritize investment and deployment of **foundational registries** and adopt **HL7 FHIR messaging standards**. This is the necessary technical backbone for the **NHDR**. | DOH              |                  |
|                               | **4. Enforce True Interoperability:** Review the SCIV framework, establish a **test bed for EMR providers** against mandatory interoperability standards, and **sunset the Google Forms-based practice** of data collection/reporting. | DOH (SCIV/KMITS) |                  |
| **III. Governance and HR**    | **5. Strengthen Leadership Mandate:** Secure **cabinet-level participation** in the inter-agency digital health governance committee, **finalize the National Digital Health Strategy**, and ensure **dedicated HIS budget lines** are secured in appropriations. | DOH/DICT/DILG    |                  |
|                               | **6. Institutionalize Digital HRH:** Strategically utilize the DOH National Health Workforce Support System to deploy IT personnel to LGUs and explore leveraging the Local Infrastructure Program for Health (LIPH) budget to hire IT personnel, addressing the PS cap issue. | DOH, DILG, DBM   |                  |
| **IV. Legislative Anchor**    | **7. Pursue Dedicated Legislation:** Actively advocate for a dedicated **eHealth/Digital Health Law** to provide statutory backing for the governance structure, prioritized core data sets, standards, and the conformance pathway for interoperability. | DOH/Congress     |                  |