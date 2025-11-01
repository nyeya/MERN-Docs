# Axios - Promise-Based HTTP Client

## Overview

Axios is a promise-based HTTP client for the browser and Node.js. It provides a simple and elegant API for making HTTP requests with features like request/response interceptors, automatic JSON transformation, request cancellation, and more. Axios has become the de facto standard for HTTP requests in React applications due to its reliability, extensive features, and excellent developer experience.

**Key Features:**
- Promise-based API
- Request and response interceptors
- Automatic JSON transformation
- Request cancellation
- Timeout support
- CSRF protection
- Progress tracking
- Works in browser and Node.js
- TypeScript support
- Wide browser support

## Why Use Axios?

### Problems It Solves

1. **Simplified API**: Cleaner syntax than fetch
2. **Interceptors**: Global request/response handling
3. **Error Handling**: Better error handling than fetch
4. **Cancellation**: Cancel requests easily
5. **Timeouts**: Built-in timeout support
6. **Transformations**: Automatic data transformation

### When to Use Axios

- ✅ Making API requests
- ✅ Need request/response interceptors
- ✅ File uploads with progress
- ✅ Request cancellation
- ✅ Automatic retries
- ✅ Global error handling
- ✅ Authentication headers

### When NOT to Use Axios

- ❌ Simple GET requests (use fetch)
- ❌ Bundle size is critical
- ❌ Native fetch is sufficient
- ❌ GraphQL only (use Apollo)

## Comparison with Alternatives

### Axios vs Fetch

| Feature | Axios | Fetch |
|---------|-------|-------|
| Browser Support | Older browsers | Modern browsers |
| JSON Parsing | Automatic | Manual |
| Interceptors | Built-in | Manual |
| Timeout | Built-in | Manual |
| Progress | Built-in | Manual |
| Cancellation | Built-in | AbortController |
| Error Handling | Better | Basic |
| Bundle Size | ~13KB | 0KB (native) |

**Choose Axios if:** You need interceptors, timeouts, or better DX
**Choose Fetch if:** You want zero dependencies

### Axios vs TanStack Query

Note: These are complementary! TanStack Query handles caching/state, Axios handles HTTP.

| Tool | Purpose |
|------|---------|
| Axios | HTTP client |
| TanStack Query | Data fetching & caching |
| Typical Usage | Use both together |

## Installation & Setup

### Installation

```bash
# npm
npm install axios

# yarn
yarn add axios

# pnpm
pnpm add axios
```

### Basic Setup

```typescript
import axios from 'axios'

// Simple GET request
const response = await axios.get('/api/users')
console.log(response.data)

// POST request
const newUser = await axios.post('/api/users', {
  name: 'John',
  email: 'john@example.com'
})
```

### Create Instance

```typescript
// src/api/axios.ts
import axios from 'axios'

export const api = axios.create({
  baseURL: 'https://api.example.com',
  timeout: 10000,
  headers: {
    'Content-Type': 'application/json'
  }
})
```

## Core Concepts

### 1. HTTP Methods

```typescript
// GET
const users = await axios.get('/users')
const user = await axios.get('/users/1')

// POST
const newUser = await axios.post('/users', {
  name: 'John',
  email: 'john@example.com'
})

// PUT
const updatedUser = await axios.put('/users/1', {
  name: 'John Doe'
})

// PATCH
const patchedUser = await axios.patch('/users/1', {
  email: 'newemail@example.com'
})

// DELETE
await axios.delete('/users/1')
```

### 2. Request Configuration

```typescript
const response = await axios({
  method: 'post',
  url: '/users',
  data: {
    name: 'John'
  },
  headers: {
    'Authorization': 'Bearer token'
  },
  timeout: 5000,
  params: {
    page: 1,
    limit: 10
  }
})
```

### 3. Response Structure

