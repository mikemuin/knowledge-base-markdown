# ActionItems Module Implementation Guide

## ActionItems Migration & Model

- Create action_items table migration
  - Fields: id, project_id (FK), aitem_group_id (FK nullable)
  - Fields: title, description (text), assigned_to (FK to users, nullable)
  - Fields: target_date, target_week (string, auto-computed)
  - Fields: status (ExecutionStatus enum), priority (Priority enum)
  - Fields: completed_at (nullable timestamp)
  - Fields: is_internal_only (boolean default false), softDeletes()
  - Metadata: created_at, created_by, updated_at, updated_by
  - Indexes: project_id, aitem_group_id, assigned_to, status, priority, target_week, is_internal_only
  - Composite index: (project_id, status, deleted_at)
- Create aitem_groups table migration
  - Fields: id, project_id (FK), title
  - Fields: parent_id (FK to aitem_groups, nullable) - self-referential for 2-level hierarchy
  - Fields: softDeletes()
  - Metadata: created_at, created_by, updated_at, updated_by
  - Indexes: project_id, parent_id
- Run migrations
  - Command: `php artisan migrate`
  - Validate: Check both tables created with correct structure
- Create ActionItem model
  - Location: `app/Modules/ActionItems/Models/ActionItem.php`
  - Add SoftDeletes trait
  - Add LogsActivity trait
  - Relationships: belongsTo(Project), belongsTo(AitemGroup), belongsTo(User as assignedTo)
  - Casts: status to ExecutionStatus enum, priority to Priority enum, target_date to date, completed_at to datetime
- Create AitemGroup model
  - Location: `app/Modules/ActionItems/Models/AitemGroup.php`
  - Add SoftDeletes trait
  - Add LogsActivity trait
  - Relationships: belongsTo(Project), belongsTo(AitemGroup as parent), hasMany(AitemGroup as children), hasMany(ActionItem)
- Create ActionItemObserver (target_week auto-computation)
  - Location: `app/Modules/ActionItems/Observers/ActionItemObserver.php`
  - On creating/updating: Auto-compute target_week from target_date (ISO 8601 format)
  - Use WeekCalculatorService for computation
  - Register in AppServiceProvider
- Create ActionItem factory
  - Location: `database/factories/ActionItemFactory.php`
  - Generate realistic test data with various statuses, priorities, assignments

## ActionItems DTOs

- Create CreateActionItemData DTO
  - Location: `app/Modules/ActionItems/DTOs/CreateActionItemData.php`
  - Properties: project_id, aitem_group_id (nullable), title, description
  - Properties: assigned_to (nullable), target_date, status, priority
  - Properties: is_internal_only
  - Factory method: `fromRequest(Request $request)`
- Create UpdateActionItemData DTO
  - Location: `app/Modules/ActionItems/DTOs/UpdateActionItemData.php`
  - Similar properties to Create (excluding project_id)
  - Include completed_at for marking completion

## ActionItems Repository

- Create ActionItemRepository
  - Location: `app/Modules/ActionItems/Repositories/ActionItemRepository.php`
  - Method: `queryByProject($projectId)` - returns Query Builder (NOT paginated)
  - Method: `findById($id)` - returns model or null
  - Method: `queryByAssignedUser($userId, $projectId = null)` - returns Query Builder
  - Method: `queryInActiveWindow($projectId, $weeks = 2)` - returns Query Builder for items from past + 2 weeks ahead
  - Method: `queryByStatus($projectId, $status)` - returns Query Builder
  - Method: `queryByPriority($projectId, $priority)` - returns Query Builder

## ActionItems Service Layer

- Create ActionItemService
  - Location: `app/Modules/ActionItems/Services/ActionItemService.php`
  - Inject ActionItemRepository
  - Method: `create(CreateActionItemData $data)` - fires ActionItemCreated event
- Implement update method in ActionItemService
  - Method: `update(ActionItem $actionItem, UpdateActionItemData $data)`
  - Fire ActionItemUpdated event
- Implement markAsCompleted method in ActionItemService
  - Method: `markAsCompleted(ActionItem $actionItem, User $user)`
  - Set status to 'completed', set completed_at timestamp
  - Fire ActionItemCompleted event
- Implement delete method in ActionItemService
  - Method: `delete(ActionItem $actionItem)`
  - Soft delete only
  - Fire ActionItemDeleted event
- Implement list method in ActionItemService
  - Method: `list(int $projectId, array $filters = [])`
  - Support filters: status, priority, assigned_to, aitem_group_id, is_internal_only
  - Return paginated results using repository
- Implement assignment methods in ActionItemService
  - Method: `assignTo(ActionItem $actionItem, User $user)`
  - Method: `unassign(ActionItem $actionItem)`
  - Fire ActionItemAssigned/ActionItemUnassigned events
