# 10 Problems and Lessons Learned from the Field

## Here are the 10 Problems:

1. EMR selection is evaluated at the wrong level of specificity — feature categories instead of concrete workflow requirements
2. IT and infrastructure readiness is never formally assessed against the post-go-live operating scenario
3. EMR implementation is treated as a digitization project, not an organizational transformation
4. Data governance and ownership are absent across all data that must be prepared, loaded, and maintained in the EMR
5. Integration is treated as an afterthought, delegated entirely to vendors with no internal ownership
6. Training is under-invested and delivered in a single modality, despite being the primary driver of adoption success
7. The implementation team has no plan for post-go-live support and stabilization
8. Clinician resistance is treated as an administrative problem rather than a change management failure
9. Post-implementation review is absent, superficial, or not acted upon
10. Go-live is treated as the finish line — adoption is never formally measured or managed

---

## Problem 1: EMR Selection Is Evaluated at the Wrong Level of Specificity

**The Problem:**
Selection committees evaluate EMR systems using high-level feature category labels — CPOE, Clinical Documentation, Medication Management — rather than specific, concrete workflows the organization needs to support. A vendor who answers "yes" to "does your system support CPOE?" has answered a question that carries almost no useful information. What the hospital actually needs to know is whether a resident can place a standing vital signs order for every 4 hours, whether a nurse can acknowledge and document compliance against that order, or whether a resident can save a draft discharge summary for an attending to review and finalize at the point of discharge. These are not sub-features of CPOE or Clinical Documentation — they are the actual clinical requirements that will determine whether the system works in practice. When evaluation stops at the category level, critical gaps go undetected until after go-live.

**Lessons Learned:**
- Feature category names ("CPOE," "eMar," "Clinical Documentation") describe module presence, not workflow capability. Two systems can both have CPOE and behave completely differently for the same clinical task.
- Vendors answer high-level questions with "yes" in good faith — the question was too vague to produce a meaningful answer. The failure is in the question, not the response.
- The people who know what specific capabilities are needed are clinicians and operational staff — not procurement teams or IT leads working from a generic RFP template.
- Gaps discovered during implementation are expensive. Gaps discovered after go-live are worse. Gaps that were never articulated as requirements cannot be evaluated at all.

**Solutions:**
- Before vendor evaluation begins, convene clinical and operational staff to produce a list of specific, scenario-level requirements: not "supports order entry" but "nurse can acknowledge a standing order and document the result against a specific order instance."
- Structure vendor demonstrations around your scenario list, not the vendor's standard demo script. Ask vendors to show — not describe — how each scenario works in their system.
- Use a scripted demonstration scorecard: for each scenario, record whether it was demonstrated, how many steps it required, and whether it matched the clinical expectation.
- Treat any requirement answered with "that can be configured" or "that would be addressed in implementation" as unverified. Require working demonstrations or written contractual commitments.
- Require reference calls with organizations that use the same modules for the same clinical scenarios — not general satisfaction references.

---

## Problem 2: IT and Infrastructure Readiness Is Never Formally Assessed Against the Post-Go-Live Operating Scenario

**The Problem:**
Implementation projects assume that the existing network, devices, and physical environment are adequate for EMR operation. No one formally tests this assumption against the actual post-go-live scenario — the specific mix of users, locations, workflows, and simultaneous system load that the organization will face on day one. Network coverage gaps in wards, procedure rooms, and patient bays go undetected. Device counts are based on procurement budgets rather than workflow analysis. Printers, label makers, barcode scanners, and workstations-on-wheels are placed without reference to where clinical tasks actually occur. These deficiencies are invisible during implementation and surface only after go-live, where they manifest as slow adoption, workflow workarounds, and, in serious cases, system failure under clinical load.

**Lessons Learned:**
- Infrastructure problems discovered after go-live are far more disruptive than problems discovered during planning — the clinical environment is live, staff are under pressure, and remediation competes directly with patient care.
- "We have Wi-Fi everywhere" and "we have enough devices" are not readiness assessments. They are assumptions. The gap between assumption and verified reality is where go-live problems live.
- Device availability must be mapped to clinical workflow, not purchased as a round number. A ward with ten nurses charting simultaneously needs a different device-to-staff ratio than a clinic with sequential patient flow.
- The right mental model for infrastructure readiness is to imagine the organization operating as a fully digital environment — every action that was previously paper-based now requires a device, a connection, and a place to use it — and then assess whether the infrastructure supports that scenario end to end. Only after that baseline is confirmed should contingency planning for downtime and failure begin.

