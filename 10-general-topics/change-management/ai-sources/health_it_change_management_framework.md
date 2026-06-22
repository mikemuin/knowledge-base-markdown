# Health IT Change Management Framework

## Introduction

This framework was not built in an academic setting. It emerged from field experience — from the pattern recognition that accumulates across Health IT implementations that succeed, fail, and fail quietly enough that no one agrees they've failed at all.

It is a personal work framework. It makes no claims to universality and does not attempt to replace established methodologies. What it offers instead is more specific: a structured way of thinking about change management grounded in what actually happens inside hospitals during a digital transformation.

The central premise is a deliberate departure from how most practitioners frame the discipline. Traditional frameworks are optimistic by design — they assume adoption is achievable and resistance is an exception to be managed. This one starts from the opposite assumption: **failure is the default, and success is what you engineer against it.**

I call this framework "**Failure-Aware Change Management Framework**".

This framework is best understood as a practical guide to managing change by addressing potential failures across the three surfaces on which any Health IT implementation succeeds or fails: **People, Process, and Technology.**

Everything in this body of work derives from a single defining sentence—the **Core Statement**—and the five **Governing Principles** that follow from it.

## Core Statement

> Change management is the disciplined management of failure across People, Process, and Technology — concurrent with project execution — to protect the integrity of the transition from the current to the future state.

------

## Governing Principles

#### 01 — Start on day one, not day done

- Change management runs alongside the project from the beginning — not added after delivery. It is an organizational commitment, not a project phase.

#### 02 — Engineer for failure, not for optimism

- Failure is the default state. Success is what you deliberately build against it. Traditional frameworks assume adoption will happen — this one assumes it won't unless you design for it.

#### 03 — What you can't see will hurt you most

- Projects in Health IT rarely fail loudly. They fail quietly — systems go live while staff find workarounds, metrics look acceptable while clinical behavior hasn't changed. Detection must catch silent failure.

#### 04 — If everyone owns it, no one owns it

- The gap between who is responsible for technology, process, and people is structural — not accidental. Unified accountability for the transition as a whole must be deliberately assigned, not assumed.

#### 05 — Not all failure costs the same

- A training gap is recoverable. A go-live collapse that destroys clinician trust for years is not. A patient safety event is irreversible. The response must match the consequence — stratification is built in from the start.

------

## The PPT Lens

The PPT Lens is the structural overlay through which this framework diagnoses and manages change. PPT stands for **People, Process, and Technology** — three lenses, not three workstreams. They represent the things we need to change.

#### People — the Behavior System

The hardest to manage, the most sensitive, and the domain where many practitioners hesitate to intervene decisively. What you are managing: stakeholder behavior from engagement through adoption — continuously, not episodically.

#### Process — the Execution System

The most consistently underestimated lens. What you are managing: the transition from current state to future state — from what is, to what it should be.

#### Technology — the Enablement System

The most visible, the most concrete, and the most frequently overestimated as a solution in isolation. What you are managing: how tools interact with real human behavior and workflows.

------

### The Relationship Between the Three Lenses

The three lenses are not equal in their structural role.

**People are the behavioral substrate** on which the other two operate. A technically sound system and a well-designed process will both fail if deployed into a field of unmanaged human resistance. People failure is the last failure noticed and the most expensive to recover.

**Process is the execution architecture** that determines whether Technology delivers its intended value. A workflow that cannot withstand real operational conditions will fail regardless of how well the system beneath it functions.

**Technology is the enabler — necessary but not sufficient.** It is the most concrete, the most visible, and the most commonly overestimated as a solution in isolation.

Failure in one layer almost always activates failure in another. This is the cross-layer dynamic the framework is designed to detect early and contain.

Good. I have what I need. Here is the structured outline.

------

## How to Use the Framework

### Overview

The framework is applied identically across all three layers — People, Process, and Technology. For each layer, you first identify its components, then run the same six-question logic against each one. This is not a linear checklist completed at project initiation. It is a **continuous management discipline** executed concurrently with project execution — revisited at every major milestone, every go-live event, and every post-live review cycle.

