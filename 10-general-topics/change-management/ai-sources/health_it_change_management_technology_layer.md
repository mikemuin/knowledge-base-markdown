# Health IT Change Management Framework
## Technology Layer

---

## Overarching Principle

**Change management is the disciplined management of failure within People, Process, and Technology — concurrent with project execution — in order to protect the integrity of the transition from current to future state.**

### Key Clarifications

**Disciplined** means change management runs *concurrent* with project execution, not after it. It is not something layered on top of a completed delivery. This is a significant organizational commitment and where most Health IT programs quietly abandon the framework mid-stream.

**Failure as the default.** Traditional frameworks (Kotter, ADKAR, Prosci) are optimistic by design — they assume buy-in is achievable, adoption is linear, and resistance is an exception. This framework starts from the opposite assumption: failure is the default, and success is what you engineer against it.

**Silent failure is the primary threat.** Projects rarely fail loudly in Health IT. They fail quietly — adoption numbers that look acceptable but mask workaround behavior, process maps technically followed but clinically bypassed, technology that is live but unused. Detection mechanisms must be sensitive to silent failure, not just visible failure.

**The ownership gap is structural.** Failure in change management is almost always ownerless by default. No single person wakes up responsible for the integrity of the People-Process-Technology transition as a whole. The framework must deliberately assign unified accountability, not just distributed ownership per layer.

**Proportionality matters.** Not all failures are equal. Some are recoverable — a workflow that needs redesign, a configuration error, a training gap. Others are irreversible — a patient safety event, a data breach, a go-live collapse that destroys clinician trust for years. Stratification logic must be built in from the start, not retrofitted.

### Strategic Questions
- Where can we expect failure? *(Reframe: If this project fails, where would it fail?)*
- What does failure look like before anyone admits it is happening?
- What are the sociotechnical dynamics at play?
- Who needs to decide?

---

## The PPT Overlay

Change management operates through three lenses. These are the things we need to change.

| Lens | System | Character |
|---|---|---|
| **People** | Behavior system | Hardest to manage. Most sensitive. Many hesitate to act. |
| **Process** | Execution system | Easiest to underestimate. |
| **Technology** | Enablement system | Most often, the easiest to solve — but the most dangerous when it fails quietly. |

Within each lens, the same logic runs:

**Define → Anticipate → Prevent → Detect → Respond**

---

## Technology Layer

> *Technology is the Enablement System. What you are managing: how tools interact with real human behavior and workflows.*

### Why Technology Is the Most Manageable Layer

Technology is the most manageable because it is the most **concrete**. It has specs, vendors, acceptance criteria, and a go-live date you can point to. Failure is often visible — a system is down, an interface breaks, a report produces wrong numbers. These are fixable. You can open a ticket.

But Technology gets underestimated in three specific places:

1. **Integration** — The system works perfectly in isolation and fails in the ecosystem. HL7 feeds drop. FHIR mappings are semantically wrong even when syntactically valid. The lab system and the EMR speak different dialects of the same standard. This is the most common and most expensive Technology failure in Health IT, and it almost always surfaces *after* go-live.

2. **Data quality and migration** — Dirty data migrated into a clean system. Duplicate patient records. Unmapped legacy codes. The technology works; the *content* inside it doesn't. This is insidious because the system *appears* functional until a clinician acts on bad data.

3. **Infrastructure-reality gap** — The system was designed and tested for an environment that doesn't exist in the field. The vendor demo ran on fiber in a conference room. Your deployment runs on a 3G connection in a rural health center.

### A Critical Insight

Technology failure is rarely purely technical. A system that is technically perfect but behaviorally ignored is a failed system. This means some Technology risks will have their root cause in People or Process. The Technology layer does not exist in isolation.

---

## Technology Success Definition

> *The system is the right fit for context, consistently available in the real deployment environment, behaviorally reliable, and usable enough that clinical and operational staff perform their intended workflows through it — enabling measurable process outcomes — and this state is sustained beyond go-live.*

Each element of this definition is load-bearing:

**"The right system"** — Not the best system globally, not the most feature-rich. The right system for *this* clinical context, *this* workflow, *this* population. Technology failure often begins at selection — a vendor chosen for political, financial, or familiarity reasons, not fit.

**"Available"** — The system is accessible to the people who need it, when they need it, on the devices they actually have, on the connectivity that actually exists. Availability is not a binary — it has a threshold that must be defined per context. In a tertiary hospital, 99.9% uptime on fiber. In a rural health unit, offline-first capability with intelligent sync. Both are valid; neither should be assumed.

**"Reliable"** — The system behaves consistently when reached. Interfaces don't silently drop messages. Reports produce the same result from the same data. Data doesn't corrupt across migration or sync cycles. Integrations fail loudly, not quietly. A system can be available and unreliable simultaneously — and in Health IT, that is often more dangerous than being simply down.

