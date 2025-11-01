# Vitest - Blazing Fast Unit Test Framework

## Overview

Vitest is a next-generation testing framework powered by Vite. It provides a fast, modern testing experience with native ESM support, TypeScript integration, and an API compatible with Jest. Created by the Vite team, Vitest leverages Vite's transformation pipeline to provide instant feedback and hot module replacement for tests.

**Key Features:**
- Lightning-fast test execution
- Jest-compatible API
- Native ESM, TypeScript, and JSX support
- Smart instant watch mode
- Component testing (React, Vue, Svelte)
- Built-in code coverage with c8
- Snapshot testing
- Mocking utilities
- Multi-threading with worker threads
- Vite ecosystem integration

## Why Use Vitest?

### Problems It Solves

1. **Speed**: Significantly faster than Jest
2. **Modern Syntax**: Native ESM and TypeScript support
3. **Developer Experience**: Instant feedback with HMR
4. **Configuration**: Works out-of-the-box with Vite projects
5. **Compatibility**: Jest-compatible API for easy migration
6. **Bundle Size**: Smaller footprint than Jest

### When to Use Vitest

- ✅ Vite-based projects
- ✅ Modern JavaScript/TypeScript projects
- ✅ When you need fast test execution
- ✅ Component testing
- ✅ Unit and integration tests
- ✅ Projects requiring ESM support

### When NOT to Use Vitest

- ❌ Non-Vite projects (Jest might be easier)
- ❌ Projects with heavy Jest plugin dependencies
- ❌ When team is unfamiliar with Vite
- ❌ Legacy projects with complex Jest setups

## Comparison with Alternatives

### Vitest vs Jest

| Feature | Vitest | Jest |
|---------|--------|------|
| Speed | Very Fast | Moderate |
| ESM Support | Native | Experimental |
| TypeScript | Native | Needs ts-jest |
| Watch Mode | Instant (HMR) | Slower |
| Config | Vite config | Separate config |
| API | Jest-compatible | Standard |
| Ecosystem | Growing | Massive |
| Best For | Vite projects | All projects |

**Choose Vitest if:** You use Vite or want blazing-fast tests
**Choose Jest if:** You need the largest ecosystem or don't use Vite

### Vitest vs React Testing Library

Note: These are complementary! Vitest is the test runner, React Testing Library provides testing utilities.

| Tool | Purpose |
|------|---------|
| Vitest | Test runner and framework |
| React Testing Library | Component testing utilities |
| Typical Usage | Use both together |

## Installation & Setup

### Installation

```bash
# npm
npm install -D vitest

# yarn
yarn add -D vitest

# pnpm
pnpm add -D vitest
```

### For React Testing

```bash
# Install React testing dependencies
npm install -D @testing-library/react @testing-library/jest-dom @testing-library/user-event jsdom
```

### Vite Configuration

```typescript
// vite.config.ts
import { defineConfig } from 'vite'
import react from '@vitejs/plugin-react'

export default defineConfig({
  plugins: [react()],
  test: {
    globals: true,
    environment: 'jsdom',
    setupFiles: './src/test/setup.ts',
    css: true,
  },
})
```

### Test Setup File

```typescript
// src/test/setup.ts
import { expect, afterEach } from 'vitest'
import { cleanup } from '@testing-library/react'
import * as matchers from '@testing-library/jest-dom/matchers'

expect.extend(matchers)

afterEach(() => {
  cleanup()
})
```

### Package.json Scripts

```json
{
  "scripts": {
    "test": "vitest",
    "test:ui": "vitest --ui",
    "test:run": "vitest run",
    "test:coverage": "vitest --coverage"
  }
}
```

### TypeScript Configuration

```json
// tsconfig.json
{
  "compilerOptions": {
    "types": ["vitest/globals", "@testing-library/jest-dom"]
  }
}
```

## Core Concepts

### 1. Test Structure

```typescript
import { describe, it, expect, test } from 'vitest'

// describe: Group related tests
describe('Math operations', () => {
  // it or test: Individual test case
  it('should add numbers correctly', () => {
    expect(1 + 1).toBe(2)
  })

  test('should subtract numbers correctly', () => {
    expect(5 - 3).toBe(2)
  })
})
```

### 2. Assertions

