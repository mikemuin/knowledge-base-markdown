# Requirements

## General/ Setup

- Application Purpose:
  - Small personal database of domains that are owned, with details of their registrar, nameservers, status
  - Includes information of the subdomains per domain, with details of their nameserver, cloud hosting provider, IP address, project
- Basic Laravel Application
  - With basic user authentication. Single user only.
- Will run using Herd.app
  - Will run on local machine
- SQLite as database
- 100% Laravel. No other frameworks used for frontend.
  - Use "out-of-the-box" frontend libraries and features
- JSON files:
  - All list-of-values column will extract from a JSON file. This is for easy access to the local file folders for later editing.
  - All JSON configuration files must be stored in: `resources/data/`

## Data Model

- domains
  - id
  - domain
  - registrar (from a list of values)
  - nameserver (from a list of values)
  - mx_records (from a list of values)
  - status (from a list of values)
  - expiration_date
  - task (free text)
  - remarks (free text)
- subdomains
  - id
  - subdomain (free text)
    - just the item before root domain (mail, portal, beta)
    - can be used to monitor root (no subdomain, just @)
  - domain_id (dropdown )
  - dns_manager (from a list of values)
  - cloud_hosting (from a list of values)
  - project (from a list of values)
  - ip_address (free text)
  - status (from a list of values)
  - task (free text)
  - remarks (free text)
- Model Relationships
  - Domain `hasMany` Subdomain
  - Subdomain `belongsTo` Domain
  - Cascade delete: When a domain is deleted, all associated subdomains are automatically deleted

## Forms

- Data Management Forms for the following:
  - Add, Update, and Delete Domains
  - Add, Update, and Delete Subdomains
    - domain field is a dropdown of active domains from the domains table
  - No validations needed
- No need for user management forms. Since this is local, user management is direct to DB.

## Reports / Pages

- Table view of:
  - Domains
  - Subdomains
- Show all columns available on table view
- Add additional column for action buttons, such as edit and delete.
  - Delete requires confirmation dialogue.
  - Edit button leads to Edit Entry Form
- No need for pages or reports on user management

## Enhancements

- Use the default, embedded CSS libraries and designs of Laravel
- No need to customize or add additional CSS styles
- Authentication Requirements
  - Type: Single user authentication
  - Implementation: Laravel Breeze or manual auth implementation
  - User Management: Direct database manipulation (no UI for user management)
  - Login Required: All application pages require authentication
  - Session Management: Standard Laravel session handling
- User Table (Minimal)
  - Use Laravel's default users migration
  - Seed with one user account
  - No registration page needed
  - No password reset functionality required (local application)
- Access Control
  - No role-based access control needed
  - Authenticated user has full CRUD access to all features
  - Logout functionality in navbar
