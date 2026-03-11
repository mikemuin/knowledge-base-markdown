# Laravel Application Layers - Complete Reference

## Database & Schema Layers

### 1. Migration Layer
Schema definitions with version control for database structure.

**When to use:** Every database change - tables, columns, indexes, foreign keys.

```php
Schema::create('posts', function (Blueprint $table) {
    $table->id();
    $table->foreignId('user_id')->constrained()->cascadeOnDelete();
    $table->string('title')->index();
    $table->enum('status', ['draft', 'published'])->default('draft');
});
```

### 2. Seeder Layer
Populate database with initial or test data.

**When to use:** Development data, production defaults (roles, settings), testing fixtures.

```php
class DatabaseSeeder extends Seeder
{
    public function run(): void
    {
        User::factory()->count(10)->create();
        Post::factory()->count(50)->create();
    }
}
```

### 3. Factory Layer
Generate fake model instances for testing and seeding.

**When to use:** Every model that needs test data or seeding capabilities.

```php
class PostFactory extends Factory
{
    public function definition(): array
    {
        return [
            'title' => fake()->sentence(),
            'content' => fake()->paragraphs(3, true),
            'user_id' => User::factory(),
        ];
    }
}
```

## Model & Data Layers

### 4. Model Layer (Eloquent ORM)
Database table representation with relationships, casts, and scopes.

**When to use:** Every database table needs a model. Define relationships and query scopes here.

```php
class Post extends Model
{
    protected $fillable = ['title', 'content', 'status'];
    protected $casts = ['published_at' => 'datetime', 'status' => PostStatus::class];

    public function author(): BelongsTo
    {
        return $this->belongsTo(User::class, 'user_id');
    }

    public function scopePublished($query)
    {
        return $query->where('status', PostStatus::Published);
    }
}
```

### 5. Enum Layer
Type-safe constants with methods, replacing magic strings.

**When to use:** Any field with fixed set of values (status, role, type). Prevents typos and provides IDE autocomplete.

```php
enum PostStatus: string
{
    case Draft = 'draft';
    case Published = 'published';
    case Archived = 'archived';

    public function label(): string
    {
        return match($this) {
            self::Draft => 'Draft',
            self::Published => 'Published',
            self::Archived => 'Archived',
        };
    }

    public function color(): string
    {
        return match($this) {
            self::Draft => 'gray',
            self::Published => 'green',
            self::Archived => 'red',
        };
    }
}
```

### 6. Cast Layer
Transform model attributes to/from database values.

**When to use:** Complex type conversions (JSON to objects, encrypted values, value objects).

```php
class MoneyCast implements CastsAttributes
{
    public function get($model, string $key, $value, array $attributes): Money
    {
        return new Money($value, $attributes[$key . '_currency']);
    }

    public function set($model, string $key, $value, array $attributes): array
    {
        return [
            $key => $value->getAmount(),
            $key . '_currency' => $value->getCurrency(),
        ];
    }
}

// In Model:
protected $casts = ['price' => MoneyCast::class];
```

### 7. DTO Layer (Data Transfer Objects)
Immutable data containers for passing data between layers.

**When to use:** Decouple HTTP input from models, type-safe service parameters, API contracts.

```php
readonly class PostData
{
    public function __construct(
        public string $title,
        public string $content,
        public PostStatus $status,
        public int $authorId,
        public ?Carbon $publishedAt = null,
    ) {}

    public static function fromRequest(Request $request): self
    {
        return new self(
            title: $request->input('title'),
            content: $request->input('content'),
            status: PostStatus::from($request->input('status')),
            authorId: $request->user()->id,
        );
    }

    public function toArray(): array
    {
        return [
            'title' => $this->title,
            'content' => $this->content,
            'status' => $this->status->value,
            'user_id' => $this->authorId,
            'published_at' => $this->publishedAt,
        ];
    }
}
```

### 8. Value Object Layer
Immutable objects representing domain concepts with validation.

