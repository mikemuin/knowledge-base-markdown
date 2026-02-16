---
marp: true
theme: default
paginate: true
size: 16:9
---

------

# Clinical Workflow Analysis

## Case Scenario and SIPOC Analysis

**From Accident to ICU: Miguel Santos' Healthcare Journey**
*Understanding Real-World Processes Through Systematic Analysis*

------

## Case Overview

**Patient:** Miguel Santos, 35-year-old motorcycle taxi driver
**Incident:** Motorcycle vs. Jeepney collision
**Location:** Provincial highway, 15km from Batangas Provincial Hospital
**Hospital:** 200-bed regional facility
**Date:** Friday, May 13, 2025, 2:45 PM

**Journey:** Complete healthcare workflow from accident scene to ICU admission
**Analysis:** SIPOC framework applied to each process step
**Purpose:** Understand current state and identify improvement opportunities

------

## 1. Accident Response and Transport - Scenario

### What Happened (2:45 PM)

- Miguel's motorcycle collides with jeepney avoiding pothole
- Patient thrown from bike, conscious but in severe pain
- Visible injuries: deformed left arm, chest/abdominal pain
- Good Samaritan calls emergency hotline

### Transport Decision (3:00 PM)

- Ambulance 30 minutes away
- Community decides on jeepney transport
- Wife Maria arrives with PhilHealth documents
- 20-minute journey to hospital begins

**Total Time: 35 minutes from accident to hospital arrival**

------

## 1. Accident Response and Transport - SIPOC

• **Suppliers:** Accident witnesses, Good Samaritans, community members, jeepney driver, patient's family (Maria), emergency hotline operators

• **Inputs:** Patient with traumatic injuries, accident scene assessment, patient identification, PhilHealth documents, transportation vehicle, basic first aid knowledge from bystanders

• **Process:** Initial accident assessment and stabilization, transport decision making, community mobilization, patient loading and positioning, continuous monitoring during transport, family communication

• **Outputs:** Patient transported to hospital, basic vital status information, preliminary injury assessment, family notification and mobilization, accident documentation, estimated hospital arrival time

• **Customers:** Patient requiring emergency care, hospital emergency department, family members needing support, insurance providers requiring documentation

------

## 2. Hospital Registration - Scenario

### Registration Process (3:20-3:35 PM)

- Hospital arrival via jeepney at ED entrance
- Registration clerk Rosa Mendoza begins patient intake
- Basic demographics collected from Maria
- PhilHealth verification attempted but system slow

### Documentation Challenges

- Multiple manual forms required
- Internet connectivity issues affect verification
- Incomplete accident information gathering
- Medical record number (MRN: 2025-05-001234) assigned

**Registration Time: 15 minutes | Total: 50 minutes**

------

## 2. Hospital Registration - SIPOC

• **Suppliers:** Patient and family providing information, PhilHealth system for verification, hospital registration database, emergency department triage system, medical records department

• **Inputs:** Patient demographic information, insurance/PhilHealth details, accident details and mechanism, emergency contact information, previous medical history, legal identification documents

• **Process:** Patient demographic data collection, medical record number assignment, PhilHealth membership verification, insurance eligibility confirmation, emergency contact documentation, patient wristband creation, initial medical record establishment

• **Outputs:** Completed patient registration record, medical record number assignment, patient identification wristband, PhilHealth eligibility confirmation, ED logbook entry, initial billing account creation, patient chart for clinical staff

• **Customers:** Emergency department clinical staff, patient requiring medical care, billing and financial departments, PhilHealth for claims processing, quality assurance

------

## 3. Emergency Department Triage - Scenario

### Triage Assessment (3:35-3:45 PM)

- Nurse Jenny Reyes performs systematic assessment
- Vital signs: BP 110/70, HR 98, RR 22, O2 Sat 96%, Pain 8/10
- Philippine Triage Scale assessment completed
- Category 2 (Urgent - to be seen within 30 minutes)

### Clinical Findings

- Obvious left arm deformity
- Chest and abdominal pain with high-energy mechanism
- Alert and oriented patient
- Emergency Bay 2 assignment with monitoring

**Triage Time: 10 minutes | Total: 65 minutes**

------

## 3. Emergency Department Triage - SIPOC

