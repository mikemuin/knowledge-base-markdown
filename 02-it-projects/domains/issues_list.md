# Domain Management System - GitHub Issues

## Issue Organization Strategy

Issues are organized into three categories:
- **Manual Terminal Tasks** - Requires developer to run commands locally
- **Automated Code Tasks** - Can be completed by coding agent (GitHub Copilot)
- **Single PR Tasks** - Can be safely merged without blocking other work

---

## MANUAL TERMINAL TASKS

### Issue #1: Environment Setup and Verification
**Labels:** `setup`, `manual`, `P0-critical`

**Description:**
Set up the local development environment with Laravel Herd and SQLite database.

**Tasks:**
- [ ] Verify Laravel 12.x is properly installed
- [ ] Verify PHP 8.2+ is available
- [ ] Configure `.env` file for SQLite:
  ```
  DB_CONNECTION=sqlite
  DB_DATABASE=/absolute/path/to/database/database.sqlite
  ```
- [ ] Create SQLite database file: `touch database/database.sqlite`
- [ ] Test database connection: `php artisan db:show`
- [ ] Verify Composer dependencies: `composer install`
- [ ] Verify Node dependencies: `npm install`

**Acceptance Criteria:**
- `.env` configured correctly
- `database.sqlite` file exists
- Database connection successful
- All dependencies installed

**Estimated Time:** 30 minutes

---

### Issue #2: Install and Configure Laravel Breeze
**Labels:** `authentication`, `manual`, `P0-critical`

**Description:**
Install Laravel Breeze for authentication scaffolding and disable registration functionality.

**Tasks:**
- [ ] Install Breeze: `composer require laravel/breeze --dev`
- [ ] Install Blade stack: `php artisan breeze:install blade`
- [ ] Install NPM dependencies: `npm install`
- [ ] Build assets: `npm run build`
- [ ] Test login page is accessible at `/login`
- [ ] Verify authentication middleware works

**Acceptance Criteria:**
- Breeze installed successfully
- Login page accessible
- Assets compiled
- Authentication routes available

**Estimated Time:** 20 minutes

**Depends On:** Issue #1

---

### Issue #3: Run Database Migrations
**Labels:** `database`, `manual`, `P0-critical`

**Description:**
Execute database migrations to create tables in SQLite.

**Tasks:**
- [ ] Run migrations: `php artisan migrate`
- [ ] Verify tables created: `php artisan db:show`
- [ ] Verify foreign key constraints are in place
- [ ] Check migration status: `php artisan migrate:status`

**Acceptance Criteria:**
- All migrations run successfully
- Tables visible in database
- Foreign key constraints working

**Estimated Time:** 5 minutes

**Depends On:** Issue #1, Issue #5

---

### Issue #4: Seed Database with Initial Data
**Labels:** `database`, `manual`, `P0-critical`

**Description:**
Populate database with admin user and sample data for testing.

**Tasks:**
- [ ] Run seeders: `php artisan db:seed`
- [ ] Verify admin user created: `admin@localhost` / `password@123`
- [ ] Test login with seeded credentials
- [ ] Verify sample domains and subdomains (if included)

**Acceptance Criteria:**
- Admin user can login successfully
- Sample data visible in database
- No seeding errors

**Estimated Time:** 5 minutes

**Depends On:** Issue #3, Issue #9

---

### Issue #5: Create Database Migrations
**Labels:** `database`, `manual`, `P0-critical`

**Description:**
Generate migration files using Artisan commands.

**Tasks:**
- [ ] Generate domains migration: `php artisan make:migration create_domains_table`
- [ ] Generate subdomains migration: `php artisan make:migration create_subdomains_table`
- [ ] Verify migration files created in `database/migrations/`

**Acceptance Criteria:**
- Two new migration files exist
- Files have proper timestamps
- Ready for code implementation

**Estimated Time:** 5 minutes

**Depends On:** Issue #1

---

### Issue #6: Create Eloquent Models
**Labels:** `models`, `manual`, `P0-critical`

**Description:**
Generate Eloquent model files using Artisan commands.

**Tasks:**
- [ ] Generate Domain model: `php artisan make:model Domain`
- [ ] Generate Subdomain model: `php artisan make:model Subdomain`
- [ ] Verify model files created in `app/Models/`

**Acceptance Criteria:**
- Two new model files exist
- Ready for relationship implementation

**Estimated Time:** 2 minutes

**Depends On:** Issue #1

---

### Issue #7: Create Controllers
**Labels:** `controllers`, `manual`, `P0-critical`

**Description:**
Generate resource controller files using Artisan commands.

**Tasks:**
- [ ] Generate DomainController: `php artisan make:controller DomainController --resource`
- [ ] Generate SubdomainController: `php artisan make:controller SubdomainController --resource`
- [ ] Verify controller files created in `app/Http/Controllers/`
- [ ] Verify all CRUD methods present (index, create, store, edit, update, destroy)

**Acceptance Criteria:**
- Two new controller files exist with resource methods
- Ready for implementation

**Estimated Time:** 2 minutes

**Depends On:** Issue #1

---

### Issue #8: Generate Routes List and Verify
**Labels:** `routing`, `manual`, `testing`, `P1-high`

