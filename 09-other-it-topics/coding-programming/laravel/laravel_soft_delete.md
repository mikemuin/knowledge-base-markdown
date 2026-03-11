# Laravel Soft Deletes

Great question — **soft deletes** in Laravel are handled **entirely at the Eloquent model level**, but your architecture (controllers, services, repositories) should consciously **respect or expose** soft-delete behavior as needed.

------

### 1. Model Layer: Where Soft Delete Lives

You enable it like this:

```php
use Illuminate\Database\Eloquent\SoftDeletes;

class Post extends Model
{
    use SoftDeletes;
}
```

Ensure your table has a `deleted_at` column:

```php
$table->softDeletes(); // in migration
```

------

### 2. How It Works in Laravel

| Operation               | Behavior                                      |
| ----------------------- | --------------------------------------------- |
| `$model->delete()`      | Sets `deleted_at` timestamp (not hard delete) |
| `Model::all()`          | Excludes soft-deleted records                 |
| `Model::withTrashed()`  | Includes deleted records                      |
| `Model::onlyTrashed()`  | Only soft-deleted records                     |
| `$model->restore()`     | Nullifies `deleted_at`, restores              |
| `$model->forceDelete()` | Truly deletes record                          |

------

### 3. Where It Belongs in Layered Architectures

#### a. Controller

- Might expose routes for `DELETE`, `PATCH /restore`, `DELETE /force`.

#### b. Service Layer

Soft-deleting a model:

```php
public function delete(Post $post): void {
    $post->delete();
}
```

Restoring:

```php
public function restore(int $id): void {
    Post::withTrashed()->findOrFail($id)->restore();
}
```

#### c. Repository Layer

Make sure your repository includes/excludes trashed as needed:

```php
public function findWithTrashed(int $id): ?Post {
    return Post::withTrashed()->find($id);
}
```

------

### 4. Best Practices (Laravel Way)

- Use soft deletes when data **should be recoverable** (users, posts, orders, etc.).
- Avoid soft deletes for audit-logged records or data that legally must not be retained.
- Expose restore/force delete intentionally (not by default).
- When listing, always consider if soft-deleted records should be shown or hidden.

