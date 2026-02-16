# Chat Summary

Here is a consolidated summary of the product discussions. This document captures the evolution, final architecture, and strategic constraints of the **PMO & Governance Suite (V1)** to provide full context for the next session.

------

# Context Summary: Enterprise PMO & Governance Suite (V1)

## 1. Product Vision

The product is a **PMO (Project Management Office) System of Record**.

- **It IS:** A governance tool for standardization, auditability, executive reporting, and decision tracking.
- **It is NOT:** A task management tool (like Jira/Trello) for developers, nor a financial ERP.
- **Core Philosophy:** "The Manual System of Record." The tool prioritizes **human judgment over automation**. It does not guess status; it requires Project Managers to explicitly declare it.

## 2. Target Audience & Roles

- **PMO Lead (Admin):** Process owner. Configures the system (dropdowns, templates) and views the Master Portfolio.
- **Project Manager (Editor):** The operational owner. Responsible for data entry, maintaining registers, and publishing the "Status Report."
- **Executive Stakeholder (Viewer):** Read-only consumer of Dashboards and immutable Reports.

## 3. The Architecture (The 4 Pillars)

The application is structured into four distinct domains to separate **Governance** (Reporting) from **Execution** (Doing).

### A. GOVERNANCE

- **Project Hub:** Metadata (Phase, Sponsor, Dates) and the **Manual RAG Board** (Red/Amber/Green status for Schedule, Scope, Quality).
- **Status Reports:** The "Heartbeat." A snapshot engine that freezes project data into an immutable executive summary (PDF-style). Supports versioning (Revised reports).

### B. EXECUTION

- **Milestones:** Critical path tracking (Baseline vs. Forecast dates).
- **Deliverables:** Tangible outputs and sign-off tracking.
- **Action Items:** High-level directives (e.g., SteerCo instructions), not low-level tasks.
- **Dependencies:** (Formerly "Waiting Fors"). Tracks internal blockers and external vendor constraints.
- **Meeting Events:** The calendar/schedule view of governance meetings. (Distinct from Minutes).

### C. CONTROL (REGISTERS)

- **Risks:** Future uncertain events. Scored by Probability (1-5) $\times$ Impact (1-5).
- **Issues:** Active problems currently impacting the project.
- **Change Requests:** Formal workflow for altering Scope or Timeline (Draft $\rightarrow$ Approved $\rightarrow$ Rejected).
- **Decisions:** An immutable log of strategic decisions ("The Graveyard of Arguments").

### D. KNOWLEDGE

- **Project Journal:** A social-media-style "News Feed" for ad-hoc updates (e.g., "Vendor delayed due to weather").
- **Meeting Minutes:** The permanent archive of notes, linked to the "Meeting Events."
- **Stakeholders:** Directory of key players (Influence/Interest mapping).
- **Assets:** Consolidated container for Notes, Links, and Lists.
- **Lessons Learned:** Retrospectives.

## 4. Key Design Decisions & Constraints (V1)

### Universal Features

- **Notes as Audit Trail:** Every item (Risk, Action, etc.) has a timestamped, immutable "Notes" stream to log activity.
- **Soft Deletes:** Data is never destroyed, only archived, to preserve audit history.

### Strategic Exclusions (Scope Cuts)

1. **No Financials:** Budget/Spend tracking is removed for V1. We assume this is handled in an external ERP.
2. **No Automations/Auto-Flagging:** The system will **never** automatically turn a project "Red" (e.g., because a meeting was missed). RAG status changes must be manual decisions made by the PM to prevent "alert fatigue."
3. **No Resource Management:** We are not tracking individual user capacity or hours.

### Specific Workflows

- **Meetings vs. Minutes:** Split into two modules. "Meeting Events" (Execution) tracks *if* it happened. "Meeting Minutes" (Knowledge) tracks *what was said*.
- **Journal to Status Report:** The "Project Journal" captures daily noise/context, which informs the formal weekly "Status Report."

## 5. Current Status

The conceptual design is complete. The next phase (if applicable) would likely involve detailed UI wireframing or database schema design based on the **Product Description Sheet V1**.