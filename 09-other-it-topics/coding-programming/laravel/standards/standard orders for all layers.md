# Laravel 12+ Layer Standard Order — Complete Reference

> **Laravel 12+ · PHP 8.3+ · Livewire 3+**
>
> A comprehensive, opinionated structured-order guide for all major Laravel layers. Consistent ordering reduces cognitive load, accelerates code reviews, prevents architectural drift, and enforces the Laravel Way across the entire team — from junior developers to senior architects.
>
> Every example is built around a shared `Order` domain so patterns can be compared across layers.

------

## Table of Contents

1. [Models](https://claude.ai/chat/ea9657c3-b479-4203-8c90-ada27bd7a89d#1-models)
2. [Controllers](https://claude.ai/chat/ea9657c3-b479-4203-8c90-ada27bd7a89d#2-controllers)
3. [Form Requests](https://claude.ai/chat/ea9657c3-b479-4203-8c90-ada27bd7a89d#3-form-requests)
4. [Service Classes](https://claude.ai/chat/ea9657c3-b479-4203-8c90-ada27bd7a89d#4-service-classes)
5. [Livewire Components](https://claude.ai/chat/ea9657c3-b479-4203-8c90-ada27bd7a89d#5-livewire-components)
6. [Migrations](https://claude.ai/chat/ea9657c3-b479-4203-8c90-ada27bd7a89d#6-migrations)
7. [Service Providers](https://claude.ai/chat/ea9657c3-b479-4203-8c90-ada27bd7a89d#7-service-providers)
8. [API Resources](https://claude.ai/chat/ea9657c3-b479-4203-8c90-ada27bd7a89d#8-api-resources)
9. [Policies](https://claude.ai/chat/ea9657c3-b479-4203-8c90-ada27bd7a89d#9-policies)
10. [Enums](https://claude.ai/chat/ea9657c3-b479-4203-8c90-ada27bd7a89d#10-enums)
11. [Observers](https://claude.ai/chat/ea9657c3-b479-4203-8c90-ada27bd7a89d#11-observers)
12. [Quick Reference Card](https://claude.ai/chat/ea9657c3-b479-4203-8c90-ada27bd7a89d#12-quick-reference-card)

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
8. **Accessors & Mutators** (`Attribute::make()`)
9. **Business methods**
10. **`newFactory()` method**

### Notes:

- **Constants** should be PHP enums wherever possible (Laravel 12 casts Enums natively). Use class constants only for truly primitive scalar values with no behaviour.
- **`booted()`** is preferred over `boot()` in Laravel 12 — it runs after the parent `boot()` completes, avoiding accidental override issues.
- **`casts()`** is a method, not a property, since Laravel 10. Never use `protected $casts = []` in new code.
- **Scopes** use the `#[Scope]` attribute (introduced in Laravel 11, standard in 12). The `scope` prefix on method names is no longer required.
- **Accessors & Mutators** use `Attribute::make()` exclusively. The legacy `getXxxAttribute()` / `setXxxAttribute()` pattern is deprecated style.
- **Observers** should be registered on the model via the `#[ObservedBy]` attribute rather than in a Service Provider.
- **`newFactory()`** is only needed when overriding the default factory resolution. Place it consistently at the end when present.

### Annotated Example:

```php
<?php

namespace App\Models;

use App\Enums\OrderStatus;
use App\Models\Observers\OrderObserver;
use App\Models\Factories\OrderFactory;
use Illuminate\Database\Eloquent\Attributes\ObservedBy;
use Illuminate\Database\Eloquent\Attributes\Scope;
use Illuminate\Database\Eloquent\Builder;
use Illuminate\Database\Eloquent\Casts\Attribute;
use Illuminate\Database\Eloquent\Factories\HasFactory;
use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\Relations\BelongsTo;
use Illuminate\Database\Eloquent\Relations\HasMany;
use Illuminate\Database\Eloquent\SoftDeletes;

#[ObservedBy(OrderObserver::class)]
class Order extends Model
{
    // -------------------------------------------------------------------------
    // 1. Constants
    // Use only for primitive scalars with no behaviour. Prefer Enums otherwise.
    // -------------------------------------------------------------------------

    const MINIMUM_TOTAL = 100; // cents

// -------------------------------------------------------------------------
    // 2. Traits
    // -------------------------------------------------------------------------

    use HasFactory, SoftDeletes;

// -------------------------------------------------------------------------
    // 3. Properties
    // -------------------------------------------------------------------------

    protected $fillable = [
        'user_id',
        'coupon_id',
        'reference',
        'status',
        'subtotal',
        'discount',
        'total',
        'currency',
        'notes',
        'metadata',
        'shipping_address',
        'is_gift',
        'placed_at',
    ];

    protected $hidden = [
        'internal_reference',
    ];

    protected $appends = [
        'display_total',
    ];

    protected $with = [
        // Eager-load only globally-required relationships here.
        // Be conservative — this applies to every query on this model.
    ];

// -------------------------------------------------------------------------
    // 4. booted()
    // Preferred over boot() in Laravel 12. Runs after parent boot() completes.
    // -------------------------------------------------------------------------

    protected static function booted(): void
    {
        static::creating(function (self $order): void {
            $order->currency ??= 'USD';
            $order->status   ??= OrderStatus::Pending;
        });

        static::updating(function (self $order): void {
            if ($order->isDirty('total') && $order->total < self::MINIMUM_TOTAL) {
                throw new \DomainException('Order total cannot be below minimum.');
            }
        });
    }

// -------------------------------------------------------------------------
    // 5. casts()
    // Method form — never use the $casts property array in Laravel 12.
    // -------------------------------------------------------------------------

    protected function casts(): array
    {
        return [
            'status'           => OrderStatus::class,
            'subtotal'         => 'integer',
            'discount'         => 'integer',
            'total'            => 'integer',
            'metadata'         => 'array',
            'shipping_address' => 'array',
            'is_gift'          => 'boolean',
            'placed_at'        => 'datetime',
        ];
    }

// -------------------------------------------------------------------------
    // 6. Relationships
    // Order: parent relationships first (belongsTo), then children (hasMany, etc.)
    // -------------------------------------------------------------------------

    public function user(): BelongsTo
    {
        return $this->belongsTo(User::class);
    }

    public function coupon(): BelongsTo
    {
        return $this->belongsTo(Coupon::class);
    }

    public function items(): HasMany
    {
        return $this->hasMany(OrderItem::class);
    }

// -------------------------------------------------------------------------
    // 7. Scopes
    // Use #[Scope] attribute — no scope prefix required in Laravel 12.
    // -------------------------------------------------------------------------

    #[Scope]
    protected function pending(Builder $query): void
    {
        $query->where('status', OrderStatus::Pending);
    }

    #[Scope]
    protected function complete(Builder $query): void
    {
        $query->where('status', OrderStatus::Complete);
    }

    #[Scope]
    protected function forUser(Builder $query, User $user): void
    {
        $query->where('user_id', $user->id);
    }

    #[Scope]
    protected function placedBetween(Builder $query, \Carbon\Carbon $from, \Carbon\Carbon $to): void
    {
        $query->whereBetween('placed_at', [$from, $to]);
    }

// -------------------------------------------------------------------------
    // 8. Accessors & Mutators
    // Always use Attribute::make(). Legacy getXxxAttribute() is not used in L12.
    // -------------------------------------------------------------------------

    protected function displayTotal(): Attribute
    {
        return Attribute::make(
            get: fn () => number_format($this->total / 100, 2) . ' ' . $this->currency,
        );
    }

    protected function notes(): Attribute
    {
        return Attribute::make(
            get: fn (?string $value) => $value ?? '',
            set: fn (string $value)  => trim($value),
        );
    }

    protected function shippingAddress(): Attribute
    {
        return Attribute::make(
            get: fn (?array $value) => $value ? new \App\ValueObjects\Address(...$value) : null,
            set: fn (\App\ValueObjects\Address $value) => ['shipping_address' => $value->toArray()],
        )->withoutObjectCaching();
    }

// -------------------------------------------------------------------------
    // 9. Business Methods
    // Domain logic belongs here, not in controllers or services.
    // Return $this for fluency where it makes sense.
    // -------------------------------------------------------------------------

    public function cancel(): static
    {
        $this->update(['status' => OrderStatus::Cancelled]);

        return $this;
    }

    public function complete(): static
    {
        $this->update(['status' => OrderStatus::Complete]);

        return $this;
    }

    public function isPending(): bool
    {
        return $this->status === OrderStatus::Pending;
    }

    public function isCancelled(): bool
    {
        return $this->status === OrderStatus::Cancelled;
    }

    public function isComplete(): bool
    {
        return $this->status === OrderStatus::Complete;
    }

    public function applyDiscount(int $amountInCents): static
    {
        $this->update([
            'discount' => $amountInCents,
            'total'    => max(0, $this->subtotal - $amountInCents),
        ]);

        return $this;
    }

// -------------------------------------------------------------------------
    // 10. newFactory()
    // Only include when overriding default factory resolution.
    // -------------------------------------------------------------------------

    protected static function newFactory(): OrderFactory
    {
        return OrderFactory::new();
    }
}
```

------

## 2. Controllers

### Standard Order:

1. **`middleware()` static method** (Laravel 12 middleware registration)
2. **Constructor** (injected dependencies)
3. **Resource methods** (`index`, `create`, `store`, `show`, `edit`, `update`, `destroy`)
4. **Custom action methods**
5. **Private helper methods**

### Notes:

- In Laravel 12, middleware is registered via the static `middleware()` method on the controller class, not inside the constructor via `$this->middleware()`. This is the idiomatic pattern.
- **Single-action controllers** use `__invoke()` as their only public method, replacing the entire resource method block. Always suffix with `Controller` and name them after the action: `CancelOrderController`.
- Controllers should be **thin**. Any logic beyond delegating to a service, forming a response, or calling a policy belongs in a Service or Action class.
- Omit `create()` and `edit()` in API-only controllers. Only include them in full-stack (Blade/Livewire) controllers.
- Type-hint return types on all methods.

### Annotated Example (Resource Controller):

```php
<?php

namespace App\Http\Controllers;

use App\Http\Requests\StoreOrderRequest;
use App\Http\Requests\UpdateOrderRequest;
use App\Http\Resources\OrderResource;
use App\Models\Order;
use App\Services\OrderService;
use Illuminate\Http\JsonResponse;
use Illuminate\Http\Resources\Json\AnonymousResourceCollection;
use Illuminate\Routing\Controllers\HasMiddleware;
use Illuminate\Routing\Controllers\Middleware;

class OrderController extends Controller implements HasMiddleware
{
    // -------------------------------------------------------------------------
    // 1. middleware() — Laravel 12 static middleware registration
    // -------------------------------------------------------------------------

    public static function middleware(): array
    {
        return [
            new Middleware('auth'),
            new Middleware('verified', only: ['store', 'update', 'destroy']),
            new Middleware('throttle:60,1', only: ['store']),
        ];
    }

// -------------------------------------------------------------------------
    // 2. Constructor
    // -------------------------------------------------------------------------

    public function __construct(
        protected readonly OrderService $orderService,
    ) {}

// -------------------------------------------------------------------------
    // 3. Resource Methods (CRUD order)
    // -------------------------------------------------------------------------

    public function index(): AnonymousResourceCollection
    {
        $orders = Order::query()
            ->forUser(auth()->user())
            ->with(['items'])
            ->latest('placed_at')
            ->paginate();

        return OrderResource::collection($orders);
    }

    public function store(StoreOrderRequest $request): OrderResource
    {
        $order = $this->orderService->create($request->validated());

        return (new OrderResource($order))
            ->response()
            ->setStatusCode(201);
    }

    public function show(Order $order): OrderResource
    {
        $this->authorize('view', $order);

        return new OrderResource($order->load(['user', 'items', 'coupon']));
    }

    public function update(UpdateOrderRequest $request, Order $order): OrderResource
    {
        $this->authorize('update', $order);

        $this->orderService->update($order, $request->validated());

        return new OrderResource($order->refresh());
    }

    public function destroy(Order $order): JsonResponse
    {
        $this->authorize('delete', $order);

        $order->delete();

        return response()->json(null, 204);
    }

// -------------------------------------------------------------------------
    // 4. Custom Action Methods
    // -------------------------------------------------------------------------

    public function cancel(Order $order): OrderResource
    {
        $this->authorize('cancel', $order);

        $this->orderService->cancel($order);

        return new OrderResource($order->refresh());
    }

// -------------------------------------------------------------------------
    // 5. Private Helper Methods
    // -------------------------------------------------------------------------

    private function paginationLimit(): int
    {
        return min((int) request('per_page', 15), 100);
    }
}
```

### Annotated Example (Single-Action Controller):

```php
<?php

namespace App\Http\Controllers;

use App\Http\Resources\OrderResource;
use App\Models\Order;
use App\Services\OrderService;
use Illuminate\Routing\Controllers\HasMiddleware;
use Illuminate\Routing\Controllers\Middleware;

class CancelOrderController extends Controller implements HasMiddleware
{
    // -------------------------------------------------------------------------
    // 1. middleware()
    // -------------------------------------------------------------------------

    public static function middleware(): array
    {
        return [
            new Middleware('auth'),
        ];
    }

// -------------------------------------------------------------------------
    // 2. Constructor
    // -------------------------------------------------------------------------

    public function __construct(
        protected readonly OrderService $orderService,
    ) {}

// -------------------------------------------------------------------------
    // 3. __invoke() — the sole public method
    // -------------------------------------------------------------------------

    public function __invoke(Order $order): OrderResource
    {
        $this->authorize('cancel', $order);

        $this->orderService->cancel($order);

        return new OrderResource($order->refresh());
    }
}
```

------

## 3. Form Requests

### Standard Order:

1. **`authorize()`**
2. **`rules()`**
3. **`stopOnFirstFailure()`** (optional override)
4. **`messages()`**
5. **`attributes()`**
6. **`prepareForValidation()`**
7. **`passedValidation()`**
8. **`after()`**
9. **Private helper methods**

### Notes:

- **`authorize()`** should use `$this->user()->can()` or Gate checks. Never return a hard-coded `true` in production — use a policy or a permission check.
- **`stopOnFirstFailure()`** is an override method returning `bool`. Place it directly after `rules()` when used, as it modifies rule execution behaviour.
- **`after()`** (added in Laravel 10.1) accepts an array of closures or invokable classes for cross-field validation that cannot be expressed as rules. This is the correct place for complex business-rule validation.
- **`passedValidation()`** is for augmenting the validated payload (e.g. injecting `user_id`). Do not use it to re-validate — that belongs in `after()`.
- **`prepareForValidation()`** runs before validation. Use it only for normalisation (e.g. `strtoupper`, `trim`), never for authorisation.
- Keep private helpers at the bottom. They support `after()` logic and should be clearly separated.

### Annotated Example:

```php
<?php

namespace App\Http\Requests;

use App\Models\Coupon;
use App\Models\Order;
use Illuminate\Foundation\Http\FormRequest;
use Illuminate\Validation\Rule;
use Illuminate\Validation\Validator;

class StoreOrderRequest extends FormRequest
{
    // -------------------------------------------------------------------------
    // 1. authorize()
    // -------------------------------------------------------------------------

    public function authorize(): bool
    {
        return $this->user()->can('create', Order::class);
    }

// -------------------------------------------------------------------------
    // 2. rules()
    // -------------------------------------------------------------------------

    public function rules(): array
    {
        return [
            'items'           => ['required', 'array', 'min:1'],
            'items.*.id'      => ['required', 'integer', Rule::exists('products', 'id')->whereNull('deleted_at')],
            'items.*.qty'     => ['required', 'integer', 'min:1', 'max:100'],
            'notes'           => ['nullable', 'string', 'max:500'],
            'coupon_code'     => ['nullable', 'string', Rule::exists('coupons', 'code')],
            'is_gift'         => ['boolean'],
            'shipping_address'=> ['required', 'array'],
            'shipping_address.line1'   => ['required', 'string', 'max:255'],
            'shipping_address.city'    => ['required', 'string', 'max:100'],
            'shipping_address.country' => ['required', 'string', 'size:2'],
        ];
    }

// -------------------------------------------------------------------------
    // 3. stopOnFirstFailure()
    // -------------------------------------------------------------------------

    public function stopOnFirstFailure(): bool
    {
        return false;
    }

// -------------------------------------------------------------------------
    // 4. messages()
    // -------------------------------------------------------------------------

    public function messages(): array
    {
        return [
            'items.required'              => 'Your order must contain at least one item.',
            'items.*.id.exists'           => 'One or more selected products are unavailable.',
            'items.*.qty.max'             => 'A maximum of 100 units per product is allowed.',
            'shipping_address.required'   => 'A shipping address is required.',
        ];
    }

// -------------------------------------------------------------------------
    // 5. attributes()
    // -------------------------------------------------------------------------

    public function attributes(): array
    {
        return [
            'items.*.id'               => 'product',
            'items.*.qty'              => 'quantity',
            'coupon_code'              => 'coupon',
            'shipping_address.line1'   => 'address line 1',
            'shipping_address.city'    => 'city',
            'shipping_address.country' => 'country',
        ];
    }

// -------------------------------------------------------------------------
    // 6. prepareForValidation()
    // Normalise incoming data before the validator runs. Never authorise here.
    // -------------------------------------------------------------------------

    protected function prepareForValidation(): void
    {
        $this->merge([
            'coupon_code' => $this->coupon_code
                ? strtoupper(trim($this->coupon_code))
                : null,
            'is_gift' => $this->boolean('is_gift'),
        ]);
    }

// -------------------------------------------------------------------------
    // 7. passedValidation()
    // Augment the validated payload after successful validation.
    // -------------------------------------------------------------------------

    protected function passedValidation(): void
    {
        $this->merge([
            'user_id' => $this->user()->id,
        ]);
    }

// -------------------------------------------------------------------------
    // 8. after()
    // Cross-field and business-rule validation. Returns array of closures
    // or invokable validator classes.
    // -------------------------------------------------------------------------

    public function after(): array
    {
        return [
            function (Validator $validator): void {
                if ($this->couponIsExpired()) {
                    $validator->errors()->add('coupon_code', 'This coupon has expired.');
                }
            },
            function (Validator $validator): void {
                if ($this->exceedsUserOrderLimit()) {
                    $validator->errors()->add('items', 'You have reached your daily order limit.');
                }
            },
        ];
    }

// -------------------------------------------------------------------------
    // 9. Private Helper Methods
    // -------------------------------------------------------------------------

    private function couponIsExpired(): bool
    {
        if (! $this->coupon_code) {
            return false;
        }

        return Coupon::where('code', $this->coupon_code)
            ->where('expires_at', '<', now())
            ->exists();
    }

    private function exceedsUserOrderLimit(): bool
    {
        return Order::query()
            ->where('user_id', $this->user()->id)
            ->whereDate('created_at', today())
            ->count() >= 10;
    }
}
```

------

## 4. Service Classes

### Standard Order:

1. **Constructor** (injected dependencies via interface contracts)
2. **Primary public methods** (the core contract — matches the bound interface)
3. **Secondary public methods** (supporting / convenience methods)
4. **Protected methods**
5. **Private methods**

### Notes:

- Every Service class should implement a **contract interface**. Bind the interface to the concrete class in a Service Provider. This decouples dependents and enables swapping implementations or mocking in tests.
- In Laravel 12, declare the class `readonly` (PHP 8.2+) when all dependencies are injected via the constructor and never mutated. This provides immutability guarantees at no runtime cost.
- **Primary public methods** mirror the interface contract exactly. Keep them focused on a single outcome. They should read like English: `create`, `cancel`, `refund`.
- Wrap multi-step operations in `DB::transaction()`. Keep the transaction boundary inside the service, not in the controller.
- **Never** dispatch events or send notifications from within a transaction closure if those side-effects depend on the committed data being visible to external systems. Dispatch after the transaction commits using `DB::afterCommit()`.

### Contract Interface:

```php
<?php

namespace App\Contracts;

use App\Models\Order;

interface OrderServiceContract
{
    public function create(array $data): Order;
    public function update(Order $order, array $data): Order;
    public function cancel(Order $order): void;
    public function refund(Order $order, ?int $amountInCents = null): void;
}
```

### Annotated Example:

```php
<?php

namespace App\Services;

use App\Contracts\OrderServiceContract;
use App\Events\OrderCancelled;
use App\Events\OrderCreated;
use App\Models\Order;
use App\Models\Product;
use Illuminate\Support\Facades\DB;

readonly class OrderService implements OrderServiceContract
{
    // -------------------------------------------------------------------------
    // 1. Constructor
    // All dependencies injected via constructor. readonly enforces immutability.
    // -------------------------------------------------------------------------

    public function __construct(
        private InventoryService $inventoryService,
        private PricingService   $pricingService,
        private CouponService    $couponService,
    ) {}

// -------------------------------------------------------------------------
    // 2. Primary Public Methods (matches OrderServiceContract interface)
    // -------------------------------------------------------------------------

    public function create(array $data): Order
    {
        return DB::transaction(function () use ($data): Order {
            $subtotal = $this->calculateSubtotal($data['items']);
            $discount = $this->resolveDiscount($data['coupon_code'] ?? null, $subtotal);

            $order = Order::create([
                'user_id'          => $data['user_id'],
                'coupon_id'        => $this->couponService->findByCode($data['coupon_code'] ?? null)?->id,
                'subtotal'         => $subtotal,
                'discount'         => $discount,
                'total'            => $subtotal - $discount,
                'notes'            => $data['notes'] ?? null,
                'is_gift'          => $data['is_gift'] ?? false,
                'shipping_address' => $data['shipping_address'],
                'placed_at'        => now(),
            ]);

            $this->attachItems($order, $data['items']);
            $this->inventoryService->reserve($data['items']);

            DB::afterCommit(fn () => OrderCreated::dispatch($order));

            return $order;
        });
    }

    public function update(Order $order, array $data): Order
    {
        $order->update($data);

        return $order->refresh();
    }

    public function cancel(Order $order): void
    {
        abort_if($order->isCancelled(), 422, 'Order is already cancelled.');
        abort_if($order->isComplete(), 422, 'Completed orders cannot be cancelled.');

        DB::transaction(function () use ($order): void {
            $order->cancel();
            $this->inventoryService->release($order->items);

            DB::afterCommit(fn () => OrderCancelled::dispatch($order));
        });
    }

    public function refund(Order $order, ?int $amountInCents = null): void
    {
        abort_unless($order->isComplete(), 422, 'Only completed orders can be refunded.');

        $amount = $amountInCents ?? $order->total;

        DB::transaction(function () use ($order, $amount): void {
            // Refund logic via payment gateway...
            DB::afterCommit(fn () => \App\Events\OrderRefunded::dispatch($order, $amount));
        });
    }

// -------------------------------------------------------------------------
    // 3. Secondary Public Methods
    // -------------------------------------------------------------------------

    public function calculateSubtotal(array $items): int
    {
        return collect($items)->sum(
            fn ($item) => $this->pricingService->getPrice($item['id']) * $item['qty']
        );
    }

// -------------------------------------------------------------------------
    // 4. Protected Methods
    // -------------------------------------------------------------------------

    protected function attachItems(Order $order, array $items): void
    {
        foreach ($items as $item) {
            $product = Product::findOrFail($item['id']);

            $order->items()->create([
                'product_id' => $product->id,
                'quantity'   => $item['qty'],
                'unit_price' => $this->pricingService->getPrice($product->id),
            ]);
        }
    }

// -------------------------------------------------------------------------
    // 5. Private Methods
    // -------------------------------------------------------------------------

    private function resolveDiscount(?string $couponCode, int $subtotal): int
    {
        if (! $couponCode) {
            return 0;
        }

        return $this->couponService->calculateDiscount($couponCode, $subtotal);
    }
}
```

------

## 5. Livewire Components

### Standard Order:

1. **Traits**
2. **Public properties** (standard state)
3. **Public properties with Livewire attributes** (`#[Url]`, `#[Locked]`, `#[Modelable]`)
4. **Protected properties**
5. **`#[Computed]` properties**
6. **`#[Validate]` rules** (or `rules()` method for complex cases)
7. **`boot()`** (Livewire's component-level boot)
8. **`mount()`**
9. **Lifecycle hooks** (`hydrate`, `dehydrate`, `updating`, `updated`, etc.)
10. **`#[On]` event listeners**
11. **Public action methods**
12. **`render()`**
13. **Private helper methods**

### Notes:

- **`#[Url]`** binds a property to the browser's query string. Declare it alongside the property, not separately.
- **`#[Locked]`** prevents the property from being updated by the client. Always use it on sensitive properties like `userId` that are set server-side.
- **`#[Computed]`** properties are lazily evaluated and cached per request. `unset($this->propertyName)` to bust the cache when underlying data changes.
- **`boot()`** in Livewire runs on every request (initial and subsequent). Use it for injecting services since `mount()` only runs on the initial page load.
- **`render()`** is placed just before private helpers — it is a lifecycle method, not an action, and should never contain logic. Delegate to computed properties or private methods.
- Services should be injected via `boot()` or `mount()` method injection, not constructor injection, as Livewire does not use a standard constructor.

### Annotated Example:

```php
<?php

namespace App\Livewire;

use App\Enums\OrderStatus;
use App\Models\Order;
use App\Services\OrderService;
use Illuminate\Contracts\View\View;
use Livewire\Attributes\Computed;
use Livewire\Attributes\Locked;
use Livewire\Attributes\On;
use Livewire\Attributes\Url;
use Livewire\Attributes\Validate;
use Livewire\Component;
use Livewire\WithPagination;

class OrderManager extends Component
{
    // -------------------------------------------------------------------------
    // 1. Traits
    // -------------------------------------------------------------------------

    use WithPagination;

// -------------------------------------------------------------------------
    // 2. Public Properties (standard state)
    // -------------------------------------------------------------------------

    public bool   $showModal  = false;
    public string $notes      = '';

// -------------------------------------------------------------------------
    // 3. Public Properties with Livewire Attributes
    // #[Url]    — synced to the browser query string
    // #[Locked] — client cannot modify; set server-side only
    // -------------------------------------------------------------------------

    #[Url(as: 'q', history: true)]
    public string $search = '';

    #[Url(as: 'status', history: true)]
    public string $statusFilter = '';

    #[Locked]
    public ?int $editingId = null;

    #[Locked]
    public int $userId;

// -------------------------------------------------------------------------
    // 4. Protected Properties
    // -------------------------------------------------------------------------

    protected OrderService $orderService;

// -------------------------------------------------------------------------
    // 5. Computed Properties
    // Cache is busted by calling unset($this->orders) after mutations.
    // -------------------------------------------------------------------------

    #[Computed]
    public function orders()
    {
        return Order::query()
            ->with(['user', 'items'])
            ->when($this->search, fn ($q) => $q->where('reference', 'like', "%{$this->search}%"))
            ->when($this->statusFilter, fn ($q) => $q->where('status', $this->statusFilter))
            ->where('user_id', $this->userId)
            ->latest('placed_at')
            ->paginate(15);
    }

    #[Computed]
    public function editingOrder(): ?Order
    {
        return $this->editingId ? Order::find($this->editingId) : null;
    }

    #[Computed]
    public function statusOptions(): array
    {
        return OrderStatus::options();
    }

// -------------------------------------------------------------------------
    // 6. Validation
    // Use #[Validate] for simple per-property rules.
    // Use a rules() method for complex, interdependent, or dynamic rules.
    // -------------------------------------------------------------------------

    #[Validate('nullable|string|max:500')]
    public string $notes = '';

// -------------------------------------------------------------------------
    // 7. boot()
    // Runs on every Livewire request. Inject services here.
    // -------------------------------------------------------------------------

    public function boot(OrderService $orderService): void
    {
        $this->orderService = $orderService;
    }

// -------------------------------------------------------------------------
    // 8. mount()
    // Runs only on the initial page load. Set locked/server-side properties here.
    // -------------------------------------------------------------------------

    public function mount(): void
    {
        $this->userId = auth()->id();
    }

// -------------------------------------------------------------------------
    // 9. Lifecycle Hooks
    // -------------------------------------------------------------------------

    public function updatedSearch(): void
    {
        $this->resetPage();
        unset($this->orders);
    }

    public function updatedStatusFilter(): void
    {
        $this->resetPage();
        unset($this->orders);
    }

    public function updatingNotes(string $value): string
    {
        return trim($value);
    }

// -------------------------------------------------------------------------
    // 10. Event Listeners
    // -------------------------------------------------------------------------

    #[On('order-cancelled')]
    public function handleOrderCancelled(): void
    {
        unset($this->orders);
        $this->closeModal();
    }

    #[On('order-created')]
    public function handleOrderCreated(): void
    {
        unset($this->orders);
    }

// -------------------------------------------------------------------------
    // 11. Public Action Methods
    // -------------------------------------------------------------------------

    public function openModal(int $orderId): void
    {
        $this->editingId = $orderId;
        $this->showModal = true;
    }

    public function closeModal(): void
    {
        $this->reset(['editingId', 'showModal', 'notes']);
    }

    public function cancelOrder(int $orderId): void
    {
        $order = $this->findOrder($orderId);

        $this->authorize('cancel', $order);
        $this->orderService->cancel($order);

        unset($this->orders);
        $this->dispatch('order-cancelled');
    }

    public function saveNotes(): void
    {
        $this->validate();

        $order = $this->findOrder($this->editingId);
        $this->orderService->update($order, ['notes' => $this->notes]);

        unset($this->orders, $this->editingOrder);
        $this->closeModal();
    }

// -------------------------------------------------------------------------
    // 12. render()
    // No logic here. All data comes from computed properties or the view itself.
    // -------------------------------------------------------------------------

    public function render(): View
    {
        return view('livewire.order-manager');
    }

// -------------------------------------------------------------------------
    // 13. Private Helper Methods
    // -------------------------------------------------------------------------

    private function findOrder(int $id): Order
    {
        return Order::findOrFail($id);
    }
}
```

------

## 6. Migrations

### Standard Order:

1. **Primary key** (`id`)
2. **Foreign key columns** (bare `unsignedBigInteger` columns — constraints declared at the end)
3. **Core domain columns** (the essential data of the entity)
4. **Status / enum columns**
5. **JSON / metadata columns**
6. **Boolean flags**
7. **Timestamps** (`timestamps()`)
8. **Soft delete** (`softDeletes()`)
9. **Indexes**
10. **Foreign key constraints**

### Notes:

- Always declare **foreign key columns and constraints separately**. Define the bare column early (step 2) so the intent is clear, then declare the constraint at the very end (step 10). This makes constraint removal easier and keeps the schema readable.
- Add `->comment('explanation')` to any non-obvious column. This documents intent directly in the database schema, visible via `SHOW FULL COLUMNS`.
- Prefer **composite indexes** on columns commonly queried together (e.g. `['user_id', 'status']`). Add them explicitly rather than relying on single-column foreign key indexes.
- Store **monetary values as integers** (cents). Never use `decimal` or `float` for money.
- Use `->after('column_name')` when adding columns to existing tables in MySQL/MariaDB to control column position for readability.

### Annotated Example:

```php
<?php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

return new class extends Migration
{
    public function up(): void
    {
        Schema::create('orders', function (Blueprint $table): void {

            // -----------------------------------------------------------------
            // 1. Primary Key
            // -----------------------------------------------------------------
            $table->id();

            // -----------------------------------------------------------------
            // 2. Foreign Key Columns (bare columns; constraints declared last)
            // -----------------------------------------------------------------
            $table->unsignedBigInteger('user_id');
            $table->unsignedBigInteger('coupon_id')->nullable();

            // -----------------------------------------------------------------
            // 3. Core Domain Columns
            // -----------------------------------------------------------------
            $table->string('reference')->unique()->comment('Human-readable order identifier');
            $table->string('currency', 3)->default('USD');
            $table->integer('subtotal')->comment('Pre-discount total in smallest currency unit');
            $table->integer('discount')->default(0)->comment('Total discount applied in smallest currency unit');
            $table->integer('total')->comment('Final charged amount in smallest currency unit');
            $table->text('notes')->nullable();

            // -----------------------------------------------------------------
            // 4. Status / Enum Columns
            // -----------------------------------------------------------------
            $table->string('status')->default('pending')->comment('pending|complete|cancelled|refunded');

            // -----------------------------------------------------------------
            // 5. JSON / Metadata Columns
            // -----------------------------------------------------------------
            $table->json('shipping_address');
            $table->json('metadata')->nullable()->comment('Arbitrary key-value store for extensibility');

            // -----------------------------------------------------------------
            // 6. Boolean Flags
            // -----------------------------------------------------------------
            $table->boolean('is_gift')->default(false);
            $table->boolean('requires_signature')->default(false);

            // -----------------------------------------------------------------
            // 7. Timestamps
            // -----------------------------------------------------------------
            $table->timestamp('placed_at')->nullable()->comment('When the customer confirmed the order');
            $table->timestamps();

            // -----------------------------------------------------------------
            // 8. Soft Delete
            // -----------------------------------------------------------------
            $table->softDeletes();

            // -----------------------------------------------------------------
            // 9. Indexes
            // -----------------------------------------------------------------
            $table->index('status');
            $table->index('placed_at');
            $table->index(['user_id', 'status']);      // Composite: orders by user + status
            $table->index(['user_id', 'placed_at']);   // Composite: orders by user + date range

            // -----------------------------------------------------------------
            // 10. Foreign Key Constraints
            // -----------------------------------------------------------------
            $table->foreign('user_id')
                  ->references('id')->on('users')
                  ->cascadeOnDelete();

            $table->foreign('coupon_id')
                  ->references('id')->on('coupons')
                  ->nullOnDelete();
        });
    }

    public function down(): void
    {
        Schema::dropIfExists('orders');
    }
};
```

------

## 7. Service Providers

### Standard Order:

1. **Class properties** (`$bindings`, `$singletons`)
2. **`register()`**
3. **`boot()`**
4. **Private helper methods**

### Notes:

- **`$bindings`** and **`$singletons`** are array properties on the provider that Laravel resolves automatically — use them for simple interface-to-implementation bindings that need no configuration logic. For anything requiring constructor arguments or conditional logic, use `register()`.
- **`register()`** is for binding things into the service container only. Never call `route()`, `view()`, `event()`, or any other bootstrapping here — that belongs in `boot()`.
- **`boot()`** is called after all providers are registered. It is safe to resolve bindings from the container here.
- In Laravel 12, use **deferred providers** (implement `DeferrableProvider`) for providers that register bindings only used in specific contexts (e.g. CLI commands). This improves boot performance by deferring resolution until the binding is actually requested.
- Extract complex `boot()` logic into **private helper methods** to keep `boot()` readable as a high-level index of bootstrapping steps.
- Use **`callAfterResolving()`** when you need to configure a resolved instance post-resolution (e.g. adding macros or extending a third-party class).

### Annotated Example:

```php
<?php

namespace App\Providers;

use App\Contracts\OrderServiceContract;
use App\Contracts\PaymentGatewayContract;
use App\Contracts\ShippingContract;
use App\Services\OrderService;
use App\Services\StripePaymentGateway;
use App\Services\FedExShippingService;
use Illuminate\Contracts\Foundation\Application;
use Illuminate\Database\Eloquent\Model;
use Illuminate\Support\Facades\DB;
use Illuminate\Support\Facades\Gate;
use Illuminate\Support\ServiceProvider;

class AppServiceProvider extends ServiceProvider
{
    // -------------------------------------------------------------------------
    // 1. Class Properties
    // Simple interface-to-concrete bindings with no configuration logic.
    // -------------------------------------------------------------------------

    public array $bindings = [
        PaymentGatewayContract::class => StripePaymentGateway::class,
    ];

    public array $singletons = [
        ShippingContract::class => FedExShippingService::class,
    ];

// -------------------------------------------------------------------------
    // 2. register()
    // Container bindings only. No bootstrapping, no routes, no events.
    // -------------------------------------------------------------------------

    public function register(): void
    {
        // Use scoped() for bindings that should share one instance per request.
        $this->app->scoped(OrderServiceContract::class, function (Application $app): OrderService {
            return new OrderService(
                inventoryService: $app->make(InventoryService::class),
                pricingService:   $app->make(PricingService::class),
                couponService:    $app->make(CouponService::class),
            );
        });

        // callAfterResolving: extend a resolved instance post-construction.
        $this->callAfterResolving(SomeThirdPartyClass::class, function ($instance, $app): void {
            $instance->addExtension(new CustomExtension());
        });
    }

// -------------------------------------------------------------------------
    // 3. boot()
    // All bootstrapping: models, gates, macros, observers, view composers, etc.
    // Keep this as a high-level index — extract details into private helpers.
    // -------------------------------------------------------------------------

    public function boot(): void
    {
        $this->configureModels();
        $this->configureGates();
        $this->configureMacros();
    }

// -------------------------------------------------------------------------
    // 4. Private Helper Methods
    // -------------------------------------------------------------------------

    private function configureModels(): void
    {
        // Strict mode: throws exceptions for lazy loading, mass assignment,
        // and accessing missing attributes. Disable in production for safety.
        Model::shouldBeStrict(! $this->app->isProduction());

        // Log slow queries in non-production environments.
        if (! $this->app->isProduction()) {
            DB::listen(function ($query): void {
                if ($query->time > 1000) {
                    logger()->warning('Slow query detected', [
                        'sql'      => $query->sql,
                        'time_ms'  => $query->time,
                    ]);
                }
            });
        }
    }

    private function configureGates(): void
    {
        Gate::before(function ($user, $ability): ?bool {
            return $user->isSuperAdmin() ? true : null;
        });
    }

    private function configureMacros(): void
    {
        // Register Collection, Builder, or Response macros here.
    }
}
```

### Deferred Provider Example:

```php
<?php

namespace App\Providers;

use Illuminate\Contracts\Support\DeferrableProvider;
use Illuminate\Support\ServiceProvider;

class ReportingServiceProvider extends ServiceProvider implements DeferrableProvider
{
    public function register(): void
    {
        $this->app->singleton(ReportingService::class, fn () => new ReportingService());
    }

    // Tells Laravel which bindings this provider offers.
    // The provider is only loaded when one of these is resolved.
    public function provides(): array
    {
        return [ReportingService::class];
    }
}
```

------

## 8. API Resources

### Standard Order:

1. **Static properties** (`$wrap`, `$preserveKeys`)
2. **`toArray()`**
3. **`with()`**
4. **`withResponse()`**

### Notes:

- **`$wrap`** defaults to `'data'` on collection resources and the class name (snake_case) on single resources. Override it explicitly so the API contract is clear to the team.
- **`$preserveKeys`** controls whether array keys are preserved in collection resources. Defaults to `false`.
- **`toArray()`** should express the full resource shape. Use conditional helpers (`when()`, `whenLoaded()`, `whenHas()`, `whenNotNull()`, `mergeWhen()`) liberally rather than writing `if` statements inside the array.
- **`with()`** adds top-level keys alongside `data` in the response envelope. Use it for consistent `meta` or `links` sections across all resources of a type.
- **`withResponse()`** mutates the underlying `Response` object. Use it sparingly — only for headers that are intrinsic to the resource (e.g. `ETag`, `X-Resource-Id`).
- For custom collection-level metadata (e.g. totals, aggregates), extend `ResourceCollection` rather than using `Resource::collection()`.

### Annotated Example (Single Resource):

```php
<?php

namespace App\Http\Resources;

use Illuminate\Http\Request;
use Illuminate\Http\Resources\Json\JsonResource;

class OrderResource extends JsonResource
{
    // -------------------------------------------------------------------------
    // 1. Static Properties
    // -------------------------------------------------------------------------

    public static $wrap = 'order';

// -------------------------------------------------------------------------
    // 2. toArray()
    // -------------------------------------------------------------------------

    public function toArray(Request $request): array
    {
        return [
            // Core identity
            'id'            => $this->id,
            'reference'     => $this->reference,

            // Status & financials
            'status'        => $this->status->value,
            'status_label'  => $this->status->label(),
            'status_color'  => $this->status->color(),
            'subtotal'      => $this->subtotal,
            'discount'      => $this->discount,
            'total'         => $this->total,
            'display_total' => $this->display_total,
            'currency'      => $this->currency,

            // Optional fields (only included when not null)
            'notes'         => $this->whenNotNull($this->notes),
            'is_gift'       => $this->is_gift,

            // Admin-only fields
            'metadata'      => $this->when(
                $request->user()?->isAdmin(),
                $this->metadata,
            ),

            // Dates
            'placed_at'     => $this->placed_at?->toISOString(),
            'created_at'    => $this->created_at->toISOString(),
            'updated_at'    => $this->updated_at->toISOString(),

            // Conditional relationships (only included when eager-loaded)
            'user'          => UserResource::make($this->whenLoaded('user')),
            'items'         => OrderItemResource::collection($this->whenLoaded('items')),
            'coupon'        => CouponResource::make($this->whenLoaded('coupon')),
        ];
    }

// -------------------------------------------------------------------------
    // 3. with()
    // -------------------------------------------------------------------------

    public function with(Request $request): array
    {
        return [
            'meta' => [
                'api_version' => 'v1',
            ],
        ];
    }

// -------------------------------------------------------------------------
    // 4. withResponse()
    // -------------------------------------------------------------------------

    public function withResponse(Request $request, $response): void
    {
        $response->header('X-Order-Id', (string) $this->id);
        $response->header('ETag', md5($this->updated_at->toISOString()));
    }
}
```

### Annotated Example (Custom Resource Collection):

```php
<?php

namespace App\Http\Resources;

use Illuminate\Http\Request;
use Illuminate\Http\Resources\Json\ResourceCollection;

class OrderCollection extends ResourceCollection
{
    public static $wrap = 'orders';

    public bool $preserveKeys = false;

    public function toArray(Request $request): array
    {
        return [
            'data' => $this->collection,
        ];
    }

    public function with(Request $request): array
    {
        return [
            'meta' => [
                'total_value' => $this->collection->sum('total'),
                'currencies'  => $this->collection->pluck('currency')->unique()->values(),
            ],
        ];
    }
}
```

------

## 9. Policies

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

- **`before()`** must return **`null`** (not `false`) to fall through to the individual policy method. Returning `false` denies unconditionally, bypassing all other checks. This is one of the most common policy mistakes in teams.
- **`before()`** is for super-admin or global bypass logic only. Do not put any domain-specific logic here.
- Policy methods receive the **authenticated user as the first argument always**. Guest access (unauthenticated) requires the user parameter to be nullable (`?User $user`) — omitting the `?` means guests are always denied before the method body runs.
- Return type should always be `bool` (or `bool|null` for `before()`).
- **Custom policy methods** (step 9) should be named after the action, not the resource: `cancel`, `export`, `approve` — not `cancelOrder`.
- Private helpers (step 10) support complex permission logic shared across multiple policy methods.

### Annotated Example:

```php
<?php

namespace App\Policies;

use App\Models\Order;
use App\Models\User;

class OrderPolicy
{
    // -------------------------------------------------------------------------
    // 1. before()
    // CRITICAL: return null (not false) to fall through. Return true to bypass.
    // -------------------------------------------------------------------------

    public function before(User $user, string $ability): bool|null
    {
        if ($user->isSuperAdmin()) {
            return true; // Bypass all checks for super admins.
        }

        return null; // null = fall through to the specific policy method.
    }

// -------------------------------------------------------------------------
    // 2. viewAny()
    // -------------------------------------------------------------------------

    public function viewAny(User $user): bool
    {
        return $user->hasVerifiedEmail();
    }

// -------------------------------------------------------------------------
    // 3. view()
    // -------------------------------------------------------------------------

    public function view(User $user, Order $order): bool
    {
        return $this->isOwner($user, $order);
    }

// -------------------------------------------------------------------------
    // 4. create()
    // -------------------------------------------------------------------------

    public function create(User $user): bool
    {
        return $user->hasVerifiedEmail() && ! $user->isSuspended();
    }

// -------------------------------------------------------------------------
    // 5. update()
    // -------------------------------------------------------------------------

    public function update(User $user, Order $order): bool
    {
        return $this->isOwner($user, $order) && $order->isPending();
    }

// -------------------------------------------------------------------------
    // 6. delete()
    // -------------------------------------------------------------------------

    public function delete(User $user, Order $order): bool
    {
        return $this->isOwner($user, $order) && $order->isPending();
    }

// -------------------------------------------------------------------------
    // 7. restore()
    // -------------------------------------------------------------------------

    public function restore(User $user, Order $order): bool
    {
        return $user->hasPermission('orders.restore');
    }

// -------------------------------------------------------------------------
    // 8. forceDelete()
    // -------------------------------------------------------------------------

    public function forceDelete(User $user, Order $order): bool
    {
        return $user->hasPermission('orders.force-delete');
    }

// -------------------------------------------------------------------------
    // 9. Custom Policy Methods
    // Named after the action, not the resource.
    // -------------------------------------------------------------------------

    public function cancel(User $user, Order $order): bool
    {
        return $this->isOwner($user, $order) && $order->isPending();
    }

    public function refund(User $user, Order $order): bool
    {
        return $user->hasPermission('orders.refund') && $order->isComplete();
    }

    public function export(User $user): bool
    {
        return $user->hasPermission('orders.export');
    }

    // Guest access example: nullable User allows unauthenticated users through.
    public function viewPublicSummary(?User $user, Order $order): bool
    {
        return $order->is_public;
    }

// -------------------------------------------------------------------------
    // 10. Private Helper Methods
    // -------------------------------------------------------------------------

    private function isOwner(User $user, Order $order): bool
    {
        return $user->id === $order->user_id;
    }
}
```

------

## 10. Enums

### Standard Order:

1. **Cases**
2. **Implemented interface methods**
3. **Instance helper methods** (non-interface)
4. **Static helper methods**

### Notes:

- Enums should implement **shared interfaces** (e.g. `HasLabel`, `HasColor`) when multiple Enums in the system share the same behaviour surface. This allows type-hinted helpers and UI components to work against the interface, not a specific Enum.
- **Always use `tryFrom()`** when parsing untrusted input (e.g. from a request or database). `from()` throws a `ValueError` on invalid values; `tryFrom()` returns `null`, which is far safer.
- In Laravel 12, Enum casts are native. Declare `OrderStatus::class` in `casts()` — no custom cast class needed.
- Enum **cases must be listed first**, before any methods. This is a PHP language convention.
- Static `options()` and `values()` are particularly useful for Livewire select inputs and validation `Rule::in()` constraints.

### Interface Contracts:

```php
<?php

namespace App\Contracts\Enums;

interface HasLabel
{
    public function label(): string;
}

interface HasColor
{
    public function color(): string;
}
```

### Annotated Example:

```php
<?php

namespace App\Enums;

use App\Contracts\Enums\HasColor;
use App\Contracts\Enums\HasLabel;

enum OrderStatus: string implements HasLabel, HasColor
{
    // -------------------------------------------------------------------------
    // 1. Cases
    // -------------------------------------------------------------------------

    case Pending   = 'pending';
    case Complete  = 'complete';
    case Cancelled = 'cancelled';
    case Refunded  = 'refunded';

// -------------------------------------------------------------------------
    // 2. Implemented Interface Methods
    // -------------------------------------------------------------------------

    public function label(): string
    {
        return match($this) {
            self::Pending   => 'Pending',
            self::Complete  => 'Complete',
            self::Cancelled => 'Cancelled',
            self::Refunded  => 'Refunded',
        };
    }

    public function color(): string
    {
        return match($this) {
            self::Pending   => 'yellow',
            self::Complete  => 'green',
            self::Cancelled => 'red',
            self::Refunded  => 'purple',
        };
    }

// -------------------------------------------------------------------------
    // 3. Instance Helper Methods (non-interface)
    // -------------------------------------------------------------------------

    public function icon(): string
    {
        return match($this) {
            self::Pending   => 'clock',
            self::Complete  => 'check-circle',
            self::Cancelled => 'x-circle',
            self::Refunded  => 'refresh-cw',
        };
    }

    public function isFinal(): bool
    {
        return match($this) {
            self::Complete, self::Cancelled, self::Refunded => true,
            default => false,
        };
    }

    public function canTransitionTo(self $next): bool
    {
        return match($this) {
            self::Pending   => in_array($next, [self::Complete, self::Cancelled]),
            self::Complete  => $next === self::Refunded,
            default         => false,
        };
    }

// -------------------------------------------------------------------------
    // 4. Static Helper Methods
    // -------------------------------------------------------------------------

    /**
     * Safe parse from untrusted input. Always prefer over from() externally.
     */
    public static function tryFromLabel(string $label): ?self
    {
        foreach (self::cases() as $case) {
            if ($case->label() === $label) {
                return $case;
            }
        }

        return null;
    }

    /**
     * Returns array suitable for Livewire/Blade select inputs.
     */
    public static function options(): array
    {
        return array_map(
            fn (self $case) => ['value' => $case->value, 'label' => $case->label()],
            self::cases(),
        );
    }

    /**
     * Returns plain string values — useful for Rule::in() validation.
     */
    public static function values(): array
    {
        return array_column(self::cases(), 'value');
    }

    /**
     * Returns only the cases that represent a terminal state.
     */
    public static function finalStates(): array
    {
        return array_filter(self::cases(), fn (self $case) => $case->isFinal());
    }
}
```

------

## 11. Observers

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

- In Laravel 12, register observers on the model using the **`#[ObservedBy]`** attribute. Do not register them in Service Providers.
- **`withoutObservers()`** is used when you need to bypass all observers for a specific operation (e.g. data seeding, bulk updates). Use it deliberately and always add a comment explaining why.
- Only implement the observer methods you actually need. Empty methods are noise — omit them entirely.
- Observers should remain **free of business logic**. They are event hooks, not the place to orchestrate complex operations. Dispatch jobs or events from here; do not perform multi-step workflows inline.
- Avoid triggering database writes in `saving`/`creating` that themselves trigger observers — this can cause infinite loops or unexpected cascades.
- Keep private helpers at the bottom to support observer method implementations.

### Model Registration (preferred in Laravel 12):

```php
// On the Model class — no Service Provider registration needed.
#[ObservedBy(OrderObserver::class)]
class Order extends Model { ... }
```

### Bypassing Observers:

```php
// Use withoutObservers() for bulk operations where observer side-effects
// would be incorrect (e.g. sending 10,000 notification emails during a migration).
Order::withoutObservers(function (): void {
    Order::query()->whereNull('reference')->each(function (Order $order): void {
        $order->update(['reference' => 'LEGACY-' . $order->id]);
    });
});
```

### Annotated Example:

```php
<?php

namespace App\Observers;

use App\Models\Order;
use App\Jobs\SendOrderCreatedNotification;
use App\Jobs\ReleaseOrderInventory;
use Illuminate\Support\Str;
use Illuminate\Support\Facades\Log;

class OrderObserver
{
    // -------------------------------------------------------------------------
    // 1. creating / created
    // -------------------------------------------------------------------------

    public function creating(Order $order): void
    {
        // Set server-generated values before the record is persisted.
        $order->reference ??= $this->generateReference();
    }

    public function created(Order $order): void
    {
        // Dispatch a job rather than sending inline — keeps the observer thin.
        SendOrderCreatedNotification::dispatch($order);
    }

// -------------------------------------------------------------------------
    // 2. updating / updated
    // -------------------------------------------------------------------------

    public function updating(Order $order): void
    {
        if ($order->isDirty('status')) {
            Log::info('Order status transition', [
                'order_id' => $order->id,
                'from'     => $order->getOriginal('status'),
                'to'       => $order->status->value,
            ]);
        }
    }

    public function updated(Order $order): void
    {
        if ($order->wasChanged('status')) {
            \App\Events\OrderStatusChanged::dispatch(
                $order,
                $order->getOriginal('status'),
                $order->status,
            );
        }
    }

// -------------------------------------------------------------------------
    // 3. saving / saved
    // (Omitted — not required for this model)
    // -------------------------------------------------------------------------

// -------------------------------------------------------------------------
    // 4. deleting / deleted
    // -------------------------------------------------------------------------

    public function deleting(Order $order): void
    {
        // Guard: prevent deletion of completed orders at the persistence layer.
        abort_if(
            $order->isComplete(),
            422,
            'Completed orders cannot be deleted.'
        );
    }

    public function deleted(Order $order): void
    {
        Log::info('Order soft-deleted', ['order_id' => $order->id]);
    }

// -------------------------------------------------------------------------
    // 5. restoring / restored
    // -------------------------------------------------------------------------

    public function restoring(Order $order): void
    {
        // Validate business rules before a soft-deleted record is restored.
        abort_if(
            $order->user->isSuspended(),
            422,
            'Cannot restore an order belonging to a suspended user.'
        );
    }

    public function restored(Order $order): void
    {
        Log::info('Order restored', ['order_id' => $order->id]);
    }

// -------------------------------------------------------------------------
    // 6. forceDeleting / forceDeleted
    // -------------------------------------------------------------------------

    public function forceDeleting(Order $order): void
    {
        // Clean up child records that do not cascade automatically.
        $order->items()->forceDelete();
    }

    public function forceDeleted(Order $order): void
    {
        // Dispatch a job to release reserved inventory.
        ReleaseOrderInventory::dispatch($order->id);

        Log::warning('Order permanently deleted', ['order_id' => $order->id]);
    }

// -------------------------------------------------------------------------
    // 7. Private Helper Methods
    // -------------------------------------------------------------------------

    private function generateReference(): string
    {
        return 'ORD-' . strtoupper(Str::random(8));
    }
}
```

------

## 12. Quick Reference Card

| Layer                   | Standard Order                                               | Key Principle                                                |
| ----------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| **Models**              | Constants → Traits → Properties → booted() → casts() → Relationships → Scopes → Accessors/Mutators → Business Methods → newFactory() | Data contract first, behaviour last                          |
| **Controllers**         | middleware() → Constructor → Resource methods → Custom actions → Private helpers | Thin; delegate everything; authorize early                   |
| **Form Requests**       | authorize() → rules() → stopOnFirstFailure() → messages() → attributes() → prepareForValidation() → passedValidation() → after() → Private helpers | Mirror the validation lifecycle exactly                      |
| **Service Classes**     | Constructor → Primary public methods → Secondary public methods → Protected → Private | Implement a contract interface; `readonly` class; wrap mutations in `DB::transaction()` |
| **Livewire Components** | Traits → Public props → Attributed props (#[Url], #[Locked]) → Protected props → #[Computed] → #[Validate] → boot() → mount() → Lifecycle hooks → #[On] listeners → Action methods → render() → Private helpers | State first, lifecycle second, actions third; `render()` is always logic-free |
| **Migrations**          | id → Foreign columns → Domain columns → Status/enum → JSON → Booleans → timestamps() → softDeletes() → Indexes → FK constraints | Money as integers; constraints always last; comments on non-obvious columns |
| **Service Providers**   | $bindings/$singletons → register() → boot() → Private helpers | `register()` = container only; `boot()` = bootstrapping; extract private helpers |
| **API Resources**       | $wrap/$preserveKeys → toArray() → with() → withResponse()    | Use conditional helpers; never write `if` inside `toArray()` |
| **Policies**            | before() → viewAny() → view() → create() → update() → delete() → restore() → forceDelete() → Custom methods → Private helpers | `before()` returns `null` to fall through, never `false`; name custom methods after the action |
| **Enums**               | Cases → Interface methods → Instance helpers → Static helpers | Implement shared interfaces; always use `tryFrom()` for untrusted input |
| **Observers**           | creating/created → updating/updated → saving/saved → deleting/deleted → restoring/restored → forceDeleting/forceDeleted → Private helpers | Register via `#[ObservedBy]`; dispatch jobs not inline logic; use `withoutObservers()` for bulk ops |

------

> **Revision:** Laravel 12 · PHP 8.3 · Livewire 3 **Maintained by:** Architecture Team