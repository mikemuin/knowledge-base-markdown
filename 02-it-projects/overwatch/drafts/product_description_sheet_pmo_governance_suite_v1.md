# Product Description Sheet: PMO Governance Suite V1

AI: Claude

## 1. Product Overview

The **PMO Governance Suite V1** is a centralized system of record for project health, execution, and decision-making.

For V1, the system prioritizes **human judgment over automation**. It does not try to guess a project's status; it asks the Project Manager to declare it. It serves as the single source of truth for *what was decided*, *what is at risk*, and *where we stand*.

**Core Philosophy:** This system captures PM decisions; it does not make decisions. Manual control, flexible organization, and immutable history define the V1 approach.

------

## 2. User Roles

**Admin**

- Full CRUD access across ALL projects
- System configuration (dropdowns, settings, workflow states)
- User access management
- Portfolio Dashboard access
- The "PMO Lead" who oversees the entire portfolio

**Editor**

- Full CRUD access on ASSIGNED projects only
- Cannot access other projects' data
- The "Captain of the Ship" for their project
- Fully responsible for manually updating registers and publishing reports
- Creates and manages all project content

**Viewer**

- Read-only access across ALL projects
- Can view Dashboards and Reports
- Cannot edit any content
- Typically Executive Stakeholders, external consultants, or observers

------

## 3. The Navigation Menu (V1 Structure)

The sidebar provides a flat, logical structure for all project management functions.

### 1. GOVERNANCE

**Overview**

- High-level project details (Code, Title, Description)
- Project dates (Start, Target End, Actual End)
- Key people (Project Manager, Executive Sponsor)
- Project phase/status
- **Manual RAG Board:** PM selects Red/Amber/Green status from dropdown

**Status Reports**

- The weekly/monthly executive report generator
- PM creates draft, manually edits narrative, then publishes
- Once published, report is locked (immutable)
- System captures snapshot of current state at publication time

------

### 2. EXECUTION

**Milestones**

- Critical stage gates and project checkpoints
- Baseline vs. Target vs. Actual dates
- Status tracking (pending, on_track, at_risk, delayed, completed, cancelled)
- Critical path flagging
- Auto-computed target week (ISO 8601)
- Supports 2-level grouping for organization

**Deliverables**

- Tangible outputs and sign-offs
- Target dates with auto-computed weeks
- Status and approval workflow
- Linked to approvers
- Supports 2-level grouping

**Action Items**

- SteerCo directives and high-level tasks
- Assigned to specific users
- **Manual priority setting** (low, medium, high, critical)
- Target dates with status tracking
- Supports 2-level grouping

**Dependencies**

- Internal blockers and vendor constraints
- Dependency type classification
- Impact assessment
- Target resolution dates
- Supports 2-level grouping

**Meeting Events**

- Schedule of governance meetings (SteerCo, working groups, status reviews)
- Calendar and meeting links
- Status tracking (scheduled, completed, cancelled, rescheduled)
- Links to Meeting Minutes
- Supports 2-level grouping

------

### 3. CONTROL (REGISTERS)

**Risks**

- Future uncertain events that could impact the project
- **Manual severity assessment** by PM based on probability and impact
- Proximity tracking (when risk might materialize)
- Mitigation strategy and contingency plans
- Owner assignment
- Status lifecycle tracking
- Supports 2-level grouping

**Issues**

- Active problems hindering project progress
- Impact and urgency assessment
- **Manual priority setting** by PM (independent of impact/urgency)
- Owner assignment and escalation workflow
- Resolution tracking
- Supports 2-level grouping

**Change Requests**

- Formal workflow for **Scope and Schedule changes only**
- No budget/financial change requests in V1
- Workflow states: Draft → Submitted → Under Review → Approved/Rejected → Implemented
- Mandatory justification and impact assessment
- Schedule impact tracking (days)
- Approval notes and audit trail

**Decisions**

- The "Graveyard of Arguments"
- Immutable record of strategic decisions
- Context, decision, rationale, alternatives, implications
- Decision maker and date
- Locked after creation window
- Can be superseded by new decisions (with linkage)
- Cannot be deleted or edited once locked

------

### 4. KNOWLEDGE

**Project Journal**

- The social-style feed for context and updates
- Timeline of project-level announcements, milestones, incidents, changes
- Can link to specific domain items (Milestones, Risks, Decisions, etc.)
- Recent entries displayed on Project Dashboard

