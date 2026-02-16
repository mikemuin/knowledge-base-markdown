# AI Agent Instructions: User Interface

Reviewed: Jan-27-2026

## Overview

This document defines UI/UX patterns, component structures, and interface implementation guidelines for the Overwatch PMO Governance Suite. The focus is on **validating functionality first**, with aesthetics and advanced features deferred to future iterations.

**Read this in conjunction with:**

- `AI_Agent_Instructions_Folder_Structure_V2.md`
- `AI_Agent_Instructions_Data_Models_V2.md`

------

## Core UI Philosophy

### V1 Priorities

1. **Functionality validation over aesthetics** - Clean, functional interfaces without heavy styling
2. **API-first development** - Build and test APIs before Web/Livewire interfaces
3. **Native Livewire + Tailwind only** - NO third-party UI libraries in V1 (Flux UI removed)
4. **Testing-friendly** - Enable all UI elements initially; apply restrictions (immutability, visibility) in later iterations
5. **Efficient patterns** - Consistent, repeatable UI patterns across all modules

------

## Triple Interface Architecture

### Development Sequence

1. **API** - Build first for testing and validation
2. **Web (Livewire)** - Build after API is validated
3. All interfaces use **Services/Repositories directly** (not API wrapping)

### Interface Responsibilities

**API (`Controllers/Api/`):**

- RESTful JSON endpoints

- Uses API Resources for response transformation

- Token-based authentication (Laravel Sanctum)

- Standard response format:

  ```json
  {  "data": { ... },  "message": "Success message",  "meta": { "pagination": { ... } }}
  ```

**Web (`Controllers/Web/`):**

- Full-page Blade views for traditional navigation
- Used for landing pages, dashboards, main layouts
- Embeds Livewire components for interactivity

**Livewire (`Livewire/`):**

- All CRUD operations
- List/table displays with filtering, sorting, pagination
- Modal-based forms
- Inline notes/comments
- Real-time interactivity without full page reloads

### Repository Pattern (Returns Query Builder)

**CRITICAL: Repository methods return `Builder`, NOT paginated results.**

**Repository Pattern in Livewire and API Controllers:**

- **Main entity queries** (Risks, Milestones, ActionItems, etc.) → Use Repository
- **Create/Update/Delete operations** → Use Service
- **Shared/polymorphic entities** (Notes, EntityLinks) → Direct Eloquent acceptable
- **Simple metadata entities** (Groups) → Direct Eloquent acceptable for simple operations
- **Code snippets** showing business logic → May show direct access for illustration

------

## Layout Structure

### Application Shell

**Top Bar:**

```
┌─────────────────────────────────────────────────────────────┐
│ [Logo] Overwatch    [Project Selector ▼]    [User: Admin ▼] │
└─────────────────────────────────────────────────────────────┘
```

Components:

- **Logo/Brand** - Links to landing page
- **Project Selector** - Dropdown to switch between projects (always visible)
- **User Menu** - Shows current user name + role, logout option

**Sidebar Navigation:**

```
┌─────────────────────┐
│ GOVERNANCE         ▼│
│   └ Status Reports  │
│                     │
│ EXECUTION          ▼│
│   └ Milestones      │
│   └ Action Items    │
│   └ Deliverables    │
│   └ Dependencies    │
│   └ Meeting Events  │
│                     │
│ CONTROL            ▼│
│   └ Risks           │
│   └ Issues          │
│   └ Change Requests │
│   └ Decisions       │
│                     │
│ KNOWLEDGE          ▼│
│   └ Project Journal │
│   └ Meeting Minutes │
│   └ Stakeholders    │
│   └ Links           │
│   └ Lists           │
│   └ Lessons Learned │
└─────────────────────┘
```

Behavior:

- **Collapsible sections** - Each domain (GOVERNANCE, EXECUTION, etc.) can expand/collapse
- **Active state** - Highlight current module
- **Fixed position** - Sidebar stays visible during scroll

**Main Content Area:**

```
┌────────────────────────────────────────────────────────────┐
│ [Breadcrumbs: Projects > Project ABC > Risks]             │
├────────────────────────────────────────────────────────────┤
│                                                            │
│   [Page Content - Livewire Components]                    │
│                                                            │
└────────────────────────────────────────────────────────────┘
```

**Breadcrumbs Format:**

- `Projects > [Project Name] > [Module] > [Detail]`
- Clickable links for navigation
- Current page is not linked (plain text)

------

## Landing Pages and Routing

### After Login Redirect Logic

```php
// In LoginController or AuthController
public function redirectAfterLogin(User $user)
{
    if ($user->role === 'admin') {
        return redirect()->route('portfolio.dashboard');
    }

    $assignedProjects = $user->projects;

    if ($assignedProjects->count() === 1) {
        $project = $assignedProjects->first();
        return redirect()->route('projects.dashboard', $project);
    }

    return redirect()->route('projects.selection');
}
```

### Route Structure

**Portfolio Dashboard (Admin only):**

- Route: `/portfolio`
- Controller: `Web/PortfolioDashboardController`
- View: `resources/views/portfolio/dashboard.blade.php`

**Project Selection (Multi-project Editors):**

- Route: `/projects`
- Controller: `Web/ProjectSelectionController`
- View: `resources/views/projects/selection.blade.php`

**Project Dashboard (Editor/Viewer):**

- Route: `/projects/{project}/dashboard`
- Controller: `Web/ProjectDashboardController`
- View: `resources/views/projects/dashboard.blade.php`
- **Note:** Skip implementation in V1 (defer to later)

**Module Routes:**

- Pattern: `/projects/{project}/{module}`
- Examples:
  - `/projects/123/risks`
  - `/projects/123/milestones`
  - `/projects/123/action-items`

------

## Dashboard Specifications

### Portfolio Dashboard (Admin Only)

**Purpose:** High-level overview of all projects in the portfolio

**Components:**

1. **Projects Table**
   - Columns:
     - Project Code
     - Project Title
     - Project Manager
     - RAG Status (color-coded badge)
     - Last Report Date
     - Stale Indicator (icon if >30 days)
   - Sorting: By project code (default), clickable column headers
   - Filtering: By RAG status, by PM
   - Pagination: 50 records per page
2. **Header Stats (Optional Simple Counts):**
   - Total Projects: X
   - Red: X | Amber: X | Green: X
   - Stale Reports: X

**Implementation:**

```
Livewire Component: app/Modules/Shared/Livewire/PortfolioDashboard.php
View: resources/views/livewire/portfolio-dashboard.blade.php
```

**No charts or widgets in V1** - Keep it a simple, functional table

### Project Dashboard

**Status:** **Deferred to V2** - Not required for V1 functionality validation

When implemented, it will show:

- Manual RAG status display
- Recent journal entries
- Upcoming milestones
- Top risks
- Open issues count

