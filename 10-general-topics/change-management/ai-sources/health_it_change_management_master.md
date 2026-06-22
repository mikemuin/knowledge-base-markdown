# Health IT Change Management Framework
## Master Document

*This document integrates the Technology, Process, and People layers into a unified framework. Each layer is documented in full in its respective companion document. This master document provides the integrating logic, cross-layer failure interactions, consolidated failure domain map, ownership architecture, and implementation guidance.*

---

## Framework Identity

**Name:** Health IT Change Management Framework
**Organizing Principle:** Failure Management across People, Process, and Technology
**Primary Lens:** PPT — People, Process, Technology
**Framework Logic:** Define → Anticipate → Prevent → Detect → Respond
**Scope:** Full EMR and Health IT implementation lifecycle — from vendor selection through post-live sustainment
**Companion Documents:** Technology Layer · Process Layer · People Layer

---

## Core Principle

> **Change management is the disciplined management of failure within People, Process, and Technology — concurrent with project execution — in order to protect the integrity of the transition from current to future state.**

### Five Structural Commitments This Principle Makes

**Concurrency, not sequence.** Disciplined means change management runs alongside project execution from day one — not layered on top after delivery. This is an organizational commitment, not a project phase.

**Failure as the default.** Traditional frameworks assume buy-in is achievable, adoption is linear, and resistance is an exception. This framework starts from the opposite: failure is the default, and success is what you engineer against it. That reframe changes everything about how you design the management system.

**Silent failure is the primary threat.** Projects rarely fail loudly in Health IT. They fail quietly — adoption numbers masking workaround behavior, process maps technically followed but clinically bypassed, systems live and behaviorally ignored. Detection mechanisms must be sensitive to silent failure, not just visible failure.

**The ownership gap is structural.** Failure in change management is almost always ownerless by default. No single person is responsible for the integrity of the People-Process-Technology transition as a whole. This framework must deliberately assign unified accountability — not just distributed ownership per layer.

**Proportionality is non-negotiable.** Not all failures are equal. Some are recoverable — a workflow that needs redesign, a configuration error, a training gap. Others are irreversible — a patient safety event, a data breach, a go-live collapse that destroys clinician trust for years. Stratification logic must be built in from the start.

---

## Strategic Questions

These questions are asked at project initiation and revisited at every major milestone. They operate across all three layers simultaneously.

> **Where can we expect failure?**
> *Reframe: If this project fails, where would it fail?*

> **What does failure look like before anyone admits it is happening?**
> *The silent failure question. The most important detection instrument in the framework.*

> **What are the sociotechnical dynamics at play?**
> *People and Process: Who is positioned where, why, and what informal structures are load-bearing?*

> **Who needs to decide?**
> *Not who is assigned — who will actually exercise the authority when it is required.*

---

## The PPT Framework

Change management operates through three lenses. These are the things we need to change.

| Layer | System | Character | What You Are Managing |
|---|---|---|---|
| **People** | Behavior system | Hardest to manage. Most sensitive. Many hesitate to act. | Stakeholder behavior from engagement through adoption — continuously, not episodically |
| **Process** | Execution system | Easiest to underestimate. | The transition from what is to what it should be — from current state to future state |
| **Technology** | Enablement system | Most often, the easiest to solve. | How tools interact with real human behavior and workflows |

### The Relationship Between the Three Layers

The layers are not equal in their structural role. **People is the behavioral substrate on which the other two operate.** A technically sound system deployed into a field of Passive Resistance will fail. A well-designed process owned by nominal champions will regress. People failure is the last failure noticed and the most expensive to recover.

**Process is the execution architecture that determines whether Technology delivers its intended value.** A system configured against a misrepresented current state produces the wrong future. A workflow that cannot survive real operational conditions fails regardless of how well the technology underneath it functions.

**Technology is the enabler — necessary but not sufficient.** It is the most concrete, the most visible, and the most commonly overestimated as a solution in isolation.

Failure in one layer almost always activates failure in another. This is the cross-layer dynamic the framework is designed to manage.

---

## Layer Success Definitions

### Technology
> *The system is the right fit for context, consistently available in the real deployment environment, behaviorally reliable, and usable enough that clinical and operational staff perform their intended workflows through it — enabling measurable process outcomes — and this state is sustained beyond go-live.*

