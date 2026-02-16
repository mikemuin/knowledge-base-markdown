# Product Description Sheet: Overwatch

## 1. Product Overview

The **PMO Governance Suite V1** is a centralized system of record for project health, execution, and decision-making.

For V1, the system prioritizes **human judgment over automation**. It does not try to guess a project's status; it asks the Project Manager to declare it. It serves as the single source of truth for *what was decided*, *what is at risk*, and *where we stand*, without duplicating financial or HR systems.

## 2. User Roles (Capabilities)

> **Decision (Jan 25, 2026):** V1 uses only **Admin**, **Project Editor**, and **Project Viewer**. Editors/Viewers are **assignment-scoped** (assigned projects only). Admin handles **project creation**, **configuration**, and **access control**. Viewers can **export/download PDFs**. One role per user per project (Editor **or** Viewer).

- **Admin**
  - Full access across the portfolio (all projects + Portfolio Dashboard).
  - Create and archive projects.
  - Manage users, roles, and project assignments.
  - Enforce **one role per user per project** (Editor *or* Viewer).
  - Configure system settings (dropdown values, templates, scoring/report rules).

- **Project Editor**
  - Access **assigned projects only**.
  - Create, edit, and **publish** Status Reports for assigned projects.
  - Create/edit project content across modules and registers (e.g., milestones, deliverables, actions, dependencies, meetings, RAID, decisions, knowledge).

- **Project Viewer**
  - Access **assigned projects only**.
  - View dashboards and published reports for assigned projects.
  - Export/download PDF versions of published reports.

------

## 3. The Navigation Menu (V1 Structure)

> **Decision (Jan 25, 2026): Key Modules for V1** = **Governance**, **Execution**, **Control (Registers)**, **Knowledge**.
> - **Financials:** Out (no financial module in V1)
> - **RAG:** Manual only (no calculated status / automation)
> - **Meetings:** Manual flagging only (no auto-flagging for missed meetings)
> - **Change Requests:** Scope + Time only (no budget)
> - **Knowledge:** Keep all (Journal, Minutes, Stakeholders, Notes, Links, Lists, Lessons Learned). Remove the **Assets** grouping.

> **Decision (Jan 25, 2026): Specification order (dependency-first)**
> For this document, we will define modules in the order they **depend on each other**:
> **Foundations (Projects, Users, Assignments, Cross-links)** → **Execution** → **Control (Registers)** → **Knowledge** → **Governance**.
> *Note:* UI navigation can still show Governance first; this is just the **spec walkthrough order**.

The sidebar is streamlined to remove complex dependencies.

> **1. GOVERNANCE**
>
> - **Overview:** High-level details (Sponsor, Dates, Phase) and the **Manual RAG Board**.
> - **Status Reports:** The weekly/monthly executive report generator.
>
> **2. EXECUTION**
>
> - **Milestones:** Key checkpoints and target dates.
> - **Deliverables:** Tangible outputs (no approval workflow in V1).
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
> - **Project Journal:** Project-level narrative and context.
> - **Meeting Minutes:** Minutes created from Meeting Events.
> - **Stakeholders:** Directory of key players.
> - **Notes:** Free-form project notes.
> - **Links:** Curated list of key URLs.
> - **Lists:** Simple lists/checklists.
> - **Lessons Learned:** Captured anytime.

## 4. Foundations (Cross-cutting Rules)

> **Decision (Jan 25, 2026): Foundations for V1**
> - **Project fields:** Bare minimum (Name, Sponsor, Start Date, End Date, Phase)
> - **Assignments:** Users are **assigned per project** with **one role per project** (Editor *or* Viewer)
> - **Access control:** **Admin-only** can assign users to projects
> - **Attachments/links:** Allowed on **all records** across modules
> - **Lifecycle:** **Soft-delete only** (archive/unarchive). No hard deletes in V1

### A. Projects (Minimum Metadata)

- **Required fields (V1):** Project Name, Sponsor, Start Date, End Date, Phase
- **Optional fields:** Keep minimal; anything beyond the required list is treated as “nice-to-have” for later releases.
- **Archive:** Projects can be archived by Admin; archived projects remain searchable/readable depending on permissions.

### B. Assignments & Permissions (Per-Project)

- A user must be explicitly **assigned** to a project to access it.
- Each assigned user has exactly **one role** on that project:
  - **Project Editor** *or* **Project Viewer**