**Meeting Minutes**

- Archive of formal meeting records
- Links to Meeting Events
- Agenda, minutes, action items summary, decisions summary
- Attendees tracking
- Full-text searchable

**Stakeholders**

- Directory of key players
- Role in project, influence, interest levels
- Contact information
- Engagement strategy
- Stakeholder matrix visualization (influence vs. interest)

**Links**

- Centralized repository of project-related URLs
- Grouped and organized (2-level hierarchy)
- Descriptions and positioning

**Lists**

- Flexible checklists and list management
- List items with checked/unchecked status
- Tracked by user and timestamp
- Grouped and organized (2-level hierarchy)

**Lessons Learned**

- Structured retrospective tool
- Categorized by phase and type
- What went well/wrong, root cause, recommendations
- Severity flagging
- Exportable for organizational knowledge base

------

## 4. Key Module Behaviors (V1 Rules)

### A. Status Reports (The Core Output)

**Manual Snapshot Technology:**

- When PM creates a report, system pulls current data from registers (e.g., top 5 Risks)
- **PM has full editing rights** to rewrite narrative before publishing
- PM can adjust the auto-pulled content to tell the real story

**Human-Driven Status:**

- The "Overall RAG Status" dropdown is **manually selected by the PM**
- System does NOT calculate this automatically
- PM must justify their choice in the Executive Summary
- When Executive sees "RED," they know a human being decided it was Red, not an algorithm

**Immutability:**

- Once "Published," the report is locked
- Published reports become permanent PDF-style records
- No edits allowed after publication
- System captures snapshot_data (JSON) of metrics at publication time

------

### B. Registers (Risks, Issues, Actions, Dependencies)

**Manual Grading Philosophy:**

- **Risk Assessment:** Probability and Impact fields exist as reference, but PM makes final severity call
- **Issue Priority:** Impact and Urgency are tracked, but PM manually sets Priority based on full context
- **No Auto-Escalation:** A "High" Risk does not automatically turn the Project Red
- **PM Professional Judgment:** The system trusts the PM to make the right call

**Commentary System:**

- Universal Notes/Comments on every item
- Attach context, rationale, and updates to any entity
- Immutable once created (permanent audit trail)
- Chronological timeline view

**Grouping for Organization:**

- All registers support 2-level grouping (Parent → Child → Items)
- Provides structure without overwhelming complexity
- Groups are optional but recommended for large projects

------

### C. Meeting Management

**The Workflow:**

1. **Schedule:** PM creates Meeting Event with date, links, type
2. **Execute:** Meeting happens (outside the system)
3. **Document:** PM clicks "Create Minutes," which links to the Meeting Event
4. **Reference:** Minutes are searchable and form permanent meeting archive

**No Auto-Flagging:**

- System does not automatically alert for missed meetings
- PM professional responsibility to ensure meetings happen
- If a meeting is cancelled/rescheduled, PM updates the Meeting Event status

------

### D. Change Requests (Scope & Schedule Only)

**V1 Scope:**

- Since **Financials are out**, CR module focuses strictly on:
  - **Schedule:** Extensions or acceleration of timelines
  - **Scope:** Feature additions, removals, or modifications

**Workflow:**

- Draft → Submitted → Under Review → Approved/Rejected → Implemented
- Simple state changes with mandatory "Justification" and "Impact Assessment" fields
- Once a CR is approved, the PM manually updates project baseline dates, milestones, or deliverables to reflect the approved changes
- The CR serves as the permanent record of what change was authorized and why
- Full audit trail of all changes and approvals

------

### E. Visibility Control

**is_internal_only Flag:**

- Available on all entities (Milestones, Deliverables, Action Items, Dependencies, Meetings, Risks, Issues, Decisions, Journal Entries, Meeting Minutes, Links, Lists)
- Controls visibility of sensitive information
- PM decides what to hide from external viewers (consultants, vendors, external stakeholders)
- Example: Mark sensitive Risks as internal-only to prevent vendor access

------

## 5. Dashboards (Visuals)

### Portfolio Dashboard (Admin View)

**Manual RAG Aggregation:**

- Displays RAG status **as last reported by PMs**
- No algorithmic computation
- Simple count: X projects Red, Y projects Amber, Z projects Green

**"Stale Report" Indicator:**

- The **only automated logic in V1**
- Visual cue (gray dot, warning icon) if project hasn't published Status Report in >30 days
- Catches "zombie projects" where PM isn't actively managing
- Simple rule: `days_since_last_report > 30`

