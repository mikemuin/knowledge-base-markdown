# Overwatch PMO Governance Suite - Implementation Task List

**Version:** 1.1 (Corrected) **Date:** January 28, 2026 **Total Tasks:** 117 atomic steps **Architecture:** Laravel 11 + Livewire 3 (TALL Stack) Modular Monolith

------

## CRITICAL IMPLEMENTATION RULES

**Before starting any task:**

1. Execute steps in EXACT sequential order
2. Validate each step before proceeding to the next
3. Run `php artisan test` after every 10-15 steps
4. Git commit after each successfully validated step
5. Read relevant SKILL.md files before file creation tasks

**Architecture Principles:**

- **Modular Monolith** - Self-contained modules under `app/Modules/`
- **Triple Interface** - API, Web, Livewire for every module
- **RBAC Enforcement** - Admin, Editor, Viewer roles throughout
- **Soft Deletes Only** - No hard deletes anywhere
- **Event-Driven** - All state changes fire events
- **Immutability** - Notes, published reports, locked decisions

------

## PHASE 1: FOUNDATION & SHARED SYSTEMS (Steps 1-50c)

### Initial Laravel Setup

- [ ] **Step 1:** Initialize fresh Laravel 11 application
  - Command: `composer create-project laravel/laravel overwatch-pm`
  - Validate: `php artisan --version` returns Laravel 11.x
- [ ] **Step 2:** Configure environment file for local development
  - Set `APP_NAME=Overwatch`
  - Set `DB_CONNECTION=sqlite`
  - Remove MySQL credentials
  - Set `DB_DATABASE` to absolute path
- [ ] **Step 3:** Create SQLite database file
  - Command: `touch database/database.sqlite`
  - Validate: `php artisan migrate:status` connects successfully

### TALL Stack Installation

- [ ] **Step 4:** Install Livewire 3
  - Command: `composer require livewire/livewire:^3.0`
  - Validate: `composer show livewire/livewire` shows version 3.x
- [ ] **Step 5:** Publish Livewire configuration
  - Command: `php artisan livewire:publish --config`
  - Validate: `config/livewire.php` exists
- [ ] **Step 6:** Install Node dependencies for TALL stack
  - Command: `npm install -D tailwindcss postcss autoprefixer alpinejs`
  - Validate: Check `package.json` for dependencies
- [ ] **Step 7:** Initialize Tailwind CSS configuration
  - Command: `npx tailwindcss init -p`
  - Validate: Both `tailwind.config.js` and `postcss.config.js` exist
- [ ] **Step 8:** Configure Tailwind content paths
  - Update `tailwind.config.js` with module paths
  - Include: `./app/Modules/**/Views/**/*.blade.php`
  - Include: `./app/Modules/**/Livewire/**/*.php`
- [ ] **Step 9:** Set up Tailwind directives in CSS
  - Update `resources/css/app.css` with `@tailwind` directives
  - Must include: base, components, utilities
- [ ] **Step 10:** Configure Alpine.js in app.js
  - Import Alpine in `resources/js/app.js`
  - Initialize: `Alpine.start()`
- [ ] **Step 11:** Update Vite configuration for module structure
  - Add module paths to refresh array in `vite.config.js`
- [ ] **Step 12:** Build frontend assets
  - Command: `npm run build`
  - Validate: `public/build/manifest.json` exists

### Base Layout & Authentication

- [ ] **Step 13:** Create base layout Blade template
  - File: `resources/views/layouts/app.blade.php`
  - Include: Vite directives, Livewire styles/scripts
- [ ] **Step 14:** Install Laravel Breeze (minimal auth scaffolding)
  - Command: `composer require laravel/breeze --dev`
  - Command: `php artisan breeze:install blade`
- [ ] **Step 15:** Run Breeze migrations
  - Command: `php artisan migrate`
  - Validate: Users table created
- [ ] **Step 16:** Create base guest layout (if not from Breeze)
  - File: `resources/views/layouts/guest.blade.php`

### Core Database Migrations

- [ ] **Step 17:** Modify users table migration (add role, soft deletes)
  - Add: `role` enum (admin, editor, viewer) with default 'viewer'
  - Add: `softDeletes()`
  - Add indexes on `email` and `role`
  - **Complete schema must include:** id, name, email, email_verified_at, password, role, remember_token, timestamps, soft deletes