------

## CRUD Patterns (Standard Across All Modules)

### List/Index Pages

**Layout:**

```
┌────────────────────────────────────────────────────────────┐
│ [Module Title]                          [+ Create New]     │
├────────────────────────────────────────────────────────────┤
│ Filters: [Status ▼] [Date Range] [Search] [Group ▼] [Clear]│
├────────────────────────────────────────────────────────────┤
│ ┌──────┬──────────┬────────┬────────┬──────────────────┐  │
│ │ ID   │ Title    │ Status │ Date   │ Actions          │  │
│ ├──────┼──────────┼────────┼────────┼──────────────────┤  │
│ │ R-01 │ Risk A   │ High   │ W4 Jan │ [👁️] [✏️] [🗑️]   │  │
│ │ R-02 │ Risk B   │ Medium │ W5 Jan │ [👁️] [✏️] [🗑️]   │  │
│ └──────┴──────────┴────────┴────────┴──────────────────┘  │
│                                                            │
│ Showing 1-50 of 150    [◀️ 1 2 3 ... ▶️]                   │
└────────────────────────────────────────────────────────────┘
```

**Standard Filters (All Modules):**

- **Status** - Dropdown with enum values
- **Date Range** - From/To date pickers (filters `created_at`, `target_date`, etc.)
- **Search** - Text input (searches `title`, `description`)
- **Group** - Dropdown (for entities with grouping)
- **Clear All Filters** - Reset button

**Module-Specific Filters:** Add as additional dropdowns/inputs:

- **Milestones:** `is_critical_path` checkbox
- **Risks:** `severity`, `proximity` dropdowns
- **Issues:** `priority`, `impact` dropdowns
- **Dependencies:** `dependency_type` dropdown
- **Action Items:** `priority`, `assigned_to` dropdowns
- **Deliverables:** `deliverable_type`, `assigned_to` dropdowns

**Table Features:**

- **Sorting:** Click column headers (default: sort by `created_at` DESC, then by date-related fields)
- **Pagination:** 50 records per page (default), standard Laravel pagination links
- **Actions per Row:**
  - **View** - Opens detail modal
  - **Edit** - Opens edit modal
  - **Delete** - Opens confirmation modal
  - Use icon buttons (individual, not dropdown)

**Empty State:**

```
┌────────────────────────────────────────────────────────────┐
│                                                            │
│               No [Module] Found                            │
│                                                            │
│            [+ Create Your First [Module]]                  │
│                                                            │
└────────────────────────────────────────────────────────────┘
```

**Livewire Component Pattern:**

```php
// app/Modules/Risks/Livewire/RiskList.php
class RiskList extends Component
{
    public $projectId;

    // Filter properties
    public $status = '';
    public $search = '';
    public $groupId = '';
    public $dateFrom = '';
    public $dateTo = '';

    // Module-specific filters
    public $severity = '';

    // Pagination
    protected $paginationTheme = 'tailwind';

    public function __construct(
        private RiskRepository $riskRepository
    ) {}

    public function render()
    {
        $filters = [
            'status' => $this->status,
            'search' => $this->search,
            'group_id' => $this->groupId,
            'severity' => $this->severity,
            'date_from' => $this->dateFrom,
            'date_to' => $this->dateTo,
        ];

        $risks = $this->riskRepository->queryByProject(
            $this->projectId,
            with: ['group', 'createdBy'],
            paginate: 50,
            filters: array_filter($filters) // Remove empty filters
        );

        return view('livewire.risks.risk-list', compact('risks'));
    }

    public function clearFilters()
    {
        $this->reset(['status', 'search', 'groupId', 'dateFrom', 'dateTo', 'severity']);
    }
}
```

**Note:** Livewire components also use **Repositories for queries** to maintain consistency across all interfaces.

**When to Use Services vs Repositories in Livewire:**

- **Use Repository:** For simple data retrieval (list, view, filtering)
- **Use Service:** For business logic (create, update, delete, complex operations)

Example:

```php
// Reading data - use Repository
$risks = $this->riskRepository->queryByProject($projectId);

// Creating/Updating/Deleting - use Service
$risk = $this->riskService->create($data);
$this->riskService->update($risk, $data);
$this->riskService->delete($risk);
```

------

### Create/Edit Modals

**Modal Structure:**

```
┌─────────────────────────────────────────────────────────┐
│ [X] Create New Risk                                     │
├─────────────────────────────────────────────────────────┤
│                                                         │
│  Title: [________________________]                      │
│                                                         │
│  Description:                                           │
│  [____________________________________________]         │
│  [____________________________________________]         │
│                                                         │
│  Status: [Dropdown ▼]                                  │
│                                                         │
│  Target Date: [Date Picker]                            │
│  Target Week: [2026-W04] (read-only, computed)         │
│                                                         │
│  Group: [Select Group ▼]  [Manage Groups]             │
│                                                         │
│  □ Internal Only                                        │
│                                                         │
│                    [Cancel]  [Save]                     │
└─────────────────────────────────────────────────────────┘
```

**Form Behavior:**

- **Validation:** On submit only (not real-time)
- **Error Display:** Show validation errors above form or next to fields
- **Auto-computed Fields:** Display as read-only (e.g., `target_week`)
- **Markdown Fields:** Use `<textarea>`, render markdown after save
- **Checkboxes:** For boolean fields (`is_critical_path`, `is_internal_only`)
- **Foreign Keys:** Dropdowns with meaningful labels (not IDs)

**Modal Implementation:**

```php
// Livewire Component
class CreateRisk extends Component
{
    public $projectId;
    public $title;
    public $description;
    public $status;
    public $target_date;
    public $risk_group_id;
    public $is_internal_only = false;

    public $showModal = false;

    protected function rules()
    {
        return [
            'title' => 'required|string|max:255',
            'description' => 'nullable|string',
            'status' => ['required', Rule::enum(RiskStatus::class)],
            'target_date' => 'required|date',
            'risk_group_id' => 'nullable|exists:risk_groups,id',
        ];
    }

    public function save()
    {
        $validated = $this->validate();

        $data = CreateRiskData::from($validated);
        app(RiskService::class)->create($data);

        $this->showModal = false;
        $this->reset();
        $this->dispatch('risk-created'); // Refresh parent list

        session()->flash('success', 'Risk created successfully.');
    }

    public function render()
    {
        return view('livewire.risks.create-risk');
    }
}
```

**Blade Template Pattern:**

