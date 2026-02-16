# Dependencies Module - Implementation Guide

**Module:** Dependencies (EXECUTION Domain)
**Purpose:** Track blockers and external constraints with hierarchical grouping
**Dependencies:** Project, DependencyType enum, DependencyStatus enum, HasGroups trait
**Estimated Steps:** 58

---

## Module Overview

Dependencies track blocking relationships and external constraints that impact project execution. They support both internal (within project) and external (outside project control) dependencies.

### Key Features
- **2-level grouping:** DependencyGroup
- **Type classification:** Internal, External, Vendor, Technical, Business
- **Status tracking:** Open, In Progress, Resolved, Blocked
- **Resolution tracking:** Resolution date and notes
- **Owner accountability:** Single owner per dependency

---

## Database Schema

### Step 1-2: Create Migrations

**dependency_groups table:**
```php
$table->id();
$table->foreignId('project_id')->constrained()->cascadeOnDelete();
$table->foreignId('parent_id')->nullable()->constrained('dependency_groups')->nullOnDelete();
$table->string('name');
$table->text('description')->nullable();
$table->integer('display_order')->default(0);
$table->boolean('is_internal_only')->default(false);
// timestamps, soft deletes, indexes
```

**dependencies table:**
```php
$table->id();
$table->foreignId('project_id')->constrained()->cascadeOnDelete();
$table->foreignId('dependency_group_id')->nullable()->constrained()->nullOnDelete();
$table->string('title');
$table->text('description')->nullable();
$table->string('type', 20); // DependencyType enum
$table->string('status', 20)->default('open'); // DependencyStatus enum
$table->date('identified_date');
$table->date('required_by_date');
$table->string('required_by_week', 8); // Auto-computed
$table->date('resolution_date')->nullable();
$table->text('resolution_notes')->nullable();
$table->foreignId('owner_id')->nullable()->constrained('users')->nullOnDelete();
$table->string('external_party', 255)->nullable(); // For external dependencies
$table->boolean('is_critical')->default(false);
$table->boolean('is_internal_only')->default(false);
// timestamps, soft deletes, indexes
```

### Step 3: Run Migrations

---

## Models

### Step 4-5: Create Models

**DependencyGroup:** Standard group model with HasGroups trait

**Dependency:**

**Casts:**
- `identified_date` → date
- `required_by_date` → date
- `resolution_date` → date
- `type` → DependencyType enum
- `status` → DependencyStatus enum
- `is_critical` → boolean

**Methods:**

```php
public function resolve(string $notes, ?Carbon $resolutionDate = null): void
{
    $this->update([
        'status' => DependencyStatus::RESOLVED,
        'resolution_date' => $resolutionDate ?? now(),
        'resolution_notes' => $notes,
        'updated_by' => auth()->id(),
    ]);
}

public function block(string $reason): void
{
    $this->update([
        'status' => DependencyStatus::BLOCKED,
        'resolution_notes' => $reason,
        'updated_by' => auth()->id(),
    ]);
}

public function isOverdue(): bool
{
    return $this->status !== DependencyStatus::RESOLVED
        && $this->required_by_date < now()->toDateString();
}
```

### Step 6: Create Observer

Auto-compute `required_by_week` from `required_by_date`

---

## DTOs

### Step 7-10: Create DTOs

- `CreateDependencyGroupData`
- `UpdateDependencyGroupData`
- `CreateDependencyData`
- `UpdateDependencyData`

---

## Repositories

### Step 11-12: Create Repositories

**DependencyRepository additional methods:**
- `getOpenDependencies(projectId)`
- `getCriticalDependencies(projectId)`
- `getOverdueDependencies(projectId)`
- `getDependenciesByType(projectId, type)`
- `getDependenciesByStatus(projectId, status)`

---

## Services

### Step 13-14: Create Services

**DependencyService key methods:**

