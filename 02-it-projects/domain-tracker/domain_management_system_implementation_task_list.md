# Domain Management System - Implementation Task List

## Overview

This document provides a complete task breakdown for implementing the Domain Management System. Each task is formatted as a GitHub issue with descriptions, acceptance criteria, and labels.

------

## Phase 1: Project Setup & Foundation

### Issue #1: Initialize Laravel Project and Configure Environment

**Labels**: `setup`, `phase-1`, `priority-high`

**Description:** Set up the Laravel project foundation and configure the development environment for Herd.app with SQLite database.

**Tasks:**

- [ ] Create new Laravel project (latest stable version)
- [ ] Configure `.env` file for SQLite database
- [ ] Set `DB_CONNECTION=sqlite` in `.env`
- [ ] Create SQLite database file in `database/database.sqlite`
- [ ] Test database connection
- [ ] Configure app name and URL for local development
- [ ] Verify application runs on Herd.app

**Acceptance Criteria:**

- Laravel project runs successfully on Herd.app
- SQLite database connection works
- Default Laravel welcome page displays

**Dependencies:** None

------

### Issue #2: Install and Configure Bootstrap 5

**Labels**: `frontend`, `setup`, `phase-1`, `priority-high`

**Description:** Download and integrate Bootstrap 5 (self-contained, no CDN) into the Laravel application.

**Tasks:**

- [ ] Download Bootstrap 5 CSS and JS files
- [ ] Create `public/assets/css/` directory
- [ ] Create `public/assets/js/` directory
- [ ] Place Bootstrap CSS in `public/assets/css/bootstrap.min.css`
- [ ] Place Bootstrap JS in `public/assets/js/bootstrap.bundle.min.js`
- [ ] Download Bootstrap Icons (optional but recommended)
- [ ] Verify all files are self-contained (no CDN dependencies)

**Acceptance Criteria:**

- Bootstrap files are available locally in public directory
- No external CDN links in the application
- Bootstrap styles and JavaScript work offline

**Dependencies:** Issue #1

------

### Issue #3: Set Up Authentication System

**Labels**: `authentication`, `setup`, `phase-1`, `priority-high`

**Description:** Implement single-user authentication system using Laravel's built-in authentication or Laravel Breeze.

**Tasks:**

- [ ] Install Laravel Breeze (or implement manual auth)
- [ ] Run authentication scaffolding
- [ ] Remove registration routes and views
- [ ] Remove password reset routes and views
- [ ] Keep only login and logout functionality
- [ ] Protect all routes with `auth` middleware (except login)
- [ ] Configure redirect after login to `/domains`
- [ ] Configure redirect after logout to `/login`

**Acceptance Criteria:**

- User can log in with credentials
- Only login and logout pages are accessible
- No registration or password reset pages exist
- Authenticated users redirect to `/domains`
- Unauthenticated users redirect to `/login`

**Dependencies:** Issue #1

------

### Issue #4: Create Base Layout with Dark Navbar

**Labels**: `frontend`, `layout`, `phase-1`, `priority-high`

**Description:** Create the master Blade layout with Bootstrap 5 dark navbar and main content structure.

**Tasks:**

- [ ] Create `resources/views/layouts/app.blade.php`
- [ ] Include Bootstrap CSS and JS in layout
- [ ] Create dark navbar (`navbar-dark bg-dark`)
- [ ] Add application name/logo to navbar
- [ ] Add navigation links: "Domains" and "Subdomains"
- [ ] Add user info and logout button to navbar (right side)
- [ ] Create main content container
- [ ] Add flash message display area
- [ ] Ensure responsive design
- [ ] Test navbar on different screen sizes

**Acceptance Criteria:**

- Dark navbar displays at top of page
- Navigation links work correctly
- Logout button functions properly
- Main content area uses Bootstrap container
- Layout is responsive
- Flash messages can be displayed

**Dependencies:** Issue #2, Issue #3

------

### Issue #5: Create JSON Configuration Files

**Labels**: `configuration`, `setup`, `phase-1`, `priority-medium`

**Description:** Create JSON configuration files for all dropdown list-of-values in `resources/data/` directory.

**Tasks:**

- [ ] Create `resources/data/` directory
- [ ] Create `registrar.json` with sample data: ["GoDaddy", "Namecheap", "Google Domains", "Cloudflare", "Name.com"]
- [ ] Create `nameserver.json` with sample data: ["Cloudflare", "Route53", "Google Cloud DNS", "Azure DNS", "Registrar Default"]
- [ ] Create `mx_records.json` with sample data: ["Google Workspace", "Microsoft 365", "Zoho Mail", "Custom", "None"]
- [ ] Create `dns_manager.json` with sample data: ["Cloudflare", "Route53", "cPanel", "Plesk", "Azure DNS"]
- [ ] Create `cloud_hosting.json` with sample data: ["AWS", "Google Cloud", "Azure", "DigitalOcean", "Linode", "Vercel"]
- [ ] Create `project.json` with sample data: ["E-commerce Platform", "Corporate Website", "Blog", "API Service", "Development"]
- [ ] Create `status.json` with sample data: ["Active", "Inactive", "Pending", "Expired", "Suspended"]
- [ ] Verify all JSON files are valid JSON format

**Acceptance Criteria:**

- All 7 JSON files exist in `resources/data/` directory
- Each file contains valid JSON array format
- Each file has at least 3-5 sample values
- Files are editable as plain text

**Dependencies:** Issue #1

------

### Issue #6: Create JSON Helper Function/Service

**Labels**: `backend`, `helper`, `phase-1`, `priority-medium`

**Description:** Create a helper function or service class to load and cache JSON configuration files.

