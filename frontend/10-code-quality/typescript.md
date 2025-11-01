# TypeScript - JavaScript with Syntax for Types

## Overview

TypeScript is a strongly typed programming language that builds on JavaScript, giving you better tooling at any scale. Developed and maintained by Microsoft, TypeScript adds optional static typing to JavaScript, enabling you to catch errors early, improve code quality, and enhance developer experience with intelligent code completion and refactoring.

**Key Features:**
- Static type checking
- Type inference
- Interfaces and type aliases
- Generics
- Enums and advanced types
- Excellent IDE support
- Gradual adoption
- Compiles to clean JavaScript
- Works with all JavaScript libraries
- Active development and community

## Why Use TypeScript?

### Problems It Solves

1. **Runtime Errors**: Catch type-related bugs at compile time
2. **Code Documentation**: Types serve as inline documentation
3. **Refactoring**: Safe and confident code refactoring
4. **IntelliSense**: Better autocomplete and code navigation
5. **Team Collaboration**: Clearer contracts between code modules
6. **Scalability**: Easier to maintain large codebases

### When to Use TypeScript

- ✅ Large-scale applications
- ✅ Team projects
- ✅ Long-term maintained projects
- ✅ When you want better IDE support
- ✅ Libraries and packages
- ✅ When working with complex data structures

### When NOT to Use TypeScript

- ❌ Quick prototypes or scripts
- ❌ Very small projects
- ❌ When the team is unfamiliar and unwilling to learn
- ❌ Legacy projects with no type definitions

## Comparison with Alternatives

### TypeScript vs JavaScript

| Feature | TypeScript | JavaScript |
|---------|-----------|------------|
| Type Safety | Static typing | Dynamic typing |
| Compile Time | Required | Not required |
| Learning Curve | Steeper | Easier |
| IDE Support | Excellent | Good |
| Error Detection | Compile-time | Runtime |
| Refactoring | Safer | Riskier |
| Bundle Size | Same (compiles to JS) | N/A |
| Best For | Large applications | Small scripts |

**Choose TypeScript if:** You want type safety and better tooling
**Choose JavaScript if:** You need quick prototyping or have a very small project

### TypeScript vs Flow

| Feature | TypeScript | Flow |
|---------|-----------|------|
| Maintainer | Microsoft | Meta |
| Community | Very Large | Smaller |
| Adoption | Widespread | Limited |
| IDE Support | Excellent | Good |
| Type Definitions | @types/* | flow-typed |
| Best For | Most projects | Meta projects |

## Installation & Setup

### Installation

```bash
# npm
npm install -D typescript @types/react @types/react-dom @types/node

# yarn
yarn add -D typescript @types/react @types/react-dom @types/node

# pnpm
pnpm add -D typescript @types/react @types/react-dom @types/node
```

### Initialize TypeScript

```bash
# Create tsconfig.json
npx tsc --init

# Or with specific settings
npx tsc --init --target ES2020 --module ESNext --jsx react-jsx
```

### React + Vite Setup

```bash
# Create new project
npm create vite@latest my-app -- --template react-ts

# Or add to existing project
npm install -D typescript @types/react @types/react-dom
```

### tsconfig.json for React

```json
{
  "compilerOptions": {
    "target": "ES2020",
    "useDefineForClassFields": true,
    "lib": ["ES2020", "DOM", "DOM.Iterable"],
    "module": "ESNext",
    "skipLibCheck": true,

    /* Bundler mode */
    "moduleResolution": "bundler",
    "allowImportingTsExtensions": true,
    "resolveJsonModule": true,
    "isolatedModules": true,
    "noEmit": true,
    "jsx": "react-jsx",

    /* Linting */
    "strict": true,
    "noUnusedLocals": true,
    "noUnusedParameters": true,
    "noFallthroughCasesInSwitch": true
  },
  "include": ["src"],
  "references": [{ "path": "./tsconfig.node.json" }]
}
```

## Core Concepts

### 1. Basic Types

```typescript
// Primitives
let name: string = "John"
let age: number = 30
let isActive: boolean = true
let nothing: null = null
let notDefined: undefined = undefined

