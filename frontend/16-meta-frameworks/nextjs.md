# Next.js - The React Framework for Production

## Overview

Next.js is a full-stack React framework that enables you to build production-ready applications with features like server-side rendering (SSR), static site generation (SSG), API routes, and more. Created by Vercel, it has become the most popular way to build React applications, offering excellent developer experience and performance out of the box.

**Key Features:**
- Server-side rendering (SSR)
- Static site generation (SSG)
- App Router with React Server Components
- File-based routing
- API routes
- Built-in image optimization
- Automatic code splitting
- TypeScript support
- Fast Refresh (HMR)
- Edge runtime support

## Why Use Next.js?

### Problems It Solves

1. **SEO**: Server-side rendering for better search engine optimization
2. **Performance**: Automatic code splitting and optimization
3. **Routing**: File-based routing system
4. **Full-Stack**: API routes for backend functionality
5. **Image Optimization**: Automatic image optimization
6. **Developer Experience**: Fast Refresh, TypeScript, ESLint built-in

### When to Use Next.js

- ✅ SEO-critical applications
- ✅ E-commerce websites
- ✅ Marketing websites
- ✅ Blogs and content sites
- ✅ Full-stack applications
- ✅ When you need SSR or SSG

### When NOT to Use Next.js

- ❌ Simple SPAs without SEO needs
- ❌ Mobile apps (use React Native)
- ❌ When you need full control over server
- ❌ Highly interactive dashboards (SPA might be better)

## Comparison with Alternatives

### Next.js vs Create React App

| Feature | Next.js | CRA |
|---------|---------|-----|
| Rendering | SSR/SSG/CSR | CSR only |
| Routing | File-based | Manual |
| API Routes | Yes | No |
| SEO | Excellent | Poor |
| Image Optimization | Built-in | Manual |
| Best For | Production apps | Simple SPAs |

### Next.js vs Remix

| Feature | Next.js | Remix |
|---------|---------|-------|
| Maturity | Very mature | Growing |
| Hosting | Vercel-optimized | Platform agnostic |
| Data Loading | Server Components | Loaders |
| Community | Huge | Growing |
| Best For | Most apps | Web standards fans |

### Next.js vs Gatsby

| Feature | Next.js | Gatsby |
|---------|---------|--------|
| Rendering | SSR/SSG | SSG only |
| Build Time | Faster | Slower |
| Data Sources | Flexible | GraphQL-focused |
| Use Case | Dynamic sites | Static sites |

## Installation & Setup

### Create New Project

```bash
# npm
npx create-next-app@latest my-app

# yarn
yarn create next-app my-app

# pnpm
pnpm create next-app my-app
```

### Setup Options

```
✔ Would you like to use TypeScript? Yes
✔ Would you like to use ESLint? Yes
✔ Would you like to use Tailwind CSS? Yes
✔ Would you like to use `src/` directory? Yes
✔ Would you like to use App Router? Yes
✔ Would you like to customize the default import alias? No
```

### Project Structure (App Router)

```
my-app/
├── src/
│   ├── app/
│   │   ├── layout.tsx
│   │   ├── page.tsx
│   │   ├── globals.css
│   │   └── api/
│   │       └── route.ts
│   ├── components/
│   └── lib/
├── public/
├── next.config.js
├── package.json
└── tsconfig.json
```

## Core Concepts

### 1. App Router (New)

File-based routing with React Server Components:

```typescript
// app/page.tsx - Home page
export default function Home() {
  return <h1>Home Page</h1>
}

// app/about/page.tsx - About page
export default function About() {
  return <h1>About Page</h1>
}

// app/blog/[slug]/page.tsx - Dynamic route
export default function BlogPost({ params }: { params: { slug: string } }) {
  return <h1>Post: {params.slug}</h1>
}
```

### 2. Server Components vs Client Components

```typescript
// Server Component (default)
export default async function ServerComponent() {
  const data = await fetch('https://api.example.com/data')
  return <div>{data}</div>
}

// Client Component (use 'use client')
'use client'
import { useState } from 'react'

export default function ClientComponent() {
  const [count, setCount] = useState(0)
  return <button onClick={() => setCount(count + 1)}>{count}</button>
}
```

### 3. Layouts

```typescript
// app/layout.tsx - Root layout
export default function RootLayout({ children }: { children: React.ReactNode }) {
  return (
    <html lang="en">
      <body>
        <nav>Navigation</nav>
        {children}
        <footer>Footer</footer>
      </body>
    </html>
  )
}

// app/dashboard/layout.tsx - Nested layout
export default function DashboardLayout({ children }: { children: React.ReactNode }) {
  return (
    <div>
      <aside>Sidebar</aside>
      <main>{children}</main>
    </div>
  )
}
```

### 4. Data Fetching