```php
public function resolveDependency(int $id, string $notes, ?Carbon $resolutionDate = null): Dependency
{
    $dependency = $this->repository->findById($id);
    $dependency->resolve($notes, $resolutionDate);
    event(new DependencyResolved($dependency));
    return $dependency;
}

public function blockDependency(int $id, string $reason): Dependency
{
    $dependency = $this->repository->findById($id);
    $dependency->block($reason);
    event(new DependencyBlocked($dependency));
    return $dependency;
}

public function getCriticalPath(int $projectId): Collection
{
    return $this->repository->getCriticalDependencies($projectId)
        ->where('status', '!=', DependencyStatus::RESOLVED)
        ->sortBy('required_by_date');
}
```

---

## Events

### Step 15-18: Create Events

- `DependencyCreated`
- `DependencyResolved`
- `DependencyBlocked`
- `DependencyOverdue`

---

## Policies

### Step 19-21: Create and Register Policies

Standard RBAC with BasePolicy

---

## API Interface

### Step 22-23: Create Resources

Include resolution details, owner info, overdue status

### Step 24-25: Create API Controllers

**Additional Endpoints:**

```php
// DependencyController
public function resolve(Dependency $dependency, ResolveDependencyRequest $request)
{
    $this->authorize('update', $dependency);

    $dependency = $this->service->resolveDependency(
        $dependency->id,
        $request->input('resolution_notes'),
        $request->input('resolution_date') ? Carbon::parse($request->input('resolution_date')) : null
    );

    return new DependencyResource($dependency);
}

public function block(Dependency $dependency, BlockDependencyRequest $request)
{
    $this->authorize('update', $dependency);
    $dependency = $this->service->blockDependency($dependency->id, $request->input('reason'));
    return new DependencyResource($dependency);
}
```

### Step 26-30: Create FormRequests (API)

Include `ResolveDependencyRequest` and `BlockDependencyRequest`

### Step 31: Register API Routes

```php
Route::post('dependencies/{dependency}/resolve', [DependencyController::class, 'resolve']);
Route::post('dependencies/{dependency}/block', [DependencyController::class, 'block']);
```

---

## Web Interface

### Step 32-33: Create Web Controllers

**Additional Method:**

```php
public function dashboard(Project $project)
{
    $viewModel = new DependencyDashboardViewModel($project, $this->service);
    return view('dependencies::dashboard', $viewModel->toArray());
}
```

### Step 34-39: Create Views

- `index.blade.php`
- `dashboard.blade.php` (critical path, overdue, by type)
- `create.blade.php`
- `edit.blade.php`
- `show.blade.php` (with resolution history)
- `groups/manage.blade.php`

### Step 40-44: Create ViewModels

- `DependencyIndexViewModel`
- `DependencyDashboardViewModel`
- `DependencyFormViewModel`
- `DependencyDetailViewModel`
- `DependencyResolutionViewModel`

### Step 45-49: Create FormRequests (Web)

### Step 50: Register Web Routes

```php
Route::get('projects/{project}/dependencies/dashboard', [DependencyController::class, 'dashboard'])
    ->name('projects.dependencies.dashboard');
Route::post('dependencies/{dependency}/resolve', [DependencyController::class, 'resolve'])
    ->name('dependencies.resolve');
Route::post('dependencies/{dependency}/block', [DependencyController::class, 'block'])
    ->name('dependencies.block');
```

---

## Livewire Components

### Step 51-53: Create Components

- `DependencyForm` - Type selector, critical flag, external party field
- `DependencyTracker` - Real-time status board
- `DependencyResolutionForm` - Resolution notes and date

### Step 54-56: Create Livewire Views

---

## Testing

### Step 57: DependencyServiceTest

- Resolve dependency workflow
- Block dependency
- Critical path identification
- Overdue detection

### Step 58: DependencyPolicyTest

---

## Completion Checklist

- [ ] All migrations run
- [ ] Resolution workflow functional
- [ ] Critical path tracking works
- [ ] Overdue highlighting functional
- [ ] All tests passing (minimum 35 tests)

---

**Implementation Ready:** âœ…
**Next Module:** MeetingEvents
