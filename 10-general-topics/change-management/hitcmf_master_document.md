# Failure-Aware Change Management Framework

## Master Document

------

## I. Purpose of This Document

This is the master reference for the Failure-Aware Change Management Framework. It sits above the three layer documents — People, Process, and Technology — and provides what those documents cannot provide individually: the unified view.

Three things belong here that do not belong in any single layer document.

The first is consolidation. Each layer document describes how its failures propagate into the other two layers. This document maps that propagation from above — showing how failure moves across the full system, not just from one layer's perspective.

The second is unified accountability. The layer documents assign responsibility at the domain level. This document consolidates those assignments into a single governance structure so that the accountability picture can be seen whole — and the gaps in it made visible.

The third is the project lifecycle overlay. The layer documents describe what to manage. This document describes *when* — how the framework applies across the phases of a project, from the first planning meeting through post-go-live sustainment.

------

## II. What This Framework Is

This framework emerged from field experience — from pattern recognition accumulated across Health IT implementations that succeed, fail, and fail quietly enough that no one agrees they have failed at all.

It makes no claims to universality. It does not attempt to replace established methodologies. What it offers is more specific: a structured way of thinking about change management grounded in what actually happens inside hospitals during a digital transformation.

The central premise is a deliberate departure from how most practitioners frame the discipline. Traditional frameworks are optimistic by design — they assume adoption is achievable and resistance is an exception to be managed. This one starts from the opposite assumption.

**Failure is the default. Success is what you engineer against it.**

------

## III. Core Statement

> Change management is the disciplined management of failure across People, Process, and Technology — concurrent with project execution — to protect the integrity of the transition from the current to the future state.

Everything in this framework derives from the Core Statement. The three layers are its operational surfaces. The Governing Principles are its behavioral commitments. The failure control maps are its implementation logic.

------

## IV. Governing Principles

Five commitments derived from the Core Statement. They govern how the framework is applied — not what it covers, but how it is used.

------

**01 — Start on day one, not day done**

Change management runs alongside the project from the beginning — not added after delivery. It is an organizational commitment, not a project phase.

The implication is structural. If change management is introduced after the system has been configured, the process has been documented, or the go-live date has been set, its most important opportunities have already passed. System configuration embeds process decisions. Go-live dates create pressure that forecloses options. The framework must be active before those things happen — not called in to manage their consequences.

------

**02 — Engineer for failure, not for optimism**

Failure is the default state. Success is what you deliberately build against it. Traditional frameworks assume adoption will happen — this one assumes it will not unless you design for it.

The implication is diagnostic. Every element of the project plan — every assumption, every dependency, every timeline — must be examined for where it could break, not where it is expected to hold. The failure control maps in the layer documents are the operational expression of this principle.

------

**03 — What you can't see will hurt you most**

Projects in Health IT rarely fail loudly. They fail quietly — systems go live while staff find workarounds, metrics look acceptable while clinical behavior has not changed, stakeholders appear supportive in governance meetings and slow-roll in operations.

The implication is methodological. Detection — the active, disciplined search for early failure signals — is as important as prevention. Silent failure must be designed against as deliberately as visible failure. Fourteen of the twenty failure domains across the three layers carry an explicit silent failure risk designation. They do not generate alerts. They require human observation, maintained instruments, and a detection cadence that does not collapse under project pressure.

------

**04 — If everyone owns it, no one owns it**

The gap between who is responsible for technology, process, and people is structural — not accidental. Unified accountability for the transition as a whole must be deliberately assigned, not assumed.

The implication is governance. Ownership fragmented across a CIO, a project manager, and a change manager — with no single point of accountability for the transition as a whole — produces a project where each person manages their lane and no one manages the intersections. The intersections are where the most consequential failures occur. Section VII of this document addresses this directly.

------

**05 — Not all failure costs the same**

A training gap is recoverable. A go-live collapse that destroys clinician trust for years is not. A patient safety event is irreversible. The response must match the consequence — stratification is built in from the start.

The implication is operational. Response protocols cannot be generic. The trigger, the escalation path, and the recovery target must be calibrated to the severity of the failure being addressed. For the highest-consequence failures — particularly those involving patient safety — the response protocol must specify containment as the first step, before diagnosis, before escalation, before anything else.

------

## V. The PPT Lens

