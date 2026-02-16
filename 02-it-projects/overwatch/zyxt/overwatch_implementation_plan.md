# Overwatch Project Management System

## Complete Implementation Blueprint - 117 Atomic Steps

**Version:** 1.1 (Corrected)
**Date:** January 28, 2026
**Architecture:** Laravel 11 + Livewire 3 (TALL Stack) Modular Monolith

------

## Table of Contents

1. [Architectural Summary](https://claude.ai/chat/91c45866-17fc-494a-9eed-8cad63d9efee#architectural-summary)
2. [Phase 1: Foundation & Shared Systems (Steps 1-50c)](https://claude.ai/chat/91c45866-17fc-494a-9eed-8cad63d9efee#phase-1-foundation--shared-systems)
3. [Phase 2: StatusReports Reference Module (Steps 51-110)](https://claude.ai/chat/91c45866-17fc-494a-9eed-8cad63d9efee#phase-2-statusreports-reference-module)
4. [Questions & Clarifications](https://claude.ai/chat/91c45866-17fc-494a-9eed-8cad63d9efee#questions--clarifications)
5. [Execution Guidelines](https://claude.ai/chat/91c45866-17fc-494a-9eed-8cad63d9efee#execution-guidelines)
6. [Corrections Log](https://claude.ai/chat/91c45866-17fc-494a-9eed-8cad63d9efee#corrections-log)

------

## Architectural Summary

This is a **Laravel 11 + Livewire 3 (TALL Stack) modular monolith** implementing a comprehensive project management system with triple interface support (API/Web/Livewire). The architecture follows a flat module structure with shared systems for Notes, Audit Trails, and Entity Linking, emphasizing RBAC, soft deletes, immutability constraints, and polymorphic relationships across 16+ business modules organized into four conceptual domains (Governance, Execution, Control, Knowledge).

**Key Architectural Principles:**

- **Modular Monolith:** Each feature is self-contained within its module
- **Triple Interface Support:** Every module exposes API, Web, and Livewire interfaces
- **Shared Systems:** Notes, Audit Trails, and Entity Links are globally reusable
- **RBAC Enforcement:** Admin, Editor, Viewer roles with granular permissions
- **Immutability Patterns:** Status Reports lock on publish, Decisions lock when finalized
- **Soft Deletes Only:** No hard deletes anywhere in the system
- **Event-Driven:** All state changes fire events for audit logging

------

## Phase 1: Foundation & Shared Systems

### Steps 1-50c: Core Infrastructure Setup (53 steps total)

------

### Step 1: Initialize Fresh Laravel 11 Application

- **Goal:** Create a new Laravel 11 project with default structure.
- **Files Involved:** All Laravel core files
- **Dependencies:** None
- **Command:** `composer create-project laravel/laravel overwatch-pm`
- **Validation:** `php artisan --version` returns Laravel 11.x, `php artisan about` runs successfully.

------

### Step 2: Configure Environment File for Local Development

- **Goal:** Set up database connection and basic app configuration.
- **Files Involved:** `.env`
- **Dependencies:** Step 1
- **Changes:**
  - Set `APP_NAME=Overwatch`
  - Set `DB_CONNECTION=sqlite`
  - Remove MySQL credentials
  - Set `DB_DATABASE=/absolute/path/to/database.sqlite`
- **Validation:** `.env` file contains correct SQLite configuration.

------

### Step 3: Create SQLite Database File

- **Goal:** Initialize empty SQLite database file.
- **Files Involved:** `database/database.sqlite`
- **Dependencies:** Step 2
- **Command:** `touch database/database.sqlite`
- **Validation:** File exists and `php artisan migrate:status` connects successfully (empty migrations list).

------

### Step 4: Install Livewire 3

- **Goal:** Add Livewire package to project.
- **Files Involved:** `composer.json`
- **Dependencies:** Step 1
- **Command:** `composer require livewire/livewire:^3.0`
- **Validation:** `composer show livewire/livewire` shows version 3.x installed.

------

### Step 5: Publish Livewire Configuration

- **Goal:** Create Livewire config file for customization.
- **Files Involved:** `config/livewire.php`
- **Dependencies:** Step 4
- **Command:** `php artisan livewire:publish --config`
- **Validation:** `config/livewire.php` exists.

------

### Step 6: Install Node Dependencies for TALL Stack

- **Goal:** Add Alpine.js and Tailwind CSS to package.json.
- **Files Involved:** `package.json`
- **Dependencies:** Step 1
- **Command:** `npm install -D tailwindcss postcss autoprefixer alpinejs`
- **Validation:** `package.json` contains tailwindcss, alpinejs dependencies.

------

### Step 7: Initialize Tailwind CSS Configuration

- **Goal:** Generate Tailwind config file.
- **Files Involved:** `tailwind.config.js`, `postcss.config.js`
- **Dependencies:** Step 6
- **Command:** `npx tailwindcss init -p`
- **Validation:** Both config files exist.

------

### Step 8: Configure Tailwind Content Paths

- **Goal:** Set up Tailwind to scan Blade and Livewire files.
- **Files Involved:** `tailwind.config.js`
- **Dependencies:** Step 7
- **Changes:**

```javascript
content: [
    "./resources/**/*.blade.php",
    "./resources/**/*.js",
    "./app/Livewire/**/*.php",
    "./app/Modules/**/Views/**/*.blade.php",
    "./app/Modules/**/Livewire/**/*.php",
]
```

- **Validation:** Config file contains correct content paths.

------

### Step 9: Set Up Tailwind Directives in CSS

- **Goal:** Add Tailwind directives to application CSS.
- **Files Involved:** `resources/css/app.css`
- **Dependencies:** Step 7
- **Changes:** Replace contents with:

```css
@tailwind base;
@tailwind components;
@tailwind utilities;
```

- **Validation:** File contains only three Tailwind directives.

------

### Step 10: Configure Alpine.js in app.js

- **Goal:** Import and initialize Alpine.js.
- **Files Involved:** `resources/js/app.js`
- **Dependencies:** Step 6
- **Changes:**

```javascript
import Alpine from 'alpinejs';
window.Alpine = Alpine;
Alpine.start();
```

- **Validation:** File imports Alpine correctly.

------

### Step 11: Update Vite Configuration for Module Structure

- **Goal:** Configure Vite to handle modular Blade files.
- **Files Involved:** `vite.config.js`
- **Dependencies:** Step 1
- **Changes:**

```javascript
export default defineConfig({
    plugins: [
        laravel({
            input: ['resources/css/app.css', 'resources/js/app.js'],
            refresh: [
                'resources/**',
                'app/Livewire/**',
                'app/Modules/**/Views/**',
                'app/Modules/**/Livewire/**',
            ],
        }),
    ],
});
```

- **Validation:** Config includes module paths in refresh array.

------

### Step 12: Build Frontend Assets

- **Goal:** Compile CSS and JS for first time.
- **Files Involved:** `public/build/`
- **Dependencies:** Steps 9, 10, 11
- **Command:** `npm run build`
- **Validation:** `public/build/manifest.json` exists, assets compiled without errors.

------

### Step 13: Create Base Layout Blade Template

- **Goal:** Build master layout with Livewire and Vite directives.
- **Files Involved:** `resources/views/layouts/app.blade.php`
- **Dependencies:** Step 12
- **Content:**

```blade
<!DOCTYPE html>
<html lang="{{ str_replace('_', '-', app()->getLocale()) }}">
<head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <title>{{ config('app.name') }}</title>
    @vite(['resources/css/app.css', 'resources/js/app.js'])
    @livewireStyles
</head>
<body class="antialiased">
    {{ $slot }}
    @livewireScripts
</body>
</html>
```

- **Validation:** File exists and compiles without errors.

------

### Step 14: Create Test Livewire Component

- **Goal:** Verify Livewire installation with simple component.
- **Files Involved:**
  - `app/Livewire/TestComponent.php`
  - `resources/views/livewire/test-component.blade.php`
- **Dependencies:** Steps 4, 13
- **Command:** `php artisan make:livewire TestComponent`
- **Validation:** Both files created, component renders "Test Component" text.

------

### Step 15: Create Test Route for Livewire Component

- **Goal:** Add route to verify TALL stack working end-to-end.
- **Files Involved:** `routes/web.php`
- **Dependencies:** Step 14
- **Changes:**

```php
Route::get('/test', function () {
    return view('layouts.app')->with('slot', '<livewire:test-component />');
});
```

- **Validation:** Visit `/test`, see styled component with Alpine.js working.

------

### Step 16: Run Dev Server Smoke Test

- **Goal:** Confirm application runs without errors.
- **Files Involved:** N/A
- **Dependencies:** Step 15
- **Command:** `php artisan serve` → visit `http://localhost:8000/test`
- **Validation:** Page loads, Tailwind styles apply, no console errors.

------

### Step 17: Create Users Table Migration

- **Goal:** Define users table with role field.
- **Files Involved:** `database/migrations/2026_01_27_000001_create_users_table.php`
- **Dependencies:** Step 3
- **Schema:**

```php
$table->id();
$table->string('name');
$table->string('email')->unique();
$table->timestamp('email_verified_at')->nullable();
$table->string('password');
$table->enum('role', ['admin', 'editor', 'viewer'])->default('viewer');
$table->rememberToken();
$table->timestamps();
$table->softDeletes();

$table->index('email');
$table->index('role');
```

- **Validation:** Migration file exists with correct schema including role enum.

------

### Step 18: Create Projects Table Migration

- **Goal:** Define projects table with core fields.
- **Files Involved:** `database/migrations/2026_01_27_000002_create_projects_table.php`
- **Dependencies:** Step 17
- **Schema:**

```php
$table->id();
$table->string('name');
$table->text('description')->nullable();
$table->enum('status', ['draft', 'active', 'on_hold', 'completed', 'cancelled'])->default('draft');
$table->date('start_date')->nullable();
$table->date('end_date')->nullable();
$table->timestamps();
$table->softDeletes();
```

- **Validation:** Migration file exists with correct columns and indexes.

------

### Step 19: Create Project-User Pivot Table Migration

- **Goal:** Enable many-to-many relationship between users and projects.
- **Files Involved:** `database/migrations/2026_01_27_000003_create_project_user_table.php`
- **Dependencies:** Steps 17, 18
- **Schema:**

```php
$table->foreignId('project_id')->constrained()->cascadeOnDelete();
$table->foreignId('user_id')->constrained()->cascadeOnDelete();
$table->timestamps();
$table->primary(['project_id', 'user_id']);
```

- **Validation:** Migration file exists with composite primary key and foreign keys.

------

### Step 20: Run Migrations

- **Goal:** Execute all migrations to create database structure.
- **Files Involved:** `database/database.sqlite`
- **Dependencies:** Steps 17, 18, 19
- **Command:** `php artisan migrate`
- **Validation:** All migrations run successfully, tables visible in database.

------

### Step 20a: Install Laravel Sanctum

- **Goal:** Add API authentication package for token-based auth.
- **Files Involved:** `composer.json`
- **Dependencies:** Step 1
- **Command:** `composer require laravel/sanctum`
- **Validation:** `composer show laravel/sanctum` shows package installed.

------

### Step 20b: Publish Sanctum Configuration

- **Goal:** Create Sanctum config and migration files.
- **Files Involved:**
  - `config/sanctum.php`
  - `database/migrations/*_create_personal_access_tokens_table.php`
- **Dependencies:** Step 20a
- **Command:** `php artisan vendor:publish --provider="Laravel\Sanctum\SanctumServiceProvider"`
- **Validation:** Config file and migration exist.

------

### Step 20c: Run Sanctum Migration

- **Goal:** Create personal_access_tokens table for API tokens.
- **Files Involved:** `database/database.sqlite`
- **Dependencies:** Step 20b
- **Command:** `php artisan migrate`
- **Validation:** `personal_access_tokens` table exists in database.

------

### Step 20d: Configure Sanctum Middleware

- **Goal:** Add Sanctum middleware to API routes.
- **Files Involved:** `bootstrap/app.php` (Laravel 11)
- **Dependencies:** Step 20c
- **Changes:**

```php
// In bootstrap/app.php
->withMiddleware(function (Middleware $middleware) {
    $middleware->api(prepend: [
        \Laravel\Sanctum\Http\Middleware\EnsureFrontendRequestsAreStateful::class,
    ]);
})
```

- **Validation:** Sanctum middleware configured for API protection.

------

### Step 21: Create User Model

- **Goal:** Define User model with relationships and role constants.
- **Files Involved:** `app/Models/User.php`
- **Dependencies:** Step 20
- **Content:**

```php
class User extends Authenticatable
{
    use HasFactory, Notifiable;

    const ROLE_ADMIN = 'admin';
    const ROLE_EDITOR = 'editor';
    const ROLE_VIEWER = 'viewer';

    protected $fillable = ['name', 'email', 'password', 'role'];

    public function projects()
    {
        return $this->belongsToMany(Project::class)->withTimestamps();
    }

    public function isAdmin(): bool
    {
        return $this->role === self::ROLE_ADMIN;
    }

    public function isEditor(): bool
    {
        return $this->role === self::ROLE_EDITOR;
    }

    public function isViewer(): bool
    {
        return $this->role === self::ROLE_VIEWER;
    }
}
```

- **Validation:** Model exists, compiles without errors.

------

### Step 22: Create Project Model

- **Goal:** Define Project model with relationships and soft deletes.
- **Files Involved:** `app/Models/Project.php`
- **Dependencies:** Step 20
- **Content:**

```php
class Project extends Model
{
    use HasFactory, SoftDeletes;

    protected $fillable = [
        'name',
        'description',
        'status',
        'start_date',
        'end_date'
    ];

    protected $casts = [
        'start_date' => 'date',
        'end_date' => 'date',
    ];

    public function users()
    {
        return $this->belongsToMany(User::class)->withTimestamps();
    }
}
```

- **Validation:** Model exists, relationships defined correctly.

------

### Step 23: Test Models in Tinker

- **Goal:** Verify models and relationships work correctly.
- **Files Involved:** N/A
- **Dependencies:** Steps 21, 22
- **Commands:**

```php
$user = User::create(['name' => 'Test', 'email' => 'test@test.com', 'password' => bcrypt('password'), 'role' => 'admin']);
$project = Project::create(['name' => 'Test Project']);
$project->users()->attach($user);
$user->projects; // Should return collection with test project
```

- **Validation:** All Tinker commands execute successfully, relationships work.

------

### Step 24: Create Shared Modules Directory Structure

- **Goal:** Set up base folder structure for shared systems.
- **Files Involved:** Directory structure only
- **Dependencies:** Step 1
- **Commands:**

```bash
mkdir -p app/Modules/Shared/{Enums,Traits,Services}
mkdir -p app/Modules/Shared/{Notes,EntityLinks}/{Models,Services,Policies,DTOs,Resources}
```

- **Validation:** All directories exist.

------

### Step 25: Create RagStatus Enum

- **Goal:** Define RAG status enum (Red, Amber, Green).
- **Files Involved:** `app/Modules/Shared/Enums/RagStatus.php`
- **Dependencies:** Step 24
- **Content:**

```php
namespace App\Modules\Shared\Enums;

enum RagStatus: string
{
    case RED = 'red';
    case AMBER = 'amber';
    case GREEN = 'green';

    public function label(): string
    {
        return match($this) {
            self::RED => 'Red',
            self::AMBER => 'Amber',
            self::GREEN => 'Green',
        };
    }

    public function color(): string
    {
        return match($this) {
            self::RED => 'bg-red-500',
            self::AMBER => 'bg-amber-500',
            self::GREEN => 'bg-green-500',
        };
    }
}
```

- **Validation:** Enum exists, can be imported and used.

------

### Step 26: Create ProjectStatus Enum

- **Goal:** Define project status enum.
- **Files Involved:** `app/Modules/Shared/Enums/ProjectStatus.php`
- **Dependencies:** Step 24
- **Content:**

```php
namespace App\Modules\Shared\Enums;

enum ProjectStatus: string
{
    case DRAFT = 'draft';
    case ACTIVE = 'active';
    case ON_HOLD = 'on_hold';
    case COMPLETED = 'completed';
    case CANCELLED = 'cancelled';

    public function label(): string
    {
        return match($this) {
            self::DRAFT => 'Draft',
            self::ACTIVE => 'Active',
            self::ON_HOLD => 'On Hold',
            self::COMPLETED => 'Completed',
            self::CANCELLED => 'Cancelled',
        };
    }
}
```

- **Validation:** Enum exists, compiles correctly.

------

### Step 27: Create UserRole Enum

- **Goal:** Define user role enum for RBAC.
- **Files Involved:** `app/Modules/Shared/Enums/UserRole.php`
- **Dependencies:** Step 24
- **Content:**

```php
namespace App\Modules\Shared\Enums;

enum UserRole: string
{
    case ADMIN = 'admin';
    case EDITOR = 'editor';
    case VIEWER = 'viewer';

    public function label(): string
    {
        return match($this) {
            self::ADMIN => 'Administrator',
            self::EDITOR => 'Editor',
            self::VIEWER => 'Viewer',
        };
    }
}
```

- **Validation:** Enum exists and can be used in policies.

------

### Step 28: Create HasGroups Trait

- **Goal:** Implement trait for 2-level grouping functionality.
- **Files Involved:** `app/Modules/Shared/Traits/HasGroups.php`
- **Dependencies:** Step 24
- **Content:**

```php
namespace App\Modules\Shared\Traits;

trait HasGroups
{
    public function group()
    {
        return $this->belongsTo(static::getGroupClass(), 'group_id');
    }

    public function scopeWithoutGroup($query)
    {
        return $query->whereNull('group_id');
    }

    public function scopeInGroup($query, $groupId)
    {
        return $query->where('group_id', $groupId);
    }

    protected static function getGroupClass(): string
    {
        // Override in implementing class
        // e.g., return MilestoneGroup::class;
        throw new \Exception('Must define getGroupClass() method');
    }
}
```

- **Validation:** Trait file exists, compiles without errors.

------

### Step 29: Create HasInternalVisibility Trait

- **Goal:** Implement trait for is_internal_only filtering.
- **Files Involved:** `app/Modules/Shared/Traits/HasInternalVisibility.php`
- **Dependencies:** Step 24
- **Content:**

```php
namespace App\Modules\Shared\Traits;

trait HasInternalVisibility
{
    public function scopeVisibleToUser($query, $user)
    {
        if ($user->isAdmin() || $user->isEditor()) {
            return $query;
        }

        return $query->where('is_internal_only', false);
    }

    public function scopeInternalOnly($query)
    {
        return $query->where('is_internal_only', true);
    }

    public function scopePublic($query)
    {
        return $query->where('is_internal_only', false);
    }
}
```

- **Validation:** Trait file exists and can be applied to models.

------

### Step 30: Create WeekCalculatorService

- **Goal:** Implement ISO 8601 week calculation utility.
- **Files Involved:** `app/Modules/Shared/Services/WeekCalculatorService.php`
- **Dependencies:** Step 24
- **Content:**

```php
namespace App\Modules\Shared\Services;

use Carbon\Carbon;

class WeekCalculatorService
{
    public function dateToWeekString(Carbon $date): string
    {
        return $date->format('o-\WW'); // e.g., "2026-W04"
    }

    public function weekStringToDisplay(string $weekString): string
    {
        [$year, $week] = explode('-W', $weekString);
        $date = Carbon::now()->setISODate((int)$year, (int)$week);

        return "Week {$date->weekOfMonth} of {$date->format('F')}";
    }

    public function calculateWeekFromDate(?string $date): ?string
    {
        if (!$date) {
            return null;
        }

        return $this->dateToWeekString(Carbon::parse($date));
    }
}
```

- **Validation:** Service exists, methods return correct ISO 8601 format.

------

### Step 31: Test WeekCalculatorService

- **Goal:** Verify week calculation logic in Tinker.
- **Files Involved:** N/A
- **Dependencies:** Step 30
- **Commands:**

```php
$service = new App\Modules\Shared\Services\WeekCalculatorService();
$service->dateToWeekString(now()); // Should return "2026-W05" or similar
$service->weekStringToDisplay('2026-W04'); // Should return "Week 4 of January"
```

- **Validation:** Methods return expected formats.

------

### Step 32: Create Notes Table Migration

- **Goal:** Define polymorphic notes table.
- **Files Involved:** `database/migrations/2026_01_27_000010_create_notes_table.php`
- **Dependencies:** Step 20
- **Schema:**

```php
$table->id();
$table->morphs('notable'); // notable_type, notable_id
$table->foreignId('user_id')->constrained()->cascadeOnDelete();
$table->text('content');
$table->timestamps();

$table->index(['notable_type', 'notable_id', 'created_at']);
```

- **Validation:** Migration file exists with polymorphic indexes.

------

### Step 33: Run Notes Migration

- **Goal:** Create notes table in database.
- **Files Involved:** `database/database.sqlite`
- **Dependencies:** Step 32
- **Command:** `php artisan migrate`
- **Validation:** `notes` table exists with correct columns.

------

### Step 34: Create Note Model

- **Goal:** Define Note model with polymorphic relationship.
- **Files Involved:** `app/Modules/Shared/Notes/Models/Note.php`
- **Dependencies:** Step 33
- **Content:**

```php
namespace App\Modules\Shared\Notes\Models;

use Illuminate\Database\Eloquent\Model;
use App\Models\User;

class Note extends Model
{
    protected $fillable = ['notable_type', 'notable_id', 'user_id', 'content'];

    public function notable()
    {
        return $this->morphTo();
    }

    public function user()
    {
        return $this->belongsTo(User::class);
    }

    // Immutability: Prevent updates and deletes
    protected static function booted()
    {
        static::updating(function () {
            throw new \Exception('Notes cannot be updated once created.');
        });

        static::deleting(function () {
            throw new \Exception('Notes cannot be deleted.');
        });
    }
}
```

- **Validation:** Model exists, immutability constraints in place.

------

### Step 35: Create CreateNoteData DTO

- **Goal:** Define DTO for note creation.
- **Files Involved:** `app/Modules/Shared/Notes/DTOs/CreateNoteData.php`
- **Dependencies:** Step 24
- **Content:**

```php
namespace App\Modules\Shared\Notes\DTOs;

class CreateNoteData
{
    public function __construct(
        public readonly string $notable_type,
        public readonly int $notable_id,
        public readonly int $user_id,
        public readonly string $content
    ) {}

    public static function fromArray(array $data): self
    {
        return new self(
            notable_type: $data['notable_type'],
            notable_id: $data['notable_id'],
            user_id: $data['user_id'],
            content: $data['content']
        );
    }

    public function toArray(): array
    {
        return [
            'notable_type' => $this->notable_type,
            'notable_id' => $this->notable_id,
            'user_id' => $this->user_id,
            'content' => $this->content,
        ];
    }
}
```

- **Validation:** DTO instantiates correctly with type hints.

------

### Step 36: Create NoteService

- **Goal:** Implement service layer for note creation.
- **Files Involved:** `app/Modules/Shared/Notes/Services/NoteService.php`
- **Dependencies:** Steps 34, 35
- **Content:**

```php
namespace App\Modules\Shared\Notes\Services;

use App\Modules\Shared\Notes\Models\Note;
use App\Modules\Shared\Notes\DTOs\CreateNoteData;

class NoteService
{
    public function create(CreateNoteData $data): Note
    {
        return Note::create($data->toArray());
    }

    public function getNotesFor(string $notableType, int $notableId)
    {
        return Note::where('notable_type', $notableType)
            ->where('notable_id', $notableId)
            ->with('user')
            ->orderBy('created_at', 'desc')
            ->get();
    }
}
```

- **Validation:** Service methods work, notes can be created and retrieved.

------

### Step 37: Create NotePolicy

- **Goal:** Implement authorization rules for notes.
- **Files Involved:** `app/Modules/Shared/Notes/Policies/NotePolicy.php`
- **Dependencies:** Step 34
- **Content:**

```php
namespace App\Modules\Shared\Notes\Policies;

use App\Models\User;
use App\Modules\Shared\Notes\Models\Note;

class NotePolicy
{
    public function viewAny(User $user): bool
    {
        return true; // All authenticated users can view notes
    }

    public function view(User $user, Note $note): bool
    {
        return true; // All authenticated users can view individual notes
    }

    public function create(User $user): bool
    {
        return $user->isAdmin() || $user->isEditor();
    }

    public function update(User $user, Note $note): bool
    {
        return false; // Notes are immutable
    }

    public function delete(User $user, Note $note): bool
    {
        return false; // Notes cannot be deleted
    }
}
```

- **Validation:** Policy methods return correct boolean values.

------

### Step 38: Create NoteResource for API

- **Goal:** Build JSON transformer for notes.
- **Files Involved:** `app/Modules/Shared/Notes/Resources/NoteResource.php`
- **Dependencies:** Step 34
- **Content:**

```php
namespace App\Modules\Shared\Notes\Resources;

use Illuminate\Http\Resources\Json\JsonResource;

class NoteResource extends JsonResource
{
    public function toArray($request): array
    {
        return [
            'id' => $this->id,
            'content' => $this->content,
            'user' => [
                'id' => $this->user->id,
                'name' => $this->user->name,
            ],
            'created_at' => $this->created_at->toISOString(),
        ];
    }
}
```

- **Validation:** Resource formats data correctly.

------

### Step 39: Register NotePolicy

- **Goal:** Map Note model to NotePolicy in service provider.
- **Files Involved:** `app/Providers/AuthServiceProvider.php`
- **Dependencies:** Step 37
- **Changes:**

```php
use App\Modules\Shared\Notes\Models\Note;
use App\Modules\Shared\Notes\Policies\NotePolicy;

protected $policies = [
    Note::class => NotePolicy::class,
];
```

- **Validation:** Policy registered, authorization checks work.

------

### Step 40: Test Notes System in Tinker

- **Goal:** Verify complete notes functionality.
- **Files Involved:** N/A
- **Dependencies:** Steps 34-39
- **Commands:**

```php
$service = new App\Modules\Shared\Notes\Services\NoteService();
$dto = new App\Modules\Shared\Notes\DTOs\CreateNoteData(
    notable_type: 'App\Models\Project',
    notable_id: 1,
    user_id: 1,
    content: 'Test note'
);
$note = $service->create($dto);
$note->update(['content' => 'Try to update']); // Should throw exception
```

- **Validation:** Note created successfully, update throws immutability exception.

------

### Step 41: Install Spatie Activity Log

- **Goal:** Add audit trail package to project.
- **Files Involved:** `composer.json`
- **Dependencies:** Step 1
- **Command:** `composer require spatie/laravel-activitylog`
- **Validation:** Package installed, shows in `composer show`.

------

### Step 42: Publish Activity Log Configuration

- **Goal:** Create config file for customization.
- **Files Involved:** `config/activitylog.php`
- **Dependencies:** Step 41
- **Command:** `php artisan vendor:publish --provider="Spatie\Activitylog\ActivitylogServiceProvider" --tag="activitylog-config"`
- **Validation:** Config file exists.

------

### Step 43: Run Activity Log Migration

- **Goal:** Create activity_log table.
- **Files Involved:** `database/database.sqlite`
- **Dependencies:** Step 42
- **Command:** `php artisan vendor:publish --provider="Spatie\Activitylog\ActivitylogServiceProvider" --tag="activitylog-migrations"` then `php artisan migrate`
- **Validation:** `activity_log` table exists in database.

------

### Step 44: Configure Project Model for Activity Logging

- **Goal:** Enable automatic audit trail on Project model.
- **Files Involved:** `app/Models/Project.php`
- **Dependencies:** Step 43
- **Changes:** Add to Project model:

```php
use Spatie\Activitylog\Traits\LogsActivity;
use Spatie\Activitylog\LogOptions;

class Project extends Model
{
    use HasFactory, SoftDeletes, LogsActivity;

    // ... existing code ...

    public function getActivitylogOptions(): LogOptions
    {
        return LogOptions::defaults()->logAll();
    }
}
```

- **Validation:** Model changes logged automatically to activity_log table.

------

### Step 45: Test Activity Log in Tinker

- **Goal:** Verify audit trail captures changes.
- **Files Involved:** N/A
- **Dependencies:** Step 44
- **Commands:**

```php
$project = Project::first();
$project->update(['name' => 'Updated Name']);
activity()->forSubject($project)->get(); // Should show update event
```

- **Validation:** Activity log entry created with old/new values.

------

### Step 46: Create EntityLinks Table Migration

- **Goal:** Define polymorphic entity links table.
- **Files Involved:** `database/migrations/2026_01_27_000020_create_entity_links_table.php`
- **Dependencies:** Step 20
- **Schema:**

```php
$table->id();
$table->string('source_type'); // Must be JournalEntry
$table->unsignedBigInteger('source_id');
$table->string('target_type');
$table->unsignedBigInteger('target_id');
$table->timestamps();

$table->index(['source_type', 'source_id']);
$table->index(['target_type', 'target_id']);
$table->unique(['source_type', 'source_id', 'target_type', 'target_id'], 'entity_links_unique');
```

- **Validation:** Migration file exists with composite unique constraint.

------

### Step 47: Run EntityLinks Migration

- **Goal:** Create entity_links table in database.
- **Files Involved:** `database/database.sqlite`
- **Dependencies:** Step 46
- **Command:** `php artisan migrate`
- **Validation:** `entity_links` table exists with indexes.

------

### Step 48: Create EntityLink Model

- **Goal:** Define EntityLink model with polymorphic relationships.
- **Files Involved:** `app/Modules/Shared/EntityLinks/Models/EntityLink.php`
- **Dependencies:** Step 47
- **Content:**

```php
namespace App\Modules\Shared\EntityLinks\Models;

use Illuminate\Database\Eloquent\Model;

class EntityLink extends Model
{
    protected $fillable = ['source_type', 'source_id', 'target_type', 'target_id'];

    public function source()
    {
        return $this->morphTo();
    }

    public function target()
    {
        return $this->morphTo();
    }

    // Validate source is always JournalEntry
    protected static function booted()
    {
        static::creating(function ($link) {
            if (!str_contains($link->source_type, 'JournalEntry')) {
                throw new \Exception('Only JournalEntry can be source of entity links.');
            }
        });
    }
}
```

- **Validation:** Model exists with validation logic.

------

### Step 49: Create CreateEntityLinkData DTO

- **Goal:** Define DTO for entity link creation.
- **Files Involved:** `app/Modules/Shared/EntityLinks/DTOs/CreateEntityLinkData.php`
- **Dependencies:** Step 24
- **Content:**

```php
namespace App\Modules\Shared\EntityLinks\DTOs;

class CreateEntityLinkData
{
    public function __construct(
        public readonly string $source_type,
        public readonly int $source_id,
        public readonly string $target_type,
        public readonly int $target_id
    ) {}

    public static function fromArray(array $data): self
    {
        return new self(
            source_type: $data['source_type'],
            source_id: $data['source_id'],
            target_type: $data['target_type'],
            target_id: $data['target_id']
        );
    }

    public function toArray(): array
    {
        return [
            'source_type' => $this->source_type,
            'source_id' => $this->source_id,
            'target_type' => $this->target_type,
            'target_id' => $this->target_id,
        ];
    }
}
```

- **Validation:** DTO instantiates correctly.

------

### Step 50: Create EntityLinkService

- **Goal:** Implement service layer for entity linking.
- **Files Involved:** `app/Modules/Shared/EntityLinks/Services/EntityLinkService.php`
- **Dependencies:** Steps 48, 49
- **Content:**

```php
namespace App\Modules\Shared\EntityLinks\Services;

use App\Modules\Shared\EntityLinks\Models\EntityLink;
use App\Modules\Shared\EntityLinks\DTOs\CreateEntityLinkData;

class EntityLinkService
{
    public function create(CreateEntityLinkData $data): EntityLink
    {
        return EntityLink::create($data->toArray());
    }

    public function getLinksFrom(string $sourceType, int $sourceId)
    {
        return EntityLink::where('source_type', $sourceType)
            ->where('source_id', $sourceId)
            ->with('target')
            ->get();
    }

    public function getLinksTo(string $targetType, int $targetId)
    {
        return EntityLink::where('target_type', $targetType)
            ->where('target_id', $targetId)
            ->with('source')
            ->get();
    }

    public function delete(int $linkId): bool
    {
        return EntityLink::destroy($linkId) > 0;
    }
}
```

- **Validation:** Service methods work, links can be created and retrieved.

------

### Step 50a: Create Module View Service Provider

- **Goal:** Register module view paths so Laravel can find Blade templates.
- **Files Involved:** `app/Providers/ModuleViewServiceProvider.php`
- **Dependencies:** Step 1
- **Content:**

```php
namespace App\Providers;

use Illuminate\Support\ServiceProvider;
use Illuminate\Support\Facades\View;

class ModuleViewServiceProvider extends ServiceProvider
{
    public function boot(): void
    {
        // Register view namespaces for each module
        $modules = [
            'status-reports' => 'StatusReports',
            'milestones' => 'Milestones',
            'action-items' => 'ActionItems',
            'deliverables' => 'Deliverables',
            'dependencies' => 'Dependencies',
            'meeting-events' => 'MeetingEvents',
            'risks' => 'Risks',
            'issues' => 'Issues',
            'change-requests' => 'ChangeRequests',
            'decisions' => 'Decisions',
            'journal-entries' => 'JournalEntries',
            'meeting-minutes' => 'MeetingMinutes',
            'stakeholders' => 'Stakeholders',
            'links' => 'Links',
            'lists' => 'Lists',
            'lessons-learned' => 'LessonsLearned',
        ];

        foreach ($modules as $namespace => $moduleName) {
            View::addNamespace(
                $namespace,
                app_path("Modules/{$moduleName}/Views")
            );
        }
    }
}
```

- **Validation:** Service provider file created.

------

### Step 50b: Register Module View Service Provider

- **Goal:** Add ModuleViewServiceProvider to application bootstrap.
- **Files Involved:** `bootstrap/providers.php` (Laravel 11)
- **Dependencies:** Step 50a
- **Changes:**

```php
return [
    App\Providers\AppServiceProvider::class,
    App\Providers\ModuleViewServiceProvider::class,
];
```

- **Validation:** Provider registered, views can be accessed via namespace.

------

### Step 50c: Test View Registration

- **Goal:** Verify module views are accessible.
- **Files Involved:** N/A
- **Dependencies:** Step 50b
- **Commands:**

```php
// In tinker or temporary route
view()->exists('status-reports::index'); // Should return true once StatusReports module created
```

- **Validation:** View namespace resolution works correctly.

------

## Phase 2: StatusReports Reference Module

### Steps 51-110: Complete StatusReports Implementation

------

### Step 51: Create StatusReports Module Directory Structure

- **Goal:** Set up complete folder hierarchy for StatusReports module.
- **Files Involved:** Directory structure only
- **Dependencies:** Step 1
- **Commands:**

```bash
mkdir -p app/Modules/StatusReports/{Controllers/{Api,Web},DTOs,Events,FormRequests/{Api,Web},Models,Policies,Repositories,Resources,Services,ViewModels,Views,Livewire}
mkdir -p resources/views/livewire/status-reports
```

- **Validation:** All directories exist with correct nesting.

------

### Step 52: Create status_reports Table Migration

- **Goal:** Define status reports table with core fields.
- **Files Involved:** `database/migrations/2026_01_27_100001_create_status_reports_table.php`
- **Dependencies:** Step 18 (projects table)
- **Schema:**

```php
$table->id();
$table->foreignId('project_id')->constrained()->cascadeOnDelete();
$table->string('title');
$table->date('reporting_period_start');
$table->date('reporting_period_end');
$table->enum('overall_status', ['red', 'amber', 'green'])->default('green');
$table->text('executive_summary')->nullable();
$table->text('accomplishments')->nullable();
$table->text('upcoming_activities')->nullable();
$table->text('risks_issues_summary')->nullable();
$table->timestamp('published_at')->nullable(); // Immutability trigger
$table->timestamps();
$table->softDeletes();

$table->index(['project_id', 'published_at']);
$table->index('reporting_period_start');
```

- **Validation:** Migration file exists with indexes and immutability field.

------

### Step 53: Create status_report_snapshots Table Migration

- **Goal:** Define snapshots table for point-in-time captures.
- **Files Involved:** `database/migrations/2026_01_27_100002_create_status_report_snapshots_table.php`
- **Dependencies:** Step 52
- **Schema:**

```php
$table->id();
$table->foreignId('status_report_id')->constrained()->cascadeOnDelete();
$table->json('snapshot_data'); // Full report state as JSON
$table->timestamp('captured_at');
$table->timestamps();

$table->index(['status_report_id', 'captured_at']);
```

- **Validation:** Migration file exists with foreign key to status_reports.

------

### Step 54: Run StatusReports Migrations

- **Goal:** Create both tables in database.
- **Files Involved:** `database/database.sqlite`
- **Dependencies:** Steps 52, 53
- **Command:** `php artisan migrate`
- **Validation:** Both tables exist with correct schema.

------

### Step 55: Create StatusReport Model

- **Goal:** Define StatusReport model with relationships and immutability logic.
- **Files Involved:** `app/Modules/StatusReports/Models/StatusReport.php`
- **Dependencies:** Step 54
- **Content:**

```php
namespace App\Modules\StatusReports\Models;

use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\SoftDeletes;
use Spatie\Activitylog\Traits\LogsActivity;
use Spatie\Activitylog\LogOptions;
use App\Models\Project;
use App\Modules\Shared\Enums\RagStatus;

class StatusReport extends Model
{
    use SoftDeletes, LogsActivity;

    protected $fillable = [
        'project_id',
        'title',
        'reporting_period_start',
        'reporting_period_end',
        'overall_status',
        'executive_summary',
        'accomplishments',
        'upcoming_activities',
        'risks_issues_summary',
        'published_at',
    ];

    protected $casts = [
        'reporting_period_start' => 'date',
        'reporting_period_end' => 'date',
        'published_at' => 'datetime',
        'overall_status' => RagStatus::class,
    ];

    public function project()
    {
        return $this->belongsTo(Project::class);
    }

    public function snapshots()
    {
        return $this->hasMany(StatusReportSnapshot::class);
    }

    public function isPublished(): bool
    {
        return !is_null($this->published_at);
    }

    public function isLocked(): bool
    {
        return $this->isPublished();
    }

    public function getActivitylogOptions(): LogOptions
    {
        return LogOptions::defaults()->logFillable();
    }

    protected static function booted()
    {
        static::updating(function ($report) {
            if ($report->isLocked() && $report->isDirty()) {
                throw new \Exception('Cannot update published status report.');
            }
        });
    }
}
```

- **Validation:** Model exists, immutability enforced when published_at is set.

------

### Step 56: Create StatusReportSnapshot Model

- **Goal:** Define snapshot model for historical captures.
- **Files Involved:** `app/Modules/StatusReports/Models/StatusReportSnapshot.php`
- **Dependencies:** Step 54
- **Content:**

```php
namespace App\Modules\StatusReports\Models;

use Illuminate\Database\Eloquent\Model;

class StatusReportSnapshot extends Model
{
    protected $fillable = [
        'status_report_id',
        'snapshot_data',
        'captured_at',
    ];

    protected $casts = [
        'snapshot_data' => 'array',
        'captured_at' => 'datetime',
    ];

    public function statusReport()
    {
        return $this->belongsTo(StatusReport::class);
    }
}
```

- **Validation:** Model exists with JSON casting for snapshot_data.

------

### Step 57: Test StatusReport Model in Tinker

- **Goal:** Verify model creation and immutability constraint.
- **Files Involved:** N/A
- **Dependencies:** Steps 55, 56
- **Commands:**

```php
$report = App\Modules\StatusReports\Models\StatusReport::create([
    'project_id' => 1,
    'title' => 'Q1 Status',
    'reporting_period_start' => '2026-01-01',
    'reporting_period_end' => '2026-01-31',
    'overall_status' => 'green',
]);
$report->update(['published_at' => now()]);
$report->update(['title' => 'Updated']); // Should throw exception
```

- **Validation:** Report created, publishing works, update after publish throws exception.

------

### Step 58: Create CreateStatusReportData DTO

- **Goal:** Define DTO for report creation.
- **Files Involved:** `app/Modules/StatusReports/DTOs/CreateStatusReportData.php`
- **Dependencies:** Step 51
- **Content:**

```php
namespace App\Modules\StatusReports\DTOs;

use Carbon\Carbon;

class CreateStatusReportData
{
    public function __construct(
        public readonly int $project_id,
        public readonly string $title,
        public readonly Carbon $reporting_period_start,
        public readonly Carbon $reporting_period_end,
        public readonly string $overall_status,
        public readonly ?string $executive_summary = null,
        public readonly ?string $accomplishments = null,
        public readonly ?string $upcoming_activities = null,
        public readonly ?string $risks_issues_summary = null,
    ) {}

    public static function fromArray(array $data): self
    {
        return new self(
            project_id: $data['project_id'],
            title: $data['title'],
            reporting_period_start: Carbon::parse($data['reporting_period_start']),
            reporting_period_end: Carbon::parse($data['reporting_period_end']),
            overall_status: $data['overall_status'],
            executive_summary: $data['executive_summary'] ?? null,
            accomplishments: $data['accomplishments'] ?? null,
            upcoming_activities: $data['upcoming_activities'] ?? null,
            risks_issues_summary: $data['risks_issues_summary'] ?? null,
        );
    }

    public function toArray(): array
    {
        return [
            'project_id' => $this->project_id,
            'title' => $this->title,
            'reporting_period_start' => $this->reporting_period_start->toDateString(),
            'reporting_period_end' => $this->reporting_period_end->toDateString(),
            'overall_status' => $this->overall_status,
            'executive_summary' => $this->executive_summary,
            'accomplishments' => $this->accomplishments,
            'upcoming_activities' => $this->upcoming_activities,
            'risks_issues_summary' => $this->risks_issues_summary,
        ];
    }
}
```

- **Validation:** DTO instantiates correctly with all fields.

------

### Step 59: Create UpdateStatusReportData DTO

- **Goal:** Define DTO for report updates.
- **Files Involved:** `app/Modules/StatusReports/DTOs/UpdateStatusReportData.php`
- **Dependencies:** Step 51
- **Content:**

```php
namespace App\Modules\StatusReports\DTOs;

use Carbon\Carbon;

class UpdateStatusReportData
{
    public function __construct(
        public readonly ?string $title = null,
        public readonly ?Carbon $reporting_period_start = null,
        public readonly ?Carbon $reporting_period_end = null,
        public readonly ?string $overall_status = null,
        public readonly ?string $executive_summary = null,
        public readonly ?string $accomplishments = null,
        public readonly ?string $upcoming_activities = null,
        public readonly ?string $risks_issues_summary = null,
    ) {}

    public static function fromArray(array $data): self
    {
        return new self(
            title: $data['title'] ?? null,
            reporting_period_start: isset($data['reporting_period_start']) ? Carbon::parse($data['reporting_period_start']) : null,
            reporting_period_end: isset($data['reporting_period_end']) ? Carbon::parse($data['reporting_period_end']) : null,
            overall_status: $data['overall_status'] ?? null,
            executive_summary: $data['executive_summary'] ?? null,
            accomplishments: $data['accomplishments'] ?? null,
            upcoming_activities: $data['upcoming_activities'] ?? null,
            risks_issues_summary: $data['risks_issues_summary'] ?? null,
        );
    }

    public function toArray(): array
    {
        return array_filter([
            'title' => $this->title,
            'reporting_period_start' => $this->reporting_period_start?->toDateString(),
            'reporting_period_end' => $this->reporting_period_end?->toDateString(),
            'overall_status' => $this->overall_status,
            'executive_summary' => $this->executive_summary,
            'accomplishments' => $this->accomplishments,
            'upcoming_activities' => $this->upcoming_activities,
            'risks_issues_summary' => $this->risks_issues_summary,
        ], fn($value) => !is_null($value));
    }
}
```

- **Validation:** DTO handles partial updates correctly.

------

### Step 60: Create StatusReportSnapshotData DTO

- **Goal:** Define DTO for snapshot data.
- **Files Involved:** `app/Modules/StatusReports/DTOs/StatusReportSnapshotData.php`
- **Dependencies:** Step 51
- **Content:**

```php
namespace App\Modules\StatusReports\DTOs;

use Carbon\Carbon;

class StatusReportSnapshotData
{
    public function __construct(
        public readonly int $status_report_id,
        public readonly array $snapshot_data,
        public readonly Carbon $captured_at,
    ) {}

    public static function fromStatusReport($report): self
    {
        return new self(
            status_report_id: $report->id,
            snapshot_data: $report->toArray(),
            captured_at: now(),
        );
    }

    public function toArray(): array
    {
        return [
            'status_report_id' => $this->status_report_id,
            'snapshot_data' => $this->snapshot_data,
            'captured_at' => $this->captured_at,
        ];
    }
}
```

- **Validation:** DTO creates snapshots from report models.

------

### Step 61: Create StatusReportRepository

- **Goal:** Implement data access layer with query scoping.
- **Files Involved:** `app/Modules/StatusReports/Repositories/StatusReportRepository.php`
- **Dependencies:** Step 55
- **Content:**

```php
namespace App\Modules\StatusReports\Repositories;

use App\Modules\StatusReports\Models\StatusReport;
use Illuminate\Database\Eloquent\Collection;

class StatusReportRepository
{
    public function findById(int $id): ?StatusReport
    {
        return StatusReport::with(['project', 'snapshots'])->find($id);
    }

    public function findByProject(int $projectId): Collection
    {
        return StatusReport::where('project_id', $projectId)
            ->with('snapshots')
            ->orderBy('reporting_period_start', 'desc')
            ->get();
    }

    public function findPublished(int $projectId): Collection
    {
        return StatusReport::where('project_id', $projectId)
            ->whereNotNull('published_at')
            ->orderBy('published_at', 'desc')
            ->get();
    }

    public function findDrafts(int $projectId): Collection
    {
        return StatusReport::where('project_id', $projectId)
            ->whereNull('published_at')
            ->orderBy('created_at', 'desc')
            ->get();
    }

    public function all(): Collection
    {
        return StatusReport::with('project')->orderBy('created_at', 'desc')->get();
    }
}
```

- **Validation:** Repository methods return correct filtered data.

------

### Step 62: Create StatusReportPublished Event

- **Goal:** Define event fired when report is published.
- **Files Involved:** `app/Modules/StatusReports/Events/StatusReportPublished.php`
- **Dependencies:** Step 55
- **Content:**

```php
namespace App\Modules\StatusReports\Events;

use App\Modules\StatusReports\Models\StatusReport;
use Illuminate\Foundation\Events\Dispatchable;
use Illuminate\Queue\SerializesModels;

class StatusReportPublished
{
    use Dispatchable, SerializesModels;

    public function __construct(
        public StatusReport $statusReport
    ) {}
}
```

- **Validation:** Event class exists and can be dispatched.

------

### Step 63: Create StatusReportService - Create Method

- **Goal:** Implement service method for creating reports.
- **Files Involved:** `app/Modules/StatusReports/Services/StatusReportService.php`
- **Dependencies:** Steps 55, 58, 61
- **Content:**

```php
namespace App\Modules\StatusReports\Services;

use App\Modules\StatusReports\Models\StatusReport;
use App\Modules\StatusReports\DTOs\CreateStatusReportData;
use App\Modules\StatusReports\Repositories\StatusReportRepository;

class StatusReportService
{
    public function __construct(
        private StatusReportRepository $repository
    ) {}

    public function create(CreateStatusReportData $data): StatusReport
    {
        return StatusReport::create($data->toArray());
    }
}
```

- **Validation:** Service creates reports successfully via DTO.

------

### Step 64: StatusReportService - Add Update Method

- **Goal:** Implement report update logic with immutability check.
- **Files Involved:** `app/Modules/StatusReports/Services/StatusReportService.php`
- **Dependencies:** Steps 59, 63
- **Changes:** Add method:

```php
public function update(int $id, UpdateStatusReportData $data): StatusReport
{
    $report = $this->repository->findById($id);

    if (!$report) {
        throw new \Exception("Status report not found.");
    }

    if ($report->isLocked()) {
        throw new \Exception("Cannot update published status report.");
    }

    $report->update($data->toArray());

    return $report->fresh();
}
```

- **Validation:** Update works for drafts, throws exception for published reports.

------

### Step 65: StatusReportService - Add Publish Method

- **Goal:** Implement publish logic that locks report and fires event.
- **Files Involved:** `app/Modules/StatusReports/Services/StatusReportService.php`
- **Dependencies:** Steps 62, 64
- **Changes:** Add method:

```php
use App\Modules\StatusReports\Events\StatusReportPublished;

public function publish(int $id): StatusReport
{
    $report = $this->repository->findById($id);

    if (!$report) {
        throw new \Exception("Status report not found.");
    }

    if ($report->isPublished()) {
        throw new \Exception("Report is already published.");
    }

    $report->update(['published_at' => now()]);

    event(new StatusReportPublished($report));

    return $report->fresh();
}
```

- **Validation:** Publishing sets timestamp, fires event, locks report.

------

### Step 66: StatusReportService - Add Snapshot Capture Method

- **Goal:** Implement snapshot creation at publish time.
- **Files Involved:** `app/Modules/StatusReports/Services/StatusReportService.php`
- **Dependencies:** Steps 56, 60, 65
- **Changes:** Add method:

```php
use App\Modules\StatusReports\Models\StatusReportSnapshot;
use App\Modules\StatusReports\DTOs\StatusReportSnapshotData;

public function captureSnapshot(StatusReport $report): StatusReportSnapshot
{
    $snapshotData = StatusReportSnapshotData::fromStatusReport($report);

    return StatusReportSnapshot::create($snapshotData->toArray());
}
```

- **Validation:** Snapshot created with full report data as JSON.

------

### Step 67: Update Publish Method to Auto-Capture Snapshot

- **Goal:** Automatically create snapshot when report is published.
- **Files Involved:** `app/Modules/StatusReports/Services/StatusReportService.php`
- **Dependencies:** Step 66
- **Changes:** Modify publish method:

```php
public function publish(int $id): StatusReport
{
    $report = $this->repository->findById($id);

    if (!$report) {
        throw new \Exception("Status report not found.");
    }

    if ($report->isPublished()) {
        throw new \Exception("Report is already published.");
    }

    $report->update(['published_at' => now()]);

    // Capture snapshot at publish time
    $this->captureSnapshot($report);

    event(new StatusReportPublished($report));

    return $report->fresh(['snapshots']);
}
```

- **Validation:** Publishing creates snapshot automatically.

------

### Step 68: StatusReportService - Add Delete Method

- **Goal:** Implement soft delete with authorization check.
- **Files Involved:** `app/Modules/StatusReports/Services/StatusReportService.php`
- **Dependencies:** Step 65
- **Changes:** Add method:

```php
public function delete(int $id): bool
{
    $report = $this->repository->findById($id);

    if (!$report) {
        throw new \Exception("Status report not found.");
    }

    return $report->delete();
}
```

- **Validation:** Soft delete works, deleted_at timestamp set.

------

### Step 69: StatusReportService - Add List Methods

- **Goal:** Add convenience methods for listing reports.
- **Files Involved:** `app/Modules/StatusReports/Services/StatusReportService.php`
- **Dependencies:** Step 61
- **Changes:** Add methods:

```php
public function getById(int $id): ?StatusReport
{
    return $this->repository->findById($id);
}

public function getByProject(int $projectId)
{
    return $this->repository->findByProject($projectId);
}

public function getPublished(int $projectId)
{
    return $this->repository->findPublished($projectId);
}

public function getDrafts(int $projectId)
{
    return $this->repository->findDrafts($projectId);
}

public function getAll()
{
    return $this->repository->all();
}
```

- **Validation:** List methods return correct filtered collections, getById returns single report.

------

### Step 70: Test StatusReportService in Tinker

- **Goal:** Verify complete service layer functionality.
- **Files Involved:** N/A
- **Dependencies:** Step 69
- **Commands:**

```php
$service = new App\Modules\StatusReports\Services\StatusReportService(
    new App\Modules\StatusReports\Repositories\StatusReportRepository()
);
$dto = App\Modules\StatusReports\DTOs\CreateStatusReportData::fromArray([
    'project_id' => 1,
    'title' => 'Test Report',
    'reporting_period_start' => '2026-01-01',
    'reporting_period_end' => '2026-01-31',
    'overall_status' => 'green',
]);
$report = $service->create($dto);
$service->publish($report->id);
$report->fresh()->snapshots()->count(); // Should be 1
```

- **Validation:** Full workflow works end-to-end.

------

### Step 71: Create StatusReportPolicy

- **Goal:** Implement RBAC authorization rules.
- **Files Involved:** `app/Modules/StatusReports/Policies/StatusReportPolicy.php`
- **Dependencies:** Step 55
- **Content:**

```php
namespace App\Modules\StatusReports\Policies;

use App\Models\User;
use App\Modules\StatusReports\Models\StatusReport;

class StatusReportPolicy
{
    public function viewAny(User $user): bool
    {
        return true; // All authenticated users
    }

    public function view(User $user, StatusReport $report): bool
    {
        return true; // All authenticated users
    }

    public function create(User $user): bool
    {
        return $user->isAdmin() || $user->isEditor();
    }

    public function update(User $user, StatusReport $report): bool
    {
        if ($report->isLocked()) {
            return false; // No one can update published reports
        }

        if ($user->isAdmin()) {
            return true;
        }

        if ($user->isEditor()) {
            return $user->projects->contains($report->project_id);
        }

        return false;
    }

    public function delete(User $user, StatusReport $report): bool
    {
        if ($user->isAdmin()) {
            return true;
        }

        if ($user->isEditor()) {
            return $user->projects->contains($report->project_id) && !$report->isPublished();
        }

        return false;
    }

    public function publish(User $user, StatusReport $report): bool
    {
        return $this->update($user, $report);
    }
}
```

- **Validation:** Policy methods enforce role-based and state-based rules correctly.

------

### Step 72: Register StatusReportPolicy

- **Goal:** Map policy to model in AuthServiceProvider.
- **Files Involved:** `app/Providers/AuthServiceProvider.php`
- **Dependencies:** Step 71
- **Changes:**

```php
use App\Modules\StatusReports\Models\StatusReport;
use App\Modules\StatusReports\Policies\StatusReportPolicy;

protected $policies = [
    // ... existing policies
    StatusReport::class => StatusReportPolicy::class,
];
```

- **Validation:** Authorization checks work in controllers.

------

### Step 73: Create API StoreStatusReportRequest

- **Goal:** Validate API creation requests.
- **Files Involved:** `app/Modules/StatusReports/FormRequests/Api/StoreStatusReportRequest.php`
- **Dependencies:** Step 51
- **Content:**

```php
namespace App\Modules\StatusReports\FormRequests\Api;

use Illuminate\Foundation\Http\FormRequest;

class StoreStatusReportRequest extends FormRequest
{
    public function authorize(): bool
    {
        return $this->user()->can('create', \App\Modules\StatusReports\Models\StatusReport::class);
    }

    public function rules(): array
    {
        return [
            'project_id' => 'required|exists:projects,id',
            'title' => 'required|string|max:255',
            'reporting_period_start' => 'required|date',
            'reporting_period_end' => 'required|date|after_or_equal:reporting_period_start',
            'overall_status' => 'required|in:red,amber,green',
            'executive_summary' => 'nullable|string',
            'accomplishments' => 'nullable|string',
            'upcoming_activities' => 'nullable|string',
            'risks_issues_summary' => 'nullable|string',
        ];
    }
}
```

- **Validation:** Validation rules catch invalid data, authorization integrated.

------

### Step 74: Create API UpdateStatusReportRequest

- **Goal:** Validate API update requests.
- **Files Involved:** `app/Modules/StatusReports/FormRequests/Api/UpdateStatusReportRequest.php`
- **Dependencies:** Step 51
- **Content:**

```php
namespace App\Modules\StatusReports\FormRequests\Api;

use Illuminate\Foundation\Http\FormRequest;

class UpdateStatusReportRequest extends FormRequest
{
    public function authorize(): bool
    {
        $report = $this->route('status_report');
        return $this->user()->can('update', $report);
    }

    public function rules(): array
    {
        return [
            'title' => 'sometimes|string|max:255',
            'reporting_period_start' => 'sometimes|date',
            'reporting_period_end' => 'sometimes|date|after_or_equal:reporting_period_start',
            'overall_status' => 'sometimes|in:red,amber,green',
            'executive_summary' => 'nullable|string',
            'accomplishments' => 'nullable|string',
            'upcoming_activities' => 'nullable|string',
            'risks_issues_summary' => 'nullable|string',
        ];
    }
}
```

- **Validation:** Partial update validation works correctly.

------

### Step 75: Create StatusReportResource

- **Goal:** Build JSON transformer for API responses.
- **Files Involved:** `app/Modules/StatusReports/Resources/StatusReportResource.php`
- **Dependencies:** Step 55
- **Content:**

```php
namespace App\Modules\StatusReports\Resources;

use Illuminate\Http\Resources\Json\JsonResource;

class StatusReportResource extends JsonResource
{
    public function toArray($request): array
    {
        return [
            'id' => $this->id,
            'project_id' => $this->project_id,
            'project_name' => $this->project->name,
            'title' => $this->title,
            'reporting_period_start' => $this->reporting_period_start->toDateString(),
            'reporting_period_end' => $this->reporting_period_end->toDateString(),
            'overall_status' => $this->overall_status->value,
            'overall_status_label' => $this->overall_status->label(),
            'executive_summary' => $this->executive_summary,
            'accomplishments' => $this->accomplishments,
            'upcoming_activities' => $this->upcoming_activities,
            'risks_issues_summary' => $this->risks_issues_summary,
            'published_at' => $this->published_at?->toISOString(),
            'is_published' => $this->isPublished(),
            'is_locked' => $this->isLocked(),
            'created_at' => $this->created_at->toISOString(),
            'updated_at' => $this->updated_at->toISOString(),
        ];
    }
}
```

- **Validation:** Resource formats data consistently for API.

------

### Step 76: Create StatusReportSnapshotResource

- **Goal:** Build JSON transformer for snapshots.
- **Files Involved:** `app/Modules/StatusReports/Resources/StatusReportSnapshotResource.php`
- **Dependencies:** Step 56
- **Content:**

```php
namespace App\Modules\StatusReports\Resources;

use Illuminate\Http\Resources\Json\JsonResource;

class StatusReportSnapshotResource extends JsonResource
{
    public function toArray($request): array
    {
        return [
            'id' => $this->id,
            'status_report_id' => $this->status_report_id,
            'snapshot_data' => $this->snapshot_data,
            'captured_at' => $this->captured_at->toISOString(),
            'created_at' => $this->created_at->toISOString(),
        ];
    }
}
```

- **Validation:** Snapshot resource returns correct JSON structure.

------

### Step 77: Create API StatusReportController - Index Method

- **Goal:** List all status reports with filtering.
- **Files Involved:** `app/Modules/StatusReports/Controllers/Api/StatusReportController.php`
- **Dependencies:** Steps 69, 75
- **Content:**

```php
namespace App\Modules\StatusReports\Controllers\Api;

use App\Http\Controllers\Controller;
use App\Modules\StatusReports\Services\StatusReportService;
use App\Modules\StatusReports\Resources\StatusReportResource;
use Illuminate\Http\Request;

class StatusReportController extends Controller
{
    public function __construct(
        private StatusReportService $service
    ) {}

    public function index(Request $request)
    {
        $this->authorize('viewAny', \App\Modules\StatusReports\Models\StatusReport::class);

        $projectId = $request->query('project_id');

        if ($projectId) {
            $reports = $this->service->getByProject($projectId);
        } else {
            $reports = $this->service->getAll();
        }

        return StatusReportResource::collection($reports);
    }
}
```

- **Validation:** GET /api/status-reports returns paginated JSON.

------

### Step 78: API StatusReportController - Store Method

- **Goal:** Create new status report via API.
- **Files Involved:** `app/Modules/StatusReports/Controllers/Api/StatusReportController.php`
- **Dependencies:** Steps 73, 77
- **Changes:** Add method:

```php
use App\Modules\StatusReports\FormRequests\Api\StoreStatusReportRequest;
use App\Modules\StatusReports\DTOs\CreateStatusReportData;

public function store(StoreStatusReportRequest $request)
{
    $dto = CreateStatusReportData::fromArray($request->validated());
    $report = $this->service->create($dto);

    return new StatusReportResource($report);
}
```

- **Validation:** POST /api/status-reports creates report and returns 201.

------

### Step 79: API StatusReportController - Show Method

- **Goal:** Retrieve single status report by ID.
- **Files Involved:** `app/Modules/StatusReports/Controllers/Api/StatusReportController.php`
- **Dependencies:** Step 77
- **Changes:** Add method:

```php
use App\Modules\StatusReports\Models\StatusReport;

public function show(StatusReport $statusReport)
{
    $this->authorize('view', $statusReport);

    return new StatusReportResource($statusReport->load(['project', 'snapshots']));
}
```

- **Validation:** GET /api/status-reports/{id} returns single report with relationships.

------

### Step 80: API StatusReportController - Update Method

- **Goal:** Update existing status report.
- **Files Involved:** `app/Modules/StatusReports/Controllers/Api/StatusReportController.php`
- **Dependencies:** Steps 74, 79
- **Changes:** Add method:

```php
use App\Modules\StatusReports\FormRequests\Api\UpdateStatusReportRequest;
use App\Modules\StatusReports\DTOs\UpdateStatusReportData;

public function update(UpdateStatusReportRequest $request, StatusReport $statusReport)
{
    $dto = UpdateStatusReportData::fromArray($request->validated());
    $updated = $this->service->update($statusReport->id, $dto);

    return new StatusReportResource($updated);
}
```

- **Validation:** PUT/PATCH /api/status-reports/{id} updates report, returns 200.

------

### Step 81: API StatusReportController - Destroy Method

- **Goal:** Soft delete status report.
- **Files Involved:** `app/Modules/StatusReports/Controllers/Api/StatusReportController.php`
- **Dependencies:** Step 79
- **Changes:** Add method:

```php
public function destroy(StatusReport $statusReport)
{
    $this->authorize('delete', $statusReport);

    $this->service->delete($statusReport->id);

    return response()->json(['message' => 'Status report deleted successfully'], 200);
}
```

- **Validation:** DELETE /api/status-reports/{id} soft deletes, returns 200.

------

### Step 82: API StatusReportController - Publish Method

- **Goal:** Add custom endpoint to publish report.
- **Files Involved:** `app/Modules/StatusReports/Controllers/Api/StatusReportController.php`
- **Dependencies:** Step 81
- **Changes:** Add method:

```php
public function publish(StatusReport $statusReport)
{
    $this->authorize('publish', $statusReport);

    $published = $this->service->publish($statusReport->id);

    return new StatusReportResource($published);
}
```

- **Validation:** POST /api/status-reports/{id}/publish locks report and returns updated resource.

------

### Step 83: Register API Routes for StatusReports

- **Goal:** Define RESTful routes in api.php.
- **Files Involved:** `routes/api.php`
- **Dependencies:** Step 82
- **Changes:**

```php
use App\Modules\StatusReports\Controllers\Api\StatusReportController;

Route::middleware('auth:sanctum')->group(function () {
    Route::apiResource('status-reports', StatusReportController::class);
    Route::post('status-reports/{statusReport}/publish', [StatusReportController::class, 'publish']);
});
```

- **Validation:** `php artisan route:list` shows all status report API routes.

------

### Step 84: Test API Endpoints with HTTP Client

- **Goal:** Verify all API endpoints work end-to-end.
- **Files Involved:** N/A
- **Dependencies:** Step 83
- **Commands:** Use Postman/Insomnia or curl
- **Validation:** All CRUD operations work, authorization enforced.

------

### Step 85: Create Web StoreStatusReportRequest

- **Goal:** Validate web form submissions.
- **Files Involved:** `app/Modules/StatusReports/FormRequests/Web/StoreStatusReportRequest.php`
- **Dependencies:** Step 73 (same rules, different namespace)
- **Content:** Same as API version but in Web namespace
- **Validation:** Web form validation works independently of API.

------

### Step 86: Create Web UpdateStatusReportRequest

- **Goal:** Validate web update forms.
- **Files Involved:** `app/Modules/StatusReports/FormRequests/Web/UpdateStatusReportRequest.php`
- **Dependencies:** Step 74 (same rules, different namespace)
- **Content:** Same as API version but in Web namespace
- **Validation:** Web update validation works correctly.

------

### Step 87: Create StatusReportViewModel

- **Goal:** Prepare data for Blade views.
- **Files Involved:** `app/Modules/StatusReports/ViewModels/StatusReportViewModel.php`
- **Dependencies:** Step 55
- **Content:**

```php
namespace App\Modules\StatusReports\ViewModels;

use App\Modules\StatusReports\Models\StatusReport;
use App\Modules\Shared\Enums\RagStatus;

class StatusReportViewModel
{
    public function __construct(
        private ?StatusReport $report = null
    ) {}

    public function report(): ?StatusReport
    {
        return $this->report;
    }

    public function ragStatusOptions(): array
    {
        return collect(RagStatus::cases())
            ->mapWithKeys(fn($status) => [$status->value => $status->label()])
            ->toArray();
    }

    public function isEditable(): bool
    {
        return $this->report && !$this->report->isLocked();
    }

    public function formAction(): string
    {
        return $this->report
            ? route('status-reports.update', $this->report)
            : route('status-reports.store');
    }

    public function formMethod(): string
    {
        return $this->report ? 'PUT' : 'POST';
    }
}
```

- **Validation:** ViewModel provides clean data to views.

------

### Step 88: Create Web StatusReportController - Index Method

- **Goal:** Display list of status reports.
- **Files Involved:** `app/Modules/StatusReports/Controllers/Web/StatusReportController.php`
- **Dependencies:** Step 69
- **Content:**

```php
namespace App\Modules\StatusReports\Controllers\Web;

use App\Http\Controllers\Controller;
use App\Modules\StatusReports\Services\StatusReportService;
use Illuminate\Http\Request;

class StatusReportController extends Controller
{
    public function __construct(
        private StatusReportService $service
    ) {}

    public function index(Request $request)
    {
        $this->authorize('viewAny', \App\Modules\StatusReports\Models\StatusReport::class);

        $projectId = $request->query('project_id');

        $reports = $projectId
            ? $this->service->getByProject($projectId)
            : $this->service->getAll();

        return view('status-reports::index', compact('reports'));
    }
}
```

- **Validation:** Controller method compiles without errors.

------

### Step 89: Create Index Blade View

- **Goal:** Build table view listing all reports.
- **Files Involved:** `app/Modules/StatusReports/Views/index.blade.php`
- **Dependencies:** Steps 13, 88
- **Content:** Table with reports list, links to create/edit/view
- **Validation:** Page renders with Tailwind styling, table displays reports.

------

### Step 90: Web Controller - Create Method

- **Goal:** Display report creation form.
- **Files Involved:** `app/Modules/StatusReports/Controllers/Web/StatusReportController.php`
- **Dependencies:** Steps 87, 88
- **Changes:** Add method:

```php
use App\Modules\StatusReports\ViewModels\StatusReportViewModel;
use App\Models\Project;

public function create()
{
    $this->authorize('create', \App\Modules\StatusReports\Models\StatusReport::class);

    $viewModel = new StatusReportViewModel();
    $projects = Project::all();

    return view('status-reports::create', compact('viewModel', 'projects'));
}
```

- **Validation:** Route returns view correctly.

------

### Step 91: Create Form Blade View

- **Goal:** Build report creation form.
- **Files Involved:** `app/Modules/StatusReports/Views/create.blade.php`
- **Dependencies:** Steps 13, 90
- **Content:** Full form with all fields
- **Validation:** Form renders, validation errors display, old input repopulated.

------

### Step 92: Web Controller - Store Method

- **Goal:** Process form submission and create report.
- **Files Involved:** `app/Modules/StatusReports/Controllers/Web/StatusReportController.php`
- **Dependencies:** Steps 85, 91
- **Changes:** Add store method
- **Validation:** Form submission creates report and redirects with success message.

------

### Step 93: Web Controller - Show Method

- **Goal:** Display single report details.
- **Files Involved:** `app/Modules/StatusReports/Controllers/Web/StatusReportController.php`
- **Dependencies:** Step 92
- **Changes:** Add method:

```php
use App\Modules\StatusReports\Models\StatusReport;

public function show(StatusReport $statusReport)
{
    $this->authorize('view', $statusReport);

    $statusReport->load(['project', 'snapshots']);

    return view('status-reports::show', compact('statusReport'));
}
```

- **Validation:** Show route returns view with loaded relationships.

------

### Step 94: Create Show Blade View

- **Goal:** Build report detail view.
- **Files Involved:** `app/Modules/StatusReports/Views/show.blade.php`
- **Dependencies:** Steps 13, 93
- **Content:** Full report display with sections
- **Validation:** Report displays with all sections, publish button shows when appropriate.

------

### Step 95: Web Controller - Edit Method

- **Goal:** Display report edit form.
- **Files Involved:** `app/Modules/StatusReports/Controllers/Web/StatusReportController.php`
- **Dependencies:** Step 93
- **Changes:** Add method:

```php
public function edit(StatusReport $statusReport)
{
    $this->authorize('update', $statusReport);

    $viewModel = new StatusReportViewModel($statusReport);
    $projects = Project::all();

    return view('status-reports::edit', compact('statusReport', 'viewModel', 'projects'));
}
```

- **Validation:** Edit route loads existing data.

------

### Step 96: Create Edit Form Blade View

- **Goal:** Build pre-populated edit form.
- **Files Involved:** `app/Modules/StatusReports/Views/edit.blade.php`
- **Dependencies:** Steps 13, 95
- **Content:** Form pre-filled with existing data
- **Validation:** Form pre-fills with existing data, updates work.

------

### Step 97: Web Controller - Update Method

- **Goal:** Process form submission and update report.
- **Files Involved:** `app/Modules/StatusReports/Controllers/Web/StatusReportController.php`
- **Dependencies:** Steps 86, 96
- **Changes:** Add update method
- **Validation:** Update works, redirects with success message.

------

### Step 98: Web Controller - Destroy Method

- **Goal:** Soft delete report from web interface.
- **Files Involved:** `app/Modules/StatusReports/Controllers/Web/StatusReportController.php`
- **Dependencies:** Step 97
- **Changes:** Add destroy method
- **Validation:** Delete button works, soft deletes report.

------

### Step 99: Web Controller - Publish Method

- **Goal:** Web route to publish report.
- **Files Involved:** `app/Modules/StatusReports/Controllers/Web/StatusReportController.php`
- **Dependencies:** Step 98
- **Changes:** Add publish method
- **Validation:** Publish button locks report and shows success.

------

### Step 100: Register Web Routes for StatusReports

- **Goal:** Define resourceful and custom routes.
- **Files Involved:** `routes/web.php`
- **Dependencies:** Step 99
- **Changes:**

```php
use App\Modules\StatusReports\Controllers\Web\StatusReportController;

Route::middleware('auth')->group(function () {
    Route::resource('status-reports', StatusReportController::class);
    Route::post('status-reports/{statusReport}/publish', [StatusReportController::class, 'publish'])->name('status-reports.publish');
});
```

- **Validation:** `php artisan route:list --path=status-reports` shows all web routes.

------

### Step 101: Create Livewire StatusReportList Component Class

- **Goal:** Build reactive list component.
- **Files Involved:** `app/Modules/StatusReports/Livewire/StatusReportList.php`
- **Dependencies:** Step 69
- **Content:** Component class with projectId property
- **Validation:** Component class compiles without errors.

------

### Step 102: Create Livewire List Component View

- **Goal:** Build reactive table view.
- **Files Involved:** `resources/views/livewire/status-reports/list.blade.php`
- **Dependencies:** Step 101
- **Content:** Table with wire:model bindings
- **Validation:** Component updates reactively on property changes.

------

### Step 103: Create Livewire StatusReportForm Component Class

- **Goal:** Build reactive create/edit form.
- **Files Involved:** `app/Modules/StatusReports/Livewire/StatusReportForm.php`
- **Dependencies:** Step 69
- **Content:** Form component with validation
- **Validation:** Form handles both create and edit modes.

------

### Step 104: Create Livewire Form Component View

- **Goal:** Build reactive form with real-time validation.
- **Files Involved:** `resources/views/livewire/status-reports/form.blade.php`
- **Dependencies:** Step 103
- **Content:** Form with wire:model.live bindings
- **Validation:** Form validates in real-time, shows success messages.

------

### Step 105: Create Livewire StatusReportCard Component Class

- **Goal:** Build reusable card component for dashboards.
- **Files Involved:** `app/Modules/StatusReports/Livewire/StatusReportCard.php`
- **Dependencies:** Step 55
- **Content:** Card component class
- **Validation:** Component accepts report model and renders.

------

### Step 106: Create Livewire Card Component View

- **Goal:** Build compact card layout.
- **Files Involved:** `resources/views/livewire/status-reports/card.blade.php`
- **Dependencies:** Step 105
- **Content:** Card with summary display
- **Validation:** Card displays report summary attractively.

------

### Step 107: Test Complete StatusReports Module

- **Goal:** Verify all three interfaces work end-to-end.
- **Files Involved:** N/A
- **Dependencies:** Steps 84, 100, 106
- **Test Cases:**
  - API: Create report via POST, publish via POST, verify immutability
  - Web: Create via form, edit, publish, verify locked state
  - Livewire: Use components in test page, verify reactive updates
- **Validation:** All interfaces functional, RBAC enforced, immutability works.

------

### Step 108: Create Feature Tests for StatusReports

- **Goal:** Write automated tests for all endpoints.
- **Files Involved:** `tests/Feature/Modules/StatusReports/`
- **Dependencies:** Step 107
- **Test Files:**
  - `ApiStatusReportTest.php`
  - `WebStatusReportTest.php`
  - `LivewireStatusReportTest.php`
- **Validation:** `php artisan test --filter=StatusReport` passes all tests.

------

### Step 109: Create Unit Tests for StatusReport Service

- **Goal:** Test business logic in isolation.
- **Files Involved:** `tests/Unit/Modules/StatusReports/Services/StatusReportServiceTest.php`
- **Dependencies:** Step 69
- **Test Cases:**
  - Create report
  - Update draft report
  - Attempt to update published report (should throw exception)
  - Publish report (should fire event and create snapshot)
  - Delete report
- **Validation:** All unit tests pass with correct assertions.

------

### Step 110: Document StatusReports API in Comments

- **Goal:** Add PHPDoc blocks for API documentation generation.
- **Files Involved:** `app/Modules/StatusReports/Controllers/Api/StatusReportController.php`
- **Dependencies:** Step 82
- **Changes:** Add docblocks to each method for Scribe documentation
- **Validation:** Documentation annotations complete and accurate.

------

## Questions & Clarifications

### Specification Ambiguities Requiring Resolution:

1. **Flux UI Component Library Version & Integration:**
   - Which specific version of Flux UI should be used?
   - Are there specific Flux components documented that must be utilized?
   - Should we integrate Flux via CDN or build process?
2. **Week Calculation Display Logic:**
   - Should "Week 4 of January" represent ISO week 4 if it falls in January, or actual week number within January (1-5)?
   - How should edge cases be handled (ISO weeks spanning two months)?
3. **Status Report Snapshot Technology Details:**
   - Should snapshots capture *all* related data (risks, milestones, etc.) or only status report fields?
   - Are snapshots point-in-time copies stored as JSON or references to historical records?
   - Is there a retention policy for snapshots?
4. **Entity Linking Scope Expansion:**
   - Is V1 limitation "Only ProjectJournal entries can link" a hard technical constraint or business rule that may expand?
5. **RBAC - Editor Project Assignment Mechanism:**
   - How are Editors assigned to projects? Manual by Admin, automatic based on team, or self-service?
6. **Internal Visibility Flag Implementation:**
   - Which specific modules support `is_internal_only`?
   - Should this be a global query scope or manually applied per query?
7. **Testing Requirements - Coverage Thresholds:**
   - What are the minimum acceptable code coverage percentages?
   - Are there specific critical paths that require 100% coverage?
8. **MeetingEvents vs MeetingMinutes Relationship:**
   - Are these linked (MeetingEvent scheduled → MeetingMinutes created after)?
   - Should there be a foreign key relationship?
9. **Change Request Approval Workflow:**
   - Is there a formal multi-stage approval process?
   - Should approval history be tracked in a separate table?
10. **Lists Module - Item Hierarchy:**
    - Are list items flat, or can they have parent-child relationships (sub-tasks)?
11. **Immutability Edge Cases:**
    - If a published StatusReport contains errors, is there an "unpublish" workflow?
    - Can locked Decisions be "superseded" while preserving the original?
12. **Performance Requirements:**
    - Expected concurrent user count?
    - Expected data volume per project?
    - Any specific response time SLAs?

------

## Execution Guidelines

### How to Use This Blueprint

1. **Sequential Execution:** Execute steps in exact order - dependencies must be satisfied.
2. **One Step Per Prompt:** Feed the AI coding agent one step at a time, wait for completion and validation before proceeding.
3. **Validation Critical:** After each step, verify the validation criteria before moving forward.
4. **Test Frequently:** Run `php artisan test` after major milestones (every 10-15 steps).
5. **Commit Often:** Git commit after each successfully validated step.

### Module Replication Pattern

After completing StatusReports (Steps 51-110), replicate this exact 60-step pattern for each remaining module:

- Milestones
- ActionItems
- Deliverables
- Dependencies
- MeetingEvents
- Risks
- Issues
- ChangeRequests
- Decisions
- ProjectJournal
- MeetingMinutes
- Stakeholders
- Links
- Lists
- LessonsLearned

**Total Project Scope:** ~1,067 atomic steps (53 foundation + 60 × 16 modules + integration steps)

**Note:** Each subsequent module will benefit from the corrected patterns established in StatusReports, particularly:

- View namespace registration (already done in Step 50a-50c)
- Sanctum authentication (already configured in Steps 20a-20d)
- Complete service layer including `getById()` method

### Quality Checkpoints

Every 25 steps:

- Run full test suite
- Check code coverage
- Review architectural consistency
- Validate RBAC enforcement
- Test all three interfaces

------

## Next Steps

**Ready to begin implementation?**

Choose your starting approach:

- **Option A:** Begin Step 1 immediately (sequential execution)
- **Option B:** Review and clarify ambiguous specifications first
- **Option C:** Generate scaffolding Artisan command to accelerate module creation
- **Option D:** Customize the plan based on your priorities

------

**Document Version:** 1.1 (Corrected)
**Last Updated:** January 28, 2026
**Total Steps:** 117 (53 Foundation + 60 StatusReports Module + 4 Infrastructure)
**Estimated Completion Time:** 45-65 hours (at 20-30 min per step)

------

## Corrections Log

**Version 1.1 Changes:**

1. **Step 17:** Added complete users table migration schema with all fields
2. **Steps 20a-20d:** Added Laravel Sanctum installation and configuration (4 new steps)
3. **Step 44:** Corrected Spatie Activity Log trait usage (`LogOptions::defaults()->logAll()`)
4. **Steps 50a-50c:** Added Module View Service Provider for Blade template registration (3 new steps)
5. **Step 69:** Added missing `getById()` method to StatusReportService
6. **Steps 88, 90, 93, 95:** Corrected view paths to use namespace notation (`status-reports::viewname`)

**Critical Fixes:**

- Fixed missing `getById()` service method that would cause Livewire components to fail
- Added Sanctum authentication setup required for API routes
- Implemented view namespace registration so Laravel can find module Blade templates
- Corrected activity logging implementation to use proper Spatie syntax

**Total Step Count:** Increased from 110 to 117 steps to include missing infrastructure components.