**Description:**
Verify all routes are properly registered and accessible.

**Tasks:**
- [ ] Run route list: `php artisan route:list`
- [ ] Verify all expected routes exist (see PRD Appendix A)
- [ ] Test authenticated routes require login
- [ ] Test unauthenticated redirect to login

**Acceptance Criteria:**
- All routes listed correctly
- Authentication middleware applied
- Registration routes removed

**Estimated Time:** 10 minutes

**Depends On:** Issue #11, Issue #12

---

### Issue #9: Update Composer Autoload for JSON Helper
**Labels:** `configuration`, `manual`, `P0-critical`

**Description:**
Configure Composer to autoload the JSON helper file.

**Tasks:**
- [ ] Add helper to `composer.json` autoload section
- [ ] Run: `composer dump-autoload`
- [ ] Test helper function loads without errors

**Acceptance Criteria:**
- Helper function accessible globally
- No autoload errors

**Estimated Time:** 5 minutes

**Depends On:** Issue #13

---

### Issue #10: Run Automated Tests
**Labels:** `testing`, `manual`, `P1-high`

**Description:**
Execute PHPUnit test suite to verify application functionality.

**Tasks:**
- [ ] Run tests: `php artisan test`
- [ ] Review test results
- [ ] Fix any failing tests
- [ ] Verify 100% pass rate

**Acceptance Criteria:**
- All tests pass
- No errors or warnings
- Coverage meets requirements

**Estimated Time:** 15 minutes

**Depends On:** All feature issues completed

---

## AUTOMATED CODE TASKS (Single PR Each)

### Issue #11: Disable Registration Routes and Views
**Labels:** `authentication`, `automated`, `P0-critical`, `single-pr`

**Description:**
Remove registration functionality from Laravel Breeze as per PRD requirements.

**Tasks:**
- [ ] Remove registration routes from `routes/auth.php`
- [ ] Delete `resources/views/auth/register.blade.php`
- [ ] Remove registration links from login page
- [ ] Remove registration links from navigation layout

**Files to Modify:**
- `routes/auth.php`
- `resources/views/auth/login.blade.php` (if contains register link)
- `resources/views/layouts/guest.blade.php` (if contains register link)

**Files to Delete:**
- `resources/views/auth/register.blade.php`

**Acceptance Criteria:**
- No registration routes accessible
- No registration links visible
- Login page only shows login form
- Attempting to access `/register` returns 404

**Estimated Time:** 30 minutes

**Depends On:** Issue #2

---

### Issue #12: Implement Domains Migration Schema
**Labels:** `database`, `automated`, `P0-critical`, `single-pr`

**Description:**
Implement the domains table migration according to PRD specifications.

**Tasks:**
- [ ] Define all columns as per PRD Section 3.1
- [ ] Add indexes on `domain` and `status` columns
- [ ] Set proper nullable constraints
- [ ] Add timestamps

**Files to Modify:**
- `database/migrations/YYYY_MM_DD_XXXXXX_create_domains_table.php`

**Schema:**
```php
Schema::create('domains', function (Blueprint $table) {
    $table->id();
    $table->string('domain', 255);
    $table->string('registrar', 255)->nullable();
    $table->string('nameserver', 255)->nullable();
    $table->string('mx_records', 255)->nullable();
    $table->string('status', 100)->nullable();
    $table->date('expiration_date')->nullable();
    $table->text('task')->nullable();
    $table->text('remarks')->nullable();
    $table->timestamps();

    $table->index('domain');
    $table->index('status');
});
```

**Acceptance Criteria:**
- Migration file matches PRD exactly
- Indexes defined correctly
- Ready to migrate

**Estimated Time:** 20 minutes

**Depends On:** Issue #5

---

### Issue #13: Implement Subdomains Migration Schema
**Labels:** `database`, `automated`, `P0-critical`, `single-pr`

**Description:**
Implement the subdomains table migration with foreign key cascade delete.

**Tasks:**
- [ ] Define all columns as per PRD Section 3.1
- [ ] Add foreign key constraint with `onDelete('cascade')`
- [ ] Add index on `status` column
- [ ] Set proper nullable constraints
- [ ] Add timestamps

**Files to Modify:**
- `database/migrations/YYYY_MM_DD_XXXXXX_create_subdomains_table.php`

**Schema:**
```php
Schema::create('subdomains', function (Blueprint $table) {
    $table->id();
    $table->string('subdomain', 255)->nullable();
    $table->unsignedBigInteger('domain_id');
    $table->string('dns_manager', 255)->nullable();
    $table->string('cloud_hosting', 255)->nullable();
    $table->string('project', 255)->nullable();
    $table->string('ip_address', 45)->nullable();
    $table->string('status', 100)->nullable();
    $table->text('task')->nullable();
    $table->text('remarks')->nullable();
    $table->timestamps();

    $table->foreign('domain_id')
          ->references('id')
          ->on('domains')
          ->onDelete('cascade');

    $table->index('status');
});
```

**Acceptance Criteria:**
- Migration file matches PRD exactly
- Foreign key constraint with cascade delete
- Ready to migrate

**Estimated Time:** 20 minutes

**Depends On:** Issue #5

---

