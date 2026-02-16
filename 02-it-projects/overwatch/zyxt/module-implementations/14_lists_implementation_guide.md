# Lists Module - Implementation Guide

**Module:** Lists (KNOWLEDGE Domain)
**Purpose:** Flexible checklist management with hierarchical grouping
**Dependencies:** Project, HasGroups trait
**Estimated Steps:** 52

---

## Module Overview

Lists provide flexible, lightweight checklist management for miscellaneous project tracking needs (not formal action items).

### Key Features
- **2-level grouping:** ListGroup
- **Checkable items:** Track completion status
- **Ordering:** Drag-and-drop reordering
- **Description:** Optional context per item
- **Completion tracking:** Completed date and user

---

## Database Schema

### Step 1-2: Create Migrations

**list_groups table:** Standard group structure

**list_items table:**
```php
$table->id();
$table->foreignId('project_id')->constrained()->cascadeOnDelete();
$table->foreignId('list_group_id')->nullable()->constrained()->nullOnDelete();
$table->string('title');
$table->text('description')->nullable();
$table->boolean('is_completed')->default(false);
$table->date('completed_at')->nullable();
$table->foreignId('completed_by')->nullable()->constrained('users')->nullOnDelete();
$table->integer('display_order')->default(0);
$table->boolean('is_internal_only')->default(false);
// timestamps, soft deletes, indexes
$table->index(['project_id', 'is_completed', 'deleted_at']);
$table->index('list_group_id');
$table->index('display_order');
```

### Step 3: Run Migration

---

## Models (Steps 4-5)

**ListGroup:** Standard with HasGroups

**ListItem:**

**Casts:** is_completed (boolean), completed_at (date)

**Methods:**

```php
public function markComplete(): void
{
    $this->update([
        'is_completed' => true,
        'completed_at' => now(),
        'completed_by' => auth()->id(),
    ]);
}

public function markIncomplete(): void
{
    $this->update([
        'is_completed' => false,
        'completed_at' => null,
        'completed_by' => null,
    ]);
}
```

---

## DTOs/Repository/Service (Steps 6-11)

**Service additional methods:**

```php
public function toggleComplete(int $id): ListItem
{
    $item = $this->repository->findById($id);

    if ($item->is_completed) {
        $item->markIncomplete();
    } else {
        $item->markComplete();
    }

    return $item;
}

public function getCompletionStats(int $projectId): array
{
    $all = $this->repository->getItemsForProject($projectId);
    $completed = $all->where('is_completed', true)->count();

    return [
        'total' => $all->count(),
        'completed' => $completed,
        'percentage' => $all->count() > 0 ? ($completed / $all->count()) * 100 : 0
    ];
}
```

---

## Events (Steps 12-15)

- `ListItemCreated`
- `ListItemCompleted`
- `ListItemIncompleted`
- `ListItemDeleted`

---

## Policy (Steps 16-17)

Standard RBAC

---

## API/Web/Livewire (Steps 18-47)

**Additional API Endpoints:**
- `POST /list-items/{item}/toggle`
- `GET /projects/{project}/lists/stats`

**Livewire Components:**
- `ChecklistManager` - Interactive checklist with real-time toggle
- `CompletionProgress` - Visual progress bar

---

## Testing (Steps 48-52)

- Toggle completion
- Completion stats calculation
- Reordering
- Standard CRUD

---

**Implementation Ready:** ✅
**Next Module:** LessonsLearned
