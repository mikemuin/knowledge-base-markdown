# Product Requirements Document: Domain Management System

## 1. Project Overview

### 1.1 Purpose

A personal Laravel-based web application for managing a database of owned domains and their associated subdomains, including details about registrars, nameservers, hosting providers, and other relevant metadata.

### 1.2 Scope

- Single-user domain and subdomain management system
- Local development environment using Herd.app
- SQLite database backend
- 100% Laravel implementation (Blade templates, no separate frontend framework)
- Bootstrap 5 UI with dark navbar theme

### 1.3 Target Environment

- **Development Server**: Herd.app (local machine)
- **Database**: SQLite
- **Framework**: Laravel (latest stable version)
- **PHP Version**: Compatible with Herd.app requirements
- **Frontend**: Bootstrap 5 (self-contained, no CDN dependencies)

------

## 2. Technical Architecture

### 2.1 Technology Stack

- **Backend**: PHP with Laravel framework
- **Database**: SQLite
- **Frontend**: Blade templates with Bootstrap 5
- **Authentication**: Laravel built-in authentication
- **Development Environment**: Herd.app

### 2.2 Application Structure

```
app/
├── Http/
│   ├── Controllers/
│   │   ├── DomainController.php
│   │   └── SubdomainController.php
├── Models/
│   ├── Domain.php
│   └── Subdomain.php
database/
├── migrations/
├── seeders/
resources/
├── data/
│   ├── registrar.json
│   ├── nameserver.json
│   ├── mx_records.json
│   ├── dns_manager.json
│   ├── cloud_hosting.json
│   ├── project.json
│   └── status.json
├── views/
│   ├── layouts/
│   │   └── app.blade.php
│   ├── domains/
│   │   ├── index.blade.php
│   │   ├── create.blade.php
│   │   └── edit.blade.php
│   └── subdomains/
│       ├── index.blade.php
│       ├── create.blade.php
│       └── edit.blade.php
routes/
└── web.php
```

------

## 3. Data Model Specifications

### 3.1 Domains Table

**Table Name**: `domains`

| Column Name     | Data Type       | Constraints                 | Description                            |
| --------------- | --------------- | --------------------------- | -------------------------------------- |
| id              | BIGINT UNSIGNED | PRIMARY KEY, AUTO_INCREMENT | Unique identifier                      |
| domain          | VARCHAR(255)    | NOT NULL                    | Domain name (e.g., example.com)        |
| registrar       | VARCHAR(100)    | NULLABLE                    | Domain registrar from JSON list        |
| nameserver      | VARCHAR(100)    | NULLABLE                    | Nameserver provider from JSON list     |
| mx_records      | VARCHAR(100)    | NULLABLE                    | MX record configuration from JSON list |
| status          | VARCHAR(50)     | NULLABLE                    | Domain status from JSON list           |
| expiration_date | DATE            | NULLABLE                    | Domain expiration date                 |
| task            | TEXT            | NULLABLE                    | Free text for tasks/todos              |
| remarks         | TEXT            | NULLABLE                    | Free text for additional notes         |
| created_at      | TIMESTAMP       |                             | Laravel timestamp                      |
| updated_at      | TIMESTAMP       |                             | Laravel timestamp                      |

### 3.2 Subdomains Table

**Table Name**: `subdomains`

| Column Name   | Data Type       | Constraints                 | Description                                                  |
| ------------- | --------------- | --------------------------- | ------------------------------------------------------------ |
| id            | BIGINT UNSIGNED | PRIMARY KEY, AUTO_INCREMENT | Unique identifier                                            |
| subdomain     | VARCHAR(100)    | NULLABLE                    | Subdomain prefix (e.g., "mail", "portal", "beta") or "@" for root |
| domain_id     | BIGINT UNSIGNED | FOREIGN KEY, NOT NULL       | References domains.id, CASCADE on delete                     |
| dns_manager   | VARCHAR(100)    | NULLABLE                    | DNS management service from JSON list                        |
| cloud_hosting | VARCHAR(100)    | NULLABLE                    | Cloud hosting provider from JSON list                        |
| project       | VARCHAR(100)    | NULLABLE                    | Associated project from JSON list                            |
| ip_address    | VARCHAR(45)     | NULLABLE                    | IPv4 or IPv6 address                                         |
| status        | VARCHAR(50)     | NULLABLE                    | Subdomain status from JSON list                              |
| task          | TEXT            | NULLABLE                    | Free text for tasks/todos                                    |
| remarks       | TEXT            | NULLABLE                    | Free text for additional notes                               |
| created_at    | TIMESTAMP       |                             | Laravel timestamp                                            |
| updated_at    | TIMESTAMP       |                             | Laravel timestamp                                            |

