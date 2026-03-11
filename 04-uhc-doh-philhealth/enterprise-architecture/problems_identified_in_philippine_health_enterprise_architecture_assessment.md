# Problems Identified in Philippine Health Enterprise Architecture Assessment

## 1. Enterprise Architecture and Governance Issues

### 1.1 Fragmented Architecture Approach

- **Organization-centric vs Person-centric**: Existing segment architectures for DOH and PhilHealth are organization-centric rather than person-centric, which doesn't align with the Philippine Health Agenda's requirements
- **Missing Health Sector Blueprint**: No overarching, person-centric, health sector-wide blueprint exists for the Philippines
- **Lack of EA Repository**: No functioning Enterprise Architecture repository exists, making it challenging to access, categorize, and manage EA artifacts

### 1.2 Governance and Representation

- **Weak Stakeholder Representation**: Private sector and LGU-level care provider representation within eHealth governance structures is inadequate
- **Missing Vendor Voices**: Notable absence of representatives from private sector eHealth solutions and services providers (vendors) in eHealth governance meetings
- **Insufficient Multi-stakeholder Participation**: Limited collaboration between public, private, and civil society sectors in health governance

## 2. Identity Management and Data Sharing

### 2.1 Patient Identity Challenges

- **Wrong ID Usage**: PhilHealth account numbers (family-based) are used instead of individual member ID numbers for care delivery purposes
- **Identity System Gaps**: The person-centric PhilHealth ID# needed for unique client identification is not properly implemented in eHealth systems
- **Missing Individual Cards**: Dependents don't have their own individual PhilHealth cards with individual ID numbers

### 2.2 Information Sharing Barriers

- **No Information Exchange Infrastructure**: No established infrastructure for health information sharing between disparate systems
- **Under-engineered Interoperability**: Interoperability specifications for health information exchange are insufficient
- **Opt-in Consent Model Limitation**: Current "opt-in" consent policy framework impedes necessary health information sharing for patient safety
- **Private Sector Disincentives**: Perverse incentives against sharing health information in private sector due to financial considerations

## 3. Clinical Workflow and Care Delivery Issues

### 3.1 Care Coordination Problems

- **Lack of Guideline Adherence**: No systematic approach to evidence-based, guideline-adherent care delivery
- **Missing Integrated Care Pathways**: Absence of national integrated care pathways (ICPs) defining care practice guidelines and referral patterns
- **Poor Referral System**: Ad hoc provider referrals with undefined and unenforced referral patterns
- **No Gatekeeping**: Patients can "jump" between levels of care without proper gatekeeping mechanisms

### 3.2 Health Outcome Disparities

- **Income Quintile Disparities**: Wide disparity in health outcomes between first and fifth income quintiles
- **Inequitable Access**: Bottom income quintiles don't exhibit consistent care-seeking behaviors and lack longitudinal care coordination
- **Geographic Inequities**: Health outcome disparities between urban and rural areas, particularly in GIDA (Geographically Isolated and Disadvantaged Areas)

## 4. Health Information Systems Problems

### 4.1 EMR/EHR Implementation Issues

- **Workflow Disconnection**: EMR systems are weakly integrated into front-line health workers' care delivery workflows
- **Limited Value Proposition**: EMR systems provide low value to healthcare workflows and are perceived only as data collection tools
- **Dual Data Entry Burden**: Health workers use paper forms during patient encounters and then perform additional data entry into EMR systems
- **Performance Issues**: Slow processing speeds and latency problems with EMR systems (particularly iClinicSys)

### 4.2 Multiple System Challenges

- **System Fragmentation**: Multiple independent EMR systems creating data silos (SHINE, iClinicSys, WAH, etc.)
- **Inconsistent Reporting**: Different systems generating conflicting or incompatible reports
- **Version Management Problems**: Frequent system updates (iClinicSys versions 1.2 to 1.9 in 7 months) causing confusion and implementation challenges

### 4.3 Data Quality and Reliability Issues

- **Unreliable Data**: EMR-generated reports not reliable for program managers' planning and investment decisions
- **Data Validation Problems**: No data quality assurance mechanisms at regional or provincial levels
- **Incomplete Records**: Significant data loss in care delivery workflows, particularly at barangay level
- **Error-prone Systems**: System errors affecting patient family counts, medicine inventory, and other critical data

