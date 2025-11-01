# clsx - Utility for Constructing className Strings

## Overview

clsx is a tiny (228B) utility for constructing `className` strings conditionally. It's the perfect tool for dynamically building CSS class names in React components. It's faster and smaller than alternatives like classnames, making it ideal for modern React applications.

**Key Features:**
- Tiny bundle size (228 bytes)
- Extremely fast
- Conditional class names
- Supports strings, objects, arrays
- TypeScript support
- Zero dependencies
- Works with any framework
- Tree-shakeable

## Why Use clsx?

### Problems It Solves

1. **Conditional Classes**: Easy conditional class application
2. **Clean Code**: Avoid messy template literals
3. **Performance**: Faster than string concatenation
4. **Type Safety**: Works great with TypeScript
5. **Readability**: Clear, declarative class logic
6. **Bundle Size**: Minimal impact on bundle

### When to Use clsx

- ✅ Conditional CSS classes
- ✅ Dynamic styling
- ✅ Component variants
- ✅ State-based styling
- ✅ Combining multiple class sources
- ✅ Tailwind CSS utility classes
- ✅ CSS Modules

### When NOT to Use clsx

- ❌ Static class names (just use strings)
- ❌ Single class name
- ❌ No conditional logic needed

## Comparison with Alternatives

### clsx vs classnames

| Feature | clsx | classnames |
|---------|------|------------|
| Bundle Size | 228B | 1.1KB |
| Performance | Faster | Fast |
| API | Same | Same |
| TypeScript | Built-in | @types needed |
| Maintenance | Active | Active |
| Best For | Modern apps | Legacy apps |

**Choose clsx if:** You want the smallest, fastest option
**Choose classnames if:** You have existing code using it

### clsx vs Template Literals

| Feature | clsx | Template Literals |
|---------|------|-------------------|
| Readability | High | Low (complex) |
| Performance | Optimized | Slower |
| Conditional Logic | Clean | Messy |
| Falsy Handling | Automatic | Manual |
| Best For | Complex logic | Simple strings |

## Installation & Setup

### Installation

```bash
# npm
npm install clsx

# yarn
yarn add clsx

# pnpm
pnpm add clsx
```

### TypeScript Setup

clsx has built-in TypeScript support. No additional types needed!

### Basic Setup

```typescript
import clsx from 'clsx'

function Button() {
  return (
    <button className={clsx('btn', 'btn-primary')}>
      Click me
    </button>
  )
}
```

## Core Concepts

### 1. Basic Usage

```typescript
import clsx from 'clsx'

// Strings
clsx('foo', 'bar')
// "foo bar"

// Multiple arguments
clsx('foo', true && 'bar', 'baz')
// "foo bar baz"

// Falsy values are ignored
clsx('foo', false, null, undefined, 0, '')
// "foo"
```

### 2. Objects

```typescript
import clsx from 'clsx'

// Object with boolean values
clsx({
  'btn': true,
  'btn-primary': true,
  'btn-disabled': false
})
// "btn btn-primary"

// Conditional classes
const isActive = true
clsx({
  'item': true,
  'item-active': isActive
})
// "item item-active"
```

### 3. Arrays

```typescript
import clsx from 'clsx'

// Arrays
clsx(['foo', 'bar'])
// "foo bar"

// Nested arrays
clsx(['foo', ['bar', 'baz']])
// "foo bar baz"

// Mixed
clsx(['foo', { bar: true, baz: false }])
// "foo bar"
```

### 4. Mixed Arguments

```typescript
import clsx from 'clsx'

clsx(
  'foo',
  { bar: true },
  ['baz', { qux: false }],
  'quux'
)
// "foo bar baz quux"
```

## Basic Usage

### Button Variants

```typescript
import clsx from 'clsx'

interface ButtonProps {
  variant?: 'primary' | 'secondary' | 'danger'
  size?: 'sm' | 'md' | 'lg'
  disabled?: boolean
  children: React.ReactNode
}

function Button({ 
  variant = 'primary', 
  size = 'md', 
  disabled = false,
  children 
}: ButtonProps) {
  return (
    <button
      className={clsx(
        'btn',
        `btn-${variant}`,
        `btn-${size}`,
        {
          'btn-disabled': disabled
        }
      )}
      disabled={disabled}
    >
      {children}
    </button>
  )
}
```