### 3.3 Model Relationships

- **Domain** `hasMany` **Subdomain**
- **Subdomain** `belongsTo` **Domain**
- Cascade delete: When a domain is deleted, all associated subdomains are automatically deleted

------

## 4. JSON Configuration Files

### 4.1 Storage Location

All JSON configuration files must be stored in: `resources/data/`

### 4.2 File Structure

Each file should follow this format:

```json
[
    "Value 1",
    "Value 2",
    "Value 3"
]
```

### 4.3 Required JSON Files

| Filename           | Purpose                  | Sample Dummy Data                                            |
| ------------------ | ------------------------ | ------------------------------------------------------------ |
| registrar.json     | Domain registrars        | ["GoDaddy", "Namecheap", "Google Domains", "Cloudflare", "Name.com"] |
| nameserver.json    | Nameserver providers     | ["Cloudflare", "Route53", "Google Cloud DNS", "Azure DNS", "Registrar Default"] |
| mx_records.json    | MX record configurations | ["Google Workspace", "Microsoft 365", "Zoho Mail", "Custom", "None"] |
| dns_manager.json   | DNS management services  | ["Cloudflare", "Route53", "cPanel", "Plesk", "Azure DNS"]    |
| cloud_hosting.json | Hosting providers        | ["AWS", "Google Cloud", "Azure", "DigitalOcean", "Linode", "Vercel"] |
| project.json       | Project names            | ["E-commerce Platform", "Corporate Website", "Blog", "API Service", "Development"] |
| status.json        | Status values            | ["Active", "Inactive", "Pending", "Expired", "Suspended"]    |

### 4.4 JSON Loading Service

Create a helper service or use a helper function to load JSON data:

- Cache JSON file contents for performance
- Return empty array if file doesn't exist or is malformed
- Used in controllers to populate dropdown options

------

## 5. Authentication & Authorization

### 5.1 Authentication Requirements

- **Type**: Single user authentication
- **Implementation**: Laravel Breeze or manual auth implementation
- **User Management**: Direct database manipulation (no UI for user management)
- **Login Required**: All application pages require authentication
- **Session Management**: Standard Laravel session handling

### 5.2 User Table (Minimal)

- Use Laravel's default users migration
- Seed with one user account
- No registration page needed
- No password reset functionality required (local application)

### 5.3 Access Control

- No role-based access control needed
- Authenticated user has full CRUD access to all features
- Logout functionality in navbar

------

## 6. User Interface Specifications

### 6.1 Layout & Navigation

#### 6.1.1 Master Layout (`layouts/app.blade.php`)

- Dark Bootstrap 5 navbar at the top
- Navbar contents:
  - Application name/logo (left)
  - Navigation links: "Domains", "Subdomains" (center/left)
  - User info and logout button (right)
- Main content area with container
- No sidebar required

#### 6.1.2 Navigation Flow

- After login: Redirect to Domains index page
- Primary navigation between Domains and Subdomains pages
- Breadcrumb navigation not required

### 6.2 Bootstrap 5 Implementation

- Use self-contained Bootstrap 5 (download and include in public/assets)
- Dark navbar variant (`navbar-dark bg-dark`)
- Use standard Bootstrap components:
  - Tables (`.table`, `.table-striped`, `.table-hover`)
  - Forms (`.form-control`, `.form-select`, `.form-label`)
  - Buttons (`.btn`, `.btn-primary`, `.btn-danger`, etc.)
  - Modals for delete confirmations
  - Pagination component
- No custom CSS required beyond Bootstrap defaults
- Responsive design using Bootstrap grid system

------

## 7. Feature Specifications

### 7.1 Domains Management

#### 7.1.1 Domains Index Page (`/domains`)

**URL**: `/domains`
**Purpose**: Display all domains in a searchable, sortable, paginated table

**Table Columns**:

1. Domain
2. Registrar
3. Nameserver
4. MX Records
5. Status
6. Expiration Date (format: Mmm-DD-YYYY, e.g., "Jan-15-2025")
7. Task
8. Remarks
9. Subdomains Count (display number, e.g., "5 subdomains")
10. Actions (Edit | Delete buttons)

**Features**:

- **Search/Filter**:
  - Search box for domain name (live filter or submit button)
  - Dropdown filters for: registrar, nameserver, mx_records, status
  - Filter submission applies all active filters
  - Clear filters option
