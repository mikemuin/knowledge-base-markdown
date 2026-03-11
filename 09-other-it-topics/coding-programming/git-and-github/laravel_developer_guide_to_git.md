# Laravel Developer's Guide: Git, Versioning & CI/CD

**A Comprehensive Reference for Entry-Level Laravel Developers**

------

## Table of Contents

1. [Introduction](https://claude.ai/chat/e11061f7-a82b-4896-ac68-2a42bc958b42#introduction)
2. [Core Concepts](https://claude.ai/chat/e11061f7-a82b-4896-ac68-2a42bc958b42#core-concepts)
3. [Development Workflow](https://claude.ai/chat/e11061f7-a82b-4896-ac68-2a42bc958b42#development-workflow)
4. [Pull Requests & Code Review](https://claude.ai/chat/e11061f7-a82b-4896-ac68-2a42bc958b42#pull-requests--code-review)
5. [Release Management](https://claude.ai/chat/e11061f7-a82b-4896-ac68-2a42bc958b42#release-management)
6. [CI/CD Automation](https://claude.ai/chat/e11061f7-a82b-4896-ac68-2a42bc958b42#cicd-automation)
7. [Laravel-Specific Optimizations](https://claude.ai/chat/e11061f7-a82b-4896-ac68-2a42bc958b42#laravel-specific-optimizations)
8. [Troubleshooting & Common Pitfalls](https://claude.ai/chat/e11061f7-a82b-4896-ac68-2a42bc958b42#troubleshooting--common-pitfalls)
9. [Quick Reference](https://claude.ai/chat/e11061f7-a82b-4896-ac68-2a42bc958b42#quick-reference)

------

## Introduction

### Purpose of This Guide

This document serves as your foundational reference for working with Laravel applications in a professional, enterprise-grade environment. You'll learn how to:

- Manage your code effectively using Git and GitHub
- Collaborate safely with your team through proper branching strategies
- Create and deploy versioned releases
- Leverage automation to ensure code quality
- Follow industry best practices for software delivery

### Why This Matters

In professional software development, **stability** and **traceability** are paramount. We need to know:

- What code is currently running in production?
- Who made what changes, when, and why?
- Can we safely roll back to a previous version if something breaks?
- How do we ensure every change is tested before it reaches users?

This guide shows you the proven systems that answer these questions.

------

## Core Concepts

### 1. The Single Source of Truth

**Principle:** The `main` branch represents production-ready code at all times.

Think of `main` as the "gold standard" version of your application:

- It should always be stable and deployable
- All code in `main` has been reviewed and tested
- Direct commits to `main` are prohibited (enforced by branch protection)
- Production deployments always come from `main`

**Analogy:** If your code were a published book, `main` is the version on bookstore shelves. You wouldn't edit that directly—you'd work on a draft first.

------

### 2. Semantic Versioning (SemVer)

Semantic Versioning is a universal standard for numbering software releases. It uses a three-part number: `MAJOR.MINOR.PATCH`

#### Version Format: `X.Y.Z`

```
Example: v2.5.3
         │ │ │
         │ │ └─ PATCH: Bug fixes (2.5.3 → 2.5.4)
         │ └─── MINOR: New features, backward-compatible (2.5.0 → 2.6.0)
         └───── MAJOR: Breaking changes (2.0.0 → 3.0.0)
```

#### Decision Matrix

| Change Type                 | Impact       | Version Bump | Example       |
| --------------------------- | ------------ | ------------ | ------------- |
| Fixed typo in error message | None         | PATCH        | 1.0.0 → 1.0.1 |
| Added new API endpoint      | New feature  | MINOR        | 1.0.0 → 1.1.0 |
| Changed API response format | Breaking     | MAJOR        | 1.0.0 → 2.0.0 |
| Security vulnerability fix  | Critical fix | PATCH        | 1.2.3 → 1.2.4 |
| New optional parameter      | Enhancement  | MINOR        | 1.2.0 → 1.3.0 |

#### Real-World Example

```
Laravel Version History:
v10.0.0 - Major release with PHP 8.1 requirement (breaking)
v10.1.0 - Added new Artisan commands (new feature)
v10.1.5 - Fixed validation bug (bug fix)
v10.2.0 - Added Prompts feature (new feature)
```

------

### 3. Git Tags: Marking Official Releases

A **Git tag** is a permanent label attached to a specific commit. Unlike branches (which move as you add commits), tags are immutable markers.

#### Why Tags Matter

- **Traceability:** You can always find the exact code for any release
- **Deployment:** Production systems pull specific tagged versions
- **History:** Tags create a clear timeline of your product evolution
- **Rollback:** If v2.1.0 has a bug, you can instantly deploy v2.0.5

#### Tag Format Convention

```bash
v[MAJOR].[MINOR].[PATCH]

Examples:
v1.0.0    # First production release
v1.0.1    # First patch
v1.1.0    # First minor feature update
v2.0.0    # Major version with breaking changes
```

**The "v" prefix** is conventional but optional. Be consistent across your organization.

------

### 4. GitHub Container Registry (GHCR)

GHCR stores **Docker container images**—packaged versions of your application ready to deploy.

#### The Version Chain

```
Git Commit → Git Tag → Docker Image → GHCR → Production
   (code)   (v1.2.0)  (container)  (registry) (deployment)
```

#### Image Naming Convention

```
ghcr.io/[organization]/[application]:[version]

Example:
ghcr.io/acme-corp/customer-api:v1.2.0
        │          │              │
        │          │              └─ Matches Git tag exactly
        │          └──────────────── Your Laravel app name
        └─────────────────────────── Your GitHub organization
```

#### Multiple Tags Strategy

```
Single image, multiple tags:
ghcr.io/acme-corp/api:v1.2.0    # Specific version (production)
ghcr.io/acme-corp/api:v1.2      # Latest 1.2.x (convenience)
ghcr.io/acme-corp/api:v1        # Latest 1.x (convenience)
ghcr.io/acme-corp/api:latest    # Development/staging only
```

**Production Rule:** Always deploy using the full version tag (`v1.2.0`), never `latest`.

------

## Development Workflow

### The Feature Branch Workflow

This is the foundation of all team-based development. Every change—no matter how small—follows this pattern.

------

### Step-by-Step Process

#### Step 1: Start Fresh from `main`

Always begin with the latest stable code:

```bash
# Switch to main branch
git checkout main

# Get the latest changes from GitHub
git pull origin main
```

**Why?** This ensures your new work is based on the most recent stable code, preventing merge conflicts later.

------

#### Step 2: Create Your Feature Branch

```bash
# Create and switch to a new branch
git checkout -b feature/user-profile-page

# Alternative (two commands):
git branch feature/user-profile-page
git checkout feature/user-profile-page
```

#### Branch Naming Conventions

Follow these patterns for clarity:

```
feature/    - New functionality
feature/add-payment-gateway
feature/user-dashboard-redesign

bugfix/     - Fixing a bug
bugfix/login-timeout-error
bugfix/email-validation-issue

hotfix/     - Urgent production fix
hotfix/security-patch-sql-injection
hotfix/payment-processing-failure

refactor/   - Code improvement (no behavior change)
refactor/extract-payment-service
refactor/optimize-database-queries

chore/      - Maintenance tasks
chore/update-dependencies
chore/improve-documentation
```

**Best Practices:**

- Use lowercase with hyphens
- Be descriptive but concise
- Include ticket/issue number if applicable: `feature/user-auth-123`

------

#### Step 3: Develop and Test Locally

Now you're free to write code without affecting anyone else:

```bash
# Your Laravel development server runs the current branch
php artisan serve

# Or use your local environment (Sail, Valet, Homestead, etc.)
./vendor/bin/sail up
```

#### Understanding Branch Testing

**Critical Concept:** Your Laravel application runs whatever code is currently checked out in your working directory.

```bash
# Currently on feature branch
git branch
* feature/user-profile-page
  main

# Server uses feature branch code
php artisan serve
# Visit http://127.0.0.1:8000 - you see NEW features

# Switch to main
git checkout main

# Restart server - now uses main branch code
php artisan serve
# Visit http://127.0.0.1:8000 - NEW features are GONE
```

This lets you:

- Test your new feature in isolation
- Quickly verify the old version still works
- Compare behavior between branches

------

#### Step 4: Commit Your Work

Make small, logical commits with clear messages:

```bash
# Check what you've changed
git status

# Add files to staging
git add app/Http/Controllers/ProfileController.php
git add resources/views/profile/edit.blade.php
git add routes/web.php

# Or add everything (use with caution)
git add .

# Commit with a descriptive message
git commit -m "feat: add user profile editing functionality"
```

#### Commit Message Best Practices

Follow the **Conventional Commits** standard:

```
<type>(<scope>): <subject>

<body>

<footer>
```

**Common types:**

```bash
feat:     # New feature
git commit -m "feat: add password reset email functionality"

fix:      # Bug fix
git commit -m "fix: resolve validation error on user registration"

refactor: # Code restructuring
git commit -m "refactor: extract payment logic into service class"

test:     # Adding tests
git commit -m "test: add unit tests for user authentication"

docs:     # Documentation
git commit -m "docs: update API endpoint documentation"

chore:    # Maintenance
git commit -m "chore: update Laravel to v10.45.0"

perf:     # Performance improvement
git commit -m "perf: optimize database queries for user listing"
```

**Good commit messages:**

```
feat: add email verification for new users
fix: resolve null pointer exception in payment controller
refactor: extract validation logic into form request
perf: add database indexes for faster user lookups
```

**Bad commit messages:**

```
fixed stuff
wip
changes
asdfasdf
final version (no really this time)
```

------

#### Step 5: Push to GitHub

```bash
# First time pushing this branch
git push origin feature/user-profile-page

# On subsequent commits
git push
```

**Tip:** Push frequently, even if your work isn't finished. This:

- Backs up your code to the cloud
- Lets others see your progress (optional)
- Enables collaboration if you need help
- Triggers early CI checks

------

### Working with Multiple Features

You can have multiple feature branches and switch between them:

```bash
# Working on feature A
git checkout -b feature/payment-integration
# ... make changes ...
git add .
git commit -m "feat: add Stripe integration"

# Need to start feature B urgently
git checkout main
git checkout -b feature/urgent-bug-fix
# ... fix bug ...
git commit -m "fix: resolve login timeout issue"
git push origin feature/urgent-bug-fix

# Return to feature A
git checkout feature/payment-integration
# ... continue work ...
```

Each branch maintains its own isolated changes.

------

### Local Testing Strategies

#### Database Management for Feature Branches

When testing features that involve database changes:

```bash
# Create a fresh test database for your feature
php artisan migrate:fresh --seed

# Test your new migration
php artisan migrate

# Rollback if needed
php artisan migrate:rollback

# When switching to main, reset database
git checkout main
php artisan migrate:fresh --seed
```

**Pro Tip:** Use SQLite for faster local testing:

```env
# .env.testing
DB_CONNECTION=sqlite
DB_DATABASE=:memory:
```

------

## Pull Requests & Code Review

### What is a Pull Request (PR)?

A Pull Request is a formal request to merge your feature branch into `main`. It's much more than just merging code—it's:

- A discussion forum for your changes
- A quality gate with automated checks
- A knowledge-sharing opportunity
- A historical record of decisions

**Analogy:** Think of a PR like submitting a paper for peer review before publication.

------

### Creating a Pull Request

#### Step 1: Push Your Branch

```bash
# Ensure all your commits are pushed
git push origin feature/user-profile-page
```

#### Step 2: Open the PR on GitHub

1. Go to your repository on GitHub
2. You'll see a yellow banner: **"feature/user-profile-page had recent pushes"**
3. Click **"Compare & pull request"**

Alternatively:

1. Click **"Pull requests"** tab
2. Click **"New pull request"**
3. Set **base:** `main` ← **compare:** `feature/user-profile-page`

------

#### Step 3: Write a Comprehensive PR Description

A good PR description includes:

**Title:**

```
Add user profile editing functionality
```

**Description Template:**

```markdown
## Description
Implements the user profile editing feature allowing users to update their personal information.

## Changes Made
- Created ProfileController with edit/update actions
- Added profile edit form with validation
- Implemented profile photo upload with S3 storage
- Added unit and feature tests

## Related Issue
Closes #123

## Type of Change
- [x] New feature
- [ ] Bug fix
- [ ] Breaking change
- [ ] Documentation update

## Testing
- [x] Unit tests pass
- [x] Feature tests pass
- [x] Manually tested in local environment
- [x] Tested with various image formats (jpg, png, webp)

## Screenshots
[Include before/after screenshots if UI changes]

## Checklist
- [x] Code follows project style guidelines
- [x] Comments added for complex logic
- [x] Documentation updated
- [x] No console errors or warnings
- [x] Database migrations included
```

**Why detail matters:**

- Reviewers understand context immediately
- Future developers understand the "why" behind changes
- Reduces back-and-forth questions
- Creates searchable documentation

------

### The Automated Checks (CI Pipeline)

The moment you open a PR, GitHub Actions automatically runs your Continuous Integration pipeline.

#### What Gets Checked?

**1. Code Quality**

```bash
# PHP CodeSniffer - Style enforcement
composer run-script phpcs

# PHPStan - Static analysis
composer run-script phpstan

# PHP-CS-Fixer - Auto-formatting check
composer run-script php-cs-fixer -- --dry-run
```

**2. Automated Tests**

```bash
# Unit tests
php artisan test --testsuite=Unit

# Feature tests
php artisan test --testsuite=Feature

# Browser tests (Dusk)
php artisan dusk
```

**3. Security Scans**

```bash
# Dependency vulnerability check
composer audit

# SAST (Static Application Security Testing)
# Checks for common vulnerabilities
```

**4. Build Verification**

```bash
# Ensure application can build
composer install --no-dev --optimize-autoloader
npm ci
npm run build
```

#### The PR Status Display

GitHub shows all check results:

```
Code Style (phpcs)                     Passed
Static Analysis (phpstan)              Passed
Unit Tests                             Passed
Feature Tests                          Passed
Security Scan                          Passed
Build                                  Passed

All checks have passed                    6 successful checks
```

**Critical Rule:** A PR with failing checks **cannot be merged**. You must fix the issues first.

------

### Code Review Process

#### Requesting Reviewers

Assign 1-3 teammates with relevant expertise:

- **Senior developer:** Architectural decisions
- **Domain expert:** Business logic verification
- **Team lead:** Final approval

#### What Reviewers Check

**1. Correctness**

- Does the code do what it claims?
- Are edge cases handled?
- Is error handling robust?

**2. Quality**

- Is the code readable and maintainable?
- Are there code smells (duplicated logic, god objects)?
- Is it consistent with project patterns?

**3. Security**

- SQL injection risks?
- XSS vulnerabilities?
- Authentication/authorization properly enforced?
- Sensitive data properly handled?

**4. Performance**

- N+1 query problems?
- Inefficient algorithms?
- Missing database indexes?
- Caching opportunities?

**5. Testing**

- Are tests comprehensive?
- Do tests cover edge cases?
- Are tests maintainable?

#### Review Feedback Types

**1. Comments** - Questions or suggestions

```
"Could you explain the reasoning behind using a job here instead of
handling it synchronously?"
```

**2. Approve** - Code is good to merge

```
"Great work! The test coverage is excellent. Approved."
```

**3. Request Changes** - Must fix before merging

```
"This endpoint is missing authentication. Please add the auth middleware
before we can merge."
```

------

#### Responding to Review Feedback

**Step 1: Read all feedback carefully**

**Step 2: Make changes locally**

```bash
# You're still on your feature branch
git checkout feature/user-profile-page

# Make the requested changes in your editor

# Commit the changes
git add .
git commit -m "refactor: extract validation into form request per review feedback"

# Push updates
git push origin feature/user-profile-page
```

**Step 3: The PR updates automatically**

GitHub automatically:

- Adds your new commits to the PR
- Re-runs all CI checks
- Notifies reviewers of updates

**Step 4: Respond to comments**

```markdown
@reviewer Thanks for catching that! I've:
- Extracted validation into UpdateProfileRequest
- Added missing test coverage for validation rules
- Updated the controller to be more concise

Ready for another look.
```

**Step 5: Request re-review**

Click **"Re-request review"** button to notify reviewers.

------

### Protected Branch Rules

The `main` branch has strict protections enforced by GitHub:

#### Common Protection Rules

**1. Require pull request reviews**

```
Require 2 approving reviews
Dismiss stale reviews when new commits are pushed
Require review from code owners
```

**2. Require status checks**

```
Require branches to be up to date before merging
Required status checks:
   - Code Style
   - Static Analysis
   - Unit Tests
   - Feature Tests
```

**3. Enforce for administrators**

```
Include administrators (no one gets special treatment)
```

**4. Require linear history**

```
Prevent merge commits
Require "Squash and merge" or "Rebase and merge"
```

**What this means for you:**

- You **cannot** force push to `main`
- You **cannot** bypass CI checks
- You **cannot** merge without approval
- Everyone follows the same rules

------

### Merging Strategies

Once your PR is approved and all checks pass, you have merge options:

#### 1. Squash and Merge (Recommended)

**What it does:**

- Combines all your feature branch commits into a single commit
- Creates a clean, linear history on `main`

**When to use:**

- Default choice for most features
- When your feature branch has many small commits
- When commits are messy ("wip", "fix typo", etc.)

**Result:**

```
Before merge (feature branch):
- feat: add profile controller
- wip
- fix typo
- add tests
- fix tests
- final touches

After merge (main):
- feat: add user profile editing (#123)
```

#### 2. Rebase and Merge

**What it does:**

- Replays your commits on top of `main`
- Keeps all individual commits
- Maintains linear history

**When to use:**

- When your commits are clean and meaningful
- When each commit represents a logical step
- When you want detailed history

**Result:**

```
All your feature commits appear in main as-is
```

#### 3. Create a Merge Commit (Avoid)

**What it does:**

- Creates a merge commit showing the branch merge
- Preserves branch structure in history

**When to use:**

- Rarely; creates cluttered history
- Only for very special cases

------

### Post-Merge Cleanup

After your PR is merged:

**1. Delete the remote branch**

```bash
# GitHub offers a button: "Delete branch"
# Or manually:
git push origin --delete feature/user-profile-page
```

**2. Update your local repository**

```bash
# Switch back to main
git checkout main

# Get the merged changes
git pull origin main

# Delete your local feature branch
git branch -d feature/user-profile-page
```

**3. Verify the merge**

```bash
# Check that main contains your changes
git log --oneline -10

# You should see your merged commit
```

------

## Release Management

### When to Create a Release

Releases are created when you have a collection of changes ready to deploy to production.

#### Release Cadence Examples

**Time-based:**

- **Daily:** Fast-moving SaaS products
- **Weekly:** Standard web applications
- **Bi-weekly:** Enterprise applications
- **Monthly:** Complex systems requiring extensive testing

**Feature-based:**

- Release when a milestone is complete
- Release when a critical bug is fixed
- Release when a customer-requested feature is ready

**Your team decides the cadence.** Consistency matters more than frequency.

------

### Creating a Release

#### Pre-Release Checklist

Before tagging a release, verify:

```bash
All desired features are merged into main
All CI checks pass on main
Staging environment testing complete
Documentation updated
CHANGELOG.md updated
Database migrations tested
Rollback plan prepared
```

------

#### Step 1: Determine the Version Number

Use SemVer rules:

**Example Decision Process:**

Current version: `v1.4.2`

**Changes since last release:**

- Fixed email validation bug
- Added user profile editing feature
- Added password reset feature
- Fixed avatar upload bug

**Analysis:**

- 2 bug fixes → PATCH
- 2 new features → MINOR
- No breaking changes → No MAJOR bump

**Next version:** `v1.5.0` (MINOR bump)

------

#### Step 2: Update CHANGELOG.md

```markdown
# Changelog

## [1.5.0] - 2025-11-22

### Added
- User profile editing functionality
- Password reset via email
- Profile avatar upload with S3 storage

### Fixed
- Email validation now properly handles plus addressing
- Avatar upload now respects maximum file size limit

### Changed
- Improved validation error messages for better UX

## [1.4.2] - 2025-11-15

### Fixed
- Login session timeout issue
- Database connection pool exhaustion
```

------

#### Step 3: Create the Git Tag

```bash
# Ensure you're on main with latest changes
git checkout main
git pull origin main

# Create annotated tag with message
git tag -a v1.5.0 -m "Release version 1.5.0: User profile management"

# View the tag
git show v1.5.0

# Push tag to GitHub (this triggers CI/CD)
git push origin v1.5.0
```

**Annotated vs Lightweight Tags:**

```bash
# Annotated (recommended) - includes metadata
git tag -a v1.5.0 -m "Release version 1.5.0"

# Lightweight (not recommended) - just a pointer
git tag v1.5.0
```

Always use annotated tags for releases. They store:

- Tagger name and email
- Date
- Message
- Can be GPG signed

------

#### Step 4: The Automated Release Process

When you push the tag, GitHub Actions triggers the **Release & Deploy** workflow:

```yaml
# .github/workflows/release.yml
name: Release & Deploy

on:
  push:
    tags:
      - 'v[0-9]+.[0-9]+.[0-9]+'  # Matches v1.0.0, v2.5.3, etc.
```

**What happens automatically:**

**1. Checkout code**

```bash
# GitHub Actions checks out the exact commit the tag points to
git checkout v1.5.0
```

**2. Run full test suite**

```bash
composer install
php artisan test
php artisan dusk
```

**3. Build production assets**

```bash
npm ci
npm run build
composer install --no-dev --optimize-autoloader
```

**4. Build Docker image**

```bash
docker build -t ghcr.io/acme-corp/app:v1.5.0 .
```

**5. Tag the image**

```bash
# Specific version
docker tag ghcr.io/acme-corp/app:v1.5.0

# Also tag as latest
docker tag ghcr.io/acme-corp/app:latest

# Also tag as minor version
docker tag ghcr.io/acme-corp/app:v1.5
```

**6. Push to GHCR**

```bash
docker push ghcr.io/acme-corp/app:v1.5.0
docker push ghcr.io/acme-corp/app:latest
docker push ghcr.io/acme-corp/app:v1.5
```

**7. Create GitHub Release**

- Auto-generates release notes from merged PRs
- Attaches build artifacts if configured
- Publishes release on GitHub

------

### The Complete Traceability Chain

This system creates perfect traceability:

```
Production Issue
    ↓
Check container tag: ghcr.io/acme-corp/app:v1.5.0
    ↓
Find Git tag: v1.5.0
    ↓
View tagged commit: abc123def
    ↓
See exact code that was deployed
    ↓
View related PRs and discussions
    ↓
Identify who wrote the code and why
```

**Real-world benefit:** When a bug appears in production, you can:

1. Check which version is running (30 seconds)
2. Find the exact source code (1 minute)
3. Identify the PR that introduced the issue (2 minutes)
4. Understand the context and reasoning (5 minutes)
5. Prepare a hotfix (10 minutes)

**Total time to identify and fix: ~15-20 minutes instead of hours of detective work.**

------

### Emergency Hotfix Process

When a critical bug is discovered in production:

#### Step 1: Create hotfix branch from the problematic tag

```bash
# Create branch from the production version
git checkout v1.5.0
git checkout -b hotfix/critical-security-patch

# Make the minimal fix
# ... edit files ...

# Commit
git commit -m "fix: patch SQL injection vulnerability in user search"

# Push
git push origin hotfix/critical-security-patch
```

#### Step 2: Fast-track PR

```markdown
CRITICAL SECURITY HOTFIX

## Issue
SQL injection vulnerability in user search endpoint

## Impact
HIGH - User data at risk

## Fix
Properly parameterize SQL query using Eloquent

## Testing
- [x] Verified fix locally
- [x] Confirmed no injection possible
- [x] Existing tests pass

Request expedited review from @security-team
```

#### Step 3: Emergency merge and release

```bash
# After approval, merge to main
# Then immediately create hotfix release
git checkout main
git pull origin main
git tag -a v1.5.1 -m "Hotfix: Security patch for SQL injection"
git push origin v1.5.1
```

#### Step 4: Deploy immediately

The hotfix tag triggers the release pipeline, and the patched version is deployed to production within minutes.

------

### Version Management Best Practices

#### Keep Versions Moving Forward

```bash
v1.5.0 → v1.5.1 → v1.6.0
v1.5.0 → v1.4.9 (going backward)
```

#### Never Re-tag

```bash
Never do this:
git tag -d v1.5.0                    # Delete tag
git tag -a v1.5.0 -m "Different content"  # Re-create tag

This breaks traceability and can cause deployment issues
```

If you tagged the wrong commit:

- Create a new version (v1.5.1)
- Document the mistake in CHANGELOG

#### Pre-release Versions

For testing releases before production:

```bash
# Alpha releases (very unstable)
v2.0.0-alpha.1
v2.0.0-alpha.2

# Beta releases (feature complete, needs testing)
v2.0.0-beta.1
v2.0.0-beta.2

# Release candidates (potentially final)
v2.0.0-rc.1
v2.0.0-rc.2

# Final release
v2.0.0
```

------

## CI/CD Automation

### Understanding CI vs CD

**Continuous Integration (CI)**

- Runs on every commit/PR
- Validates code quality
- Runs automated tests
- Catches bugs early
- Fast feedback (minutes)

**Continuous Delivery (CD)**

- Runs on version tags
- Builds production artifacts
- Publishes to registries
- Deploys to environments
- Slower, more comprehensive (10-30 minutes)

------

### GitHub Actions Workflow Structure

#### Anatomy of a Workflow File

```yaml
# .github/workflows/ci.yml

# Workflow name (shown in GitHub UI)
name: Continuous Integration

# When to run this workflow
on:
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]

# Environment-wide settings
env:
  PHP_VERSION: '8.2'

# Individual jobs (run in parallel by default)
jobs:
  # Job 1: Code Quality
  code-quality:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Setup PHP
        uses: shivammathur/setup-php@v2
        with:
          php-version: ${{ env.PHP_VERSION }}
          extensions: mbstring, xml, ctype, json, bcmath

      - name: Install dependencies
        run: composer install --prefer-dist --no-progress

      - name: Run PHP CodeSniffer
        run: composer run-script phpcs

      - name: Run PHPStan
        run: composer run-script phpstan

  # Job 2: Tests (depends on code-quality passing)
  tests:
    runs-on: ubuntu-latest
    needs: code-quality  # Wait for code-quality to pass

    services:
      mysql:
        image: mysql:8.0
        env:
          MYSQL_ROOT_PASSWORD: password
          MYSQL_DATABASE: testing
        ports:
          - 3306:3306

    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Setup PHP
        uses: shivammathur/setup-php@v2
        with:
          php-version: ${{ env.PHP_VERSION }}

      - name: Install dependencies
        run: composer install

      - name: Prepare Laravel
        run: |
          cp .env.ci .env
          php artisan key:generate
          php artisan migrate --force

      - name: Run tests
        run: php artisan test
```

------

### Trigger Patterns

#### 1. Push to Branches

```yaml
on:
  push:
    branches:
      - main
      - develop
      - 'release/**'  # Matches release/v1, release/v2, etc.
```

**Use case:** Run CI on every commit to important branches

#### 2. Pull Requests

```yaml
on:
  pull_request:
    branches: [ main ]
    types: [ opened, synchronize, reopened ]
```

**Use case:** Validate all PRs before they can be merged

#### 3. Version Tags (Release)

```yaml
on:
  push:
    tags:
      - 'v[0-9]+.[0-9]+.[0-9]+'  # Matches v1.0.0, v10.5.23, etc.
```

**Use case:** Build and deploy only on official releases

#### 4. Path Filters

```yaml
on:
  push:
    paths:
      - 'app/**'              # PHP code
      - 'composer.json'       # Dependencies
      - 'composer.lock'
      - 'tests/**'            # Tests
      - '.github/workflows/**' # CI config
    paths-ignore:
      - 'docs/**'             # Documentation
      - 'README.md'
      - '**.md'
```

**Use case:** Skip expensive CI when only docs change

#### 5. Manual Trigger

```yaml
on:
  workflow_dispatch:  # Adds "Run workflow" button in GitHub UI
```

**Use case:** Allow manual re-runs or emergency deployments

#### 6. Scheduled

```yaml
on:
  schedule:
    - cron: '0 2 * * *'  # 2 AM UTC daily
```

**Use case:** Nightly builds, dependency updates, cleanup tasks

------

### Conditional Job Execution

Run different jobs based on conditions:

```yaml
jobs:
  deploy-staging:
    if: github.ref == 'refs/heads/develop'
    runs-on: ubuntu-latest
    steps:
      - name: Deploy to staging
        run: ./deploy-staging.sh

  deploy-production:
    if: startsWith(github.ref, 'refs/tags/v')
    runs-on: ubuntu-latest
    steps:
      - name: Deploy to production
        run: ./deploy-production.sh
```

------

## Laravel-Specific Optimizations

### Why Optimization Matters

**Unoptimized CI:**

- 15-30 minutes per PR
- Developers lose focus waiting
- Slows down the entire team

**Optimized CI:**

- 3-5 minutes per PR
- Fast feedback loop
- Developers stay productive

------

### 1. Caching Dependencies

#### The Problem

Every CI run starts from scratch:

```bash
# Without caching (slow):
1. Download Composer            30s
2. composer install             120s
3. Download npm                 20s
4. npm install                  180s
5. Run tests                    60s
                        Total: 410s (6.8 minutes)
```

#### The Solution: Cache Everything

```yaml
jobs:
  test:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      # Cache Composer dependencies
      - name: Cache Composer dependencies
        uses: actions/cache@v4
        with:
          path: vendor
          key: ${{ runner.os }}-composer-${{ hashFiles('**/composer.lock') }}
          restore-keys: |
            ${{ runner.os }}-composer-

      # Cache npm dependencies
      - name: Cache npm dependencies
        uses: actions/cache@v4
        with:
          path: ~/.npm
          key: ${{ runner.os }}-npm-${{ hashFiles('**/package-lock.json') }}
          restore-keys: |
            ${{ runner.os }}-npm-

      - name: Install Composer dependencies
        run: |
          if [ ! -d "vendor" ]; then
            composer install --no-progress --prefer-dist
          else
            echo "Using cached vendor directory"
          fi

      - name: Install npm dependencies
        run: npm ci --cache ~/.npm --prefer-offline
```

**Result:**

```bash
# With caching (fast):
1. Restore Composer cache        5s
2. Restore npm cache              3s
3. Run tests                      60s
                         Total: 68s (1.1 minutes)

Speedup: 6x faster! 🚀
```

------

### 2. Parallel Testing with Matrix Strategy

#### The Problem

Running all tests sequentially is slow:

```bash
Unit tests:     120s
Feature tests:  180s
Browser tests:  240s
              ------
Total:         540s (9 minutes)
```

#### The Solution: Run in Parallel

```yaml
jobs:
  test:
    runs-on: ubuntu-latest

    strategy:
      fail-fast: false  # Don't cancel other jobs if one fails
      matrix:
        test-suite: [Unit, Feature, Browser]
        php-version: ['8.1', '8.2', '8.3']

    name: Tests (${{ matrix.test-suite }}, PHP ${{ matrix.php-version }})

    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Setup PHP
        uses: shivammathur/setup-php@v2
        with:
          php-version: ${{ matrix.php-version }}

      - name: Run tests
        run: php artisan test --testsuite=${{ matrix.test-suite }}
```

**Result:**

```bash
# 9 parallel jobs run simultaneously:
Job 1: Unit + PHP 8.1       → 120s
Job 2: Unit + PHP 8.2       → 120s
Job 3: Unit + PHP 8.3       → 120s
Job 4: Feature + PHP 8.1    → 180s
Job 5: Feature + PHP 8.2    → 180s
Job 6: Feature + PHP 8.3    → 180s
Job 7: Browser + PHP 8.1    → 240s
Job 8: Browser + PHP 8.2    → 240s
Job 9: Browser + PHP 8.3    → 240s

Total wall-clock time: 240s (4 minutes)

Speedup: 2.25x faster! 🚀
```

------

### 3. Database Optimization

#### Use Service Containers

Don't install MySQL on the runner—use Docker containers:

```yaml
services:
  mysql:
    image: mysql:8.0
    env:
      MYSQL_ROOT_PASSWORD: password
      MYSQL_DATABASE: testing
    ports:
      - 3306:3306
    options: >-
      --health-cmd="mysqladmin ping"
      --health-interval=10s
      --health-timeout=5s
      --health-retries=3

  redis:
    image: redis:7-alpine
    ports:
      - 6379:6379
    options: >-
      --health-cmd="redis-cli ping"
      --health-interval=10s
      --health-timeout=5s
      --health-retries=3
```

#### Use SQLite for Fast Tests

For tests that don't need MySQL-specific features:

```php
// phpunit.xml
<php>
    <env name="DB_CONNECTION" value="sqlite"/>
    <env name="DB_DATABASE" value=":memory:"/>
</php>
```

**Benefit:** In-memory SQLite is 5-10x faster than MySQL for simple tests.

------

### 4. Fail Fast Strategy

#### The Problem

Running slow checks first wastes time:

```bash
1. Browser tests (slow)        240s
2. Feature tests (medium)      180s
3. Unit tests (fast)           120s
4. Code style (very fast)       10s  ← Fails here!

Wasted time: 540s before seeing the real problem
```

#### The Solution: Run Fast Checks First

```yaml
jobs:
  # Job 1: Lightning-fast checks (fail fast)
  lint:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Setup PHP
        uses: shivammathur/setup-php@v2
        with:
          php-version: '8.2'
      - name: Check code style
        run: composer run-script phpcs
      - name: Run static analysis
        run: composer run-script phpstan
    # If this fails, all other jobs are cancelled automatically

  # Job 2: Fast tests
  unit-tests:
    needs: lint  # Only runs if lint passes
    runs-on: ubuntu-latest
    steps:
      - name: Run unit tests
        run: php artisan test --testsuite=Unit

  # Job 3: Slower tests
  feature-tests:
    needs: unit-tests
    runs-on: ubuntu-latest
    steps:
      - name: Run feature tests
        run: php artisan test --testsuite=Feature

  # Job 4: Slowest tests
  browser-tests:
    needs: feature-tests
    runs-on: ubuntu-latest
    steps:
      - name: Run browser tests
        run: php artisan dusk
```

**Result:**

```bash
Style check fails at 10s
→ All other jobs cancelled immediately
→ Developer gets feedback in 10s instead of 9 minutes

Speedup: 54x faster feedback on common errors! 🚀
```

------

### 5. Optimize Composer Install

```bash
# Slow (default)
composer install

# Fast (production-ready, no dev dependencies)
composer install --no-dev --prefer-dist --optimize-autoloader --no-interaction

# Even faster (with cache)
composer install --prefer-dist --optimize-autoloader --no-interaction
```

**Flags explained:**

- `--prefer-dist`: Download zip archives instead of cloning git repos (faster)
- `--optimize-autoloader`: Generate optimized class loader (faster at runtime)
- `--no-interaction`: Don't ask questions (CI environment)
- `--no-dev`: Skip dev dependencies like PHPUnit (use for production builds only)

------

### 6. Optimize Asset Building

```yaml
- name: Build assets
  run: |
    npm ci --prefer-offline --no-audit
    npm run build
```

**Flags explained:**

- `npm ci`: Clean install (faster and more reliable than `npm install`)
- `--prefer-offline`: Use cache when possible
- `--no-audit`: Skip security audit (run separately in a scheduled job)

------

### Complete Optimized Laravel CI Workflow

```yaml
name: Laravel CI

on:
  pull_request:
    branches: [ main ]
  push:
    branches: [ main ]
    paths:
      - 'app/**'
      - 'config/**'
      - 'database/**'
      - 'resources/**'
      - 'routes/**'
      - 'tests/**'
      - 'composer.json'
      - 'composer.lock'

env:
  PHP_VERSION: '8.2'

jobs:
  # Stage 1: Fast checks (10-20 seconds)
  lint:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Setup PHP
        uses: shivammathur/setup-php@v2
        with:
          php-version: ${{ env.PHP_VERSION }}
          extensions: mbstring, xml, ctype, json
          coverage: none

      - name: Cache Composer
        uses: actions/cache@v4
        with:
          path: vendor
          key: composer-${{ hashFiles('composer.lock') }}

      - name: Install dependencies
        run: composer install --prefer-dist --no-progress

      - name: Code style
        run: composer run-script phpcs

      - name: Static analysis
        run: composer run-script phpstan

  # Stage 2: Unit tests (1-2 minutes)
  unit-tests:
    needs: lint
    runs-on: ubuntu-latest

    strategy:
      matrix:
        php: ['8.1', '8.2', '8.3']

    steps:
      - uses: actions/checkout@v4

      - name: Setup PHP
        uses: shivammathur/setup-php@v2
        with:
          php-version: ${{ matrix.php }}
          coverage: pcov

      - name: Cache Composer
        uses: actions/cache@v4
        with:
          path: vendor
          key: composer-${{ matrix.php }}-${{ hashFiles('composer.lock') }}

      - name: Install dependencies
        run: composer install --prefer-dist

      - name: Run unit tests
        run: php artisan test --testsuite=Unit --coverage-clover coverage.xml

      - name: Upload coverage
        uses: codecov/codecov-action@v3
        with:
          files: ./coverage.xml

  # Stage 3: Integration tests (2-4 minutes)
  integration-tests:
    needs: unit-tests
    runs-on: ubuntu-latest

    services:
      mysql:
        image: mysql:8.0
        env:
          MYSQL_ROOT_PASSWORD: password
          MYSQL_DATABASE: testing
        ports:
          - 3306:3306
        options: --health-cmd="mysqladmin ping" --health-interval=10s

      redis:
        image: redis:alpine
        ports:
          - 6379:6379
        options: --health-cmd="redis-cli ping" --health-interval=10s

    steps:
      - uses: actions/checkout@v4

      - name: Setup PHP
        uses: shivammathur/setup-php@v2
        with:
          php-version: ${{ env.PHP_VERSION }}

      - name: Cache Composer
        uses: actions/cache@v4
        with:
          path: vendor
          key: composer-${{ hashFiles('composer.lock') }}

      - name: Install dependencies
        run: composer install --prefer-dist

      - name: Prepare Laravel
        run: |
          cp .env.ci .env
          php artisan key:generate
          php artisan migrate --force

      - name: Run feature tests
        run: php artisan test --testsuite=Feature --parallel
        env:
          DB_HOST: 127.0.0.1
          DB_PORT: 3306
          REDIS_HOST: 127.0.0.1
          REDIS_PORT: 6379
```

**Performance Summary:**

```
Unoptimized: 15-20 minutes
Optimized:   3-5 minutes

Productivity gain:
- 50 PRs per week
- Time saved: (15 min - 4 min) × 50 = 550 minutes/week
- That's 9+ hours saved every week for your team!
```

------

## Troubleshooting & Common Pitfalls

### Common Problems and Solutions

#### 1. "Failed to push to remote"

**Error:**

```bash
! [rejected]        main -> main (fetch first)
error: failed to push some refs to 'origin'
```

**Cause:** Someone else pushed to `main` while you were working

**Solution:**

```bash
# Pull the changes first
git pull origin main

# Resolve any conflicts in your editor
# Then commit the merge
git add .
git commit -m "merge: resolve conflicts"

# Now push
git push origin main
```

**Better Solution:** Use feature branches and PRs (you'll never push directly to `main`)

------

#### 2. "Merge Conflicts"

**Error:**

```bash
CONFLICT (content): Merge conflict in app/Http/Controllers/UserController.php
Automatic merge failed; fix conflicts and then commit the result.
```

**What you see in the file:**

```php
<<<<<<< HEAD (your changes)
public function store(Request $request)
{
    $user = User::create($request->validated());
=======
public function store(StoreUserRequest $request)
{
    $user = User::create($request->all());
>>>>>>> main (their changes)
}
```

**Solution:**

```php
// 1. Decide which version to keep (or combine them)
public function store(StoreUserRequest $request)
{
    $user = User::create($request->validated());
}

// 2. Remove the conflict markers
// 3. Stage the resolved file
git add app/Http/Controllers/UserController.php

// 4. Complete the merge
git commit -m "merge: resolve UserController conflicts"
```

**Pro Tip:** Keep your feature branches short-lived to minimize conflicts.

------

#### 3. "CI Failing on Dependencies"

**Error:**

```bash
composer install failed
Your requirements could not be resolved to an installable set of packages.
```

**Cause:** Someone updated `composer.json` without updating `composer.lock`

**Solution:**

```bash
# Update the lock file locally
composer update --lock

# Commit the updated lock file
git add composer.lock
git commit -m "chore: update composer.lock"
git push
```

------

#### 4. "Tests Pass Locally But Fail in CI"

**Common causes:**

**a) Database state issues**

```php
// Bad (test depends on previous test's data)
public function test_user_can_login()
{
    $user = User::first(); // Assumes user exists
}

// Good (test is self-contained)
public function test_user_can_login()
{
    $user = User::factory()->create();
}
```

**b) Environment differences**

```php
// Check your .env.ci file matches test requirements
// Common issues:
- Wrong database credentials
- Missing Redis connection
- Missing API keys for test mode
```

**c) Timezone issues**

```php
// Bad (depends on server timezone)
$date = Carbon::now();

// Good (explicit timezone)
$date = Carbon::now('UTC');
```

------

#### 5. "Accidentally Committed Secrets"

**Error:** You committed `.env` file or API keys

**Solution (if not pushed yet):**

```bash
# Remove file from git but keep it locally
git rm --cached .env

# Add .env to .gitignore if not already there
echo ".env" >> .gitignore

# Amend the commit
git add .gitignore
git commit --amend -m "chore: add .gitignore (remove .env)"
```

**Solution (if already pushed):**

```bash
# 1. Immediately rotate all exposed secrets!
# 2. Remove from history (nuclear option)
git filter-branch --force --index-filter \
  "git rm --cached --ignore-unmatch .env" \
  --prune-empty --tag-name-filter cat -- --all

# 3. Force push (requires admin rights)
git push origin --force --all
```

**Prevention:**

```bash
# Add .env to .gitignore FIRST
echo ".env" >> .gitignore
git add .gitignore
git commit -m "chore: add .gitignore"
```

------

#### 6. "Wrong Branch Tagged"

**Error:** You tagged `feature/xyz` instead of `main`

**Solution:**

```bash
# Delete the local tag
git tag -d v1.5.0

# Delete the remote tag
git push origin --delete v1.5.0

# Checkout main
git checkout main
git pull

# Create tag on correct branch
git tag -a v1.5.0 -m "Release 1.5.0"
git push origin v1.5.0
```

------

#### 7. "Docker Build Fails in CI"

**Error:**

```bash
ERROR [build 3/8] COPY composer.json composer.lock ./
failed to compute cache key: "/composer.lock" not found
```

**Cause:** `.dockerignore` is excluding necessary files

**Solution:**

```dockerfile
# .dockerignore
node_modules
vendor
.git
.env

# Don't ignore these:
# composer.json
# composer.lock
# package.json
```

------

### Laravel-Specific Gotchas

#### 1. Queued Jobs in Tests

```php
// Problem: Job runs asynchronously, test completes before job
Mail::to($user)->queue(new WelcomeEmail());
$this->assertDatabaseHas('emails', ['user_id' => $user->id]); // Fails!

// Solution 1: Use sync queue for tests
// config/queue.php or .env.testing
'default' => env('QUEUE_CONNECTION', 'sync'),

// Solution 2: Use Queue fake
Queue::fake();
Mail::to($user)->queue(new WelcomeEmail());
Queue::assertPushed(WelcomeEmail::class);
```

#### 2. Database Transactions

```php
// Most feature tests should use this
use RefreshDatabase;

// Or for faster tests (doesn't drop tables)
use DatabaseTransactions;
```

#### 3. File Storage in Tests

```php
// Always use fake storage in tests
Storage::fake('s3');

$response = $this->post('/upload', ['file' => $file]);

// Assert file was stored
Storage::disk('s3')->assertExists('uploads/test.jpg');
```

------

## Quick Reference

### Essential Git Commands

```bash
# Daily workflow
git status                                  # Check what's changed
git add .                                   # Stage all changes
git commit -m "feat: your message"         # Commit
git push                                   # Push to remote

# Branch management
git checkout -b feature/new-feature        # Create and switch to branch
git checkout main                          # Switch to main
git branch -d feature/old-feature          # Delete local branch
git push origin --delete feature/old       # Delete remote branch

# Staying up to date
git fetch                                  # Download remote changes (doesn't merge)
git pull origin main                       # Download and merge main
git rebase main                            # Reapply your commits on top of main

# Fixing mistakes
git reset HEAD~1                           # Undo last commit (keep changes)
git reset --hard HEAD~1                    # Undo last commit (discard changes)
git revert abc123                          # Create new commit that undoes abc123

# Viewing history
git log --oneline                          # Compact commit history
git log --graph --all                      # Visual branch history
git show v1.5.0                            # Show tagged commit

# Tagging
git tag                                    # List all tags
git tag -a v1.5.0 -m "Release 1.5.0"      # Create annotated tag
git push origin v1.5.0                     # Push tag to remote
git push origin --tags                     # Push all tags
```

------

### Laravel Artisan Commands

```bash
# Development
php artisan serve                          # Start dev server
php artisan tinker                         # Interactive REPL
php artisan make:controller UserController # Generate controller
php artisan make:model User -mf           # Model + migration + factory
php artisan make:request StoreUserRequest  # Form request

# Database
php artisan migrate                        # Run migrations
php artisan migrate:fresh --seed           # Fresh database with seeds
php artisan migrate:rollback              # Rollback last migration
php artisan db:seed                        # Run seeders

# Testing
php artisan test                           # Run all tests
php artisan test --filter UserTest         # Run specific test
php artisan test --parallel                # Parallel testing
php artisan dusk                           # Browser tests

# Optimization
php artisan optimize                       # Cache config, routes, views
php artisan optimize:clear                 # Clear all caches
php artisan config:cache                   # Cache configuration
php artisan route:cache                    # Cache routes
php artisan view:cache                     # Cache Blade views

# Queue
php artisan queue:work                     # Process queue jobs
php artisan queue:failed                   # List failed jobs
php artisan queue:retry all                # Retry failed jobs

# Maintenance
php artisan down                           # Put app in maintenance mode
php artisan up                             # Bring app out of maintenance
```

------

### Composer Commands

```bash
# Installing packages
composer require laravel/sanctum            # Add package
composer require --dev phpunit/phpunit      # Add dev package
composer install                            # Install all dependencies
composer update                             # Update all packages
composer update laravel/framework           # Update specific package

# Optimization
composer dump-autoload                      # Regenerate autoloader
composer dump-autoload --optimize           # Optimized autoloader
composer install --no-dev --optimize-autoloader  # Production install

# Information
composer show                               # List installed packages
composer show laravel/framework             # Package details
composer outdated                           # Show outdated packages
composer validate                           # Validate composer.json

# Scripts
composer run-script phpcs                   # Run custom script
composer run-script phpstan                 # Static analysis
```

------

### GitHub CLI Commands

```bash
# Install: https://cli.github.com/

# Repository
gh repo view                               # View repo details
gh repo clone user/repo                    # Clone repo

# Pull Requests
gh pr create                               # Create PR from current branch
gh pr list                                 # List PRs
gh pr checkout 123                         # Checkout PR #123
gh pr view 123                             # View PR #123
gh pr review 123 --approve                 # Approve PR
gh pr merge 123 --squash                   # Squash and merge

# Issues
gh issue create                            # Create issue
gh issue list                              # List issues
gh issue view 123                          # View issue

# Workflows
gh workflow list                           # List workflows
gh workflow view ci.yml                    # View workflow
gh run list                                # List workflow runs
gh run view 123456                         # View specific run
```

------

### Version Bumping Cheat Sheet

```bash
Current: v1.4.2

Bug fix only:
→ v1.4.3 (PATCH)

New feature (backward-compatible):
→ v1.5.0 (MINOR)

Breaking change:
→ v2.0.0 (MAJOR)

Pre-releases:
→ v2.0.0-alpha.1
→ v2.0.0-beta.1
→ v2.0.0-rc.1
→ v2.0.0
```

------

### PR Review Checklist

```markdown
## Functionality
- [ ] Feature works as described
- [ ] Edge cases handled
- [ ] Error handling implemented

## Code Quality
- [ ] Follows Laravel conventions
- [ ] DRY (Don't Repeat Yourself)
- [ ] Clear variable/method names
- [ ] Comments for complex logic

## Security
- [ ] Input validation present
- [ ] SQL injection prevented (using Eloquent/Query Builder)
- [ ] XSS prevented (using Blade {{ }} escaping)
- [ ] Authentication/authorization checked
- [ ] Sensitive data not logged

## Performance
- [ ] No N+1 queries (use eager loading)
- [ ] Database indexes added if needed
- [ ] Caching used where appropriate
- [ ] Jobs queued for long-running tasks

## Testing
- [ ] Unit tests added
- [ ] Feature tests added
- [ ] Tests actually test the feature
- [ ] Tests are maintainable

## Documentation
- [ ] README updated if needed
- [ ] Inline documentation for complex logic
- [ ] CHANGELOG.md updated
```

------

### CI/CD Troubleshooting Checklist

```markdown
## Tests failing in CI but pass locally?

- [ ] Are you using the same PHP version?
- [ ] Is the database service running?
- [ ] Are environment variables set correctly?
- [ ] Is the cache causing issues? (try clearing)
- [ ] Are tests truly independent?
- [ ] Are you testing with production dependencies?

## Build taking too long?

- [ ] Is dependency caching enabled?
- [ ] Are you using parallel testing?
- [ ] Are fast checks running first?
- [ ] Are you filtering unnecessary runs?
- [ ] Is your Docker image optimized?

## Deployment failing?

- [ ] Is the version tag correct format?
- [ ] Do you have permission to push to GHCR?
- [ ] Are secrets configured?
- [ ] Is the Dockerfile correct?
- [ ] Are migrations up to date?
```

------

## Best Practices Summary

### Git Workflow

**DO:**

- Work on feature branches
- Commit frequently with clear messages
- Pull `main` often to stay updated
- Use descriptive branch names
- Squash commits when merging
- Tag releases with semantic versions

**DON'T:**

- Commit directly to `main`
- Use generic commit messages
- Let branches live for weeks
- Commit secrets or `.env` files
- Force push to shared branches
- Re-tag existing versions

------

### Laravel Development

**DO:**

- Use Eloquent over raw SQL
- Validate all user input
- Use Form Requests for complex validation
- Queue long-running jobs
- Use database transactions
- Write tests for all features
- Use eager loading to prevent N+1
- Cache expensive queries
- Use environment variables for config

**DON'T:**

- Use `DB::raw()` with user input
- Trust user input without validation
- Run slow operations synchronously
- Forget database indexes
- Skip testing "because it's simple"
- Hard-code configuration values
- Ignore security best practices

------

### CI/CD Pipeline

**DO:**

- Cache dependencies
- Run fast checks first
- Use parallel testing
- Filter unnecessary runs
- Set up proper secrets management
- Monitor build times
- Keep workflows maintainable

**DON'T:**

- Skip caching (wastes time)
- Run slow tests first
- Run everything sequentially
- Commit secrets to `.yml` files
- Ignore failed checks
- Create overly complex workflows

------

## Conclusion

You now have a comprehensive understanding of professional Laravel development workflows. Remember:

1. **`main` is sacred** - Never commit directly
2. **Feature branches** - Isolate all work
3. **Pull Requests** - Gateway to `main`
4. **CI/CD** - Automated quality gates
5. **Semantic Versioning** - Clear communication
6. **Git Tags** - Immutable release markers
7. **GHCR** - Versioned deployments

### Next Steps

**Week 1-2:** Practice basic Git workflow

- Create feature branches
- Make PRs
- Review code

**Week 3-4:** Learn CI/CD

- Understand GitHub Actions
- Write basic workflows
- Optimize build times

**Week 5-6:** Master releases

- Create tagged releases
- Understand versioning
- Deploy containers

**Ongoing:** Build good habits

- Write clear commit messages
- Review others' code
- Keep learning

------

### Additional Resources

**Git & GitHub:**

- [Pro Git Book](https://git-scm.com/book/en/v2) (free)
- [GitHub Docs](https://docs.github.com/)
- [Oh Shit, Git!?!](https://ohshitgit.com/) (fixing mistakes)

**Laravel:**

- [Laravel Documentation](https://laravel.com/docs)
- [Laravel Bootcamp](https://bootcamp.laravel.com/)
- [Laracasts](https://laracasts.com/) (video tutorials)

**Testing:**

- [Laravel Testing Docs](https://laravel.com/docs/testing)
- [Pest PHP](https://pestphp.com/) (alternative test framework)

**CI/CD:**

- [GitHub Actions Docs](https://docs.github.com/en/actions)
- [Docker Documentation](https://docs.docker.com/)

**Community:**

- [Laravel Discord](https://discord.gg/laravel)
- [Laracasts Forum](https://laracasts.com/discuss)
- [r/laravel](https://reddit.com/r/laravel)

------

**Remember:** Every senior developer was once a junior developer who kept practicing, learning from mistakes, and asking questions. You've got this! 🚀

------

*This guide is a living document. As you gain experience, revisit sections and deepen your understanding. Good luck on your Laravel journey!*