// Arrays
let numbers: number[] = [1, 2, 3]
let strings: Array<string> = ["a", "b", "c"]

// Tuples
let tuple: [string, number] = ["hello", 10]

// Any (avoid when possible)
let anything: any = "could be anything"

// Unknown (safer than any)
let something: unknown = "safer"

// Void
function log(): void {
  console.log("No return value")
}

// Never
function throwError(): never {
  throw new Error("Error")
}
```

### 2. Interfaces

```typescript
interface User {
  id: number
  name: string
  email: string
  age?: number // Optional property
  readonly createdAt: Date // Read-only
}

const user: User = {
  id: 1,
  name: "John",
  email: "john@example.com",
  createdAt: new Date()
}

// Extending interfaces
interface Admin extends User {
  role: string
  permissions: string[]
}
```

### 3. Type Aliases

```typescript
type ID = string | number

type User = {
  id: ID
  name: string
  email: string
}

// Union types
type Status = "pending" | "approved" | "rejected"

// Intersection types
type Employee = User & {
  employeeId: string
  department: string
}
```

### 4. Generics

```typescript
// Generic function
function identity<T>(arg: T): T {
  return arg
}

const num = identity<number>(42)
const str = identity<string>("hello")

// Generic interface
interface Response<T> {
  data: T
  status: number
  message: string
}

const userResponse: Response<User> = {
  data: { id: 1, name: "John", email: "john@example.com", createdAt: new Date() },
  status: 200,
  message: "Success"
}

// Generic constraints
interface HasLength {
  length: number
}

function logLength<T extends HasLength>(arg: T): void {
  console.log(arg.length)
}
```

## Basic Usage

### React Component Types

```typescript
import { FC, ReactNode } from 'react'

// Function component with props
interface ButtonProps {
  label: string
  onClick: () => void
  variant?: 'primary' | 'secondary'
  disabled?: boolean
}

export const Button: FC<ButtonProps> = ({ 
  label, 
  onClick, 
  variant = 'primary',
  disabled = false 
}) => {
  return (
    <button 
      onClick={onClick} 
      disabled={disabled}
      className={`btn btn-${variant}`}
    >
      {label}
    </button>
  )
}

// With children
interface CardProps {
  title: string
  children: ReactNode
}

export const Card: FC<CardProps> = ({ title, children }) => {
  return (
    <div className="card">
      <h2>{title}</h2>
      {children}
    </div>
  )
}
```

### React Hooks with TypeScript

```typescript
import { useState, useEffect, useRef } from 'react'

// useState
const [count, setCount] = useState<number>(0)
const [user, setUser] = useState<User | null>(null)

// useEffect
useEffect(() => {
  // Effect logic
  return () => {
    // Cleanup
  }
}, [])

// useRef
const inputRef = useRef<HTMLInputElement>(null)
const timerRef = useRef<number | null>(null)

// Custom hook
function useLocalStorage<T>(key: string, initialValue: T) {
  const [value, setValue] = useState<T>(() => {
    const item = localStorage.getItem(key)
    return item ? JSON.parse(item) : initialValue
  })

  useEffect(() => {
    localStorage.setItem(key, JSON.stringify(value))
  }, [key, value])

  return [value, setValue] as const
}
```

### Event Handlers

```typescript
import { ChangeEvent, FormEvent, MouseEvent } from 'react'

function Form() {
  const handleChange = (e: ChangeEvent<HTMLInputElement>) => {
    console.log(e.target.value)
  }

  const handleSubmit = (e: FormEvent<HTMLFormElement>) => {
    e.preventDefault()
    // Handle submit
  }

  const handleClick = (e: MouseEvent<HTMLButtonElement>) => {
    console.log('Clicked')
  }

  return (
    <form onSubmit={handleSubmit}>
      <input onChange={handleChange} />
      <button onClick={handleClick}>Submit</button>
    </form>
  )
}
```

## Advanced Usage

### 1. Utility Types

```typescript
// Partial - Makes all properties optional
interface User {
  id: number
  name: string
  email: string
}

type PartialUser = Partial<User>
// { id?: number; name?: string; email?: string }