- **Sorting**:
  - Click column headers to sort alphabetically (ascending/descending)
  - Default sort: domain name ascending
  - Sortable columns: Domain, Registrar, Nameserver, MX Records, Status, Expiration Date
- **Pagination**:
  - 100 records per page
  - Bootstrap pagination component
  - Display total record count
- **Actions**:
  - "Add New Domain" button (prominent, top-right)
  - "Edit" button per row (navigate to edit page)
  - "Delete" button per row (trigger confirmation modal)

**Delete Confirmation**:

- Bootstrap modal dialog
- Display: "Are you sure you want to delete [domain name]? This will also delete all associated subdomains."
- Buttons: "Cancel" and "Delete" (danger variant)
- On confirm: Delete domain and cascade delete subdomains, show success message

#### 7.1.2 Create Domain Page (`/domains/create`)

**URL**: `/domains/create`
**Purpose**: Form to add a new domain

**Form Fields**:

- Domain (text input)
- Registrar (dropdown from registrar.json)
- Nameserver (dropdown from nameserver.json)
- MX Records (dropdown from mx_records.json)
- Status (dropdown from status.json)
- Expiration Date (date picker, format: Mmm-DD-YYYY)
- Task (textarea)
- Remarks (textarea)

**Buttons**:

- "Save" (submit form, redirect to domains index with success message)
- "Cancel" (return to domains index without saving)

**Validation**: None required (as per requirements)

#### 7.1.3 Edit Domain Page (`/domains/{id}/edit`)

**URL**: `/domains/{id}/edit`
**Purpose**: Form to update an existing domain

**Form Fields**: Same as Create, pre-populated with existing data

**Buttons**:

- "Update" (submit form, redirect to domains index with success message)
- "Cancel" (return to domains index without saving)

**Validation**: None required

### 7.2 Subdomains Management

#### 7.2.1 Subdomains Index Page (`/subdomains`)

**URL**: `/subdomains`
**Purpose**: Display all subdomains in a searchable, sortable, paginated table

**Table Columns**:

1. Subdomain (display as "subdomain.domain" or "@.domain" for root)
2. Domain (parent domain name)
3. DNS Manager
4. Cloud Hosting
5. Project
6. IP Address
7. Status
8. Task
9. Remarks
10. Actions (Edit | Delete buttons)

**Features**:

- **Search/Filter**:
  - Search box for subdomain name
  - Dropdown filter for domain (list all domains)
  - Dropdown filters for: dns_manager, cloud_hosting, project, status
  - Filter submission applies all active filters
  - Clear filters option
- **Sorting**:
  - Click column headers to sort alphabetically (ascending/descending)
  - Default sort: subdomain name ascending
  - Sortable columns: Subdomain, Domain, DNS Manager, Cloud Hosting, Project, Status
- **Pagination**:
  - 100 records per page
  - Bootstrap pagination component
  - Display total record count
- **Actions**:
  - "Add New Subdomain" button (prominent, top-right)
  - "Edit" button per row (navigate to edit page)
  - "Delete" button per row (trigger confirmation modal)

**Delete Confirmation**:

- Bootstrap modal dialog
- Display: "Are you sure you want to delete [subdomain.domain]?"
- Buttons: "Cancel" and "Delete" (danger variant)
- On confirm: Delete subdomain, show success message

#### 7.2.2 Create Subdomain Page (`/subdomains/create`)

**URL**: `/subdomains/create`
**Purpose**: Form to add a new subdomain

**Form Fields**:

- Subdomain (text input, placeholder: "e.g., mail, portal, or @ for root")
- Domain (dropdown of all domains, required)
- DNS Manager (dropdown from dns_manager.json)
- Cloud Hosting (dropdown from cloud_hosting.json)
- Project (dropdown from project.json)
- IP Address (text input)
- Status (dropdown from status.json)
- Task (textarea)
- Remarks (textarea)

**Buttons**:

- "Save" (submit form, redirect to subdomains index with success message)
- "Cancel" (return to subdomains index without saving)

**Validation**: None required

#### 7.2.3 Edit Subdomain Page (`/subdomains/{id}/edit`)

**URL**: `/subdomains/{id}/edit`
**Purpose**: Form to update an existing subdomain

**Form Fields**: Same as Create, pre-populated with existing data

**Buttons**:

- "Update" (submit form, redirect to subdomains index with success message)
- "Cancel" (return to subdomains index without saving)

**Validation**: None required

