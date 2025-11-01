# Redux Toolkit - The Official Redux Toolset

## Overview

Redux Toolkit (RTK) is the official, opinionated, batteries-included toolset for efficient Redux development. It simplifies Redux configuration, reduces boilerplate, and includes best practices by default. Created by the Redux team, RTK is now the recommended way to write Redux logic, replacing the traditional Redux approach with a more modern, streamlined API.

**Key Features:**
- Simplified store setup
- Built-in Immer for immutable updates
- createSlice API reduces boilerplate
- RTK Query for data fetching
- Redux DevTools integration
- TypeScript support
- Thunk middleware included
- Automatic action creators
- Entity adapter for normalized state

## Why Use Redux Toolkit?

### Problems It Solves

1. **Boilerplate**: Dramatically reduces Redux boilerplate code
2. **Immutability**: Immer allows "mutative" code that's actually immutable
3. **Configuration**: Simplified store setup with good defaults
4. **Data Fetching**: RTK Query handles caching and synchronization
5. **TypeScript**: Excellent type inference and support
6. **Best Practices**: Enforces Redux best practices

### When to Use Redux Toolkit

- ✅ Large-scale applications
- ✅ Complex state management needs
- ✅ Multiple developers/teams
- ✅ When you need time-travel debugging
- ✅ Predictable state updates
- ✅ Centralized state management
- ✅ When you need middleware ecosystem

### When NOT to Use Redux Toolkit

- ❌ Simple applications (use Zustand or Context)
- ❌ Server state only (use TanStack Query)
- ❌ Small projects with minimal state
- ❌ When learning curve is a concern

## Comparison with Alternatives

### Redux Toolkit vs Zustand

| Feature | Redux Toolkit | Zustand |
|---------|--------------|---------|
| Bundle Size | ~11KB | ~1KB |
| Boilerplate | Low (with RTK) | Minimal |
| Learning Curve | Moderate | Easy |
| DevTools | Excellent | Good |
| Middleware | Extensive | Basic |
| Best For | Large apps | Most apps |
| Time Travel | Yes | No |

**Choose Redux Toolkit if:** You need robust state management for large applications
**Choose Zustand if:** You want simplicity and don't need Redux's ecosystem

### Redux Toolkit vs Context API

| Feature | Redux Toolkit | Context API |
|---------|--------------|-------------|
| Performance | Excellent | Can cause re-renders |
| DevTools | Yes | No |
| Middleware | Yes | No |
| Learning Curve | Moderate | Easy |
| Best For | Complex state | Simple state |

### Redux Toolkit vs MobX

| Feature | Redux Toolkit | MobX |
|---------|--------------|------|
| Paradigm | Immutable | Mutable |
| Boilerplate | Low | Very Low |
| Learning Curve | Moderate | Easy |
| Predictability | High | Moderate |
| Best For | Predictable updates | Reactive updates |

## Installation & Setup

### Installation

```bash
# npm
npm install @reduxjs/toolkit react-redux

# yarn
yarn add @reduxjs/toolkit react-redux

# pnpm
pnpm add @reduxjs/toolkit react-redux
```

### Basic Store Setup

```typescript
// src/store/store.ts
import { configureStore } from '@reduxjs/toolkit'
import counterReducer from './features/counterSlice'

export const store = configureStore({
  reducer: {
    counter: counterReducer,
  },
})

export type RootState = ReturnType<typeof store.getState>
export type AppDispatch = typeof store.dispatch
```

### Provider Setup

```typescript
// src/main.tsx
import React from 'react'
import ReactDOM from 'react-dom/client'
import { Provider } from 'react-redux'
import { store } from './store/store'
import App from './App'

ReactDOM.createRoot(document.getElementById('root')!).render(
  <React.StrictMode>
    <Provider store={store}>
      <App />
    </Provider>
  </React.StrictMode>
)
```

### TypeScript Hooks

```typescript
// src/store/hooks.ts
import { TypedUseSelectorHook, useDispatch, useSelector } from 'react-redux'
import type { RootState, AppDispatch } from './store'

export const useAppDispatch = () => useDispatch<AppDispatch>()
export const useAppSelector: TypedUseSelectorHook<RootState> = useSelector
```

## Core Concepts

### 1. createSlice

