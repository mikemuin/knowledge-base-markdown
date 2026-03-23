# Multi-Domain Directory Platform with One Laravel App + One PostgreSQL Database

## Scenario

You need to run 3 public directories with separate domains:

1. Health Facilities
2. Preschools
3. Golf Repair Shops

And also support:

- one shared Laravel codebase
- one managed PostgreSQL database (DigitalOcean)
- one common admin panel for authenticated users
- full audit trail of changes

---

## Recommended Approach (High-Level)

Use a **single Laravel application** with **domain-based site context** and a **shared database schema**.

- Each public domain maps to a `site` record (`health`, `preschool`, `golf-repair`).
- The app resolves the current site from the incoming hostname.
- Public queries are automatically scoped by `site_id`.
- Admin panel can switch between sites (or show all, based on permission).
- Audit logs capture create/update/delete actions with actor + before/after values.

This is not full tenant-isolation SaaS; it is a **multi-site architecture** with controlled domain separation and shared operations.

---

## Why This Architecture Fits

### Strengths

- Lower infrastructure and maintenance overhead (one app, one DB, one CI/CD pipeline).
- Shared admin workflows and reusable features across directories.
- Faster delivery of cross-site features (search, moderation, reporting).
- Centralized security and observability.

### Trade-offs

- Strong discipline required for `site_id` scoping to prevent data leakage.
- One deployment impacts all sites.
- Schema changes must consider all three business domains.

---

## Key Concepts to Make This Work

## 1) Site Context Resolution (Domain → Site)

Create a `sites` table with one row per directory and map domains in `site_domains`.

- `healthfacilities.com` -> `site_id=1`
- `preschools.com` -> `site_id=2`
- `golfrepairshops.com` -> `site_id=3`

Add middleware, e.g. `ResolveCurrentSite`, that:

- reads `request()->getHost()`
- loads active site/domain mapping
- stores the resolved site in a request-scoped service (e.g. `CurrentSite`)

All public routes use this middleware.

## 2) Data Partitioning by `site_id`

For shared entities (`listings`, `categories`, `reviews`, `media`, etc.), include:

- `site_id` (indexed)
- unique constraints including `site_id` where needed (e.g. slug uniqueness per site)

Use global scopes or repository-level scoping so normal queries are always site-aware.

## 3) Modular Domain Logic

Use shared core modules + site-specific modules only where behavior diverges.

- Shared: listings lifecycle, moderation, search pipeline, media handling
- Site-specific: fields, validation rules, taxonomies, SEO templates

## 4) Common Admin Panel with RBAC

Single admin domain, e.g. `admin.directory-platform.com`.

Use permissions with optional site scope:

- Super Admin (all sites)
- Site Manager (one site)
- Editor/Reviewer (content workflow)

Recommended package: `spatie/laravel-permission`.

## 5) Audit Trail

Track all critical writes:

- actor (`user_id`)
- action (`created`, `updated`, `deleted`, `restored`, `published`)
- subject type/id
- `site_id`
- metadata (`ip`, `user_agent`)
- changed fields (before/after)

Recommended package: `spatie/laravel-activitylog` with custom enrichment for `site_id` and request metadata.

---

## Proposed Database Design (Core)

### Core Tables

- `sites` (`id`, `code`, `name`, `status`)
- `site_domains` (`id`, `site_id`, `domain`, `is_primary`, `status`)
- `users`
- `roles`, `permissions`, `model_has_roles` (Spatie)
- `listings` (`id`, `site_id`, `status`, `title`, `slug`, `payload`, ...)
- `categories` (`id`, `site_id`, `name`, `slug`)
- `reviews` (`id`, `site_id`, `listing_id`, ...)
- `activity_log` (audit table)

### Indexing / Constraints

- index: `(site_id, status)` on `listings`
- unique: `(site_id, slug)` on `listings`
- unique: `site_domains.domain`
- FK constraints from all scoped entities to `sites.id`

---

## Routing and Domain Setup

Use route groups by middleware instead of duplicating applications:

- Public routes: resolve site from host and scope queries.
- Admin routes: central domain + auth + permission middleware.

Example strategy:

- `Route::middleware(['resolve.site'])->group(base_path('routes/public.php'));`
- `Route::domain(config('app.admin_domain'))->middleware(['auth', 'verified'])->group(base_path('routes/admin.php'));`

On DigitalOcean:

- Point all 3 public domains and 1 admin domain to the same app load balancer/ingress.
- Configure TLS certificates for all domains.
- Ensure trusted proxy + correct host handling.