**Top Risks Across Portfolio:**

- Aggregated list of highest-severity risks (based on PM manual assessment)
- Filtered by status (active risks only)

**Overdue Items:**

- Count of overdue Action Items by project
- Count of at-risk Milestones by project

------

### Project Dashboard (Editor/Viewer View)

**Manual RAG Status Display:**

- Shows current RAG as declared by PM
- No trend calculation or prediction

**Journal Feed:**

- Last 3 manual entries from Project Journal
- Quick context for what's happening

**Milestone Timeline:**

- Next 3 upcoming milestones
- Simple list with dates and status

**Top Risks:**

- List of highest-severity risks (PM assessed)
- Current mitigation status

**Open Issues Count:**

- Simple counter of unresolved issues
- Categorized by impact level

**Recent Activity Feed:**

- Latest notes/comments across all modules
- Chronological timeline of project activity
- Shows WHO said WHAT and WHEN

------

## 6. Automation vs. Manual Control Summary

### What V1 AUTOMATES (Minimal)

**Target Week Calculation:** ISO 8601 week auto-computed from target dates (pure math) ✅ **Scheduled Week Calculation:** ISO 8601 week auto-computed from meeting scheduled dates ✅ **Stale Report Detection:** Flag projects with no Status Report >30 days

### What V1 DOES NOT AUTOMATE (Manual PM Control)

**RAG Status:** PM selects Red/Amber/Green manually from dropdown ❌ **Risk Scoring:** No probability × impact calculation; PM assesses severity ❌ **Issue Priority:** No impact + urgency formula; PM sets priority manually ❌ **Status Derivation:** System never auto-changes status based on downstream data ❌ **Escalation Triggers:** No automatic alerts or status changes

**Design Principle:** If it requires judgment, the PM makes the call. If it's pure math, the system calculates it.

------

## 7. Data Integrity & Immutability

### Immutable Records (Cannot Be Changed After Creation/Publication)

**Status Reports:**

- Once `published_at` is set, all fields are locked
- Becomes permanent historical record
- PDF-generation ready

**Decisions:**

- Once `is_locked` is set, no edits allowed
- Can be superseded by new decision (with linkage)
- "Graveyard of Arguments" philosophy

**Notes/Comments:**

- Once created, cannot be edited or deleted
- Permanent audit trail
- Shows WHO said WHAT and WHEN forever

**Audit Log:**

- System automatically logs all field changes
- Old value → New value, user, timestamp
- Cannot be modified or deleted

------

## 8. Consultant's Closing View on V1

This version is **tight, trustworthy, and human-centric**.

### Why V1 Works

**Trust Through Transparency:**

- When an Executive sees "RED," they know a PM decided it was Red, not an algorithm that glitched
- When they see a "Decision," they know it was manually logged and locked
- No black-box logic—everything is traceable to a person

**PM Empowerment:**

- The system respects PM expertise
- Provides structure without imposing rigid automation
- Editor role = "Captain of the Ship" philosophy

**Simple But Sufficient:**

- Focused on essentials: What happened? What's at risk? What was decided?
- No feature bloat or "nice-to-have" distractions
- 2-level grouping provides organization without complexity

**Immutable History:**

- Once something is published or locked, it becomes permanent record
- Supports governance and audit requirements
- "What was decided and why" is always traceable

### Success Metric for V1

**Does the Admin (PMO Lead) trust the Portfolio Dashboard enough to present it to the CEO without double-checking the data first?**

If **yes**, you win.

If the PMO Lead can confidently open the Portfolio Dashboard in a C-suite meeting and say "Here's where we stand" without pre-vetting every number, the system has achieved its purpose.

------

## 9. V1 Design Constraints & Principles

### What's IN Scope

Manual RAG status declaration ✅ Immutable decisions and published reports ✅ Universal notes/comments system ✅ 2-level grouping across all registers ✅ Visibility control (is_internal_only) ✅ Scope and Schedule change requests ✅ Project Journal with linking to domain items ✅ Meeting Minutes linked to Meeting Events ✅ Auto-computed target weeks (ISO 8601)

### What's OUT of Scope

Financial tracking (budgets, spend, variance) ❌ Auto-computed RAG status ❌ Auto-computed risk scores ❌ Auto-computed issue priority ❌ Auto-escalation workflows ❌ Wiki functionality ❌ General cross-entity linking (beyond Journal → Items, Minutes → Meeting) ❌ More than 2-level grouping hierarchy

