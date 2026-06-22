# Health IT Change Management Framework
## Process Layer

---

## Overarching Principle

**Change management is the disciplined management of failure within People, Process, and Technology — concurrent with project execution — in order to protect the integrity of the transition from current to future state.**

*For the full articulation of the overarching principle, including key clarifications, strategic questions, and the PPT overlay, refer to the Technology Layer document.*

---

## The Process Layer

> *Process is the Execution System. What you are managing: the transition from what is to what it should be — from current state to future state.*

**Character of this layer:** The easiest to underestimate.

---

## Why Process Is Easy to Underestimate

Process feels manageable because it appears visible. You can draw it. You can put it in a swimlane diagram, walk stakeholders through it, get heads nodding in a conference room. That visibility creates a dangerous illusion of control — because what you draw is almost never what actually happens.

### The Two Versions of Every Clinical Process

Every clinical process has two versions operating simultaneously:

**The documented process** — what the policy says, what the flowchart shows, what leadership believes is happening.

**The enacted process** — what clinical staff actually do, shift by shift, under real time pressure, with real constraints.

The gap between those two things is where your implementation will live. In most Health IT projects, that gap is never honestly measured before the future state is designed. You are building the future on a misrepresentation of the present.

### Why Process Change Is Structurally Difficult

**Informal processes are load-bearing.** Every clinical environment runs on a layer of undocumented workarounds, informal handoffs, and tacit agreements between staff that have evolved over years to compensate for system and process deficiencies. These are invisible to any process mapping exercise conducted in a boardroom. But they are often what is actually holding care delivery together. When you implement new technology or redesign a workflow, you disrupt these informal processes — and if you have not identified them, you have no plan to replace them.

**Process change is not instantaneous.** Technology has a go-live date. Process change does not happen on a date — it happens gradually, incompletely, and frequently in reverse. This asymmetry is where most implementations fracture. The system is live, but the process is still half in the old state. That hybrid is not a transition phase — it is the highest-risk operational state in the entire project lifecycle.

**Technology implementations force process decisions that were never made.** When you configure an EMR, you are implicitly making hundreds of process decisions — who documents what, when, in what sequence, with what handoffs, escalated to whom. Most of these decisions are made during configuration by an IT analyst, not during design by a clinical process owner. By go-live, the process has been decided. The organization just does not know it yet.

**Process change requires behavior change — which means Process always bleeds into People.** Every process has a human executing it. The moment you redesign a workflow, you are asking someone to change a habit, relinquish a workaround, or accept a new accountability. Failure that looks like a process problem often has its root cause in People.

### The Critical Trap in Framing Process Change

The phrase *"current to future state"* implies two fixed points and a defined gap between them. In practice:

- **Current state** is almost always misrepresented. What you document in a process mapping workshop is the sanitized, consensus version — not the messy, variable, workaround-laden reality of how care is actually delivered.
- **Future state** is almost always over-engineered. It assumes best-case execution, adequate training, staff compliance, and technical reliability — none of which are guaranteed.

The real work of Process change management is honestly characterizing Point A, designing a Point B that can survive contact with clinical reality, and managing the transition between them without leaving any process in a permanent hybrid state.

### The Hybrid State

> **The hybrid state is the most dangerous operational condition in any Health IT implementation.**

It is the period where some staff are on the new process, some are on the old, some documentation is in the system, some is on paper, and no one has a complete picture of the patient. This is when errors happen. This is when data is missing. This is when care coordination breaks down.

You cannot eliminate the hybrid state. The goal is to compress it as aggressively as possible — not as a phase to be managed, but as a risk condition to be actively closed.

---

## Process Success Definition

> *The future state workflow is executing as designed — by actual clinical staff, in real operational conditions — with load-bearing informal processes consciously addressed, the hybrid state compressed to its minimum viable duration, measurable improvement in defined process outcomes achieved, and no regression to the current state.*

Each element is load-bearing.

---