---

## Application Setup (Laravel)

## Packages

- `spatie/laravel-permission` for RBAC
- `spatie/laravel-activitylog` for audit trail
- optional: `spatie/laravel-data` for DTOs

## Configuration

- `config/sites.php` for site behavior toggles
- `config/audit.php` for audit retention and exclusions

## Middleware

- `ResolveCurrentSite`
- `EnsureUserCanAccessSite`

## Services

- `SiteResolverService`
- `ListingService`
- `AuditLogService` (if extending package behavior)

---

## Proposed Folder Setup

```text
app/
├── Domain/
│   ├── Shared/
│   │   ├── Models/
│   │   │   ├── Site.php
│   │   │   ├── SiteDomain.php
│   │   │   ├── Listing.php
│   │   │   └── Category.php
│   │   ├── Actions/
│   │   ├── Services/
│   │   ├── DTOs/
│   │   └── Policies/
│   ├── HealthFacilities/
│   │   ├── Actions/
│   │   ├── Rules/
│   │   └── Support/
│   ├── Preschools/
│   │   ├── Actions/
│   │   ├── Rules/
│   │   └── Support/
│   └── GolfRepairShops/
│       ├── Actions/
│       ├── Rules/
│       └── Support/
├── Http/
│   ├── Controllers/
│   │   ├── Public/
│   │   └── Admin/
│   ├── Middleware/
│   │   ├── ResolveCurrentSite.php
│   │   └── EnsureUserCanAccessSite.php
│   └── Requests/
├── Livewire/
│   ├── Admin/
│   └── Public/
└── Support/
    ├── SiteContext/
    └── Audit/

routes/
├── public.php
├── admin.php
└── api.php

database/
├── migrations/
│   ├── 2026_.._create_sites_table.php
│   ├── 2026_.._create_site_domains_table.php
│   ├── 2026_.._create_listings_table.php
│   └── ...
└── seeders/
    └── SiteSeeder.php
```

---

## Access Control Model

Use both role and site scope:

- `users`
- `roles`/`permissions`
- `user_site_access` pivot (`user_id`, `site_id`, `role`, `status`)

Authorization checks should validate:

1. user has permission for action
2. user is allowed for the current `site_id` (unless super admin)

---

## Audit Trail Strategy

Capture changes through model events or service-layer actions.

Minimum audit payload:

- `event` (created/updated/deleted/restored)
- `causer_id`
- `subject_type`, `subject_id`
- `site_id`
- `properties.old` / `properties.attributes`
- `ip_address`, `user_agent`, `request_id`

Retention policy:

- keep full logs for compliance period
- archive old logs to object storage if table growth becomes high

---

## Delivery Plan (Implementation Sequence)

1. Create `sites` + `site_domains` schema and seeding.
2. Implement `ResolveCurrentSite` middleware and site context service.
3. Add `site_id` to scoped domain tables + constraints/indexes.
4. Build shared listing CRUD with strict site scoping.
5. Build admin auth + RBAC + per-site access control.
6. Add audit logging and verify before/after capture.
7. Add site-specific validation/presentation modules.
8. Add integration tests for cross-site isolation.

---

## Critical Risk Controls

- Never query scoped models without `site_id` constraint.
- Add automated tests asserting cross-domain data isolation.
- Add DB-level unique constraints including `site_id`.
- Put destructive admin actions behind policy + audit + confirmation.

---

## Final Recommendation

Start with a **shared-schema, site-scoped monolith** in Laravel.
It gives the best balance of delivery speed, operational simplicity, and future scalability for 3 directories.

If one directory grows significantly, you can later split by module or move to service boundaries without rewriting the entire platform.

---

## Step Back: Better Approach If Starting From Scratch

If I ignore the initial constraints and optimize for long-term maintainability, I would run this as a **modular monolith with per-site bounded contexts**, not as three heavily customized copies under one app.

### What I Would Choose

- **One Laravel codebase** (still correct at this stage).
- **One PostgreSQL cluster**, but with **logical isolation strategy** from day one.
- **One admin app** with a true back-office workflow model.
- **Site-aware domain modules** with strict contracts.
- **Evented audit + outbox pattern** for reliable history and integrations.

This gives you strong delivery speed now, with a low-friction path to future service extraction.

---

## Architecture I Would Implement

## 1) Use "Brand" as a First-Class Domain Object

Instead of treating domains as just routing metadata, model them explicitly:

