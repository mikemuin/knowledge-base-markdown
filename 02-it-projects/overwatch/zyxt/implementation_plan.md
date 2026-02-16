# Implementation Blueprint: Overwatch Project Management System

## 1. Architectural Summary

This is a **Laravel 11+ modular monolith** using the **TALL stack** (Tailwind, Alpine, Livewire, Laravel) with **Flux UI components**, structured around four core domains (Governance, Execution, Control, Knowledge) with subdomain-driven module separation. The system supports triple interfaces (API/Web/Livewire), polymorphic shared systems (Notes, AuditTrail, EntityLinks), and enforces RBAC with immutability patterns for audit compliance.

------

## 2. The Implementation Plan

### Phase 1: Foundation & Infrastructure

#### Step 1: Database Configuration & Core Migrations

- **Goal:** Establish database foundation with core system tables (users, projects, roles)
- **Files Involved:**
  - `database/migrations/2026_01_27_000001_create_users_table.php`
  - `database/migrations/2026_01_27_000002_create_projects_table.php`
  - `database/migrations/2026_01_27_000003_create_project_user_pivot_table.php`
- **Dependencies:** None
- **Validation:** Run `php artisan migrate` successfully; verify tables exist in the database

#### Step 2: User & Project Base Models

- **Goal:** Create foundational Eloquent models with relationships and RBAC enum
- **Files Involved:**
  - `app/Models/User.php`
  - `app/Models/Project.php`
  - `app/Enums/UserRole.php`
  - `app/Enums/ProjectStatus.php`
- **Dependencies:** Step 1
- **Validation:** Tinker commands can create users and projects; relationships load correctly

#### Step 3: Shared System - Notes (Polymorphic Commentary)

- **Goal:** Implement a universal immutable notes system attachable to any entity
- **Files Involved:**
  - `database/migrations/2026_01_27_000004_create_shared_notes_table.php`
  - `app/Modules/Shared/Notes/Models/Note.php`
  - `app/Modules/Shared/Notes/Services/NoteService.php`
  - `app/Modules/Shared/Notes/Policies/NotePolicy.php`
  - `app/Modules/Shared/Notes/Resources/NoteResource.php`
- **Dependencies:** Step 2
- **Validation:** Can attach notes to the Project model; notes are immutable (no update/delete); policy enforces creator-only visibility

#### Step 4: Shared System - Audit Trail

- **Goal:** Implement field-level change logging for all domain entities
- **Files Involved:**
  - `database/migrations/2026_01_27_000005_create_audit_logs_table.php`
  - `app/Modules/Shared/AuditTrail/Models/AuditLog.php`
  - `app/Modules/Shared/AuditTrail/Services/AuditService.php`
  - `app/Modules/Shared/AuditTrail/Observers/AuditableObserver.php`
  - `app/Modules/Shared/Traits/Auditable.php`
- **Dependencies:** Step 2
- **Validation:** Update a project; verify audit_logs table captures old_value → new_value with user_id and timestamp

#### Step 5: Shared System - Entity Links (Polymorphic)

- **Goal:** Enable ProjectJournal entries to link to other domain entities
- **Files Involved:**
  - `database/migrations/2026_01_27_000006_create_shared_entity_links_table.php`
  - `app/Modules/Shared/EntityLinks/Models/EntityLink.php`
  - `app/Modules/Shared/EntityLinks/Services/EntityLinkService.php`
- **Dependencies:** Step 2
- **Validation:** Can create link from JournalEntry (source) to any other entity (target); query retrieves linked entities

#### Step 6: Shared Utilities & Enums

- **Goal:** Create reusable enums, traits, and utility services
- **Files Involved:**
  - `app/Modules/Shared/Enums/RagStatus.php`
  - `app/Modules/Shared/Traits/HasGroups.php`
  - `app/Modules/Shared/Traits/HasInternalVisibility.php`
  - `app/Modules/Shared/Services/WeekCalculatorService.php`
- **Dependencies:** None
- **Validation:** Unit tests for WeekCalculatorService; traits can be attached to test models

------

### Phase 2: GOVERNANCE Domain - Status Reports

#### Step 7: Status Reports - Database Schema

- **Goal:** Create status_reports table with immutability enforcement via published_at
- **Files Involved:**
  - `database/migrations/2026_01_27_100001_create_governance_status_reports_table.php`
