# ChangeRequests Module - Implementation Guide

**Module:** ChangeRequests (CONTROL Domain)
**Purpose:** Formal scope and schedule change workflow with hierarchical grouping
**Dependencies:** Project, ChangeRequestStatus, ChangeRequestType enums, HasGroups trait
**Estimated Steps:** 62

---

## Module Overview

ChangeRequests track formal requests to modify project scope, schedule, budget, or resources. They require approval workflow and impact assessment.

### Key Features
- **2-level grouping:** ChangeRequestGroup
- **Type classification:** Scope, Schedule, Budget, Resource, Technical
- **Approval workflow:** Submitted → Under Review → Approved → Rejected → Implemented
- **Impact assessment:** Scope, schedule, budget, resource impacts
- **Approver tracking:** Approval date and approver
- **Implementation tracking:** Implementation date and notes

---

## Database Schema

### Step 1-2: Create Migrations

**change_request_groups table:** Standard

**change_requests table:**
```php
$table->id();
$table->foreignId('project_id')->constrained()->cascadeOnDelete();
$table->foreignId('change_request_group_id')->nullable()->constrained()->nullOnDelete();
$table->string('title');
$table->text('description')->nullable();
$table->string('type', 30); // ChangeRequestType enum
$table->string('status', 20)->default('submitted'); // ChangeRequestStatus enum
$table->text('justification')->nullable();
$table->text('scope_impact')->nullable();
$table->integer('schedule_impact_days')->nullable(); // Delay in days
$table->decimal('budget_impact', 15, 2)->nullable(); // Cost impact
$table->text('resource_impact')->nullable();
$table->date('requested_date');
$table->foreignId('requested_by')->constrained('users');
$table->date('review_date')->nullable();
$table->foreignId('reviewed_by')->nullable()->constrained('users')->nullOnDelete();
$table->date('approval_date')->nullable();
$table->foreignId('approved_by')->nullable()->constrained('users')->nullOnDelete();
$table->text('approval_notes')->nullable();
$table->text('rejection_reason')->nullable();
$table->date('implementation_date')->nullable();
$table->text('implementation_notes')->nullable();
$table->boolean('is_internal_only')->default(false);
// timestamps, soft deletes, indexes
$table->index(['project_id', 'status', 'deleted_at']);
$table->index('type');
$table->index('requested_by');
$table->index('approved_by');
```

### Step 3: Run Migrations

---

## Models

### Step 4-5: Create Models

**ChangeRequestGroup:** Standard

**ChangeRequest:**

**Casts:** All dates, enums, schedule_impact_days (int), budget_impact (decimal)

**Methods:**

```php
public function approve(int $approverId, ?string $approvalNotes = null): void
{
    $this->update([
        'status' => ChangeRequestStatus::APPROVED,
        'approval_date' => now(),
        'approved_by' => $approverId,
        'approval_notes' => $approvalNotes,
        'updated_by' => auth()->id(),
    ]);
}

public function reject(string $rejectionReason): void
{
    $this->update([
        'status' => ChangeRequestStatus::REJECTED,
        'rejection_reason' => $rejectionReason,
        'updated_by' => auth()->id(),
    ]);
}

public function markImplemented(string $implementationNotes): void
{
    $this->update([
        'status' => ChangeRequestStatus::IMPLEMENTED,
        'implementation_date' => now(),
        'implementation_notes' => $implementationNotes,
        'updated_by' => auth()->id(),
    ]);
}

public function getTotalImpactScore(): int
{
    $score = 0;
    if ($this->schedule_impact_days) $score += abs($this->schedule_impact_days);
    if ($this->budget_impact) $score += (int)(abs($this->budget_impact) / 1000);
    return $score;
}
```

---

## DTOs (Steps 6-9)

Include all impact fields, approval workflow fields

---

## Repositories (Steps 10-11)

**ChangeRequestRepository additional methods:**
- `getPendingApproval(projectId)`
- `getApprovedChangeRequests(projectId)`
- `getChangeRequestsByType(projectId, type)`
- `getHighImpactChangeRequests(projectId, impactThreshold)`

---

## Services (Steps 12-13)

**ChangeRequestService key methods:**

```php
public function submitForReview(CreateChangeRequestData $data): ChangeRequest
{
    $cr = $this->repository->create($data);
    event(new ChangeRequestSubmitted($cr));
    return $cr;
}

public function approveChangeRequest(
    int $id,
    int $approverId,
    ?string $approvalNotes = null
): ChangeRequest {
    $cr = $this->repository->findById($id);

    if ($cr->status !== ChangeRequestStatus::UNDER_REVIEW) {
        throw new \DomainException('Can only approve requests under review');
    }

    $cr->approve($approverId, $approvalNotes);
    event(new ChangeRequestApproved($cr));

    return $cr;
}

public function rejectChangeRequest(int $id, string $rejectionReason): ChangeRequest
{
    $cr = $this->repository->findById($id);
    $cr->reject($rejectionReason);
    event(new ChangeRequestRejected($cr));
    return $cr;
}

public function markAsImplemented(int $id, string $implementationNotes): ChangeRequest
{
    $cr = $this->repository->findById($id);

    if ($cr->status !== ChangeRequestStatus::APPROVED) {
        throw new \DomainException('Can only implement approved change requests');
    }

    $cr->markImplemented($implementationNotes);
    event(new ChangeRequestImplemented($cr));

    return $cr;
}
```

---

## Events (Steps 14-18)

- `ChangeRequestSubmitted`
- `ChangeRequestApproved`
- `ChangeRequestRejected`
- `ChangeRequestImplemented`
- `HighImpactChangeRequestSubmitted`

---

## Policies (Steps 19-21)

**Additional Policy Methods:**

```php
public function approve(User $user, ChangeRequest $cr): bool
{
    // Only admins can approve
    return $this->isAdmin($user);
}

public function markImplemented(User $user, ChangeRequest $cr): bool
{
    // Only admins or editors on project can mark implemented
    if ($this->isAdmin($user)) return true;
    if ($this->isEditor($user) && $this->canEdit($user, $cr)) return true;
    return false;
}
```

---

## API Interface (Steps 22-33)

Resources, Controllers, FormRequests, Routes

**Additional Endpoints:**
```php
Route::post('change-requests/{changeRequest}/approve', [ChangeRequestController::class, 'approve']);
Route::post('change-requests/{changeRequest}/reject', [ChangeRequestController::class, 'reject']);
Route::post('change-requests/{changeRequest}/implement', [ChangeRequestController::class, 'markImplemented']);
Route::get('projects/{project}/change-requests/pending-approval', [ChangeRequestController::class, 'pendingApproval']);
```

---

## Web Interface (Steps 34-55)

Views include:
- `index.blade.php`
- `approval-queue.blade.php` (pending approvals for admins)
- `impact-analysis.blade.php` (visual impact dashboard)
- Standard CRUD views

ViewModels:
- `ChangeRequestApprovalQueueViewModel`
- `ChangeRequestImpactAnalysisViewModel`
- Standard ViewModels

---

## Livewire Components (Steps 56-59)

- `ChangeRequestForm` - Impact assessment fields
- `ChangeRequestApprovalCard` - Approve/Reject actions
- `ImpactSummary` - Visual impact indicators

---

## Testing (Steps 60-62)

- Approval workflow
- Rejection workflow
- Implementation workflow
- Impact score calculation
- Policy tests for approval authorization

---

**Implementation Ready:** ✅
**Next Module:** Decisions