### Core Principles

1. **Human Judgment First:** Capture PM decisions, don't make them
2. **Manual Control:** Status, priority, severity = PM declares
3. **Simple Math Only:** Date-to-week conversion is the only automation
4. **Immutable History:** Published reports, decisions, notes cannot change
5. **Flexible Organization:** 2-level grouping for structure
6. **Selective Visibility:** Control what external stakeholders see
7. **Universal Commentary:** Attach context to anything

------

## 10. Implementation Roadmap

### Phase 1: Foundation

- Projects, Users (3 roles), Notes system
- Core RBAC implementation

### Phase 2: Execution Module

- Milestones, Deliverables, Action Items, Dependencies, Meeting Events
- All with 2-level grouping support

### Phase 3: Control Registers

- Risks, Issues, Change Requests, Decisions
- All with 2-level grouping support
- Manual assessment workflows

### Phase 4: Knowledge Module

- Project Journal, Meeting Minutes, Stakeholders
- Links, Lists, Lessons Learned
- All with appropriate grouping

### Phase 5: Governance

- Status Reports with snapshot technology
- Manual RAG declaration
- Publish/lock workflow

### Phase 6: Cross-Cutting Features

- Journal → Domain Item linking
- Minutes → Meeting Event linking
- Audit trail integration
- Stale Report detection

### Phase 7: Dashboards

- Portfolio Dashboard (Admin view)
- Project Dashboard (Editor/Viewer view)
- Reporting views

------

## 11. User Experience Highlights

### For Editors (Project Managers)

- **Single Entry Point:** All project data in one place
- **Flexible Updates:** Update registers at own pace, publish reports when ready
- **Context Preservation:** Notes on everything—never lose the "why"
- **No Guesswork:** You declare status; system doesn't override your judgment
- **Organization:** Group items logically without complex hierarchies

### For Admins (PMO Leads)

- **Portfolio View:** See all projects' manually-declared RAG status at a glance
- **Stale Detection:** Automatic flag for projects going dark
- **Risk Aggregation:** Top risks across portfolio
- **User Management:** Control who can edit what
- **System Configuration:** Manage dropdowns, workflow states

### For Viewers (Executives/Stakeholders)

- **Read-Only Trust:** See what PMs have declared without edit risk
- **Historical Reports:** Access immutable Status Report archive
- **Decision Trail:** Review permanent decision log ("Why did we do that?")
- **Filtered Visibility:** See only what's relevant (internal-only items hidden)

------

## 12. FAQ & Clarifications

**Q: Why no automated RAG calculation?** A: Trust. When you see "Red," you know the PM assessed the full context—not just that a formula triggered. Human judgment > algorithms for executive confidence.

**Q: Why only Scope and Schedule change requests?** A: V1 doesn't track financials. Budget change requests would have nowhere to integrate. Focus on what matters: timeline and deliverables.

**Q: Why limit grouping to 2 levels?** A: Balance. One level is too flat for large projects; three levels becomes folder-hell. Two levels = just right.

**Q: Can I undo a published Status Report?** A: No. Once published, it's locked. This is by design—immutable history is trustworthy history.

**Q: Why have is_internal_only if we have roles?** A: Roles control project access; is_internal_only controls item visibility within a project. An external consultant might be a Viewer on the project but shouldn't see sensitive internal risks.

**Q: What happens to notes if I soft-delete an item?** A: Notes remain attached and visible in audit history. When you restore the item (is_active = true), notes reappear.

**Q: Can Editors see other Editors' projects?** A: No. Editors only see their assigned projects. This prevents cross-contamination and maintains clear ownership.

------

## 13. Final Words

**V1 is intentionally constrained.** It doesn't try to be everything to everyone. It solves one problem exceptionally well:

> **"Give me a trustworthy, human-driven system of record for project governance that respects PM judgment and provides immutable history."**

No magic. No black boxes. No "the system decided for you."

Just:

- What the PM declared
- What was decided
- What is at risk
- Where we stand

If you execute V1 well, it becomes the **source of truth** everyone relies on—not because it's fancy, but because it's **trustworthy**.

**That's the win.**

------

**Document Version:** V1.0 (Harmonized with Data Models V1) **Last Updated:** 2025-01-25 **Status:** Final for V1 Implementation