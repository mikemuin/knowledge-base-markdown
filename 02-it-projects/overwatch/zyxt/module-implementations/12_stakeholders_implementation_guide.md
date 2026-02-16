# Stakeholders Module - Implementation Guide

**Module:** Stakeholders (KNOWLEDGE Domain)
**Purpose:** Stakeholder registry - NO hierarchical grouping
**Dependencies:** Project, StakeholderRole enum
**Estimated Steps:** 48

---

## Module Overview

Stakeholders track key individuals and organizations with interest in the project. This is a registry for engagement tracking.

### Key Features
- **No grouping:** Flat registry structure
- **Role classification:** Sponsor, Customer, Vendor, Team Member, Other
- **Engagement tracking:** Engagement level, communication preferences
- **Contact details:** Name, organization, email, phone
- **Internal notes:** Private notes about stakeholder management

---

## Database Schema

### Step 1: Create Migration

```php
$table->id();
$table->foreignId('project_id')->constrained()->cascadeOnDelete();
$table->string('name');
$table->string('organization')->nullable();
$table->string('role', 30); // StakeholderRole enum
$table->string('email')->nullable();
$table->string('phone', 50)->nullable();
$table->string('engagement_level', 20)->nullable(); // High, Medium, Low
$table->text('interests')->nullable(); // What they care about
$table->text('communication_preferences')->nullable();
$table->text('internal_notes')->nullable(); // Always internal-only
$table->boolean('is_key_stakeholder')->default(false);
$table->boolean('is_internal_only')->default(false);
// timestamps, soft deletes, indexes
$table->index(['project_id', 'role', 'deleted_at']);
$table->index('is_key_stakeholder');
```

### Step 2: Run Migration

---

## Models (Steps 3-4)

**Stakeholder:**

**Casts:** role (StakeholderRole enum), is_key_stakeholder (boolean)

**NO complex business methods** (simple CRUD entity)

---

## DTOs/Repository/Service (Steps 5-9)

Standard patterns, no special workflows

**Repository additional methods:**
- `getKeyStakeholders(projectId)`
- `getStakeholdersByRole(projectId, role)`
- `getStakeholdersByEngagementLevel(projectId, level)`

---

## Events (Steps 10-11)

- `StakeholderAdded`
- `StakeholderUpdated`

---

## Policy (Steps 12-13)

Standard RBAC

---

## API/Web/Livewire (Steps 14-43)

Standard triple interface

**Special Web Views:**
- `stakeholder-map.blade.php` - Visual stakeholder matrix (engagement × influence)

**Livewire Components:**
- `StakeholderForm`
- `StakeholderCard`
- `StakeholderMatrix` - Interactive engagement/influence map

---

## Testing (Steps 44-48)

Standard CRUD tests

---

**Implementation Ready:** ✅
**Next Module:** Links