### Issue #14: Create JSON Configuration Files
**Labels:** `configuration`, `automated`, `P0-critical`, `single-pr`

**Description:**
Create all JSON configuration files in `resources/data/` directory.

**Tasks:**
- [ ] Create directory: `resources/data/`
- [ ] Create `registrars.json` with 5 options
- [ ] Create `nameservers.json` with 4 options
- [ ] Create `mx_records.json` with 4 options
- [ ] Create `statuses.json` with 5 options
- [ ] Create `dns_managers.json` with 5 options
- [ ] Create `cloud_hostings.json` with 6 options
- [ ] Create `projects.json` with 6 options

**Files to Create:**
- `resources/data/registrars.json`
- `resources/data/nameservers.json`
- `resources/data/mx_records.json`
- `resources/data/statuses.json`
- `resources/data/dns_managers.json`
- `resources/data/cloud_hostings.json`
- `resources/data/projects.json`

**Content Reference:** PRD Section 4.5

**Acceptance Criteria:**
- All 7 JSON files created
- Valid JSON syntax
- Content matches PRD specifications

**Estimated Time:** 15 minutes

**Depends On:** Issue #1

---

### Issue #15: Create JSON Helper Function
**Labels:** `helpers`, `automated`, `P0-critical`, `single-pr`

**Description:**
Create a helper function to load JSON configuration files.

**Tasks:**
- [ ] Create file: `app/Helpers/JsonHelper.php`
- [ ] Implement `getJsonData($filename)` function
- [ ] Add error handling for missing files
- [ ] Return decoded array or empty array on error

**Files to Create:**
- `app/Helpers/JsonHelper.php`

**Implementation:**
```php
<?php

if (!function_exists('getJsonData')) {
    function getJsonData(string $filename): array
    {
        $path = resource_path("data/{$filename}");

        if (!file_exists($path)) {
            return [];
        }

        $content = file_get_contents($path);
        $data = json_decode($content, true);

        return $data ?? [];
    }
}
```

**Acceptance Criteria:**
- Helper function loads JSON files correctly
- Returns empty array for missing files
- No errors when loading valid JSON

**Estimated Time:** 15 minutes

**Depends On:** Issue #14

---

### Issue #16: Implement Domain Model with Relationships
**Labels:** `models`, `automated`, `P0-critical`, `single-pr`

**Description:**
Implement the Domain Eloquent model with fillable attributes, casts, and relationships.

**Tasks:**
- [ ] Define `$fillable` array with all domain columns
- [ ] Add date cast for `expiration_date`
- [ ] Implement `subdomains()` hasMany relationship
- [ ] Add PHPDoc blocks

**Files to Modify:**
- `app/Models/Domain.php`

**Implementation:**
```php
<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\Relations\HasMany;

class Domain extends Model
{
    protected $fillable = [
        'domain',
        'registrar',
        'nameserver',
        'mx_records',
        'status',
        'expiration_date',
        'task',
        'remarks',
    ];

    protected $casts = [
        'expiration_date' => 'date',
    ];

    public function subdomains(): HasMany
    {
        return $this->hasMany(Subdomain::class);
    }
}
```

**Acceptance Criteria:**
- All attributes fillable
- Date casting works
- Relationship defined correctly

**Estimated Time:** 15 minutes

**Depends On:** Issue #6

---

### Issue #17: Implement Subdomain Model with Relationships
**Labels:** `models`, `automated`, `P0-critical`, `single-pr`

**Description:**
Implement the Subdomain Eloquent model with fillable attributes and relationships.

**Tasks:**
- [ ] Define `$fillable` array with all subdomain columns
- [ ] Implement `domain()` belongsTo relationship
- [ ] Add PHPDoc blocks

**Files to Modify:**
- `app/Models/Subdomain.php`

**Implementation:**
```php
<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\Relations\BelongsTo;

class Subdomain extends Model
{
    protected $fillable = [
        'subdomain',
        'domain_id',
        'dns_manager',
        'cloud_hosting',
        'project',
        'ip_address',
        'status',
        'task',
        'remarks',
    ];

    public function domain(): BelongsTo
    {
        return $this->belongsTo(Domain::class);
    }
}
```

**Acceptance Criteria:**
- All attributes fillable
- Relationship defined correctly
- Foreign key works

**Estimated Time:** 15 minutes

**Depends On:** Issue #6

---

### Issue #18: Create Database Seeder
**Labels:** `database`, `automated`, `P0-critical`, `single-pr`

**Description:**
Implement database seeder to create admin user and sample data.

**Tasks:**
- [ ] Seed admin user with specified credentials
- [ ] Create 10 sample domains with realistic data
- [ ] Create 20 sample subdomains linked to domains
- [ ] Use JSON configuration values for dropdowns
- [ ] Include varied expiration dates

**Files to Modify:**
- `database/seeders/DatabaseSeeder.php`

**Admin Credentials:**
- Email: `admin@localhost`
- Password: `password@123`
- Name: `Administrator`

**Acceptance Criteria:**
- Admin user created correctly
- Sample data realistic and varied
- Foreign keys properly linked

**Estimated Time:** 45 minutes

**Depends On:** Issue #3, Issue #16, Issue #17

---