```typescript
const response = await axios.get('/users')

console.log(response.data)      // Response body
console.log(response.status)    // HTTP status code
console.log(response.statusText) // HTTP status message
console.log(response.headers)   // Response headers
console.log(response.config)    // Request config
```

### 4. Error Handling

```typescript
try {
  const response = await axios.get('/users')
  console.log(response.data)
} catch (error) {
  if (axios.isAxiosError(error)) {
    if (error.response) {
      // Server responded with error status
      console.log(error.response.data)
      console.log(error.response.status)
    } else if (error.request) {
      // Request made but no response
      console.log('No response received')
    } else {
      // Error setting up request
      console.log('Error:', error.message)
    }
  }
}
```

## Basic Usage

### Simple API Calls

```typescript
import axios from 'axios'

interface User {
  id: number
  name: string
  email: string
}

// GET request
async function getUsers(): Promise<User[]> {
  const response = await axios.get<User[]>('/api/users')
  return response.data
}

// POST request
async function createUser(user: Omit<User, 'id'>): Promise<User> {
  const response = await axios.post<User>('/api/users', user)
  return response.data
}

// PUT request
async function updateUser(id: number, user: Partial<User>): Promise<User> {
  const response = await axios.put<User>(`/api/users/${id}`, user)
  return response.data
}

// DELETE request
async function deleteUser(id: number): Promise<void> {
  await axios.delete(`/api/users/${id}`)
}
```

### With React Component

```typescript
import { useState, useEffect } from 'react'
import axios from 'axios'

interface User {
  id: number
  name: string
  email: string
}

function UserList() {
  const [users, setUsers] = useState<User[]>([])
  const [loading, setLoading] = useState(true)
  const [error, setError] = useState<string | null>(null)

  useEffect(() => {
    async function fetchUsers() {
      try {
        const response = await axios.get<User[]>('/api/users')
        setUsers(response.data)
      } catch (err) {
        setError('Failed to fetch users')
      } finally {
        setLoading(false)
      }
    }

    fetchUsers()
  }, [])

  if (loading) return <div>Loading...</div>
  if (error) return <div>Error: {error}</div>

  return (
    <ul>
      {users.map(user => (
        <li key={user.id}>{user.name}</li>
      ))}
    </ul>
  )
}
```

## Advanced Usage

### 1. Interceptors

```typescript
import axios from 'axios'

const api = axios.create({
  baseURL: 'https://api.example.com'
})

// Request interceptor
api.interceptors.request.use(
  (config) => {
    // Add auth token
    const token = localStorage.getItem('token')
    if (token) {
      config.headers.Authorization = `Bearer ${token}`
    }
    
    // Log request
    console.log('Request:', config.method?.toUpperCase(), config.url)
    
    return config
  },
  (error) => {
    return Promise.reject(error)
  }
)

// Response interceptor
api.interceptors.response.use(
  (response) => {
    // Log response
    console.log('Response:', response.status, response.config.url)
    return response
  },
  (error) => {
    // Handle errors globally
    if (error.response?.status === 401) {
      // Redirect to login
      window.location.href = '/login'
    }
    
    if (error.response?.status === 500) {
      console.error('Server error')
    }
    
    return Promise.reject(error)
  }
)

export default api
```

### 2. Request Cancellation

```typescript
import axios from 'axios'
import { useEffect, useState } from 'react'

function SearchComponent() {
  const [query, setQuery] = useState('')
  const [results, setResults] = useState([])

  useEffect(() => {
    const controller = new AbortController()

    async function search() {
      if (!query) return

      try {
        const response = await axios.get('/api/search', {
          params: { q: query },
          signal: controller.signal
        })
        setResults(response.data)
      } catch (error) {
        if (axios.isCancel(error)) {
          console.log('Request cancelled')
        }
      }
    }

    search()

    return () => {
      controller.abort()
    }
  }, [query])

  return (
    <div>
      <input
        value={query}
        onChange={(e) => setQuery(e.target.value)}
        placeholder="Search..."
      />
      {/* Display results */}
    </div>
  )
}
```