```blade
<div>
    <!-- Trigger Button -->
    <button wire:click="$set('showModal', true)">
        + Create New Risk
    </button>

    <!-- Modal -->
    @if($showModal)
    <div class="modal-overlay">
        <div class="modal-content">
            <div class="modal-header">
                <h3>Create New Risk</h3>
                <button wire:click="$set('showModal', false)">×</button>
            </div>

            <form wire:submit.prevent="save">
                <div class="form-group">
                    <label>Title *</label>
                    <input type="text" wire:model="title">
                    @error('title') <span class="error">{{ $message }}</span> @enderror
                </div>

                <div class="form-group">
                    <label>Description</label>
                    <textarea wire:model="description"></textarea>
                </div>

                <div class="form-group">
                    <label>Status *</label>
                    <select wire:model="status">
                        <option value="">Select Status</option>
                        @foreach(RiskStatus::cases() as $status)
                            <option value="{{ $status->value }}">{{ $status->name }}</option>
                        @endforeach
                    </select>
                    @error('status') <span class="error">{{ $message }}</span> @enderror
                </div>

                <!-- Additional fields... -->

                <div class="modal-footer">
                    <button type="button" wire:click="$set('showModal', false)">Cancel</button>
                    <button type="submit">Save</button>
                </div>
            </form>
        </div>
    </div>
    @endif
</div>
```

------

### Detail Modals

**Modal Structure with Tabs:**

```
┌──────────────────────────────────────────────────────────┐
│ [X] Risk #R-001: Server Infrastructure Risk              │
├──────────────────────────────────────────────────────────┤
│ [Details] [Notes] [Audit Trail] [Links]                 │
├──────────────────────────────────────────────────────────┤
│                                                          │
│  Title: Server Infrastructure Risk                       │
│  Status: High                                            │
│  Target Date: 2026-01-15  |  Target Week: Week 3 Jan   │
│  Group: Technical Risks                                  │
│  Created: 2026-01-10 by John Doe                        │
│  Updated: 2026-01-12 by Jane Smith                      │
│                                                          │
│  Description:                                            │
│  Risk of server failure due to aging infrastructure...  │
│                                                          │
│                               [Edit]  [Delete]           │
└──────────────────────────────────────────────────────────┘
```

**Tab Content:**

**1. Details Tab:**

- All entity fields in read-only format
- Markdown fields rendered as HTML
- Dates formatted consistently
- Computed fields displayed
- Action buttons at bottom (Edit, Delete)

**2. Notes Tab:**

```
┌──────────────────────────────────────────────────────────┐
│                                        [+ Add Comment]   │
├──────────────────────────────────────────────────────────┤
│                                                          │
│  John Doe • 2 hours ago                                 │
│  We need to prioritize this risk. Meeting with vendor   │
│  scheduled for next week.                               │
│                                                          │
│  ─────────────────────────────────────────────────────  │
│                                                          │
│  Jane Smith • 1 day ago                                 │
│  Initial assessment complete. Severity confirmed.       │
│                                                          │
└──────────────────────────────────────────────────────────┘
```

Behavior:

- Display most recent first (reverse chronological)
- Show author name + timestamp
- Render markdown content as HTML
- "Add Comment" button reveals textarea + Save/Cancel buttons
- Notes are immutable (no edit/delete in V1)

**3. Audit Trail Tab:**

```
┌──────────────────────────────────────────────────────────┐
│  2026-01-12 14:32 • Jane Smith                          │
│  Changed status from "Medium" to "High"                 │
│                                                          │
│  2026-01-10 09:15 • John Doe                            │
│  Created risk                                            │
└──────────────────────────────────────────────────────────┘
```

Display:

- Chronological list of all changes
- Format: `Changed [field] from "[old]" to "[new]"`
- User + timestamp

**4. Links Tab (for entities that support linking):**

```
┌──────────────────────────────────────────────────────────┐
│  Referenced in:                                          │
│                                                          │
│  • Journal Entry #JE-012: "Risk Assessment Update"      │
│  • Decision #DEC-005: "Infrastructure Investment"       │
│                                                          │
└──────────────────────────────────────────────────────────┘
```

Show reverse links (where this entity is referenced)

**Implementation Pattern:**

```php
class ViewRisk extends Component
{
    public $riskId;
    public $activeTab = 'details';
    public $showModal = false;

    public function mount($riskId)
    {
        $this->riskId = $riskId;
    }

    public function __construct(
        private RiskRepository $riskRepository
    ) {}

    public function render()
    {
        $risk = $this->riskRepository->findOrFail(
            auth()->user()->currentProjectId(), // or passed via component
            $this->riskId,
            with: ['notes', 'group', 'createdBy', 'updatedBy']
        );

        $auditTrail = activity()
            ->forSubject($risk)
            ->get();

        return view('livewire.risks.view-risk', compact('risk', 'auditTrail'));
    }

    public function switchTab($tab)
    {
        $this->activeTab = $tab;
    }
}
```

------

### Delete Confirmation Modal

**Pattern:**

```
┌────────────────────────────────────────────────┐
│ Confirm Deletion                               │
├────────────────────────────────────────────────┤
│                                                │
│ Are you sure you want to delete this risk?     │
│                                                │
│ Risk: "Server Infrastructure Risk"            │
│                                                │
│ This action cannot be undone.                  │
│                                                │
│              [Cancel]  [Delete]                │
└────────────────────────────────────────────────┘
```

**Implementation:**

```php
class DeleteRisk extends Component
{
    public $riskId;
    public $showModal = false;

    public function __construct(
        private RiskRepository $riskRepository,
        private RiskService $riskService
    ) {}

    public function confirmDelete()
    {
        $this->showModal = true;
    }

    public function delete()
    {
        $risk = $this->riskRepository->findOrFail(
            auth()->user()->currentProjectId(),
            $this->riskId
        );

        $this->riskService->delete($risk);

        $this->showModal = false;
        $this->dispatch('risk-deleted');

        session()->flash('success', 'Risk deleted successfully.');
    }

    public function render()
    {
        $risk = $this->riskRepository->findOrFail(
            auth()->user()->currentProjectId(),
            $this->riskId
        );

        return view('livewire.risks.delete-risk', compact('risk'));
    }
}
```

------

## Group Management

### "Manage Groups" Modal

**Access:**

- Button on list page: "Manage Groups" (next to "Create New")
- Opens modal showing all groups for current module

**Modal Layout:**

```
┌──────────────────────────────────────────────────────────┐
│ [X] Manage Risk Groups                                   │
├──────────────────────────────────────────────────────────┤
│                                      [+ Add New Group]   │
├──────────────────────────────────────────────────────────┤
│                                                          │
│  Technical Risks                      [Edit] [Delete]    │
│    └─ Infrastructure                 [Edit] [Delete]    │
│    └─ Security                       [Edit] [Delete]    │
│                                                          │
│  Process Risks                        [Edit] [Delete]    │
│    └─ Regulatory                     [Edit] [Delete]    │
│                                                          │
└──────────────────────────────────────────────────────────┘
```

**Display Pattern:**

