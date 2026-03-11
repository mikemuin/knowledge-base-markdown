# DOH Health Sector Enterprise Architecture Briefing

## Executive Summary

This briefing document summarizes the updated Health Sector Enterprise Architecture (HSEA) for the Philippines' Department of Health (DOH), aligning it with the Universal Health Care (UHC) Law, the Health Sector Strategy 2023-2028, and the COVID-19 pandemic-to-endemic transition. The DOH, as the lead health agency, aims to rationalize, streamline, integrate, and implement various operating models to achieve its health goals. The HSEA leverages the TOGAF framework and the Sinag Enterprise Architecture Model to harmonize people, processes, information, and technology, fostering efficiency, innovation, and data-driven decision-making across the health sector. Key initiatives include digitalization, strengthening human resources for health, improving health information management, and enhancing regulatory and supply chain processes.

## 1. Introduction and Rationale

The DOH plays a pivotal role in the well-being of the Filipino population, with mandates encompassing health promotion, disease prevention, healthcare service delivery, policy development, and emergency response. Its three distinct roles are **Leadership in Health**, **Enabler and Capacity Builder**, and **Administrator of Specific Services**.

The **Universal Health Care (UHC) Law**, enacted in February 2019, is a landmark legislation aimed at ensuring "equitable access to quality healthcare services for all Filipinos." It mandates enrollment in the National Health Insurance Program (NHIP), expands benefit packages, emphasizes primary care, and allocates resources for healthcare infrastructure and workforce development. A crucial aspect is the "integration of health information systems and technologies" to facilitate efficient data collection and communication.

The **rationale for updating the HSEA** stems from the 2016 policy shifts, the implementation of the Health Sector Strategy 2023-2028, the UHC Law, and the COVID-19 pandemic-to-endemic transition. The ultimate goal is an "integrated sector-wide HEA that both supports the most efficient and secure IT environment, and contiguously responds and meets the sectoral business, data, and resource needs in an efficient, agile, adaptable and sustainable manner at all levels of healthcare utilization and management."

## 2. Enterprise Architecture Framework and Model

The HSEA update utilizes **The Open Group Architecture Framework (TOGAF)**, a widely recognized methodology for designing, planning, implementing, and governing enterprise IT architectures. Its core is the **Architecture Development Method (ADM)**, a step-by-step framework that moves from defining goals and vision (Architecture Context) to developing detailed architectures (Business, Application, Information, Technology), planning transitions, and establishing governance.

The **Sinag Enterprise Architecture Model** aligns with TOGAF, emphasizing foundational elements:

- **Goals and Key Performance Indicators (KPIs):** Defining success and benchmarks.
- **Business Processes:** Designed to achieve goals and measured by KPIs.
- **Four Key Components for Process Execution:** People, Data, Applications, and Technology.

This structured approach aims to:

- Harmonize operations with strategic vision by identifying and rectifying redundancies and inefficiencies.
- Streamline workflows, optimize resource allocation, and eliminate duplication for "elevated efficiency and productivity levels."
- Foster innovation by cultivating a "dynamic, flexible, and adaptable environment" through modular design.
- Elevate "data-driven decision-making" by centralizing data repositories and ensuring unobstructed information flow.
- Act as a "custodian of security" by establishing standardized protocols and compliance frameworks.
- Serve as a "common language," bridging communication gaps between departments and fostering collaboration.

## 3. Architecture Vision, Goals, and KPIs

The **Architecture Vision** establishes a high-level description of the enterprise architecture, outlining strategic goals, business needs, and fundamental concepts. **Goals** are desired outcomes, while **KPIs** are quantifiable metrics to gauge progress. Clear articulation of these ensures alignment with business objectives and informed decision-making.

The DOH HSEA directly aligns with the **Health Sector Strategy 2023-2028** and the **Secretary of Health's Eight-Point Agenda**:

### Health Sector Strategy KPIs:

The document lists 33 KPIs across four categories:

