# ActionItems Module - Implementation Guide

**Module:** ActionItems (EXECUTION Domain)
**Purpose:** High-level directives and task tracking with hierarchical grouping
**Dependencies:** Project, UserRole enum, Priority enum, ExecutionStatus enum, HasGroups trait
**Estimated Steps:** 58

---

## Module Overview

ActionItems represent high-level strategic tasks and directives that drive project execution. Unlike granular task management systems, these are executive-level actions requiring tracking and accountability.

### Key Features
- **2-level grouping:** AitemGroup (parent/child structure)
- **Priority tracking:** Low, Medium, High, Critical
- **Execution status:** Draft → In Progress → Completed → Cancelled
- **Assignee accountability:** Single owner per action item
- **Target week tracking:** ISO 8601 week-based scheduling
- **Internal visibility:** Control exposure via `is_internal_only` flag

### Business Rules
- Groups enforce maximum 2-level hierarchy (validated in service layer)
- Target week auto-computed from target_date
- Completed items require `actual_completion_date`
- Soft deletes only (no hard deletes)
- Changes logged via Spatie activity log

---

## Database Schema

### Step 1: Create aitem_groups Migration

**File:** `database/migrations/YYYY_MM_DD_000001_create_aitem_groups_table.php`

```php
Schema::create('aitem_groups', function (Blueprint $table) {
    $table->id();
    $table->foreignId('project_id')->constrained()->cascadeOnDelete();
    $table->foreignId('parent_id')->nullable()->constrained('aitem_groups')->nullOnDelete();
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
});
```

### Step 2: Create action_items Migration

**File:** `database/migrations/YYYY_MM_DD_000002_create_action_items_table.php`

```php
Schema::create('action_items', function (Blueprint $table) {
    $table->id();
    $table->foreignId('project_id')->constrained()->cascadeOnDelete();
    $table->foreignId('aitem_group_id')->nullable()->constrained()->nullOnDelete();
    $table->string('title');
    $table->text('description')->nullable();
    $table->date('target_date');
    $table->string('target_week', 8); // "2026-W04"
    $table->date('actual_completion_date')->nullable();
    $table->string('priority', 20); // Priority enum
    $table->string('execution_status', 20)->default('draft'); // ExecutionStatus enum
    $table->foreignId('assigned_to')->nullable()->constrained('users')->nullOnDelete();
    $table->boolean('is_internal_only')->default(false);
    $table->foreignId('created_by')->constrained('users');
    $table->foreignId('updated_by')->nullable()->constrained('users');
    $table->timestamps();
    $table->softDeletes();

    $table->index(['project_id', 'execution_status', 'deleted_at']);
    $table->index(['project_id', 'target_week']);
    $table->index('priority');
    $table->index('assigned_to');
    $table->index('is_internal_only');
});
```

### Step 3: Run Migrations

```bash
php artisan migrate
```

---

## Models

### Step 4: Create AitemGroup Model

**File:** `app/Modules/ActionItems/Models/AitemGroup.php`

**Traits:** HasFactory, SoftDeletes, HasGroups, LogsActivity
**Relationships:** project, parent, children, actionItems, creator, updater
**Activity Log:** Log all changes

### Step 5: Create ActionItem Model

**File:** `app/Modules/ActionItems/Models/ActionItem.php`

**Traits:** HasFactory, SoftDeletes, HasInternalVisibility, LogsActivity
**Casts:**
- `target_date` → date
- `actual_completion_date` → date
- `priority` → Priority enum
- `execution_status` → ExecutionStatus enum
- `is_internal_only` → boolean

**Relationships:** project, aitemGroup, assignedTo, creator, updater

**Methods:**

```php
public function isOverdue(): bool
{
    return $this->execution_status !== ExecutionStatus::COMPLETED
        && $this->target_date < now()->toDateString();
}

public function markComplete(Carbon $completionDate): void
{
    $this->update([
        'execution_status' => ExecutionStatus::COMPLETED,
        'actual_completion_date' => $completionDate,
        'updated_by' => auth()->id(),
    ]);
}
```

