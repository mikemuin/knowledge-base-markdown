# Eloquent Deep Dive: Query Building & Relationships

## Part I: Query Building

Query building in Eloquent exists on a **spectrum of abstraction**—from simple model methods to complex conditional queries. Understanding this spectrum is critical to writing efficient, maintainable code.

### Core Concepts

1. **Lazy Evaluation**: Queries aren't executed until you request results (terminating methods)
2. **Method Chaining**: Fluent interface allows building complex queries step-by-step
3. **Query Builder Inheritance**: Eloquent extends Laravel's base Query Builder
4. **Scopes**: Encapsulated, reusable query logic
5. **Soft Deletes**: Non-destructive deletion with automatic query filtering

### Retrieval Methods (Terminating Operations)

These actually execute the query and return results:

- **`get()`** - Retrieve collection of models
- **`first()`** - Retrieve first matching model (or null)
- **`firstOr(callable)`** - First model or execute callback
- **`firstOrFail()`** - First model or throw ModelNotFoundException
- **`find($id)`** - Retrieve by primary key
- **`findOr($id, callable)`** - Find or execute callback
- **`findOrFail($id)`** - Find or throw exception
- **`findMany([ids])`** - Retrieve multiple by primary keys
- **`sole()`** - Retrieve exactly one record or throw exception
- **`value('column')`** - Retrieve single column value from first record
- **`pluck('column')`** - Retrieve collection of single column values
- **`count()`** - Get count of records
- **`exists()`** - Determine if records exist
- **`doesntExist()`** - Inverse of exists()

### Constraint Methods (Non-Terminating)

These build the query but don't execute it:

**Basic Constraints:**

- **`where('column', 'value')`** - Basic WHERE clause
- **`where('column', '>', 'value')`** - WHERE with operator
- **`where([conditions])`** - Array of conditions (AND)
- **`orWhere('column', 'value')`** - OR WHERE clause
- **`whereNot('column', 'value')`** - WHERE NOT clause
- **`whereBetween('column', [min, max])`** - BETWEEN clause
- **`whereNotBetween()`** - NOT BETWEEN
- **`whereIn('column', [values])`** - IN clause
- **`whereNotIn('column', [values])`** - NOT IN clause
- **`whereNull('column')`** - IS NULL
- **`whereNotNull('column')`** - IS NOT NULL
- **`whereDate('column', 'date')`** - Compare date portion
- **`whereMonth()`, `whereDay()`, `whereYear()`, `whereTime()`** - Temporal comparisons
- **`whereColumn('col1', 'col2')`** - Compare two columns

**Advanced Constraints:**

- **`whereExists(callable)`** - EXISTS subquery
- **`whereRaw('sql', [bindings])`** - Raw WHERE clause
- **`whereJsonContains('column->path', 'value')`** - JSON column queries
- **`whereJsonLength('column->path', operator, value)`** - JSON array length
- **`whereLike('column', '%pattern%')`** - Case-insensitive LIKE
- **`whereFullText('column', 'search')`** - Full-text search

**Conditional Application:**

- **`when(condition, callable)`** - Apply query modifier conditionally
- **`unless(condition, callable)`** - Inverse of when

**Ordering & Limiting:**

- **`orderBy('column', 'direction')`** - ORDER BY clause
- **`orderByDesc('column')`** - Shorthand for DESC
- **`latest('column')`** - Order by DESC (defaults to created_at)
- **`oldest('column')`** - Order by ASC
- **`inRandomOrder()`** - Random ordering
- **`reorder()`** - Remove existing orderings
- **`skip($n)` / `offset($n)`** - Skip records
- **`take($n)` / `limit($n)`** - Limit results
- **`forPage($page, $perPage)`** - Pagination helper

**Grouping & Aggregation:**

- **`groupBy('column')`** - GROUP BY
- **`having('column', 'operator', 'value')`** - HAVING clause
- **`distinct()`** - SELECT DISTINCT