- `brands` (`health`, `preschool`, `golf_repair`)
- each content object belongs to one `brand_id`
- domain mappings resolve to `brand_id`

This keeps language business-focused (`brand`, `directory`) instead of technical (`tenant`, `site`) and makes policy/UX easier.

## 2) Enforce Isolation at Multiple Layers

I would enforce isolation in 4 places (defense in depth):

1. **Route context**: host -> brand resolution
2. **Application layer**: brand-scoped repositories/services only
3. **Database constraints**: composite uniqueness with `brand_id`
4. **Authorization**: user permissions constrained by allowed brands

If one layer is bypassed accidentally, others still protect data.

## 3) Prefer Feature Modules Over Layer-Only Structure

Instead of organizing mostly by framework layers, I would structure by feature domain:

- Directory Listings
- Taxonomy/Categories
- Reviews
- Media
- Search
- Back-office moderation

Each module owns its Actions, DTOs, Policies, Requests, and UI components.

## 4) Add Back-Office Workflow, Not Just CRUD

For directory products, operational workflow matters more than CRUD:

- Draft -> In Review -> Approved -> Published -> Archived
- Assignment queues
- Change requests
- Moderation notes

Model this explicitly with status transitions and transition policies.

## 5) Audit + Versioning Strategy

A plain activity log is useful, but I would add **record versioning for key entities**:

- `listing_revisions` table storing snapshot JSON + editor + reason
- ability to diff and revert
- activity log for who did what; revisions for what changed in content

For directory operations, this is much more practical than audit logs alone.

## 6) Search Architecture Early

Directories live or die by search quality. I would plan for:

- PostgreSQL full-text search initially (`tsvector`, weighted columns)
- later move to Meilisearch/Elasticsearch if needed
- per-brand ranking profiles (health vs preschool vs repair intent differs)

---

## Database Strategy I’d Use

### Option A (Recommended now): Shared Schema + `brand_id`

- fastest to deliver
- easiest operationally
- good until very high scale or strict compliance isolation

### Option B (Future-ready): Schema-per-Brand in same PostgreSQL cluster

- stronger logical isolation
- more migration complexity
- useful if one brand starts diverging heavily

I would begin with Option A, but keep code ready for Option B by avoiding hardcoded table names in domain services and centralizing repository queries.

---

## Folder Setup I’d Actually Start With

```text
app/
├── Modules/
│   ├── Directory/
│   │   ├── Domain/
│   │   │   ├── Models/
│   │   │   ├── Actions/
│   │   │   ├── Policies/
│   │   │   ├── Services/
│   │   │   └── DTOs/
│   │   ├── Application/
│   │   │   ├── Queries/
│   │   │   ├── Commands/
│   │   │   └── Workflows/
│   │   ├── Http/
│   │   │   ├── Controllers/
│   │   │   └── Requests/
│   │   └── Livewire/
│   ├── Reviews/
│   ├── Taxonomy/
│   ├── Search/
│   └── BackOffice/
├── Platform/
│   ├── BrandContext/
│   ├── Authorization/
│   ├── Auditing/
│   └── Support/
└── Http/
    └── Middleware/
        ├── ResolveBrandFromHost.php
        └── EnsureBrandAccess.php

routes/
├── web_public.php
├── web_admin.php
└── api.php
```

This keeps framework concerns secondary and business modules primary.

---

## Infra I’d Use on DigitalOcean

- **App Platform or Kubernetes**: one app service for now.
- **Managed PostgreSQL**: primary + PITR enabled.
- **Redis managed**: cache, queue, rate limits.
- **Object storage**: media + audit export/archive.
- **Queue workers**: dedicated process for indexing, emails, moderation events.
- **Centralized logs** + alerting from day one.

---

## Delivery Sequence I’d Follow

1. Brand context + routing + brand-scoped auth.
2. Core listing workflow (status machine + moderation).
3. Search + filter UX baseline.
4. Audit + revision history + diff view.
5. Brand-specific extensions (fields/templates/ranking).
6. Performance hardening and operational dashboards.

---

## Bottom Line

Yes, there is a better framing than just “3 domains on 1 app”: build a **brand-aware directory platform** with strict scoping, workflow-driven back office, and revision-capable auditing.

That gives you cleaner evolution paths:

- add a 4th/5th directory quickly
- split modules later if required
- keep one coherent operational admin for all brands

---

## Implementation Blueprint (Concrete, Buildable)

