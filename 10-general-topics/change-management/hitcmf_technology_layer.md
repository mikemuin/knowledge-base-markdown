# Health IT Change Management Framework

## Technology Layer

**Layer Role:** Enablement System
**Framework Logic:** Define → Anticipate → Prevent → Detect → Respond

------

## I. What It Is

> *Technology is the Enablement System. What you are managing: whether the tools you deploy actually get used — and whether they help people do their jobs better.*

Technology is the most visible layer of any health IT change. It is the system your staff will log into every day — the screens, the records, the reports, the devices. It has a name, a vendor, a launch date, and a price tag. You can point to it.

That visibility is both its greatest strength and its most common source of overconfidence. The fact that you can point to a system does not mean the system is working.

------

## II. Definition of Success

> *The right system is in place, reliably accessible to staff in their actual work environment, easy enough to use that people choose to use it, and it measurably improves the work it was meant to support — and that remains true long after launch day.*

Each part of this definition matters. Leaving any one out produces a system that looks successful and isn't.

| What Success Requires                         | What It Actually Means                                       |
| --------------------------------------------- | ------------------------------------------------------------ |
| **The right system**                          | Not the most impressive system. Not the cheapest. Not the one the vendor demonstrated best. The one that fits *this* organization, *these* staff, *this* set of workflows. Most technology failures begin here — at selection — not at implementation. |
| **Accessible to staff**                       | Staff can open the system when they need it, on the device they are actually using, in the location where they actually work. A system that only works reliably in the main office but not in the ward, the clinic, or the field has an accessibility problem. |
| **Behaves consistently**                      | Every time staff use the system, it behaves the same way. Records don't go missing. Reports produce the same results from the same data. Information entered in one part of the system appears correctly everywhere else. A system that works most of the time but fails unpredictably is often more dangerous than one that is simply unavailable — because staff continue to act on information they believe is correct. |
| **Easy enough to use**                        | Not whether staff *can* use the system if they try hard enough — but whether they *will* use it as part of their normal working day. A system that adds significant extra steps to tasks that were simpler before is not easy enough. Staff will find a way around it. |
| **Works in the real environment**             | Success must be measured in the actual conditions staff work in — not in a training room, a demo environment, or the best-performing site. The right test is whether it works for the most stretched team, on the busiest shift, under the most difficult conditions. |
| **Improves the work it was meant to support** | Technology is not an end in itself. A system that is accessible, consistent, and usable but does not actually make the underlying work better has succeeded technically and failed in purpose. The improvement it is meant to deliver must be defined before launch, not discovered afterward. |
| **Remains true after launch**                 | Launch day is not the finish line. A system that works well at launch and quietly deteriorates over the following months has not succeeded — it has simply failed slowly. |

------

## III. Why It Is the Most Manageable Layer

Technology is the most manageable layer because when something goes wrong, you usually know about it.

If the system goes down, staff cannot log in. If a report produces the wrong result, someone notices and raises it. If a record is missing, a query gets raised. These problems are visible — and visible problems are fixable. You can contact the vendor, roll back a change, or raise a support ticket.

This is very different from the People layer, where the most serious problems — staff resistance, disengagement, loss of confidence — can build for months before anyone formally raises them. And it is different from the Process layer, where teams can appear to be following the new way of working while quietly doing things the old way in practice.

Technology also tends to have clearer ownership. There is usually a named lead, a vendor relationship, a contract, and a documented system. Responsibility is easier to assign.

That said, there are three places where technology is consistently underestimated — and where the problems that emerge are harder to spot than they first appear:

| Where Technology Gets Underestimated                        | What Actually Happens                                        |
| ----------------------------------------------------------- | ------------------------------------------------------------ |
| **Connecting systems together**                             | Most health organisations run multiple systems — patient records, lab results, scheduling, pharmacy, and others. Getting these to share information correctly is one of the most technically complex parts of any implementation. Two systems can each work perfectly on their own and fail to exchange information accurately. These failures are often invisible — the systems appear to be working, but the information flowing between them is incomplete or wrong. |
| **The quality of the data being moved**                     | When organisations move to a new system, they bring their existing records with them. If those records are incomplete, duplicated, or inconsistently formatted, the new system inherits all of those problems. The system works — but the information inside it does not. This is not noticed until someone acts on it. |
| **The gap between the test environment and the real world** | Systems are tested under controlled conditions — reliable internet, standard equipment, a small group of trained users. The real deployment often involves older devices, patchy connectivity, high staff turnover, and users who were trained once six months ago. A system that passed every test can still struggle badly in the field. |

