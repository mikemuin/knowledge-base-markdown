# MeetingEvents Module - Implementation Guide

**Module:** MeetingEvents (EXECUTION Domain)
**Purpose:** Governance meeting scheduling with hierarchical grouping
**Dependencies:** Project, MeetingEventStatus enum, MeetingType enum, HasGroups trait
**Estimated Steps:** 55

---

## Module Overview

MeetingEvents track governance meetings, steering committees, status reviews, and other scheduled project events. Not a full calendar system, but governance-focused meeting tracking.

### Key Features
- **2-level grouping:** MeetingGroup (e.g., Steering Committee > Monthly Reviews)
- **Type classification:** Steering, Status Review, Working Session, Stakeholder Briefing
- **Status tracking:** Scheduled, Completed, Cancelled, Rescheduled
- **Recurrence:** Track recurring meeting series
- **Attendance:** Track expected vs actual attendees
- **Meeting minutes link:** Reference to MeetingMinutes module

---

## Database Schema

### Step 1-2: Create Migrations

**meeting_groups table:** Standard group structure

**meeting_events table:**
```php
$table->id();
$table->foreignId('project_id')->constrained()->cascadeOnDelete();
$table->foreignId('meeting_group_id')->nullable()->constrained()->nullOnDelete();
$table->string('title');
$table->text('description')->nullable();
$table->string('type', 30); // MeetingType enum
$table->string('status', 20)->default('scheduled'); // MeetingEventStatus enum
$table->dateTime('scheduled_start');
$table->dateTime('scheduled_end');
$table->dateTime('actual_start')->nullable();
$table->dateTime('actual_end')->nullable();
$table->string('location', 255)->nullable();
$table->string('meeting_link', 500)->nullable(); // Zoom/Teams URL
$table->text('agenda')->nullable();
$table->boolean('is_recurring')->default(false);
$table->string('recurrence_pattern', 50)->nullable(); // weekly, monthly, etc.
$table->foreignId('organizer_id')->constrained('users');
$table->json('expected_attendees')->nullable(); // Array of user IDs
$table->json('actual_attendees')->nullable(); // Array of user IDs
$table->foreignId('meeting_minutes_id')->nullable()->constrained('meeting_minutes')->nullOnDelete();
$table->boolean('is_internal_only')->default(false);
// timestamps, soft deletes, indexes
$table->index(['project_id', 'scheduled_start']);
$table->index('status');
$table->index('organizer_id');
```

### Step 3: Run Migrations

---

## Models

### Step 4-5: Create Models

**MeetingGroup:** Standard

**MeetingEvent:**

**Casts:**
- `scheduled_start` → datetime
- `scheduled_end` → datetime
- `actual_start` → datetime
- `actual_end` → datetime
- `type` → MeetingType enum
- `status` → MeetingEventStatus enum
- `expected_attendees` → array
- `actual_attendees` → array
- `is_recurring` → boolean

**Relationships:**
- `project`, `meetingGroup`, `organizer`, `meetingMinutes`
- `expectedAttendeeUsers()` - belongsToMany (derived from JSON)

**Methods:**

```php
public function complete(Carbon $actualStart, Carbon $actualEnd, array $actualAttendees): void
{
    $this->update([
        'status' => MeetingEventStatus::COMPLETED,
        'actual_start' => $actualStart,
        'actual_end' => $actualEnd,
        'actual_attendees' => $actualAttendees,
        'updated_by' => auth()->id(),
    ]);
}

public function cancel(string $reason): void
{
    $this->update([
        'status' => MeetingEventStatus::CANCELLED,
        'description' => $this->description . "\n\nCancellation reason: " . $reason,
        'updated_by' => auth()->id(),
    ]);
}

public function reschedule(Carbon $newStart, Carbon $newEnd): void
{
    $this->update([
        'status' => MeetingEventStatus::RESCHEDULED,
        'scheduled_start' => $newStart,
        'scheduled_end' => $newEnd,
        'updated_by' => auth()->id(),
    ]);
}

public function getAttendanceRate(): ?float
{
    if (!$this->actual_attendees || !$this->expected_attendees) return null;
    $expected = count($this->expected_attendees);
    $actual = count($this->actual_attendees);
    return $expected > 0 ? ($actual / $expected) * 100 : null;
}
```

---

## DTOs

### Step 6-9: Create DTOs

Include `expected_attendees` (array), `actual_attendees` (array), recurrence fields

---

## Repositories

### Step 10-11: Create Repositories

**MeetingEventRepository additional methods:**
- `getUpcomingMeetings(projectId, dayRange = 30)`
- `getPastMeetings(projectId, dayRange = 90)`
- `getMeetingsByType(projectId, type)`
- `getMeetingsByOrganizer(organizerId)`
- `getRecurringMeetingSeries(projectId, recurrencePattern)`

---

## Services

### Step 12-13: Create Services

**MeetingEventService key methods:**

