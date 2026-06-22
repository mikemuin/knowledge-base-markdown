Good. Let's build the map domain by domain. Seven domains, same logic as Technology: top risks, prevent, detect, respond, ownership. But I want to call out the nuances as we go — because Process has more moving parts than Technology at almost every step.

------

## Domain 1 · Current State Misrepresentation

This is where Process failure most often begins — before the future state is even designed. You are building on a false foundation.

**Top risks**

The core risk is **workshop capture bias**. When you bring clinical staff into a room and ask them to describe how they work, they describe the *ideal* version — the policy-compliant, fully-staffed, everything-working version. Not the 2am version. Not the short-staffed Friday version. Not the version where the lab system is down and everyone is running on memory and informal agreements. That gap between the presented process and the enacted process is where your future state will fail.

The second risk is **single-source discovery** — interviewing managers and senior nurses but not the ward clerk who actually coordinates the handoff. The people closest to the informal process are rarely the ones in the room.

The third is **site homogeneity assumption** — documenting one site and treating it as representative. In any multi-site Health IT implementation, process variation across sites is almost always underestimated.

**Prevent**

Multi-method discovery. Observation and shadowing alongside interviews — not instead of them. Explicit questions designed to surface the real process: *"What do you do when the formal process doesn't work? What would break if you weren't here?"* Cross-shift discovery — night shift behavior is almost always more revealing than day shift. And critically: front-line staff must be represented, not just clinical leadership.

**Detect**

The clearest detection signal comes during configuration. When the system can't accommodate the documented workflow, it usually means the documentation was wrong, not that the system is wrong. Staff pushback during training is the second signal — when people say *"that's not how we actually do it,"* that is a current state re-mapping opportunity, not a training compliance problem. A representative pilot is your last chance to catch this before full rollout.

**Respond**

Current state re-mapping based on findings. If the gap is significant, the future state design must be revisited before configuration is locked. This is expensive and uncomfortable — but far less expensive than a go-live built on a false foundation.

**Owner:** Clinical Process Owner / Business Analyst — but with a critical annotation: this role needs *access*, not just authority. If the discovery team cannot get to front-line staff across shifts and sites, the current state will be misrepresented regardless of methodology.

------

## Domain 2 · Future State Design Failure

The current state was accurately mapped. The future state was designed in a conference room by people who don't work the night shift.

**Top risks**

**Designed for ideal conditions.** Full staffing. Working technology. No competing priorities. No interruptions. The process works beautifully when everything goes right. Clinical environments are defined by things not going right. A future state that cannot be executed under degraded conditions is not a future state — it's a plan.

**Step count inflation.** Technology implementations almost always add steps before they remove them. When the net step count goes up, staff find workarounds — not because they're resistant, but because they're busy. If the future state is more burdensome than the current state for the median performer, you have already designed your workarounds.

**Designed without stress-testing.** The future state was validated by champions and approved by leadership, but never tested against: a system outage, a staff shortage, a surge in patient volume, or a new staff member on their third shift. These are not edge cases. They are the operational baseline of most clinical environments.

**Prevent**

Clinical co-design — not just clinical validation. There is a meaningful difference between designing *with* clinicians and presenting *to* clinicians for sign-off. The former catches design failures before they are built. The latter catches them at go-live.

Stress-test the future state explicitly against worst-case operational scenarios before configuration is locked. Define a downtime procedure from the beginning — not as an afterthought. Compare the step count of the future state to the current state and justify every net addition.

**Detect**

Resistance during training that is specific, not general. When staff say *"this will never work on a night shift"* — that is a design signal, not a training problem. Pilot site failures are your most valuable detection mechanism, but only if the pilot site is genuinely representative of your hardest-case deployment, not your most cooperative one.

**Respond**

Workflow redesign before full go-live — uncomfortable but recoverable. Scope reduction: implement a simpler version first and add complexity once the core workflow is stable. Reconfiguration where the system can accommodate a simplified design.

