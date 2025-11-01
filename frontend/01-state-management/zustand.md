# Zustand - Lightweight State Management for React

## Overview

Zustand (German for "state") is a small, fast, and scalable state management solution for React. It uses simplified flux principles with a hooks-based API that doesn't require boilerplate code, providers, or complex setup. Created by the developers behind React Spring, Zustand has become one of the most popular state management libraries in the React ecosystem.

**Key Features:**
- Minimal boilerplate
- No providers needed
- TypeScript-first
- Supports middleware
- DevTools integration
- Works with React 18+ concurrent features
- Tiny bundle size (~1KB)

## Why Use Zustand?

### Problems It Solves

1. **Context Hell**: Eliminates the need for multiple nested Context providers
2. **Prop Drilling**: Share state across components without passing props
3. **Boilerplate Reduction**: Much simpler than Redux with less code
4. **Performance**: Only re-renders components that use changed state
5. **Learning Curve**: Easy to learn with a simple API

### When to Use Zustand

- ✅ Global state management across your app
- ✅ Sharing state between unrelated components
- ✅ Managing complex application state
- ✅ When you want something simpler than Redux
- ✅ When Context API becomes too complex
- ✅ Server state that needs to be cached globally

### When NOT to Use Zustand

- ❌ Very simple apps with minimal state (use useState)
- ❌ State that's only used in one component (use useState)
- ❌ Server state management (use TanStack Query instead)
- ❌ When you need time-travel debugging (use Redux DevTools)

## Comparison with Alternatives

### Zustand vs Redux Toolkit

| Feature | Zustand | Redux Toolkit |
|---------|---------|---------------|
| Bundle Size | ~1KB | ~11KB |
| Boilerplate | Minimal | Moderate |
| Learning Curve | Easy | Moderate |
| DevTools | Yes | Yes |
| Middleware | Yes | Yes |
| TypeScript | Excellent | Excellent |
| Community | Growing | Massive |
| Best For | Most apps | Large enterprise apps |

**Choose Zustand if:** You want simplicity and don't need Redux's ecosystem
**Choose Redux if:** You need extensive middleware, time-travel debugging, or work on large teams with established Redux patterns

### Zustand vs Context API

| Feature | Zustand | Context API |
|---------|---------|-------------|
| Performance | Excellent | Can cause unnecessary re-renders |
| Setup | Simple | Requires providers |
| Selectors | Built-in | Manual optimization needed |
| DevTools | Yes | No |
| Outside React | Yes | No |
| Best For | Global state | Dependency injection |

**Choose Zustand if:** You need performant global state
**Choose Context if:** You're passing down dependencies or theme data

### Zustand vs Jotai/Recoil

| Feature | Zustand | Jotai/Recoil |
|---------|---------|--------------|
| Approach | Store-based | Atom-based |
| Learning Curve | Easy | Moderate |
| Bundle Size | Smaller | Larger |
| Async | Manual | Built-in |
| Best For | Traditional state | Derived/computed state |

## Installation & Setup

### Installation

```bash
# npm
npm install zustand

# yarn
yarn add zustand

# pnpm
pnpm add zustand
```

### TypeScript Setup

Zustand works great with TypeScript out of the box. No additional types needed!

### Basic Store Setup

```typescript
// src/stores/useStore.ts
import { create } from 'zustand'

interface StoreState {
  count: number
  increment: () => void
  decrement: () => void
  reset: () => void
}

export const useStore = create<StoreState>((set) => ({
  count: 0,
  increment: () => set((state) => ({ count: state.count + 1 })),
  decrement: () => set((state) => ({ count: state.count - 1 })),
  reset: () => set({ count: 0 }),
}))
```

## Core Concepts

### 1. Store Creation

The `create` function creates a hook that you can use in your components:

```typescript
import { create } from 'zustand'

const useStore = create((set) => ({
  // State
  bears: 0,
  
  // Actions
  increasePopulation: () => set((state) => ({ bears: state.bears + 1 })),
  removeAllBears: () => set({ bears: 0 }),
}))
```

### 2. State Updates

**Immutable Updates:**
```typescript
set((state) => ({ count: state.count + 1 }))
```

**Replace State:**
```typescript
set({ count: 0 })
```

**Partial Updates:**
```typescript
set({ count: 5 }) // Only updates count, other state remains
```

### 3. Selectors

Selectors prevent unnecessary re-renders by subscribing to specific state slices:

```typescript
// Only re-renders when count changes
const count = useStore((state) => state.count)

// Only re-renders when user changes
const user = useStore((state) => state.user)
```

### 4. Actions

Actions are functions that modify state:

```typescript
const increment = useStore((state) => state.increment)

// Use in component
<button onClick={increment}>Increment</button>
```

## Basic Usage

### Simple Counter Example

```typescript
// stores/counterStore.ts
import { create } from 'zustand'

interface CounterStore {
  count: number
  increment: () => void
  decrement: () => void
}

export const useCounterStore = create<CounterStore>((set) => ({
  count: 0,
  increment: () => set((state) => ({ count: state.count + 1 })),
  decrement: () => set((state) => ({ count: state.count - 1 })),
}))

// components/Counter.tsx
import { useCounterStore } from '../stores/counterStore'

export function Counter() {
  const count = useCounterStore((state) => state.count)
  const increment = useCounterStore((state) => state.increment)
  const decrement = useCounterStore((state) => state.decrement)

  return (
    <div>
      <h1>Count: {count}</h1>
      <button onClick={increment}>+</button>
      <button onClick={decrement}>-</button>
    </div>
  )
}
```

### Todo List Example

```typescript
// stores/todoStore.ts
import { create } from 'zustand'

interface Todo {
  id: string
  text: string
  completed: boolean
}

interface TodoStore {
  todos: Todo[]
  addTodo: (text: string) => void
  toggleTodo: (id: string) => void
  removeTodo: (id: string) => void
}

export const useTodoStore = create<TodoStore>((set) => ({
  todos: [],
  
  addTodo: (text) =>
    set((state) => ({
      todos: [
        ...state.todos,
        { id: Date.now().toString(), text, completed: false },
      ],
    })),
  
  toggleTodo: (id) =>
    set((state) => ({
      todos: state.todos.map((todo) =>
        todo.id === id ? { ...todo, completed: !todo.completed } : todo
      ),
    })),
  
  removeTodo: (id) =>
    set((state) => ({
      todos: state.todos.filter((todo) => todo.id !== id),
    })),
}))

// components/TodoList.tsx
import { useState } from 'react'
import { useTodoStore } from '../stores/todoStore'

export function TodoList() {
  const [input, setInput] = useState('')
  const todos = useTodoStore((state) => state.todos)
  const addTodo = useTodoStore((state) => state.addTodo)
  const toggleTodo = useTodoStore((state) => state.toggleTodo)
  const removeTodo = useTodoStore((state) => state.removeTodo)

  const handleSubmit = (e: React.FormEvent) => {
    e.preventDefault()
    if (input.trim()) {
      addTodo(input)
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
              onChange={() => toggleTodo(todo.id)}
            />
            <span style={{ textDecoration: todo.completed ? 'line-through' : 'none' }}>
              {todo.text}
            </span>
            <button onClick={() => removeTodo(todo.id)}>Delete</button>
          </li>
        ))}
      </ul>
    </div>
  )
}
```

## Advanced Usage

### 1. Async Actions

```typescript
interface UserStore {
  user: User | null
  loading: boolean
  error: string | null
  fetchUser: (id: string) => Promise<void>
}

export const useUserStore = create<UserStore>((set) => ({
  user: null,
  loading: false,
  error: null,
  
  fetchUser: async (id) => {
    set({ loading: true, error: null })
    try {
      const response = await fetch(`/api/users/${id}`)
      const user = await response.json()
      set({ user, loading: false })
    } catch (error) {
      set({ error: error.message, loading: false })
    }
  },
}))
```

### 2. Slices Pattern (Organizing Large Stores)

```typescript
// stores/slices/userSlice.ts
export interface UserSlice {
  user: User | null
  setUser: (user: User) => void
  clearUser: () => void
}

export const createUserSlice = (set) => ({
  user: null,
  setUser: (user) => set({ user }),
  clearUser: () => set({ user: null }),
})

// stores/slices/cartSlice.ts
export interface CartSlice {
  items: CartItem[]
  addItem: (item: CartItem) => void
  removeItem: (id: string) => void
}

export const createCartSlice = (set) => ({
  items: [],
  addItem: (item) => set((state) => ({ items: [...state.items, item] })),
  removeItem: (id) => set((state) => ({ 
    items: state.items.filter((item) => item.id !== id) 
  })),
})

// stores/useAppStore.ts
import { create } from 'zustand'
import { createUserSlice, UserSlice } from './slices/userSlice'
import { createCartSlice, CartSlice } from './slices/cartSlice'

type AppStore = UserSlice & CartSlice

export const useAppStore = create<AppStore>()((...a) => ({
  ...createUserSlice(...a),
  ...createCartSlice(...a),
}))
```

