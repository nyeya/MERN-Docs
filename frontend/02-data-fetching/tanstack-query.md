# TanStack Query (React Query) - Powerful Data Synchronization for React

## Overview

TanStack Query (formerly React Query) is a powerful data-fetching and state management library for web applications. It handles server state management, caching, synchronization, and updates with minimal configuration. Unlike traditional state management libraries, TanStack Query is specifically designed for asynchronous data that comes from external sources.

**Key Features:**
- Automatic caching and background updates
- Request deduplication
- Automatic retries and error handling
- Pagination and infinite scroll support
- Optimistic updates
- Prefetching
- DevTools for debugging
- Framework agnostic (React, Vue, Solid, Svelte)
- TypeScript-first design

## Why Use TanStack Query?

### Problems It Solves

1. **Server State Complexity**: Eliminates boilerplate for fetching, caching, and updating server data
2. **Stale Data**: Automatically keeps data fresh with background refetching
3. **Loading States**: Built-in loading, error, and success states
4. **Cache Management**: Intelligent caching without manual cache invalidation
5. **Performance**: Request deduplication and automatic garbage collection
6. **UX**: Optimistic updates and instant feedback

### When to Use TanStack Query

- ✅ Fetching data from REST APIs
- ✅ GraphQL queries (though Apollo Client might be better)
- ✅ Real-time data that needs to stay fresh
- ✅ Paginated or infinite scroll data
- ✅ Data that needs to be cached and shared across components
- ✅ Forms that submit to a server
- ✅ Any asynchronous server state

### When NOT to Use TanStack Query

- ❌ Local UI state (use useState/Zustand)
- ❌ Simple one-time fetches that don't need caching
- ❌ When you need Redux's time-travel debugging
- ❌ Synchronous data transformations

## Comparison with Alternatives

### TanStack Query vs SWR

| Feature | TanStack Query | SWR |
|---------|---------------|-----|
| Bundle Size | ~13KB | ~5KB |
| Features | Comprehensive | Minimal |
| Mutations | Built-in | Manual |
| DevTools | Yes | No |
| Pagination | Built-in | Manual |
| Optimistic Updates | Built-in | Manual |
| Learning Curve | Moderate | Easy |
| Community | Large | Large |
| Best For | Complex apps | Simple apps |

**Choose TanStack Query if:** You need advanced features like mutations, pagination, optimistic updates
**Choose SWR if:** You want something lightweight and simple

### TanStack Query vs Redux + RTK Query

| Feature | TanStack Query | RTK Query |
|---------|---------------|-----------|
| Setup | Simple | Moderate |
| Redux Required | No | Yes |
| Cache Management | Automatic | Automatic |
| Normalized Cache | No | Yes |
| Bundle Size | Smaller | Larger |
| Best For | Server state | Redux apps |

**Choose TanStack Query if:** You don't use Redux or want simpler setup
**Choose RTK Query if:** You already use Redux and want integration

### TanStack Query vs Apollo Client

| Feature | TanStack Query | Apollo Client |
|---------|---------------|---------------|
| GraphQL Focus | No | Yes |
| REST APIs | Excellent | Possible |
| Normalized Cache | No | Yes |
| Bundle Size | Smaller | Larger |
| Learning Curve | Easy | Moderate |
| Best For | REST/any API | GraphQL |

**Choose TanStack Query if:** You use REST APIs or want flexibility
**Choose Apollo Client if:** You use GraphQL extensively

## Installation & Setup

### Installation

```bash
# npm
npm install @tanstack/react-query

# yarn
yarn add @tanstack/react-query

# pnpm
pnpm add @tanstack/react-query
```

### DevTools (Optional but Recommended)

```bash
npm install @tanstack/react-query-devtools
```

### Basic Setup

