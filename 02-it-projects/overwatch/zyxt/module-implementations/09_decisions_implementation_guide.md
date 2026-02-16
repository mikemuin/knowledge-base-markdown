# Decisions Module - Implementation Guide

**Module:** Decisions (CONTROL Domain)
**Purpose:** Immutable strategic decision log with NO hierarchical grouping
**Dependencies:** Project, DecisionStatus enum
**Estimated Steps:** 52

---

## Module Overview

Decisions are **immutable records** of strategic choices made during the project. Once locked, they cannot be edited or deleted. NO grouping supported.

### Key Features
- **Immutability:** Locked decisions cannot be modified
- **No grouping:** Flat structure only
- **Decision types:** Strategic, Technical, Resource, Process
- **Locking mechanism:** `is_locked` flag prevents changes
- **Outcome tracking:** Track decision outcomes over time

---

## Database Schema

### Step 1: Create Migration

**decisions table:**
```php
$table->id();
$table->foreignId('project_id')->constrained()->cascadeOnDelete();
$table->string('title');
$table->text('context')->nullable(); // Background/situation
$table->text('decision_made'); // The actual decision (required)
$table->text('rationale')->nullable(); // Why this decision
$table->text('alternatives_considered')->nullable();
$table->date('decision_date');
$table->foreignId('decided_by')->constrained('users'); // Decision maker
$table->json('participants')->nullable(); // Array of user IDs
$table->boolean('is_locked')->default(false); // Immutability flag
$table->date('locked_at')->nullable();
$table->foreignId('locked_by')->nullable()->constrained('users')->nullOnDelete();
$table->text('outcome_notes')->nullable(); // Track how decision played out
$table->boolean('is_internal_only')->default(false);
$table->foreignId('created_by')->constrained('users');
$table->foreignId('updated_by')->nullable()->constrained('users');
$table->timestamps();
$table->softDeletes();

$table->index(['project_id', 'is_locked', 'deleted_at']);
$table->index('decision_date');
$table->index('decided_by');
```

### Step 2: Run Migration

---

## Models

### Step 3: Create Decision Model

**NO HasGroups trait** (decisions are flat)

**Casts:** decision_date, locked_at (dates), participants (array), is_locked (boolean)

**Methods:**

```php
public function lock(int $lockedBy): void
{
    if ($this->is_locked) {
        throw new \DomainException('Decision is already locked');
    }

    $this->update([
        'is_locked' => true,
        'locked_at' => now(),
        'locked_by' => $lockedBy,
    ]);
}

public function canBeModified(): bool
{
    return !$this->is_locked;
}

public function addOutcomeNotes(string $notes): void
{
    if ($this->is_locked) {
        // Outcome notes are the ONLY thing that can be added after locking
        $this->update([
            'outcome_notes' => $this->outcome_notes
                ? $this->outcome_notes . "\n\n" . now()->format('Y-m-d') . ": " . $notes
                : now()->format('Y-m-d') . ": " . $notes,
            'updated_by' => auth()->id(),
        ]);
    }
}
```

---

## DTOs (Steps 4-6)

- `CreateDecisionData` - Include participants array
- `UpdateDecisionData` - Can only update if not locked
- `AddOutcomeNotesData`

---

## Repository (Step 7)

**DecisionRepository methods:**
- `getDecisionsForProject(projectId, includeInternal = true)`
- `getLockedDecisions(projectId)`
- `getUnlockedDecisions(projectId)`
- `getRecentDecisions(projectId, days = 90)`

---

## Service (Step 8)

**DecisionService key methods:**

```php
public function createDecision(CreateDecisionData $data): Decision
{
    $decision = $this->repository->create($data);
    event(new DecisionRecorded($decision));
    return $decision;
}

public function updateDecision(int $id, UpdateDecisionData $data): Decision
{
    $decision = $this->repository->findById($id);

    if ($decision->is_locked) {
        throw new \DomainException('Cannot update locked decision');
    }

    $decision = $this->repository->update($id, $data);
    event(new DecisionUpdated($decision));

    return $decision;
}

public function lockDecision(int $id, int $lockedBy): Decision
{
    $decision = $this->repository->findById($id);
    $decision->lock($lockedBy);
    event(new DecisionLocked($decision));
    return $decision;
}

public function addOutcome(int $id, string $notes): Decision
{
    $decision = $this->repository->findById($id);
    $decision->addOutcomeNotes($notes);
    event(new DecisionOutcomeAdded($decision));
    return $decision;
}
```

---

## Events (Steps 9-12)

- `DecisionRecorded`
- `DecisionUpdated`
- `DecisionLocked`
- `DecisionOutcomeAdded`

---

## Policy (Steps 13-14)

**DecisionPolicy additional methods:**

```php
public function update(User $user, Decision $decision): bool
{
    // Cannot update locked decisions
    if ($decision->is_locked) return false;

    // Standard RBAC for unlocked
    if ($this->isAdmin($user)) return true;
    if ($this->isEditor($user) && $this->canEdit($user, $decision)) return true;
    return false;
}

public function lock(User $user, Decision $decision): bool
{
    // Only admins and decision maker can lock
    if ($this->isAdmin($user)) return true;
    return $decision->decided_by === $user->id;
}

public function addOutcome(User $user, Decision $decision): bool
{
    // Anyone who can view can add outcome notes
    return $this->view($user, $decision);
}
```

---

## API Interface (Steps 15-26)

Resources, Controllers, FormRequests, Routes

**Additional Endpoints:**
```php
Route::post('decisions/{decision}/lock', [DecisionController::class, 'lock']);
Route::post('decisions/{decision}/add-outcome', [DecisionController::class, 'addOutcome']);
Route::get('projects/{project}/decisions/decision-log', [DecisionController::class, 'decisionLog']);
```

---

## Web Interface (Steps 27-42)

Views include:
- `index.blade.php` (with locked indicator badges)
- `decision-log.blade.php` (immutable decision register)
- `create.blade.php`
- `edit.blade.php` (disabled if locked)
- `show.blade.php` (with outcome timeline)

**Special UI considerations:**
- Show lock icon for locked decisions
- Disable edit button for locked decisions
- Allow outcome notes form even for locked decisions

---

## Livewire Components (Steps 43-46)

- `DecisionForm` - With participants multi-select
- `DecisionTimeline` - Chronological decision history
- `OutcomeNotesForm` - Add outcome notes (always enabled)

---

## Testing (Steps 47-52)

- Create decision
- Lock decision
- Prevent editing locked decision
- Add outcome notes to locked decision
- Policy tests for lock permission
- Soft delete (but never hard delete)

---

**Implementation Ready:** ✅
**Next Module:** ProjectJournal