```typescript
import { expect } from 'vitest'

// Equality
expect(value).toBe(5)
expect(value).toEqual({ name: 'John' })
expect(value).not.toBe(10)

// Truthiness
expect(value).toBeTruthy()
expect(value).toBeFalsy()
expect(value).toBeNull()
expect(value).toBeUndefined()
expect(value).toBeDefined()

// Numbers
expect(value).toBeGreaterThan(3)
expect(value).toBeGreaterThanOrEqual(3)
expect(value).toBeLessThan(5)
expect(value).toBeCloseTo(0.3)

// Strings
expect(value).toMatch(/pattern/)
expect(value).toContain('substring')

// Arrays
expect(array).toContain(item)
expect(array).toHaveLength(3)

// Objects
expect(object).toHaveProperty('key')
expect(object).toMatchObject({ name: 'John' })

// Functions
expect(fn).toThrow()
expect(fn).toThrow('error message')
expect(fn).toHaveBeenCalled()
expect(fn).toHaveBeenCalledWith(arg1, arg2)
```

### 3. Setup and Teardown

```typescript
import { describe, it, beforeEach, afterEach, beforeAll, afterAll } from 'vitest'

describe('Database tests', () => {
  // Runs once before all tests
  beforeAll(() => {
    // Setup database connection
  })

  // Runs before each test
  beforeEach(() => {
    // Reset database state
  })

  // Runs after each test
  afterEach(() => {
    // Cleanup
  })

  // Runs once after all tests
  afterAll(() => {
    // Close database connection
  })

  it('should insert data', () => {
    // Test code
  })
})
```

### 4. Mocking

```typescript
import { vi } from 'vitest'

// Mock functions
const mockFn = vi.fn()
mockFn('hello')
expect(mockFn).toHaveBeenCalledWith('hello')

// Mock return values
const mockFn = vi.fn().mockReturnValue(42)
expect(mockFn()).toBe(42)

// Mock implementations
const mockFn = vi.fn((x) => x * 2)
expect(mockFn(5)).toBe(10)

// Mock modules
vi.mock('./api', () => ({
  fetchUser: vi.fn().mockResolvedValue({ id: 1, name: 'John' })
}))
```

## Basic Usage

### Simple Function Testing

```typescript
// src/utils/math.ts
export function add(a: number, b: number): number {
  return a + b
}

export function multiply(a: number, b: number): number {
  return a * b
}

// src/utils/math.test.ts
import { describe, it, expect } from 'vitest'
import { add, multiply } from './math'

describe('Math utilities', () => {
  describe('add', () => {
    it('should add two positive numbers', () => {
      expect(add(2, 3)).toBe(5)
    })

    it('should add negative numbers', () => {
      expect(add(-2, -3)).toBe(-5)
    })

    it('should handle zero', () => {
      expect(add(0, 5)).toBe(5)
    })
  })

  describe('multiply', () => {
    it('should multiply two numbers', () => {
      expect(multiply(3, 4)).toBe(12)
    })

    it('should return zero when multiplying by zero', () => {
      expect(multiply(5, 0)).toBe(0)
    })
  })
})
```

### React Component Testing

```typescript
// src/components/Button.tsx
interface ButtonProps {
  label: string
  onClick: () => void
  disabled?: boolean
}

export function Button({ label, onClick, disabled = false }: ButtonProps) {
  return (
    <button onClick={onClick} disabled={disabled}>
      {label}
    </button>
  )
}

// src/components/Button.test.tsx
import { describe, it, expect, vi } from 'vitest'
import { render, screen } from '@testing-library/react'
import userEvent from '@testing-library/user-event'
import { Button } from './Button'

describe('Button', () => {
  it('should render with label', () => {
    render(<Button label="Click me" onClick={() => {}} />)
    expect(screen.getByText('Click me')).toBeInTheDocument()
  })

  it('should call onClick when clicked', async () => {
    const handleClick = vi.fn()
    render(<Button label="Click me" onClick={handleClick} />)
    
    await userEvent.click(screen.getByText('Click me'))
    expect(handleClick).toHaveBeenCalledTimes(1)
  })

  it('should be disabled when disabled prop is true', () => {
    render(<Button label="Click me" onClick={() => {}} disabled />)
    expect(screen.getByText('Click me')).toBeDisabled()
  })
})
```

### Async Testing

```typescript
// src/api/users.ts
export async function fetchUser(id: number) {
  const response = await fetch(`/api/users/${id}`)
  return response.json()
}

// src/api/users.test.ts
import { describe, it, expect, vi } from 'vitest'
import { fetchUser } from './users'

describe('fetchUser', () => {
  it('should fetch user data', async () => {
    const mockUser = { id: 1, name: 'John' }
    
    global.fetch = vi.fn().mockResolvedValue({
      json: () => Promise.resolve(mockUser)
    })

    const user = await fetchUser(1)
    expect(user).toEqual(mockUser)
    expect(fetch).toHaveBeenCalledWith('/api/users/1')
  })

  it('should handle errors', async () => {
    global.fetch = vi.fn().mockRejectedValue(new Error('Network error'))

    await expect(fetchUser(1)).rejects.toThrow('Network error')
  })
})
```