- [ ] **Step 18:** Create projects table migration
  - Fields: id, code (unique), title, description (text), status enum, rag_status enum
  - Fields: start_date, target_end_date, actual_end_date (nullable)
  - Foreign keys: project_manager_id, executive_sponsor_id (to users)
  - Add: softDeletes(), metadata (created_by, updated_by)
  - Indexes: code, status, rag_status, project_manager_id, executive_sponsor_id
- [ ] **Step 19:** Create project_user pivot table migration
  - For Editor role project assignments
  - Fields: project_id, user_id, timestamps
  - Composite unique index on (project_id, user_id)

### Laravel Sanctum Setup (CRITICAL FOR API)

- [ ] **Step 20a:** Install Laravel Sanctum package
  - Command: `composer require laravel/sanctum`
  - Validate: Package appears in composer.json
- [ ] **Step 20b:** Publish Sanctum configuration and migrations
  - Command: `php artisan vendor:publish --provider="Laravel\Sanctum\SanctumServiceProvider"`
  - Validate: `config/sanctum.php` and migration files exist
- [ ] **Step 20c:** Run Sanctum migration
  - Command: `php artisan migrate`
  - Validate: `personal_access_tokens` table created
- [ ] **Step 20d:** Configure Sanctum middleware in bootstrap/app.php
  - Add Sanctum API middleware to API routes
  - Ensure `auth:sanctum` works in routes

### Shared Models & Migrations

- [ ] **Step 21:** Create projects model
  - Location: `app/Models/Project.php`
  - Add relationships: belongsToMany(User), hasMany for all module entities
  - Add soft deletes trait
- [ ] **Step 22:** Create shared enums directory and enums
  - Location: `app/Modules/Shared/Enums/`
  - Create: `ProjectStatus.php`, `RagStatus.php`, `ExecutionStatus.php`
  - Create: `MeetingEventStatus.php`, `MeetingType.php`
  - Create: `DeliverableType.php`, `DependencyType.php`, `DependencyStatus.php`
  - Create: `RiskStatus.php`, `RiskSeverity.php`, `RiskProbability.php`, `RiskImpact.php`
  - Create: `IssueStatus.php`, `IssueImpact.php`, `IssueUrgency.php`, `IssuePriority.php`
  - Create: `ChangeRequestStatus.php`, `DecisionStatus.php`
  - Create: `ProjectPhase.php`, `LessonsCategory.php`, `LessonsImportance.php`
  - Create: `JournalCategory.php`, `LinkType.php`, `EntityLinkType.php`
  - Create: `Priority.php` (for general use)
- [ ] **Step 23:** Create notes table migration (polymorphic)
  - Fields: id, notable_type, notable_id, content (text), is_system_generated (boolean)
  - Metadata: created_at, created_by (NO updated_at/updated_by - immutable)
  - Indexes: Composite on (notable_type, notable_id)
- [ ] **Step 24:** Create notes model
  - Location: `app/Modules/Shared/Notes/Models/Note.php`
  - Implement morphTo relationship for `notable`
  - NO soft deletes (notes cannot be deleted)
- [ ] **Step 25:** Create note observer (enforce immutability)
  - Location: `app/Modules/Shared/Notes/Observers/NoteObserver.php`
  - Throw exception on `updating()` event
  - Throw exception on `deleting()` event
  - Register in AppServiceProvider
- [ ] **Step 26:** Create entity_links table migration (polymorphic)
  - Fields: id, source_type (always 'JournalEntry'), source_id
  - Fields: target_type (polymorphic), target_id
  - Fields: link_type enum, description (text), metadata
  - Indexes: Composite on (source_type, source_id), (target_type, target_id)
- [ ] **Step 27:** Create entity link model
  - Location: `app/Modules/Shared/EntityLinks/Models/EntityLink.php`
  - Implement morphTo relationships

### Service Providers & Observers

- [ ] **Step 28:** Create base model observer for metadata
  - Location: `app/Observers/BaseModelObserver.php`
  - Auto-populate created_by/updated_by in creating/updating events
  - Apply to all models via AppServiceProvider
- [ ] **Step 29:** Update AppServiceProvider
  - Register all observers (Note, BaseModel)
  - Boot shared resources
  - Register enum casting for Carbon

### Policies & Authorization

- [ ] **Step 30:** Create base policy
  - Location: `app/Policies/BasePolicy.php`
  - Implement RBAC logic (admin, editor, viewer roles)
  - Helper methods for role checks
