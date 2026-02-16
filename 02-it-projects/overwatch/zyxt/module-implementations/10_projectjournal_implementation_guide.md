# ProjectJournal Module - Implementation Guide

**Module:** ProjectJournal (KNOWLEDGE Domain)
**Purpose:** Social-style activity feed with entity linking - NO hierarchical grouping
**Dependencies:** Project, JournalEntryType enum, EntityLinkService
**Estimated Steps:** 55

---

## Module Overview

ProjectJournal is a chronological, social-media-style feed of project activities. It's the ONLY module that can link to other entities via EntityLink.

### Key Features
- **No grouping:** Flat chronological feed only
- **Entry types:** Update, Milestone, Decision, Meeting, Risk, Issue, General
- **Entity linking:** Can reference ANY other module's entities
- **Rich content:** Markdown support for content
- **Immutability:** Entries cannot be edited, only soft deleted
- **Social features:** Author, timestamp, linked entities

---

## Database Schema

### Step 1: Create Migration

**project_journal_entries table:**
```php
$table->id();
$table->foreignId('project_id')->constrained()->cascadeOnDelete();
$table->string('entry_type', 30)->default('general'); // JournalEntryType enum
$table->text('content'); // Markdown supported
$table->dateTime('entry_date'); // When the activity occurred
$table->foreignId('author_id')->constrained('users');
$table->boolean('is_pinned')->default(false);
$table->boolean('is_internal_only')->default(false);
$table->foreignId('created_by')->constrained('users');
$table->timestamps();
$table->softDeletes();

$table->index(['project_id', 'entry_date', 'deleted_at']);
$table->index('entry_type');
$table->index('author_id');
$table->index('is_pinned');
```

**Note:** EntityLinks are stored separately in the Shared/EntityLinks table.

### Step 2: Run Migration

---

## Models

### Step 3: Create JournalEntry Model

**NO HasGroups trait** (flat structure)

**Casts:**
- `entry_date` → datetime
- `entry_type` → JournalEntryType enum
- `is_pinned` → boolean
- `is_internal_only` → boolean

**Relationships:**
```php
public function entityLinks()
{
    return $this->morphMany(EntityLink::class, 'source');
}

public function linkedEntities()
{
    // Returns collection of all linked entities (polymorphic)
    return $this->entityLinks()->with('target')->get()->pluck('target');
}
```

**Methods:**
```php
public function pin(): void
{
    $this->update(['is_pinned' => true]);
}

public function unpin(): void
{
    $this->update(['is_pinned' => false]);
}

public function linkEntity(string $targetType, int $targetId, string $linkType, ?string $description = null): EntityLink
{
    return app(EntityLinkService::class)->createLinkFromParams(
        JournalEntry::class,
        $this->id,
        $targetType,
        $targetId,
        $linkType,
        $description,
        auth()->id()
    );
}
```

---

## DTOs (Steps 4-5)

- `CreateJournalEntryData` - Include links array
- `UpdateJournalEntryData` - IMMUTABLE: Cannot update content, only metadata like is_pinned

---

## Repository (Step 6)

**JournalEntryRepository methods:**
- `getEntriesForProject(projectId, limit = 50, offset = 0)`
- `getPinnedEntries(projectId)`
- `getEntriesByType(projectId, type)`
- `getEntriesByDateRange(projectId, startDate, endDate)`
- `searchEntries(projectId, searchTerm)`

---

## Service (Step 7)

**JournalEntryService key methods:**

```php
public function createEntry(CreateJournalEntryData $data): JournalEntry
{
    $entry = $this->repository->create($data);

    // Create entity links if provided
    if (isset($data->links) && is_array($data->links)) {
        foreach ($data->links as $link) {
            $entry->linkEntity(
                $link['target_type'],
                $link['target_id'],
                $link['link_type'],
                $link['description'] ?? null
            );
        }
    }

    event(new JournalEntryCreated($entry));
    return $entry;
}

public function pinEntry(int $id): JournalEntry
{
    $entry = $this->repository->findById($id);
    $entry->pin();
    return $entry;
}

public function getActivityFeed(int $projectId, int $limit = 50): Collection
{
    // Returns feed with pinned entries at top, then chronological
    $pinned = $this->repository->getPinnedEntries($projectId);
    $recent = $this->repository->getEntriesForProject($projectId, $limit)
        ->where('is_pinned', false);

    return $pinned->concat($recent);
}
```

---

## Events (Steps 8-9)

- `JournalEntryCreated`
- `JournalEntryDeleted`

---

## Policy (Steps 10-11)

**JournalEntryPolicy:**

```php
public function update(User $user, JournalEntry $entry): bool
{
    // Can only update metadata (pinned status), not content
    // Only admins or author can update
    if ($this->isAdmin($user)) return true;
    return $entry->author_id === $user->id;
}

public function delete(User $user, JournalEntry $entry): bool
{
    // Only admins or author can delete
    if ($this->isAdmin($user)) return true;
    return $entry->author_id === $user->id;
}
```

---

## API Interface (Steps 12-23)

### Step 12: JournalEntryResource