**"Executing as designed"**

The process on paper matches the process in practice. Not approximately. Not by the champions and super-users. By the full population of staff the process was designed for, across shifts, across sites, under normal and abnormal operating conditions.

This criterion immediately exposes the gap between process design and process reality — and forces you to close it before you call something successful.

---

**"By actual clinical staff"**

Success requires the *median* performer — not the champion — to execute the workflow correctly as a matter of routine. Processes that only work when executed by trained super-users, or by staff in ideal conditions, have not succeeded. They have been demonstrated.

This is one of the hardest criteria to accept organizationally, because leadership often measures adoption by looking at the best-performing units. The worst-performing unit is where your process failure lives.

---

**"In real operational conditions"**

Not in a training environment. Not in a well-staffed morning shift. In the conditions where clinical care actually happens — short-staffed night shifts, emergency surges, system slowdowns, competing priorities. If the process only works when conditions are favorable, it is fragile by design.

This criterion demands that process design be stress-tested against worst-case operational scenarios, not just optimal ones. In Health IT, this is almost never done.

---

**"Load-bearing informal processes consciously addressed"**

This is the criterion most programs miss entirely. Every clinical environment runs on undocumented workarounds and tacit handoffs that have evolved over years. These are not inefficiencies to be eliminated — they are often compensating mechanisms for legitimate gaps in the formal process.

Success requires that you have found them, understood them, and made a conscious decision about each one: formalize it, preserve it deliberately, or retire it because the new process genuinely makes it obsolete. An informal process you did not find is a ticking clock. It will resurface as a workaround, a data gap, or a care coordination failure — usually within the first ninety days post-go-live.

---

**"Hybrid state compressed to its minimum viable duration"**

You cannot eliminate the hybrid state. Accepting that lets you stop trying to prevent it and start managing it. This criterion acknowledges the hybrid exists — and demands it be as short and as controlled as possible.

Compressing the hybrid state requires: a cutover strategy that is definitive; clear rules for what documentation lives where during the transition; and active monitoring for the signature of hybrid state persistence — split documentation, inconsistent handoffs, parallel paper and digital records.

The hybrid state is not a phase. It is a risk condition that must be actively closed.

---

**"Measurable improvement in defined process outcomes"**

Success is not about executing the workflow — it is about what the workflow produces. Defined outcomes must be established before the future state is designed, so you can measure improvement against a pre-implementation baseline.

This criterion also catches the most insidious Process failure mode: a workflow that is perfectly executed and produces no improvement. That is a design failure, not an execution failure — and it requires a different response.

---

**"No regression to the current state"**

Process change is reversible. Under pressure — new leadership, system downtime, staff turnover, competing priorities — clinical staff revert to familiar behaviors. Regression is not dramatic. It is incremental. A form goes back to paper. A handoff reverts to verbal. A documentation step gets skipped. The cumulative effect is the quiet restoration of the current state inside the shell of the future state.

---

## The Structural Shape of Process Success

The success definition frames the entire process transition as a compression problem:

1. You begin with a **misrepresented current state** — more complex, more informal, more variable than documented
2. You design a **future state** that must survive contact with clinical reality
3. You manage a **hybrid state** that is your highest-risk operational window
4. You reach **sustainable future state execution** when all criteria are met

The failure control map is organized around where that compression breaks down.

---

## Process Failure Control Map

Seven failure domains, derived from inverting the process success definition.

> ⚠ indicates **high silent failure risk** — failure is most likely to be invisible before it becomes irreversible or normalized.

---

### Domain 1 · Current State Misrepresentation
**Failure mode:** The future state was designed against a false picture of how work is actually done today

#### Top Risks
- Workshop capture bias — staff describe the ideal, policy-compliant version of their process, not the enacted one
- Single-source discovery — managers and senior clinicians interviewed, but not the front-line staff closest to the informal process
- Site homogeneity assumption — one site documented and treated as representative across a multi-site deployment
- Cross-shift blindness — discovery conducted during day shifts; night shift behavior, which is often more revealing, never observed