### Conditional Styling

```typescript
import clsx from 'clsx'
import { useState } from 'react'

function Card() {
  const [isActive, setIsActive] = useState(false)
  const [isHovered, setIsHovered] = useState(false)

  return (
    <div
      className={clsx(
        'card',
        {
          'card-active': isActive,
          'card-hover': isHovered
        }
      )}
      onClick={() => setIsActive(!isActive)}
      onMouseEnter={() => setIsHovered(true)}
      onMouseLeave={() => setIsHovered(false)}
    >
      Card Content
    </div>
  )
}
```

### With Tailwind CSS

```typescript
import clsx from 'clsx'

interface AlertProps {
  type: 'success' | 'error' | 'warning' | 'info'
  message: string
}

function Alert({ type, message }: AlertProps) {
  return (
    <div
      className={clsx(
        'p-4 rounded-lg border',
        {
          'bg-green-50 border-green-500 text-green-900': type === 'success',
          'bg-red-50 border-red-500 text-red-900': type === 'error',
          'bg-yellow-50 border-yellow-500 text-yellow-900': type === 'warning',
          'bg-blue-50 border-blue-500 text-blue-900': type === 'info'
        }
      )}
    >
      {message}
    </div>
  )
}
```

## Advanced Usage

### 1. With CSS Modules

```typescript
import clsx from 'clsx'
import styles from './Button.module.css'

interface ButtonProps {
  variant: 'primary' | 'secondary'
  active?: boolean
  children: React.ReactNode
}

function Button({ variant, active, children }: ButtonProps) {
  return (
    <button
      className={clsx(
        styles.button,
        styles[variant],
        {
          [styles.active]: active
        }
      )}
    >
      {children}
    </button>
  )
}
```

### 2. Reusable Class Builder

```typescript
import clsx from 'clsx'

type ClassValue = string | number | boolean | undefined | null | ClassValue[]

function cn(...inputs: ClassValue[]) {
  return clsx(inputs)
}

// Usage
function Component() {
  return (
    <div className={cn(
      'base-class',
      condition && 'conditional-class',
      {
        'active': isActive,
        'disabled': isDisabled
      }
    )}>
      Content
    </div>
  )
}
```

### 3. Component Variants System

```typescript
import clsx from 'clsx'

const buttonVariants = {
  variant: {
    primary: 'bg-blue-500 text-white hover:bg-blue-600',
    secondary: 'bg-gray-500 text-white hover:bg-gray-600',
    outline: 'border border-blue-500 text-blue-500 hover:bg-blue-50'
  },
  size: {
    sm: 'px-3 py-1 text-sm',
    md: 'px-4 py-2 text-base',
    lg: 'px-6 py-3 text-lg'
  }
}

interface ButtonProps {
  variant?: keyof typeof buttonVariants.variant
  size?: keyof typeof buttonVariants.size
  className?: string
  children: React.ReactNode
}

function Button({ 
  variant = 'primary', 
  size = 'md', 
  className,
  children 
}: ButtonProps) {
  return (
    <button
      className={clsx(
        'rounded font-medium transition-colors',
        buttonVariants.variant[variant],
        buttonVariants.size[size],
        className
      )}
    >
      {children}
    </button>
  )
}
```

### 4. With Tailwind Merge

```typescript
import clsx from 'clsx'
import { twMerge } from 'tailwind-merge'

function cn(...inputs: ClassValue[]) {
  return twMerge(clsx(inputs))
}

// Prevents duplicate Tailwind classes
function Component() {
  return (
    <div className={cn(
      'px-4 py-2',
      'px-6' // This will override px-4
    )}>
      Content
    </div>
  )
}
```

### 5. Dynamic Theme Classes

