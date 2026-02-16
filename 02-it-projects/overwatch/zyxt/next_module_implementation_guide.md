# Guide: Implementing the Next 15 Modules

**For:** AI Coding Agents and Developers **Context:** After completing StatusReports (Steps 1-110) **Goal:** Efficiently replicate the proven pattern across 15 remaining modules

------

## RECOMMENDED APPROACH: The "Template + Prompt" Method

### Overview

**Don't copy-paste blindly.** Instead, use the StatusReports implementation as a **living template** combined with **module-specific prompts**.

This approach:

- Maintains consistency
- Adapts to module-specific requirements
- Reduces errors from blind copying
- Allows AI to apply learned patterns intelligently

------

## STEP-BY-STEP WORKFLOW

### Phase 1: Choose Your Next Module

**Recommended Implementation Order:**

1. **Start with EXECUTION modules** (simpler, similar to StatusReports)
   - Milestones (has grouping, target_week calculation)
   - ActionItems (has grouping, assignment, priority)
   - Deliverables (has grouping, assignment, deliverable types)
   - Dependencies (has grouping, dependency types)
   - MeetingEvents (has grouping, different status enum)
2. **Then CONTROL modules** (more complex business logic)
   - Risks (severity assessment, mitigation strategies)
   - Issues (priority calculation, escalation)
   - ChangeRequests (workflow states, approval tracking)
   - Decisions (immutability, superseding logic)
3. **Then KNOWLEDGE modules** (unique patterns)
   - ProjectJournal (entity linking, social feed)
   - MeetingMinutes (FK to MeetingEvents, not polymorphic)
   - Stakeholders (influence/interest matrix)
   - Links (simple grouping)
   - Lists (parent-child ListItems)
   - LessonsLearned (categorization, export)

**Rationale:** Start simple, build confidence, tackle complexity later.

------

### Phase 2: Prepare Module-Specific Reference Document

For each module, create a **Module Implementation Spec** that includes:

#### A. Data Model Extract (from Data Models document)

**Example for Milestones:**

```markdown
## Milestones Data Model

**Table:** milestones

**Fields:**
- id (PK)
- project_id (FK)
- milestone_group_id (FK, nullable)
- title
- description (markdown)
- baseline_date
- target_date
- target_week (ISO 8601, auto-computed)
- actual_date
- status (ExecutionStatus enum)
- is_critical_path (boolean)
- completed_at (nullable)
- is_internal_only (boolean, default false)
- deleted_at (soft delete)
- metadata (created_at, created_by, updated_at, updated_by)

**Group Table:** milestone_groups
- id (PK)
- project_id (FK)
- title
- parent_id (FK, nullable, self-referential)
- deleted_at
- metadata

**Relationships:**
- belongsTo(Project)
- belongsTo(MilestoneGroup, 'milestone_group_id')
- hasMany(Note) - polymorphic

**Enums:**
- status: ExecutionStatus (pending, on_track, at_risk, delayed, completed, cancelled)

**Business Rules:**
- Maximum 2-level grouping hierarchy
- target_week auto-computed from target_date
- is_critical_path is manual flag (no automation)
- Special query: Non-completed/cancelled items from past + 4 weeks ahead

**Indexes:**
- project_id
- milestone_group_id
- status
- target_week
- is_critical_path
- is_internal_only
- Composite: (project_id, status, deleted_at)
```

#### B. Module-Specific Differences from StatusReports

**Example for Milestones:**

```markdown
## Key Differences from StatusReports:

1. **HAS Grouping:** Needs MilestoneGroup model and table
2. **NO Immutability:** Can always be edited (unlike published StatusReports)
3. **Auto-Computed Field:** target_week (same as StatusReports snapshot_data logic)
4. **Special Query Method:** inActiveWindow() scope for past + 4 weeks
5. **Additional Field:** is_critical_path (manual flag)
6. **Different Enum:** ExecutionStatus instead of RagStatus
```

#### C. Custom Business Logic

**Example for Milestones:**