- Create ActionItemPolicy
  - Location: `app/Modules/ActionItems/Policies/ActionItemPolicy.php`
  - Methods: viewAny, view, create, update, delete, assign
  - Admin: Full access
  - Editor: Only assigned projects
  - Viewer: Read-only (excluding internal-only items)
- Register ActionItemPolicy in AuthServiceProvider

## ActionItems Events

- Create ActionItemCreated event
  - Location: `app/Modules/ActionItems/Events/ActionItemCreated.php`
  - Property: ActionItem $actionItem
- Create ActionItemUpdated event
  - Location: `app/Modules/ActionItems/Events/ActionItemUpdated.php`
  - Property: ActionItem $actionItem
- Create ActionItemCompleted event
  - Location: `app/Modules/ActionItems/Events/ActionItemCompleted.php`
  - Property: ActionItem $actionItem
  - For notifications and audit logging
- Create ActionItemAssigned event
  - Location: `app/Modules/ActionItems/Events/ActionItemAssigned.php`
  - Properties: ActionItem $actionItem, User $assignedUser
- Add missing getById method to ActionItemService
  - **CRITICAL FIX:** Method: `getById(int $id): ?ActionItem`
  - Implementation: `return $this->repository->findById($id);`
  - Required by Livewire components in Step 103

## ActionItems Form Requests

- Create StoreActionItemRequest (API)
  - Location: `app/Modules/ActionItems/FormRequests/Api/StoreActionItemRequest.php`
  - Validation rules: project_id, title (required), description, assigned_to (exists:users,id or nullable)
  - Validation: target_date (required, date), status (enum), priority (enum)
  - Authorize based on policy
- Create UpdateActionItemRequest (API)
  - Location: `app/Modules/ActionItems/FormRequests/Api/UpdateActionItemRequest.php`
  - Similar validation rules (project_id not required)
- Create AssignActionItemRequest (API)
  - Location: `app/Modules/ActionItems/FormRequests/Api/AssignActionItemRequest.php`
  - Validate assigned_to (required, exists:users,id)
- Create StoreActionItemRequest (Web)
  - Location: `app/Modules/ActionItems/FormRequests/Web/StoreActionItemRequest.php`
  - Same validation as API version
- Create UpdateActionItemRequest (Web)
  - Location: `app/Modules/ActionItems/FormRequests/Web/UpdateActionItemRequest.php`
  - Same validation as API version

## ActionItems API Resources

- Create ActionItemResource
  - Location: `app/Modules/ActionItems/Resources/ActionItemResource.php`
  - Transform model to API JSON format
  - Include relationships: project, group, assignedTo
  - Format dates consistently
  - Display target_week in human-readable format ("Week 4 of January")
- Create ActionItemCollection
  - Location: `app/Modules/ActionItems/Resources/ActionItemCollection.php`
  - Wrap paginated results
  - Include meta information (filters applied, totals by status/priority)

## ActionItems API Controller

- Create ActionItemController (API)
  - Location: `app/Modules/ActionItems/Controllers/Api/ActionItemController.php`
  - Inject ActionItemService
  - Method: `index()` - list action items with filters (status, priority, assigned_to, group)
- Implement store method in API controller
  - Method: `store(StoreActionItemRequest $request)`
  - Convert request to DTO
  - Call service create method
  - Return ActionItemResource with 201 status
- Implement show method in API controller
  - Method: `show(ActionItem $actionItem)`
  - Authorize via policy
  - Return ActionItemResource
- Implement update method in API controller
  - Method: `update(UpdateActionItemRequest $request, ActionItem $actionItem)`
  - Convert request to DTO
  - Call service update method
  - Return ActionItemResource
- Implement destroy method in API controller
  - Method: `destroy(ActionItem $actionItem)`
  - Authorize via policy
  - Call service delete method (soft delete)
  - Return 204 No Content
- Implement complete method in API controller
  - Route: `POST /api/projects/{project}/action-items/{actionItem}/complete`
  - Method: `complete(ActionItem $actionItem)`
  - Call service markAsCompleted method
  - Return ActionItemResource with updated data
- Implement assign method in API controller
  - Route: `POST /api/projects/{project}/action-items/{actionItem}/assign`
  - Method: `assign(AssignActionItemRequest $request, ActionItem $actionItem)`
  - Call service assignTo method
  - Return ActionItemResource
- Register API routes for ActionItems
  - File: `routes/api.php`
  - Resource routes with `auth:sanctum` middleware
  - Additional routes for complete and assign actions

## ActionItems Web Views (Traditional Blade)

- Create ActionItems index view
  - Location: `app/Modules/ActionItems/Views/index.blade.php`
  - Display: Project info, list of action items, filter controls (status, priority, assigned user, group)
  - Embed Livewire component for interactive list