**When to use:** Complex values with business rules (Email, Money, Address) that need validation.

```php
readonly class Email
{
    private function __construct(public string $value)
    {
        if (!filter_var($value, FILTER_VALIDATE_EMAIL)) {
            throw new InvalidArgumentException("Invalid email: {$value}");
        }
    }

    public static function from(string $email): self
    {
        return new self($email);
    }

    public function domain(): string
    {
        return Str::after($this->value, '@');
    }
}
```

## Repository & Query Layers

### 9. Repository Layer
Abstract database queries, enable testing with mock implementations.

**When to use:** Complex queries used across multiple services, when testing requires mocked data sources.

```php
interface PostRepositoryInterface
{
    public function findPublished(int $perPage = 15): LengthAwarePaginator;
    public function findBySlug(string $slug): ?Post;
    public function create(array $data): Post;
}

class PostRepository implements PostRepositoryInterface
{
    public function findPublished(int $perPage = 15): LengthAwarePaginator
    {
        return Post::query()
            ->published()
            ->with(['author', 'comments'])
            ->latest('published_at')
            ->paginate($perPage);
    }

    public function findBySlug(string $slug): ?Post
    {
        return Cache::remember(
            "post.{$slug}",
            3600,
            fn() => Post::where('slug', $slug)->firstOrFail()
        );
    }
}
```

### 10. Query Builder Layer
Reusable, chainable query logic for complex queries.

**When to use:** Complex queries with multiple conditions that need to be reusable across controllers/services.

```php
class PostQueryBuilder extends Builder
{
    public function published(): self
    {
        return $this->where('status', PostStatus::Published)
            ->whereNotNull('published_at');
    }

    public function byAuthor(User $author): self
    {
        return $this->where('user_id', $author->id);
    }

    public function withEngagement(): self
    {
        return $this->withCount(['comments', 'likes'])
            ->withAvg('ratings', 'score');
    }

    public function recent(int $days = 7): self
    {
        return $this->where('published_at', '>=', now()->subDays($days));
    }
}

// In Model:
public function newEloquentBuilder($query): PostQueryBuilder
{
    return new PostQueryBuilder($query);
}

// Usage:
Post::query()->published()->byAuthor($user)->withEngagement()->get();
```

## Business Logic Layers

### 11. Action Layer
Single-responsibility operations, reusable across contexts.

**When to use:** Discrete operations that do ONE thing well, needed in multiple places (controllers, jobs, commands).

```php
class CreatePost
{
    public function __construct(
        private GenerateSlug $slugGenerator
    ) {}

    public function execute(PostData $data): Post
    {
        return DB::transaction(function () use ($data) {
            $post = Post::create([
                ...$data->toArray(),
                'slug' => $this->slugGenerator->from($data->title),
            ]);

            event(new PostCreated($post));

            return $post->load('author');
        });
    }
}
```

### 12. Service Layer
Orchestrate multiple actions, coordinate complex business workflows.

**When to use:** Multi-step operations, business logic spanning multiple models/actions.

```php
class PostService
{
    public function __construct(
        private CreatePost $createPost,
        private PublishPost $publishPost,
        private NotifySubscribers $notifySubscribers,
        private PostRepository $repository,
    ) {}

    public function createAndPublish(PostData $data): Post
    {
        $post = $this->createPost->execute($data);

        if ($data->status === PostStatus::Published) {
            $this->publishPost->execute($post);
            $this->notifySubscribers->execute($post);
        }

        return $post;
    }

    public function getPublishedPosts(int $perPage = 15): LengthAwarePaginator
    {
        return $this->repository->findPublished($perPage);
    }
}
```

### 13. Domain Service Layer
Pure business logic without infrastructure dependencies.

**When to use:** Complex calculations, business rules that are framework-agnostic and testable in isolation.