**Solutions:**
- Conduct a formal infrastructure readiness assessment at the start of implementation, not in the weeks before go-live. Cover network coverage (signal strength, redundancy, and load capacity by location), device inventory (type, count, placement, and condition), printing and peripheral logistics, and physical workspace for device use at point of care.
- Map device requirements to clinical workflow: for each role and each location, identify how many concurrent users will need access, what type of device is appropriate, and where the device will physically be during use.
- Perform a load simulation or stress test of the network and EMR under a realistic concurrent-user scenario before go-live is authorized.
- Walk every clinical area with the go-live scenario in mind — follow the workflow from patient arrival to discharge and identify every point where a device, connection, or peripheral is required. Treat gaps as blockers, not post-go-live items.
- Plan downtime contingencies only after the fully digital baseline is confirmed. A downtime procedure built on top of an unverified infrastructure baseline is not a contingency — it is a workaround for a problem that should have been fixed.

---

## Problem 3: EMR Implementation Is Treated as a Digitization Project, Not an Organizational Transformation

**The Problem:**
The most consequential framing mistake in EMR implementation is treating the project as a digitization effort — the conversion of paper-based forms and manual steps into their electronic equivalents. Under this framing, success means that what was done on paper is now done on a screen. The workflow is preserved; only the medium changes. This framing is wrong, and it produces predictably poor outcomes. An EMR does not digitize work — it changes how work is done, who does it, when it happens, and what information is available at each step. Organizations that do not approach implementation as an organizational transformation project arrive at go-live having digitized their existing processes, including the broken ones. The result is not improvement — it is entrenchment. Inefficiencies that were slow and paper-based become fast and electronic. Workarounds that were informal become embedded in system configuration. Problems that were visible become invisible behind a green checkmark.

Process improvement must precede digitalization, not follow it. If the process is broken before the EMR, it will be broken after — and harder to fix because it is now baked into system logic, user habits, and vendor configuration.

**Lessons Learned:**
- Digitizing a broken process does not fix it. It makes it faster, more legible, and more permanent.
- The question before implementation is never "how do we do this in the system?" — it is "should we still be doing this at all, and if so, how should it work?"
- Process mapping done for the purpose of system configuration is documentation of the status quo, not process improvement. These are different activities requiring different disciplines and different participants.
- Organizations that complete process improvement work before system configuration achieve higher adoption rates, fewer post-go-live workarounds, and more of the system's intended value.
- IT teams cannot redesign clinical workflows. Transformation requires clinical and operational ownership with IT as an enabling function, not the lead.

**Solutions:**
- Formally declare the EMR project an organizational transformation initiative from the outset — not a technology deployment. Communicate this framing explicitly to leadership, department heads, and clinical staff.
- Conduct process improvement activities (Lean, PDSA, value stream mapping, or equivalent) as a prerequisite to system configuration, not a parallel track or a post-go-live activity.
- For each workflow to be implemented, ask three questions before configuration begins: Is this process working? If not, what needs to change? Does the new process work before the system is built around it?
- Distinguish explicitly between workflows to preserve, workflows to redesign, and workflows to eliminate. Configuration of the system should follow those decisions, not drive them.
- Assign clinical and operational leads — not IT — as owners of workflow redesign. IT's role is to implement what the clinical process requires, not to determine what the process should be.
- Budget process improvement as a distinct, resourced workstream in the project plan with its own timeline, participants, and deliverables.

---

## Problem 4: Data Governance and Ownership Are Absent Across All Data That Must Be Prepared, Loaded, and Maintained in the EMR

**The Problem:**
Data problems in EMR implementation are almost always framed as a migration problem — the challenge of moving historical patient records from a legacy system into the new one. This framing is too narrow. Before an EMR can function on day one, a much broader set of data must be correctly prepared and loaded into the system: order items and order sets, drug formularies, inventory lists, diagnosis and procedure code sets, user accounts and role-based permissions, facility and department structures, provider directories, care protocols, and clinical templates. None of this is migrated from a legacy system — it is built, curated, and loaded from scratch. And in most implementations, no one owns it.

