# AI Agent Instructions: Folder Structure

Reviewed:

## Folder Structure Instructions

## AI Coding Agent Instructions: Project Folder Structure & Development Approach

### Architecture Philosophy

This codebase follows a **modular monolith** approach using **subdomain-driven module separation**. Each business area is represented as a domain module with internal submodules.

**The Four Core Domains:**
1. **GOVERNANCE** - Strategic oversight and reporting
2. **EXECUTION** - Project delivery and scheduling
3. **CONTROL** - Risk, Issue, Change, and Decision management
4. **KNOWLEDGE** - Context preservation and stakeholder management

All code should be scoped within its respective module unless explicitly shared via the `Shared`, `Notes`, or `AuditTrail` systems.

---

### Folder Structure Overview

Each top-level domain module contains nested **feature submodules** with isolated structure.

```
app/Modules/
└── <Domain>/
    └── <Submodule>/
        ├── Controllers/
        │   ├── Api/
        │   ├── Web/
        │   └── Livewire/
        ├── DTOs/
        ├── Events/
        ├── FormRequests/
        ├── Models/
        ├── Policies/
        ├── Repositories/
        ├── Resources/
        ├── Services/
        ├── ViewModels/
        ├── Views/
        └── Livewire/
```

#### Domain-to-Submodule Mapping

