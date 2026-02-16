# Product Requirements Document

## Domain Management System

**Version:** 1.0
**Date:** November 19, 2025
**Target Framework:** Laravel 12.x
**Database:** SQLite
**PHP Version:** 8.2+

------

## 1. Executive Summary

A single-user Laravel application for managing a personal database of owned domains and their associated subdomains. The system tracks domain registrar information, nameserver configurations, expiration dates, and subdomain deployment details including cloud hosting providers and IP addresses.

------

## 2. Technical Stack

### 2.1 Core Technologies

- **Framework:** Laravel 12.x (latest stable)
- **PHP Version:** 8.2 or higher
- **Database:** SQLite (single-file database)
- **Local Development:** Laravel Herd
- **Authentication:** Laravel Breeze (or manual implementation)
- **Frontend:** 100% Laravel Blade templates with default styling
- **CSS Framework:** Laravel's default embedded CSS (Tailwind CSS via Breeze)

### 2.2 Technical Constraints

- No external frontend frameworks (Vue, React, Alpine.js beyond Breeze defaults)
- No additional CSS frameworks or custom styling
- Use only Laravel's out-of-the-box frontend libraries
- All configuration data stored in JSON files at `resources/data/`

------

## 3. Data Model

### 3.1 Database Schema

#### Domains Table

```
Table: domains
- id: bigInteger, primary key, auto-increment
- domain: string (255), nullable: false
- registrar: string (255), nullable: true
- nameserver: string (255), nullable: true
- mx_records: string (255), nullable: true
- status: string (100), nullable: true
- expiration_date: date, nullable: true
- task: text, nullable: true
- remarks: text, nullable: true
- timestamps (created_at, updated_at)
```

#### Subdomains Table

```
Table: subdomains
- id: bigInteger, primary key, auto-increment
- subdomain: string (255), nullable: true
  // Stores prefix only (e.g., "mail", "portal", "beta")
  // Empty/null for root domain monitoring (@)
- domain_id: unsignedBigInteger, foreign key -> domains.id
- dns_manager: string (255), nullable: true
- cloud_hosting: string (255), nullable: true
- project: string (255), nullable: true
- ip_address: string (45), nullable: true
- status: string (100), nullable: true
- task: text, nullable: true
- remarks: text, nullable: true
- timestamps (created_at, updated_at)
```

#### Users Table

```
Table: users (Laravel default migration)
- id: bigInteger, primary key, auto-increment
- name: string
- email: string, unique
- email_verified_at: timestamp, nullable
- password: string
- remember_token: string, nullable
- timestamps (created_at, updated_at)
```

### 3.2 Eloquent Model Relationships

#### Domain Model

```php
class Domain extends Model
{
    // Relationship
    public function subdomains(): HasMany
    {
        return $this->hasMany(Subdomain::class);
    }
}
```

#### Subdomain Model

```php
class Subdomain extends Model
{
    // Relationship
    public function domain(): BelongsTo
    {
        return $this->belongsTo(Domain::class);
    }
}
```

### 3.3 Cascade Delete Behavior

- When a domain is deleted, all associated subdomains MUST be automatically deleted
- Implement via foreign key constraint: `onDelete('cascade')`
- Delete confirmation modal MUST display the count of subdomains that will be deleted

------

## 4. JSON Configuration Files

### 4.1 File Location

All JSON configuration files MUST be stored in: `resources/data/`

### 4.2 File Naming Convention

Use table column names (plural form):

- `registrars.json`
- `nameservers.json`
- `mx_records.json`
- `statuses.json`
- `dns_managers.json`
- `cloud_hostings.json`
- `projects.json`

### 4.3 JSON Structure

Simple string arrays:

```json
[
  "Namecheap",
  "GoDaddy",
  "Cloudflare",
  "Google Domains"
]
```

### 4.4 Usage Pattern

- Load JSON data in controllers or form requests
- Populate dropdown/select elements in Blade views
- No database storage of list-of-values data

### 4.5 Initial JSON File Contents

**registrars.json:**

```json
["Namecheap", "GoDaddy", "Cloudflare", "Google Domains", "Name.com"]
```

**nameservers.json:**

```json
["Cloudflare", "Route53", "Google Cloud DNS", "Custom"]
```