### Step 6: Create Model Observer

**File:** `app/Modules/ActionItems/Observers/ActionItemObserver.php`

```php
public function saving(ActionItem $actionItem): void
{
    if ($actionItem->isDirty('target_date') && $actionItem->target_date) {
        $actionItem->target_week = app(WeekCalculatorService::class)
            ->computeTargetWeek($actionItem->target_date);
    }
}
```

Register in `AppServiceProvider::boot()`

---

## DTOs

### Step 7: CreateAitemGroupData

**File:** `app/Modules/ActionItems/DTOs/CreateAitemGroupData.php`

**Properties:** project_id, parent_id, name, description, display_order, is_internal_only, created_by

### Step 8: UpdateAitemGroupData

**File:** `app/Modules/ActionItems/DTOs/UpdateAitemGroupData.php`

**Properties:** parent_id, name, description, display_order, is_internal_only, updated_by

### Step 9: CreateActionItemData

**File:** `app/Modules/ActionItems/DTOs/CreateActionItemData.php`

**Properties:** project_id, aitem_group_id, title, description, target_date, priority, execution_status, assigned_to, is_internal_only, created_by

### Step 10: UpdateActionItemData

**File:** `app/Modules/ActionItems/DTOs/UpdateActionItemData.php`

**Properties:** aitem_group_id, title, description, target_date, actual_completion_date, priority, execution_status, assigned_to, is_internal_only, updated_by

---

## Repositories

### Step 11: AitemGroupRepository

**File:** `app/Modules/ActionItems/Repositories/AitemGroupRepository.php`

**Methods:**
- `getGroupsForProject(projectId)`
- `getTopLevelGroups(projectId)`
- `getChildGroups(parentId)`
- `create(CreateAitemGroupData)`
- `update(id, UpdateAitemGroupData)`
- `delete(id)`

### Step 12: ActionItemRepository

**File:** `app/Modules/ActionItems/Repositories/ActionItemRepository.php`

**Methods:**
- `getActionItemsForProject(projectId, filters = [])`
- `getActionItemsByGroup(groupId)`
- `getOverdueActionItems(projectId)`
- `getActionItemsByAssignee(userId, projectId = null)`
- `getUpcomingActionItems(projectId, weekRange = 4)`
- `findById(id)`
- `create(CreateActionItemData)`
- `update(id, UpdateActionItemData)`
- `delete(id)`

---

## Services

### Step 13: AitemGroupService

**File:** `app/Modules/ActionItems/Services/AitemGroupService.php`

Standard CRUD with hierarchy validation

### Step 14: ActionItemService

**File:** `app/Modules/ActionItems/Services/ActionItemService.php`

**Key Methods:**

```php
public function createActionItem(CreateActionItemData $data): ActionItem
{
    $actionItem = $this->repository->create($data);
    event(new ActionItemCreated($actionItem));
    return $actionItem;
}

public function completeActionItem(int $id, ?Carbon $completionDate = null): ActionItem
{
    $actionItem = $this->repository->findById($id);
    $completionDate = $completionDate ?? now();

    $actionItem->markComplete($completionDate);
    event(new ActionItemCompleted($actionItem));

    return $actionItem;
}

public function getMyActionItems(int $userId, ?int $projectId = null): Collection
{
    return $this->repository->getActionItemsByAssignee($userId, $projectId)
        ->where('execution_status', '!=', ExecutionStatus::COMPLETED)
        ->sortBy('target_date');
}

public function getOverdueItems(int $projectId): Collection
{
    return $this->repository->getOverdueActionItems($projectId);
}
```

---

## Events

### Step 15-18: Create Events

- `ActionItemCreated`
- `ActionItemAssigned`
- `ActionItemCompleted`
- `ActionItemOverdue` (dispatched via scheduled job)

**Location:** `app/Modules/ActionItems/Events/`

---

## Policies

### Step 19: AitemGroupPolicy