The framework diagnoses and manages change through three lenses. PPT stands for People, Process, and Technology. They are lenses, not workstreams. A workstream can be handed to a team and managed in isolation. A lens cannot — because what you see through one changes what you see through the others.

------

### People — the Behavior System

**What you are managing:** stakeholder behavior from engagement through adoption — continuously, not episodically.

People is the most challenging layer. Technology failure is visible. Process failure is observable. People failure is neither — it accumulates in silence. A stakeholder who was supportive at the start of a project and has drifted to disengaged by month four generates no alert. A champion who is burning out withdraws from advocacy before anyone notices. A senior leader who approves everything in governance and slow-rolls everything in operation is, by every formal measure, a supporter — and by every operational measure, an obstacle.

People is also the layer with the longest tail. Technology has a go-live date. Process has a future state target. People has neither. Behavioral commitment, champion engagement, and neutral zone movement do not resolve on a project timeline. The People Layer must be managed actively after go-live — not wound down with the project structure.

People is not a third equal alongside Process and Technology. It is the behavioral substrate on which the other two operate. A technically sound system deployed into a field of passive resistance will fail. A well-designed process owned by nominal champions will regress.

------

### Process — the Execution System

**What you are managing:** the transition from current state to future state — from what is, to what it should be.

Process is the most consistently underestimated layer. It feels manageable because you can see it — you can map it, diagram it, present it in a meeting. That visible, tidy picture creates a false sense of control, because what gets drawn in a planning room is almost never what actually happens on the floor.

The defining risk of the Process Layer is the transition period — the interval where old and new processes run simultaneously, where data is split between the old way and the new system, and where no one has a complete picture of what is happening with any given patient. This is when mistakes happen. When information gets lost. When care coordination breaks down.

The most dangerous thing that can happen to a transition period is that it stops feeling like one. Within approximately thirty days of launch, if the old and new processes are still running side by side, staff stop perceiving the situation as temporary. It becomes the new normal — and a normalised transition period is not a phase. It is a failed implementation.

------

### Technology — the Enablement System

**What you are managing:** whether the tools you deploy actually get used — and whether they help people do their jobs better.

Technology is the most visible layer and the most frequently overestimated as a solution in isolation. You can point to it. It has a name, a vendor, a launch date, and a price tag. That visibility is both its greatest strength and its most common source of overconfidence.

The most dangerous assumption in Health IT is defining technology success as going live. Launch day is a milestone, not an outcome. Organisations that declare victory on launch day stop actively managing the technology at precisely the moment when the most significant risks begin to emerge.

The true measure of technology success is adoption — whether staff across the organisation are consistently using the system to do the work it was designed to support. And adoption is not purely a people and behaviour problem. How easy the system is to use, how well it fits into existing work patterns, and how much it adds to rather than reduces the burden on staff are technology design decisions. They determine whether adoption is even possible.

------

## VI. The Complete Failure Domain Map

Twenty failure domains across the three layers. Each domain has a full treatment — definition, causes, prevention, detection, response, and ownership — in its respective layer document. This section provides the consolidated view.

> ⚠ indicates **high risk of silent failure** — failure that can build to serious consequence without generating an obvious warning signal. Fourteen of twenty domains carry this risk.

------

### People Layer — Seven Domains

| Domain                              | What Fails                                                   | ⚠    | Owner                                  |
| ----------------------------------- | ------------------------------------------------------------ | ---- | -------------------------------------- |
| Pe1 · Stakeholder Mapping Failure   | The project team does not know who is where, why, or what authority they hold | ⚠    | Change Manager + Project Sponsor       |
| Pe2 · Critical Node Exposure        | A decision, resource, influence, workflow, or network node sits below Passive Support with no active management of that gap | ⚠    | Project Sponsor / Executive Leadership |
| Pe3 · Authority-Behavior Gap        | A stakeholder holds formal authority but does not exercise it | ⚠    | Project Sponsor                        |
| Pe4 · Champion Deficit              | The champion network does not cover the organisational terrain it needs to cover | ⚠    | Change Manager + Executive             |
| Pe5 · Neutral Zone Inertia          | The largest addressable population is not moving toward support |      | Champion Network                       |
| Pe6 · Resistance Mismanagement      | Resistance is handled incorrectly — escalated when it should be engaged, or generalised when it requires individual diagnosis |      | Change Manager                         |
| Pe7 · Tracking Decay and Regression | The stakeholder map was accurate at initiation and never updated; the behavioral landscape shifted unseen | ⚠    | Change Manager                         |