```php
class PostStatisticsService
{
    public function calculateEngagementScore(Post $post): float
    {
        $commentsWeight = $post->comments_count * 2;
        $likesWeight = $post->likes_count * 1;
        $viewsWeight = $post->views_count * 0.1;
        $ageInDays = $post->published_at->diffInDays(now());

        $rawScore = $commentsWeight + $likesWeight + $viewsWeight;
        $decayFactor = 1 / (1 + ($ageInDays / 7));

        return round($rawScore * $decayFactor, 2);
    }
}
```

## Authorization & Security Layers

### 14. Policy Layer
Resource authorization logic, gate access control.

**When to use:** Every model that needs authorization. Centralizes permission logic.

```php
class PostPolicy
{
    public function viewAny(User $user): bool
    {
        return true;
    }

    public function view(?User $user, Post $post): bool
    {
        return $post->status === PostStatus::Published ||
               $user?->id === $post->user_id;
    }

    public function create(User $user): bool
    {
        return $user->hasRole(['author', 'admin']);
    }

    public function update(User $user, Post $post): bool
    {
        return $user->id === $post->user_id || $user->isAdmin();
    }

    public function delete(User $user, Post $post): bool
    {
        return $user->isAdmin();
    }

    public function publish(User $user, Post $post): bool
    {
        return $user->isAdmin() ||
               ($user->isAuthor() && $user->id === $post->user_id);
    }
}
```

### 15. Gate Layer
Simple authorization checks not tied to models.

**When to use:** Feature flags, role checks, non-resource permissions.

```php
// In AuthServiceProvider:
Gate::define('access-admin-panel', function (User $user) {
    return $user->hasRole(['admin', 'moderator']);
});

Gate::define('moderate-comments', function (User $user) {
    return $user->hasRole(['admin', 'moderator']);
});

// Usage:
if (Gate::allows('access-admin-panel')) {
    // Show admin panel
}
```

### 16. Middleware Layer
HTTP request filtering and cross-cutting concerns.

**When to use:** Authentication checks, rate limiting, request transformation, logging.

```php
class EnsureUserIsAuthor
{
    public function handle(Request $request, Closure $next): Response
    {
        if (!$request->user()?->hasRole('author')) {
            abort(403, 'Only authors can access this resource');
        }

        return $next($request);
    }
}

class LogPostViews
{
    public function handle(Request $request, Closure $next): Response
    {
        $response = $next($request);

        if ($request->route('post')) {
            PostView::create([
                'post_id' => $request->route('post')->id,
                'user_id' => $request->user()?->id,
                'ip_address' => $request->ip(),
            ]);
        }

        return $response;
    }
}
```

## HTTP & API Layers

### 17. Request Layer (Form Requests)
Input validation and authorization combined.

**When to use:** Every form submission or API endpoint that accepts data. Keeps controllers thin.

```php
class StorePostRequest extends FormRequest
{
    public function authorize(): bool
    {
        return $this->user()->can('create', Post::class);
    }

    public function rules(): array
    {
        return [
            'title' => ['required', 'string', 'max:255', 'unique:posts,title'],
            'content' => ['required', 'string', 'min:100'],
            'status' => ['required', Rule::enum(PostStatus::class)],
            'tags' => ['sometimes', 'array'],
            'tags.*' => ['exists:tags,id'],
        ];
    }

    public function messages(): array
    {
        return [
            'content.min' => 'Post content must be at least 100 characters.',
        ];
    }

    public function toDTO(): PostData
    {
        return PostData::fromRequest($this);
    }
}
```

### 18. Controller Layer
HTTP request handling, delegates to services.

**When to use:** Every route needs a controller. Keep them thin - just coordinate between requests and services.