**Owner:** Clinical Informatics Lead / CMIO — requires clinical authority. A project manager cannot mandate a clinical workflow redesign. This is a structural reality most projects resist until they can't.

------

## Domain 3 · Hybrid State Persistence

The most dangerous operational condition in any Health IT implementation. You cannot eliminate it. The goal is to compress it as aggressively as possible.

**Top risks**

**No cutover strategy.** The implementation drifts from old to new without a defined transition point. Some staff move to the new process, some stay on the old. Documentation splits between paper and system. No one has a complete picture of the patient.

**Ambiguous rules during transition.** Staff don't know which process to follow when. When in doubt, they follow what is comfortable — usually the old process. The hybrid deepens.

**Gradual rollout without phase endpoints.** Phased rollouts are sometimes necessary. But each phase must have a defined endpoint — a date after which the old process is closed in that area. Without that, the gradual rollout becomes a permanent hybrid.

**The hybrid becoming normalized.** This is the most insidious form. After thirty days, the hybrid is no longer perceived as a transition state — it is perceived as the new normal. At that point, you are no longer managing a transition. You are managing a failed implementation.

**Prevent**

A definitive cutover strategy. Big bang where feasible. Phased only where necessary — with explicit phase endpoint dates and a clear protocol for closing the old process at each phase boundary.

Unambiguous rules for what lives where during the transition period. Published. Communicated. Not assumed.

Active at-elbow support concentrated in the hybrid window — not distributed evenly across the implementation timeline. The hybrid window is where support investment has the highest return.

**Detect**

The signature of hybrid state persistence is specific and observable: split documentation (some entries in the system, some on paper), inconsistent handoffs between shifts, data gaps in system reports that correspond to activities still being documented on paper, and staff reporting confusion about which process to follow.

These signals should be actively looked for — not waited for. A daily check in the first two weeks of go-live, then weekly for the first ninety days.

**Respond**

Immediate escalation protocol when hybrid state persistence is detected. Additional at-elbow support deployed within hours, not days. If the hybrid is widespread and entrenched, a formal recovery plan — which may include a temporary rollback to the current state with a defined re-launch date. This is a difficult decision, but a controlled rollback is recoverable. A permanent hybrid is not.

**Owner:** Project Manager / Clinical Operations Lead — with a critical annotation: this requires *operational* authority. The ability to close the old process — to actually remove the paper forms, retire the old system access, mandate the cutover — must come from operational leadership, not the project team.

------

## Domain 4 · Informal Process Blindness

The load-bearing undocumented processes were never found. The future state has no equivalent for them. Care coordination breaks in ways nobody predicted.

**Top risks**

**Discovery limited to formal channels.** Workshops, policy documents, existing SOPs. These capture the formal process only. The informal process — the charge nurse who coordinates a handoff that has no formal mechanism, the verbal escalation agreement between two departments that has never been written down, the workaround that compensates for a chronic system deficiency — is invisible to formal discovery.

**Informal processes framed as problems.** When informal processes are identified, they are often treated as compliance failures to be eliminated rather than compensating mechanisms to be understood. This is a category error with real consequences. Before you eliminate an informal process, you must understand what it was compensating for. If you don't, you eliminate the symptom and leave the gap.

**Single-shift, single-site discovery.** Informal processes are often shift-specific and site-specific. The informal handoff mechanism that exists on the night shift at the rural facility is invisible to a daytime discovery workshop at the main campus.

**Prevent**

Structured informal process discovery as a formal project workstream — not an add-on to the formal process mapping exercise. The explicit discovery question is: *"What would break if you weren't here?"* That question surfaces load-bearing informal processes faster than any other.

Cross-shift, cross-site observation. The people who know the informal process best are the longest-serving front-line staff. They are rarely the people in the conference room.

An explicit inventory of informal processes with a conscious disposition decision for each: formalize it, preserve it deliberately, or retire it because the new process genuinely makes it obsolete.

**Detect**

Post-go-live workarounds emerging rapidly and in clusters. When new informal processes appear within the first thirty days, they are almost always reconstructions of informal processes that were disrupted and never replaced. They are a map of what was missed.