------

## IV. Critical Insights

### A. The Go-Live Trap

Most organisations define technology success as **going live**. The system is launched — therefore, the technology succeeded.

This is one of the most dangerous assumptions in health IT.

Launch day is a milestone, not an outcome. A system that launches successfully and then deteriorates over the next six months — because support was withdrawn, because staff found workarounds, because small technical problems were never fixed — has not succeeded. It has failed on a delayed timeline.

Organisations that declare victory on launch day stop actively managing the technology at precisely the moment when the most significant risks begin to emerge.

This framework treats success not as something that happens on launch day, but as something that must be maintained consistently in the months and years that follow.

### B. Adoption Is the Real Measure

A system that staff do not use has failed — regardless of how well it was built.

This is the insight most commonly resisted by technology teams, because technology success tends to be measured in technical terms: whether the system is running, whether it is available, whether defects have been closed. These things matter. But they measure whether the system *can* be used — not whether it *is* being used as intended.

The true measure is adoption: whether staff across the organisation are consistently using the system to do the work it was designed to support. A system that is technically excellent but that staff work around, ignore, or use only partially has failed at its fundamental purpose.

Adoption is not just a people and behaviour problem. How easy the system is to use, how well it fits into existing work patterns, and how much it adds to — rather than reduces — the burden on staff are all technology design decisions. They determine whether adoption is even possible.

### C. People and Process Determine Whether Technology Succeeds

Technology does not succeed or fail in isolation.

A well-chosen, well-built, well-supported system can still fail if:

- Staff were never genuinely brought on board with the change and are quietly resisting it.
- The new ways of working the system was designed to support were never properly designed or communicated.
- The decision to select the system was made without meaningful clinical or operational input, and trust was lost early.

This matters for how you manage risk. Some of the most significant risks in the technology layer are not technical in origin — they come from the people and process layers. Treating them purely as technical problems produces technical solutions that miss the actual cause.

The Technology Failure Map in Section V must be read alongside the People and Process layers. Problems do not stay neatly within one layer.

------

## V. Technology Failure Control Map

The six failure areas below each correspond to one element of the success definition. Each one describes what goes wrong, what to watch for, how to prevent it, and who is responsible.

> ⚠ indicates **high risk of silent failure** — situations where things can go seriously wrong without obvious warning signs. Four of the six areas carry this risk.

### Summary Overview

| Area                                      | What Goes Wrong                                              | ⚠    | Responsible                                |
| ----------------------------------------- | ------------------------------------------------------------ | ---- | ------------------------------------------ |
| T1 · Wrong System Selected                | The system chosen does not fit how staff actually work       | ⚠    | CIO / Chief Medical Officer                |
| T2 · Access and Infrastructure            | Staff cannot reliably access the system in their actual work environment |      | IT Lead / Operations Lead                  |
| T3 · Wrong Information, Real Consequences | The system produces unreliable or incorrect information      | ⚠    | Health Informatics Lead                    |
| T4 · Staff Not Using the System           | Staff avoid the system and find other ways to get the work done | ⚠    | Clinical Informatics Lead / Change Manager |
| T5 · System Does Not Improve the Work     | The system is running but the expected improvements are not happening | ⚠    | Chief Medical Officer / Project Sponsor    |
| T6 · Decline After Launch                 | The system works at launch and gradually deteriorates        |      | IT Operations Lead                         |

------

### T1 · Wrong System Selected

**What goes wrong:** The organisation chose a system that does not fit the way staff actually work.

