# Clinical Data Value Hierarchy - Part 2

ChatGPT: Feb-04-2026

## Clinical Data Value Hierarchy — operational framework + scoring rubric

### Value criteria (score each data domain against these)

1. **Actionability** (changes a decision now)
2. **Time-sensitivity** (supports rapid detection/titration)
3. **Specificity** (narrows diagnosis or picks therapy)
4. **Causal linkability** (can tie to intervention + time window)
5. **Longitudinal signal** (trajectory vs snapshot)
6. **Computability** (structured + coded enough for CDS/analytics)

Use these for both **assessment** and **roadmap sequencing**.

------

## A. Minimum viable dataset (MVDS) per hierarchy level

### Level 0 — Context (interpretation layer)

**Must have**

- Patient identifier(s)
- Encounter ID, type, start/end, location/unit/bed
- Responsible clinician/team
- Source + timestamps (created/updated, author)

**FHIR-ish anchors:** Patient, Encounter, Location, Practitioner, Provenance

------

### Level 1 — Priors (baseline risk & constraints)

**Must have**

- Allergies (substance + reaction + severity + status)
- Active meds (name + dose + route + frequency + start/end)
- Problem list / comorbidities (coded + onset/active/resolved)
- Baseline function (at least one structured measure) + key social risks (smoking, pregnancy, etc.)

**Key rule:** every item needs **status + “as-of” date**.

**FHIR-ish anchors:** AllergyIntolerance, MedicationStatement/Request, Condition, Observation

------

### Level 2 — Episode narrative & hypotheses (framing layer)

**Must have**

- Chief complaint (structured)
- Symptom onset timeline (start date/time + duration)
- Initial assessment summary + differential (can be narrative but time-stamped + authored)
- Initial exam key findings (at least a structured “abnormal findings” list)

**FHIR-ish anchors:** ClinicalImpression, Condition (suspected), Observation

------

### Level 3 — Objective physiologic state (situational truth)

**Must have**

- Vitals with timestamps (HR, BP, RR, temp, SpO₂)
- Key nursing observations/flowsheet items relevant to acuity (mental status, pain, I/O)
- Trend capability (store as time series; not “latest only”)

**FHIR-ish anchors:** Observation (vitals/flowsheets), Device (optional)

------

### Level 4 — Diagnostic evidence (uncertainty reduction)

**Must have**

- Lab results: test code, value, unit, ref range, collection time, result time
- Imaging reports: modality, body site, impression, timestamp, author
- Micro/path where relevant (specimen, organism, sensitivities)
- Severity/risk score outputs (score + components if available)

**FHIR-ish anchors:** Observation, DiagnosticReport, ImagingStudy, Specimen

------

### Level 5 — Problem representation & intent (goal-directed care)

**Must have**

- Problem list with: code, laterality/site (if applicable), severity/stage, status, start date
- Care goals with: target metric, target range, timeframe, priority
- Plan of care elements: selected pathway/protocol (even as a coded tag)

**This level is the prerequisite for reliable “response.”**

**FHIR-ish anchors:** Condition, Goal, CarePlan

------

### Level 6 — Interventions delivered (what actually happened)

**Must have**

- Orders: what, why (indication), priority, ordering clinician, time
- Execution proof: administered/performed (dose/time/route, performer, completion status)
- Exceptions: held/refused/cancelled + reason

**FHIR-ish anchors:** MedicationRequest + MedicationAdministration, Procedure, ServiceRequest

------

### Level 7 — Response to treatment (feedback loop for personalization)

**Must have**

- A defined **response window** per intervention (e.g., 0–6h, 6–24h, day 2–3)
- Response observations tied to a goal/target (symptom score, vitals trend, lab delta)
- Adverse effects/complications + attribution when known
- Clinician response assessment (improving/stable/worsening) time-stamped + authored

**FHIR-ish anchors:** Observation, AdverseEvent, ClinicalImpression

------