**Tasks:**

- [ ] Create `app/Helpers/JsonHelper.php` or add to helper file
- [ ] Implement `loadJsonConfig($filename)` function
- [ ] Add error handling for missing files
- [ ] Return empty array if file doesn't exist
- [ ] Cache JSON data within request lifecycle
- [ ] Create helper autoloading in `composer.json` if needed
- [ ] Write inline documentation for the helper

**Acceptance Criteria:**

- Helper function loads JSON files from `resources/data/`
- Returns array of values
- Returns empty array for missing files
- No errors when file doesn't exist
- Can be called from controllers and views

**Dependencies:** Issue #5

------

## Phase 2: Database & Models

### Issue #7: Create Domains Table Migration

**Labels**: `database`, `migration`, `phase-2`, `priority-high`

**Description:** Create database migration for the domains table with all required fields.

**Tasks:**

- [ ] Create migration file: `create_domains_table`
- [ ] Add `id` field (bigInteger, primary key, auto-increment)
- [ ] Add `domain` field (string, 255)
- [ ] Add `registrar` field (string, 100, nullable)
- [ ] Add `nameserver` field (string, 100, nullable)
- [ ] Add `mx_records` field (string, 100, nullable)
- [ ] Add `status` field (string, 50, nullable)
- [ ] Add `expiration_date` field (date, nullable)
- [ ] Add `task` field (text, nullable)
- [ ] Add `remarks` field (text, nullable)
- [ ] Add timestamps (created_at, updated_at)
- [ ] Test migration up and down

**Acceptance Criteria:**

- Migration creates domains table successfully
- All fields have correct data types
- Migration can be rolled back
- Table structure matches PRD specifications

**Dependencies:** Issue #1

------

### Issue #8: Create Subdomains Table Migration

**Labels**: `database`, `migration`, `phase-2`, `priority-high`

**Description:** Create database migration for the subdomains table with foreign key relationship to domains.

**Tasks:**

- [ ] Create migration file: `create_subdomains_table`
- [ ] Add `id` field (bigInteger, primary key, auto-increment)
- [ ] Add `subdomain` field (string, 100, nullable)
- [ ] Add `domain_id` field (bigInteger, foreign key)
- [ ] Add foreign key constraint to domains.id with CASCADE on delete
- [ ] Add `dns_manager` field (string, 100, nullable)
- [ ] Add `cloud_hosting` field (string, 100, nullable)
- [ ] Add `project` field (string, 100, nullable)
- [ ] Add `ip_address` field (string, 45, nullable)
- [ ] Add `status` field (string, 50, nullable)
- [ ] Add `task` field (text, nullable)
- [ ] Add `remarks` field (text, nullable)
- [ ] Add timestamps (created_at, updated_at)
- [ ] Test migration up and down
- [ ] Test cascade delete behavior

**Acceptance Criteria:**

- Migration creates subdomains table successfully
- Foreign key relationship to domains works
- Cascade delete removes subdomains when domain is deleted
- Migration can be rolled back
- Table structure matches PRD specifications

**Dependencies:** Issue #7

------

### Issue #9: Create Domain Model

**Labels**: `backend`, `model`, `phase-2`, `priority-high`

**Description:** Create the Domain Eloquent model with relationships and proper configuration.

**Tasks:**

- [ ] Create `app/Models/Domain.php`
- [ ] Define fillable fields array
- [ ] Cast `expiration_date` to date
- [ ] Define `hasMany` relationship to Subdomain model
- [ ] Add accessor for formatted expiration date (Mmm-DD-YYYY)
- [ ] Add any necessary model methods
- [ ] Document the model with PHPDoc

**Acceptance Criteria:**

- Domain model exists and follows Laravel conventions
- Relationship to Subdomain is defined
- Fillable fields are specified
- Date casting works correctly
- Model can create, read, update, delete records

**Dependencies:** Issue #7

------

### Issue #10: Create Subdomain Model

**Labels**: `backend`, `model`, `phase-2`, `priority-high`

**Description:** Create the Subdomain Eloquent model with relationships and proper configuration.

**Tasks:**

- [ ] Create `app/Models/Subdomain.php`
- [ ] Define fillable fields array
- [ ] Define `belongsTo` relationship to Domain model
- [ ] Add accessor for full subdomain display (subdomain.domain)
- [ ] Add any necessary model methods
- [ ] Document the model with PHPDoc

**Acceptance Criteria:**

- Subdomain model exists and follows Laravel conventions
- Relationship to Domain is defined
- Fillable fields are specified
- Model can create, read, update, delete records
- Full subdomain name can be accessed

**Dependencies:** Issue #8, Issue #9

------

### Issue #11: Create Database Seeders

**Labels**: `database`, `seeder`, `phase-2`, `priority-medium`

**Description:** Create database seeders for default user and optional sample data.

**Tasks:**

- [ ] Create `UserSeeder` for default admin user
- [ ] Set email: `admin@localhost`
- [ ] Set password: `password` (or from .env)
- [ ] Create `DomainSeeder` for 10-15 sample domains (optional)
- [ ] Create `SubdomainSeeder` for 30-40 sample subdomains (optional)
- [ ] Update `DatabaseSeeder` to call all seeders
- [ ] Test seeding process
- [ ] Document seeder usage in README

**Acceptance Criteria:**

- Default user can be seeded
- User can log in with seeded credentials
- Sample data seeds successfully (if implemented)
- Seeder can be run multiple times (use updateOrCreate or truncate)

**Dependencies:** Issue #3, Issue #9, Issue #10

------

### Issue #12: Run Migrations and Seed Database

**Labels**: `database`, `setup`, `phase-2`, `priority-high`