### 3. Middleware

**Persist Middleware (LocalStorage):**

```typescript
import { create } from 'zustand'
import { persist, createJSONStorage } from 'zustand/middleware'

interface AuthStore {
  token: string | null
  user: User | null
  login: (token: string, user: User) => void
  logout: () => void
}

export const useAuthStore = create<AuthStore>()(
  persist(
    (set) => ({
      token: null,
      user: null,
      login: (token, user) => set({ token, user }),
      logout: () => set({ token: null, user: null }),
    }),
    {
      name: 'auth-storage', // localStorage key
      storage: createJSONStorage(() => localStorage),
    }
  )
)
```

**Immer Middleware (Mutable Updates):**

```typescript
import { create } from 'zustand'
import { immer } from 'zustand/middleware/immer'

export const useStore = create<State>()(
  immer((set) => ({
    todos: [],
    addTodo: (text) =>
      set((state) => {
        // Mutate directly with Immer
        state.todos.push({ id: Date.now(), text, completed: false })
      }),
  }))
)
```

**DevTools Middleware:**

```typescript
import { create } from 'zustand'
import { devtools } from 'zustand/middleware'

export const useStore = create<State>()(
  devtools(
    (set) => ({
      count: 0,
      increment: () => set((state) => ({ count: state.count + 1 })),
    }),
    { name: 'MyStore' }
  )
)
```

**Combining Middleware:**

```typescript
export const useStore = create<State>()(
  devtools(
    persist(
      immer((set) => ({
        // Your store
      })),
      { name: 'my-storage' }
    ),
    { name: 'MyStore' }
  )
)
```

### 4. Computed Values (Selectors)

```typescript
interface Store {
  todos: Todo[]
  completedTodos: () => Todo[]
  activeTodos: () => Todo[]
}

export const useTodoStore = create<Store>((set, get) => ({
  todos: [],
  
  // Computed values
  completedTodos: () => get().todos.filter((t) => t.completed),
  activeTodos: () => get().todos.filter((t) => !t.completed),
}))

// Usage
const completedTodos = useTodoStore((state) => state.completedTodos())
```

### 5. Subscribing Outside React

```typescript
// Subscribe to changes
const unsubscribe = useStore.subscribe(
  (state) => state.count,
  (count) => {
    console.log('Count changed:', count)
  }
)

// Get state outside components
const count = useStore.getState().count

// Set state outside components
useStore.setState({ count: 5 })

// Cleanup
unsubscribe()
```

### 6. Transient Updates (No Re-renders)

```typescript
const useStore = create((set) => ({
  count: 0,
  // This won't trigger re-renders
  incrementTransient: () => {
    useStore.setState({ count: useStore.getState().count + 1 }, true)
  },
}))
```

## Real-World Examples

### E-commerce Shopping Cart

```typescript
// stores/cartStore.ts
import { create } from 'zustand'
import { persist } from 'zustand/middleware'

interface CartItem {
  id: string
  name: string
  price: number
  quantity: number
  image: string
}

interface CartStore {
  items: CartItem[]
  addItem: (item: Omit<CartItem, 'quantity'>) => void
  removeItem: (id: string) => void
  updateQuantity: (id: string, quantity: number) => void
  clearCart: () => void
  total: () => number
  itemCount: () => number
}

export const useCartStore = create<CartStore>()(
  persist(
    (set, get) => ({
      items: [],

      addItem: (item) =>
        set((state) => {
          const existingItem = state.items.find((i) => i.id === item.id)
          if (existingItem) {
            return {
              items: state.items.map((i) =>
                i.id === item.id ? { ...i, quantity: i.quantity + 1 } : i
              ),
            }
          }
          return { items: [...state.items, { ...item, quantity: 1 }] }
        }),

      removeItem: (id) =>
        set((state) => ({
          items: state.items.filter((item) => item.id !== id),
        })),

      updateQuantity: (id, quantity) =>
        set((state) => ({
          items: state.items.map((item) =>
            item.id === id ? { ...item, quantity } : item
          ),
        })),

      clearCart: () => set({ items: [] }),

      total: () => {
        const { items } = get()
        return items.reduce((sum, item) => sum + item.price * item.quantity, 0)
      },

      itemCount: () => {
        const { items } = get()
        return items.reduce((count, item) => count + item.quantity, 0)
      },
    }),
    {
      name: 'shopping-cart',
    }
  )
)

// components/Cart.tsx
import { useCartStore } from '../stores/cartStore'

export function Cart() {
  const items = useCartStore((state) => state.items)
  const total = useCartStore((state) => state.total())
  const removeItem = useCartStore((state) => state.removeItem)
  const updateQuantity = useCartStore((state) => state.updateQuantity)

  return (
    <div className="cart">
      <h2>Shopping Cart</h2>
      {items.map((item) => (
        <div key={item.id} className="cart-item">
          <img src={item.image} alt={item.name} />
          <div>
            <h3>{item.name}</h3>
            <p>${item.price}</p>
          </div>
          <input
            type="number"
            value={item.quantity}
            onChange={(e) => updateQuantity(item.id, parseInt(e.target.value))}
            min="1"
          />
          <button onClick={() => removeItem(item.id)}>Remove</button>
        </div>
      ))}
      <div className="cart-total">
        <h3>Total: ${total.toFixed(2)}</h3>
      </div>
    </div>
  )
}
```