------

## 8. Functional Requirements

### 8.1 Domain Management Functions

#### FR-1: View All Domains

- **Priority**: High
- **Description**: Display paginated list of all domains with filtering and sorting
- **Acceptance Criteria**:
  - Table displays all domain fields
  - Shows subdomain count for each domain
  - Search and filter work correctly
  - Column sorting works alphabetically
  - Pagination shows 100 records per page
  - Edit and Delete actions are available per row

#### FR-2: Create Domain

- **Priority**: High
- **Description**: Add a new domain to the database
- **Acceptance Criteria**:
  - Form displays all domain fields
  - Dropdowns populate from JSON files
  - Date picker formats date as Mmm-DD-YYYY
  - Save button creates domain and redirects to index
  - Cancel button returns to index without saving
  - Success message displayed after creation

#### FR-3: Edit Domain

- **Priority**: High
- **Description**: Update an existing domain's information
- **Acceptance Criteria**:
  - Form pre-populates with existing domain data
  - All fields can be modified
  - Update button saves changes and redirects to index
  - Cancel button returns to index without saving
  - Success message displayed after update

#### FR-4: Delete Domain

- **Priority**: High
- **Description**: Remove a domain and its associated subdomains
- **Acceptance Criteria**:
  - Delete button triggers confirmation modal
  - Modal displays domain name and cascade warning
  - Confirmation deletes domain and all subdomains
  - Cancel dismisses modal without deletion
  - Success message displayed after deletion
  - User redirected to domains index

### 8.2 Subdomain Management Functions

#### FR-5: View All Subdomains

- **Priority**: High
- **Description**: Display paginated list of all subdomains with filtering and sorting
- **Acceptance Criteria**:
  - Table displays all subdomain fields
  - Shows parent domain name
  - Search and filter work correctly
  - Column sorting works alphabetically
  - Pagination shows 100 records per page
  - Edit and Delete actions are available per row

#### FR-6: Create Subdomain

- **Priority**: High
- **Description**: Add a new subdomain to the database
- **Acceptance Criteria**:
  - Form displays all subdomain fields
  - Domain dropdown shows all available domains
  - Other dropdowns populate from JSON files
  - Save button creates subdomain and redirects to index
  - Cancel button returns to index without saving
  - Success message displayed after creation

#### FR-7: Edit Subdomain

- **Priority**: High
- **Description**: Update an existing subdomain's information
- **Acceptance Criteria**:
  - Form pre-populates with existing subdomain data
  - All fields can be modified
  - Update button saves changes and redirects to index
  - Cancel button returns to index without saving
  - Success message displayed after update

#### FR-8: Delete Subdomain

- **Priority**: High
- **Description**: Remove a subdomain
- **Acceptance Criteria**:
  - Delete button triggers confirmation modal
  - Modal displays full subdomain.domain name
  - Confirmation deletes subdomain only (not parent domain)
  - Cancel dismisses modal without deletion
  - Success message displayed after deletion
  - User redirected to subdomains index

------

## 9. Non-Functional Requirements

### 9.1 Performance

- Page load time: < 2 seconds for index pages with 100 records
- Search/filter response: < 1 second
- JSON files cached in memory for duration of request

### 9.2 Usability

- Intuitive navigation between Domains and Subdomains
- Clear visual feedback for actions (success/error messages)
- Responsive design works on desktop browsers
- Form fields clearly labeled

### 9.3 Maintainability

- Follow Laravel best practices and conventions
- Use Eloquent ORM for all database operations
- Blade components for reusable UI elements
- Clear separation of concerns (MVC pattern)

### 9.4 Security

- Authentication required for all pages
- CSRF protection on all forms
- SQL injection prevention via Eloquent ORM
- XSS protection via Blade escaping

------

## 10. Database Seeding

### 10.1 JSON Files Seeding

Create database seeder that generates dummy data for all JSON files with the sample data provided in section 4.3.

### 10.2 User Seeding

Create a default user:

- Email: admin@localhost
- Password: password (or configurable via .env)

### 10.3 Sample Data (Optional)

Optionally seed 10-20 sample domains and 30-50 sample subdomains for testing purposes.

------

## 11. Routing Structure

### 11.1 Web Routes

