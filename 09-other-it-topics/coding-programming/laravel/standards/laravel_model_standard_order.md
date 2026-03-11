# Laravel Model Standard Order

### Standard Order:

1. **Constants** (STATUS_*, ROLE_*, TYPE_*, etc.)
2. **Traits** (HasFactory, SoftDeletes, etc.)
3. **Properties** ($fillable, $guarded, $hidden, $appends, $with, $perPage, etc.)
4. **boot() / booted() methods**
5. **casts() method**
6. **Relationships**
7. **Scopes** (local & global)
8. **Accessors & Mutators** (Attribute::make())
9. **Business methods**
10. **newFactory() method** (consistently at the end)

------

### Annotated Example:

```php
<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Attributes\Scope;
use Illuminate\Database\Eloquent\Builder;
use Illuminate\Database\Eloquent\Casts\Attribute;
use Illuminate\Database\Eloquent\Factories\HasFactory;
use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\Relations\BelongsTo;
use Illuminate\Database\Eloquent\Relations\HasMany;
use Illuminate\Database\Eloquent\SoftDeletes;

class Order extends Model
{
    // -------------------------------------------------------------------------
    // 1. Constants
    // -------------------------------------------------------------------------

    const STATUS_PENDING   = 'pending';
    const STATUS_COMPLETE  = 'complete';
    const STATUS_CANCELLED = 'cancelled';

// -------------------------------------------------------------------------
    // 2. Traits
    // -------------------------------------------------------------------------

    use HasFactory, SoftDeletes;

// -------------------------------------------------------------------------
    // 3. Properties
    // -------------------------------------------------------------------------

    protected $fillable = [
        'user_id',
        'status',
        'total',
        'notes',
    ];

    protected $hidden = [
        'internal_reference',
    ];

    protected $appends = [
        'display_total',
    ];

// -------------------------------------------------------------------------
    // 4. boot() / booted()
    // -------------------------------------------------------------------------

    protected static function booted(): void
    {
        static::creating(function (Order $order) {
            $order->status = self::STATUS_PENDING;
        });
    }

// -------------------------------------------------------------------------
    // 5. casts()
    // -------------------------------------------------------------------------

    protected function casts(): array
    {
        return [
            'total'      => 'integer',
            'metadata'   => 'array',
            'placed_at'  => 'datetime',
        ];
    }

// -------------------------------------------------------------------------
    // 6. Relationships
    // -------------------------------------------------------------------------

    public function user(): BelongsTo
    {
        return $this->belongsTo(User::class);
    }

    public function items(): HasMany
    {
        return $this->hasMany(OrderItem::class);
    }

// -------------------------------------------------------------------------
    // 7. Scopes
    // -------------------------------------------------------------------------

    #[Scope]
    protected function pending(Builder $query): void
    {
        $query->where('status', self::STATUS_PENDING);
    }

    #[Scope]
    protected function complete(Builder $query): void
    {
        $query->where('status', self::STATUS_COMPLETE);
    }

// -------------------------------------------------------------------------
    // 8. Accessors & Mutators
    // -------------------------------------------------------------------------

    protected function displayTotal(): Attribute
    {
        return Attribute::make(
            get: fn () => number_format($this->total / 100, 2),
        );
    }

    protected function notes(): Attribute
    {
        return Attribute::make(
            get: fn (?string $value) => $value ?? '',
            set: fn (string $value)  => trim($value),
        );
    }

// -------------------------------------------------------------------------
    // 9. Business Methods
    // -------------------------------------------------------------------------

    public function cancel(): bool
    {
        return $this->update(['status' => self::STATUS_CANCELLED]);
    }

    public function complete(): bool
    {
        return $this->update(['status' => self::STATUS_COMPLETE]);
    }

    public function isPending(): bool
    {
        return $this->status === self::STATUS_PENDING;
    }

// -------------------------------------------------------------------------
    // 10. newFactory()
    // -------------------------------------------------------------------------

    protected static function newFactory(): OrderFactory
    {
        return OrderFactory::new();
    }
}
```
