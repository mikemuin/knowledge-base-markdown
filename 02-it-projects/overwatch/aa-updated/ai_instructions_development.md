# AI Instructions

## Summarize

Summarize what we completed or accomplished in this chat so far.so that the next AI instructions and prompts have an idea of the current project status. Append to the completed summary file. Take out redundancies.

Update and restructure the completed summary file to include summaries of what we've done in this chat so far. Make it more complete and concise for subsequent instructions. Eliminate redundancies.

## Projects as a Module

```text
Convert the Projects domain into a complete module following the existing ActionItems pattern.

Requirements:
1. Ensure Projects has all necessary components:
   - Models (Project.php already exists)
   - Actions directory for business logic
   - Policies for authorization
   - Http/Controllers directory
   - Livewire components directory
   - Factory and seeder
2. Follow the exact structure and naming conventions from app/Modules/ActionItems/
3. Create a README.md documenting the module's purpose and boundaries
4. Add any missing files that ActionItems has but Projects doesn't
5. Ensure all namespaces use App\Modules\Projects\
6. Run tests after changes to verify nothing broke
7. Format code with Pint
```

---

```text
Convert the [MODULE_NAME] module to use vertically-separated views within the module itself.

Steps:
1. Create Views directory in app/Modules/[MODULE_NAME]/Views/
2. Move all views from resources/views/[module-name]/ to the new Views directory
3. Create app/Modules/[MODULE_NAME]/Providers/[MODULE_NAME]ServiceProvider.php
4. Register view path with namespace in the service provider's boot() method
5. Add service provider to bootstrap/providers.php
6. Update all view references from view('[module-name].index') to view('[module-name]::index')
7. Check these locations for view references:
   - Controllers
   - Livewire components
   - Routes
8. Run module tests to verify functionality
9. Format code with Pint

Do NOT modify other modules - only focus on [MODULE_NAME].
```

---

```text
Convert the following modules to use vertically-separated views: [LIST_MODULES].

For EACH module, perform these steps in sequence (complete one fully before moving to the next):

1. Create Views directory and move views
2. Create service provider with view registration
3. Register service provider in bootstrap/providers.php
4. Update all view references to use namespace (::)
5. Run that module's tests
6. Format code with Pint

Stop after each module and report results. Wait for my approval before proceeding to the next module.

This ensures we can catch issues early rather than breaking multiple modules at once.
```

## Phased Prompting (Improved)

You are tasked with developing a new Laravel module following our established architecture and best practices.

**Context:**
I have attached a data model document that defines the business requirements, fields, relationships, and rules for this module.

**Your Task:**
Develop this module by following our module development guides in the correct sequence:

1. **Phase 1: Data Foundation**
   - Review the attached data model document
   - Create enums for all constrained values
   - Create migrations (entity table + groups table if applicable)
   - Create Eloquent models with relationships, casts, scopes, and business methods
   - Create factories with initial state defaults and state methods
   - Create repositories with eager loading
   - Create DTOs (CreateData and UpdateData)

2. **Phase 2: Business Logic**
   - Create service class with CRUD methods using DTOs
   - Create events (Created, Updated, Deleted + custom business actions)
   - Create observer (for created_by, updated_by, and target_week if applicable)
   - Create policy in app/Policies/ with all authorization methods
   - Register observer in service provider

3. **Phase 3: HTTP Integration**
   - Create form requests (Store and Update) with validation and authorization
   - Create Livewire components (Index, Create, Edit)
   - Create minimal HTML views with Livewire directives
   - Define routes with proper middleware and scoping
   - Create tests for components and policies

**Guidelines:**
- Reference the module development guides for Laravel best practices
- Use the WeekCalculatorService for target_week computation if the entity has target_date
- All entities are project-scoped with universal patterns: metadata, soft deletes, internal visibility
- If the entity uses groups, implement 2-level hierarchy validation
- Keep views minimal and functional (basic HTML) - UI refinement comes later
- Always use dependency injection, DTOs, and proper separation of concerns

**Before you begin:**
Confirm you understand the data model by summarizing:
1. The primary entity and its purpose
2. Key fields and their types
3. Relationships (project, groups, owner, etc.)
4. Enums that need to be created
5. Any special business rules or validations

Then proceed through each phase systematically, showing your implementation for each component.

---

I’ve read the Digital Health Blueprint Toolkit carefully, and I think we should be honest about what it is.

It’s not a roadmap, and it’s not an implementation plan. It’s basically a set of guided questions to help a group reflect on where things stand.

The output is a description of our current situation. For people in this group with years of experience, none of this should be new. These questions can be answered well in a day.

If the reason we’re doing this is to meet a World Bank funding requirement, then we should do it as simply and quickly as possible, without turning it into another lengthy planning exercise.

We already have many years’ worth of strategies and reports. The real gap now is not more thinking or writing, but actually building and running systems.

My concern is that unless we are very disciplined, this will just add more paperwork. I’d strongly prefer we agree upfront to keep this short, complete it quickly, and then move straight into clear implementation directions.

