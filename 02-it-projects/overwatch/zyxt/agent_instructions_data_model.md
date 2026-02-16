# AGENT Instructions: Data Model

Based on the data models discussed above, here are the specific instructions that should be added to an AGENTS.md file:

## Critical Data Model Rules

### 1. Auto-Computed Fields - NEVER Accept User Input
The following fields MUST be computed automatically by the application and NEVER accepted as direct user input:

- **`target_week`**: Always computed from `target_date` using ISO 8601 format (`Carbon::parse($targetDate)->format('o-\WW')`)
- **`risk_score`**: Always computed as `probability × impact` (use numeric values 1-5 for probability and impact enums)
- **`variance`**: Always computed as `approved_budget - actual_spend`
- **`variance_percentage`**: Always computed as `((approved_budget - actual_spend) / approved_budget) * 100`
- **`priority`** (in issues): May be computed from `impact + urgency` or using custom business logic

### 2. Immutability Rules
Certain entities and fields are IMMUTABLE after creation or publication. Implement hard blocks at the model level:

- **Notes table**: Once created, records CANNOT be edited or deleted (not even soft-deleted)
- **Status Reports**: Once `published_at` is set, ALL fields become immutable
- **Decisions**: Once `is_locked = true`, ALL fields become immutable
- **Audit log entries**: ALWAYS immutable

Implementation: Use Laravel model events (`creating`, `updating`) to enforce these rules and throw exceptions if immutability is violated.

### 3. ISO 8601 Week Format
When storing `target_week` or `scheduled_week`, ALWAYS use the format: `YYYY-WWW` (e.g., "2025-W04")

```php
// Correct implementation
$model->target_week = Carbon::parse($model->target_date)->format('o-\WW');
```