```typescript
// app/providers.tsx
'use client' // If using Next.js App Router

import { QueryClient, QueryClientProvider } from '@tanstack/react-query'
import { ReactQueryDevtools } from '@tanstack/react-query-devtools'
import { useState } from 'react'

export function Providers({ children }: { children: React.ReactNode }) {
  const [queryClient] = useState(
    () =>
      new QueryClient({
        defaultOptions: {
          queries: {
            staleTime: 60 * 1000, // 1 minute
            refetchOnWindowFocus: false,
          },
        },
      })
  )

  return (
    <QueryClientProvider client={queryClient}>
      {children}
      <ReactQueryDevtools initialIsOpen={false} />
    </QueryClientProvider>
  )
}

// app/layout.tsx or main.tsx
import { Providers } from './providers'

export default function RootLayout({ children }) {
  return (
    <html>
      <body>
        <Providers>{children}</Providers>
      </body>
    </html>
  )
}
```

## Core Concepts

### 1. Queries

Queries are for fetching data (GET requests):

```typescript
import { useQuery } from '@tanstack/react-query'

function Users() {
  const { data, isLoading, error } = useQuery({
    queryKey: ['users'],
    queryFn: async () => {
      const response = await fetch('/api/users')
      if (!response.ok) throw new Error('Failed to fetch')
      return response.json()
    },
  })

  if (isLoading) return <div>Loading...</div>
  if (error) return <div>Error: {error.message}</div>

  return <div>{/* Render users */}</div>
}
```

### 2. Mutations

Mutations are for creating/updating/deleting data (POST/PUT/DELETE):

```typescript
import { useMutation, useQueryClient } from '@tanstack/react-query'

function CreateUser() {
  const queryClient = useQueryClient()

  const mutation = useMutation({
    mutationFn: async (newUser) => {
      const response = await fetch('/api/users', {
        method: 'POST',
        body: JSON.stringify(newUser),
      })
      return response.json()
    },
    onSuccess: () => {
      // Invalidate and refetch
      queryClient.invalidateQueries({ queryKey: ['users'] })
    },
  })

  return (
    <button onClick={() => mutation.mutate({ name: 'John' })}>
      Create User
    </button>
  )
}
```

### 3. Query Keys

Query keys uniquely identify queries for caching:

```typescript
// Simple key
useQuery({ queryKey: ['users'], ... })

// Key with parameters
useQuery({ queryKey: ['user', userId], ... })

// Key with filters
useQuery({ queryKey: ['users', { status: 'active', page: 1 }], ... })
```

### 4. Cache Time vs Stale Time

```typescript
const queryClient = new QueryClient({
  defaultOptions: {
    queries: {
      staleTime: 5 * 60 * 1000, // 5 minutes - data is fresh
      cacheTime: 10 * 60 * 1000, // 10 minutes - cache retention
    },
  },
})
```

- **staleTime**: How long data is considered fresh (no refetch)
- **cacheTime**: How long unused data stays in cache

## Basic Usage

### Simple Data Fetching

```typescript
// api/users.ts
export async function getUsers() {
  const response = await fetch('https://api.example.com/users')
  if (!response.ok) throw new Error('Failed to fetch users')
  return response.json()
}

// components/UserList.tsx
import { useQuery } from '@tanstack/react-query'
import { getUsers } from '../api/users'

export function UserList() {
  const { data: users, isLoading, error } = useQuery({
    queryKey: ['users'],
    queryFn: getUsers,
  })

  if (isLoading) {
    return <div>Loading users...</div>
  }

  if (error) {
    return <div>Error: {error.message}</div>
  }

  return (
    <ul>
      {users.map((user) => (
        <li key={user.id}>{user.name}</li>
      ))}
    </ul>
  )
}
```

### Query with Parameters

```typescript
// api/users.ts
export async function getUser(id: string) {
  const response = await fetch(`https://api.example.com/users/${id}`)
  if (!response.ok) throw new Error('Failed to fetch user')
  return response.json()
}

// components/UserProfile.tsx
import { useQuery } from '@tanstack/react-query'
import { getUser } from '../api/users'

export function UserProfile({ userId }: { userId: string }) {
  const { data: user, isLoading } = useQuery({
    queryKey: ['user', userId],
    queryFn: () => getUser(userId),
    enabled: !!userId, // Only run if userId exists
  })

  if (isLoading) return <div>Loading...</div>

  return (
    <div>
      <h1>{user.name}</h1>
      <p>{user.email}</p>
    </div>
  )
}
```

### Creating Data (Mutations)

```typescript
// api/users.ts
export async function createUser(userData: { name: string; email: string }) {
  const response = await fetch('https://api.example.com/users', {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify(userData),
  })
  if (!response.ok) throw new Error('Failed to create user')
  return response.json()
}