- **Dependencies:** Step 1, Step 2
- **Validation:** Migration runs; table includes project_id, period_start, period_end, published_at, is_locked fields

#### Step 8: Status Reports - Model & Repository

- **Goal:** Implement StatusReport model with soft deletes, audit trail, and query scopes
- **Files Involved:**
  - `app/Modules/Governance/StatusReports/Models/StatusReport.php`
  - `app/Modules/Governance/StatusReports/Repositories/StatusReportRepository.php`
- **Dependencies:** Step 7, Step 4
- **Validation:** Can create draft report; cannot update after published_at is set; soft delete works

#### Step 9: Status Reports - DTOs

- **Goal:** Define data transfer objects for create/update operations
- **Files Involved:**
  - `app/Modules/Governance/StatusReports/DTOs/CreateStatusReportData.php`
  - `app/Modules/Governance/StatusReports/DTOs/UpdateStatusReportData.php`
  - `app/Modules/Governance/StatusReports/DTOs/StatusReportData.php`
- **Dependencies:** Step 8
- **Validation:** DTOs can be instantiated from arrays; transformation methods work correctly

#### Step 10: Status Reports - Service Layer

- **Goal:** Orchestrate business logic including publish workflow, locking, and event emission
- **Files Involved:**
  - `app/Modules/Governance/StatusReports/Services/StatusReportService.php`
  - `app/Modules/Governance/StatusReports/Events/StatusReportCreated.php`
  - `app/Modules/Governance/StatusReports/Events/StatusReportPublished.php`
- **Dependencies:** Step 9, Step 4
- **Validation:** Service enforces immutability after publish; events fire correctly; audit logs capture changes

#### Step 11: Status Reports - Form Requests

- **Goal:** Create validation rules for API and Web interfaces
- **Files Involved:**
  - `app/Modules/Governance/StatusReports/FormRequests/Api/StoreStatusReportRequest.php`
  - `app/Modules/Governance/StatusReports/FormRequests/Api/UpdateStatusReportRequest.php`
  - `app/Modules/Governance/StatusReports/FormRequests/Web/StoreStatusReportRequest.php`
  - `app/Modules/Governance/StatusReports/FormRequests/Web/UpdateStatusReportRequest.php`
- **Dependencies:** Step 10
- **Validation:** Validation passes for valid data; fails for invalid data with appropriate messages

#### Step 12: Status Reports - Policy

- **Goal:** Implement RBAC authorization (Admin: full, Editor: assigned projects, Viewer: read-only)
- **Files Involved:**
  - `app/Modules/Governance/StatusReports/Policies/StatusReportPolicy.php`
- **Dependencies:** Step 2, Step 8
- **Validation:** Policy tests verify: admin can do all; editor limited to assigned projects; viewer read-only

#### Step 13: Status Reports - API Controllers & Resources

- **Goal:** Build RESTful JSON API endpoints with resource transformers
- **Files Involved:**
  - `app/Modules/Governance/StatusReports/Controllers/Api/StatusReportController.php`
  - `app/Modules/Governance/StatusReports/Resources/StatusReportResource.php`
  - `app/Modules/Governance/StatusReports/Resources/StatusReportCollection.php`
  - `routes/api.php` (add routes)
- **Dependencies:** Step 11, Step 12
- **Validation:** Postman/curl tests for CRUD operations; responses match resource format; 401/403 for unauthorized

#### Step 14: Status Reports - Web Controllers & Views

- **Goal:** Create server-rendered Blade interface with ViewModels
- **Files Involved:**
  - `app/Modules/Governance/StatusReports/Controllers/Web/StatusReportController.php`
  - `app/Modules/Governance/StatusReports/ViewModels/StatusReportViewModel.php`
  - `app/Modules/Governance/StatusReports/Views/index.blade.php`
  - `app/Modules/Governance/StatusReports/Views/create.blade.php`
  - `app/Modules/Governance/StatusReports/Views/edit.blade.php`
  - `app/Modules/Governance/StatusReports/Views/show.blade.php`
  - `routes/web.php` (add routes)
- **Dependencies:** Step 13
- **Validation:** Navigate to web routes; forms submit correctly; published reports show locked state

#### Step 15: Status Reports - Livewire Components

