# Clinical Data Depth Hierarchy

ChatGPT: Feb-04-2026

Here’s a practical **hierarchy of clinical data by “depth” of care** — basically: *baseline → current state → evidence → intent → action → effect → outcomes*. Lower levels are more **static/contextual**; higher levels are more **situational, causal, and decision-shaping**.

## Clinical Data Depth Hierarchy (for relevance to clinical care)

### Level 0 — Identity & encounter context (enables everything)

- Patient identity, demographics, encounter type, location/unit, provider/team, timestamps
- Why it matters: without solid context + time, “clinical” data is hard to interpret

### Level 1 — Baseline risk & constraints (mostly slow-changing)

- Past medical history, surgical history, family/social history, allergies, immunizations
- Baseline meds, baseline function, baseline vitals/weight, baseline renal function
- Why it matters: sets risk, contraindications, and starting point

### Level 2 — Presenting episode narrative (situational framing)

- Chief complaint, HPI, ROS, initial exam, triage acuity
- Working symptoms and onset timeline
- Why it matters: defines *what problem we’re solving right now*

### Level 3 — Objective state (what’s happening now, measured)

- Vitals, pain score, neuro status, intake/output, bedside observations
- Nursing flowsheets, device data (vent settings), monitoring trends
- Why it matters: the “truth on the ground” for moment-to-moment decisions

### Level 4 — Diagnostic evidence (why we think it’s happening)

- Labs, imaging results, micro, pathology, scoring tools (e.g., risk scores)
- Differentials (even if narrative), diagnostic reasoning artifacts
- Why it matters: transforms “state” into a *likely cause*

### Level 5 — Clinical intent (what we’re trying to achieve)

- Problem list/diagnoses with severity/stage, goals of care, care plan
- Targets: BP goal, O2 sat target, glucose range, symptom control goals
- Why it matters: makes care *directional* and measurable

### Level 6 — Interventions delivered (what we actually did)

- Orders + administration/verification (meds given, dose/time/route)
- Procedures performed, therapies, devices applied, consults completed
- Education delivered, care coordination actions
- Why it matters: separates “planned” care from **real** care

### Level 7 — Response to treatment (high-level, high-value)

- Trends and deltas: vitals response, symptom change, lab improvement/worsening
- Side effects/adverse events, complications, tolerance, adherence
- Clinician assessment: “improving / stable / deteriorating” tied to evidence
- Why it matters: this is the **feedback loop** that drives next decisions

### Level 8 — Outcomes & longitudinal trajectory (ultimate depth)

- Discharge status, mortality, LOS, readmissions, complications, recovery time
- Functional status, quality of life, PROMs, disease control markers over time (e.g., HbA1c)
- Follow-up completion, sustained response, relapse
- Why it matters: closes the loop on whether care **worked**, not just whether it was delivered

------

## The key trick: make higher-level data *linkable*

To make “response” and “outcomes” clinically useful, you want explicit links:

- **Intervention → indication/goal** (why we did it, what target we aimed for)
- **Response → intervention + time window** (what changed after what, and when)

Example (simple but powerful):

- Antibiotic start time + suspected source + lactate trend + MAP trend + cultures → *true response-to-treatment storyline*

------

If you want, I can turn this into a **data dictionary template** where each level has:

- required fields
- whether it should be structured vs narrative
- minimum timestamps/linkages
- “quality rules” (completeness/consistency)
So you can grade an EMR/HIE dataset by **care depth**, not just by “has notes / has labs.”