|                                |                                                              |
| ------------------------------ | ------------------------------------------------------------ |
| **How this happens**           | System chosen primarily on cost, or because it was familiar to the procurement team · Staff who will actually use the system had little or no input into the decision · The system was assessed in a controlled environment that did not reflect real working conditions · Whether the system could connect with existing systems was not properly assessed |
| **How to prevent it**          | Involve clinical and operational staff in defining what the system must do before any vendor is considered · Have the people who will use the system evaluate the options, not just IT · Visit other organisations using the same system in similar settings · Calculate the full cost including setup, ongoing support, and connecting with other systems |
| **How to spot it early**       | Review whether the system actually meets the original requirements immediately after selection · Ask staff to walk through real workflows in the system before configuration begins · Track issues raised during setup — a pattern of gaps is an early sign of fit failure |
| **What to do when it happens** | Negotiate with the vendor to address the gaps · Find configuration solutions where the gaps are manageable · Escalate to leadership if the fit problem is fundamental · Consider changing vendors before launch if the gap is serious — difficult, but less damaging than discovering it after |
| **Who is responsible**         | CIO / Chief Medical Officer                                  |

------

### T2 · Access and Infrastructure

**What goes wrong:** Staff cannot reliably access the system in the environment where they actually work.

|                                |                                                              |
| ------------------------------ | ------------------------------------------------------------ |
| **How this happens**           | Assumptions about internet reliability were never tested at actual work sites · Power supply issues were not considered, particularly in under-resourced settings · The system requires an internet connection but some locations have unreliable or no connectivity · Older devices in use are incompatible with the system · Security requirements make it harder for staff to log in quickly in a clinical setting |
| **How to prevent it**          | Assess the actual infrastructure at every site before the system is configured — not just at head office · Define what "working reliably" means for each type of site and plan accordingly · Where connectivity is a problem, ensure the system can work offline and sync when connection is restored · Agree on a device standard or bring-your-own-device policy before launch · Include power backup planning for sites where outages are a risk |
| **How to spot it early**       | Monitor system availability at each site separately · Maintain a log of access problems reported by staff · Test connectivity at actual work sites regularly, not just from central offices · Set up alerts when the system goes offline unexpectedly |
| **What to do when it happens** | Have a paper-based backup process ready so clinical work can continue during outages · Fix infrastructure problems site by site with a clear plan and timeline · Have a protocol for unplanned outages, including who to call and how long response should take · Escalate persistent infrastructure problems to leadership |
| **Who is responsible**         | IT Infrastructure Lead / Site Operations Lead                |

------

### T3 · Wrong Information, Real Consequences

**What goes wrong:** The system produces unreliable or incorrect information — often without any obvious warning.

> This is the highest-risk area in health IT technology management. Errors here can directly affect patient safety, are often invisible until someone acts on the wrong information, and are frequently misdiagnosed as user error rather than a system problem. This area requires the most rigorous attention of all six.

|                                |                                                              |
| ------------------------------ | ------------------------------------------------------------ |
| **How this happens**           | Two systems are connected but information is transferred incorrectly between them · The connection between systems fails silently — information stops flowing without any alert · Patient records are duplicated when moving to the new system · Old records use different codes or categories that do not map correctly to the new system · Information becomes corrupted when the system is used offline and then reconnected |
| **How to prevent it**          | Define and sign off exactly how each system will connect and exchange information before any build begins · Clean and review existing data before migrating it to the new system · Verify that information transferred between systems is not just technically correct but means the same thing in both · Define and test how duplicate records will be identified and resolved · Ensure the test environment uses realistic, complex data — not just simple test cases |
| **How to spot it early**       | Set up active alerts when connections between systems fail — do not rely on passive logs that no one checks · Run regular checks on data completeness and consistency · After migration, run reconciliation reports to confirm records transferred correctly · Regularly audit high-risk information such as allergy records, medication lists, and diagnoses · Create an easy way for staff to report when something in the system does not look right |
| **What to do when it happens** | Have a defined response process with clear timelines for fixing connection failures · Have a process for correcting records, with a full audit trail of what was changed and why · If incorrect information may already have been acted upon clinically, escalate immediately through patient safety channels · Fix the root cause, not just the symptom |
| **Who is responsible**         | Health Informatics Lead                                      |

------

### T4 · Staff Not Using the System

**What goes wrong:** Staff find the system too difficult, too slow, or too disconnected from how they work — and quietly find other ways to get the job done.

> This is where the technology layer meets the people layer. A system that works perfectly but that staff work around has failed. The technology map ends here; the people map continues on the other side.

