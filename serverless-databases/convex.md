# Convex - Complete Reactive Backend Platform Guide

## Table of Contents
- [What is Convex?](#what-is-convex)
- [Why Use Convex?](#why-use-convex)
- [Convex vs Alternatives](#convex-vs-alternatives)
- [Installation & Setup](#installation--setup)
- [Database Operations](#database-operations)
- [Queries](#queries)
- [Mutations](#mutations)
- [Actions](#actions)
- [Authentication](#authentication)
- [File Storage](#file-storage)
- [Real-World Examples](#real-world-examples)
- [Best Practices](#best-practices)

## What is Convex?

Convex is a reactive backend platform that provides a database, serverless functions, and real-time subscriptions with automatic caching and TypeScript support.

### Key Features
- **Reactive Queries** - Auto-updating data
- **TypeScript-First** - Full type safety
- **Real-time by Default** - No manual subscriptions
- **ACID Transactions** - Guaranteed consistency
- **Serverless Functions** - Built-in backend logic
- **File Storage** - Integrated file handling
- **Authentication** - Built-in auth providers
- **Automatic Caching** - Optimized performance

## Why Use Convex?

### 1. Reactive by Default

```typescript
// Traditional approach - manual updates
const [posts, setPosts] = useState([]);

useEffect(() => {
  fetchPosts().then(setPosts);
  
  // Manual polling or WebSocket setup
  const interval = setInterval(() => {
    fetchPosts().then(setPosts);
  }, 5000);
  
  return () => clearInterval(interval);
}, []);

// Convex - automatic reactivity
const posts = useQuery(api.posts.list);
// Automatically updates when data changes!
```

### 2. Full TypeScript Safety

```typescript
// Convex generates types automatically
import { api } from '../convex/_generated/api';

// Type-safe queries
const posts = useQuery(api.posts.list); // posts is typed!

// Type-safe mutations
const createPost = useMutation(api.posts.create);
await createPost({ title: 'Hello', content: 'World' });
// TypeScript knows the exact shape of arguments
```

### 3. ACID Transactions

```typescript
// All mutations are transactional
export const transferFunds = mutation({
  args: { fromId: v.id('accounts'), toId: v.id('accounts'), amount: v.number() },
  handler: async (ctx, args) => {
    const from = await ctx.db.get(args.fromId);
    const to = await ctx.db.get(args.toId);
    
    // Either both succeed or both fail
    await ctx.db.patch(args.fromId, { balance: from.balance - args.amount });
    await ctx.db.patch(args.toId, { balance: to.balance + args.amount });
  },
});
```

## Convex vs Alternatives

### Convex vs Supabase

| Feature | Convex | Supabase |
|---------|--------|----------|
| **Database** | Document store | PostgreSQL |
| **Query Language** | TypeScript | SQL |
| **Real-time** | Automatic | Manual subscriptions |
| **Type Safety** | ✅ Excellent | ⚠️ Manual |
| **Maturity** | Newer | More established |
| **Flexibility** | Opinionated | More flexible |

### Convex vs Firebase

| Feature | Convex | Firebase |
|---------|--------|----------|
| **TypeScript** | ✅ First-class | ⚠️ Added on |
| **Reactivity** | Automatic | Manual listeners |
| **Transactions** | ACID | Limited |
| **Pricing** | Generous free tier | Pay-as-you-go |
| **Mobile SDKs** | Web-focused | Excellent |

## Installation & Setup

### 1. Create Convex Project

```bash
npm create convex@latest
# or
npx convex dev
```

### 2. Install Dependencies

```bash
npm install convex
```

### 3. Project Structure

```
my-app/
├── convex/
│   ├── _generated/
│   ├── schema.ts
│   ├── posts.ts
│   └── auth.config.ts
├── src/
│   └── main.tsx
└── package.json
```

### 4. Initialize Convex

**src/main.tsx:**
```typescript
import { ConvexProvider, ConvexReactClient } from 'convex/react';

const convex = new ConvexReactClient(import.meta.env.VITE_CONVEX_URL);

function App() {
  return (
    <ConvexProvider client={convex}>
      <YourApp />
    </ConvexProvider>
  );
}
```

**.env:**
```env
VITE_CONVEX_URL=https://your-project.convex.cloud
```

### 5. Define Schema

**convex/schema.ts:**
```typescript
import { defineSchema, defineTable } from 'convex/server';
import { v } from 'convex/values';

export default defineSchema({
  posts: defineTable({
    title: v.string(),
    content: v.string(),
    authorId: v.id('users'),
    status: v.union(v.literal('draft'), v.literal('published')),
    createdAt: v.number(),
    updatedAt: v.number(),
  })
    .index('by_author', ['authorId'])
    .index('by_status', ['status']),
    
  users: defineTable({
    name: v.string(),
    email: v.string(),
    avatarUrl: v.optional(v.string()),
  })
    .index('by_email', ['email']),
});
```

## Database Operations

### Queries (Read)

**convex/posts.ts:**
```typescript
import { query } from './_generated/server';
import { v } from 'convex/values';

// List all posts
export const list = query({
  handler: async (ctx) => {
    return await ctx.db.query('posts').collect();
  },
});

// Get single post
export const get = query({
  args: { id: v.id('posts') },
  handler: async (ctx, args) => {
    return await ctx.db.get(args.id);
  },
});

// Query with filter
export const listByAuthor = query({
  args: { authorId: v.id('users') },
  handler: async (ctx, args) => {
    return await ctx.db
      .query('posts')
      .withIndex('by_author', (q) => q.eq('authorId', args.authorId))
      .collect();
  },
});

// Query with pagination
export const listPaginated = query({
  args: { paginationOpts: paginationOptsValidator },
  handler: async (ctx, args) => {
    return await ctx.db
      .query('posts')
      .order('desc')
      .paginate(args.paginationOpts);
  },
});
```

### Using Queries in React

```typescript
import { useQuery } from 'convex/react';
import { api } from '../convex/_generated/api';

function PostList() {
  const posts = useQuery(api.posts.list);
  
  if (posts === undefined) {
    return <div>Loading...</div>;
  }
  
  return (
    <ul>
      {posts.map(post => (
        <li key={post._id}>{post.title}</li>
      ))}
    </ul>
  );
}
```

## Mutations

### Create Mutations

**convex/posts.ts:**
```typescript
import { mutation } from './_generated/server';
import { v } from 'convex/values';

// Create post
export const create = mutation({
  args: {
    title: v.string(),
    content: v.string(),
  },
  handler: async (ctx, args) => {
    const identity = await ctx.auth.getUserIdentity();
    if (!identity) throw new Error('Not authenticated');
    
    const postId = await ctx.db.insert('posts', {
      title: args.title,
      content: args.content,
      authorId: identity.subject as Id<'users'>,
      status: 'draft',
      createdAt: Date.now(),
      updatedAt: Date.now(),
    });
    
    return postId;
  },
});

// Update post
export const update = mutation({
  args: {
    id: v.id('posts'),
    title: v.optional(v.string()),
    content: v.optional(v.string()),
  },
  handler: async (ctx, args) => {
    const { id, ...updates } = args;
    
    await ctx.db.patch(id, {
      ...updates,
      updatedAt: Date.now(),
    });
  },
});

// Delete post
export const remove = mutation({
  args: { id: v.id('posts') },
  handler: async (ctx, args) => {
    await ctx.db.delete(args.id);
  },
});
```

### Using Mutations in React

```typescript
import { useMutation } from 'convex/react';
import { api } from '../convex/_generated/api';

function CreatePost() {
  const createPost = useMutation(api.posts.create);
  
  const handleSubmit = async (e: React.FormEvent) => {
    e.preventDefault();
    await createPost({
      title: 'My Post',
      content: 'Post content'
    });
  };
  
  return <form onSubmit={handleSubmit}>...</form>;
}
```

## Actions

Actions can call external APIs and are not transactional:

**convex/posts.ts:**
```typescript
import { action } from './_generated/server';
import { v } from 'convex/values';
import { api } from './_generated/api';

export const generateSummary = action({
  args: { postId: v.id('posts') },
  handler: async (ctx, args) => {
    // Get post data
    const post = await ctx.runQuery(api.posts.get, { id: args.postId });
    
    // Call external API (OpenAI, etc.)
    const response = await fetch('https://api.openai.com/v1/completions', {
      method: 'POST',
      headers: {
        'Authorization': `Bearer ${process.env.OPENAI_API_KEY}`,
        'Content-Type': 'application/json',
      },
      body: JSON.stringify({
        model: 'gpt-3.5-turbo',
        prompt: `Summarize: ${post.content}`,
      }),
    });
    
    const data = await response.json();
    
    // Update post with summary
    await ctx.runMutation(api.posts.update, {
      id: args.postId,
      summary: data.choices[0].text,
    });
  },
});
```

## Authentication

### Setup Auth

**convex/auth.config.ts:**
```typescript
export default {
  providers: [
    {
      domain: import.meta.env.VITE_CLERK_DOMAIN,
      applicationID: import.meta.env.VITE_CLERK_APP_ID,
    },
  ],
};
```

### Using Clerk with Convex

```bash
npm install @clerk/clerk-react
```

**src/main.tsx:**
```typescript
import { ClerkProvider, useAuth } from '@clerk/clerk-react';
import { ConvexProviderWithClerk } from 'convex/react-clerk';
import { ConvexReactClient } from 'convex/react';

const convex = new ConvexReactClient(import.meta.env.VITE_CONVEX_URL);

function App() {
  return (
    <ClerkProvider publishableKey={import.meta.env.VITE_CLERK_PUBLISHABLE_KEY}>
      <ConvexProviderWithClerk client={convex} useAuth={useAuth}>
        <YourApp />
      </ConvexProviderWithClerk>
    </ClerkProvider>
  );
}
```

### Protected Queries/Mutations

```typescript
import { query, mutation } from './_generated/server';

export const getProfile = query({
  handler: async (ctx) => {
    const identity = await ctx.auth.getUserIdentity();
    if (!identity) throw new Error('Not authenticated');
    
    return await ctx.db
      .query('users')
      .withIndex('by_email', (q) => q.eq('email', identity.email))
      .first();
  },
});
```

## File Storage

### Upload Files

**convex/files.ts:**
```typescript
import { mutation } from './_generated/server';

export const generateUploadUrl = mutation({
  handler: async (ctx) => {
    return await ctx.storage.generateUploadUrl();
  },
});

export const saveFile = mutation({
  args: { storageId: v.id('_storage'), name: v.string() },
  handler: async (ctx, args) => {
    await ctx.db.insert('files', {
      storageId: args.storageId,
      name: args.name,
      uploadedAt: Date.now(),
    });
  },
});
```

**React Component:**
```typescript
import { useMutation } from 'convex/react';
import { api } from '../convex/_generated/api';

function FileUpload() {
  const generateUploadUrl = useMutation(api.files.generateUploadUrl);
  const saveFile = useMutation(api.files.saveFile);
  
  const handleUpload = async (file: File) => {
    // Get upload URL
    const uploadUrl = await generateUploadUrl();
    
    // Upload file
    const result = await fetch(uploadUrl, {
      method: 'POST',
      headers: { 'Content-Type': file.type },
      body: file,
    });
    
    const { storageId } = await result.json();
    
    // Save metadata
    await saveFile({ storageId, name: file.name });
  };
  
  return <input type="file" onChange={(e) => handleUpload(e.target.files[0])} />;
}
```

### Get File URL

```typescript
import { query } from './_generated/server';

export const getFileUrl = query({
  args: { storageId: v.id('_storage') },
  handler: async (ctx, args) => {
    return await ctx.storage.getUrl(args.storageId);
  },
});
```

## Real-World Examples

### Todo App

**convex/schema.ts:**
```typescript
export default defineSchema({
  todos: defineTable({
    text: v.string(),
    completed: v.boolean(),
    userId: v.string(),
  }).index('by_user', ['userId']),
});
```

**convex/todos.ts:**
```typescript
import { query, mutation } from './_generated/server';
import { v } from 'convex/values';

export const list = query({
  handler: async (ctx) => {
    const identity = await ctx.auth.getUserIdentity();
    if (!identity) return [];
    
    return await ctx.db
      .query('todos')
      .withIndex('by_user', (q) => q.eq('userId', identity.subject))
      .collect();
  },
});

export const create = mutation({
  args: { text: v.string() },
  handler: async (ctx, args) => {
    const identity = await ctx.auth.getUserIdentity();
    if (!identity) throw new Error('Not authenticated');
    
    await ctx.db.insert('todos', {
      text: args.text,
      completed: false,
      userId: identity.subject,
    });
  },
});

export const toggle = mutation({
  args: { id: v.id('todos') },
  handler: async (ctx, args) => {
    const todo = await ctx.db.get(args.id);
    await ctx.db.patch(args.id, { completed: !todo.completed });
  },
});

export const remove = mutation({
  args: { id: v.id('todos') },
  handler: async (ctx, args) => {
    await ctx.db.delete(args.id);
  },
});
```

**React Component:**
```typescript
import { useQuery, useMutation } from 'convex/react';
import { api } from '../convex/_generated/api';

function TodoApp() {
  const todos = useQuery(api.todos.list);
  const createTodo = useMutation(api.todos.create);
  const toggleTodo = useMutation(api.todos.toggle);
  const removeTodo = useMutation(api.todos.remove);
  
  const [text, setText] = useState('');
  
  const handleSubmit = async (e: React.FormEvent) => {
    e.preventDefault();
    await createTodo({ text });
    setText('');
  };
  
  return (
    <div>
      <form onSubmit={handleSubmit}>
        <input value={text} onChange={(e) => setText(e.target.value)} />
        <button type="submit">Add</button>
      </form>
      
      <ul>
        {todos?.map(todo => (
          <li key={todo._id}>
            <input
              type="checkbox"
              checked={todo.completed}
              onChange={() => toggleTodo({ id: todo._id })}
            />
            <span>{todo.text}</span>
            <button onClick={() => removeTodo({ id: todo._id })}>Delete</button>
          </li>
        ))}
      </ul>
    </div>
  );
}
```

## Best Practices

### 1. Use Indexes

```typescript
// Define indexes in schema
export default defineSchema({
  posts: defineTable({
    title: v.string(),
    authorId: v.id('users'),
    createdAt: v.number(),
  })
    .index('by_author', ['authorId'])
    .index('by_created', ['createdAt']),
});

// Use indexes in queries
export const listByAuthor = query({
  args: { authorId: v.id('users') },
  handler: async (ctx, args) => {
    return await ctx.db
      .query('posts')
      .withIndex('by_author', (q) => q.eq('authorId', args.authorId))
      .collect();
  },
});
```

### 2. Validate Arguments

```typescript
import { v } from 'convex/values';

export const create = mutation({
  args: {
    title: v.string(),
    content: v.string(),
    tags: v.array(v.string()),
    metadata: v.object({
      category: v.string(),
      featured: v.boolean(),
    }),
  },
  handler: async (ctx, args) => {
    // args are validated automatically
  },
});
```

### 3. Use Optimistic Updates

```typescript
const createTodo = useMutation(api.todos.create).withOptimisticUpdate(
  (localStore, args) => {
    const todos = localStore.getQuery(api.todos.list);
    if (todos !== undefined) {
      localStore.setQuery(api.todos.list, {}, [
        ...todos,
        {
          _id: 'temp-id' as Id<'todos'>,
          _creationTime: Date.now(),
          text: args.text,
          completed: false,
          userId: 'current-user',
        },
      ]);
    }
  }
);
```

### 4. Handle Loading States

```typescript
function PostList() {
  const posts = useQuery(api.posts.list);
  
  if (posts === undefined) {
    return <Skeleton />;
  }
  
  if (posts.length === 0) {
    return <EmptyState />;
  }
  
  return <List posts={posts} />;
}
```

### 5. Use Pagination

```typescript
export const listPaginated = query({
  args: { paginationOpts: paginationOptsValidator },
  handler: async (ctx, args) => {
    return await ctx.db
      .query('posts')
      .order('desc')
      .paginate(args.paginationOpts);
  },
});

// In React
const { results, status, loadMore } = usePaginatedQuery(
  api.posts.listPaginated,
  {},
  { initialNumItems: 20 }
);
```

## Resources

- [Convex Documentation](https://docs.convex.dev)
- [Convex Discord](https://convex.dev/community)
- [Example Apps](https://github.com/get-convex/convex-demos)
- [Convex Blog](https://blog.convex.dev)