**Selecting Columns:**

- **`select('col1', 'col2')`** - Specify columns
- **`selectRaw('sql', [bindings])`** - Raw select
- **`addSelect('column')`** - Add to existing select

**Joins:**

- **`join('table', 'col1', '=', 'col2')`** - INNER JOIN
- **`leftJoin()`**, **`rightJoin()`**, **`crossJoin()`** - Other join types
- **`joinSub(query, 'alias')`** - Join with subquery

### Aggregation Methods

- **`count('column')`** - Count records
- **`max('column')`** - Maximum value
- **`min('column')`** - Minimum value
- **`avg('column')`** - Average value
- **`sum('column')`** - Sum of values

### Scopes

**Local Scopes** (defined in model):

```php
public function scopeActive($query) {
    return $query->where('active', true);
}

// Usage: Post::active()->get();
```

**Global Scopes** (automatically applied):

```php
protected static function booted() {
    static::addGlobalScope('active', function ($query) {
        $query->where('active', true);
    });
}

// Remove: Post::withoutGlobalScope('active')->get();
```

**Scope Methods:**

- **`withoutGlobalScope('name')`** - Remove specific global scope
- **`withoutGlobalScopes()`** - Remove all global scopes

### Chunking & Iteration

For processing large datasets efficiently:

- **`chunk($count, callable)`** - Process records in chunks
- **`chunkById($count, callable)`** - Chunk using ID (safer for updates)
- **`lazy($chunkSize)`** - Lazy collection (memory efficient)
- **`lazyById($chunkSize)`** - Lazy by ID
- **`cursor()`** - Stream results (single query, low memory)
- **`each(callable)`** - Iterate without loading all into memory

### Mutation Methods

- **`update([attributes])`** - Mass update
- **`increment('column', $amount)`** - Increment value
- **`decrement('column', $amount)`** - Decrement value
- **`delete()`** - Delete matching records
- **`forceDelete()`** - Permanently delete (soft deletes)
- **`restore()`** - Restore soft deleted records

### Debugging Methods

- **`toSql()`** - Get SQL query string
- **`dd()`** - Dump query and die
- **`dump()`** - Dump query and continue
- **`explain()`** - Get query execution plan

------

## Part II: Relationships

Relationships are Eloquent's **architectural superpower**—they transform foreign keys into object graphs, enabling you to traverse your data model naturally.

### Core Concepts

1. **Foreign Key Conventions**: Eloquent assumes `{model}_id` naming
2. **Inverse Relationships**: Define both sides for full traversal
3. **Eager Loading**: Load relationships upfront to avoid N+1
4. **Lazy Loading**: Load relationships on-demand (default)
5. **Relationship Constraints**: Chain query methods on relationships
6. **Polymorphic Relations**: One model belongs to multiple types

### One-to-One Relationships

**Methods:**

- **`hasOne(Model::class, 'foreign_key', 'local_key')`** - Define ownership
- **`belongsTo(Model::class, 'foreign_key', 'owner_key')`** - Define belonging

**Concepts:**

```php
// User has one Profile
User → hasOne → Profile (profiles.user_id references users.id)

// Profile belongs to User
Profile → belongsTo → User
```

### One-to-Many Relationships

**Methods:**

- **`hasMany(Model::class, 'foreign_key', 'local_key')`** - One has many
- **`belongsTo(Model::class, 'foreign_key', 'owner_key')`** - Many belong to one

**Concepts:**

```php
// User has many Posts
User → hasMany → Post (posts.user_id references users.id)

// Post belongs to User
Post → belongsTo → User
```

### Many-to-Many Relationships

**Methods:**

- **`belongsToMany(Model::class, 'pivot_table', 'foreign_key', 'related_key')`**

**Pivot Table Methods:**