**Description:** Execute all migrations and seed the database with initial data.

**Tasks:**

- [ ] Run `php artisan migrate:fresh`
- [ ] Verify domains table exists
- [ ] Verify subdomains table exists
- [ ] Run `php artisan db:seed`
- [ ] Verify user was created
- [ ] Test login with seeded user
- [ ] Verify sample data exists (if seeded)
- [ ] Document database setup steps

**Acceptance Criteria:**

- Database tables are created
- Foreign keys work correctly
- User can log in
- No migration errors
- Database is ready for development

**Dependencies:** Issue #7, Issue #8, Issue #11

------

## Phase 3: Domains Management

### Issue #13: Create DomainController with Basic CRUD

**Labels**: `backend`, `controller`, `phase-3`, `priority-high`

**Description:** Create the DomainController with all CRUD methods and helper functions.

**Tasks:**

- [ ] Create `app/Http/Controllers/DomainController.php`
- [ ] Implement `index()` method
- [ ] Implement `create()` method
- [ ] Implement `store(Request $request)` method
- [ ] Implement `edit($id)` method
- [ ] Implement `update(Request $request, $id)` method
- [ ] Implement `destroy($id)` method
- [ ] Add method to load JSON config files
- [ ] Add flash message handling
- [ ] Document all methods with PHPDoc

**Acceptance Criteria:**

- Controller exists with all CRUD methods
- Methods follow Laravel conventions
- Flash messages are set on create/update/delete
- JSON config data is loaded for dropdowns
- Code follows PSR-12 standards

**Dependencies:** Issue #9, Issue #6

------

### Issue #14: Create Domain Routes

**Labels**: `backend`, `routes`, `phase-3`, `priority-high`

**Description:** Define all routes for domain management in web.php.

**Tasks:**

- [ ] Add route: `GET /domains` → `DomainController@index`
- [ ] Add route: `GET /domains/create` → `DomainController@create`
- [ ] Add route: `POST /domains` → `DomainController@store`
- [ ] Add route: `GET /domains/{id}/edit` → `DomainController@edit`
- [ ] Add route: `PUT /domains/{id}` → `DomainController@update`
- [ ] Add route: `DELETE /domains/{id}` → `DomainController@destroy`
- [ ] Protect all routes with `auth` middleware
- [ ] Use route names for easier reference
- [ ] Add redirect from `/` to `/domains`

**Acceptance Criteria:**

- All domain routes are defined
- Routes require authentication
- Route names follow Laravel conventions
- Root path redirects to domains index

**Dependencies:** Issue #13

------

### Issue #15: Create Domains Index View

**Labels**: `frontend`, `view`, `phase-3`, `priority-high`

**Description:** Create the domains index page with table display and "Add New" button.

**Tasks:**

- [ ] Create `resources/views/domains/index.blade.php`
- [ ] Extend layout (`@extends('layouts.app')`)
- [ ] Display flash messages
- [ ] Add "Add New Domain" button
- [ ] Create Bootstrap table with all columns
- [ ] Display: Domain, Registrar, Nameserver, MX Records, Status, Expiration Date, Task, Remarks
- [ ] Add Subdomains Count column (display count)
- [ ] Add Actions column with Edit and Delete buttons
- [ ] Format expiration date as Mmm-DD-YYYY
- [ ] Display total record count
- [ ] Use Bootstrap table classes (table, table-striped, table-hover)

**Acceptance Criteria:**

- Table displays all domain fields
- Subdomain count shows correct number
- Edit button links to edit page
- Delete button triggers action (will add modal later)
- Table is responsive
- Styling matches Bootstrap 5 design

**Dependencies:** Issue #4, Issue #13, Issue #14

------

### Issue #16: Create Domain Create View and Form

**Labels**: `frontend`, `view`, `phase-3`, `priority-high`

**Description:** Create the domain creation form with all fields and proper Bootstrap styling.

**Tasks:**

- [ ] Create `resources/views/domains/create.blade.php`
- [ ] Extend layout
- [ ] Create form with POST method to `/domains`
- [ ] Add CSRF token
- [ ] Add text input for domain
- [ ] Add dropdown for registrar (populate from JSON)
- [ ] Add dropdown for nameserver (populate from JSON)
- [ ] Add dropdown for mx_records (populate from JSON)
- [ ] Add dropdown for status (populate from JSON)
- [ ] Add date input for expiration_date
- [ ] Add textarea for task
- [ ] Add textarea for remarks
- [ ] Add "Save" button (btn-primary)
- [ ] Add "Cancel" button (btn-secondary, link to index)
- [ ] Use Bootstrap form classes (form-control, form-label, form-select)

**Acceptance Criteria:**

- Form displays all domain fields
- Dropdowns populate from JSON files
- Save button submits form
- Cancel button returns to index
- Form styling matches Bootstrap 5
- All fields are properly labeled

**Dependencies:** Issue #4, Issue #13, Issue #14

------

### Issue #17: Create Domain Edit View and Form

**Labels**: `frontend`, `view`, `phase-3`, `priority-high`

**Description:** Create the domain edit form with pre-populated data and proper Bootstrap styling.

**Tasks:**

- [ ] Create `resources/views/domains/edit.blade.php`
- [ ] Extend layout
- [ ] Create form with POST method and @method('PUT')
- [ ] Add CSRF token
- [ ] Pre-populate all fields with domain data
- [ ] Use same field structure as create form
- [ ] Change button text to "Update"
- [ ] Add "Cancel" button (link to index)
- [ ] Ensure dropdowns show current selected values
- [ ] Format expiration date for date input

**Acceptance Criteria:**

