# EMR Implementation Problems: Field Cases

> These are not hypotheticals. These are Tuesday afternoons.

---

## Pre-Implementation

---

### P-01 · Executive sponsor vanishes when pressure mounts

**The problem:** Leadership signs off on the project, appears at the kickoff, then delegates everything to a mid-level IT manager who has no authority to make clinical decisions. When hard trade-offs arrive — cut a feature or miss go-live — nobody with decision-making power is in the room.

**Field case:** A 400-bed regional hospital in the Visayas designated their CIO as executive sponsor. Three months into the project, the CIO was reassigned to oversee a new facility build. The replacement had no context on the EMR initiative and refused to approve any scope changes without "a full briefing" — a briefing that was scheduled and rescheduled four times over six weeks. The go-live date slipped by three months. The vendor charged for the extension. Nobody had anticipated this in the contract.

**Lesson learned:** Designate a named decision-making authority with a documented successor before project kickoff. Executive sponsors should be formally committed to the project duration — or at minimum, successor protocols should be built into the project charter, including a structured handoff process that includes vendor notification and a scope re-confirmation meeting.

---

### P-02 · Broken workflows get digitized, not fixed

**The problem:** The organization assumes the EMR will fix their process problems. Instead, the system faithfully replicates every inefficiency, paper redundancy, and approval bottleneck — now at electronic speed.

**Field case:** A tertiary hospital's nursing station had a manual process where ward nurses filled out a paper medication reconciliation form, handed it to a senior nurse, who then re-encoded it into a separate system. During EMR build, nobody questioned this. The workflow was replicated: nurses filled a digital form, submitted it to a queue, and a senior nurse re-encoded into the pharmacy module. Two data entry points for one action. Medication discrepancies actually increased post-go-live because neither record was considered authoritative.

**Lesson learned:** Map every workflow before build begins. Convene a cross-functional process review team — clinicians, operations, and IT together — and challenge every step. If a step exists only to compensate for a flaw in a previous step, eliminate the flaw. The EMR build phase is the last practical opportunity to redesign; once a workflow is live, changing it requires re-training an entire department.

---

### P-03 · Vendor demo environment bears no resemblance to live operations

**The problem:** The procurement decision is made based on a polished demo with curated data, pre-configured workflows, and a system running on hardware that does not reflect the hospital's actual infrastructure.

**Field case:** A hospital selected an EMR after a vendor demo showed sub-second response times on the physician order entry screen. Post-contract, when the system was installed on the hospital's existing on-premise servers — which the vendor had been told about — response times averaged 8–12 seconds per page load. The vendor's position was that the hospital's hardware did not meet "recommended" specs. The word "recommended" had never appeared in any pre-sales conversation. A server refresh added ₱4.2M to the project budget four months before go-live.

**Lesson learned:** Require the vendor to provide written, signed performance benchmarks tied to your specific infrastructure profile during procurement — not after contract signing. If they will not commit to benchmarks on your hardware, treat that as a selection disqualifier. Minimum acceptable response times for core clinical screens belong in the contract as acceptance criteria, with remedies defined if they are not met in production.

---

### P-04 · Legacy data is migrated without validation

**The problem:** Patient records from the old system are moved to the new one without a structured data profiling and clinical validation phase. Garbage in, garbage out — but now the garbage has a clinical interface on top of it.

**Field case:** During migration from a legacy HIS to a new EMR, a hospital's data team moved 11 years of patient records. Post-migration audit (done only after several physicians reported anomalies) revealed that approximately 14% of patient allergy records had been mapped to the wrong patient due to a surname collision issue in the legacy system's patient matching logic. The MPI had never enforced unique identifiers. Three patients received orders that should have triggered allergy alerts — but didn't, because the allergy was on a different chart.

**Lesson learned:** Data migration is a clinical project, not an IT project. Mandate a pre-migration data quality assessment, a post-migration validation report signed off by a clinician, and a reconciliation period before decommissioning the legacy system. Patient safety data — allergies, active medications, critical diagnoses — must be validated record by record by a clinical reviewer, not just checked for row counts.