- **`withPivot('column1', 'column2')`** - Include extra pivot columns
- **`withTimestamps()`** - Include created_at/updated_at on pivot
- **`as('name')`** - Rename pivot accessor
- **`wherePivot('column', 'value')`** - Constrain by pivot column
- **`wherePivotIn('column', [values])`** - Pivot IN clause
- **`wherePivotBetween('column', [min, max])`** - Pivot BETWEEN

**Attach/Detach/Sync:**

- **`attach($id, ['pivot_data' => 'value'])`** - Add relationship
- **`detach($id)`** - Remove relationship (or all if no ID)
- **`sync([ids])`** - Synchronize relationships (add/remove to match)
- **`syncWithoutDetaching([ids])`** - Add new, keep existing
- **`toggle([ids])`** - Add if missing, remove if present
- **`updateExistingPivot($id, ['pivot_data'])`** - Update pivot data

**Concepts:**

```php
// User belongs to many Roles
// Role belongs to many Users
// Pivot table: role_user (role_id, user_id)

$user->roles()->attach($roleId, ['expires_at' => now()->addYear()]);
```

### Has-One-Through

**Method:**

- **`hasOneThrough(FinalModel::class, ThroughModel::class, 'through_fk', 'final_fk', 'local_key', 'through_local_key')`**

**Concept:**

```php
// Country → User → Phone
// Country has one Phone through User
Country → hasOneThrough → Phone (via User)
```

### Has-Many-Through

**Method:**

- **`hasManyThrough(FinalModel::class, ThroughModel::class, 'through_fk', 'final_fk', 'local_key', 'through_local_key')`**

**Concept:**

```php
// Country → User → Post
// Country has many Posts through Users
Country → hasManyThrough → Post (via User)
```

### Polymorphic Relationships

**One-to-One/One-to-Many Polymorphic:**

**Methods:**

- **`morphTo('name')`** - Define polymorphic belonging (many → one)
- **`morphOne(Model::class, 'name')`** - Define morphOne (one → one)
- **`morphMany(Model::class, 'name')`** - Define morphMany (one → many)

**Concept:**

```php
// Image belongs to Post OR User
// Images table: imageable_id, imageable_type

Image → morphTo('imageable')
Post → morphMany(Image::class, 'imageable')
User → morphMany(Image::class, 'imageable')
```

**Many-to-Many Polymorphic:**

**Methods:**

- **`morphToMany(Model::class, 'name', 'pivot_table')`**
- **`morphedByMany(Model::class, 'name', 'pivot_table')`**

**Concept:**

```php
// Posts and Videos can have many Tags
// Tags can belong to many Posts/Videos
// Pivot: taggables (tag_id, taggable_id, taggable_type)

Post → morphToMany(Tag::class, 'taggable')
Tag → morphedByMany(Post::class, 'taggable')
```

### Relationship Loading

**Eager Loading:**

- **`with('relation')`** - Load relationship
- **`with(['relation1', 'relation2'])`** - Load multiple
- **`with('relation:column1,column2')`** - Select specific columns
- **`with('relation' => function($query) {})`** - Constrain relationship query
- **`with('relation.nested')`** - Nested eager loading

**Lazy Eager Loading:**

- **`load('relation')`** - Load after retrieval
- **`loadMissing('relation')`** - Load only if not already loaded

**Relationship Existence:**

- **`has('relation')`** - WHERE EXISTS relationship
- **`has('relation', '>=', $count)`** - Has at least N related
- **`whereHas('relation', callable)`** - Constrain by relationship query
- **`doesntHave('relation')`** - WHERE NOT EXISTS
- **`whereDoesntHave('relation', callable)`** - Inverse of whereHas
- **`withCount('relation')`** - Add count column
- **`withCount(['relation' => function($query) {}])`** - Constrained count
- **`withSum('relation', 'column')`** - Add sum column
- **`withAvg()`, `withMin()`, `withMax()`** - Other aggregates

**Relationship Querying:**