### 3. File Upload with Progress

```typescript
import axios from 'axios'
import { useState } from 'react'

function FileUpload() {
  const [progress, setProgress] = useState(0)

  const handleUpload = async (file: File) => {
    const formData = new FormData()
    formData.append('file', file)

    try {
      const response = await axios.post('/api/upload', formData, {
        headers: {
          'Content-Type': 'multipart/form-data'
        },
        onUploadProgress: (progressEvent) => {
          const percentCompleted = Math.round(
            (progressEvent.loaded * 100) / (progressEvent.total || 1)
          )
          setProgress(percentCompleted)
        }
      })

      console.log('Upload complete:', response.data)
    } catch (error) {
      console.error('Upload failed:', error)
    }
  }

  return (
    <div>
      <input
        type="file"
        onChange={(e) => {
          const file = e.target.files?.[0]
          if (file) handleUpload(file)
        }}
      />
      {progress > 0 && <div>Progress: {progress}%</div>}
    </div>
  )
}
```

### 4. Retry Logic

```typescript
import axios, { AxiosError, AxiosRequestConfig } from 'axios'

async function axiosRetry<T>(
  config: AxiosRequestConfig,
  maxRetries = 3,
  delay = 1000
): Promise<T> {
  let lastError: AxiosError

  for (let i = 0; i < maxRetries; i++) {
    try {
      const response = await axios(config)
      return response.data
    } catch (error) {
      lastError = error as AxiosError
      
      // Don't retry on client errors (4xx)
      if (error.response?.status && error.response.status < 500) {
        throw error
      }

      if (i < maxRetries - 1) {
        await new Promise(resolve => setTimeout(resolve, delay * (i + 1)))
      }
    }
  }

  throw lastError!
}

// Usage
const data = await axiosRetry({ url: '/api/users', method: 'GET' })
```

### 5. Concurrent Requests

```typescript
import axios from 'axios'

async function fetchMultipleResources() {
  try {
    const [users, posts, comments] = await Promise.all([
      axios.get('/api/users'),
      axios.get('/api/posts'),
      axios.get('/api/comments')
    ])

    return {
      users: users.data,
      posts: posts.data,
      comments: comments.data
    }
  } catch (error) {
    console.error('Failed to fetch resources:', error)
    throw error
  }
}
```

### 6. Custom Instance with TypeScript

```typescript
import axios, { AxiosInstance, AxiosRequestConfig } from 'axios'

class ApiClient {
  private client: AxiosInstance

  constructor(baseURL: string) {
    this.client = axios.create({
      baseURL,
      timeout: 10000,
      headers: {
        'Content-Type': 'application/json'
      }
    })

    this.setupInterceptors()
  }

  private setupInterceptors() {
    this.client.interceptors.request.use(
      (config) => {
        const token = localStorage.getItem('token')
        if (token) {
          config.headers.Authorization = `Bearer ${token}`
        }
        return config
      }
    )

    this.client.interceptors.response.use(
      (response) => response,
      (error) => {
        if (error.response?.status === 401) {
          // Handle unauthorized
          localStorage.removeItem('token')
          window.location.href = '/login'
        }
        return Promise.reject(error)
      }
    )
  }

  async get<T>(url: string, config?: AxiosRequestConfig): Promise<T> {
    const response = await this.client.get<T>(url, config)
    return response.data
  }

  async post<T>(url: string, data?: any, config?: AxiosRequestConfig): Promise<T> {
    const response = await this.client.post<T>(url, data, config)
    return response.data
  }

  async put<T>(url: string, data?: any, config?: AxiosRequestConfig): Promise<T> {
    const response = await this.client.put<T>(url, data, config)
    return response.data
  }

  async delete<T>(url: string, config?: AxiosRequestConfig): Promise<T> {
    const response = await this.client.delete<T>(url, config)
    return response.data
  }
}

export const api = new ApiClient('https://api.example.com')
```

