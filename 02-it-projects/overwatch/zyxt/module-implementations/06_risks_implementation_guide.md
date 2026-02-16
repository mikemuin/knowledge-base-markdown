# Risks Module - Implementation Guide

**Module:** Risks (CONTROL Domain)
**Purpose:** Future uncertainties requiring mitigation with hierarchical grouping
**Dependencies:** Project, RiskStatus, RiskCategory, RiskSeverity, RiskProbability, RiskImpact, RiskProximity enums, HasGroups trait
**Estimated Steps:** 62

---

## Module Overview

Risks track future uncertainties that could impact project success. They include probability/impact assessment, mitigation strategies, and risk ownership. This is a forward-looking control mechanism.

### Key Features
- **2-level grouping:** RiskGroup
- **Comprehensive categorization:** Technical, Resource, Schedule, Budget, External, Compliance
- **Probability & Impact matrix:** Low/Medium/High/Critical scoring
- **Risk severity:** Auto-calculated from probability × impact
- **Proximity tracking:** Imminent, Near-term, Mid-term, Long-term
- **Mitigation planning:** Strategy, contingency, owner
- **Status lifecycle:** Identified → Analyzing → Mitigating → Monitored → Closed

---

## Database Schema

### Step 1-2: Create Migrations

**risk_groups table:** Standard group structure

**risks table:**
```php
$table->id();
$table->foreignId('project_id')->constrained()->cascadeOnDelete();
$table->foreignId('risk_group_id')->nullable()->constrained()->nullOnDelete();
$table->string('title');
$table->text('description')->nullable();
$table->string('category', 30); // RiskCategory enum
$table->string('status', 20)->default('identified'); // RiskStatus enum
$table->string('probability', 20); // RiskProbability enum
$table->string('impact', 20); // RiskImpact enum
$table->string('severity', 20); // RiskSeverity enum (auto-calculated)
$table->string('proximity', 20); // RiskProximity enum
$table->date('identified_date');
$table->date('review_date')->nullable(); // Next review
$table->text('mitigation_strategy')->nullable();
$table->text('contingency_plan')->nullable();
$table->foreignId('owner_id')->nullable()->constrained('users')->nullOnDelete();
$table->date('closure_date')->nullable();
$table->text('closure_notes')->nullable();
$table->boolean('is_internal_only')->default(false);
// timestamps, soft deletes, indexes
$table->index(['project_id', 'status', 'deleted_at']);
$table->index('severity');
$table->index('category');
$table->index('owner_id');
```

### Step 3: Run Migrations

---

## Models

### Step 4-5: Create Models

**RiskGroup:** Standard

**Risk:**

**Casts:**
- `identified_date` → date
- `review_date` → date
- `closure_date` → date
- `category` → RiskCategory enum
- `status` → RiskStatus enum
- `probability` → RiskProbability enum
- `impact` → RiskImpact enum
- `severity` → RiskSeverity enum
- `proximity` → RiskProximity enum

**Methods:**

```php
public function calculateSeverity(): RiskSeverity
{
    // Probability × Impact matrix
    $matrix = [
        // [probability][impact] => severity
        'low' => ['low' => 'low', 'medium' => 'low', 'high' => 'medium', 'critical' => 'high'],
        'medium' => ['low' => 'low', 'medium' => 'medium', 'high' => 'high', 'critical' => 'critical'],
        'high' => ['low' => 'medium', 'medium' => 'high', 'high' => 'critical', 'critical' => 'critical'],
        'critical' => ['low' => 'high', 'medium' => 'critical', 'high' => 'critical', 'critical' => 'critical'],
    ];

    $prob = $this->probability->value;
    $imp = $this->impact->value;

    return RiskSeverity::from($matrix[$prob][$imp]);
}

public function close(string $closureNotes): void
{
    $this->update([
        'status' => RiskStatus::CLOSED,
        'closure_date' => now(),
        'closure_notes' => $closureNotes,
        'updated_by' => auth()->id(),
    ]);
}

public function escalate(): void
{
    // Increase severity if possible
    if ($this->severity !== RiskSeverity::CRITICAL) {
        $this->update(['severity' => RiskSeverity::CRITICAL]);
    }
}

public function requiresReview(): bool
{
    return $this->review_date && $this->review_date <= now()->toDateString();
}
```

### Step 6: Create Observer

Auto-compute `severity` when `probability` or `impact` changes:

```php
public function saving(Risk $risk): void
{
    if ($risk->isDirty(['probability', 'impact'])) {
        $risk->severity = $risk->calculateSeverity();
    }
}
```

---

## DTOs

### Step 7-10: Create DTOs

Include mitigation_strategy, contingency_plan, review_date

---

## Repositories

### Step 11-12: Create Repositories

**RiskRepository additional methods:**
- `getActiveby(projectId)`
- `getRisksBySeverity(projectId, severity)`
- `getRisksByCategory(projectId, category)`
- `getRisksRequiringReview(projectId)`
- `getCriticalRisks(projectId)`

---

## Services

### Step 13-14: Create Services

**RiskService key methods:**