```php
class PostController extends Controller
{
    public function __construct(
        private PostService $postService
    ) {}

    public function index(): JsonResponse
    {
        $posts = $this->postService->getPublishedPosts(
            perPage: request('per_page', 15)
        );

        return PostResource::collection($posts);
    }

    public function store(StorePostRequest $request): JsonResponse
    {
        $post = $this->postService->createAndPublish(
            $request->toDTO()
        );

        return response()->json(
            new PostResource($post),
            201
        );
    }

    public function show(Post $post): JsonResponse
    {
        $this->authorize('view', $post);

        return response()->json(
            new PostResource($post->load(['author', 'comments']))
        );
    }
}
```

### 19. Resource Layer (API Transformers)
Consistent API response formatting and data transformation.

**When to use:** Every API endpoint. Provides consistent structure, hides internal fields, manages relationships.

```php
class PostResource extends JsonResource
{
    public function toArray(Request $request): array
    {
        return [
            'id' => $this->id,
            'title' => $this->title,
            'slug' => $this->slug,
            'excerpt' => Str::limit($this->content, 150),
            'content' => $this->when($this->isDetailView(), $this->content),
            'status' => [
                'value' => $this->status->value,
                'label' => $this->status->label(),
                'color' => $this->status->color(),
            ],
            'published_at' => $this->published_at?->toIso8601String(),
            'reading_time' => $this->reading_time . ' min',
            'author' => new UserResource($this->whenLoaded('author')),
            'comments' => CommentResource::collection($this->whenLoaded('comments')),
            'comments_count' => $this->whenCounted('comments'),
            'likes_count' => $this->whenCounted('likes'),
            'meta' => [
                'can_edit' => $request->user()?->can('update', $this->resource),
                'can_delete' => $request->user()?->can('delete', $this->resource),
            ],
        ];
    }

    private function isDetailView(): bool
    {
        return request()->route()->getName() === 'posts.show';
    }
}
```

### 20. Collection Resource Layer
Custom formatting for resource collections.

**When to use:** When collections need metadata, pagination customization, or aggregate data.

```php
class PostCollection extends ResourceCollection
{
    public function toArray(Request $request): array
    {
        return [
            'data' => $this->collection,
            'meta' => [
                'total' => $this->total(),
                'authors_count' => $this->collection->pluck('author_id')->unique()->count(),
                'average_reading_time' => round($this->collection->avg('reading_time'), 1),
            ],
            'links' => [
                'self' => request()->url(),
            ],
        ];
    }
}
```

## Event & Messaging Layers

### 21. Event Layer
Decouple side effects, enable pub/sub patterns.

**When to use:** When action triggers multiple consequences, async processing needed, or broadcasting to frontend.

```php
class PostPublished implements ShouldBroadcast
{
    use Dispatchable, InteractsWithSockets, SerializesModels;

    public function __construct(
        public Post $post
    ) {}

    public function broadcastOn(): array
    {
        return [
            new Channel('posts'),
            new PrivateChannel('user.' . $this->post->user_id),
        ];
    }

    public function broadcastAs(): string
    {
        return 'post.published';
    }

    public function broadcastWith(): array
    {
        return [
            'id' => $this->post->id,
            'title' => $this->post->title,
            'author' => $this->post->author->name,
        ];
    }
}
```

### 22. Listener Layer
Handle events, execute side effects.

**When to use:** Every event needs listeners. Separate concerns - each listener does one thing.

```php
class SendPostPublishedNotification implements ShouldQueue
{
    use InteractsWithQueue;

    public function handle(PostPublished $event): void
    {
        $subscribers = User::whereHas('subscriptions', function ($q) use ($event) {
            $q->where('author_id', $event->post->user_id);
        })->get();

        Notification::send(
            $subscribers,
            new NewPostNotification($event->post)
        );
    }

    public function failed(PostPublished $event, Throwable $exception): void
    {
        Log::error('Failed to send post notification', [
            'post_id' => $event->post->id,
            'error' => $exception->getMessage(),
        ]);
    }
}
```

### 23. Notification Layer
Multi-channel user notifications (email, SMS, Slack, database).

**When to use:** Informing users about events via email, SMS, push notifications, or in-app alerts.