```typescript
// src/store/features/counterSlice.ts
import { createSlice, PayloadAction } from '@reduxjs/toolkit'

interface CounterState {
  value: number
  status: 'idle' | 'loading'
}

const initialState: CounterState = {
  value: 0,
  status: 'idle'
}

export const counterSlice = createSlice({
  name: 'counter',
  initialState,
  reducers: {
    increment: (state) => {
      // Immer allows "mutative" code
      state.value += 1
    },
    decrement: (state) => {
      state.value -= 1
    },
    incrementByAmount: (state, action: PayloadAction<number>) => {
      state.value += action.payload
    },
    reset: (state) => {
      state.value = 0
    }
  }
})

export const { increment, decrement, incrementByAmount, reset } = counterSlice.actions
export default counterSlice.reducer
```

### 2. Using in Components

```typescript
import { useAppSelector, useAppDispatch } from '../store/hooks'
import { increment, decrement, incrementByAmount } from '../store/features/counterSlice'

function Counter() {
  const count = useAppSelector((state) => state.counter.value)
  const dispatch = useAppDispatch()

  return (
    <div>
      <h1>Count: {count}</h1>
      <button onClick={() => dispatch(increment())}>+</button>
      <button onClick={() => dispatch(decrement())}>-</button>
      <button onClick={() => dispatch(incrementByAmount(5))}>+5</button>
    </div>
  )
}
```

### 3. Async Thunks

```typescript
import { createSlice, createAsyncThunk, PayloadAction } from '@reduxjs/toolkit'

interface User {
  id: number
  name: string
  email: string
}

interface UserState {
  user: User | null
  loading: boolean
  error: string | null
}

const initialState: UserState = {
  user: null,
  loading: false,
  error: null
}

// Async thunk
export const fetchUser = createAsyncThunk(
  'user/fetchUser',
  async (userId: number) => {
    const response = await fetch(`/api/users/${userId}`)
    return response.json()
  }
)

export const userSlice = createSlice({
  name: 'user',
  initialState,
  reducers: {
    clearUser: (state) => {
      state.user = null
    }
  },
  extraReducers: (builder) => {
    builder
      .addCase(fetchUser.pending, (state) => {
        state.loading = true
        state.error = null
      })
      .addCase(fetchUser.fulfilled, (state, action: PayloadAction<User>) => {
        state.loading = false
        state.user = action.payload
      })
      .addCase(fetchUser.rejected, (state, action) => {
        state.loading = false
        state.error = action.error.message || 'Failed to fetch user'
      })
  }
})

export const { clearUser } = userSlice.actions
export default userSlice.reducer
```

### 4. Selectors

```typescript
// src/store/features/counterSlice.ts
export const selectCount = (state: RootState) => state.counter.value
export const selectStatus = (state: RootState) => state.counter.status

// Memoized selector with createSelector
import { createSelector } from '@reduxjs/toolkit'

export const selectDoubleCount = createSelector(
  [selectCount],
  (count) => count * 2
)

// Usage in component
const count = useAppSelector(selectCount)
const doubleCount = useAppSelector(selectDoubleCount)
```

## Basic Usage

### Todo List Example

```typescript
// src/store/features/todosSlice.ts
import { createSlice, PayloadAction } from '@reduxjs/toolkit'

interface Todo {
  id: string
  text: string
  completed: boolean
}

interface TodosState {
  items: Todo[]
  filter: 'all' | 'active' | 'completed'
}

const initialState: TodosState = {
  items: [],
  filter: 'all'
}

export const todosSlice = createSlice({
  name: 'todos',
  initialState,
  reducers: {
    addTodo: (state, action: PayloadAction<string>) => {
      state.items.push({
        id: Date.now().toString(),
        text: action.payload,
        completed: false
      })
    },
    toggleTodo: (state, action: PayloadAction<string>) => {
      const todo = state.items.find(t => t.id === action.payload)
      if (todo) {
        todo.completed = !todo.completed
      }
    },
    removeTodo: (state, action: PayloadAction<string>) => {
      state.items = state.items.filter(t => t.id !== action.payload)
    },
    setFilter: (state, action: PayloadAction<TodosState['filter']>) => {
      state.filter = action.payload
    }
  }
})

export const { addTodo, toggleTodo, removeTodo, setFilter } = todosSlice.actions

// Selectors
export const selectTodos = (state: RootState) => state.todos.items
export const selectFilter = (state: RootState) => state.todos.filter

export const selectFilteredTodos = createSelector(
  [selectTodos, selectFilter],
  (todos, filter) => {
    switch (filter) {
      case 'active':
        return todos.filter(t => !t.completed)
      case 'completed':
        return todos.filter(t => t.completed)
      default:
        return todos
    }
  }
)

export default todosSlice.reducer
```