```php
public function createRisk(CreateRiskData $data): Risk
{
    $risk = $this->repository->create($data);

    // Auto-calculate severity is handled by observer

    if ($risk->severity === RiskSeverity::CRITICAL) {
        event(new CriticalRiskIdentified($risk));
    }

    event(new RiskCreated($risk));
    return $risk;
}

public function updateRiskAssessment(
    int $id,
    RiskProbability $probability,
    RiskImpact $impact
): Risk {
    $risk = $this->repository->findById($id);

    $oldSeverity = $risk->severity;

    $risk->update([
        'probability' => $probability,
        'impact' => $impact,
        // Severity auto-calculated by observer
        'updated_by' => auth()->id(),
    ]);

    $risk->refresh();

    if ($risk->severity !== $oldSeverity) {
        event(new RiskSeverityChanged($risk, $oldSeverity));
    }

    return $risk;
}

public function closeRisk(int $id, string $closureNotes): Risk
{
    $risk = $this->repository->findById($id);
    $risk->close($closureNotes);
    event(new RiskClosed($risk));
    return $risk;
}

public function getRiskRegister(int $projectId): Collection
{
    // Comprehensive risk register export
    return $this->repository->getActiveRisks($projectId)
        ->sortByDesc(fn($r) => $r->severity->value);
}
```

---

## Events

### Step 15-19: Create Events

- `RiskCreated`
- `CriticalRiskIdentified`
- `RiskSeverityChanged`
- `RiskClosed`
- `RiskReviewRequired` (dispatched via scheduled job)

---

## Policies

### Step 20-22: Create and Register Policies

Standard RBAC

---

## API Interface

### Step 23-24: Create Resources

Include severity calculation, requires_review flag, closure details

### Step 25-26: Create API Controllers

**Additional Endpoints:**

```php
public function updateAssessment(Risk $risk, UpdateRiskAssessmentRequest $request)
{
    $this->authorize('update', $risk);

    $risk = $this->service->updateRiskAssessment(
        $risk->id,
        RiskProbability::from($request->input('probability')),
        RiskImpact::from($request->input('impact'))
    );

    return new RiskResource($risk);
}

public function close(Risk $risk, CloseRiskRequest $request)
{
    $this->authorize('update', $risk);
    $risk = $this->service->closeRisk($risk->id, $request->input('closure_notes'));
    return new RiskResource($risk);
}

public function riskRegister(Project $project)
{
    $this->authorize('view', $project);
    $risks = $this->service->getRiskRegister($project->id);
    return RiskResource::collection($risks);
}
```

### Step 27-32: Create FormRequests (API)

Include `UpdateRiskAssessmentRequest`, `CloseRiskRequest`

### Step 33: Register API Routes

```php
Route::get('projects/{project}/risks/register', [RiskController::class, 'riskRegister']);
Route::put('risks/{risk}/assessment', [RiskController::class, 'updateAssessment']);
Route::post('risks/{risk}/close', [RiskController::class, 'close']);
```

---

## Web Interface

### Step 34-35: Create Web Controllers

**Additional Methods:**

```php
public function heatmap(Project $project)
{
    $viewModel = new RiskHeatmapViewModel($project, $this->service);
    return view('risks::heatmap', $viewModel->toArray());
}

public function register(Project $project)
{
    $viewModel = new RiskRegisterViewModel($project, $this->service);
    return view('risks::register', $viewModel->toArray());
}
```

### Step 36-42: Create Views

- `index.blade.php`
- `heatmap.blade.php` (probability × impact matrix visualization)
- `register.blade.php` (comprehensive risk register table)
- `create.blade.php`
- `edit.blade.php`
- `show.blade.php` (with mitigation tracking)
- `groups/manage.blade.php`

### Step 43-48: Create ViewModels

- `RiskIndexViewModel`
- `RiskHeatmapViewModel` - Generate 4x4 matrix data
- `RiskRegisterViewModel`
- `RiskFormViewModel`
- `RiskDetailViewModel`
- `RiskAssessmentViewModel`

### Step 49-54: Create FormRequests (Web)

### Step 55: Register Web Routes

```php
Route::get('projects/{project}/risks/heatmap', [RiskController::class, 'heatmap'])
    ->name('projects.risks.heatmap');
Route::get('projects/{project}/risks/register', [RiskController::class, 'register'])
    ->name('projects.risks.register');
Route::put('risks/{risk}/assessment', [RiskController::class, 'updateAssessment'])
    ->name('risks.update-assessment');
Route::post('risks/{risk}/close', [RiskController::class, 'close'])
    ->name('risks.close');
```

---

## Livewire Components

### Step 56-58: Create Components

- `RiskForm` - Probability/Impact dropdowns with live severity calculation
- `RiskHeatmapInteractive` - Clickable heatmap matrix
- `RiskCard` - Dashboard card with severity color coding

### Step 59-61: Create Livewire Views

---

## Scheduled Jobs

### Step 62: RiskReviewNotification Job

**Purpose:** Daily job to identify risks requiring review

```php
$risksForReview = Risk::where('review_date', '<=', now())
    ->where('status', '!=', RiskStatus::CLOSED)
    ->get();

foreach ($risksForReview as $risk) {
    event(new RiskReviewRequired($risk));
}
```

---

## Testing

- Severity calculation matrix
- Update assessment workflow
- Close risk workflow
- Risk register generation
- Policy tests

---

**Implementation Ready:** âœ…
**Next Module:** Issues