### Process
> *The future state workflow is executing as designed — by actual clinical staff, in real operational conditions — with load-bearing informal processes consciously addressed, the hybrid state compressed to its minimum viable duration, measurable improvement in defined process outcomes achieved, and no regression to the current state.*

### People
> *The People layer succeeds when enough stakeholders of sufficient authority are positioned at Passive Support or above — with active champions distributed at critical nodes across the organizational network — to sustain forward project momentum through resistance, absorb setbacks without regression, and progressively move the neutral zone upward as the project matures.*

---

## Consolidated Failure Domain Map

Twenty failure domains across three layers. Each domain inverts a specific element of its layer's success definition.

> ⚠ indicates **high silent failure risk** — failure most likely to be invisible before it becomes irreversible or normalized.
> 14 of 20 domains carry silent failure risk.

### Technology Layer — 6 Domains

| # | Domain | Failure Mode | Owner |
|---|---|---|---|
| T1 | Selection failure ⚠ | Wrong fit — vendor chosen without adequate clinical alignment | CIO / CMIO |
| T2 | Infrastructure failure | Not available — system inaccessible in the real deployment environment | IT Infrastructure Lead |
| T3 | Integration & data quality failure ⚠ | Not reliable — system produces inconsistent or clinically unsafe data | Health Informatics Lead / Integration Architect |
| T4 | Adoption & workaround failure ⚠ | Not usable — staff bypass the system in real clinical conditions | Clinical Informatics Lead / Change Manager |
| T5 | Strategic misalignment failure ⚠ | Doesn't enable process — technology live but outcomes not achieved | CMIO / Project Sponsor |
| T6 | Post-live degradation failure | Not sustained — system drifts from its intended state after go-live | IT Operations Lead / Clinical Informatics Lead |

### Process Layer — 7 Domains

| # | Domain | Failure Mode | Owner |
|---|---|---|---|
| P1 | Current state misrepresentation ⚠ | Future state designed against a false picture of how work is actually done | Clinical Process Owner / Business Analyst |
| P2 | Future state design failure | The designed workflow cannot survive real operational conditions | Clinical Informatics Lead / CMIO |
| P3 | Hybrid state persistence ⚠ | Transition period extends, normalizes, and becomes operational reality | Project Manager / Clinical Operations Lead |
| P4 | Informal process blindness ⚠ | Load-bearing undocumented processes never found; care coordination breaks | Clinical Process Owner + Front-line Staff |
| P5 | Execution inconsistency ⚠ | Workflow executes correctly for champions; not for the median performer | Clinical Operations Manager / Unit Leads |
| P6 | Outcome misalignment ⚠ | Workflow executes correctly and produces no measurable improvement | CMIO / Project Sponsor |
| P7 | Regression | Future state erodes back toward current state under operational pressure | Clinical Operations Management / Department Heads |

### People Layer — 7 Domains

| # | Domain | Failure Mode | Owner |
|---|---|---|---|
| Pe1 | Stakeholder mapping failure ⚠ | You do not know who is where, why, or what authority they hold | Change Manager / Project Sponsor |
| Pe2 | Critical node exposure ⚠ | A critical node held by someone whose position makes them a structural threat | Project Sponsor / Executive Leadership |
| Pe3 | Authority-behavior gap ⚠ | The person with the title does not exercise the authority | Project Sponsor |
| Pe4 | Champion deficit ⚠ | Insufficient champions, wrong distribution, burnout before sustainability | Change Manager + Executive |
| Pe5 | Neutral zone inertia | The largest addressable population is not moving | Change Manager / Champion Network |
| Pe6 | Resistance mismanagement | Wrong intervention applied because the why was never diagnosed | Change Manager |
| Pe7 | Tracking decay and regression ⚠ | The stakeholder map was accurate at initiation and never updated | Change Manager |

---

## Cross-Layer Failure Interactions

This is the integrating logic of the master document. Failure in one layer does not stay in that layer — it propagates. Understanding the propagation paths is what makes this a management system rather than three separate checklists.

### Technology Failures That Activate Process Failures

**T3 → P4 and P5:** Integration and data quality failure creates process execution gaps that clinical staff compensate for with informal workarounds. These workarounds become load-bearing before they are identified. Execution then becomes inconsistent because some staff have the workaround and some do not.