**"Usable"** — Not whether the system *can* be used, but whether it *will* be used as designed, by actual users, in the actual workflow context. Usability is the bridge between the Technology and People layers. UX assessment is a Technology success criterion, not a design preference.

**"In the real environment it was built for"** — Success cannot be defined against a test environment, a vendor sandbox, or a pilot site that is not representative. Success must be validated in the messiest, most constrained, most representative environment in the deployment scope.

**"Enables the process it was designed to support"** — Technology is not an end in itself. A system that is available, reliable, and usable but doesn't accelerate, improve, or protect the process has succeeded technically and failed strategically. This criterion requires defining, before go-live, exactly which process outcomes the technology is expected to produce.

---

## Technology Failure Control Map

The six failure domains below are derived directly from inverting the success definition. Each domain includes top risks, prevention and detection mechanisms, response actions, and ownership.

> ⚠ indicates **high silent failure risk** — the domain where failure is most likely to be invisible before it becomes irreversible.

---

### Domain 1 · Selection Failure
**Failure mode:** Wrong fit — vendor chosen without adequate clinical alignment

#### Top Risks
- Vendor selected on cost or familiarity, not clinical fit
- Requirements gathered by IT without clinical voice
- Pilot environment not representative of real deployment context
- Scalability and interoperability not assessed at selection stage

#### Prevent
- Structured clinical fit assessment against defined requirements
- Clinician-led vendor evaluation and scoring
- Reference site visits in comparable deployment contexts
- Total cost of ownership analysis including integration and maintenance costs

#### Detect
- Requirements traceability review conducted immediately post-selection
- Clinical workflow simulation before configuration begins
- Requirement gap log surfacing during build phase — early signal of fit failure

#### Respond
- Scope negotiation with vendor to address gaps
- Configuration workarounds where feasible and safe
- Governance escalation for fundamental fit failure
- Vendor replacement before go-live if gap is critical — painful, but less so than post-go-live failure

**Owner:** CIO / CMIO

---

### Domain 2 · Infrastructure Failure
**Failure mode:** Not available — system inaccessible in the real deployment environment

#### Top Risks
- Connectivity assumptions not field-validated before deployment
- Power instability not accounted for (critical in resource-limited settings)
- Offline capability absent where connectivity is unreliable
- Device constraints overlooked — older hardware, mobile vs. desktop mismatches
- Security requirements creating unintended access barriers for clinical users

#### Prevent
- Infrastructure readiness assessment conducted per deployment site
- Context-specific availability threshold defined (not assumed)
- Offline-first design where connectivity is unreliable
- Hardware standardization policy or BYOD policy defined
- UPS and power backup planning included in site preparation

#### Detect
- Uptime monitoring dashboards per site
- User-reported access issues log maintained and reviewed
- Regular field connectivity testing (not just HQ testing)
- Sync failure alerts for offline-capable systems

#### Respond
- Downtime procedures with paper-based clinical fallback
- Per-site infrastructure remediation plan
- Incident response protocol for unplanned outages
- Escalation path for systemic infrastructure failures

**Owner:** IT Infrastructure Lead / Site Operations Lead

---

### Domain 3 · Integration & Data Quality Failure
**Failure mode:** Not reliable — system produces inconsistent or clinically unsafe data

> This is the highest technical risk domain in Health IT. It is the domain most likely to produce a patient safety event, most likely to be invisible until a clinician acts on bad data, and most likely to have its root cause misattributed. It deserves the most rigorous prevention and detection investment of all six domains.

#### Top Risks
- HL7 / FHIR mappings syntactically valid but semantically wrong
- Silent interface failures — messages dropped without alert
- Duplicate patient records post-migration
- Unmapped legacy codes and terminology mismatches across systems
- Data corruption across sync cycles in offline-capable deployments

#### Prevent
- Interface specification completed and approved before build begins
- Data quality assessment and cleansing completed pre-migration
- Semantic mapping validation — not just syntactic conformance
- Deduplication logic defined and tested pre-migration
- Test environment mirrors production data complexity (not just structure)

#### Detect
- Interface monitoring with active failure alerting (not passive logging)
- Data quality dashboards tracking completeness, accuracy, and consistency
- Reconciliation reports run post-migration and at regular intervals
- Audit of high-risk fields: allergies, medications, diagnoses, dosing
- Clinician-reported data anomaly log reviewed regularly

#### Respond
- Interface break response protocol with defined SLAs
- Data correction and reprocessing workflow with audit trail
- Patient safety escalation protocol if bad data has been acted upon clinically
- Root cause analysis and permanent fix cycle — not just symptom correction

**Owner:** Health Informatics Lead / Integration Architect

---

### Domain 4 · Adoption & Workaround Failure
**Failure mode:** Not usable — staff bypass the system in real clinical conditions

