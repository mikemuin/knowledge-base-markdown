# EMR Implementation Lessons: No-Nonsense Field Notes

> Compiled from the trenches — not from vendor brochures.

---

## Pre-Implementation

### Governance & Readiness

- **Executive sponsorship is not enough — you need executive accountability.** A champion who disappears when the going gets hard is worse than no champion at all. Name names, assign decisions, and document who owns what before a single server is provisioned.
- **Do a workflow audit before touching the system.** You are not automating a process — you are digitizing it. Broken manual workflows become expensive broken digital workflows. Fix the process first.
- **Never trust the vendor's "standard workflow."** Their demo environment is optimized for demos. Your ICU is optimized for keeping people alive. These are not the same thing.
- **Assess your data before you migrate it.** Legacy data is almost always dirtier than anyone admits. Budget time for data profiling, deduplication, and clinical validation before go-live — not after.
- **Do a real infrastructure audit.** Network bandwidth, Wi-Fi coverage, device refresh cycles, printer locations — these mundane details will derail go-live more reliably than any software bug.
- **Identify your superusers early and protect their time.** The best clinical informatics ally you have is the floor nurse who actually wants to learn the system. Guard their schedule like it is a surgical slot.
- **Training must be role-based, not system-based.** A pharmacist does not need to know how the billing module works. Irrelevant training is remembered as wasted time and poisons adoption.
- **Build a realistic timeline, then add 30%.** If the vendor says six months, plan for eight. Scope creep, interface builds, and third-party delays are not exceptions — they are the rule.
- **Get all interface requirements on paper, signed.** "The system can connect to anything" means nothing without a defined HL7 message specification, agreed-upon acknowledgment logic, and a test plan.
- **Establish a clinical governance committee with veto power.** IT cannot unilaterally decide what a physician order set looks like. Clinical sign-off on all content is non-negotiable.

### Vendor Selection & RFQ

- **Write your RFQ around capabilities, not features.** A feature is "the system can generate a discharge summary." A capability is "clinicians can produce a complete, structured discharge summary at the point of care without leaving the patient record." Features describe what software does. Capabilities describe what your organization needs to be able to do. Evaluate vendors against capabilities, and you will make a procurement decision grounded in operational reality, not a checkbox comparison of module lists.
- **Define your must-have capabilities before you talk to any vendor.** The sequence matters: identify organizational capability gaps first, translate them into requirements, then issue the RFQ. If you reverse this — evaluate vendors first, then backfill your requirements around the winner — you are rationalizing a preference, not conducting a selection.
- **Require vendors to demonstrate capabilities against your scenarios, not their scripts.** Give each vendor the same three realistic patient scenarios drawn from your own operations. Ask them to demonstrate how their system handles each one. The gaps between what they show and what you need are your implementation risk.

### Contracts & Vendor Management

- **Read the SLA.** Specifically: uptime guarantees during business hours vs. 24/7, response time tiers, and what constitutes a "critical" issue in their definition vs. yours.
- **Negotiate access to your own data from day one.** Data export format, frequency, and cost should be in the contract — not discovered at the end of the relationship.
- **Define go-live support terms explicitly.** "Hypercare" means different things to different vendors. Get headcount, availability hours, and escalation paths in writing.

---

## Implementation

### Technical Execution

- **Build your interfaces in a dedicated middleware layer, not point-to-point.** Direct HL7 connections between systems are technical debt that accumulates interest daily. An integration engine (Mirth, Rhapsody, Azure Service Bus) is not a luxury.
- **Test with real patient data scenarios, not synthetic edge cases only.** Your test scripts should include the actual messy, multi-episode, multi-provider records that exist in your system today.
- **Cutover strategy is a clinical decision, not an IT decision.** Big bang vs. phased rollout has patient safety implications. That decision sits with the CMO and CNO, not the project manager.
- **Never go live on a Friday.** Go live on a Tuesday. You want your most experienced staff on-site and your vendor fully staffed for the first 48–72 hours.
- **Downtime procedures must be operational before go-live, not planned for later.** Paper downtime forms, downtime read-only reports, and staff drills are mandatory — not optional.
- **Retire the legacy system on a defined date.** "We'll keep it running just in case" becomes "we are running two systems indefinitely." Set a hard cutoff and hold to it.

### Change Management

- **Use multi-modal training — no single format works for everyone.** Classroom instruction, e-learning modules, at-the-elbow coaching, quick-reference cards, simulation labs, and peer-to-peer learning each reach different learners differently. A training program that relies on a single modality will leave a significant portion of your staff underprepared. Combine formats: use classroom for process context, e-learning for self-paced skill building, and at-the-elbow coaching in the first days after go-live when anxiety is highest.
- **Competency validation is not optional.** Training completion is not competency. Build a brief, scenario-based assessment for each role before certifying readiness. A nurse who attended all sessions but cannot navigate the medication administration record is a go-live risk, not a trained staff member.
- **Resistance is data, not insubordination.** When a physician refuses to use the new system, investigate the workflow friction before escalating to administration. They are usually right about the problem, even if wrong about the solution.
- **The first two weeks post go-live set the cultural tone permanently.** Visible leadership presence on the floors, fast issue resolution, and honest communication about known bugs determine whether staff view the system as supported or abandoned.
- **Communicate problems before users discover them.** A proactive "we know about this issue and here is the workaround" message builds more trust than a reactive response to a complaint storm.
- **Every workaround that survives 30 days becomes a permanent process.** Document all workarounds, assign owners, and set resolution deadlines. Otherwise your EMR accumulates shadow workflows that nobody controls.