### Issue #19: Configure Web Routes
**Labels:** `routing`, `automated`, `P0-critical`, `single-pr`

**Description:**
Configure all web routes according to PRD specifications.

**Tasks:**
- [ ] Set root route to redirect to domains index (authenticated)
- [ ] Create authenticated route group
- [ ] Register resource routes for domains
- [ ] Register resource routes for subdomains
- [ ] Ensure auth middleware applied

**Files to Modify:**
- `routes/web.php`

**Implementation:**
```php
<?php

use App\Http\Controllers\DomainController;
use App\Http\Controllers\SubdomainController;
use Illuminate\Support\Facades\Route;

Route::get('/', function () {
    return redirect()->route('domains.index');
})->middleware('auth');

Route::middleware(['auth'])->group(function () {
    Route::resource('domains', DomainController::class);
    Route::resource('subdomains', SubdomainController::class);
});

require __DIR__.'/auth.php';
```

**Acceptance Criteria:**
- All routes properly registered
- Authentication middleware applied
- Root redirect works

**Estimated Time:** 15 minutes

**Depends On:** Issue #7

---

### Issue #20: Implement DomainController Index Method
**Labels:** `controllers`, `automated`, `P0-critical`, `single-pr`

**Description:**
Implement the index method with search, sort, and pagination functionality.

**Tasks:**
- [ ] Implement search across domain, registrar, nameserver, status
- [ ] Implement sorting with column whitelist
- [ ] Default sort: id DESC
- [ ] Pagination: 100 per page
- [ ] Eager load subdomain count
- [ ] Pass data to view

**Files to Modify:**
- `app/Http/Controllers/DomainController.php` (index method only)

**Acceptance Criteria:**
- Search works across multiple columns
- Sorting works with proper validation
- Pagination shows 100 items
- No N+1 query issues

**Estimated Time:** 1 hour

**Depends On:** Issue #7, Issue #16

---

### Issue #21: Implement DomainController Create and Store Methods
**Labels:** `controllers`, `automated`, `P0-critical`, `single-pr`

**Description:**
Implement create form display and store functionality for domains.

**Tasks:**
- [ ] Load JSON data for all dropdowns
- [ ] Pass data to create view
- [ ] Implement store method with mass assignment
- [ ] Redirect to index with success message
- [ ] Handle date field properly

**Files to Modify:**
- `app/Http/Controllers/DomainController.php` (create and store methods)

**Acceptance Criteria:**
- Create form loads with dropdowns populated
- Store creates domain successfully
- Success flash message displays
- Redirects to index page

**Estimated Time:** 45 minutes

**Depends On:** Issue #7, Issue #15, Issue #16

---

### Issue #22: Implement DomainController Edit and Update Methods
**Labels:** `controllers`, `automated`, `P0-critical`, `single-pr`

**Description:**
Implement edit form display and update functionality for domains.

**Tasks:**
- [ ] Load domain by ID
- [ ] Load JSON data for dropdowns
- [ ] Pass data to edit view
- [ ] Implement update method
- [ ] Redirect to index with success message

**Files to Modify:**
- `app/Http/Controllers/DomainController.php` (edit and update methods)

**Acceptance Criteria:**
- Edit form loads with pre-populated data
- Update saves changes successfully
- Success flash message displays
- Redirects to index page

**Estimated Time:** 45 minutes

**Depends On:** Issue #7, Issue #15, Issue #16

---

### Issue #23: Implement DomainController Destroy Method
**Labels:** `controllers`, `automated`, `P0-critical`, `single-pr`

**Description:**
Implement delete functionality with cascade delete of subdomains.

**Tasks:**
- [ ] Delete domain by ID
- [ ] Verify cascade delete works (subdomains deleted automatically)
- [ ] Redirect to index with success message
- [ ] Handle deletion errors gracefully

**Files to Modify:**
- `app/Http/Controllers/DomainController.php` (destroy method only)

**Acceptance Criteria:**
- Domain deletes successfully
- Associated subdomains deleted automatically
- Success flash message displays
- No orphaned records

**Estimated Time:** 20 minutes

**Depends On:** Issue #7, Issue #16

---

### Issue #24: Implement SubdomainController Index Method
**Labels:** `controllers`, `automated`, `P0-critical`, `single-pr`

**Description:**
Implement the index method with search, sort, and pagination functionality.

**Tasks:**
- [ ] Implement search across subdomain, domain name, dns_manager, cloud_hosting, project, status
- [ ] Join domains table for domain name search
- [ ] Implement sorting with column whitelist
- [ ] Default sort: id DESC
- [ ] Pagination: 100 per page
- [ ] Eager load domain relationship
- [ ] Pass data to view

**Files to Modify:**
- `app/Http/Controllers/SubdomainController.php` (index method only)

**Acceptance Criteria:**
- Search works including domain name
- Sorting works properly
- Pagination shows 100 items
- Domain relationship loaded

**Estimated Time:** 1 hour

**Depends On:** Issue #7, Issue #17

---

### Issue #25: Implement SubdomainController Create and Store Methods
**Labels:** `controllers`, `automated`, `P0-critical`, `single-pr`

**Description:**
Implement create form display and store functionality for subdomains.