#### Prevent
- Multi-method discovery: direct observation and shadowing alongside interviews — not instead of them
- Explicit discovery questions designed to surface the enacted process: *"What do you do when the formal process does not work? What would break if you were not here?"*
- Cross-shift observation included as a formal discovery requirement
- Front-line staff represented directly in discovery — not only through clinical leadership
- Multi-site sampling where the deployment spans more than one facility

#### Detect
- Configuration resistance — when the system cannot accommodate the documented workflow, the documentation was likely wrong, not the system
- Staff pushback during training that is specific: *"That is not how we actually do it"* — a current state re-mapping signal, not a training compliance problem
- Process gaps surfacing during pilot that were not in the current state documentation

#### Respond
- Current state re-mapping based on findings, conducted before future state design is locked
- Future state design revisited if the gap between documented and enacted process is significant
- Delay configuration lock until current state accuracy is validated — expensive in the short term, far less expensive than a go-live built on a false foundation

**Owner:** Clinical Process Owner / Business Analyst
*Annotation: This role requires access, not just authority. If the discovery team cannot reach front-line staff across shifts and sites, the current state will be misrepresented regardless of methodology.*

---

### Domain 2 · Future State Design Failure
**Failure mode:** The designed workflow cannot survive real operational conditions

#### Top Risks
- Designed for ideal conditions — full staffing, working technology, no competing priorities; fails under the operational baseline of most clinical environments
- Step count inflation — technology implementations almost always add steps before removing them; when net step count rises, workarounds follow
- Validated by champions only — future state approved by early adopters and leadership, never tested against a new staff member, a surge shift, or a system outage
- Designed without a downtime procedure — the future state has no defined behavior when the technology it depends on is unavailable

#### Prevent
- Clinical co-design, not just clinical validation — designing *with* clinicians, not presenting *to* them for sign-off
- Explicit stress-testing of the future state against worst-case operational scenarios before configuration is locked: short-staffed shifts, surge volumes, system downtime
- Step count comparison: future state vs. current state — every net addition must be justified
- Downtime procedure defined from the start as a first-class design output, not an afterthought

#### Detect
- Training resistance that is specific and operational: *"This will never work on a night shift"* — a design signal, not a training problem
- Pilot failures at representative sites — most valuable when the pilot site is the hardest deployment context, not the most cooperative one
- Super-users generating workarounds during training — if the people most motivated to make the system work are already finding ways around it, the design has a problem

#### Respond
- Workflow redesign before full go-live — uncomfortable and recoverable
- Scope reduction: implement a simpler version of the future state first; add complexity once the core workflow is stable
- Reconfiguration where the system can accommodate a simplified workflow design

**Owner:** Clinical Informatics Lead / CMIO
*Annotation: Requires clinical authority. A project manager cannot mandate a clinical workflow redesign. This is a structural reality most projects resist until they cannot.*

---

### Domain 3 · Hybrid State Persistence
**Failure mode:** The transition period extends, becomes normalized, and becomes the operational reality

#### Top Risks
- No cutover strategy — the implementation drifts from old to new without a defined transition point; documentation splits; no one has a complete picture of the patient
- Ambiguous transition rules — staff do not know which process to follow when; when uncertain, they follow what is comfortable, usually the old process
- Gradual rollout without phase endpoints — phased implementations without explicit closure dates allow each phase to persist indefinitely
- Hybrid normalization — after thirty days, the hybrid is no longer perceived as a transition state but as the new operational normal; at that point it is no longer a transition, it is a failed implementation

#### Prevent
- Definitive cutover strategy: big bang where feasible; phased only where operationally necessary — with explicit phase endpoint dates and a formal protocol for closing the old process at each boundary
- Unambiguous, published rules for what documentation lives where during the transition period — communicated, not assumed
- At-elbow support concentrated in the hybrid window — not distributed evenly across the implementation timeline; the hybrid window has the highest return on support investment
- Leadership visibility sustained through the transition — the hybrid normalizes fastest when executive attention withdraws

