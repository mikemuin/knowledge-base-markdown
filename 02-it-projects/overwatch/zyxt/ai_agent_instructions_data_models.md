# AI Agent Instructions: Data Models

## Overview

This document defines critical data model rules, constraints, and implementation patterns for the Overwatch PMO Governance Suite. These rules enforce data integrity, immutability, and the "human judgment first" philosophy.

**Read this in conjunction with:** `AGENT_Instructions__Folder_Structure.md`

---

## 1. Auto-Computed Fields (Read-Only)

The following fields are **automatically computed** by the system and **NEVER accepted as direct user input**:

### target_week (ISO 8601 Week)
**Applies to:** `milestones`, `action_items`, `deliverables`, `dependencies`, `meeting_events`

**Computation:**
```php
$entity->target_week = Carbon::parse($entity->target_date)->format('o-\WW');
// Result: "2026-W04"
```

**Implementation:**
- Trigger computation in Model Observer `creating` and `updating` events
- Recompute whenever `target_date` changes
- Validation: Always ensure `target_date` exists before computing

**Display Transformation:**
```php
// Stored: "2026-W04"
// Display: "Week 4 of January"

[$year, $week] = explode('-W', $entity->target_week);
$date = Carbon::now()->setISODate($year, $week);
return "Week {$date->weekOfMonth} of {$date->format('F')}";
```

---

## 2. Immutability Rules (Hard Enforcement)

Certain entities and fields become **immutable** after specific lifecycle events. Implement enforcement at the Model level using Laravel Observers.

### A. Notes (Universal Commentary)
**Table:** `notes`

**Rule:** Once created, records **CANNOT** be edited or deleted (including soft-delete).

**Implementation:**
```php
// In NoteObserver
public function updating(Note $note)
{
    throw new \Exception('Notes are immutable and cannot be modified.');
}

public function deleting(Note $note)
{
    throw new \Exception('Notes are immutable and cannot be deleted.');
}
```

**Rationale:** Permanent audit trail of who said what and when.

---

### B. Status Reports
**Table:** `status_reports`

**Rule:** Once `published_at` is set (not null), **ALL fields become immutable**.

**Implementation:**
```php
// In StatusReportObserver
public function updating(StatusReport $report)
{
    if ($report->published_at !== null && $report->isDirty()) {
        throw new \Exception('Published reports are immutable.');
    }
}

public function deleting(StatusReport $report)
{
    if ($report->published_at !== null) {
        throw new \Exception('Published reports cannot be deleted.');
    }
}
```

**Snapshot Trigger:**
When `published_at` is set, capture snapshot data:
```php
$report->snapshot_data = [
    'milestone_status' => $this->getMilestoneStatusCounts($report->project_id),
    'risk_counts' => $this->getRiskCountsBySeverity($report->project_id),
    'issue_counts' => $this->getIssueCountsByImpact($report->project_id),
    'change_request_counts' => $this->getChangeRequestCounts($report->project_id),
];
```

**Note:** Do NOT include financial data in snapshots (V1 excludes financials).

---

### C. Decisions
**Table:** `decisions`

**Rule:** Once `is_locked = true`, **ALL fields become immutable**.

**Implementation:**
```php
// In DecisionObserver
public function updating(Decision $decision)
{
    if ($decision->is_locked && $decision->isDirty()) {
        throw new \Exception('Locked decisions are immutable.');
    }
}

// Allow soft-delete (sets deleted_at = true)
// But prevent hard delete
public function forceDeleting(Decision $decision)
{
    throw new \Exception('Decisions cannot be permanently deleted.');
}
```

**Superseding Logic:**
When creating a new decision that supersedes an old one:
```php
// In DecisionService
public function supersede(Decision $oldDecision, CreateDecisionData $data): Decision
{
    $newDecision = $this->create($data);

    $oldDecision->update([
        'superseded_by' => $newDecision->id,
        'is_locked' => true, // Lock the old decision
    ]);

    return $newDecision;
}
```

---

### D. Audit Log Entries
**Implementation:** `spatie/laravel-activitylog` or custom `audit_logs` table

**Rule:** Audit entries are **ALWAYS immutable**.

**Configuration:**
```php
// config/activitylog.php
'delete_records_older_than_days' => 0, // Never delete
```

---

## 3. Soft-Delete Architecture