---

### P-05 · Infrastructure gaps discovered on go-live week

**The problem:** Network coverage, device availability, and physical logistics are assumed to be adequate. They are not checked until the system is live and staff are trying to use it.

**Field case:** A hospital's surgical ward was the last floor added to the EMR rollout scope, late in the project. On go-live day, nursing staff discovered that two of the six bedside workstations had no working network connection — the data ports had never been patched to the switch. Wireless coverage in the far end of the ward dropped to unusable levels. Nurses documented on paper for 11 days while the facilities team ran cable. During those 11 days, two medication administration records were lost.

**Lesson learned:** Treat infrastructure readiness as a go/no-go criterion, not a background task. Conduct a floor-by-floor physical walkthrough with the networking team at least 60 days before go-live. Every workstation, network port, wireless access point, and printer must be tested under simulated load, signed off, and documented. No department goes live without a completed infrastructure sign-off checklist.

---

### P-06 · Superusers are named but never given time to train

**The problem:** Clinicians are designated as superusers in the project plan but continue to carry full patient loads through the entire training and build phase. They arrive at go-live knowing slightly more than their peers, but nowhere near enough to support them.

**Field case:** A nursing supervisor was named the ward superuser for a medical floor. She attended two of the five scheduled training sessions — the others conflicted with her shift. On go-live day, she was fielding questions she had never been trained to answer. By day three she submitted a formal complaint to nursing administration that she had been "set up to fail." The floor's go-live support was effectively provided entirely by the vendor's implementation consultant, who left after two weeks.

**Lesson learned:** Superuser designation must come with protected time — formal schedule relief and backfill coverage, not just a calendar invite. Define training attendance as a project deliverable with a minimum hour requirement. Attendance is tracked. Missed sessions are rescheduled, not excused. A superuser who has not completed training is not a superuser.

---

### P-07 · Training is one-size-fits-all

**The problem:** All staff attend the same training curriculum regardless of role. Physicians sit through billing module walkthroughs. Billing staff sit through clinical documentation sessions. Everyone leaves having retained less than half of what was relevant to them.

**Field case:** A 200-bed hospital ran a three-day mandatory training for all 340 staff. Attendance compliance was high. Post-go-live competency assessments showed that only 38% of physicians could complete a medication order without assistance, and only 51% of nurses could document a full set of vitals in the correct flowsheet. Exit surveys revealed the dominant complaint: "Most of what we were taught had nothing to do with our job."

**Lesson learned:** Build a role-based training matrix before any curriculum is designed. Define each role's daily EMR interactions first, then design training to cover exactly — and only — those interactions. Assess competency per role with a scenario-based test before go-live clearance. Attendance compliance without competency validation is a false signal of readiness.

---

### P-08 · Interface requirements are verbal agreements

**The problem:** Integration with laboratory systems, pharmacy, radiology, and billing is discussed in meetings and assumed to be understood. No formal interface specification document exists until problems arise.

**Field case:** The EMR team assumed the laboratory system would send results in HL7 v2.5 ORU messages. The LIS vendor, never formally consulted, was sending HL7 v2.3 with a non-standard OBX segment structure for numeric ranges. This was discovered during UAT, six weeks before go-live. The middleware team needed four weeks to remap and retest. Go-live was delayed. The LIS vendor billed for the additional development work. No one had a signed interface specification to dispute the charge.

**Lesson learned:** Every integration requires a signed Interface Control Document before any build begins. The ICD must specify message format, HL7 version, segment definitions, required fields, acknowledgment behavior, error handling logic, and test acceptance criteria — with all parties (EMR vendor, receiving system vendor, hospital IT) signing before a single line of mapping is written.

---

### P-27 · RFQ is built around features, not capabilities

**The problem:** The procurement process evaluates vendors against a checklist of software features — "does it have a medication module?", "does it support HL7?" — rather than against the organization's actual operational capability needs. The winning vendor checks the most boxes, but the boxes were the wrong questions.