## Advanced Usage

### 1. Snapshot Testing

```typescript
import { describe, it, expect } from 'vitest'
import { render } from '@testing-library/react'
import { UserCard } from './UserCard'

describe('UserCard', () => {
  it('should match snapshot', () => {
    const { container } = render(
      <UserCard name="John Doe" email="john@example.com" />
    )
    expect(container).toMatchSnapshot()
  })
})
```

### 2. Mocking Modules

```typescript
// src/services/api.ts
export const api = {
  getUsers: async () => {
    const response = await fetch('/api/users')
    return response.json()
  }
}

// src/components/UserList.test.tsx
import { describe, it, expect, vi, beforeEach } from 'vitest'
import { render, screen, waitFor } from '@testing-library/react'
import { UserList } from './UserList'
import { api } from '../services/api'

vi.mock('../services/api', () => ({
  api: {
    getUsers: vi.fn()
  }
}))

describe('UserList', () => {
  beforeEach(() => {
    vi.clearAllMocks()
  })

  it('should display users', async () => {
    const mockUsers = [
      { id: 1, name: 'John' },
      { id: 2, name: 'Jane' }
    ]

    vi.mocked(api.getUsers).mockResolvedValue(mockUsers)

    render(<UserList />)

    await waitFor(() => {
      expect(screen.getByText('John')).toBeInTheDocument()
      expect(screen.getByText('Jane')).toBeInTheDocument()
    })
  })

  it('should show error message on failure', async () => {
    vi.mocked(api.getUsers).mockRejectedValue(new Error('Failed'))

    render(<UserList />)

    await waitFor(() => {
      expect(screen.getByText(/error/i)).toBeInTheDocument()
    })
  })
})
```

### 3. Testing Custom Hooks

```typescript
// src/hooks/useCounter.ts
import { useState } from 'react'

export function useCounter(initialValue = 0) {
  const [count, setCount] = useState(initialValue)

  const increment = () => setCount(c => c + 1)
  const decrement = () => setCount(c => c - 1)
  const reset = () => setCount(initialValue)

  return { count, increment, decrement, reset }
}

// src/hooks/useCounter.test.ts
import { describe, it, expect } from 'vitest'
import { renderHook, act } from '@testing-library/react'
import { useCounter } from './useCounter'

describe('useCounter', () => {
  it('should initialize with default value', () => {
    const { result } = renderHook(() => useCounter())
    expect(result.current.count).toBe(0)
  })

  it('should initialize with custom value', () => {
    const { result } = renderHook(() => useCounter(10))
    expect(result.current.count).toBe(10)
  })

  it('should increment count', () => {
    const { result } = renderHook(() => useCounter())
    
    act(() => {
      result.current.increment()
    })

    expect(result.current.count).toBe(1)
  })

  it('should decrement count', () => {
    const { result } = renderHook(() => useCounter(5))
    
    act(() => {
      result.current.decrement()
    })

    expect(result.current.count).toBe(4)
  })

  it('should reset to initial value', () => {
    const { result } = renderHook(() => useCounter(10))
    
    act(() => {
      result.current.increment()
      result.current.increment()
      result.current.reset()
    })

    expect(result.current.count).toBe(10)
  })
})
```

### 4. Testing with Context

```typescript
// src/contexts/AuthContext.tsx
import { createContext, useContext, useState, ReactNode } from 'react'

interface AuthContextType {
  user: { name: string } | null
  login: (name: string) => void
  logout: () => void
}

const AuthContext = createContext<AuthContextType | undefined>(undefined)

export function AuthProvider({ children }: { children: ReactNode }) {
  const [user, setUser] = useState<{ name: string } | null>(null)

  const login = (name: string) => setUser({ name })
  const logout = () => setUser(null)

  return (
    <AuthContext.Provider value={{ user, login, logout }}>
      {children}
    </AuthContext.Provider>
  )
}

export const useAuth = () => {
  const context = useContext(AuthContext)
  if (!context) throw new Error('useAuth must be used within AuthProvider')
  return context
}

// src/components/UserProfile.test.tsx
import { describe, it, expect } from 'vitest'
import { render, screen } from '@testing-library/react'
import userEvent from '@testing-library/user-event'
import { AuthProvider } from '../contexts/AuthContext'
import { UserProfile } from './UserProfile'

function renderWithAuth(ui: React.ReactElement) {
  return render(<AuthProvider>{ui}</AuthProvider>)
}

describe('UserProfile', () => {
  it('should show login button when not authenticated', () => {
    renderWithAuth(<UserProfile />)
    expect(screen.getByText('Login')).toBeInTheDocument()
  })

  it('should show user name after login', async () => {
    renderWithAuth(<UserProfile />)
    
    await userEvent.click(screen.getByText('Login'))
    await userEvent.type(screen.getByPlaceholderText('Name'), 'John')
    await userEvent.click(screen.getByText('Submit'))

    expect(screen.getByText('Welcome, John')).toBeInTheDocument()
  })
})
```