- Flat list with visual indentation for child groups
- Parent groups shown first, then children nested below
- Each group has Edit + Delete buttons

**Add/Edit Group Inline:**

```
┌──────────────────────────────────────────────────────────┐
│ Add New Group                                            │
├──────────────────────────────────────────────────────────┤
│                                                          │
│  Group Name: [_____________________]                    │
│                                                          │
│  Parent Group: [None ▼]                                 │
│                                                          │
│                          [Cancel]  [Save]                │
└──────────────────────────────────────────────────────────┘
```

**Business Rules Enforced:**

- Maximum 2-level hierarchy
- If parent group has a parent, disable it as option for new groups
- Cannot delete group if it has assigned items
- Cannot delete group if it has child groups

**In Create/Edit Item Forms:**

```
Group: [Select Group ▼]  [Manage Groups]
       ├─ Technical Risks
       │  ├─ Infrastructure
       │  └─ Security
       └─ Process Risks
          └─ Regulatory
```

Dropdown shows:

- Hierarchical structure with indentation
- Clicking "Manage Groups" opens the manage modal

**Implementation:**

```php
class ManageRiskGroups extends Component
{
    public $projectId;
    public $groups;
    public $editingGroupId = null;
    public $groupName = '';
    public $parentGroupId = null;
    public $showAddForm = false;

    public function mount($projectId)
    {
        $this->projectId = $projectId;
        $this->loadGroups();
    }

    public function loadGroups()
    {
        $this->groups = RiskGroup::where('project_id', $this->projectId)
            ->with('parent', 'children')
            ->orderBy('title')
            ->get()
            ->groupBy('parent_id');
    }

    public function saveGroup()
    {
        $this->validate([
            'groupName' => 'required|string|max:255',
            'parentGroupId' => 'nullable|exists:risk_groups,id',
        ]);

        // Business rule: Check 2-level hierarchy
        if ($this->parentGroupId) {
            $parent = RiskGroup::find($this->parentGroupId);
            if ($parent->parent_id !== null) {
                session()->flash('error', 'Cannot create more than 2 levels of groups.');
                return;
            }
        }

        RiskGroup::create([
            'project_id' => $this->projectId,
            'title' => $this->groupName,
            'parent_id' => $this->parentGroupId,
        ]);

        $this->reset(['groupName', 'parentGroupId', 'showAddForm']);
        $this->loadGroups();
    }

    public function deleteGroup($groupId)
    {
        $group = RiskGroup::findOrFail($groupId);

        // Check if group has items
        if ($group->risks()->count() > 0) {
            session()->flash('error', 'Cannot delete group with assigned risks.');
            return;
        }

        // Check if group has children
        if ($group->children()->count() > 0) {
            session()->flash('error', 'Cannot delete group with child groups.');
            return;
        }

        $group->delete();
        $this->loadGroups();
    }

    public function render()
    {
        return view('livewire.risks.manage-risk-groups');
    }
}
```

**Note on Group Management:** For simple Group CRUD operations, direct Eloquent usage is acceptable since groups are lightweight metadata entities with minimal business logic. However, for complex projects, consider creating a `GroupRepository` if group queries become complex.

------

## Notes/Comments Interface

### Display on Detail Page (Notes Tab)

**Empty State:**

```
┌──────────────────────────────────────────────────────────┐
│                                        [+ Add Comment]   │
├──────────────────────────────────────────────────────────┤
│                                                          │
│  [Empty textarea - no placeholder text]                 │
│                                                          │
└──────────────────────────────────────────────────────────┘
```

**With Notes:**

```
┌──────────────────────────────────────────────────────────┐
│                                        [+ Add Comment]   │
├──────────────────────────────────────────────────────────┤
│                                                          │
│  Jane Smith • 2 hours ago                               │
│  Updated the mitigation strategy based on vendor        │
│  feedback. Will monitor closely over next 2 weeks.      │
│                                                          │
│  ─────────────────────────────────────────────────────  │
│                                                          │
│  John Doe • 1 day ago                                   │
│  Initial assessment shows **high impact** on timeline.  │
│  See [Decision #5](/decisions/5) for context.           │
│                                                          │
└──────────────────────────────────────────────────────────┘
```

**Add Comment Form (revealed when button clicked):**

```
┌──────────────────────────────────────────────────────────┐
│  Add a comment (Markdown supported)                     │
│  ┌────────────────────────────────────────────────────┐ │
│  │                                                    │ │
│  │                                                    │ │
│  │                                                    │ │
│  └────────────────────────────────────────────────────┘ │
│                               [Cancel]  [Save Comment] │
└──────────────────────────────────────────────────────────┘
```

**Implementation:**

```php
class EntityNotes extends Component
{
    public $notableType;
    public $notableId;
    public $notes;
    public $newNoteContent = '';
    public $showAddForm = false;

    public function mount($notableType, $notableId)
    {
        $this->notableType = $notableType;
        $this->notableId = $notableId;
        $this->loadNotes();
    }

    public function loadNotes()
    {
        $this->notes = Note::where('notable_type', $this->notableType)
            ->where('notable_id', $this->notableId)
            ->with('createdBy')
            ->orderBy('created_at', 'desc') // Most recent first
            ->get();
    }

    public function saveNote()
    {
        $this->validate([
            'newNoteContent' => 'required|string',
        ]);

        Note::create([
            'notable_type' => $this->notableType,
            'notable_id' => $this->notableId,
            'content' => $this->newNoteContent,
            'is_system_generated' => false,
        ]);

        $this->reset(['newNoteContent', 'showAddForm']);
        $this->loadNotes();
    }

    public function render()
    {
        return view('livewire.shared.entity-notes');
    }
}
```

**Blade Template:**

```blade
<div>
    <div class="notes-header">
        <button wire:click="$toggle('showAddForm')">+ Add Comment</button>
    </div>

    @if($showAddForm)
    <form wire:submit.prevent="saveNote" class="add-note-form">
        <label>Add a comment (Markdown supported)</label>
        <textarea wire:model="newNoteContent" rows="4"></textarea>
        @error('newNoteContent') <span class="error">{{ $message }}</span> @enderror

        <div class="form-actions">
            <button type="button" wire:click="$set('showAddForm', false)">Cancel</button>
            <button type="submit">Save Comment</button>
        </div>
    </form>
    @endif

    <div class="notes-list">
        @forelse($notes as $note)
        <div class="note-item">
            <div class="note-meta">
                <strong>{{ $note->createdBy->name }}</strong> •
                <span>{{ $note->created_at->diffForHumans() }}</span>
            </div>
            <div class="note-content">
                {!! Str::markdown($note->content) !!}
            </div>
        </div>
        <hr>
        @empty
        <textarea disabled></textarea>
        @endforelse
    </div>
</div>
```

------

## Special Features

