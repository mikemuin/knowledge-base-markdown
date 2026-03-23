---
title: 10 Problems and Lessons Learned from the Field
subtitle: EMR Implementation — What Goes Wrong and How to Fix It
author: Health IT Implementation Team
date: March 2026
---

# 10 Problems and Lessons Learned from the Field

- A practitioner's guide to the most common EMR implementation failures
- Based on patterns observed across real-world deployments

<div class="notes">Welcome everyone. This presentation covers ten of the most common and consequential problems encountered in EMR implementations. These are not theoretical concerns — they are patterns that repeat across organizations of different sizes, specialties, and resource levels. Our goal today is to name these failures clearly, understand why they happen, and walk away with practical solutions that can be applied in your own context. We will move through each problem in three parts: the problem itself, the lessons the field has learned, and the solutions that actually work. Let's begin.</div>

---

## Agenda

- Ten recurring EMR implementation failures
- For each problem: the issue, lessons learned, and solutions
- Topics covered:
    - Selection and infrastructure readiness
    - Organizational transformation and data governance
    - Integration, training, and go-live support
    - Post-go-live stabilization, change management, and adoption

<div class="notes">Here is how we have structured today's session. We will move through all ten problems, and for each one we will cover three slides: the problem itself, the key lessons the field has learned, and the solutions that address it. The topics span the full implementation lifecycle — from system selection through long-term adoption management. Each section builds on the last, so even if one area feels familiar, the cumulative picture matters. Feel free to raise questions as we go or hold them for the end.</div>

---

## Problem 1: EMR Selection Is Evaluated at the Wrong Level of Specificity

- Selection committees evaluate feature categories, not concrete workflows
- Vendors answer "yes" to "do you support CPOE?" — a question that carries almost no useful information
- What matters: can a resident place a standing vital signs order? Can a nurse document compliance against it?
- Gaps at the category level go undetected until after go-live

<div class="notes">The first problem happens before a single line of the system is configured. Organizations evaluate EMR systems using high-level feature labels — CPOE, Clinical Documentation, Medication Management — and vendors answer "yes" in good faith because the question is too vague to produce a meaningful answer. The failure is not in the vendor's response; it is in the question. What the hospital actually needs to know are scenario-level capabilities: specific, concrete workflows that will determine whether the system works in practice. Gaps at this level are invisible until after go-live, and by then they are expensive to address.</div>

---

## Problem 1 — Lessons Learned

- Feature category names describe module presence, not workflow capability
- Two systems can both have CPOE and behave completely differently for the same clinical task
- The people who know the requirements are clinicians and operational staff — not procurement teams
- Gaps discovered after go-live are worse than gaps discovered during implementation

<div class="notes">The lesson is that evaluation must go deeper than category labels. Two systems can both claim CPOE support and differ completely in whether a specific clinical task is achievable. Vendors are not deceiving anyone when they say "yes" to a vague question — the question was simply not designed to surface the relevant information. The people who can produce scenario-level requirements are the ones doing the work: clinicians and operational staff. Procurement teams working from generic RFP templates are not equipped to surface these requirements on their own, and gaps that are never articulated as requirements cannot be evaluated at all.</div>

---

## Problem 1 — Solutions

- Before evaluation begins, convene clinical and operational staff to define scenario-level requirements
- Structure vendor demonstrations around your scenario list, not the vendor's standard demo script
- Use a scripted demonstration scorecard: demonstrated, steps required, clinical match
- Treat "that can be configured" as unverified — require demonstrations or written commitments
- Require reference calls with organizations using the same modules for the same clinical scenarios

<div class="notes">The corrective actions here are front-loaded and require deliberate effort before the selection process begins. The scenario list produced by clinical and operational staff is the most valuable output — it transforms the evaluation from a feature comparison into a workflow validation exercise. The scripted scorecard disciplines the evaluation: for each scenario, record whether it was demonstrated, how many steps it required, and whether it matched clinical expectations. The reference call requirement is a filter that eliminates aspirational selling. Pause here and ask: in your last vendor evaluation, how many scenarios were demonstrated versus described? That gap is where selection mistakes live.</div>

