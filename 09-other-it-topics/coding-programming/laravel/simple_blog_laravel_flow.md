# Blog Application: Layer-by-Layer Build Sequence

## Step 1: Database Layer (Migrations + Models)

**Order of migrations:**

1. `users` - base authentication table
2. `posts` - belongs to users (authors)
3. `comments` - belongs to posts and users
4. Add `role` enum to users ('admin', 'author', 'reader')

**Models with relationships:**

- `User` → hasMany(Post), hasMany(Comment)
- `Post` → belongsTo(User), hasMany(Comment)
- `Comment` → belongsTo(Post), belongsTo(User)

Add indexes on foreign keys and frequently queried columns (post status, published_at).

## Step 2: Domain Layer (Business Logic)

**Create in this order:**

1. **DTOs** (data contracts)
   - `PostData` (title, content, status, author_id)
   - `CommentData` (content, post_id, user_id)
2. **Actions** (single operations)
   - `CreatePost`, `PublishPost`, `DeletePost`
   - `CreateComment`, `ApproveComment`
   - `AssignAuthorRole`
3. **Services** (orchestration)
   - `PostService` - coordinates post operations with permissions
   - `CommentModerationService` - handles approval workflow

## Step 3: Authorization Layer (Gates & Policies)

1. `PostPolicy` - viewAny, create, update, delete, publish
2. `CommentPolicy` - create, update, delete, approve
3. Gates for admin checks

## Step 4: API/HTTP Layer

1. **Form Requests** - `StorePostRequest`, `StoreCommentRequest`
2. **Controllers** (thin) - `PostController`, `CommentController`
3. **Resources** - `PostResource`, `CommentResource`
4. **Routes** - resource routes with middleware

## Step 5: Livewire Components (Frontend)

1. **Admin components:**
   - `PostManager` (CRUD + publish)
   - `CommentModerator` (approve/reject)
2. **Public components:**
   - `PostList`, `PostDetail`
   - `CommentSection`
3. **Flux UI integration** - buttons, modals, forms

## Key Sequence Logic

Each layer **depends on** the previous:

- Services need Models
- Controllers need Services + Policies
- Livewire needs Controllers' logic patterns

Build **bottom-up** (data → logic → interface), test at each layer before moving up.