• **Suppliers:** Registered patient from registration process, triage nurse with clinical assessment skills, triage protocols and guidelines, monitoring equipment for vital signs, Philippine Triage Scale standards

• **Inputs:** Patient presenting with trauma injuries, initial vital signs and basic assessment, mechanism of injury information, patient pain level and symptoms, clinical triage protocols, emergency department capacity status

• **Process:** Primary assessment using ABCDE approach, vital signs measurement and documentation, pain assessment and scoring, triage category assignment using Philippine Triage Scale, resource allocation and bed assignment, initial emergency interventions, notification of clinical staff

• **Outputs:** Triage category assignment (Category 2 - Urgent), completed triage assessment form, vital signs documentation, pain assessment score, bed assignment (Emergency Bay 2), clinical priority designation, notification to emergency physician

• **Customers:** Emergency physician requiring prioritized patients, patient needing appropriate level of care, ED staff for workflow coordination, hospital bed management

------

## 4. Emergency Physician Assessment - Scenario

### Initial Evaluation (3:45-4:05 PM)

- Dr. Maria Reyes begins systematic trauma assessment
- History from Maria: high-speed collision, no loss of consciousness
- Physical exam reveals multiple trauma areas
- Clinical impression: hemodynamically stable, multiple injuries

### Treatment Decisions

- Pain management: Morphine 5mg IV
- Diagnostic workup: X-ray chest, abdomen, left forearm
- Laboratory: CBC, basic metabolic panel
- Continuous monitoring initiated

**Assessment Time: 20 minutes | Total: 85 minutes**

------

## 4. Emergency Physician Assessment - SIPOC

• **Suppliers:** Triaged patient from triage process, emergency physician with trauma experience, clinical examination equipment, patient's family providing history, nursing staff for assistance

• **Inputs:** Patient with assigned triage priority, triage assessment and vital signs, mechanism of injury details, patient symptoms and complaints, clinical examination tools, emergency department protocols

• **Process:** Systematic trauma assessment (primary and secondary survey), detailed history taking from patient and family, physical examination of all body systems, clinical decision making for diagnostic workup, initial treatment order generation, pain management and comfort measures, documentation in emergency record

• **Outputs:** Completed emergency physician assessment, initial diagnosis and clinical impression, diagnostic test orders (imaging, laboratory), treatment orders (medications, monitoring), clinical documentation in medical record, communication with family about findings, consultation requests if needed

• **Customers:** Patient requiring diagnosis and treatment, radiology department receiving imaging orders, laboratory receiving test orders, nursing staff implementing treatment orders, family needing medical information, consulting specialists

------

## 5. X-ray Imaging Process - Scenario Part 1

### Order and Scheduling (4:05-4:20 PM)

- Dr. Reyes completes manual X-ray requisitions
- Three studies ordered: chest, abdomen, left forearm
- Nurse hand-carries requisitions to radiology
- 15-minute delay for equipment availability

### Image Acquisition (4:20-4:40 PM)

- Roberto Gonzalez, radiology technologist, receives patient
- Patient positioning challenging due to pain and injuries
- Three separate X-ray studies completed
- Technical quality assessment confirms diagnostic images

**Process Time So Far: 35 minutes**

------

## 5. X-ray Imaging Process - Scenario Part 2

### Processing and Review (4:40-5:15 PM)

- Film processing in automatic processor: 8 minutes
- Roberto reviews films for technical quality
- Obvious fractures noted in forearm X-ray
- Possible rib fractures identified on chest X-ray
- Immediate call to Dr. Reyes for urgent findings
- Films prepared for radiologist interpretation

**Total X-ray Process Time: 70 minutes | Total: 155 minutes**

------

## 5. X-ray Imaging Process - SIPOC

• **Suppliers:** Emergency physician providing imaging orders, radiology department (clerk, technologist), X-ray equipment and imaging systems, patient transport services, radiology supplies and materials

• **Inputs:** Completed X-ray requisition forms, patient with clinical indications for imaging, X-ray equipment and accessories, positioning aids and immobilization devices, film or digital imaging supplies, radiation safety equipment

• **Process:** Order receipt and patient scheduling, patient transport to radiology department, patient identification and order verification, patient positioning and immobilization, radiation safety measures implementation, image acquisition (multiple views as ordered), image processing and quality assessment, patient return to emergency department