```php
class NewPostNotification extends Notification implements ShouldQueue
{
    public function __construct(
        private Post $post
    ) {}

    public function via(User $notifiable): array
    {
        return ['mail', 'database', 'broadcast'];
    }

    public function toMail(User $notifiable): MailMessage
    {
        return (new MailMessage)
            ->subject("New post from {$this->post->author->name}")
            ->line($this->post->title)
            ->line(Str::limit($this->post->content, 200))
            ->action('Read Post', route('posts.show', $this->post))
            ->line('Thank you for subscribing!');
    }

    public function toArray(User $notifiable): array
    {
        return [
            'post_id' => $this->post->id,
            'title' => $this->post->title,
            'author' => $this->post->author->name,
            'url' => route('posts.show', $this->post),
        ];
    }
}
```

### 24. Job Layer
Queued background processing for heavy operations.

**When to use:** Long-running tasks, external API calls, batch processing, scheduled tasks.

```php
class GeneratePostMetadata implements ShouldQueue
{
    use Dispatchable, InteractsWithQueue, Queueable, SerializesModels;

    public $tries = 3;
    public $timeout = 120;

    public function __construct(
        public Post $post
    ) {}

    public function handle(MetadataService $service): void
    {
        $metadata = $service->extract($this->post->content);

        $this->post->update([
            'meta_description' => $metadata->description,
            'reading_time' => $metadata->readingTime,
            'keywords' => $metadata->keywords,
        ]);
    }

    public function failed(Throwable $exception): void
    {
        Log::error('Metadata generation failed', [
            'post_id' => $this->post->id,
            'error' => $exception->getMessage(),
        ]);
    }
}
```

## Model Lifecycle Layers

### 25. Observer Layer
Model lifecycle hooks for automatic behaviors.

**When to use:** Actions that always happen on model events (create, update, delete). Keeps models clean.

```php
class PostObserver
{
    public function creating(Post $post): void
    {
        if (empty($post->slug)) {
            $post->slug = Str::slug($post->title);
        }
    }

    public function updating(Post $post): void
    {
        if ($post->isDirty('title')) {
            $post->slug = Str::slug($post->title);
        }
    }

    public function updated(Post $post): void
    {
        if ($post->wasChanged('status') && $post->status === PostStatus::Published) {
            event(new PostPublished($post));
        }

        Cache::forget("post.{$post->slug}");
    }

    public function deleted(Post $post): void
    {
        $post->comments()->delete();
        Storage::delete($post->featured_image);
        Cache::forget("post.{$post->slug}");
    }
}
```

### 26. Trait Layer
Reusable model behaviors and cross-cutting concerns.

**When to use:** Shared functionality across multiple models (slugs, UUID, soft deletes, auditing).

```php
trait HasSlug
{
    protected static function bootHasSlug(): void
    {
        static::creating(function ($model) {
            if (empty($model->slug)) {
                $model->slug = Str::slug($model->{$model->getSlugSource()});
            }
        });
    }

    public function getRouteKeyName(): string
    {
        return 'slug';
    }

    protected function getSlugSource(): string
    {
        return 'title';
    }
}

trait Taggable
{
    public function tags(): MorphToMany
    {
        return $this->morphToMany(Tag::class, 'taggable');
    }

    public function syncTags(array $tags): void
    {
        $tagIds = collect($tags)->map(function ($tag) {
            return Tag::firstOrCreate(['name' => $tag])->id;
        });

        $this->tags()->sync($tagIds);
    }
}
```

## Frontend Layers

### 27. Livewire Component Layer
Server-side reactive UI components without JavaScript.

**When to use:** Interactive forms, real-time updates, dynamic UIs without writing JavaScript/Vue/React.

