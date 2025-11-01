# React Router - Declarative Routing for React

## Overview

React Router is the standard routing library for React applications. It enables navigation among views in your application, allows changing the browser URL, and keeps the UI in sync with the URL. React Router provides a declarative way to define routes and handle navigation in single-page applications.

**Key Features:**
- Declarative routing
- Nested routes
- Dynamic route matching
- URL parameters and query strings
- Programmatic navigation
- Route guards and protection
- Lazy loading support
- TypeScript support
- Server-side rendering compatible
- Active link styling

## Why Use React Router?

### Problems It Solves

1. **Navigation**: Handle client-side routing without page reloads
2. **URL Management**: Keep UI in sync with browser URL
3. **Code Splitting**: Load route components on demand
4. **Nested Layouts**: Share layouts across multiple routes
5. **Route Protection**: Implement authentication guards
6. **Deep Linking**: Support bookmarkable URLs

### When to Use React Router

- ✅ Multi-page single-page applications (SPAs)
- ✅ Apps with complex navigation
- ✅ When you need URL-based navigation
- ✅ Protected routes (authentication)
- ✅ Nested routing requirements
- ✅ SEO-friendly URLs

### When NOT to Use React Router

- ❌ Simple single-page apps with no navigation
- ❌ Server-side rendered apps (use Next.js instead)
- ❌ Mobile apps (use React Navigation)
- ❌ Very simple apps with 1-2 views

## Comparison with Alternatives

### React Router vs TanStack Router

| Feature | React Router | TanStack Router |
|---------|-------------|-----------------|
| Type Safety | Good | Excellent |
| Bundle Size | ~13KB | ~15KB |
| Learning Curve | Easy | Moderate |
| Search Params | Manual | Type-safe |
| Data Loading | Manual | Built-in |
| Community | Massive | Growing |
| Best For | Most apps | Type-safe apps |

**Choose React Router if:** You want the standard, battle-tested solution
**Choose TanStack Router if:** You need full type safety and built-in data loading

### React Router vs Next.js Router

| Feature | React Router | Next.js Router |
|---------|-------------|----------------|
| Type | Client-side | File-based |
| SSR | Manual | Built-in |
| API Routes | No | Yes |
| Learning Curve | Easy | Easy |
| Flexibility | High | Moderate |
| Best For | SPAs | Full-stack apps |

## Installation & Setup

### Installation

```bash
# npm
npm install react-router-dom

# yarn
yarn add react-router-dom

# pnpm
pnpm add react-router-dom
```

### Basic Setup

```typescript
// main.tsx or index.tsx
import React from 'react'
import ReactDOM from 'react-dom/client'
import { BrowserRouter } from 'react-router-dom'
import App from './App'

ReactDOM.createRoot(document.getElementById('root')!).render(
  <React.StrictMode>
    <BrowserRouter>
      <App />
    </BrowserRouter>
  </React.StrictMode>
)
```

### TypeScript Setup

React Router has built-in TypeScript support. No additional types needed!

## Core Concepts

### 1. Routes and Route

```typescript
import { Routes, Route } from 'react-router-dom'
import Home from './pages/Home'
import About from './pages/About'
import NotFound from './pages/NotFound'

function App() {
  return (
    <Routes>
      <Route path="/" element={<Home />} />
      <Route path="/about" element={<About />} />
      <Route path="*" element={<NotFound />} />
    </Routes>
  )
}
```

### 2. Link and NavLink

```typescript
import { Link, NavLink } from 'react-router-dom'

function Navigation() {
  return (
    <nav>
      {/* Basic link */}
      <Link to="/">Home</Link>
      <Link to="/about">About</Link>

      {/* NavLink with active styling */}
      <NavLink 
        to="/products"
        className={({ isActive }) => isActive ? 'active' : ''}
      >
        Products
      </NavLink>

      {/* NavLink with style function */}
      <NavLink
        to="/contact"
        style={({ isActive }) => ({
          color: isActive ? 'red' : 'black'
        })}
      >
        Contact
      </NavLink>
    </nav>
  )
}
```

### 3. useNavigate Hook

```typescript
import { useNavigate } from 'react-router-dom'

function LoginForm() {
  const navigate = useNavigate()

  const handleSubmit = async (e: React.FormEvent) => {
    e.preventDefault()
    // Login logic
    await login()
    
    // Navigate programmatically
    navigate('/dashboard')
    
    // Navigate with replace (no history entry)
    navigate('/dashboard', { replace: true })
    
    // Navigate back
    navigate(-1)
  }

  return <form onSubmit={handleSubmit}>...</form>
}
```