• **Outputs:** Completed X-ray images (chest, abdomen, forearm), technical quality assessment, images prepared for radiologist interpretation, patient returned to emergency care, radiation dose documentation, imaging completion notification

• **Customers:** Radiologist requiring images for interpretation, emergency physician awaiting results, patient needing diagnostic information, medical records for documentation, radiation safety officer

------

## 6. Radiology Interpretation - Scenario

### Radiologist Review (5:15-5:45 PM)

- Dr. Ramon Mendoza receives urgent films
- Systematic interpretation of all three studies
- **Chest X-ray:** Left 7th and 8th rib fractures
- **Abdominal X-ray:** No obvious free air or abnormalities
- **Forearm X-ray:** Comminuted radius and ulna fractures

### Critical Communication (5:35 PM)

- Immediate phone call to Dr. Reyes
- Recommendation for CT scan due to rib fractures
- Formal reports dictated and transmitted
- Orthopedic consultation suggested for forearm fractures

**Interpretation Time: 30 minutes | Total: 185 minutes**

------

## 6. Radiology Interpretation - SIPOC

• **Suppliers:** Completed X-ray images from imaging process, radiologist with interpretation expertise, previous imaging studies if available, clinical information from requisitions, radiology information system

• **Inputs:** X-ray images requiring interpretation, clinical history and indications, patient demographic information, previous comparative studies if available, radiology reporting templates, medical terminology and coding systems

• **Process:** Systematic image review and analysis, comparison with previous studies if available, findings documentation and reporting, critical findings identification, report generation and documentation, communication of urgent findings, report transmission to ordering physician

• **Outputs:** Formal radiology reports for all studies, critical findings immediate communication, diagnostic impressions and recommendations, documentation in radiology information system, report transmission to emergency department, billing codes for radiology services

• **Customers:** Emergency physician making treatment decisions, patient needing diagnostic results, medical records for permanent documentation, billing department for service coding, quality assurance for peer review

------

## 7. CT Scan Process - Scenario Part 1

### CT Decision and Ordering (5:45-6:30 PM)

- Dr. Reyes decides CT scan needed based on rib fractures
- Family discussion about procedure and contrast
- CT requisition completed with clinical indications
- Patient preparation: NPO status, IV access verification
- 30-minute scheduling delay due to routine scan ahead

### Patient Preparation and Transport

- Grace Villanueva, CT technologist, prepares room
- Patient transported to CT scanner
- Consent obtained for IV contrast administration
- Patient positioned on scanner table

**Preparation Time: 45 minutes**

------

## 7. CT Scan Process - Scenario Part 2

### Image Acquisition (6:45-7:10 PM)

- Multi-phase CT protocol executed
- Non-contrast, arterial, and portal venous phases
- 100ml IV contrast administered at 3ml/second
- Patient monitoring throughout procedure

### Technical Review and Urgent Findings (7:10-7:30 PM)

- Grace notes concerning findings requiring immediate attention
- Apparent abdominal fluid identified
- Immediate call to Dr. Mendoza for urgent interpretation
- Patient returned to ED while images processed

**Total CT Process Time: 105 minutes | Total: 290 minutes**

------

## 7. CT Scan Process - SIPOC

• **Suppliers:** Emergency physician ordering CT scan, CT technologist and support staff, CT scanner and associated equipment, contrast agents and injection systems, patient monitoring equipment

• **Inputs:** CT scan requisition with clinical indications, patient requiring advanced imaging, IV contrast agent and injection protocol, CT scanner and positioning equipment, patient monitoring devices, radiation safety protocols

• **Process:** Patient preparation and consent obtaining, IV contrast agent preparation and administration, patient positioning and immobilization, CT scan protocol selection and execution, multi-phase imaging (non-contrast, arterial, venous), image reconstruction and processing, quality assessment and urgent findings identification, patient monitoring during and after procedure

• **Outputs:** Completed CT images (chest, abdomen, pelvis), image data for radiologist interpretation, contrast administration record, radiation dose documentation, patient monitoring records, urgent findings preliminary assessment

• **Customers:** Radiologist requiring images for interpretation, emergency physician awaiting critical results, patient needing diagnostic information, surgical teams if intervention needed, radiation safety officer

------

## 8. CT Interpretation - Scenario

### Critical Findings (7:30-8:00 PM)