---

## Problem 2: IT and Infrastructure Readiness Is Never Formally Assessed

- Implementation projects assume existing network, devices, and environment are adequate
- No one tests this assumption against the actual post-go-live operating scenario
- Network gaps, device shortfalls, and peripheral placement problems go undetected
- Deficiencies surface after go-live — under clinical load, with real patients

<div class="notes">The second problem is one of assumption. Implementation teams proceed on the belief that existing infrastructure can support EMR operation at volume, but no one formally tests this belief against the specific mix of users, locations, workflows, and simultaneous system load that the organization will face on day one. Network coverage gaps in wards, procedure rooms, and patient bays are invisible until go-live. Device counts based on procurement budgets rather than workflow analysis are insufficient. These are not surprises — they are predictable outcomes of skipping a formal assessment.</div>

---

## Problem 2 — Lessons Learned

- Infrastructure problems discovered after go-live are far more disruptive than those found in planning
- "We have Wi-Fi everywhere" is not a readiness assessment — it is an assumption
- Device availability must be mapped to clinical workflow, not purchased as a round number
- The right mental model: imagine the organization operating as a fully digital environment end to end

<div class="notes">The field has learned that the gap between assumption and verified reality is where go-live problems live. "We have Wi-Fi everywhere" and "we have enough devices" are statements of belief, not results of assessment. A ward with ten nurses charting simultaneously needs a different device-to-staff ratio than a clinic with sequential patient flow — procurement based on a round number misses this entirely. The mental model is useful: imagine every action that was previously paper-based now requires a device, a connection, and a place to use it, and then ask whether the infrastructure supports that scenario end to end. Only after confirming that baseline should contingency planning begin.</div>

---

## Problem 2 — Solutions

- Conduct a formal infrastructure readiness assessment at the start of implementation, not weeks before go-live
- Map device requirements to clinical workflow: concurrent users, device type, physical location
- Perform a load simulation of the network and EMR under a realistic concurrent-user scenario before go-live
- Walk every clinical area following the workflow from patient arrival to discharge — treat gaps as blockers
- Plan downtime contingencies only after the fully digital baseline is confirmed

<div class="notes">The timing is critical: a readiness assessment conducted weeks before go-live leaves no time to act on what it finds. It must begin at project initiation. The device mapping exercise — for each role and location, how many concurrent users need access, what type of device, and where will it physically be during use — is the practical output. The walkthrough is irreplaceable: following the workflow from patient arrival to discharge through every clinical area, identifying every point where a device, connection, or peripheral is required. Gaps found in this walkthrough are blockers, not post-go-live items. A downtime procedure built on top of an unverified infrastructure baseline is not a contingency — it is a workaround for a problem that should have been fixed.</div>

---

## Problem 3: EMR Implementation Is Treated as a Digitization Project

- The framing mistake: implementation as conversion of paper forms to electronic equivalents
- Success is defined as "what was done on paper is now done on a screen"
- Broken workflows are preserved — only the medium changes
- Inefficiencies become faster, more permanent, and harder to fix

<div class="notes">This is one of the most consequential framing mistakes in EMR implementation. Under the digitization framing, the goal is to replicate what exists in electronic form. The workflow is preserved; only the medium changes. This framing is wrong and produces predictably poor outcomes. An EMR does not digitize work — it changes how work is done, who does it, when it happens, and what information is available at each step. Organizations that do not approach implementation as an organizational transformation arrive at go-live having digitized their existing processes, including the broken ones. The result is not improvement — it is entrenchment.</div>

---

## Problem 3 — Lessons Learned

- Digitizing a broken process does not fix it — it makes it faster, more legible, and more permanent
- The question before implementation is not "how do we do this in the system?" but "should we still be doing this?"
- Process mapping for configuration is documentation of the status quo, not process improvement
- IT teams cannot redesign clinical workflows — transformation requires clinical ownership