```typescript
// src/components/TodoList.tsx
import { useState } from 'react'
import { useAppSelector, useAppDispatch } from '../store/hooks'
import { addTodo, toggleTodo, removeTodo, selectFilteredTodos } from '../store/features/todosSlice'

function TodoList() {
  const [input, setInput] = useState('')
  const todos = useAppSelector(selectFilteredTodos)
  const dispatch = useAppDispatch()

  const handleSubmit = (e: React.FormEvent) => {
    e.preventDefault()
    if (input.trim()) {
      dispatch(addTodo(input))
      setInput('')
    }
  }

  return (
    <div>
      <form onSubmit={handleSubmit}>
        <input
          value={input}
          onChange={(e) => setInput(e.target.value)}
          placeholder="Add todo..."
        />
        <button type="submit">Add</button>
      </form>

      <ul>
        {todos.map((todo) => (
          <li key={todo.id}>
            <input
              type="checkbox"
              checked={todo.completed}
              onChange={() => dispatch(toggleTodo(todo.id))}
            />
            <span style={{ textDecoration: todo.completed ? 'line-through' : 'none' }}>
              {todo.text}
            </span>
            <button onClick={() => dispatch(removeTodo(todo.id))}>Delete</button>
          </li>
        ))}
      </ul>
    </div>
  )
}
```

## Advanced Usage

### 1. RTK Query

```typescript
// src/store/services/api.ts
import { createApi, fetchBaseQuery } from '@reduxjs/toolkit/query/react'

interface Post {
  id: number
  title: string
  body: string
}

export const api = createApi({
  reducerPath: 'api',
  baseQuery: fetchBaseQuery({ baseUrl: '/api' }),
  tagTypes: ['Post'],
  endpoints: (builder) => ({
    getPosts: builder.query<Post[], void>({
      query: () => 'posts',
      providesTags: ['Post']
    }),
    getPost: builder.query<Post, number>({
      query: (id) => `posts/${id}`,
      providesTags: (result, error, id) => [{ type: 'Post', id }]
    }),
    createPost: builder.mutation<Post, Partial<Post>>({
      query: (body) => ({
        url: 'posts',
        method: 'POST',
        body
      }),
      invalidatesTags: ['Post']
    }),
    updatePost: builder.mutation<Post, Partial<Post> & { id: number }>({
      query: ({ id, ...patch }) => ({
        url: `posts/${id}`,
        method: 'PATCH',
        body: patch
      }),
      invalidatesTags: (result, error, { id }) => [{ type: 'Post', id }]
    }),
    deletePost: builder.mutation<void, number>({
      query: (id) => ({
        url: `posts/${id}`,
        method: 'DELETE'
      }),
      invalidatesTags: ['Post']
    })
  })
})

export const {
  useGetPostsQuery,
  useGetPostQuery,
  useCreatePostMutation,
  useUpdatePostMutation,
  useDeletePostMutation
} = api
```

```typescript
// Add to store
import { api } from './services/api'

export const store = configureStore({
  reducer: {
    [api.reducerPath]: api.reducer,
  },
  middleware: (getDefaultMiddleware) =>
    getDefaultMiddleware().concat(api.middleware),
})
```

```typescript
// Usage in component
import { useGetPostsQuery, useCreatePostMutation } from '../store/services/api'

function Posts() {
  const { data: posts, isLoading, error } = useGetPostsQuery()
  const [createPost] = useCreatePostMutation()

  if (isLoading) return <div>Loading...</div>
  if (error) return <div>Error loading posts</div>

  return (
    <div>
      {posts?.map(post => (
        <div key={post.id}>
          <h3>{post.title}</h3>
          <p>{post.body}</p>
        </div>
      ))}
      <button onClick={() => createPost({ title: 'New Post', body: 'Content' })}>
        Add Post
      </button>
    </div>
  )
}
```

### 2. Entity Adapter

```typescript
import { createSlice, createEntityAdapter, PayloadAction } from '@reduxjs/toolkit'

interface User {
  id: string
  name: string
  email: string
}

const usersAdapter = createEntityAdapter<User>({
  selectId: (user) => user.id,
  sortComparer: (a, b) => a.name.localeCompare(b.name)
})

const usersSlice = createSlice({
  name: 'users',
  initialState: usersAdapter.getInitialState(),
  reducers: {
    userAdded: usersAdapter.addOne,
    usersReceived: usersAdapter.setAll,
    userUpdated: usersAdapter.updateOne,
    userRemoved: usersAdapter.removeOne
  }
})

export const { userAdded, usersReceived, userUpdated, userRemoved } = usersSlice.actions

// Selectors
export const {
  selectAll: selectAllUsers,
  selectById: selectUserById,
  selectIds: selectUserIds
} = usersAdapter.getSelectors((state: RootState) => state.users)

export default usersSlice.reducer
```