```
GET  /                          -> Redirect to /domains (requires auth)
GET  /login                     -> Login form
POST /login                     -> Process login
POST /logout                    -> Logout user

GET  /domains                   -> DomainController@index
GET  /domains/create            -> DomainController@create
POST /domains                   -> DomainController@store
GET  /domains/{id}/edit         -> DomainController@edit
PUT  /domains/{id}              -> DomainController@update
DELETE /domains/{id}            -> DomainController@destroy

GET  /subdomains                -> SubdomainController@index
GET  /subdomains/create         -> SubdomainController@create
POST /subdomains                -> SubdomainController@store
GET  /subdomains/{id}/edit      -> SubdomainController@edit
PUT  /subdomains/{id}           -> SubdomainController@update
DELETE /subdomains/{id}         -> SubdomainController@destroy
```

All routes except login should be protected by `auth` middleware.

------

## 12. Controller Specifications

### 12.1 DomainController

**Methods**:

- `index()`: Display domains list with search, filter, sort, pagination
- `create()`: Show create domain form
- `store(Request $request)`: Save new domain
- `edit($id)`: Show edit domain form
- `update(Request $request, $id)`: Update domain
- `destroy($id)`: Delete domain (cascade deletes subdomains)

**Additional Functions**:

- Load JSON data for dropdowns
- Handle query parameters for search/filter/sort
- Flash success messages

### 12.2 SubdomainController

**Methods**:

- `index()`: Display subdomains list with search, filter, sort, pagination
- `create()`: Show create subdomain form
- `store(Request $request)`: Save new subdomain
- `edit($id)`: Show edit subdomain form
- `update(Request $request, $id)`: Update subdomain
- `destroy($id)`: Delete subdomain

**Additional Functions**:

- Load JSON data for dropdowns
- Load domains list for dropdown
- Handle query parameters for search/filter/sort
- Flash success messages

------

## 13. View Specifications

### 13.1 Common Elements

#### Flash Messages

Display success/error messages at the top of content area using Bootstrap alerts:

- Success: `.alert-success`
- Error: `.alert-danger`
- Auto-dismiss after 5 seconds (optional)

#### Dropdowns

All dropdowns should:

- Include a default "Select..." option
- Allow clearing selection (except domain_id in subdomains)
- Populate from JSON files or database

#### Date Fields

- Use HTML5 date input (`<input type="date">`)
- Display format: Mmm-DD-YYYY
- Storage format: YYYY-MM-DD (database standard)

### 13.2 Table Views (Index Pages)

**Common Table Structure**:

```html
<div class="container mt-4">
    <!-- Flash messages -->

    <!-- Filter/Search Form -->
    <form method="GET" class="mb-3">
        <!-- Search and filter inputs -->
        <button type="submit" class="btn btn-primary">Filter</button>
        <a href="[current-route]" class="btn btn-secondary">Clear</a>
    </form>

    <!-- Add New Button -->
    <a href="[create-route]" class="btn btn-success mb-3">Add New [Entity]</a>

    <!-- Table -->
    <table class="table table-striped table-hover">
        <thead>
            <tr>
                <!-- Sortable column headers with up/down arrows -->
            </tr>
        </thead>
        <tbody>
            <!-- Data rows with Edit and Delete buttons -->
        </tbody>
    </table>

    <!-- Pagination -->
    {{ $items->links() }}

    <!-- Total count -->
    <p>Total: {{ $items->total() }} records</p>
</div>

<!-- Delete Confirmation Modal -->
```

### 13.3 Form Views (Create/Edit Pages)

**Common Form Structure**:

```html
<div class="container mt-4">
    <!-- Flash messages -->

    <h2>[Create/Edit] [Entity]</h2>

    <form method="POST" action="[store/update-route]">
        @csrf
        @method([POST/PUT])

        <!-- Form fields with labels and inputs -->

        <div class="mt-3">
            <button type="submit" class="btn btn-primary">[Save/Update]</button>
            <a href="[index-route]" class="btn btn-secondary">Cancel</a>
        </div>
    </form>
</div>
```

------

## 14. Implementation Phases

### Phase 1: Project Setup & Foundation

1. Initialize Laravel project
2. Configure SQLite database
3. Set up authentication (Laravel Breeze or manual)
4. Install and configure Bootstrap 5 (self-contained)
5. Create base layout with dark navbar
6. Create JSON configuration files with dummy data

### Phase 2: Database & Models

1. Create migrations for domains and subdomains tables
2. Create Domain and Subdomain models with relationships
3. Create database seeders (user, JSON files, optional sample data)
4. Run migrations and seeders

### Phase 3: Domains Management

1. Create DomainController with all CRUD methods
2. Create domain views (index, create, edit)
3. Implement search, filter, sort, pagination
4. Implement delete with confirmation modal
5. Add subdomain count to domains index
6. Test all domain operations

