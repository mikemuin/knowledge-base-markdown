# Integrated Clinical Data Hierarchy (ICDH) Framework

**A Global Health IT Standard for Moving from Digitization to Clinical Intelligence**

## 1. Executive Summary

The Integrated Clinical Data Hierarchy (ICDH) is a dual-axis framework designed to guide the architectural design, implementation, and optimization of Electronic Medical Records (EMRs). It moves beyond the traditional "module-based" view of Health IT (e.g., "having a CPOE module") and focuses on the **epistemological value** of data—its ability to support clinical reasoning and drive outcomes.

This framework integrates:

1.  **The Depth-of-Care Hierarchy:** Seven levels of clinical reasoning (Baseline → Outcomes).
2.  **The Data Type Hierarchy:** Technical characteristics (Static/Contextual vs. Dynamic/Causal).
3.  **HIMSS EMRAM Alignment:** Mapping data maturity to global adoption standards.

---

## 2. The Seven Levels of Clinical Data Depth

This hierarchy tracks how data flows through the clinical reasoning pipeline—from establishing who a patient is to measuring the ultimate impact of care.'

| Level  | Name              | Clinical Question                         | Cognitive Function    |
| :----- | :---------------- | :---------------------------------------- | :-------------------- |
| **L1** | **Baseline**      | Who is this patient fundamentally?        | Context Establishment |
| **L2** | **Current State** | What is wrong with the patient right now? | Problem Formulation   |
| **L3** | **Evidence**      | What does the objective data show?        | Hypothesis Testing    |
| **L4** | **Intent**        | What is our strategy and why?             | Care Planning         |
| **L5** | **Action**        | What was actually delivered?              | Care Execution        |
| **L6** | **Effect**        | How did the patient respond?              | Efficacy Assessment   |
| **L7** | **Outcomes**      | Did we achieve the ultimate goals?        | System Learning       |

---

## 3. The 2D Clinical Data Matrix: Integration & Alignment

This matrix maps the clinical reasoning depth against technical complexity and international standards (EMRAM).

| Depth Level           | Data Type / Nature        | EMRAM Stage | Temporal Nature   | Example Data Points                    |
| :-------------------- | :------------------------ | :---------- | :---------------- | :------------------------------------- |
| **L1: Baseline**      | Static / Contextual       | 0–3         | Years             | Identity, Genetics, PHM, SDOH          |
| **L2: Current State** | Semi-Static / Present     | 3–4         | Days/Weeks        | Chief Complaint, Active Problem List   |
| **L3: Evidence**      | Dynamic / Measured        | 2–5         | Minutes/Hours     | Vitals, Labs, Imaging, Monitoring      |
| **L4: Intent**        | Prospective / Strategic   | 4–6         | Future-looking    | Orders, Care Plans, Clinical Rationale |
| **L5: Action**        | Real-time / Execution     | 5–6         | Immediate         | Med Admin (BCMA), Procedures           |
| **L6: Effect**        | Feedback / Causal         | 6–7         | Post-intervention | Treatment Response, Symptom Trends     |
| **L7: Outcomes**      | Evaluative / Longitudinal | 7           | Long-term         | Mortality, QOL, Readmission, Cost      |

---

## 4. Implementation Toolkit: Minimum Viable Dataset (MVDS)

To move from one level to the next, organizations must ensure these core data elements are captured with high integrity.

### L1–L2: Foundational Context

*   **Identity (L1):** Unique Identifier (MRN), Name, DOB, Gender, Race/Ethnicity.
*   **Priors (L1):** Past Medical/Surgical History, Family History, Life-long Allergies.
*   **Active State (L2):** Current Medication List, Structured Active Problem List (SNOMED/ICD-10).

### L3–L4: The Decision Engine

*   **Evidence (L3):** Time-stamped Vitals, LIS/PACS Results with reference ranges, Microbiology sensitivities.
*   **Intent (L4):** CPOE Orders linked to an Indication (Problem), Care Goals (Target Parameters), Clinical Decision Support (CDS) logs.

### L5–L6: The Feedback Loop

*   **Action (L5):** eMAR/BCMA timestamps, Procedure logs with performer IDs, Refusals/Holds with reason codes.
*   **Effect (L6):** Delta values (e.g., Lactate clearance), Trend visualization, Adverse Event reports linked to Suspected Interventions.

### L7: The Learning Health System

*   **Outcomes (L7):** 30-day Readmission/Mortality, Patient-Reported Outcome Measures (PROMs), Cost-per-episode, Return-to-function scores.

---

## 5. The "Data Readiness" Scoring Rubric

Evaluate your clinical data assets on a scale of **0 (Absent)** to **3 (Intelligent/Linked)** across six dimensions:

1.  **Completeness:** Is the data captured for all relevant patients?
2.  **Timeliness:** Is the data available fast enough to change the decision?
3.  **Structure/Coding:** Is it discrete (SNOMED, LOINC, RxNorm) or buried in text?
4.  **Provenance:** Do we know who, when, and from what source it originated?
5.  **Linkability:** Can we link the **Intent** (Order) to the **Action** (Admin) and the **Effect** (Response)?
6.  **Longitudinality:** Can we trend this data across time and settings?

---

## 6. Strategic Insights for Global Health IT Leaders

### Insight 1: The "Hinge" of Intent (L4)

Level 4 (Intent) is the most critical transition point. Without documenting **why** an action is taken (linking an order to a problem/goal), Level 6 (Effect) becomes impossible to compute. You cannot automate the assessment of "Is this working?" if the system doesn't know what you were trying to achieve.

### Insight 2: Leapfrogging in Low-Resource Settings

In resource-constrained environments, don't prioritize perfect historical documentation (L1-L2). Instead, **leapfrog** directly to L3 (Evidence) and L4 (Intent) to ensure acute care safety. Use mobile CPOE and point-of-care testing to drive immediate clinical value.

### Insight 3: The AI Prerequisite

Artificial Intelligence and Machine Learning (Level 6 Predictive Analytics) cannot function on Level 1-2 data alone. High-fidelity Level 6 intelligence requires high-resolution Level 3 (Real-time monitoring) and Level 5 (High-fidelity execution) data.

### Insight 4: Interoperability Priorities

Interoperability should prioritize **High-Value, High-Velocity data (L3-L5)** over static summaries. Sharing a real-time lactate trend (L3) or an active high-risk medication order (L4) across facilities saves more lives than sharing a 5-year-old surgical history (L1).

---

## 7. Conclusion

A mature Health IT ecosystem is not measured by the number of digital modules, but by its **Epistemic Depth**. By advancing through these seven levels, healthcare organizations transform from passive record-keepers into dynamic, learning health systems capable of delivering truly personalized and adaptive care.

---

*Framework synthesized by Global Health IT Expert - March 10, 2026*