**T4 → P3:** Adoption and workaround failure feeds the hybrid state directly. When staff bypass the system, part of the process lives in the system and part lives outside it — the definitional condition of hybrid state persistence.

**T2 → P3:** Infrastructure failure forces process adaptations in the field. When the system is unavailable, staff develop paper-based alternatives. When the system returns, both the system and the paper alternative are in use simultaneously. The hybrid state is born from an infrastructure event, not a process decision.

**T5 → P6:** Strategic misalignment at the technology level — the system was built without clear process outcome targets — guarantees outcome misalignment at the process level. The two failure modes share the same root cause: neither asked what success looks like before the work began.

### Technology Failures That Activate People Failures

**T1 → Pe2 and Pe5:** Selection failure generates resistance. When clinical staff perceive that a system was chosen without their input or clinical fit assessment, critical nodes that might have been Passive Support become Passive Resistance. The neutral zone watches and waits.

**T4 → Pe5:** Adoption failure is partly a People problem wearing a Technology label. When staff are not using the system as designed, the neutral zone interprets this as a signal that the project is not succeeding. The neutral calculation tips toward inertia.

**T6 → Pe4:** Post-live degradation erodes champion credibility. A champion who advocated for the system and watches it deteriorate post-go-live faces peer pressure from the stakeholders they convinced. Champion fatigue accelerates. The network thins precisely when it is most needed.

### Process Failures That Activate Technology Failures

**P1 → T1 and T4:** Current state misrepresentation feeds directly into selection failure. If the current state was not accurately mapped, the requirements used to select the technology were wrong. The system is then adopted — or not — against a workflow reality it was never configured to match.

**P2 → T4:** Future state design failure creates technology workarounds. If the designed workflow cannot survive real operational conditions, staff will find a path around it. The workaround path usually bypasses the system entirely or uses it partially, producing the adoption and data quality failures that appear to be technology problems.

**P6 → T5:** Process outcome misalignment and technology strategic misalignment are almost always co-occurring. They share the same prevention: outcome KPIs defined before design begins. When neither layer defines success before execution, both fail simultaneously and the root cause is difficult to attribute.

### Process Failures That Activate People Failures

**P3 → Pe5:** Hybrid state persistence is highly visible to the neutral zone — and it is interpreted as project failure. When staff observe that the new process and the old process are both operating simultaneously, their neutral calculation tips toward waiting longer. The hybrid state is the most powerful accelerant of neutral zone inertia.

**P4 → Pe6:** Informal process blindness generates resistance that is frequently mismanaged. When a load-bearing informal process is disrupted without replacement, the staff who depended on it resist — not because they oppose the project but because something they relied on is gone. Treating this resistance as political opposition and escalating it is one of the most common resistance mismanagement failures in Health IT.

**P7 → Pe7:** Process regression and stakeholder tracking decay reinforce each other. As the future state erodes, the behavioral landscape that supported it erodes with it. Champions who sustained the process lose their organizational rationale for advocacy. If tracking decay means this regression goes undetected, both layers are silently returning to their pre-implementation states.

### People Failures That Activate Technology Failures

**Pe2 → T1:** Critical node exposure at the decision level blocks or distorts technology selection. A governance member in Passive Resistance who controls vendor approval does not block the selection — they shape it toward the path of least resistance, which is frequently not the path of best clinical fit.

**Pe5 → T4:** Neutral zone inertia produces adoption failure. A system can be technically sound, properly configured, and adequately trained — and fail to achieve adoption because the People layer never moved the neutral zone to a position where adoption was the natural behavior.

**Pe4 → T6:** Champion deficit accelerates post-live degradation. The champion network is the primary sustaining mechanism for both technology adoption and process compliance. When champions burn out or are unevenly distributed, the monitoring and peer influence that sustains the system post-go-live disappears. Degradation follows.

### People Failures That Activate Process Failures

**Pe3 → P2, P5, P7:** The authority-behavior gap is the single most damaging People failure to the Process layer. A process owner who is nominally accountable but behaviorally absent means process design goes unvalidated, execution inconsistency goes unaddressed, and regression goes unchallenged. Every process domain that requires an owner to exercise authority is vulnerable when that authority is not exercised.