**mx_records.json:**

```json
["Google Workspace", "Microsoft 365", "Cloudflare Email", "Custom"]
```

**statuses.json:**

```json
["Active", "Inactive", "Pending", "Expired", "Suspended"]
```

**dns_managers.json:**

```json
["Cloudflare", "Route53", "cPanel", "Plesk", "Custom"]
```

**cloud_hostings.json:**

```json
["AWS", "Google Cloud", "DigitalOcean", "Linode", "Vercel", "Netlify"]
```

**projects.json:**

```json
["Personal Website", "E-commerce", "Blog", "API", "Portfolio", "Other"]
```

------

## 5. Authentication & Authorization

### 5.1 Authentication Requirements

- **Type:** Single-user authentication
- **Implementation:** Laravel Breeze (Blade stack) or manual auth implementation
- **Protected Routes:** ALL application routes require authentication
- **Login Page:** Standard Laravel Breeze login page
- **No Registration:** Registration routes and pages MUST be disabled/removed

### 5.2 User Management

- **User Creation:** Direct database seeding only (no UI)
- **Seeded User Credentials:**
  - Email: `admin@localhost`
  - Password: `password@123`
  - Name: `Administrator`
- **Password Reset:** NOT required (local application)
- **Email Verification:** NOT required

### 5.3 Access Control

- No role-based access control (RBAC)
- Single authenticated user has full CRUD access to all features
- No permission or policy checks required

### 5.4 Session Management

- Standard Laravel session handling
- Remember me functionality (optional)
- Logout functionality in navbar

------

## 6. User Interface Structure

### 6.1 Navigation Layout

- **Type:** Top horizontal navbar
- **Authenticated Navigation Items:**
  - "Domains" (links to domains index)
  - "Subdomains" (links to subdomains index)
  - User dropdown with "Logout" option
- **No Dashboard:** Direct navigation between list pages

### 6.2 Page Structure

All authenticated pages MUST include:

- Top navbar with navigation links
- Page title/heading
- Main content area
- Use Laravel Breeze default layouts

------

## 7. Domains Module

### 7.1 Domains Index Page (`/domains`)

#### 7.1.1 URL & Route

- URL: `/domains`
- Route Name: `domains.index`
- Method: GET
- Middleware: `auth`

#### 7.1.2 Page Components

- **Page Title:** "Domains"
- **Action Button:** "Add New Domain" (links to create page)
- **Search/Filter:**
  - Single search input field
  - Searches across: domain, registrar, nameserver, status
  - Real-time or submit button search
- **Data Table:**
  - All columns displayed
  - Sortable columns (click header to sort ascending/descending)
  - Pagination: 100 records per page
  - Action column with Edit and Delete buttons

#### 7.1.3 Table Columns

1. ID
2. Domain
3. Registrar
4. Nameserver
5. MX Records
6. Status
7. Expiration Date (format: M-d-Y, e.g., "Nov-19-2025")
8. Task (truncate if long)
9. Remarks (truncate if long)
10. Actions (Edit | Delete buttons)

#### 7.1.4 Sorting Functionality

- Default sort: ID descending (newest first)
- Sortable columns: ID, Domain, Registrar, Status, Expiration Date
- Visual indicator for current sort column and direction

#### 7.1.5 Search/Filter Behavior

- Search term applied across multiple columns
- Case-insensitive search
- Maintains pagination during search

#### 7.1.6 Action Buttons

- **Edit Button:**
  - Links to edit page: `/domains/{id}/edit`
  - Style: Primary/default button
- **Delete Button:**
  - Triggers modal confirmation dialog
  - Style: Danger/destructive button

### 7.2 Create Domain Page (`/domains/create`)

#### 7.2.1 URL & Route

- URL: `/domains/create`
- Route Name: `domains.create`
- Method: GET (form display), POST (form submission to `domains.store`)
- Middleware: `auth`

#### 7.2.2 Form Fields

1. **Domain** (text input, required in migration but no frontend validation)
2. **Registrar** (dropdown/select, populated from `registrars.json`)
3. **Nameserver** (dropdown/select, populated from `nameservers.json`)
4. **MX Records** (dropdown/select, populated from `mx_records.json`)
5. **Status** (dropdown/select, populated from `statuses.json`)
6. **Expiration Date** (date picker, HTML5 date input, nullable, display format M-d-Y)
7. **Task** (textarea, nullable)
8. **Remarks** (textarea, nullable)