- [ ] **Step 31:** Create project policy
  - Location: `app/Policies/ProjectPolicy.php`
  - Implement: viewAny, view, create, update, delete
  - Admin: Full access
  - Editor: Only assigned projects
  - Viewer: Read-only
- [ ] **Step 32:** Register policies in AuthServiceProvider
  - Map Project model to ProjectPolicy

### Shared Traits & Utilities

- [ ] **Step 33:** Create HasGroups trait
  - Location: `app/Modules/Shared/Traits/HasGroups.php`
  - For entities supporting 2-level grouping
  - Include validation logic for max 2 levels
- [ ] **Step 34:** Create HasInternalVisibility trait
  - Location: `app/Modules/Shared/Traits/HasInternalVisibility.php`
  - Scope: `visibleTo($user)` method
  - Filter based on `is_internal_only` and user role
- [ ] **Step 35:** Create WeekCalculatorService
  - Location: `app/Modules/Shared/Services/WeekCalculatorService.php`
  - Method: `computeTargetWeek($date)` - returns ISO 8601 format (2026-W04)
  - Method: `formatWeekForDisplay($targetWeek)` - returns "Week 4 of January"

### Shared Services

- [ ] **Step 36:** Create NoteService
  - Location: `app/Modules/Shared/Notes/Services/NoteService.php`
  - Methods: create, getForEntity, list (recent first)
- [ ] **Step 37:** Create EntityLinkService
  - Location: `app/Modules/Shared/EntityLinks/Services/EntityLinkService.php`
  - Methods: createLink, getLinksForJournalEntry, getLinksToEntity

### Install Spatie Activity Log

- [ ] **Step 38:** Install spatie/laravel-activitylog
  - Command: `composer require spatie/laravel-activitylog`
- [ ] **Step 39:** Publish activitylog migrations
  - Command: `php artisan vendor:publish --provider="Spatie\Activitylog\ActivitylogServiceProvider" --tag="activitylog-migrations"`
- [ ] **Step 40:** Run activitylog migration
  - Command: `php artisan migrate`
- [ ] **Step 41:** Publish activitylog config
  - Command: `php artisan vendor:publish --provider="Spatie\Activitylog\ActivitylogServiceProvider" --tag="activitylog-config"`
- [ ] **Step 42:** Configure activitylog
  - File: `config/activitylog.php`
  - Set: `delete_records_older_than_days` to 0 (never delete)
- [ ] **Step 43:** Create Project model
  - Location: `app/Models/Project.php`
  - Add SoftDeletes trait
  - Add activity logging
- [ ] **Step 44:** Configure activity logging on Project model
  - Use trait: `Spatie\Activitylog\Traits\LogsActivity`
  - Method: `getActivitylogOptions()` returns `LogOptions::defaults()->logAll()`
  - **CRITICAL:** Use correct Spatie syntax, not fictional `LogsAllChanges` trait

### Seeders

- [ ] **Step 45:** Create UserSeeder
  - Create admin, editor, viewer test users
  - Location: `database/seeders/UserSeeder.php`
- [ ] **Step 46:** Create ProjectSeeder
  - Create 3-5 test projects with various statuses
  - Location: `database/seeders/ProjectSeeder.php`
- [ ] **Step 47:** Update DatabaseSeeder
  - Call UserSeeder and ProjectSeeder
- [ ] **Step 48:** Run seeders
  - Command: `php artisan db:seed`
  - Validate: Users and projects exist in database

### API Routes Structure

- [ ] **Step 49:** Create API route structure
  - File: `routes/api.php`
  - Setup route groups with `auth:sanctum` middleware
  - Structure: `/api/projects/{project}/{module}`

### Module View Service Provider (CRITICAL FOR BLADE TEMPLATES)

- [ ] **Step 50a:** Create ModuleViewServiceProvider
  - Location: `app/Providers/ModuleViewServiceProvider.php`
  - Register view namespaces for all modules
  - Example: `View::addNamespace('status-reports', app_path('Modules/StatusReports/Views'))`
- [ ] **Step 50b:** Register ModuleViewServiceProvider in bootstrap/providers.php
  - Add to providers array
- [ ] **Step 50c:** Test view registration
  - Create test to verify `view()->exists('status-reports::index')` works
  - Run after StatusReports views are created

------

## PHASE 2: STATUSREPORTS REFERENCE MODULE (Steps 51-110)

### StatusReports Migration & Model