### 5. Code Coverage

```bash
# Install coverage tool
npm install -D @vitest/coverage-v8

# Run tests with coverage
npm run test:coverage
```

```typescript
// vite.config.ts
export default defineConfig({
  test: {
    coverage: {
      provider: 'v8',
      reporter: ['text', 'json', 'html'],
      exclude: [
        'node_modules/',
        'src/test/',
      ],
    },
  },
})
```

### 6. Concurrent and Sequential Tests

```typescript
import { describe, it, expect } from 'vitest'

// Run tests concurrently (default)
describe('Concurrent tests', () => {
  it.concurrent('test 1', async () => {
    // Test code
  })

  it.concurrent('test 2', async () => {
    // Test code
  })
})

// Run tests sequentially
describe.sequential('Sequential tests', () => {
  it('test 1', () => {
    // Runs first
  })

  it('test 2', () => {
    // Runs after test 1
  })
})
```

## Real-World Examples

### Form Validation Testing

```typescript
// src/utils/validation.ts
export function validateEmail(email: string): boolean {
  const regex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/
  return regex.test(email)
}

export function validatePassword(password: string): {
  valid: boolean
  errors: string[]
} {
  const errors: string[] = []

  if (password.length < 8) {
    errors.push('Password must be at least 8 characters')
  }
  if (!/[A-Z]/.test(password)) {
    errors.push('Password must contain an uppercase letter')
  }
  if (!/[0-9]/.test(password)) {
    errors.push('Password must contain a number')
  }

  return {
    valid: errors.length === 0,
    errors
  }
}

// src/utils/validation.test.ts
import { describe, it, expect } from 'vitest'
import { validateEmail, validatePassword } from './validation'

describe('validateEmail', () => {
  it('should accept valid emails', () => {
    expect(validateEmail('test@example.com')).toBe(true)
    expect(validateEmail('user.name@domain.co.uk')).toBe(true)
  })

  it('should reject invalid emails', () => {
    expect(validateEmail('invalid')).toBe(false)
    expect(validateEmail('test@')).toBe(false)
    expect(validateEmail('@example.com')).toBe(false)
  })
})

describe('validatePassword', () => {
  it('should accept valid passwords', () => {
    const result = validatePassword('Password123')
    expect(result.valid).toBe(true)
    expect(result.errors).toHaveLength(0)
  })

  it('should reject short passwords', () => {
    const result = validatePassword('Pass1')
    expect(result.valid).toBe(false)
    expect(result.errors).toContain('Password must be at least 8 characters')
  })

  it('should require uppercase letter', () => {
    const result = validatePassword('password123')
    expect(result.valid).toBe(false)
    expect(result.errors).toContain('Password must contain an uppercase letter')
  })

  it('should require number', () => {
    const result = validatePassword('Password')
    expect(result.valid).toBe(false)
    expect(result.errors).toContain('Password must contain a number')
  })
})
```

### API Integration Testing

```typescript
// src/services/userService.ts
export class UserService {
  async getUser(id: number) {
    const response = await fetch(`/api/users/${id}`)
    if (!response.ok) throw new Error('User not found')
    return response.json()
  }

  async createUser(data: { name: string; email: string }) {
    const response = await fetch('/api/users', {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify(data)
    })
    if (!response.ok) throw new Error('Failed to create user')
    return response.json()
  }
}

// src/services/userService.test.ts
import { describe, it, expect, vi, beforeEach } from 'vitest'
import { UserService } from './userService'

describe('UserService', () => {
  let service: UserService

  beforeEach(() => {
    service = new UserService()
    vi.clearAllMocks()
  })

  describe('getUser', () => {
    it('should fetch user successfully', async () => {
      const mockUser = { id: 1, name: 'John', email: 'john@example.com' }
      
      global.fetch = vi.fn().mockResolvedValue({
        ok: true,
        json: () => Promise.resolve(mockUser)
      })

      const user = await service.getUser(1)
      
      expect(user).toEqual(mockUser)
      expect(fetch).toHaveBeenCalledWith('/api/users/1')
    })

    it('should throw error when user not found', async () => {
      global.fetch = vi.fn().mockResolvedValue({
        ok: false
      })

      await expect(service.getUser(999)).rejects.toThrow('User not found')
    })
  })

  describe('createUser', () => {
    it('should create user successfully', async () => {
      const newUser = { name: 'Jane', email: 'jane@example.com' }
      const createdUser = { id: 2, ...newUser }

      global.fetch = vi.fn().mockResolvedValue({
        ok: true,
        json: () => Promise.resolve(createdUser)
      })

      const result = await service.createUser(newUser)

      expect(result).toEqual(createdUser)
      expect(fetch).toHaveBeenCalledWith('/api/users', {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify(newUser)
      })
    })
  })
})
```