**Mechanism:** Use `deleted_at` **Boolean flag** (not timestamp).

**Implementation:**
```php
// Migration
$table->boolean('deleted_at')->default(false);

// Model
use Illuminate\Database\Eloquent\SoftDeletes;

protected $dates = ['deleted_at'];
protected $casts = ['deleted_at' => 'boolean'];

// Override SoftDeletes behavior to use boolean
public function runSoftDelete()
{
    $this->{$this->getDeletedAtColumn()} = true;
    return $this->save();
}

public function restore()
{
    $this->{$this->getDeletedAtColumn()} = false;
    return $this->save();
}
```

**Global Scope:**
```php
// Apply to all models
protected static function booted()
{
    static::addGlobalScope('active', function ($query) {
        $query->where('deleted_at', false);
    });
}

// Query archived records explicitly
Model::withoutGlobalScope('active')->where('deleted_at', true)->get();
```

**Applies to:** All entities except `notes` (which cannot be deleted at all).

---

## 4. Metadata Fields Standard

**Every table** includes these metadata fields:

```php
// Migration
$table->timestamp('created_at');
$table->foreignId('created_by')->constrained('users');
$table->timestamp('updated_at');
$table->foreignId('updated_by')->constrained('users');
```

**Auto-Population via Model Observer:**
```php
// In BaseModelObserver (applied to all models)
public function creating(Model $model)
{
    $model->created_by = auth()->id();
    $model->updated_by = auth()->id();
}

public function updating(Model $model)
{
    $model->updated_by = auth()->id();
}
```

---

## 5. Enum Values (Use Exact Strings)

**Critical:** Do NOT create custom enum values. Use ONLY the values specified below.

### Project Status
```php
enum ProjectStatus: string
{
    case DRAFT = 'draft';
    case ACTIVE = 'active';
    case ON_HOLD = 'on_hold';
    case COMPLETED = 'completed';
    case CANCELLED = 'cancelled';
}
```

### RAG Status (Manual Selection)
```php
enum RagStatus: string
{
    case RED = 'red';
    case AMBER = 'amber';
    case GREEN = 'green';
}
```

### Execution Entities (Milestones, Action Items, Deliverables, Dependencies, Meeting Events)
```php
enum ExecutionStatus: string
{
    case PENDING = 'pending';
    case ON_TRACK = 'on_track';
    case AT_RISK = 'at_risk';
    case DELAYED = 'delayed';
    case BLOCKED = 'blocked'; // Not applicable to Milestones
    case COMPLETED = 'completed';
    case CANCELLED = 'cancelled';
}
```

### Risk Status
```php
enum RiskStatus: string
{
    case IDENTIFIED = 'identified';
    case ASSESSED = 'assessed';
    case MITIGATING = 'mitigating';
    case MONITORING = 'monitoring';
    case CLOSED = 'closed';
    case REALIZED = 'realized';
}
```

### Issue Status
```php
enum IssueStatus: string
{
    case IDENTIFIED = 'identified';
    case UNDER_REVIEW = 'under_review';
    case ONGOING_RESOLUTION = 'ongoing_resolution';
    case ESCALATED = 'escalated';
    case MONITORING = 'monitoring';
    case CLOSED = 'closed';
}
```

### Change Request Status
```php
enum ChangeRequestStatus: string
{
    case DRAFT = 'draft';
    case SUBMITTED = 'submitted';
    case UNDER_REVIEW = 'under_review';
    case APPROVED = 'approved';
    case REJECTED = 'rejected';
    case IMPLEMENTED = 'implemented';
    case CANCELLED = 'cancelled';
}
```

### Risk Severity (Manual Assessment)
```php
enum RiskSeverity: string
{
    case LOW = 'low';
    case MEDIUM = 'medium';
    case HIGH = 'high';
    case CRITICAL = 'critical';
}
```

### Issue Priority (Manual Setting)
```php
enum IssuePriority: string
{
    case LOW = 'low';
    case MEDIUM = 'medium';
    case HIGH = 'high';
    case CRITICAL = 'critical';
}
```

**Storage:** Store as `string` in database, cast to enum in models:
```php
protected $casts = [
    'status' => ProjectStatus::class,
    'rag_status' => RagStatus::class,
];
```

---

## 6. Nullable Foreign Keys

The following foreign keys are **optional** (nullable):