- **Better, Equitable Health Outcomes:** e.g., Average Life Expectancy, Maternal mortality ratio.
- **Responsive Health System:** e.g., Responsiveness score, Client satisfaction rate.
- **Financial Risk Protection:** e.g., Household out-of-pocket health spending, Incidence of catastrophic health expenditure.
- **ENABLE to be Healthy:** e.g., Comprehensive health literacy, Health facility visits.
- **PROTECT from Health Risks:** e.g., Health event response, Fully immunized children, Regulatory compliance.
- **CARE for Health and Wellness:** e.g., PhilHealth registration, Functional HPCNs.
- **STRENGTHEN Institutions and Workforce:** e.g., Positions filled, EMR adoption, HRH ratio.

### Secretary of Health's Eight-Point Agenda:

1. **Bawat Pilipino ramdam ang kalusugan (every Filipino should feel healthy):** Mainstream primary health care.
2. **Ligtas, kalidad, at mapagkalingang serbisyo (safe, quality, and compassionate service):** Ensure high-quality, safe, and people-centered services, including affordable medicines.
3. **Teknolohiya para sa mabilis na serbisyo (technology for prompt services):** Leverage digital health and technology for efficient service delivery and data utilization.
4. **Handa sa krisis (crisis-ready):** Ensure a responsive and resilient health system for emergencies.
5. **Pag-iwas sa sakit (disease prevention):** Address health determinants, promote healthy behaviors, and enhance health literacy.
6. **Ginhawa sa isip at damdamin (mental and emotional comfort):** Enhance well-being and ensure quality mental health services.
7. **Kapakanan at karapatan ng health workers (welfare and rights of health workers):** Ensure adequate, competent, and committed health workforce with fair compensation and development opportunities.
8. **Proteksyon sa anumang pandemya (protection against any pandemic):** Strengthen health systems for prevention, management, and recovery from outbreaks.

## 4. Architecture Principles

Nine architectural principles guide the HSEA, ensuring alignment and consistency:

- **Business Process Transformation:** "We will transform our processes so that we deliver greater and long-term value." Implies change management, shared functions, and standardized processes.
- **Compliance to Laws, Rules & Regulations:** "Enterprise policy is to abide by laws, policies, and regulations." Emphasizes data management compliance and regulatory changes driving process/application changes.
- **Data is Shared:** "Data Driven where data can be delivered as to when, where, and how needed to support the requirements of the users." Aims for timely access to accurate data from fewer, ultimately single, virtual sources.
- **People Centric:** "DOH will be responsive, proactive, accessible, and will manage properly all information from the citizenry." Ensures technology addresses citizen needs and fosters team collaboration.
- **Requirements-Based Change:** "Only in response to business needs are changes to applications and technology made." Focuses on health outcomes, not technology needs, and minimizes unintended operational effects.
- **Security Driven:** "Security is driven to protect the systems and information from unauthorized access, changes, or destruction." Highlights designing security from the start and data-level protection.
- **System Interoperability and Reusability:** "Designing information systems to interoperate based on reusable component services whenever there are changes and new mandates will optimize resources, reduce redundancy, and allow systems to adapt to changing requirements."
- **System Resiliency:** "Resilience is where systems are built to be stable, reliable, maintainable, flexible, robust, and extensive enough to meet the requirements of the organization."
- **Use of Standards:** "Standards Driven for better interoperability of information systems, ease of transfer, ease of harmonization and/or integration, and optimal use of resources."

## 5. Architectural Components and Capabilities

The HSEA defines key architectural components:

- **Architecture Definition Document (ADD):** Provides an overview of the enterprise's business, data, application, and technology architectures.
- **Domain Architecture Requirements Specification:** Outlines specific architectural requirements for a particular domain.
- **Domain Architecture Components:** Fundamental building blocks within a specific functional area.
- **Business and IT Capability Assessment:** Appraises the organization's capacity to deliver outcomes (Business Capability) and the efficacy of its technological infrastructure (IT Capability).
- **Reusable Building Blocks:** Standardized, modular components to streamline IT systems and business process development.

### Business Capability Model:

The HSEA presents a **Health Sector Business Capability Model** patterned from the UHC Policy to Practice document, depicting 12 top-level capabilities:

1. **Vision and Strategy Development:** Defining long-term vision and managing strategic initiatives.
2. **Local Health System Management:** Assessing maturity, developing investment plans, contracting services, monitoring LGU performance.
3. **Primary Care and Facility Management:** Licensing, permit applications, quality accreditation, patient registration to providers.
4. **Population-based Health Services Management:** Health promotion, surveillance, disaster risk reduction, vector control, water quality assurance.
5. **Individual-based Health Services Management:** Provision of services chargeable to Special Health Fund, including ambulatory and inpatient care.
6. **Health Financing Management:** Establishing Special Health Funds, PhilHealth's global budget scheme, No Balance Billing, HTA processes.
7. **Good Governance and Accountability Management:** Conflict of interest management, financial relationship tracking, Public Health Ethics Committee.
8. **Human Resources for Health Management:** Workforce planning, development, and management across education and labor markets.
9. **Health Information Management:** Developing and maintaining an integrated health information system (iHIS), national health data standards, electronic medical records, UHC IS.
10. **Health Services and Goods Regulation Management:** Regulatory measures for quality, safety, efficacy, National Practice Guidelines, HTA process, price regulation of medicines.
11. **Asset Management and Supply Chain Management:** Streamlining procurement, capacity building, maintenance processes for assets and facilities.
12. **Futures Thinking:** Strategic capacity to envision, plan for, and adapt to evolving healthcare landscape.

### Application Landscape Model:

The model visually represents the organization and interactions of different applications, systems, data stores, and interfaces, grouped into categories:

1. Healthcare Providers and Facilities Information Systems (e.g., EHRs, patient management)
2. Payer Information Systems (e.g., insurance, claims, billing)
3. Asset and Supply Chain Information Systems (e.g., procurement, inventory)
4. HR for Health Information Systems (e.g., employee information, recruitment)
5. Regulatory, Compliance, and Monitoring and Evaluation Information Systems
6. Public Health and Policy Information Systems (e.g., disease surveillance, outbreak management)
7. Research and Technical Assistance Information Systems
8. Integration (Integration and IT) and Healthcare Data Analysis
9. Patient Information Systems (e.g., patient portals, health apps)

The report includes a proposed **Digital Health Ecosystem** and its mapping against the Secretary’s 8-point agenda.

### Health Sector Data:

The importance of well-defined data sets and data elements is highlighted for uniformity, interoperability, and efficient management. The document lists **38 categories of health sector data**, ranging from Accidents/Injuries to Workplace Safety and Health Standards. Standardized data elements are crucial for "quality patient care," "regulatory requirements," "medical research," and "informed decision-making."

## 6. Health Sector Strategic Digital Programs

Eight strategic digital programs are identified, aligning with the Secretary's 8-point agenda and various DOH bureaus:

- **DOH Digitalization:** Lead by KMITS, addresses "Teknolohiya para sa Mabilis na Serbisyo."
- **HRH Workforce:** Lead by HHRDB, addresses "Kapakanan at Karapatan ng Health Workers."
- **EMR Implementation & RISE:** Lead by Epidemiology Bureau and KMITS, addresses "Ligtas, Dekalidad, at Mapagkalingang Serbisyo" and "Teknolohiya para sa Mabilis na Serbisyo."
- **National Health Data Repository (NHDR):** Lead by PhilHealth, aligns with all 8 agenda points.
- **National Health Information Exchange (NHIE):** Lead by KMITS and PhilHealth, aligns with all 8 agenda points.
- **UHC IS and P/CWHS Integration:** Lead by PhilHealth and BLHSD, addresses "Bawat Pilipino, Ramdam ang Kalusugan."
- **Digital Vaccinations:** Lead by Epidemiology Bureau, addresses "Pag-iwas sa Sakit."
- **Healthcare Financing System:** Lead by FMS and HPDPB, aligns with all 8 agenda points.

## 7. Enterprise Architecture Artifacts (Bureau-Specific Insights)

The report provides detailed overviews of various DOH bureaus, their mandates, organizational structures, core procedures, current information systems, challenges, and roadmaps.

**Key Themes from Bureau Overviews:**