**Field case:** A 300-bed hospital issued an RFQ with 140 line items, most of which were features copied from a trade publication's EMR comparison matrix. The vendor that won scored 138 out of 140. Post-implementation, the hospital discovered their single most critical workflow — real-time nursing documentation at the bedside for a dual-shift dispensing model — required a configuration that the vendor supported only as a paid add-on module, never mentioned during selection. The capability gap had never been articulated as a requirement because nobody had asked "what do we need our nurses to be able to do?" before writing the RFQ. The add-on cost ₱1.6M and delayed a full ward rollout by five months.

**Lesson learned:** Start RFQ preparation with a capability gap analysis, not a feature list. Document what your organization must be able to do — operationally, clinically, and administratively — and write requirements that test whether a vendor can enable those capabilities end-to-end. Score vendor demonstrations against your own real-world scenarios, not against the vendor's prepared scripts. A vendor who scores 138 out of 140 on the wrong checklist is still the wrong vendor.

---

### P-09 · Order sets built without clinical governance

**The problem:** IT and the vendor build order sets based on system defaults and whatever a single physician requests during build review. There is no formal clinical governance process, no evidence-based review, and no sign-off trail.

**Field case:** A default sepsis order set was built using a protocol a physician had pulled from a journal article — without disclosing it was from a 2014 study that had since been superseded. The order set went live and was used for eight months before a newly hired ID consultant flagged that the antibiotic regimen did not align with current institutional antibiogram data. A full review of the order set library was triggered, which found six additional sets with similar provenance issues.

**Lesson learned:** Establish a Clinical Content Governance Committee with formal sign-off authority over all order sets, alerts, and clinical decision support rules before build begins. Every item in the clinical content library must carry a named author, a named approver, a dated evidence source, and a defined review cycle. Content without provenance does not go live.

---

## Implementation

---

### P-10 · Point-to-point interfaces multiply into an unmaintainable web

**The problem:** Interfaces between systems are built directly, without a middleware layer, because it is "faster." Within two years, every system change requires touching every connection, and nobody has a complete map of what talks to what.

**Field case:** A hospital built 14 direct HL7 connections over three years: EMR to LIS, EMR to RIS, EMR to pharmacy, RIS to PACS, and so on — all point-to-point, all without an integration engine. When the EMR vendor released a major version upgrade that changed their HL7 output format, the IT team discovered they had to modify and retest all 14 connections individually. Three interfaces broke silently — no error alerting had been configured. Lab results stopped flowing to the EMR for 72 hours before a nurse noticed the queue was empty.

**Lesson learned:** Mandate an integration engine as a non-negotiable architectural requirement from day one. No direct system-to-system connections, ever. The middleware layer is also your monitoring layer — configure queue alerting, message failure notifications, and throughput thresholds from go-live. The short-term savings from skipping middleware are consumed in full the first time a vendor upgrade touches your interfaces.

---

### P-11 · Test scripts do not reflect real patient complexity

**The problem:** UAT is conducted with clean, simple test scenarios. The messy, multi-episode, multi-provider records that represent the actual patient population are never tested. Failures are discovered by clinicians on real patients.

**Field case:** Testing for the patient lookup function was done exclusively with single-episode patients with unique names. Post-go-live, staff discovered that searching for a common surname returned hundreds of results with no effective secondary sort. More critically, patients with multiple active encounters — admitted inpatients who also had open outpatient records — appeared duplicated in search results. Three nurses documented vitals on what they believed was the current inpatient encounter but was actually an old outpatient record.

**Lesson learned:** UAT scripts must be derived from your actual patient population — including your most complex cases. Pull anonymized records from the legacy system to build test scenarios that include common surnames, multi-episode patients, and edge-case demographics. If a scenario exists in your patient population, it belongs in the test plan. UAT sign-off requires evidence of complexity testing, not just clean-case coverage.

---

### P-12 · Go-live happens on a Friday

