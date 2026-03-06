# Integrated Clinical Data Framework: Depth of Care Hierarchy

Claude: Feb-04-2026

## Synthesis: Combining Data Type Hierarchy with Clinical Reasoning Depth

You've articulated the **epistemological structure of clinical decision-making**. This "depth of care" dimension reveals **how clinical data flows through the cognitive and therapeutic process**—from establishing context to measuring impact. This is the **clinical reasoning pipeline** that transforms raw data into actionable intelligence and, ultimately, into outcomes.

Let me integrate your depth framework with the type hierarchy to create a **two-dimensional clinical data matrix**.

------

## The Clinical Data Depth-of-Care Hierarchy: Seven Levels

```
CLINICAL REASONING DEPTH PROGRESSION
═══════════════════════════════════════════════════════════════

Level 7: OUTCOMES ────────────► Did we achieve health goals?
   │                              (Long-term results, quality of life,
   │                               functional recovery, mortality)
   │
Level 6: EFFECT ──────────────► What happened after intervention?
   │                              (Treatment response, physiologic change,
   │                               adverse events, trajectory shift)
   │
Level 5: ACTION ──────────────► What did we do?
   │                              (Medications given, procedures performed,
   │                               orders executed, interventions delivered)
   │
Level 4: INTENT ───────────────► What are we planning to do and why?
   │                              (Orders placed, care plan, clinical reasoning,
   │                               diagnostic/therapeutic strategy)
   │
Level 3: EVIDENCE ─────────────► What objective findings inform decisions?
   │                              (Lab results, imaging, vital signs, physical
   │                               exam findings, diagnostic test results)
   │
Level 2: CURRENT STATE ────────► What is the patient's condition now?
   │                              (Active problems, symptoms, functional status,
   │                               current medications, acute complaints)
   │
Level 1: BASELINE ─────────────► Who is this patient fundamentally?
                                  (Identity, demographics, past medical history,
                                   genetic/social context, chronic conditions)

STATIC/CONTEXTUAL ←───────────────────────→ DYNAMIC/CAUSAL
```

------

## Detailed Framework: Depth of Care × Data Characteristics

### LEVEL 1: BASELINE (Foundation)

**Clinical Question**: *"Who is this patient? What is their context?"*

**Characteristics**:

- **Temporality**: Static or very slow-changing (years)
- **Volatility**: Low (rarely updated)
- **Decision Impact**: Provides risk context and constraints
- **Cognitive Function**: Establishes patient identity and background risk profile

| **Data Category**          | **Examples**                                          | **Clinical Use**                           | **Update Frequency**        |
| -------------------------- | ----------------------------------------------------- | ------------------------------------------ | --------------------------- |
| **Identity**               | Name, DOB, MRN, gender, race/ethnicity                | Patient matching; demographic risk factors | Essentially never           |
| **Genetic/Family Context** | BRCA1 carrier, family history of early MI             | Hereditary risk stratification             | Rarely (at diagnosis)       |
| **Past Medical History**   | Remote MI (10 years ago), childhood asthma            | Long-term risk assessment                  | Occasionally (years)        |
| **Surgical History**       | Prior CABG (5 years ago), hysterectomy                | Anatomic changes; surgical risk            | Occasionally (years)        |
| **Social Determinants**    | Housing instability, food insecurity, education level | Social risk; care barriers                 | Occasionally (months-years) |
| **Life-Long Allergies**    | Verified anaphylaxis to penicillin                    | Permanent treatment contraindication       | Rarely (at discovery)       |
| **Chronic Baseline Labs**  | Baseline Cr 1.2, chronic anemia (Hb 10)               | Establishes patient's "normal"             | Rarely (stable chronic)     |

**Data Governance Requirements**:

- Master Patient Index (MPI) for identity
- Reconciliation processes (merging historical records)
- Standardized coding (ICD-10, SNOMED CT for diagnoses)