> The logic sequence: **Identify → Define → Assume → Anticipate → Detect → Prevent → Respond**

------

### Step 1 — Identify the Components of Each Layer

Before any analysis begins, you must map what exists in each layer. This is the inventory step. You cannot define success or anticipate failure against a component you have not named.

#### People Layer Components

- Stakeholder population (by role, authority level, and site)
- Stakeholder positions on the Stakeholder Scale (Active Support → Active Resistance)
- Champion network (existing, potential, coverage gaps)
- Sponsor structure (formal authority holders)
- Resistance nodes (identified and diagnosed)
- Neutral zone (size, location, influence dynamics)
- Governance and decision rights holders

#### Process Layer Components

- Current state workflows (documented and undocumented)
- Future state workflow designs
- Informal and load-bearing undocumented processes
- Handoff points and care coordination dependencies
- Governance and accountability structures (RACI)
- Regulatory and compliance requirements tied to process
- Hybrid state conditions (where old and new processes co-exist)

#### Technology Layer Components

- System(s) being implemented or changed
- Integration points and interfaces (HL7, FHIR, DICOM, legacy feeds)
- Infrastructure (connectivity, devices, power, physical environment)
- Data migration scope and legacy data quality
- Configuration and build decisions
- Training and access provisioning
- Post-live sustainment and support structures

------

### Step 2 — Apply the Six-Question Logic to Each Component

Once the components are identified, each is interrogated using the same six questions. This is where the actual management work happens.

------

#### Question 1 — Define Success

**What does success look like for this component?**

- Success must be defined before failure can be anticipated. A component without a success definition has no failure threshold.
- Definitions must be specific, observable, and time-bounded — not aspirational.
- Success in People is a sustained behavioral state, not a moment.
- Success in Process is the future state executing as designed under real operational conditions.
- Success in Technology is fit, availability, reliability, and usability — sustained beyond go-live.
- Where possible, success definitions should be measurable: adoption rates, cycle times, uptime thresholds, stakeholder position targets.

------

#### Question 2 — List Assumptions

**What are we assuming must be true for this component to succeed?**

- Assumptions are the hidden architecture of every project plan. Name them explicitly or they become silent failure conditions.
- Distinguish between assumptions that are **validated** (confirmed by evidence) and **unvalidated** (accepted without verification).
- Unvalidated assumptions are risk by another name.
- Common assumption categories:
  - *People:* "Sponsor will remain engaged." "Champions are genuinely at Active Support." "Resistance is manageable."
  - *Process:* "Current state documentation reflects how work actually happens." "Future state design can survive real operational conditions." "Staff will follow the new workflow."
  - *Technology:* "Infrastructure is sufficient for deployment." "Integration feeds will be stable." "The vendor's system performs as demonstrated."
- Each assumption should be tracked. When an assumption breaks, the failure control logic activates.

------

#### Question 3 — Identify Risks

**Where can we fail? If this component fails, how does it fail?**

- Risk identification here is not generic. It is component-specific, layer-specific, and grounded in the failure domains documented for each layer.
- The reframe question: *If this project fails, where in this component would the failure originate?*
- Risks must be stratified by consequence:
  - **Recoverable failure** — a workflow redesign, a configuration fix, a training gap
  - **High-cost failure** — clinician trust damage, delayed go-live, adoption collapse
  - **Irreversible failure** — patient safety event, data breach, permanent behavioral rejection of the system
- The risk of silent failure must be explicitly flagged. If this component can fail without anyone noticing, it is the highest-priority risk on the list.
- Cross-layer activation must be considered: failure here — does it trigger failure in another layer?

------

#### Question 4 — Ideate Detection Methods

**How do we detect that failure is happening — or has already happened?**