|                                |                                                              |
| ------------------------------ | ------------------------------------------------------------ |
| **How this happens**           | The system takes more steps to complete a task than the old way of doing it · The system is not designed for the pace and interruptions of clinical work · Training taught staff how the system works, but not how to use it in real situations · Staff develop informal workarounds — keeping paper records alongside digital ones, entering information in batches, or skipping fields · Devices available at the point of care are not suitable for using the system efficiently |
| **How to prevent it**          | Have actual end users test the system in realistic conditions before launch — not just a demonstration · Map out how each type of staff member will complete their key tasks in the system, and compare to how they do it today · Train staff on their actual tasks, not on system features in the abstract · Identify and prepare a network of experienced users who can support colleagues after launch |
| **How to spot it early**       | Track whether staff are actually logging in and completing tasks — not just whether the system is available · Watch for signs of workarounds: paper records still being kept, fields left empty, data entered in batches rather than in real time · Review the types of issues staff are raising for patterns · Go and observe staff using the system at the point of care |
| **What to do when it happens** | Fix the specific points of friction quickly — do not wait for the next planned update cycle · Retrain on the specific tasks causing difficulty, not the whole system · Understand whether the problem is that the system is hard to use, or that staff were not adequately prepared — the response is different · Escalate persistent usability problems to the vendor |
| **Who is responsible**         | Clinical Informatics Lead / Change Manager                   |

------

### T5 · System Does Not Improve the Work

**What goes wrong:** The system is running and being used, but the improvements it was meant to deliver are not happening.

|                                |                                                              |
| ------------------------------ | ------------------------------------------------------------ |
| **How this happens**           | No one defined what improvement looked like before the project started · The system was implemented on top of a broken process — the process is now faster or more digital, but still broken · The measures of success were not agreed before launch, so there is no baseline to compare against · The system's capabilities do not match what the organisation actually needed |
| **How to prevent it**          | Define the specific improvements expected — and how they will be measured — before selecting the system · Redesign the underlying work process before configuring the system to support it · Agree with leadership on what the system is actually expected to achieve · Document a benefits plan at the start of the project and assign someone to own it |
| **How to spot it early**       | Track agreed measures of improvement from day one of launch — not months later · Assess whether the work process is actually running more effectively, not just digitally · Monitor clinical and operational outcomes that the system was intended to improve · Ask staff and managers regularly whether the system is delivering on what was promised |
| **What to do when it happens** | If the underlying process is broken, redesign it — the system cannot fix a process problem · Adjust system configuration to better support the redesigned process · If the gap between what was promised and what was delivered is fundamental, escalate to leadership · Revisit and reset the benefits plan with realistic targets and communicate the revised expectations |
| **Who is responsible**         | Chief Medical Officer / Project Sponsor                      |

------

### T6 · Decline After Launch

**What goes wrong:** The system works at launch and gradually deteriorates — performance drops, knowledge gaps grow, and small problems compound into larger ones.

> Launch is the beginning of operational management, not the end of the project. This is where underfunding has its most visible consequences.

|                                |                                                              |
| ------------------------------ | ------------------------------------------------------------ |
| **How this happens**           | Support resources were removed shortly after launch · System updates from the vendor break things that were working — and the changes go unnoticed · Staff who were trained at launch leave, and new staff are not trained to the same standard · The relationship with the vendor was not maintained after go-live · Small technical problems were never resolved and have accumulated |
| **How to prevent it**          | Plan and fund ongoing support before launch — not as an afterthought · Before support is handed over to the internal team, make sure knowledge has been properly transferred and tested · Put a formal process in place for managing system updates and changes · Maintain an active relationship with the vendor beyond the implementation phase · Keep the network of experienced users active and refreshed as staff change |
| **How to spot it early**       | Conduct a formal system health review at least every quarter · Track staff satisfaction with the system over time, not just at launch · Monitor whether the improvements achieved at launch are being maintained · Watch for increases in the volume or seriousness of issues being raised |
| **What to do when it happens** | Act on health review findings promptly · Retrain when staff turnover has created visible knowledge gaps · Escalate to the vendor when their updates have caused regressions · Prioritise and resource a plan to address accumulated technical problems |
| **Who is responsible**         | IT Operations Lead / Clinical Informatics Lead               |

------

### Key Observations Across All Six Areas

