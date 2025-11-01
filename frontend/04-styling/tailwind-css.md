# Tailwind CSS - Utility-First CSS Framework

## Overview

Tailwind CSS is a utility-first CSS framework that provides low-level utility classes to build custom designs without leaving your HTML. Unlike traditional CSS frameworks that provide pre-designed components, Tailwind gives you the building blocks to create your own unique designs.

**Key Features:**
- Utility-first approach
- Highly customizable
- Responsive design built-in
- Dark mode support
- JIT (Just-In-Time) compiler
- Automatic unused CSS purging
- Small production bundle sizes

## Why Use Tailwind CSS?

### Problems It Solves

1. **CSS Bloat**: Only includes CSS you actually use
2. **Naming Conventions**: No need to invent class names
3. **Context Switching**: Write styles without leaving HTML
4. **Consistency**: Enforces design system constraints
5. **Responsiveness**: Built-in responsive utilities

### When to Use Tailwind CSS

- ✅ Building custom designs from scratch
- ✅ Rapid prototyping
- ✅ Component-based applications
- ✅ When you want design system constraints
- ✅ Projects requiring responsive design

### When NOT to Use Tailwind CSS

- ❌ Simple static websites
- ❌ When you need pre-built components
- ❌ Teams unfamiliar with utility-first approach

## Comparison with Alternatives

### Tailwind CSS vs Bootstrap

| Feature | Tailwind CSS | Bootstrap |
|---------|-------------|-----------|
| Approach | Utility-first | Component-first |
| Customization | Highly customizable | Limited |
| Bundle Size | Smaller | Larger |
| Design Freedom | High | Low |

### Tailwind CSS vs CSS Modules

| Feature | Tailwind CSS | CSS Modules |
|---------|-------------|-------------|
| Approach | Utility classes | Scoped CSS |
| Maintenance | Easier | More work |
| Learning Curve | Moderate | Easy |

## Installation & Setup

### With Vite

```bash
npm create vite@latest my-app -- --template react-ts
cd my-app
npm install -D tailwindcss postcss autoprefixer
npx tailwindcss init -p
```

### Configuration

```javascript
// tailwind.config.js
export default {
  content: ["./index.html", "./src/**/*.{js,ts,jsx,tsx}"],
  theme: {
    extend: {
      colors: {
        primary: '#3B82F6',
      },
    },
  },
  plugins: [],
}
```

### Add Directives

```css
/* src/index.css */
@tailwind base;
@tailwind components;
@tailwind utilities;
```

## Core Concepts

### Utility Classes

```tsx
<div className="p-4 m-2 bg-blue-500 text-white rounded-lg shadow-md">
  Content
</div>
```

### Responsive Design

```tsx
<div className="text-sm md:text-base lg:text-lg">
  Responsive text
</div>
```

### State Variants

```tsx
<button className="bg-blue-500 hover:bg-blue-700 focus:ring-2">
  Button
</button>
```

### Dark Mode

```tsx
<div className="bg-white dark:bg-gray-800 text-black dark:text-white">
  Dark mode content
</div>
```

## Basic Usage

### Button Component

```tsx
export function Button({ children }: { children: React.ReactNode }) {
  return (
    <button className="bg-blue-500 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded">
      {children}
    </button>
  )
}
```

### Card Component

```tsx
export function Card({ children }: { children: React.ReactNode }) {
  return (
    <div className="bg-white rounded-lg shadow-md p-6 hover:shadow-lg transition-shadow">
      {children}
    </div>
  )
}
```

### Form Input

```tsx
export function Input({ label, ...props }: { label?: string } & React.InputHTMLAttributes<HTMLInputElement>) {
  return (
    <div className="mb-4">
      {label && <label className="block text-gray-700 text-sm font-bold mb-2">{label}</label>}
      <input
        className="shadow border rounded w-full py-2 px-3 text-gray-700 focus:outline-none focus:ring-2 focus:ring-blue-500"
        {...props}
      />
    </div>
  )
}
```

## Advanced Usage

### Dynamic Classes with clsx

```bash
npm install clsx
```

```tsx
import clsx from 'clsx'

interface ButtonProps {
  variant?: 'primary' | 'secondary'
  size?: 'sm' | 'md' | 'lg'
  children: React.ReactNode
}

export function Button({ variant = 'primary', size = 'md', children }: ButtonProps) {
  return (
    <button
      className={clsx(
        'font-semibold rounded transition-colors',
        {
          'bg-blue-500 hover:bg-blue-700 text-white': variant === 'primary',
          'bg-gray-200 hover:bg-gray-300 text-gray-800': variant === 'secondary',
          'py-1 px-2 text-sm': size === 'sm',
          'py-2 px-4 text-base': size === 'md',
          'py-3 px-6 text-lg': size === 'lg',
        }
      )}
    >
      {children}
    </button>
  )
}
```