```markdown
## Custom Methods Needed:

**In Model:**
- `scopeInActiveWindow($query)` - Non-completed/cancelled from past + 4 weeks
- `scopeCriticalPath($query)` - Where is_critical_path = true
- `markAsCompleted()` - Set status, actual_date, completed_at

**In Service:**
- `getUpcomingMilestones(Project $project, int $weeks = 4)` - For dashboard
- `getCriticalPathMilestones(Project $project)` - For reports

**In Observer:**
- Auto-compute target_week when target_date changes (creating/updating)
```

------

### Phase 3: Generate the AI Prompt

**Template Format:**

```markdown
# Implement [MODULE_NAME] Module

## Context
I've completed the StatusReports module (Steps 51-110) as a reference implementation.
Now implement the [MODULE_NAME] module following the same 60-step pattern.

## Reference Pattern (StatusReports)
The StatusReports module includes:
- Migration with proper indexes
- Model with SoftDeletes, LogsActivity traits
- Observer for [immutability rules if any]
- Factory for test data
- DTOs (CreateData, UpdateData)
- Repository (returns Query Builder, NOT paginated)
- Service layer (CRUD + business logic)
- Policy (RBAC: Admin, Editor, Viewer)
- Events (Created, Updated, Deleted)
- FormRequests (API + Web, Store + Update)
- API Resources (Resource + Collection)
- API Controller (index, store, show, update, destroy + custom actions)
- Web Views (index, create, show, edit)
- Web Controller (full CRUD + custom actions)
- ViewModels (for complex views)
- Livewire Components (List, Form, Card)
- Livewire Views
- Routes (API + Web)
- Tests (Feature + Unit)
- API Documentation

## Module-Specific Requirements

[PASTE YOUR MODULE SPEC FROM PHASE 2 HERE]

## Implementation Instructions

Follow the StatusReports pattern (Steps 51-110) but adapt for [MODULE_NAME]:

**Step 51-52:** Create [module_table] migration and run it
**Step 53-54:** Create [Module] model and observer [if needed]
**Step 55:** Create [Module] factory
**Step 56-57:** Create DTOs
**Step 58:** Create Repository
**Step 59-64:** Create Service with methods: [list custom methods]
**Step 65-66:** Create and register Policy
**Step 67-68:** Create Events
**Step 69:** [Add any missing helper methods]
**Step 70-74:** Create FormRequests
**Step 75-76:** Create API Resources
**Step 77-83:** Create API Controller and register routes
**Step 84-87:** Create Web Views
**Step 88-96:** Create Web Controller and register routes
**Step 97-98:** Create ViewModels [if needed]
**Step 99-106:** Create Livewire Components
**Step 107:** End-to-end testing
**Step 108:** Feature tests
**Step 109:** Unit tests
**Step 110:** API documentation

## Key Adaptations

[LIST THE DIFFERENCES FROM STATUSREPORTS HERE]

Example:
- INCLUDE MilestoneGroup model and table (StatusReports has no groups)
- DO NOT implement immutability observer (StatusReports has this, Milestones don't)
- ADD target_week auto-computation in observer (same as StatusReports)
- ADD inActiveWindow() scope for temporal queries
- ADD is_critical_path manual flag handling

## Validation Checklist

After implementation, verify:
- [ ] Migration creates table with correct fields and indexes
- [ ] Model has correct relationships and traits
- [ ] Group model and table created [if applicable]
- [ ] Repository returns Query Builder (not paginated)
- [ ] Service layer has all CRUD + custom methods
- [ ] Policy enforces RBAC correctly
- [ ] API endpoints return correct status codes and data format
- [ ] Web pages render without errors
- [ ] Livewire components are reactive
- [ ] All tests pass: `php artisan test --filter=[Module]`
- [ ] Routes registered correctly in api.php and web.php

## Start Implementation

Begin with Step 51: Create the migration.
```

------

### Phase 4: Feed to AI Agent in Chunks

**Don't give all 60 steps at once.** Break into logical chunks:

#### Chunk 1: Data Layer (Steps 51-55)

```
Implement Steps 51-55 for [MODULE_NAME]:
- Create migration with all fields, indexes, and group table if needed
- Run migration
- Create model with traits and relationships
- Create observer if needed [specify rules]
- Create factory for test data

[Include module spec]
```

**Validate before proceeding:**

```bash
php artisan migrate:status
php artisan tinker
>>> App\Modules\Milestones\Models\Milestone::count()
```

#### Chunk 2: Business Logic (Steps 56-69)

```
Implement Steps 56-69 for [MODULE_NAME]:
- Create DTOs
- Create Repository (remember: returns Query Builder!)
- Create Service with methods: [list all methods including custom ones]
- Create Policy
- Create Events
- Add helper methods [list them]

[Include business logic specs]
```

**Validate:**

```bash
php artisan tinker
>>> $service = app(App\Modules\Milestones\Services\MilestoneService::class);
>>> method_exists($service, 'getUpcomingMilestones');
```

#### Chunk 3: API Layer (Steps 70-83)

```
Implement Steps 70-83 for [MODULE_NAME]:
- Create FormRequests for API
- Create API Resources
- Create API Controller
- Register API routes

Test immediately with Postman/Insomnia.
```

**Validate:**

```bash
php artisan route:list | grep milestones
# Test in Postman: POST /api/projects/1/milestones
```

#### Chunk 4: Web Layer (Steps 84-96)

```
Implement Steps 84-96 for [MODULE_NAME]:
- Create Web Views (index, create, show, edit)
- Create Web Controller
- Register Web routes

Use namespace notation for views: view('milestones::index')
```

**Validate:**

```bash
# Visit in browser: /projects/1/milestones
```

#### Chunk 5: Livewire Layer (Steps 97-106)

```
Implement Steps 97-106 for [MODULE_NAME]:
- Create ViewModels if needed
- Create Livewire Components (List, Form, Card)
- Create Livewire Views
```

**Validate:**

```bash
php artisan livewire:list | grep Milestone
# Test reactivity in browser
```

#### Chunk 6: Testing & Documentation (Steps 107-110)

```
Implement Steps 107-110 for [MODULE_NAME]:
- End-to-end manual testing
- Feature tests
- Unit tests
- API documentation (PHPDoc)

Run: php artisan test --filter=Milestone
```

------

### Phase 5: Use a Tracking System

Create a simple tracking spreadsheet or markdown file:

```markdown
# Module Implementation Tracker

## Status Legend
- ⬜ Not Started
- In Progress
- Complete
- Blocked

| Module | Data Layer | Business Logic | API | Web | Livewire | Tests | Status |
|--------|------------|----------------|-----|-----|----------|-------|--------|
| StatusReports | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ Complete |
| Milestones | 🟡 | ⬜ | ⬜ | ⬜ | ⬜ | ⬜ | 🟡 In Progress |
| ActionItems | ⬜ | ⬜ | ⬜ | ⬜ | ⬜ | ⬜ | ⬜ Not Started |
| Deliverables | ⬜ | ⬜ | ⬜ | ⬜ | ⬜ | ⬜ | ⬜ Not Started |
| ... | | | | | | | |

## Current Module: Milestones
**Started:** Jan 28, 2026
**Steps Completed:** 51-55 ✅, 56 🟡
**Next:** Complete DTOs (Step 57)
**Blockers:** None
```

------

## AUTOMATION OPTION: Create a Scaffolding Command

**For advanced efficiency**, create an Artisan command that generates the boilerplate:

### Step 1: Create the Command

```bash
php artisan make:command ScaffoldModule
```

### Step 2: Implement the Command