// components/CreateUserForm.tsx
import { useMutation, useQueryClient } from '@tanstack/react-query'
import { createUser } from '../api/users'
import { useState } from 'react'

export function CreateUserForm() {
  const [name, setName] = useState('')
  const [email, setEmail] = useState('')
  const queryClient = useQueryClient()

  const mutation = useMutation({
    mutationFn: createUser,
    onSuccess: () => {
      // Invalidate users query to refetch
      queryClient.invalidateQueries({ queryKey: ['users'] })
      // Reset form
      setName('')
      setEmail('')
    },
  })

  const handleSubmit = (e: React.FormEvent) => {
    e.preventDefault()
    mutation.mutate({ name, email })
  }

  return (
    <form onSubmit={handleSubmit}>
      <input
        value={name}
        onChange={(e) => setName(e.target.value)}
        placeholder="Name"
      />
      <input
        value={email}
        onChange={(e) => setEmail(e.target.value)}
        placeholder="Email"
        type="email"
      />
      <button type="submit" disabled={mutation.isPending}>
        {mutation.isPending ? 'Creating...' : 'Create User'}
      </button>
      {mutation.isError && <div>Error: {mutation.error.message}</div>}
      {mutation.isSuccess && <div>User created successfully!</div>}
    </form>
  )
}
```

## Advanced Usage

### 1. Optimistic Updates

```typescript
const mutation = useMutation({
  mutationFn: updateUser,
  onMutate: async (newUser) => {
    // Cancel outgoing refetches
    await queryClient.cancelQueries({ queryKey: ['users'] })

    // Snapshot previous value
    const previousUsers = queryClient.getQueryData(['users'])

    // Optimistically update
    queryClient.setQueryData(['users'], (old) => {
      return old.map((user) =>
        user.id === newUser.id ? { ...user, ...newUser } : user
      )
    })

    // Return context with snapshot
    return { previousUsers }
  },
  onError: (err, newUser, context) => {
    // Rollback on error
    queryClient.setQueryData(['users'], context.previousUsers)
  },
  onSettled: () => {
    // Refetch after error or success
    queryClient.invalidateQueries({ queryKey: ['users'] })
  },
})
```

### 2. Pagination

```typescript
import { useQuery } from '@tanstack/react-query'
import { useState } from 'react'

function PaginatedUsers() {
  const [page, setPage] = useState(1)

  const { data, isLoading, isPlaceholderData } = useQuery({
    queryKey: ['users', page],
    queryFn: () => fetchUsers(page),
    placeholderData: (previousData) => previousData, // Keep previous data while fetching
  })

  return (
    <div>
      {isLoading ? (
        <div>Loading...</div>
      ) : (
        <>
          <ul>
            {data.users.map((user) => (
              <li key={user.id}>{user.name}</li>
            ))}
          </ul>
          <button
            onClick={() => setPage((old) => Math.max(old - 1, 1))}
            disabled={page === 1}
          >
            Previous
          </button>
          <span>Page {page}</span>
          <button
            onClick={() => setPage((old) => old + 1)}
            disabled={isPlaceholderData || !data.hasMore}
          >
            Next
          </button>
        </>
      )}
    </div>
  )
}
```

### 3. Infinite Scroll

```typescript
import { useInfiniteQuery } from '@tanstack/react-query'