### 4. URL Parameters

```typescript
import { useParams } from 'react-router-dom'

// Route definition
<Route path="/users/:id" element={<UserProfile />} />

// Component
function UserProfile() {
  const { id } = useParams<{ id: string }>()
  
  return <div>User ID: {id}</div>
}
```

### 5. Query Parameters

```typescript
import { useSearchParams } from 'react-router-dom'

function ProductList() {
  const [searchParams, setSearchParams] = useSearchParams()
  
  const category = searchParams.get('category')
  const sort = searchParams.get('sort')

  const updateFilters = () => {
    setSearchParams({ category: 'electronics', sort: 'price' })
  }

  return (
    <div>
      <p>Category: {category}</p>
      <p>Sort: {sort}</p>
      <button onClick={updateFilters}>Update Filters</button>
    </div>
  )
}
```

## Basic Usage

### Simple Multi-Page App

```typescript
// App.tsx
import { Routes, Route } from 'react-router-dom'
import Layout from './components/Layout'
import Home from './pages/Home'
import About from './pages/About'
import Contact from './pages/Contact'
import NotFound from './pages/NotFound'

function App() {
  return (
    <Routes>
      <Route path="/" element={<Layout />}>
        <Route index element={<Home />} />
        <Route path="about" element={<About />} />
        <Route path="contact" element={<Contact />} />
        <Route path="*" element={<NotFound />} />
      </Route>
    </Routes>
  )
}

export default App
```

```typescript
// components/Layout.tsx
import { Outlet, Link } from 'react-router-dom'

function Layout() {
  return (
    <div>
      <nav>
        <Link to="/">Home</Link>
        <Link to="/about">About</Link>
        <Link to="/contact">Contact</Link>
      </nav>
      
      <main>
        <Outlet /> {/* Child routes render here */}
      </main>
      
      <footer>© 2025 My App</footer>
    </div>
  )
}

export default Layout
```

### Dynamic Routes

```typescript
// App.tsx
<Routes>
  <Route path="/products" element={<Products />} />
  <Route path="/products/:id" element={<ProductDetail />} />
</Routes>

// pages/ProductDetail.tsx
import { useParams, useNavigate } from 'react-router-dom'
import { useEffect, useState } from 'react'

interface Product {
  id: string
  name: string
  price: number
  description: string
}

function ProductDetail() {
  const { id } = useParams<{ id: string }>()
  const navigate = useNavigate()
  const [product, setProduct] = useState<Product | null>(null)
  const [loading, setLoading] = useState(true)

  useEffect(() => {
    async function fetchProduct() {
      try {
        const response = await fetch(`/api/products/${id}`)
        const data = await response.json()
        setProduct(data)
      } catch (error) {
        console.error('Failed to fetch product')
      } finally {
        setLoading(false)
      }
    }

    fetchProduct()
  }, [id])

  if (loading) return <div>Loading...</div>
  if (!product) return <div>Product not found</div>

  return (
    <div>
      <button onClick={() => navigate('/products')}>Back to Products</button>
      <h1>{product.name}</h1>
      <p>${product.price}</p>
      <p>{product.description}</p>
    </div>
  )
}
```

## Advanced Usage

### 1. Nested Routes

```typescript
// App.tsx
<Routes>
  <Route path="/dashboard" element={<DashboardLayout />}>
    <Route index element={<DashboardHome />} />
    <Route path="profile" element={<Profile />} />
    <Route path="settings" element={<Settings />} />
    <Route path="users" element={<Users />}>
      <Route index element={<UserList />} />
      <Route path=":id" element={<UserDetail />} />
    </Route>
  </Route>
</Routes>

// components/DashboardLayout.tsx
import { Outlet, NavLink } from 'react-router-dom'

function DashboardLayout() {
  return (
    <div className="dashboard">
      <aside>
        <nav>
          <NavLink to="/dashboard">Home</NavLink>
          <NavLink to="/dashboard/profile">Profile</NavLink>
          <NavLink to="/dashboard/settings">Settings</NavLink>
          <NavLink to="/dashboard/users">Users</NavLink>
        </nav>
      </aside>
      
      <main>
        <Outlet />
      </main>
    </div>
  )
}
```

