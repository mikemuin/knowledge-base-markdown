# Data Models

## What?

This is an **Enterprise PMO & Governance Suite** — a centralized platform for strategic project oversight. The application tracks projects across their full lifecycle, managing governance, execution, control registers, and knowledge assets. It enforces standardized measurement, financial rigor, and reporting structures across the portfolio.

---

## Cross-Cutting Concerns

### Universal Features (Applied to ALL modules)

Every record in the system includes:

#### A. Contextual Notes & Status Logs
- **Implementation:** Polymorphic `notes` table
- **Purpose:** Chronological, immutable audit trail of remarks and decisions
- **Fields:**
  - id (PK)
  - notable_type (polymorphic relationship)
  - notable_id (polymorphic relationship)
  - content (text)
  - is_system_generated (Boolean) - distinguishes user notes from auto-generated audit entries
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
- rag_status (red, amber, green) - Overall health indicator
- start_date
- target_end_date
- actual_end_date
- project_manager_id (FK to users)
- executive_sponsor_id (FK to users)
- is_active (Boolean)
- metadata (created_at, created_by, updated_at, updated_by)

#### Queries/Processes
- CRUD operations
- Calculate RAG status based on downstream metrics
- Portfolio rollup queries (count by RAG, financial aggregates)
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
- overall_status (red, amber, green)
- executive_summary (markdown)
- achievements (markdown)
- challenges (markdown)
- next_period_focus (markdown)
- snapshot_data (JSON) - Frozen metrics at time of publication
- published_at (immutable once set)
- published_by (FK to users)
- is_draft (Boolean)
- metadata (created_at, created_by, updated_at, updated_by)

#### Queries/Processes
- Create draft reports
- Publish reports (locks all fields, triggers snapshot)
- Read published reports (chronological archive)
- Query filters: project_id, report_period, is_draft

#### Business Rules
- Once `published_at` is set, no fields can be modified
- Snapshot captures: budget variance, milestone status, risk count, issue count, etc.

---

### Financials (financials)

Tracks budgetary control and spend forecasting.

#### Data Model
- id (PK)
- project_id (FK)
- original_budget (decimal)
- approved_budget (decimal) - After approved change requests
- actual_spend (decimal)
- forecasted_spend (decimal)
- budget_period (YYYY-MM format for monthly tracking)
- variance (computed: approved_budget - actual_spend)
- variance_percentage (computed)
- notes (text) - Explanations for variances
- metadata (created_at, created_by, updated_at, updated_by)

#### Queries/Processes
- CRUD operations
- Calculate variance and percentage automatically
- Portfolio financial rollup (sum across all projects)
- Query filters: project_id, budget_period, variance thresholds

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
- target_week (ISO 8601)
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
- parent_id (FK) - self-referential for hierarchy
- is_active (Boolean)
- metadata (created_at, created_by, updated_at, updated_by)

#### Queries/Processes
- CRUD for milestones and groups
- Auto-compute target_week from target_date
- Track baseline vs. actual variance
- Query filters: status, is_critical_path, is_internal_only
- Special query: All incomplete milestones from past + 4 weeks ahead (ISO 8601)

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
- target_week (ISO 8601)
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
- parent_id (FK)
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
- target_week (ISO 8601)
- status (pending, on_track, at_risk, delayed, blocked, completed, cancelled)
- priority (low, medium, high, critical)
- completed_at
- is_internal_only (Boolean) (default: false)
- is_active (Boolean) (default: true)
- metadata (created_at, created_by, updated_at, updated_by)

#### Action Items Groups (ai_groups)
- id (PK)
- project_id (FK)
- title
- parent_id (FK)
- is_active (Boolean)
- metadata (created_at, created_by, updated_at, updated_by)

#### Queries/Processes
- CRUD for action items and groups
- Assign/reassign/de-assign to groups
- Auto-compute target_week
- Query filters: status, priority, assigned_to, is_internal_only
- Special query: Non-completed/cancelled items from past + 2 weeks ahead (ISO 8601)

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
- target_week (ISO 8601)
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
- parent_id (FK)
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
- scheduled_week (ISO 8601)
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
- parent_id (FK)
- is_active (Boolean)
- metadata (created_at, created_by, updated_at, updated_by)

#### Queries/Processes
- CRUD for meetings and groups
- Auto-compute scheduled_week
- Flag project if critical meeting is missed
- Query filters: status, meeting_type, is_internal_only
- Special query: Incomplete meetings from past + 2 weeks ahead

---

## C. CONTROL REGISTERS (The "Guardrails")

### Risk Register (risks)

Tracks future uncertain events with probability/impact scoring.

#### Data Model
- id (PK)
- project_id (FK)
- risk_group_id (FK) - nullable
- title
- description (markdown)
- risk_category (strategic, financial, operational, technical, legal, reputational)
- probability (very_low, low, medium, high, very_high) - 1-5 scale
- impact (negligible, minor, moderate, major, catastrophic) - 1-5 scale
- risk_score (computed: probability × impact)
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
- parent_id (FK)
- is_active (Boolean)
- metadata (created_at, created_by, updated_at, updated_by)

#### Queries/Processes
- CRUD for risks and groups
- Auto-calculate risk_score
- Track risk evolution via notes (e.g., probability reduction)
- Query filters: status, risk_category, probability, impact, owner_id, is_internal_only
- Generate risk heat map (probability vs. impact matrix)

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
- priority (computed: impact + urgency or custom)
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
- parent_id (FK)
- is_active (Boolean)
- metadata (created_at, created_by, updated_at, updated_by)