### Level 8 — Outcomes & trajectory (system + long-term personalization)

**Must have**

- Disposition + discharge dx + key complications
- Readmission/ED return within a defined window
- Functional outcome (at least one structured measure) and/or PROMs where relevant
- Longitudinal disease control marker trends (e.g., HbA1c, BP control) with dates

**FHIR-ish anchors:** Encounter (discharge), Condition, Observation, MeasureReport (optional)

------

## B. Required linkages (the “epistemic graph”)

These are the minimum edges that unlock high-value reasoning:

1. **Observation → Encounter** (everything time-bound to the episode)
2. **Condition/Problem → Encounter** (problem-at-encounter context; not just lifetime list)
3. **Goal → Condition (or Problem)** (what success looks like)
4. **Order/Intervention → Condition + Goal** (why it was done; target intended)
5. **Administration/Procedure → Order** (what was actually delivered)
6. **Response Observation → Intervention + Goal + Time window** (what changed after what)
7. **AdverseEvent → Intervention** (harm attribution)
8. **Outcome → Encounter + key Problems + key Interventions** (what happened and why)

If you implement only one “advanced” thing: implement **(4)–(6)** consistently.

------

## C. Scoring rubric (use for baseline assessment + roadmap)

### Scoring scale (0–3)

- **0** = absent or unusable
- **1** = present but inconsistent / mostly narrative / weak timestamps
- **2** = reliable for clinical operations; partial coding/linking
- **3** = reliable + coded + linked + trendable; supports CDS/analytics

### Score each domain (rows) against these dimensions (columns)

**Dimensions**

1. Completeness
2. Timeliness (captured fast enough to matter)
3. Structure/Coding (standard codes, units, enumerations)
4. Provenance (who/when/source-of-truth)
5. Linkability (supports the edges in section B)
6. Longitudinality (trend across time/encounters)

**Domains to score**

- Identity/Encounter context (L0)
- Priors: allergies, meds, comorbidities (L1)
- Episode narrative/hypotheses (L2)
- Vitals/flowsheets (L3)
- Labs/imaging/micro/path (L4)
- Problems + goals + plan (L5)
- Orders + administration/procedures (L6)
- Response + adverse events (L7)
- Outcomes + follow-up trajectory (L8)

**Suggested weighting (if you want a single index)**

- L0–L2: 15%
- L3–L4: 25%
- L5–L6: 30%
- L7–L8: 30%
(Heavily weights “intent → action → response → outcome”.)

------

## D. KPIs (simple, measurable, decision-power oriented)

### Linkage KPIs (most important)

- **% interventions with indication (Condition) recorded**
- **% interventions linked to a Goal/target**
- **% administrations/procedures linked to the originating order**
- **% response observations captured within defined windows after intervention**
- **% adverse events linked to a suspected intervention**
- **% discharge outcomes linked to top Problems + key Interventions**

### Data quality KPIs (high signal, low complexity)

- Allergy list: % with reaction + severity + status + last verified date
- Problem list: % with status + onset + severity/stage (where applicable)
- Vitals: median documentation delay; % with complete set per shift/hour (by unit)
- Labs: % with consistent units + LOINC (or local mapped codes)
- Med admin: barcode compliance rate (if applicable); % “reason for hold/refusal”
- Response: % of sepsis cases with lactate trend captured per protocol window (example pattern)

------

## E. A ready-to-use worksheet template (copy/paste)

**For each domain**, fill:

- Current score (0–3) for each dimension (completeness/timeliness/structure/provenance/linkability/longitudinality)
- Top 3 blockers (workflow, system, governance, terminology, integration)
- Next-step capability (what gets you from 1→2 or 2→3)
- KPI to prove improvement (from section D)

------

If you tell me your priority clinical programs (e.g., meds safety, sepsis, maternal, stroke, diabetes), I can **instantiate Level 7 response windows + goal/target sets** per program so the framework directly drives care personalization (not just generic maturity scoring).