------

### Process Layer — Seven Domains

| Domain                                               | What Fails                                                   | ⚠    | Owner                                             |
| ---------------------------------------------------- | ------------------------------------------------------------ | ---- | ------------------------------------------------- |
| P1 · Getting the Current Picture Wrong               | New process designed against a false picture of how things actually work today | ⚠    | Clinical Process Owner / Business Analyst         |
| P2 · Designing a Process That Cannot Survive Reality | Workflow works in planning but falls apart in real operating conditions |      | Clinical Informatics Lead / CMIO                  |
| P3 · Transition Period Becoming Permanent            | Old and new processes run simultaneously until the hybrid becomes the new normal | ⚠    | Project Manager / Clinical Operations Lead        |
| P4 · Missing the Unofficial Workarounds              | Informal practices disrupted by the new process with no plan to replace them | ⚠    | Clinical Process Owner + Front-line Staff         |
| P5 · Process Working for Champions, Not for Everyone | Inconsistent execution hidden below headline compliance numbers | ⚠    | Clinical Operations Manager / Unit Leads          |
| P6 · Process Works but Produces No Improvement       | Compliance achieved; outcomes flat                           | ⚠    | CMIO / Project Sponsor                            |
| P7 · Sliding Back to the Old Way of Working          | Future state erodes under pressure after the project closes  |      | Clinical Operations Management / Department Heads |

------

### Technology Layer — Six Domains

| Domain                                    | What Fails                                                   | ⚠    | Owner                                          |
| ----------------------------------------- | ------------------------------------------------------------ | ---- | ---------------------------------------------- |
| T1 · Wrong System Selected                | The system chosen does not fit how staff actually work       | ⚠    | CIO / Chief Medical Officer                    |
| T2 · Access and Infrastructure            | Staff cannot reliably access the system in their actual work environment |      | IT Infrastructure Lead / Site Operations Lead  |
| T3 · Data Accuracy and System Connections | The system produces unreliable or incorrect information      | ⚠    | Health Informatics Lead                        |
| T4 · Staff Not Using the System           | Staff avoid the system and find other ways to get the work done | ⚠    | Clinical Informatics Lead / Change Manager     |
| T5 · System Does Not Improve the Work     | The system is running but the expected improvements are not happening | ⚠    | Chief Medical Officer / Project Sponsor        |
| T6 · Decline After Launch                 | The system works at launch and gradually deteriorates        |      | IT Operations Lead / Clinical Informatics Lead |

------

## VII. Cross-Layer Failure Propagation

Failure does not respect layer boundaries. A problem that originates in one layer almost always creates downstream consequences in one or both of the others — and those consequences can become self-reinforcing. The layer documents each describe propagation from their own perspective. This section maps it from above.

The propagation logic is not theoretical. It describes the specific mechanisms by which failure in one domain creates the conditions for failure in another. Understanding these pathways is what makes it possible to catch a problem early — before it has already crossed layers and compounded.

------

### How Technology Failure Spreads

**Technology → Process**

Data accuracy failure (T3) drives informal compensating workarounds across clinical teams. Those workarounds spread unevenly — some units develop them, others do not — and the formal process becomes inconsistent across the organisation. Staff not using the system (T4) splits the process in two: part runs through the system, part runs outside it. No one has a complete picture. This parallel-running state — the most dangerous operational condition in any health IT change — begins from a technology event, not a process decision. Access failure (T2) has the same effect: paper-based workarounds emerge to keep work moving during outages, and when the system comes back, both versions of the process run simultaneously.

**Technology → People**

Wrong system selection (T1) — particularly when staff perceive the decision was made without their input — damages trust early and durably. People who might have been broadly supportive become resistant. Those who were uncertain become more so. Low adoption (T4) is visible to everyone: when staff see that colleagues are not using the system as intended, it reads as evidence that the project is not working, and it becomes harder to sustain engagement. Post-launch deterioration (T6) is the most personal of the technology-to-people pathways — the individuals who championed the system and persuaded colleagues to commit to it face a credibility problem when the system degrades. Their capacity to advocate erodes at precisely the moment it is most needed.

------

### How Process Failure Spreads