- Only **Admin** can create/remove assignments and change a user’s per-project role.

### C. Attachments, Links, and Notes (Everywhere)

- Every module record can include supporting context via **attachments and/or links**.
- Attachments follow the same permission model as the parent record (no separate access layer in V1).

### D. Record Lifecycle (Soft Deletes)

- All records across modules use **soft delete** (archive/unarchive).
- Archived items are excluded from default views but can be included via filters.
- Hard delete is not available in V1.

------

## 5. Key Module Behaviors (V1 Rules)

> **Decision (Jan 25, 2026): Module dependency order**
> - Build **Execution → Control (Registers) → Knowledge → Governance**.
> - Governance is last because it summarizes the other modules.

### A. Execution (Delivery Tracking)

> **Decision (Jan 25, 2026): Execution rules**
> - **Milestone Status:** pending, on_track, at_risk, delayed, completed, cancelled
> - **Deliverables:** No approval workflow
> - **Action Items:** Target Date required; Owner optional
> - **Action Item Status:** pending, on_track, at_risk, delayed, blocked, completed, cancelled
> - **Dependency Type:** internal, external, vendor, regulatory, technical
> - **Dependencies:** Nearly the same fields as Action Items
> - **Meeting Events:** No recurring; all manual; keep Data Model fields

#### 1) Milestones
- Track key project checkpoints.
- **Minimum fields:** Title, Target Date, Status, Notes (optional), Attachments/Links (optional)
- **Status:** pending | on_track | at_risk | delayed | completed | cancelled

#### 2) Deliverables
- Track tangible outputs for the project.
- **No approval workflow** in V1.
- **Minimum fields:** Title, Target Date, Status, Notes (optional), Attachments/Links (optional)
- **Status:** pending | on_track | at_risk | delayed | completed | cancelled

#### 3) Action Items
- Track discrete tasks.
- **Rules:** Target Date required; Owner optional.
- **Minimum fields:** Title, Target Date (required), Owner (optional), Status, Notes (optional), Attachments/Links (optional)
- **Status:** pending | on_track | at_risk | delayed | blocked | completed | cancelled

#### 4) Dependencies
- Track blockers/constraints that can impact delivery.
- **Dependency Type (required):** internal | external | vendor | regulatory | technical
- **Fields:** Nearly the same as Action Items:
  - Title
  - Dependency Type
  - Target Date (required)
  - Owner (optional)
  - Status (use Action Item statuses)
  - Notes (optional)
  - Attachments/Links (optional)
  - “Related Party/Project” (optional, free-text or link)

#### 5) Meeting Events
- Meetings are **manual** in V1.
- **No recurring meetings**.
- Keep the existing Data Model fields for Meeting Events in the V1 UI (no field trimming).

### B. Control (Registers)

> **Decision (Jan 25, 2026): Control rules**
> - **No automation** in Control (no scoring, no auto-escalations, no auto-linking).
> - **Risks:** No auto-score (no Probability × Impact calculation).
> - **Issues:** Use the same status list as Action Items.
> - **Change Requests:** Scope + Schedule only (no budget). Capture **Scope Impact** + **Schedule Impact** as structured fields.
> - **Decisions:** Keep **critical items** (capture critical decisions; optionally flag criticality).
> - **No linking** across modules for now (use notes/links if needed).

#### 1) Risks
- Track future uncertain events.
- **No auto-score:** the system does not compute Probability × Impact.
- **Required fields:** Title, **Severity (manual)**, Status
- **Severity (manual):** low | medium | high | critical
- **Status:** open | mitigating | monitoring | closed | realized | cancelled
- **Optional fields (keep light):** Description, Probability (very_low/low/medium/high/very_high), Impact (negligible/minor/moderate/major/catastrophic), Proximity (immediate/near_term/medium_term/long_term), Owner (optional), Target Date (optional), Notes (optional), Attachments/Links (optional)
- **Rule:** If a risk becomes **realized**, the PM creates an Issue manually (no auto-creation).

#### 2) Issues
- Track active problems (current-state blockers).
- **Status (same as Action Items):** pending | on_track | at_risk | delayed | blocked | completed | cancelled
- **Rules:** Target Date required; Owner optional.
- **Minimum fields:** Title, Target Date (required), Owner (optional), Status, Notes (optional), Attachments/Links (optional)
- **Optional fields:** Description (if needed). Keep it minimal in V1.

