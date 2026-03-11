# NHDR should be a Network of Networks

The assertion that the National Health Data Repository (NHDR) should be conceptually structured as a **"network of networks"** rather than a single top-down Health Information Exchange (HIE), with local units empowered to become technological networks, is strongly supported and reflected in the existing design philosophy and implementation guidelines for Universal Health Care (UHC).

The sources indicate that the NHDR, while established as a centralized point for authoritative data submission, is designed to integrate and rely upon localized, interoperable systems established by empowered local health units. The resulting structure is functionally a network of interconnected local and national systems.

Here is how the sources support the concept of a "network of networks" and the empowerment of local technological units:

### 1. NHDR as the Central Node of an Interoperable Ecosystem

While the NHDR is defined nationally as the "single point-of-submission system and authoritative repository" for all health and health-related data, its function is not merely a monolithic top-down data dump. It is the central node necessary to harmonize the output of numerous decentralized systems.

- **National Interoperable Digital Health Ecosystem:** The end-state environment the NHDR intends to achieve is a **"national interoperable digital health ecosystem"** where health information systems are interoperable among each other and can share or exchange health and health-related data. This interoperability is mandated for all health-related entities.
- **Integration with the Philippine Health Information Exchange (PHIE):** The structure includes a **Philippine Health Information Exchange (PHIE)**, which the integrated Health Information System (iHIS) must integrate with. The PHIE is described as the mechanism that operationalizes the longitudinal electronic health record (EHR) for Filipinos and is the "heart of a person-centric EA" (Enterprise Architecture). The PHIE is envisioned to connect DOH systems and be a component of the NHDR.
- **Interoperability Layer (HIE):** The NHDR framework includes an **Interoperability Layer**, which serves as the single point of entry to enable the submission of data from various sources (EMRs, Hospital Information Systems, etc.) to the NHDR. This layer validates that local systems comply with the mandatory **National Health Data Standards for Interoperability**.

### 2. Empowering Local Units as Technological Networks

The UHC Act and its implementing rules recognize the decentralized nature of service delivery (through Local Government Units or LGUs) and mandate that these local units build sophisticated, interconnected technology systems.

- **P/CWHS as Integrated Networks:** The core of UHC delivery is the integration of fragmented local health systems into **Province-wide and City-wide Health Systems (P/CWHS)**. These P/CWHS must ensure **"managerial and technical integration"**, where **technical integration** refers to the functional and efficient linking of health service provision from primary to tertiary care.
- **Health Care Provider Networks (HCPNs):** The P/CWHS operationalizes care through **Health Care Provider Networks (HCPNs)**, which are explicitly required to standardize their technology infrastructure. HCPNs must establish a **"patient records management system with an interoperable electronic medical record in all member health facilities capable of real-time information-sharing"**.
- **PCPNs as Foundational Networks:** Primary Care Provider Networks (PCPNs), the "basic foundation of HCPNs", must have **patient records accessible throughout the health system**. The Integrated Health Information System (iHIS) is designed to operationalize an ICT-enabled, integrated, and functional referral system that **links the members of the PCPN to secondary and tertiary care providers within and across P/CWHS**. This structure necessitates local networks that feed into the larger system.
- **Local Mandate for Digital Infrastructure:** Empowering the local unit involves mandating investment in technology. The P/CWHS’s Health Systems Support Division manages the **health information system**. The Local Investment Plan for Health (LIPH) must outline the **"strategies, interventions and investment needs"** based on the baseline assessment and the status of local integration.
- **LGU Choice of Systems:** The emphasis on local empowerment is seen in the policy allowing LGUs and healthcare facilities the **right to select their preferred Health Information System (HIS)/Electronic Medical Record (EMR)/Electronic Health Record (EHR) provider or develop their own** based on their specific needs and requirements. However, these chosen systems must comply with national certification, confirming conformance to **national health data standards for interoperability**.

### 3. Decentralized Submission and Data Collection

The NHDR's design accommodates local autonomy by anticipating data submission from two types of models, acknowledging that many critical data sources reside at the local government level.

- **Centralized Model:** Health care providers, insurers, and health-related entities submit data directly to the NHDR.
- **Decentralized Model (Submission through LGUs):** The system recognizes that **national and local government units collect data to serve their mandates or functions**. Data that must undergo local processing (such as licensing data from DOH's Integrated Licensing Information System) will be reviewed and decided upon before submission to the NHDR. This explicitly incorporates local governmental data systems into the national repository structure.
- **Focus on Primary Data Sources:** Health and health-related data must come from the **primary data source or as close as possible to the point of creation**. Since primary care is the foundation of the HCPN, this necessitates that the data originate from the local facility/network.

In conclusion, the architecture described in the sources is not purely top-down, but rather a **federated system** where the centralized NHDR acts as the authoritative hub for integration and monitoring. The system relies on decentralized, interconnected "networks" (HCPNs/PCPNs and LGU-managed iHIS) which are technically and managerially required to achieve interoperability standards for data submission to the national repository. The requirement for interoperability standards and validation (SCIV) is the mechanism that binds these "networks of networks" together toward the goal of UHC.