- Form pre-populates with existing domain data
- All fields can be edited
- Update button submits form
- Cancel button returns to index
- Dropdowns show correct selected values
- Form styling matches create form

**Dependencies:** Issue #4, Issue #13, Issue #14

------

### Issue #18: Implement Domain Delete with Confirmation Modal

**Labels**: `frontend`, `backend`, `phase-3`, `priority-high`

**Description:** Add delete confirmation modal to domains index and implement delete functionality.

**Tasks:**

- [ ] Add Bootstrap modal to domains index view
- [ ] Modal displays domain name
- [ ] Modal warns about cascade delete of subdomains
- [ ] Add "Cancel" button (dismisses modal)
- [ ] Add "Delete" button (btn-danger, submits delete form)
- [ ] Create hidden form for DELETE request
- [ ] Link Delete button in table to open modal
- [ ] Pass domain name to modal dynamically
- [ ] Implement destroy method in controller
- [ ] Delete domain and cascade delete subdomains
- [ ] Flash success message after deletion
- [ ] Redirect to domains index

**Acceptance Criteria:**

- Delete button opens confirmation modal
- Modal shows correct domain name
- Modal warns about subdomain deletion
- Cancel dismisses modal without deleting
- Delete removes domain and all subdomains
- Success message displays after deletion
- User redirects to domains index

**Dependencies:** Issue #13, Issue #15

------

### Issue #19: Implement Domain Search and Filter

**Labels**: `frontend`, `backend`, `phase-3`, `priority-medium`

**Description:** Add search and filter functionality to domains index page.

**Tasks:**

- [ ] Add search form above table
- [ ] Add text input for domain name search
- [ ] Add dropdown filter for registrar
- [ ] Add dropdown filter for nameserver
- [ ] Add dropdown filter for mx_records
- [ ] Add dropdown filter for status
- [ ] Add "Filter" submit button
- [ ] Add "Clear Filters" link
- [ ] Update controller index method to handle query parameters
- [ ] Apply filters to database query
- [ ] Preserve filter values in form after submission
- [ ] Display active filters clearly

**Acceptance Criteria:**

- Search box filters by domain name
- Dropdown filters work correctly
- Multiple filters can be applied simultaneously
- Filter button applies all filters
- Clear button removes all filters
- Filter values persist after page reload
- Filtered results display correctly

**Dependencies:** Issue #13, Issue #15

------

### Issue #20: Implement Domain Column Sorting

**Labels**: `frontend`, `backend`, `phase-3`, `priority-medium`

**Description:** Add sortable column headers to domains table with visual indicators.

**Tasks:**

- [ ] Make column headers clickable links
- [ ] Add sort query parameters (field, direction)
- [ ] Add up/down arrow icons to headers
- [ ] Highlight currently sorted column
- [ ] Update controller to handle sort parameters
- [ ] Apply sorting to database query
- [ ] Toggle sort direction on header click
- [ ] Make sortable: Domain, Registrar, Nameserver, MX Records, Status, Expiration Date
- [ ] Default sort: Domain name ascending
- [ ] Preserve filters when sorting

**Acceptance Criteria:**

- Column headers are clickable
- Clicking header sorts data
- Sort direction toggles (asc/desc)
- Visual indicator shows current sort
- Sorting preserves active filters
- All specified columns are sortable
- Default sort is domain name ascending

**Dependencies:** Issue #13, Issue #15

------

### Issue #21: Implement Domain Pagination

**Labels**: `frontend`, `backend`, `phase-3`, `priority-medium`

**Description:** Add pagination to domains index with 100 records per page.

**Tasks:**

- [ ] Update controller to use `paginate(100)`
- [ ] Add Laravel pagination links to view
- [ ] Style pagination with Bootstrap
- [ ] Preserve filters in pagination links
- [ ] Preserve sorting in pagination links
- [ ] Display total record count
- [ ] Display current page information
- [ ] Test with large dataset

**Acceptance Criteria:**

- Pagination displays 100 records per page
- Pagination links work correctly
- Filters persist across pages
- Sorting persists across pages
- Bootstrap pagination styling applied
- Total record count displays
- Works with filtered and sorted data

**Dependencies:** Issue #13, Issue #15

------

### Issue #22: Add Subdomain Count to Domains Table

**Labels**: `backend`, `frontend`, `phase-3`, `priority-medium`

**Description:** Display the count of associated subdomains in the domains index table.

**Tasks:**

- [ ] Update controller query to use `withCount('subdomains')`
- [ ] Add Subdomains Count column to table
- [ ] Display count (e.g., "5 subdomains")
- [ ] Handle zero count gracefully (e.g., "0 subdomains")
- [ ] Style count appropriately
- [ ] Ensure count doesn't affect pagination performance

**Acceptance Criteria:**

- Subdomain count displays for each domain
- Count is accurate
- Zero count displays correctly
- Column is properly labeled
- No performance issues with large datasets

**Dependencies:** Issue #10, Issue #15

------

## Phase 4: Subdomains Management

### Issue #23: Create SubdomainController with Basic CRUD

**Labels**: `backend`, `controller`, `phase-4`, `priority-high`

**Description:** Create the SubdomainController with all CRUD methods and helper functions.

**Tasks:**

- [ ] Create `app/Http/Controllers/SubdomainController.php`
- [ ] Implement `index()` method
- [ ] Implement `create()` method
- [ ] Implement `store(Request $request)` method
- [ ] Implement `edit($id)` method
- [ ] Implement `update(Request $request, $id)` method
- [ ] Implement `destroy($id)` method
- [ ] Add method to load JSON config files
- [ ] Add method to load domains list
- [ ] Add flash message handling
- [ ] Document all methods with PHPDoc