// Required - Makes all properties required
type RequiredUser = Required<PartialUser>

// Pick - Select specific properties
type UserPreview = Pick<User, 'id' | 'name'>
// { id: number; name: string }

// Omit - Exclude specific properties
type UserWithoutId = Omit<User, 'id'>
// { name: string; email: string }

// Record - Create object type with specific keys
type Roles = 'admin' | 'user' | 'guest'
type RolePermissions = Record<Roles, string[]>
// { admin: string[]; user: string[]; guest: string[] }

// ReturnType - Get function return type
function getUser() {
  return { id: 1, name: "John" }
}
type UserReturn = ReturnType<typeof getUser>

// Parameters - Get function parameters
type UserParams = Parameters<typeof getUser>
```

### 2. Advanced React Patterns

```typescript
// Discriminated Unions
type LoadingState = { status: 'loading' }
type SuccessState<T> = { status: 'success'; data: T }
type ErrorState = { status: 'error'; error: string }

type AsyncState<T> = LoadingState | SuccessState<T> | ErrorState

function DataDisplay<T>({ state }: { state: AsyncState<T> }) {
  switch (state.status) {
    case 'loading':
      return <div>Loading...</div>
    case 'success':
      return <div>{JSON.stringify(state.data)}</div>
    case 'error':
      return <div>Error: {state.error}</div>
  }
}

// Render Props
interface RenderPropComponent<T> {
  data: T
  render: (data: T) => ReactNode
}

function DataRenderer<T>({ data, render }: RenderPropComponent<T>) {
  return <>{render(data)}</>
}

// Higher Order Components
function withLoading<P extends object>(
  Component: FC<P>
): FC<P & { loading: boolean }> {
  return ({ loading, ...props }) => {
    if (loading) return <div>Loading...</div>
    return <Component {...(props as P)} />
  }
}
```

### 3. API Response Typing

```typescript
// API Response types
interface ApiResponse<T> {
  data: T
  status: number
  message: string
}

interface PaginatedResponse<T> {
  data: T[]
  page: number
  totalPages: number
  totalItems: number
}

// Fetch with types
async function fetchUser(id: number): Promise<User> {
  const response = await fetch(`/api/users/${id}`)
  const data: ApiResponse<User> = await response.json()
  return data.data
}

async function fetchUsers(): Promise<PaginatedResponse<User>> {
  const response = await fetch('/api/users')
  return response.json()
}

// With error handling
type Result<T, E = Error> = 
  | { success: true; data: T }
  | { success: false; error: E }

async function safeF fetch<T>(url: string): Promise<Result<T>> {
  try {
    const response = await fetch(url)
    const data = await response.json()
    return { success: true, data }
  } catch (error) {
    return { success: false, error: error as Error }
  }
}
```

### 4. Type Guards

```typescript
// Type predicate
function isUser(obj: any): obj is User {
  return 'id' in obj && 'name' in obj && 'email' in obj
}

// Usage
function processData(data: unknown) {
  if (isUser(data)) {
    console.log(data.name) // TypeScript knows it's a User
  }
}

// Discriminated union type guard
type Shape = 
  | { kind: 'circle'; radius: number }
  | { kind: 'rectangle'; width: number; height: number }

function getArea(shape: Shape): number {
  switch (shape.kind) {
    case 'circle':
      return Math.PI * shape.radius ** 2
    case 'rectangle':
      return shape.width * shape.height
  }
}
```

## Real-World Examples

### Form Handling with TypeScript

```typescript
import { useState, FormEvent, ChangeEvent } from 'react'

interface FormData {
  email: string
  password: string
  rememberMe: boolean
}

interface FormErrors {
  email?: string
  password?: string
}