- **`whereRelation('relation', 'column', 'value')`** - Constraint shorthand
- **`whereMorphRelation('morph', [Types], 'column', 'value')`** - Polymorphic constraint

### Touching Parent Timestamps

**Method:**

- **`touch()`** - Update model's timestamps
- Define **`$touches = ['relation']`** in model to auto-update parent

**Concept:** When a Comment is updated, touch its parent Post's `updated_at`.

------

## The Synthesis

**Query Building** gives you the *vocabulary* to express data requirements.
**Relationships** give you the *grammar* to express data structure.

Together, they form a complete language for data manipulation that's both expressive and performant. The master architect knows:

- When to eager load (`with`) vs lazy load (default)
- When to use `whereHas` (existence check) vs `with` (data retrieval)
- When relationships simplify code vs when raw joins perform better
- How to chain relationship queries for surgical data retrieval

This isn't just API knowledge—it's architectural fluency.

---

## Eloquent Mastery: A Blog System Deep Dive

Let me walk you through a comprehensive exploration using a realistic blog system. We'll build understanding progressively, from simple queries to advanced relationship patterns.

------

## The Domain Model

First, let's establish our **data architecture**:

```
Users (authentication/profiles)
├── Admins (extends User with admin privileges)
├── Authors (extends User with publishing rights)
└── Regular Users (can comment only)

Posts (content)
├── written by Authors
├── can have many Comments
└── can have many Tags (many-to-many)

Comments (engagement)
├── belong to Posts
├── belong to Users
└── can be nested (self-referential)

Tags (categorization)
└── belong to many Posts
```

### Database Schema Context

```php
// users table
id, name, email, role (enum: admin, author, user), bio, avatar, created_at, updated_at

// posts table
id, author_id, title, slug, content, excerpt, status (enum: draft, published, archived),
published_at, views_count, featured, created_at, updated_at, deleted_at (soft deletes)

// comments table
id, post_id, user_id, parent_id (nullable, for nesting), content, approved,
created_at, updated_at

// tags table
id, name, slug, created_at, updated_at

// post_tag pivot table
post_id, tag_id, created_at
```

------

## Part I: Query Building Exploration

### 1. Basic Retrieval - Starting Simple

```php
// Get all published posts
$posts = Post::where('status', 'published')->get();

// Get a specific post by slug
$post = Post::where('slug', 'eloquent-mastery')->first();

// Or fail if not found (useful for route model binding)
$post = Post::where('slug', $slug)->firstOrFail();

// Find by primary key
$post = Post::find(1);
$post = Post::findOrFail(1); // Throw 404 if not found
```

**Teaching Point**: Notice how `first()` and `get()` are **terminating methods**. Before them, you're building a query. After them, you have results.

### 2. Multiple Constraints - Building Complexity

```php
// Published posts by specific author, ordered by date
$posts = Post::where('status', 'published')
    ->where('author_id', $authorId)
    ->orderBy('published_at', 'desc')
    ->get();

// Featured posts published in the last 30 days
$posts = Post::where('featured', true)
    ->where('status', 'published')
    ->where('published_at', '>=', now()->subDays(30))
    ->get();

// Alternative: using whereDate for cleaner date comparisons
$posts = Post::where('featured', true)
    ->whereDate('published_at', '>=', now()->subDays(30))
    ->get();
```

### 3. Advanced WHERE Clauses - Precision Filtering

```php
// Posts with high engagement (views between 1000-10000)
$posts = Post::whereBetween('views_count', [1000, 10000])->get();

// Posts by multiple specific authors
$authorIds = [1, 5, 8, 12];
$posts = Post::whereIn('author_id', $authorIds)->get();

// Posts that have NEVER been published
$drafts = Post::whereNull('published_at')->get();

// Posts published this month
$posts = Post::whereMonth('published_at', now()->month)
    ->whereYear('published_at', now()->year)
    ->get();
```