- Dr. Mendoza performs urgent CT interpretation
- Critical findings identified:
  - Grade II liver laceration with moderate hemoperitoneum
  - Grade II splenic laceration with perisplenic hematoma
  - Left rib fractures with small pleural effusion

### Immediate Communication (7:42 PM)

- Emergency call to Dr. Reyes with critical findings
- Recommendation for immediate surgical consultation
- Dr. Jose Cruz, general surgeon, contacted
- Detailed report completion and transmission

**CT Interpretation Time: 30 minutes | Total: 320 minutes**

------

## 8. CT Interpretation - SIPOC

• **Suppliers:** Completed CT images from scanning process, radiologist with advanced imaging expertise, PACS or image viewing workstation, clinical information and previous studies, radiology reporting system

• **Inputs:** CT images requiring interpretation, clinical history and mechanism of injury, previous imaging studies for comparison, laboratory results for correlation, radiology interpretation protocols, critical findings communication pathways

• **Process:** Systematic CT image review and analysis, multi-planar image reconstruction and review, comparison with baseline and previous studies, critical findings identification and grading, comprehensive report generation, immediate communication of critical findings, report finalization and transmission

• **Outputs:** Comprehensive CT interpretation report, critical findings immediate communication, injury grading and severity assessment, treatment recommendations, documentation in medical record, surgeon notification for critical findings

• **Customers:** Emergency physician requiring immediate information, surgical team for operative planning, patient and family needing diagnostic information, ICU team for admission planning, medical records for permanent documentation

------

## 9. Surgical Consultation - Scenario Part 1

### Surgeon Assessment (8:00-8:20 PM)

- Dr. Jose Cruz arrives for urgent consultation
- Clinical reassessment: stable vitals, increased pulse to 105
- Abdominal exam: increased tenderness, slight distension
- Image review with Dr. Mendoza

### Treatment Decision (8:20 PM)

- Grade II injuries with stable hemodynamics
- Conservative management decision made
- ICU admission for close monitoring
- Serial examinations and laboratory monitoring planned

------

## 9. Surgical Consultation - Scenario Part 2

### Multidisciplinary Planning (8:20-8:45 PM)

- Dr. Elena Ramos, orthopedic surgeon, consulted
- Forearm fractures require surgical repair
- Surgery delayed until internal injuries stabilized
- Family conference with all specialists

### Treatment Plan Finalized

- Priority 1: ICU monitoring for internal injuries
- Priority 2: Orthopedic surgery in 24-48 hours
- Expected hospitalization: 5-7 days
- Consent obtained for treatment plan

**Consultation Time: 45 minutes | Total: 365 minutes**

------

## 9. Surgical Consultation - SIPOC

• **Suppliers:** Radiologist providing critical imaging findings, general and orthopedic surgeons, emergency physician coordinating care, surgical consultation protocols, operating room availability information

• **Inputs:** Critical CT findings requiring surgical evaluation, patient with stable but concerning injuries, surgical expertise and clinical experience, operating room schedules and availability, anesthesia consultation availability, ICU bed availability for post-operative care

• **Process:** Surgical assessment and examination, review of imaging studies and findings, treatment decision making (conservative vs. operative), risk-benefit analysis for surgical intervention, multidisciplinary consultation and coordination, patient and family counseling about options, treatment plan development and documentation

• **Outputs:** Surgical consultation report, treatment plan (conservative management), ICU admission orders, orthopedic surgery scheduling plan, patient and family education, care coordination with multiple specialties

• **Customers:** Patient requiring definitive treatment plan, ICU team receiving patient admission, orthopedic surgery for delayed procedure, family needing treatment information, case management for care coordination

------

## 10. ICU Admission - Scenario Part 1

### Admission Coordination (8:45-9:10 PM)

- ICU bed 3 assigned and prepared
- Linda Garcia, ICU supervisor, coordinates admission
- Transfer orders completed by Dr. Reyes
- Financial counseling provided to family

### Cost and Insurance (9:00 PM)

- PhilHealth verification finally completed
- Active member status confirmed
- ₱32,000 trauma case rate coverage
- Estimated total cost: ₱80,000-120,000
- Out-of-pocket estimate: ₱48,000-88,000

------

## 10. ICU Admission - Scenario Part 2

### ICU Transfer (9:10-9:30 PM)

