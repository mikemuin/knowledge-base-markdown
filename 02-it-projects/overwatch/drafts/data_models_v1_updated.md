# Data Models - V1

## What?

This is an **Enterprise PMO & Governance Suite** – a centralized platform for strategic project oversight. The application tracks projects across their full lifecycle, managing governance, execution, control registers, and knowledge assets. It enforces standardized measurement and reporting structures across the portfolio.

**V1 Philosophy:** This system prioritizes **human judgment over automation**. It does not attempt to derive project status algorithmically; it asks the Project Manager to declare it. The system serves as the single source of truth for *what was decided*, *what is at risk*, and *where we stand*.

---

## Cross-Cutting Concerns

### Universal Features (Applied to ALL modules)

Every record in the system includes:

#### A. Contextual Notes & Comments
- **Implementation:** Polymorphic `notes` table
- **Purpose:** Universal comment system providing chronological, immutable audit trail of remarks, context, and decisions across all entities
- **Fields:**
  - id (PK)
  - notable_type (polymorphic relationship)
  - notable_id (polymorphic relationship)
  - content (text)
  - is_system_generated (Boolean) - distinguishes user comments from auto-generated audit entries
  - metadata (created_at, created_by - immutable)

#### B. Audit Trail
- **Implementation:** System-level event logging (Laravel's activity log or custom audit table)
- **Captures:** All field changes with old/new values, user, timestamp
- **Integration:** Audit entries may be surfaced alongside user notes in the timeline view

---

## Core Entities

### Projects (projects)

The foundational entity representing a strategic initiative.

#### Data Model
- id (PK)
- code (unique identifier, e.g., "PRJ-2024-001")
- title
- description (markdown)
- status (draft, active, on_hold, completed, cancelled)
- rag_status (red, amber, green) - **Manually set by PM** - Overall health indicator
- start_date
- target_end_date
- actual_end_date
- project_manager_id (FK to users)
- executive_sponsor_id (FK to users)
- is_active (Boolean)
- metadata (created_at, created_by, updated_at, updated_by)

#### Queries/Processes
- CRUD operations
- **Manual RAG Status:** PM selects Red/Amber/Green from dropdown based on their professional judgment
- Portfolio rollup queries (count by RAG status)
- Query filters: status, rag_status, project_manager, executive_sponsor

---

## A. GOVERNANCE MODULE (The "Steering Wheel")

### Status Reports (status_reports)

Immutable executive summaries using "Snapshot Technology."

#### Data Model
- id (PK)
- project_id (FK)
- report_period_start
- report_period_end
- overall_status (red, amber, green) - **Manually selected by PM**
- executive_summary (markdown)
- achievements (markdown)
- challenges (markdown)
- next_period_focus (markdown)
- snapshot_data (JSON) - Frozen metrics at time of publication (milestone status, risk count, issue count, etc.)
- published_at (immutable once set)
- published_by (FK to users)
- is_draft (Boolean)
- metadata (created_at, created_by, updated_at, updated_by)

#### Queries/Processes
- Create draft reports
- PM manually edits all narrative content before publishing
- Publish reports (locks all fields, triggers snapshot)
- Read published reports (chronological archive)
- Query filters: project_id, report_period, is_draft

#### Business Rules
- Once `published_at` is set, no fields can be modified
- Snapshot captures: milestone status counts, risk counts, issue counts, change request counts
- **No financial data** in snapshots
- PM must justify RAG status selection in Executive Summary

---

## B. EXECUTION MODULE (The "Engine")

### Milestones (milestones)

Major stage gates in the project lifecycle.

#### Data Model
- id (PK)
- project_id (FK)
- milestone_group_id (FK) - nullable
- title
- description (markdown)
- baseline_date (original planned date)
- target_date
- target_week (ISO 8601) - **Auto-computed from target_date**
- actual_date
- status (pending, on_track, at_risk, delayed, completed, cancelled)
- is_critical_path (Boolean)
- completed_at
- is_internal_only (Boolean) (default: false)
- is_active (Boolean) (default: true)
- metadata (created_at, created_by, updated_at, updated_by)

#### Milestone Groups (milestone_groups)
- id (PK)
- project_id (FK)
- title
- parent_id (FK) - self-referential for hierarchy (maximum 2 levels)
- is_active (Boolean)
- metadata (created_at, created_by, updated_at, updated_by)

#### Queries/Processes
- CRUD for milestones and groups
- Auto-compute target_week from target_date (ISO 8601 format: "2025-W04")
- Track baseline vs. actual variance
- Query filters: status, is_critical_path, is_internal_only
- Special query: All incomplete milestones from past + 4 weeks ahead (ISO 8601)

#### Business Rules
- **Grouping Hierarchy:** Maximum 2 levels (Parent Group → Child Group → Items)
- System enforces that a group with a parent_id cannot itself be a parent

---

### Deliverables (deliverables)

Tangible outputs required for project progression.

#### Data Model
- id (PK)
- project_id (FK)
- deliverables_group_id (FK) - nullable
- title
- description (markdown)
- deliverable_type (document, software, hardware, service, other)
- target_date
- target_week (ISO 8601) - **Auto-computed from target_date**
- actual_delivery_date
- status (pending, on_track, at_risk, delayed, blocked, completed, cancelled)
- approval_status (not_required, pending_review, approved, rejected)
- approved_by (FK to users) - nullable
- approved_at
- completed_at
- is_internal_only (Boolean) (default: false)
- is_active (Boolean) (default: true)
- metadata (created_at, created_by, updated_at, updated_by)

#### Deliverables Groups (deliverables_groups)
- id (PK)
- project_id (FK)
- title
- parent_id (FK) - maximum 2 levels
- is_active (Boolean)
- metadata (created_at, created_by, updated_at, updated_by)

#### Queries/Processes
- CRUD for deliverables and groups
- Assign/reassign/de-assign to groups
- Auto-compute target_week
- Query filters: status, approval_status, is_internal_only
- Special query: Incomplete items from past + 2 weeks ahead (ISO 8601)

---

### Action Items (action_items)

High-level directives requiring execution.

#### Data Model
- id (PK)
- project_id (FK)
- ai_group_id (FK) - nullable
- title
- description (markdown)
- assigned_to (FK to users) - nullable
- target_date
- target_week (ISO 8601) - **Auto-computed from target_date**
- status (pending, on_track, at_risk, delayed, blocked, completed, cancelled)
- priority (low, medium, high, critical) - **Manually set by PM**
- completed_at
- is_internal_only (Boolean) (default: false)
- is_active (Boolean) (default: true)
- metadata (created_at, created_by, updated_at, updated_by)

#### Action Items Groups (ai_groups)
- id (PK)
- project_id (FK)
- title
- parent_id (FK) - maximum 2 levels
- is_active (Boolean)
- metadata (created_at, created_by, updated_at, updated_by)

#### Queries/Processes
- CRUD for action items and groups
- Assign/reassign/de-assign to groups
- Auto-compute target_week
- Query filters: status, priority, assigned_to, is_internal_only
- Special query: Non-completed/cancelled items from past + 2 weeks ahead (ISO 8601)

#### Business Rules
- **No Auto-Priority Calculation:** PM manually sets priority based on context
- Impact and urgency can be tracked in notes but do not auto-compute priority

---

### Dependencies (dependencies)

Formal register for project blockers and external constraints.

#### Data Model
- id (PK)
- project_id (FK)
- dependency_group_id (FK) - nullable
- title
- description (markdown)
- dependency_type (internal, external, vendor, regulatory, technical)
- dependent_on (text) - Party or system being depended upon
- target_resolution_date
- target_week (ISO 8601) - **Auto-computed from target_resolution_date**
- status (pending, in_progress, blocked, resolved, cancelled)
- impact (low, medium, high, critical)
- resolved_at
- is_internal_only (Boolean) (default: false)
- is_active (Boolean) (default: true)
- metadata (created_at, created_by, updated_at, updated_by)

#### Dependencies Groups (dependency_groups)
- id (PK)
- project_id (FK)
- title
- parent_id (FK) - maximum 2 levels
- is_active (Boolean)
- metadata (created_at, created_by, updated_at, updated_by)

#### Queries/Processes
- CRUD for dependencies and groups
- Track vendor chase-up activity via notes
- Query filters: status, dependency_type, impact, is_internal_only
- Special query: Unresolved dependencies from past + 2 weeks ahead

---

### Meeting Events (meetings)

Governance and coordination meetings.

#### Data Model
- id (PK)
- project_id (FK)
- meet_group_id (FK) - nullable
- title
- description (markdown)
- meeting_type (steering_committee, working_group, status_review, other)
- calendar_link (URL)
- meeting_link (URL)
- scheduled_date
- scheduled_week (ISO 8601) - **Auto-computed from scheduled_date**
- duration_minutes
- status (scheduled, completed, cancelled, rescheduled)
- actual_date
- attendance_count
- completed_at
- is_internal_only (Boolean) (default: false)
- is_active (Boolean) (default: true)
- metadata (created_at, created_by, updated_at, updated_by)

#### Meeting Groups (meet_groups)
- id (PK)
- project_id (FK)
- title
- parent_id (FK) - maximum 2 levels
- is_active (Boolean)
- metadata (created_at, created_by, updated_at, updated_by)

#### Queries/Processes
- CRUD for meetings and groups
- Auto-compute scheduled_week
- Query filters: status, meeting_type, is_internal_only
- Special query: Incomplete meetings from past + 2 weeks ahead

---

## C. CONTROL REGISTERS (The "Guardrails")

### Risk Register (risks)

Tracks future uncertain events with probability/impact assessment.

#### Data Model
- id (PK)
- project_id (FK)
- risk_group_id (FK) - nullable
- title
- description (markdown)
- risk_category (strategic, financial, operational, technical, legal, reputational)
- probability (very_low, low, medium, high, very_high) - 1-5 scale
- impact (negligible, minor, moderate, major, catastrophic) - 1-5 scale
- proximity (when risk might materialize: immediate, near_term, medium_term, long_term)
- mitigation_strategy (markdown)
- contingency_plan (markdown)
- owner_id (FK to users)
- status (identified, assessed, mitigating, monitoring, closed, realized)
- closed_at
- is_internal_only (Boolean) (default: false)
- is_active (Boolean) (default: true)
- metadata (created_at, created_by, updated_at, updated_by)

#### Risk Groups (risk_groups)
- id (PK)
- project_id (FK)
- title
- parent_id (FK) - maximum 2 levels
- is_active (Boolean)
- metadata (created_at, created_by, updated_at, updated_by)

#### Queries/Processes
- CRUD for risks and groups
- **No Auto-Risk Score:** PM assesses severity based on probability and impact fields but makes final determination manually
- Track risk evolution via notes (e.g., probability reduction after mitigation)
- Query filters: status, risk_category, probability, impact, owner_id, is_internal_only
- Generate risk heat map (probability vs. impact matrix) for visualization

#### Business Rules
- Probability and Impact are reference fields for PM assessment
- PM documents severity rationale in notes
- **No automated risk scoring**

---

### Issue Log (issues)

Active problems hindering project progress.

#### Data Model
- id (PK)
- project_id (FK)
- i_group_id (FK) - nullable
- title
- description (markdown)
- issue_category (scope, schedule, resource, quality, communication, technical, other)
- impact (low, medium, high, critical)
- urgency (low, medium, high, critical)
- priority (low, medium, high, critical) - **Manually set by PM**
- status (identified, under_review, ongoing_resolution, escalated, monitoring, closed)
- owner_id (FK to users)
- escalated_to (FK to users) - nullable
- escalated_at
- closed_at
- resolution_summary (markdown)
- is_internal_only (Boolean) (default: false)
- is_active (Boolean) (default: true)
- metadata (created_at, created_by, updated_at, updated_by)

#### Issues Groups (i_groups)
- id (PK)
- project_id (FK)
- title
- parent_id (FK) - maximum 2 levels
- is_active (Boolean)
- metadata (created_at, created_by, updated_at, updated_by)

#### Queries/Processes
- CRUD for issues and groups
- Escalation workflow
- Resolution diary via notes
- Query filters: status, impact, urgency, priority, owner_id, is_internal_only
- Dashboard: Open issues by category and impact

#### Business Rules
- **No Auto-Priority Calculation:** Impact and urgency are reference fields; PM manually sets priority based on full context
- Priority is independent of impact + urgency calculation

---

### Change Requests (change_requests)

Formal workflow for baseline changes (scope and schedule only).

#### Data Model
- id (PK)
- project_id (FK)
- cr_number (unique identifier, e.g., "CR-001")
- title
- description (markdown)
- change_type (scope, schedule, other)
- justification (markdown)
- impact_assessment (markdown) - Analysis of scope/schedule impact
- requested_by (FK to users)
- requested_at
- status (draft, submitted, under_review, approved, rejected, implemented, cancelled)
- reviewed_by (FK to users)
- reviewed_at
- approval_notes (markdown)
- implemented_at
- schedule_impact_days (integer) - nullable
- is_internal_only (Boolean) (default: false)
- is_active (Boolean) (default: true)
- metadata (created_at, created_by, updated_at, updated_by)

#### Queries/Processes
- CRUD with workflow state transitions
- Approval workflow (draft → submitted → approved/rejected → implemented)
- Update project baseline upon approval
- Query filters: status, change_type, requested_by, reviewed_by
- Audit trail for approval decisions

#### Business Rules
- **No Budget Change Requests:** V1 focuses on Scope and Schedule changes only
- Mandatory fields: justification, impact_assessment

---

### Decision Log (decisions)

Permanent, immutable record of strategic decisions.

#### Data Model
- id (PK)
- project_id (FK)
- decision_number (unique identifier, e.g., "DEC-001")
- title
- context (markdown) - Background and circumstances
- decision (markdown) - The actual decision made
- rationale (markdown) - Why this decision was made
- alternatives_considered (markdown)
- implications (markdown)
- decision_maker_id (FK to users)
- decision_date
- review_date (nullable) - For decisions requiring future review
- status (active, superseded, reversed) - Tracks decision lifecycle
- superseded_by (FK to decisions) - nullable, links to newer decision
- is_internal_only (Boolean) (default: false)
- is_locked (Boolean) (default: false) - Prevents editing once locked
- metadata (created_at, created_by, updated_at, updated_by)

#### Queries/Processes
- Create decisions (automatically locked after creation window)
- Read decisions
- Supersede decisions (creates new decision, links to old)
- Query filters: status, decision_maker, decision_date, is_internal_only
- Immutable audit trail on all fields

---

## D. KNOWLEDGE MODULE (The "Library")

### Project Journal (journal_entries)

Timeline of broad, project-level updates. The "News Feed."

#### Data Model
- id (PK)
- project_id (FK)
- title
- content (markdown)
- entry_date
- entry_type (milestone, announcement, incident, change, other)
- is_internal_only (Boolean) (default: false)
- is_active (Boolean) (default: true)
- metadata (created_at, created_by, updated_at, updated_by)

#### Queries/Processes
- CRUD operations
- Display chronologically on project dashboard
- Query filters: entry_type, entry_date, is_internal_only
- Special query: Recent entries (last 2 weeks or 4 most recent)

#### Cross-Module Linking
- Journal entries can link to domain items (Milestones, Deliverables, Action Items, Dependencies, Meetings, Risks, Issues, Change Requests, Decisions) using simplified linking mechanism
- **Implementation:** Via `entity_links` table (limited use):
  - source_type (always "JournalEntry")
  - source_id (the journal entry ID)
  - target_type (polymorphic)
  - target_id (polymorphic)
  - link_type (references, relates_to, announces)

---

### Meeting Minutes Archive (meeting_minutes)

Repository for formal meeting records.

#### Data Model
- id (PK)
- project_id (FK)
- meeting_id (FK to meetings) - nullable (can exist independently or link to Meeting Event)
- title
- meeting_date
- attendees (JSON array or separate attendees table)
- agenda (markdown)
- minutes (markdown)
- action_items_summary (markdown)
- decisions_summary (markdown)
- next_meeting_date
- is_internal_only (Boolean) (default: false)
- is_active (Boolean) (default: true)
- metadata (created_at, created_by, updated_at, updated_by)

#### Queries/Processes
- CRUD operations
- Link to meeting events via meeting_id FK
- Query filters: meeting_date, is_internal_only
- Full-text search on minutes

---

### Stakeholder Register (stakeholders)

Directory of individuals impacting the project.

#### Data Model
- id (PK)
- project_id (FK)
- name
- title
- organization
- role_in_project (sponsor, steering_committee, approver, consulted, informed, other)
- contact_email
- contact_phone
- influence (low, medium, high)
- interest (low, medium, high)
- engagement_strategy (markdown)
- is_internal (Boolean)
- is_active (Boolean) (default: true)
- metadata (created_at, created_by, updated_at, updated_by)

#### Queries/Processes
- CRUD operations
- Stakeholder matrix (influence vs. interest)
- Query filters: role_in_project, influence, interest, is_internal

---

### Links (links)

Centralized repository of project-related URLs and references.

#### Data Model
- id (PK)
- project_id (FK)
- link_group_id (FK) - nullable
- title
- url
- description (markdown)
- position (integer for ordering)
- is_internal_only (Boolean) (default: false)
- is_active (Boolean) (default: true)
- metadata (created_at, created_by, updated_at, updated_by)

#### Link Groups (link_groups)
- id (PK)
- project_id (FK)
- title
- parent_id (FK) - maximum 2 levels
- is_active (Boolean)
- metadata (created_at, created_by, updated_at, updated_by)

#### Queries/Processes
- CRUD for links and groups
- Query filters: link_group_id, is_internal_only
- Position-based ordering within groups

---

### Lists / Checklists (lists)

Flexible list and checklist management.

#### Data Model - Lists
- id (PK)
- project_id (FK)
- list_group_id (FK) - nullable
- title
- description (markdown)
- position (integer)
- is_internal_only (Boolean) (default: false)
- is_active (Boolean) (default: true)
- metadata (created_at, created_by, updated_at, updated_by)

#### Data Model - List Items
- id (PK)
- list_id (FK)
- content (text)
- position (integer)
- is_checked (Boolean)
- checked_at
- checked_by (FK to users)
- metadata (created_at, created_by, updated_at, updated_by)

#### List Groups (list_groups)
- id (PK)
- project_id (FK)
- title
- parent_id (FK) - maximum 2 levels
- is_active (Boolean)
- metadata (created_at, created_by, updated_at, updated_by)

#### Queries/Processes
- CRUD for lists, list items, and groups
- Track completion status via is_checked
- Query filters: list_group_id, is_internal_only
- Position-based ordering

---

### Lessons Learned (lessons_learned)

Structured retrospective tool.

#### Data Model
- id (PK)
- project_id (FK)
- phase (initiation, planning, execution, monitoring, closing)
- category (process, technology, people, communication, risk_management, other)
- what_went_well (markdown)
- what_went_wrong (markdown)
- root_cause (markdown)
- recommendation (markdown)
- applicable_to_future_projects (Boolean)
- severity (low, medium, high) - Importance of the lesson
- captured_by (FK to users)
- captured_at
- is_internal_only (Boolean) (default: false)
- is_active (Boolean) (default: true)
- metadata (created_at, created_by, updated_at, updated_by)

#### Queries/Processes
- CRUD operations
- Export lessons for organizational knowledge base
- Query filters: phase, category, severity, is_internal_only

---

## E. SYSTEM-LEVEL ENTITIES

### Users (users)

System users with role-based access control.

#### Data Model
- id (PK)
- name
- email (unique)
- password (hashed)
- role (admin, editor, viewer)
- is_active (Boolean)
- last_login_at
- metadata (created_at, updated_at)

#### Queries/Processes
- Authentication and authorization
- Role-based access control (RBAC):
  - **Admin:** Full CRUD across ALL projects + system configuration + user management + Portfolio Dashboard access
  - **Editor:** Full CRUD on ASSIGNED projects only (the "Captain of the Ship" for their project)
  - **Viewer:** Read-only access across ALL projects (Dashboards and Reports)

---

### Cross-Module Linking (entity_links)

Limited polymorphic relationships for Journal Entries only.

#### Data Model
- id (PK)
- source_type (always "JournalEntry")
- source_id (Journal Entry ID)
- target_type (polymorphic - any module)
- target_id (polymorphic)
- link_type (references, relates_to, announces)
- description (text)
- metadata (created_at, created_by)

#### Use Cases (V1 Scope)
- Link Journal Entry to Milestone
- Link Journal Entry to Risk
- Link Journal Entry to Decision
- **Note:** Meeting Minutes → Meeting Event linking uses direct FK (meeting_id), not this table

---

## F. REPORTING & ANALYTICS

### Portfolio Dashboard Queries
- Count projects by manually-set RAG status (Red/Amber/Green)
- **"Stale Report" Indicator:** Flag projects without Status Report published in >30 days
- Top critical risks across portfolio (based on manual PM assessment)
- Overdue action items by project

### Project Dashboard Queries
- Manual RAG status display
- Recent journal entries (last 2 weeks or last 3)
- Recent activity feed (latest notes/comments across all modules)
- Milestone timeline (next 3 upcoming)
- Open risks/issues count

---

## Technical Implementation Notes

### Soft-Delete Architecture
- All entities use `is_active` flag
- Data is archived, never destroyed
- Implement Laravel's `SoftDeletes` trait globally

### Immutable Records
- `status_reports`: Locked once published
- `decisions`: Locked after creation window
- `notes`: Immutable once created (no delete/edit)

### Audit Trail
- Use `spatie/laravel-activitylog` or custom audit table
- Log all field changes: old_value, new_value, user, timestamp
- Surface audit entries alongside user notes in timeline views

### Auto-Computed Fields
- `target_week`: Derived from `target_date` using Carbon/ISO 8601
- `scheduled_week`: Derived from `scheduled_date` using Carbon/ISO 8601
- **No auto-computed status, priority, or risk scores**

### ISO 8601 Week Calculation
```php
// Example: When setting target_date
$targetWeek = Carbon::parse($targetDate)->format('o-\WW');
// Result: "2025-W04"
```

### Polymorphic Relationships
- `notes`: Polymorphic `notable_type`, `notable_id` (attaches to ANY entity)
- `entity_links`: Limited to Journal Entries linking to domain items

### Query Optimization
- Index all foreign keys
- Composite indexes on `(project_id, status, is_active)`
- Index on `target_week` for temporal queries
- Full-text search indexes on markdown content fields
- Index on `is_internal_only` for visibility filtering

### Grouping Hierarchy Validation
- System enforces maximum 2-level hierarchy
- Business rule: If a group has a `parent_id`, it cannot itself be a parent
- Validation on group creation/update

---

## Migration Strategy

1. **Phase 1:** Core entities (projects, users, notes)
2. **Phase 2:** Execution module (milestones, deliverables, action items, dependencies, meetings) with groups
3. **Phase 3:** Control registers (risks, issues, change requests, decisions) with groups
4. **Phase 4:** Knowledge module (journal, meeting minutes, stakeholders, links, lists, lessons learned) with groups
5. **Phase 5:** Governance (status reports)
6. **Phase 6:** Cross-cutting (audit trail, entity linking for journal entries)

---

## Validation Rules Summary

- **Required Fields:**
  - All entities: project_id (except projects and users tables)
  - Target date entities: target_date (auto-generates target_week)
  - Change requests: justification, impact_assessment
  - Decisions: decision, rationale, decision_maker_id

- **Immutability:**
  - Notes: No edit/delete after creation
  - Status reports: No changes after `published_at` is set
  - Decisions: No changes after `is_locked` = true
  - Audit log entries: Always immutable

- **Business Logic:**
  - Change requests update project baselines upon approval (scope/schedule only)
  - Target week auto-calculated on target date change
  - **Manual RAG status:** PM selects from dropdown
  - **Manual priority:** PM sets based on context (no auto-calculation)
  - **Manual risk assessment:** No auto-scoring
  - Grouping hierarchy limited to 2 levels maximum

- **Visibility Control:**
  - `is_internal_only` flag controls item visibility based on user context
  - Filtering applied in all queries

---

## V1 Design Principles

1. **Human Judgment First:** The system captures PM decisions; it does not make decisions
2. **Manual Control:** RAG status, priorities, and risk severity are declared by PMs, not computed
3. **Simple Automation:** Only mathematical operations (date-to-week conversion) are automated
4. **Immutable History:** Notes, decisions, and published reports cannot be changed
5. **Flexible Organization:** 2-level grouping provides structure without overwhelming complexity
6. **Selective Visibility:** `is_internal_only` flag enables appropriate information sharing
7. **Universal Commentary:** Notes system allows context and rationale to be attached to any item

---