#### 7.2.3 Form Behavior

- **Validation:** NONE required (as per requirements)
- **Submit Button:** "Create Domain"
- **Cancel Button:** Returns to domains index
- **Success:** Redirect to domains index with success message
- **Date Picker:** HTML5 `<input type="date">` with nullable support

### 7.3 Edit Domain Page (`/domains/{id}/edit`)

#### 7.3.1 URL & Route

- URL: `/domains/{id}/edit`
- Route Name: `domains.edit`
- Method: GET (form display), PUT/PATCH (form submission to `domains.update`)
- Middleware: `auth`

#### 7.3.2 Form Fields

- Identical to Create form
- Pre-populated with existing domain data
- Same dropdown options from JSON files
- Date field pre-filled in M-d-Y format

#### 7.3.3 Form Behavior

- **Submit Button:** "Update Domain"
- **Cancel Button:** Returns to domains index
- **Success:** Redirect to domains index with success message

### 7.4 Delete Domain Functionality

#### 7.4.1 Delete Confirmation Modal

- **Trigger:** Click "Delete" button on index page
- **Modal Content:**
  - Title: "Delete Domain"
  - Message: "Are you sure you want to delete this domain?"
  - **Subdomain Warning:** "This will also delete X associated subdomain(s)." (where X is the count)
  - If zero subdomains: "This domain has no subdomains."
- **Modal Actions:**
  - "Cancel" button (closes modal)
  - "Delete" button (danger/destructive style, submits delete request)

#### 7.4.2 Delete Route

- Route Name: `domains.destroy`
- Method: DELETE
- URL: `/domains/{id}`
- Middleware: `auth`

#### 7.4.3 Delete Behavior

- Delete the domain record
- Cascade delete all associated subdomains (via foreign key constraint)
- Redirect to domains index with success message

------

## 8. Subdomains Module

### 8.1 Subdomains Index Page (`/subdomains`)

#### 8.1.1 URL & Route

- URL: `/subdomains`
- Route Name: `subdomains.index`
- Method: GET
- Middleware: `auth`

#### 8.1.2 Page Components

- **Page Title:** "Subdomains"
- **Action Button:** "Add New Subdomain" (links to create page)
- **Search/Filter:**
  - Single search input field
  - Searches across: subdomain, domain name, dns_manager, cloud_hosting, project, status
- **Data Table:**
  - All columns displayed
  - Sortable columns
  - Pagination: 100 records per page
  - Action column with Edit and Delete buttons

#### 8.1.3 Table Columns

1. ID
2. Subdomain (the prefix only, e.g., "mail", "www", or empty for root)
3. Domain (domain name only, e.g., "example.com")
4. DNS Manager
5. Cloud Hosting
6. Project
7. IP Address
8. Status
9. Task (truncate if long)
10. Remarks (truncate if long)
11. Actions (Edit | Delete buttons)

#### 8.1.4 Sorting Functionality

- Default sort: ID descending (newest first)
- Sortable columns: ID, Subdomain, Domain, DNS Manager, Cloud Hosting, Project, Status
- Visual indicator for current sort column and direction

#### 8.1.5 Action Buttons

- **Edit Button:** Links to `/subdomains/{id}/edit`
- **Delete Button:** Triggers modal confirmation (simpler than domains, no cascade warning)

### 8.2 Create Subdomain Page (`/subdomains/create`)

#### 8.2.1 URL & Route

- URL: `/subdomains/create`
- Route Name: `subdomains.create`
- Method: GET (form display), POST (form submission to `subdomains.store`)
- Middleware: `auth`

#### 8.2.2 Form Fields

1. **Subdomain** (text input, nullable - stores prefix only like "mail", "www")
2. **Domain** (dropdown/select, populated from active domains in database, required)
3. **DNS Manager** (dropdown/select, populated from `dns_managers.json`)
4. **Cloud Hosting** (dropdown/select, populated from `cloud_hostings.json`)
5. **Project** (dropdown/select, populated from `projects.json`)
6. **IP Address** (text input, nullable)
7. **Status** (dropdown/select, populated from `statuses.json`)
8. **Task** (textarea, nullable)
9. **Remarks** (textarea, nullable)