```typescript
// Server Component - Fetch at build time
export default async function Page() {
  const res = await fetch('https://api.example.com/data', {
    cache: 'force-cache' // SSG
  })
  const data = await res.json()
  return <div>{data.title}</div>
}

// Revalidate every 60 seconds
export default async function Page() {
  const res = await fetch('https://api.example.com/data', {
    next: { revalidate: 60 } // ISR
  })
  const data = await res.json()
  return <div>{data.title}</div>
}

// No caching - SSR
export default async function Page() {
  const res = await fetch('https://api.example.com/data', {
    cache: 'no-store' // SSR
  })
  const data = await res.json()
  return <div>{data.title}</div>
}
```

## Basic Usage

### Simple Page

```typescript
// app/page.tsx
export default function Home() {
  return (
    <main className="flex min-h-screen flex-col items-center justify-center p-24">
      <h1 className="text-4xl font-bold">Welcome to Next.js</h1>
      <p className="mt-4">The React Framework for Production</p>
    </main>
  )
}
```

### Navigation

```typescript
// components/Navigation.tsx
import Link from 'next/link'

export function Navigation() {
  return (
    <nav className="flex gap-4 p-4">
      <Link href="/">Home</Link>
      <Link href="/about">About</Link>
      <Link href="/blog">Blog</Link>
    </nav>
  )
}
```

### Dynamic Routes

```typescript
// app/blog/[slug]/page.tsx
interface PageProps {
  params: { slug: string }
}

export default function BlogPost({ params }: PageProps) {
  return (
    <article>
      <h1>Blog Post: {params.slug}</h1>
    </article>
  )
}

// Generate static paths
export async function generateStaticParams() {
  const posts = await fetch('https://api.example.com/posts').then(res => res.json())
  
  return posts.map((post: any) => ({
    slug: post.slug,
  }))
}
```

### API Routes

```typescript
// app/api/users/route.ts
import { NextResponse } from 'next/server'

export async function GET() {
  const users = [
    { id: 1, name: 'John' },
    { id: 2, name: 'Jane' },
  ]
  
  return NextResponse.json(users)
}

export async function POST(request: Request) {
  const body = await request.json()
  // Save to database
  return NextResponse.json({ success: true, data: body })
}
```

## Advanced Usage

### 1. Server Actions

```typescript
// app/actions.ts
'use server'

export async function createUser(formData: FormData) {
  const name = formData.get('name')
  const email = formData.get('email')
  
  // Save to database
  await db.users.create({ name, email })
  
  return { success: true }
}

// app/page.tsx
import { createUser } from './actions'

export default function Page() {
  return (
    <form action={createUser}>
      <input name="name" required />
      <input name="email" type="email" required />
      <button type="submit">Create User</button>
    </form>
  )
}
```

### 2. Metadata for SEO

```typescript
// app/page.tsx
import { Metadata } from 'next'

export const metadata: Metadata = {
  title: 'Home | My App',
  description: 'Welcome to my app',
  openGraph: {
    title: 'Home | My App',
    description: 'Welcome to my app',
    images: ['/og-image.jpg'],
  },
}

export default function Page() {
  return <h1>Home</h1>
}

// Dynamic metadata
export async function generateMetadata({ params }: { params: { slug: string } }): Promise<Metadata> {
  const post = await fetch(`https://api.example.com/posts/${params.slug}`).then(res => res.json())
  
  return {
    title: post.title,
    description: post.excerpt,
  }
}
```

### 3. Image Optimization

```typescript
import Image from 'next/image'

export default function Page() {
  return (
    <div>
      {/* Optimized image */}
      <Image
        src="/hero.jpg"
        alt="Hero"
        width={1200}
        height={600}
        priority
      />
      
      {/* External image */}
      <Image
        src="https://example.com/image.jpg"
        alt="External"
        width={800}
        height={400}
      />
    </div>
  )
}

// next.config.js
module.exports = {
  images: {
    domains: ['example.com'],
  },
}
```

### 4. Middleware

```typescript
// middleware.ts
import { NextResponse } from 'next/server'
import type { NextRequest } from 'next/server'

export function middleware(request: NextRequest) {
  // Check authentication
  const token = request.cookies.get('token')
  
  if (!token && request.nextUrl.pathname.startsWith('/dashboard')) {
    return NextResponse.redirect(new URL('/login', request.url))
  }
  
  return NextResponse.next()
}

export const config = {
  matcher: '/dashboard/:path*',
}
```

### 5. Loading and Error States

```typescript
// app/dashboard/loading.tsx
export default function Loading() {
  return <div>Loading dashboard...</div>
}

// app/dashboard/error.tsx
'use client'

export default function Error({
  error,
  reset,
}: {
  error: Error
  reset: () => void
}) {
  return (
    <div>
      <h2>Something went wrong!</h2>
      <button onClick={() => reset()}>Try again</button>
    </div>
  )
}
```

### 6. Parallel Routes

```typescript
// app/dashboard/layout.tsx
export default function Layout({
  children,
  analytics,
  team,
}: {
  children: React.ReactNode
  analytics: React.ReactNode
  team: React.ReactNode
}) {
  return (
    <>
      {children}
      {analytics}
      {team}
    </>
  )
}