- [ ] **Step 51:** Create status_reports table migration
  - Fields: id, project_id (FK), report_period_start, report_period_end
  - Fields: overall_status (RAG enum), executive_summary (text)
  - Fields: achievements (text), challenges (text), next_period_focus (text)
  - Fields: snapshot_data (JSON), published_at (nullable), published_by (FK nullable)
  - Fields: is_draft (boolean default true), softDeletes()
  - Metadata: created_at, created_by, updated_at, updated_by
  - Indexes: project_id, published_at, is_draft
- [ ] **Step 52:** Run migration
  - Command: `php artisan migrate`
- [ ] **Step 53:** Create StatusReport model
  - Location: `app/Modules/StatusReports/Models/StatusReport.php`
  - Add SoftDeletes trait
  - Add LogsActivity trait
  - Relationships: belongsTo(Project), belongsTo(User as publishedBy)
  - Casts: overall_status to RagStatus enum, snapshot_data to array
- [ ] **Step 54:** Create StatusReportObserver (immutability enforcement)
  - Location: `app/Modules/StatusReports/Observers/StatusReportObserver.php`
  - Prevent updates when `published_at` is set
  - Prevent deletion when `published_at` is set
  - Register in AppServiceProvider
- [ ] **Step 55:** Create StatusReport factory
  - Location: `database/factories/StatusReportFactory.php`
  - Generate realistic test data

### StatusReports DTOs

- [ ] **Step 56:** Create CreateStatusReportData DTO
  - Location: `app/Modules/StatusReports/DTOs/CreateStatusReportData.php`
  - Properties: project_id, report_period_start, report_period_end, overall_status
  - Properties: executive_summary, achievements, challenges, next_period_focus
  - Factory method: `fromRequest(Request $request)`
- [ ] **Step 57:** Create UpdateStatusReportData DTO
  - Location: `app/Modules/StatusReports/DTOs/UpdateStatusReportData.php`
  - Similar properties to Create (excluding project_id)

### StatusReports Repository

- [ ] **Step 58:** Create StatusReportRepository
  - Location: `app/Modules/StatusReports/Repositories/StatusReportRepository.php`
  - Method: `queryByProject($projectId)` - returns Query Builder (NOT paginated)
  - Method: `findById($id)` - returns model or null
  - Method: `getPublishedReports($projectId)` - returns Query Builder
  - Method: `getDraftReports($projectId)` - returns Query Builder

### StatusReports Service Layer

- [ ] **Step 59:** Create StatusReportService
  - Location: `app/Modules/StatusReports/Services/StatusReportService.php`
  - Inject StatusReportRepository
  - Method: `create(CreateStatusReportData $data)` - fires StatusReportCreated event
- [ ] **Step 60:** Implement update method in StatusReportService
  - Method: `update(StatusReport $report, UpdateStatusReportData $data)`
  - Validate not published before updating
  - Fire StatusReportUpdated event
- [ ] **Step 61:** Implement publish method in StatusReportService
  - Method: `publish(StatusReport $report, User $user)`
  - Set published_at, published_by
  - Generate snapshot_data (milestone counts, risk counts, issue counts, CR counts)
  - Fire StatusReportPublished event
  - **NO financial data in snapshot**
- [ ] **Step 62:** Implement delete method in StatusReportService
  - Method: `delete(StatusReport $report)`
  - Validate not published before deleting (soft delete only)
  - Fire StatusReportDeleted event
- [ ] **Step 63:** Implement list method in StatusReportService
  - Method: `list(int $projectId, array $filters = [])`
  - Return paginated results using repository
- [ ] **Step 64:** Implement snapshot generation helper
  - Private method: `generateSnapshot(Project $project)`
  - Count milestones by status
  - Count risks by severity
  - Count issues by impact
  - Count change requests by status
  - Return as array for JSON storage
- [ ] **Step 65:** Create StatusReportPolicy
  - Location: `app/Modules/StatusReports/Policies/StatusReportPolicy.php`
  - Methods: viewAny, view, create, update, delete, publish
  - Admin: Full access
  - Editor: Only assigned projects
  - Viewer: Read-only published reports
- [ ] **Step 66:** Register StatusReportPolicy in AuthServiceProvider

### StatusReports Events

- [ ] **Step 67:** Create StatusReportCreated event
  - Location: `app/Modules/StatusReports/Events/StatusReportCreated.php`
  - Property: StatusReport $statusReport
- [ ] **Step 68:** Create StatusReportPublished event
  - Location: `app/Modules/StatusReports/Events/StatusReportPublished.php`
  - Property: StatusReport $statusReport
  - For audit logging and notifications