**Tasks:**
- [ ] Load all domains for dropdown (sorted alphabetically)
- [ ] Load JSON data for other dropdowns
- [ ] Pass data to create view
- [ ] Implement store method with mass assignment
- [ ] Redirect to index with success message

**Files to Modify:**
- `app/Http/Controllers/SubdomainController.php` (create and store methods)

**Acceptance Criteria:**
- Create form loads with all dropdowns populated
- Domain dropdown shows all domains
- Store creates subdomain successfully
- Success flash message displays

**Estimated Time:** 45 minutes

**Depends On:** Issue #7, Issue #15, Issue #16, Issue #17

---

### Issue #26: Implement SubdomainController Edit and Update Methods
**Labels:** `controllers`, `automated`, `P0-critical`, `single-pr`

**Description:**
Implement edit form display and update functionality for subdomains.

**Tasks:**
- [ ] Load subdomain by ID with domain relationship
- [ ] Load all domains for dropdown
- [ ] Load JSON data for dropdowns
- [ ] Pass data to edit view
- [ ] Implement update method
- [ ] Redirect to index with success message

**Files to Modify:**
- `app/Http/Controllers/SubdomainController.php` (edit and update methods)

**Acceptance Criteria:**
- Edit form loads with pre-populated data
- Current domain pre-selected in dropdown
- Update saves changes successfully
- Success flash message displays

**Estimated Time:** 45 minutes

**Depends On:** Issue #7, Issue #15, Issue #16, Issue #17

---

### Issue #27: Implement SubdomainController Destroy Method
**Labels:** `controllers`, `automated`, `P0-critical`, `single-pr`

**Description:**
Implement delete functionality for subdomains.

**Tasks:**
- [ ] Delete subdomain by ID
- [ ] Redirect to index with success message
- [ ] Handle deletion errors gracefully

**Files to Modify:**
- `app/Http/Controllers/SubdomainController.php` (destroy method only)

**Acceptance Criteria:**
- Subdomain deletes successfully
- Success flash message displays
- No errors

**Estimated Time:** 15 minutes

**Depends On:** Issue #7, Issue #17

---

### Issue #28: Update Application Layout with Navigation
**Labels:** `views`, `automated`, `P0-critical`, `single-pr`

**Description:**
Update the main layout to include navigation links for Domains and Subdomains.

**Tasks:**
- [ ] Add "Domains" link to navbar
- [ ] Add "Subdomains" link to navbar
- [ ] Ensure user dropdown with logout exists
- [ ] Style with Tailwind CSS (Breeze defaults)
- [ ] Make responsive for mobile

**Files to Modify:**
- `resources/views/layouts/app.blade.php`

**Acceptance Criteria:**
- Navigation links visible when authenticated
- Links navigate correctly
- Responsive on mobile
- Breeze styling maintained

**Estimated Time:** 30 minutes

**Depends On:** Issue #2

---

### Issue #29: Create Domains Index View
**Labels:** `views`, `automated`, `P0-critical`, `single-pr`

**Description:**
Create the domains index page with table, search, sort, and pagination.

**Tasks:**
- [ ] Create page title and "Add New Domain" button
- [ ] Implement search form
- [ ] Create data table with all columns (PRD Section 7.1.3)
- [ ] Implement sortable column headers
- [ ] Format expiration date as M-d-Y
- [ ] Truncate long text fields (task, remarks)
- [ ] Add Edit and Delete buttons per row
- [ ] Implement pagination links
- [ ] Handle empty state

**Files to Create:**
- `resources/views/domains/index.blade.php`

**Acceptance Criteria:**
- All columns display correctly
- Sorting indicators show
- Date format correct
- Pagination works
- Empty state handled

**Estimated Time:** 2 hours

**Depends On:** Issue #20, Issue #28

---

### Issue #30: Create Domains Create View
**Labels:** `views`, `automated`, `P0-critical`, `single-pr`

**Description:**
Create the domain creation form with all fields and dropdowns.

**Tasks:**
- [ ] Create form with all fields (PRD Section 7.2.2)
- [ ] Populate dropdowns from passed JSON data
- [ ] Use HTML5 date input for expiration date
- [ ] Add "Create Domain" submit button
- [ ] Add "Cancel" button linking to index
- [ ] Use Breeze default styling

**Files to Create:**
- `resources/views/domains/create.blade.php`

**Acceptance Criteria:**
- All fields render correctly
- Dropdowns populated
- Date picker works
- Form submits to store route
- Cancel returns to index

**Estimated Time:** 1.5 hours

**Depends On:** Issue #21, Issue #28

---

### Issue #31: Create Domains Edit View
**Labels:** `views`, `automated`, `P0-critical`, `single-pr`

**Description:**
Create the domain edit form with pre-populated data.

**Tasks:**
- [ ] Create form identical to create view
- [ ] Pre-populate all fields with domain data
- [ ] Use `@method('PUT')` for update route
- [ ] Change submit button to "Update Domain"
- [ ] Ensure date displays in correct format

**Files to Create:**
- `resources/views/domains/edit.blade.php`

**Acceptance Criteria:**
- All fields pre-populated
- Dropdowns show selected values
- Date field shows existing date
- Form submits to update route
- Cancel returns to index