<div class="notes">The field has learned this lesson repeatedly. Organizations that build process improvement into the implementation — as a prerequisite to configuration, not a parallel activity — see meaningfully better outcomes. Adoption rates are higher because the system reflects workflows that actually make sense to the people using them. The process mapping distinction is important: mapping done for the purpose of system configuration documents what is currently happening; process improvement asks whether what is currently happening is right. These are different activities requiring different disciplines and different participants. IT teams can document workflows, but they cannot redesign clinical practice.</div>

---

## Problem 3 — Solutions

- Formally declare the EMR project an organizational transformation initiative — not a technology deployment
- Conduct process improvement activities as a prerequisite to system configuration, not a parallel track
- For each workflow: Is this process working? What needs to change? Does the new process work before configuration begins?
- Distinguish workflows to preserve, workflows to redesign, and workflows to eliminate
- Budget process improvement as a distinct workstream with its own timeline, participants, and deliverables

<div class="notes">The practical implication is that process improvement must appear as a named workstream in the project plan with its own budget, timeline, and staffing. If it is not in the plan, it will not happen. The three-question framework is a useful discipline: before any workflow is configured, the team must answer whether the process is working, what needs to change, and whether the new process functions before the system is built around it. Lean methodology, PDSA cycles, and structured workflow redesign sessions all work — the specific method matters less than the commitment to do the work before configuration begins. Assign clinical and operational leads as workflow owners; IT implements what the clinical process requires.</div>

---

## Problem 4: Data Governance and Ownership Are Absent

- Data problems are framed too narrowly as a migration problem
- Before go-live, much more must be built from scratch: order items, formularies, user accounts, provider directories, permissions, templates
- These domains are managed by different departments — each assumes someone else is coordinating with IT
- The result: data submitted late, inconsistently, or not at all

<div class="notes">Data problems in EMR implementation are almost always framed as a migration challenge — moving historical records from the legacy system into the new one. This framing is too narrow. Before an EMR can function on day one, a much broader set of data must be correctly prepared and loaded: order items and order sets, drug formularies, user accounts and role-based permissions, facility structures, provider directories, and clinical templates. None of this is migrated — it is built, curated, and loaded from scratch. And in most implementations, no one owns it. Departments assume someone else is coordinating, and the result is data that arrives late, wrong, or incomplete.</div>

---

## Problem 4 — Lessons Learned

- Data migration is one data problem — data preparation and loading is a separate, larger, more neglected problem
- Every data domain needs a named owner outside of IT with subject matter authority
- Data without an owner has no accountability for accuracy, completeness, or timeliness
- User permissions and access control are data — they require the same governance rigor as clinical content
- Incomplete foundational data forces workarounds on day one that become entrenched practice

<div class="notes">The governance point is foundational. If no one owns the data, no one is accountable for its quality, and it will arrive late, wrong, or both. The data steward model — named individuals with formal responsibility for each master data domain — is the minimum viable governance structure. The user permissions point is often overlooked: access control configuration is not a technical task to be managed informally by IT. It requires a policy, named owners, an approval process, and an audit trail. And the consequence of incomplete foundational data is not a minor inconvenience — it is day-one workarounds that become permanent practice and undermine clinical trust in the system immediately.</div>

---

## Problem 4 — Solutions

- Produce a complete data inventory at project initiation: every domain required for go-live
- Assign a named data owner for each domain — a clinical or operational expert, not IT acting as proxy
- Define data readiness requirements and submission deadlines as formal project milestones
- Establish a user access and permissions policy before any accounts are created
- Conduct data validation reviews with domain owners — a pharmacist verifies the formulary, not IT
- Treat data readiness as a go/no-go criterion

<div class="notes">The data inventory must be completed at project initiation — not when migration begins. It should cover every data domain: order items, order sets, formularies, inventory, user accounts, permissions, provider directories, facility structures, and clinical templates. Each domain gets a named owner with authority over the content. Submission deadlines are project milestones, not administrative conveniences. The validation requirement is critical: IT cannot validate clinical content. A pharmacist must verify the formulary; a charge nurse must verify the order sets. And the go/no-go criterion is the enforcement mechanism — missing or unvalidated foundational data is grounds to delay go-live, not a punch list item to resolve after the fact.</div>