## 5. Technical Infrastructure Challenges

### 5.1 Connectivity and Infrastructure

- **Poor Internet Connectivity**: Patchy, slow, or unavailable internet connections in many areas, particularly GIDA
- **Bandwidth Limitations**: Insufficient bandwidth for system requirements (PHIC UPCM requires 1 Mbps connectivity)
- **Network Infrastructure Gaps**: Inadequate network infrastructure in rural and remote areas
- **Power and Utility Issues**: Limited availability of basic utilities in some healthcare facilities

### 5.2 Hardware and Equipment Issues

- **Insufficient Equipment**: Limited number of computers and IT equipment in healthcare facilities
- **Maintenance Challenges**: No dedicated IT personnel for system maintenance and troubleshooting at facility level
- **Equipment Procurement Delays**: Challenges in procuring ICT equipment due to complex government procurement processes

## 6. Human Resource and Training Problems

### 6.1 Training and Capacity Building

- **Inadequate Training**: Limited or no training provided for EMR usage (many health workers learn by trial and error)
- **Age-related Challenges**: Older healthcare workers (50s-60s) having difficulty adapting to EMR systems
- **Skill Mismatches**: Systems designed for statisticians' requirements rather than end-user (healthcare provider) needs
- **Training Costs**: High costs for comprehensive training programs (Region V spent P10 million for 14 training batches)

### 6.2 Staffing Issues

- **Limited IT Personnel**: No permanent positions for IT personnel at RHU level
- **Manpower Shortages**: Insufficient staff to manage both healthcare delivery and administrative/data entry tasks
- **HRH Geographic Distribution**: Rural areas and GIDA suffer human resource for health shortages
- **Encoder Dependency**: Heavy reliance on dedicated encoders rather than integrated workflow systems

## 7. Financial and Sustainability Issues

### 7.1 Funding Challenges

- **Delayed PhilHealth Payments**: Significant delays in PhilHealth claim payments to providers (cases of P15M unpaid claims mentioned)
- **Capitation Fund Misuse**: Issues with proper utilization of PCB capitation funds by LGUs
- **Procurement Delays**: Medicine supply delays due to bidding process failures and procurement requirement compliance issues
- **Investment Sustainability**: Challenges in sustaining ICT investments across political administration changes

### 7.2 Cost and Resource Allocation

- **High Software Costs**: Expensive commercial health IT solutions (P1.5M for BizBox HIS) that small hospitals cannot afford
- **Maintenance Costs**: Limited budget for IT personnel (P13K/month cap, P4M/year total for Region V)
- **Infrastructure Investment**: Significant costs for wiring, network setup, and equipment procurement

## 8. Regulatory and Compliance Issues

### 8.1 Health Software Regulation

- **Lack of SAMD Framework**: Absence of Software as Medical Device (SAMD) regulatory framework
- **No ISO-13485 Compliance**: DOH doesn't employ ISO-13485 conformant practices for eHealth software development
- **Weak Market Regulation**: Insufficient regulation of eHealth products in the market

### 8.2 Policy and Legal Challenges

- **Data Privacy Compliance**: Challenges in implementing health information sharing while complying with Data Privacy Act
- **Licensing vs Accreditation**: Separation between DOH licensing and PhilHealth accreditation creating compliance complexity
- **Mandatory Reporting Issues**: Challenges in enforcing mandatory electronic health record updates and reporting

## 9. PhilHealth-Specific Issues

### 9.1 Membership and Coverage

- **Coverage Gaps**: 8% of non-formal sector Filipinos not covered by PhilHealth
- **Demographic Update Limitations**: Member demographic record updates limited to PhilHealth membership offices only
- **ID Issuance Problems**: Dependents don't receive individual PhilHealth ID cards
- **Eligibility Verification**: Challenges in verifying member eligibility, particularly for indigent and sponsored members

### 9.2 Claims Processing