**Pe6 → P7:** Resistance mismanagement entrenches the current state. When the intervention applied to a resistor confirms their belief that the project is not listening, they become more committed to the behaviors that preserve the current state — and they communicate that commitment to the neutral zone around them.

**Pe7 → P7:** Tracking decay in People is the structural cause of most Process regression that goes undetected. When the stakeholder map is not maintained, the process compliance monitoring that depends on People ownership is the first thing to erode. Both layers regress simultaneously, invisibly, along the same timeline.

---

## The Hybrid State as Cross-Layer Organizing Risk

The hybrid state deserves special attention in the master document because it is the only risk condition that operates simultaneously across all three layers.

**In Technology:** The hybrid state exists when some clinical data lives in the system and some lives outside it. Reports are incomplete. Interfaces carry partial information. Clinical decisions are made on data that does not reflect the full patient picture.

**In Process:** The hybrid state exists when some staff are on the new workflow and some are on the old one. Handoffs break at the seam between the two versions. No one has a complete picture of the process.

**In People:** The hybrid state exists when some stakeholders have committed to the new reality and some are still operating in the old one. Champions are advocating for a system that part of the organization is not using. The neutral zone reads this as evidence that the project has not succeeded.

These three hybrid states are not independent — they are the same underlying condition expressed through three different lenses. Compressing the hybrid state is therefore not a Process intervention alone. It requires:

- Technology reliability sufficient that the system is the obvious place for clinical data
- Process authority sufficient to close the old workflow definitively
- People commitment sufficient that the stakeholders responsible for enforcement exercise that authority

No single layer can compress the hybrid state alone. This is the clearest expression of why the framework must be managed as an integrated system.

---

## The Silent Failure Registry

Fourteen of twenty failure domains carry silent failure risk. This concentration is the most important structural characteristic of the framework — and the most commonly underappreciated in Health IT implementation.

| Layer | Silent Failure Domains | % of Layer |
|---|---|---|
| Technology | T1, T3, T4, T5 | 4 of 6 — 67% |
| Process | P1, P3, P4, P5, P6 | 5 of 7 — 71% |
| People | Pe1, Pe2, Pe3, Pe4, Pe7 | 5 of 7 — 71% |
| **Total** | **14 of 20** | **70%** |

### What This Means for Detection

Seventy percent of the failure domains in this framework will not announce themselves. They require deliberate detection mechanisms — human observation, structured assessment, behavioral tracking, and data instrumentation — operating concurrently with project execution.

The frameworks that rely on failure being visible before it is managed are managing thirty percent of the risk. This framework is designed to manage all of it.

---

## Ownership Architecture

The full ownership picture across all three layers, organized by the type of authority required.

### By Authority Type

**Strategic authority** — the ability to redefine scope, reset objectives, and re-engage executive and clinical leadership
- T5 Strategic misalignment · P6 Outcome misalignment · Pe2 Critical node exposure
- *Owner: Project Sponsor / CMIO / Executive Leadership*

**Clinical authority** — the ability to mandate clinical workflow design and practice standards
- T1 Selection · P2 Future state design · P5 Execution inconsistency
- *Owner: CMIO / Clinical Operations Management*

**Operational authority** — the ability to close old processes, enforce cutover, and mandate unit-level practice
- P3 Hybrid state persistence · P7 Process regression · Pe3 Authority-behavior gap
- *Owner: Clinical Operations Lead / Department Heads*

**Technical authority** — the ability to direct system configuration, integration design, and infrastructure decisions
- T2 Infrastructure · T3 Integration & data quality · T6 Post-live degradation
- *Owner: IT Infrastructure Lead / Health Informatics Lead / Integration Architect*

**Discovery authority** — the ability to access front-line staff across all shifts and sites
- P1 Current state misrepresentation · P4 Informal process blindness
- *Owner: Clinical Process Owner + Front-line Staff Representatives*

**Diagnostic skill** — behavioral intelligence that cannot be substituted with organizational position
- Pe1 Stakeholder mapping · Pe6 Resistance mismanagement · Pe7 Tracking decay
- *Owner: Change Manager*

**Network influence** — peer credibility and connectivity that formal authority cannot replicate
- Pe5 Neutral zone inertia
- *Owner: Champion Network*