**The problem:** Go-live is scheduled for end-of-week, often to avoid disrupting a full work week. By Saturday, the vendor's A-team has left and the hospital is operating a new system over the weekend with skeleton staffing.

**Field case:** A hospital went live on a Friday at 6:00 AM. By Friday afternoon, three critical issues had been identified: the downtime report was not generating correctly, medication labels were printing to the wrong printers, and one physician's login was locked due to an Active Directory misconfiguration. The vendor's onsite team left at 5:00 PM. Remote support resolved the printer issue by Saturday noon. The medication label issue was not fully resolved until Monday morning. Weekend nursing staff improvised a manual workaround that introduced two documentation gaps.

**Lesson learned:** Go-live date must be Tuesday or Wednesday — contractually. Ensure the vendor's full support team is committed to on-site presence for the first five business days post-go-live. This is a project scheduling requirement, not a preference. The first 72 hours of a live clinical system surface more issues than six months of UAT. You need your best people — and the vendor's best people — present and rested when those issues hit.

---

### P-13 · Downtime procedures are not rehearsed before go-live

**The problem:** Paper downtime forms exist as a PDF somewhere. Nobody has drilled the process. When the system goes down — and it will — staff revert to chaos rather than a practiced protocol.

**Field case:** Six weeks after go-live, a scheduled maintenance window ran long and the system was unavailable for four hours during a morning shift. Nursing staff on two floors had never used the downtime forms — they had been distributed but never practiced. One floor improvised by using sticky notes on the medication cart. When the system came back online, reconciliation between downtime paper records and the EMR took over three hours. One dose of anticoagulant was double-documented, triggering a pharmacy review and an incident report.

**Lesson learned:** Downtime procedures are clinical procedures — they require the same training and drilling as any patient care protocol. Run a mandatory downtime simulation at least two weeks before go-live, covering all shifts and all clinical areas. The drill must include the full reconciliation cycle when the system comes back online. Pass/fail is determined by documentation completeness, not just participation.

---

### P-14 · Physician resistance is escalated to administration instead of investigated

**The problem:** When physicians push back on the system, the default response is political pressure rather than workflow analysis. The underlying friction is never resolved — it is suppressed. It resurfaces as workarounds, non-compliance, or formal grievances.

**Field case:** A senior surgeon refused to use the electronic surgical order entry module, continuing to handwrite orders that a clerk then encoded. Administration issued a formal directive. He complied — minimally. An informatics review three months later found that his electronic orders were consistently incomplete, missing laterality and anesthesia preferences. Investigation revealed the order entry screen for surgical cases required 23 clicks to complete a standard pre-op order set. His paper form took 4. The order set was redesigned. Compliance improved without further administrative intervention.

**Lesson learned:** Before escalating resistance to administration, send a clinical informatics resource to shadow the resistant user for a shift. The friction they experience is almost always real and diagnosable. Address the root cause — whether it is a workflow design problem, a training gap, or a configuration issue — before applying administrative pressure. Political compliance without workflow resolution is a time-delayed failure.

---

### P-15 · Workarounds become permanent processes

**The problem:** Workarounds created during go-live chaos are never formally addressed. Thirty days later they are muscle memory. Six months later they are "how we do things here." They now exist outside any governance, audit trail, or safety review.

**Field case:** During go-live, the electronic consent process was not working correctly for emergency cases. Staff were instructed to use paper consent as a temporary measure. A ticket was logged. The ticket sat in the optimization backlog for four months while higher-priority issues were addressed. By the time the fix was ready, nursing leadership reported that staff "preferred the paper process" and resisted switching back. The hybrid paper-digital consent process persisted for 14 months, creating an inconsistent legal and clinical documentation trail.

**Lesson learned:** Every workaround created at go-live must be logged in the formal optimization backlog on the day it is created, with a named owner, a resolution target date, and a defined escalation path. Any workaround still open at 30 days escalates to the clinical governance committee. At 60 days, it requires a formal variance approval or a committed resolution date with clinical sign-off. Workarounds do not age out — they harden.

---

### P-28 · Training relies on a single format — and half the staff does not learn that way