- Patient transferred to ICU bed 3
- Continuous monitoring established
- ICU nurse Jennifer Reyes completes admission assessment
- Family accommodation arranged in waiting area

### Care Establishment

- Hourly vital sign monitoring initiated
- Pain management and comfort measures
- ICU flow sheet documentation begun
- Multidisciplinary care team communication

**ICU Admission Time: 45 minutes | Total: 410 minutes (6h 50min)**

------

## 10. ICU Admission - SIPOC

• **Suppliers:** Surgical team providing admission orders, ICU nursing staff and physicians, bed management and coordination, ICU monitoring equipment, pharmacy for medication orders

• **Inputs:** ICU admission orders from surgical consultation, patient requiring intensive monitoring, ICU bed availability, monitoring and life support equipment, nursing protocols for trauma patients, medication orders for pain and monitoring

• **Process:** ICU bed preparation and equipment setup, patient transfer from emergency department, comprehensive nursing admission assessment, monitoring equipment connection and calibration, initial nursing care plan development, physician admission note completion, family education about ICU policies

• **Outputs:** Patient successfully admitted to ICU, continuous monitoring established, nursing care plan initiated, physician admission documentation, family education and support, coordinated care team communication

• **Customers:** Patient requiring intensive monitoring, ICU medical team providing ongoing care, family needing information and support, insurance/billing for admission documentation, case management for discharge planning

------

## Complete Timeline Summary

| Time    | Elapsed | Process Step               | Duration |
| ------- | ------- | -------------------------- | -------- |
| 2:45 PM | 0 min   | **Accident occurs**        | -        |
| 3:20 PM | 35 min  | **Hospital arrival**       | 35 min   |
| 3:35 PM | 50 min  | **Registration complete**  | 15 min   |
| 3:45 PM | 60 min  | **Triage complete**        | 10 min   |
| 4:05 PM | 80 min  | **Physician assessment**   | 20 min   |
| 5:15 PM | 150 min | **X-ray process complete** | 70 min   |
| 5:45 PM | 180 min | **X-ray interpretation**   | 30 min   |
| 7:30 PM | 285 min | **CT scan complete**       | 105 min  |
| 8:00 PM | 320 min | **CT interpretation**      | 30 min   |
| 8:45 PM | 365 min | **Surgical consultation**  | 45 min   |
| 9:30 PM | 410 min | **ICU admission complete** | 45 min   |

**Total Door-to-Disposition Time: 6 hours 10 minutes**

------

## Performance Analysis

### Current vs. Target Performance

| Metric                  | Current  | Target   | Gap       |
| ----------------------- | -------- | -------- | --------- |
| **Door-to-Triage**      | 15 min   | <10 min  | -5 min    |
| **Door-to-Physician**   | 25 min   | <15 min  | -10 min   |
| **X-ray Process**       | 70 min   | <30 min  | -40 min   |
| **CT Process**          | 105 min  | <60 min  | -45 min   |
| **Critical Results**    | 15 min   | <10 min  | -5 min    |
| **Door-to-Disposition** | 6h 10min | <4 hours | -2h 10min |

**Key Insight:** Major delays concentrated in imaging processes (2h 45min total)

------

## Key Pain Points Identified

### Process Delays

- **Transport decision:** 15 minutes initial delay at accident scene
- **Registration bottlenecks:** PhilHealth system connectivity issues
- **Manual requisitions:** Hand-carried forms between departments
- **Equipment scheduling:** Waiting for X-ray and CT availability
- **Communication delays:** Phone-based result reporting

### System Issues

- **Paper-based workflows:** Manual forms and documentation
- **Limited integration:** No automatic order transmission
- **Single points of failure:** One CT scanner, one radiologist
- **Resource constraints:** Equipment and staffing limitations
- **Information silos:** Disconnected departmental systems

------

## Major Improvement Opportunities

### Quick Wins (0-3 months)

- **Standardized protocols:** Consistent trauma care pathways
- **Mobile communication:** WhatsApp groups for urgent findings
- **Parallel processing:** Concurrent rather than sequential activities
- **Process optimization:** Eliminate unnecessary waiting periods

### Technology Solutions (3-18 months)

- **Electronic ordering:** CPOE system implementation
- **Digital imaging:** PACS with immediate image availability
- **System integration:** HL7/DICOM connectivity
- **Mobile access:** Real-time results for physicians
- **Automated scheduling:** Resource optimization systems