function InfiniteUsers() {
  const {
    data,
    fetchNextPage,
    hasNextPage,
    isFetchingNextPage,
  } = useInfiniteQuery({
    queryKey: ['users'],
    queryFn: ({ pageParam = 1 }) => fetchUsers(pageParam),
    getNextPageParam: (lastPage, pages) => {
      return lastPage.hasMore ? pages.length + 1 : undefined
    },
    initialPageParam: 1,
  })

  return (
    <div>
      {data?.pages.map((page, i) => (
        <div key={i}>
          {page.users.map((user) => (
            <div key={user.id}>{user.name}</div>
          ))}
        </div>
      ))}
      <button
        onClick={() => fetchNextPage()}
        disabled={!hasNextPage || isFetchingNextPage}
      >
        {isFetchingNextPage
          ? 'Loading more...'
          : hasNextPage
          ? 'Load More'
          : 'Nothing more to load'}
      </button>
    </div>
  )
}
```

### 4. Dependent Queries

```typescript
function UserPosts({ userId }: { userId: string }) {
  // First query
  const { data: user } = useQuery({
    queryKey: ['user', userId],
    queryFn: () => getUser(userId),
  })

  // Second query depends on first
  const { data: posts } = useQuery({
    queryKey: ['posts', user?.id],
    queryFn: () => getPosts(user.id),
    enabled: !!user?.id, // Only run when user.id exists
  })

  return <div>{/* Render posts */}</div>
}
```

### 5. Parallel Queries

```typescript
function Dashboard() {
  const users = useQuery({ queryKey: ['users'], queryFn: getUsers })
  const posts = useQuery({ queryKey: ['posts'], queryFn: getPosts })
  const comments = useQuery({ queryKey: ['comments'], queryFn: getComments })

  if (users.isLoading || posts.isLoading || comments.isLoading) {
    return <div>Loading...</div>
  }

  return (
    <div>
      <div>Users: {users.data.length}</div>
      <div>Posts: {posts.data.length}</div>
      <div>Comments: {comments.data.length}</div>
    </div>
  )
}

// Or use useQueries for dynamic parallel queries
function DynamicDashboard({ userIds }: { userIds: string[] }) {
  const results = useQueries({
    queries: userIds.map((id) => ({
      queryKey: ['user', id],
      queryFn: () => getUser(id),
    })),
  })

  return <div>{/* Render results */}</div>
}
```

### 6. Prefetching

```typescript
import { useQueryClient } from '@tanstack/react-query'

function UserList() {
  const queryClient = useQueryClient()

  const { data: users } = useQuery({
    queryKey: ['users'],
    queryFn: getUsers,
  })

  return (
    <ul>
      {users.map((user) => (
        <li
          key={user.id}
          onMouseEnter={() => {
            // Prefetch user details on hover
            queryClient.prefetchQuery({
              queryKey: ['user', user.id],
              queryFn: () => getUser(user.id),
            })
          }}
        >
          <Link to={`/users/${user.id}`}>{user.name}</Link>
        </li>
      ))}
    </ul>
  )
}
```

### 7. Query Invalidation

```typescript
const queryClient = useQueryClient()

// Invalidate specific query
queryClient.invalidateQueries({ queryKey: ['users'] })

// Invalidate all queries starting with 'users'
queryClient.invalidateQueries({ queryKey: ['users'], exact: false })

// Invalidate multiple queries
queryClient.invalidateQueries({ queryKey: ['users'] })
queryClient.invalidateQueries({ queryKey: ['posts'] })

// Refetch immediately
queryClient.invalidateQueries({ 
  queryKey: ['users'],
  refetchType: 'active' // or 'all', 'none'
})
```

### 8. Manual Cache Updates

```typescript
// Set query data manually
queryClient.setQueryData(['user', '1'], newUser)

// Update query data
queryClient.setQueryData(['users'], (oldData) => {
  return [...oldData, newUser]
})

// Get query data
const users = queryClient.getQueryData(['users'])
```

## Real-World Examples

### Complete CRUD Application

```typescript
// api/posts.ts
const API_URL = 'https://api.example.com'

export interface Post {
  id: string
  title: string
  content: string
  authorId: string
  createdAt: string
}

export async function getPosts() {
  const response = await fetch(`${API_URL}/posts`)
  if (!response.ok) throw new Error('Failed to fetch posts')
  return response.json() as Promise<Post[]>
}

export async function getPost(id: string) {
  const response = await fetch(`${API_URL}/posts/${id}`)
  if (!response.ok) throw new Error('Failed to fetch post')
  return response.json() as Promise<Post>
}

export async function createPost(post: Omit<Post, 'id' | 'createdAt'>) {
  const response = await fetch(`${API_URL}/posts`, {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify(post),
  })
  if (!response.ok) throw new Error('Failed to create post')
  return response.json() as Promise<Post>
}