### 2. Protected Routes

```typescript
// components/ProtectedRoute.tsx
import { Navigate, Outlet } from 'react-router-dom'
import { useAuth } from '../hooks/useAuth'

interface ProtectedRouteProps {
  redirectPath?: string
}

function ProtectedRoute({ redirectPath = '/login' }: ProtectedRouteProps) {
  const { isAuthenticated } = useAuth()

  if (!isAuthenticated) {
    return <Navigate to={redirectPath} replace />
  }

  return <Outlet />
}

// App.tsx
<Routes>
  <Route path="/login" element={<Login />} />
  
  <Route element={<ProtectedRoute />}>
    <Route path="/dashboard" element={<Dashboard />} />
    <Route path="/profile" element={<Profile />} />
    <Route path="/settings" element={<Settings />} />
  </Route>
</Routes>
```

### 3. Lazy Loading Routes

```typescript
import { lazy, Suspense } from 'react'
import { Routes, Route } from 'react-router-dom'

// Lazy load components
const Home = lazy(() => import('./pages/Home'))
const About = lazy(() => import('./pages/About'))
const Dashboard = lazy(() => import('./pages/Dashboard'))

function App() {
  return (
    <Suspense fallback={<div>Loading...</div>}>
      <Routes>
        <Route path="/" element={<Home />} />
        <Route path="/about" element={<About />} />
        <Route path="/dashboard" element={<Dashboard />} />
      </Routes>
    </Suspense>
  )
}
```

### 4. Route-Based Code Splitting

```typescript
import { lazy, Suspense } from 'react'
import { Routes, Route } from 'react-router-dom'
import LoadingSpinner from './components/LoadingSpinner'

// Lazy load route components
const routes = {
  Home: lazy(() => import('./pages/Home')),
  Products: lazy(() => import('./pages/Products')),
  ProductDetail: lazy(() => import('./pages/ProductDetail')),
  Cart: lazy(() => import('./pages/Cart')),
  Checkout: lazy(() => import('./pages/Checkout')),
}

function App() {
  return (
    <Suspense fallback={<LoadingSpinner />}>
      <Routes>
        <Route path="/" element={<routes.Home />} />
        <Route path="/products" element={<routes.Products />} />
        <Route path="/products/:id" element={<routes.ProductDetail />} />
        <Route path="/cart" element={<routes.Cart />} />
        <Route path="/checkout" element={<routes.Checkout />} />
      </Routes>
    </Suspense>
  )
}
```

### 5. useLocation Hook

```typescript
import { useLocation } from 'react-router-dom'

function Analytics() {
  const location = useLocation()

  useEffect(() => {
    // Track page views
    trackPageView(location.pathname)
  }, [location])

  return null
}

// Get state passed via navigate
function ResultsPage() {
  const location = useLocation()
  const state = location.state as { from: string }

  return <div>Navigated from: {state?.from}</div>
}

// Navigate with state
function SearchPage() {
  const navigate = useNavigate()

  const handleSearch = () => {
    navigate('/results', { state: { from: '/search' } })
  }
}
```

### 6. Custom Route Configuration

```typescript
// routes/config.tsx
import { lazy } from 'react'
import type { RouteObject } from 'react-router-dom'

const Home = lazy(() => import('../pages/Home'))
const About = lazy(() => import('../pages/About'))
const Products = lazy(() => import('../pages/Products'))

export const routes: RouteObject[] = [
  {
    path: '/',
    element: <Home />,
  },
  {
    path: '/about',
    element: <About />,
  },
  {
    path: '/products',
    element: <Products />,
    children: [
      {
        index: true,
        element: <ProductList />,
      },
      {
        path: ':id',
        element: <ProductDetail />,
      },
    ],
  },
]

// App.tsx
import { useRoutes } from 'react-router-dom'
import { routes } from './routes/config'

function App() {
  const element = useRoutes(routes)
  return <Suspense fallback={<Loading />}>{element}</Suspense>
}
```

## Real-World Examples

### E-commerce App Routing