**Process → Technology**

An inaccurate current state picture (P1) means the requirements used to select and configure the system were built on a false foundation. The system is deployed into a reality it was never designed for — and the mismatch presents as a technology problem. A process that cannot survive reality (P2) produces the same result through a different route: staff find ways around an unworkable workflow, and those workarounds involve bypassing the system. What looks like technology adoption failure frequently began as process design failure. A permanent transition period (P3) corrupts the data layer: records are split between the old process and the new system, reports become unreliable, and the system is technically operational but clinically untrustworthy.

**Process → People**

When staff recognise that the new process does not reflect how care is actually delivered, trust in the design — and in the project leadership — is damaged early (P1). Visibly uneven compliance (P5) signals to the broader workforce that non-compliance carries no consequences, making resistance easier to sustain. Process regression (P7) publicly undermines the staff who championed the new way of working — the effort they invested is visibly eroded, and their willingness to advocate collapses.

------

### How People Failure Spreads

**People → Process**

An authority-behavior gap (Pe3) — a process owner who is nominally accountable but behaviorally absent — means process decisions go unmade, execution inconsistency goes unchallenged, and regression goes unaddressed. A champion deficit (Pe4) removes the primary mechanism for sustaining process compliance after go-live. When champions burn out or coverage gaps emerge, the peer reinforcement that holds the future state in place disappears and the process drifts. Tracking decay (Pe7) and process regression run on the same timeline, in the same direction, invisibly.

**People → Technology**

A critical node in passive resistance who controls vendor approval does not block selection — they shape it toward the path of least resistance, which is frequently not the path of best clinical fit (Pe2). Technology selection failure begins in the People Layer. Neutral zone inertia (Pe5) produces technology adoption failure: a system can be technically sound, properly configured, and adequately trained — and fail because the People Layer never moved the population to a position where using the system was the natural behavior. Champion deficit (Pe4) removes the sustaining mechanism for post-launch adoption. The system does not deteriorate — the human infrastructure supporting it does.

------

### The Three Most Consequential Propagation Chains

Three cross-layer failure chains appear most consistently across health IT implementations and carry the highest combined consequence.

**Chain 1 — The Silent Parallel State**

P1 (inaccurate current picture) → P2 (process cannot survive reality) → T4 (staff not using system) → P3 (transition period permanent) → T3 (data unreliable) → Pe6 (resistance legitimised by visible failure)

The organisation never had an honest picture of how work was actually done. The future state process was designed against the sanitised version, and it cannot hold under real conditions. Staff find the system unworkable and route around it. The transition period never closes. Data splits between the formal system and informal workarounds. Reports become unreliable. Clinical staff who were sceptical from the start now have observable evidence that the system does not work — and resistance hardens.

**Chain 2 — The Post-Launch Collapse**

T6 (decline after launch) → Pe4 (champion deficit) → P7 (process regression) → Pe7 (tracking decay)

Support resources are withdrawn after go-live. The system deteriorates gradually. Champions who staked their credibility on the project face a trust problem with their peers and disengage. The process they were sustaining begins to erode without their peer reinforcement. No one is actively tracking any of this — the stakeholder map was last updated at launch, and the monitoring cadence was wound down when the project team left.

**Chain 3 — The Authority Vacuum**

Pe3 (authority-behavior gap) → P6 (process produces no improvement) → Pe2 (critical node exposure) → T1 (wrong system, compounded)

A senior leader holds formal accountability for the project but does not exercise it. Process ownership is nominal. The system runs; the improvements do not materialise, because neither the process nor the governance structure was ever held to account for delivering them. Critical decisions — about scope, configuration, and vendor management — are made by default rather than by authority. The system that was selected becomes progressively less fit for purpose as the gap between design intent and operational reality widens.

------

## VIII. Unified Accountability Structure

The layer documents assign ownership at the domain level. This section consolidates those assignments into a structure that can be read whole — and used to identify gaps before they become operational problems.

The framework makes a structural distinction between two types of ownership:

**Methodology ownership** — who designs and maintains the approach, runs the diagnostic instruments, and manages the cadence of assessment and detection. This belongs to the Change Manager.

**Authority ownership** — who has the organizational power to act on what the methodology surfaces: to close a transition, challenge a vendor, mandate a practice standard, or remove a nominal owner who is not performing. This belongs to the Project Sponsor and executive leadership.