These data domains are managed by different departments — pharmacy owns the formulary, nursing owns order sets, HR owns user accounts, supply chain owns inventory — and each department assumes that someone else is coordinating with IT. The result is that data is submitted late, submitted inconsistently, or not submitted at all. Permissions are configured without a governing policy. Order sets are loaded in draft form and never finalized. Inventory is incomplete at go-live. These are not technical failures — they are data governance failures, and they are entirely preventable.

**Lessons Learned:**
- Data migration is one data problem. Data preparation and loading is a separate, larger, and more commonly neglected data problem.
- Every data domain required for go-live needs a named owner outside of IT — the person with subject matter authority over that data, not just the person who can enter it into a spreadsheet.
- Data without an owner has no accountability for accuracy, completeness, or timeliness. It will arrive late, wrong, or both.
- User permissions and access control are data. They require the same governance rigor as clinical content — a policy, an owner, an approval process, and an audit trail.
- Incomplete or incorrect foundational data — wrong formulary, missing order items, broken permission sets — forces workarounds on day one that become entrenched practice.

**Solutions:**
- Produce a complete data inventory at project initiation: every data domain required for go-live, including order items, order sets, formularies, inventory, user accounts, permissions, provider directories, facility structures, and clinical templates.
- Assign a named data owner for each domain — a clinical or operational subject matter expert with authority over the content, not an IT team member acting as a proxy.
- Define data readiness requirements and submission deadlines for each domain as formal project milestones. Late data is a project risk, not an administrative inconvenience.
- Establish a user access and permissions policy before any accounts are created. Define roles, access levels, and the approval workflow — do not configure permissions ad hoc under go-live pressure.
- Conduct data validation reviews with the domain owners, not just IT. A pharmacist must verify the formulary. A charge nurse must verify the order sets. IT cannot validate clinical content.
- Treat data readiness as a go/no-go criterion. Missing or unvalidated foundational data is grounds to delay go-live, not a punch list item to resolve after the fact.

---

## Problem 5: Integration Is Treated as an Afterthought, Delegated Entirely to Vendors

**The Problem:**
Healthcare organizations operate complex ecosystems of systems — laboratory, radiology, pharmacy, billing, scheduling, and external referral networks. EMR implementation plans often treat integration as a vendor-to-vendor problem, assuming that interface engines and HL7 connections will be configured without requiring significant organizational input. Integration is scoped late, dependencies are poorly documented, and testing is rushed. The result is go-live on a system that does not reliably exchange data with the broader ecosystem, forcing manual workarounds that persist for months.

**Lessons Learned:**
- Integration failures are among the most damaging post-go-live problems because they affect patient safety, billing accuracy, and clinical workflow simultaneously.
- Vendors cannot define integration requirements in isolation — the organization must own the specification of what data needs to move, between which systems, in what format, and with what timing.
- Interface testing requires clinical participation, not just IT sign-off. Clinicians need to validate that the correct data appears in the right place.
- Integration dependencies must be mapped and sequenced early; late discovery of integration requirements is a leading cause of delayed go-live.

**Solutions:**
- Produce a complete integration map at project initiation: every system, every data exchange, every direction of flow.
- Assign an internal integration lead who owns the interface inventory and tracks testing status.
- Require vendor contracts to explicitly specify interface delivery timelines and testing responsibilities.
- Build integration testing into the UAT cycle with clinical validation scenarios, not just technical message acknowledgment.
- Define a minimum viable integration baseline that must be achieved before go-live is authorized.

---

## Problem 6: Training Is Under-Invested and Delivered in a Single Modality, Despite Being the Primary Driver of Adoption Success

**The Problem:**
Of all the factors that determine whether an EMR is actually used well, training has the most direct and measurable impact on adoption. Yet it is consistently under-resourced, compressed into the final weeks of implementation, and delivered in a single format — typically a classroom session or an e-learning module — applied uniformly to all staff regardless of role, learning style, or prior experience with clinical systems. When training fails to reach users in a way they can absorb and apply, they arrive at go-live unprepared. Unprepared users do not adopt — they avoid, workaround, or resist.