```typescript
// App.tsx
import { Routes, Route } from 'react-router-dom'
import { lazy, Suspense } from 'react'
import Layout from './components/Layout'
import ProtectedRoute from './components/ProtectedRoute'
import LoadingSpinner from './components/LoadingSpinner'

// Lazy load pages
const Home = lazy(() => import('./pages/Home'))
const Products = lazy(() => import('./pages/Products'))
const ProductDetail = lazy(() => import('./pages/ProductDetail'))
const Cart = lazy(() => import('./pages/Cart'))
const Checkout = lazy(() => import('./pages/Checkout'))
const Login = lazy(() => import('./pages/Login'))
const Register = lazy(() => import('./pages/Register'))
const Profile = lazy(() => import('./pages/Profile'))
const Orders = lazy(() => import('./pages/Orders'))
const OrderDetail = lazy(() => import('./pages/OrderDetail'))
const NotFound = lazy(() => import('./pages/NotFound'))

function App() {
  return (
    <Suspense fallback={<LoadingSpinner />}>
      <Routes>
        {/* Public routes */}
        <Route path="/" element={<Layout />}>
          <Route index element={<Home />} />
          <Route path="products">
            <Route index element={<Products />} />
            <Route path=":id" element={<ProductDetail />} />
          </Route>
          <Route path="cart" element={<Cart />} />
          <Route path="login" element={<Login />} />
          <Route path="register" element={<Register />} />

          {/* Protected routes */}
          <Route element={<ProtectedRoute />}>
            <Route path="checkout" element={<Checkout />} />
            <Route path="profile" element={<Profile />} />
            <Route path="orders">
              <Route index element={<Orders />} />
              <Route path=":id" element={<OrderDetail />} />
            </Route>
          </Route>

          {/* 404 */}
          <Route path="*" element={<NotFound />} />
        </Route>
      </Routes>
    </Suspense>
  )
}

export default App
```

### Search and Filter with URL Params

```typescript
import { useSearchParams, useNavigate } from 'react-router-dom'
import { useEffect, useState } from 'react'

interface Product {
  id: string
  name: string
  category: string
  price: number
}

function ProductList() {
  const [searchParams, setSearchParams] = useSearchParams()
  const navigate = useNavigate()
  
  const [products, setProducts] = useState<Product[]>([])
  const [loading, setLoading] = useState(true)

  // Get params from URL
  const category = searchParams.get('category') || 'all'
  const sort = searchParams.get('sort') || 'name'
  const search = searchParams.get('search') || ''
  const page = parseInt(searchParams.get('page') || '1')

  useEffect(() => {
    async function fetchProducts() {
      setLoading(true)
      try {
        const params = new URLSearchParams({
          category,
          sort,
          search,
          page: page.toString(),
        })
        
        const response = await fetch(`/api/products?${params}`)
        const data = await response.json()
        setProducts(data.products)
      } catch (error) {
        console.error('Failed to fetch products')
      } finally {
        setLoading(false)
      }
    }

    fetchProducts()
  }, [category, sort, search, page])

  const updateFilter = (key: string, value: string) => {
    const newParams = new URLSearchParams(searchParams)
    newParams.set(key, value)
    newParams.set('page', '1') // Reset to first page
    setSearchParams(newParams)
  }

  const handleSearch = (value: string) => {
    updateFilter('search', value)
  }

  const handleCategoryChange = (value: string) => {
    updateFilter('category', value)
  }

  const handleSortChange = (value: string) => {
    updateFilter('sort', value)
  }

  const handlePageChange = (newPage: number) => {
    updateFilter('page', newPage.toString())
  }

  if (loading) return <div>Loading...</div>

  return (
    <div>
      <div className="filters">
        <input
          type="text"
          placeholder="Search..."
          value={search}
          onChange={(e) => handleSearch(e.target.value)}
        />

        <select value={category} onChange={(e) => handleCategoryChange(e.target.value)}>
          <option value="all">All Categories</option>
          <option value="electronics">Electronics</option>
          <option value="clothing">Clothing</option>
          <option value="books">Books</option>
        </select>

        <select value={sort} onChange={(e) => handleSortChange(e.target.value)}>
          <option value="name">Name</option>
          <option value="price-asc">Price: Low to High</option>
          <option value="price-desc">Price: High to Low</option>
        </select>
      </div>

      <div className="products">
        {products.map((product) => (
          <div key={product.id} onClick={() => navigate(`/products/${product.id}`)}>
            <h3>{product.name}</h3>
            <p>${product.price}</p>
          </div>
        ))}
      </div>

      <div className="pagination">
        <button 
          onClick={() => handlePageChange(page - 1)}
          disabled={page === 1}
        >
          Previous
        </button>
        <span>Page {page}</span>
        <button onClick={() => handlePageChange(page + 1)}>
          Next
        </button>
      </div>
    </div>
  )
}
```

