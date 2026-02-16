# Health Information Exchange (HIE) Implementation: International Comparative Analysis

Based on current data, here's a comprehensive overview of how HIEs are implemented in the United States, United Kingdom, Canada, and Australia:

## UNITED STATES

### National Framework: TEFCA (Trusted Exchange Framework and Common Agreement)

The United States operates TEFCA as a nationwide framework for health information sharing, created by the Department of Health & Human Services to remove barriers for sharing health records electronically among healthcare providers, patients, public health agencies, and payers.

**Architectural Model:**

- TEFCA functions as a "network of networks" connecting existing health information networks across states, regions, and localities through Qualified Health Information Networks (QHINs)
- Large health information networks serve as QHINs, acting as the backbone for network connectivity, with each QHIN representing dozens or hundreds of health systems, federal care providers, public health agencies, payers, and health IT vendors
- As of February 2024, seven organizations have been officially designated as QHINs: eHealth Exchange, Epic Nexus, Health Gorilla, KONZA, MedAllies, CommonWell Health Alliance, and Kno2

**Technical Standards:**

- The technical architecture centers around existing Integrating the Healthcare Enterprise (IHE) adopted standards and profiles widely deployed by national networks
- Platforms support a broad range of standards, including FHIR, HL7, C-CDA, and IHE Profiles
- A FHIR roadmap has been released outlining plans for incorporating HL7's FHIR in interoperability initiatives

**Governance:**

- The Common Agreement defines baseline legal and technical requirements for secure information sharing, establishing infrastructure models and governing approaches to enable users in different health information networks to securely share information under commonly agreed-to expectations
- TEFCA is managed by the Recognized Coordinating Entity (RCE), the Sequoia Project, which develops, updates, and oversees implementation and governance
- A final rule effective January 15, 2025 amends information blocking regulations and implements provisions to support reliability, privacy, security, and trust within TEFCA

**Current Status:**

- As of 2023, 70% of US hospitals engaged in interoperable exchange at least sometimes across four domains: send, receive, find, and integrate
- In 2023, 8% of hospitals did not send, 18% did not find, 14% did not receive, and 22% did not integrate electronic health information
- Many Health Information Exchange Organizations are already connected to public health agencies and assuming an emerging role to facilitate public health reporting

**Key Challenges:**

- Sharing of clinical data across disparate healthcare organizations continues to be hampered by lack of standardization in EHR features and functionality, and lack of interoperability among them
- Identified challenges include the need for data standards, lack of implementation and adoption of standards for EHR and other health IT technologies, and difficulties with accurately matching patients' health records

------

## UNITED KINGDOM (NHS England)

### Decentralized Shared Care Records Model

**National Architecture:**

- NHS England has invested approximately £20 million to connect care records across England by March 2026
- NHS England has made it clear that each Integrated Care System (ICS) needs to develop or join a shared care record joining data safely across all health and social care settings
- The National Care Records Service (NCRS) provides quick, secure access to national patient information, providing access to over 63 million patient records

**Technical Infrastructure:**

- Connecting Care Records programme is working with healthcare teams and IT suppliers to improve interoperability between shared care records, ensuring clinical teams have access to data they need when they need it using interoperability tools such as National Record Locator
- CareConnect Open API enables clinicians in one care setting to view records from across other care settings, such as a clinician in A&E accessing a patient's medical record from an out of area service
- SNOMED CT is used as a clinical vocabulary giving systems a single shared language which makes exchanging information between systems easier, safer and more accurate

**Governance and Policy:**

- The Data (Use and Access) Act 2025, which received Royal Assent on June 19, 2025, imposes mandatory information standards for IT suppliers engaging with the NHS
- The NHS 10-Year Plan proposes implementing a Single Patient Record (SPR) to create a single, unified record of health data for each patient, accessible by healthcare professionals with patient consent
- NHS England has set out how health and care information should be accessed and managed in the Information Governance Framework for Integrated Health and Care: Part 1 - Shared Care Records

**Implementation Status:**

- Each ICS now has a shared care record in place between NHS trusts and GP practices, with more being done to incorporate social care
- By 2026, all ambulance services are expected to be able to access a patient's shared care record information in whatever location the patient needs care
- General practices in England do not use a single clinical system and are not expected to do so anytime in the future, with information sharing enabled through centralized API mechanisms

**Current Challenges:**

- Healthcare professionals report lacking time to implement standards properly, as these efforts compete with immediate patient care demands, creating a self-perpetuating cycle where poor interoperability generates more administrative burden
- Previous uncoordinated efforts have failed, with technical challenges paling in comparison to power struggles behind closed doors

------

## CANADA

### Emerging National Legislative Framework

**Legislative Foundation:**

- In June 2024, the Government of Canada introduced Bill C-72, the Connected Care for Canadians Act, requiring that health information technology be interoperable and prohibiting data blocking by vendors
- Less than 40% of Canadians currently report accessing some of their health information electronically and only 35% of physicians share patient information outside their practice

**Standards Development:**

- The Canadian Institute for Health Information was scheduled to complete the Pan-Canadian Health Data Content Framework in fall 2024, which will define the minimum data fields necessary for interoperable care
- Canada Health Infoway is developing the CA Core+ standard, which translates the Pan-Canadian Health Data Content Framework requirements to the FHIR format
- The data standards in the Pan-Canadian Interoperability Roadmap are due to be completed in 2027