The investment required to train well is substantial, and it shows. Organizations that allocate serious resources to multi-modal, role-specific training programs see measurably faster adoption, lower post-go-live support volume, and higher long-term utilization. The inverse is equally true: no amount of system quality, workflow redesign, or infrastructure readiness compensates for a workforce that does not know how to use the system. Training is not a delivery milestone — it is the primary mechanism through which implementation value is realized.

Clinicians and physicians are a specific concern. They have the least tolerance for inefficient training, the most constrained availability, and the highest consequence of poor adoption. They also learn in ways that differ from administrative staff — they respond to peer demonstration, brief targeted materials they can reference independently, and direct application to their own workflows. A single mandatory classroom session is unlikely to meet any of those needs.

**Lessons Learned:**
- Training is the single greatest lever for adoption. The more thoroughly and accessibly training is delivered, the better the adoption outcome — particularly for clinical and physician users.
- Different users learn differently. A nurse who learns best by doing will not retain a lecture. A physician who has ten minutes between patients will not sit through a two-hour module. Training design must account for this.
- Single-modality training leaves a substantial portion of users under-prepared. Not because the training was bad, but because it was not delivered in a form that worked for them.
- Training retention degrades with distance from go-live. Materials and formats that users can return to independently — videos, quick reference guides, searchable manuals — extend the effective life of training far beyond what any classroom session can.
- Go-live itself is a training event. Hands-on support during the first days of live operation — at the point of care, in real workflows, with real patients — is often more effective than any pre-go-live session.

**Solutions:**
- Declare training a primary workstream of the implementation, budgeted and resourced proportionally to its impact on adoption — not a compressed final phase.
- Design a multi-modal training program that combines: self-directed learning materials (written manuals, workflow quick-reference cards, short task-specific videos); scheduled classroom or small-group sessions for structured practice on role-specific workflows; and on-demand access to a training environment where users can practice independently before and after go-live.
- For physicians and senior clinicians, prioritize brief, targeted, peer-led sessions — ideally demonstrated by a clinical champion using their actual workflow — over generic system walkthroughs. One-on-one or small-group formats are significantly more effective than large cohort training for this group.
- Staff go-live with dedicated at-the-elbow support: trained individuals present on the floor, in the unit, or at the workstation to provide real-time guidance during the first days of live operation. This is the highest-value training investment relative to adoption impact.
- Maintain training materials and the training environment post-go-live. New hires, returning staff, and users who struggled at go-live need ongoing access to learning resources — not just a recording of the pre-go-live session.
- Measure training completion and correlate it with adoption metrics by role and department. Use that data to identify under-trained populations and intervene before low adoption becomes entrenched.

---

## Problem 7: The Implementation Team Has No Plan for Post-Go-Live Support and Stabilization

**The Problem:**
Implementation teams are typically structured around project delivery milestones. When go-live arrives, the project is considered complete, and the team begins to demobilize. However, the highest-intensity support period begins at go-live, not before it. Users encounter problems they did not anticipate during training. Workflows that functioned in testing break under real clinical volume. Workarounds emerge organically and become entrenched. Without a structured stabilization plan, the organization is left managing a crisis with a team that has already moved on.

**Lessons Learned:**
- The 30 to 90 days following go-live are the most critical period in the entire implementation lifecycle.
- Issue logging, triage, and resolution require dedicated capacity — it cannot be absorbed by staff already returning to full clinical workloads.
- Unresolved workarounds harden into permanent practice and undermine the value of the new system.
- Post-go-live monitoring data (error rates, ticket volumes, downtime events) should trigger structured response protocols rather than ad hoc reactions.

**Solutions:**

- Define a stabilization plan before go-live: staffing model, issue triage process, escalation path, and exit criteria for stabilization.
- Retain core implementation team members in a support capacity for at least 4–8 weeks post-go-live.
- Establish a command center or war room model during the first two weeks after go-live to enable rapid issue resolution.
- Categorize and track issues by severity, affected workflow, and resolution status — do not manage post-go-live issues informally.
- Schedule a formal stabilization review at 30, 60, and 90 days post-go-live with leadership involvement.

---

## Problem 8: Clinician Resistance Is Treated as an Administrative Problem Rather Than a Change Management Failure