When one operates without the other, the framework has structural gaps that no amount of tactical skill can close. A Change Manager without executive backing cannot close a permanent transition period or address an authority-behavior gap. A Project Sponsor without a functioning methodology has no reliable signal that these problems exist.

------

### Consolidated Ownership by Role

**Project Sponsor**

The Project Sponsor holds authority accountability across the framework — the role that must act when no other role has sufficient organizational power to do so. This includes: addressing authority-behavior gaps (Pe3); engaging critical nodes that sit beyond the Change Manager's organizational reach (Pe2); clearing the path for the Change Manager to exercise the full methodology; and making the call to delay a go-live when success conditions have not been met.

The Project Sponsor does not manage the methodology. The Project Sponsor creates the conditions under which it can work.

*Primary ownership:* Pe2, Pe3, P6, T1, T5

------

**Change Manager**

The Change Manager holds methodology ownership across the People Layer and the detection function across all three layers. This includes: maintaining the stakeholder map continuously from initiation through post-go-live sustainment (Pe1, Pe7); designing and running the champion network (Pe4); diagnosing and managing resistance (Pe6); and owning the cross-layer detection cadence.

The Change Manager cannot close problems that require organizational authority. The Change Manager identifies them, escalates them, and supports the Project Sponsor in addressing them.

*Primary ownership:* Pe1, Pe4, Pe6, Pe7; detection cadence across all layers

------

**Clinical Operations Lead / Clinical Operations Management**

Operational accountability for the transition period and for process sustainment after go-live. This role has the authority that the project team does not: to physically close the old process, remove paper forms, revoke old system access, and enforce the cutover (P3). It is also the role that must own process compliance as an ongoing management accountability, not a project team accountability — and that must formally receive process ownership before the project closes (P7).

*Primary ownership:* P3, P5, P7

------

**CMIO / Clinical Informatics Lead**

Clinical authority for process design and outcome accountability. The only role with the authority to mandate new practice standards (P2) and to reopen the design question when compliance is high but outcomes are not moving (P6). Also holds joint ownership of the system adoption problem at the intersection of technology and clinical workflow (T4).

*Primary ownership:* P2, P6, T4 (joint)

------

**Clinical Process Owner / Business Analyst**

Discovery accountability for the current state. The role responsible for going beyond official documentation to find how work actually happens — including the informal workarounds that no planning exercise surfaces (P1, P4). This role requires access to front-line staff across all shifts and the organisational permission to hear what is actually happening, not the official version.

*Primary ownership:* P1, P4

------

**Health Informatics Lead**

Accountability for the highest-risk technical domain in the framework. Data accuracy and system connections (T3) is the area that can cause direct patient harm — silently, without obvious warning — when information flowing between systems is incomplete, incorrectly mapped, or corrupted during migration. This role owns the active monitoring of data integrity from migration through post-live operations, and holds the escalation responsibility when incorrect information may already have been acted upon clinically.

*Primary ownership:* T3

------

**CIO / IT Operations Lead**

Infrastructure and system lifecycle accountability. The CIO holds joint ownership with the Chief Medical Officer for system selection (T1) — the failure area where most technology problems originate. The IT Operations Lead holds accountability for post-launch system health (T6) and the access and infrastructure conditions that determine whether staff can use the system at all (T2).

*Primary ownership:* T1 (joint), T2, T6

------

**Chief Medical Officer**

Clinical accountability for whether the system was the right system (T1, joint with CIO) and whether it is delivering the improvements it was meant to deliver (T5). The CMO is the organisational voice for whether technology is serving clinical purpose — and the role that must escalate when it is not.

*Primary ownership:* T1 (joint), T5

------

### The Accountability Gap Test

The most reliable way to identify an accountability gap is to ask, for each of the twenty failure domains: if this problem were happening right now, who would know, and who has the authority to fix it?

If the answer to either question is "unclear," the gap is structural. It will not resolve itself under project pressure. It must be assigned before it is needed.

------

## IX. The Project Lifecycle Overlay

The framework applies continuously from project initiation through post-go-live sustainment. Its emphasis shifts across phases — not because different things matter at different times, but because the nature of the risk changes, and the instruments most relevant to managing it change with it.

This section describes what the framework looks like in each phase, what the primary risks are, and what the most important management activities are.