### Project Selector (Top Bar)

**Dropdown Component:**

```
[📁 Project ABC ▼]
  ├─ Project ABC (current)
  ├─ Project XYZ
  └─ Project 123
```

**Implementation:**

```php
class ProjectSelector extends Component
{
    public $currentProjectId;
    public $projects;

    public function mount()
    {
        $this->projects = auth()->user()->projects;
        $this->currentProjectId = session('current_project_id');
    }

    public function switchProject($projectId)
    {
        session(['current_project_id' => $projectId]);

        return redirect()->route('projects.dashboard', $projectId);
    }

    public function render()
    {
        $currentProject = $this->projects->find($this->currentProjectId);
        return view('livewire.shared.project-selector', compact('currentProject'));
    }
}
```

**Blade Template:**

```blade
<div class="project-selector">
    @if($projects->count() > 1)
    <div class="dropdown">
        <button class="dropdown-toggle">
            📁 {{ $currentProject->title ?? 'Select Project' }} ▼
        </button>
        <div class="dropdown-menu">
            @foreach($projects as $project)
            <a wire:click="switchProject({{ $project->id }})"
               class="{{ $project->id === $currentProjectId ? 'active' : '' }}">
                {{ $project->code }} - {{ $project->title }}
            </a>
            @endforeach
        </div>
    </div>
    @else
    <span class="project-name">📁 {{ $currentProject->title }}</span>
    @endif
</div>
```

------

### Status Report Publishing

**Draft Report Display:**

```
┌──────────────────────────────────────────────────────────┐
│ Status Report - Draft                                    │
│                                          [📤 Publish]    │
├──────────────────────────────────────────────────────────┤
│                                                          │
│  Report Period: Jan 1 - Jan 15, 2026                    │
│  Overall Status: 🟢 Green                               │
│                                                          │
│  Executive Summary:                                      │
│  [______________________________________]                │
│                                                          │
│  Key Achievements:                                       │
│  [______________________________________]                │
│                                                          │
│                               [Save Draft]  [Cancel]     │
└──────────────────────────────────────────────────────────┘
```

**Publish Button Behavior:**

```php
public function publish()
{
    $report = StatusReport::findOrFail($this->reportId);

    // Capture snapshot data
    $snapshotData = [
        'milestone_counts' => $this->getMilestoneStatusCounts(),
        'risk_counts' => $this->getRiskCountsBySeverity(),
        'issue_counts' => $this->getIssueCountsByPriority(),
        'change_request_counts' => $this->getChangeRequestStatusCounts(),
    ];

    $report->update([
        'published_at' => now(),
        'published_by' => auth()->id(),
        'snapshot_data' => $snapshotData,
    ]);

    session()->flash('success', 'Report published successfully.');
    return redirect()->route('status-reports.view', $report);
}
```

**Published Report Display:**

```
┌──────────────────────────────────────────────────────────┐
│ Status Report - Published                    🔒 Locked   │
│                                                          │
│  Published: Jan 15, 2026 by Jane Smith                  │
├──────────────────────────────────────────────────────────┤
│  [Details] [Snapshot Data]                              │
├──────────────────────────────────────────────────────────┤
│                                                          │
│  Report Period: Jan 1 - Jan 15, 2026                    │
│  Overall Status: 🟢 Green                               │
│                                                          │
│  Executive Summary:                                      │
│  Project is on track. All major milestones achieved...   │
│                                                          │
└──────────────────────────────────────────────────────────┘
```

**Snapshot Data Tab:**

```
┌──────────────────────────────────────────────────────────┐
│  Snapshot captured on: Jan 15, 2026 at 14:30           │
├──────────────────────────────────────────────────────────┤
│                                                          │
│  Milestones:                                             │
│    • Completed: 5                                        │
│    • On Track: 3                                         │
│    • At Risk: 1                                          │
│                                                          │
│  Risks:                                                  │
│    • Critical: 0                                         │
│    • High: 2                                             │
│    • Medium: 5                                           │
│    • Low: 3                                              │
│                                                          │
│  Issues:                                                 │
│    • Open: 4                                             │
│    • Escalated: 1                                        │
│    • Closed: 8                                           │
│                                                          │
│  Change Requests:                                        │
│    • Approved: 2                                         │
│    • Under Review: 1                                     │
│    • Implemented: 3                                      │
│                                                          │
└──────────────────────────────────────────────────────────┘
```

------

### Decision Superseding

**On Decision Detail Page:**

```
┌──────────────────────────────────────────────────────────┐
│ Decision #DEC-005: Infrastructure Investment Strategy    │
│                                            [Supersede]   │
├──────────────────────────────────────────────────────────┤
│  Status: Active                                          │
│  Decision Maker: John Doe                                │
│  Decision Date: 2026-01-10                               │
│                                                          │
│  Decision:                                               │
│  Invest $500K in new server infrastructure...            │
│                                                          │
└──────────────────────────────────────────────────────────┘
```

**Supersede Button Behavior:**

- Opens "Create New Decision" modal
- Pre-fills context with reference to old decision
- On save, links old decision via `superseded_by` field

**Superseded Decision Display:**

```
┌──────────────────────────────────────────────────────────┐
│ Decision #DEC-005: Infrastructure Investment Strategy    │
│                                                          │
│  ⚠️ This decision has been superseded by Decision #12   │
│     [View Current Decision]                              │
├──────────────────────────────────────────────────────────┤
│  Status: Superseded                                      │
│  ...                                                     │
└──────────────────────────────────────────────────────────┘
```

------

### Entity Linking (Project Journal → Other Entities)

**In Journal Entry Create/Edit Form:**

```
┌──────────────────────────────────────────────────────────┐
│ Create Journal Entry                                     │
├──────────────────────────────────────────────────────────┤
│                                                          │
│  Title: [_____________________________]                 │
│                                                          │
│  Content:                                                │
│  [____________________________________________]          │
│                                                          │
│  Link to Entities:                  [+ Add Link]        │
│    • Risk #R-005: Server Risk           [Remove]        │
│    • Decision #DEC-012: Infrastructure  [Remove]        │
│                                                          │
│                          [Cancel]  [Save]                │
└──────────────────────────────────────────────────────────┘
```

**Add Link Modal:**

```
┌──────────────────────────────────────────────────────────┐
│ Link to Entity                                           │
├──────────────────────────────────────────────────────────┤
│                                                          │
│  Entity Type: [Select Type ▼]                           │
│               - Milestone                                │
│               - Risk                                     │
│               - Issue                                    │
│               - Decision                                 │
│               - ...                                      │
│                                                          │
│  Entity: [Select Entity ▼]                              │
│          (dropdown populated based on type)              │
│                                                          │
│  Link Type: [References ▼]                              │
│             - References                                 │
│             - Relates To                                 │
│             - Announces                                  │
│                                                          │
│  Description: [Optional note about the link]            │
│                                                          │
│                          [Cancel]  [Add Link]            │
└──────────────────────────────────────────────────────────┘
```