**The problem:** The entire training program is delivered as instructor-led classroom sessions. Staff who learn better through hands-on practice, self-paced modules, visual references, or peer coaching are undertrained by design. Go-live reveals the gap, floor by floor.

**Field case:** A hospital delivered EMR training exclusively through five-day classroom sessions in a computer lab. Attendance was strong. Confidence surveys at the end of training were favorable. On go-live day, the ED — a unit with high staff turnover, rotating shift schedules, and a learning culture built on real-time peer coaching rather than formal instruction — had the worst performance of any department. Nurses were freezing at order verification screens. Physicians were abandoning orders mid-entry. The classroom format had never mapped to how ED staff actually learned. An at-the-elbow coaching team was deployed on day two. Within a week, the ED was outperforming wards that had been live for the same duration. The training program was rebuilt after go-live to include scenario simulation, quick-reference badge cards, and a self-paced e-learning refresher accessible from any device — none of which had been part of the original plan.

**Lesson learned:** Design a multi-modal training program from the start. The minimum viable mix: instructor-led sessions for process context, e-learning modules for self-paced skill building, simulation labs for hands-on practice, quick-reference job aids for point-of-care support, and at-the-elbow coaching for the first week post-go-live. Match the modality to how each department actually learns — not to what is easiest to schedule. Budget all five formats before the project plan is finalized.

---

### P-16 · Security roles are over-restricted, creating dangerous workarounds

**The problem:** IT security locks down the system tightly to reduce audit risk. Clinicians find they cannot access what they need during care. They share credentials, use supervisor overrides routinely, or find other paths around the restrictions. The audit trail becomes meaningless.

**Field case:** Nurses in the ICU were not given access to view physician progress notes in the initial security role build — an oversight flagged as a "low priority" request. Nurses needed the notes to understand care plans. The workaround: a physician would log in and turn the monitor toward the nurse. Credential sharing became normalized on that unit within two weeks. When a security audit flagged multiple logins from the same account at the same time from different terminals, the root cause was a role configuration gap that had been known for months.

**Lesson learned:** Security role design requires a clinical informatics lead in the room alongside IT security — for every role. Before go-live sign-off, simulate a complete clinical shift using each role: if a nurse cannot complete their standard duties without requesting an override or borrowing a colleague's credentials, the role is not ready. Over-restriction is a patient safety issue, not just a usability inconvenience.

---

## Post-Implementation

---

### P-17 · Adoption measured by login counts, not clinical outcomes

**The problem:** The project is declared a success because 95% of staff have logged in. Nobody is measuring whether the system is being used correctly, efficiently, or safely.

**Field case:** A hospital reported 97% EMR adoption at the 90-day mark based on active login data. A concurrent clinical audit found that 41% of nursing notes were being entered more than four hours after the documented care event — beyond the threshold for clinical reliability. Physician documentation was being completed largely during administrative hours rather than at point of care. The system was being used for compliance, not for care. Nobody had defined what "adoption" meant clinically.

**Lesson learned:** Define adoption before go-live — in writing, by role. An adoption scorecard should include documentation timeliness, order entry rates at point of care, structured field utilization, and flowsheet completion rates. Set targets. Review the scorecard at 30, 60, and 90 days. Login rate does not appear on the scorecard. If you cannot measure it clinically, you have not defined it.

---

### P-18 · Alert fatigue becomes a patient safety event

**The problem:** Clinical decision support alerts fire for every possible condition, including low-acuity reminders. Clinicians override everything reflexively. The one alert that matters gets overridden with the same muscle memory as the fifty that did not.

**Field case:** A pharmacy team configured 340 drug-drug interaction alerts in the EMR at go-live, replicating the full alert library from their previous system. At the 60-day mark, the override rate was 91%. A pharmacist review found that 280 of the 340 alerts were for interactions that were either clinically insignificant or routinely managed in their patient population. Three months after go-live, a physician overrode a high-severity warfarin-antibiotic interaction alert. The patient developed a supratherapeutic INR and was readmitted. The alert had been overridden 847 times in 60 days with no review.