#### 8.2.3 Domain Dropdown Behavior

- Populate with all domains from `domains` table
- Display format: domain name (e.g., "example.com")
- Sort alphabetically
- Required selection (no empty/null option)

#### 8.2.4 Form Behavior

- **Validation:** NONE required
- **Submit Button:** "Create Subdomain"
- **Cancel Button:** Returns to subdomains index
- **Success:** Redirect to subdomains index with success message

### 8.3 Edit Subdomain Page (`/subdomains/{id}/edit`)

#### 8.3.1 URL & Route

- URL: `/subdomains/{id}/edit`
- Route Name: `subdomains.edit`
- Method: GET (form display), PUT/PATCH (form submission to `subdomains.update`)
- Middleware: `auth`

#### 8.3.2 Form Fields

- Identical to Create form
- Pre-populated with existing subdomain data
- Domain dropdown pre-selected to current domain

#### 8.3.3 Form Behavior

- **Submit Button:** "Update Subdomain"
- **Cancel Button:** Returns to subdomains index
- **Success:** Redirect to subdomains index with success message

### 8.4 Delete Subdomain Functionality

#### 8.4.1 Delete Confirmation Modal

- **Trigger:** Click "Delete" button on index page
- **Modal Content:**
  - Title: "Delete Subdomain"
  - Message: "Are you sure you want to delete this subdomain?"
  - No cascade warning (subdomains have no child records)
- **Modal Actions:**
  - "Cancel" button (closes modal)
  - "Delete" button (danger style, submits delete request)

#### 8.4.2 Delete Route

- Route Name: `subdomains.destroy`
- Method: DELETE
- URL: `/subdomains/{id}`
- Middleware: `auth`

#### 8.4.3 Delete Behavior

- Delete the subdomain record
- Redirect to subdomains index with success message

------

## 9. Technical Implementation Requirements

### 9.1 Database Migrations

- Create migrations in chronological order:
  1. Default Laravel users migration
  2. `create_domains_table` migration
  3. `create_subdomains_table` migration (with foreign key constraint)
- Foreign key constraint: `onDelete('cascade')` for subdomains.domain_id

### 9.2 Database Seeding

- Create `DatabaseSeeder` that seeds:
  - One user account with specified credentials
  - Optional: Sample domain and subdomain data for testing

### 9.3 Eloquent Models

- `User` model (Laravel default)
- `Domain` model with:
  - Fillable attributes
  - `subdomains()` relationship method
  - Casts for date field: `expiration_date` → `date`
- `Subdomain` model with:
  - Fillable attributes
  - `domain()` relationship method

### 9.4 Controllers

- `DomainController` (resource controller)
  - index, create, store, edit, update, destroy methods
  - Load JSON data for dropdowns
  - Eager load subdomain count for delete warnings
- `SubdomainController` (resource controller)
  - index, create, store, edit, update, destroy methods
  - Load JSON data for dropdowns
  - Load domains for dropdown

### 9.5 Routes

```php
// Authentication routes (Laravel Breeze)
require __DIR__.'/auth.php';

// Protected routes
Route::middleware(['auth'])->group(function () {
    Route::resource('domains', DomainController::class);
    Route::resource('subdomains', SubdomainController::class);
});

// Redirect root to domains
Route::get('/', function () {
    return redirect()->route('domains.index');
})->middleware('auth');
```

### 9.6 Blade Views Structure

```
resources/views/
├── layouts/
│   └── app.blade.php (main layout with navbar)
├── domains/
│   ├── index.blade.php
│   ├── create.blade.php
│   └── edit.blade.php
├── subdomains/
│   ├── index.blade.php
│   ├── create.blade.php
│   └── edit.blade.php
└── components/
    └── delete-modal.blade.php (reusable delete confirmation modal)
```

### 9.7 Pagination Configuration

- Set pagination to 100 items per page in controllers:

  ```php
  $domains = Domain::paginate(100);
  ```

- Use Laravel's default pagination views (Tailwind CSS)

### 9.8 Search/Filter Implementation

- Accept `search` query parameter