export async function updatePost(id: string, post: Partial<Post>) {
  const response = await fetch(`${API_URL}/posts/${id}`, {
    method: 'PUT',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify(post),
  })
  if (!response.ok) throw new Error('Failed to update post')
  return response.json() as Promise<Post>
}

export async function deletePost(id: string) {
  const response = await fetch(`${API_URL}/posts/${id}`, {
    method: 'DELETE',
  })
  if (!response.ok) throw new Error('Failed to delete post')
}

// hooks/usePosts.ts
import { useQuery, useMutation, useQueryClient } from '@tanstack/react-query'
import * as api from '../api/posts'

export function usePosts() {
  return useQuery({
    queryKey: ['posts'],
    queryFn: api.getPosts,
  })
}

export function usePost(id: string) {
  return useQuery({
    queryKey: ['post', id],
    queryFn: () => api.getPost(id),
    enabled: !!id,
  })
}

export function useCreatePost() {
  const queryClient = useQueryClient()

  return useMutation({
    mutationFn: api.createPost,
    onSuccess: () => {
      queryClient.invalidateQueries({ queryKey: ['posts'] })
    },
  })
}

export function useUpdatePost() {
  const queryClient = useQueryClient()

  return useMutation({
    mutationFn: ({ id, ...post }: { id: string } & Partial<Post>) =>
      api.updatePost(id, post),
    onSuccess: (_, variables) => {
      queryClient.invalidateQueries({ queryKey: ['posts'] })
      queryClient.invalidateQueries({ queryKey: ['post', variables.id] })
    },
  })
}

export function useDeletePost() {
  const queryClient = useQueryClient()

  return useMutation({
    mutationFn: api.deletePost,
    onSuccess: () => {
      queryClient.invalidateQueries({ queryKey: ['posts'] })
    },
  })
}

// components/PostList.tsx
import { usePosts, useDeletePost } from '../hooks/usePosts'

export function PostList() {
  const { data: posts, isLoading, error } = usePosts()
  const deleteMutation = useDeletePost()

  if (isLoading) return <div>Loading posts...</div>
  if (error) return <div>Error: {error.message}</div>

  return (
    <div>
      <h1>Posts</h1>
      {posts.map((post) => (
        <div key={post.id} className="post-card">
          <h2>{post.title}</h2>
          <p>{post.content}</p>
          <button onClick={() => deleteMutation.mutate(post.id)}>
            Delete
          </button>
        </div>
      ))}
    </div>
  )
}

// components/CreatePostForm.tsx
import { useState } from 'react'
import { useCreatePost } from '../hooks/usePosts'

export function CreatePostForm() {
  const [title, setTitle] = useState('')
  const [content, setContent] = useState('')
  const createMutation = useCreatePost()

  const handleSubmit = (e: React.FormEvent) => {
    e.preventDefault()
    createMutation.mutate(
      { title, content, authorId: 'current-user' },
      {
        onSuccess: () => {
          setTitle('')
          setContent('')
        },
      }
    )
  }

  return (
    <form onSubmit={handleSubmit}>
      <input
        value={title}
        onChange={(e) => setTitle(e.target.value)}
        placeholder="Title"
        required
      />
      <textarea
        value={content}
        onChange={(e) => setContent(e.target.value)}
        placeholder="Content"
        required
      />
      <button type="submit" disabled={createMutation.isPending}>
        {createMutation.isPending ? 'Creating...' : 'Create Post'}
      </button>
      {createMutation.isError && (
        <div>Error: {createMutation.error.message}</div>
      )}
    </form>
  )
}
```

## Best Practices

### 1. Organize Query Keys

```typescript
// queryKeys.ts
export const queryKeys = {
  users: ['users'] as const,
  user: (id: string) => ['users', id] as const,
  posts: ['posts'] as const,
  post: (id: string) => ['posts', id] as const,
  userPosts: (userId: string) => ['posts', 'user', userId] as const,
}

// Usage
useQuery({ queryKey: queryKeys.user('123'), ... })
```

### 2. Create Custom Hooks

```typescript
// ✅ Good: Encapsulate query logic
export function useUser(id: string) {
  return useQuery({
    queryKey: ['user', id],
    queryFn: () => getUser(id),
    staleTime: 5 * 60 * 1000,
  })
}