**Teaching Point**: Each `where*` method corresponds to SQL patterns. `whereIn` = `IN ()`, `whereBetween` = `BETWEEN`, etc. Eloquent is **translating your intent** into SQL.

### 4. Conditional Queries - Dynamic Filtering

This is **critical for real applications** where queries change based on user input:

```php
// Search posts with optional filters
public function searchPosts(Request $request)
{
    $query = Post::query(); // Start with base query

    // Apply status filter if provided
    $query->when($request->status, function ($q, $status) {
        $q->where('status', $status);
    });

    // Apply author filter if provided
    $query->when($request->author_id, function ($q, $authorId) {
        $q->where('author_id', $authorId);
    });

    // Apply search term if provided
    $query->when($request->search, function ($q, $search) {
        $q->where('title', 'like', "%{$search}%")
          ->orWhere('content', 'like', "%{$search}%");
    });

    // Apply date range if provided
    $query->when($request->from_date, function ($q, $date) {
        $q->whereDate('published_at', '>=', $date);
    });

    return $query->orderBy('published_at', 'desc')->paginate(15);
}
```

**Teaching Point**: The `when()` method is **architectural elegance**. It eliminates messy if-statements and keeps your query chain fluent. The query is only modified **if the condition is truthy**.

### 5. Scopes - Reusable Query Logic

Instead of repeating the same WHERE clauses, **encapsulate them**:

```php
// In Post model
class Post extends Model
{
    // Local scope: published posts
    public function scopePublished($query)
    {
        return $query->where('status', 'published')
                     ->whereNotNull('published_at');
    }

    // Local scope: featured posts
    public function scopeFeatured($query)
    {
        return $query->where('featured', true);
    }

    // Local scope: recent posts (within days)
    public function scopeRecent($query, $days = 7)
    {
        return $query->whereDate('published_at', '>=', now()->subDays($days));
    }

    // Local scope: by author
    public function scopeByAuthor($query, $authorId)
    {
        return $query->where('author_id', $authorId);
    }

    // Local scope: search by term
    public function scopeSearch($query, $term)
    {
        return $query->where(function ($q) use ($term) {
            $q->where('title', 'like', "%{$term}%")
              ->orWhere('content', 'like', "%{$term}%");
        });
    }
}

// Usage: Chain scopes like English sentences
$posts = Post::published()->featured()->recent(30)->get();

$posts = Post::published()
    ->byAuthor($authorId)
    ->search($searchTerm)
    ->orderBy('views_count', 'desc')
    ->paginate(10);
```

**Teaching Point**: Scopes are **named query fragments**. They make your code self-documenting and testable. Notice how `recent(30)` accepts a parameter—scopes can be dynamic.

### 6. Aggregations - Statistical Queries

```php
// Count published posts
$count = Post::published()->count();

// Average views across all posts
$avgViews = Post::published()->avg('views_count');

// Most viewed post
$maxViews = Post::published()->max('views_count');

// Total views across all posts
$totalViews = Post::published()->sum('views_count');

// Author with most posts
$topAuthor = Post::published()
    ->select('author_id')
    ->groupBy('author_id')
    ->orderByRaw('COUNT(*) DESC')
    ->first();
```

### 7. Chunking - Memory-Efficient Processing

For operations on **thousands of posts** (like sending notifications):

```php
// Process 100 posts at a time to avoid memory exhaustion
Post::published()->chunk(100, function ($posts) {
    foreach ($posts as $post) {
        // Send email notification
        Mail::to($post->author)->send(new PostPublishedMail($post));
    }
});

// Safer for updates (uses primary key ordering)
Post::chunkById(100, function ($posts) {
    foreach ($posts as $post) {
        $post->update(['processed' => true]);
    }
});

// Lazy collections: memory-efficient iteration
Post::published()->lazy()->each(function ($post) {
    // Process one at a time without loading all into memory
});
```

