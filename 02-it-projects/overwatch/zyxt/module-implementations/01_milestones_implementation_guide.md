# Milestones Module - Implementation Guide

**Module:** Milestones (EXECUTION Domain)
**Purpose:** Stage gates and critical path tracking with hierarchical grouping
**Dependencies:** Project, UserRole enum, RagStatus enum, ExecutionStatus enum, HasGroups trait
**Estimated Steps:** 60

---

## Module Overview

Milestones represent critical stage gates and deliverable checkpoints in project execution. They support 2-level hierarchical grouping and include RAG status tracking, dependencies, and stakeholder accountability.

### Key Features
- **2-level grouping:** MilestoneGroup (parent/child structure)
- **Critical path tracking:** Flag milestones as critical to overall timeline
- **RAG status:** Visual health indicators
- **Execution tracking:** Draft → In Progress → Completed → Cancelled
- **Accountability:** Assigned owner per milestone
- **Internal visibility:** Control exposure via `is_internal_only` flag

### Business Rules
- Groups enforce maximum 2-level hierarchy (validated in service layer)
- Target week auto-computed from target_date
- Soft deletes only (no hard deletes)
- Changes logged via Spatie activity log

---

## Database Schema

### Step 1: Create milestone_groups Migration

**File:** `database/migrations/YYYY_MM_DD_000001_create_milestone_groups_table.php`

```php
Schema::create('milestone_groups', function (Blueprint $table) {
    $table->id();
    $table->foreignId('project_id')->constrained()->cascadeOnDelete();
    $table->foreignId('parent_id')->nullable()->constrained('milestone_groups')->nullOnDelete();
    $table->string('name');
    $table->text('description')->nullable();
    $table->integer('display_order')->default(0);
    $table->boolean('is_internal_only')->default(false);
    $table->foreignId('created_by')->constrained('users');
    $table->foreignId('updated_by')->nullable()->constrained('users');
    $table->timestamps();
    $table->softDeletes();

    $table->index(['project_id', 'parent_id', 'deleted_at']);
    $table->index('display_order');
    $table->index('is_internal_only');
});
```

### Step 2: Create milestones Migration

**File:** `database/migrations/YYYY_MM_DD_000002_create_milestones_table.php`

```php
Schema::create('milestones', function (Blueprint $table) {
    $table->id();
    $table->foreignId('project_id')->constrained()->cascadeOnDelete();
    $table->foreignId('milestone_group_id')->nullable()->constrained()->nullOnDelete();
    $table->string('title');
    $table->text('description')->nullable();
    $table->date('target_date');
    $table->string('target_week', 8); // Auto-computed: "2026-W04"
    $table->date('actual_date')->nullable();
    $table->string('rag_status', 20); // RagStatus enum
    $table->string('execution_status', 20)->default('draft'); // ExecutionStatus enum
    $table->boolean('is_critical')->default(false);
    $table->foreignId('owner_id')->nullable()->constrained('users')->nullOnDelete();
    $table->boolean('is_internal_only')->default(false);
    $table->foreignId('created_by')->constrained('users');
    $table->foreignId('updated_by')->nullable()->constrained('users');
    $table->timestamps();
    $table->softDeletes();

    $table->index(['project_id', 'execution_status', 'deleted_at']);
    $table->index(['project_id', 'target_week']);
    $table->index('rag_status');
    $table->index('is_critical');
    $table->index('owner_id');
    $table->index('is_internal_only');
});
```

### Step 3: Run Migrations

```bash
php artisan migrate
```

---

## Models

### Step 4: Create MilestoneGroup Model

**File:** `app/Modules/Milestones/Models/MilestoneGroup.php`

**Traits:** HasFactory, SoftDeletes, HasGroups, LogsActivity
**Relationships:** project, parent, children, milestones, creator, updater
**Activity Log:** Log all changes

### Step 5: Create Milestone Model

**File:** `app/Modules/Milestones/Models/Milestone.php`

**Traits:** HasFactory, SoftDeletes, HasInternalVisibility, LogsActivity
**Casts:**
- `target_date` → date
- `actual_date` → date
- `rag_status` → RagStatus enum
- `execution_status` → ExecutionStatus enum
- `is_critical` → boolean
- `is_internal_only` → boolean

**Relationships:** project, milestoneGroup, owner, creator, updater

**Observer Pattern:** Auto-compute `target_week` on save when `target_date` changes