- [ ] **Step 69:** Add missing getById method to StatusReportService
  - **CRITICAL FIX:** Method: `getById(int $id): ?StatusReport`
  - Implementation: `return $this->repository->findById($id);`
  - **Required by Livewire components in Step 103**

### StatusReports Form Requests

- [ ] **Step 70:** Create StoreStatusReportRequest (API)
  - Location: `app/Modules/StatusReports/FormRequests/Api/StoreStatusReportRequest.php`
  - Validation rules for all required fields
  - Authorize based on policy
- [ ] **Step 71:** Create UpdateStatusReportRequest (API)
  - Location: `app/Modules/StatusReports/FormRequests/Api/UpdateStatusReportRequest.php`
  - Check report is not published before allowing update
- [ ] **Step 72:** Create PublishStatusReportRequest (API)
  - Location: `app/Modules/StatusReports/FormRequests/Api/PublishStatusReportRequest.php`
  - Validate report is complete before publishing
- [ ] **Step 73:** Create StoreStatusReportRequest (Web)
  - Location: `app/Modules/StatusReports/FormRequests/Web/StoreStatusReportRequest.php`
  - Same validation as API version
- [ ] **Step 74:** Create UpdateStatusReportRequest (Web)
  - Location: `app/Modules/StatusReports/FormRequests/Web/UpdateStatusReportRequest.php`
  - Same validation as API version

### StatusReports API Resources

- [ ] **Step 75:** Create StatusReportResource
  - Location: `app/Modules/StatusReports/Resources/StatusReportResource.php`
  - Transform model to API JSON format
  - Include relationships: project, publishedBy
  - Format dates consistently
- [ ] **Step 76:** Create StatusReportCollection
  - Location: `app/Modules/StatusReports/Resources/StatusReportCollection.php`
  - Wrap paginated results
  - Include meta information

### StatusReports API Controller

- [ ] **Step 77:** Create StatusReportController (API)
  - Location: `app/Modules/StatusReports/Controllers/Api/StatusReportController.php`
  - Inject StatusReportService
  - Method: `index()` - list reports with filters
- [ ] **Step 78:** Implement store method in API controller
  - Method: `store(StoreStatusReportRequest $request)`
  - Convert request to DTO
  - Call service create method
  - Return StatusReportResource with 201 status
- [ ] **Step 79:** Implement show method in API controller
  - Method: `show(StatusReport $report)`
  - Authorize via policy
  - Return StatusReportResource
- [ ] **Step 80:** Implement update method in API controller
  - Method: `update(UpdateStatusReportRequest $request, StatusReport $report)`
  - Convert request to DTO
  - Call service update method
  - Return StatusReportResource
- [ ] **Step 81:** Implement destroy method in API controller
  - Method: `destroy(StatusReport $report)`
  - Authorize via policy
  - Call service delete method (soft delete)
  - Return 204 No Content
- [ ] **Step 82:** Implement publish method in API controller
  - Route: `POST /api/projects/{project}/status-reports/{report}/publish`
  - Method: `publish(PublishStatusReportRequest $request, StatusReport $report)`
  - Call service publish method
  - Return StatusReportResource with published data
- [ ] **Step 83:** Register API routes for StatusReports
  - File: `routes/api.php`
  - Resource routes with `auth:sanctum` middleware
  - Additional route for publish action

### StatusReports Web Views (Traditional Blade)

- [ ] **Step 84:** Create StatusReports index view
  - Location: `app/Modules/StatusReports/Views/index.blade.php`
  - Display: Project info, list of reports, filter controls
  - Embed Livewire component for interactive list
- [ ] **Step 85:** Create StatusReports create view
  - Location: `app/Modules/StatusReports/Views/create.blade.php`
  - Form for new report
  - Use Livewire component for reactive form
- [ ] **Step 86:** Create StatusReports show view
  - Location: `app/Modules/StatusReports/Views/show.blade.php`
  - Display full report details
  - Show snapshot data
  - Display publish status and locked indicator
- [ ] **Step 87:** Create StatusReports edit view
  - Location: `app/Modules/StatusReports/Views/edit.blade.php`
  - Only accessible for draft reports
  - Use Livewire component for reactive form

### StatusReports Web Controller

- [ ] **Step 88:** Create StatusReportController (Web)
  - Location: `app/Modules/StatusReports/Controllers/Web/StatusReportController.php`
  - Method: `index(Project $project)` - returns view with reports
  - **CRITICAL:** Use namespace notation: `view('status-reports::index')`
