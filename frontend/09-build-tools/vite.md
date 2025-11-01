# Vite - Next Generation Frontend Tooling

## Overview

Vite is a modern build tool that provides an extremely fast development experience for modern web projects. Created by Evan You (creator of Vue.js), Vite leverages native ES modules and esbuild to deliver instant server start and lightning-fast Hot Module Replacement (HMR). It has become the de facto standard for new React projects.

**Key Features:**
- Instant server start (no bundling in dev)
- Lightning-fast HMR
- Optimized production builds with Rollup
- Built-in TypeScript support
- CSS pre-processor support
- Plugin ecosystem
- Framework agnostic
- Extremely small configuration

## Why Use Vite?

### Problems It Solves

1. **Slow Dev Server**: Instant start vs minutes with Webpack
2. **Slow HMR**: Updates in milliseconds
3. **Complex Configuration**: Minimal setup required
4. **Build Performance**: Fast production builds
5. **Modern Standards**: Native ESM support

### When to Use Vite

- ✅ New React projects
- ✅ When development speed matters
- ✅ Modern browser targets
- ✅ TypeScript projects
- ✅ Any framework (React, Vue, Svelte, etc.)

### When NOT to Use Vite

- ❌ Legacy browser support (IE11)
- ❌ Existing CRA projects (migration effort)
- ❌ When you need Webpack-specific features

## Comparison with Alternatives

### Vite vs Create React App (CRA)

| Feature | Vite | CRA |
|---------|------|-----|
| Dev Server Start | Instant | 20-60s |
| HMR Speed | <50ms | 1-5s |
| Build Tool | Rollup | Webpack |
| Configuration | Minimal | Hidden/complex |
| Bundle Size | Smaller | Larger |
| Maintenance | Active | Deprecated |
| Best For | New projects | Legacy |

**Choose Vite**: For all new projects (CRA is deprecated)

### Vite vs Next.js

| Feature | Vite | Next.js |
|---------|------|---------|
| Type | Build tool | Framework |
| SSR | Manual | Built-in |
| Routing | Manual | File-based |
| API Routes | No | Yes |
| Image Optimization | Manual | Built-in |
| Best For | SPAs | Full-stack apps |

**Choose Vite if:** Building a SPA
**Choose Next.js if:** Need SSR, routing, API routes

### Vite vs Turbopack

| Feature | Vite | Turbopack |
|---------|------|-----------|
| Maturity | Stable | Beta |
| Speed | Very fast | Faster |
| Ecosystem | Large | Growing |
| Framework | Agnostic | Next.js focused |
| Best For | Production | Experimental |

## Installation & Setup

### Create New Project

```bash
# npm
npm create vite@latest my-app -- --template react-ts

# yarn
yarn create vite my-app --template react-ts

# pnpm
pnpm create vite my-app --template react-ts
```

### Available Templates

- `react` - React with JavaScript
- `react-ts` - React with TypeScript
- `react-swc` - React with SWC (faster)
- `react-swc-ts` - React with SWC and TypeScript

### Project Structure

```
my-app/
├── node_modules/
├── public/
│   └── vite.svg
├── src/
│   ├── assets/
│   ├── App.tsx
│   ├── App.css
│   ├── main.tsx
│   └── index.css
├── index.html
├── package.json
├── tsconfig.json
├── tsconfig.node.json
└── vite.config.ts
```

### Basic Configuration

```typescript
// vite.config.ts
import { defineConfig } from 'vite'
import react from '@vitejs/plugin-react'

export default defineConfig({
  plugins: [react()],
  server: {
    port: 3000,
    open: true,
  },
  build: {
    outDir: 'dist',
    sourcemap: true,
  },
})
```

## Core Concepts

### 1. Native ESM in Development

Vite serves source code over native ESM:

```typescript
// No bundling needed in dev
import { useState } from 'react'
import './App.css'
```

### 2. Hot Module Replacement (HMR)

Instant updates without full reload:

```typescript
// Vite automatically handles HMR
if (import.meta.hot) {
  import.meta.hot.accept()
}
```

### 3. Environment Variables

```bash
# .env
VITE_API_URL=https://api.example.com
VITE_APP_TITLE=My App
```

```typescript
// Access in code
const apiUrl = import.meta.env.VITE_API_URL
const title = import.meta.env.VITE_APP_TITLE
```

### 4. Static Asset Handling

```typescript
// Import assets
import logo from './assets/logo.png'

// Use in JSX
<img src={logo} alt="Logo" />

// Public folder (no import needed)
<img src="/favicon.ico" alt="Favicon" />
```

## Basic Usage

### Development

```bash
# Start dev server
npm run dev

# Server starts instantly at http://localhost:5173
```

### Production Build

```bash
# Build for production
npm run build

# Preview production build
npm run preview
```

### TypeScript Support

TypeScript works out of the box:

```typescript
// src/App.tsx
import { useState } from 'react'

interface User {
  name: string
  email: string
}

function App() {
  const [user, setUser] = useState<User | null>(null)
  
  return <div>{user?.name}</div>
}

export default App
```

## Advanced Configuration

### 1. Path Aliases

```typescript
// vite.config.ts
import { defineConfig } from 'vite'
import react from '@vitejs/plugin-react'
import path from 'path'

export default defineConfig({
  plugins: [react()],
  resolve: {
    alias: {
      '@': path.resolve(__dirname, './src'),
      '@components': path.resolve(__dirname, './src/components'),
      '@utils': path.resolve(__dirname, './src/utils'),
    },
  },
})

// tsconfig.json
{
  "compilerOptions": {
    "paths": {
      "@/*": ["./src/*"],
      "@components/*": ["./src/components/*"],
      "@utils/*": ["./src/utils/*"]
    }
  }
}

// Usage
import Button from '@components/Button'
import { formatDate } from '@utils/date'
```

### 2. CSS Pre-processors

```bash
# Install Sass
npm install -D sass

# Install Less
npm install -D less
```

```typescript
// Use directly
import './styles.scss'
import './styles.less'
```

### 3. Environment-Specific Config

```typescript
// vite.config.ts
import { defineConfig } from 'vite'
import react from '@vitejs/plugin-react'

export default defineConfig(({ mode }) => {
  const isDev = mode === 'development'
  
  return {
    plugins: [react()],
    server: {
      port: isDev ? 3000 : 8080,
    },
    build: {
      sourcemap: isDev,
      minify: !isDev,
    },
  }
})
```

### 4. Proxy Configuration

```typescript
// vite.config.ts
export default defineConfig({
  server: {
    proxy: {
      '/api': {
        target: 'http://localhost:5000',
        changeOrigin: true,
        rewrite: (path) => path.replace(/^\/api/, ''),
      },
    },
  },
})

// Now /api/users -> http://localhost:5000/users
```

### 5. Build Optimization

```typescript
// vite.config.ts
export default defineConfig({
  build: {
    rollupOptions: {
      output: {
        manualChunks: {
          'react-vendor': ['react', 'react-dom'],
          'ui-vendor': ['@radix-ui/react-dialog', '@radix-ui/react-dropdown-menu'],
        },
      },
    },
    chunkSizeWarningLimit: 1000,
  },
})
```

### 6. PWA Support

```bash
npm install -D vite-plugin-pwa
```

```typescript
// vite.config.ts
import { VitePWA } from 'vite-plugin-pwa'

export default defineConfig({
  plugins: [
    react(),
    VitePWA({
      registerType: 'autoUpdate',
      manifest: {
        name: 'My App',
        short_name: 'App',
        theme_color: '#ffffff',
        icons: [
          {
            src: '/icon-192.png',
            sizes: '192x192',
            type: 'image/png',
          },
        ],
      },
    }),
  ],
})
```

## Popular Plugins

### 1. React with SWC (Faster)

```bash
npm install -D @vitejs/plugin-react-swc
```