```php
class PostManager extends Component
{
    public Post $post;
    public string $title = '';
    public string $content = '';
    public PostStatus $status = PostStatus::Draft;

    public function mount(?Post $post = null): void
    {
        if ($post) {
            $this->authorize('update', $post);
            $this->post = $post;
            $this->title = $post->title;
            $this->content = $post->content;
            $this->status = $post->status;
        } else {
            $this->authorize('create', Post::class);
        }
    }

    public function save(PostService $service): void
    {
        $this->validate([
            'title' => 'required|min:3|max:255',
            'content' => 'required|min:100',
        ]);

        $data = new PostData(
            title: $this->title,
            content: $this->content,
            status: $this->status,
            authorId: auth()->id()
        );

        $this->post = $service->createAndPublish($data);

        $this->dispatch('post-saved', postId: $this->post->id);
        session()->flash('message', 'Post saved successfully');

        $this->redirectRoute('posts.show', $this->post);
    }

    public function render(): View
    {
        return view('livewire.post-manager');
    }
}
```

### 28. View Component Layer (Blade Components)
Reusable UI elements with encapsulated logic.

**When to use:** Repeated UI patterns (cards, modals, buttons) that need server-side data or logic.

```php
class PostCard extends Component
{
    public function __construct(
        public Post $post,
        public bool $showActions = false,
        public string $variant = 'default'
    ) {}

    public function canEdit(): bool
    {
        return auth()->user()?->can('update', $this->post) ?? false;
    }

    public function canDelete(): bool
    {
        return auth()->user()?->can('delete', $this->post) ?? false;
    }

    public function render(): View
    {
        return view('components.post-card');
    }
}

// Usage: <x-post-card :post="$post" :show-actions="true" />
```

### 29. View Composer Layer
Share data with specific views automatically.

**When to use:** Common data needed across many views (navigation items, settings, user data).

```php
class PostComposer
{
    public function compose(View $view): void
    {
        $view->with([
            'categories' => Cache::remember(
                'blog.categories',
                3600,
                fn() => Category::withCount('posts')->get()
            ),
            'popularPosts' => Cache::remember(
                'blog.popular',
                3600,
                fn() => Post::published()
                    ->orderBy('views_count', 'desc')
                    ->limit(5)
                    ->get()
            ),
        ]);
    }
}

// In ServiceProvider:
View::composer(['posts.*', 'blog.*'], PostComposer::class);
```

## Infrastructure & Support Layers

### 30. Provider Layer
Service registration, binding, bootstrapping.

**When to use:** Register repositories, bind interfaces, configure packages, register observers.

```php
class RepositoryServiceProvider extends ServiceProvider
{
    public array $bindings = [
        PostRepositoryInterface::class => PostRepository::class,
        CommentRepositoryInterface::class => CommentRepository::class,
    ];

    public function register(): void
    {
        //
    }

    public function boot(): void
    {
        Post::observe(PostObserver::class);
        Comment::observe(CommentObserver::class);
    }
}
```

### 31. Command Layer (Artisan Console)
CLI commands for maintenance, automation, data processing.

**When to use:** Scheduled tasks, batch operations, admin utilities, deployment scripts.

```php
class PublishScheduledPosts extends Command
{
    protected $signature = 'posts:publish-scheduled';
    protected $description = 'Publish posts scheduled for current time';

    public function handle(PostService $service): int
    {
        $posts = Post::where('status', PostStatus::Draft)
            ->where('scheduled_at', '<=', now())
            ->get();

        $this->info("Found {$posts->count()} posts to publish");

        $bar = $this->output->createProgressBar($posts->count());

        foreach ($posts as $post) {
            $service->publishPost($post);
            $bar->advance();
        }

        $bar->finish();
        $this->newLine();
        $this->info('Done!');

        return self::SUCCESS;
    }
}
```

### 32. Exception Layer
Custom exceptions for domain-specific errors.

**When to use:** Business rule violations, domain errors that need specific handling.