### Group Assignments
- `milestone_group_id`
- `aitem_group_id`
- `deliverable_group_id`
- `dependency_group_id`
- `meeting_group_id`
- `risk_group_id`
- `issue_group_id`
- `link_group_id`
- `list_group_id`

### Hierarchical Structures
- `parent_id` (on all `*_groups` tables)

### Assignments and Ownership
- `assigned_to` (action_items, deliverables)
- `owner_id` (risks, issues)
- `escalated_to` (issues)
- `approver_id` (deliverables)
- `approved_by` (change_requests)

### Decision Linking
- `superseded_by` (decisions)

### Meeting Linking
- `meeting_id` (meeting_minutes - can exist independently)

**Migration Pattern:**
```php
$table->foreignId('milestone_group_id')->nullable()->constrained('milestone_groups');
```

---

## 7. Required Fields (NOT NULL Constraints)

### Universal Requirements
- `project_id` (all tables except `projects`, `users`, `notes`)
- `title` (all primary entities)

### Date-Driven Entities
- `target_date` (action_items, deliverables, milestones, dependencies, meeting_events)

### Decisions
- `decision` (text, markdown)
- `rationale` (text, markdown)
- `decision_maker_id` (FK to users)
- `decision_date` (date)

### Change Requests
- `justification` (text, markdown)
- `impact_assessment` (text, markdown)

### Status Reports
- `report_period_start` (date)
- `report_period_end` (date)
- `overall_status` (enum: red/amber/green)

**Validation:** Enforce at both database level (`NOT NULL`) and application level (FormRequest validation).

---

## 8. Polymorphic Relationships

### A. Notes (Universal Commentary)
**Attach to ANY entity:**

```php
// Migration
Schema::create('notes', function (Blueprint $table) {
    $table->id();
    $table->morphs('notable'); // Creates notable_type, notable_id
    $table->text('content');
    $table->boolean('is_system_generated')->default(false);
    $table->timestamp('created_at');
    $table->foreignId('created_by')->constrained('users');
});

// Model
class Note extends Model
{
    public function notable()
    {
        return $this->morphTo();
    }
}

// Usage in any model
class Milestone extends Model
{
    public function notes()
    {
        return $this->morphMany(Note::class, 'notable');
    }
}
```

---

### B. Entity Links (Limited Scope)
**V1 Scope:** Only ProjectJournal entries can link to other entities.

```php
// Migration
Schema::create('entity_links', function (Blueprint $table) {
    $table->id();
    $table->string('source_type'); // Always 'JournalEntry'
    $table->foreignId('source_id'); // Journal Entry ID
    $table->morphs('target'); // Creates target_type, target_id
    $table->enum('link_type', ['references', 'relates_to', 'announces']);
    $table->text('description')->nullable();
    $table->timestamp('created_at');
    $table->foreignId('created_by')->constrained('users');
});

// Model
class EntityLink extends Model
{
    public function source()
    {
        return $this->belongsTo(JournalEntry::class, 'source_id');
    }

    public function target()
    {
        return $this->morphTo();
    }
}

// Usage
class JournalEntry extends Model
{
    public function linkedEntities()
    {
        return $this->hasMany(EntityLink::class, 'source_id')
                    ->where('source_type', 'JournalEntry');
    }
}
```

**Note:** Meeting Minutes → Meeting Event linking uses direct FK (`meeting_id`), not this table.

---

## 9. Grouping Hierarchy Validation

**Rule:** Maximum 2-level hierarchy (Parent Group → Child Group → Items).

**Constraint:** If a group has a `parent_id`, it cannot itself be a parent.

**Implementation:**
```php
// In GroupService
public function create(CreateGroupData $data): Group
{
    if ($data->parent_id) {
        $parent = Group::findOrFail($data->parent_id);

        if ($parent->parent_id !== null) {
            throw new \Exception('Maximum 2-level hierarchy exceeded. Cannot create child of a child group.');
        }
    }

    return Group::create($data->toArray());
}

// Database constraint (optional)
// In migration
$table->foreignId('parent_id')->nullable()->constrained('milestone_groups');
$table->unique(['id', 'parent_id'], 'prevent_third_level');

// Trigger or check constraint
DB::statement('
    CREATE TRIGGER prevent_third_level_hierarchy
    BEFORE INSERT ON milestone_groups
    FOR EACH ROW
    BEGIN
        IF NEW.parent_id IS NOT NULL THEN
            IF (SELECT parent_id FROM milestone_groups WHERE id = NEW.parent_id) IS NOT NULL THEN
                SIGNAL SQLSTATE "45000" SET MESSAGE_TEXT = "Maximum 2-level hierarchy";
            END IF;
        END IF;
    END
');
```