**The Problem:**
Physician and clinician resistance to EMR adoption is one of the most commonly cited implementation challenges and one of the most consistently mismanaged. It is frequently framed as individual non-compliance, stubbornness, or failure to adapt — problems to be resolved through policy enforcement or administrative pressure. This framing misses the underlying causes: inadequate involvement in system design, insufficient training, loss of workflow efficiency, perceived threats to clinical autonomy, and legitimate concerns about patient safety or data quality that go unheard.

**Lessons Learned:**
- Resistance is information. It signals that the implementation has failed to adequately address the concerns, workflows, or autonomy of the people being asked to change.
- Physicians who are involved in EMR design and configuration are significantly more likely to adopt and champion the system.
- Administrative mandates may force compliance, but do not produce adoption. Compliance without adoption produces workarounds, unsafe shortcuts, and hostility toward the system.
- Clinical champions — respected peers who genuinely use and advocate for the system — are more effective than any administrative communication.

**Solutions:**
- Involve physicians and clinical staff in workflow design, configuration decisions, and testing from the beginning of the project.
- Create a clinical advisory group with real decision-making authority over clinically relevant configuration choices.
- Design physician-specific training that addresses efficiency concerns directly — demonstrate that the system can be used without extending clinical time.
- Establish a formal feedback channel for clinicians to raise concerns during and after go-live, with visible follow-through on issues raised.
- Identify and invest in clinical champions early; their credibility with peers is irreplaceable.

---

## Problem 9: Post-Implementation Review Is Absent, Superficial, or Not Acted Upon

**The Problem:**
Organizations that conduct post-implementation reviews often do so as a formality — a report produced for leadership that documents what went well and what did not, then filed without generating corrective action. More commonly, no structured review occurs at all. The implementation team disperses, the project closes, and institutional knowledge about what worked and what failed is lost. The same mistakes recur in subsequent phases or in future implementations at other sites.

**Lessons Learned:**
- Honest post-implementation review requires psychological safety. Staff will not report failures candidly if doing so carries personal risk.
- Reviews that focus only on technical delivery miss the most important dimensions: adoption rates, workflow impact, clinical outcomes, and user experience.
- Findings without owners and deadlines do not produce change. A review that does not assign corrective actions is documentation, not improvement.
- Lessons learned must be institutionalized — captured in a form that is accessible to future project teams, not buried in a final project report.

**Solutions:**
- Schedule post-implementation reviews at 30, 90, and 180 days post-go-live — not as a single event after stabilization.
- Use structured tools: surveys, focus groups, system usage analytics, and ticket analysis to generate objective findings alongside qualitative feedback.
- Assign a neutral facilitator for review sessions to reduce defensiveness and encourage candid input.
- Require each finding to have a named owner, a corrective action, and a completion date before the review is closed.
- Create a lessons-learned repository that is referenced at the start of each new implementation or phase.

---

## Problem 10: Go-Live Is Treated as the Finish Line — Adoption Is Never Formally Measured or Managed

**The Problem:**
In most EMR implementations, organizational attention, project resources, and leadership focus peak at go-live and then rapidly decline. The system is declared implemented. The project is closed. Contracts are settled. However, the organization has not yet recognized the value the system was implemented to deliver. Adoption — consistent, effective use of the system in clinical and operational workflows — takes months or years to mature. Without sustained attention, measurement, and management, adoption plateaus well below its potential, and the organization carries the cost of a system it is only partially using.

**Lessons Learned:**
- Implementation is not adoption. A system can be fully deployed and deeply underused simultaneously.
- Adoption requires the same structured management attention as implementation — goals, metrics, accountability, and iteration.
- The return on investment from an EMR is directly tied to the depth of adoption. Organizations that measure and manage adoption consistently outperform those that do not.
- Adoption barriers that emerge post-go-live — workflow friction, data quality issues, training gaps — must be surfaced through measurement and addressed systematically.

**Solutions:**
- Define adoption metrics before go-live: documentation completion rates, order-entry rates, alert-acknowledgment rates, and system utilization by module and role.
- Build an adoption dashboard visible to leadership, department heads, and implementation leads.
- Assign adoption accountability to operational leaders — not IT — for each department or clinical unit.
- Conduct regular adoption reviews at 3, 6, and 12 months post-go-live with specific improvement targets.
- Tie optimization investments (additional training, workflow redesign, configuration changes) directly to adoption data rather than anecdote.