```php
class PostNotFoundException extends Exception
{
    public static function withSlug(string $slug): self
    {
        return new self("Post with slug '{$slug}' not found");
    }
}

class PostPublishingException extends Exception
{
    public static function notDraft(Post $post): self
    {
        return new self(
            "Cannot publish post #{$post->id} - current status is {$post->status->value}"
        );
    }

    public static function missingContent(): self
    {
        return new self("Cannot publish post without content");
    }
}
```

### 33. Handler Layer (Exception Handler)
Global exception handling and error responses.

**When to use:** Centralized error logging, custom error responses, third-party error tracking.

```php
class Handler extends ExceptionHandler
{
    public function register(): void
    {
        $this->reportable(function (PostNotFoundException $e) {
            Log::warning('Post not found', [
                'exception' => $e->getMessage(),
                'user_id' => auth()->id(),
            ]);
        });

        $this->renderable(function (PostPublishingException $e, Request $request) {
            if ($request->expectsJson()) {
                return response()->json([
                    'message' => $e->getMessage(),
                    'error' => 'publishing_failed',
                ], 422);
            }
        });
    }
}
```

### 34. Rule Layer (Validation Rules)
Custom validation logic reusable across form requests.

**When to use:** Complex validation that goes beyond built-in rules, database checks, business rules.

```php
class UniquePostSlug implements ValidationRule
{
    public function __construct(
        private ?int $exceptId = null
    ) {}

    public function validate(string $attribute, mixed $value, Closure $fail): void
    {
        $slug = Str::slug($value);

        $exists = Post::where('slug', $slug)
            ->when($this->exceptId, fn($q) => $q->where('id', '!=', $this->exceptId))
            ->exists();

        if ($exists) {
            $fail("A post with this slug already exists");
        }
    }
}

// Usage in Request:
public function rules(): array
{
    return [
        'title' => ['required', new UniquePostSlug($this->post?->id)],
    ];
}
```

## Testing Layers

### 35. Feature Test Layer
End-to-end testing of complete features and workflows.

**When to use:** Test entire user journeys, API endpoints, authentication flows.

```php
class PostCreationTest extends TestCase
{
    use RefreshDatabase;

    public function test_author_can_create_draft_post(): void
    {
        $author = User::factory()->author()->create();

        $response = $this->actingAs($author)
            ->postJson('/api/posts', [
                'title' => 'Test Post',
                'content' => str_repeat('Content ', 50),
                'status' => 'draft',
            ]);

        $response->assertCreated()
            ->assertJsonStructure([
                'data' => ['id', 'title', 'status', 'author'],
            ]);

        $this->assertDatabaseHas('posts', [
            'title' => 'Test Post',
            'status' => 'draft',
            'user_id' => $author->id,
        ]);
    }
}
```

### 36. Unit Test Layer
Test individual classes and methods in isolation.

**When to use:** Test services, actions, value objects, calculations without database.

```php
class PostStatisticsServiceTest extends TestCase
{
    public function test_calculates_engagement_score_correctly(): void
    {
        $service = new PostStatisticsService();

        $post = new Post([
            'comments_count' => 10,
            'likes_count' => 50,
            'views_count' => 1000,
            'published_at' => now()->subDays(3),
        ]);

        $score = $service->calculateEngagementScore($post);

        $this->assertEqualsWithDelta(142.85, $score, 0.1);
    }
}
```

### 37. Mock & Fake Layer
Test doubles for external dependencies.

**When to use:** Test code that depends on external services, queues, storage, notifications.

```php
class PostPublishingTest extends TestCase
{
    use RefreshDatabase;

    public function test_publishing_post_sends_notifications(): void
    {
        Notification::fake();
        Event::fake([PostPublished::class]);

        $author = User::factory()->create();
        $subscribers = User::factory()->count(5)->create();

        $post = Post::factory()->for($author)->create();
        app(PublishPost::class)->execute($post);

        Event::assertDispatched(PostPublished::class, function ($e) use ($post) {
            return $e->post->id === $post->id;
        });

        Notification::assertSentTo(
            $subscribers,
            NewPostNotification::class
        );
    }
}
```