---

## 10. Query Optimization & Indexing

### Required Indexes

**All Foreign Keys:**
```php
$table->foreignId('project_id')->constrained()->index();
```

**Composite Indexes:**
```php
// All primary entities
$table->index(['project_id', 'status', 'deleted_at']);

// Date-driven entities
$table->index(['project_id', 'target_week']);

// Visibility filtering
$table->index(['project_id', 'is_internal_only']);
```

**Full-Text Search:**
```php
// Markdown content fields
$table->text('description');
$table->fullText('description');
```

**Unique Constraints:**
```php
// Projects
$table->string('code')->unique();

// Users
$table->string('email')->unique();
```

---

## 11. Temporal Query Patterns

### ISO 8601 Week-Based Queries

**Pattern:** Get incomplete items from past + items within target week range.

#### Action Items, Deliverables, Dependencies, Meeting Events
**Window:** Past + 2 weeks ahead

```php
// Model Scope
public function scopeInActiveWindow($query)
{
    $currentWeek = now()->format('o-\WW');
    $futureWeeks = collect(range(0, 2))->map(fn($i) =>
        now()->addWeeks($i)->format('o-\WW')
    )->toArray();

    return $query->where(function($q) use ($currentWeek, $futureWeeks) {
        $q->whereNotIn('status', ['completed', 'cancelled'])
          ->where(function($q2) use ($currentWeek, $futureWeeks) {
              $q2->where('target_week', '<', $currentWeek)
                 ->orWhereIn('target_week', $futureWeeks);
          });
    });
}
```

#### Milestones
**Window:** Past + 4 weeks ahead

```php
public function scopeInActiveWindow($query)
{
    $currentWeek = now()->format('o-\WW');
    $futureWeeks = collect(range(0, 4))->map(fn($i) =>
        now()->addWeeks($i)->format('o-\WW')
    )->toArray();

    return $query->where(function($q) use ($currentWeek, $futureWeeks) {
        $q->whereNotIn('status', ['completed', 'cancelled'])
          ->where(function($q2) use ($currentWeek, $futureWeeks) {
              $q2->where('target_week', '<', $currentWeek)
                 ->orWhereIn('target_week', $futureWeeks);
          });
    });
}
```

---

### Recent Activity Queries

**Notes/Comments:** Last 2 weeks OR most recent 4

```php
public function scopeRecentActivity($query)
{
    $twoWeeksAgo = now()->subWeeks(2);

    $recentNotes = $query->where('created_at', '>=', $twoWeeksAgo)->get();

    if ($recentNotes->count() >= 4) {
        return $recentNotes;
    }

    // Fallback: Get 4 most recent
    return $query->orderByDesc('created_at')->limit(4)->get();
}
```

---

## 12. Snapshot Technology (Status Reports)

When `published_at` is set:

**Capture snapshot data:**
```php
// In StatusReportService
public function publish(StatusReport $report): StatusReport
{
    $snapshot = [
        'milestone_status' => Milestone::where('project_id', $report->project_id)
            ->selectRaw('status, COUNT(*) as count')
            ->groupBy('status')
            ->pluck('count', 'status')
            ->toArray(),

        'risk_counts' => Risk::where('project_id', $report->project_id)
            ->where('status', '!=', 'closed')
            ->selectRaw('severity, COUNT(*) as count')
            ->groupBy('severity')
            ->pluck('count', 'severity')
            ->toArray(),

        'issue_counts' => Issue::where('project_id', $report->project_id)
            ->where('status', '!=', 'closed')
            ->selectRaw('priority, COUNT(*) as count')
            ->groupBy('priority')
            ->pluck('count', 'priority')
            ->toArray(),

        'change_request_counts' => ChangeRequest::where('project_id', $report->project_id)
            ->selectRaw('status, COUNT(*) as count')
            ->groupBy('status')
            ->pluck('count', 'status')
            ->toArray(),
    ];

    $report->update([
        'snapshot_data' => $snapshot,
        'published_at' => now(),
        'published_by' => auth()->id(),
        'is_draft' => false,
    ]);

    event(new StatusReportPublished($report));

    return $report->fresh();
}
```