- Create ActionItems create view
  - Location: `app/Modules/ActionItems/Views/create.blade.php`
  - Form for new action item
  - Use Livewire component for reactive form
  - Dropdown for assignment, group selection
- Create ActionItems show view
  - Location: `app/Modules/ActionItems/Views/show.blade.php`
  - Display full action item details
  - Show assigned user, priority badge, status badge
  - Display target_week in readable format
  - Show completion status
- Create ActionItems edit view
  - Location: `app/Modules/ActionItems/Views/edit.blade.php`
  - Editable form with assignment controls
  - Use Livewire component for reactive form

## ActionItems Web Controller

- Create ActionItemController (Web)
  - Location: `app/Modules/ActionItems/Controllers/Web/ActionItemController.php`
  - Method: `index(Project $project)` - returns view with action items
  - **CRITICAL:** Use namespace notation: `view('action-items::index')`
- Implement create method in Web controller
  - Method: `create(Project $project)`
  - Load users for assignment dropdown
  - Load groups for group selection
  - Return create view with project context
- Implement show method in Web controller
  - Method: `show(Project $project, ActionItem $actionItem)`
  - **CRITICAL:** Use namespace notation: `view('action-items::show')`
  - Pass action item to view
- Implement edit method in Web controller
  - Method: `edit(Project $project, ActionItem $actionItem)`
  - Load users and groups
  - Return edit view
- Implement store method in Web controller
  - Method: `store(StoreActionItemRequest $request, Project $project)`
  - Create via service
  - Redirect to show page with success message
- Implement update method in Web controller
  - Method: `update(UpdateActionItemRequest $request, Project $project, ActionItem $actionItem)`
  - Update via service
  - **CRITICAL:** Redirect using proper route naming
  - Flash success message
- Implement destroy method in Web controller
  - Method: `destroy(Project $project, ActionItem $actionItem)`
  - Soft delete via service
  - Redirect to index with success message
- Implement complete method in Web controller
  - Route: `POST /projects/{project}/action-items/{actionItem}/complete`
  - Method: `complete(Project $project, ActionItem $actionItem)`
  - Call service markAsCompleted method
  - Flash success message
- Register Web routes for ActionItems
  - File: `routes/web.php`
  - Resource routes with `auth` middleware
  - Additional route for complete action

## ActionItems ViewModels (Optional for complex views)

- Create ActionItemIndexViewModel
  - Location: `app/Modules/ActionItems/ViewModels/ActionItemIndexViewModel.php`
  - Prepare data for index view (project, action items, filters)
  - Include summary counts by status and priority
- Create ActionItemShowViewModel
  - Location: `app/Modules/ActionItems/ViewModels/ActionItemShowViewModel.php`
  - Format target_week for display
  - Calculate days until/overdue
  - Include assignment history

## ActionItems Livewire Components

- Create ActionItemList Livewire component class
  - Location: `app/Modules/ActionItems/Livewire/ActionItemList.php`
  - Properties: projectId, filters (status, priority, assigned_to, group_id), sortField, sortDirection
  - Methods: render, applyFilters, sort, delete, toggleComplete
  - Pagination support
- Create ActionItemList component view
  - Location: `resources/views/livewire/action-items/list.blade.php`
  - Table with filter controls (status, priority, assigned user, group dropdowns)
  - Action buttons (view, edit, delete, complete)
  - Display priority badges (color-coded)
  - Pagination links
- Create ActionItemTable Livewire component class
  - Location: `app/Modules/ActionItems/Livewire/ActionItemTable.php`
  - Focused component for just the table display
  - Properties: actionItems collection
  - Real-time reactive updates
- Create ActionItemTable component view
  - Location: `resources/views/livewire/action-items/table.blade.php`
  - Clean table markup with wire:model bindings
  - Priority and status badges
- Create ActionItemForm Livewire component class
  - Location: `app/Modules/ActionItems/Livewire/ActionItemForm.php`
  - Properties: actionItem (nullable for create mode), form fields
  - Methods: mount, save, rules
  - Handle both create and edit modes
  - **USES getById() from service layer**
  - Include user assignment dropdown
- Create ActionItemForm component view
  - Location: `resources/views/livewire/action-items/form.blade.php`
  - Form fields with wire:model.live for reactive validation
  - Priority selector (radio buttons or dropdown)
  - User assignment dropdown (searchable)
  - Group selection dropdown
  - Submit button with loading state
  - Display validation errors inline
- Create ActionItemCard Livewire component class
  - Location: `app/Modules/ActionItems/Livewire/ActionItemCard.php`
  - Properties: actionItem model
  - For displaying summary in dashboards
  - Compact card format with priority indicator