**Estimated Time:** 1 hour

**Depends On:** Issue #22, Issue #28

---

### Issue #32: Create Reusable Delete Modal Component
**Labels:** `views`, `components`, `automated`, `P0-critical`, `single-pr`

**Description:**
Create a reusable modal component for delete confirmations.

**Tasks:**
- [ ] Create modal component with Tailwind styling
- [ ] Accept props: title, message, subdomain count (optional), form action
- [ ] Display subdomain warning when count > 0
- [ ] Include Cancel and Delete buttons
- [ ] Add modal open/close functionality (Alpine.js or vanilla JS)
- [ ] Style Delete button as danger/destructive

**Files to Create:**
- `resources/views/components/delete-modal.blade.php`

**Acceptance Criteria:**
- Modal displays centered
- Backdrop dims background
- Shows subdomain count for domains
- Cancel closes modal
- Delete submits form
- Accessible with keyboard

**Estimated Time:** 1.5 hours

**Depends On:** Issue #2

---

### Issue #33: Integrate Delete Modal into Domains Index
**Labels:** `views`, `automated`, `P0-critical`, `single-pr`

**Description:**
Add delete modal functionality to domains index page.

**Tasks:**
- [ ] Add modal trigger to Delete buttons
- [ ] Pass subdomain count to modal
- [ ] Display appropriate warning message
- [ ] Ensure modal submits DELETE request to destroy route

**Files to Modify:**
- `resources/views/domains/index.blade.php`

**Acceptance Criteria:**
- Delete button opens modal
- Modal shows correct subdomain count
- Delete action works correctly
- No orphaned records after delete

**Estimated Time:** 30 minutes

**Depends On:** Issue #29, Issue #32

---

### Issue #34: Create Subdomains Index View
**Labels:** `views`, `automated`, `P0-critical`, `single-pr`

**Description:**
Create the subdomains index page with table, search, sort, and pagination.

**Tasks:**
- [ ] Create page title and "Add New Subdomain" button
- [ ] Implement search form
- [ ] Create data table with all columns (PRD Section 8.1.3)
- [ ] Display subdomain prefix and domain name separately
- [ ] Handle empty subdomain (show as "@" or "root")
- [ ] Implement sortable column headers
- [ ] Truncate long text fields
- [ ] Add Edit and Delete buttons
- [ ] Implement pagination
- [ ] Handle empty state

**Files to Create:**
- `resources/views/subdomains/index.blade.php`

**Acceptance Criteria:**
- All columns display correctly
- Domain relationship shows
- Empty subdomain handled
- Sorting works
- Pagination works

**Estimated Time:** 2 hours

**Depends On:** Issue #24, Issue #28

---

### Issue #35: Create Subdomains Create View
**Labels:** `views`, `automated`, `P0-critical`, `single-pr`

**Description:**
Create the subdomain creation form with domain dropdown.

**Tasks:**
- [ ] Create form with all fields (PRD Section 8.2.2)
- [ ] Create domain dropdown populated from database
- [ ] Populate other dropdowns from JSON data
- [ ] Add placeholder for subdomain field: "e.g., mail, www (leave empty for root)"
- [ ] Add "Create Subdomain" submit button
- [ ] Add "Cancel" button linking to index
- [ ] Use Breeze default styling

**Files to Create:**
- `resources/views/subdomains/create.blade.php`

**Acceptance Criteria:**
- All fields render correctly
- Domain dropdown populated from database
- Other dropdowns from JSON
- Form submits to store route
- Cancel returns to index

**Estimated Time:** 1.5 hours

**Depends On:** Issue #25, Issue #28

---

### Issue #36: Create Subdomains Edit View
**Labels:** `views`, `automated`, `P0-critical`, `single-pr`

**Description:**
Create the subdomain edit form with pre-populated data.

**Tasks:**
- [ ] Create form identical to create view
- [ ] Pre-populate all fields with subdomain data
- [ ] Pre-select current domain in dropdown
- [ ] Use `@method('PUT')` for update route
- [ ] Change submit button to "Update Subdomain"

**Files to Create:**
- `resources/views/subdomains/edit.blade.php`

**Acceptance Criteria:**
- All fields pre-populated
- Current domain selected in dropdown
- Form submits to update route
- Cancel returns to index

**Estimated Time:** 1 hour

**Depends On:** Issue #26, Issue #28

---

### Issue #37: Integrate Delete Modal into Subdomains Index
**Labels:** `views`, `automated`, `P0-critical`, `single-pr`

**Description:**
Add delete modal functionality to subdomains index page (simpler than domains).

**Tasks:**
- [ ] Add modal trigger to Delete buttons
- [ ] No subdomain count needed (no cascade)
- [ ] Display simple confirmation message
- [ ] Ensure modal submits DELETE request to destroy route

**Files to Modify:**
- `resources/views/subdomains/index.blade.php`

**Acceptance Criteria:**
- Delete button opens modal
- Modal shows confirmation
- Delete action works correctly

**Estimated Time:** 20 minutes

**Depends On:** Issue #34, Issue #32

---

### Issue #38: Implement Flash Messages in Layout
**Labels:** `views`, `automated`, `P1-high`, `single-pr`