## Best Practices

### 1. Use Index Routes

```typescript
// ✅ Good: Use index route for default child
<Route path="dashboard" element={<Dashboard />}>
  <Route index element={<DashboardHome />} />
  <Route path="settings" element={<Settings />} />
</Route>

// ❌ Bad: Empty path
<Route path="dashboard" element={<Dashboard />}>
  <Route path="" element={<DashboardHome />} />
</Route>
```

### 2. Organize Routes

```typescript
// ✅ Good: Separate route configuration
// routes/index.tsx
export const routes = [
  {
    path: '/',
    element: <Layout />,
    children: [
      { index: true, element: <Home /> },
      { path: 'about', element: <About /> },
    ],
  },
]

// ❌ Bad: All routes in App.tsx
```

### 3. Use Relative Paths in Nested Routes

```typescript
// ✅ Good: Relative paths
<Route path="dashboard" element={<Dashboard />}>
  <Route path="settings" element={<Settings />} />
  <Route path="profile" element={<Profile />} />
</Route>

// ❌ Bad: Absolute paths
<Route path="dashboard" element={<Dashboard />}>
  <Route path="/dashboard/settings" element={<Settings />} />
</Route>
```

### 4. Handle 404s

```typescript
// ✅ Good: Catch-all route
<Routes>
  <Route path="/" element={<Home />} />
  <Route path="/about" element={<About />} />
  <Route path="*" element={<NotFound />} />
</Routes>
```

### 5. Use NavLink for Active States

```typescript
// ✅ Good: NavLink with active styling
<NavLink 
  to="/dashboard"
  className={({ isActive }) => isActive ? 'active' : ''}
>
  Dashboard
</NavLink>

// ❌ Bad: Manual active state management
```

## Common Pitfalls

### 1. Not Using Outlet

```typescript
// ❌ Bad: No Outlet in parent
function Dashboard() {
  return <div>Dashboard</div>
}

// ✅ Good: Include Outlet
function Dashboard() {
  return (
    <div>
      <h1>Dashboard</h1>
      <Outlet />
    </div>
  )
}
```

### 2. Forgetting Replace on Redirects

```typescript
// ❌ Bad: Creates history entry
navigate('/login')

// ✅ Good: Replace for redirects
navigate('/login', { replace: true })
```

### 3. Not Handling Loading States

```typescript
// ❌ Bad: No loading fallback
const Products = lazy(() => import('./Products'))

<Routes>
  <Route path="/products" element={<Products />} />
</Routes>

// ✅ Good: Suspense with fallback
<Suspense fallback={<Loading />}>
  <Routes>
    <Route path="/products" element={<Products />} />
  </Routes>
</Suspense>
```

### 4. Incorrect Path Matching

```typescript
// ❌ Bad: Will never match
<Route path="/products/:id" element={<ProductDetail />} />
<Route path="/products/new" element={<NewProduct />} />

// ✅ Good: Specific routes first
<Route path="/products/new" element={<NewProduct />} />
<Route path="/products/:id" element={<ProductDetail />} />
```

## Resources

### Official Documentation
- [React Router Docs](https://reactrouter.com/)
- [React Router GitHub](https://github.com/remix-run/react-router)
- [React Router Tutorial](https://reactrouter.com/en/main/start/tutorial)

### Tutorials & Guides
- [React Router v6 Guide](https://reactrouter.com/en/main/start/overview)
- [Migration Guide](https://reactrouter.com/en/main/upgrading/v5)

### Video Tutorials
- [React Router Crash Course](https://www.youtube.com/results?search_query=react+router+v6+crash+course)
- [React Router Deep Dive](https://egghead.io/courses/up-and-running-with-react-router)

### Community
- [GitHub Discussions](https://github.com/remix-run/react-router/discussions)
- [Discord](https://rmx.as/discord)
- [Stack Overflow](https://stackoverflow.com/questions/tagged/react-router)

### Tools
- [React Router DevTools](https://github.com/remix-run/react-router-devtools)

---

**Next Steps:**
- Explore [TanStack Router](./tanstack-router.md) for type-safe routing
- Learn [Next.js](../16-meta-frameworks/nextjs.md) for file-based routing
- Check out [React Query](../02-data-fetching/tanstack-query.md) for data fetching