- **Goal:** Build reactive TALL stack interface with Flux UI components
- **Files Involved:**
  - `app/Modules/Governance/StatusReports/Livewire/StatusReportList.php`
  - `app/Modules/Governance/StatusReports/Livewire/StatusReportForm.php`
  - `app/Modules/Governance/StatusReports/Livewire/StatusReportDetail.php`
  - `resources/views/livewire/governance/status-reports/list.blade.php`
  - `resources/views/livewire/governance/status-reports/form.blade.php`
  - `resources/views/livewire/governance/status-reports/detail.blade.php`
- **Dependencies:** Step 14
- **Validation:** Livewire components render; reactive updates work without page refresh; Flux UI styling consistent

------

### Phase 3: EXECUTION Domain - Milestones

#### Step 16: Milestones - Database Schema with Grouping

- **Goal:** Create milestones and milestone_groups tables with 2-level hierarchy support
- **Files Involved:**
  - `database/migrations/2026_01_27_200001_create_execution_milestone_groups_table.php`
  - `database/migrations/2026_01_27_200002_create_execution_milestones_table.php`
- **Dependencies:** Step 1, Step 2
- **Validation:** Tables created; foreign keys enforce project_id; parent_id allows 2-level nesting

#### Step 17: Milestones - Models & Repository

- **Goal:** Implement Milestone and MilestoneGroup models with HasGroups trait
- **Files Involved:**
  - `app/Modules/Execution/Milestones/Models/MilestoneGroup.php`
  - `app/Modules/Execution/Milestones/Models/Milestone.php`
  - `app/Modules/Execution/Milestones/Repositories/MilestoneRepository.php`
- **Dependencies:** Step 16, Step 6
- **Validation:** Can create group hierarchy; milestones assigned to groups; trait methods work

#### Step 18: Milestones - DTOs & Service Layer

- **Goal:** Build service with target_week auto-calculation and grouping validation
- **Files Involved:**
  - `app/Modules/Execution/Milestones/DTOs/CreateMilestoneData.php`
  - `app/Modules/Execution/Milestones/DTOs/UpdateMilestoneData.php`
  - `app/Modules/Execution/Milestones/Services/MilestoneService.php`
  - `app/Modules/Execution/Milestones/Events/MilestoneCreated.php`
  - `app/Modules/Execution/Milestones/Events/MilestoneStatusChanged.php`
- **Dependencies:** Step 17, Step 6 (WeekCalculatorService)
- **Validation:** target_week computed from target_date; service prevents 3-level grouping; events fire

#### Step 19: Milestones - Form Requests & Policy

- **Goal:** Validation rules and RBAC enforcement
- **Files Involved:**
  - `app/Modules/Execution/Milestones/FormRequests/Api/StoreMilestoneRequest.php`
  - `app/Modules/Execution/Milestones/FormRequests/Api/UpdateMilestoneRequest.php`
  - `app/Modules/Execution/Milestones/Policies/MilestonePolicy.php`
- **Dependencies:** Step 18
- **Validation:** Validation enforces required fields; policy tests pass for all roles

#### Step 20: Milestones - API Interface

- **Goal:** RESTful JSON endpoints with nested routes for groups
- **Files Involved:**
  - `app/Modules/Execution/Milestones/Controllers/Api/MilestoneController.php`
  - `app/Modules/Execution/Milestones/Controllers/Api/MilestoneGroupController.php`
  - `app/Modules/Execution/Milestones/Resources/MilestoneResource.php`
  - `routes/api.php`
- **Dependencies:** Step 19
- **Validation:** Can CRUD milestones and groups via API; resources format correctly; grouping hierarchy respected

#### Step 21: Milestones - Web Interface

- **Goal:** Blade views with hierarchical group display
- **Files Involved:**
  - `app/Modules/Execution/Milestones/Controllers/Web/MilestoneController.php`
  - `app/Modules/Execution/Milestones/ViewModels/MilestoneViewModel.php`
  - `app/Modules/Execution/Milestones/Views/index.blade.php`
  - `app/Modules/Execution/Milestones/Views/create.blade.php`
  - `app/Modules/Execution/Milestones/Views/edit.blade.php`
  - `routes/web.php`
- **Dependencies:** Step 20
- **Validation:** Web UI displays nested groups correctly; target_week shows as "Week X of Month"

#### Step 22: Milestones - Livewire Interface

- **Goal:** Reactive components with drag-and-drop group reordering (if applicable)
- **Files Involved:**
  - `app/Modules/Execution/Milestones/Livewire/MilestoneList.php`
  - `app/Modules/Execution/Milestones/Livewire/MilestoneForm.php`
  - `resources/views/livewire/execution/milestones/list.blade.php`
  - `resources/views/livewire/execution/milestones/form.blade.php`