---

## Problem 5: Integration Is Treated as an Afterthought

- Healthcare organizations operate complex multi-system ecosystems
- Integration is assumed to be a vendor-to-vendor problem requiring minimal organizational input
- Scoped late, dependencies poorly documented, testing rushed
- Go-live occurs on a system that does not reliably exchange data with the broader ecosystem

<div class="notes">Integration failures are particularly damaging because they are invisible until they cause harm. A clinician placing an order that does not reach the pharmacy, a lab result that does not flow back to the physician, a billing record that does not transmit — these are not technical inconveniences, they are patient safety and revenue integrity failures. The problem is that integration is treated as plumbing: something the vendors work out between themselves, without requiring significant organizational input. This assumption is wrong. The organization must define what data needs to move, between which systems, in what format, and with what timing. Vendors cannot specify requirements they do not know.</div>

---

## Problem 5 — Lessons Learned

- Integration failures affect patient safety, billing accuracy, and clinical workflow simultaneously
- Organizations must own integration specifications — vendors cannot define them in isolation
- Interface testing requires clinical participation, not just IT sign-off
- Late discovery of integration requirements is a leading cause of delayed go-live

<div class="notes">The lesson is one of ownership. Integration is not a technical problem that lives entirely in the IT department — it is a clinical and operational problem that requires clinical and operational input to specify correctly. IT sign-off on message acknowledgment confirms that a message was sent and received. It does not confirm that the correct data appeared in the right place in a format clinicians can use. Clinical validation catches a different and more consequential class of errors. And the sequencing point is critical: integration dependencies discovered late are a leading cause of delayed go-live, because fixing an interface that was never mapped requires time that the project plan does not have.</div>

---

## Problem 5 — Solutions

- Produce a complete integration map at project initiation: every system, every data exchange, every direction of flow
- Assign an internal integration lead who owns the interface inventory and tracks testing status
- Require vendor contracts to explicitly specify interface delivery timelines and testing responsibilities
- Build integration testing into the UAT cycle with clinical validation scenarios
- Define a minimum viable integration baseline that must be achieved before go-live is authorized

<div class="notes">The integration map is the starting point and must exist before the project plan is finalized, because it determines sequencing, dependencies, and testing timelines. If you do not know all your interfaces at project start, you will discover them at the worst possible time. The internal integration lead role is essential — this person owns the interface inventory, tracks testing status, and escalates dependencies at risk. The minimum viable baseline concept is a useful forcing function: it requires the organization and its vendors to agree in advance on what integration capability is required before go-live is approved, which prevents the pressure of a deadline from overriding unresolved integration failures.</div>

---

## Problem 6: Training Is Under-Invested and Delivered in a Single Modality

- Training is consistently under-resourced and compressed into the final weeks of implementation
- Delivered in a single format — classroom or e-learning — applied uniformly to all staff
- No adjustment for role, learning style, or prior experience with clinical systems
- Unprepared users do not adopt — they avoid, workaround, or resist

<div class="notes">Of all the factors that determine whether an EMR is used well, training has the most direct and measurable impact on adoption. Yet it is consistently the first activity to be compressed when the project runs behind. The logic — that training is near the end and can absorb schedule overruns — misses the point entirely. Training is the primary mechanism through which the organization's workforce learns to use what has been built. Compress it and you arrive at go-live with an under-prepared workforce. The consequences are immediate and lasting: workarounds, errors, frustration, and a lasting association between the system and difficulty that undermines adoption for months.</div>

---

## Problem 6 — Lessons Learned

- Training is the single greatest lever for adoption — investment here has direct, measurable returns
- Single-modality training leaves a substantial portion of users under-prepared
- Training retention degrades with distance from go-live — materials users can return to independently extend its reach
- Go-live itself is a training event — at-the-elbow support during live operation is often the highest-value intervention
- Clinicians and physicians require brief, targeted, peer-led training — not generic system walkthroughs