**Acceptance Criteria:**

- Controller exists with all CRUD methods
- Methods follow Laravel conventions
- Flash messages are set on create/update/delete
- JSON config data is loaded for dropdowns
- Domains list is loaded for dropdown
- Code follows PSR-12 standards

**Dependencies:** Issue #10, Issue #6

------

### Issue #24: Create Subdomain Routes

**Labels**: `backend`, `routes`, `phase-4`, `priority-high`

**Description:** Define all routes for subdomain management in web.php.

**Tasks:**

- [ ] Add route: `GET /subdomains` → `SubdomainController@index`
- [ ] Add route: `GET /subdomains/create` → `SubdomainController@create`
- [ ] Add route: `POST /subdomains` → `SubdomainController@store`
- [ ] Add route: `GET /subdomains/{id}/edit` → `SubdomainController@edit`
- [ ] Add route: `PUT /subdomains/{id}` → `SubdomainController@update`
- [ ] Add route: `DELETE /subdomains/{id}` → `SubdomainController@destroy`
- [ ] Protect all routes with `auth` middleware
- [ ] Use route names for easier reference

**Acceptance Criteria:**

- All subdomain routes are defined
- Routes require authentication
- Route names follow Laravel conventions

**Dependencies:** Issue #23

------

### Issue #25: Create Subdomains Index View

**Labels**: `frontend`, `view`, `phase-4`, `priority-high`

**Description:** Create the subdomains index page with table display showing subdomain and parent domain.

**Tasks:**

- [ ] Create `resources/views/subdomains/index.blade.php`
- [ ] Extend layout
- [ ] Display flash messages
- [ ] Add "Add New Subdomain" button
- [ ] Create Bootstrap table with all columns
- [ ] Display: Subdomain (as subdomain.domain), Domain, DNS Manager, Cloud Hosting, Project, IP Address, Status, Task, Remarks
- [ ] Add Actions column with Edit and Delete buttons
- [ ] Display total record count
- [ ] Use Bootstrap table classes
- [ ] Show "@" for root domain subdomains

**Acceptance Criteria:**

- Table displays all subdomain fields
- Full subdomain.domain format displays correctly
- Root domain (@) displays properly
- Edit button links to edit page
- Delete button triggers action
- Table is responsive
- Styling matches Bootstrap 5 design

**Dependencies:** Issue #4, Issue #23, Issue #24

------

### Issue #26: Create Subdomain Create View and Form

**Labels**: `frontend`, `view`, `phase-4`, `priority-high`

**Description:** Create the subdomain creation form with domain dropdown and all other fields.

**Tasks:**

- [ ] Create `resources/views/subdomains/create.blade.php`
- [ ] Extend layout
- [ ] Create form with POST method to `/subdomains`
- [ ] Add CSRF token
- [ ] Add text input for subdomain (with placeholder hint)
- [ ] Add dropdown for domain_id (populate from domains table)
- [ ] Add dropdown for dns_manager (populate from JSON)
- [ ] Add dropdown for cloud_hosting (populate from JSON)
- [ ] Add dropdown for project (populate from JSON)
- [ ] Add text input for ip_address
- [ ] Add dropdown for status (populate from JSON)
- [ ] Add textarea for task
- [ ] Add textarea for remarks
- [ ] Add "Save" button (btn-primary)
- [ ] Add "Cancel" button (btn-secondary, link to index)
- [ ] Use Bootstrap form classes

**Acceptance Criteria:**

- Form displays all subdomain fields
- Domain dropdown shows all available domains
- Other dropdowns populate from JSON files
- Save button submits form
- Cancel button returns to index
- Form styling matches Bootstrap 5
- Placeholder shows example subdomain formats

**Dependencies:** Issue #4, Issue #23, Issue #24

------

### Issue #27: Create Subdomain Edit View and Form

**Labels**: `frontend`, `view`, `phase-4`, `priority-high`

**Description:** Create the subdomain edit form with pre-populated data.

**Tasks:**

- [ ] Create `resources/views/subdomains/edit.blade.php`
- [ ] Extend layout
- [ ] Create form with POST method and @method('PUT')
- [ ] Add CSRF token
- [ ] Pre-populate all fields with subdomain data
- [ ] Use same field structure as create form
- [ ] Change button text to "Update"
- [ ] Add "Cancel" button (link to index)
- [ ] Ensure domain dropdown shows current domain
- [ ] Ensure other dropdowns show current selected values

**Acceptance Criteria:**

- Form pre-populates with existing subdomain data
- All fields can be edited
- Update button submits form
- Cancel button returns to index
- All dropdowns show correct selected values
- Form styling matches create form

**Dependencies:** Issue #4, Issue #23, Issue #24

------

### Issue #28: Implement Subdomain Delete with Confirmation Modal

**Labels**: `frontend`, `backend`, `phase-4`, `priority-high`

**Description:** Add delete confirmation modal to subdomains index and implement delete functionality.

**Tasks:**

- [ ] Add Bootstrap modal to subdomains index view
- [ ] Modal displays full subdomain.domain name
- [ ] Add "Cancel" button (dismisses modal)
- [ ] Add "Delete" button (btn-danger, submits delete form)
- [ ] Create hidden form for DELETE request
- [ ] Link Delete button in table to open modal
- [ ] Pass subdomain name to modal dynamically
- [ ] Implement destroy method in controller
- [ ] Delete subdomain only (not parent domain)
- [ ] Flash success message after deletion
- [ ] Redirect to subdomains index

**Acceptance Criteria:**