### Step 6: Create Model Observer

**File:** `app/Modules/Milestones/Observers/MilestoneObserver.php`

```php
public function saving(Milestone $milestone): void
{
    if ($milestone->isDirty('target_date') && $milestone->target_date) {
        $milestone->target_week = app(WeekCalculatorService::class)
            ->computeTargetWeek($milestone->target_date);
    }
}
```

Register observer in `AppServiceProvider::boot()`

---

## DTOs

### Step 7: CreateMilestoneGroupData

**File:** `app/Modules/Milestones/DTOs/CreateMilestoneGroupData.php`

**Properties:** project_id, parent_id, name, description, display_order, is_internal_only, created_by

### Step 8: UpdateMilestoneGroupData

**File:** `app/Modules/Milestones/DTOs/UpdateMilestoneGroupData.php`

**Properties:** parent_id, name, description, display_order, is_internal_only, updated_by

### Step 9: CreateMilestoneData

**File:** `app/Modules/Milestones/DTOs/CreateMilestoneData.php`

**Properties:** project_id, milestone_group_id, title, description, target_date, rag_status, execution_status, is_critical, owner_id, is_internal_only, created_by

### Step 10: UpdateMilestoneData

**File:** `app/Modules/Milestones/DTOs/UpdateMilestoneData.php`

**Properties:** milestone_group_id, title, description, target_date, actual_date, rag_status, execution_status, is_critical, owner_id, is_internal_only, updated_by

---

## Repositories

### Step 11: MilestoneGroupRepository

**File:** `app/Modules/Milestones/Repositories/MilestoneGroupRepository.php`

**Methods:**
- `getGroupsForProject(projectId, includeChildren = true)`
- `getTopLevelGroups(projectId)`
- `getChildGroups(parentId)`
- `create(CreateMilestoneGroupData)`
- `update(id, UpdateMilestoneGroupData)`
- `delete(id)` // Soft delete
- `validateHierarchy(parentId)` // Ensure max 2 levels

### Step 12: MilestoneRepository

**File:** `app/Modules/Milestones/Repositories/MilestoneRepository.php`

**Methods:**
- `getMilestonesForProject(projectId, filters = [])`
- `getMilestonesByGroup(groupId)`
- `getCriticalMilestones(projectId)`
- `getUpcomingMilestones(projectId, weekRange = 4)`
- `getMilestonesByStatus(projectId, executionStatus)`
- `findById(id)`
- `create(CreateMilestoneData)`
- `update(id, UpdateMilestoneData)`
- `delete(id)` // Soft delete

---

## Services

### Step 13: MilestoneGroupService

**File:** `app/Modules/Milestones/Services/MilestoneGroupService.php`

**Key Methods:**

```php
public function createGroup(CreateMilestoneGroupData $data): MilestoneGroup
{
    // Validate hierarchy if parent_id exists
    if ($data->parent_id) {
        $parent = $this->repository->findById($data->parent_id);
        if ($parent->parent_id) {
            throw new \DomainException('Cannot create 3-level hierarchy');
        }
    }

    $group = $this->repository->create($data);
    event(new MilestoneGroupCreated($group));
    return $group;
}

public function reorderGroups(int $projectId, array $orderMap): void
{
    // $orderMap = ['group_id' => display_order]
    foreach ($orderMap as $groupId => $order) {
        $this->repository->updateDisplayOrder($groupId, $order);
    }
}
```

### Step 14: MilestoneService

**File:** `app/Modules/Milestones/Services/MilestoneService.php`

**Key Methods:**

```php
public function createMilestone(CreateMilestoneData $data): Milestone
{
    $milestone = $this->repository->create($data);
    event(new MilestoneCreated($milestone));
    return $milestone;
}

public function completeMilestone(int $id, Carbon $actualDate): Milestone
{
    $milestone = $this->repository->findById($id);

    $milestone->update([
        'execution_status' => ExecutionStatus::COMPLETED,
        'actual_date' => $actualDate,
        'updated_by' => auth()->id(),
    ]);

    event(new MilestoneCompleted($milestone));
    return $milestone;
}

public function getCriticalPathMilestones(int $projectId): Collection
{
    return $this->repository->getCriticalMilestones($projectId)
        ->sortBy('target_date');
}
```

---

## Events

### Step 15-18: Create Events

- `MilestoneGroupCreated`
- `MilestoneGroupUpdated`
- `MilestoneCreated`
- `MilestoneCompleted`