- [ ] **Step 89:** Implement create method in Web controller
  - Method: `create(Project $project)`
  - Return create view with project context
- [ ] **Step 90:** Implement show method in Web controller
  - Method: `show(Project $project, StatusReport $report)`
  - **CRITICAL:** Use namespace notation: `view('status-reports::show')`
  - Pass report to view
- [ ] **Step 91:** Implement edit method in Web controller
  - Method: `edit(Project $project, StatusReport $report)`
  - Check if report is draft (not published)
  - Return edit view
- [ ] **Step 92:** Implement store method in Web controller
  - Method: `store(StoreStatusReportRequest $request, Project $project)`
  - Create via service
  - Redirect to show page with success message
- [ ] **Step 93:** Implement update method in Web controller
  - Method: `update(UpdateStatusReportRequest $request, Project $project, StatusReport $report)`
  - Update via service
  - **CRITICAL:** Redirect using proper route naming
  - Flash success message
- [ ] **Step 94:** Implement destroy method in Web controller
  - Method: `destroy(Project $project, StatusReport $report)`
  - Soft delete via service
  - Redirect to index with success message
- [ ] **Step 95:** Implement publish method in Web controller
  - Route: `POST /projects/{project}/status-reports/{report}/publish`
  - Method: `publish(Project $project, StatusReport $report)`
  - **CRITICAL:** Use namespace notation for redirects
  - Call service publish method
  - Flash success message
- [ ] **Step 96:** Register Web routes for StatusReports
  - File: `routes/web.php`
  - Resource routes with `auth` middleware
  - Additional route for publish action

### StatusReports ViewModels (Optional for complex views)

- [ ] **Step 97:** Create StatusReportIndexViewModel
  - Location: `app/Modules/StatusReports/ViewModels/StatusReportIndexViewModel.php`
  - Prepare data for index view (project, reports, filters)
- [ ] **Step 98:** Create StatusReportShowViewModel
  - Location: `app/Modules/StatusReports/ViewModels/StatusReportShowViewModel.php`
  - Format snapshot data for display
  - Calculate derived values

### StatusReports Livewire Components

- [ ] **Step 99:** Create StatusReportList Livewire component class
  - Location: `app/Modules/StatusReports/Livewire/StatusReportList.php`
  - Properties: projectId, filters, sortField, sortDirection
  - Methods: render, applyFilters, sort, delete
  - Pagination support
- [ ] **Step 100:** Create StatusReportList component view
  - Location: `resources/views/livewire/status-reports/list.blade.php`
  - Table with filter controls
  - Action buttons (view, edit, delete, publish)
  - Pagination links
- [ ] **Step 101:** Create StatusReportTable Livewire component class
  - Location: `app/Modules/StatusReports/Livewire/StatusReportTable.php`
  - Focused component for just the table display
  - Properties: reports collection
  - Real-time reactive updates
- [ ] **Step 102:** Create StatusReportTable component view
  - Location: `resources/views/livewire/status-reports/table.blade.php`
  - Clean table markup with wire:model bindings
- [ ] **Step 103:** Create StatusReportForm Livewire component class
  - Location: `app/Modules/StatusReports/Livewire/StatusReportForm.php`
  - Properties: report (nullable for create mode), form fields
  - Methods: mount, save, rules
  - Handle both create and edit modes
  - **USES getById() from service layer**
- [ ] **Step 104:** Create StatusReportForm component view
  - Location: `resources/views/livewire/status-reports/form.blade.php`
  - Form fields with wire:model.live for reactive validation
  - Submit button with loading state
  - Display validation errors inline
- [ ] **Step 105:** Create StatusReportCard Livewire component class
  - Location: `app/Modules/StatusReports/Livewire/StatusReportCard.php`
  - Properties: report model
  - For displaying summary in dashboards
  - Compact card format
- [ ] **Step 106:** Create StatusReportCard component view
  - Location: `resources/views/livewire/status-reports/card.blade.php`
  - Card layout with RAG badge, dates, summary
  - Link to full report

### Testing & Documentation

- [ ] **Step 107:** Test complete StatusReports module end-to-end
  - **API Testing:** Use Postman/Insomnia
  - Create report via POST
  - Update draft report
  - Publish report
  - Verify immutability (attempt to update published report should fail)
  - **Web Testing:** Manual browser testing
  - Navigate through all CRUD operations
  - Test publish workflow
  - **Livewire Testing:** Test reactive components
  - Verify real-time updates
  - Test form validation