```php
public function completeMeeting(
    int $id,
    Carbon $actualStart,
    Carbon $actualEnd,
    array $actualAttendees
): MeetingEvent {
    $meeting = $this->repository->findById($id);
    $meeting->complete($actualStart, $actualEnd, $actualAttendees);
    event(new MeetingCompleted($meeting));
    return $meeting;
}

public function rescheduleMeeting(int $id, Carbon $newStart, Carbon $newEnd): MeetingEvent
{
    $meeting = $this->repository->findById($id);
    $meeting->reschedule($newStart, $newEnd);
    event(new MeetingRescheduled($meeting));
    return $meeting;
}

public function createRecurringSeries(
    CreateMeetingEventData $data,
    Carbon $startDate,
    Carbon $endDate,
    string $pattern // 'weekly', 'biweekly', 'monthly'
): Collection {
    $meetings = collect();
    $current = $startDate->copy();

    while ($current->lte($endDate)) {
        $meetingData = clone $data;
        $meetingData->scheduled_start = $current->copy();
        $meetingData->scheduled_end = $current->copy()->addHours(1);
        $meetingData->is_recurring = true;
        $meetingData->recurrence_pattern = $pattern;

        $meetings->push($this->createMeetingEvent($meetingData));

        $current = match($pattern) {
            'weekly' => $current->addWeek(),
            'biweekly' => $current->addWeeks(2),
            'monthly' => $current->addMonth(),
            default => throw new \InvalidArgumentException("Invalid pattern: $pattern"),
        };
    }

    return $meetings;
}
```

---

## Events

### Step 14-17: Create Events

- `MeetingScheduled`
- `MeetingCompleted`
- `MeetingCancelled`
- `MeetingRescheduled`

---

## Policies

### Step 18-20: Create and Register Policies

**Additional Policy Method:**

```php
public function complete(User $user, MeetingEvent $meeting): bool
{
    // Only organizer or admin can complete
    if ($this->isAdmin($user)) return true;
    return $meeting->organizer_id === $user->id;
}
```

---

## API Interface

### Step 21-22: Create Resources

Include attendance rate, attendee details, meeting minutes link

### Step 23-24: Create API Controllers

**Additional Endpoints:**

```php
public function complete(MeetingEvent $meeting, CompleteMeetingRequest $request)
{
    $this->authorize('complete', $meeting);

    $meeting = $this->service->completeMeeting(
        $meeting->id,
        Carbon::parse($request->input('actual_start')),
        Carbon::parse($request->input('actual_end')),
        $request->input('actual_attendees')
    );

    return new MeetingEventResource($meeting);
}

public function reschedule(MeetingEvent $meeting, RescheduleMeetingRequest $request)
{
    $this->authorize('update', $meeting);

    $meeting = $this->service->rescheduleMeeting(
        $meeting->id,
        Carbon::parse($request->input('new_start')),
        Carbon::parse($request->input('new_end'))
    );

    return new MeetingEventResource($meeting);
}

public function upcoming(Project $project)
{
    $this->authorize('view', $project);
    $meetings = $this->service->getUpcomingMeetings($project->id);
    return MeetingEventResource::collection($meetings);
}
```

### Step 25-30: Create FormRequests (API)

Include `CompleteMeetingRequest`, `RescheduleMeetingRequest`

### Step 31: Register API Routes

```php
Route::get('projects/{project}/meetings/upcoming', [MeetingEventController::class, 'upcoming']);
Route::post('meetings/{meeting}/complete', [MeetingEventController::class, 'complete']);
Route::post('meetings/{meeting}/reschedule', [MeetingEventController::class, 'reschedule']);
```

---

## Web Interface

### Step 32-33: Create Web Controllers

**Additional Method:**

```php
public function calendar(Project $project)
{
    $viewModel = new MeetingCalendarViewModel($project, $this->service);
    return view('meetings::calendar', $viewModel->toArray());
}
```

### Step 34-39: Create Views

- `index.blade.php`
- `calendar.blade.php` (month/week view)
- `create.blade.php` (with recurring options)
- `edit.blade.php`
- `show.blade.php` (with attendance tracking)
- `groups/manage.blade.php`

### Step 40-44: Create ViewModels

### Step 45-49: Create FormRequests (Web)

### Step 50: Register Web Routes

```php
Route::get('projects/{project}/meetings/calendar', [MeetingEventController::class, 'calendar'])
    ->name('projects.meetings.calendar');
Route::post('meetings/{meeting}/complete', [MeetingEventController::class, 'complete'])
    ->name('meetings.complete');
```

---

## Livewire Components

### Step 51-52: Create Components

- `MeetingCalendar` - Interactive calendar view
- `MeetingForm` - With datetime picker, attendee multi-select, recurring options
- `AttendanceTracker` - Check-in interface

### Step 53-54: Create Livewire Views

---

## Testing

### Step 55: Tests

- Complete meeting workflow
- Reschedule meeting
- Create recurring series
- Attendance rate calculation
- Policy tests for organizer permissions

---

**Implementation Ready:** âœ…
**Next Module:** Risks
