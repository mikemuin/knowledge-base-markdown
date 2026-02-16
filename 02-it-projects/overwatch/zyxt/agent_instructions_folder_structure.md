# Folder Structure Instructions

## AI Coding Agent Instructions: Project Folder Structure & Development Approach

### Architecture Philosophy

This codebase follows a **modular monolith** approach using **subdomain-driven module separation**. Each business area (e.g., Governance, Execution, Risk Control) is represented as a domain module with internal submodules.

All code should be scoped within its respective module unless explicitly shared via the `Shared` or `Notes` systems.

------

### Folder Structure Overview

Each top-level domain module (e.g., `Governance`, `Execution`, etc.) contains nested **feature submodules** with isolated structure.

```
app/Modules/
└── <Domain>/
    └── <Submodule>/
        ├── Controllers/
        │   ├── Api/
        │   ├── Web/
        │   └── Livewire/
        ├── DTOs/
        ├── Events/
        ├── FormRequests/
        ├── Models/
        ├── Policies/
        ├── Repositories/
        ├── Resources/
        ├── Services/
        ├── ViewModels/
        ├── Views/
        └── Livewire/
```

Example: `app/Modules/Execution/ActionItems/Services/ActionItemService.php`

------

### Triple Interface Support

Each feature must support:

- **API** via `Controllers/Api`
- **Blade Web Pages** via `Controllers/Web` and `Views/`
- **Livewire Components** via `Livewire/`

Form Requests and DTOs are reused across interfaces where applicable.

------

### Common Layers Used Per Submodule

| Layer           | Purpose                                                      |
| --------------- | ------------------------------------------------------------ |
| `DTOs/`         | Transform and normalize data across interfaces and services  |
| `Events/`       | Triggered on lifecycle changes for logging, notifications, workflows |
| `FormRequests/` | Validate data before service or controller use               |
| `Services/`     | Core business logic and orchestration                        |
| `Repositories/` | Data access layer, abstracting models and queries            |
| `Policies/`     | Authorization layer per user profile and model               |
| `Resources/`    | API transformers for consistent JSON output                  |
| `ViewModels/`   | Data prep for Blade and Livewire views                       |

------

### Shared Systems

These are globally available and **must be reused** where applicable:

- `Notes/`: Immutable, timestamped comment streams attached to any entity.
- `AuditTrail/`: Logs of field-level changes and actions.
- `Shared/`: Reusable enums, traits, constants, utility classes.

------

### Key Rules

1. **Keep each submodule self-contained**. No logic leakage across modules.
2. **Never reuse controllers across interfaces** (API ≠ Web ≠ Livewire).
3. **Prefer services + DTOs** over placing logic in controllers or models.
4. **FormRequests are interface-bound**, DTOs are domain-bound.
5. **All data changes must trigger events for audit logging.**
6. **Soft deletes only** — no hard deletes allowed.

------

Let me know if you’d like this converted into Markdown syntax or embedded with template examples.