**Include:**
- Entry content (rendered markdown)
- Linked entities (with type and title)
- Author details
- Pin status

### Step 13: JournalEntryController (API)

**Additional Endpoints:**

```php
public function activityFeed(Project $project, Request $request)
{
    $this->authorize('view', $project);
    $limit = $request->query('limit', 50);
    $entries = $this->service->getActivityFeed($project->id, $limit);
    return JournalEntryResource::collection($entries);
}

public function pin(JournalEntry $entry)
{
    $this->authorize('update', $entry);
    $entry = $this->service->pinEntry($entry->id);
    return new JournalEntryResource($entry);
}

public function search(Project $project, Request $request)
{
    $this->authorize('view', $project);
    $entries = $this->repository->searchEntries($project->id, $request->query('q'));
    return JournalEntryResource::collection($entries);
}
```

### Step 14-17: Create FormRequests (API)

- `StoreJournalEntryRequest` (validate links array structure)
- `UpdateJournalEntryRequest` (only allow metadata updates)

### Step 18: Register API Routes

```php
Route::get('projects/{project}/journal/feed', [JournalEntryController::class, 'activityFeed']);
Route::get('projects/{project}/journal/search', [JournalEntryController::class, 'search']);
Route::post('journal-entries/{entry}/pin', [JournalEntryController::class, 'pin']);
Route::post('journal-entries/{entry}/unpin', [JournalEntryController::class, 'unpin']);
```

---

## Web Interface (Steps 19-35)

### Step 19: JournalEntryController (Web)

**Methods:**
- `feed(Project $project)` - Main activity feed view
- `create(Project $project)` - Create new entry with entity linking
- `show(JournalEntry $entry)` - Detail view
- `search(Project $project, Request $request)` - Search interface

### Step 20-24: Create Views

- `feed.blade.php` - Infinite scroll feed, pinned entries at top
- `create.blade.php` - Rich editor with entity linking interface
- `show.blade.php` - Single entry with linked entities
- `search.blade.php` - Search results
- `partials/entry-card.blade.php` - Reusable entry display

### Step 25-28: Create ViewModels

- `JournalFeedViewModel`
- `JournalEntryFormViewModel` (includes entity selection data)
- `JournalEntryDetailViewModel`
- `JournalSearchViewModel`

### Step 29-32: Create FormRequests (Web)

### Step 33: Register Web Routes

```php
Route::get('projects/{project}/journal', [JournalEntryController::class, 'feed'])
    ->name('projects.journal.feed');
Route::get('projects/{project}/journal/search', [JournalEntryController::class, 'search'])
    ->name('projects.journal.search');
Route::post('journal-entries/{entry}/pin', [JournalEntryController::class, 'pin'])
    ->name('journal-entries.pin');
```

---

## Livewire Components (Steps 34-38)

### Step 34: JournalEntryForm Component

**Features:**
- Markdown editor
- Entry type selector
- Entity linking interface (search and select from other modules)
- Real-time preview

### Step 35: ActivityFeed Component

**Features:**
- Infinite scroll
- Real-time updates (optional)
- Filter by entry type
- Search functionality

### Step 36: EntityLinkSelector Component

**Purpose:** Reusable component for selecting entities to link

**Features:**
- Module type dropdown
- Entity search within selected module
- Display selected links with remove option

### Step 37-38: Create Livewire Views

---

## Testing (Steps 39-45)

### Step 39: JournalEntryServiceTest

- Create entry with links
- Pin/unpin entry
- Activity feed ordering (pinned first)
- Search functionality

### Step 40: EntityLinkingTest

- Link to milestone
- Link to risk
- Link to multiple entities
- Remove link

### Step 41-45: Additional Tests

- Immutability enforcement
- Policy tests
- API endpoint tests
- Livewire component tests

---

## Integration Notes

### EntityLink Usage

```php
// When creating journal entry via service
$entry = $service->createEntry(new CreateJournalEntryData([
    'project_id' => 1,
    'content' => 'Completed milestone review',
    'entry_type' => JournalEntryType::MILESTONE,
    'entry_date' => now(),
    'author_id' => auth()->id(),
    'links' => [
        [
            'target_type' => Milestone::class,
            'target_id' => 5,
            'link_type' => 'references',
            'description' => 'Q1 Milestone'
        ],
        [
            'target_type' => Risk::class,
            'target_id' => 12,
            'link_type' => 'mentions',
        ]
    ]
]));
```

### Querying Linked Entities

```php
// Get all entities linked to a journal entry
$entry->linkedEntities(); // Collection of mixed entity types

// Get links grouped by type
$grouped = app(EntityLinkService::class)->getLinksGroupedByType($entry);
// Returns: ['milestones' => [...], 'risks' => [...]]
```

---

## UI Considerations

- **Feed display:** Show entry type icon, author avatar, timestamp, content preview
- **Pinned entries:** Visual indicator (pin icon), always at top of feed
- **Linked entities:** Display as chips/badges with click-through to entity
- **Markdown rendering:** Use a library like CommonMark for rendering
- **Infinite scroll:** Load 50 entries at a time, append on scroll

---

**Implementation Ready:** ✅
**Next Module:** MeetingMinutes