**Lesson learned:** Conduct an alert rationalization review before go-live — not after. Start with the smallest clinically defensible alert set and expand from evidence, not from defaults. Set an override rate threshold: any alert overridden more than 70% of the time within 30 days is reviewed by a clinical pharmacist and a physician within two weeks. The goal is a small number of high-specificity alerts that clinicians actually read, not a comprehensive library that nobody trusts.

---

### P-19 · No optimization backlog — complaints disappear into a support queue

**The problem:** Post-go-live issues are logged as support tickets, resolved or closed, and never analyzed for patterns. Systemic workflow problems are treated as isolated incidents. The system degrades in usability over time without anyone noticing the trend.

**Field case:** Over 18 months, a hospital's help desk received 1,400 EMR-related tickets. When a new clinical informatics manager reviewed the queue, she found that 380 tickets — 27% — described the same workflow problem in the medication administration record: nurses could not easily document a "held" dose without a multi-step override process. Each ticket had been resolved individually with the same workaround instructions. Nobody had aggregated them into a single optimization request. The fix, once identified, took the vendor three days to implement.

**Lesson learned:** Stand up a formal optimization backlog on go-live day, separate from the help desk queue. Assign a clinical informatics analyst to review incoming tickets weekly, identify patterns, and aggregate systemic issues into optimization requests. Any issue appearing in three or more tickets within 30 days is promoted to an optimization item and assigned a product owner. The help desk resolves incidents. The optimization backlog resolves causes.

---

### P-20 · Reporting queries run against the production database

**The problem:** There is no separate reporting environment. Administrators and quality teams run their reports directly against the live transactional database. During peak clinical hours, this causes system slowdowns that affect care delivery.

**Field case:** A hospital's quality team ran a monthly PHIC quality indicator report — a large, multi-table query — at 9:00 AM on a Tuesday. Response times across the EMR slowed to 15–20 seconds for physician order entry for approximately 40 minutes while the query ran. Physicians called the help desk. The query was killed. The report was never completed that morning. The quality team had been running this report monthly for six months; the performance impact had been occurring every time, but nobody had connected the two events until the help desk logs were reviewed side by side with the performance monitoring data.

**Lesson learned:** A reporting infrastructure separate from the transactional EMR database is a go-live prerequisite, not a post-implementation project. A read replica, a vendor-provided reporting environment, or a lightweight data extract must be operational before the first quality report is ever requested. Clinical system performance during care hours is non-negotiable and cannot be traded against reporting convenience.

---

### P-21 · Master data has no owner after go-live

**The problem:** Provider directories, diagnosis code mappings, formulary data, and facility configurations are set up at go-live and then left to drift. Nobody is assigned to maintain them. Over time, the data diverges from operational reality.

**Field case:** A hospital's provider master file — used for ordering authority validation, referral routing, and billing — was not updated for 11 months after go-live. During that period, six physicians left the institution and four new ones joined. Orders placed by the new physicians were routing to a default "unassigned provider" billing bucket. The error was not caught until an external billing audit flagged ₱2.1M in unattributed professional fee claims. Correcting the records required manual review of every order placed by the affected physicians over the 11-month period.

**Lesson learned:** Every master data domain must have a named steward and a defined update cadence before go-live. Provider directories are reviewed on staff onboarding and monthly. Formulary data is updated with every pharmacy committee cycle. Diagnosis code mappings are reviewed with each ICD revision. Data stewardship is a permanent operational role, not a project task that closes at go-live.

---

### P-29 · No structured post-implementation review — problems compound undetected

**The problem:** The hypercare team leaves. The project is closed. The system is declared live. Nobody conducts a formal, structured review of whether the system is actually performing as intended. Issues accumulate quietly until they are too entrenched to correct without significant disruption.