## Best Practices

### 1. Organize Tests

```typescript
// ✅ Good: Clear test structure
describe('UserService', () => {
  describe('getUser', () => {
    it('should fetch user successfully', () => {})
    it('should handle errors', () => {})
  })

  describe('createUser', () => {
    it('should create user', () => {})
  })
})

// ❌ Bad: Flat structure
it('test 1', () => {})
it('test 2', () => {})
```

### 2. Use Descriptive Test Names

```typescript
// ✅ Good: Descriptive
it('should return error when email is invalid', () => {})

// ❌ Bad: Vague
it('works', () => {})
it('test email', () => {})
```

### 3. Follow AAA Pattern

```typescript
// ✅ Good: Arrange, Act, Assert
it('should add item to cart', () => {
  // Arrange
  const cart = new ShoppingCart()
  const item = { id: 1, name: 'Product' }

  // Act
  cart.addItem(item)

  // Assert
  expect(cart.items).toContain(item)
})
```

### 4. Mock External Dependencies

```typescript
// ✅ Good: Mock API calls
vi.mock('./api', () => ({
  fetchData: vi.fn().mockResolvedValue({ data: 'test' })
}))

// ❌ Bad: Real API calls in tests
```

### 5. Clean Up After Tests

```typescript
// ✅ Good: Clean up
afterEach(() => {
  vi.clearAllMocks()
  cleanup()
})
```

## Common Pitfalls

### 1. Not Waiting for Async Operations

```typescript
// ❌ Bad: Not waiting
it('should load data', () => {
  render(<DataComponent />)
  expect(screen.getByText('Data')).toBeInTheDocument() // Fails!
})

// ✅ Good: Wait for async
it('should load data', async () => {
  render(<DataComponent />)
  await waitFor(() => {
    expect(screen.getByText('Data')).toBeInTheDocument()
  })
})
```

### 2. Testing Implementation Details

```typescript
// ❌ Bad: Testing state
it('should update state', () => {
  const { result } = renderHook(() => useState(0))
  // Testing internal state
})

// ✅ Good: Testing behavior
it('should increment counter', async () => {
  render(<Counter />)
  await userEvent.click(screen.getByText('Increment'))
  expect(screen.getByText('Count: 1')).toBeInTheDocument()
})
```

### 3. Not Cleaning Up Mocks

```typescript
// ❌ Bad: Mocks persist
vi.mock('./api')
// Affects other tests

// ✅ Good: Clean up
beforeEach(() => {
  vi.clearAllMocks()
})
```

### 4. Overly Complex Tests

```typescript
// ❌ Bad: Testing too much
it('should do everything', () => {
  // 50 lines of test code
})

// ✅ Good: One concept per test
it('should add item', () => {})
it('should remove item', () => {})
it('should update quantity', () => {})
```

## Resources

### Official Documentation
- [Vitest Docs](https://vitest.dev/)
- [Vitest GitHub](https://github.com/vitest-dev/vitest)
- [API Reference](https://vitest.dev/api/)

### Testing Libraries
- [Testing Library](https://testing-library.com/)
- [React Testing Library](https://testing-library.com/react)
- [User Event](https://testing-library.com/docs/user-event/intro)

### Tutorials & Guides
- [Vitest Guide](https://vitest.dev/guide/)
- [Testing Best Practices](https://kentcdodds.com/blog/common-mistakes-with-react-testing-library)

### Video Tutorials
- [Vitest Crash Course](https://www.youtube.com/results?search_query=vitest+crash+course)

### Community
- [Discord](https://chat.vitest.dev/)
- [GitHub Discussions](https://github.com/vitest-dev/vitest/discussions)
- [Stack Overflow](https://stackoverflow.com/questions/tagged/vitest)

---

**Next Steps:**
- Learn [React Testing Library](./react-testing-library.md) for component testing
- Explore [Playwright](./playwright.md) for E2E testing
- Check out [Cypress](./cypress.md) as an alternative E2E tool