## Real-World Examples

### Complete API Service

```typescript
// src/services/api.ts
import axios, { AxiosError } from 'axios'

const api = axios.create({
  baseURL: import.meta.env.VITE_API_URL || 'http://localhost:3000/api',
  timeout: 10000,
  headers: {
    'Content-Type': 'application/json'
  }
})

// Request interceptor
api.interceptors.request.use(
  (config) => {
    const token = localStorage.getItem('authToken')
    if (token) {
      config.headers.Authorization = `Bearer ${token}`
    }
    return config
  },
  (error) => Promise.reject(error)
)

// Response interceptor
api.interceptors.response.use(
  (response) => response,
  async (error: AxiosError) => {
    const originalRequest = error.config

    // Handle token refresh
    if (error.response?.status === 401 && originalRequest) {
      try {
        const refreshToken = localStorage.getItem('refreshToken')
        const response = await axios.post('/api/auth/refresh', { refreshToken })
        
        const { token } = response.data
        localStorage.setItem('authToken', token)
        
        originalRequest.headers.Authorization = `Bearer ${token}`
        return api(originalRequest)
      } catch (refreshError) {
        localStorage.removeItem('authToken')
        localStorage.removeItem('refreshToken')
        window.location.href = '/login'
        return Promise.reject(refreshError)
      }
    }

    return Promise.reject(error)
  }
)

// API methods
export const userApi = {
  getAll: () => api.get('/users'),
  getById: (id: number) => api.get(`/users/${id}`),
  create: (data: any) => api.post('/users', data),
  update: (id: number, data: any) => api.put(`/users/${id}`, data),
  delete: (id: number) => api.delete(`/users/${id}`)
}

export const authApi = {
  login: (email: string, password: string) =>
    api.post('/auth/login', { email, password }),
  logout: () => api.post('/auth/logout'),
  register: (data: any) => api.post('/auth/register', data)
}

export default api
```

### React Hook for API Calls

```typescript
import { useState, useEffect } from 'react'
import axios, { AxiosRequestConfig } from 'axios'

interface UseApiOptions<T> extends AxiosRequestConfig {
  initialData?: T
  skip?: boolean
}

interface UseApiReturn<T> {
  data: T | null
  loading: boolean
  error: string | null
  refetch: () => void
}

function useApi<T>(
  url: string,
  options: UseApiOptions<T> = {}
): UseApiReturn<T> {
  const { initialData = null, skip = false, ...axiosConfig } = options
  
  const [data, setData] = useState<T | null>(initialData)
  const [loading, setLoading] = useState(!skip)
  const [error, setError] = useState<string | null>(null)
  const [refetchIndex, setRefetchIndex] = useState(0)

  useEffect(() => {
    if (skip) return

    const controller = new AbortController()

    async function fetchData() {
      setLoading(true)
      setError(null)

      try {
        const response = await axios({
          url,
          signal: controller.signal,
          ...axiosConfig
        })
        setData(response.data)
      } catch (err) {
        if (!axios.isCancel(err)) {
          setError(err instanceof Error ? err.message : 'An error occurred')
        }
      } finally {
        setLoading(false)
      }
    }

    fetchData()

    return () => {
      controller.abort()
    }
  }, [url, skip, refetchIndex])

  const refetch = () => setRefetchIndex(prev => prev + 1)

  return { data, loading, error, refetch }
}

// Usage
function UserProfile({ userId }: { userId: number }) {
  const { data: user, loading, error, refetch } = useApi<User>(
    `/api/users/${userId}`
  )

  if (loading) return <div>Loading...</div>
  if (error) return <div>Error: {error}</div>
  if (!user) return null

  return (
    <div>
      <h1>{user.name}</h1>
      <p>{user.email}</p>
      <button onClick={refetch}>Refresh</button>
    </div>
  )
}
```

