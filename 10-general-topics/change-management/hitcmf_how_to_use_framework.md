# How to Use the Framework

## Overview

This framework is applied the same way across all three layers — People, Process, and Technology. For each layer, you first identify what belongs to it, then ask six questions about each item you identified, and then put your answers to work inside the actual project.

This is not a one-time exercise. You do not complete it at the start of the project and move on. It runs **alongside** the project — continuously — from the first planning meeting through go-live and beyond.

> The sequence: **Identify → Question → Implement**

------

## Step 1 — Identify

### What are we working with?

Before you can ask any questions, you need to know what you are looking at. This step is simply about taking inventory — naming the specific things that belong to each layer so you have something concrete to work with.

Think of it like listing the moving parts before you figure out which ones could break.

| Layer          | The Question to Answer   | What You Are Inventorying                                    |
| -------------- | ------------------------ | ------------------------------------------------------------ |
| **People**     | Who is involved?         | Everyone affected by or involved in the project — their roles, positions, level of support, and authority |
| **Process**    | What are we changing?    | How work is done today, how it should be done after the project, and everything in between |
| **Technology** | What tools are involved? | The systems, connections, infrastructure, data, and support structures that make the technology work |

------

### People — Who is involved?

| What to Identify                | What to Look For                                             |
| ------------------------------- | ------------------------------------------------------------ |
| The full stakeholder population | Everyone affected by or involved — by name, role, and department |
| Where each person stands        | Are they supportive, neutral, or resistant?                  |
| Champions and support gaps      | Who is actively promoting the project — and where is support missing? |
| Decision-makers                 | Who holds the authority to make decisions that matter to this project? |
| Sources of resistance           | Where is pushback coming from — and why?                     |
| The undecided middle            | The large group of people who have not yet decided how they feel |
| Formal accountability           | Who is responsible for what — and is that accountability real? |

------

### Process — What are we changing?

| What to Identify          | What to Look For                                             |
| ------------------------- | ------------------------------------------------------------ |
| Current state             | How work is actually being done today — not the official version, but the real one |
| Future state              | How work is supposed to be done after the project is complete |
| Informal processes        | The unofficial, undocumented steps people quietly rely on to get things done |
| Handoff points            | Where work passes from one person or team to another         |
| Accountability structures | Who is responsible for what — and who decides when things are unclear |
| Compliance requirements   | Rules, regulations, or standards tied to the workflow        |
| The overlap period        | Where some people are working the new way and some are still on the old way |

------

### Technology — What tools are involved?

| What to Identify            | What to Look For                                             |
| --------------------------- | ------------------------------------------------------------ |
| Systems being changed       | The system or systems being implemented or replaced          |
| Connections between systems | How different tools talk to each other and pass information  |
| Physical environment        | Internet connectivity, devices, power supply, and the realities of each location |
| Data                        | The quality of existing data and how much needs to be moved into the new system |
| System setup                | How the system is being configured and built                 |
| Training and access         | Who needs access and training — and when                     |
| Post-live support           | Who will maintain and support the system after go-live, and how |

------

## Step 2 — Question

### What do we need to think through?

Once you have identified the components of each layer, you run the same six questions against each one. These questions are the thinking work of the framework. They force you to be honest about what you know, what you are assuming, and where things could go wrong.

| #    | Question                                           | What It Forces You to Do                                     |
| ---- | -------------------------------------------------- | ------------------------------------------------------------ |
| 1    | What does success look like?                       | Define a clear, measurable target before anything else       |
| 2    | What do we think is true?                          | Surface hidden assumptions before they become surprises      |
| 3    | Where could we fail?                               | Name the risks honestly, before the pressure hits            |
| 4    | How will we know if something is going wrong?      | Build a way to see failure early — especially the quiet kind |
| 5    | What do we do now to reduce the chance of failure? | Take deliberate action before problems occur                 |
| 6    | If something fails, what do we do?                 | Prepare your response before you need it                     |

------

### Question 1 — Define Success

**What does success look like for this?**

Start here. You cannot recognize failure if you have never defined what success looks like. And you cannot define it vaguely — "things are going well" is not a success definition.

Success must be:

| Quality        | What It Means                                                |
| -------------- | ------------------------------------------------------------ |
| **Specific**   | Clear enough that two different people would agree on whether it has been achieved |
| **Observable** | Something you can actually see or measure, not just feel     |
| **Time-bound** | By when does this need to be true?                           |

What success looks like varies by layer:

| Layer          | What Success Means                                           |
| -------------- | ------------------------------------------------------------ |
| **People**     | Not just "are people on board at go-live?" but "are they still on board three months later?" Success is a condition you maintain, not a moment you reach. |
| **Process**    | The new way of working is actually happening in the real environment — not just in a training room or on paper. |
| **Technology** | The system is working where it needs to work, doing what it was supposed to do, and people are actually using it as intended. |

------

### Question 2 — List Assumptions

**What do we think is true?**

Every project runs on assumptions. The problem is that most of them are never written down — they are just quietly believed to be true. When an unspoken assumption turns out to be wrong, the team is caught off guard.