- **System Connectivity Issues**: Frequent disconnections affecting claims processing
- **Real-time Update Problems**: PhilHealth portal not showing updated/real-time payment information
- **Documentation Requirements**: Delays in claims processing due to missing documentary requirements
- **Case Rate Adequacy**: Questions about whether current case rates adequately compensate healthcare providers

## 10. Health Information Exchange (HIE) Implementation Challenges

### 10.1 PHIE Development Issues

- **Unclear PHIE Strategy**: Confusion about whether PHIE Lite is truly evolutionary toward PHIE "full"
- **Missing Interoperability Profiles**: Lack of articulated interoperability profiles for PHIE operational interfaces
- **Canonical Model Concerns**: Questions about whether iClinicSys provides suitable canonical information model for HIE
- **Implementation Readiness**: PHIE not listed as top priority in DOH's Information Systems Strategic Plan

### 10.2 Standards and Interoperability

- **Missing Standards Framework**: No normative set of interoperability profiles for health information sharing
- **Integration Challenges**: Difficulties in connecting various health systems and ensuring interoperability
- **Standards Adoption**: Inconsistent adoption of health informatics standards across the sector

## 11. Operational and Workflow Issues

### 11.1 Care Delivery Workflow Problems

- **Paper-based Dependencies**: Continued reliance on paper forms and manual processes despite EMR implementation
- **Follow-up and Referral Gaps**: Patient follow-ups and referrals not properly recorded or tracked
- **Consultation Workflow Issues**: EMRs not used during actual patient consultations but only for post-encounter data entry
- **Medical Records Access**: Patients must pay for reprinting of medical records (2 pesos per page)

### 11.2 Quality and Safety Concerns

- **Limited Clinical Decision Support**: EMR systems don't incorporate clinical practice guidelines or decision support
- **No Care Continuity**: Lack of longitudinal health records affecting care continuity across providers
- **Quality Metrics Weakness**: Provider quality monitoring largely ad hoc and focused on claim processing rather than care quality
- **Patient Safety Risks**: Potential patient safety issues due to inadequate health information sharing

## 12. Regional and Local Implementation Challenges

### 12.1 Geographic and Infrastructure Barriers

- **GIDA Connectivity**: Severe connectivity challenges in geographically isolated and disadvantaged areas
- **Island Connectivity**: Internet services unavailable, disconnected, or having poor signal in island areas
- **Infrastructure Maintenance**: Difficulties in maintaining IT infrastructure across wide geographic areas
- **Support Service Limitations**: Challenges in providing technical support across dispersed rural facilities

### 12.2 LGU-Level Implementation Issues

- **Resource Constraints**: Limited local government resources for implementing and maintaining health IT systems
- **Political Continuity**: Health programs affected by changes in local political administration
- **Capacity Building Needs**: Need for additional ICT personnel and skills development at local level
- **Budget Allocation**: Issues with proper allocation and utilization of health IT budgets at LGU level

## 13. Private Sector Integration Challenges

### 13.1 Public-Private Coordination

- **Trust and Participation**: Private sector providers reluctant to participate in government-led eHealth initiatives
- **Regulatory Alignment**: Challenges in aligning private sector practices with government health information requirements
- **Financial Incentives**: Lack of appropriate incentives for private sector participation in health information sharing

### 13.2 Market and Competition Issues

- **Vendor Certification**: Limited number of certified Health IT Providers (only 6 HITPs)
- **Cost Barriers**: High costs of commercial health IT solutions creating barriers for smaller private providers
- **Interoperability Requirements**: Need for private sector systems to demonstrate compliance and interoperability with government systems

## 14. Cultural and Behavioral Barriers

### 14.1 Technology Adoption Challenges

- **Digital Literacy**: Limited digital literacy among healthcare workers, particularly older staff
- **Resistance to Change**: Cultural resistance to transitioning from paper-based to electronic systems
- **Trust Issues**: Concerns about data confidentiality and sharing, particularly for stigmatized conditions (HIV, mental health)

### 14.2 System Design and User Experience

- **Poor User Interface Design**: EMR systems not designed with end-user (healthcare provider) requirements in mind
- **Complex Workflows**: Overly complex system workflows that don't match actual healthcare delivery patterns
- **Lack of User Feedback**: Insufficient incorporation of healthcare worker feedback in system design and improvement