**Dual ownership** — domains requiring both methodology and authority operating simultaneously
- Pe4 Champion deficit → Change Manager + Executive
- Pe1 Stakeholder mapping → Change Manager + Project Sponsor
- T4 Adoption & workaround → Clinical Informatics Lead + Change Manager

### The Two Non-Negotiable Ownership Pairs

**Change Manager + Project Sponsor** — the central ownership pair of the framework. The Change Manager owns the methodology, the tracking instrument, the diagnostic logic, and the champion network. The Project Sponsor owns the authority interventions — the conversations and decisions that cannot be made below the authority level of the stakeholder they address. When one operates without the other, the People layer has structural gaps that no amount of tactical skill can close.

**CMIO + Clinical Operations** — the clinical ownership pair. The CMIO holds the strategic and clinical authority to mandate workflow design. Clinical Operations holds the operational authority to enforce it in practice. When these two are not aligned, process compliance becomes aspirational rather than managed.

---

## Framework Implementation Guide

How to deploy this framework across the project lifecycle.

### At Project Initiation

**Run the strategic questions across all three layers simultaneously.** Where will this project fail? What will silent failure look like? What are the sociotechnical dynamics? Who needs to decide?

**Build the stakeholder map using the two-axis framework.** Scale position × organizational authority for every stakeholder. Identify critical nodes. Document the why for every stakeholder below Passive Support. This is a hypothesis document — it will be updated continuously.

**Define success for all three layers before design begins.** Not after go-live. Not during configuration. Before the design is locked. The Technology success definition anchors vendor selection. The Process success definition anchors workflow design. The People success definition anchors the engagement strategy.

**Establish the failure control map as the project's primary risk register.** Assign owners to every domain. Define the detection mechanisms that will be active throughout the project. Name the specific indicators that will trigger a response.

### During Design and Configuration

**Activate the Prevention mechanisms across all twenty domains.** The prevent column of each domain is a design checklist. Infrastructure readiness assessment. Multi-method current state discovery. Clinical co-design of the future state. Stakeholder engagement strategy mapped against the two-axis framework.

**Run the current state discovery with the misrepresentation risk explicitly in view.** Who is in the room? Who is not? What version of the process are you being shown? Where is the night shift perspective? Where are the informal processes?

**Begin the stakeholder tracking cadence.** The first baseline assessment is at project initiation. The second is at design completion. The map must reflect the behavioral landscape as it actually exists at each point — not as it was when the project began.

**Assign champion roles deliberately.** Map critical nodes. Identify coverage gaps. Cultivate, do not just recruit.

### During Testing and Pilot

**The pilot is your last chance to detect failure before it goes live.** Choose the most representative pilot site — not the most cooperative one. The hardest-case deployment context is the one most likely to surface the failures that will affect the full rollout.

**Activate detection mechanisms actively, not passively.** Usage analytics. Workflow observation. Workaround identification. Stakeholder position reassessment triggered by pilot experience.

**Treat pilot failure as information, not embarrassment.** A workflow that fails in the pilot has failed cheaply. The same failure at full go-live is expensive. The pilot exists to find the domains where prevent failed — so that respond can be activated before scale.

### At Go-Live

**Concentrate support in the hybrid window.** At-elbow support, stakeholder engagement, and process monitoring should be at their highest intensity in the first two weeks of go-live — not distributed evenly across the implementation timeline. The hybrid window is the highest-risk operational period in the entire project.

**Activate all detection mechanisms simultaneously.** The hybrid state signature. Adoption analytics. Data quality dashboards. Interface monitoring. Stakeholder position assessment — event-triggered by go-live. Champion engagement check.

**Do not declare success at go-live.** Go-live is an event. Success is a sustained condition. The framework does not recognize go-live as a closing milestone.

### Post Go-Live and Sustainment

**Transfer ownership from project to operations before the project closes.** Process ownership formally transferred to Clinical Operations. Technology sustainment formally transferred to IT Operations. People tracking formally retained by the Change Manager and embedded in operational governance.

**Maintain the tracking cadence.** Ninety-day and six-month stakeholder reassessments. Quarterly system health reviews. KPI trending against pre-implementation baseline. The delta between assessments is the signal.

**Monitor for regression across all three layers simultaneously.** Process regression, stakeholder regression, and technology degradation travel together. They share the same root causes — fading leadership attention, staff turnover, organizational pressure — and they require an integrated response, not three separate interventions.