- **Digitization and Automation:** A recurring theme is the desire and plan to move away from manual, paper-based processes and Google Sheets to integrated information systems. This is seen as crucial for efficiency, accuracy, and timely reporting (e.g., OSEC, LS, HPDPB, FMS).
- **Data Quality and Standardization:** Many bureaus highlight issues with "inconsistent and incomplete inputs," "duplicates," and "misspelled data." The need for data standardization and governance is a critical concern (e.g., HPB, EB-A, EB-B, HFDB, BLHSD).
- **Interoperability and Integration:** There's a strong emphasis on integrating disparate systems within and across bureaus and with external stakeholders (e.g., KMITS, EB-A, EB-B, SCMS, BOQ, HHRDB, PD). The National Health Data Repository (NHDR) and National Health Information Exchange (NHIE) are key initiatives for this.
- **System Development and Enhancement:** Bureaus are actively developing or enhancing internal information systems, often with support from KMITS. Examples include ILIMS (OHL), MAIS (MPO), HFP (HFDB), POMIS (PS).
- **Capacity Building and Training:** Recognition of the need to build the capacity of personnel to utilize new systems and adapt to digital transformation (e.g., HHRDB, KMITS, BIHC, FOSM).
- **Regulatory Compliance and Accountability:** Ensuring that all operations align with laws, rules, and regulations, particularly the UHC Act (e.g., LS, IAS, HFSRB).
- **Addressing Workforce Challenges:** Initiatives to improve HRH planning, deployment, and welfare (e.g., HHRDB, AS).
- **Resource Constraints:** Issues such as understaffing, insufficient ICT equipment, and server capacity are noted as challenges hindering progress (e.g., KMITS, HHRDB, SCMS).
- **Centralized vs. Decentralized Systems:** Some bureaus aim for centralized systems (e.g., HPDPB with its proposed HPDPB Centralized Info System), while others face challenges due to varied local implementations (e.g., EB-A with DRUs).
- **Strategic Alignment:** Projects are explicitly mapped to the UHC Act, Health Sector Strategy KPIs, and the Secretary's 8-Point Agenda, demonstrating a concerted effort towards overarching goals.

**Examples of Bureau-Specific Projects/Needs:**

- **KMITS:** Focus on developing core IT infrastructure (Data Center, Network Security), integrating DOH systems (iHIS, PHIE), and establishing data governance (DWH, DEX, EDC, MDR).
- **HHRDB:** Developing the National Health Workforce Registry (NHWR) and a PSSP Monitoring System to track scholars and deployed HRH, enhancing PTIS for payroll and HR statistics.
- **HPDPB:** Creating a centralized information system for policy, planning, and research processes, moving away from Google Sheets.
- **HFSRB:** Full implementation of the Online Licensing Regulatory System (OLRS) to digitize licensing processes and address data inconsistencies.
- **BOQ:** Fast-tracking the Border Surveillance System and developing the QSIHSS to fulfill its original purpose of international health surveillance.
- **MPO:** Deploying MAIS to the LGU level and implementing the Unified Intake Sheet Portal and Unified Reporting System for medical assistance.
- **SCMS:** Full operationalization and integration of the Electronic Logistics Management Information System (eLMIS).
- **FOSM (FICT):** Critical need for a bureau-wide information system to improve data analysis, reduce manual data collection, and provide better visibility for LGU prioritization.

## 8. Implementation and Governance

### Architecture/Implementation with Costing Analysis:

**Phase E (Opportunities and Solutions)** focuses on identifying and evaluating strategic opportunities for architectural enhancement. This includes:

- Reaffirming architectural principles and requirements.
- Identifying opportunities by analyzing strategic goals and pain points.
- Crafting tailored architectural solutions.
- Assessing feasibility (technical, financial, operational, organizational).
- Prioritizing solutions based on strategic alignment and potential benefits.
- Developing transition architectures with intermediate states and migration plans.
- Ensuring solutions align with business strategy, involve stakeholder collaboration, mitigate risks, and plan for organizational changes.

### Implementation and Migration Plan:

**Phase F (Migration Planning)** crafts a comprehensive transition plan from the current to target architecture, including:

- Assessing migration readiness and evaluating risks.
- Detailing implementation and migration strategies, timelines, and resource allocations.
- Designing transition architectures for smooth transitions.
- Preparing stakeholders and the organization for change.

### Implementation Governance Model:

An effective enterprise architecture governance model is essential for managing architectural initiatives and ensuring alignment. Key components include:

- **Architecture Board:** A vital governing body responsible for strategic architectural decisions, ensuring alignment with business objectives, resolving conflicts, enforcing standards, and managing risks. Composed of senior stakeholders (Enterprise Architects, Business Leaders, IT Leaders, Risk Management Representatives).
- **Architecture Compliance Review:** A process to assess whether projects adhere to established architectural standards, guidelines, and best practices. Ensures integrity, consistency, and alignment, with formal reporting and remediation.
- **Architecture Repository Management:** A central, structured repository for architecture assets, models, and standards. Ensures assets are up-to-date, accessible, and managed efficiently through content organization, version control, metadata management, and access control.
- **Architecture Maturity Assessments:** Systematic evaluations of an organization's architectural practices and capabilities to identify strengths, weaknesses, and areas for improvement. Uses maturity models and assessment criteria to benchmark progress and prioritize improvements.

The **Implementation Approach** for this TOGAF-based governance model involves:

1. Stakeholder Identification and Engagement
2. Governance Framework Development
3. Standardization
4. Repository Setup and Management
5. Compliance Review Process
6. Training and Communication
7. Pilot Phase
8. Continuous Improvement
9. Integration with TOGAF ADM
10. Change Management
11. Measurement and Reporting

## 9. Annexes: Project Lists for DOH Bureaus and CHDs

Annex A and B provide extensive lists of ongoing and planned projects for individual DOH bureaus and Centers for Health Development (CHDs), respectively. These lists detail specific initiatives aimed at achieving the strategic digital programs and broader HSEA goals.

**Examples of Projects across Bureaus:**

- **EB-A:** FETP Ownership Transition, SEIR: Transition VAS VORS for COVID vaccines, TKC WGS Module Completion.
- **HFDB:** Data Standardization Project, HFP Dashboard, Integrate Hospital Development Plan in DOH Data Collect Hub.
- **HPB:** Build Institute of Health Promotion, Maximize KIRA Chatbot, Develop Healthcare Facility Certification AO.
- **HPDPB:** HPDPB Centralized System, AHEAD and DOH-funded Research Database.
- **HFSRB:** Decentralize Health Facilities' Licensing, Implement OLRS, Develop HFSRB Internal IS.
- **KMITS:** Data Warehouse Scale Up, Develop AI for DOH Issuances, Enhance NHFR.
- **OHL:** Develop ILIMS (Integrated Laboratory Management Information System), Build Subnational Laboratories.
- **PS:** Deploy POMIS Outside DOH, Enhance POMIS, Integrate POMIS with other systems.
- **BIHC:** Automation of International Travel Tracking, Deployment of i-DASH.
- **DPCB:** CAF Information System Deployment, DPCB Knowledge Management Platform Deployment.
- **EB-B:** Deployment of RISE PH, Deployment of GEEKS.
- **HFEP-MO:** ARTA Concerns and Complaints System, Deployment of ODK Collect.
- **MPO:** Deployment of MAIS to LGUs, Deployment of Unified Intake Sheet Portal.
- **PD:** EDPMS and iHOMIS Interoperability, EPNF Mobile App Development.
- **BOQ:** Procurement of Border Surveillance System, QSIHSS Development.
- **HHRDB:** Development of NHWR, Enhancement of NDHRHIS, Deployment of PTIS to regions and hospitals.
- **IAS:** Development of IAS Information System.
- **AS:** Enhancement of DOH Employee Health Information System.
- **FMS:** Assessment of Financial Management System for DOH.
- **LS:** Digitalization of Data Processes.
- **OSEC:** Digitalize all DOH processes, Implement an Asset Management System.
- **SCMS:** Implement eLMIS, Integration of eLMIS with other systems.

The DOH Information Systems (Annex C) and Application Rationalization Report (Annex D) are also referenced as complementary documents.