- **Dependencies:** Step 21
- **Validation:** Livewire updates milestone status reactively; group selection works; Flux UI components render

------

### Phase 4: EXECUTION Domain - Action Items

#### Step 23: Action Items - Database Schema with Grouping

- **Goal:** Create action_items and aitem_groups tables
- **Files Involved:**
  - `database/migrations/2026_01_27_200003_create_execution_aitem_groups_table.php`
  - `database/migrations/2026_01_27_200004_create_execution_action_items_table.php`
- **Dependencies:** Step 1, Step 2
- **Validation:** Migration successful; tables support grouping and target_week

#### Step 24: Action Items - Models, DTOs, Service, Policy

- **Goal:** Full CRUD implementation following milestone pattern
- **Files Involved:**
  - `app/Modules/Execution/ActionItems/Models/AitemGroup.php`
  - `app/Modules/Execution/ActionItems/Models/ActionItem.php`
  - `app/Modules/Execution/ActionItems/Repositories/ActionItemRepository.php`
  - `app/Modules/Execution/ActionItems/DTOs/CreateActionItemData.php`
  - `app/Modules/Execution/ActionItems/Services/ActionItemService.php`
  - `app/Modules/Execution/ActionItems/Policies/ActionItemPolicy.php`
- **Dependencies:** Step 23, Step 6
- **Validation:** Service enforces business rules; policy tests pass; audit trail captures changes

#### Step 25: Action Items - Triple Interface Implementation

- **Goal:** API, Web, and Livewire controllers/components
- **Files Involved:**
  - `app/Modules/Execution/ActionItems/Controllers/Api/ActionItemController.php`
  - `app/Modules/Execution/ActionItems/Controllers/Web/ActionItemController.php`
  - `app/Modules/Execution/ActionItems/Livewire/ActionItemList.php`
  - `app/Modules/Execution/ActionItems/Resources/ActionItemResource.php`
  - `app/Modules/Execution/ActionItems/Views/*.blade.php`
  - `resources/views/livewire/execution/action-items/*.blade.php`
- **Dependencies:** Step 24
- **Validation:** All three interfaces functional; CRUD operations work; grouping displays correctly

------

### Phase 5: EXECUTION Domain - Deliverables

#### Step 26: Deliverables - Full Module Implementation

- **Goal:** Complete deliverables module following established patterns
- **Files Involved:**
  - `database/migrations/2026_01_27_200005_create_execution_deliverable_groups_table.php`
  - `database/migrations/2026_01_27_200006_create_execution_deliverables_table.php`
  - `app/Modules/Execution/Deliverables/Models/*.php`
  - `app/Modules/Execution/Deliverables/Services/DeliverableService.php`
  - `app/Modules/Execution/Deliverables/Controllers/**/*.php`
  - `app/Modules/Execution/Deliverables/Livewire/*.php`
- **Dependencies:** Step 25 (pattern established)
- **Validation:** Full CRUD via API/Web/Livewire; grouping works; policies enforce RBAC

------

### Phase 6: EXECUTION Domain - Dependencies & Meeting Events

#### Step 27: Dependencies - Full Module Implementation

- **Goal:** Dependency tracking with external/internal flags and grouping
- **Files Involved:**
  - `database/migrations/2026_01_27_200007_create_execution_dependency_groups_table.php`
  - `database/migrations/2026_01_27_200008_create_execution_dependencies_table.php`
  - `app/Modules/Execution/Dependencies/Models/*.php`
  - `app/Modules/Execution/Dependencies/Services/DependencyService.php`
  - [Additional controllers, livewire, views as per pattern]
- **Dependencies:** Step 26
- **Validation:** Can track blockers; RAG status updates; triple interface functional

#### Step 28: Meeting Events - Full Module Implementation

- **Goal:** Governance meeting scheduling with recurring event support
- **Files Involved:**
  - `database/migrations/2026_01_27_200009_create_execution_meeting_groups_table.php`
  - `database/migrations/2026_01_27_200010_create_execution_meeting_events_table.php`
  - `app/Modules/Execution/MeetingEvents/Models/*.php`
  - `app/Modules/Execution/MeetingEvents/Services/MeetingEventService.php`
  - [Additional controllers, livewire, views as per pattern]