<div class="notes">The field's evidence on training is consistent: organizations that allocate serious resources to multi-modal, role-specific training programs see measurably faster adoption, lower post-go-live support volume, and higher long-term utilization. The inverse is equally true. Different users learn differently, and a single modality leaves a substantial portion under-prepared — not because the training was bad, but because it was not delivered in a form that worked for them. The clinician and physician finding is particularly important: they have the least tolerance for inefficient training, the most constrained availability, and the highest consequence of poor adoption. They respond to peer demonstration and brief targeted materials, not two-hour classroom sessions.</div>

---

## Problem 6 — Solutions

- Declare training a primary workstream, budgeted and resourced proportionally to its adoption impact
- Design a multi-modal program: self-directed materials, role-based classroom sessions, on-demand training environment
- For physicians and senior clinicians: brief peer-led sessions using their actual workflow
- Staff go-live with dedicated at-the-elbow support — the highest-value training investment for adoption
- Maintain training materials and the training environment post-go-live for new hires and returning staff
- Measure training completion and correlate with adoption metrics by role and department

<div class="notes">The structural fix is to treat training as a workstream with its own dedicated resources, timeline, and leadership — not a phase that follows system build. The multi-modal program combines self-directed learning materials that users can return to independently, scheduled sessions for structured practice on role-specific workflows, and on-demand access to a training environment for practice before and after go-live. For physicians, one-on-one or small-group formats demonstrated by a clinical champion using their actual workflow are significantly more effective than large cohort training. The measurement requirement closes the loop: correlating training completion with adoption metrics by role surfaces under-trained populations before low adoption becomes entrenched.</div>

---

## Problem 7: The Implementation Team Has No Plan for Post-Go-Live Support

- Implementation teams are structured around project delivery milestones
- At go-live, the project is declared complete and the team begins to demobilize
- The highest-intensity support period begins **at go-live**, not before
- Without a stabilization plan, the organization manages a crisis with a departing team

<div class="notes">This is one of the most structurally predictable failures in the entire implementation lifecycle. Project teams are built to deliver — their incentives, their contracts, and their mental models are oriented toward the go-live date. When that date arrives, the natural impulse is to close out the project and move on. But from the organization's perspective, go-live is the beginning of the most demanding period. Users encounter problems they did not anticipate during training. Workflows that functioned in testing break under real clinical volume. Workarounds emerge organically. If the team that built the system is leaving, who is managing this?</div>

---

## Problem 7 — Lessons Learned

- The 30–90 days following go-live are the most critical period in the entire implementation lifecycle
- Issue resolution requires dedicated capacity — it cannot be absorbed by staff returning to full clinical workloads
- Unresolved workarounds harden into permanent practice and undermine system value
- Post-go-live monitoring data should trigger structured response protocols, not ad hoc reactions

<div class="notes">The stabilization period needs to be treated as a distinct phase with its own staffing model, governance structure, and exit criteria. The workaround hardening problem is particularly insidious: when users encounter a problem and no one is available to fix it, they devise a workaround. If that workaround is not identified and resolved within a few weeks, it becomes the way things are done. Six months later, it is invisible infrastructure — everyone does it, no one questions it, and it is working against the system's intended function. The only way to prevent this is rapid, systematic issue identification and resolution during the stabilization window.</div>

---

## Problem 7 — Solutions

- Define a stabilization plan before go-live: staffing model, triage process, escalation path, and exit criteria
- Retain core implementation team members in a support capacity for at least 4–8 weeks post-go-live
- Establish a command center model during the first two weeks after go-live
- Categorize and track issues by severity, affected workflow, and resolution status
- Schedule formal stabilization reviews at 30, 60, and 90 days with leadership involvement

<div class="notes">The stabilization plan must be written before go-live — not improvised after it. It should specify who is available and when, how issues are logged and triaged, what the escalation path is for critical failures, and what criteria define the end of the stabilization period. The command center model during the first two weeks is particularly effective: a dedicated space where implementation leads, IT, and clinical champions are co-located and responsive. Issues get resolved in hours rather than days. The 30-60-90 review cadence creates leadership accountability: someone must report on stabilization progress at regular intervals, which keeps the organization from treating post-go-live as someone else's problem.</div>