**Most failures are invisible until they become serious.** Four of the six areas can go wrong without any obvious warning sign. The system appears to be working while the failure builds quietly underneath. Detection — actively looking for problems — is as important as prevention.

**Wrong information is the most dangerous.** The system can be accessible, adopted, and strategically aligned — and still cause harm if the information it holds is wrong. This area deserves disproportionate investment and attention.

**Staff workarounds are a signal, not a discipline problem.** When staff find ways around the system, it almost always means the system is not working for them. The right response is to understand why — not to enforce compliance more strictly.

**The period after launch is when most organisations stop paying attention.** This is precisely when problems compound. The project structure is wound down, attention moves elsewhere, and small issues grow into large ones unchecked.

------

## VI. How Technology Failure Spreads to Other Layers

Technology problems do not stay within the technology layer. They trigger problems in how work gets done (Process) and how people engage with the change (People) — and failures in those layers flow back into technology in return.

### A. Technology Problems That Affect How Work Gets Done

| What Fails                             | What It Triggers                                             |
| -------------------------------------- | ------------------------------------------------------------ |
| **Wrong Information, Real Consequences (T3)**        | Staff develop informal workarounds to compensate for unreliable information. These workarounds spread unevenly — some teams develop them, others do not — and the work process becomes inconsistent across the organisation. |
| **Staff not using the system (T4)**    | When staff bypass the system, part of the process runs through the system and part runs outside it. No one has a complete picture. This parallel running — where the old and new ways of working coexist — is one of the most dangerous operational conditions in any health IT change. |
| **Access failures (T2)**               | When the system is unavailable, staff create paper-based workarounds to keep work moving. When the system comes back, both the paper process and the digital process are running simultaneously. The parallel state began from a technical event, not a deliberate decision. |
| **System not improving the work (T5)** | When the technology was built without a clear definition of what improvement would look like, the work process itself ends up equally undefined. Both the technology and the process fail for the same underlying reason: no one agreed on what success meant before the work began. |

### B. Technology Problems That Affect People and Engagement

| What Fails                          | What It Triggers                                             |
| ----------------------------------- | ------------------------------------------------------------ |
| **Wrong system selected (T1)**      | When staff perceive that the system was chosen without their input or without regard for how they actually work, trust is damaged early. People who might have been broadly supportive become resistant. Those who are already uncertain become more so. |
| **Staff not using the system (T4)** | Low adoption is visible to everyone. When staff across the organisation see that colleagues are not using the system as intended, it reads as evidence that the project is not working — and it becomes harder to sustain engagement and momentum. |
| **Decline after launch (T6)**       | Individuals who championed the system within their teams and persuaded colleagues to commit to it face a credibility problem when the system deteriorates. Peer pressure mounts. Their capacity and willingness to advocate erodes at exactly the point when it is most needed. |

### C. People and Process Problems That Feed Back into Technology

Failure travels in both directions. Technology does not only create problems elsewhere — it also receives them.

| What Fails Elsewhere                                         | How It Affects Technology                                    |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| **Current state not accurately understood (Process)**        | If the organisation did not have an accurate picture of how work was actually done before the project started, the requirements used to select and configure the system were built on a false foundation. The system is then deployed into a reality it was never designed for. |
| **New ways of working not designed to survive real conditions (Process)** | If the redesigned work process cannot hold up under real operational pressure — staffing shortfalls, high patient volumes, time constraints — staff will find ways around it. Those workarounds usually involve bypassing the system, producing the same adoption failures and data gaps that appear to be technology problems. |
| **Key decision-makers not genuinely committed to the change (People)** | A senior stakeholder who appears supportive but is not genuinely committed can shape technology decisions in ways that prioritise convenience or cost over clinical fit. The result is a system selected for the wrong reasons — which is where T1 begins. |
| **Staff not moving toward using the system (People)**        | Technology adoption requires people to change their behaviour. If the people management work has not been done — if staff have not been genuinely engaged, supported, and prepared — even a well-designed system will not achieve adoption. The technology cannot compensate for an absent people strategy. |
| **Loss of experienced advocates over time (People)**         | The people who champion the system in their teams are a critical part of what sustains it after launch. When those advocates burn out, move on, or disengage, the system loses its most important protection against post-launch decline. |

------

*Companion documents: Process Layer · People Layer · Master Document*