export function LoginForm() {
  const [formData, setFormData] = useState<FormData>({
    email: '',
    password: '',
    rememberMe: false
  })

  const [errors, setErrors] = useState<FormErrors>({})
  const [isSubmitting, setIsSubmitting] = useState(false)

  const handleChange = (e: ChangeEvent<HTMLInputElement>) => {
    const { name, value, type, checked } = e.target
    setFormData(prev => ({
      ...prev,
      [name]: type === 'checkbox' ? checked : value
    }))
  }

  const validate = (): boolean => {
    const newErrors: FormErrors = {}

    if (!formData.email) {
      newErrors.email = 'Email is required'
    } else if (!/\S+@\S+\.\S+/.test(formData.email)) {
      newErrors.email = 'Email is invalid'
    }

    if (!formData.password) {
      newErrors.password = 'Password is required'
    } else if (formData.password.length < 6) {
      newErrors.password = 'Password must be at least 6 characters'
    }

    setErrors(newErrors)
    return Object.keys(newErrors).length === 0
  }

  const handleSubmit = async (e: FormEvent<HTMLFormElement>) => {
    e.preventDefault()

    if (!validate()) return

    setIsSubmitting(true)
    try {
      const response = await fetch('/api/login', {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify(formData)
      })

      if (!response.ok) throw new Error('Login failed')

      const data = await response.json()
      console.log('Success:', data)
    } catch (error) {
      console.error('Error:', error)
    } finally {
      setIsSubmitting(false)
    }
  }

  return (
    <form onSubmit={handleSubmit}>
      <div>
        <label htmlFor="email">Email</label>
        <input
          id="email"
          name="email"
          type="email"
          value={formData.email}
          onChange={handleChange}
        />
        {errors.email && <span className="error">{errors.email}</span>}
      </div>

      <div>
        <label htmlFor="password">Password</label>
        <input
          id="password"
          name="password"
          type="password"
          value={formData.password}
          onChange={handleChange}
        />
        {errors.password && <span className="error">{errors.password}</span>}
      </div>

      <div>
        <label>
          <input
            name="rememberMe"
            type="checkbox"
            checked={formData.rememberMe}
            onChange={handleChange}
          />
          Remember me
        </label>
      </div>

      <button type="submit" disabled={isSubmitting}>
        {isSubmitting ? 'Logging in...' : 'Login'}
      </button>
    </form>
  )
}
```

### Context API with TypeScript

```typescript
import { createContext, useContext, useState, ReactNode, FC } from 'react'

interface User {
  id: number
  name: string
  email: string
}

interface AuthContextType {
  user: User | null
  login: (email: string, password: string) => Promise<void>
  logout: () => void
  isAuthenticated: boolean
}

const AuthContext = createContext<AuthContextType | undefined>(undefined)

export const useAuth = () => {
  const context = useContext(AuthContext)
  if (!context) {
    throw new Error('useAuth must be used within AuthProvider')
  }
  return context
}

interface AuthProviderProps {
  children: ReactNode
}

export const AuthProvider: FC<AuthProviderProps> = ({ children }) => {
  const [user, setUser] = useState<User | null>(null)

  const login = async (email: string, password: string) => {
    const response = await fetch('/api/login', {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({ email, password })
    })

    if (!response.ok) throw new Error('Login failed')

    const userData: User = await response.json()
    setUser(userData)
  }

  const logout = () => {
    setUser(null)
  }

  const value: AuthContextType = {
    user,
    login,
    logout,
    isAuthenticated: !!user
  }

  return <AuthContext.Provider value={value}>{children}</AuthContext.Provider>
}

// Usage
function App() {
  return (
    <AuthProvider>
      <Dashboard />
    </AuthProvider>
  )
}

function Dashboard() {
  const { user, logout, isAuthenticated } = useAuth()

  if (!isAuthenticated) {
    return <div>Please login</div>
  }

  return (
    <div>
      <h1>Welcome, {user?.name}</h1>
      <button onClick={logout}>Logout</button>
    </div>
  )
}
```

## Best Practices

### 1. Use Strict Mode

```json
// tsconfig.json
{
  "compilerOptions": {
    "strict": true,
    "noImplicitAny": true,
    "strictNullChecks": true,
    "strictFunctionTypes": true,
    "strictBindCallApply": true,
    "strictPropertyInitialization": true,
    "noImplicitThis": true,
    "alwaysStrict": true
  }
}
```

### 2. Avoid `any`

```typescript
// ❌ Bad
function processData(data: any) {
  return data.value
}