```typescript
import clsx from 'clsx'
import { useState } from 'react'

type Theme = 'light' | 'dark' | 'auto'

function ThemeProvider({ children }: { children: React.ReactNode }) {
  const [theme, setTheme] = useState<Theme>('light')

  return (
    <div
      className={clsx(
        'min-h-screen transition-colors',
        {
          'bg-white text-gray-900': theme === 'light',
          'bg-gray-900 text-white': theme === 'dark',
          'bg-auto text-auto': theme === 'auto'
        }
      )}
    >
      {children}
    </div>
  )
}
```

### 6. Form Input States

```typescript
import clsx from 'clsx'

interface InputProps {
  value: string
  error?: string
  disabled?: boolean
  required?: boolean
  onChange: (value: string) => void
}

function Input({ value, error, disabled, required, onChange }: InputProps) {
  const [isFocused, setIsFocused] = useState(false)

  return (
    <div className="relative">
      <input
        className={clsx(
          'w-full px-4 py-2 border rounded-lg transition-all',
          {
            'border-gray-300': !error && !isFocused,
            'border-blue-500 ring-2 ring-blue-200': isFocused && !error,
            'border-red-500 ring-2 ring-red-200': error,
            'bg-gray-100 cursor-not-allowed': disabled,
            'border-l-4': required
          }
        )}
        value={value}
        onChange={(e) => onChange(e.target.value)}
        onFocus={() => setIsFocused(true)}
        onBlur={() => setIsFocused(false)}
        disabled={disabled}
      />
      {error && (
        <p className="mt-1 text-sm text-red-600">{error}</p>
      )}
    </div>
  )
}
```

## Real-World Examples

### Navigation Menu

```typescript
import clsx from 'clsx'
import { useRouter } from 'next/router'

interface NavItem {
  label: string
  href: string
  icon?: React.ReactNode
}

function Navigation({ items }: { items: NavItem[] }) {
  const router = useRouter()

  return (
    <nav className="flex space-x-4">
      {items.map((item) => {
        const isActive = router.pathname === item.href

        return (
          <a
            key={item.href}
            href={item.href}
            className={clsx(
              'px-4 py-2 rounded-lg transition-all',
              {
                'bg-blue-500 text-white': isActive,
                'text-gray-700 hover:bg-gray-100': !isActive
              }
            )}
          >
            {item.icon && <span className="mr-2">{item.icon}</span>}
            {item.label}
          </a>
        )
      })}
    </nav>
  )
}
```

### Data Table Row

```typescript
import clsx from 'clsx'

interface RowProps {
  data: any
  index: number
  isSelected: boolean
  isHovered: boolean
  onSelect: () => void
  onHover: (hovered: boolean) => void
}

function TableRow({ 
  data, 
  index, 
  isSelected, 
  isHovered,
  onSelect,
  onHover 
}: RowProps) {
  return (
    <tr
      className={clsx(
        'transition-colors cursor-pointer',
        {
          'bg-blue-50': isSelected,
          'bg-gray-50': !isSelected && index % 2 === 0,
          'bg-white': !isSelected && index % 2 === 1,
          'hover:bg-gray-100': !isSelected && isHovered
        }
      )}
      onClick={onSelect}
      onMouseEnter={() => onHover(true)}
      onMouseLeave={() => onHover(false)}
    >
      <td className="px-4 py-2">{data.name}</td>
      <td className="px-4 py-2">{data.email}</td>
    </tr>
  )
}
```

### Status Badge

```typescript
import clsx from 'clsx'

type Status = 'pending' | 'processing' | 'completed' | 'failed'

interface StatusBadgeProps {
  status: Status
  size?: 'sm' | 'md' | 'lg'
}

const statusConfig = {
  pending: {
    color: 'bg-yellow-100 text-yellow-800 border-yellow-300',
    label: 'Pending'
  },
  processing: {
    color: 'bg-blue-100 text-blue-800 border-blue-300',
    label: 'Processing'
  },
  completed: {
    color: 'bg-green-100 text-green-800 border-green-300',
    label: 'Completed'
  },
  failed: {
    color: 'bg-red-100 text-red-800 border-red-300',
    label: 'Failed'
  }
}

function StatusBadge({ status, size = 'md' }: StatusBadgeProps) {
  return (
    <span
      className={clsx(
        'inline-flex items-center border rounded-full font-medium',
        statusConfig[status].color,
        {
          'px-2 py-0.5 text-xs': size === 'sm',
          'px-3 py-1 text-sm': size === 'md',
          'px-4 py-1.5 text-base': size === 'lg'
        }
      )}
    >
      {statusConfig[status].label}
    </span>
  )
}
```