### Authentication Store

```typescript
// stores/authStore.ts
import { create } from 'zustand'
import { persist } from 'zustand/middleware'

interface User {
  id: string
  email: string
  name: string
}

interface AuthStore {
  user: User | null
  token: string | null
  isAuthenticated: boolean
  loading: boolean
  error: string | null
  login: (email: string, password: string) => Promise<void>
  logout: () => void
  register: (email: string, password: string, name: string) => Promise<void>
  checkAuth: () => Promise<void>
}

export const useAuthStore = create<AuthStore>()(
  persist(
    (set) => ({
      user: null,
      token: null,
      isAuthenticated: false,
      loading: false,
      error: null,

      login: async (email, password) => {
        set({ loading: true, error: null })
        try {
          const response = await fetch('/api/auth/login', {
            method: 'POST',
            headers: { 'Content-Type': 'application/json' },
            body: JSON.stringify({ email, password }),
          })
          
          if (!response.ok) throw new Error('Login failed')
          
          const { user, token } = await response.json()
          set({ user, token, isAuthenticated: true, loading: false })
        } catch (error) {
          set({ error: error.message, loading: false })
        }
      },

      logout: () => {
        set({ user: null, token: null, isAuthenticated: false })
      },

      register: async (email, password, name) => {
        set({ loading: true, error: null })
        try {
          const response = await fetch('/api/auth/register', {
            method: 'POST',
            headers: { 'Content-Type': 'application/json' },
            body: JSON.stringify({ email, password, name }),
          })
          
          if (!response.ok) throw new Error('Registration failed')
          
          const { user, token } = await response.json()
          set({ user, token, isAuthenticated: true, loading: false })
        } catch (error) {
          set({ error: error.message, loading: false })
        }
      },

      checkAuth: async () => {
        const token = useAuthStore.getState().token
        if (!token) return

        try {
          const response = await fetch('/api/auth/me', {
            headers: { Authorization: `Bearer ${token}` },
          })
          
          if (!response.ok) throw new Error('Auth check failed')
          
          const user = await response.json()
          set({ user, isAuthenticated: true })
        } catch (error) {
          set({ user: null, token: null, isAuthenticated: false })
        }
      },
    }),
    {
      name: 'auth-storage',
      partialize: (state) => ({ token: state.token, user: state.user }),
    }
  )
)
```

## Best Practices

### 1. Store Organization

```typescript
// ✅ Good: Separate stores by domain
stores/
  ├── authStore.ts
  ├── cartStore.ts
  ├── userStore.ts
  └── settingsStore.ts

// ❌ Bad: One giant store
stores/
  └── globalStore.ts // Everything in one file
```

### 2. Use Selectors

```typescript
// ✅ Good: Specific selectors
const count = useStore((state) => state.count)
const increment = useStore((state) => state.increment)

// ❌ Bad: Selecting entire store
const store = useStore() // Re-renders on any state change
```

### 3. Shallow Equality for Objects

```typescript
import { shallow } from 'zustand/shallow'

// ✅ Good: Shallow comparison for objects
const { count, user } = useStore(
  (state) => ({ count: state.count, user: state.user }),
  shallow
)

// ❌ Bad: New object on every render
const { count, user } = useStore((state) => ({ 
  count: state.count, 
  user: state.user 
}))
```