// ✅ Good
function processData<T extends { value: string }>(data: T) {
  return data.value
}

// ✅ Or use unknown
function processData(data: unknown) {
  if (typeof data === 'object' && data !== null && 'value' in data) {
    return (data as { value: string }).value
  }
}
```

### 3. Use Type Inference

```typescript
// ❌ Bad: Redundant type annotation
const count: number = 5

// ✅ Good: Let TypeScript infer
const count = 5

// ✅ Good: Explicit when needed
const users: User[] = []
```

### 4. Prefer Interfaces for Objects

```typescript
// ✅ Good: Interface for object shapes
interface User {
  id: number
  name: string
}

// ✅ Good: Type for unions/primitives
type Status = 'active' | 'inactive'
type ID = string | number
```

### 5. Use Const Assertions

```typescript
// ❌ Bad: Mutable array
const colors = ['red', 'green', 'blue']

// ✅ Good: Readonly tuple
const colors = ['red', 'green', 'blue'] as const

// ✅ Good: Readonly object
const config = {
  api: 'https://api.example.com',
  timeout: 5000
} as const
```

## Common Pitfalls

### 1. Type Assertions Gone Wrong

```typescript
// ❌ Bad: Unsafe assertion
const user = {} as User
console.log(user.name) // Runtime error!

// ✅ Good: Proper initialization
const user: User = {
  id: 1,
  name: 'John',
  email: 'john@example.com',
  createdAt: new Date()
}
```

### 2. Ignoring Null/Undefined

```typescript
// ❌ Bad: Not handling null
function getName(user: User | null) {
  return user.name // Error if user is null
}

// ✅ Good: Null check
function getName(user: User | null) {
  return user?.name ?? 'Unknown'
}
```

### 3. Overusing Enums

```typescript
// ❌ Bad: Enum (generates runtime code)
enum Status {
  Active,
  Inactive
}

// ✅ Good: Union type (no runtime code)
type Status = 'active' | 'inactive'

// ✅ Or const object
const Status = {
  Active: 'active',
  Inactive: 'inactive'
} as const

type Status = typeof Status[keyof typeof Status]
```

### 4. Not Using Discriminated Unions

```typescript
// ❌ Bad: Hard to narrow types
interface Response {
  success: boolean
  data?: User
  error?: string
}

// ✅ Good: Discriminated union
type Response = 
  | { success: true; data: User }
  | { success: false; error: string }

function handleResponse(response: Response) {
  if (response.success) {
    console.log(response.data.name) // TypeScript knows data exists
  } else {
    console.log(response.error) // TypeScript knows error exists
  }
}
```

## Resources

### Official Documentation
- [TypeScript Handbook](https://www.typescriptlang.org/docs/handbook/intro.html)
- [TypeScript Playground](https://www.typescriptlang.org/play)
- [TypeScript GitHub](https://github.com/microsoft/TypeScript)

### React + TypeScript
- [React TypeScript Cheatsheet](https://react-typescript-cheatsheet.netlify.app/)
- [TypeScript with React](https://www.typescriptlang.org/docs/handbook/react.html)

### Tutorials & Courses
- [TypeScript Deep Dive](https://basarat.gitbook.io/typescript/)
- [Total TypeScript](https://www.totaltypescript.com/)
- [Execute Program - TypeScript](https://www.executeprogram.com/courses/typescript)

### Tools
- [DefinitelyTyped](https://github.com/DefinitelyTyped/DefinitelyTyped) - Type definitions
- [ts-node](https://github.com/TypeStrong/ts-node) - TypeScript execution
- [type-fest](https://github.com/sindresorhus/type-fest) - Utility types

### Community
- [TypeScript Discord](https://discord.com/invite/typescript)
- [Stack Overflow](https://stackoverflow.com/questions/tagged/typescript)
- [r/typescript](https://www.reddit.com/r/typescript/)

---

**Next Steps:**
- Learn [ESLint](./eslint.md) for code quality
- Explore [Zod](../06-forms/zod.md) for runtime type validation
- Check out [tRPC](../02-data-fetching/trpc.md) for end-to-end type safety