#### 3) Change Requests (Scope + Schedule Only)
- Formal baseline change control for **scope and/or schedule** only.
- **Structured fields (required):**
  - **Scope Impact:** increase | decrease | none
  - **Scope Impact Description:** short text/markdown
  - **Schedule Impact:** numeric value + unit (days/weeks/months) + short description (use 0 or “none” when no schedule change)
- **Workflow (lightweight):** draft → submitted → approved / rejected → implemented (or cancelled at any stage)
- **Rules:**
  - Approval/rejection requires a rationale/notes.
  - Editors can create/manage CRs for assigned projects; Admin can override.

#### 4) Decisions (Critical Log)
- Use the Decisions log for **critical decisions** (not every minor choice).
- **Minimum fields:** Title, Context (optional), Decision, Rationale, Decision Maker (optional), Decision Date, Status, Notes (optional), Attachments/Links (optional)
- **Status:** active | superseded | reversed | cancelled
- **Rule:** Prefer **immutability** — if a decision changes, create a new decision and mark the prior one as superseded/reversed (no hard edits).

### C. Knowledge (Kept in V1)

> **Decision (Jan 25, 2026): Knowledge rules**
> - **Project Journal:** Admin + Project Editor can post. **Admin can manage all content**.
> - **Meeting Minutes:** Created **from a Meeting Event** (manual). Free-form with **two fields:** Raw Notes + Processed Notes.
> - **Stakeholders:** Keep minimum fields.
> - **Assets grouping removed:** Notes, Links, and Lists are **first-class** Knowledge items.
> - **Lessons Learned:** Capture anytime.

#### 1) Project Journal
- A running feed of project context and updates.
- **Create/edit:** Admin + Project Editor (assigned projects only). Project Viewer is read-only.
- **Admin override:** Admin can edit/delete any journal entry; Editors can manage their own entries.

#### 2) Meeting Minutes
- Minutes are created **from a Meeting Event** via a manual “Create Minutes” action.
- **Free-form (no enforced template)** with two text fields:
  - **Raw Notes:** verbatim notes / pasted content
  - **Processed Notes:** cleaned, structured summary (human-written)
- **Access:** Admin + Project Editor edit; Project Viewer read-only.

#### 3) Stakeholders
- Minimal stakeholder directory (no influence/interest scoring in V1).
- **Minimum fields:** Name, Role/Title, Organization (optional), Email (optional), Notes (optional), Attachments/Links (optional).

#### 4) Notes
- Standalone project notes (not tied to a meeting or register item).
- **Minimum fields:** Title, Body, Tags (optional), Attachments/Links (optional).

#### 5) Links
- Curated list of key project URLs (docs, trackers, folders).
- **Minimum fields:** Label, URL, Description (optional), Tags (optional).

#### 6) Lists
- Simple project lists/checklists (kept lightweight in V1).
- **Minimum fields:** Title, Items (free-text list), Notes (optional).

#### 7) Lessons Learned
- Captured anytime (not only at phase-close).
- **Minimum fields:** Title, What Happened, Lesson, Recommendation (optional), Date, Tags (optional), Attachments/Links (optional).

### D. Governance (Last)
- Governance summarizes Execution + Control + Knowledge.
- **Manual RAG** and **no automation** remain the guiding principle.
- Status Reports are published to an immutable PDF snapshot.

## 6. Dashboards (Visuals)

### Portfolio Dashboard

- **Manual RAG Aggregation:** Displays the RAG status *as last reported* by the PMs.
- **"Stale Report" Indicator:** A simple visual cue (e.g., a gray dot) if a project hasn't published a Status Report in >30 days. This is the *only* automated logic in V1, designed to catch "zombie projects."

### Project Dashboard

- **Journal Feed:** Shows the last 3 manual entries from the Project Journal.
- **Milestone Table:** A simple list of the next 3 upcoming dates.
- **Top Risks:** List of the highest-scored risks.

------

## 7. Consultant's Closing View on V1

This version is much tighter. By removing the "magic" (automations), you increase the **trust**.

- When an Executive sees "RED," they know a human being decided it was Red, not an algorithm that glitched.
- When they see a "Decision," they know it was manually logged.

**Your success metric for V1 is simple:**

Does the PMO Lead trust the "Portfolio Dashboard" enough to present it to the CEO without double-checking the data first? If yes, you win.