---

## Problem 8: Clinician Resistance Is Treated as an Administrative Problem

- Physician resistance is framed as individual non-compliance or failure to adapt
- Resolved through policy enforcement or administrative pressure
- This framing misses the underlying causes:
    - Inadequate involvement in system design
    - Insufficient training and loss of workflow efficiency
    - Legitimate concerns about patient safety or data quality that go unheard

<div class="notes">Clinician resistance to EMR adoption is one of the most commonly cited implementation challenges and one of the most consistently mismanaged. The administrative reflex — mandate compliance, enforce the policy — is understandable because it appears to produce results. Clinicians use the system because they have to. What it produces, however, is not adoption. It is compliance without ownership. Clinicians who have been mandated into a system they distrust will find ways to use it minimally, incorrectly, or in ways that create documentation that is technically complete but clinically useless. The underlying causes of resistance — which are often legitimate — go unaddressed.</div>

---

## Problem 8 — Lessons Learned

- **Resistance is information** — it signals that the implementation has failed to address clinician concerns
- Physicians involved in EMR design are significantly more likely to adopt and champion the system
- Administrative mandates force compliance but do not produce adoption
- Compliance without adoption produces workarounds, unsafe shortcuts, and hostility toward the system
- Clinical champions — respected peers — are more effective than any administrative communication

<div class="notes">The reframe is important: resistance is not a behavior problem, it is a signal that the implementation has failed to adequately address the concerns of the people being asked to change. When a physician says the system slows them down, they are usually right. When they say it does not match their clinical workflow, they are telling you something actionable. Organizations that treat resistance as information and engage with it systematically — involving resisters in redesign, demonstrating responsiveness to feedback, using clinical champions who have credibility with peers — achieve adoption outcomes that mandate-driven implementations never reach.</div>

---

## Problem 8 — Solutions

- Involve physicians and clinical staff in workflow design, configuration, and testing from the start
- Create a clinical advisory group with **real decision-making authority** over clinically relevant configuration
- Design physician-specific training that addresses efficiency concerns directly
- Establish a formal feedback channel for clinicians with visible follow-through on issues raised
- Identify and invest in clinical champions early — their peer credibility is irreplaceable

<div class="notes">The solutions here are about genuine involvement versus performative consultation. A clinical advisory group that meets monthly to hear updates is not a decision-making body — it is an audience. A clinical advisory group with authority to approve or reject configuration choices is a fundamentally different thing. The feedback channel is equally important: clinicians will raise concerns once or twice. If they see no action, they stop raising them and start working around the system. Visible follow-through — publishing what was raised, what was done, and why — demonstrates that the organization takes clinical input seriously. This is how you build the trust that makes adoption possible.</div>

---

## Problem 9: Post-Implementation Review Is Absent, Superficial, or Not Acted Upon

- Reviews are conducted as a formality — reports produced then filed without generating corrective action
- More often, no structured review occurs at all
- Implementation teams disperse and institutional knowledge about what worked and what failed is lost
- The same mistakes recur in subsequent phases and future implementations

<div class="notes">The post-implementation review is the organization's opportunity to learn from the experience it has just completed. It is also one of the most consistently skipped or superficial activities in the entire lifecycle. The reasons are understandable: teams are exhausted, leadership attention has shifted, and no one wants to conduct a structured review on a project that was declared successful. But the cost of skipping this review is paid in the next implementation, when the same avoidable mistakes recur. In large health systems with multi-phase rollouts, that next implementation may begin within months of the last.</div>

---

## Problem 9 — Lessons Learned

- Honest review requires **psychological safety** — staff will not report failures candidly if doing so carries personal risk
- Reviews focused only on technical delivery miss the most important dimensions: adoption, workflow impact, and user experience
- Findings without owners and deadlines do not produce change
- Lessons must be institutionalized — captured in a form accessible to future project teams, not buried in a final report

