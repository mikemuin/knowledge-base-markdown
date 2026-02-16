# DOH Enterprise Architecture Implementation Battle Plan

## IMMEDIATE ACTIONS (Start Tomorrow - Day 1-30)

### Command Structure Establishment

1. **Activate Digital Futures Group** (Page mentions creation but unclear on structure)
   - **ACTION UNCLEAR**: Document mentions establishing Digital Futures Group but provides no specific structure, roles, or immediate tasks
   - **CRITICAL NEED**: Define roles, responsibilities, and authority levels
2. **Implement EA Governance Model** (Pages 282-289)
   - Establish Architecture Board with defined membership from bureaus
   - Create Architecture Compliance Review process
   - Set up weekly Architecture Board meetings starting Week 2
3. **Deploy ADOIT DOH Instance** (Already owned by PPMEU)
   - Train KMITS-PPMEU team on TOGAF and ArchiMate (identified concern on page for PPMEU)
   - Begin populating enterprise architecture repository
   - Create user access controls and training materials

## PHASE 1: FOUNDATION (Days 1-90)

### Critical Infrastructure Projects

1. **National Health Data Repository (NHDR)** - Strategic Program
   - **ACTION UNCLEAR**: Document identifies NHDR as strategic program but provides no implementation details, timeline, or specific requirements
2. **Philippine Health Information Exchange (PHIE)** - Under development by KMITS-ESD
   - Accelerate development as component of NHDR
   - Define integration requirements with existing systems
3. **Integrated Health Information System (iHIS)** - KMITS-ESD managing
   - Complete Stage 1 (clinical modules) - currently being completed
   - Begin Stage 2 (non-clinical modules)
   - Prepare Stage 3 (PHIE integration)

### Data Standardization Offensive

1. **National Health Data Standards (NHDS)** - KMITS-PPMEU maintaining
   - Enforce compliance across all bureau systems
   - Implement Standards Conformance and Interoperability Validation (SCIV)
   - Deploy Centralized Online SCIV System (currently not operational)
2. **Electronic Medical Records Standardization**
   - Complete NEHEHRVS to Centralized Online SCIV System transition
   - Mandate FHIR compliance for all health facilities

## PHASE 2: BUREAU INTEGRATION (Days 91-180)

### High-Priority Bureau Systems (Based on Annexes A & B)

#### KMITS Operations

1. **Data Center Infrastructure**
   - Upgrade DOH Corporate Network Infrastructure
   - Implement robust network security controls
   - Scale up Data Warehouse
2. **Enterprise Integration Platform**
   - Develop Data Exchange (DEX) system
   - Implement Enterprise Data Catalog (EDC)
   - Deploy Metadata Registry (MDR)

#### Epidemiology Bureau Systems

1. Complete Disease Surveillance Integration
   - Finalize TKC (Tanod Kontra COVID) full user transition
   - Complete SEIR (Synchronized Electronic Immunization Repository) VAS VORS transition
   - Deploy RISE PH system to GIDAs (Geographically Isolated and Disadvantaged Areas)

#### Health Facilities and Regulation

1. **HFSRB Critical Systems**
   - Implement Online Licensing Regulatory System (OLRS) - currently limited functionality
   - Deploy HFSRB Internal Document Tracking System
   - Integrate OHSRS with RISE PH
2. **Hospital Operations Standardization**
   - Complete IHOMIS deployment to all DOH hospitals
   - Standardize EMR systems across hospital network

### Financial and Administrative Systems

1. **Procurement Integration**
   - Deploy POMIS v2 (enhanced version)
   - Integrate POMIS with DPRI, eLMIS, and EPNF
   - Implement OBDPS (Online Bidding Documents Payment System)
2. **Supply Chain Management**
   - Implement eLMIS (Electronic Logistics Management Information System)
   - Integrate with eNGAS (Electronic National Government Accounting System)

## PHASE 3: ADVANCED CAPABILITIES (Days 181-365)

### Population Health Systems

1. **Complete FOSM Integration**
   - **ACTION UNCLEAR**: Document states FOSM needs direct access to CHD data for better analysis and monitoring, but provides no specific technical requirements or integration approach
2. **Health Emergency Management**
   - Enhance IIS (Integrated Information System)
   - Develop Tele-triage system
   - Complete DRRM-H Plan Information System

### Specialized Programs

1. **Pharmaceutical Division Integration**
   - Complete EDPMS version 4 deployment
   - Implement EPNF mobile app
   - Establish Drug Price Reference Index real-time updates
2. **Human Resources Systems**
   - Deploy PTIS to regions and hospitals
   - Complete National Health Workforce Registry (NHWR) phases 1-3
   - Integrate with CHED and DMW systems