**Important:** Do NOT include financial data (V1 excludes budgets and spend).

---

## 13. JSON Field Structures

### Snapshot Data (status_reports.snapshot_data)
```json
{
  "milestone_status": {
    "completed": 5,
    "on_track": 3,
    "at_risk": 2,
    "delayed": 1
  },
  "risk_counts": {
    "critical": 1,
    "high": 2,
    "medium": 5,
    "low": 8
  },
  "issue_counts": {
    "critical": 1,
    "high": 3,
    "medium": 2,
    "low": 1
  },
  "change_request_counts": {
    "approved": 2,
    "under_review": 1,
    "implemented": 3
  }
}
```

### Attendees (meeting_minutes.attendees)
```json
[
  {"user_id": 1, "name": "John Doe", "attended": true},
  {"user_id": 2, "name": "Jane Smith", "attended": false},
  {"user_id": 3, "name": "Bob Wilson", "attended": true}
]
```

**Casting in Models:**
```php
protected $casts = [
    'snapshot_data' => 'array',
    'attendees' => 'array',
];
```

---

## 14. Change Request Workflow

### Status Progression
`draft` → `submitted` → `under_review` → `approved`/`rejected` → `implemented`

### When Approved
**Manual Process:** PM manually updates affected entities.

**V1 Scope:** Only Scope and Schedule changes (no budget).

**Implementation:**
```php
// In ChangeRequestService
public function approve(ChangeRequest $cr, ApproveChangeRequestData $data): ChangeRequest
{
    $cr->update([
        'status' => ChangeRequestStatus::APPROVED,
        'approved_by' => auth()->id(),
        'approved_at' => now(),
        'approval_notes' => $data->approval_notes,
    ]);

    // Log the approval
    event(new ChangeRequestApproved($cr));

    // NOTE: PM must manually update:
    // - project.target_end_date (if schedule impact)
    // - affected milestones (if schedule impact)
    // - affected deliverables (if scope impact)

    return $cr;
}
```

**No automatic cascade updates** - PM judgment required.

---

## 15. Markdown Fields

The following fields store **markdown content**:

### Universal
- `description` (most entities)
- `content` (notes, project_journal)

### Status Reports
- `executive_summary`
- `achievements`
- `challenges`
- `next_period_focus`

### Risks
- `mitigation_strategy`
- `contingency_plan`

### Issues
- `resolution_summary`

### Change Requests
- `justification`
- `impact_assessment`

### Decisions
- `context`
- `decision`
- `rationale`
- `alternatives_considered`
- `implications`

**Rendering:**
Use a markdown parser (e.g., `league/commonmark`):
```php
// In ViewModel or Blade
use League\CommonMark\CommonMarkConverter;

$converter = new CommonMarkConverter();
$html = $converter->convert($model->description);
```

---

## 16. Migration Creation Order

**Follow this sequence** to avoid foreign key constraint errors:

1. `users`
2. `projects`
3. All group tables (`milestone_groups`, `aitem_groups`, etc.)
4. All primary entity tables (in any order):
   - `status_reports`
   - `milestones`
   - `action_items`
   - `deliverables`
   - `dependencies`
   - `meeting_events`
   - `risks`
   - `issues`
   - `change_requests`
   - `decisions`
   - `project_journal`
   - `meeting_minutes`
   - `stakeholders`
   - `links`
   - `lists`, `list_items`
   - `lessons_learned`
5. `notes` (polymorphic)
6. `entity_links` (polymorphic)
7. `activity_log` or custom `audit_logs`

---

## 17. Model Events to Implement

### Creating Event
**Triggers:**
- Set `created_by` and `updated_by` to `auth()->id()`
- Compute `target_week` from `target_date` (if applicable)

```php
public function creating(Model $model)
{
    $model->created_by = auth()->id();
    $model->updated_by = auth()->id();

    if ($model->target_date && empty($model->target_week)) {
        $model->target_week = Carbon::parse($model->target_date)->format('o-\WW');
    }
}
```

### Updating Event
**Triggers:**
- Set `updated_by` to `auth()->id()`
- Recompute `target_week` if `target_date` changed
- Enforce immutability rules (Notes, published Status Reports, locked Decisions)