**Field case:** A hospital's EMR vendor provided two weeks of on-site hypercare support, then transitioned to remote support. At the six-month mark, a PhilHealth accreditation survey team visited the hospital and reviewed clinical documentation in the EMR as part of their assessment. They found that four of the seven required documentation elements for inpatient case records were being completed inconsistently or not at all — not because clinicians were non-compliant, but because the corresponding fields in the EMR had been misconfigured during build and were not visible in the default documentation view. The issue had existed since go-live. No one had noticed because no one had gone back to look. A formal 90-day post-implementation review with floor observation and chart audit would have caught the configuration gap within the first month. The remediation required retroactive chart corrections on 2,300 patient records.

**Lesson learned:** Schedule the 90-day post-implementation review on go-live day — not after. The PIR agenda must include floor observation by a clinical informatics lead, structured interviews with at least one representative from each department, a documentation completeness audit, and a ticket pattern review. Findings are presented to the clinical governance committee within two weeks, with an action plan and named owners. The PIR is not a project closeout ceremony — it is the first real quality review of a live clinical system.

---

### P-22 · Documentation culture is never addressed — data quality silently degrades

**The problem:** Clinicians document in free text instead of structured fields, skip required sections, or copy-forward previous notes without updating them. Reports and quality metrics are generated from this data and treated as accurate.

**Field case:** A hospital began generating monthly readmission rate reports from their EMR at the six-month mark. The reports showed a favorable 7.2% readmission rate — below the national benchmark. An external review found that the discharge diagnosis field — used to calculate readmission — was being populated by copy-forward from the admission diagnosis in 63% of cases. Actual discharge diagnoses were documented in free-text progress notes but never mapped to a structured code. The 7.2% figure was not a readmission rate. It was an artifact of how clinicians had learned to use the system. The true rate, calculated manually from chart review, was 14.1%.

**Lesson learned:** Documentation quality is a governance and incentive problem, not a technology problem. Define structured field requirements per role, embed them in clinical onboarding, and run monthly documentation audits tied to existing quality review cycles. Give clinicians timely, department-level feedback — a monthly documentation scorecard is more effective than an annual compliance report that arrives after the habits are set.

---

## Overall

---

### P-23 · EMR is expected to solve care coordination problems it was never designed to solve

**The problem:** Leadership sells the EMR as the solution to fragmented care, communication failures, and care gaps. When these problems persist post-go-live, confidence in the system collapses — along with the team that implemented it.

**Field case:** A hospital CEO publicly committed that the new EMR would "eliminate handoff errors" between the ER and the wards. No specific workflow redesign, no structured handoff tool, and no change management plan was attached to this commitment. Post-go-live, handoff errors continued at the same rate as before — because the errors had always been a communication and culture problem, not a documentation technology problem. The EMR team was blamed. The clinical informatics lead resigned within 90 days. The structured handoff workflow that would have addressed the actual problem was finally implemented 18 months later, as a standalone initiative, with measurable results.

**Lesson learned:** Separate clinical workflow change management from EMR implementation — structurally, not just semantically. Every identified care coordination gap requires its own initiative: its own workflow design, its own change management plan, its own success metrics. The EMR is the tool. The workflow is the intervention. Never allow executive communications to conflate the two, because when the technology is live and the problem persists, only one of them gets blamed.

---

### P-24 · Total cost of ownership is severely underestimated

**The problem:** The budget presented to the board covers licensing and implementation. It does not cover interfaces, training backfill, productivity loss during go-live, third-party consulting, optimization, hardware refresh, and annual upgrade support. The actual cost is typically 40–70% higher.

**Field case:** A hospital board approved an EMR project at ₱28M based on the vendor's proposal. Final project costs at the 24-month mark totaled ₱47M. The unbudgeted items included: integration engine licensing (₱3.2M), additional server hardware (₱4.2M), third-party implementation consultants brought in after the primary vendor's team underperformed (₱5.1M), staff overtime during go-live (₱1.8M), and an unplanned data migration remediation project triggered by the allergy record issue described above (₱2.4M). None of these were surprises to anyone who had done this before. All of them were surprises to the board.

