# Links Module - Implementation Guide

**Module:** Links (KNOWLEDGE Domain)
**Purpose:** Centralized URL repository with hierarchical grouping
**Dependencies:** Project, LinkType enum, HasGroups trait
**Estimated Steps:** 50

---

## Module Overview

Links provide a centralized repository for project-related URLs (documentation, repositories, dashboards, external resources).

### Key Features
- **2-level grouping:** LinkGroup
- **Type classification:** Documentation, Repository, Dashboard, External Resource
- **URL validation:** Ensure valid URLs
- **Description:** Context for each link
- **Ordering:** Display order within groups

---

## Database Schema

### Step 1-2: Create Migrations

**link_groups table:** Standard group structure

**links table:**
```php
$table->id();
$table->foreignId('project_id')->constrained()->cascadeOnDelete();
$table->foreignId('link_group_id')->nullable()->constrained()->nullOnDelete();
$table->string('title');
$table->text('description')->nullable();
$table->string('url', 500); // Validated URL
$table->string('type', 30); // LinkType enum
$table->integer('display_order')->default(0);
$table->boolean('is_internal_only')->default(false);
// timestamps, soft deletes, indexes
$table->index(['project_id', 'type', 'deleted_at']);
$table->index('link_group_id');
$table->index('display_order');
```

### Step 3: Run Migration

---

## Models (Steps 4-5)

**LinkGroup:** Standard with HasGroups

**Link:**

**Casts:** type (LinkType enum)

**Validation:** URL must be valid (enforce in FormRequest)

---

## DTOs/Repository/Service (Steps 6-11)

Standard patterns with hierarchy validation

**Service additional method:**

```php
public function reorderLinks(int $groupId, array $orderMap): void
{
    foreach ($orderMap as $linkId => $order) {
        $this->repository->updateDisplayOrder($linkId, $order);
    }
}
```

---

## Events (Steps 12-14)

- `LinkCreated`
- `LinkUpdated`
- `LinkDeleted`

---

## Policy (Steps 15-16)

Standard RBAC

---

## API/Web/Livewire (Steps 17-45)

Standard triple interface

**Special Web Views:**
- `directory.blade.php` - Grouped, organized link directory

**Livewire Components:**
- `LinkForm`
- `LinkDirectory` - Collapsible groups, drag-and-drop reordering

---

## Testing (Steps 46-50)

- URL validation
- Reordering within groups
- Standard CRUD

---

**Implementation Ready:** ✅
**Next Module:** Lists
