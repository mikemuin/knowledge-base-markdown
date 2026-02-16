# Deliverables Module - Implementation Guide

**Module:** Deliverables (EXECUTION Domain)
**Purpose:** Tangible outputs and sign-off tracking with hierarchical grouping
**Dependencies:** Project, UserRole enum, DeliverableType enum, ExecutionStatus enum, HasGroups trait
**Estimated Steps:** 60

---

## Module Overview

Deliverables represent tangible project outputs requiring formal sign-off. They track production status, acceptance criteria, and stakeholder approvals.

### Key Features
- **2-level grouping:** DeliverableGroup (parent/child structure)
- **Type classification:** Document, Software, Hardware, Service, Other
- **Sign-off workflow:** Draft → In Progress → Review → Accepted → Rejected
- **Acceptance tracking:** Formal acceptance date and approver
- **Version control:** Track deliverable versions
- **Internal visibility:** Control exposure via `is_internal_only` flag

### Business Rules
- Groups enforce maximum 2-level hierarchy
- Target week auto-computed from target_delivery_date
- Accepted deliverables require acceptance_date and accepted_by
- Rejected deliverables require rejection_reason
- Soft deletes only

---

## Database Schema

### Step 1: Create deliverable_groups Migration

```php
Schema::create('deliverable_groups', function (Blueprint $table) {
    $table->id();
    $table->foreignId('project_id')->constrained()->cascadeOnDelete();
    $table->foreignId('parent_id')->nullable()->constrained('deliverable_groups')->nullOnDelete();
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

### Step 2: Create deliverables Migration

```php
Schema::create('deliverables', function (Blueprint $table) {
    $table->id();
    $table->foreignId('project_id')->constrained()->cascadeOnDelete();
    $table->foreignId('deliverable_group_id')->nullable()->constrained()->nullOnDelete();
    $table->string('title');
    $table->text('description')->nullable();
    $table->string('type', 20); // DeliverableType enum
    $table->text('acceptance_criteria')->nullable();
    $table->date('target_delivery_date');
    $table->string('target_week', 8);
    $table->date('actual_delivery_date')->nullable();
    $table->string('execution_status', 20)->default('draft'); // ExecutionStatus enum
    $table->date('acceptance_date')->nullable();
    $table->foreignId('accepted_by')->nullable()->constrained('users')->nullOnDelete();
    $table->text('rejection_reason')->nullable();
    $table->string('version', 50)->nullable();
    $table->foreignId('owner_id')->nullable()->constrained('users')->nullOnDelete();
    $table->boolean('is_internal_only')->default(false);
    $table->foreignId('created_by')->constrained('users');
    $table->foreignId('updated_by')->nullable()->constrained('users');
    $table->timestamps();
    $table->softDeletes();

    $table->index(['project_id', 'execution_status', 'deleted_at']);
    $table->index(['project_id', 'target_week']);
    $table->index('type');
    $table->index('owner_id');
    $table->index('accepted_by');
});
```

### Step 3: Run Migrations

---

## Models

### Step 4: Create DeliverableGroup Model

**Traits:** HasFactory, SoftDeletes, HasGroups, LogsActivity

### Step 5: Create Deliverable Model

**Traits:** HasFactory, SoftDeletes, HasInternalVisibility, LogsActivity

**Casts:**
- `target_delivery_date` → date
- `actual_delivery_date` → date
- `acceptance_date` → date
- `type` → DeliverableType enum
- `execution_status` → ExecutionStatus enum
- `is_internal_only` → boolean

**Methods:**

```php
public function accept(int $approverId, Carbon $acceptanceDate): void
{
    $this->update([
        'execution_status' => ExecutionStatus::COMPLETED,
        'acceptance_date' => $acceptanceDate,
        'accepted_by' => $approverId,
        'rejection_reason' => null,
        'updated_by' => auth()->id(),
    ]);
}

public function reject(string $reason): void
{
    $this->update([
        'execution_status' => ExecutionStatus::CANCELLED,
        'rejection_reason' => $reason,
        'acceptance_date' => null,
        'accepted_by' => null,
        'updated_by' => auth()->id(),
    ]);
}

public function isAccepted(): bool
{
    return $this->acceptance_date !== null && $this->accepted_by !== null;
}
```

### Step 6: Create Model Observer

Auto-compute target_week from target_delivery_date

---

## DTOs

### Step 7-10: Create DTOs

- `CreateDeliverableGroupData`
- `UpdateDeliverableGroupData`
- `CreateDeliverableData` (includes acceptance_criteria, version)
- `UpdateDeliverableData`

---

## Repositories

### Step 11: DeliverableGroupRepository

Standard methods

### Step 12: DeliverableRepository

**Additional Methods:**
- `getDeliverablesByType(projectId, type)`
- `getPendingAcceptance(projectId)`
- `getAcceptedDeliverables(projectId)`
- `getOverdueDeliverables(projectId)`

---

## Services

### Step 13: DeliverableGroupService

Standard CRUD with hierarchy validation

### Step 14: DeliverableService

**Key Methods:**

```php
public function submitForReview(int $id): Deliverable
{
    $deliverable = $this->repository->findById($id);

    if ($deliverable->execution_status !== ExecutionStatus::IN_PROGRESS) {
        throw new \DomainException('Only in-progress deliverables can be submitted for review');
    }

    $deliverable->update([
        'execution_status' => ExecutionStatus::UNDER_REVIEW,
        'actual_delivery_date' => now(),
        'updated_by' => auth()->id(),
    ]);

    event(new DeliverableSubmittedForReview($deliverable));
    return $deliverable;
}