**GOVERNANCE/**
- `StatusReports/` - Executive reporting with snapshot technology

**EXECUTION/**
- `Milestones/` - Stage gates and critical path tracking
- `ActionItems/` - High-level directives and tasks
- `Deliverables/` - Tangible outputs and sign-offs
- `Dependencies/` - Blockers and external constraints
- `MeetingEvents/` - Governance meeting scheduling

**CONTROL/**
- `Risks/` - Future uncertainties requiring mitigation
- `Issues/` - Active problems requiring resolution
- `ChangeRequests/` - Formal scope and schedule change workflow
- `Decisions/` - Immutable strategic decision log

**KNOWLEDGE/**
- `ProjectJournal/` - Social-style activity feed with entity linking
- `MeetingMinutes/` - Formal meeting records
- `Stakeholders/` - Stakeholder registry and engagement
- `Links/` - Centralized URL repository
- `Lists/` - Flexible checklist management
- `LessonsLearned/` - Retrospective and knowledge capture

**Example Path:**
```
app/Modules/Execution/ActionItems/Services/ActionItemService.php
app/Modules/Control/Risks/Repositories/RiskRepository.php
app/Modules/Knowledge/ProjectJournal/Events/JournalEntryCreated.php
```

---

### Grouping Entities Pattern

Many submodules support **2-level hierarchical grouping**. These require companion models:

**Pattern:**
```
app/Modules/<Domain>/<Submodule>/
├── Models/
│   ├── <Entity>.php
│   └── <Entity>Group.php
```

**Submodules with Grouping:**
- `Milestones/` → `MilestoneGroup`
- `ActionItems/` → `AitemGroup`
- `Deliverables/` → `DeliverableGroup`
- `Dependencies/` → `DependencyGroup`
- `MeetingEvents/` → `MeetingGroup`
- `Risks/` → `RiskGroup`
- `Issues/` → `IssueGroup`
- `Links/` → `LinkGroup`
- `Lists/` → `ListGroup`

**Business Rule:** Maximum 2-level hierarchy enforced at the service layer.

---

### Triple Interface Support

Each feature must support:

- **API** via `Controllers/Api` - RESTful JSON responses
- **Blade Web Pages** via `Controllers/Web` and `Views/` - Server-rendered HTML
- **Livewire Components** via `Livewire/` - Reactive TALL stack interfaces

**Reusable Across Interfaces:**
- `FormRequests/` - Validate inbound data
- `DTOs/` - Normalize data structures
- `Services/` - Orchestrate business logic
- `Policies/` - Authorize actions

---

### Common Layers Used Per Submodule

| Layer           | Purpose                                                      | When to Use |
| --------------- | ------------------------------------------------------------ | ----------- |
| `DTOs/`         | Transform and normalize data across interfaces and services  | Always - Core data transfer objects |
| `Events/`       | Triggered on lifecycle changes for logging, notifications, workflows | When state changes require side effects |
| `FormRequests/` | Validate data before service or controller use               | For each controller endpoint receiving input |
| `Services/`     | Core business logic and orchestration                        | Always - Single source of truth for operations |
| `Repositories/` | Data access layer, abstracting models and queries            | When queries are complex or reused |
| `Policies/`     | Authorization layer per user profile and model               | Always - RBAC enforcement |
| `Resources/`    | API transformers for consistent JSON output                  | API endpoints only |
| `ViewModels/`   | Data prep for Blade and Livewire views                       | Complex view data composition |

---

### Shared Systems

These are globally available and **must be reused** where applicable:

**`Notes/` (Polymorphic Commentary System)**
- Universal, immutable comment streams attached to any entity
- Polymorphic relationship: `notable_type`, `notable_id`
- Chronological, append-only audit trail
- **Implementation:**
  ```
  app/Modules/Shared/Notes/
  ├── Models/Note.php
  ├── Services/NoteService.php
  ├── Policies/NotePolicy.php
  └── Resources/NoteResource.php
  ```

**`AuditTrail/` (System-Level Change Logging)**
- Logs field-level changes (old_value → new_value)
- User and timestamp tracking
- Immutable once written
- **Implementation:** Via `spatie/laravel-activitylog` or custom audit table

**`EntityLinks/` (Limited Cross-Module Linking)**
- **V1 Scope:** Only ProjectJournal entries can link to other entities
- Polymorphic: `source_type` (always JournalEntry), `target_type` (any module)
- **Implementation:**
  ```
  app/Modules/Shared/EntityLinks/
  ├── Models/EntityLink.php
  └── Services/EntityLinkService.php
  ```

**`Shared/` (Utilities and Constants)**
- Reusable enums, traits, constants, utility classes
- **Examples:**
  - `Enums/RagStatus.php` (red, amber, green)
  - `Enums/ProjectStatus.php` (draft, active, on_hold, completed, cancelled)
  - `Traits/HasGroups.php` (for entities supporting 2-level grouping)
  - `Traits/HasInternalVisibility.php` (for `is_internal_only` flag)
  - `Services/WeekCalculatorService.php` (ISO 8601 week computation)

---

### Key Rules

1. **Keep each submodule self-contained**. No logic leakage across modules except via explicit Service contracts.

2. **Never reuse controllers across interfaces** (API ≠ Web ≠ Livewire). Each interface has distinct concerns:
   - API: RESTful JSON responses with Resources
   - Web: Blade views with ViewModels
   - Livewire: Reactive components with inline view data

3. **Prefer services + DTOs** over placing logic in controllers or models.
   - Controllers: Thin routing layer
   - Models: Data structure and relationships only
   - Services: All business logic, validation orchestration, event triggering

4. **FormRequests are interface-bound, DTOs are domain-bound.**
   - FormRequests: Validate HTTP input for specific endpoints
   - DTOs: Represent domain concepts across all layers

5. **All data changes must trigger events for audit logging.**
   - Use Laravel Events for lifecycle hooks
   - Example: `RiskCreated`, `MilestoneStatusChanged`, `StatusReportPublished`

6. **Soft deletes only** — No hard deletes allowed.
   - Use `deleted_at` boolean flag (not timestamp)
   - Implement `SoftDeletes` trait on all domain models

7. **Respect immutability constraints:**
   - Notes: Cannot be edited or deleted after creation
   - Status Reports: Locked once `published_at` is set
   - Decisions: Locked once `is_locked` = true
   - Audit log entries: Always immutable

8. **Enforce grouping hierarchy limits:**
   - Maximum 2 levels: Parent Group → Child Group → Items
   - Service layer must validate: groups with `parent_id` cannot themselves be parents

9. **Apply visibility filtering:**
   - All queries must respect `is_internal_only` flag based on user role/context
   - Implement at Repository or Query Scope level

10. **Use polymorphic relationships correctly:**
    - `notes`: Attach to ANY entity via `notable_type`, `notable_id`
    - `entity_links`: ONLY from ProjectJournal to other entities

---

### DTO vs FormRequest Guidelines

**FormRequests** (Interface Layer):
- Purpose: Validate HTTP input for specific controller actions
- Location: `FormRequests/`
- Naming: `Store{Entity}Request`, `Update{Entity}Request`
- Scope: Tied to specific endpoints (API vs Web may differ)
- Contains: Validation rules, authorization checks, error messages

**DTOs** (Domain Layer):
- Purpose: Represent domain concepts, transfer data between layers
- Location: `DTOs/`
- Naming: `{Entity}Data`, `Create{Entity}Data`, `Update{Entity}Data`
- Scope: Used across all interfaces and services
- Contains: Type-hinted properties, transformation logic, factory methods

**Example:**
```php
// FormRequest (Interface Layer - API-specific)
app/Modules/Execution/ActionItems/FormRequests/Api/StoreActionItemRequest.php

// DTO (Domain Layer - Reusable)
app/Modules/Execution/ActionItems/DTOs/CreateActionItemData.php

// Usage in Controller
public function store(StoreActionItemRequest $request)
{
    $dto = CreateActionItemData::fromRequest($request);
    return $this->actionItemService->create($dto);
}
```

---

### Auto-Computed Fields Pattern

**Target Week Calculation** (ISO 8601):
- Stored format: `2026-W04` (year-week)
- Display format: "Week 4 of January"
- **Implementation:**
  ```php
  // In Model Observer or Service
  $entity->target_week = Carbon::parse($entity->target_date)->format('o-\WW');

  // In ViewModel/Resource for display
  [$year, $week] = explode('-W', $entity->target_week);
  $date = Carbon::now()->setISODate($year, $week);
  return "Week {$date->weekOfMonth} of {$date->format('F')}";
  ```

---

### Module Interaction Patterns

**Allowed:**
- Services calling other Services within same Domain
- Services emitting Events consumed by other Domains
- Repositories returning Models with eager-loaded relationships
- Shared utilities and enums

**Prohibited:**
- Direct Model queries outside Repository layer
- Controllers directly accessing other Domain Services
- Bypass of Policy authorization checks
- Cross-Domain direct data manipulation

---

### Testing Structure

Mirror the module structure in tests:

```
tests/
├── Feature/
│   └── Modules/
│       └── <Domain>/
│           └── <Submodule>/
│               ├── Api/
│               ├── Web/
│               └── Livewire/
└── Unit/
    └── Modules/
        └── <Domain>/
            └── <Submodule>/
                ├── Services/
                ├── DTOs/
                └── Policies/
```

---

### Migration Naming Convention

```
{timestamp}_create_{domain}_{submodule}_{table}_table.php

Examples:
2026_01_25_000001_create_execution_milestones_table.php
2026_01_25_000002_create_execution_milestone_groups_table.php
2026_01_25_000003_create_control_risks_table.php
```

---

### Role-Based Access Control (RBAC) Pattern

**Policies enforce these rules:**

| Role   | Access Pattern |
|--------|----------------|
| Admin  | Full CRUD across ALL projects + system config + Portfolio Dashboard |
| Editor | Full CRUD on ASSIGNED projects only (via `project_user` pivot) |
| Viewer | Read-only across ALL projects (Dashboards and Reports) |

**Policy Implementation:**
```php
// app/Modules/<Domain>/<Submodule>/Policies/<Entity>Policy.php

public function update(User $user, Entity $entity): bool
{
    if ($user->role === 'admin') return true;
    if ($user->role === 'editor') {
        return $user->projects->contains($entity->project_id);
    }
    return false;
}
```

---

This structure ensures:
- Clear separation of concerns
- Consistent patterns across all domains
- Scalable architecture for future features
- Comprehensive testing coverage
- Explicit authorization at every layer