```php
<?php

namespace App\Console\Commands;

use Illuminate\Console\Command;
use Illuminate\Support\Facades\File;
use Illuminate\Support\Str;

class ScaffoldModule extends Command
{
    protected $signature = 'overwatch:scaffold-module {name} {--group}';
    protected $description = 'Scaffold a new Overwatch module based on StatusReports pattern';

    public function handle()
    {
        $moduleName = $this->argument('name');
        $hasGroup = $this->option('group');

        $this->info("Scaffolding module: {$moduleName}");

        // Create directory structure
        $this->createDirectoryStructure($moduleName);

        // Generate files from stubs
        $this->generateMigration($moduleName, $hasGroup);
        $this->generateModel($moduleName);
        if ($hasGroup) {
            $this->generateGroupModel($moduleName);
        }
        $this->generateRepository($moduleName);
        $this->generateService($moduleName);
        $this->generatePolicy($moduleName);
        $this->generateDTOs($moduleName);
        $this->generateFormRequests($moduleName);
        $this->generateControllers($moduleName);
        $this->generateViews($moduleName);
        $this->generateLivewireComponents($moduleName);

        $this->info("✅ Module {$moduleName} scaffolded successfully!");
        $this->info("Next steps:");
        $this->info("1. Review and customize the generated migration");
        $this->info("2. Run: php artisan migrate");
        $this->info("3. Implement custom business logic in the Service");
        $this->info("4. Customize views and Livewire components");
    }

    protected function createDirectoryStructure($moduleName)
    {
        $basePath = app_path("Modules/{$moduleName}");

        $directories = [
            'Controllers/Api',
            'Controllers/Web',
            'DTOs',
            'Events',
            'FormRequests/Api',
            'FormRequests/Web',
            'Livewire',
            'Models',
            'Policies',
            'Repositories',
            'Resources',
            'Services',
            'ViewModels',
            'Views',
        ];

        foreach ($directories as $dir) {
            File::makeDirectory("{$basePath}/{$dir}", 0755, true, true);
        }

        // Create Livewire views directory
        File::makeDirectory(
            resource_path("views/livewire/" . Str::kebab($moduleName)),
            0755,
            true,
            true
        );

        // Create module views directory
        File::makeDirectory(
            app_path("Modules/{$moduleName}/Views"),
            0755,
            true,
            true
        );
    }

    protected function generateMigration($moduleName, $hasGroup)
    {
        // Use stub file and replace placeholders
        $stub = File::get(base_path('stubs/module-migration.stub'));

        // Replace placeholders
        $stub = str_replace('{{MODULE_TABLE}}', Str::snake(Str::plural($moduleName)), $stub);
        $stub = str_replace('{{MODULE_NAME}}', $moduleName, $stub);

        // Generate migration file
        $timestamp = now()->format('Y_m_d_His');
        $tableName = Str::snake(Str::plural($moduleName));
        $fileName = "{$timestamp}_create_{$tableName}_table.php";

        File::put(
            database_path("migrations/{$fileName}"),
            $stub
        );

        if ($hasGroup) {
            // Generate group migration
            $groupStub = File::get(base_path('stubs/module-group-migration.stub'));
            $groupStub = str_replace('{{MODULE_TABLE}}', $tableName, $groupStub);
            $groupStub = str_replace('{{GROUP_TABLE}}', "{$tableName}_groups", $groupStub);

            $groupFileName = "{$timestamp}_create_{$tableName}_groups_table.php";
            File::put(
                database_path("migrations/{$groupFileName}"),
                $groupStub
            );
        }
    }

    protected function generateModel($moduleName)
    {
        $stub = File::get(base_path('stubs/module-model.stub'));
        $stub = str_replace('{{MODULE_NAME}}', $moduleName, $stub);
        $stub = str_replace('{{MODULE_TABLE}}', Str::snake(Str::plural($moduleName)), $stub);

        File::put(
            app_path("Modules/{$moduleName}/Models/{$moduleName}.php"),
            $stub
        );
    }

    // ... similar methods for other components
}
```

### Step 3: Create Stub Files

Create stubs in `stubs/` directory:

**stubs/module-migration.stub:**