<div class="notes">The psychological safety point is foundational. If the review process is perceived as a blame exercise, participants will manage their exposure rather than provide honest assessments. This means the most important lessons — the failures that were known internally but not escalated — will never surface. The facilitator choice matters: someone perceived as neutral, with no stake in the project's political outcomes, will elicit more candid input than a project sponsor or vendor representative. The finding-ownership requirement is the difference between a review that generates change and one that generates documentation. Every finding needs a name, a corrective action, and a date. Without those three things, the review is a formality.</div>

---

## Problem 9 — Solutions

- Schedule post-implementation reviews at 30, 90, and 180 days post-go-live — not as a single end-of-project event
- Use structured tools: surveys, focus groups, system usage analytics, and ticket analysis
- Assign a neutral facilitator for review sessions to reduce defensiveness and encourage candid input
- Require each finding to have a named owner, a corrective action, and a completion date before the review is closed
- Create a lessons-learned repository that is referenced at the start of each new implementation or phase

<div class="notes">The timing structure — 30, 90, 180 days — reflects the reality that different problems surface at different points. The 30-day review captures acute stabilization failures. The 90-day review captures workflow and adoption trends. The 180-day review captures whether the system is delivering on its original value proposition. The lessons-learned repository is the long-term payoff: it is the organization's institutional memory of what was tried, what worked, and what failed. If it is referenced at the start of every new phase, it prevents the organization from reinventing the same wheel — or repeating the same mistakes — indefinitely.</div>

---

## Problem 10: Go-Live Is Treated as the Finish Line

- Organizational attention, resources, and leadership focus peak at go-live then rapidly decline
- The system is declared implemented — the project is closed
- But the organization has not yet realized the value the implementation was meant to deliver
- Adoption — consistent, effective use — takes months or years to mature

<div class="notes">The finish line problem is perhaps the most pervasive framing failure in EMR implementation. Go-live is a milestone, not an outcome. The outcome is a workforce that uses the system consistently and effectively to deliver better, safer, more efficient care. That outcome is not achieved on go-live day — it is achieved through months of sustained attention, measurement, and management. Organizations that close the project at go-live and redirect leadership attention to the next initiative discover, often years later, that they are operating a system at a fraction of its potential, carrying the full cost of ownership while realizing a fraction of the benefit.</div>

---

## Problem 10 — Lessons Learned

- **Implementation is not adoption** — a system can be fully deployed and deeply underused simultaneously
- Adoption requires the same structured management attention as implementation: goals, metrics, accountability, and iteration
- ROI from an EMR is directly tied to the depth of adoption
- Adoption barriers that emerge post-go-live must be surfaced through measurement and addressed systematically

<div class="notes">The implementation-versus-adoption distinction is one of the most important contributions from the field. Deployment is a technical achievement. Adoption is a behavioral and organizational one. They require different skills, different metrics, and different management approaches. The ROI connection is direct and empirically supported: organizations that measure and manage adoption consistently outperform those that treat go-live as the endpoint. The measurement point is the key mechanism: you cannot manage adoption you cannot see. Without adoption metrics — documentation rates, order-entry rates, alert-acknowledgment rates — the organization is operating blind, and problems are invisible until they become crises.</div>

---

## Problem 10 — Solutions

- Define adoption metrics **before go-live**: documentation completion rates, order-entry rates, alert-acknowledgment rates, utilization by module and role
- Build an adoption dashboard visible to leadership, department heads, and implementation leads
- Assign adoption accountability to operational leaders — not IT — for each department or clinical unit
- Conduct adoption reviews at 3, 6, and 12 months post-go-live with specific improvement targets
- Tie optimization investments directly to adoption data — not anecdote

<div class="notes">The adoption dashboard is the operationalization of everything we have discussed. It makes adoption visible, creates accountability, and provides the data needed to target optimization investments. The ownership assignment — operational leaders, not IT — reflects the reality that adoption is a people and process problem, not a technology problem. IT can support and analyze, but the accountability for whether a nursing unit is using the system effectively belongs to that unit's operational leader. When the dashboard shows low utilization in a particular module, that is the trigger for investigation and targeted intervention — not a general complaint or an anecdote from a department head.</div>