- [ ] **Step 108:** Create feature tests for StatusReports
  - Location: `tests/Feature/Modules/StatusReports/`
  - Files: `ApiStatusReportTest.php`, `WebStatusReportTest.php`, `LivewireStatusReportTest.php`
  - Test all CRUD operations
  - Test RBAC enforcement
  - Test publish workflow and immutability
  - Command: `php artisan test --filter=StatusReport`
- [ ] **Step 109:** Create unit tests for StatusReport service
  - Location: `tests/Unit/Modules/StatusReports/Services/StatusReportServiceTest.php`
  - Test cases:
    - Create report
    - Update draft report
    - Attempt to update published report (should throw exception)
    - Publish report (verify snapshot generation)
    - Delete report
  - Mock repository dependencies
- [ ] **Step 110:** Document StatusReports API in PHPDoc
  - File: `app/Modules/StatusReports/Controllers/Api/StatusReportController.php`
  - Add comprehensive docblocks to each method
  - Include: description, parameters, return types, response codes
  - For future API documentation generation (Scribe, OpenAPI)

------

## VALIDATION CHECKPOINTS

### After Step 20d (Sanctum Setup)

```bash
php artisan route:list | grep sanctum
# Should show Sanctum middleware registered
```

### After Step 50c (View Registration)

```php
// In tinker
view()->exists('status-reports::index');
// Should return true (once StatusReports views created)
```

### After Step 69 (Service Methods)

```php
// In tinker
$service = app(StatusReportService::class);
method_exists($service, 'getById'); // Should return true
```

### After Step 107 (Complete Module Test)

```bash
# Verify all interfaces work
php artisan test --filter=StatusReport
# All tests should pass
```

------

## NEXT MODULES TO IMPLEMENT (After StatusReports)

Use the same 60-step pattern established in StatusReports (Steps 51-110) for each of these modules:

### GOVERNANCE Domain

- [ ] **StatusReports** - COMPLETED (Reference Module)

### EXECUTION Domain

- [ ] **Milestones** - Critical stage gates with 2-level grouping
- [ ] **ActionItems** - High-level directives with assignment
- [ ] **Deliverables** - Tangible outputs with sign-offs
- [ ] **Dependencies** - Blockers and constraints register
- [ ] **MeetingEvents** - Governance meeting schedule

### CONTROL Domain

- [ ] **Risks** - Future uncertainties with mitigation
- [ ] **Issues** - Active problems requiring resolution
- [ ] **ChangeRequests** - Scope and schedule change workflow
- [ ] **Decisions** - Immutable strategic decision log

### KNOWLEDGE Domain

- [ ] **ProjectJournal** - Social feed with entity linking
- [ ] **MeetingMinutes** - Formal meeting records
- [ ] **Stakeholders** - Stakeholder registry
- [ ] **Links** - Centralized URL repository
- [ ] **Lists** - Flexible checklist management
- [ ] **LessonsLearned** - Retrospective tool

**Total Remaining Modules:** 15 **Estimated Steps:** 15 modules × 60 steps = 900 additional steps **Total Project Scope:** ~1,067 atomic steps

------

## MODULE REPLICATION CHECKLIST

For each new module, replicate this pattern:

- [ ] Create migration (tables + group table if needed)
- [ ] Create model with traits (SoftDeletes, LogsActivity)
- [ ] Create observer (if immutability needed)
- [ ] Create factory
- [ ] Create DTOs (Create, Update)
- [ ] Create repository
- [ ] Create service layer (CRUD + business logic)
- [ ] Create policy
- [ ] Create events (Created, Updated, Deleted)
- [ ] Create form requests (API + Web, Store + Update)
- [ ] Create API resources (Resource + Collection)
- [ ] Create API controller (index, store, show, update, destroy)
- [ ] Create web views (index, create, show, edit)
- [ ] Create web controller (full CRUD + custom actions)
- [ ] Create view models (if needed for complex views)
- [ ] Create Livewire components (List, Form, Card, etc.)
- [ ] Create Livewire views
- [ ] Register routes (API + Web)
- [ ] End-to-end testing (API, Web, Livewire)
- [ ] Feature tests
- [ ] Unit tests (service layer)
- [ ] API documentation (PHPDoc)

------

## QUALITY GATES (Every 25 Steps)