**Description:**
Add flash message display to application layout for success/error messages.

**Tasks:**
- [ ] Add flash message section to layout
- [ ] Style success messages (green, Tailwind)
- [ ] Style error messages (red, Tailwind)
- [ ] Add auto-dismiss after 5 seconds (optional JavaScript)
- [ ] Make responsive

**Files to Modify:**
- `resources/views/layouts/app.blade.php`

**Acceptance Criteria:**
- Success messages display correctly
- Error messages display correctly
- Messages dismiss automatically or manually
- Accessible

**Estimated Time:** 45 minutes

**Depends On:** Issue #28

---

### Issue #39: Add Table Styling Enhancements
**Labels:** `views`, `styling`, `automated`, `P1-high`, `single-pr`

**Description:**
Enhance table appearance with hover effects and sort indicators.

**Tasks:**
- [ ] Add hover effects to table rows
- [ ] Style sort indicators (up/down arrows)
- [ ] Highlight active sort column
- [ ] Add alternating row colors (optional)
- [ ] Ensure responsive on mobile

**Files to Modify:**
- `resources/views/domains/index.blade.php`
- `resources/views/subdomains/index.blade.php`

**Acceptance Criteria:**
- Hover effects work smoothly
- Sort indicators visible and clear
- Active column highlighted
- Responsive design

**Estimated Time:** 1 hour

**Depends On:** Issue #29, Issue #34

---

### Issue #40: Create Feature Tests for Domains
**Labels:** `testing`, `automated`, `P1-high`, `single-pr`

**Description:**
Create comprehensive feature tests for domain CRUD operations.

**Tasks:**
- [ ] Test domain creation
- [ ] Test domain reading/index
- [ ] Test domain update
- [ ] Test domain deletion
- [ ] Test cascade delete of subdomains
- [ ] Test search functionality
- [ ] Test sorting functionality
- [ ] Test authentication requirement

**Files to Create:**
- `tests/Feature/DomainTest.php`

**Acceptance Criteria:**
- All tests pass
- Full CRUD coverage
- Edge cases tested
- Authentication tested

**Estimated Time:** 2 hours

**Depends On:** All domain features completed

---

### Issue #41: Create Feature Tests for Subdomains
**Labels:** `testing`, `automated`, `P1-high`, `single-pr`

**Description:**
Create comprehensive feature tests for subdomain CRUD operations.

**Tasks:**
- [ ] Test subdomain creation
- [ ] Test subdomain reading/index
- [ ] Test subdomain update
- [ ] Test subdomain deletion
- [ ] Test domain relationship
- [ ] Test search functionality
- [ ] Test sorting functionality
- [ ] Test authentication requirement

**Files to Create:**
- `tests/Feature/SubdomainTest.php`

**Acceptance Criteria:**
- All tests pass
- Full CRUD coverage
- Relationship tested
- Authentication tested

**Estimated Time:** 2 hours

**Depends On:** All subdomain features completed

---

### Issue #42: Create Feature Tests for Authentication
**Labels:** `testing`, `automated`, `P1-high`, `single-pr`

**Description:**
Create tests to verify authentication and registration restrictions.

**Tasks:**
- [ ] Test login functionality
- [ ] Test logout functionality
- [ ] Test protected routes require authentication
- [ ] Test registration routes are disabled
- [ ] Test unauthenticated redirect to login

**Files to Create:**
- `tests/Feature/AuthTest.php`

**Acceptance Criteria:**
- All auth tests pass
- Registration disabled confirmed
- Protected routes tested

**Estimated Time:** 1 hour

**Depends On:** Issue #11

---

### Issue #43: Create Unit Tests for Models
**Labels:** `testing`, `automated`, `P1-high`, `single-pr`

**Description:**
Create unit tests for model relationships and functionality.

**Tasks:**
- [ ] Test Domain model relationships
- [ ] Test Subdomain model relationships
- [ ] Test date casting on Domain model
- [ ] Test fillable attributes

**Files to Create:**
- `tests/Unit/DomainModelTest.php`
- `tests/Unit/SubdomainModelTest.php`

**Acceptance Criteria:**
- All tests pass
- Relationships verified
- Casts verified

**Estimated Time:** 1 hour

**Depends On:** Issue #16, Issue #17

---

### Issue #44: Create Unit Tests for JSON Helper
**Labels:** `testing`, `automated`, `P1-high`, `single-pr`

**Description:**
Create unit tests for the JSON helper function.

**Tasks:**
- [ ] Test successful JSON loading
- [ ] Test invalid filename handling
- [ ] Test malformed JSON handling
- [ ] Test empty array return on error

**Files to Create:**
- `tests/Unit/JsonHelperTest.php`

**Acceptance Criteria:**
- All tests pass
- Error handling verified
- Edge cases tested

**Estimated Time:** 30 minutes

**Depends On:** Issue #15

---

### Issue #45: Create README Documentation
**Labels:** `documentation`, `automated`, `P2-medium`, `single-pr`

**Description:**
Create comprehensive README.md with setup and usage instructions.

**Tasks:**
- [ ] Write project description
- [ ] Document system requirements
- [ ] Provide installation instructions
- [ ] Document database setup
- [ ] Document seeding instructions
- [ ] List default login credentials
- [ ] Add feature list
- [ ] Add troubleshooting section