- Delete button opens confirmation modal
- Modal shows correct subdomain.domain name
- Cancel dismisses modal without deleting
- Delete removes only the subdomain
- Parent domain remains intact
- Success message displays after deletion
- User redirects to subdomains index

**Dependencies:** Issue #23, Issue #25

------

### Issue #29: Implement Subdomain Search and Filter

**Labels**: `frontend`, `backend`, `phase-4`, `priority-medium`

**Description:** Add search and filter functionality to subdomains index page.

**Tasks:**

- [ ] Add search form above table
- [ ] Add text input for subdomain name search
- [ ] Add dropdown filter for domain (list all domains)
- [ ] Add dropdown filter for dns_manager
- [ ] Add dropdown filter for cloud_hosting
- [ ] Add dropdown filter for project
- [ ] Add dropdown filter for status
- [ ] Add "Filter" submit button
- [ ] Add "Clear Filters" link
- [ ] Update controller index method to handle query parameters
- [ ] Apply filters to database query with joins for domain
- [ ] Preserve filter values in form after submission
- [ ] Display active filters clearly

**Acceptance Criteria:**

- Search box filters by subdomain name
- All dropdown filters work correctly
- Domain filter shows domain names
- Multiple filters can be applied simultaneously
- Filter button applies all filters
- Clear button removes all filters
- Filter values persist after page reload
- Filtered results display correctly

**Dependencies:** Issue #23, Issue #25

------

### Issue #30: Implement Subdomain Column Sorting

**Labels**: `frontend`, `backend`, `phase-4`, `priority-medium`

**Description:** Add sortable column headers to subdomains table with visual indicators.

**Tasks:**

- [ ] Make column headers clickable links
- [ ] Add sort query parameters (field, direction)
- [ ] Add up/down arrow icons to headers
- [ ] Highlight currently sorted column
- [ ] Update controller to handle sort parameters
- [ ] Apply sorting to database query (with joins for domain)
- [ ] Toggle sort direction on header click
- [ ] Make sortable: Subdomain, Domain, DNS Manager, Cloud Hosting, Project, Status
- [ ] Default sort: Subdomain name ascending
- [ ] Preserve filters when sorting

**Acceptance Criteria:**

- Column headers are clickable
- Clicking header sorts data
- Sort direction toggles (asc/desc)
- Visual indicator shows current sort
- Sorting preserves active filters
- All specified columns are sortable
- Sorting by Domain works correctly (uses domain name)
- Default sort is subdomain name ascending

**Dependencies:** Issue #23, Issue #25

------

### Issue #31: Implement Subdomain Pagination

**Labels**: `frontend`, `backend`, `phase-4`, `priority-medium`

**Description:** Add pagination to subdomains index with 100 records per page.

**Tasks:**

- [ ] Update controller to use `paginate(100)`
- [ ] Add Laravel pagination links to view
- [ ] Style pagination with Bootstrap
- [ ] Preserve filters in pagination links
- [ ] Preserve sorting in pagination links
- [ ] Display total record count
- [ ] Display current page information
- [ ] Test with large dataset

**Acceptance Criteria:**

- Pagination displays 100 records per page
- Pagination links work correctly
- Filters persist across pages
- Sorting persists across pages
- Bootstrap pagination styling applied
- Total record count displays
- Works with filtered and sorted data

**Dependencies:** Issue #23, Issue #25

------

## Phase 5: Testing & Refinement

### Issue #32: Test Domain CRUD Operations

**Labels**: `testing`, `phase-5`, `priority-high`

**Description:** Perform comprehensive manual testing of all domain management features.

**Tasks:**

- [ ] Test creating a new domain with all fields
- [ ] Test creating a domain with minimal fields
- [ ] Test editing an existing domain
- [ ] Test deleting a domain without subdomains
- [ ] Test deleting a domain with subdomains (verify cascade)
- [ ] Test cancel button on create form
- [ ] Test cancel button on edit form
- [ ] Verify all dropdowns populate correctly
- [ ] Verify date picker works
- [ ] Verify success messages display
- [ ] Test with invalid domain ID (404 handling)

**Acceptance Criteria:**

- All create operations work correctly
- All update operations work correctly
- All delete operations work correctly
- Cascade delete removes subdomains
- Cancel buttons work without saving
- Error handling works properly
- No JavaScript errors in console

**Dependencies:** Issue #13-#22

------

### Issue #33: Test Subdomain CRUD Operations

**Labels**: `testing`, `phase-5`, `priority-high`

**Description:** Perform comprehensive manual testing of all subdomain management features.

**Tasks:**

- [ ] Test creating a new subdomain with all fields
- [ ] Test creating a subdomain with minimal fields
- [ ] Test creating subdomain with "@" for root
- [ ] Test editing an existing subdomain
- [ ] Test deleting a subdomain (verify parent domain remains)
- [ ] Test cancel button on create form
- [ ] Test cancel button on edit form
- [ ] Verify all dropdowns populate correctly
- [ ] Verify domain dropdown shows all domains
- [ ] Verify success messages display
- [ ] Test with invalid subdomain ID (404 handling)

**Acceptance Criteria:**

- All create operations work correctly
- All update operations work correctly
- All delete operations work correctly
- Parent domain is not affected by subdomain deletion
- Cancel buttons work without saving
- Root domain "@" displays properly
- Error handling works properly
- No JavaScript errors in console

**Dependencies:** Issue #23-#31

------

### Issue #34: Test Search, Filter, and Sort Functionality

**Labels**: `testing`, `phase-5`, `priority-high`

**Description:** Test all search, filter, and sorting features on both domains and subdomains.

**Tasks:**