**Implementation:**

```php
class ManageEntityLinks extends Component
{
    public $journalEntryId;
    public $links = [];
    public $entityType;
    public $entityId;
    public $linkType = 'references';
    public $description;
    public $showAddModal = false;

    public function mount($journalEntryId)
    {
        $this->journalEntryId = $journalEntryId;
        $this->loadLinks();
    }

    public function loadLinks()
    {
        $this->links = EntityLink::where('source_type', 'JournalEntry')
            ->where('source_id', $this->journalEntryId)
            ->with('target')
            ->get();
    }

    public function addLink()
    {
        $this->validate([
            'entityType' => 'required|string',
            'entityId' => 'required|integer',
            'linkType' => 'required|string',
        ]);

        EntityLink::create([
            'source_type' => 'JournalEntry',
            'source_id' => $this->journalEntryId,
            'target_type' => $this->entityType,
            'target_id' => $this->entityId,
            'link_type' => $this->linkType,
            'description' => $this->description,
        ]);

        $this->reset(['entityType', 'entityId', 'linkType', 'description', 'showAddModal']);
        $this->loadLinks();
    }

    public function removeLink($linkId)
    {
        EntityLink::destroy($linkId);
        $this->loadLinks();
    }

    public function render()
    {
        $availableTypes = [
            'Milestone', 'Risk', 'Issue', 'Decision', 'ActionItem',
            'Deliverable', 'Dependency', 'ChangeRequest'
        ];

        return view('livewire.journal.manage-entity-links', compact('availableTypes'));
    }
}
```

**Reverse Display (on linked entity):**

```
┌──────────────────────────────────────────────────────────┐
│ Risk #R-005: Server Infrastructure Risk                  │
├──────────────────────────────────────────────────────────┤
│  [Details] [Notes] [Audit Trail] [References]           │
├──────────────────────────────────────────────────────────┤
│                                                          │
│  Referenced in:                                          │
│    • Journal Entry #JE-012: "Risk Assessment Complete"  │
│    • Journal Entry #JE-015: "Mitigation Strategy"       │
│                                                          │
└──────────────────────────────────────────────────────────┘
```

------

### Meeting Minutes ↔ Meeting Events

**Create Minutes from Meeting Event:**

On Meeting Event detail page:

```
┌──────────────────────────────────────────────────────────┐
│ Meeting Event: Steering Committee                        │
│                                    [Create Minutes]      │
├──────────────────────────────────────────────────────────┤
│  Date: 2026-01-15                                        │
│  Status: Completed                                       │
│  ...                                                     │
│                                                          │
│  Minutes: None recorded yet                              │
└──────────────────────────────────────────────────────────┘
```

**Create Minutes Button Opens Form:**

```
┌──────────────────────────────────────────────────────────┐
│ Create Meeting Minutes                                   │
├──────────────────────────────────────────────────────────┤
│                                                          │
│  Meeting: Steering Committee - Jan 15, 2026 (pre-fill) │
│                                                          │
│  Attendees:                                              │
│  [____________________________________________]          │
│                                                          │
│  Agenda:                                                 │
│  [____________________________________________]          │
│                                                          │
│  Minutes:                                                │
│  [____________________________________________]          │
│                                                          │
│  Action Items Summary:                                   │
│  [____________________________________________]          │
│                                                          │
│  Decisions Summary:                                      │
│  [____________________________________________]          │
│                                                          │
│                          [Cancel]  [Save]                │
└──────────────────────────────────────────────────────────┘
```

**After Minutes Created:**

```
┌──────────────────────────────────────────────────────────┐
│ Meeting Event: Steering Committee                        │
├──────────────────────────────────────────────────────────┤
│  ...                                                     │
│                                                          │
│  Minutes: [View Minutes #MM-012]                        │
└──────────────────────────────────────────────────────────┘
```

**Implementation:**

```php
public function createMinutes()
{
    $meetingEvent = MeetingEvent::findOrFail($this->meetingEventId);

    // Check if minutes already exist
    if ($meetingEvent->minutes()->exists()) {
        session()->flash('error', 'Minutes already exist for this meeting.');
        return;
    }

    // Pre-fill form data
    $this->meetingId = $this->meetingEventId;
    $this->showCreateForm = true;
}

public function saveMinutes()
{
    $validated = $this->validate([
        'meetingId' => 'required|exists:meeting_events,id',
        'attendees' => 'required|string',
        'agenda' => 'nullable|string',
        'minutes' => 'required|string',
        'action_items_summary' => 'nullable|string',
        'decisions_summary' => 'nullable|string',
    ]);

    MeetingMinute::create(array_merge($validated, [
        'project_id' => $this->projectId,
    ]));

    session()->flash('success', 'Meeting minutes created successfully.');
    return redirect()->route('meeting-events.view', $this->meetingId);
}
```

------

## Role-Based UI Elements

### User Menu (Top Bar)

```
[User: Jane Smith (Admin) ▼]
  ├─ Profile
  ├─ Settings
  └─ Logout
```

Display current user name + role badge

### Button Visibility

**V1 Approach (Testing Phase):**

- Show all buttons (Edit, Delete, Create, etc.) for all users
- Do NOT enforce role-based hiding yet
- Apply restrictions in later iterations

**Future Implementation (V2+):**

```php
// In Blade templates
@can('update', $risk)
    <button wire:click="edit({{ $risk->id }})">Edit</button>
@endcan

@can('delete', $risk)
    <button wire:click="confirmDelete({{ $risk->id }})">Delete</button>
@endcan
```

### Internal-Only Content

**V1 Approach:**

- Display ALL content regardless of `is_internal_only` flag
- Do NOT filter based on user role yet
- Apply visibility rules in later iterations

**Future Implementation (V2+):**

```php
// In queries
$risks = Risk::where('project_id', $projectId)
    ->when(auth()->user()->role === 'viewer', function($q) {
        $q->where('is_internal_only', false);
    })
    ->get();
```

------

## API Specifications

### Standard Response Format

**Success Response:**

```json
{
  "data": {
    "id": 1,
    "title": "Risk Title",
    "status": "high",
    ...
  },
  "message": "Risk created successfully"
}
```

**Collection Response:**

```json
{
  "data": [
    { "id": 1, "title": "Risk A", ... },
    { "id": 2, "title": "Risk B", ... }
  ],
  "meta": {
    "current_page": 1,
    "per_page": 50,
    "total": 150
  }
}
```

**Error Response:**

```json
{
  "message": "The given data was invalid.",
  "errors": {
    "title": ["The title field is required."],
    "status": ["The selected status is invalid."]
  }
}
```

