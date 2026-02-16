# MeetingMinutes Module - Implementation Guide

**Module:** MeetingMinutes (KNOWLEDGE Domain)
**Purpose:** Formal meeting records - NO hierarchical grouping
**Dependencies:** Project, MeetingEvent (optional linkage)
**Estimated Steps:** 50

---

## Module Overview

MeetingMinutes are formal records of meeting discussions, decisions, and action items. They can optionally link to MeetingEvents but exist independently.

### Key Features
- **No grouping:** Flat chronological structure
- **Structured format:** Attendees, agenda, discussion, decisions, action items
- **Optional MeetingEvent link:** Connect to scheduled meeting
- **Approval workflow:** Draft → Approved
- **Immutability:** Once approved, cannot be edited

---

## Database Schema

### Step 1: Create Migration

```php
$table->id();
$table->foreignId('project_id')->constrained()->cascadeOnDelete();
$table->foreignId('meeting_event_id')->nullable()->constrained()->nullOnDelete();
$table->string('title');
$table->date('meeting_date');
$table->dateTime('start_time')->nullable();
$table->dateTime('end_time')->nullable();
$table->string('location', 255)->nullable();
$table->json('attendees'); // Array of user IDs
$table->text('agenda')->nullable();
$table->text('discussion_notes'); // Main content (required)
$table->text('decisions_made')->nullable();
$table->text('action_items')->nullable();
$table->text('next_steps')->nullable();
$table->foreignId('recorded_by')->constrained('users');
$table->boolean('is_approved')->default(false);
$table->date('approved_at')->nullable();
$table->foreignId('approved_by')->nullable()->constrained('users')->nullOnDelete();
$table->boolean('is_internal_only')->default(false);
// timestamps, soft deletes, indexes
$table->index(['project_id', 'meeting_date', 'deleted_at']);
$table->index('meeting_event_id');
$table->index('is_approved');
```

### Step 2: Run Migration

---

## Models (Steps 3-4)

**MeetingMinute:**

**Casts:** meeting_date, start/end_time (datetimes), attendees (array), approved_at (date), is_approved (boolean)

**Methods:**

```php
public function approve(int $approverId): void
{
    if ($this->is_approved) {
        throw new \DomainException('Minutes already approved');
    }

    $this->update([
        'is_approved' => true,
        'approved_at' => now(),
        'approved_by' => $approverId,
    ]);
}

public function canBeModified(): bool
{
    return !$this->is_approved;
}
```

---

## DTOs (Steps 5-7)

- `CreateMeetingMinuteData` - Include attendees array
- `UpdateMeetingMinuteData` - Only if not approved
- NO delete DTO (use service method)

---

## Repository (Step 8)

**Methods:**
- `getMinutesForProject(projectId)`
- `getApprovedMinutes(projectId)`
- `getUnapprovedMinutes(projectId)`
- `getMinutesByDateRange(projectId, startDate, endDate)`

---

## Service (Step 9)

**Key methods:**

```php
public function createMinutes(CreateMeetingMinuteData $data): MeetingMinute
{
    $minutes = $this->repository->create($data);
    event(new MeetingMinutesCreated($minutes));
    return $minutes;
}

public function updateMinutes(int $id, UpdateMeetingMinuteData $data): MeetingMinute
{
    $minutes = $this->repository->findById($id);

    if ($minutes->is_approved) {
        throw new \DomainException('Cannot update approved minutes');
    }

    return $this->repository->update($id, $data);
}

public function approveMinutes(int $id, int $approverId): MeetingMinute
{
    $minutes = $this->repository->findById($id);
    $minutes->approve($approverId);
    event(new MeetingMinutesApproved($minutes));
    return $minutes;
}
```

---

## Events (Steps 10-12)

- `MeetingMinutesCreated`
- `MeetingMinutesApproved`
- `MeetingMinutesDeleted`

---

## Policy (Steps 13-14)

**Additional methods:**

```php
public function update(User $user, MeetingMinute $minutes): bool
{
    if ($minutes->is_approved) return false; // Cannot update approved

    if ($this->isAdmin($user)) return true;
    if ($this->isEditor($user) && $this->canEdit($user, $minutes)) return true;
    return $minutes->recorded_by === $user->id; // Recorder can update own
}

public function approve(User $user, MeetingMinute $minutes): bool
{
    return $this->isAdmin($user); // Only admins approve
}
```

---

## API/Web/Livewire (Steps 15-45)

Standard triple interface implementation with:

**Additional API Endpoints:**
- `POST /meeting-minutes/{minutes}/approve`
- `GET /projects/{project}/meeting-minutes/pending-approval`

**Additional Web Views:**
- `approval-queue.blade.php`
- `show.blade.php` (with approval status badge)

**Livewire Components:**
- `MeetingMinutesForm` - Structured sections (agenda, discussion, decisions, actions)
- `MinutesApprovalCard`

---

## Testing (Steps 46-50)

- Create minutes
- Approve minutes
- Prevent editing approved minutes
- Policy tests

---

**Implementation Ready:** ✅
**Next Module:** Stakeholders