### 4. Soft-Delete Architecture
ALL entities use soft deletes via the `is_active` flag (NOT Laravel's `deleted_at` timestamp):

- Set `is_active = false` instead of hard deleting
- Always scope queries to `where('is_active', true)` unless explicitly querying archived records
- Implement a global scope on all models to filter inactive records by default

### 5. Polymorphic Relationships
The following tables use polymorphic relationships:

- **`notes`**: `notable_type` and `notable_id` (can belong to ANY entity)
- **`entity_links`**: `source_type`, `source_id`, `target_type`, `target_id` (links any two entities)

Use Laravel's `morphTo()` and `morphMany()` relationships.

### 6. Metadata Fields Standard
ALL tables include these metadata fields:
- `created_at` (timestamp)
- `created_by` (foreign key to users table)
- `updated_at` (timestamp)
- `updated_by` (foreign key to users table)

Use model events to automatically populate `created_by` and `updated_by` with the authenticated user's ID.

### 7. Status Enum Values - Use Exact Strings
Do NOT create custom status values. Use ONLY the enum values specified in the data model:

**Action Items, Deliverables, Milestones, Meetings, Dependencies:**
- `pending`, `on_track`, `at_risk`, `delayed`, `blocked`, `completed`, `cancelled`

**Issues:**
- `identified`, `under_review`, `ongoing_resolution`, `escalated`, `monitoring`, `closed`

**Risks:**
- `identified`, `assessed`, `mitigating`, `monitoring`, `closed`, `realized`

**Change Requests:**
- `draft`, `submitted`, `under_review`, `approved`, `rejected`, `implemented`, `cancelled`

**Projects:**
- `draft`, `active`, `on_hold`, `completed`, `cancelled`

**RAG Status:**
- `red`, `amber`, `green`

### 8. Nullable Foreign Keys
The following foreign keys are NULLABLE (optional relationships):
- All `*_group_id` fields (group assignment is optional)
- `parent_id` (for hierarchical structures)
- `assigned_to` (action items)
- `owner_id` (risks, issues)
- `escalated_to` (issues)
- `approved_by` (deliverables)
- `reviewed_by` (change requests)
- `superseded_by` (decisions)
- `meeting_id` (meeting minutes can exist independently)

### 9. Required Fields Validation
The following fields are ALWAYS required and must have database-level `NOT NULL` constraints:

- `project_id` (on all tables except projects and users)
- `title` (on all primary entities)
- `target_date` (on: action_items, deliverables, milestones, meetings, dependencies)
- `decision`, `rationale`, `decision_maker_id`, `decision_date` (on decisions)
- `justification`, `impact_assessment` (on change_requests)

### 10. Special Query Patterns
Implement these as model scopes or repository methods:

**Updates:**
- Get all from last 2 weeks OR if none exist, get 4 most recent

**Action Items, Deliverables, Dependencies, Meetings:**
- Get all incomplete (status NOT `completed` OR `cancelled`) from the past PLUS items within target week range (current week + 2 weeks ahead)

**Milestones:**
- Get all incomplete from the past PLUS items within target week range (current week + 4 weeks ahead)

**Use ISO 8601 week comparison:**
```php
// Example scope
public function scopeInActiveWindow($query) {
    $currentWeek = now()->format('o-\WW');
    $futureWeeks = collect(range(0, 2))->map(fn($i) =>
        now()->addWeeks($i)->format('o-\WW')
    );

    return $query->where(function($q) use ($currentWeek, $futureWeeks) {
        $q->whereNotIn('status', ['completed', 'cancelled'])
          ->where(fn($q2) =>
              $q2->where('target_week', '<', $currentWeek)
                 ->orWhereIn('target_week', $futureWeeks)
          );
    });
}
```

### 11. Snapshot Technology (Status Reports)
When `published_at` is set on a status report:
1. Capture ALL relevant metrics in the `snapshot_data` JSON field:
   - Budget variance
   - Milestone status counts
   - Risk counts by severity
   - Issue counts by impact
   - Action item completion rate
2. Lock ALL fields for editing
3. This is a ONE-WAY operation (cannot be unpublished)

### 12. Cascading Updates (Change Requests)
When a change request is approved (`status = 'approved'`):
- If `budget_impact` is set, update the project's `approved_budget` in the financials table
- If `schedule_impact_days` is set, consider updating dependent milestones
- Log these changes in the audit trail

### 13. Index Requirements
Create database indexes on:
- All foreign keys
- Composite index: `(project_id, status, is_active)` on all primary entities
- `target_week` on all date-driven entities
- Full-text index on `content`, `description` fields where markdown is stored
- `email` (unique) on users table

### 14. JSON Field Structure
**Snapshot Data (status_reports.snapshot_data):**
```json
{
  "budget_variance": -15000.50,
  "milestone_status": {"completed": 5, "on_track": 3, "at_risk": 2},
  "risk_counts": {"high": 2, "medium": 5, "low": 8},
  "issue_counts": {"critical": 1, "high": 3, "medium": 2},
  "action_completion_rate": 0.78
}
```

**Attendees (meeting_minutes.attendees):**
```json
[
  {"user_id": 1, "name": "John Doe", "attended": true},
  {"user_id": 2, "name": "Jane Smith", "attended": false}
]
```

### 15. Business Logic Enforcement
Implement these rules at the model or service layer:

- **RAG Status Calculation**: Derive from downstream metrics (risks, issues, milestones, budget variance)
- **Risk Score**: Always recalculate when probability or impact changes
- **Meeting Flags**: If a critical meeting (`meeting_type = 'steering_committee'`) is missed, flag the project
- **Decision Superseding**: When creating a new decision that supersedes an old one, set `status = 'superseded'` on the old decision and set its `superseded_by` field

### 16. Markdown Fields
The following fields store markdown content and should be rendered with a markdown parser:
- `content`, `description` (on most entities)
- `executive_summary`, `achievements`, `challenges` (status_reports)
- `mitigation_strategy`, `contingency_plan` (risks)
- `resolution_summary` (issues)
- `justification`, `impact_assessment` (change_requests)
- `context`, `decision`, `rationale`, `alternatives_considered`, `implications` (decisions)

### 17. User Role-Based Access Control
Implement these access rules:

- **PMO Lead**: Full CRUD on ALL projects
- **Project Manager**: Full CRUD only on projects where `project_manager_id = current_user.id`
- **Executive Stakeholder**: Read-only access to dashboards, published reports, and non-internal items
- **Team Member**: Limited access based on assignments (`assigned_to`, `owner_id`)

Filter by `is_internal_only = false` for Executive Stakeholder views.

### 18. Migration Order
Create migrations in this exact order to avoid foreign key constraint errors:

1. users
2. projects
3. All group tables (*_groups)
4. All primary entity tables (risks, issues, milestones, etc.)
5. notes (polymorphic)
6. entity_links (polymorphic)
7. Audit/activity log table

### 19. Model Events to Implement
Use Laravel model events for:

- **`creating`**: Set `created_by`, compute `target_week`, compute `risk_score`
- **`updating`**: Set `updated_by`, recompute `target_week` if `target_date` changed, enforce immutability rules
- **`deleting`**: Block hard deletes, set `is_active = false` instead
- **`saving`**: Validate enum values, validate required fields

### 20. Do NOT Use Laravel's SoftDeletes Trait
Use `is_active` boolean flag instead. Do NOT add `deleted_at` timestamp columns.