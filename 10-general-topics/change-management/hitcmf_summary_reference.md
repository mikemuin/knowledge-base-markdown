# Failure-Aware Change Management Framework

## Summary Reference Document

------

## Core Statement

> Change management is the disciplined management of failure across People, Process, and Technology — concurrent with project execution — to protect the integrity of the transition from the current to the future state.

------

## Governing Principles

| #    | Principle                                  | Operational Implication                                      |
| ---- | ------------------------------------------ | ------------------------------------------------------------ |
| 01   | **Start on day one, not day done**         | Change management runs from the first planning meeting — not after delivery. Introduced after configuration begins, its most important opportunities have already passed. |
| 02   | **Engineer for failure, not for optimism** | Failure is the default. Success is what you deliberately build against it. Every assumption, dependency, and timeline must be examined for where it breaks. |
| 03   | **What you can't see will hurt you most**  | Fourteen of twenty failure domains fail silently. Detection — actively designed, continuously maintained — is as important as prevention. |
| 04   | **If everyone owns it, no one owns it**    | Unified accountability for the transition as a whole must be deliberately assigned. The intersections between layers are where the most consequential failures occur. |
| 05   | **Not all failure costs the same**         | A training gap is recoverable. A go-live collapse that destroys clinician trust is not. A patient safety event is irreversible. Response must be calibrated to consequence. |

------

## The Three Layers

| Layer          | Role              | What Is Being Managed                                        |
| -------------- | ----------------- | ------------------------------------------------------------ |
| **People**     | Behavior System   | Stakeholder behavior from engagement through adoption — continuously. Where each person stands in relation to the change, and whether they move in the direction the project requires. |
| **Process**    | Execution System  | The transition from current state to future state — from how things are done today to how they need to be done. The highest risk is the transition period itself: the interval where old and new processes run simultaneously. |
| **Technology** | Enablement System | Whether the tools deployed actually get used, and whether they help people do their jobs better. The true measure is adoption, not go-live. |

------

## Complete Failure Domain Map

> ⚠ = high risk of silent failure — failure that builds to serious consequence without generating an obvious warning signal.

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

## The Three Most Consequential Cross-Layer Failure Chains

**Chain 1 — The Silent Parallel State** P1 → P2 → T4 → P3 → T3 → Pe6

The current state was never honestly mapped. The future state process was designed against the sanitised version and cannot hold under real conditions. Staff route around the unworkable system. The transition period never closes. Data splits between formal records and informal workarounds. Reports become unreliable. Visible system failure legitimises the resistance that was present from the start.

**Chain 2 — The Post-Launch Collapse** T6 → Pe4 → P7 → Pe7

Support resources withdraw after go-live. The system deteriorates. Champions who staked their credibility on the project disengage. The process they were sustaining begins to erode without peer reinforcement. No one is tracking any of it — the stakeholder map was last updated at launch, the monitoring cadence was wound down when the project team left.

**Chain 3 — The Authority Vacuum** Pe3 → P6 → Pe2 → T1 (compounded)

A senior leader holds formal accountability but does not exercise it. Process ownership is nominal. The system runs; the improvements do not materialise because neither the process nor the governance structure was ever held to account for delivering them. Critical decisions are made by default rather than by authority. The system becomes progressively less fit for purpose as the gap between design intent and operational reality widens.

------

## Application Sequence: Identify → Question → Implement

### Step 1 — Identify

Take inventory of what belongs to each layer before applying any analysis.

- **People:** Who is involved, where they stand, who the champions are, where authority sits, where resistance originates.
- **Process:** How work is actually done today, how it should be done after, informal workarounds, handoffs, compliance requirements, the transition overlap period.
- **Technology:** Systems being implemented, integrations, infrastructure realities, data quality, configuration, training, post-go-live support.

### Step 2 — Question

Run these six questions against each identified component.

| #    | Question                                                     | Purpose                                                      |
| ---- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| Q1   | What does success look like for this?                        | Forces a specific, observable, time-bound success definition before any other analysis begins. |
| Q2   | What are we taking for granted?                              | Surfaces unspoken assumptions and separates confirmed from unconfirmed ones. Unconfirmed assumptions are risks in disguise. |
| Q3   | Where could we fail?                                         | Identifies failure points by severity — low consequence, high consequence, irreversible — with explicit attention to risks that could fail silently. |
| Q4   | How will we know if something is going wrong before it becomes a crisis? | Designs the detection mechanisms — data, observation, conversation, behavioral signals — with cadence and named owner for each. |
| Q5   | What do we do now to reduce the chance of failure?           | Defines proactive prevention actions, each with an owner and a completion date. |
| Q6   | If something fails, what do we do?                           | Prepares response protocols before they are needed: trigger, steps, owner, recovery target. |

### Step 3 — Implement

Embed the outputs of Step 2 into the live project.

| Output               | How It Is Used                                               |
| -------------------- | ------------------------------------------------------------ |
| Success definitions  | Become milestone gate criteria. The project advances when conditions are met, not on schedule alone. |
| Assumptions          | Tracked in a live Assumptions Tracker, reviewed at every major milestone, carried through post-go-live. |
| Risks                | Recorded in a Risk Register by layer and severity, with silent failure risks flagged for higher-frequency review. |
| Detection mechanisms | Active from day one through post-live sustainment, with named owners. Transfer to operations at project closure. |
| Prevention measures  | Scheduled as live project tasks with deadlines and completion criteria. |
| Response protocols   | Prepared and stress-tested before they are needed. Reviewed during piloting. High-consequence protocols specify containment before diagnosis. |

------

## Unified Accountability: The Structural Distinction

The framework makes a non-negotiable distinction between two types of ownership. Neither functions without the other.

**Methodology ownership — Change Manager** Designs and maintains the approach. Runs the diagnostic instruments. Manages the assessment and detection cadence. Identifies problems and escalates them. Cannot close problems that require organisational authority.

**Authority ownership — Project Sponsor / Executive Leadership** Has the organisational power to act on what the methodology surfaces: close a transition, challenge a vendor, mandate a practice standard, address a nominal owner who is not performing. Does not manage the methodology — creates the conditions under which it can work.

When one operates without the other, the framework has structural gaps that no amount of tactical skill can close. A Change Manager without executive backing cannot close a permanent transition period. A Project Sponsor without a functioning methodology has no reliable signal that these problems exist.

------

## When the Framework Ends

The framework does not end at go-live. It ends when accountability has been formally transferred — documented, deliberate, and complete — from the project team to the operational management structure: process ownership to Clinical Operations, technology health to IT Operations, stakeholder tracking and champion network stewardship to the Change Manager embedded in operations. That transfer must include the detection mechanisms, the Assumptions Tracker, the Risk Register, and the defined escalation pathways. Compliance monitoring must be confirmed active for a minimum of twelve months post-launch. Stakeholder reassessments must be scheduled at ninety days and six months post-go-live. Until every one of these conditions is verifiably met, the project is not done — it is in an unmanaged transition that the framework was specifically designed to prevent.