- Apply search using Eloquent `where()` with `LIKE` operator

- Example for domains:

  ```php
  $query = Domain::query();if ($search = request('search')) {    $query->where(function($q) use ($search) {        $q->where('domain', 'like', "%{$search}%")          ->orWhere('registrar', 'like', "%{$search}%")          ->orWhere('nameserver', 'like', "%{$search}%")          ->orWhere('status', 'like', "%{$search}%");    });}
  ```

### 9.9 Sorting Implementation

- Accept `sort` and `direction` query parameters

- Validate sort column against whitelist

- Default: `id` column, `desc` direction

- Example:

  ```php
  $sortColumn = request('sort', 'id');$sortDirection = request('direction', 'desc');$domains = Domain::orderBy($sortColumn, $sortDirection)->paginate(100);
  ```

### 9.10 Date Handling

- Database storage: SQLite DATE type (ISO 8601 format: YYYY-MM-DD)
- Eloquent cast: `'expiration_date' => 'date'`
- Display format: M-d-Y (e.g., "Nov-19-2025")
- Blade formatting: `{{ $domain->expiration_date?->format('M-d-Y') }}`
- Form input: HTML5 `<input type="date">` (browser native date picker)
- Allow null/empty values

### 9.11 JSON File Loading

- Create a helper or service class for loading JSON files

- Example helper function:

  ```php
  function getJsonData($filename) {    $path = resource_path("data/{$filename}");    return json_decode(file_get_contents($path), true);}
  ```

- Load in controllers and pass to views

------

## 10. User Experience Requirements

### 10.1 Success Messages

Display flash messages after successful operations:

- "Domain created successfully"
- "Domain updated successfully"
- "Domain deleted successfully"
- "Subdomain created successfully"
- "Subdomain updated successfully"
- "Subdomain deleted successfully"

### 10.2 Delete Modal UX

- Modal appears centered on screen
- Backdrop overlay dims background
- Modal includes clear warning text
- For domains: Display subdomain count
- Cancel button allows easy exit without action
- Delete button clearly indicates destructive action (red/danger styling)

### 10.3 Table UX

- Hover effects on table rows
- Sort indicators (up/down arrows) on sortable column headers
- Active sort column visually distinct
- Pagination controls at bottom of table
- "No records found" message when search returns no results
- Action buttons clearly separated and styled appropriately

### 10.4 Form UX

- Clear field labels
- Appropriate input types (text, textarea, select, date)
- Submit button at bottom of form
- Cancel button returns to previous page
- Use Laravel Breeze default form styling

------

## 11. Security Considerations

### 11.1 Authentication

- All application routes protected by `auth` middleware
- Redirect unauthenticated users to login page
- Disable registration routes
- Standard CSRF protection on all forms

### 11.2 Mass Assignment Protection

- Define `$fillable` or `$guarded` properties on all models
- Never use `Model::create($request->all())` without filtering

### 11.3 SQL Injection Prevention

- Use Eloquent ORM (parameterized queries)
- Never use raw SQL with user input without parameter binding

### 11.4 XSS Prevention

- Blade's `{{ }}` syntax auto-escapes output
- Use `{!! !!}` only when absolutely necessary and with sanitized data

------

## 12. Performance Considerations

### 12.1 Query Optimization

- Eager load relationships when displaying lists with related data
- Use `with()` to avoid N+1 query problems
- Example: `Domain::withCount('subdomains')->paginate(100)`

### 12.2 Database Indexes

- Add indexes on frequently searched columns:
  - `domains.domain`
  - `domains.status`
  - `subdomains.domain_id` (foreign key, auto-indexed)
  - `subdomains.status`

### 12.3 Pagination

- Always paginate large result sets (100 per page)
- Use Laravel's efficient cursor-based pagination if needed for very large datasets

------

## 13. Testing Requirements (Optional but Recommended)

### 13.1 Feature Tests

- Test CRUD operations for domains
- Test CRUD operations for subdomains
- Test cascade delete functionality
- Test authentication requirements

### 13.2 Unit Tests

- Test model relationships
- Test JSON file loading helper
- Test search/filter logic

------

## 14. Deployment & Environment

### 14.1 Local Development