**Clinical Intelligence Value**: **Low-Moderate** (essential context but doesn't drive acute decisions)

**EMRAM Stage**: 0-3 (registration, basic CDR, problem list capture)

**Absence Impact**: Incomplete risk assessment; may miss contraindications; difficulty with care coordination across settings

------

### LEVEL 2: CURRENT STATE (Active Clinical Picture)

**Clinical Question**: *"What is wrong with the patient right now? What is their presenting condition?"*

**Characteristics**:

- **Temporality**: Present moment to recent days/weeks
- **Volatility**: Moderate (changes with disease progression/treatment)
- **Decision Impact**: Defines the clinical problem to be solved
- **Cognitive Function**: Problem formulation and differential diagnosis generation

| **Data Category**                      | **Examples**                                                 | **Clinical Use**                            | **Update Frequency** |
| -------------------------------------- | ------------------------------------------------------------ | ------------------------------------------- | -------------------- |
| **Chief Complaint/Presenting Problem** | "Chest pain for 2 hours", "Fever and cough × 3 days"         | Initiates diagnostic reasoning              | Each encounter       |
| **Active Problem List**                | Acute decompensated heart failure, Community-acquired pneumonia | Current disease states requiring management | Daily-weekly         |
| **Current Symptoms**                   | Dyspnea (NYHA Class III), pain 7/10, productive cough        | Symptom burden and severity                 | Hourly-daily         |
| **Functional Status**                  | Bedridden (was ambulatory last week), unable to perform ADLs | Acuity and care needs                       | Daily-weekly         |
| **Current Medications**                | Furosemide 40mg PO daily, albuterol inhaler PRN              | Therapeutic baseline; DDI context           | Daily-weekly         |
| **Recent History**                     | Stopped taking diuretics 1 week ago; recent sick contacts    | Precipitating factors                       | Each encounter       |
| **Mental Status**                      | Alert and oriented × 3, anxious, confused (new onset)        | Neurologic/cognitive state                  | Hourly-daily         |

**Data Governance Requirements**:

- Structured problem list with clinical status (active vs. resolved)
- Medication reconciliation processes
- Standardized symptom assessment tools (pain scales, dyspnea scores)

**Clinical Intelligence Value**: **High** (defines what needs to be investigated and treated)

**EMRAM Stage**: 3-4 (structured documentation, CPOE foundation)

**Absence Impact**: Cannot formulate differential diagnosis; unclear treatment priorities; poor care coordination

**Key Distinction from Level 1**: This is **time-sensitive and actionable**—it describes the **current clinical problem** requiring evaluation/treatment.

------

### LEVEL 3: EVIDENCE (Objective Clinical Data)

**Clinical Question**: *"What does the data show? What are the objective findings?"*

**Characteristics**:

- **Temporality**: Real-time to recent hours (results reporting lag)
- **Volatility**: High (changes with disease evolution and interventions)
- **Decision Impact**: Very high (confirms/refutes diagnoses; guides treatment)
- **Cognitive Function**: Hypothesis testing and clinical reasoning refinement

| **Data Category**         | **Examples**                                             | **Clinical Use**                                | **Update Frequency**     |
| ------------------------- | -------------------------------------------------------- | ----------------------------------------------- | ------------------------ |
| **Vital Signs**           | BP 90/60, HR 120, RR 28, SpO2 88%, temp 38.9°C           | Physiologic stability; shock recognition        | Continuous to hourly     |
| **Physical Examination**  | Rales bilateral bases, JVD 10cm, pedal edema 3+          | Clinical findings support/refute diagnoses      | Per assessment (q4-8h)   |
| **Laboratory Results**    | Troponin 2.5 ng/mL, BNP 1200 pg/mL, WBC 18K, lactate 3.2 | Diagnostic confirmation; severity assessment    | Hours to days            |
| **Imaging Results**       | CXR: pulmonary edema, CT PE protocol: no PE              | Anatomic/pathologic findings                    | Hours to days            |
| **Microbiology**          | Blood cultures: S. aureus (MRSA), sputum: P. aeruginosa  | Pathogen identification; antimicrobial guidance | Days (culture time)      |
| **Pathology**             | Biopsy: adenocarcinoma, moderate differentiation         | Definitive diagnosis                            | Days to weeks            |
| **Monitoring Data**       | Telemetry: atrial fibrillation with RVR; A-line: MAP 55  | Continuous physiologic surveillance             | Real-time (milliseconds) |
| **Point-of-Care Testing** | Bedside glucose 45, iSTAT: K+ 6.2 mEq/L                  | Immediate metabolic state                       | Minutes (immediate)      |

**Data Governance Requirements**:

- Laboratory information system (LIS) integration
- HL7 v2 / FHIR for results reporting
- Standardized terminologies (LOINC for labs, RadLex for imaging)
- Critical value alerting and acknowledgment workflows
- Medical device integration (vital signs, telemetry, ventilators)

**Clinical Intelligence Value**: **Very High** (objective truth; diagnostic certainty)

**EMRAM Stage**: 2-5 (ancillary results, device integration, continuous monitoring)

**Absence Impact**: Diagnostic uncertainty persists; cannot confirm/refute hypotheses; delayed or incorrect treatment

**Key Distinction from Level 2**: This is **objective, measured data** vs. subjective clinical state—it provides the **evidence base** for decision-making.

------

### LEVEL 4: INTENT (Clinical Reasoning & Care Planning)

**Clinical Question**: *"What is our diagnostic/therapeutic strategy? Why are we doing this?"*

**Characteristics**:

- **Temporality**: Forward-looking (orders not yet executed)
- **Volatility**: Moderate-high (changes with evidence and response)
- **Decision Impact**: Very high (translates reasoning into action plan)
- **Cognitive Function**: Clinical synthesis, hypothesis generation, and care planning

| **Data Category**                     | **Examples**                                                 | **Clinical Use**                     | **Update Frequency** |
| ------------------------------------- | ------------------------------------------------------------ | ------------------------------------ | -------------------- |
| **Diagnostic Orders**                 | Order: CT angiography chest (r/o PE); Echocardiogram (assess EF) | Structured diagnostic workup         | Per clinical change  |
| **Therapeutic Orders**                | Order: Heparin drip (for suspected PE); Furosemide 40mg IV (for volume overload) | Treatment plan articulation          | Daily or more        |
| **Medication Orders (CPOE)**          | Start vancomycin 1g IV q12h; Increase lisinopril to 20mg daily | Pharmacologic intent                 | Daily or more        |
| **Clinical Rationale/Reasoning**      | "Chest pain + elevated troponin → r/o NSTEMI; starting heparin pending cath" | Documented thought process           | Per encounter        |
| **Care Plans**                        | Goal: euvolemia by discharge; daily weights, strict I/O monitoring | Structured care objectives           | Daily-weekly         |
| **Consultations Requested**           | Consult cardiology for cath consideration; Consult nephrology for AKI | Expertise mobilization               | As needed            |
| **Advanced Directives/Goals of Care** | DNR/DNI status; focus on comfort, not aggressive intervention | Treatment intensity boundaries       | Per goals discussion |
| **Protocols/Order Sets**              | Sepsis bundle activated; STEMI protocol initiated            | Standardized evidence-based pathways | Per diagnosis        |

**Data Governance Requirements**:

- CPOE system with structured order entry
- Clinical decision support (CDS) rules embedded in ordering
- Order set governance (evidence-based content management)
- Clinical documentation standards (progress notes with assessment/plan)
- Interruptive and passive alerts (drug-drug interactions, dosing guidance)

**Clinical Intelligence Value**: **Very High** (translates clinical reasoning into executable plan)

**EMRAM Stage**: 4-6 (full CPOE, CDS integration, protocol-driven care)

**Absence Impact**: Care plan unclear; team misalignment; delayed interventions; reactive rather than proactive care

**Key Distinction from Level 3**: This is **prospective and strategic**—it captures **what we think** and **what we plan to do**, not what has happened yet.

------

### LEVEL 5: ACTION (Intervention Execution)

**Clinical Question**: *"What interventions were actually delivered? What did we do?"*

**Characteristics**:

- **Temporality**: Real-time execution (timestamp critical)
- **Volatility**: High (frequent interventions in acute care)
- **Decision Impact**: Very high (confirms plan execution; creates accountability)
- **Cognitive Function**: Documentation of care delivery; enables closed-loop verification

| **Data Category**                | **Examples**                                                 | **Clinical Use**                        | **Update Frequency**     |
| -------------------------------- | ------------------------------------------------------------ | --------------------------------------- | ------------------------ |
| **Medication Administration**    | Vancomycin 1g IV given at 14:35 (barcode scan confirmed)     | Closes the loop on medication orders    | Per administration event |
| **Procedures Performed**         | Central line placed (right IJ, 3 attempts, ultrasound-guided) | Documentation of invasive interventions | Per procedure            |
| **Interventions Delivered**      | Patient turned q2h; incentive spirometry performed; ambulated 50 feet | Nursing/therapy interventions           | Per intervention         |
| **Treatments Administered**      | Hemodialysis completed (3.5 hours, 2L removed); chest tube inserted | Complex therapeutic interventions       | Per treatment session    |
| **Fluid/Blood Administration**   | 1L NS bolus infused over 1 hour; 2 units PRBCs transfused    | Resuscitation and volume management     | Per infusion             |
| **Nutrition Delivered**          | TPN 1500 kcal/day infused; PO intake: 50% of meals           | Nutritional support tracking            | Daily                    |
| **Patient Positioning/Mobility** | Prone positioning for ARDS; ambulated with PT × 10 minutes   | Non-pharmacologic interventions         | Per intervention         |
| **Deviation Documentation**      | Medication held (patient NPO for surgery); dose refused by patient | Exceptions to plan                      | As they occur            |

**Data Governance Requirements**:

- Electronic Medication Administration Record (eMAR)
- Barcode medication administration (BCMA) infrastructure
- Procedure documentation templates
- Closed-loop administration tracking (order → dispense → administer → document)
- Exception/variance documentation workflows

**Clinical Intelligence Value**: **Very High** (confirms fidelity of care delivery; enables accountability)

**EMRAM Stage**: 5-6 (closed-loop medication, full eMAR, procedure documentation)

**Absence Impact**: Cannot verify treatment delivery; risk of medication errors; no accountability for interventions; medicolegal exposure

**Key Distinction from Level 4**: This is **execution vs. intent**—captures **what actually happened**, including deviations from plan (delayed, refused, held doses).

------

### LEVEL 6: EFFECT (Treatment Response & Physiologic Impact)

**Clinical Question**: *"How did the patient respond? What changed after our interventions?"*

**Characteristics**:

- **Temporality**: Minutes to days post-intervention
- **Volatility**: Very high (dynamic feedback)
- **Decision Impact**: Highest (determines if plan is working or needs adjustment)
- **Cognitive Function**: Therapeutic efficacy assessment; adaptive decision-making

| **Data Category**                  | **Examples**                                                 | **Clinical Use**                            | **Update Frequency** |
| ---------------------------------- | ------------------------------------------------------------ | ------------------------------------------- | -------------------- |
| **Immediate Physiologic Response** | After 1L bolus: BP ↑ 85→110, HR ↓ 120→95, UOP ↑ 10→40 mL/hr  | Real-time hemodynamic response              | Minutes to hours     |
| **Symptom Trajectory**             | Pain score: 8→4→2 after morphine; dyspnea improved after diuresis | Symptom control efficacy                    | Hours                |
| **Lab Trends Post-Intervention**   | After antibiotics: WBC ↓ 18K→12K→9K; lactate ↓ 3.2→1.5       | Infection resolution; organ recovery        | Hours to days        |
| **Disease Severity Scores**        | SOFA score: 8→6→4 (improving); APACHE trending down          | Severity trajectory and prognosis           | Daily                |
| **Therapeutic Drug Levels**        | Vancomycin trough: 8→15 mcg/mL (now therapeutic after dose ↑) | Pharmacokinetic response; dose optimization | Per dosing interval  |
| **Imaging Response**               | CXR: pulmonary edema resolved after diuresis; tumor size ↓ 50% after chemo | Anatomic/pathologic response                | Days to weeks        |
| **Adverse Events**                 | Developed rash after penicillin; acute kidney injury after contrast | Iatrogenic harm detection                   | As they occur        |
| **Functional Improvement**         | Ambulation distance: 10 feet→50 feet→100 feet; ADL independence ↑ | Functional recovery trajectory              | Daily to weekly      |
| **Hemodynamic/Monitoring Trends**  | Cardiac output improving (3.5→4.8 L/min); A-line MAP stable 70-80 | Continuous physiologic stabilization        | Real-time to hourly  |

**Data Governance Requirements**:

- Clinical data warehouse (CDW) for trend analysis
- Automated calculation of delta values and rates of change
- Clinical decision support for response assessment (e.g., "Is this working?")
- Integration of multi-parameter data for composite response metrics
- Adverse event reporting systems (pharmacovigilance)

**Clinical Intelligence Value**: **Highest** (enables precision medicine; adaptive therapy)

**EMRAM Stage**: 6-7 (advanced analytics, predictive models, closed-loop learning)

**Absence Impact**: Trial-and-error medicine; delayed recognition of treatment failure; missed adverse events; inability to personalize therapy

**Key Distinction from Level 5**: This is **causal inference**—it links **interventions (Level 5)** to **outcomes (change in evidence Level 3)**, answering **"Did it work?"**

------

### LEVEL 7: OUTCOMES (Long-Term Results & Quality of Life)

**Clinical Question**: *"Did we achieve the health goals? What was the ultimate impact?"*

**Characteristics**:

- **Temporality**: Weeks to years post-intervention
- **Volatility**: Low (endpoints achieved or not)
- **Decision Impact**: Highest for future patients (learning health system)
- **Cognitive Function**: Quality assessment; comparative effectiveness; system learning

| **Data Category**                    | **Examples**                                                 | **Clinical Use**                         | **Update Frequency** |
| ------------------------------------ | ------------------------------------------------------------ | ---------------------------------------- | -------------------- |
| **Clinical Endpoints**               | 30-day mortality, 90-day readmission, hospital length of stay | Care quality metrics; benchmarking       | Post-discharge       |
| **Functional Outcomes**              | Return to work, independent ADLs, Karnofsky performance score | Patient-centered outcomes                | Weeks to months      |
| **Quality of Life (QOL)**            | PROMIS scores, SF-36, disease-specific QOL measures          | Holistic health impact                   | Months               |
| **Patient-Reported Outcomes (PROs)** | Symptom burden, treatment satisfaction, side effect tolerance | Patient perspective on care              | Ongoing              |
| **Complication Rates**               | Surgical site infection, catheter-associated UTI, falls      | Safety and quality metrics               | Per episode          |
| **Disease Progression**              | Cancer recurrence, HF exacerbation rate, diabetes control (A1c) | Disease trajectory and control           | Months to years      |
| **Survival/Mortality**               | 1-year survival, disease-free survival, all-cause mortality  | Ultimate outcome measure                 | Long-term            |
| **Healthcare Utilization**           | ED visits, hospitalizations, outpatient visits               | System burden and care fragmentation     | Ongoing              |
| **Cost-Effectiveness**               | Cost per QALY, total cost of care, resource utilization      | Economic impact                          | Per episode          |
| **Comparative Effectiveness**        | Treatment A vs. B: survival 75% vs. 65%, QOL higher with A   | Evidence generation for future decisions | Population-level     |

**Data Governance Requirements**:

- Outcomes registries (disease-specific, procedural)
- Longitudinal data linkage across care settings
- Patient-reported outcome (PRO) collection platforms
- Risk-adjustment methodologies (case-mix adjustment)
- Federated data networks for comparative effectiveness research (CER)

**Clinical Intelligence Value**: **Highest for system learning** (informs future care; drives quality improvement)

**EMRAM Stage**: 7 (learning health system, continuous improvement, registry participation)

**Absence Impact**: Cannot learn from experience; quality is invisible; unable to do comparative effectiveness; value-based care impossible

**Key Distinction from Level 6**: This is **ultimate impact assessment**—it moves beyond **"Did the treatment work acutely?"** to **"Did the patient's life improve? What can we learn for future patients?"**

------

## Two-Dimensional Clinical Data Matrix: Type × Depth Integration

Now let's integrate your **depth-of-care hierarchy** with the earlier **data type/intelligence hierarchy** to show how they intersect:

| **Depth Level**            | **Data Type Focus**                                          | **Temporal Nature**            | **EMRAM Stage** | **Clinical Reasoning Phase** | **Example Data Points**                                      |
| -------------------------- | ------------------------------------------------------------ | ------------------------------ | --------------- | ---------------------------- | ------------------------------------------------------------ |
| **Level 1: Baseline**      | Identity, demographics, past medical history, family history | **Static** (years)             | 0-3             | Context establishment        | 65yo male, HTN, prior MI 10y ago, BRCA2+                     |
| **Level 2: Current State** | Active problems, symptoms, functional status, current meds   | **Slow-changing** (days-weeks) | 3-4             | Problem formulation          | Acute decompensated HF, DOE NYHA III, on furosemide 40mg     |
| **Level 3: Evidence**      | Vitals, labs, imaging, physical exam, monitoring data        | **Dynamic** (minutes-hours)    | 2-5             | Hypothesis testing           | BP 90/60, BNP 1800, CXR: pulmonary edema, crackles bilateral |
| **Level 4: Intent**        | Orders, care plans, clinical reasoning, protocols            | **Prospective** (plan future)  | 4-6             | Care planning                | Increase furosemide to 80mg IV, strict I/O, daily weights, goals: euvolemia |
| **Level 5: Action**        | Medication administration, procedures, interventions         | **Real-time execution** (now)  | 5-6             | Care delivery                | Furosemide 80mg IV given 14:30, Foley placed, 1200mL UOP today |
| **Level 6: Effect**        | Treatment response, trends, adverse events, trajectories     | **Feedback** (hours-days post) | 6-7             | Efficacy assessment          | After diuresis: BP ↑ 90→120, weight ↓ 5kg, BNP ↓ 1800→600, breathing improved |
| **Level 7: Outcomes**      | Clinical endpoints, QOL, survival, complications, utilization | **Long-term** (weeks-years)    | 7               | Quality/learning             | Discharged home, 30-day readmission: no, 6mo: ambulatory without DOE, QOL improved |

------

## Critical Insights from the Integrated Framework

### Insight 1: Data Depth Determines Decision-Making Power

The **lower levels (1-2)** answer **"Who and what?"** (descriptive)
The **middle levels (3-4)** answer **"Why and how?"** (diagnostic and prescriptive)
The **upper levels (5-7)** answer **"Did it work and what did we learn?"** (evaluative and adaptive)

**Example: Sepsis Management**

| **Depth** | **Question**                               | **Data Required**                                            | **Decision Enabled**                                         |
| --------- | ------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| **1-2**   | Who is the patient? What's their baseline? | Age, immunosuppression, chronic diseases                     | Risk stratification (high-risk patient)                      |
| **3**     | Do they have sepsis?                       | Vitals (hypotension, tachycardia), labs (lactate 4.5, WBC 22K), source (pneumonia on CXR) | Diagnosis confirmation; severity (septic shock)              |
| **4**     | What's the treatment plan?                 | Sepsis bundle: blood cultures, broad-spectrum ABx, IVF 30mL/kg, vasopressors if needed | Standardized evidence-based care                             |
| **5**     | What did we actually do?                   | Cultures drawn 13:00, vanc+pip-tazo given 13:15, 2L LR bolus given, norepinephrine started | Compliance with time-sensitive protocols                     |
| **6**     | Is it working?                             | Lactate ↓ 4.5→2.2, MAP ↑ 55→70, UOP improving, trending toward vasopressor weaning | Adaptive therapy (de-escalation vs. escalation)              |
| **7**     | What was the outcome?                      | Survived to discharge, return to baseline function, 90-day survival, cost of care | Quality metric; CMS SEP-1 compliance; learning for future patients |

**Without Levels 5-7 data**: You can diagnose and plan, but you cannot verify execution, assess response, or learn from outcomes—**care becomes theoretical rather than evidence-based**.

------

### Insight 2: Cognitive Load and Data Volume Increase Exponentially at Higher Levels

| **Level** | **Data Volume**                   | **Cognitive Load**                          | **Automation Need**                                        |
| --------- | --------------------------------- | ------------------------------------------- | ---------------------------------------------------------- |
| **1-2**   | Low (static, infrequent updates)  | Low (background context)                    | Low (manual entry acceptable)                              |
| **3**     | Moderate (periodic updates)       | Moderate (interpretation required)          | Moderate (automated results reporting)                     |
| **4**     | Moderate (order entry)            | High (synthesis and planning)               | Moderate (CPOE, order sets, CDS)                           |
| **5**     | High (frequent interventions)     | Moderate (execution documentation)          | High (BCMA, automated documentation)                       |
| **6**     | Very high (continuous trends)     | Very high (pattern recognition across time) | **Very high (AI/ML for trend detection)**                  |
| **7**     | Extremely high (population-level) | Very high (comparative analytics)           | **Extremely high (automated registries, risk adjustment)** |

**Implication**: **Levels 6-7 are impossible without advanced analytics and AI**—human clinicians cannot manually track treatment response across dozens of parameters for hundreds of patients.

------

### Insight 3: Clinical Decision Latency Decreases at Higher Levels (Speed Matters More)

| **Level** | **Acceptable Data Lag** | **Decision Urgency** | **Clinical Impact of Delay**         |
| --------- | ----------------------- | -------------------- | ------------------------------------ |
| **1**     | Days to months          | Very low             | Minimal (unless wrong patient)       |
| **2**     | Hours to days           | Low-moderate         | Delayed diagnosis                    |
| **3**     | Minutes to hours        | Moderate-high        | Diagnostic uncertainty               |
| **4**     | Minutes                 | High                 | Delayed treatment initiation         |
| **5**     | Seconds to minutes      | Very high            | Medication errors; missed doses      |
| **6**     | Real-time to minutes    | **Extremely high**   | Missed deterioration; delayed rescue |
| **7**     | Weeks to months         | Low                  | Cannot learn; no QI                  |

**Implication**: **Level 6 (Effect) requires near-real-time analytics**—waiting hours or days to assess treatment response results in preventable harm (e.g., delayed recognition of sepsis treatment failure).

------

### Insight 4: Interoperability Requirements Vary Dramatically by Depth Level

| **Level** | **Primary Interoperability Need**                  | **Standards/Protocols**                                      | **Exchange Urgency**            |
| --------- | -------------------------------------------------- | ------------------------------------------------------------ | ------------------------------- |
| **1**     | Patient matching across systems                    | HL7 v2 ADT, FHIR Patient resource, MPI/EMPI                  | Moderate (care coordination)    |
| **2**     | Medication reconciliation, problem list exchange   | FHIR MedicationStatement, Condition, USCDI                   | Moderate-high (prevent ADEs)    |
| **3**     | Lab/imaging results sharing                        | HL7 v2 ORU, FHIR Observation, DiagnosticReport               | High (avoid duplicate testing)  |
| **4**     | Order exchange (ePrescribing, eOrdering)           | NCPDP SCRIPT, FHIR MedicationRequest, ServiceRequest         | High (care coordination)        |
| **5**     | Real-time intervention notification                | FHIR MedicationAdministration, Procedure                     | Moderate (audit/compliance)     |
| **6**     | Streaming physiologic data; alert exchange         | HL7 v2 ORU (streaming), FHIR Observation (continuous), Fast Healthcare Interoperability Resources (FHIR) Subscription | **Very high (safety-critical)** |
| **7**     | Outcomes registry participation; research networks | FHIR Measure, MeasureReport; CQL (Clinical Quality Language) | Low (retrospective)             |

**Implication**: **Level 6 data exchange (treatment response, real-time monitoring) is safety-critical but technically challenging**—requires streaming data standards and low-latency infrastructure.

------

### Insight 5: Data Quality Requirements Escalate Dramatically for Levels 6-7

| **Level** | **Data Quality Dimension**                                 | **Tolerance for Error** | **Impact of Poor Quality**                         |
| --------- | ---------------------------------------------------------- | ----------------------- | -------------------------------------------------- |
| **1**     | Accuracy (correct patient)                                 | **Zero tolerance**      | Wrong-patient events                               |
| **2**     | Completeness (all active problems)                         | Moderate                | Suboptimal care planning                           |
| **3**     | Validity (physiologically plausible results)               | Low                     | Incorrect diagnoses                                |
| **4**     | Precision (correct dosing, drug selection)                 | Very low                | Medication errors                                  |
| **5**     | Timeliness + accuracy (right med, right time)              | **Very low**            | Patient harm                                       |
| **6**     | **Multi-parameter consistency, temporal alignment**        | **Extremely low**       | **False alarms; missed deterioration**             |
| **7**     | **Risk adjustment, bias mitigation, longitudinal linkage** | **Extremely low**       | **Incorrect quality metrics; unfair benchmarking** |

**Implication**: **Level 6-7 analytics are only as good as the underlying data quality**—predictive models trained on incomplete, biased, or misaligned data produce dangerous false alarms or miss critical events.

------

## Strategic Application: Using the Depth Hierarchy

### Use Case 1: Prioritizing EMR Implementation Features

**Scenario**: Hospital has limited budget. What to implement first?

**Traditional approach**: Start with Level 1-2 (demographics, history capture)
**Depth-informed approach**:

1. **First**: Levels 3-4 (evidence + intent) → Enables diagnostic accuracy and treatment planning
2. **Second**: Level 5 (action) → Closes the loop and prevents medication errors
3. **Third**: Level 6 (effect) → Enables adaptive care and early warning
4. **Finally**: Levels 1-2 (optimize historical documentation)

**Rationale**: **Acute care quality depends on Levels 3-6**; comprehensive Level 1-2 documentation is valuable but less time-sensitive.

------

### Use Case 2: Designing Clinical Decision Support (CDS) by Depth Level

| **CDS Type**               | **Depth Levels Required** | **Example**                                                  | **Implementation Complexity**                    |
| -------------------------- | ------------------------- | ------------------------------------------------------------ | ------------------------------------------------ |
| **Basic alerts**           | 1-2-3                     | Drug-allergy alert (allergy list + current order)            | Low                                              |
| **Diagnostic support**     | 2-3                       | Differential diagnosis generator (symptoms + labs)           | Moderate                                         |
| **Treatment planning**     | 2-3-4                     | Evidence-based order sets (problem + guidelines → orders)    | Moderate-high                                    |
| **Execution verification** | 4-5                       | Medication administration confirmation (order vs. administration) | High (BCMA infrastructure)                       |
| **Response monitoring**    | 3-5-6                     | Sepsis early warning (baseline → evidence → action → effect tracking) | Very high (streaming data, ML)                   |
| **Outcome prediction**     | 1-2-3-6-7                 | Readmission risk model (baseline + current state + treatment response + historical outcomes) | Extremely high (federated data, risk adjustment) |

**Key Insight**: **Most organizations deploy Level 1-4 CDS (alerts, order sets) but struggle with Level 6 CDS (response monitoring)**—the latter requires real-time analytics infrastructure.

------

### Use Case 3: Optimizing Care Team Communication by Depth Level

**Handoff Scenarios**:

| **Transition**              | **Critical Depth Levels** | **What Must Be Communicated**                                |
| --------------------------- | ------------------------- | ------------------------------------------------------------ |
| **Shift handoff (nursing)** | 2-3-4-5                   | Current state, recent evidence, care plan, what was done     |
| **Physician sign-out**      | 2-3-4-6                   | Current problems, pending results, care plan, anticipated changes/responses |
| **Transfer (ICU → floor)**  | 2-3-4-5-6                 | Why admitted, course, current meds/drips, response to treatment, trajectory |
| **Discharge**               | 2-4-5-6-7                 | Diagnosis, treatment given, response, follow-up plan, expected recovery |

**Key Insight**: **Poor handoffs typically miss Levels 5-6 (what was actually done; how they responded)**—this leads to duplicate interventions or missed care continuity.

------

### Use Case 4: Telemedicine and Remote Monitoring Data Requirements

**Telehealth Scenarios by Depth Level**:

| **Clinical Context**           | **Minimum Depth Levels Needed** | **Data Sources**                                             | **Infrastructure**                               |
| ------------------------------ | ------------------------------- | ------------------------------------------------------------ | ------------------------------------------------ |
| **Primary care visit**         | 1-2-3                           | Demographics, problem list, vitals (BP cuff at home)         | Basic video + patient-entered data               |
| **Chronic disease management** | 2-3-6                           | Problem list, home monitoring (glucose, BP, weight), trends  | Bluetooth-enabled devices; data streaming        |
| **Post-discharge follow-up**   | 2-4-5-6                         | Diagnosis, treatment given, adherence, symptoms, recovery trajectory | EMR access + patient-reported outcomes (PROs)    |
| **ICU tele-monitoring**        | 3-5-6                           | Real-time vitals, vent settings, infusions, continuous trends | High-bandwidth streaming; enterprise integration |

**Key Insight**: **Effective telemedicine requires Level 6 data (remote treatment response monitoring)**—without this, remote care is limited to Level 1-3 (history and static assessment).

------

### Use Case 5: Global Health and Low-Resource Setting Adaptation

**Depth Level Prioritization in Resource-Constrained Environments**:

| **Context**                           | **High-Priority Levels** | **Rationale**                                                | **Technology Approach**                                      |
| ------------------------------------- | ------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| **Rural clinic (no EMR)**             | 2-3-4                    | Current state, evidence, care plan (skip extensive Level 1 historical documentation) | Paper forms with structured templates; periodic digitization |
| **District hospital (basic EMR)**     | 3-4-5                    | Evidence, intent, action (close loop on essential medications like antibiotics, antimalarials) | Mobile CPOE + barcode for critical meds                      |
| **Referral hospital (advanced care)** | 3-5-6                    | Evidence, action, effect (monitor treatment response for complex cases) | Point-of-care testing; wireless vital signs; basic trending  |
| **National health system**            | 1-7 (full spectrum)      | Interoperability + outcomes tracking for population health   | National HIE; FHIR APIs; outcomes registries                 |

**Key Insight**: **Low-resource settings should leapfrog Level 1-2 historical documentation and prioritize Levels 3-6 (acute care quality)**—mobile and cloud-based systems enable this.

------

## Advanced Concept: Temporal Loops and Feedback Cycles

The depth hierarchy isn't strictly linear—there are **critical feedback loops**:

```
┌──────────────────────────────────────────────────────────────┐
│                   CLINICAL LEARNING CYCLES                    │
└──────────────────────────────────────────────────────────────┘

IMMEDIATE LOOP (Minutes to Hours):
Evidence (3) → Intent (4) → Action (5) → Effect (6) → [Modify Intent (4)]
Example: Hypotension → Order fluids → Give fluids → BP improves → Continue plan
         OR: BP doesn't improve → Order vasopressors

SHORT-TERM LOOP (Hours to Days):
Current State (2) → Evidence (3) → Intent (4) → Action (5) → Effect (6) → [Update Current State (2)]
Example: Pneumonia diagnosis → ABx ordered → ABx given → Fever resolves, WBC down → Pneumonia improving

LONG-TERM LOOP (Weeks to Years):
Baseline (1) → ... → Outcomes (7) → [Update Baseline (1)]
Example: Prior MI → Treatment → Survived → Now "history of prior MI" for future encounters

POPULATION LEARNING LOOP (Population-Level):
Multiple Patients' Outcomes (7) → [Inform Intent (4) via evidence-based guidelines]
Example: Registry data shows Treatment A better than B → Update order sets/protocols
```

**Key Insight**: **Level 6 (Effect) is the critical feedback node**—it determines whether the immediate loop continues, modifies, or escalates care.

------

## Practical Implementation Roadmap by Depth Level

### Phase 1: Foundation (Levels 1-2) [EMRAM 0-3]

**Goal**: Establish patient identity and baseline clinical context

**Technical Requirements**:

- Master Patient Index (MPI)
- Basic clinical documentation (problem lists, medication lists)
- Registration and scheduling systems

**Clinical Impact**: Care coordination enabled; baseline risk assessment

**Timeline**: 6-12 months

------

### Phase 2: Evidence & Planning (Levels 3-4) [EMRAM 3-4]

**Goal**: Enable evidence-based diagnostic and therapeutic decision-making

**Technical Requirements**:

- LIS/PACS integration (HL7 v2 interfaces)
- CPOE system with order sets
- Basic CDS (drug-drug interactions, allergy checking)
- Structured documentation templates

**Clinical Impact**: Diagnostic accuracy; evidence-based care; reduced errors

**Timeline**: 12-18 months

------

### Phase 3: Execution & Closed Loop (Level 5) [EMRAM 5]

**Goal**: Verify care delivery and close medication loop

**Technical Requirements**:

- eMAR with barcode medication administration (BCMA)
- Procedure documentation templates
- Closed-loop tracking (order → dispense → administer → document)

**Clinical Impact**: Medication safety; accountability; care fidelity

**Timeline**: 18-24 months

------

### Phase 4: Adaptive Intelligence (Level 6) [EMRAM 6]

**Goal**: Enable real-time treatment response monitoring and adaptive care

**Technical Requirements**:

- Clinical data warehouse (CDW) with trend analysis
- Medical device integration (vital signs, monitors)
- Advanced CDS (early warning scores, predictive models)
- Real-time dashboards and alerting

**Clinical Impact**: Early deterioration detection; precision therapy; proactive care

**Timeline**: 24-36 months

------

### Phase 5: Learning Health System (Level 7) [EMRAM 7]

**Goal**: Continuous quality improvement and comparative effectiveness

**Technical Requirements**:

- Outcomes registries (disease-specific)
- Risk-adjustment and benchmarking platforms
- Patient-reported outcome (PRO) collection
- Federated data networks for research
- FHIR APIs for external data exchange

**Clinical Impact**: Quality transparency; evidence generation; value-based care

**Timeline**: 36+ months (ongoing)

------

## Conclusion: Integrating Type and Depth Hierarchies

Your **depth-of-care hierarchy** reveals the **cognitive and therapeutic pipeline** that transforms raw data into health outcomes:

**BASELINE** (who are they?) →
**CURRENT STATE** (what's wrong?) →
**EVIDENCE** (what does the data show?) →
**INTENT** (what should we do?) →
**ACTION** (what did we do?) →
**EFFECT** (did it work?) →
**OUTCOMES** (what was the ultimate impact?)

### Critical Success Factors for Advancing Through Depth Levels:

1. **Lower levels are prerequisites for higher levels**: Cannot assess treatment effect (Level 6) without knowing what was done (Level 5) and what the evidence showed (Level 3)
2. **Temporal resolution increases with depth**: Level 1-2 data changes yearly; Level 6 data changes by the minute
3. **Automation becomes essential at higher levels**: Human clinicians cannot manually track Level 6-7 data at scale—AI/ML required
4. **Clinical decision-making power peaks at Levels 4-6**: These levels transform reactive medicine (responding to problems) into proactive/adaptive medicine (anticipating and preventing deterioration)
5. **Learning health systems require full-spectrum depth**: Cannot improve care without Level 7 outcomes data fed back into Level 4 intent (evidence-based protocols)

------

**Would you like me to develop specific clinical scenarios (e.g., sepsis, heart failure, stroke) mapped across all seven depth levels to illustrate how data flows through this framework? Or create implementation toolkits for advancing from one depth level to the next?**