- [ ] Test domain search with various keywords
- [ ] Test each domain filter dropdown individually
- [ ] Test multiple domain filters combined
- [ ] Test domain column sorting (all sortable columns)
- [ ] Test clearing domain filters
- [ ] Test subdomain search with various keywords
- [ ] Test each subdomain filter dropdown individually
- [ ] Test multiple subdomain filters combined
- [ ] Test subdomain column sorting (all sortable columns)
- [ ] Test clearing subdomain filters
- [ ] Test filter persistence across pagination
- [ ] Test sort persistence across pagination
- [ ] Test combined filters and sorting

**Acceptance Criteria:**

- All search functionality works correctly
- All filters work individually and combined
- All sortable columns sort correctly
- Sort direction toggles properly
- Filters persist across pages
- Sorting persists across pages
- Clear filters button works
- No conflicts between features

**Dependencies:** Issue #19-#21, Issue #29-#31

------

### Issue #35: Test Pagination and Performance

**Labels**: `testing`, `performance`, `phase-5`, `priority-medium`

**Description:** Test pagination functionality and application performance with various data volumes.

**Tasks:**

- [ ] Test domains pagination with < 100 records
- [ ] Test domains pagination with > 100 records
- [ ] Test domains pagination with > 1000 records
- [ ] Test subdomains pagination with < 100 records
- [ ] Test subdomains pagination with > 100 records
- [ ] Test subdomains pagination with > 1000 records
- [ ] Test pagination navigation (next, prev, first, last)
- [ ] Test jumping to specific page
- [ ] Measure page load times
- [ ] Test with slow network simulation
- [ ] Verify subdomain count query performance

**Acceptance Criteria:**

- Pagination works with all data volumes
- Page load times are acceptable (< 2 seconds)
- Navigation between pages is smooth
- No memory issues with large datasets
- Subdomain count doesn't cause performance issues
- Total record count is accurate

**Dependencies:** Issue #21, Issue #31

------

### Issue #36: Test Authentication and Security

**Labels**: `testing`, `security`, `phase-5`, `priority-high`

**Description:** Test authentication, authorization, and security features.

**Tasks:**

- [ ] Test login with correct credentials
- [ ] Test login with incorrect credentials
- [ ] Test accessing pages without authentication
- [ ] Verify redirect to login for unauthenticated users
- [ ] Verify redirect to domains after login
- [ ] Test logout functionality
- [ ] Verify CSRF tokens on all forms
- [ ] Test form submission without CSRF token
- [ ] Test XSS protection (input/output escaping)
- [ ] Test SQL injection attempts (Eloquent should prevent)
- [ ] Verify session management works correctly

**Acceptance Criteria:**

- Login works with correct credentials
- Login fails with incorrect credentials
- All pages require authentication
- Redirects work correctly
- Logout clears session
- CSRF protection works on all forms
- XSS attempts are escaped
- SQL injection attempts fail
- No security vulnerabilities found

**Dependencies:** Issue #3, All controller issues

------

### Issue #37: Test Cascade Delete and Data Integrity

**Labels**: `testing`, `data-integrity`, `phase-5`, `priority-high`

**Description:** Thoroughly test cascade delete behavior and data integrity constraints.

**Tasks:**

- [ ] Create domain with multiple subdomains
- [ ] Delete domain and verify all subdomains are deleted
- [ ] Verify subdomain count is accurate before deletion
- [ ] Attempt to create subdomain with non-existent domain_id
- [ ] Test foreign key constraints
- [ ] Test data integrity after multiple operations
- [ ] Verify no orphaned subdomain records exist
- [ ] Test database rollback scenarios (if applicable)

**Acceptance Criteria:**

- Cascade delete removes all related subdomains
- No orphaned subdomains remain after domain deletion
- Foreign key constraints work correctly
- Cannot create subdomain with invalid domain_id
- Data integrity is maintained throughout operations
- Database is in consistent state after all operations

**Dependencies:** Issue #8, Issue #18, Issue #28

------

### Issue #38: UI/UX Refinement and Responsive Design

**Labels**: `frontend`, `ui-ux`, `phase-5`, `priority-medium`

**Description:** Review and refine UI/UX across all pages, ensure responsive design.

**Tasks:**

- [ ] Test all pages on desktop browsers (Chrome, Firefox, Safari, Edge)
- [ ] Test responsive design on tablet sizes
- [ ] Test responsive design on mobile sizes
- [ ] Verify Bootstrap styling is consistent
- [ ] Check button placements and sizing
- [ ] Verify form spacing and alignment
- [ ] Test table responsiveness with many columns
- [ ] Verify modal appearance and behavior
- [ ] Check navigation usability
- [ ] Verify flash messages are visible and dismissible
- [ ] Test date picker usability
- [ ] Ensure adequate color contrast
- [ ] Test with long text in fields (overflow handling)

**Acceptance Criteria:**

- Application is fully responsive
- Design is consistent across all pages
- Tables adapt to smaller screens appropriately
- Forms are usable on mobile devices
- Navigation works on all screen sizes
- All interactive elements are easily clickable
- Text is readable at all sizes
- No UI breaking issues on any browser

**Dependencies:** All frontend issues

------

### Issue #39: Documentation and Code Cleanup

**Labels**: `documentation`, `cleanup`, `phase-5`, `priority-medium`

**Description:** Create documentation and clean up code for maintainability.

**Tasks:**

- [ ] Write README.md with project overview
- [ ] Document installation steps
- [ ] Document database setup instructions
- [ ] Document default login credentials
- [ ] Document how to edit JSON configuration files
- [ ] Add inline code comments where necessary
- [ ] Remove unused code and imports
- [ ] Verify PSR-12 code style compliance
- [ ] Add PHPDoc to all methods
- [ ] Create .env.example with required variables
- [ ] Document Herd.app setup requirements