- **Dependencies:** Step 27
- **Validation:** Can schedule meetings; recurring patterns work; calendar integration possible

------

### Phase 7: CONTROL Domain - Risks

#### Step 29: Risks - Full Module Implementation

- **Goal:** Risk register with probability/impact matrix, mitigation tracking, and grouping
- **Files Involved:**
  - `database/migrations/2026_01_27_300001_create_control_risk_groups_table.php`
  - `database/migrations/2026_01_27_300002_create_control_risks_table.php`
  - `app/Modules/Control/Risks/Models/*.php`
  - `app/Modules/Control/Risks/Services/RiskService.php`
  - `app/Modules/Control/Risks/Enums/RiskProbability.php`
  - `app/Modules/Control/Risks/Enums/RiskImpact.php`
  - [Additional controllers, livewire, views as per pattern]
- **Dependencies:** Step 28, Step 6 (RagStatus)
- **Validation:** Risk scoring calculation works; mitigation workflow functional; heat map possible

------

### Phase 8: CONTROL Domain - Issues

#### Step 30: Issues - Full Module Implementation

- **Goal:** Issue tracking with resolution workflow and grouping
- **Files Involved:**
  - `database/migrations/2026_01_27_300003_create_control_issue_groups_table.php`
  - `database/migrations/2026_01_27_300004_create_control_issues_table.php`
  - `app/Modules/Control/Issues/Models/*.php`
  - `app/Modules/Control/Issues/Services/IssueService.php`
  - [Additional controllers, livewire, views as per pattern]
- **Dependencies:** Step 29
- **Validation:** Issue lifecycle (open → in_progress → resolved) works; priority filtering functional

------

### Phase 9: CONTROL Domain - Change Requests & Decisions

#### Step 31: Change Requests - Full Module Implementation

- **Goal:** Formal change control workflow with approval process
- **Files Involved:**
  - `database/migrations/2026_01_27_300005_create_control_change_requests_table.php`
  - `app/Modules/Control/ChangeRequests/Models/ChangeRequest.php`
  - `app/Modules/Control/ChangeRequests/Services/ChangeRequestService.php`
  - `app/Modules/Control/ChangeRequests/Enums/ChangeStatus.php`
  - [Additional controllers, livewire, views as per pattern]
- **Dependencies:** Step 30
- **Validation:** Approval workflow enforces state transitions; impact tracking works

#### Step 32: Decisions - Full Module Implementation with Immutability

- **Goal:** Immutable decision log with locking mechanism
- **Files Involved:**
  - `database/migrations/2026_01_27_300006_create_control_decisions_table.php`
  - `app/Modules/Control/Decisions/Models/Decision.php`
  - `app/Modules/Control/Decisions/Services/DecisionService.php`
  - [Additional controllers, livewire, views as per pattern]
- **Dependencies:** Step 31
- **Validation:** Decisions cannot be edited after is_locked=true; audit trail captures locking event

------

### Phase 10: KNOWLEDGE Domain - Project Journal

#### Step 33: Project Journal - Database Schema

- **Goal:** Activity feed with entity linking support
- **Files Involved:**
  - `database/migrations/2026_01_27_400001_create_knowledge_project_journal_table.php`
- **Dependencies:** Step 1, Step 2, Step 5 (EntityLinks)
- **Validation:** Table supports polymorphic links; chronological ordering; rich text content

#### Step 34: Project Journal - Models & Service with Entity Linking

- **Goal:** Journal entry creation with automatic entity link creation
- **Files Involved:**
  - `app/Modules/Knowledge/ProjectJournal/Models/JournalEntry.php`
  - `app/Modules/Knowledge/ProjectJournal/Services/JournalService.php`
  - `app/Modules/Knowledge/ProjectJournal/DTOs/CreateJournalEntryData.php`
- **Dependencies:** Step 33, Step 5
- **Validation:** Can create journal entry; links to risks/issues/milestones work; query retrieves linked entities

#### Step 35: Project Journal - Triple Interface with Activity Feed UI

- **Goal:** Social-style feed with inline entity previews
- **Files Involved:**
  - `app/Modules/Knowledge/ProjectJournal/Controllers/Api/JournalController.php`
  - `app/Modules/Knowledge/ProjectJournal/Controllers/Web/JournalController.php`
  - `app/Modules/Knowledge/ProjectJournal/Livewire/JournalFeed.php`
  - `app/Modules/Knowledge/ProjectJournal/Views/*.blade.php`
  - `resources/views/livewire/knowledge/journal/feed.blade.php`
