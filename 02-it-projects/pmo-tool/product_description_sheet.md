# Product Description Sheet

**Version 1.3**

## 1. Product Overview

The **Enterprise PMO & Governance Suite** is a centralized platform that elevates project management from simple task tracking to strategic governance. It acts as the organization’s system of record, ensuring that every project is measured by the same standards, financial rigors, and reporting structures.

## 2. User Profiles & Access Control

- **PMO Lead (Administrator):** Process owner with full portfolio visibility and configuration rights.
- **Project Manager (Editor):** Operational owner responsible for delivery, reporting, and data accuracy.
- **Executive Stakeholder (Viewer):** Read-only consumer of Dashboards and Published Reports.

------

## 3. Universal Capabilities (Cross-Module Features)

*These features apply to every single module in the system (Risks, Actions, Deliverables, etc.).*

### A. Contextual Notes & Status Logs

Every individual record (e.g., *Risk #104*, *Action Item #22*) includes a dedicated **Notes/Comments Stream**.

- **Purpose:** Acts as a chronological log of remarks, updates, and decisions specific to that item.
- **Functionality:**
  - **Timestamped:** Every note is automatically stamped with the User, Date, and Time.
  - **Immutable:** Notes cannot be deleted, ensuring a permanent history of the item's lifecycle.
  - **Usage Example:** A user posts a note on *Action Item #22*: "Spoke to vendor; they promised delivery by Tuesday." This explains the status change without altering the core data fields.

### B. Audit Trail

System automatically logs field changes (e.g., "Priority changed from Low to High") alongside the user-generated Notes, creating a complete timeline of the item's history.

------

## 4. Functional Modules

### A. GOVERNANCE (The "Steering Wheel")

- **Project Overview Hub:** Landing page with metadata and live **RAG Status**.
- **Status Reports:** Engine for creating immutable executive summaries. Uses "Snapshot Technology" to freeze data.
- **Financials:** Tracks Original Budget, Approved Budget, Actual Spend, and Forecast.

### B. EXECUTION (The "Engine")

- **Milestones:** Tracks major stage gates. Notes here explain *why* a date slipped.
- **Deliverables:** Manages tangible outputs. Notes here track draft feedback and sign-off remarks.
- **Action Items:** High-level directives. Notes here function as a progress log.
- **Dependencies:** Formal register for blockers. Notes here document chase-up emails to vendors.
- **Meeting Events:** Tracks governance meetings. If a meeting is missed, the system flags the project schedule.

### C. CONTROL REGISTERS (The "Guardrails")

- **Risk Register:** Tracks future uncertain events with Probability/Impact scoring.
  - *Usage:* Notes are used to document the evolution of the risk (e.g., "Risk probability reduced due to new mitigation").
- **Issue Log:** Tracks active problems. Notes serve as the "Resolution Diary."
- **Change Requests (CR):** Formal workflow for baseline changes.
- **Decision Log:** Permanent record of strategic decisions.

### D. KNOWLEDGE & ASSETS (The "Library")

- **Project Journal (The "News Feed"):** A timeline for broad, project-level updates (e.g., "Office closed for snow," "Key stakeholder resigned"). Distinct from item-level notes.
- **Meeting Minutes Archive:** Repository for meeting records.
- **Stakeholder Register:** Directory of individuals impacting the project.
- **Project Assets:** A container for unstructured *Project-Level* data.
  - **General Wiki:** For high-level project context that doesn't fit into a specific item.
  - **Links & Lists:** URLs and ad-hoc checklists.
- **Lessons Learned:** Structured retrospective tool.

------

## 5. Dashboards & Visibility

### The Portfolio Command Center

- **Aggregate Health:** Visual count of Red/Amber/Green projects.
- **Financial Roll-up:** Portfolio-wide spend analysis.

### The Project Health Dashboard

- **Trend Indicators:** Visuals showing health direction ($\uparrow$ $\rightarrow$ $\downarrow$).
- **Journal Feed:** Displays recent "Project Journal" entries.
- **Recent Activity:** Displays the latest **Notes** added across the project (e.g., "John added a note to Risk #5"), providing a pulse on team activity.

------

## 6. Technical Specifications

- **Immutable Audit Trail:** All edits to Decision Logs and Published Reports are locked.
- **Soft-Delete Architecture:** Data is archived, never destroyed.
- **Cross-Module Linking:** Ability to link an Issue to a Change Request, or a Meeting Minute to a Decision.