**Location:** `app/Modules/Milestones/Events/`

---

## Policies

### Step 19: MilestoneGroupPolicy

**File:** `app/Modules/Milestones/Policies/MilestoneGroupPolicy.php`

Extends BasePolicy, implements standard CRUD authorization

### Step 20: MilestonePolicy

**File:** `app/Modules/Milestones/Policies/MilestonePolicy.php`

Extends BasePolicy, implements standard CRUD authorization with `is_internal_only` checks

### Step 21: Register Policies

Add to `AppServiceProvider::registerPolicies()`

---

## API Interface

### Step 22: MilestoneGroupResource

**File:** `app/Modules/Milestones/Resources/MilestoneGroupResource.php`

Transform to JSON including children count, milestone count

### Step 23: MilestoneResource

**File:** `app/Modules/Milestones/Resources/MilestoneResource.php`

Transform with formatted target_week display, owner details, group info

### Step 24: MilestoneGroupController (API)

**File:** `app/Modules/Milestones/Controllers/Api/MilestoneGroupController.php`

**Routes:**
- `GET /api/projects/{project}/milestone-groups`
- `POST /api/projects/{project}/milestone-groups`
- `GET /api/milestone-groups/{group}`
- `PUT /api/milestone-groups/{group}`
- `DELETE /api/milestone-groups/{group}`

### Step 25: MilestoneController (API)

**File:** `app/Modules/Milestones/Controllers/Api/MilestoneController.php`

**Routes:**
- `GET /api/projects/{project}/milestones`
- `POST /api/projects/{project}/milestones`
- `GET /api/milestones/{milestone}`
- `PUT /api/milestones/{milestone}`
- `DELETE /api/milestones/{milestone}`
- `POST /api/milestones/{milestone}/complete`

### Step 26-29: Create FormRequests (API)

- `StoreMilestoneGroupRequest`
- `UpdateMilestoneGroupRequest`
- `StoreMilestoneRequest`
- `UpdateMilestoneRequest`

**Location:** `app/Modules/Milestones/FormRequests/Api/`

### Step 30: Register API Routes

**File:** `routes/api.php`

```php
Route::middleware('auth:sanctum')->group(function () {
    Route::apiResource('projects.milestone-groups', MilestoneGroupController::class);
    Route::apiResource('milestone-groups', MilestoneGroupController::class)->except(['index', 'store']);

    Route::apiResource('projects.milestones', MilestoneController::class);
    Route::apiResource('milestones', MilestoneController::class)->except(['index', 'store']);
    Route::post('milestones/{milestone}/complete', [MilestoneController::class, 'complete']);
});
```

---

## Web Interface

### Step 31: MilestoneGroupController (Web)

**File:** `app/Modules/Milestones/Controllers/Web/MilestoneGroupController.php`

Standard CRUD controller using ViewModels

### Step 32: MilestoneController (Web)

**File:** `app/Modules/Milestones/Controllers/Web/MilestoneController.php`

**Key Method:**

```php
public function dashboard(Project $project)
{
    $this->authorize('view', $project);

    $viewModel = new MilestoneDashboardViewModel(
        $project,
        $this->milestoneService,
        $this->groupService
    );

    return view('milestones::dashboard', $viewModel->toArray());
}
```

### Step 33-38: Create Views

**Location:** `app/Modules/Milestones/Views/`

- `index.blade.php` - Grouped list view with expand/collapse
- `dashboard.blade.php` - Critical path visualization, upcoming milestones
- `create.blade.php` - Creation form
- `edit.blade.php` - Edit form
- `show.blade.php` - Detail view with related deliverables
- `groups/manage.blade.php` - Group hierarchy management

### Step 39-42: Create ViewModels

- `MilestoneIndexViewModel`
- `MilestoneDashboardViewModel`
- `MilestoneFormViewModel`
- `MilestoneDetailViewModel`

**Location:** `app/Modules/Milestones/ViewModels/`

### Step 43-46: Create FormRequests (Web)

- `StoreMilestoneGroupRequest`
- `UpdateMilestoneGroupRequest`
- `StoreMilestoneRequest`
- `UpdateMilestoneRequest`

**Location:** `app/Modules/Milestones/FormRequests/Web/`

### Step 47: Register Web Routes

**File:** `routes/web.php`