// app/dashboard/@analytics/page.tsx
export default function Analytics() {
  return <div>Analytics</div>
}

// app/dashboard/@team/page.tsx
export default function Team() {
  return <div>Team</div>
}
```

## Real-World Example

### Complete Blog Application

```typescript
// app/page.tsx
import Link from 'next/link'

async function getPosts() {
  const res = await fetch('https://api.example.com/posts', {
    next: { revalidate: 60 }
  })
  return res.json()
}

export default async function Home() {
  const posts = await getPosts()
  
  return (
    <main className="container mx-auto px-4 py-8">
      <h1 className="text-4xl font-bold mb-8">Blog</h1>
      <div className="grid gap-6 md:grid-cols-2 lg:grid-cols-3">
        {posts.map((post: any) => (
          <article key={post.id} className="border rounded-lg p-6">
            <h2 className="text-2xl font-bold mb-2">
              <Link href={`/blog/${post.slug}`}>{post.title}</Link>
            </h2>
            <p className="text-gray-600 mb-4">{post.excerpt}</p>
            <Link href={`/blog/${post.slug}`} className="text-blue-600">
              Read more →
            </Link>
          </article>
        ))}
      </div>
    </main>
  )
}

// app/blog/[slug]/page.tsx
import { notFound } from 'next/navigation'
import { Metadata } from 'next'

async function getPost(slug: string) {
  const res = await fetch(`https://api.example.com/posts/${slug}`)
  if (!res.ok) return null
  return res.json()
}

export async function generateMetadata({ params }: { params: { slug: string } }): Promise<Metadata> {
  const post = await getPost(params.slug)
  if (!post) return {}
  
  return {
    title: post.title,
    description: post.excerpt,
  }
}

export default async function BlogPost({ params }: { params: { slug: string } }) {
  const post = await getPost(params.slug)
  
  if (!post) {
    notFound()
  }
  
  return (
    <article className="container mx-auto px-4 py-8 max-w-3xl">
      <h1 className="text-4xl font-bold mb-4">{post.title}</h1>
      <time className="text-gray-600">{post.date}</time>
      <div className="prose mt-8" dangerouslySetInnerHTML={{ __html: post.content }} />
    </article>
  )
}

export async function generateStaticParams() {
  const posts = await fetch('https://api.example.com/posts').then(res => res.json())
  return posts.map((post: any) => ({ slug: post.slug }))
}
```

## Best Practices

### 1. Use Server Components by Default

```typescript
// ✅ Good: Server Component (default)
export default async function Page() {
  const data = await fetch('...')
  return <div>{data}</div>
}

// Only use 'use client' when needed
'use client'
export default function InteractiveComponent() {
  const [state, setState] = useState()
  return <button onClick={() => setState(...)}>Click</button>
}
```

### 2. Optimize Images

```typescript
// ✅ Good: Use Next.js Image
import Image from 'next/image'
<Image src="/hero.jpg" width={1200} height={600} alt="Hero" />

// ❌ Bad: Regular img tag
<img src="/hero.jpg" alt="Hero" />
```

### 3. Use Metadata API

```typescript
// ✅ Good: Use Metadata API
export const metadata = {
  title: 'My Page',
  description: 'Description',
}

// ❌ Bad: Manual head tags
<head>
  <title>My Page</title>
</head>
```

## Common Pitfalls

### 1. Using Client Components Unnecessarily

```typescript
// ❌ Bad: Client component for static content
'use client'
export default function Page() {
  return <div>Static content</div>
}

// ✅ Good: Server component
export default function Page() {
  return <div>Static content</div>
}
```

### 2. Not Handling Loading States

```typescript
// ❌ Bad: No loading state
export default async function Page() {
  const data = await fetch('...')
  return <div>{data}</div>
}

// ✅ Good: Add loading.tsx
// app/loading.tsx
export default function Loading() {
  return <div>Loading...</div>
}
```

## Resources

### Official Documentation
- [Next.js Docs](https://nextjs.org/docs)
- [App Router](https://nextjs.org/docs/app)
- [Examples](https://github.com/vercel/next.js/tree/canary/examples)

### Tutorials
- [Next.js Learn](https://nextjs.org/learn)
- [Next.js Crash Course](https://www.youtube.com/results?search_query=nextjs+crash+course)

### Community
- [Discord](https://nextjs.org/discord)
- [GitHub Discussions](https://github.com/vercel/next.js/discussions)

---

**Next Steps:**
- Explore [Remix](./remix.md) as an alternative
- Learn [React Server Components](https://react.dev/blog/2023/03/22/react-labs-what-we-have-been-working-on-march-2023)
- Check out [Vercel](https://vercel.com) for deployment
