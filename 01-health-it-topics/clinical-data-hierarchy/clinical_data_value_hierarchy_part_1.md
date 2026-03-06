# Clinical Data Value Hierarchy - Part 1

ChatGPT: Feb-04-2026

## Clinical Data Value Hierarchy (epistemology → decision power → impact)

Think of clinical data as a **reasoning stack**: it starts with priors and context, climbs through state and evidence, and peaks with *causal feedback (response)* and *longitudinal outcomes*. The higher you go, the more you can provide personalized, adaptive care (not just standardized protocols).

### What “value” means here (why some data types are more powerful)

A data type’s clinical decision power increases when it has more of these:

- **Actionability** (can it directly change a decision right now?)
- **Time-sensitivity** (does it track rapid change/deterioration?)
- **Specificity** (does it narrow the differential or pick a treatment?)
- **Causal linkability** (can you tie it to an intervention + time window?)
- **Longitudinal signal** (does it show trajectory, not snapshots?)
- **Computability** (can CDS/analytics reliably use it?)

------

## Levels of data by clinical decision-making power

### Level 0 — Administrative/encounter context (enables interpretation, low decision power)

**Data types:** encounter type, location, team, timestamps, care setting, source of referral
**Enables:** basic coordination, handoffs, knowing “when/where/who”
**Impact:** workflow and safety indirectly; not clinical reasoning by itself

### Level 1 — Patient priors (baseline risk; low-to-moderate decision power)

**Data types:** PMH, comorbidities, allergies, baseline meds, immunizations, family/social history, baseline function
**Epistemic role:** establishes *prior probability* + constraints (contraindications)
**Impact on care:** risk stratification, avoidance of harm, guideline eligibility
**Limits:** often stale, inconsistent, and not time-bound to the current episode

### Level 2 — Episode narrative & clinician hypotheses (moderate power, high framing value)

**Data types:** chief complaint, HPI timeline, ROS, exam narrative, clinician assessment, differential
**Epistemic role:** defines the problem space and hypothesis set
**Impact:** directs what evidence to gather and what to treat first
**Limits:** high nuance but often not computable unless structured

### Level 3 — Objective physiologic state (moderate-to-high power; high immediacy)

**Data types:** vitals, scores, neuro status, I/O, ventilator settings, bedside observations, nursing flowsheets, continuous monitoring trends
**Epistemic role:** “what’s happening now” (reality checks the story)
**Impact:** triage, escalation, deterioration detection, titration decisions
**Value jumps when:** you capture **trends**, not just single values

### Level 4 — Diagnostic evidence (high decision power)

**Data types:** labs, imaging findings, micro/path, ECG, severity scores, diagnostic criteria fulfillment
**Epistemic role:** reduces uncertainty; supports or refutes hypotheses
**Impact:** selects therapies, rules out dangerous alternatives, confirms diagnoses
**Value jumps when:** results have **interpretation + context** (e.g., “AKI stage + baseline creatinine + time course”)

### Level 5 — Problem representation & intent (high leverage; turns evidence into a plan)

**Data types:** structured problem list (with status/severity), goals of care, target ranges, care plan, clinical pathways selection rationale
**Epistemic role:** makes care *goal-directed* and consistent across teams
**Impact:** coherent multidisciplinary care, fewer contradictions, measurable targets
**Key concept:** without **intent**, you can’t reliably judge response

### Level 6 — Interventions actually delivered (high operational impact; necessary for causality)

**Data types:** CPOE orders + execution proof (med admin with dose/time/route, procedures performed, therapies delivered, consult completed, device placed), refusals/holds/reasons
**Epistemic role:** distinguishes “planned care” from “real care”
**Impact:** safety, reliability, auditability, handoffs
**Value jumps when:** interventions are linked to **indication + goal**

### Level 7 — Response to treatment (very high decision power; adaptive personalization happens here)

**Data types:** symptom changes, objective deltas (vitals/labs), side effects, complications, tolerance, adherence, clinician “improving/stable/worsening” tied to evidence
**Epistemic role:** closes the feedback loop; supports *within-patient learning*
**Impact:** titration, stop/continue/switch decisions, early rescue, de-escalation
**This is where “personalized care” becomes real** (the patient’s actual response overrides population averages)

### Level 8 — Outcomes & trajectory (highest-level value; system learning + long-term personalization)

**Data types:** discharge outcomes, mortality, LOS, readmissions, complications, functional status, QoL/PROMs, disease control over time (e.g., HbA1c trend), follow-up completion
**Epistemic role:** validates whether the strategy worked and for whom
**Impact:** pathway refinement, risk models, care redesign, long-term individualized plans
**Value jumps when:** outcomes are tied back to **who received what, when, and why**

------

## The “epistemic bridge” that separates basic digitization from sophisticated reasoning

If you only capture Levels 1–4 well, you get **diagnosis + guideline care**.
To enable increasingly sophisticated clinical reasoning, you need explicit linkages:

- **Problem/goal ↔ intervention** (why this treatment, what target)
- **Intervention ↔ response** (what changed after what, within what time window)
- **Response ↔ next decision** (titration logic)
- **Trajectory ↔ phenotype** (which kinds of patients respond similarly)

Without those links, response/outcome data exists but can’t drive reliable decision support or personalization.

------

## How this maps to adoption sequencing (building on EMRAM logic)

EMRAM gets many orgs to Levels 3–6 (capture, orders, closed-loop meds). The *real frontier* for decision power is **Level 7–8 instrumentation**: response and outcomes that are structured, time-bound, and attributable to interventions. That’s often underdeveloped even in “advanced” implementations.

------

If you want something you can operationalize, I can format this as a **framework + scoring rubric** (per data domain) using the 6 value criteria above, and produce:

- a “minimum viable” dataset for each level,
- required linkages (foreign keys + timestamps),
- and example KPIs (e.g., % interventions with indication+goal; % responses captured within defined windows).