### 4. Async Actions Pattern

```typescript
// ✅ Good: Handle loading and errors
fetchData: async () => {
  set({ loading: true, error: null })
  try {
    const data = await api.fetch()
    set({ data, loading: false })
  } catch (error) {
    set({ error: error.message, loading: false })
  }
}

// ❌ Bad: No error handling
fetchData: async () => {
  const data = await api.fetch()
  set({ data })
}
```

### 5. TypeScript Best Practices

```typescript
// ✅ Good: Explicit types
interface Store {
  count: number
  increment: () => void
}

export const useStore = create<Store>((set) => ({
  count: 0,
  increment: () => set((state) => ({ count: state.count + 1 })),
}))

// ❌ Bad: Implicit any
export const useStore = create((set) => ({
  count: 0,
  increment: () => set((state) => ({ count: state.count + 1 })),
}))
```

### 6. Don't Mutate State Directly

```typescript
// ✅ Good: Immutable updates
set((state) => ({ items: [...state.items, newItem] }))

// ❌ Bad: Direct mutation
set((state) => {
  state.items.push(newItem) // Don't do this!
  return state
})

// ✅ Alternative: Use Immer middleware
import { immer } from 'zustand/middleware/immer'

const useStore = create(immer((set) => ({
  items: [],
  addItem: (item) => set((state) => {
    state.items.push(item) // OK with Immer
  }),
})))
```

## Common Pitfalls

### 1. Unnecessary Re-renders

```typescript
// ❌ Problem: Re-renders on any state change
function Component() {
  const store = useStore()
  return <div>{store.count}</div>
}

// ✅ Solution: Use selectors
function Component() {
  const count = useStore((state) => state.count)
  return <div>{count}</div>
}
```

### 2. Stale Closures in Actions

```typescript
// ❌ Problem: Stale closure
const useStore = create((set) => {
  let count = 0
  return {
    count,
    increment: () => set({ count: count + 1 }), // Always uses initial count
  }
})

// ✅ Solution: Use state parameter
const useStore = create((set) => ({
  count: 0,
  increment: () => set((state) => ({ count: state.count + 1 })),
}))
```

### 3. Not Cleaning Up Subscriptions

```typescript
// ❌ Problem: Memory leak
useEffect(() => {
  useStore.subscribe(...)
}, [])

// ✅ Solution: Return cleanup function
useEffect(() => {
  const unsubscribe = useStore.subscribe(...)
  return unsubscribe
}, [])
```

### 4. Mixing Server and Client State

```typescript
// ❌ Bad: Using Zustand for server data
const useStore = create((set) => ({
  users: [],
  fetchUsers: async () => {
    const users = await api.getUsers()
    set({ users })
  },
}))

// ✅ Good: Use TanStack Query for server data
import { useQuery } from '@tanstack/react-query'

function Users() {
  const { data: users } = useQuery({
    queryKey: ['users'],
    queryFn: api.getUsers,
  })
}
```

## Resources

### Official Documentation
- [Zustand GitHub](https://github.com/pmndrs/zustand)
- [Zustand Documentation](https://docs.pmnd.rs/zustand)

### Tutorials & Articles
- [Zustand Tutorial - Official Docs](https://docs.pmnd.rs/zustand/getting-started/introduction)
- [Why I Switched from Redux to Zustand](https://tkdodo.eu/blog/working-with-zustand)
- [Zustand vs Redux: A Comprehensive Comparison](https://blog.logrocket.com/zustand-vs-redux/)

### Video Tutorials
- [Zustand Crash Course - YouTube](https://www.youtube.com/results?search_query=zustand+crash+course)
- [State Management with Zustand](https://egghead.io/courses/manage-application-state-with-zustand)

### Community
- [GitHub Discussions](https://github.com/pmndrs/zustand/discussions)
- [Discord - Poimandres](https://discord.gg/poimandres)
- [Stack Overflow](https://stackoverflow.com/questions/tagged/zustand)

### Tools
- [Redux DevTools Extension](https://github.com/reduxjs/redux-devtools) - Works with Zustand via devtools middleware
- [Zustand Middleware Collection](https://github.com/pmndrs/zustand#middleware)

---

**Next Steps:**
- Explore [Redux Toolkit](./redux-toolkit.md) for more complex state management needs
- Learn about [TanStack Query](../02-data-fetching/tanstack-query.md) for server state
- Check out [Jotai](./jotai.md) for atomic state management
