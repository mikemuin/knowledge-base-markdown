# Issues Module - Implementation Guide

**Module:** Issues (CONTROL Domain)
**Purpose:** Active problems requiring immediate resolution with hierarchical grouping
**Dependencies:** Project, IssueStatus, IssueImpact, IssueUrgency, IssuePriority enums, HasGroups trait
**Estimated Steps:** 60

---

## Module Overview

Issues track active problems and defects requiring resolution. Unlike Risks (forward-looking), Issues are present realities requiring immediate action.

### Key Features
- **2-level grouping:** IssueGroup
- **Status lifecycle:** Open → Investigating → In Progress → Resolved → Closed
- **Priority matrix:** Auto-calculated from Urgency × Impact
- **Resolution tracking:** Resolution date, notes, resolved_by
- **Escalation:** Track escalated issues
- **SLA tracking:** Target resolution date

---

## Database Schema

### Step 1-2: Create Migrations

**issue_groups table:** Standard

**issues table:**
```php
$table->id();
$table->foreignId('project_id')->constrained()->cascadeOnDelete();
$table->foreignId('issue_group_id')->nullable()->constrained()->nullOnDelete();
$table->string('title');
$table->text('description')->nullable();
$table->string('status', 20)->default('open'); // IssueStatus enum
$table->string('urgency', 20); // IssueUrgency enum
$table->string('impact', 20); // IssueImpact enum
$table->string('priority', 20); // IssuePriority enum (auto-calculated)
$table->date('identified_date');
$table->date('target_resolution_date')->nullable();
$table->date('actual_resolution_date')->nullable();
$table->text('resolution_notes')->nullable();
$table->foreignId('assigned_to')->nullable()->constrained('users')->nullOnDelete();
$table->foreignId('resolved_by')->nullable()->constrained('users')->nullOnDelete();
$table->boolean('is_escalated')->default(false);
$table->text('escalation_notes')->nullable();
$table->boolean('is_internal_only')->default(false);
// timestamps, soft deletes, indexes
$table->index(['project_id', 'status', 'deleted_at']);
$table->index('priority');
$table->index('assigned_to');
$table->index('is_escalated');
```

### Step 3: Run Migrations

---

## Models

### Step 4-5: Create Models

**IssueGroup:** Standard

**Issue:**

**Casts:** All dates, all enums, booleans

**Methods:**

```php
public function calculatePriority(): IssuePriority
{
    // Urgency × Impact matrix
    $matrix = [
        'low' => ['low' => 'low', 'medium' => 'low', 'high' => 'medium', 'critical' => 'high'],
        'medium' => ['low' => 'low', 'medium' => 'medium', 'high' => 'high', 'critical' => 'critical'],
        'high' => ['low' => 'medium', 'medium' => 'high', 'high' => 'critical', 'critical' => 'critical'],
        'critical' => ['low' => 'high', 'medium' => 'critical', 'high' => 'critical', 'critical' => 'critical'],
    ];

    return IssuePriority::from($matrix[$this->urgency->value][$this->impact->value]);
}

public function resolve(int $resolvedBy, string $resolutionNotes): void
{
    $this->update([
        'status' => IssueStatus::RESOLVED,
        'actual_resolution_date' => now(),
        'resolution_notes' => $resolutionNotes,
        'resolved_by' => $resolvedBy,
        'updated_by' => auth()->id(),
    ]);
}

public function close(): void
{
    $this->update([
        'status' => IssueStatus::CLOSED,
        'updated_by' => auth()->id(),
    ]);
}

public function escalate(string $escalationNotes): void
{
    $this->update([
        'is_escalated' => true,
        'escalation_notes' => $escalationNotes,
        'updated_by' => auth()->id(),
    ]);
}

public function isOverdue(): bool
{
    return $this->target_resolution_date
        && $this->status !== IssueStatus::RESOLVED
        && $this->status !== IssueStatus::CLOSED
        && $this->target_resolution_date < now()->toDateString();
}
```

### Step 6: Create Observer

Auto-calculate priority when urgency or impact changes

---

## DTOs (Steps 7-10)

Include escalation fields, resolution tracking

---

## Repositories (Steps 11-12)

**IssueRepository additional methods:**
- `getOpenIssues(projectId)`
- `getEscalatedIssues(projectId)`
- `getIssuesByPriority(projectId, priority)`
- `getOverdueIssues(projectId)`
- `getMyAssignedIssues(userId)`

---

## Services (Steps 13-14)

**IssueService key methods:**

```php
public function resolveIssue(int $id, int $resolvedBy, string $resolutionNotes): Issue
{
    $issue = $this->repository->findById($id);
    $issue->resolve($resolvedBy, $resolutionNotes);
    event(new IssueResolved($issue));
    return $issue;
}

public function escalateIssue(int $id, string $escalationNotes): Issue
{
    $issue = $this->repository->findById($id);
    $issue->escalate($escalationNotes);
    event(new IssueEscalated($issue));
    return $issue;
}

public function getIssueBacklog(int $projectId): Collection
{
    return $this->repository->getOpenIssues($projectId)
        ->sortByDesc(fn($i) => $i->priority->value);
}
```

---

## Events (Steps 15-18)

- `IssueCreated`
- `IssueAssigned`
- `IssueResolved`
- `IssueEscalated`

---

## Policies (Steps 19-21)

Standard RBAC + assignee can update own issues

---

## API Interface (Steps 22-33)

Resources, Controllers with resolve/escalate endpoints, FormRequests, Routes

**Additional Endpoints:**
```php
Route::post('issues/{issue}/resolve', [IssueController::class, 'resolve']);
Route::post('issues/{issue}/escalate', [IssueController::class, 'escalate']);
Route::get('issues/my-issues', [IssueController::class, 'myIssues']);
```

---

## Web Interface (Steps 34-55)

Views include:
- `index.blade.php`
- `backlog.blade.php` (priority-sorted open issues)
- `escalations.blade.php` (escalated issues dashboard)
- Standard CRUD views

ViewModels:
- `IssueBacklogViewModel`
- `IssueEscalationsViewModel`
- Standard ViewModels

---

## Livewire Components (Steps 56-58)

- `IssueForm` - Urgency/Impact with live priority calculation
- `IssueBoard` - Kanban by status
- `IssueResolutionForm`

---

## Testing (Steps 59-60)

- Priority calculation matrix
- Resolve workflow
- Escalate workflow
- Overdue detection
- Policy tests

---

**Implementation Ready:** ✅
**Next Module:** ChangeRequests