### 3. Middleware

```typescript
import { createListenerMiddleware } from '@reduxjs/toolkit'

const listenerMiddleware = createListenerMiddleware()

// Listen to specific actions
listenerMiddleware.startListening({
  actionCreator: userLoggedIn,
  effect: async (action, listenerApi) => {
    // Save to localStorage
    localStorage.setItem('user', JSON.stringify(action.payload))
    
    // Dispatch other actions
    listenerApi.dispatch(fetchUserPreferences(action.payload.id))
  }
})

// Add to store
export const store = configureStore({
  reducer: rootReducer,
  middleware: (getDefaultMiddleware) =>
    getDefaultMiddleware().prepend(listenerMiddleware.middleware)
})
```

### 4. Prepare Reducers

```typescript
const todosSlice = createSlice({
  name: 'todos',
  initialState,
  reducers: {
    addTodo: {
      reducer: (state, action: PayloadAction<Todo>) => {
        state.items.push(action.payload)
      },
      prepare: (text: string) => {
        return {
          payload: {
            id: nanoid(),
            text,
            completed: false,
            createdAt: new Date().toISOString()
          }
        }
      }
    }
  }
})
```

## Real-World Examples

### Authentication Store

```typescript
// src/store/features/authSlice.ts
import { createSlice, createAsyncThunk, PayloadAction } from '@reduxjs/toolkit'

interface User {
  id: string
  email: string
  name: string
}

interface AuthState {
  user: User | null
  token: string | null
  loading: boolean
  error: string | null
}

const initialState: AuthState = {
  user: null,
  token: localStorage.getItem('token'),
  loading: false,
  error: null
}

export const login = createAsyncThunk(
  'auth/login',
  async ({ email, password }: { email: string; password: string }) => {
    const response = await fetch('/api/auth/login', {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({ email, password })
    })
    
    if (!response.ok) throw new Error('Login failed')
    
    const data = await response.json()
    localStorage.setItem('token', data.token)
    return data
  }
)

export const logout = createAsyncThunk('auth/logout', async () => {
  localStorage.removeItem('token')
})

export const authSlice = createSlice({
  name: 'auth',
  initialState,
  reducers: {
    clearError: (state) => {
      state.error = null
    }
  },
  extraReducers: (builder) => {
    builder
      // Login
      .addCase(login.pending, (state) => {
        state.loading = true
        state.error = null
      })
      .addCase(login.fulfilled, (state, action) => {
        state.loading = false
        state.user = action.payload.user
        state.token = action.payload.token
      })
      .addCase(login.rejected, (state, action) => {
        state.loading = false
        state.error = action.error.message || 'Login failed'
      })
      // Logout
      .addCase(logout.fulfilled, (state) => {
        state.user = null
        state.token = null
      })
  }
})

export const { clearError } = authSlice.actions
export const selectUser = (state: RootState) => state.auth.user
export const selectIsAuthenticated = (state: RootState) => !!state.auth.token

export default authSlice.reducer
```

### Shopping Cart

```typescript
// src/store/features/cartSlice.ts
import { createSlice, PayloadAction } from '@reduxjs/toolkit'

interface CartItem {
  id: string
  name: string
  price: number
  quantity: number
}

interface CartState {
  items: CartItem[]
}

const initialState: CartState = {
  items: []
}

export const cartSlice = createSlice({
  name: 'cart',
  initialState,
  reducers: {
    addToCart: (state, action: PayloadAction<Omit<CartItem, 'quantity'>>) => {
      const existingItem = state.items.find(item => item.id === action.payload.id)
      
      if (existingItem) {
        existingItem.quantity += 1
      } else {
        state.items.push({ ...action.payload, quantity: 1 })
      }
    },
    removeFromCart: (state, action: PayloadAction<string>) => {
      state.items = state.items.filter(item => item.id !== action.payload)
    },
    updateQuantity: (state, action: PayloadAction<{ id: string; quantity: number }>) => {
      const item = state.items.find(item => item.id === action.payload.id)
      if (item) {
        item.quantity = action.payload.quantity
      }
    },
    clearCart: (state) => {
      state.items = []
    }
  }
})

export const { addToCart, removeFromCart, updateQuantity, clearCart } = cartSlice.actions

// Selectors
export const selectCartItems = (state: RootState) => state.cart.items
export const selectCartTotal = createSelector(
  [selectCartItems],
  (items) => items.reduce((total, item) => total + item.price * item.quantity, 0)
)
export const selectCartItemCount = createSelector(
  [selectCartItems],
  (items) => items.reduce((count, item) => count + item.quantity, 0)
)

export default cartSlice.reducer
```