---

## Key Takeaways

- Selection must be grounded in scenario-level requirements — feature categories are insufficient
- Infrastructure readiness must be formally assessed against the post-go-live operating scenario
- EMR implementation is organizational transformation — process improvement must precede configuration
- Data governance and integration require named ownership and early, dedicated investment
- Training is the primary lever for adoption and cannot be compressed without consequence
- Post-go-live stabilization, clinician engagement, structured review, and adoption management are not optional

<div class="notes">Let us bring it together. Across all ten problems, a few consistent themes emerge. First, the planning horizon matters enormously: problems that appear at go-live almost always have roots in decisions made months earlier — in selection, in process design, in data governance, in training planning. Second, organizational and human factors are consistently more determinative than technical ones. The systems work. The challenge is getting organizations and their people to use them well. And third, the finish line is not go-live — it is measurable, sustained adoption. Organizations that internalize that distinction consistently outperform those that treat implementation as a project with an end date. These lessons are learnable. The field has paid for them many times over.</div>

---

## Summary: The 10 Problems

- Selection evaluated at feature category level, not scenario-level workflow requirements
- IT and infrastructure readiness never formally assessed against the post-go-live scenario
- Implementation treated as a digitization project, not an organizational transformation
- Data governance and ownership absent across all data domains required for go-live
- Integration treated as an afterthought, delegated entirely to vendors
- Training under-invested and delivered in a single modality
- No plan for post-go-live support and stabilization
- Clinician resistance treated as an administrative problem, not a change management failure
- Post-implementation review absent, superficial, or not acted upon
- Go-live treated as the finish line — adoption never formally measured or managed

<div class="notes">Here are all ten problems in one view. Taken together, they reveal a consistent pattern: failures that appear at go-live almost always originate in decisions made months earlier — in how the system was selected, how the organization prepared, and how much was invested in the human and process dimensions of the project. No single problem is isolated. Weak selection leads to a poor fit; poor process improvement leads to entrenched workarounds; under-resourced training leads to low adoption; and treating go-live as the finish line means none of the earlier investments ever fully pay off. The goal in naming them this explicitly is to make them preventable, not inevitable.</div>

---

## Summary: The 10 Solutions

- Define scenario-level requirements; require vendor demos against your actual workflow list
- Conduct a formal infrastructure readiness assessment at project start; treat gaps as blockers
- Declare an organizational transformation initiative; conduct process improvement before configuration
- Assign named data owners for every domain; treat data readiness as a go/no-go criterion
- Produce a complete integration map at initiation; assign an internal integration lead
- Build training as a primary workstream; deliver multi-modal, role-specific programs
- Define a stabilization plan before go-live; retain core team for 4–8 weeks post-go-live
- Involve clinicians in design from the start; create a clinical advisory group with real authority
- Schedule structured reviews at 30, 90, and 180 days; require findings to have named owners
- Define adoption metrics before go-live; assign accountability to operational leaders, not IT

<div class="notes">And here are the corresponding solutions. Each one is actionable and can be built into a project plan, a contract, or a governance policy. The common thread across all ten is that the solution requires deliberate, early action — not a last-minute fix under go-live pressure. Selection rigor, infrastructure assessment, transformation framing, data governance, integration ownership, training investment, stabilization planning, clinician engagement, post-implementation review, and adoption management all demand that the organization decide, in advance, to do the work. The organizations that do consistently outperform those that do not. This is learnable. Start early.</div>

---

## Thank You

- Questions, discussion, and reflection welcome
- Contact: [presenter contact information]
- These materials are available for reference and adaptation

<div class="notes">Thank you for your time and attention today. The ten problems we covered are not inevitable — they are patterns that repeat because organizations do not always have access to the lessons the field has already learned. Sharing this knowledge is how we raise the baseline for every implementation that follows. I am happy to take questions on any of the problems we covered, or to discuss how any of these patterns might apply to work you are currently doing. Please feel free to take these materials and adapt them for your own teams and contexts.</div>