------

## Future State Vision

### Optimized Performance Targets

- **Door-to-triage:** <10 minutes with electronic registration
- **Imaging process:** <45 minutes with digital workflow
- **Result reporting:** <5 minutes with automated notifications
- **Total disposition:** <3 hours for similar complexity cases

### Technology-Enabled Workflow

- **Integrated systems:** Seamless data flow between departments
- **Real-time communication:** Immediate status updates
- **Automated processes:** Reduced manual handoffs
- **Mobile accessibility:** Point-of-care information access
- **Predictive analytics:** Resource planning and optimization

------

## Implementation Strategy

### Phase 1: Foundation (Months 1-6)

- **EMR deployment:** Electronic clinical documentation
- **Digital X-ray:** Eliminate film processing delays
- **Basic integration:** Core system connectivity
- **Staff training:** Change management and workflow education

### Phase 2: Integration (Months 7-12)

- **System connectivity:** Full EMR-RIS-PACS integration
- **Automated workflows:** Electronic ordering and results
- **Mobile solutions:** Physician access to information
- **Performance monitoring:** Metrics dashboards and alerts

### Phase 3: Optimization (Months 13-18)

- **Advanced features:** Clinical decision support systems
- **Analytics and AI:** Predictive modeling and optimization
- **Telemedicine:** Remote consultation capabilities
- **Continuous improvement:** Ongoing workflow refinement

------

## SIPOC Analysis Benefits

### Process Understanding

- **Complete stakeholder mapping:** All parties involved identified
- **Clear input/output relationships:** Information and resource flows
- **Dependency identification:** Critical handoff points
- **Bottleneck recognition:** Where delays commonly occur

### Improvement Planning

- **Systematic analysis:** Structured approach to problem identification
- **Technology requirements:** Clear system needs for each process
- **Integration opportunities:** Connection points between systems
- **Success metrics:** Measurable outcomes for each process step

------

## Workshop Applications

### Clinical Workflow Analysis Workshop

Using this combined scenario and SIPOC analysis:

- **Current state mapping:** Document existing processes in detail
- **Pain point identification:** Analyze delays and inefficiencies systematically
- **Future state design:** Redesign processes for optimal patient outcomes
- **Implementation planning:** Develop phased improvement approaches

### System Architecture Mapping Workshop

- **System requirements:** Identify technology needs for each process
- **Integration design:** Plan connectivity between departmental systems
- **Data flow mapping:** Understand information exchange requirements
- **Technical architecture:** Design comprehensive IT infrastructure

------

## Key Takeaways

### For Healthcare Leaders

- **Process complexity:** Healthcare workflows involve many stakeholders
- **Integration importance:** Connected systems dramatically improve efficiency
- **Patient impact:** Workflow delays directly affect clinical outcomes
- **Investment justification:** Clear ROI from systematic improvements

### For Clinical Staff

- **Workflow understanding:** Your work fits into larger care processes
- **Technology benefits:** Systems can reduce documentation burden
- **Communication importance:** Effective handoffs improve patient safety
- **Continuous improvement:** Small changes can make big differences

### for IT Professionals

- **Clinical context matters:** Understanding workflows drives better solutions
- **Standards enable integration:** HL7, DICOM, and IHE facilitate connectivity
- **User experience critical:** Technology must support, not hinder, clinical work
- **Phased implementation:** Gradual deployment reduces risk and increases success

------

## Questions for Discussion

1. **Which process steps show the most improvement potential?**
2. **What technology investments would have the highest impact?**
3. **How can we prioritize improvements with limited resources?**
4. **What barriers might prevent successful implementation?**
5. **How would you measure success of these improvements?**
6. **What role should patients and families play in redesign?**

------

## Workshop Preparation

### What You'll Do Next

- **Apply SIPOC framework** to your own healthcare processes
- **Map current workflows** using systematic analysis techniques
- **Identify improvement opportunities** through structured problem-solving
- **Design future state processes** with appropriate technology integration
- **Develop implementation plans** for sustainable change

### Tools You'll Use

- Process mapping templates based on this case scenario
- SIPOC analysis worksheets for structured documentation
- Pain point identification and prioritization matrices
- Technology assessment and selection frameworks
- Implementation planning guides with Philippine context

**Ready to transform healthcare delivery through systematic analysis!**