> This domain is the bridge between the Technology layer and the People layer. A system that is technically flawless but behaviorally ignored has failed. The Technology control map ends here; the People control map picks up on the other side.

#### Top Risks
- System requires more steps than the workflow it replaces
- UI not designed for clinical interruption patterns or cognitive load
- Training insufficient for real-world complexity and edge cases
- Users develop workarounds that bypass system logic and data capture
- Device and mobile constraints limiting access at the point of care

#### Prevent
- UX assessment conducted with actual end users before go-live
- Current state vs. future state workflow mapping completed before configuration
- Role-based training aligned to actual tasks, not system features
- Super-user network established and activated before launch

#### Detect
- Usage analytics tracked: login rates, feature utilization, task completion rates
- Workaround detection: are paper forms still in use? Is data entry being bypassed?
- Support ticket pattern analysis — what are users consistently struggling with?
- Direct post-go-live clinical observation at point of care

#### Respond
- Rapid configuration adjustment for high-friction workflows (defined turnaround time)
- Targeted retraining for identified gaps — not blanket retraining
- Workaround root cause analysis — is this a usability failure or a training gap?
- Vendor escalation for UX improvements that cannot be resolved through configuration

**Owner:** Clinical Informatics Lead / Change Manager

---

### Domain 5 · Strategic Misalignment Failure
**Failure mode:** Doesn't enable process — technology live but intended outcomes not achieved

#### Top Risks
- Technology implemented without process outcome targets defined
- System automates a broken process — digitizing the problem, not solving it
- KPIs not established before implementation begins
- System capabilities mismatched to actual clinical workflow needs

#### Prevent
- Process outcome KPIs defined before technology selection, not after go-live
- Workflow redesign completed before system configuration begins
- Governance alignment on what the technology is actually expected to achieve
- Benefits realization plan documented and owned at project start

#### Detect
- KPI tracking against pre-implementation baseline from day one of go-live
- Process efficiency measurement conducted post-go-live
- Clinical outcome monitoring tied to system-enabled workflows
- Stakeholder satisfaction surveys at defined intervals

#### Respond
- Process redesign if automation was applied to a broken workflow
- Configuration adjustment to better support the redesigned process
- Governance review if strategic misalignment is fundamental, not cosmetic
- Benefits realization review, reset, and re-communication to stakeholders

**Owner:** CMIO / Project Sponsor

---

### Domain 6 · Post-Live Degradation Failure
**Failure mode:** Not sustained — system drifts from its intended state after go-live

> Go-live is not the finish line. This is where compounding begins — a small knowledge gap becomes a large one, minor technical debt becomes an unmaintainable system, and a cold vendor relationship becomes a support crisis. Prevention here is almost entirely organizational, not technical.

#### Top Risks
- Support resources withdrawn immediately after go-live
- System updates breaking existing configurations, often silently
- Staff turnover creating critical knowledge gaps
- Vendor relationship not maintained after implementation concludes
- Technical debt accumulating without a remediation plan

#### Prevent
- Sustainment plan defined and resourced before go-live
- Knowledge transfer to internal team documented and tested
- Change control process established for system updates and patches
- Long-term vendor support contract maintained
- Super-user network sustained and refreshed post-launch

#### Detect
- Periodic system health reviews on a defined schedule (minimum quarterly)
- User satisfaction tracking conducted over time, not just at go-live
- KPI degradation monitoring — are metrics trending down?
- Support ticket volume and pattern trends reviewed regularly

#### Respond
- Sustainment intervention triggered by health review findings
- Retraining cycles activated when staff turnover creates identified gaps
- Vendor escalation for update-related regressions
- Technical debt remediation planning with prioritization and resourcing

**Owner:** IT Operations Lead / Clinical Informatics Lead

---

## Key Cross-Cutting Observations

**Four of six domains carry silent failure risk.** Selection, Integration & Data Quality, Adoption & Workaround, and Strategic Misalignment can all appear healthy while actively failing. This is why detection mechanisms are as important as prevention mechanisms — you can prevent a noisy failure with good planning, but silent failure requires deliberate instrumentation.

**Domain 3 is the highest technical risk.** Integration and data quality failure is the most dangerous domain in Health IT specifically. A system can be available, selected with care, and actively used — and still cause a patient safety event through a silent semantic mapping error introduced months prior.

**Domain 4 is the seam between Technology and People.** Workaround behavior looks like a Technology problem (the system is being bypassed) but its root cause is almost always in People (behavior, incentives, trust) or Process (the workflow the system was mapped to was wrong). Both layers must address it.

**Domain 6 is consistently underfunded.** Organizations treat go-live as the destination. The sustainment plan, vendor relationship management, and technical debt remediation are afterthoughts — until they aren't, at which point the cost of recovery far exceeds the cost of prevention.

---

*Document status: Technology layer complete. Process layer to follow.*