```php
<?php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

return new class extends Migration
{
    public function up(): void
    {
        Schema::create('{{MODULE_TABLE}}', function (Blueprint $table) {
            $table->id();
            $table->foreignId('project_id')->constrained()->cascadeOnDelete();
            $table->string('title');
            $table->text('description')->nullable();
            $table->string('status'); // Customize enum

            // Add module-specific fields here

            $table->boolean('is_internal_only')->default(false);
            $table->softDeletes();
            $table->timestamps();
            $table->foreignId('created_by')->constrained('users');
            $table->foreignId('updated_by')->constrained('users');

            // Indexes
            $table->index('project_id');
            $table->index('status');
            $table->index('is_internal_only');
            $table->index(['project_id', 'status', 'deleted_at']);
        });
    }

    public function down(): void
    {
        Schema::dropIfExists('{{MODULE_TABLE}}');
    }
};
```

**stubs/module-model.stub:**

```php
<?php

namespace App\Modules\{{MODULE_NAME}}\Models;

use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\SoftDeletes;
use Illuminate\Database\Eloquent\Factories\HasFactory;
use Spatie\Activitylog\Traits\LogsActivity;
use Spatie\Activitylog\LogOptions;

class {{MODULE_NAME}} extends Model
{
    use HasFactory, SoftDeletes, LogsActivity;

    protected $table = '{{MODULE_TABLE}}';

    protected $fillable = [
        'project_id',
        'title',
        'description',
        'status',
        'is_internal_only',
        'created_by',
        'updated_by',
    ];

    protected $casts = [
        'is_internal_only' => 'boolean',
        'created_at' => 'datetime',
        'updated_at' => 'datetime',
        'deleted_at' => 'datetime',
    ];

    public function getActivitylogOptions(): LogOptions
    {
        return LogOptions::defaults()->logAll();
    }

    // Relationships
    public function project()
    {
        return $this->belongsTo(\App\Models\Project::class);
    }

    public function createdBy()
    {
        return $this->belongsTo(\App\Models\User::class, 'created_by');
    }

    public function updatedBy()
    {
        return $this->belongsTo(\App\Models\User::class, 'updated_by');
    }

    public function notes()
    {
        return $this->morphMany(\App\Modules\Shared\Notes\Models\Note::class, 'notable');
    }
}
```

------

## RECOMMENDED WORKFLOW SUMMARY

### For Each Module:

1. **Prepare** (15 minutes)
   - Extract module spec from data models document
   - Identify differences from StatusReports
   - List custom business logic needed
2. **Generate Prompt** (5 minutes)
   - Use template above
   - Fill in module-specific details
3. **Implement in Chunks** (2-4 hours per module)
   - Feed AI agent 10-15 steps at a time
   - Validate after each chunk
   - Commit after each successful chunk
4. **Test Thoroughly** (30 minutes)
   - Manual testing (API, Web, Livewire)
   - Automated tests
   - Fix any issues
5. **Document** (10 minutes)
   - Update tracker
   - Add any gotchas to notes
   - Commit final version

**Total per module:** ~3-5 hours **15 modules remaining:** ~45-75 hours total

------

## EXAMPLE: MILESTONES MODULE PROMPT

Here's a complete example for implementing Milestones:

```markdown
# Implement Milestones Module

## Context
I've completed the StatusReports module (Steps 51-110). Now implement the Milestones module following the same pattern but with the adaptations listed below.

## Milestones Data Model

**Table:** milestones

**Fields:**
- id, project_id, milestone_group_id (nullable), title, description
- baseline_date, target_date, target_week (auto-computed), actual_date
- status (ExecutionStatus enum), is_critical_path (boolean)
- completed_at (nullable), is_internal_only (default false)
- softDeletes, metadata

**Group Table:** milestone_groups (2-level hierarchy)
- id, project_id, title, parent_id (nullable), softDeletes, metadata

**Enums:**
- status: pending, on_track, at_risk, delayed, completed, cancelled

**Business Rules:**
- target_week auto-computed from target_date (ISO 8601)
- is_critical_path is manual flag (no automation triggers)
- Special query: Non-completed/cancelled from past + 4 weeks ahead
- Maximum 2-level grouping

## Key Differences from StatusReports

1. HAS Grouping (MilestoneGroup model + table + relationships)
2. NO Immutability (no Observer for locking)
3. HAS target_week auto-computation (Observer: creating/updating)
4. HAS is_critical_path manual flag
5. USES ExecutionStatus enum (not RagStatus)
6. NO snapshot_data field
7. HAS baseline_date, actual_date, completed_at fields
8. NEEDS inActiveWindow() scope for temporal queries
9. NEEDS markAsCompleted() helper method

## Custom Business Logic

**Model Scopes:**
- `scopeInActiveWindow($query)` - Non-completed/cancelled from past + 4 weeks
- `scopeCriticalPath($query)` - Where is_critical_path = true

**Model Methods:**
- `markAsCompleted()` - Set status, actual_date, completed_at

**Service Methods (in addition to CRUD):**
- `getUpcomingMilestones(Project $project, int $weeks = 4)`
- `getCriticalPathMilestones(Project $project)`

**Observer:**
- Auto-compute target_week when target_date changes (creating/updating)

## Implementation

Follow StatusReports Steps 51-110 pattern but:

**Step 51:** Create milestones migration with fields above
**Step 51b:** Create milestone_groups migration
**Step 52:** Run migrations
**Step 53:** Create Milestone model with ExecutionStatus enum
**Step 53b:** Create MilestoneGroup model
**Step 54:** Create MilestoneObserver for target_week computation (NOT immutability)
**Step 55:** Create Milestone factory

[Continue with Steps 56-110 following StatusReports pattern]

## Start Implementation: Step 51

Create the milestones table migration.
```

------

## ANTI-PATTERNS TO AVOID

### DON'T: Copy-paste entire StatusReports code and find-replace

**Why:** You'll miss module-specific logic and create subtle bugs.

### DON'T: Implement all 60 steps in one massive prompt

**Why:** Too much for AI to track, hard to debug, no intermediate validation.

### DON'T: Skip validation between chunks

**Why:** Bugs compound and become harder to fix later.

### DON'T: Implement modules in random order

**Why:** You lose the learning benefit of progressive complexity.

### DON'T: Forget to update the tracker

**Why:** You'll lose track of what's done and what needs attention.

------

## PRO TIPS

### DO: Keep a "Gotchas" Document

As you implement each module, note anything unexpected:

```markdown
# Implementation Gotchas

## Milestones
- Observer must check if target_date is set before computing target_week
- inActiveWindow() scope needs to handle cancelled status separately
- MilestoneGroup validation must check 2-level max in Service, not just migration

## ActionItems
- assigned_to is nullable (unlike Milestones)
- Priority enum is different from ExecutionStatus
```

### DO: Use Git Branches per Module

```bash
git checkout -b feature/milestones-module
# Implement Steps 51-110 for Milestones
git commit -m "feat: complete Milestones module"
git checkout main
git merge feature/milestones-module
```

### DO: Celebrate Milestones

After every module completion:

- Run full test suite
- Review code quality
- Update project tracker
- Take a break!

------

## ESTIMATED TIMELINE

**Per Module:**

- Preparation: 15 min
- Data Layer: 30 min
- Business Logic: 60 min
- API Layer: 45 min
- Web Layer: 45 min
- Livewire Layer: 60 min
- Testing: 30 min
- **Total: ~4-5 hours per module**

**15 Remaining Modules:**

- Simple modules (7): ~28 hours
- Complex modules (5): ~30 hours
- Unique modules (3): ~20 hours
- **Total: ~78 hours** (about 2 weeks at 8 hours/day)

------

## FINAL CHECKLIST: BEFORE STARTING NEXT MODULE

- [ ] StatusReports fully complete and tested
- [ ] All StatusReports tests passing
- [ ] Git repository clean and committed
- [ ] Module spec document prepared
- [ ] AI prompt drafted
- [ ] Tracker updated
- [ ] Fresh coffee/tea ready

------

**Good luck with your implementation! You've got a solid pattern to follow.**