### Phase 4: Subdomains Management

1. Create SubdomainController with all CRUD methods
2. Create subdomain views (index, create, edit)
3. Implement search, filter, sort, pagination
4. Implement delete with confirmation modal
5. Test all subdomain operations
6. Test cascade delete from domains

### Phase 5: Testing & Refinement

1. Manual testing of all features
2. UI/UX refinement
3. Performance optimization
4. Documentation updates

------

## 15. Success Criteria

The application will be considered complete when:

1. User can log in with single account
2. User lands on Domains page after login
3. User can create, read, update, and delete domains
4. User can create, read, update, and delete subdomains
5. All dropdowns populate from JSON files in `resources/data/`
6. Domains table shows subdomain count
7. Delete operations show confirmation modals
8. Search and filter work on both index pages
9. Column sorting works alphabetically
10. Pagination displays 100 records per page
11. All forms have Cancel buttons that return to index
12. Success messages display after create/update/delete operations
13. Dates display in Mmm-DD-YYYY format
14. Dark navbar with navigation between Domains and Subdomains
15. Bootstrap 5 styling applied throughout
16. Application runs successfully on Herd.app
17. SQLite database persists data correctly
18. Cascade delete removes subdomains when domain is deleted

------

## 16. Out of Scope

The following items are explicitly **not** included in this project:

- User registration and management UI
- Password reset functionality
- Role-based access control
- Email notifications
- API endpoints
- Multi-tenancy
- Import/export functionality
- Automated domain expiration monitoring
- Integration with domain registrar APIs
- DNS record management beyond display
- SSL certificate tracking
- Domain valuation or analytics
- Mobile app
- Real-time notifications
- Audit logging
- Advanced reporting/dashboards
- Multi-language support
- Custom themes or dark mode toggle

------

## 17. Assumptions & Dependencies

### Assumptions

- Single user will manage the application locally
- User has direct database access for user management
- JSON files can be manually edited when needed
- No need for data backup/restore functionality within the app
- Domain and subdomain names are entered correctly by user
- User has basic knowledge of domain management concepts

### Dependencies

- Herd.app installed and running
- PHP version compatible with Laravel and Herd.app
- SQLite extension enabled in PHP
- Modern web browser (Chrome, Firefox, Safari, Edge)
- Bootstrap 5 files included in project

------

## 18. Glossary

- **Domain**: Root domain name (e.g., example.com)
- **Subdomain**: Prefix to domain (e.g., mail.example.com where "mail" is the subdomain)
- **Registrar**: Company where domain is registered
- **Nameserver**: DNS server managing domain's DNS records
- **MX Records**: Mail exchange records for email routing
- **DNS Manager**: Service used to manage DNS records
- **Cloud Hosting**: Hosting provider for the subdomain
- **Cascade Delete**: When a domain is deleted, all its subdomains are automatically deleted

------

## 19. Additional Notes for AI Coding Agents

### Code Style Guidelines

- Follow PSR-12 coding standards
- Use Laravel naming conventions (StudlyCase for classes, snake_case for methods)
- Use Eloquent ORM exclusively (no raw queries)
- Blade directives for all template logic
- Group use statements alphabetically

### Helper Considerations

- Create a helper function or service class to load JSON files
- Cache JSON data within request lifecycle
- Use Laravel's `collect()` for array manipulation
- Use route names instead of hardcoded URLs

### Error Handling

- No validation required, but handle missing JSON files gracefully
- Use try-catch for file operations
- Display user-friendly error messages

### Form Considerations

- Use `old()` helper to persist form input on errors
- CSRF tokens on all forms
- Method spoofing for PUT/DELETE operations

### Testing Recommendations

- Test cascade delete behavior thoroughly
- Test with large datasets (100+ records)
- Test all filter combinations
- Test sorting on each column
- Verify pagination calculations

### Bootstrap Components to Use

- `.navbar`, `.navbar-dark`, `.bg-dark`
- `.container`, `.container-fluid`
- `.table`, `.table-striped`, `.table-hover`
- `.btn`, `.btn-primary`, `.btn-secondary`, `.btn-success`, `.btn-danger`
- `.form-control`, `.form-select`, `.form-label`
- `.modal`, `.modal-dialog`, `.modal-content`
- `.alert`, `.alert-success`, `.alert-danger`
- `.pagination`

------

**Document Version**: 1.0
**Last Updated**: November 19, 2025
**Status**: Ready for Implementation