- **Dependencies:** Step 34
- **Validation:** Feed displays chronologically; entity links render as cards; real-time updates via Livewire

------

### Phase 11: KNOWLEDGE Domain - Remaining Modules

#### Step 36: Meeting Minutes - Full Module Implementation

- **Goal:** Formal meeting record with attendee tracking
- **Files Involved:**
  - `database/migrations/2026_01_27_400002_create_knowledge_meeting_minutes_table.php`
  - `app/Modules/Knowledge/MeetingMinutes/Models/MeetingMinute.php`
  - `app/Modules/Knowledge/MeetingMinutes/Services/MeetingMinuteService.php`
  - [Additional controllers, livewire, views as per pattern]
- **Dependencies:** Step 35
- **Validation:** Can link to MeetingEvents; action items can be extracted; attendee list managed

#### Step 37: Stakeholders - Full Module Implementation

- **Goal:** Stakeholder registry with RACI matrix support
- **Files Involved:**
  - `database/migrations/2026_01_27_400003_create_knowledge_stakeholders_table.php`
  - `app/Modules/Knowledge/Stakeholders/Models/Stakeholder.php`
  - `app/Modules/Knowledge/Stakeholders/Services/StakeholderService.php`
  - `app/Modules/Knowledge/Stakeholders/Enums/StakeholderRole.php`
  - [Additional controllers, livewire, views as per pattern]
- **Dependencies:** Step 36
- **Validation:** RACI assignment works; engagement tracking functional; contact info managed

#### Step 38: Links - Full Module Implementation with Grouping

- **Goal:** Centralized URL repository with categorization
- **Files Involved:**
  - `database/migrations/2026_01_27_400004_create_knowledge_link_groups_table.php`
  - `database/migrations/2026_01_27_400005_create_knowledge_links_table.php`
  - `app/Modules/Knowledge/Links/Models/*.php`
  - `app/Modules/Knowledge/Links/Services/LinkService.php`
  - [Additional controllers, livewire, views as per pattern]
- **Dependencies:** Step 37
- **Validation:** URL validation works; grouping functional; quick-access bookmarks display

#### Step 39: Lists - Full Module Implementation with Grouping

- **Goal:** Flexible checklist management
- **Files Involved:**
  - `database/migrations/2026_01_27_400006_create_knowledge_list_groups_table.php`
  - `database/migrations/2026_01_27_400007_create_knowledge_lists_table.php`
  - `app/Modules/Knowledge/Lists/Models/*.php`
  - `app/Modules/Knowledge/Lists/Services/ListService.php`
  - [Additional controllers, livewire, views as per pattern]
- **Dependencies:** Step 38
- **Validation:** Checkbox toggling works; completion percentage calculates; grouping supported

#### Step 40: Lessons Learned - Full Module Implementation

- **Goal:** Retrospective knowledge capture with tagging
- **Files Involved:**
  - `database/migrations/2026_01_27_400008_create_knowledge_lessons_learned_table.php`
  - `app/Modules/Knowledge/LessonsLearned/Models/LessonLearned.php`
  - `app/Modules/Knowledge/LessonsLearned/Services/LessonLearnedService.php`
  - [Additional controllers, livewire, views as per pattern]
- **Dependencies:** Step 39
- **Validation:** Can categorize lessons; searchable by tags; exportable for future projects

------

### Phase 12: Cross-Module Integration & Dashboards

#### Step 41: Portfolio Dashboard (Admin Only)

- **Goal:** Aggregated view across all projects with drill-down capability
- **Files Involved:**
  - `app/Http/Controllers/Web/PortfolioDashboardController.php`
  - `app/Services/PortfolioAggregationService.php`
  - `resources/views/dashboards/portfolio.blade.php`
  - `app/Livewire/Dashboards/PortfolioOverview.php`
- **Dependencies:** All domain modules (Steps 1-40)
- **Validation:** Dashboard loads data from all projects; RAG roll-ups correct; admin-only access enforced

#### Step 42: Project Dashboard (All Roles)

- **Goal:** Single-project executive summary with key metrics
- **Files Involved:**
  - `app/Http/Controllers/Web/ProjectDashboardController.php`
  - `app/Services/ProjectDashboardService.php`
  - `resources/views/dashboards/project.blade.php`
  - `app/Livewire/Dashboards/ProjectMetrics.php`