### Configuration & Build

- **Minimize customization during initial build.** Get to go-live on near-vanilla configuration. Customization is debt. Pay it down only after you understand how the system actually behaves in your environment.
- **Order sets and clinical decision support rules must be evidence-based and dated.** Who approved it, when, and against what clinical guideline — this must be in the metadata, not someone's memory.
- **Security role design requires a clinical informatics lead, not just IT security.** Overly restrictive roles create dangerous workarounds. Over-permissive roles create audit and compliance exposure. Both are clinical risks.

---

## Post-Implementation

### Optimization & Adoption

- **Measure adoption by outcome metrics, not login counts.** Login rates are vanity metrics. Measure time-to-order, documentation completeness rates, alert fatigue rates, and patient throughput.
- **Alert fatigue is a patient safety issue.** Audit your clinical decision support alerts within 90 days of go-live. If override rates exceed 80%, the alert is noise. Noise kills.
- **Build a formal optimization backlog.** Every complaint, workaround, and missed requirement from go-live should be logged, triaged, and prioritized — not lost in a support ticket queue.
- **Superusers need ongoing education, not one-time training.** New builds, upgrades, and regulatory changes mean your clinical champions need a continuous learning pathway, not a one-time certification.
- **Vendor upgrade cycles are not optional maintenance windows.** Falling behind on patches creates security exposure and locks you out of interoperability features that your referral network will eventually require.

### Post-Implementation Review

- **Conduct a formal post-implementation review at 90 days — and treat it as a clinical event, not an IT audit.** The PIR is not a project close-out exercise. It is a structured assessment of whether the system is being used correctly, safely, and in a way that serves clinicians. It should include floor rounds, workflow observation, ticket pattern analysis, and direct clinical staff interviews. Findings should feed directly into the optimization backlog.
- **The PIR is your last opportunity to course-correct before bad habits calcify.** At 90 days, workarounds are still fresh enough to be challenged. Documentation gaps are still small enough to remediate with targeted training. By 12 months, they are institutional norms. Use the PIR window.
- **Post-implementation hand-holding is not a sign of a failed implementation — the absence of it is.** Hypercare ends, but clinical informatics support should not. Assign a named EMR liaison to each major department for at least 6 months post-go-live. That person is the bridge between frontline frustration and the optimization process.

### Data & Reporting

- **Invest in a reporting layer separate from your transactional EMR.** Running analytics queries against your production database is how you cause system slowdowns during peak clinical hours. Build a data warehouse or use your vendor's reporting environment.
- **Data governance does not end at go-live.** Master data management — provider directories, diagnosis code mappings, formulary updates — requires a named owner and a defined maintenance schedule indefinitely.
- **Your data is only as good as your documentation culture.** If clinicians are not documenting in the right fields, your quality metrics and population health reports are wrong. This is a training and incentive problem, not a technology problem.

---

## Overall POV

- **The EMR is infrastructure, not a solution.** It is a foundation on which clinical workflows are built. Expecting it to solve care coordination, documentation burden, or physician burnout on its own is magical thinking.
- **The biggest risk at go-live is not a system crash — it is a quiet workflow failure nobody reports.** Build a formal issue capture mechanism (a hotline, a dedicated Slack channel, a floor rounding protocol) so problems surface fast.
- **Interoperability is a negotiation, not a feature.** Getting two systems to exchange meaningful clinical data requires agreed-upon semantics, not just agreed-upon protocols. HL7 v2 pipes and FHIR APIs can both fail to communicate useful information if the underlying terminologies are misaligned.
- **Total Cost of Ownership is always higher than the contract price.** Add interfaces, training, lost productivity during go-live, overtime, third-party consulting, and annual optimization work before presenting a budget to leadership.
- **The clinicians who complain the loudest are often your best future champions.** They care enough to push back. Engage them early, take their feedback seriously, and they will defend the system once it works for them.
- **No EMR implementation survives contact with the floor unchanged.** Plan for it. Budget for it. Staff for it. Rigidity at go-live is a clinical risk.
- **The goal is not a successful go-live. The goal is a functioning clinical operation one year later.** Hold your team and your vendor to that standard, not to the project milestone chart.
- **Adoption is the final metric — and it is the hardest one to fake.** Not go-live date. Not training completion rate. Not ticket closure rate. Adoption: clinicians using the system correctly, at the point of care, in a way that improves the quality and safety of what they document and decide. Everything else in this list is scaffolding to get there. If adoption is failing, the project is failing — regardless of what the dashboard says.

---

*Last updated: March 2026*
