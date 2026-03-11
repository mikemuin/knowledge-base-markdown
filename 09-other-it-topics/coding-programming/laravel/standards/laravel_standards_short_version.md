# Laravel 12+ Layer Standard Order — Complete Reference

**Laravel 12+ · PHP 8.3+ · Livewire 3+**

A comprehensive, opinionated structured-order guide for all major Laravel layers. Consistent ordering reduces cognitive load, accelerates code reviews, prevents architectural drift, and enforces the Laravel Way across the entire team.

------

## Table of Contents

1. [Models](https://claude.ai/chat/ea9657c3-b479-4203-8c90-ada27bd7a89d#1-models)
2. [Controllers](https://claude.ai/chat/ea9657c3-b479-4203-8c90-ada27bd7a89d#2-controllers)
3. [Form Requests](https://claude.ai/chat/ea9657c3-b479-4203-8c90-ada27bd7a89d#3-form-requests)
4. [Service Classes](https://claude.ai/chat/ea9657c3-b479-4203-8c90-ada27bd7a89d#4-service-classes)
5. [Actions](https://claude.ai/chat/ea9657c3-b479-4203-8c90-ada27bd7a89d#5-actions)
6. [DTOs](https://claude.ai/chat/ea9657c3-b479-4203-8c90-ada27bd7a89d#6-dtos)
7. [Livewire Components](https://claude.ai/chat/ea9657c3-b479-4203-8c90-ada27bd7a89d#7-livewire-components)
8. [Migrations](https://claude.ai/chat/ea9657c3-b479-4203-8c90-ada27bd7a89d#8-migrations)
9. [Seeders](https://claude.ai/chat/ea9657c3-b479-4203-8c90-ada27bd7a89d#9-seeders)
10. [Factories](https://claude.ai/chat/ea9657c3-b479-4203-8c90-ada27bd7a89d#10-factories)
11. [Repositories](https://claude.ai/chat/ea9657c3-b479-4203-8c90-ada27bd7a89d#11-repositories)
12. [Service Providers](https://claude.ai/chat/ea9657c3-b479-4203-8c90-ada27bd7a89d#12-service-providers)
13. [API Resources](https://claude.ai/chat/ea9657c3-b479-4203-8c90-ada27bd7a89d#13-api-resources)
14. [Policies](https://claude.ai/chat/ea9657c3-b479-4203-8c90-ada27bd7a89d#14-policies)
15. [Enums](https://claude.ai/chat/ea9657c3-b479-4203-8c90-ada27bd7a89d#15-enums)
16. [Observers](https://claude.ai/chat/ea9657c3-b479-4203-8c90-ada27bd7a89d#16-observers)
17. [Quick Reference Card](https://claude.ai/chat/ea9657c3-b479-4203-8c90-ada27bd7a89d#17-quick-reference-card)

------

## 1. Models

### Standard Order:

1. **Constants**
2. **Traits**
3. **Properties** (`$fillable`, `$guarded`, `$hidden`, `$appends`, `$with`, `$perPage`, etc.)
4. **`booted()` method**
5. **`casts()` method**
6. **Relationships**
7. **Scopes**
8. **Accessors & Mutators**
9. **Business methods**
10. **`newFactory()` method**

### Notes:

- **Constants** should be PHP Enums wherever possible — Laravel 12 casts Enums natively. Use class constants only for truly primitive scalar values with no behaviour (e.g. `MINIMUM_TOTAL = 100`).
- **Traits** are declared with `use` immediately after constants. Multiple traits on one line unless they require conflict resolution.
- **Properties** are grouped together in full before any methods begin. Order within the group: `$fillable` / `$guarded` → `$hidden` → `$visible` → `$appends` → `$with` → `$withCount` → `$perPage` → `$dateFormat`. Using `$guarded = []` (fully open guard) in combination with `Model::unguard()` in `AppServiceProvider` is a valid team choice — pick one approach and apply it consistently across all models. Never mix `$fillable` on some models and `$guarded` on others.
- **`booted()`** is preferred over `boot()` in Laravel 12. It runs after the parent `boot()` completes, eliminating the risk of accidentally overriding parent setup. Use it for model event hooks (`creating`, `updating`, etc.).
- **`casts()`** is a method, not a property, since Laravel 10. Never use `protected $casts = []` in new code. Cast to Enum classes directly — no custom cast class needed.
- **Relationships** are ordered by directionality: parent relationships first (`belongsTo` — FK lives on this table), then peer relationships (`belongsToMany` — FK lives on a pivot), then child relationships (`hasOne`, `hasMany`, `morphOne`, `morphMany`, etc.). Each relationship must declare a return type.
- **Scopes** use the `#[Scope]` attribute (standard in Laravel 12). The `scope` prefix on method names is no longer required or recommended.
- **Accessors & Mutators** use `Attribute::make()` exclusively. The legacy `getXxxAttribute()` / `setXxxAttribute()` pattern is not used in Laravel 12. Use `->withoutObjectCaching()` for value objects that hold mutable state.
- **Business methods** are pure domain logic that belongs to the model's responsibility. Return `static` for fluent chaining. Throw domain exceptions, not HTTP exceptions, from here.
- **Observers** are registered on the model via the `#[ObservedBy]` attribute — not in a Service Provider.
- **`newFactory()`** is only needed when overriding default factory resolution. Always place it last when present.

------

## 2. Controllers

### Standard Order:

1. **`middleware()` static method**
2. **Constructor**
3. **Resource methods** (`index`, `create`, `store`, `show`, `edit`, `update`, `destroy`)
4. **Custom action methods**
5. **Private helper methods**

### Notes:

- In Laravel 12, middleware is declared via the static `middleware()` method by implementing `HasMiddleware`. Never use `$this->middleware()` inside the constructor — that pattern is removed in Laravel 12.
- **Single-action controllers** use only `__invoke()` as their public method, replacing the entire resource block. Name them after the action: `CancelOrderController`, `ApproveRefundController`. They always implement `HasMiddleware` where middleware is needed.
- Controllers must be **thin**. No business logic, no database queries, no direct model interaction beyond passing to a service or action. The controller's job is: receive request → authorize → delegate → return response.
- Omit `create()` and `edit()` in API-only controllers. They exist only for full-stack (Blade/Livewire) controllers that return views.
- Always call `$this->authorize()` at the top of each resource method. For standard CRUD policies, `authorizeResource()` can be called in the constructor — but prefer explicit per-method `$this->authorize()` calls for clarity, as they remain readable alongside the `HasMiddleware` pattern used in Laravel 12.
- All methods must be type-hinted with return types.
- Services or Actions are injected via the constructor. Never instantiate them inline with `new`.

------

## 3. Form Requests

### Standard Order:

1. **`authorize()`**
2. **`rules()`**
3. **`stopOnFirstFailure()`**
4. **`messages()`**
5. **`attributes()`**
6. **`prepareForValidation()`**
7. **`passedValidation()`**
8. **`after()`**
9. **Private helper methods**

### Notes:

- **`authorize()`** must always use `$this->user()->can()` or a Gate check. Never hardcode `return true` in production — use a policy or explicit permission check.
- **`rules()`** returns a typed array. Use `Rule::` fluent objects over string rules for any rule that accepts arguments (e.g. `Rule::exists()`, `Rule::unique()`, `Rule::in()`). Always scope `exists()` with `->whereNull('deleted_at')` for soft-deletable models.
- **`stopOnFirstFailure()`** is an optional override returning `bool`. Place it directly after `rules()` when used, as it modifies how the rule set is executed. Defaults to `false`.
- **`messages()`** overrides individual error messages. Use dot-notation keys matching the rule array exactly.
- **`attributes()`** overrides the human-readable field names used in default messages. Always define these for nested array fields (e.g. `items.*.id`).
- **`prepareForValidation()`** runs before the validator is built. Use it only for normalisation (e.g. `strtoupper`, `trim`, casting booleans with `$this->boolean()`). Never perform authorisation here.
- **`passedValidation()`** runs after successful validation. Use it to augment the validated payload with server-side values (e.g. `user_id`, `ip_address`). Do not re-validate here.
- **`after()`** (Laravel 10.1+) returns an array of closures or invokable validator classes. Use it for cross-field or business-rule validation that cannot be expressed as a standard rule. Always type-hint the `Validator` argument.
- Private helpers support `after()` logic. Keep them at the bottom and focused on a single check each.

------

## 4. Service Classes

### Standard Order:

1. **Constructor**
2. **Primary public methods**
3. **Secondary public methods**
4. **Protected methods**
5. **Private methods**

### Notes:

- Every Service class **must implement a contract interface**. Bind the interface to the concrete class in a Service Provider. Dependents always type-hint the interface, never the concrete class.
- Declare the class `readonly` (PHP 8.2+) when all dependencies are constructor-injected and never reassigned. This enforces immutability at the language level.
- **Primary public methods** mirror the bound interface contract exactly. One method per outcome: `create`, `update`, `cancel`, `refund`. Each should read as a verb in plain English.
- **Secondary public methods** are convenience or supporting methods that are public but not part of the core contract (e.g. `calculateSubtotal()`).
- Wrap all multi-step write operations in `DB::transaction()`. The transaction boundary lives in the service, never in the controller.
- Use **`DB::afterCommit()`** to dispatch events, send notifications, or trigger jobs that depend on the committed data. Never dispatch inside a transaction closure when the side effect requires the data to be visible externally.
- Services must not contain HTTP-specific logic (`abort()`, `response()`, request parsing). Those belong in the controller.
- Services are distinct from Actions: a Service manages a domain concept over time (stateful orchestration across multiple calls), while an Action executes a single operation in isolation.

------

## 5. Actions

### Standard Order:

1. **Constructor** (injected dependencies, if any)
2. **`handle()` method** (the single public entry point)
3. **Private helper methods**

### Notes:

- An Action is a **single-responsibility class** that encapsulates one discrete operation. The name is always a verb phrase: `CreateOrder`, `CancelOrder`, `SendWelcomeEmail`. Never name an Action after a resource alone.
- The **`handle()` method** is the sole public method and the only entry point. It accepts a typed parameter list (preferably a DTO) and returns a typed result. No other public methods should exist. Use `handle()` — not `execute()` or `__invoke()` — as it aligns with Laravel's own convention across Jobs, Listeners, and Mail handlers, creating a consistent mental model.
- Actions are distinct from Services: an Action does one thing, once. A Service orchestrates multiple operations or manages a concept across many interactions.
- Actions should be `readonly` classes when they have no mutable state.
- **Constructor** is optional. If the Action has no dependencies, omit it entirely. Inject dependencies via the constructor — never resolve from the container inside `handle()`.
- Actions may call Services and other Actions. They must never call Controllers.
- Actions are the correct place to put logic extracted from fat controllers. A controller action method should ideally be reduced to: validate → dispatch Action → return response.
- To make an Action queueable in native Laravel 12, create a dedicated Job class that calls the Action inside its `handle()` method. Do not implement `ShouldQueue` directly on an Action class — that interface is only meaningful on classes that extend Laravel's `Dispatchable` / `Queueable` Job base. If using the `lorisleiva/laravel-actions` package, its own `AsJob` trait handles this correctly.
- Private helpers support `handle()` only. If a helper is needed across multiple Actions, it belongs in a Service or a Trait.

------

## 6. DTOs

### Standard Order:

1. **Constructor** (promoted readonly properties)
2. **`fromRequest()` static factory**
3. **`fromModel()` static factory**
4. **`fromArray()` static factory**
5. **`toArray()` method**
6. **Instance helper methods**

### Notes:

- DTOs (Data Transfer Objects) are **`readonly` classes** in PHP 8.2+. Every property is declared via **constructor promotion** — no separate property declarations.
- All properties must be **explicitly typed**. Use value objects or Enums for domain concepts (e.g. `OrderStatus` not `string`). Use `?Type` for optional fields, never untyped `mixed`.
- **Static factory methods** (`fromRequest()`, `fromModel()`, `fromArray()`) are the standard way to construct a DTO from external boundary data. They centralise mapping logic and prevent scattered raw-array construction throughout the codebase. Using `new DTO(...)` directly is acceptable in internal code, tests, and within the DTO's own factory methods — the restriction applies to external callers passing unvalidated data.
- `fromRequest()` accepts a `FormRequest` and maps validated data to the DTO. It is the bridge between the HTTP layer and the domain layer.
- `fromModel()` accepts an Eloquent Model and maps its attributes/relations to the DTO. Use it when projecting model data outward (e.g. into an Action or a Resource).
- `fromArray()` is the fallback for non-HTTP contexts (e.g. queue jobs, console commands, tests).
- **`toArray()`** is provided when the DTO needs to be passed to systems that expect arrays (e.g. `Model::create($dto->toArray())`).
- DTOs live in `App\DTOs\`. Name them after the operation or the data shape: `CreateOrderData`, `OrderSummaryData`.
- DTOs must contain **no behaviour** beyond construction and projection. No database calls, no HTTP calls, no event dispatching. They are pure data carriers.

------

## 7. Livewire Components

### Standard Order:

1. **Traits**
2. **Public properties** (plain state)
3. **Public properties with Livewire attributes** (`#[Url]`, `#[Locked]`, `#[Modelable]`, `#[Validate]`)
4. **Protected properties**
5. **`#[Computed]` properties**
6. **`rules()` method** (only when validation rules are too complex for inline `#[Validate]`)
7. **`boot()`**
8. **`mount()`**
9. **Lifecycle hooks** (`hydrate`, `dehydrate`, `updating`, `updated`, etc.)
10. **`#[On]` event listeners**
11. **Public action methods**
12. **`render()`**
13. **Private helper methods**

### Notes:

- **`#[Url]`** binds a property to the browser query string. Always specify `as:` to control the query param name, and `history: true` when the change should create a browser history entry. Declare these alongside the property they modify.
- **`#[Locked]`** prevents the property from being updated by the client. Always apply it to server-set properties like `userId`, `teamId`, or any ID that determines data scope.
- **`#[Computed]`** properties are lazily evaluated and cached per Livewire request cycle. Bust the cache by calling `unset($this->propertyName)` after mutations that affect the computed result.
- **`boot()`** in Livewire runs on every request — initial and subsequent. Inject services here via method injection, not via the constructor. This is the correct place for service injection in Livewire.
- **`mount()`** runs only on the initial page load. Set server-side locked properties here (e.g. `$this->userId = auth()->id()`). Do not inject services in `mount()` — use `boot()`.
- **`#[Validate]`** is a property attribute, not a separate section. Declare it inline on the property it validates, co-located with the property declaration in steps 2–3. Use `#[Validate('required|string|max:255')]` for simple per-property rules. When rules are dynamic, interdependent, or too complex for an inline attribute, use a `rules()` method at step 6 instead — never both at once.
- **Lifecycle hooks** (`updatedSearch`, `updatingNotes`, etc.) follow the naming convention `updated{PropertyName}` and `updating{PropertyName}`. Place all of them together in their own section.
- **`#[On]`** event listeners respond to events dispatched from other Livewire components or the browser. Group them together before action methods.
- **`render()`** contains no logic. It returns only `view('...')`. All data passed to the view comes from computed properties or public properties — never from inline logic in `render()`.
- Public action methods are the only public methods the view should call directly. They represent the component's public interface.
- Private helpers support action methods only. If logic is reused across multiple components, extract it into a service.

------

## 8. Migrations

### Standard Order:

1. **Primary key** (`id`)
2. **Foreign key columns** (`foreignId()` columns — constraints declared at the end)
3. **Core domain columns**
4. **Status / enum columns**
5. **JSON / metadata columns**
6. **Boolean flags**
7. **Timestamps** (`timestamps()`)
8. **Soft delete** (`softDeletes()`)
9. **Indexes**
10. **Foreign key constraints**

### Notes:

- Always declare **foreign key columns and their constraints separately**. Use `$table->foreignId('user_id')` to declare the column — this creates an `UNSIGNED BIGINT` automatically and is the correct Laravel 12 style. Do not use the legacy `$table->unsignedBigInteger('user_id')`. Declare the foreign key constraint at the end of the schema definition (step 10) so it can be dropped independently without touching the column definition.
- Add `->comment('explanation')` to any column whose name does not make its purpose immediately obvious. Comments are stored in the database schema and visible via `SHOW FULL COLUMNS`.
- Store **monetary values as integers** (smallest currency unit — cents, pence, etc.). Never use `decimal` or `float` for money.
- Prefer **composite indexes** over multiple single-column indexes for columns that are always queried together (e.g. `['user_id', 'status']`).
- Use `->after('column_name')` when adding columns to existing tables in MySQL/MariaDB to control column position and maintain schema readability.
- The `down()` method must always be the inverse of `up()`. For `Schema::create()`, `down()` is `Schema::dropIfExists()`. For column additions, `down()` drops those columns.
- Never modify existing migrations that have been committed and run on any shared environment. Always write a new migration.
- Status/enum columns are stored as `string`, not MySQL `ENUM` type. MySQL `ENUM` is difficult to alter and couples the database schema to application logic.

------

## 9. Seeders

### Standard Order:

1. **`run()` method**
2. **Private helper methods**

### Notes:

- `DatabaseSeeder` is the **root seeder only**. It calls other seeders via `$this->call()` or `$this->callWith()` — it contains no direct model creation.
- Each entity gets its own dedicated seeder class: `UserSeeder`, `OrderSeeder`. One seeder per concept.
- Within `run()`, always use **factories** for data creation. Do not use `Model::create()` with raw arrays inside seeders — that duplicates factory logic and breaks when the schema changes.
- **Order of seeder calls in `DatabaseSeeder`** must respect foreign key dependencies: create parent records before child records.
- Use **`$this->callWith(SeederClass::class, ['param' => $value])`** when a seeder requires context from the parent (e.g. a specific user's ID).
- Wrap bulk seeding operations with the **`WithoutModelEvents`** trait on the seeder class — this is cleaner than calling `Model::withoutObservers()` manually and suppresses all model observer events for the entire seeder. Only add it to seeders where observer side-effects (notifications, cache invalidation) are not desired during seeding.
- Seeders must be **idempotent** where possible — use `firstOrCreate()` or `updateOrCreate()` so running seeders twice does not produce duplicate data.
- Private helpers in a seeder class break down the `run()` method into readable steps. They are not shared across seeder classes — if logic is shared, it belongs in a factory state or trait.

------

## 10. Factories

### Standard Order:

1. **`$model` property**
2. **`definition()` method**
3. **State methods**
4. **`afterMaking()` method**
5. **`afterCreating()` method**
6. **Private helper methods**

### Notes:

- **`definition()`** returns the **minimum valid state** of the model — the smallest set of attributes required for the model to exist and pass all database constraints. It must not represent a specific real-world scenario; that is the job of states.
- **State methods** represent distinct, named scenarios beyond the default. Name them after what they describe: `pending()`, `cancelled()`, `withCoupon()`, `forUser(User $user)`. States are composable: `Order::factory()->pending()->withCoupon()->create()` must work.
- States should call `$this->state()` and return the factory instance for chaining. They must not call `->create()` or `->make()` internally.
- **`afterMaking()`** runs after `make()` but before persistence. Use it to set relationships or computed values that require the model instance but not a database record.
- **`afterCreating()`** runs after `create()` and database persistence. Use it to create related records (e.g. `$order->items()->saveMany(OrderItem::factory()->count(3)->make())`).
- Factories must **never** contain conditional logic that depends on environment or configuration. They are test utilities — keep them deterministic.
- Use **`fake()->...`** (via `Faker`) for all generated values. Never use hardcoded strings for values that should vary (e.g. names, emails, amounts).
- Use **`->for($model)`** to attach a parent relationship when creating a child factory (e.g. `OrderItem::factory()->for($order)->create()`). Use **`->recycle($collection)`** to reuse a pool of related models across many factory calls instead of creating new related records for each — this dramatically reduces test database overhead for shared reference data (e.g. products, categories).
- The `$model` property is declared at the top, before all methods. Factories should be kept lean — avoid adding class properties beyond what the framework requires (`$model`, `$count` overrides). State data should be passed as method arguments, not stored as class properties.
- Private helpers support `afterMaking()` / `afterCreating()` complexity. Do not use them to bypass state methods.

------

## 11. Repositories

### Standard Order:

1. **Constructor** (injected Model or query builder dependency)
2. **Read methods** (`find`, `findOrFail`, `all`, `paginate`, `getBy...`)
3. **Write methods** (`create`, `update`, `delete`)
4. **Private helper methods**

### Notes:

- **When to use:** The Repository pattern is not native to Laravel and is intentionally absent from the framework. Use it only when there is a genuine need to abstract the data layer — for example, when the application must support multiple data sources (database + external API), or when strict unit testing of business logic without a database is a team requirement.
- **When not to use:** For standard CRUD applications backed by a single Eloquent model, a Repository adds indirection without value. Eloquent's scopes, query builder, and eager loading already provide sufficient abstraction. Prefer using the model directly in Services and Actions.
- Every Repository **must implement a contract interface**. The interface defines the read/write contract; the Eloquent implementation fulfils it. This is what enables swapping implementations or mocking in tests.
- Repositories must contain **only data access logic** — no business rules, no events, no notifications. They answer one question: how do I read or write this data?
- **Read methods** come before write methods. Within reads, order from most specific (`findOrFail`) to most general (`paginate`).
- Repositories must never call other repositories. If cross-entity data access is needed, it belongs in a Service that coordinates multiple repositories.
- For teams using repositories broadly, create an abstract **`EloquentRepository`** base class that provides common implementations of `find()`, `findOrFail()`, `paginate()`, and `delete()`. Concrete repositories extend it and override only what differs. Place the base class in `App\Repositories\EloquentRepository.php`. This eliminates boilerplate across every repository without violating the interface contract.
- Do not replicate Eloquent's entire API. Only expose methods the application actually calls. An unused `findAll()` on every repository is dead code.
- Pagination is always handled inside the repository, not in the service or controller.

------

## 12. Service Providers

### Standard Order:

1. **Class properties** (`$bindings`, `$singletons`)
2. **`register()`**
3. **`boot()`**
4. **`provides()`** (Deferred providers only)
5. **Private helper methods**

### Notes:

- **`$bindings`** and **`$singletons`** are array properties resolved automatically by Laravel. Use them for simple interface-to-implementation bindings that require no constructor arguments or conditional logic. For anything more complex, use `register()`.
- **`register()`** is for container bindings only. Never call routes, events, views, or any bootstrapping logic here. If it is not a `$this->app->bind()` / `singleton()` / `scoped()` call, it does not belong in `register()`.
- **`boot()`** is called after all providers are registered. It is safe to resolve bindings from the container here. Keep `boot()` as a high-level index of bootstrapping steps — delegate each step to a private helper method.
- Use **`scoped()`** for bindings that should share a single instance per HTTP request or job lifecycle (e.g. the current authenticated user's context).
- Use **`callAfterResolving()`** to configure a resolved instance post-construction without needing to subclass it. Useful for extending third-party classes.
- **Deferred providers** implement `DeferrableProvider` and declare a `provides()` method listing their bindings. Laravel will not load a deferred provider until one of its bindings is actually requested, improving boot performance. Use for bindings that are only needed in specific contexts (e.g. CLI, specific routes).
- Never register observers, event listeners, or model bindings in `boot()` directly. Use `#[ObservedBy]` on models for observers. For application events, register listeners via `Event::listen()` in `AppServiceProvider::boot()` or rely on Laravel's automatic event discovery — `EventServiceProvider` no longer exists in Laravel 11+. Keep `boot()` readable as a high-level index.

------

## 13. API Resources

### Standard Order:

1. **Static properties** (`$wrap`, `$preserveKeys`)
2. **`toArray()`**
3. **`with()`**
4. **`withResponse()`**

### Notes:

- **`$wrap`** should always be declared explicitly. Do not rely on the framework default (`data`) — make the API contract visible in the resource class. For collection resources, extend `ResourceCollection` and set `$wrap` there.
- **`$preserveKeys`** controls whether collection array keys are preserved. Defaults to `false`. Declare it explicitly when `true` is intentional.
- **`toArray()`** expresses the complete resource shape. Use Resource conditional helpers instead of PHP `if` statements inside the array: `when()`, `whenLoaded()`, `whenNotNull()`, `whenHas()`, `mergeWhen()`. These keep the shape declarative and scannable.
- Within `toArray()`, group fields in a consistent order: identity fields → domain fields → status/enum fields → timestamps → conditional relationships.
- **`with()`** adds top-level keys alongside `data` in the response envelope. Use it for `meta` or `links` that are consistent across all responses of this resource type.
- **`withResponse()`** mutates the underlying HTTP `Response` object directly. Use it sparingly — only for headers intrinsic to the resource itself (e.g. `ETag`, `X-Resource-Id`). Never use it to set business-logic-driven headers.
- For collection-level metadata (totals, aggregates, computed values), extend `ResourceCollection` and override `toArray()` and `with()` there. Do not attempt to do this from a single-resource class.
- Resources must not contain business logic, database queries, or calls to services. They are presentation layer only.

------

## 14. Policies

### Standard Order:

1. **`before()`**
2. **`viewAny()`**
3. **`view()`**
4. **`create()`**
5. **`update()`**
6. **`delete()`**
7. **`restore()`**
8. **`forceDelete()`**
9. **Custom policy methods**
10. **Private helper methods**

### Notes:

- **`before()`** must return **`null`** (not `false`) to fall through to the individual policy method. Returning `false` denies unconditionally for all abilities — bypassing all other checks. This is the most common policy mistake in teams. Only use `before()` for super-admin bypass logic.
- Policy methods receive the **authenticated user as the first argument always**. For guest access (unauthenticated), the user parameter must be nullable (`?User $user`). Omitting `?` means unauthenticated users are always denied before the method body runs.
- All policy methods return `bool`. `before()` returns `bool|null`.
- **Custom policy methods** (step 9) are named after the action, not the resource: `cancel`, `export`, `approve`, `refund` — not `cancelOrder` or `canUserCancelOrder`.
- Private helpers (step 10) centralise shared logic across multiple policy methods (e.g. `isOwner()`, `isSameTeam()`). Never duplicate ownership checks inline across multiple methods.
- In Laravel 12, policies are auto-discovered by convention (`OrderPolicy` → `Order` model). Manual registration in `AuthServiceProvider` is not required unless convention cannot be followed.
- Always apply policies via `$this->authorize()` in controllers, not via inline `Gate::allows()` — this ensures consistent 403 handling and integrates with Laravel's exception handler.

------

## 15. Enums

### Standard Order:

1. **Cases**
2. **Implemented interface methods**
3. **Instance helper methods** (non-interface)
4. **Static helper methods**

### Notes:

- Enums should implement **shared interfaces** (e.g. `HasLabel`, `HasColor`) when multiple Enums in the system share the same behavioural surface. This allows UI components, select helpers, and formatters to type-hint the interface rather than a specific Enum.
- **Cases must always be listed first** — before any methods. This is a PHP language convention and maximises at-a-glance readability.
- **Always use `tryFrom()`** when parsing untrusted input (HTTP requests, database values read outside of a cast, third-party APIs). `from()` throws `ValueError` on invalid input; `tryFrom()` returns `null` and allows the caller to handle the failure gracefully.
- In Laravel 12, Enum casts are native. Declare `MyEnum::class` directly in the model's `casts()` method — no custom cast class is needed.
- **Implemented interface methods** come before non-interface instance methods. This keeps the class's public contract visible at the top.
- **Static helpers** (`options()`, `values()`, `fromLabel()`, `finalStates()`) come last. They are utility methods for external consumers (e.g. Livewire selects, validation rules, API responses).
- `options()` should return an array of `['value' => ..., 'label' => ...]` maps — compatible with Flux UI `<flux:select>` and similar components.
- `values()` returns a plain `array` of raw values, suitable for `Rule::in(OrderStatus::values())` in Form Requests.
- Enum state machine logic (`canTransitionTo()`) belongs in instance helper methods, not in a Service or Model. The Enum is the single source of truth for valid transitions.

------

## 16. Observers

### Standard Order:

Mirror Eloquent's event firing sequence. Before-events are always paired with their after-event:

1. **`creating` / `created`**
2. **`updating` / `updated`**
3. **`saving` / `saved`**
4. **`deleting` / `deleted`**
5. **`restoring` / `restored`** (SoftDeletes only)
6. **`forceDeleting` / `forceDeleted`** (SoftDeletes only)
7. **Private helper methods**

### Notes:

- In Laravel 12, observers are registered on the model class via the **`#[ObservedBy(MyObserver::class)]`** attribute. Do not register observers in Service Providers — that approach is superseded.
- **Only implement methods you actually need.** Empty observer methods are noise. Omit them entirely — Eloquent will not call them if they are absent.
- Observers must remain **free of business logic**. They are event hooks. Dispatch jobs or fire events from here — do not orchestrate multi-step workflows inline. An Observer method should be 2–5 lines maximum.
- **`before` events** (`creating`, `updating`, `deleting`) are for guards and pre-population (setting server-generated values, enforcing invariants before persistence).
- **`after` events** (`created`, `updated`, `deleted`) are for side effects that depend on the record being committed (dispatching notifications, invalidating caches, publishing events).
- Use **`withoutObservers()`** for bulk operations (mass updates, imports, data migrations) where observer side-effects are incorrect or prohibitively expensive. Always add a comment explaining why observers are bypassed.
- Avoid triggering database writes inside `creating` or `saving` hooks that themselves fire the same model's events — this causes infinite loops.
- Private helpers support observer methods only. Any logic reused across observers belongs in a shared service or trait.

------

## 17. Quick Reference Card

| Layer                   | Standard Order                                               | Key Principle                                                |
| ----------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| **Models**              | Constants → Traits → Properties → `booted()` → `casts()` → Relationships → Scopes → Accessors/Mutators → Business Methods → `newFactory()` | Data contract first, behaviour last                          |
| **Controllers**         | `middleware()` → Constructor → Resource methods → Custom actions → Private helpers | Thin; delegate everything; `authorize()` on every method     |
| **Form Requests**       | `authorize()` → `rules()` → `stopOnFirstFailure()` → `messages()` → `attributes()` → `prepareForValidation()` → `passedValidation()` → `after()` → Private helpers | Mirror the validation lifecycle exactly                      |
| **Service Classes**     | Constructor → Primary public → Secondary public → Protected → Private | Implement a contract interface; `readonly` class; `DB::transaction()` for all writes |
| **Actions**             | Constructor → `handle()` → Private helpers                   | One class, one operation; `handle()` is the only public method; may call Services and other Actions; never calls Controllers |
| **DTOs**                | Constructor (promoted props) → `fromRequest()` → `fromModel()` → `fromArray()` → `toArray()` → Instance helpers | `readonly` class; typed properties only; no behaviour        |
| **Livewire Components** | Traits → Public props → Attributed props (`#[Url]`, `#[Locked]`, `#[Validate]`) → Protected props → `#[Computed]` → `rules()` → `boot()` → `mount()` → Lifecycle hooks → `#[On]` listeners → Action methods → `render()` → Private helpers | State first, lifecycle second, actions third; `#[Validate]` lives on the property; `render()` is always logic-free |
| **Migrations**          | `id` → `foreignId()` columns → Domain columns → Status/enum → JSON → Booleans → `timestamps()` → `softDeletes()` → Indexes → FK constraints | Money as integers; `foreignId()` not `unsignedBigInteger`; constraints last; `->comment()` on non-obvious columns |
| **Seeders**             | `run()` → Private helpers                                    | `DatabaseSeeder` calls only; factories for data; idempotent with `firstOrCreate()`; use `WithoutModelEvents` trait to suppress observers |
| **Factories**           | `$model` → `definition()` → State methods → `afterMaking()` → `afterCreating()` → Private helpers | `definition()` = minimum valid state; states = named scenarios; states must be composable |
| **Repositories**        | Constructor → Read methods → Write methods → Private helpers | Interface-bound; data access only; no business logic         |
| **Service Providers**   | `$bindings`/`$singletons` → `register()` → `boot()` → `provides()` → Private helpers | `register()` = container only; `boot()` = bootstrapping index only |
| **API Resources**       | `$wrap`/`$preserveKeys` → `toArray()` → `with()` → `withResponse()` | Use conditional helpers; no `if` inside `toArray()`; presentation layer only |
| **Policies**            | `before()` → `viewAny()` → `view()` → `create()` → `update()` → `delete()` → `restore()` → `forceDelete()` → Custom methods → Private helpers | `before()` returns `null` to fall through, never `false`; name custom methods after the action |
| **Enums**               | Cases → Interface methods → Instance helpers → Static helpers | Implement shared interfaces; `tryFrom()` for untrusted input; Enums own state machine logic |
| **Observers**           | `creating`/`created` → `updating`/`updated` → `saving`/`saved` → `deleting`/`deleted` → `restoring`/`restored` → `forceDeleting`/`forceDeleted` → Private helpers | Register via `#[ObservedBy]`; dispatch jobs, not inline logic; `withoutObservers()` for bulk ops |