- **Dependencies:** Step 41
- **Validation:** Displays milestone status, risk count, issue aging, recent journal entries; respects RBAC

#### Step 43: Search & Filtering System

- **Goal:** Global search across all modules with faceted filtering
- **Files Involved:**
  - `app/Services/GlobalSearchService.php`
  - `app/Http/Controllers/Web/SearchController.php`
  - `app/Livewire/Search/GlobalSearch.php`
  - `resources/views/search/results.blade.php`
- **Dependencies:** Step 42
- **Validation:** Can search across all entity types; filters by project/domain/status; results link to detail pages

------

### Phase 13: Reporting & Analytics

#### Step 44: Report Builder Service

- **Goal:** Configurable report generation with export (PDF/Excel)
- **Files Involved:**
  - `app/Services/ReportBuilderService.php`
  - `app/Exports/RiskRegisterExport.php`
  - `app/Exports/StatusReportExport.php`
  - `app/Http/Controllers/Web/ReportController.php`
- **Dependencies:** Step 43
- **Validation:** Can generate risk register PDF; status report exports to Excel; scheduled reports queue correctly

#### Step 45: Analytics & Metrics Service

- **Goal:** Compute project health scores, velocity trends, burn rates
- **Files Involved:**
  - `app/Services/AnalyticsService.php`
  - `app/DTOs/ProjectHealthMetrics.php`
  - `app/Livewire/Charts/BurndownChart.php`
  - `app/Livewire/Charts/RiskHeatMap.php`
- **Dependencies:** Step 44
- **Validation:** Health score algorithm accurate; charts render with real data; historical trends display

------

### Phase 14: Testing & Quality Assurance

#### Step 46: Unit Tests for Services & DTOs

- **Goal:** Achieve 80%+ code coverage for business logic layer
- **Files Involved:**
  - `tests/Unit/Modules/**/*ServiceTest.php`
  - `tests/Unit/Modules/**/*DTOTest.php`
  - `tests/Unit/Services/WeekCalculatorServiceTest.php`
- **Dependencies:** All service implementations (Steps 1-45)
- **Validation:** PHPUnit suite passes; coverage report meets threshold

#### Step 47: Feature Tests for API Endpoints

- **Goal:** Test all API routes with authentication/authorization scenarios
- **Files Involved:**
  - `tests/Feature/Modules/**/Api/*ControllerTest.php`
- **Dependencies:** Step 46
- **Validation:** All API endpoints return correct status codes; RBAC enforced; validation errors handled

#### Step 48: Feature Tests for Web & Livewire

- **Goal:** Browser-based testing of UI workflows
- **Files Involved:**
  - `tests/Feature/Modules/**/Web/*ControllerTest.php`
  - `tests/Feature/Modules/**/Livewire/*ComponentTest.php`
- **Dependencies:** Step 47
- **Validation:** Can navigate UI; forms submit; Livewire interactions work; policy checks function

#### Step 49: Integration Tests for Cross-Module Workflows

- **Goal:** Test scenarios involving multiple domains (e.g., Risk → Issue → Change Request)
- **Files Involved:**
  - `tests/Integration/Workflows/RiskEscalationWorkflowTest.php`
  - `tests/Integration/Workflows/JournalEntityLinkingTest.php`
- **Dependencies:** Step 48
- **Validation:** Complex workflows execute correctly; entity links resolve; audit logs complete

------

### Phase 15: Deployment Preparation

#### Step 50: Seeders for Demo Data

- **Goal:** Populate database with realistic sample projects for UAT
- **Files Involved:**
  - `database/seeders/UserSeeder.php`
  - `database/seeders/ProjectSeeder.php`
  - `database/seeders/DomainModuleSeeder.php`
- **Dependencies:** All migrations (Steps 1-40)
- **Validation:** `php artisan db:seed` completes; demo projects navigable; all relationships intact

#### Step 51: API Documentation Generation

- **Goal:** Generate Swagger/OpenAPI docs for all API endpoints
- **Files Involved:**
  - `app/Http/Controllers/Api/ApiDocumentationController.php`
  - `resources/views/api/documentation.blade.php`
  - Configure Scribe or L5-Swagger
- **Dependencies:** Step 47
- **Validation:** API docs accessible at /api/documentation; all endpoints documented; examples provided

#### Step 52: Performance Optimization