------

### Phase 1 — Initiation and Discovery

**Primary risk:** Building on a false foundation.

If the current state is misrepresented, if assumptions are left unspoken, or if the wrong system is selected without adequate clinical input, every subsequent phase inherits the consequences. The problems introduced at this stage are the hardest to correct later — because by the time they become visible, significant work has been built on top of them.

**Primary activities:**

- Establish the stakeholder map using observed behavior, not role or assumption (Pe1). Document every position below Passive Support with a diagnosed reason.
- Conduct current state discovery through direct observation across all shifts and sites — not from official documentation or conference room workshops (P1). Surface informal workarounds explicitly (P4).
- Assess infrastructure at every deployment site against what the system will actually require in real conditions (T2).
- Review existing data quality before any migration scope is defined (T3).
- Define success criteria for all three layers before design begins. Establish baselines for outcome measures before any change is made (P6, T5).
- Build the Assumptions Tracker, the Risk Register, and the detection cadence. These instruments do not start at go-live. They start here.

------

### Phase 2 — Design and Configuration

**Primary risk:** Embedding failure into the design.

This is the phase where process decisions are made — most of them inside system configuration, by IT staff, without anyone recognising them as process decisions. By the time the system goes live, the process has already been decided. The consequences arrive as operational friction, not technical errors.

**Primary activities:**

- Involve clinical and operational staff as designers of the future state workflow, not just reviewers (P2). Walk average performers through the workflow, not just champions.
- Stress-test the future state workflow against worst-case conditions before configuration is finalised — short-staffed shifts, surge scenarios, system slowdowns (P2).
- Make every process decision embedded in system configuration explicit and reviewed by clinical leadership (P2, hidden process decisions).
- Make deliberate decisions about every workaround identified in discovery: build it into the new process, preserve it intentionally, or retire it with a clear rationale (P4).
- Begin champion network design against the critical node map — placement first, recruitment second (Pe4).
- Maintain and update the stakeholder map. The design phase changes positions. Stakeholders who were neutral at initiation become resistant when they see what the new process actually requires of them. (Pe7)

------

### Phase 3 — Testing and Piloting

**Primary risk:** A clean test environment that does not predict real deployment.

Systems are tested under controlled conditions — reliable internet, standard equipment, a small group of trained users. The real deployment involves older devices, patchy connectivity, high staff turnover, and users who were trained once months ago. A system that passed every test can still struggle badly in the field.

The pilot is also the last point at which response protocols can be reviewed and refined before full-scale go-live. That opportunity is time-limited. Once the full deployment begins, the only thing available is the protocol as written.

**Primary activities:**

- Test in the real environment — actual sites, actual devices, actual connectivity conditions (T2, T3).
- Use realistic, complex data in testing — not simple test cases that do not reflect the messiness of real clinical records (T3).
- Walk actual end users through their real tasks in the system. Identify points of friction before they become workarounds (T4).
- Define and publish the cutover plan: the specific point at which the old process ends, the explicit rules about what documentation lives where during the transition, and the escalation pathway when those rules are not followed (P3).
- Review and stress-test response protocols during the pilot while the stakes are lower (all layers).
- Assess champion network coverage against the deployment map. Every critical node type must have someone at or moving toward Active Support before go-live (Pe4).
- Review all unconfirmed assumptions in the Assumptions Tracker. Unconfirmed assumptions on the eve of go-live are unmanaged risks.

------

### Phase 4 — Go-Live and Transition

**Primary risk:** Treating go-live as the finish line.

This is the phase where most organisations declare victory and begin to wind down their change management investment. It is the phase where the most significant risks are just beginning to emerge.

The transition period — the interval where old and new processes run simultaneously — opens at go-live. Everything the framework does in this phase is aimed at closing it as quickly as possible.

**Primary activities:**

- Deploy concentrated on-the-floor support during the transition window — not spread evenly across the project timeline (P3). This is where the return on support investment is highest.
- Track system login and task completion rates by unit, not in aggregate (T4, P5). Averages hide the worst-performing areas.
- Watch for signs of parallel running: paper records alongside digital ones, fields left empty, data entered in batches, both system entries and paper records for the same clinical event (P3, T4).
- Maintain active leadership visibility through the full transition period (P3). The transition normalises fastest when senior attention withdraws.
- Monitor champion health at every tracking cadence (Pe4). Advocacy frequency, peer reach, and organisational protection are observable signals. Act on signs of fatigue before withdrawal begins.
- Update the stakeholder map immediately after go-live. Go-live changes positions. (Pe7)
- If the transition is not closing within thirty days, treat it as a formal incident, not a drift. Deploy additional floor support. Escalate to operational leadership. If necessary, implement a controlled step backward to the old process with a defined re-launch plan. (P3)