### API Resource Pattern

```php
// app/Modules/Risks/Resources/RiskResource.php
class RiskResource extends JsonResource
{
    public function toArray($request)
    {
        return [
            'id' => $this->id,
            'title' => $this->title,
            'description' => $this->description,
            'status' => $this->status,
            'severity' => $this->severity,
            'target_date' => $this->target_date,
            'target_week' => $this->target_week,
            'group' => new RiskGroupResource($this->whenLoaded('group')),
            'created_at' => $this->created_at,
            'created_by' => new UserResource($this->whenLoaded('createdBy')),
            'updated_at' => $this->updated_at,
        ];
    }
}
```

### API Controller Pattern

```php
// app/Modules/Risks/Controllers/Api/RiskController.php
class RiskController extends Controller
{
    public function __construct(
        private RiskService $riskService,
        private RiskRepository $riskRepository
    ) {}

    public function index(Request $request, $projectId)
    {
        $risks = $this->riskRepository->queryByProject(
            $projectId,
            with: ['group', 'createdBy'],
            paginate: 50
        );

        return RiskResource::collection($risks);
    }

    public function store(StoreRiskRequest $request, $projectId)
    {
        $data = CreateRiskData::fromRequest($request);
        $risk = $this->riskService->create($data);

        return new RiskResource($risk->load(['group', 'createdBy']));
    }

    public function show($projectId, $riskId)
    {
        $risk = $this->riskRepository->findOrFail(
            $projectId,
            $riskId,
            with: ['group', 'createdBy', 'notes']
        );

        return new RiskResource($risk);
    }

    public function update(UpdateRiskRequest $request, $projectId, $riskId)
    {
        $risk = $this->riskRepository->findOrFail($projectId, $riskId);

        $data = UpdateRiskData::fromRequest($request);
        $risk = $this->riskService->update($risk, $data);

        return new RiskResource($risk->load(['group', 'createdBy']));
    }

    public function destroy($projectId, $riskId)
    {
        $risk = $this->riskRepository->findOrFail($projectId, $riskId);

        $this->riskService->delete($risk);

        return response()->json([
            'message' => 'Risk deleted successfully'
        ]);
    }
}
```

**Note:** Controllers use **Repository for queries** and **Service for business logic**. This keeps controllers thin and maintains separation of concerns.

### Repository Pattern Example

```php
// app/Modules/Risks/Repositories/RiskRepository.php
class RiskRepository
{
    public function queryByProject(
        int $projectId,
        array $with = [],
        int $paginate = 50,
        array $filters = []
    ) {
        $query = Risk::where('project_id', $projectId);

        if (!empty($with)) {
            $query->with($with);
        }

        // Apply filters
        if (isset($filters['status'])) {
            $query->where('status', $filters['status']);
        }

        if (isset($filters['severity'])) {
            $query->where('severity', $filters['severity']);
        }

        if (isset($filters['group_id'])) {
            $query->where('risk_group_id', $filters['group_id']);
        }

        if (isset($filters['search'])) {
            $query->where(function($q) use ($filters) {
                $q->where('title', 'like', "%{$filters['search']}%")
                  ->orWhere('description', 'like', "%{$filters['search']}%");
            });
        }

        if (isset($filters['date_from'])) {
            $query->whereDate('created_at', '>=', $filters['date_from']);
        }

        if (isset($filters['date_to'])) {
            $query->whereDate('created_at', '<=', $filters['date_to']);
        }

        return $query->orderBy('created_at', 'desc')->paginate($paginate);
    }

    public function findOrFail(
        int $projectId,
        int $id,
        array $with = []
    ): Risk {
        $query = Risk::where('project_id', $projectId)
            ->where('id', $id);

        if (!empty($with)) {
            $query->with($with);
        }

        return $query->firstOrFail();
    }
}
```

**Key Points:**

- Repositories encapsulate ALL database queries
- Controllers NEVER use Eloquent directly
- Livewire components NEVER use Eloquent directly
- Services use Repositories for data access
- This pattern applies to ALL modules (Risks, Milestones, ActionItems, etc.)

### API Routes Pattern

```php
// routes/api.php
Route::middleware(['auth:sanctum'])->group(function () {
    Route::prefix('projects/{project}')->group(function () {

        // Risks
        Route::apiResource('risks', RiskController::class);
        Route::apiResource('risks.notes', RiskNoteController::class)
            ->only(['index', 'store']);

        // Milestones
        Route::apiResource('milestones', MilestoneController::class);

        // Action Items
        Route::apiResource('action-items', ActionItemController::class);

        // ... all other modules
    });

    // Portfolio Dashboard (Admin only)
    Route::get('portfolio/dashboard', [PortfolioDashboardController::class, 'index'])
        ->middleware('role:admin');
});
```

### Authentication (Laravel Sanctum)

**Token Generation (Login):**

```php
public function login(Request $request)
{
    $credentials = $request->validate([
        'email' => 'required|email',
        'password' => 'required',
    ]);

    if (!Auth::attempt($credentials)) {
        return response()->json(['message' => 'Invalid credentials'], 401);
    }

    $user = Auth::user();
    $token = $user->createToken('api-token')->plainTextToken;

    return response()->json([
        'user' => new UserResource($user),
        'token' => $token,
    ]);
}
```

**Token Usage:**

```
Authorization: Bearer {token}
```

------

## Styling Guidelines

### CSS Approach

**V1 Philosophy:** Minimal, functional styling

**Recommended Approach:**

- Use **Tailwind CSS** utility classes for rapid development
- Create minimal custom CSS only when necessary
- Focus on usability, not aesthetics

**Basic Tailwind Setup:**

```html
<!-- In layout file -->
<head>
    @vite(['resources/css/app.css', 'resources/js/app.js'])
    @livewireStyles
</head>
<body>
    {{ $slot }}
    @livewireScripts
</body>
```

**Utility-First Styling:**

```blade
<!-- Modal Example -->
<div class="fixed inset-0 bg-gray-500 bg-opacity-75 flex items-center justify-center">
    <div class="bg-white rounded-lg shadow-xl max-w-2xl w-full p-6">
        <div class="flex justify-between items-center mb-4">
            <h3 class="text-lg font-medium">Create Risk</h3>
            <button wire:click="$set('showModal', false)" class="text-gray-400 hover:text-gray-600">
                ×
            </button>
        </div>

        <form wire:submit.prevent="save" class="space-y-4">
            <div>
                <label class="block text-sm font-medium text-gray-700">Title</label>
                <input type="text" wire:model="title"
                       class="mt-1 block w-full rounded-md border-gray-300 shadow-sm">
                @error('title')
                    <span class="text-red-600 text-sm">{{ $message }}</span>
                @enderror
            </div>

            <!-- More fields... -->

            <div class="flex justify-end space-x-3">
                <button type="button" wire:click="$set('showModal', false)"
                        class="px-4 py-2 bg-gray-200 rounded-md hover:bg-gray-300">
                    Cancel
                </button>
                <button type="submit"
                        class="px-4 py-2 bg-blue-600 text-white rounded-md hover:bg-blue-700">
                    Save
                </button>
            </div>
        </form>
    </div>
</div>
```