#### Detect
The signature of hybrid state persistence is specific and observable:
- Split documentation: some entries in the system, some on paper for the same clinical events
- Inconsistent handoffs between shifts using different process versions
- Data gaps in system reports corresponding to activities still documented on paper
- Staff reporting confusion about which process to follow

These signals must be actively looked for — not waited for. Daily review in the first two weeks of go-live; weekly for the first ninety days.

#### Respond
- Immediate escalation protocol when hybrid persistence is detected — additional at-elbow support deployed within hours, not days
- Formal recovery plan if the hybrid is widespread and entrenched — which may include a temporary, controlled rollback to the current state with a defined re-launch date
- A controlled rollback is recoverable. A normalized hybrid is not.

**Owner:** Project Manager / Clinical Operations Lead
*Annotation: Requires operational authority — the ability to actually close the old process: remove the paper forms, retire the old system access, mandate the cutover. This authority must come from operational leadership, not the project team.*

---

### Domain 4 · Informal Process Blindness
**Failure mode:** Load-bearing undocumented processes were never found; the future state has no equivalent; care coordination breaks in ways nobody predicted

#### Top Risks
- Discovery limited to formal channels — workshops, policy documents, existing SOPs capture only the formal process; the enacted informal process is invisible
- Informal processes framed as compliance failures — identified workarounds treated as problems to eliminate rather than compensating mechanisms to understand; eliminating the symptom without addressing the underlying gap
- Single-shift, single-site discovery — informal processes are often shift-specific and site-specific; a daytime workshop at the main campus cannot see the informal handoff mechanism operating on the night shift at the rural facility
- No disposition inventory — informal processes discovered but no formal decision made about each one; they are noted and then quietly dropped from the design

#### Prevent
- Structured informal process discovery as a formal project workstream — not an add-on to formal process mapping
- The primary discovery question: *"What would break if you were not here?"* — surfaces load-bearing informal processes faster than any other prompt
- Cross-shift and cross-site observation by the discovery team
- Explicit informal process inventory with a documented disposition decision for each: formalize it, preserve it deliberately, or retire it with a stated rationale
- Longest-serving front-line staff included directly in discovery — they are the primary carriers of informal process knowledge

#### Detect
- Post-go-live workarounds emerging rapidly and in clusters — new informal processes appearing within thirty days are almost always reconstructions of informal processes that were disrupted and never replaced; they are a map of what was missed
- Care gaps in areas where informal handoffs previously existed
- Staff describing a vague sense that *"something is missing"* without being able to articulate what — the characteristic signature of a disrupted informal process

#### Respond
- Informal process archaeology: use post-go-live workarounds as a guide to find what was missed
- Formal process amendment to incorporate load-bearing informal steps that were overlooked
- Interim manual bridge while the formal fix is designed and configured
- No workaround should be shut down without first understanding what it is compensating for

**Owner:** Clinical Process Owner + Front-line Staff Representatives
*Annotation: This is the strongest ownership annotation in the entire Process map. This domain cannot be owned by IT or project management. The informal process lives with the people who execute it. Discovery and response require their direct involvement — not their representation by a clinical lead who may not share their operational reality.*

---

### Domain 5 · Execution Inconsistency
**Failure mode:** The workflow executes correctly for champions and early adopters; it does not execute correctly for the median performer under operational pressure

#### Top Risks
- Training designed for the ideal user — full functionality, motivated learner, adequate time; the median clinical user is none of these things on a busy shift
- Measurement by best-performing unit — compliance reported as an average, masking the performance of the lowest-performing sites and shifts where failure actually lives
- Support concentrated at flagship sites and primary shifts — the rural facility, the night shift, the float staff have the highest execution inconsistency and the lowest support
- Process complexity exceeding sustainable execution — if a workflow requires the user to hold more than a few steps in working memory under interruption, it will be executed inconsistently; this is a design failure, not a training problem