- **Goal:** Implement caching, eager loading, query optimization
- **Files Involved:**
  - `app/Services/CacheService.php`
  - Optimize N+1 queries in repositories
  - Add Redis caching for dashboards
- **Dependencies:** Step 49
- **Validation:** Laravel Debugbar shows reduced query count; dashboard loads <500ms; cache invalidation works

#### Step 53: Security Hardening

- **Goal:** CSRF protection, rate limiting, SQL injection prevention review
- **Files Involved:**
  - `app/Http/Middleware/RateLimitMiddleware.php`
  - Review all FormRequests for injection risks
  - Configure CORS policies
- **Dependencies:** Step 52
- **Validation:** Security audit passes; rate limiting blocks abuse; CSRF tokens validated

#### Step 54: Deployment Configuration & CI/CD

- **Goal:** Docker/Kubernetes configs, GitHub Actions pipeline
- **Files Involved:**
  - `docker-compose.yml`
  - `.github/workflows/laravel-ci.yml`
  - `deploy.sh`
- **Dependencies:** Step 53
- **Validation:** CI pipeline runs tests on push; Docker build succeeds; deployment script works on staging

------

## 3. Questions & Clarifications

### Ambiguities Requiring Resolution:

1. **User Authentication System:**
   - Should we use Laravel Breeze, Jetstream, or custom authentication?
   - Do we need 2FA/MFA support?
   - SSO/SAML integration required?
2. **File Attachments:**
   - The data models mention file attachments for several entities (StatusReports, Deliverables, MeetingMinutes, etc.)
   - Should we use local storage, S3, or another cloud provider?
   - Maximum file size limits?
   - File type restrictions?
   - Do we need virus scanning for uploads?
3. **Notifications System:**
   - Email notifications required for which events? (e.g., risk status changes, approaching deadlines, new assignments)
   - In-app notifications via database?
   - Real-time notifications via WebSockets/Pusher?
4. **Recurring Meeting Events:**
   - Step 28 mentions "recurring event support" but the data model doesn't specify the recurrence pattern fields
   - Should we use `spatie/laravel-event-sourcing` or a simpler RRULE-based approach?
5. **Risk Scoring Calculation:**
   - The data model mentions Probability × Impact but doesn't specify the numeric scale
   - Should we use standard 1-5 scale or custom values?
   - How do we compute the overall RAG status from the score?
6. **Project Journal Entity Linking:**
   - Can journal entries link to multiple entities simultaneously (e.g., one entry referencing 3 risks and 2 issues)?
   - Should entity links be bidirectional (queryable from both sides)?
7. **Soft Delete Implementation:**
   - Specifications say "soft deletes only" and "boolean flag not timestamp"
   - Should we override Laravel's default `deleted_at` timestamp approach?
   - How should queries handle soft-deleted records (globally scoped or manual filtering)?
8. **Frontend Framework Version:**
   - Which version of Livewire (v2 or v3)?
   - Flux UI component library version and installation method?
   - Alpine.js version compatibility?
9. **Testing Strategy:**
   - Preferred testing framework: PHPUnit, Pest, or both?
   - Do we need browser testing with Laravel Dusk/Playwright?
   - Code coverage threshold requirement?
10. **API Versioning:**
    - Should the API be versioned from the start (e.g., `/api/v1/...`)?
    - GraphQL support needed alongside REST?
11. **Week Calculation Display Format:**
    - The specs show "Week 4 of January" but January can have 4-5 weeks
    - Should we display ISO week number, week-of-month, or both?
    - How do we handle year-end edge cases (Week 52/53)?
12. **Immutability Enforcement:**
    - For immutable entities (Notes, Decisions when locked, published StatusReports), should we:
      - Throw exceptions on update attempts?
      - Silently ignore updates?
      - Return validation errors?
13. **Portfolio Dashboard Data Volume:**
    - Expected number of concurrent projects (10s, 100s, 1000s)?
    - Pagination strategy for large datasets?
    - Caching strategy for expensive aggregations?
14. **Localization/Internationalization:**
    - Is multi-language support required?
    - Date/time format preferences?
    - Currency format for budget tracking (if applicable)?
15. **Backup & Disaster Recovery:**
    - Automated backup strategy required?
    - Point-in-time recovery needed?
    - Multi-region deployment considerations?

------

**AWAITING YOUR APPROVAL TO PROCEED WITH IMPLEMENTATION.**

Once you approve this plan or provide clarifications, I will begin sequential coding starting with Step 1.