---

## Build Sequence Summary

**Recommended order for a new feature:**

1. **Migration** → Schema first
2. **Model + Enum** → Data representation
3. **Factory + Seeder** → Test data
4. **DTO** → Data contracts
5. **Repository** (if needed) → Query abstraction
6. **Action** → Single operations
7. **Service** → Business orchestration
8. **Policy** → Authorization
9. **Request** → Validation
10. **Controller** → HTTP handling
11. **Resource** → API transformation
12. **Event + Listener** → Side effects
13. **Livewire/Components** → UI
14. **Tests** → Quality assurance

**Key Principle:** Build from data layer up, each layer depending only on layers below it.

---

## 1. When/Where Value Objects Are Called

Value Objects are instantiated at **data entry points** and used throughout your application:

**Entry Points:**

- **Form Requests** - Convert validated input
- **DTOs** - Embedded as properties
- **Controllers** - Parse route parameters
- **Jobs/Commands** - Deserialize from queue

**Example Flow:**

```php
// Form Request (entry point)
class StoreUserRequest extends FormRequest
{
    public function rules(): array
    {
        return ['email' => 'required|email'];
    }

    public function toDTO(): UserData
    {
        return new UserData(
            email: Email::from($this->input('email')), // ← Value Object created
            name: $this->input('name')
        );
    }
}

// DTO (carries Value Object)
readonly class UserData
{
    public function __construct(
        public Email $email,  // ← Value Object property
        public string $name
    ) {}
}

// Service (uses Value Object)
class UserService
{
    public function create(UserData $data): User
    {
        return User::create([
            'email' => $data->email->value,  // ← Access Value Object
            'name' => $data->name,
            'email_domain' => $data->email->domain(),  // ← Use Value Object method
        ]);
    }
}
```

**Key Point:** Value Objects are created once at boundaries, then passed through layers. They provide type safety and business rules enforcement.

------

## 2. Repository Interface vs Angular Interface

**No, completely different concepts.**

## PHP Repository Interface (Contract/Protocol)

**Purpose:** Define a contract that multiple implementations can fulfill. Enables dependency injection and testing.

```php
// Contract
interface PostRepositoryInterface
{
    public function find(int $id): ?Post;
    public function create(array $data): Post;
}

// MySQL Implementation
class EloquentPostRepository implements PostRepositoryInterface
{
    public function find(int $id): ?Post
    {
        return Post::find($id);
    }
}

// Mock Implementation (for testing)
class InMemoryPostRepository implements PostRepositoryInterface
{
    public function find(int $id): ?Post
    {
        return $this->posts[$id] ?? null;
    }
}

// Service doesn't care which implementation
class PostService
{
    public function __construct(
        private PostRepositoryInterface $repository  // ← Type-hint interface
    ) {}
}
```

**Benefits:**

- Swap implementations (Eloquent → API → Cache)
- Easy testing (mock repositories)
- Hide implementation details

## Angular Interface (TypeScript Type)

**Purpose:** Type checking only - no runtime behavior, just compile-time structure validation.

```typescript
// TypeScript interface (compile-time only)
interface Post {
    id: number;
    title: string;
    content: string;
}

// Just validates structure
function displayPost(post: Post) {
    console.log(post.title);
}
```

## Key Differences

| Aspect         | PHP Interface                  | Angular Interface         |
| -------------- | ------------------------------ | ------------------------- |
| Runtime        | Enforced at runtime            | Erased after compilation  |
| Purpose        | Contract for behavior          | Type structure validation |
| Implementation | Classes implement              | Objects match shape       |
| Polymorphism   | Yes (multiple implementations) | No (just type checking)   |

**Laravel equivalent to Angular interfaces:** TypeScript types, not PHP interfaces. PHP interfaces are about **contracts and dependency injection**.