#### Prevent
- Train for the median performer, not the expert user — design training around the most cognitively demanding scenarios, not the routine ones
- Role clarity documented at execution-level granularity — who does what, when, in what sequence — removing ambiguity at the point of care
- At-elbow support planned and resourced for all sites and all shifts, not concentrated at flagship locations
- Process simplification as a design principle: if a step generates consistent failures, the step is wrong, not the user

#### Detect
- Performance variation across sites and shifts — if unit A executes at 90% compliance and unit B at 40%, that is an execution environment problem, not a training problem; investigate, do not average
- Data quality variation by shift and site — documentation completeness at 3am is a reliable proxy for process execution under operational pressure
- Support ticket pattern variation — the same errors appearing consistently at specific sites or shifts signals a local execution environment issue

#### Respond
- Targeted support to underperforming units and shifts — not broadcast retraining
- Peer learning: what is the high-performing unit doing that the low-performing unit is not? Structured knowledge transfer between them
- Process simplification for steps generating consistent failures across multiple sites
- Role clarity intervention where ambiguity, not capability, is the root cause

**Owner:** Clinical Operations Manager / Unit Leads
*Annotation: Front-line operational ownership. The project team can identify and diagnose execution inconsistency, but closing it requires the authority of operational leadership to mandate practice standards at the unit level.*

---

### Domain 6 · Outcome Misalignment
**Failure mode:** The workflow executes correctly and produces no measurable improvement in the outcomes it was designed to change

#### Top Risks
- Outcomes defined after go-live — KPIs invented to describe what happened, not to measure what was intended; improvement cannot be measured without a baseline that was established before the implementation
- Symptom addressed instead of root cause — the process was redesigned at the wrong level; the documentation workflow was improved, but the care coordination failure it was supposed to solve was caused by something upstream the redesign did not touch
- Scope too narrow to move outcomes — the process change was real but insufficient; improving referral documentation does not reduce referral turnaround time if the bottleneck is receiving facility capacity, not documentation quality
- Wrong metrics selected — activity metrics (volumes, completion rates, timestamps) measured instead of outcome metrics; activity can be high while outcomes are unchanged

#### Prevent
- Process outcome KPIs defined before the future state is designed — not after go-live, not during go-live; before the design is locked
- Root cause analysis of current state problems before designing solutions — if you do not understand why the current state produces the outcomes it does, you cannot be confident a redesigned process will produce different ones
- Baseline measurement established before go-live — without a baseline there is no reference point
- Scope validation: is this process change capable of moving the metrics it is accountable for, or is the real lever somewhere else?

#### Detect
- KPI tracking from day one of go-live against the pre-implementation baseline
- Clinical outcome monitoring tied to the specific workflows the implementation was designed to support
- The critical detection signal: flat or worsening outcome metrics against healthy execution compliance — this means the design, not the execution, is the problem

#### Respond
- Root cause re-analysis when outcomes do not move despite compliant execution
- Scope expansion if the process change was too narrow to reach the real lever
- Process redesign at a higher level if a symptom was addressed instead of a root cause
- Governance escalation when outcome misalignment is fundamental — this is a strategic decision, not a project decision

**Owner:** CMIO / Project Sponsor
*Annotation: The highest-authority ownership in the entire Process map. Outcome misalignment cannot be resolved at the project level. It requires authority to redesign scope, redefine measures, and re-engage clinical and executive leadership.*

---

### Domain 7 · Regression
**Failure mode:** The future state erodes back toward the current state under operational pressure, staff turnover, and fading leadership attention