**File:** `app/Modules/ActionItems/Policies/AitemGroupPolicy.php`

Extends BasePolicy

### Step 20: ActionItemPolicy

**File:** `app/Modules/ActionItems/Policies/ActionItemPolicy.php`

Extends BasePolicy with additional rule:

```php
public function updateOwn(User $user, ActionItem $actionItem): bool
{
    // Assigned users can update their own action items
    return $actionItem->assigned_to === $user->id;
}
```

### Step 21: Register Policies

Add to `AppServiceProvider::registerPolicies()`

---

## API Interface

### Step 22: ActionItemResource

**File:** `app/Modules/ActionItems/Resources/ActionItemResource.php`

```php
return [
    'id' => $this->id,
    'title' => $this->title,
    'description' => $this->description,
    'target_date' => $this->target_date?->format('Y-m-d'),
    'target_week_display' => $this->target_week
        ? app(WeekCalculatorService::class)->formatWeekForDisplay($this->target_week)
        : null,
    'actual_completion_date' => $this->actual_completion_date?->format('Y-m-d'),
    'priority' => [
        'value' => $this->priority->value,
        'label' => $this->priority->label(),
    ],
    'execution_status' => [
        'value' => $this->execution_status->value,
        'label' => $this->execution_status->label(),
    ],
    'is_overdue' => $this->isOverdue(),
    'assigned_to' => $this->assignedTo ? [
        'id' => $this->assignedTo->id,
        'name' => $this->assignedTo->name,
    ] : null,
    'group' => $this->aitemGroup ? [
        'id' => $this->aitemGroup->id,
        'name' => $this->aitemGroup->name,
    ] : null,
    'is_internal_only' => $this->is_internal_only,
];
```

### Step 23: AitemGroupResource

**File:** `app/Modules/ActionItems/Resources/AitemGroupResource.php`

### Step 24: ActionItemController (API)

**File:** `app/Modules/ActionItems/Controllers/Api/ActionItemController.php`

**Additional Endpoints:**

```php
public function complete(ActionItem $actionItem, CompleteActionItemRequest $request)
{
    $this->authorize('update', $actionItem);

    $completionDate = $request->input('completion_date')
        ? Carbon::parse($request->input('completion_date'))
        : null;

    $actionItem = $this->service->completeActionItem($actionItem->id, $completionDate);

    return new ActionItemResource($actionItem);
}

public function myActionItems(Request $request)
{
    $projectId = $request->query('project_id');
    $actionItems = $this->service->getMyActionItems(auth()->id(), $projectId);

    return ActionItemResource::collection($actionItems);
}
```

### Step 25: AitemGroupController (API)

**File:** `app/Modules/ActionItems/Controllers/Api/AitemGroupController.php`

### Step 26-29: Create FormRequests (API)

- `StoreAitemGroupRequest`
- `UpdateAitemGroupRequest`
- `StoreActionItemRequest`
- `UpdateActionItemRequest`
- `CompleteActionItemRequest`

**Location:** `app/Modules/ActionItems/FormRequests/Api/`

### Step 30: Register API Routes

**File:** `routes/api.php`

```php
Route::middleware('auth:sanctum')->group(function () {
    Route::apiResource('projects.aitem-groups', AitemGroupController::class);
    Route::apiResource('aitem-groups', AitemGroupController::class)->except(['index', 'store']);

    Route::get('action-items/my-items', [ActionItemController::class, 'myActionItems']);
    Route::apiResource('projects.action-items', ActionItemController::class);
    Route::apiResource('action-items', ActionItemController::class)->except(['index', 'store']);
    Route::post('action-items/{actionItem}/complete', [ActionItemController::class, 'complete']);
});
```

---

## Web Interface

### Step 31: ActionItemController (Web)

**File:** `app/Modules/ActionItems/Controllers/Web/ActionItemController.php`

**Additional Method:**

```php
public function myItems()
{
    $viewModel = new MyActionItemsViewModel(
        $this->service,
        auth()->user()
    );

    return view('action-items::my-items', $viewModel->toArray());
}
```