```typescript
import { defineConfig } from 'vite'
import react from '@vitejs/plugin-react-swc'

export default defineConfig({
  plugins: [react()],
})
```

### 2. SVG as React Components

```bash
npm install -D vite-plugin-svgr
```

```typescript
import { defineConfig } from 'vite'
import react from '@vitejs/plugin-react'
import svgr from 'vite-plugin-svgr'

export default defineConfig({
  plugins: [react(), svgr()],
})

// Usage
import Logo from './logo.svg?react'
<Logo />
```

### 3. Bundle Analyzer

```bash
npm install -D rollup-plugin-visualizer
```

```typescript
import { visualizer } from 'rollup-plugin-visualizer'

export default defineConfig({
  plugins: [
    react(),
    visualizer({
      open: true,
      gzipSize: true,
      brotliSize: true,
    }),
  ],
})
```

## Best Practices

### 1. Use Environment Variables

```bash
# .env.development
VITE_API_URL=http://localhost:3000

# .env.production
VITE_API_URL=https://api.production.com
```

### 2. Optimize Imports

```typescript
// ✅ Good: Named imports
import { Button } from '@/components/ui/button'

// ❌ Bad: Barrel imports (slower)
import { Button } from '@/components'
```

### 3. Code Splitting

```typescript
// ✅ Good: Lazy load routes
import { lazy, Suspense } from 'react'

const Dashboard = lazy(() => import('./pages/Dashboard'))

function App() {
  return (
    <Suspense fallback={<div>Loading...</div>}>
      <Dashboard />
    </Suspense>
  )
}
```

### 4. Asset Optimization

```typescript
// ✅ Good: Import images
import logo from './logo.png'

// Vite optimizes and generates hash
<img src={logo} alt="Logo" />
```

## Common Pitfalls

### 1. Using process.env

```typescript
// ❌ Bad: Doesn't work in Vite
const apiUrl = process.env.REACT_APP_API_URL

// ✅ Good: Use import.meta.env
const apiUrl = import.meta.env.VITE_API_URL
```

### 2. Not Prefixing Environment Variables

```bash
# ❌ Bad: Won't be exposed
API_URL=http://localhost:3000

# ✅ Good: Prefix with VITE_
VITE_API_URL=http://localhost:3000
```

### 3. Importing from node_modules Directly

```typescript
// ❌ Bad: Can cause issues
import Button from 'node_modules/some-lib/Button'

// ✅ Good: Use package name
import Button from 'some-lib'
```

## Migration from CRA

### 1. Install Vite

```bash
npm install -D vite @vitejs/plugin-react
```

### 2. Update package.json

```json
{
  "scripts": {
    "dev": "vite",
    "build": "vite build",
    "preview": "vite preview"
  }
}
```

### 3. Move index.html

Move `public/index.html` to root and update:

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>My App</title>
  </head>
  <body>
    <div id="root"></div>
    <script type="module" src="/src/main.tsx"></script>
  </body>
</html>
```

### 4. Update Environment Variables

```bash
# Change REACT_APP_ to VITE_
REACT_APP_API_URL -> VITE_API_URL
```

### 5. Update Imports

```typescript
// Change process.env to import.meta.env
process.env.REACT_APP_API_URL -> import.meta.env.VITE_API_URL
```

## Resources

### Official Documentation
- [Vite Docs](https://vitejs.dev/)
- [GitHub Repository](https://github.com/vitejs/vite)
- [Plugin List](https://vitejs.dev/plugins/)

### Tutorials
- [Vite Crash Course](https://www.youtube.com/results?search_query=vite+crash+course)
- [Migration Guide](https://vitejs.dev/guide/migration.html)

### Community
- [Discord](https://chat.vitejs.dev/)
- [GitHub Discussions](https://github.com/vitejs/vite/discussions)

---

**Next Steps:**
- Explore [Turbopack](./turbopack.md) for even faster builds
- Learn [esbuild](./esbuild.md) for understanding Vite's speed
- Check out [Next.js](../16-meta-frameworks/nextjs.md) for full-stack apps