```php
Route::middleware('auth')->group(function () {
    Route::resource('projects.milestones', MilestoneController::class);
    Route::get('projects/{project}/milestones-dashboard', [MilestoneController::class, 'dashboard'])
        ->name('projects.milestones.dashboard');
    Route::resource('projects.milestone-groups', MilestoneGroupController::class);
});
```

---

## Livewire Components

### Step 48: MilestoneForm Component

**File:** `app/Modules/Milestones/Livewire/MilestoneForm.php`

**Props:** `?int $milestoneId`, `int $projectId`

**Key Features:**
- Reactive group selection (2-level dropdown)
- Target date picker with auto week calculation preview
- RAG status dropdown with color indicators
- Critical flag toggle
- Owner assignment (users assigned to project)

### Step 49: MilestoneList Component

**File:** `app/Modules/Milestones/Livewire/MilestoneList.php`

**Features:**
- Grouped view with expand/collapse
- Filter by status, RAG, critical flag
- Drag-and-drop reordering within groups
- Inline quick-edit for status/RAG
- Bulk actions (delete, change status)

### Step 50: MilestoneCard Component

**File:** `app/Modules/Milestones/Livewire/MilestoneCard.php`

**Purpose:** Reusable milestone display for dashboards

**Displays:** Title, target week formatted, RAG badge, critical indicator, owner avatar

### Step 51: CriticalPathTimeline Component

**File:** `app/Modules/Milestones/Livewire/CriticalPathTimeline.php`

**Purpose:** Visual timeline of critical milestones

**Features:** Horizontal timeline, status indicators, overdue highlights

### Step 52-55: Create Livewire Views

**Location:** `app/Modules/Milestones/Livewire/views/`

- `milestone-form.blade.php`
- `milestone-list.blade.php`
- `milestone-card.blade.php`
- `critical-path-timeline.blade.php`

---

## Testing

### Step 56: MilestoneGroupServiceTest

**Location:** `tests/Unit/Modules/Milestones/Services/MilestoneGroupServiceTest.php`

**Test Coverage:**
- Create top-level group
- Create child group
- Prevent 3-level hierarchy
- Reorder groups
- Delete group cascades to milestones

### Step 57: MilestoneServiceTest

**Location:** `tests/Unit/Modules/Milestones/Services/MilestoneServiceTest.php`

**Test Coverage:**
- Create milestone
- Auto-compute target_week
- Complete milestone
- Get critical path milestones
- Filter by status/RAG

### Step 58: MilestonePolicyTest

**Location:** `tests/Unit/Modules/Milestones/Policies/MilestonePolicyTest.php`

**Test Coverage:**
- Admin full access
- Editor can CRUD on assigned projects
- Viewer read-only
- Internal visibility filtering

### Step 59: MilestoneApiTest

**Location:** `tests/Feature/Modules/Milestones/Api/MilestoneApiTest.php`

**Test Coverage:**
- CRUD endpoints
- Complete milestone endpoint
- Filter by status, RAG, critical
- Authorization checks
- Validation errors

### Step 60: MilestoneLivewireTest

**Location:** `tests/Feature/Modules/Milestones/Livewire/MilestoneLivewireTest.php`

**Test Coverage:**
- Form component renders
- Create milestone via Livewire
- Update milestone via Livewire
- List component filters
- Drag-and-drop reorder

---

## Notes

### Critical Path Algorithm

The system doesn't auto-calculate critical path. The `is_critical` flag is manually set by the PM to indicate milestones on the critical path.

### Target Week Display

Always transform stored ISO 8601 week (`2026-W04`) to "Week 4 of January" format in views and resources using `WeekCalculatorService::formatWeekForDisplay()`.

### Hierarchy Validation

Enforce 2-level max in service layer, not database constraints. This allows for better error messaging and flexibility.

### Integration Points

Milestones can be:
- Linked from ProjectJournal entries
- Referenced in StatusReports
- Associated with Deliverables
- Dependency targets for other modules

---

## Completion Checklist

- [ ] All migrations run successfully
- [ ] Models have correct relationships and casts
- [ ] Observer auto-computes target_week
- [ ] Services enforce business rules
- [ ] Policies registered and tested
- [ ] API endpoints return correct JSON structure
- [ ] Web views render with proper data
- [ ] Livewire components are reactive
- [ ] All tests passing (minimum 40 tests)
- [ ] Code formatted with Laravel Pint

---

**Implementation Ready:** âœ…
**Next Module:** ActionItems