**Lesson learned:** Build the full TCO model before the board presentation, not after approval. Use a five-year horizon. Include licensing, implementation, interfaces, infrastructure, training, productivity loss during go-live, third-party consulting, optimization, annual upgrades, and a 20% contingency. Present the full number. If the board approves only the vendor's contract price, document the gap formally and get it on record before the project begins — because the remaining costs will arrive whether they were budgeted for or not.

---

### P-25 · A quiet workflow failure is more dangerous than a system crash

**The problem:** System crashes are visible, alarming, and trigger immediate response. Quiet failures — a lab result that stops routing, a referral letter that silently fails to send, a medication reconciliation that skips a step — are invisible until a patient is harmed.

**Field case:** A middleware configuration change made during a routine upgrade caused HL7 ADT messages to stop sending to the hospital's bed management system. The EMR remained fully operational. The bed management system showed all beds as available because no occupancy updates were being received. For 19 hours, ward clerks were manually overriding bed status based on phone calls. Three patients were assigned to beds that were still occupied. No system alert fired. The issue was discovered when a nurse walked into a room to find it already occupied. A post-incident review found that no monitoring had been configured on the ADT message queue.

**Lesson learned:** Every data flow between systems must have monitoring configured before go-live. Define expected message volumes and timing thresholds for each interface. Any queue that goes silent for more than 15 minutes during business hours triggers an automated alert to the on-call team. Monitoring is not a post-implementation enhancement — it is the difference between a 19-minute failure response and a 19-hour patient safety event.

---

### P-26 · Interoperability is treated as a checkbox, not a semantic problem

**The problem:** Two systems are "connected" — messages are flowing, acknowledgments are returning — but the data being exchanged is clinically meaningless because nobody aligned the underlying terminologies, code sets, or value mappings.

**Field case:** A hospital connected their EMR to a regional health information exchange. Both systems used SNOMED CT for diagnosis coding. The connection was declared operational. A year later, a clinical data analyst tried to use the HIE data for a population health report and found that the same diagnosis — Type 2 Diabetes Mellitus — was represented by four different SNOMED codes across sending facilities. None of the facilities had mapped to the same concept hierarchy. Queries for diabetic patients in the region returned different populations depending on which codes were used. The "connected" systems had been exchanging data for 12 months and producing noise.

**Lesson learned:** Interoperability requires semantic alignment, not just technical connectivity. Before declaring any integration operational, validate that the data being exchanged is clinically meaningful — that the same concept maps to the same code on both sides, and that the receiving system interprets it correctly. Assign a clinical terminologist to own the value set mappings. Declare an interface interoperable only after a clinician — not an engineer — has validated that the data round-trips correctly.

---

### P-30 · Adoption is never defined as the final success criterion — so the project is "done" before it is

**The problem:** The project closes when go-live is stable, or when the budget runs out, or when the vendor's contract ends. Adoption — clinicians using the system correctly and sustainably at the point of care — is never formally declared the end state. As a result, nobody is accountable for it, and nobody pursues it after the project team disbands.

**Field case:** A hospital declared their EMR implementation complete at the six-month mark. The project had come in on time and within budget. The project manager received commendation. Eighteen months later, an external clinical audit commissioned for a Joint Commission accreditation preparation found that physician order completion rates in the EMR were at 61% — meaning 39% of physician orders were still being verbally dictated and clerk-encoded, bypassing the system entirely. Nursing documentation adherence was at 54%. The system was being used as a parallel record, not as the system of record. Nobody had ever defined what "done" looked like in clinical terms. The go-live milestone had been the finish line. Adoption had never been put on the scoreboard.

**Lesson learned:** Adoption must be the named, documented success criterion of the project — written into the project charter, the vendor contract, and the board approval document. Define it in clinical terms: the system is the single system of record, used correctly at the point of care, by all roles, at rates that meet or exceed the adoption scorecard targets. Measure it at 6, 12, and 18 months. Go-live is the starting line. Adoption is the finish line. The project is not complete until you can show where you are on that track.

---

*Last updated: March 2026*