**The framework does not end at go-live.** It transitions from project management to operational governance. The tracking cadence, the champion network, and the resistance management capability become standing elements of how the organization manages the sustained future state — not artifacts of a closed project.

---

## The Failure Stratification Logic

Not all failures are equal. Every domain in this framework must be assessed against two stratification dimensions before a response is designed.

**Recoverability** — can this failure be corrected, or has it crossed into irreversible territory?

| Recoverable | Potentially Irreversible |
|---|---|
| Workflow that needs redesign | Patient safety event from bad data |
| Training gap identified post-go-live | Data breach |
| Configuration error corrected in sprint | Go-live collapse that destroys clinician trust |
| Champion burnout addressed before withdrawal | Permanent hybrid state normalized over 6+ months |
| Neutral zone inertia corrected before go-live | Active resistance that has successfully diverted funding |

**Velocity** — how quickly is this failure compounding? A static failure that has not worsened is a different management priority than a failure that is actively spreading.

The combination of recoverability and velocity produces the response urgency. High-velocity, low-recoverability failures require same-day response. Low-velocity, high-recoverability failures can be managed in the next planned cadence cycle.

---

## Using This Framework as a Diagnostic

The framework can be used in two modes: **prospective** — at the beginning of a project to design the management system — and **diagnostic** — mid-project or post-failure to identify where the breakdown occurred.

### Diagnostic Entry Points

**If the go-live failed visibly:** Start with Technology (T2, T3) and Process (P3) — these are the most common sources of visible go-live failure.

**If adoption is low but the system is working:** Start with Technology (T4) and People (Pe5) — adoption failure is almost always a People-Technology intersection problem.

**If outcomes are not improving despite adoption:** Start with Process (P6) and Technology (T5) — strategic misalignment and outcome misalignment are co-occurring failures with a shared root cause.

**If the future state is eroding post-go-live:** Start with Process (P7) and People (Pe7) — regression in both layers travels together and requires an integrated response.

**If resistance is intensifying:** Start with People (Pe6) — resistance mismanagement is the most common accelerant of resistance. Then trace back to the why: is the resistance responding to a Technology failure (T1, T4), a Process failure (P4), or a genuine People positioning problem (Pe2)?

**If the project feels stuck but nothing is visibly wrong:** This is the silent failure signature. Run the full detection inventory across all twenty domains. The failure is present — it has not announced itself yet.

---

## Framework Maintenance

This framework is a living management instrument, not a project document.

**It must be updated when:**
- Any stakeholder changes position on the scale — upward or downward
- A detection mechanism surfaces a new signal
- A response action is taken and produces a result — positive or negative
- A new failure domain is identified that is not in the current map
- The organizational context changes significantly — leadership change, merger, policy shift, funding change

**It must be reviewed at:**
- Every major project milestone
- Every significant organizational event in the participating departments
- Ninety days post-go-live
- Six months post-go-live
- Annually as a standing operational governance review

**It is owned by:**
The Change Manager for the tracking and maintenance discipline. The Project Sponsor for the authority-level decisions that arise from it. Clinical and operational leadership for the domain-specific responses within their ownership.

---

## Summary: What This Framework Does That Others Do Not

| Conventional Change Management | This Framework |
|---|---|
| Optimistic by design — assumes buy-in is achievable | Failure as the default — success is engineered against it |
| Layered on top of project delivery | Concurrent with project execution from day one |
| Manages visible failure | Designed specifically for silent failure |
| People as one workstream among many | People as the behavioral substrate of all other layers |
| Go-live as the finish line | Go-live as one event in a sustained management arc |
| Ownership distributed by workstream | Ownership mapped by authority type required |
| Static stakeholder register | Dynamic two-axis tracking with defined cadence |
| Resistance as a problem to overcome | Resistance as a diagnostic signal requiring why analysis |
| Champions as a discovery | Champions as a deliberately cultivated and distributed asset |
| Success defined at deployment | Success defined as a sustained condition beyond go-live |

---

*Health IT Change Management Framework — Master Document*
*Companion documents: Technology Layer · Process Layer · People Layer*
*Total failure domains: 20 across three layers · Silent failure domains: 14 of 20 (70%)*