------

### Phase 5 — Post-Live Sustainment

**Primary risk:** Failure that accumulates after the project team leaves.

The project structure is wound down. Attention moves elsewhere. The detection cadence collapses. Small problems compound into large ones unchecked. Champions burn out without organizational protection. Process regression begins in the pockets where compliance was thinnest. The system deteriorates as vendor updates break things that were working and no one is watching closely enough to notice.

By the time any of this becomes formally visible, it has usually been accumulating for months.

**Primary activities:**

- Formally transfer ownership of all three layers from the project team to the operational management structure before the project closes — documented, deliberate, and complete. The handover includes the detection mechanisms, the Assumptions Tracker, the Risk Register, and the champion network (P7, Pe7, T6).
- Continue compliance monitoring for a minimum of twelve months post-launch (P7). Conduct periodic audits at sixty, ninety, one hundred eighty, and three hundred sixty days.
- Conduct a formal system health review at least quarterly (T6). Track staff satisfaction with the system over time, not just at launch.
- Conduct stakeholder reassessments at ninety days and six months post-go-live at minimum (Pe7). Leadership changes, champion disengagement, and post-launch fatigue all alter the behavioral landscape.
- Maintain an active relationship with the vendor beyond the implementation phase (T6). System updates can break things that were working — the relationship that enables rapid vendor response must be sustained, not allowed to lapse.
- Monitor whether the improvements the system was meant to deliver are materialising — and are being maintained (T5, P6). Outcome measures must continue to be tracked after launch, not just reported at project closure.

------

## X. The Silent Failure Problem

Fourteen of the twenty failure domains in this framework carry a high silent failure risk designation. This concentration is not incidental. It is the defining characteristic of health IT change management, and it is what makes this discipline different from most other forms of project management.

In most project environments, failure is visible before it becomes serious. A missed deadline shows up on the project plan. A budget overrun shows up in the financial report. A system outage generates an alert.

In health IT change management, the most consequential failures do not generate alerts. They accumulate silently — in the gap between what the stakeholder map says and what is actually happening, in the parallel process that no one is formally tracking, in the data flowing incorrectly between systems that appear to be working, in the champion who has quietly stopped advocating.

The implication is direct. Detection cannot be passive. It cannot rely on problems surfacing through normal reporting channels. It must be actively designed — with instruments maintained continuously, cadences that do not collapse under pressure, and owners who are specifically responsible for looking for things that are not announcing themselves.

The detection question in the application methodology — *how will we know if something is going wrong before it becomes a crisis?* — is not the most commonly skipped question in project planning by accident. It requires the most uncomfortable thing in any project environment: actively looking for evidence that the project is not working, at a time when the organizational pressure is to demonstrate that it is.

That discomfort is the discipline. The framework requires it.

------

## XI. Reading the Framework as a System

The five documents that make up this framework are designed to be read together — not as a library of independent references, but as a system with a consistent internal logic.

The master document (this document) establishes the Core Statement, the Governing Principles, and the unified view: cross-layer propagation, consolidated accountability, and the lifecycle overlay.

The three layer documents — People, Process, and Technology — each provide a complete failure control map for their domain: the specific things that go wrong, what to watch for, how to prevent it, what to do when it happens, and who is responsible. They also each provide a cross-layer propagation section from their own perspective, which is the raw material for Section VII of this document.

The application guide — *How to Use the Framework* — provides the operational method: Identify, Question, Implement. It is the bridge between the diagnostic content of the layer documents and the active management work of the project.

The correct reading sequence for a practitioner new to the framework is: this document first, for orientation; then the application guide, for method; then the layer documents in the order most relevant to the immediate problem being managed. All three layer documents should be consulted before significant project decisions — because the decision most likely to create a cross-layer failure is the one made while looking through only one lens.

------

*Companion documents: People Layer · Process Layer · Technology Layer · How to Use the Framework*