**Acceptance Criteria:**

- README is complete and clear
- Installation instructions are accurate
- JSON configuration is documented
- Code is well-commented
- No unused code remains
- Code follows PSR-12 standards
- .env.example includes all required variables
- Documentation helps new developers get started

**Dependencies:** All issues

------

### Issue #40: Final Integration Testing

**Labels**: `testing`, `integration`, `phase-5`, `priority-high`

**Description:** Perform end-to-end integration testing of complete user workflows.

**Tasks:**

- [ ] Complete workflow: Login → View Domains → Add Domain → Add Subdomains → Edit → Delete
- [ ] Test workflow: Filter domains → Edit filtered domain → Verify in list
- [ ] Test workflow: Sort subdomains → Delete subdomain → Verify count updates in domains
- [ ] Test workflow: Add domain → Add 10 subdomains → Delete domain → Verify cascade
- [ ] Test multiple browser tabs/windows simultaneously
- [ ] Test back button behavior
- [ ] Test bookmark/direct URL access
- [ ] Test session timeout scenarios
- [ ] Verify all links work correctly
- [ ] Test with realistic data volumes
- [ ] Verify no JavaScript console errors throughout all workflows

**Acceptance Criteria:**

- All user workflows complete successfully
- No errors during typical usage patterns
- Application behaves predictably
- Data integrity maintained across workflows
- Session handling works correctly
- Navigation is intuitive throughout
- Performance is acceptable in all scenarios

**Dependencies:** All issues

------

## Quick Reference Checklist

### Phase 1: Setup (7 issues)

- [ ] #1 - Initialize Laravel Project
- [ ] #2 - Install Bootstrap 5
- [ ] #3 - Set Up Authentication
- [ ] #4 - Create Base Layout
- [ ] #5 - Create JSON Files
- [ ] #6 - Create JSON Helper
- [ ] #7 - Create Domains Migration

### Phase 2: Database (6 issues)

- [ ] #7 - Create Domains Migration
- [ ] #8 - Create Subdomains Migration
- [ ] #9 - Create Domain Model
- [ ] #10 - Create Subdomain Model
- [ ] #11 - Create Seeders
- [ ] #12 - Run Migrations

### Phase 3: Domains (10 issues)

- [ ] #13 - Domain Controller
- [ ] #14 - Domain Routes
- [ ] #15 - Domains Index View
- [ ] #16 - Domain Create Form
- [ ] #17 - Domain Edit Form
- [ ] #18 - Domain Delete Modal
- [ ] #19 - Domain Search/Filter
- [ ] #20 - Domain Sorting
- [ ] #21 - Domain Pagination
- [ ] #22 - Subdomain Count

### Phase 4: Subdomains (9 issues)

- [ ] #23 - Subdomain Controller
- [ ] #24 - Subdomain Routes
- [ ] #25 - Subdomains Index View
- [ ] #26 - Subdomain Create Form
- [ ] #27 - Subdomain Edit Form
- [ ] #28 - Subdomain Delete Modal
- [ ] #29 - Subdomain Search/Filter
- [ ] #30 - Subdomain Sorting
- [ ] #31 - Subdomain Pagination

### Phase 5: Testing (9 issues)

- [ ] #32 - Test Domain CRUD
- [ ] #33 - Test Subdomain CRUD
- [ ] #34 - Test Search/Filter/Sort
- [ ] #35 - Test Pagination/Performance
- [ ] #36 - Test Authentication/Security
- [ ] #37 - Test Cascade Delete
- [ ] #38 - UI/UX Refinement
- [ ] #39 - Documentation
- [ ] #40 - Final Integration Testing

------

## Label Definitions

Use these labels when creating GitHub issues:

- `setup` - Initial project configuration and setup tasks
- `database` - Database migrations, models, and seeders
- `migration` - Database migration files
- `model` - Eloquent model files
- `seeder` - Database seeding
- `backend` - Backend/controller logic
- `controller` - Controller files
- `routes` - Route definitions
- `helper` - Helper functions and utilities
- `frontend` - Frontend views and UI
- `view` - Blade template files
- `layout` - Layout and navigation
- `ui-ux` - User interface and user experience
- `authentication` - Login/logout functionality
- `configuration` - Configuration files and settings
- `testing` - Testing tasks
- `integration` - Integration testing
- `performance` - Performance testing and optimization
- `security` - Security testing and features
- `data-integrity` - Data integrity and validation
- `documentation` - Documentation tasks
- `cleanup` - Code cleanup and refactoring
- `phase-1` - Phase 1 tasks
- `phase-2` - Phase 2 tasks
- `phase-3` - Phase 3 tasks
- `phase-4` - Phase 4 tasks
- `phase-5` - Phase 5 tasks
- `priority-high` - Must be completed for MVP
- `priority-medium` - Important but not critical
- `priority-low` - Nice to have

------

## Notes for Implementation

1. **Sequential Dependencies**: Follow the issue numbers sequentially within each phase for optimal workflow
2. **Parallel Work**: Issues within the same phase without dependencies can be worked on in parallel
3. **Testing**: Don't wait until Phase 5 to test - test each feature as it's completed
4. **Git Workflow**: Create a feature branch for each issue, commit frequently, and merge to main after testing
5. **Issue Tracking**: Close issues only after acceptance criteria are met and tested
6. **Documentation**: Update README as you go, don't leave it all for the end

------

**Total Issues**: 40
**Estimated Complexity**: Medium
**Recommended Timeline**: 2-3 weeks for solo developer, 1-2 weeks for team