## Best Practices

### 1. Use Objects for Conditional Classes

```typescript
// ✅ Good: Object syntax for conditions
clsx({
  'active': isActive,
  'disabled': isDisabled,
  'error': hasError
})

// ❌ Bad: Multiple ternaries
clsx(
  isActive ? 'active' : '',
  isDisabled ? 'disabled' : '',
  hasError ? 'error' : ''
)
```

### 2. Combine with Base Classes

```typescript
// ✅ Good: Base class + conditionals
clsx(
  'btn',
  {
    'btn-primary': variant === 'primary',
    'btn-large': size === 'large'
  }
)

// ❌ Bad: Everything conditional
clsx({
  'btn': true,
  'btn-primary': variant === 'primary'
})
```

### 3. Extract Complex Logic

```typescript
// ✅ Good: Extract to variable
const buttonClasses = clsx(
  'btn',
  variant && `btn-${variant}`,
  size && `btn-${size}`,
  { disabled }
)

return <button className={buttonClasses}>Click</button>

// ❌ Bad: Inline complexity
return (
  <button className={clsx('btn', variant && `btn-${variant}`, size && `btn-${size}`, { disabled })}>
    Click
  </button>
)
```

### 4. Type-Safe Variants

```typescript
// ✅ Good: Type-safe variants
type Variant = 'primary' | 'secondary'
const variant: Variant = 'primary'
clsx(`btn-${variant}`)

// ❌ Bad: Untyped strings
const variant = 'primary'
clsx(`btn-${variant}`) // Could be any string
```

## Common Pitfalls

### 1. Forgetting Falsy Values

```typescript
// ❌ Bad: 0 and '' are falsy
clsx('btn', 0, '', null)
// "btn" (0 and '' are ignored)

// ✅ Good: Be explicit
clsx('btn', count > 0 && 'has-items')
```

### 2. Not Using Objects for Multiple Conditions

```typescript
// ❌ Bad: Multiple ternaries
clsx(
  isActive ? 'active' : '',
  isDisabled ? 'disabled' : ''
)

// ✅ Good: Use object
clsx({
  active: isActive,
  disabled: isDisabled
})
```

### 3. Overusing clsx

```typescript
// ❌ Bad: clsx for static classes
clsx('btn btn-primary')

// ✅ Good: Just use string
className="btn btn-primary"
```

### 4. Not Handling undefined

```typescript
// ❌ Bad: May pass undefined
<div className={clsx(className)} />

// ✅ Good: Handle undefined
<div className={clsx('base-class', className)} />
```

## Resources

### Official Documentation
- [clsx GitHub](https://github.com/lukeed/clsx)
- [npm Package](https://www.npmjs.com/package/clsx)

### Related Tools
- [tailwind-merge](https://github.com/dcastil/tailwind-merge) - Merge Tailwind classes
- [cva](https://github.com/joe-bell/cva) - Class Variance Authority
- [classnames](https://github.com/JedWatson/classnames) - Alternative library

### Tutorials
- [Using clsx with Tailwind](https://www.youtube.com/results?search_query=clsx+tailwind)
- [Component Variants with clsx](https://www.youtube.com/results?search_query=clsx+variants)

---

**Next Steps:**
- Explore [tailwind-merge](https://github.com/dcastil/tailwind-merge) for Tailwind class merging
- Learn [cva](https://github.com/joe-bell/cva) for variant management
- Check out [Tailwind CSS](../04-styling/tailwind-css.md) for utility-first styling