```php
public function updating(Model $model)
{
    $model->updated_by = auth()->id();

    if ($model->isDirty('target_date')) {
        $model->target_week = Carbon::parse($model->target_date)->format('o-\WW');
    }
}
```

### Deleting Event
**Triggers:**
- Block hard deletes for Notes
- Prevent deletion of published Status Reports and locked Decisions

```php
public function deleting(Model $model)
{
    if ($model instanceof Note) {
        throw new \Exception('Notes cannot be deleted.');
    }

    if ($model instanceof StatusReport && $model->published_at) {
        throw new \Exception('Published reports cannot be deleted.');
    }

    if ($model instanceof Decision && $model->is_locked) {
        throw new \Exception('Locked decisions cannot be deleted.');
    }
}
```

---

## 18. Visibility Control (is_internal_only)

**Applies to:** Most entities (check data model for specific entities).

**Rule:** Filter based on user role:
- **Admin:** See all items
- **Editor:** See all items on assigned projects
- **Viewer:** See only items where `is_internal_only = false`

**Implementation:**
```php
// Model Scope
public function scopeVisibleTo($query, User $user)
{
    if ($user->role === 'admin' || $user->role === 'editor') {
        return $query;
    }

    // Viewer sees only public items
    return $query->where('is_internal_only', false);
}

// Usage in Controller
$risks = Risk::where('project_id', $projectId)
    ->visibleTo(auth()->user())
    ->get();
```

---

## 19. Manual Control Fields (No Auto-Computation)

### RAG Status
**Field:** `rag_status` on `projects` and `status_reports`

**Rule:** **Manually selected by PM** from dropdown. Never auto-computed.

**Validation:**
```php
// FormRequest
public function rules(): array
{
    return [
        'rag_status' => ['required', Rule::enum(RagStatus::class)],
    ];
}
```

### Risk Severity
**Field:** `severity` on `risks`

**Rule:** **Manually assessed by PM**. No auto-computation from probability × impact.

**Note:** `probability` and `impact` fields may exist as reference, but severity is independent.

### Issue Priority
**Field:** `priority` on `issues`

**Rule:** **Manually set by PM**. No auto-computation from impact + urgency.

**Note:** `impact` and `urgency` fields may exist as reference, but priority is independent.

### Action Item Priority
**Field:** `priority` on `action_items`

**Rule:** **Manually set by PM** based on full context.

---

## 20. Critical Path Flagging (Milestones)

**Field:** `is_critical_path` on `milestones`

**Rule:** Manual checkbox set by PM for visual highlighting and filtering.

**Purpose:**
- Visual indicator in UI (bold, color coding, badge)
- Enables filtering: "Show only critical path milestones"

**NOT used for:**
- Auto-calculation of project delays
- Triggering alerts or notifications
- Affecting project RAG status

**Implementation:**
```php
// Model Scope
public function scopeCriticalPath($query)
{
    return $query->where('is_critical_path', true);
}

// Usage
$criticalMilestones = Milestone::where('project_id', $projectId)
    ->criticalPath()
    ->inActiveWindow()
    ->get();
```

---

## 21. Stale Report Detection (Portfolio Dashboard)

**Rule:** Flag projects without a Status Report published in >30 days.

**Implementation:**
```php
// In Project Model
public function scopeStaleReports($query)
{
    $thirtyDaysAgo = now()->subDays(30);

    return $query->whereDoesntHave('statusReports', function($q) use ($thirtyDaysAgo) {
        $q->whereNotNull('published_at')
          ->where('published_at', '>=', $thirtyDaysAgo);
    });
}

// Usage in PortfolioDashboard
$staleProjects = Project::staleReports()->get();
```

**Display:** Show warning icon or gray dot on Portfolio Dashboard.

---

## 22. Decision Superseding

**Pattern:** New decision replaces old decision with linkage.

**Implementation:**
```php
// When creating a superseding decision
public function create(CreateDecisionData $data, ?int $supersedesDecisionId = null): Decision
{
    $decision = Decision::create($data->toArray());

    if ($supersedesDecisionId) {
        $oldDecision = Decision::findOrFail($supersedesDecisionId);

        $oldDecision->update([
            'superseded_by' => $decision->id,
            'is_locked' => true,
        ]);

        event(new DecisionSuperseded($oldDecision, $decision));
    }

    return $decision;
}
```

