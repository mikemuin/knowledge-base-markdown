# MVP

This is a very smart move for a V1 launch.

By removing **Financials**, you avoid the "Integrations Nightmare" (trying to sync with SAP/Oracle) and the risk of "Shadow Accounting." By removing **Automations**, you eliminate the "Red Dot Fatigue" I warned about. You are forcing the Project Manager to actually *look* at the data and make a judgment call, which is the essence of good governance.

Here is the **V1 Product Description Sheet**. It is leaner, safer, and entirely focused on **Human Accountability**.

**Target:** Initial Launch | **Theme:** "The Manual System of Record"

## 1. Product Overview

The **PMO Governance Suite V1** is a centralized system of record for project health, execution, and decision-making.

For V1, the system prioritizes **human judgment over automation**. It does not try to guess a project's status; it asks the Project Manager to declare it. It serves as the single source of truth for *what was decided*, *what is at risk*, and *where we stand*, without duplicating financial or HR systems.

## 2. User Roles (Simplified)

- Admin: Configure dropdowns, view Portfolio Dashboard, manage user access.
- Editor: The "Captain of the Ship." Fully responsible for manually updating registers and publishing reports.
- Viewer: Read-only access to Dashboards and Reports.

------

## 3. The Navigation Menu (V1 Structure)

The sidebar is streamlined to remove complex dependencies.

> **1. GOVERNANCE**
>
> - **Overview:** High-level details (Sponsor, Dates, Phase) and the **Manual RAG Board**.
> - **Status Reports:** The weekly/monthly executive report generator.
>
> **2. EXECUTION**
>
> - **Milestones:** Critical path dates (Baseline vs. Forecast).
> - **Deliverables:** Tangible outputs and sign-offs.
> - **Action Items:** SteerCo directives and high-level tasks.
> - **Dependencies:** Internal blockers and Vendor constraints.
> - **Meeting Events:** The schedule of governance meetings.
>
> **3. CONTROL (REGISTERS)**
>
> - **Risks:** Threats to the project.
> - **Issues:** Active problems.
> - **Change Requests:** Scope/Time change log.
> - **Decisions:** The "Graveyard of Arguments" (immutable decision history).
>
> **4. KNOWLEDGE**
>
> - **Project Journal:** The social-style feed for context and updates.
> - **Meeting Minutes:** The archive of meeting notes.
> - **Stakeholders:** Directory of key players.
> - **Assets:** Notes, Links, and Lists.
> - **Lessons Learned:** Retrospectives.

------

## 4. Key Module Behaviors (V1 Rules)

### A. Status Reports (The Core Output)

- **Manual Snapshot:** When a PM creates a report, the system pulls the *current* text from the registers (e.g., the top 5 Risks), but the PM has full editing rights to rewrite the narrative before publishing.
- **Human-Driven Status:** The "Overall RAG Status" dropdown is selected by the PM. The system does not calculate this; the PM must justify their choice in the Executive Summary.
- **Immutability:** Once "Published," the report is locked in PDF format.

### B. Registers (Risks, Issues, Actions)

- **Manual Grading:** Risk Scores (Probability x Impact) are calculated, but the *severity level* (High/Med/Low) can be overridden by the PM if context requires it.
- **No Auto-Escalation:** A "High" Risk does not automatically turn the Project Red. The PM must decide if that risk warrants a Red status.

### C. Meeting Management

- **The Workflow:**
  1. **Event:** PM schedules the SteerCo in "Meeting Events."
  2. **Execution:** Meeting happens.
  3. **Record:** PM manually clicks "Create Minutes," which links to the Event.
- *Note:* No auto-flagging for missed meetings. It is the PM's professional responsibility to ensure they happen.

### D. Change Requests (Scope & Time Only)

- Since Financials are out, the CR module focuses strictly on **Time** (Schedule extensions) and **Scope** (Feature additions/removals).
- **Workflow:** Draft $\rightarrow$ Approved $\rightarrow$ Rejected. Simple state changes with a mandatory "Rationale" field.

------

## 5. Dashboards (Visuals)

### Portfolio Dashboard

- **Manual RAG Aggregation:** Displays the RAG status *as last reported* by the PMs.
- **"Stale Report" Indicator:** A simple visual cue (e.g., a gray dot) if a project hasn't published a Status Report in >30 days. This is the *only* automated logic in V1, designed to catch "zombie projects."

### Project Dashboard

- **Journal Feed:** Shows the last 3 manual entries from the Project Journal.
- **Milestone Table:** A simple list of the next 3 upcoming dates.
- **Top Risks:** List of the highest-scored risks.

------

## 6. Consultant's Closing View on V1

This version is much tighter. By removing the "magic" (automations), you increase the **trust**.

- When an Executive sees "RED," they know a human being decided it was Red, not an algorithm that glitched.
- When they see a "Decision," they know it was manually logged.

**Your success metric for V1 is simple:**

Does the PMO Lead trust the "Portfolio Dashboard" enough to present it to the CEO without double-checking the data first? If yes, you win.