**Files to Create:**
- `README.md`

**Acceptance Criteria:**
- Clear and comprehensive
- Easy to follow
- Covers all setup steps
- Includes credentials

**Estimated Time:** 1.5 hours

**Depends On:** None (can be done anytime)

---

### Issue #46: Add Query Optimization and Performance Checks
**Labels:** `optimization`, `automated`, `P2-medium`, `single-pr`

**Description:**
Review and optimize database queries for performance.

**Tasks:**
- [ ] Review all controller queries for N+1 issues
- [ ] Ensure eager loading used where appropriate
- [ ] Add missing indexes if needed
- [ ] Test performance with 1000+ records
- [ ] Document any performance considerations

**Files to Review:**
- `app/Http/Controllers/DomainController.php`
- `app/Http/Controllers/SubdomainController.php`

**Acceptance Criteria:**
- No N+1 queries
- Proper eager loading
- Fast response times
- Indexes optimized

**Estimated Time:** 1.5 hours

**Depends On:** All controller issues completed

---

## ISSUE DEPENDENCY GRAPH

```
Phase 1 - Setup (Manual):
├─ #1: Environment Setup
└─ #2: Install Breeze
    └─ #11: Disable Registration (Auto)

Phase 2 - Migrations (Manual + Auto):
├─ #5: Create Migration Files (Manual)
│   ├─ #12: Implement Domains Migration (Auto)
│   └─ #13: Implement Subdomains Migration (Auto)
└─ #3: Run Migrations (Manual)

Phase 3 - Configuration (Auto):
├─ #14: Create JSON Files (Auto)
│   └─ #15: Create JSON Helper (Auto)
│       └─ #9: Update Composer Autoload (Manual)

Phase 4 - Models (Manual + Auto):
├─ #6: Create Model Files (Manual)
│   ├─ #16: Implement Domain Model (Auto)
│   └─ #17: Implement Subdomain Model (Auto)
└─ #18: Create Seeder (Auto)
    └─ #4: Run Seeder (Manual)

Phase 5 - Controllers (Manual + Auto):
├─ #7: Create Controller Files (Manual)
│   ├─ #20: DomainController Index (Auto)
│   ├─ #21: DomainController Create/Store (Auto)
│   ├─ #22: DomainController Edit/Update (Auto)
│   ├─ #23: DomainController Destroy (Auto)
│   ├─ #24: SubdomainController Index (Auto)
│   ├─ #25: SubdomainController Create/Store (Auto)
│   ├─ #26: SubdomainController Edit/Update (Auto)
│   └─ #27: SubdomainController Destroy (Auto)
└─ #19: Configure Routes (Auto)
    └─ #8: Verify Routes (Manual)

Phase 6 - Views (Auto):
├─ #28: Update Layout (Auto)
├─ #32: Create Delete Modal (Auto)
├─ #38: Implement Flash Messages (Auto)
├─ Domains Views:
│   ├─ #29: Domains Index (Auto)
│   │   └─ #33: Integrate Delete Modal (Auto)
│   ├─ #30: Domains Create (Auto)
│   └─ #31: Domains Edit (Auto)
└─ Subdomains Views:
    ├─ #34: Subdomains Index (Auto)
    │   └─ #37: Integrate Delete Modal (Auto)
    ├─ #35: Subdomains Create (Auto)
    └─ #36: Subdomains Edit (Auto)

Phase 7 - Enhancement (Auto):
└─ #39: Table Styling (Auto)

Phase 8 - Testing (Manual + Auto):
├─ #40: Domain Feature Tests (Auto)
├─ #41: Subdomain Feature Tests (Auto)
├─ #42: Auth Feature Tests (Auto)
├─ #43: Model Unit Tests (Auto)
├─ #44: Helper Unit Tests (Auto)
└─ #10: Run Tests (Manual)

Phase 9 - Optimization & Docs (Auto):
├─ #46: Query Optimization (Auto)
└─ #45: README Documentation (Auto)
```

---

## EXECUTION SUMMARY

### Manual Tasks (11 total)
Issues requiring terminal commands or local environment actions:
- #1, #2, #3, #4, #5, #6, #7, #8, #9, #10

### Automated Tasks (36 total)
Issues that can be completed by a coding agent:
- #11-#46

### Total Issues: 47

### Estimated Total Time:
- **Manual Tasks:** ~2-3 hours
- **Automated Tasks:** ~35-40 hours
- **Total:** ~37-43 hours

---

## RECOMMENDED EXECUTION ORDER

1. **Start with Manual Setup** (#1, #2)
2. **Create Structure** (#5, #6, #7)
3. **Implement Automated Code in Batches:**
   - Database layer (#12, #13, #14, #15, #16, #17, #18)
   - Routing (#11, #19)
   - Domain features (#20-#23, #29-#31, #32, #33, #38)
   - Subdomain features (#24-#27, #34-#37)
   - Testing (#40-#44)
   - Polish (#39, #45, #46)
4. **Run Manual Validation** (#3, #4, #8, #9, #10)

Each automated task is designed as a single, mergeable PR that won't conflict with parallel development!