- Create ActionItemCard component view
  - Location: `resources/views/livewire/action-items/card.blade.php`
  - Card layout with priority badge, status badge, target week
  - Show assigned user avatar/name
  - Link to full action item

## Testing & Documentation

- Test complete ActionItems module end-to-end
  - **API Testing:** Use Postman/Insomnia
    - Create action item via POST
    - Update action item
    - Assign to user
    - Mark as completed
    - Filter by status, priority, assigned user
  - **Web Testing:** Manual browser testing
    - Navigate through all CRUD operations
    - Test assignment workflow
    - Test completion workflow
    - Test grouping functionality
  - **Livewire Testing:** Test reactive components
    - Verify real-time updates
    - Test form validation
    - Test filter interactions
- Create feature tests for ActionItems
  - Location: `tests/Feature/Modules/ActionItems/`
  - Files: `ApiActionItemTest.php`, `WebActionItemTest.php`, `LivewireActionItemTest.php`
  - Test all CRUD operations
  - Test RBAC enforcement
  - Test assignment workflow
  - Test completion workflow
  - Test target_week auto-computation
  - Test grouping and hierarchy (max 2 levels)
  - Command: `php artisan test --filter=ActionItem`
- Create unit tests for ActionItem service
  - Location: `tests/Unit/Modules/ActionItems/Services/ActionItemServiceTest.php`
  - Test cases:
    - Create action item
    - Update action item
    - Assign to user
    - Unassign from user
    - Mark as completed (verify timestamp)
    - Delete action item
    - Query in active window (past + 2 weeks)
  - Mock repository dependencies
- Document ActionItems API in PHPDoc
  - File: `app/Modules/ActionItems/Controllers/Api/ActionItemController.php`
  - Add comprehensive docblocks to each method
  - Include: description, parameters, return types, response codes
  - Document filter parameters (status, priority, assigned_to, group_id)
  - For future API documentation generation (Scribe, OpenAPI)

------

## Key Differences from StatusReports

1. **HAS Grouping** - AitemGroup model and table with 2-level hierarchy
2. **NO Immutability** - Can always be edited (no locking mechanism)
3. **HAS target_week** - Auto-computed from target_date (same pattern as StatusReports)
4. **HAS Assignment** - assigned_to field linking to users
5. **HAS Priority** - Manual priority setting (low, medium, high, critical)
6. **USES ExecutionStatus enum** - (pending, on_track, at_risk, delayed, blocked, completed, cancelled)
7. **NO snapshot_data** - No complex snapshot generation
8. **HAS completed_at** - Timestamp for tracking completion
9. **Special Query** - Active window is past + 2 weeks (not 4 weeks like Milestones)
10. **Additional Actions** - Assign, unassign, mark complete

## Custom Business Logic Notes

**Model Scopes:**

- `scopeInActiveWindow($query, $weeks = 2)` - Non-completed/cancelled from past + specified weeks ahead
- `scopeAssignedTo($query, $userId)` - Filter by assigned user
- `scopeByPriority($query, $priority)` - Filter by priority

**Model Methods:**

- `markAsCompleted()` - Set status to completed, set completed_at
- `isOverdue()` - Check if target_date has passed and not completed
- `assignTo(User $user)` - Assign action item
- `unassign()` - Remove assignment

**Observer:**

- Auto-compute target_week when target_date changes (creating/updating)
- Use `WeekCalculatorService::computeTargetWeek($date)` from Shared

**Service Additional Methods:**

- `getUpcomingActionItems(Project $project, int $weeks = 2)` - For dashboard
- `getOverdueActionItems(Project $project)` - For alerts
- `getActionItemsByAssignee(User $user, Project $project = null)` - User workload

------

## Validation Checklist

After implementation, verify:

- [ ] Migration creates both action_items and aitem_groups tables with correct fields and indexes
- [ ] Model has correct relationships and traits (SoftDeletes, LogsActivity)
- [ ] Group model enforces 2-level hierarchy maximum
- [ ] Observer auto-computes target_week when target_date changes
- [ ] Repository returns Query Builder (not paginated)
- [ ] Service layer has all CRUD + assignment + completion methods
- [ ] Policy enforces RBAC correctly (Admin/Editor/Viewer)
- [ ] API endpoints return correct status codes and data format
- [ ] Web pages render without errors
- [ ] Livewire components are reactive (filters, assignment, completion)
- [ ] All tests pass: `php artisan test --filter=ActionItem`
- [ ] Routes registered correctly in api.php and web.php
- [ ] Assignment workflow functions correctly
- [ ] Priority badges display with correct colors
- [ ] Target week displays in readable format ("Week 4 of January")

------

## Implementation Summary

**Estimated Time:** 4-5 hours **Complexity:** Medium (similar to StatusReports but adds assignment and priority features) **Architecture:** Modular monolith with triple interface support (API, Web, Livewire)