This question forces those assumptions into the open. Write down everything the project is counting on being true — even the things that seem obvious. Then sort them:

| Type            | What It Means                                   | Risk Level                           |
| --------------- | ----------------------------------------------- | ------------------------------------ |
| **Confirmed**   | You have evidence this is actually true         | Lower — but still worth tracking     |
| **Unconfirmed** | You believe it is true but have not verified it | Higher — these are risks in disguise |

Common assumptions that quietly sink projects:

| Layer          | Examples of Unconfirmed Assumptions                          |
| -------------- | ------------------------------------------------------------ |
| **People**     | "Leadership will stay engaged through go-live." "Staff will attend training." "The people we think are supportive actually are." |
| **Process**    | "The way we documented the current workflow is how people actually work." "The new process will hold up under real operational pressure." "Staff will follow the new steps consistently." |
| **Technology** | "The internet connection at each site is reliable enough." "The system will integrate cleanly with what we already have." "The vendor's system will perform the way it did in the demo." |

------

### Question 3 — Identify Risks

**Where could we fail?**

A useful reframe: *If this project fails, where would the failure come from?*

This question is not about catastrophizing. It is about being honest before the pressure hits. The risks you name now are the ones you can prepare for. The ones you leave unnamed are the ones that will surprise you.

Not every risk is equal. Sort them by what happens if they materialize:

| Severity             | What It Means                                                | Examples                                                     |
| -------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| **Low consequence**  | Inconvenient, but fixable without major disruption           | A training gap, a configuration error                        |
| **High consequence** | Expensive and damaging, but recoverable with significant effort | Adoption collapse, a delayed go-live that erodes trust       |
| **Irreversible**     | Cannot be undone                                             | A patient safety event, permanent loss of staff confidence in the system |

One category deserves special attention: **silent failures** — situations where something is going wrong but nobody can tell yet. These are the most dangerous because by the time they become visible, significant damage has already been done. If a risk could fail quietly, flag it explicitly and treat it as a higher priority.

Also consider: if this fails here, does it trigger a problem in another layer?

------

### Question 4 — Design Detection Methods

**How will we know if something is going wrong — before it becomes a crisis?**

This is the question most project teams skip. They plan for success and respond to visible failure. They rarely build a system for catching failure early — especially the quiet kind.

The goal here is to answer: *What does failure look like before anyone is ready to admit it is happening?*

Detection comes in different forms:

| Type                            | What It Looks Like                                           |
| ------------------------------- | ------------------------------------------------------------ |
| **Data and dashboards**         | System usage numbers, error rates, ticket volumes, process cycle times — things you can measure automatically |
| **Direct observation**          | Walking the floor, watching how people actually work, sitting with staff during their shift — things you can only see in person |
| **Conversations and check-ins** | Structured check-ins with key people, informal signals from champions, feedback from frontline staff who rarely speak up in meetings |
| **Behavioral signals**          | A champion who has gone quiet, a team that has stopped attending meetings, a department that was cooperative and suddenly is not |

Every detection method needs two things:

| Requirement   | What It Means                                                |
| ------------- | ------------------------------------------------------------ |
| **A cadence** | How often you check — defined in advance, not decided on the fly |
| **An owner**  | Who is responsible for checking and acting on what they find |

A detection method without both is not a management tool — it is just an intention.

------

### Question 5 — Implement Prevention Measures

**What do we do now to reduce the chance of failure?**

This question is about deliberate, proactive action — taken before anything goes wrong.

One of the core beliefs of this framework is that failure is the default outcome. Success is what you build against it. Prevention is that building work.

Examples of what prevention looks like in practice:

| Layer          | Prevention Examples                                          |
| -------------- | ------------------------------------------------------------ |
| **People**     | Identifying and cultivating champions early. Running regular structured check-ins with key stakeholders. Diagnosing resistance carefully before responding to it. Protecting people carrying the project forward from organizational burnout. |
| **Process**    | Going beyond the official documentation to understand how work actually happens. Stress-testing the new workflow before go-live. Defining clearly who is responsible for what — and confirming they have the authority to act. |
| **Technology** | Visiting each site to confirm infrastructure is actually ready. Testing the system in the real environment — not a clean demo environment. Reviewing the quality of existing data before moving it into the new system. Validating usability with actual end users. |

Every prevention measure needs an owner and a completion date. A prevention measure that is listed but not assigned is not prevention — it is wishful thinking.

------

### Question 6 — Be Ready to Respond

**If something fails, what do we do?**

A response planned during a crisis is a reaction. A response planned before a crisis is a protocol.

For each significant risk, define:

| Element                 | What It Means                                                |
| ----------------------- | ------------------------------------------------------------ |
| **The trigger**         | The specific signal that tells you the response needs to activate. Not "things are going badly" — something measurable, like "adoption rate drops below X% by week three." |
| **The steps**           | What happens, in what order, when the trigger is met         |
| **The owner**           | Who activates the response, who executes it, and who escalates if it is not working |
| **The recovery target** | What does "resolved" look like — and how do you confirm the situation is stable? |

Response must match the severity of the failure:

| Situation                      | Response                                                     |
| ------------------------------ | ------------------------------------------------------------ |
| A training gap                 | A targeted retraining session for the affected group         |
| A workflow that is not holding | A redesign session with the clinical team                    |
| A system outage                | An incident response protocol with a paper-based fallback    |
| A patient safety concern       | Immediate clinical response — containment first, root-cause analysis second |

For the highest-consequence risks — especially those involving patient safety — the response plan must include a **containment step** that happens before anything else. Stop the harm first. Understand it second.

------

## Step 3 — Implement

### Now make it real.

The first two steps are thinking work. This step is where that thinking becomes action. The outputs of the six questions do not belong in a report — they belong in the project plan, running alongside the work.

Each output gets embedded differently, because not all of them work the same way:

| Question Output     | How It Is Implemented                                        | When It Runs                                   |
| ------------------- | ------------------------------------------------------------ | ---------------------------------------------- |
| Success definitions | Embedded as milestone checkpoints — the project does not advance until criteria are met | Set once; reviewed at every milestone          |
| Assumptions         | Entered into a live Assumptions Tracker with owners and review dates | Continuously, from start through post-live     |
| Risks               | Recorded in a Risk Register, sorted by severity and assigned to owners | Set up once; updated at every milestone        |
| Detection methods   | Activated on a defined schedule with named owners            | Continuously, from day one through sustainment |
| Prevention measures | Added to the project plan as actual tasks with deadlines and owners | Actively executed throughout the project       |
| Response protocols  | Written and staged in advance; activated only when a trigger is met | Ready at all times; activated on trigger       |

------

### 3.1 — Turn Success Definitions into Checkpoints

*Set once. Governs the project until formally changed.*

Success definitions become the criteria that determine whether the project is ready to move forward. At each major milestone — design sign-off, testing completion, pilot review, go-live readiness, post-live review — the team checks: have the success criteria for this stage been met?

The project does not advance on schedule alone. It advances when the conditions for success have actually been reached. If they have not, the milestone is not cleared.

------

### 3.2 — Track Assumptions Actively

*Ongoing throughout the project lifecycle.*

Assumptions go into a live **Assumptions Tracker** — a running list that records what each assumption is, whether it has been confirmed, who is responsible for monitoring it, and when it was last reviewed.

This tracker is reviewed at every major milestone. When an assumption turns out to be wrong, it is not treated as an unexpected event — it is treated as a signal that a risk has materialized, and the corresponding response plan activates.

The tracker does not close at go-live. It carries into the post-live period, because assumptions made during planning continue to affect the project long after the system is live.

------

### 3.3 — Build and Maintain a Risk Register

*Set up once. Reviewed and updated continuously.*

All identified risks are recorded in a **Risk Register** — organized by layer, sorted by severity, and assigned to an owner. The initial setup is a one-time act. Maintaining it is ongoing work.

At every major milestone, the register is reviewed: Have any risks increased in severity? Have new risks appeared? Have any risks been resolved?

Risks that could fail silently — those where there is no obvious alarm — are flagged separately and reviewed more frequently than visible risks.

------

### 3.4 — Run Detection on a Schedule

*Active from day one through post-live sustainment.*

Detection mechanisms do not turn on at go-live. They start at the beginning of the project and run continuously. Each mechanism has a defined **schedule** and a named **owner** who reviews the signals and decides whether to escalate.

At project closure, detection does not stop — it transfers. Whoever takes over operational responsibility inherits the detection mechanisms as part of the handover. If detection ends when the project team leaves, the framework ends with it.

------

### 3.5 — Execute Prevention as Live Project Tasks

*Active work, running continuously.*

Prevention measures are the only outputs that become actual **tasks on the project plan** — with deadlines, assigned owners, and completion criteria. They are not background activities. They are scheduled work.

At every milestone review, prevention task completion is assessed as part of project health. A prevention measure that has not been executed is an unmanaged risk.

------

### 3.6 — Stage Response Protocols Before They Are Needed

*Prepared in advance. Activated only when a trigger is met.*

Response protocols are written, reviewed, and stress-tested before the situations that would require them arise. They sit ready — not running continuously, but staged and accessible.

Each protocol specifies its trigger, its steps, its owner, and its recovery target. For high-consequence risks, the protocol is reviewed during the pilot phase — when the stakes are lower and there is still room to refine it before full-scale go-live.

A response protocol that has never been reviewed is not a plan. It is a document. The difference matters when the pressure is on.

------

## Putting It Together

The three steps work as a system, not a sequence you complete and leave behind.

| Step          | What It Gives You                                            |
| ------------- | ------------------------------------------------------------ |
| **Identify**  | The map — a clear inventory of what exists in each layer     |
| **Question**  | The analysis — an honest picture of success, risks, and what to do about them |
| **Implement** | The action — outputs embedded into the project plan, running alongside the work |

The framework does not end at go-live. It ends when accountability has been formally transferred to the people who will own it in operations — and when those people have the tools, the knowledge, and the authority to keep running it.

Until that transfer is complete and confirmed, the project is not done.