**Teaching Point**: `chunk()` prevents memory overflow. It executes **multiple queries** behind the scenes, fetching 100 records at a time. Critical for batch operations.

### 8. Soft Deletes - Non-Destructive Deletion

```php
// In Post model
use SoftDeletes;

protected $dates = ['deleted_at'];

// Soft delete a post (sets deleted_at timestamp)
$post->delete();

// Query automatically excludes soft-deleted
$posts = Post::all(); // Only non-deleted

// Include soft-deleted
$posts = Post::withTrashed()->get();

// Only soft-deleted
$posts = Post::onlyTrashed()->get();

// Restore soft-deleted post
$post->restore();

// Permanently delete
$post->forceDelete();
```

**Teaching Point**: Soft deletes add a **global scope** automatically. Every query gets `WHERE deleted_at IS NULL` unless you explicitly include trashed records.

### 9. Debugging Queries - Understanding Execution

```php
// See the SQL query
$sql = Post::published()->featured()->toSql();
// SELECT * FROM posts WHERE status = ? AND featured = ?

// See SQL with bindings
Post::published()->featured()->dd();
// Dies and dumps complete query with values

// Explain query execution plan (performance analysis)
Post::published()->featured()->explain();
```

------

## Part II: Relationships Exploration

### 10. One-to-Many: Posts & Comments

```php
// In Post model
public function comments()
{
    return $this->hasMany(Comment::class);
}

// In Comment model
public function post()
{
    return $this->belongsTo(Post::class);
}

// Usage: Get all comments for a post
$comments = $post->comments;

// Get post from a comment
$post = $comment->post;

// Query relationship with constraints
$approvedComments = $post->comments()
    ->where('approved', true)
    ->orderBy('created_at', 'desc')
    ->get();

// Count comments without loading them
$commentCount = $post->comments()->count();
```

**Teaching Point**: Notice `$post->comments` (property) vs `$post->comments()` (method). The property **triggers lazy loading** and returns a collection. The method returns a **query builder** you can chain on.

### 11. One-to-Many: Authors & Posts

```php
// In User model (Authors are users with role='author')
public function posts()
{
    return $this->hasMany(Post::class, 'author_id');
}

// Scope to only get authors
public function scopeAuthors($query)
{
    return $query->where('role', 'author');
}

// In Post model
public function author()
{
    return $this->belongsTo(User::class, 'author_id');
}

// Usage: Get all posts by an author
$posts = $author->posts()->published()->get();

// Get author of a post
$authorName = $post->author->name;

// Find all authors who have published posts
$authors = User::authors()
    ->whereHas('posts', function ($query) {
        $query->where('status', 'published');
    })
    ->get();
```

### 12. Eager Loading - Solving N+1 Problem

This is **critical for performance**:

```php
// ❌ BAD: N+1 Problem
// 1 query for posts + N queries for each post's author
$posts = Post::published()->get();
foreach ($posts as $post) {
    echo $post->author->name; // Separate query each iteration!
}

// ✅ GOOD: Eager Loading
// 2 queries total: 1 for posts, 1 for all authors
$posts = Post::with('author')->published()->get();
foreach ($posts as $post) {
    echo $post->author->name; // No additional query
}

// Multiple relationships
$posts = Post::with(['author', 'comments'])->published()->get();

// Nested relationships
$posts = Post::with('comments.user')->published()->get();

// Constrain eager loaded relationships
$posts = Post::with(['comments' => function ($query) {
    $query->where('approved', true)
          ->orderBy('created_at', 'desc')
          ->limit(5);
}])->get();
```

**Teaching Point**: The N+1 problem is **silent performance death**. With 100 posts, you'd execute 101 queries instead of 2. Eloquent's `with()` solves this elegantly.

### 13. Relationship Existence Queries

Query posts **based on their relationships**:

```php
// Posts that HAVE comments
$posts = Post::has('comments')->get();

// Posts with at least 10 comments
$posts = Post::has('comments', '>=', 10)->get();

// Posts that DON'T have comments
$posts = Post::doesntHave('comments')->get();

// Posts with approved comments
$posts = Post::whereHas('comments', function ($query) {
    $query->where('approved', true);
})->get();

// Posts WITHOUT approved comments
$posts = Post::whereDoesntHave('comments', function ($query) {
    $query->where('approved', true);
})->get();

// Complex: Authors with published posts that have approved comments
$authors = User::authors()
    ->whereHas('posts', function ($query) {
        $query->where('status', 'published')
              ->whereHas('comments', function ($q) {
                  $q->where('approved', true);
              });
    })
    ->get();
```

**Teaching Point**: `has()` checks **existence**, `whereHas()` checks existence **with conditions**. This is how you filter models based on related data.

### 14. Counting Related Models

```php
// Load posts with comment counts
$posts = Post::withCount('comments')->get();

foreach ($posts as $post) {
    echo $post->comments_count; // No additional queries
}

// Count with constraints
$posts = Post::withCount([
    'comments as approved_comments_count' => function ($query) {
        $query->where('approved', true);
    }
])->get();

// Multiple counts
$posts = Post::withCount([
    'comments',
    'comments as pending_comments_count' => function ($query) {
        $query->where('approved', false);
    }
])->get();

// Sum, Avg, Max, Min
$posts = Post::withSum('comments', 'upvotes')
    ->withAvg('comments', 'rating')
    ->get();
```

**Teaching Point**: `withCount()` adds a derived column to your query. It's **much more efficient** than loading relationships just to count them.

### 15. Many-to-Many: Posts & Tags

```php
// In Post model
public function tags()
{
    return $this->belongsToMany(Tag::class)
                ->withTimestamps(); // Include created_at on pivot
}

// In Tag model
public function posts()
{
    return $this->belongsToMany(Post::class)
                ->withTimestamps();
}

// Usage: Get all tags for a post
$tags = $post->tags;

// Get all posts for a tag
$posts = $tag->posts()->published()->get();

// Attach tags to a post
$post->tags()->attach([1, 2, 3]);

// Attach with pivot data
$post->tags()->attach(1, ['created_at' => now()]);

// Detach specific tags
$post->tags()->detach([1, 2]);

// Detach all tags
$post->tags()->detach();

// Sync tags (add new, remove old)
$post->tags()->sync([1, 2, 3]); // Only these tags remain

// Sync without detaching existing
$post->tags()->syncWithoutDetaching([4, 5]);

// Toggle tags (add if missing, remove if present)
$post->tags()->toggle([1, 2, 3]);
```

**Teaching Point**: Many-to-many requires a **pivot table** (`post_tag`). The `sync()` method is particularly powerful—it makes the relationship match exactly what you specify, handling additions and deletions automatically.

### 16. Pivot Table Data & Queries

```php
// Extended pivot with extra columns
public function tags()
{
    return $this->belongsToMany(Tag::class)
                ->withPivot('added_by', 'notes')
                ->withTimestamps()
                ->as('categorization'); // Rename pivot accessor
}

// Access pivot data
foreach ($post->tags as $tag) {
    echo $tag->categorization->added_by;
    echo $tag->categorization->notes;
    echo $tag->categorization->created_at;
}

// Query using pivot data
$posts = Post::whereHas('tags', function ($query) use ($tagId) {
    $query->where('tags.id', $tagId)
          ->wherePivot('approved', true);
})->get();

// Update pivot data
$post->tags()->updateExistingPivot($tagId, [
    'notes' => 'Primary category'
]);
```

### 17. Self-Referential: Nested Comments

```php
// In Comment model
public function parent()
{
    return $this->belongsTo(Comment::class, 'parent_id');
}

public function replies()
{
    return $this->hasMany(Comment::class, 'parent_id');
}

// Get all top-level comments (no parent)
$topComments = $post->comments()
    ->whereNull('parent_id')
    ->with('replies.user') // Eager load nested replies with users
    ->get();

// Get comment thread (comment + all nested replies)
$comment = Comment::with('replies.replies.replies')->find($id);
```