## Best Practices

### 1. Normalize State Shape

```typescript
// ✅ Good: Normalized state
{
  users: {
    byId: {
      '1': { id: '1', name: 'John' },
      '2': { id: '2', name: 'Jane' }
    },
    allIds: ['1', '2']
  }
}

// ❌ Bad: Nested/duplicated data
{
  posts: [
    { id: 1, author: { id: 1, name: 'John' } },
    { id: 2, author: { id: 1, name: 'John' } }
  ]
}
```

### 2. Use createSlice

```typescript
// ✅ Good: createSlice
const slice = createSlice({
  name: 'counter',
  initialState: 0,
  reducers: {
    increment: state => state + 1
  }
})

// ❌ Bad: Manual action types and reducers
const INCREMENT = 'counter/increment'
const increment = () => ({ type: INCREMENT })
```

### 3. Type Your State

```typescript
// ✅ Good: Explicit types
interface CounterState {
  value: number
  status: 'idle' | 'loading'
}

const initialState: CounterState = {
  value: 0,
  status: 'idle'
}
```

### 4. Use RTK Query for Server State

```typescript
// ✅ Good: RTK Query for API data
const { data } = useGetUsersQuery()

// ❌ Bad: Manual fetch in slice
const fetchUsers = createAsyncThunk(...)
```

### 5. Memoize Selectors

```typescript
// ✅ Good: Memoized selector
export const selectExpensiveComputation = createSelector(
  [selectData],
  (data) => expensiveOperation(data)
)

// ❌ Bad: Computation in component
const result = expensiveOperation(data)
```

## Common Pitfalls

### 1. Mutating State Outside Immer

```typescript
// ❌ Bad: Direct mutation in extraReducers
.addCase(action, (state) => {
  state.items.push(item) // Only works in reducers, not extraReducers with some configs
})

// ✅ Good: Return new state or use Immer properly
.addCase(action, (state) => {
  state.items.push(item) // Works with Immer
})
```

### 2. Not Using TypeScript Helpers

```typescript
// ❌ Bad: Manual typing
const dispatch = useDispatch()
const state = useSelector((state: any) => state.counter)

// ✅ Good: Typed hooks
const dispatch = useAppDispatch()
const state = useAppSelector((state) => state.counter)
```

### 3. Putting Everything in Redux

```typescript
// ❌ Bad: Form state in Redux
const formData = useAppSelector(state => state.form)

// ✅ Good: Local state for forms
const [formData, setFormData] = useState({})
```

### 4. Not Handling Loading States

```typescript
// ❌ Bad: No loading state
.addCase(fetchData.fulfilled, (state, action) => {
  state.data = action.payload
})

// ✅ Good: Handle all states
.addCase(fetchData.pending, (state) => {
  state.loading = true
})
.addCase(fetchData.fulfilled, (state, action) => {
  state.loading = false
  state.data = action.payload
})
.addCase(fetchData.rejected, (state, action) => {
  state.loading = false
  state.error = action.error.message
})
```

## Resources

### Official Documentation
- [Redux Toolkit Docs](https://redux-toolkit.js.org/)
- [Redux Essentials Tutorial](https://redux.js.org/tutorials/essentials/part-1-overview-concepts)
- [RTK Query](https://redux-toolkit.js.org/rtk-query/overview)

### Tutorials & Guides
- [Redux Style Guide](https://redux.js.org/style-guide/)
- [TypeScript Quick Start](https://redux-toolkit.js.org/tutorials/typescript)

### Video Tutorials
- [Redux Toolkit Crash Course](https://www.youtube.com/results?search_query=redux+toolkit+crash+course)

### Community
- [Redux Discord](https://discord.gg/0ZcbPKXt5bZ6au5t)
- [GitHub Discussions](https://github.com/reduxjs/redux-toolkit/discussions)
- [Stack Overflow](https://stackoverflow.com/questions/tagged/redux-toolkit)

### Tools
- [Redux DevTools](https://github.com/reduxjs/redux-devtools)
- [Redux Toolkit CLI](https://redux-toolkit.js.org/api/other-exports#createslice)

---

**Next Steps:**
- Explore [Zustand](./zustand.md) for simpler state management
- Learn [RTK Query](https://redux-toolkit.js.org/rtk-query/overview) for data fetching
- Check out [Recoil](./recoil.md) for atomic state management