You are right to ask this. Here is the exact technical interpretation of “brand-aware directory platform”.

## 1) Data Isolation Choice (Use This First)

Use **shared tables** with a required `brand_id` column on brand-owned records.

Not separate databases, not separate table sets per brand (at first).

So:

- one `listings` table
- one `categories` table
- one `reviews` table
- every row tied to one `brand_id`

This is the most practical starting point for 3 directories.

## 2) Database Tables You Need

### Core Identity Tables

- `brands` (`id`, `code`, `name`, `status`, timestamps)
- `brand_domains` (`id`, `brand_id`, `domain`, `is_primary`, `status`, timestamps)

### Brand-Scoped Business Tables

- `listings` (`id`, `brand_id`, `title`, `slug`, `status`, `payload` jsonb, timestamps, softDeletes)
- `categories` (`id`, `brand_id`, `name`, `slug`, timestamps)
- `reviews` (`id`, `brand_id`, `listing_id`, `rating`, `comment`, timestamps)

### Access Control Tables

- `users`
- Spatie permission tables
- `user_brand_access` (`id`, `user_id`, `brand_id`, `role`, `status`, timestamps)

### Audit Tables

- `activity_log` (Spatie)
- optional: `listing_revisions` (`id`, `listing_id`, `brand_id`, `snapshot` jsonb, `reason`, `created_by`, timestamps)

## 3) Hard Constraints (Must-Have)

At DB level:

- unique `brand_domains.domain`
- unique `listings(brand_id, slug)`
- unique `categories(brand_id, slug)`
- FK `listings.brand_id -> brands.id`
- FK `reviews.brand_id -> brands.id`
- FK `reviews.listing_id -> listings.id`

And index these:

- `listings(brand_id, status)`
- `reviews(brand_id, listing_id)`

## 4) App Models (Yes, Separate Models; No, Separate Per-Brand Copies)

Create one model per entity, not one model per brand.

- `App\Domain\Shared\Models\Brand`
- `App\Domain\Shared\Models\BrandDomain`
- `App\Domain\Shared\Models\Listing`
- `App\Domain\Shared\Models\Category`
- `App\Domain\Shared\Models\Review`

Each brand-scoped model uses a shared trait, e.g. `BelongsToBrand`, which:

1. adds a global scope: `where brand_id = currentBrandId()`
2. auto-fills `brand_id` on create

That is how isolation is enforced at app layer.

## 5) Brand Context Resolution (Per Request)

Create middleware `ResolveBrandFromHost`:

1. read `request()->getHost()`
2. lookup in `brand_domains`
3. bind `CurrentBrand` service into container for that request
4. abort 404 if host is unknown/inactive

All public routes run behind this middleware.

## 6) Routing Structure

- Public domains: same code, host determines brand context.
- Admin domain: single host (`admin.yourplatform.com`) + auth.

Implementation pattern:

- `routes/web_public.php` -> middleware: `resolve.brand`
- `routes/web_admin.php` -> domain middleware + auth + permission

Admin users choose/switch active brand in UI (unless super admin viewing all).

## 7) Authorization (Brand-Scoped)

Use Spatie permissions for actions and `user_brand_access` for scope.

Policy checks should do both:

1. action permission (e.g. `listing.update`)
2. brand access (`user has access to listing.brand_id`)

Do not rely on role names alone.

## 8) Audit Trail (Implementable Pattern)

Use `spatie/laravel-activitylog` and enrich each activity with:

- `brand_id`
- request host
- ip/user-agent
- before/after attributes

For critical content, also store snapshots in `listing_revisions` for diff/rollback.

## 9) Where Brand-Specific Behavior Lives

Do **not** duplicate core models/tables.

Use brand-specific modules only for variation:

- validation rules (required fields differ)
- category taxonomy
- templates/content blocks
- search ranking profile

Core listing lifecycle remains shared.

## 10) Minimal Build Order (Directly Executable)

1. Create `brands`, `brand_domains`, `user_brand_access` migrations.
2. Add `brand_id` to `listings`, `categories`, `reviews` with indexes/uniques.
3. Seed 3 brands + domains.
4. Build `CurrentBrand` service + `ResolveBrandFromHost` middleware.
5. Build `BelongsToBrand` trait and apply to models.
6. Add brand-aware policies.
7. Add admin brand switcher + access checks.
8. Install/configure activity log and attach brand metadata.
9. Add integration tests for cross-brand isolation.

If these 9 steps are in place, the system is implementable and production-safe for your scenario.