#### Top Risks
- Process ownership not transferred from project to operations — compliance monitoring disappears when the project closes; no one in operations picked it up
- Leadership attention withdrawn — the executive sponsor moves on; the clinical champion is absorbed by other priorities; the visible accountability that drove go-live compliance disappears
- Staff turnover removing trained practitioners — new staff oriented by untrained peers, not by the formal process; within six months, informal processes have reconstituted around the gaps
- Operational pressure normalizing regression — the first regression is excused as a crisis response; the second is normalized; the third is invisible

#### Prevent
- Sustainment plan defined and resourced before go-live — not after project closure
- Process compliance built into operational management structures: embedded in performance conversations, unit dashboards, and departmental governance — not managed by the project team
- Process ownership formally transferred from project to an identified operational owner with explicit accountability before the project closes
- New staff orientation includes the process, not just the system

#### Detect
- Process compliance monitoring on a defined schedule — not continuous, but not abandoned; quarterly minimum for twelve months post-go-live
- KPI trending over six to twelve months: a downward trend after an initial improvement is a regression signal
- Direct observation at defined intervals post-go-live
- The subtlest and most reliable signal: staff describing the process in the past tense — *"We used to document it this way"*

#### Respond
- Rapid response to early regression signals — the earlier the intervention, the lower the cost
- Targeted retraining before regression becomes normalized behavior
- Process communication refresh to re-establish visibility and accountability
- If regression is widespread: a formal re-implementation plan with a new go-live structure — treated as a project, not an operational correction

**Owner:** Clinical Operations Management / Department Heads
*Annotation: Sustainment ownership cannot live with the project team because the project ends. It must be embedded in operational management from the beginning, formally transferred before project closure, not handed over as an afterthought.*

---

## Key Cross-Cutting Observations

### The Ownership Pattern

Every domain in the Process map requires a different type of authority. No single person holds all of them. This is the ownership challenge in Process that does not exist in the same way in Technology.

| Domain | Authority Type Required |
|---|---|
| Current state misrepresentation | Discovery authority — access to front-line staff across all shifts and sites |
| Future state design failure | Clinical authority — to mandate workflow redesign |
| Hybrid state persistence | Operational authority — to close old processes and enforce cutover |
| Informal process blindness | Front-line authority — direct staff involvement, not representation |
| Execution inconsistency | Operational authority — to mandate practice standards at unit level |
| Outcome misalignment | Strategic authority — to redesign scope and redefine measures |
| Regression | Sustainment authority — embedded in operational management structure |

The Process Failure Control Map must be supported by a governance structure that can activate each type of authority at the right moment. Without that structure, the map is a document, not a management tool.

### The Silent Failure Concentration

Five of seven Process domains carry silent failure risk. Process failure is structurally more silent than Technology failure — because process behavior is less instrumented, less monitored, and less visible than system behavior. A dropped interface message can be detected by a monitoring alert. A reconstituted informal process or a gradually normalizing hybrid state has no equivalent alert. Detection in the Process layer requires deliberate human observation, not just system instrumentation.

### Process and People Are Inseparable

Every Process domain has a human dimension. Current state misrepresentation is partly a function of who is in the room. Future state design failure is partly a function of whether the right voices shaped the design. Execution inconsistency is partly a function of whether staff trust the process enough to follow it. Regression is partly a function of whether leaders are still attending to it.

The Process layer does not end cleanly at the People layer's edge the way Technology does. They interpenetrate. The People Failure Control Map will address the behavioral dimensions of what begins as a process problem — and the Process map must be read with that continuity in mind.

### The Hybrid State Is the Organizing Risk

Every domain in this map either contributes to the hybrid state or is worsened by it. Misrepresented current state makes the hybrid harder to design out of. Future state design failure makes the hybrid longer to compress. Informal process blindness means the hybrid contains invisible gaps. Execution inconsistency keeps parts of the process in permanent hybrid. Outcome misalignment often originates in hybrid-state data that was never clean.

If there is a single risk to manage aggressively in the Process layer, it is the hybrid state. Everything else either feeds it or flows from it.

---

*Document status: Technology layer complete. Process layer complete. People layer to follow.*