Care gaps in areas where informal handoffs used to exist. Staff describing a vague sense that *"something is missing"* without being able to articulate what — this is the signature of a disrupted informal process.

**Respond**

Informal process archaeology — go back and find what was missed, using the post-go-live workarounds as a guide. Formal process amendment to incorporate the load-bearing informal steps that were overlooked. Interim manual bridge while the formal fix is designed and configured.

**Owner:** Clinical Process Owner + Front-line Staff Representatives — with the strongest annotation in the entire map. This domain cannot be owned by IT or by project management. The informal process lives with the people who execute it. Discovery and response require their direct involvement, not their representation by a clinical lead who may not share their operational reality.

------

## Domain 5 · Execution Inconsistency

The workflow runs correctly for champions and early adopters. It does not run correctly for the median performer under operational pressure. Success is defined by the median, not the champion.

**Top risks**

**Training designed for the ideal user.** Most training programs are designed around the system's full functionality and assume a motivated, attentive learner with adequate time. The median clinical user is none of those things on a busy shift. Training that works for a super-user in a classroom does not automatically transfer to a floor nurse managing six patients.

**Measurement by best-performing unit.** Leadership measures adoption by looking at where it is working. The failure lives where it is not. Deliberately measuring the worst-performing units and shifts is organizationally uncomfortable and analytically essential.

**Inadequate at-elbow support distribution.** Support is concentrated at flagship sites and primary shifts. The rural facility, the night shift, the float staff — these are where execution inconsistency is highest and support is lowest.

**Process complexity exceeding sustainable execution.** If the workflow requires the user to hold more than a few steps in working memory under interruption — it will be executed inconsistently. Complexity is a design failure, not a training problem.

**Prevent**

Train for the median performer, not the expert user. Design training around the most cognitively demanding scenarios, not the routine ones. Role clarity must be explicit — who does what, when, in what sequence — documented at a granularity that removes ambiguity at the point of execution.

At-elbow support planned and resourced for all sites and all shifts, not concentrated at flagship locations. Process simplification as a design principle: if a step generates consistent failures, the step is wrong, not the user.

**Detect**

Performance variation across sites and shifts is your primary signal. If unit A is executing at 90% compliance and unit B at 40%, that is not a training problem. It is an execution environment problem — and it needs to be investigated, not averaged away.

Data quality variation by shift and site is a powerful proxy. Documentation completeness at 3am tells you something real about process execution under pressure.

**Respond**

Targeted support to underperforming units and shifts — not broadcast retraining. Peer learning structures: what is the high-performing unit doing that the low-performing unit isn't? Process simplification for steps that are generating consistent failures across multiple sites. Role clarity intervention where ambiguity is the root cause.

**Owner:** Clinical Operations Manager / Unit Leads — this is front-line operational ownership. The project team can identify and diagnose execution inconsistency, but closing it requires the authority of operational leadership to mandate practice standards at the unit level.

------

## Domain 6 · Outcome Misalignment

The most quietly devastating failure mode. The workflow is executing correctly. Staff are compliant. The system is live. And the problems the implementation was meant to solve are still present.

**Top risks**

**Outcomes defined after go-live.** This is the most common version. KPIs are invented to describe what happened, not to measure what was intended. You cannot measure improvement against a baseline you didn't establish.

**A symptom was addressed instead of a root cause.** The process was redesigned at the wrong level. The documentation workflow was improved, but the care coordination failure it was supposed to solve was caused by something upstream that the redesign didn't touch.

**Scope too narrow to move outcomes.** The process change was real but insufficient. Improving the referral documentation process does not reduce referral turnaround time if the bottleneck is capacity at the receiving facility, not documentation quality.

**Wrong metrics selected.** The metrics selected are activity metrics — volumes, completion rates, time stamps — not outcome metrics. Activity can be high while outcomes are unchanged.

**Prevent**

Outcome KPIs defined before the future state is designed. Not after go-live. Not during go-live. Before the design is locked. This is the only way to verify that the designed process is actually capable of moving the metrics you care about.