- [ ] Run full test suite: `php artisan test`
- [ ] Check code coverage: `php artisan test --coverage`
- [ ] Review architectural consistency
- [ ] Validate RBAC enforcement across all new endpoints
- [ ] Test all three interfaces (API, Web, Livewire)
- [ ] Verify soft deletes working correctly
- [ ] Check event firing and audit logging
- [ ] Validate immutability constraints
- [ ] Review query performance (N+1 issues)
- [ ] Git commit with descriptive message

------

## CRITICAL REMINDERS

### Architectural Rules

1. **Repository Pattern:** Returns Query Builder, NOT paginated results
2. **Triple Interface:** API first, then Web, then Livewire
3. **Service Layer:** All business logic in services, not controllers
4. **DTOs:** Domain-bound, reusable across interfaces
5. **FormRequests:** Interface-bound, specific to endpoints

### Data Integrity Rules

1. **Soft Deletes Only:** Use `SoftDeletes` trait everywhere
2. **Auto-Compute target_week ONLY:** No other computed fields
3. **Manual PM Control:** RAG status, priorities, severity = PM declares
4. **Immutability:** Notes (always), Status Reports (when published), Decisions (when locked)
5. **Metadata:** created_by/updated_by auto-populated via observer

### Security Rules

1. **Authorize Every Action:** Use policies consistently
2. **RBAC Enforcement:** Admin, Editor, Viewer roles throughout
3. **Visibility Filtering:** Respect `is_internal_only` flag
4. **Sanctum for API:** All API routes use `auth:sanctum`

### Performance Rules

1. **Eager Loading:** Always use `with()` for relationships
2. **Index Everything:** Foreign keys, status fields, dates
3. **Query Optimization:** Composite indexes on (project_id, status, deleted_at)

------

## QUESTIONS REQUIRING PRODUCT OWNER DECISIONS

These ambiguities should be resolved before implementing affected modules:

1. **Flux UI Component Library:** Version and integration approach (RESOLVED: Not using in V1)
2. **Week Calculation Display:** ISO week vs week-of-month edge cases
3. **Status Report Snapshots:** Scope of data captured (only counts, no details?)
4. **Entity Linking Scope:** V1 limitation or future expansion?
5. **Editor Project Assignment:** Manual by Admin or automatic?
6. **Internal Visibility Implementation:** Global scope vs manual filtering?
7. **Testing Coverage Thresholds:** Minimum acceptable percentages?
8. **MeetingEvents ↔ MeetingMinutes:** Should there be FK relationship?
9. **Change Request Approval:** Multi-stage workflow or simple binary?
10. **Lists Module:** Flat items or parent-child relationships?
11. **Immutability Edge Cases:** Can published reports be "unpublished"?
12. **Performance Requirements:** Concurrent users, data volume, SLAs?

------

## SUCCESS CRITERIA

**Phase 1 Complete When:**

- All 53 foundation steps executed and validated
- Sanctum authentication working
- View namespaces registered
- Shared systems operational (Notes, Audit, EntityLinks)
- Base policies and RBAC working
- All enums defined
- All tests passing

**Phase 2 Complete When:**

- StatusReports module fully functional across all three interfaces
- API endpoints tested and documented
- Web pages rendering correctly
- Livewire components reactive and working
- Immutability constraints enforced
- Snapshot generation working
- RBAC enforced
- All tests passing
- Pattern established for replication to other 15 modules

**Project Complete When:**

- All 16 modules implemented
- Portfolio Dashboard functional
- All documentation complete
- Test coverage meets requirements
- Performance benchmarks met

------

**Document Version:** 1.1 (Corrected) **Last Updated:** January 28, 2026 **Total Implementation Steps:** 117 (Foundation) + ~900 (Remaining Modules) = ~1,067 steps **Estimated Completion Time:** 45-65 hours for Phase 1 & 2, ~450-650 hours total project

------

## DOCUMENT HISTORY

**Version 1.1 Changes:**

- Added missing getById() method to StatusReportService (Step 69)
- Added Laravel Sanctum installation (Steps 20a-20d)
- Added ModuleViewServiceProvider setup (Steps 50a-50c)
- Corrected Spatie Activity Log trait usage (Step 44)
- Corrected all view path references to use namespace notation
- Increased step count from 110 to 117

**Critical Fixes Applied:**

- Fixed missing service method that would cause Livewire failures
- Added authentication infrastructure required for API
- Implemented view registration so Laravel can find module templates
- Corrected activity logging to use proper Spatie syntax

**Status:** ✅ PRODUCTION READY - All critical issues resolved