## PHASE 4: REGIONAL DEPLOYMENT (Days 366-540)

### CHD Integration (Based on CHD-CAR and CHD-IV-A examples)

1. **Regional System Standardization**
   - Deploy standardized CHD information systems
   - Implement regional telemedicine capabilities
   - Establish regional data centers
2. **Hospital Network Integration**
   - Complete EMR standardization across all DOH hospitals
   - Implement patient referral systems
   - Deploy specialized registry systems (trauma, cancer, etc.)

## CRITICAL SUCCESS FACTORS

### Resource Requirements

1. **Technical Personnel** - Document identifies shortage of IT personnel as major concern
2. **Network Infrastructure** - Weak internet connectivity noted as barrier
3. **Training and Capacity Building** - Required for TOGAF/ArchiMate adoption

### Risk Mitigation

1. **Data Quality Issues** - Multiple bureaus report poor data quality affecting operations
2. **System Integration Complexity** - Many systems lack interoperability
3. **Change Management** - Resistance to new systems and processes

## MEASUREMENT AND MONITORING

### Key Performance Indicators (From Health Sector Strategy Document)

- Track progress against 33 KPIs identified in document
- Monthly Architecture Board reviews
- Quarterly EA maturity assessments
- Semi-annual stakeholder satisfaction surveys

### Governance Checkpoints

- Weekly Architecture Board meetings
- Monthly compliance reviews
- Quarterly migration plan updates
- Annual EA strategy refresh

## CRITICAL INTELLIGENCE GAPS - QUESTIONS REQUIRING IMMEDIATE ANSWERS

### 1. DIGITAL FUTURES GROUP (Page 15-17)

**STATUS**: Mentioned as preparation for DOH offices but no structure defined **CRITICAL QUESTIONS**:

- Who leads the Digital Futures Group?
- What are the specific roles and responsibilities?
- How many personnel are assigned?
- What is their decision-making authority?
- How do they interface with existing bureaus?
- What is their budget and resource allocation?
- Are they part of KMITS or separate entity?

### 2. NATIONAL HEALTH DATA REPOSITORY (NHDR) - Strategic Program

**STATUS**: Identified as strategic program led by PhilHealth but no implementation details **CRITICAL QUESTIONS**:

- What is the technical architecture of NHDR?
- Which systems will feed data into NHDR?
- What are the data governance policies?
- Who owns and operates NHDR - PhilHealth or DOH?
- What is the implementation timeline?
- How does NHDR relate to existing Data Warehouse?
- What are the security and privacy requirements?
- How will CHDs and hospitals connect to NHDR?

### 3. FOSM DATA ACCESS REQUIREMENTS (Page for FOSM)

**STATUS**: Document states need for "direct access to CHD data" but no technical approach **CRITICAL QUESTIONS**:

- Which specific CHD systems need integration?
- What data elements are required?
- Real-time or batch data transfer?
- What are the API requirements?
- Who builds the integration - FOSM, CHDs, or KMITS?
- What are the data privacy and security protocols?
- How will data quality be ensured?

### 4. ARCHITECTURE BOARD COMPOSITION (Pages 282-289)

**STATUS**: Governance model defined but membership unclear **CRITICAL QUESTIONS**:

- Who are the specific individuals on the Architecture Board?
- How are decisions made - consensus or voting?
- What is the meeting frequency and duration?
- Who has veto power over architectural decisions?
- How are conflicts between bureaus resolved?
- What is the escalation process to Secretary level?
- Who provides secretariat support?

### 5. BUDGET AND RESOURCE ALLOCATION

**STATUS**: No financial information provided in document **CRITICAL QUESTIONS**:

- What is the total budget for EA implementation?
- How are costs allocated across bureaus?
- Who approves budget for cross-bureau projects?
- What is the funding source (regular budget, special allocation)?
- Are there external funding sources (World Bank, ADB)?
- What is the procurement process for major systems?
- Who handles vendor management and contracts?

### 6. SYSTEMS INTEGRATION PRIORITIES (Multiple pages)

**STATUS**: 298+ systems identified but integration sequence unclear **CRITICAL QUESTIONS**:

- Which systems are critical path for EA success?
- How are integration conflicts resolved?
- Who decides on system retirement vs enhancement?
- What is the data migration strategy?
- How will business continuity be maintained during transitions?
- What is the testing and validation process?
- Who handles user training and change management?

### 7. CHD AND HOSPITAL DEPLOYMENT (CHD sections)

**STATUS**: Examples from CHD-CAR and CHD-IV-A but no standardized approach **CRITICAL QUESTIONS**:

- Are all CHDs required to use identical systems?
- How will regional variations be handled?
- What is the rollout sequence across 17 CHDs?
- Who provides technical support during deployment?
- How will hospital EMR systems be standardized?
- What happens to existing hospital investments in IT?
- How will private hospitals be integrated?

### 8. INTEROPERABILITY STANDARDS ENFORCEMENT (KMITS sections)

**STATUS**: SCIV system mentioned but enforcement mechanism unclear **CRITICAL QUESTIONS**:

- Who enforces compliance with NHDS standards?
- What are the penalties for non-compliance?
- How will legacy systems be brought into compliance?
- What is the certification process for vendors?
- Who validates FHIR implementations?
- How will API security be managed?
- What is the dispute resolution process?

### 9. DATA QUALITY AND GOVERNANCE (Multiple bureau concerns)

**STATUS**: Poor data quality identified as major issue across bureaus **CRITICAL QUESTIONS**:

- Who is the enterprise data steward?
- What are the data quality standards and metrics?
- How will data cleansing be performed?
- Who resolves data conflicts between systems?
- What is the master data management strategy?
- How will data lineage be tracked?
- What are the data retention and archival policies?

### 10. TIMELINE AND DEPENDENCIES (Project lists in Annexes)

**STATUS**: Projects listed but no sequencing or dependencies defined **CRITICAL QUESTIONS**:

- Which projects must be completed before others can start?
- How will delays in critical path projects be handled?
- What is the contingency plan for vendor delays?
- How will resource conflicts between projects be resolved?
- What is the minimum viable product for each phase?
- How will success be measured at each milestone?
- What triggers moving to the next phase?

### 11. TRAINING AND CAPACITY BUILDING (TOGAF/ArchiMate concern noted)

**STATUS**: Need identified but no training strategy provided **CRITICAL QUESTIONS**:

- Who will provide TOGAF/ArchiMate training?
- How many personnel need certification?
- What is the training budget and timeline?
- Who handles ongoing EA methodology coaching?
- How will knowledge transfer be managed?
- What happens if key EA personnel leave?
- How will bureaus build internal EA capability?

### 12. VENDOR MANAGEMENT AND PROCUREMENT (Procurement Service section)

**STATUS**: POMIS system for procurement but no EA-specific vendor strategy **CRITICAL QUESTIONS**:

- Who manages enterprise-wide vendor relationships?
- How will vendor lock-in be avoided?
- What are the technical standards for vendor solutions?
- Who approves major system procurements?
- How will vendor performance be monitored?
- What is the strategy for build vs buy decisions?
- How will intellectual property rights be managed?

------

**COMMANDER'S ASSESSMENT**: These 12 critical gaps represent **MISSION-CRITICAL INTELLIGENCE FAILURES**. Without answers to these questions, the EA implementation will face significant delays, cost overruns, and potential failure. Each gap requires immediate attention from designated subject matter experts and decision-makers.

---

### Notes

**INTELLIGENCE ANALYSIS COMPLETE**

I've identified **12 CRITICAL INTELLIGENCE GAPS** that will cripple this EA implementation if not resolved immediately. These aren't minor details - they're **MISSION CRITICAL** information requirements.

**KEY OBSERVATIONS:**

1. **GOVERNANCE VACUUM**: The Architecture Board concept exists but no one knows who's actually sitting at the table making decisions.
2. **TECHNICAL BLACK HOLES**: Major systems like NHDR are mentioned as "strategic programs" but have zero implementation details - that's like ordering a battalion to "take the hill" without telling them which hill.
3. **RESOURCE BLIND SPOTS**: No budget information means no one knows if this is a ₱100M or ₱10B operation - impossible to plan properly.
4. **INTEGRATION CHAOS**: 298+ systems need to talk to each other but there's no clear technical standard enforcement mechanism.

**IMMEDIATE ACTION REQUIRED:**

Assign a dedicated intelligence team to each of these 12 gaps. Set a **72-hour deadline** for initial answers and **2-week deadline** for complete intelligence packages.

**PRIORITY RANKING:**

1. **URGENT** (Blocks everything): Questions 1, 4, 7, 10
2. **CRITICAL** (Blocks major components): Questions 2, 3, 6, 8
3. **IMPORTANT** (Risk mitigation): Questions 5, 9, 11, 12

Without this intelligence, you're essentially ordering troops to advance through unmapped territory with unknown enemy positions and uncertain supply lines. The strategic vision is clear - the tactical intelligence is dangerously incomplete.

**RECOMMENDATION**: Halt all major system implementations until these gaps are filled. Better to delay 30 days for proper planning than fail after 300 days of chaotic execution.