- Detection must be sensitive to **silent failure**, not just visible failure. Most Health IT implementations fail quietly.
- The diagnostic question: *What does failure look like before anyone admits it is happening?*
- Detection mechanisms must be designed, not assumed. If you have not built a way to see it, you will not see it.
- Detection types:
  - *Instrumented detection:* System logs, uptime monitoring, integration error alerts, adoption dashboards, ticket volume, and categorization
  - *Behavioral detection:* Workflow observation, rounding, floor-level presence, informal conversation with frontline staff
  - *Structural detection:* Stakeholder map updates, champion network health checks, governance attendance, and engagement quality
  - *Outcome detection:* Process KPIs, clinical outcome metrics, error and exception rates, audit trails
- Detection must have a cadence — a defined frequency at which each mechanism is reviewed. Detection without cadence is not a management system; it is documentation.

------

#### Question 5 — Implement Prevention Measures

**What do we do now — before failure occurs — to reduce the probability or severity of failure?**

- Prevention is the proactive investment that determines how much response you will need.
- Prevention is not risk avoidance. In this framework, failure is the default. Prevention is the deliberate engineering of conditions that make success more likely than failure.
- Prevention categories by layer:
  - *People:* Champion cultivation and strategic placement, stakeholder engagement cadence, early resistance diagnosis, sponsor alignment and protection, neutral zone conversion strategies
  - *Process:* Honest current-state discovery (including informal processes), future-state design stress-testing against real operational conditions, hybrid state compression planning, RACI definition and governance establishment, regulatory compliance checkpoints
  - *Technology:* Clinical fit assessment at selection, infrastructure readiness validation per deployment site, integration testing in representative environments, data quality audit before migration, usability validation with actual end users before go-live
- Prevention measures must be owned. A prevention action without an accountable owner is a wish, not a plan.

------

#### Question 6 — Be Ready to Respond

**If failure happens, what do we do?**

- Response planning must precede failure. A response designed during a crisis is a reaction. A response designed before a crisis is a protocol.
- Response must be proportionate to consequence. Not all failures warrant the same response intensity.
  - A training gap → targeted retraining sprint
  - An integration failure → incident escalation to T2/T3 support, downtime procedure activation
  - A go-live collapse → executive escalation, clinical safety protocol, structured recovery plan
  - A patient safety event → immediate clinical response, regulatory notification, root cause analysis
- Response planning should specify:
  - **Trigger:** What is the threshold that activates this response?
  - **Protocol:** What are the specific steps, in sequence?
  - **Owner:** Who activates the response? Who executes it? Who escalates it?
  - **Recovery target:** What does "resolved" look like? How do you confirm it?
- For failures with irreversible consequences, the response plan must also include a harm containment step — stopping the bleeding before root-cause analysis begins.

------

### The Relationship Between the Six Questions

The six questions are not independent. They form a closed logic system:

- **Define** establishes the target. Without it, you cannot identify failure.
- **Assume** surfaces the hidden dependencies. Broken assumptions are where most unexpected failures originate.
- **Anticipate** maps where failure is structurally likely. It focuses your investment in prevention and detection.
- **Detect** creates the visibility. Without it, the framework operates blindly.
- **Prevent** reduces probability and severity before failure occurs.
- **Respond** closes the loop when prevention and detection were insufficient.

Removing any one of the six weakens the entire system. A project with strong prevention and no detection will miss the failures that prevention did not stop. A project with strong detection but no response plan will clearly see failure and be unable to contain it.

------

### A Note on Sequence and Timing

The six questions are applied in sequence during **initial framework setup** for each component. After that, they operate as a **continuous management system** — not a phased activity.

- Detection runs constantly from day one through post-live sustainment.
- Assumptions are revisited at every major milestone.
- Risks are re-stratified when the project environment changes.
- Response protocols are tested before they are needed — not assembled when the alarm sounds.

The framework does not have a completion date. It has a **transfer date** — the point at which accountability formally transfers from the project team to operational management. That transfer must be deliberate, documented, and confirmed by demonstrated capability, not administrative project closure.