**Organizational Structure:**

- CIHI is collaborating with Canada Health Infoway to develop common standards to enable the seamless flow of health information among facilities, sectors of care, patients and health care providers
- To help change the current situation, Canada is implementing the Shared Pan-Canadian Interoperability Roadmap to create a digitally connected and secure health care information system

**Current Status:**

- The most recent 2024 national survey of physicians found that 29% of physicians in Canada share patient clinical summaries electronically with care providers outside their practice
- Only 25% of general practitioners reported sharing electronic information beyond their practice, compared with 33% of specialists
- Information exchange was highest in hospital settings compared with community settings, such as doctor's offices and long-term care homes

**Governance Context:**

- Canada's health system is decentralized, and provincial/territorial governments are primarily responsible for the governance, regulation, co-financing, administration and delivery of health services for their populations
- The legislation would only apply in provinces and territories that do not have similar legislation in place

**Critical Assessment:**

- The federal government committed to improving health data exchange with the creation of Canada Health Infoway in 2001, investing an initial $500 million to create common pan-Canadian standards, but in 2024, those standards still do not exist
- It will likely take several more years for vendors to implement these standards, with many countries having already gone through this process

------

## AUSTRALIA

### Centralized National Digital Health Platform

**National System:**

- My Health Record is Australia's secure consumer-controlled national electronic health record system that contains an online summary of an individual's key health information
- The system is underpinned by the My Health Records Act 2012 and enables consumers to securely access key health information online, anytime and anywhere, including in an emergency

**Recent Legislative Developments:**

- Under the Health Legislation Amendment (Modernising My Health Record – Sharing by Default) Act 2025, healthcare providers, starting with pathology and diagnostic imaging services, will be required by law to upload specific test results and reports to My Health Record
- Amendments to the Healthcare Identifiers Act 2010 were introduced under the Aged Care and Other Legislation Amendments Bill 2025, which passed through Parliament on September 4, 2025, with amendments taking effect from November 1, 2025

**Technical Infrastructure:**

- The Healthcare Identifiers Service (HI Service) is a national system that assigns and uniquely identifies individuals, health providers and organisations using unique 16-digit healthcare identifiers
- Healthcare identifiers play a crucial role in enabling the secure and accurate exchange of health information between national digital health systems, such as My Health Record and electronic prescribing
- The National Interoperability Plan outlines the current state of interoperability in Australia's healthcare system and identifies priority actions to foster a more connected healthcare system

**State-Level Initiatives:**

- Victoria implemented CareSync Exchange, a secure electronic patient health information sharing system between specified public health services, with legislation coming into effect on February 7, 2024
- In June 2025, Austin Health became the first health service to use CareSync Exchange to view patient information, with information from Austin Health, Eastern Health, Northern Health, and Monash Health now available in the system

**Digital Health Ecosystem:**

- Recent developments include electronic prescribing allowing optional use of electronic rather than paper prescriptions, secure messaging of clinical information enabling secure encrypted exchange between health professionals, and Medicare online portal
- The Australian Digital Health Agency is responsible for driving the development and delivery of digital health solutions that improve information sharing, connectivity and health infrastructure

**Regulatory Approach:**

- In September 2024, a proposal paper was released suggesting the introduction of mandatory guardrails for AI in high-risk settings
- AI tools used in healthcare can be regulated by the TGA (Therapeutic Goods Administration) if classified as a medical device, including AI-enabled software

------

## COMPARATIVE SUMMARY

### Architectural Approaches:

1. **United States**: Federated "network of networks" model with multiple QHINs competing and collaborating
2. **United Kingdom**: Decentralized ICS-based shared care records with national coordination
3. **Canada**: Emerging national legislative framework with provincial implementation
4. **Australia**: Centralized national consumer-controlled platform with state-level supplements

### Standards Adoption:

- **US & Canada**: Strong emphasis on FHIR as the modern interoperability standard
- **UK**: SNOMED CT for terminology, CareConnect APIs, moving toward FHIR
- **Australia**: Healthcare Identifiers as foundational infrastructure, FHIR-based national standards development

### Governance Models:

- **US**: Private-sector driven with federal oversight through TEFCA
- **UK**: NHS-centralized with devolved implementation through ICSs
- **Canada**: Federal-provincial shared jurisdiction with voluntary adoption
- **Australia**: Federally mandated consumer-controlled national system

### Key Implementation Gaps:

- **US**: Patient matching and data standardization across diverse EHR systems
- **UK**: Time constraints for healthcare professionals and competing vendor interests
- **Canada**: Delayed standards development despite 20+ years of investment
- **Australia**: Slow adoption of healthcare identifiers despite available infrastructure

All four countries recognize HIE as critical infrastructure for modern healthcare delivery, but each faces unique socio-technical challenges in achieving comprehensive, nationwide interoperability. The US model emphasizes market-driven solutions, the UK focuses on NHS integration, Canada struggles with federal-provincial coordination, and Australia leads with a centralized consumer-controlled approach mandated by legislation.