- **Tool:** Laravel Herd (https://herd.laravel.com)

- **Database:** SQLite file stored in `database/database.sqlite`

- **Environment:** `.env` file configured for SQLite

  ```
  DB_CONNECTION=sqliteDB_DATABASE=/absolute/path/to/database/database.sqlite
  ```

### 14.2 Initial Setup Commands

```bash
# Create SQLite database file
touch database/database.sqlite

# Run migrations
php artisan migrate

# Run seeders
php artisan db:seed

# Install Breeze (if using)
composer require laravel/breeze --dev
php artisan breeze:install blade
npm install && npm run build
```

------

## 15. Out of Scope

The following features are explicitly NOT included in this version:

- User registration UI
- Password reset functionality
- Email verification
- User profile management
- Role-based access control
- API endpoints
- Multi-tenancy
- Domain/subdomain monitoring or automated status checks
- Export functionality (CSV, PDF)
- Import functionality
- Bulk operations
- Advanced reporting or analytics
- Mobile app
- Dark mode toggle
- Custom themes or branding
- Activity logs or audit trails
- Notifications
- Dashboard with statistics

------

## 16. Success Criteria

The application is considered complete when:

1. User can authenticate with seeded credentials
2. User can perform full CRUD on domains
3. User can perform full CRUD on subdomains
4. Dropdown fields populate from JSON files
5. Domain deletion cascades to subdomains with proper warning
6. Tables display with sorting, searching, and pagination (100/page)
7. Date picker works and displays dates in M-d-Y format
8. All pages require authentication
9. Application uses only Laravel default styling (no custom CSS)
10. All JSON files stored in `resources/data/` directory

------

## 17. Future Enhancements (Post-V1)

Potential features for future iterations:

- Dashboard with domain expiration alerts
- Email notifications for expiring domains
- CSV export of domains and subdomains
- Bulk import from CSV
- Domain status monitoring (ping, SSL check)
- DNS record verification
- Tags or categories for domains
- Advanced filtering (date ranges, multi-select)
- Activity history log

------

## Appendix A: Route List

```
GET     /                           → redirect to /domains (auth)
GET     /login                      → Auth::showLoginForm
POST    /login                      → Auth::login
POST    /logout                     → Auth::logout

GET     /domains                    → DomainController@index (auth)
GET     /domains/create             → DomainController@create (auth)
POST    /domains                    → DomainController@store (auth)
GET     /domains/{id}/edit          → DomainController@edit (auth)
PUT     /domains/{id}               → DomainController@update (auth)
DELETE  /domains/{id}               → DomainController@destroy (auth)

GET     /subdomains                 → SubdomainController@index (auth)
GET     /subdomains/create          → SubdomainController@create (auth)
POST    /subdomains                 → SubdomainController@store (auth)
GET     /subdomains/{id}/edit       → SubdomainController@edit (auth)
PUT     /subdomains/{id}            → SubdomainController@update (auth)
DELETE  /subdomains/{id}            → SubdomainController@destroy (auth)
```

------

## Appendix B: File Structure Overview

```
app/
├── Http/
│   └── Controllers/
│       ├── DomainController.php
│       └── SubdomainController.php
├── Models/
│   ├── User.php
│   ├── Domain.php
│   └── Subdomain.php
└── Helpers/
    └── JsonHelper.php (optional)

database/
├── migrations/
│   ├── 2014_10_12_000000_create_users_table.php
│   ├── 2025_XX_XX_000001_create_domains_table.php
│   └── 2025_XX_XX_000002_create_subdomains_table.php
├── seeders/
│   └── DatabaseSeeder.php
└── database.sqlite

resources/
├── data/
│   ├── registrars.json
│   ├── nameservers.json
│   ├── mx_records.json
│   ├── statuses.json
│   ├── dns_managers.json
│   ├── cloud_hostings.json
│   └── projects.json
└── views/
    ├── layouts/
    │   └── app.blade.php
    ├── domains/
    │   ├── index.blade.php
    │   ├── create.blade.php
    │   └── edit.blade.php
    ├── subdomains/
    │   ├── index.blade.php
    │   ├── create.blade.php
    │   └── edit.blade.php
    └── components/
        └── delete-modal.blade.php

routes/
└── web.php
```

------

**END OF DOCUMENT**