### Color Coding

**RAG Status:**

- Red: `bg-red-100 text-red-800` or `bg-red-600 text-white`
- Amber: `bg-amber-100 text-amber-800` or `bg-amber-600 text-white`
- Green: `bg-green-100 text-green-800` or `bg-green-600 text-white`

**Status Badges:**

```blade
<span class="px-2 py-1 rounded-full text-xs font-medium
    {{ $risk->status === 'high' ? 'bg-red-100 text-red-800' : '' }}
    {{ $risk->status === 'medium' ? 'bg-amber-100 text-amber-800' : '' }}
    {{ $risk->status === 'low' ? 'bg-green-100 text-green-800' : '' }}">
    {{ ucfirst($risk->status) }}
</span>
```

**Action Buttons:**

- View: Blue (`text-blue-600 hover:text-blue-800`)
- Edit: Yellow (`text-yellow-600 hover:text-yellow-800`)
- Delete: Red (`text-red-600 hover:text-red-800`)

------

## Testing Guidelines

### Manual Testing Priority

**Sequence:**

1. **API Testing** - Use Postman/Insomnia to validate endpoints
2. **Web/Livewire Testing** - Manual browser testing

### API Testing Checklist

For each module:

- [ ] POST `/api/projects/{project}/{module}` - Create new record
- [ ] GET `/api/projects/{project}/{module}` - List all records with pagination
- [ ] GET `/api/projects/{project}/{module}/{id}` - View single record
- [ ] PUT/PATCH `/api/projects/{project}/{module}/{id}` - Update record
- [ ] DELETE `/api/projects/{project}/{module}/{id}` - Soft delete record
- [ ] Validate all FormRequest rules trigger correctly
- [ ] Verify API Resources format data correctly
- [ ] Test filtering, sorting, pagination

### Livewire Testing Checklist

For each module:

- [ ] List page displays records
- [ ] Filters work correctly
- [ ] Sorting by clicking column headers works
- [ ] Pagination works
- [ ] Create modal opens and saves correctly
- [ ] Edit modal pre-fills data and updates correctly
- [ ] Delete confirmation works
- [ ] Detail modal displays all data
- [ ] Notes can be added and displayed
- [ ] Groups can be managed
- [ ] Validation errors display properly

------

## Performance Considerations

### Eager Loading

Always eager load relationships to avoid N+1 queries:

```php
// In Repository methods, always use with() for relationships
public function queryByProject(int $projectId, array $with = [])
{
    $query = Risk::where('project_id', $projectId);

    if (!empty($with)) {
        $query->with($with); // Eager load to avoid N+1
    }

    return $query->paginate(50);
}

// Usage in Controller/Livewire
$risks = $this->riskRepository->queryByProject(
    $projectId,
    with: ['group', 'createdBy', 'updatedBy']
);
```

### Query Optimization

**Indexes** (should already be in migrations per Data Models document):

- Foreign keys
- Status fields
- Date fields
- `is_internal_only` field
- Composite indexes on `(project_id, status, deleted_at)`

### Livewire Performance

**Lazy Loading for Heavy Components:**

```blade
<livewire:risks.risk-list :project-id="$project->id" lazy />
```

**Debounce Search Inputs:**

```blade
<input type="text" wire:model.live.debounce.500ms="search">
```

**Polling Only When Needed:**

```blade
<!-- Don't add wire:poll unless specifically needed -->
```

------

## File Structure Summary

```
app/Modules/
├── Risks/
│   ├── Controllers/
│   │   ├── Api/
│   │   │   ├── RiskController.php
│   │   │   └── RiskNoteController.php
│   │   └── Web/
│   │       └── RiskController.php (if needed for full pages)
│   ├── Livewire/
│   │   ├── RiskList.php
│   │   ├── CreateRisk.php
│   │   ├── EditRisk.php
│   │   ├── ViewRisk.php
│   │   ├── DeleteRisk.php
│   │   └── ManageRiskGroups.php
│   ├── Resources/
│   │   ├── RiskResource.php
│   │   └── RiskGroupResource.php
│   └── ... (Services, DTOs, Policies, etc.)
│
├── Shared/
│   ├── Livewire/
│   │   ├── ProjectSelector.php
│   │   ├── EntityNotes.php
│   │   └── PortfolioDashboard.php
│   └── ... (Shared components)
│
resources/
├── views/
│   ├── layouts/
│   │   ├── app.blade.php (main layout with topbar + sidebar)
│   │   └── guest.blade.php (login layout)
│   ├── livewire/
│   │   ├── risks/
│   │   │   ├── risk-list.blade.php
│   │   │   ├── create-risk.blade.php
│   │   │   ├── edit-risk.blade.php
│   │   │   ├── view-risk.blade.php
│   │   │   └── manage-risk-groups.blade.php
│   │   └── shared/
│   │       ├── project-selector.blade.php
│   │       └── entity-notes.blade.php
│   ├── portfolio/
│   │   └── dashboard.blade.php
│   └── projects/
│       ├── selection.blade.php
│       └── dashboard.blade.php (V2)
│
routes/
├── api.php (API routes)
└── web.php (Web/Livewire routes)
```

------

## Summary of Critical UI Rules

1. **API-first development sequence** - Build and test APIs before Web/Livewire
2. **All interfaces use Services/Repositories directly** - No API wrapping
3. **Repository pattern for data access** - Controllers and Livewire use Repository for queries, Service for business logic
4. **Native Livewire preference** - Use Flux only when absolutely necessary
5. **Modals for all forms** - Create, Edit, Details all in modals
6. **Validation on submit only** - No real-time validation in V1
7. **50 records per page** - Default pagination across all lists
8. **Individual icon buttons** - No action dropdown menus
9. **Standard filters** - Status, date range, search, group on all modules
10. **Notes inline on detail pages** - Markdown-supported, recent first
11. **Groups managed separately** - Dedicated modal + dropdown selection in forms
12. **Testing-friendly approach** - Show all UI elements, apply restrictions later
13. **Minimal styling** - Functional over aesthetic, use Tailwind utilities
14. **Project switcher always visible** - Top bar for easy switching
15. **Breadcrumbs on all pages** - Clear navigation context
16. **Empty states with prominent Create button** - Encourage user action

------

**Remember:** The goal of V1 is to **validate functionality**, not to create polished UI/UX. Keep it clean, functional, and testable. Aesthetics and advanced features come later.