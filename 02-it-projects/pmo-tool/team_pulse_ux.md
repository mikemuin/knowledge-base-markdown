# Team Pulse UX

This is the user experience (UX) breakdown. To make this tool successful, it cannot look like a spreadsheet. It needs to feel like a "Wizard" or a linear flow—similar to how Typeform or a modern checkout process works.

The user logs in on Friday afternoon. They don't navigate a complex menu. They see one big button: **"Start Weekly Pulse."**

Here is the step-by-step flow for the end-user.

------

### The Navigation Menu (Sidebar)

*It is intentionally minimal. We want them in and out.*

> **MY HOME**
>
> - **Current Pulse** (The active weekly draft)
> - **History** (My past submissions)
> - **My Profile** (Settings)

------

### The "Friday Ritual" Workflow

*When the user clicks "Start," they are taken through 4 focused screens.*

#### Screen 1: The Vibe Check (Capacity)

*Before asking what they did, we ask how they are. This captures the "Human" element.*

> **How was your week, [Name]?**
>
> **1. Workload Level:**
>
> [ ] Light  [ ] Balanced  [ ] Full  [X] Overloaded
>
> **2. Sentiment:**
>
> [😊]  [😐]  [😫]
>
> **3. Note to [Line Manager Name]:**
>
> *(Visible ONLY to your functional manager)*
>
> *"I'm struggling with the context switching between Project A and B. Can we chat?"*
>
> [ **NEXT** ]

------

#### Screen 2: The Project Deck (The Matrix)

*The system knows what you are assigned to. It deals the cards.*

> **Your Assigned Projects**
>
> **CARD 1: Mobile App Redesign (Project A)**
>
> - *What did you ship?*
>   - [User types](https://www.google.com/search?q=None.): Completed the Login API endpoints.
>   - [User types](https://www.google.com/search?q=None.): Fixed the crash on iOS 15.
> - *Blockers/Risks:*
>   - [User types](https://www.google.com/search?q=None.): Waiting for UX team to send the new icons.
>
> **CARD 2: Cloud Migration (Project B)**
>
> - *What did you ship?*
>
>   - [User types](https://www.google.com/search?q=None.): Updated the database schema.
>
> - *Blockers/Risks:*
>
>   *
>
>   [ **NEXT** ]

------

#### Screen 3: The "Everything Else" (BAU)

*For the work that isn't on a project code.*

> **Internal & Shadow Work**
>
> - *Other contributions (Hiring, Support, Training):*
>   - [User types](https://www.google.com/search?q=None.): Interviewed 2 candidates for the Junior Dev role.
>   - [User types](https://www.google.com/search?q=None.): Spent 4 hours fixing the printer for the Sales team.
> - *Plans for Next Week:*
>   - [User types](https://www.google.com/search?q=None.): Focus on Project A integration.
>
> [ **NEXT** ]

------

#### Screen 4: The Highlight Reel

*The career-building step.*

> **Top Highlight**
>
> *What is the one thing you want remembered from this week?*
>
> [User types](https://www.google.com/search?q=None.): **Solved a memory leak that has been plaguing the app for 3 months.**
>
> [ **SUBMIT PULSE** ]

------

### Scenario A: The Software Developer

*(Works on 2 Projects + Internal Tasks)*

**User Input:**

- **Project A:** "Refactored the payment gateway."
- **Project B:** "Wrote unit tests for the search bar."
- **BAU:** "Mentored Sarah on React hooks."
- **Blocker:** "Project B is stalled because the test environment is down."

**The Resulting Routing:**

1. **Project Manager A** gets an alert: "Dev Refactored payment gateway."
2. **Project Manager B** gets an alert: "Dev wrote unit tests" AND **"BLOCKER: Test environment down."** (PM B acts immediately).
3. **Line Manager (Dev Lead)** sees: "This Dev is productive, but flagged 'Overloaded'. They are doing a lot of mentoring (BAU). I need to check in on them."

------

### Scenario B: The Sales Rep

*(Works on 0 Projects + Pure BAU)*

*The system recognizes they have no PMO assignments, so Screen 2 is skipped.*

**User Input:**

- **Project Cards:** (None appear).
- **BAU / Internal:**
  - "Completed 40 cold calls."
  - "Demoed to Client X."
  - "Updated the CRM."
- **Blocker:** "Legal team is slow on the Contract for Client X."
- **Highlight:** "Closed the $50k deal with Acme Corp."

**The Resulting Routing:**

1. **Project Managers:** Receive nothing (Zero noise).
2. **Sales Director (Line Manager):** Sees the activity log, sees the Blocker regarding Legal (and goes to yell at Legal), and sees the "Highlight" for the weekly shout-out email.

------

### Consultant's Note:

Do you see the difference?

- The **PMO Tool** (Governance) is a **form-heavy database** for controlling the project.
- The **Team Tool** (Pulse) is a **lightweight wizard** for capturing the flow of work.

This separation is why "TeamPulse" works. It feels like a 5-minute journal entry, not a compliance tax.