**Display:** Show linkage in UI with "Superseded by Decision #X" badge.

---

## 23. Meeting Minutes → Meeting Event Linking

**Relationship:** Direct FK, not polymorphic `entity_links`.

```php
// Migration
Schema::create('meeting_minutes', function (Blueprint $table) {
    $table->id();
    $table->foreignId('project_id')->constrained();
    $table->foreignId('meeting_id')->nullable()->constrained('meeting_events');
    // ...
});

// Model
class MeetingMinute extends Model
{
    public function meetingEvent()
    {
        return $this->belongsTo(MeetingEvent::class, 'meeting_id');
    }
}

class MeetingEvent extends Model
{
    public function minutes()
    {
        return $this->hasMany(MeetingMinute::class, 'meeting_id');
    }
}
```

---

## 24. List Items (Checklists)

**Pattern:** Parent-child relationship with completion tracking.

```php
// Migration - Lists
Schema::create('lists', function (Blueprint $table) {
    $table->id();
    $table->foreignId('project_id')->constrained();
    $table->foreignId('list_group_id')->nullable()->constrained();
    $table->string('title');
    $table->text('description')->nullable();
    $table->integer('position')->default(0);
    $table->boolean('is_internal_only')->default(false);
    $table->boolean('deleted_at')->default(false);
    // metadata
});

// Migration - List Items
Schema::create('list_items', function (Blueprint $table) {
    $table->id();
    $table->foreignId('list_id')->constrained()->onDelete('cascade');
    $table->text('content');
    $table->integer('position')->default(0);
    $table->boolean('is_checked')->default(false);
    $table->timestamp('checked_at')->nullable();
    $table->foreignId('checked_by')->nullable()->constrained('users');
    // metadata
});

// Model
class ListItem extends Model
{
    public function markAsChecked(User $user)
    {
        $this->update([
            'is_checked' => true,
            'checked_at' => now(),
            'checked_by' => $user->id,
        ]);
    }
}
```

---

## 25. Validation Summary

### Required Business Logic Validation

**Grouping Hierarchy:**
```php
// In GroupService
if ($parent && $parent->parent_id !== null) {
    throw ValidationException::withMessages([
        'parent_id' => 'Maximum 2-level hierarchy exceeded.'
    ]);
}
```

**Immutability:**
```php
// In Observer
if ($model->published_at && $model->isDirty()) {
    throw new \Exception('This record is locked and cannot be modified.');
}
```

**Enum Values:**
```php
// FormRequest
public function rules(): array
{
    return [
        'status' => ['required', Rule::enum(ExecutionStatus::class)],
        'rag_status' => ['required', Rule::enum(RagStatus::class)],
    ];
}
```

**Required Fields:**
```php
public function rules(): array
{
    return [
        'project_id' => ['required', 'exists:projects,id'],
        'title' => ['required', 'string', 'max:255'],
        'target_date' => ['required', 'date'],
    ];
}
```

---

## 26. System Configuration

### Activity Log Configuration
```php
// config/activitylog.php
return [
    'enabled' => true,
    'subject_returns_soft_deleted_models' => true,
    'log_name' => 'default',
    'delete_records_older_than_days' => 0, // Never delete
];
```

### Database Connection (Production)
**Primary:** MySQL (production-grade, robust)
**Local:** SQLite (high-performance local development)

---

## Summary of Critical Rules

1. **Auto-compute `target_week` only** - no other computed fields
2. **Enforce immutability** for Notes, published Status Reports, locked Decisions
3. Use Laravel's default `deleted_at` timestamp approach
4. **Metadata fields on all tables** with auto-population
5. **Use exact enum values** - no custom strings
6. **Respect nullable constraints** on optional FKs
7. **Enforce 2-level grouping maximum**
8. **Apply polymorphic relationships correctly** (Notes, EntityLinks)
9. **Index strategically** for query performance
10. **Use ISO 8601 weeks** for temporal queries
11. **Capture snapshots** on Status Report publication (no financials)
12. **No auto-computation** of RAG status, risk severity, issue priority
13. **Manual PM control** is the core philosophy - trust human judgment
14. **Visibility filtering** based on `is_internal_only` and user role
15. **Markdown rendering** for all content fields

---

**Remember:** This system captures PM decisions; it does not make decisions. When in doubt, prefer manual control over automation.