### Responsive Navigation

```tsx
import { useState } from 'react'

export function Navbar() {
  const [isOpen, setIsOpen] = useState(false)

  return (
    <nav className="bg-white shadow-lg">
      <div className="container mx-auto px-4">
        <div className="flex justify-between items-center py-4">
          <div className="text-2xl font-bold text-blue-600">Logo</div>
          
          <div className="hidden md:flex space-x-8">
            <a href="#" className="text-gray-700 hover:text-blue-600">Home</a>
            <a href="#" className="text-gray-700 hover:text-blue-600">About</a>
            <a href="#" className="text-gray-700 hover:text-blue-600">Contact</a>
          </div>

          <button className="md:hidden" onClick={() => setIsOpen(!isOpen)}>
            Menu
          </button>
        </div>

        {isOpen && (
          <div className="md:hidden pb-4">
            <a href="#" className="block py-2 text-gray-700">Home</a>
            <a href="#" className="block py-2 text-gray-700">About</a>
            <a href="#" className="block py-2 text-gray-700">Contact</a>
          </div>
        )}
      </div>
    </nav>
  )
}
```

### Dark Mode Hook

```tsx
import { useEffect, useState } from 'react'

export function useDarkMode() {
  const [isDark, setIsDark] = useState(() => {
    const saved = localStorage.getItem('darkMode')
    return saved ? JSON.parse(saved) : false
  })

  useEffect(() => {
    const root = window.document.documentElement
    if (isDark) {
      root.classList.add('dark')
    } else {
      root.classList.remove('dark')
    }
    localStorage.setItem('darkMode', JSON.stringify(isDark))
  }, [isDark])

  return [isDark, setIsDark] as const
}
```

## Best Practices

### 1. Use Consistent Spacing

```tsx
// ✅ Good
<div className="p-4 m-2 space-y-4">

// ❌ Bad
<div className="p-[17px] m-[9px]">
```

### 2. Extract Reusable Components

```tsx
// ✅ Good
<Button variant="primary">Click</Button>

// ❌ Bad: Repeat classes everywhere
<button className="bg-blue-500 hover:bg-blue-700...">
```

### 3. Mobile-First Approach

```tsx
// ✅ Good
<div className="text-sm md:text-base lg:text-lg">

// ❌ Bad
<div className="lg:text-lg md:text-base text-sm">
```

## Common Pitfalls

### 1. Too Many Arbitrary Values

```tsx
// ❌ Bad
<div className="w-[137px] h-[42px] bg-[#1da1f2]">

// ✅ Good: Use theme values
<div className="w-32 h-10 bg-blue-500">
```

### 2. Not Using @apply for Repeated Patterns

```css
/* ✅ Good */
@layer components {
  .btn-primary {
    @apply bg-blue-500 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded;
  }
}
```

### 3. Forgetting to Purge

```javascript
// ✅ Good: Configure content paths
export default {
  content: ["./src/**/*.{js,jsx,ts,tsx}"],
}
```

## Resources

### Official Documentation
- [Tailwind CSS Docs](https://tailwindcss.com/docs)
- [GitHub Repository](https://github.com/tailwindlabs/tailwindcss)
- [Tailwind UI](https://tailwindui.com/)

### Tutorials
- [Tailwind CSS Crash Course](https://www.youtube.com/results?search_query=tailwind+css+crash+course)
- [Official Screencasts](https://tailwindcss.com/screencasts)

### Tools
- [Tailwind Play](https://play.tailwindcss.com/)
- [Tailwind CSS IntelliSense](https://marketplace.visualstudio.com/items?itemName=bradlc.vscode-tailwindcss)
- [Headless UI](https://headlessui.com/)

### Community
- [Discord](https://tailwindcss.com/discord)
- [GitHub Discussions](https://github.com/tailwindlabs/tailwindcss/discussions)

---

**Next Steps:**
- Explore [shadcn/ui](../05-ui-libraries/shadcn-ui.md) for pre-built components
- Learn [CSS Modules](./css-modules.md) as an alternative
- Check out [Framer Motion](../07-animation/framer-motion.md) for animations