**Teaching Point**: Self-referential relationships let models relate to themselves. Perfect for threaded comments, organizational hierarchies, category trees.

### 18. Polymorphic: Flexible Relationships

Imagine we add **Images** that can belong to Posts OR Users (avatars):

```php
// In Image model
public function imageable()
{
    return $this->morphTo();
}

// In Post model
public function images()
{
    return $this->morphMany(Image::class, 'imageable');
}

// In User model
public function avatar()
{
    return $this->morphOne(Image::class, 'imageable');
}

// Database: images table has imageable_id, imageable_type

// Usage
$postImage = $post->images()->create([
    'path' => 'images/post-hero.jpg'
]);

$userAvatar = $user->avatar()->create([
    'path' => 'avatars/john.jpg'
]);

// Get the parent from an image
$parent = $image->imageable; // Returns Post or User

// Query all posts that have images
$posts = Post::has('images')->get();
```

**Teaching Point**: Polymorphic relationships let one model belong to **multiple types of parents**. The `imageable_type` column stores the parent class name, `imageable_id` stores the ID.

### 19. Touching Parent Timestamps

```php
// In Comment model
protected $touches = ['post'];

// Now when a comment is created/updated, the post's updated_at is touched
$comment = new Comment(['content' => 'Great post!']);
$post->comments()->save($comment);
// $post->updated_at is now updated automatically
```

**Teaching Point**: Touching propagates updates up the relationship chain. Useful for cache invalidation and showing "last activity" on parent models.

### 20. Complex Real-World Query

Let's build a **dashboard query** that demonstrates multiple concepts:

```php
// Get featured posts with full engagement metrics
public function getDashboardPosts()
{
    return Post::query()
        // Only published, featured posts from last 90 days
        ->published()
        ->featured()
        ->recent(90)

        // Eager load relationships to avoid N+1
        ->with([
            'author:id,name,avatar',
            'comments' => fn($q) => $q->where('approved', true)
                                        ->with('user:id,name'),
            'tags:id,name,slug'
        ])

        // Add engagement metrics
        ->withCount([
            'comments',
            'comments as approved_comments_count' => fn($q) =>
                $q->where('approved', true)
        ])

        // Only posts with comments
        ->has('comments', '>=', 1)

        // Order by engagement
        ->orderBy('views_count', 'desc')

        // Paginate results
        ->paginate(20);
}
```

**Teaching Point**: This single chain demonstrates: scopes, eager loading with constraints, counting, relationship existence, ordering, and pagination. This is **production-grade Eloquent**.

------

## The Architectural Perspective

### When to Eager Load vs Lazy Load

```php
// ✅ Eager load when iterating over collections
$posts = Post::with('author')->get();

// ✅ Lazy load when you might not need the relationship
$post = Post::find($id);
if ($needsAuthor) {
    $author = $post->author; // Loaded only if needed
}

// ✅ Lazy eager loading when you decide after retrieval
$posts = Post::all();
if ($includeAuthors) {
    $posts->load('author');
}
```

### Performance Patterns

```php
// Counting: Don't load if you only need count
$count = $post->comments()->count(); // ✅ Single COUNT query
$count = $post->comments->count();   // ❌ Loads all comments

// Existence checking
$hasComments = $post->comments()->exists(); // ✅ EXISTS query
$hasComments = $post->comments->isNotEmpty(); // ❌ Loads all

// Selecting columns
Post::select('id', 'title', 'slug')->get(); // ✅ Only needed columns
Post::all(); // ❌ SELECT *
```

------

This exploration shows that Eloquent isn't just a query tool—it's a **language for expressing data relationships and requirements**. Master these patterns, and you'll write code that's both elegant and performant.