public function acceptDeliverable(int $id, int $approverId, ?Carbon $acceptanceDate = null): Deliverable
{
    $deliverable = $this->repository->findById($id);
    $acceptanceDate = $acceptanceDate ?? now();

    $deliverable->accept($approverId, $acceptanceDate);
    event(new DeliverableAccepted($deliverable));

    return $deliverable;
}

public function rejectDeliverable(int $id, string $reason): Deliverable
{
    $deliverable = $this->repository->findById($id);

    $deliverable->reject($reason);
    event(new DeliverableRejected($deliverable));

    return $deliverable;
}
```

---

## Events

### Step 15-19: Create Events

- `DeliverableCreated`
- `DeliverableSubmittedForReview`
- `DeliverableAccepted`
- `DeliverableRejected`
- `DeliverableOverdue`

---

## Policies

### Step 20-21: Create and Register Policies

- `DeliverableGroupPolicy`
- `DeliverablePolicy` (with accept/reject authorization)

**Additional Policy Method:**

```php
public function accept(User $user, Deliverable $deliverable): bool
{
    // Only admins and editors on the project can accept
    if ($this->isAdmin($user)) return true;
    if ($this->isEditor($user) && $this->canEdit($user, $deliverable)) return true;
    return false;
}
```

---

## API Interface

### Step 22-23: Create Resources

- `DeliverableGroupResource`
- `DeliverableResource` (include acceptance details, approver info)

### Step 24: DeliverableController (API)

**Additional Endpoints:**

```php
public function submitForReview(Deliverable $deliverable)
{
    $this->authorize('update', $deliverable);
    $deliverable = $this->service->submitForReview($deliverable->id);
    return new DeliverableResource($deliverable);
}

public function accept(Deliverable $deliverable, AcceptDeliverableRequest $request)
{
    $this->authorize('accept', $deliverable);

    $deliverable = $this->service->acceptDeliverable(
        $deliverable->id,
        auth()->id(),
        $request->input('acceptance_date') ? Carbon::parse($request->input('acceptance_date')) : null
    );

    return new DeliverableResource($deliverable);
}

public function reject(Deliverable $deliverable, RejectDeliverableRequest $request)
{
    $this->authorize('accept', $deliverable); // Same permission as accept
    $deliverable = $this->service->rejectDeliverable($deliverable->id, $request->input('reason'));
    return new DeliverableResource($deliverable);
}
```

### Step 25: DeliverableGroupController (API)

### Step 26-31: Create FormRequests (API)

- `StoreDeliverableGroupRequest`
- `UpdateDeliverableGroupRequest`
- `StoreDeliverableRequest`
- `UpdateDeliverableRequest`
- `AcceptDeliverableRequest`
- `RejectDeliverableRequest`

### Step 32: Register API Routes

```php
Route::post('deliverables/{deliverable}/submit-for-review', [DeliverableController::class, 'submitForReview']);
Route::post('deliverables/{deliverable}/accept', [DeliverableController::class, 'accept']);
Route::post('deliverables/{deliverable}/reject', [DeliverableController::class, 'reject']);
```

---

## Web Interface

### Step 33-34: Create Web Controllers

- `DeliverableController` (Web)
- `DeliverableGroupController` (Web)

**Additional Web Method:**

```php
public function approvalQueue(Project $project)
{
    $this->authorize('view', $project);

    $viewModel = new DeliverableApprovalQueueViewModel(
        $project,
        $this->service
    );

    return view('deliverables::approval-queue', $viewModel->toArray());
}
```

### Step 35-41: Create Views

- `index.blade.php`
- `approval-queue.blade.php` (pending sign-offs)
- `create.blade.php`
- `edit.blade.php`
- `show.blade.php` (with acceptance history)
- `groups/manage.blade.php`
- `partials/acceptance-form.blade.php`

### Step 42-46: Create ViewModels

- `DeliverableIndexViewModel`
- `DeliverableApprovalQueueViewModel`
- `DeliverableFormViewModel`
- `DeliverableDetailViewModel`
- `DeliverableAcceptanceViewModel`

### Step 47-51: Create FormRequests (Web)

Same as API

### Step 52: Register Web Routes

```php
Route::get('projects/{project}/deliverables/approval-queue', [DeliverableController::class, 'approvalQueue'])
    ->name('projects.deliverables.approval-queue');
Route::post('deliverables/{deliverable}/submit-for-review', [DeliverableController::class, 'submitForReview'])
    ->name('deliverables.submit-for-review');
Route::post('deliverables/{deliverable}/accept', [DeliverableController::class, 'accept'])
    ->name('deliverables.accept');
Route::post('deliverables/{deliverable}/reject', [DeliverableController::class, 'reject'])
    ->name('deliverables.reject');
```

---

## Livewire Components

### Step 53: DeliverableForm Component

**Features:** Type selector, acceptance criteria editor, version input

### Step 54: DeliverableApprovalCard Component

**Purpose:** Display deliverable with approve/reject actions

### Step 55: DeliverableTimeline Component

**Purpose:** Visual timeline of deliverable lifecycle

### Step 56-58: Create Livewire Views

- `deliverable-form.blade.php`
- `deliverable-approval-card.blade.php`
- `deliverable-timeline.blade.php`

---

## Testing

### Step 59: DeliverableServiceTest

**Test Coverage:**
- Submit for review workflow
- Accept deliverable
- Reject deliverable with reason
- State transition validation

### Step 60: DeliverablePolicyTest

**Test Coverage:**
- Accept/reject authorization
- Standard RBAC rules

---

## Completion Checklist

- [ ] All migrations run
- [ ] Sign-off workflow functional
- [ ] Acceptance/rejection tracked
- [ ] Approval queue view working
- [ ] All tests passing (minimum 40 tests)

---

**Implementation Ready:** âœ…
**Next Module:** Dependencies