#### Queries/Processes
- CRUD for issues and groups
- Escalation workflow
- Resolution diary via notes
- Query filters: status, impact, urgency, owner_id, is_internal_only
- Dashboard: Open issues by category and impact

---

### Change Requests (change_requests)

Formal workflow for baseline changes (scope, schedule, budget).

#### Data Model
- id (PK)
- project_id (FK)
- cr_number (unique identifier, e.g., "CR-001")
- title
- description (markdown)
- change_type (scope, schedule, budget, quality, other)
- justification (markdown)
- impact_assessment (markdown) - Analysis of scope/schedule/budget impact
- requested_by (FK to users)
- requested_at
- status (draft, submitted, under_review, approved, rejected, implemented, cancelled)
- reviewed_by (FK to users)
- reviewed_at
- approval_notes (markdown)
- implemented_at
- budget_impact (decimal) - nullable
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

## D. KNOWLEDGE & ASSETS MODULE (The "Library")

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

---

### Meeting Minutes Archive (meeting_minutes)

Repository for formal meeting records.

#### Data Model
- id (PK)
- project_id (FK)
- meeting_id (FK to meetings) - nullable (can exist independently)
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
- Link to meeting events
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

### Project Assets

Container for unstructured, project-level data.

#### General Wiki (wiki_pages)
- id (PK)
- project_id (FK)
- title
- content (markdown)
- parent_id (FK) - for hierarchical wiki structure
- slug (for URL routing)
- is_internal_only (Boolean) (default: false)
- is_active (Boolean) (default: true)
- metadata (created_at, created_by, updated_at, updated_by)

#### Links (links)
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
- parent_id (FK)
- is_active (Boolean)
- metadata (created_at, created_by, updated_at, updated_by)

#### Lists / Checklists (lists)
- id (PK)
- project_id (FK)
- list_group_id (FK) - nullable
- title
- description (markdown)
- position (integer)
- is_internal_only (Boolean) (default: false)
- is_active (Boolean) (default: true)
- metadata (created_at, created_by, updated_at, updated_by)

#### List Items (list_items)
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
- is_active (Boolean)
- metadata (created_at, created_by, updated_at, updated_by)

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
- role (pmo_lead, project_manager, executive_stakeholder, team_member)
- is_active (Boolean)
- last_login_at
- metadata (created_at, updated_at)

#### Queries/Processes
- Authentication and authorization
- Role-based access control (RBAC):
  - **PMO Lead:** Full CRUD across all projects
  - **Project Manager:** Full CRUD on assigned projects
  - **Executive Stakeholder:** Read-only on dashboards and reports
  - **Team Member:** Limited access based on assignment

---

### Cross-Module Linking (entity_links)

Polymorphic relationships between any two entities.

#### Data Model
- id (PK)
- source_type (polymorphic)
- source_id (polymorphic)
- target_type (polymorphic)
- target_id (polymorphic)
- link_type (relates_to, blocks, depends_on, mitigates, other)
- description (text)
- metadata (created_at, created_by)

#### Example Use Cases
- Link Issue #5 to Change Request #3
- Link Meeting Minute to Decision #7
- Link Risk #12 to Mitigation Action Item #45

---

## F. REPORTING & ANALYTICS

### Portfolio Dashboard Queries
- Count projects by RAG status (Red/Amber/Green)
- Portfolio-wide financial aggregates (budget, spend, variance)
- Top 10 critical risks across portfolio
- Overdue action items by project

### Project Dashboard Queries
- RAG trend indicators (↑ → ↓)
- Recent journal entries (last 2 weeks)
- Recent activity feed (latest notes across all modules)
- Milestone Gantt chart
- Budget burn rate
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
- `risk_score`: Computed from `probability × impact`
- `variance`: Computed from `approved_budget - actual_spend`
- `priority`: May be computed from `impact + urgency` (issues)

### ISO 8601 Week Calculation
```php
// Example: When setting target_date
$targetWeek = Carbon::parse($targetDate)->format('o-\WW');
// Result: "2025-W04"
```

### Polymorphic Relationships
- `notes`: Polymorphic `notable_type`, `notable_id`
- `entity_links`: Polymorphic source/target for cross-module linking

### Query Optimization
- Index all foreign keys
- Composite indexes on `(project_id, status, is_active)`
- Index on `target_week` for temporal queries
- Full-text search indexes on markdown content fields

---

## Migration Strategy

1. **Phase 1:** Core entities (projects, users)
2. **Phase 2:** Execution module (milestones, deliverables, action items)
3. **Phase 3:** Control registers (risks, issues, change requests, decisions)
4. **Phase 4:** Knowledge assets (journal, wiki, lessons learned)
5. **Phase 5:** Governance (status reports, financials)
6. **Phase 6:** Cross-cutting (notes, audit trail, entity linking)

---

## Validation Rules Summary

- **Required Fields:**
  - All entities: project_id (except projects table)
  - Target date entities: target_date (auto-generates target_week)
  - Change requests: justification, impact_assessment
  - Decisions: decision, rationale, decision_maker_id

- **Immutability:**
  - Notes: No edit/delete after creation
  - Status reports: No changes after `published_at` is set
  - Decisions: No changes after `is_locked` = true
  - Audit log entries: Always immutable

- **Business Logic:**
  - Change requests update project baselines upon approval
  - Risk score auto-calculated on probability/impact change
  - Target week auto-calculated on target date change
  - RAG status derived from downstream metrics

---