Root cause analysis of current state problems before designing solutions. If you do not understand why the current state produces the outcomes it does, you cannot be confident that a redesigned process will produce different ones.

Baseline measurement before go-live. Without a baseline, you have no reference point.

**Detect**

KPI tracking from day one of go-live against a pre-implementation baseline. Clinical outcome monitoring tied to the specific workflows the implementation was designed to support. The key signal is not absolute performance — it is the *direction and rate of change* relative to baseline. Flat or worsening metrics against a healthy execution compliance rate means the design, not the execution, is the problem.

**Respond**

Root cause re-analysis when outcomes are not moving despite compliant execution. Scope expansion if the process change was too narrow. Process redesign at a higher level if a symptom was addressed instead of a root cause. Governance escalation when outcome misalignment is fundamental — this is a strategic decision, not a project decision.

**Owner:** CMIO / Project Sponsor — this is the highest-authority ownership in the entire Process map. Outcome misalignment cannot be resolved at the project level. It requires the authority to redesign scope, redefine measures, and re-engage clinical and executive leadership.

------

## Domain 7 · Regression

The future state holds for ninety days. Then operational pressure, staff turnover, and fading leadership attention begin to pull it back toward the current state. Quietly. Incrementally. Until the future state exists on paper and the current state exists in practice.

**Top risks**

**Process ownership not transferred from project to operations.** The project team managed compliance during go-live. When the project closes, compliance monitoring goes with it. No one in operations picked it up.

**Leadership attention withdrawn.** The executive sponsor moves on. The clinical champion gets absorbed by other priorities. The visible accountability that drove compliance during go-live disappears.

**Staff turnover removing trained practitioners.** New staff are oriented by untrained peers, not by the formal process. Within six months, the informal process has reconstituted itself around the gaps that the new staff's peers know how to navigate.

**Operational pressure making the old process feel easier.** Under surge conditions, staff revert to familiar behaviors. The first regression is excused as a crisis response. The second is normalized. The third is invisible.

**Prevent**

Sustainment plan defined and resourced before go-live — not after. Process compliance built into operational management structures: embedded in performance conversations, unit dashboards, and departmental governance — not managed by the project team.

Process ownership formally transferred from the project to an identified operational owner with explicit accountability. New staff orientation includes the process, not just the system.

**Detect**

Process compliance monitoring on a defined schedule — not continuous, but not abandoned. Quarterly minimum. KPI trending over six to twelve months: a downward trend after an initial improvement is a regression signal. Direct observation at intervals post-go-live. The subtlest and most reliable signal: staff describing the process in the past tense. *"We used to document it this way."*

**Respond**

Rapid response to early regression signals — the earlier the intervention, the lower the cost. Targeted retraining before regression becomes normalized. Process communication refresh to re-establish visibility and accountability. If regression is widespread, a formal re-implementation plan with a new go-live structure — treating it as a project, not an operational correction.

**Owner:** Clinical Operations Management / Department Heads — this is sustainment ownership. It cannot live with the project team because the project ends. It must be embedded in the operational management structure from the beginning, not handed over as an afterthought at project closure.

------

**The ownership pattern across all seven domains**

Notice what happens across the map. Every domain requires a *different type* of authority:

- **Clinical authority** — to mandate practice change (Domains 1, 2, 5)
- **Operational authority** — to close old processes and enforce new ones (Domains 3, 7)
- **Discovery authority** — to access front-line staff across all shifts and sites (Domain 4)
- **Strategic authority** — to redefine scope and outcomes (Domain 6)
- **Hybrid authority** — both clinical and operational simultaneously (Domain 3)

No single person holds all of these. This is the ownership challenge in Process that doesn't exist in the same way in Technology. The Process map must be supported by a governance structure that can activate each type of authority at the right moment — otherwise the map is a document, not a management tool.

------

That's the full Process Failure Control Map. Shall I now write the complete Process document — success definition and control map — in markdown?