## Best Practices

### 1. Create Axios Instance

```typescript
// ✅ Good: Centralized configuration
const api = axios.create({
  baseURL: 'https://api.example.com',
  timeout: 10000
})

// ❌ Bad: Repeated configuration
axios.get('https://api.example.com/users', { timeout: 10000 })
```

### 2. Use Interceptors for Common Logic

```typescript
// ✅ Good: Interceptor for auth
api.interceptors.request.use(config => {
  config.headers.Authorization = `Bearer ${getToken()}`
  return config
})

// ❌ Bad: Manual auth on every request
axios.get('/users', {
  headers: { Authorization: `Bearer ${getToken()}` }
})
```

### 3. Type Your Responses

```typescript
// ✅ Good: Typed response
const response = await axios.get<User[]>('/users')
const users: User[] = response.data

// ❌ Bad: Untyped
const response = await axios.get('/users')
const users = response.data // any
```

### 4. Handle Errors Properly

```typescript
// ✅ Good: Specific error handling
try {
  const response = await axios.get('/users')
} catch (error) {
  if (axios.isAxiosError(error)) {
    if (error.response) {
      // Handle server error
    } else if (error.request) {
      // Handle network error
    }
  }
}

// ❌ Bad: Generic catch
try {
  const response = await axios.get('/users')
} catch (error) {
  console.log('Error')
}
```

### 5. Cancel Requests on Unmount

```typescript
// ✅ Good: Cancel on unmount
useEffect(() => {
  const controller = new AbortController()
  
  axios.get('/users', { signal: controller.signal })
  
  return () => controller.abort()
}, [])

// ❌ Bad: No cleanup
useEffect(() => {
  axios.get('/users')
}, [])
```

## Common Pitfalls

### 1. Not Handling Errors

```typescript
// ❌ Bad: No error handling
const users = await axios.get('/users')

// ✅ Good: Proper error handling
try {
  const users = await axios.get('/users')
} catch (error) {
  handleError(error)
}
```

### 2. Forgetting to Cancel Requests

```typescript
// ❌ Bad: Memory leak
useEffect(() => {
  axios.get('/users').then(setUsers)
}, [])

// ✅ Good: Cleanup
useEffect(() => {
  const controller = new AbortController()
  axios.get('/users', { signal: controller.signal }).then(setUsers)
  return () => controller.abort()
}, [])
```

### 3. Not Using Base URL

```typescript
// ❌ Bad: Repeated URLs
axios.get('https://api.example.com/users')
axios.get('https://api.example.com/posts')

// ✅ Good: Base URL
const api = axios.create({ baseURL: 'https://api.example.com' })
api.get('/users')
api.get('/posts')
```

### 4. Ignoring Response Status

```typescript
// ❌ Bad: Assuming success
const response = await axios.get('/users')
console.log(response.data)

// ✅ Good: Check status
const response = await axios.get('/users')
if (response.status === 200) {
  console.log(response.data)
}
```

## Resources

### Official Documentation
- [Axios Docs](https://axios-http.com/)
- [Axios GitHub](https://github.com/axios/axios)
- [API Reference](https://axios-http.com/docs/api_intro)

### Tutorials
- [Axios Guide](https://axios-http.com/docs/intro)
- [Request Config](https://axios-http.com/docs/req_config)
- [Response Schema](https://axios-http.com/docs/res_schema)

### Community
- [GitHub Issues](https://github.com/axios/axios/issues)
- [Stack Overflow](https://stackoverflow.com/questions/tagged/axios)

### Alternatives
- [Fetch API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API)
- [TanStack Query](../02-data-fetching/tanstack-query.md)

---

**Next Steps:**
- Learn [TanStack Query](../02-data-fetching/tanstack-query.md) for data fetching & caching
- Explore [Zod](../06-forms/zod.md) for response validation
- Check out [TypeScript](../10-code-quality/typescript.md) for type safety