// ❌ Bad: Inline queries everywhere
function Component() {
  const { data } = useQuery({
    queryKey: ['user', id],
    queryFn: () => getUser(id),
  })
}
```

### 3. Handle Loading and Error States

```typescript
// ✅ Good: Handle all states
function Component() {
  const { data, isLoading, error } = useQuery(...)

  if (isLoading) return <Spinner />
  if (error) return <ErrorMessage error={error} />
  
  return <div>{data}</div>
}

// ❌ Bad: Assume data exists
function Component() {
  const { data } = useQuery(...)
  return <div>{data.name}</div> // Can crash!
}
```

### 4. Use Proper Stale Times

```typescript
// ✅ Good: Set appropriate stale times
useQuery({
  queryKey: ['user'],
  queryFn: getUser,
  staleTime: 5 * 60 * 1000, // 5 minutes for user data
})

useQuery({
  queryKey: ['realtime-price'],
  queryFn: getPrice,
  staleTime: 0, // Always stale for real-time data
})
```

### 5. Invalidate Correctly

```typescript
// ✅ Good: Invalidate related queries
mutation.onSuccess(() => {
  queryClient.invalidateQueries({ queryKey: ['posts'] })
  queryClient.invalidateQueries({ queryKey: ['post', postId] })
})

// ❌ Bad: Invalidate everything
mutation.onSuccess(() => {
  queryClient.invalidateQueries() // Too broad!
})
```

## Common Pitfalls

### 1. Not Using Query Keys Correctly

```typescript
// ❌ Bad: Same key for different data
useQuery({ queryKey: ['user'], queryFn: () => getUser(id) })

// ✅ Good: Include parameters in key
useQuery({ queryKey: ['user', id], queryFn: () => getUser(id) })
```

### 2. Fetching in useEffect

```typescript
// ❌ Bad: Manual fetching
useEffect(() => {
  fetch('/api/users')
    .then(res => res.json())
    .then(setUsers)
}, [])

// ✅ Good: Use TanStack Query
const { data: users } = useQuery({
  queryKey: ['users'],
  queryFn: getUsers,
})
```

### 3. Not Handling Errors

```typescript
// ❌ Bad: Ignoring errors
const { data } = useQuery({ queryKey: ['users'], queryFn: getUsers })

// ✅ Good: Handle errors
const { data, error } = useQuery({ 
  queryKey: ['users'], 
  queryFn: getUsers 
})
if (error) return <ErrorBoundary error={error} />
```

### 4. Over-invalidating

```typescript
// ❌ Bad: Invalidating on every mutation
onSuccess: () => {
  queryClient.invalidateQueries()
}

// ✅ Good: Specific invalidation
onSuccess: () => {
  queryClient.invalidateQueries({ queryKey: ['users'] })
}
```

## Resources

### Official Documentation
- [TanStack Query Docs](https://tanstack.com/query/latest)
- [GitHub Repository](https://github.com/TanStack/query)
- [Migration Guide](https://tanstack.com/query/latest/docs/react/guides/migrating-to-v5)

### Tutorials & Articles
- [Practical React Query](https://tkdodo.eu/blog/practical-react-query)
- [React Query Tutorial](https://ui.dev/react-query-tutorial)
- [Mastering React Query](https://blog.logrocket.com/react-query-tutorial/)

### Video Tutorials
- [React Query Crash Course](https://www.youtube.com/results?search_query=tanstack+query+crash+course)
- [TanStack Query Official Videos](https://www.youtube.com/@tanstackquery)

### Community
- [Discord](https://tlinz.com/discord)
- [GitHub Discussions](https://github.com/TanStack/query/discussions)
- [Stack Overflow](https://stackoverflow.com/questions/tagged/react-query)

### Tools
- [React Query DevTools](https://tanstack.com/query/latest/docs/react/devtools)
- [ESLint Plugin](https://tanstack.com/query/latest/docs/eslint/eslint-plugin-query)

---

**Next Steps:**
- Explore [SWR](./swr.md) as a lightweight alternative
- Learn about [Apollo Client](./apollo-client.md) for GraphQL
- Check out [tRPC](./trpc.md) for type-safe APIs