### Step 32: AitemGroupController (Web)

**File:** `app/Modules/ActionItems/Controllers/Web/AitemGroupController.php`

### Step 33-38: Create Views

**Location:** `app/Modules/ActionItems/Views/`

- `index.blade.php` - Grouped list with filters
- `my-items.blade.php` - Personal action item dashboard
- `create.blade.php` - Creation form
- `edit.blade.php` - Edit form
- `show.blade.php` - Detail view
- `groups/manage.blade.php` - Group management

### Step 39-42: Create ViewModels

- `ActionItemIndexViewModel`
- `MyActionItemsViewModel`
- `ActionItemFormViewModel`
- `ActionItemDetailViewModel`

**Location:** `app/Modules/ActionItems/ViewModels/`

### Step 43-46: Create FormRequests (Web)

Same as API FormRequests

**Location:** `app/Modules/ActionItems/FormRequests/Web/`

### Step 47: Register Web Routes

**File:** `routes/web.php`

```php
Route::middleware('auth')->group(function () {
    Route::get('action-items/my-items', [ActionItemController::class, 'myItems'])
        ->name('action-items.my-items');
    Route::resource('projects.action-items', ActionItemController::class);
    Route::resource('projects.aitem-groups', AitemGroupController::class);
});
```

---

## Livewire Components

### Step 48: ActionItemForm Component

**File:** `app/Modules/ActionItems/Livewire/ActionItemForm.php`

**Key Features:**
- Group selection (2-level)
- Priority dropdown with color coding
- Assignee selection (project team members)
- Target date with week preview

### Step 49: MyActionItemsList Component

**File:** `app/Modules/ActionItems/Livewire/MyActionItemsList.php`

**Features:**
- Personal dashboard
- Filter by project, priority, status
- Quick-complete button
- Overdue highlighting

### Step 50: ActionItemCard Component

**File:** `app/Modules/ActionItems/Livewire/ActionItemCard.php`

**Purpose:** Reusable card for dashboards and lists

### Step 51: ActionItemKanban Component

**File:** `app/Modules/ActionItems/Livewire/ActionItemKanban.php`

**Features:** Drag-and-drop board (Draft | In Progress | Completed)

### Step 52-55: Create Livewire Views

**Location:** `app/Modules/ActionItems/Livewire/views/`

- `action-item-form.blade.php`
- `my-action-items-list.blade.php`
- `action-item-card.blade.php`
- `action-item-kanban.blade.php`

---

## Scheduled Jobs

### Step 56: ActionItemOverdueNotification Job

**File:** `app/Modules/ActionItems/Jobs/ActionItemOverdueNotification.php`

**Purpose:** Daily job to identify and notify about overdue action items

**Schedule:** Add to `routes/console.php`

```php
Schedule::job(new ActionItemOverdueNotification)->daily();
```

---

## Testing

### Step 57: ActionItemServiceTest

**Location:** `tests/Unit/Modules/ActionItems/Services/ActionItemServiceTest.php`

**Test Coverage:**
- Create action item
- Auto-compute target_week
- Complete action item
- Get overdue items
- Get my action items

### Step 58: ActionItemPolicyTest

**Location:** `tests/Unit/Modules/ActionItems/Policies/ActionItemPolicyTest.php`

**Test Coverage:**
- Standard RBAC rules
- Assigned user can update own items
- Internal visibility filtering

---

## Completion Checklist

- [ ] All migrations run successfully
- [ ] Models have correct relationships
- [ ] Observer auto-computes target_week
- [ ] Services enforce business rules
- [ ] Policies registered and tested
- [ ] API endpoints functional
- [ ] Web views render correctly
- [ ] Livewire components reactive
- [ ] Scheduled job registered
- [ ] All tests passing (minimum 35 tests)
- [ ] Code formatted with Laravel Pint

---

**Implementation Ready:** âœ…
**Next Module:** Deliverables
