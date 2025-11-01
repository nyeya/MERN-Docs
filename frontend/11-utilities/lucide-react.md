# Lucide React - Beautiful & Consistent Icon Library

## Overview

Lucide React is a beautiful, consistent icon library with over 1,000 carefully crafted icons. It's a community-maintained fork of Feather Icons with more icons and active development. Lucide provides clean, minimal SVG icons that are perfect for modern web applications, with excellent TypeScript support and tree-shaking capabilities.

**Key Features:**
- 1,000+ consistent, beautiful icons
- Fully tree-shakeable
- TypeScript support
- Customizable size, color, and stroke
- Zero dependencies
- SSR compatible
- Accessible by default
- Regular updates
- Consistent design language

## Why Use Lucide React?

### Problems It Solves

1. **Design Consistency**: All icons follow the same design principles
2. **Bundle Size**: Tree-shakeable - only bundle what you use
3. **Customization**: Easy to customize with props
4. **Type Safety**: Excellent TypeScript support
5. **Quality**: High-quality, pixel-perfect icons
6. **Modern**: Regular updates with new icons
7. **Accessibility**: Built-in ARIA attributes

### When to Use Lucide React

- ✅ Need consistent icon design
- ✅ Modern, minimal aesthetic
- ✅ TypeScript projects
- ✅ Tree-shaking is important
- ✅ Want customizable stroke width
- ✅ Need accessible icons
- ✅ Regular icon updates needed

### When NOT to Use Lucide React

- ❌ Need filled/solid icons (Lucide is outline-based)
- ❌ Need brand/logo icons
- ❌ Need very specific icon styles
- ❌ Need 10,000+ icons (use React Icons)

## Comparison with Alternatives

### Lucide React vs React Icons

| Feature | Lucide React | React Icons |
|---------|--------------|-------------|
| Icon Count | 1,000+ | 40,000+ |
| Consistency | Highly consistent | Varies by pack |
| Design Style | Outline | Multiple styles |
| Bundle Size | ~1KB per icon | Varies |
| Customization | Excellent | Basic |
| Best For | Consistent design | Variety |

**Choose Lucide React if:** You want beautiful, consistent icons
**Choose React Icons if:** You need variety and multiple icon styles

### Lucide React vs Heroicons

| Feature | Lucide React | Heroicons |
|---------|--------------|-----------|
| Icon Count | 1,000+ | 300+ |
| Styles | Outline | Outline + Solid |
| Stroke Width | Customizable | Fixed |
| Updates | Frequent | Less frequent |
| Community | Large | Tailwind team |
| Best For | Customization | Tailwind projects |

## Installation & Setup

### Installation

```bash
# npm
npm install lucide-react

# yarn
yarn add lucide-react

# pnpm
pnpm add lucide-react
```

### TypeScript Setup

Lucide React has built-in TypeScript support. No additional types needed!

### Basic Setup

```typescript
import { Home, User, Settings } from 'lucide-react'

function App() {
  return (
    <div>
      <Home />
      <User />
      <Settings />
    </div>
  )
}
```

## Core Concepts

### 1. Basic Icon Usage

```typescript
import { Heart } from 'lucide-react'

function LikeButton() {
  return (
    <button>
      <Heart />
      Like
    </button>
  )
}
```

### 2. Customizing Size

```typescript
import { Heart } from 'lucide-react'

function Icons() {
  return (
    <div>
      <Heart size={16} />
      <Heart size={24} />
      <Heart size={32} />
      <Heart size={48} />
    </div>
  )
}
```

### 3. Customizing Color

```typescript
import { Heart } from 'lucide-react'

function ColoredIcons() {
  return (
    <div>
      <Heart color="red" />
      <Heart color="#3b82f6" />
      <Heart className="text-blue-500" />
    </div>
  )
}
```

### 4. Customizing Stroke Width

```typescript
import { Heart } from 'lucide-react'

function StrokeIcons() {
  return (
    <div>
      <Heart strokeWidth={1} />
      <Heart strokeWidth={1.5} />
      <Heart strokeWidth={2} />
      <Heart strokeWidth={3} />
    </div>
  )
}
```

### 5. Fill and Stroke

```typescript
import { Heart } from 'lucide-react'

function FilledIcon() {
  return (
    <Heart
      fill="red"
      stroke="darkred"
      strokeWidth={2}
    />
  )
}
```

## Basic Usage

### Navigation Icons

```typescript
import { Home, User, Settings, Bell } from 'lucide-react'

function Navigation() {
  return (
    <nav className="flex space-x-4">
      <a href="/" className="flex items-center gap-2">
        <Home size={20} />
        <span>Home</span>
      </a>
      <a href="/profile" className="flex items-center gap-2">
        <User size={20} />
        <span>Profile</span>
      </a>
      <a href="/settings" className="flex items-center gap-2">
        <Settings size={20} />
        <span>Settings</span>
      </a>
      <a href="/notifications" className="flex items-center gap-2">
        <Bell size={20} />
        <span>Notifications</span>
      </a>
    </nav>
  )
}
```

### Action Buttons

```typescript
import { Edit, Trash2, Save, X } from 'lucide-react'

function ActionButtons() {
  return (
    <div className="flex gap-2">
      <button className="btn btn-primary flex items-center gap-2">
        <Edit size={16} />
        Edit
      </button>
      <button className="btn btn-danger flex items-center gap-2">
        <Trash2 size={16} />
        Delete
      </button>
      <button className="btn btn-success flex items-center gap-2">
        <Save size={16} />
        Save
      </button>
      <button className="btn btn-secondary flex items-center gap-2">
        <X size={16} />
        Cancel
      </button>
    </div>
  )
}
```

### Status Indicators

```typescript
import { CheckCircle, XCircle, AlertCircle, Info } from 'lucide-react'

function StatusMessage({ type, message }: { type: string; message: string }) {
  const icons = {
    success: <CheckCircle className="text-green-500" />,
    error: <XCircle className="text-red-500" />,
    warning: <AlertCircle className="text-yellow-500" />,
    info: <Info className="text-blue-500" />
  }

  return (
    <div className="flex items-center gap-2">
      {icons[type as keyof typeof icons]}
      <span>{message}</span>
    </div>
  )
}
```

## Advanced Usage

### 1. Dynamic Icon Component

```typescript
import { LucideIcon } from 'lucide-react'
import { Home, User, Settings } from 'lucide-react'

interface IconButtonProps {
  icon: LucideIcon
  label: string
  onClick: () => void
}

function IconButton({ icon: Icon, label, onClick }: IconButtonProps) {
  return (
    <button onClick={onClick} className="flex items-center gap-2">
      <Icon size={20} />
      {label}
    </button>
  )
}

// Usage
function Example() {
  return (
    <div>
      <IconButton icon={Home} label="Home" onClick={() => {}} />
      <IconButton icon={User} label="Profile" onClick={() => {}} />
      <IconButton icon={Settings} label="Settings" onClick={() => {}} />
    </div>
  )
}
```

### 2. Animated Icons

```typescript
import { Loader2, Heart } from 'lucide-react'
import { useState } from 'react'

function LoadingButton() {
  const [loading, setLoading] = useState(false)

  return (
    <button
      onClick={() => setLoading(!loading)}
      disabled={loading}
      className="flex items-center gap-2"
    >
      {loading ? (
        <Loader2 className="animate-spin" size={20} />
      ) : (
        <Heart size={20} />
      )}
      {loading ? 'Loading...' : 'Like'}
    </button>
  )
}
```

### 3. Icon with Badge

```typescript
import { Bell } from 'lucide-react'

interface NotificationIconProps {
  count: number
}

function NotificationIcon({ count }: NotificationIconProps) {
  return (
    <div className="relative inline-block">
      <Bell size={24} />
      {count > 0 && (
        <span className="absolute -top-2 -right-2 bg-red-500 text-white text-xs rounded-full w-5 h-5 flex items-center justify-center">
          {count > 99 ? '99+' : count}
        </span>
      )}
    </div>
  )
}
```

### 4. Icon Button Component

```typescript
import { LucideIcon } from 'lucide-react'

interface IconButtonProps {
  icon: LucideIcon
  onClick: () => void
  variant?: 'primary' | 'secondary' | 'danger'
  size?: number
  label?: string
  disabled?: boolean
}

function IconButton({ 
  icon: Icon, 
  onClick, 
  variant = 'primary',
  size = 20,
  label,
  disabled = false
}: IconButtonProps) {
  return (
    <button
      onClick={onClick}
      disabled={disabled}
      className={`icon-button icon-button-${variant}`}
      aria-label={label}
    >
      <Icon size={size} />
    </button>
  )
}
```

### 5. Search with Icon

```typescript
import { Search, X } from 'lucide-react'
import { useState } from 'react'

function SearchInput() {
  const [query, setQuery] = useState('')

  return (
    <div className="relative">
      <Search
        className="absolute left-3 top-1/2 -translate-y-1/2 text-gray-400"
        size={20}
      />
      <input
        type="text"
        value={query}
        onChange={(e) => setQuery(e.target.value)}
        placeholder="Search..."
        className="pl-10 pr-10 py-2 border rounded-lg w-full"
      />
      {query && (
        <button
          onClick={() => setQuery('')}
          className="absolute right-3 top-1/2 -translate-y-1/2 text-gray-400 hover:text-gray-600"
        >
          <X size={20} />
        </button>
      )}
    </div>
  )
}
```

### 6. File Type Icons

```typescript
import { 
  FileText, 
  FileImage, 
  FileVideo,
  FileAudio,
  File 
} from 'lucide-react'

function getFileIcon(filename: string) {
  const ext = filename.split('.').pop()?.toLowerCase()
  
  switch (ext) {
    case 'txt':
    case 'doc':
    case 'docx':
    case 'pdf':
      return FileText
    case 'jpg':
    case 'jpeg':
    case 'png':
    case 'gif':
      return FileImage
    case 'mp4':
    case 'avi':
    case 'mov':
      return FileVideo
    case 'mp3':
    case 'wav':
      return FileAudio
    default:
      return File
  }
}

function FileItem({ filename }: { filename: string }) {
  const Icon = getFileIcon(filename)
  
  return (
    <div className="flex items-center gap-2">
      <Icon size={24} className="text-gray-600" />
      <span>{filename}</span>
    </div>
  )
}
```

## Real-World Examples

### Dropdown Menu

```typescript
import { 
  MoreVertical, 
  Edit, 
  Trash2, 
  Download,
  Share2 
} from 'lucide-react'
import { useState } from 'react'

function DropdownMenu() {
  const [isOpen, setIsOpen] = useState(false)

  return (
    <div className="relative">
      <button onClick={() => setIsOpen(!isOpen)}>
        <MoreVertical size={20} />
      </button>

      {isOpen && (
        <div className="absolute right-0 mt-2 w-48 bg-white rounded-lg shadow-lg border">
          <button className="flex items-center gap-2 w-full px-4 py-2 hover:bg-gray-100">
            <Edit size={16} />
            Edit
          </button>
          <button className="flex items-center gap-2 w-full px-4 py-2 hover:bg-gray-100">
            <Download size={16} />
            Download
          </button>
          <button className="flex items-center gap-2 w-full px-4 py-2 hover:bg-gray-100">
            <Share2 size={16} />
            Share
          </button>
          <button className="flex items-center gap-2 w-full px-4 py-2 hover:bg-gray-100 text-red-600">
            <Trash2 size={16} />
            Delete
          </button>
        </div>
      )}
    </div>
  )
}
```

### Toast Notification

```typescript
import { CheckCircle, XCircle, AlertCircle, X } from 'lucide-react'

type ToastType = 'success' | 'error' | 'warning'

interface ToastProps {
  type: ToastType
  message: string
  onClose: () => void
}

function Toast({ type, message, onClose }: ToastProps) {
  const config = {
    success: {
      icon: CheckCircle,
      bgColor: 'bg-green-50',
      borderColor: 'border-green-500',
      textColor: 'text-green-900'
    },
    error: {
      icon: XCircle,
      bgColor: 'bg-red-50',
      borderColor: 'border-red-500',
      textColor: 'text-red-900'
    },
    warning: {
      icon: AlertCircle,
      bgColor: 'bg-yellow-50',
      borderColor: 'border-yellow-500',
      textColor: 'text-yellow-900'
    }
  }

  const { icon: Icon, bgColor, borderColor, textColor } = config[type]

  return (
    <div className={`flex items-center gap-3 p-4 rounded-lg border-l-4 ${bgColor} ${borderColor}`}>
      <Icon className={textColor} size={20} />
      <p className={`flex-1 ${textColor}`}>{message}</p>
      <button onClick={onClose} className={textColor}>
        <X size={20} />
      </button>
    </div>
  )
}
```

### Feature Card

```typescript
import { LucideIcon } from 'lucide-react'
import { Zap, Shield, Smartphone } from 'lucide-react'

interface FeatureCardProps {
  icon: LucideIcon
  title: string
  description: string
}

function FeatureCard({ icon: Icon, title, description }: FeatureCardProps) {
  return (
    <div className="p-6 border rounded-lg hover:shadow-lg transition-shadow">
      <div className="w-12 h-12 bg-blue-100 rounded-lg flex items-center justify-center mb-4">
        <Icon className="text-blue-600" size={24} />
      </div>
      <h3 className="text-xl font-bold mb-2">{title}</h3>
      <p className="text-gray-600">{description}</p>
    </div>
  )
}

function Features() {
  return (
    <div className="grid grid-cols-3 gap-6">
      <FeatureCard
        icon={Zap}
        title="Fast Performance"
        description="Lightning-fast load times and smooth interactions"
      />
      <FeatureCard
        icon={Shield}
        title="Secure"
        description="Enterprise-grade security for your data"
      />
      <FeatureCard
        icon={Smartphone}
        title="Responsive"
        description="Works perfectly on all devices"
      />
    </div>
  )
}
```

### Sidebar Navigation

```typescript
import { LucideIcon } from 'lucide-react'
import { 
  Home, 
  Users, 
  FileText, 
  Settings,
  ChevronRight 
} from 'lucide-react'
import { useState } from 'react'

interface NavItem {
  icon: LucideIcon
  label: string
  href: string
  badge?: number
}

const navItems: NavItem[] = [
  { icon: Home, label: 'Dashboard', href: '/' },
  { icon: Users, label: 'Users', href: '/users', badge: 5 },
  { icon: FileText, label: 'Documents', href: '/documents' },
  { icon: Settings, label: 'Settings', href: '/settings' }
]

function Sidebar() {
  const [activeItem, setActiveItem] = useState('/')

  return (
    <aside className="w-64 bg-gray-900 text-white p-4">
      <nav className="space-y-2">
        {navItems.map((item) => {
          const Icon = item.icon
          const isActive = activeItem === item.href

          return (
            <a
              key={item.href}
              href={item.href}
              onClick={() => setActiveItem(item.href)}
              className={`flex items-center gap-3 px-4 py-3 rounded-lg transition-colors ${
                isActive ? 'bg-blue-600' : 'hover:bg-gray-800'
              }`}
            >
              <Icon size={20} />
              <span className="flex-1">{item.label}</span>
              {item.badge && (
                <span className="bg-red-500 text-xs px-2 py-1 rounded-full">
                  {item.badge}
                </span>
              )}
              {isActive && <ChevronRight size={16} />}
            </a>
          )
        })}
      </nav>
    </aside>
  )
}
```

## Best Practices

### 1. Use Consistent Sizes

```typescript
// ✅ Good: Consistent sizing
const ICON_SIZES = {
  sm: 16,
  md: 20,
  lg: 24,
  xl: 32
}

<Home size={ICON_SIZES.md} />

// ❌ Bad: Random sizes
<Home size={19} />
<User size={22} />
```

### 2. Use className for Styling

```typescript
// ✅ Good: Use className with Tailwind
<Heart className="text-red-500 hover:text-red-600" />

// ❌ Bad: Inline color prop when using Tailwind
<Heart color="red" />
```

### 3. Add Accessibility Labels

```typescript
// ✅ Good: Accessible button
<button aria-label="Delete item">
  <Trash2 size={20} />
</button>

// ❌ Bad: No label
<button>
  <Trash2 size={20} />
</button>
```

### 4. Use Type-Safe Icon Props

```typescript
import { LucideIcon } from 'lucide-react'

// ✅ Good: Type-safe icon prop
interface Props {
  icon: LucideIcon
}

// ❌ Bad: Any type
interface Props {
  icon: any
}
```

## Common Pitfalls

### 1. Not Setting Icon Size

```typescript
// ❌ Bad: Default size might be too large
<Home />

// ✅ Good: Explicit size
<Home size={20} />
```

### 2. Mixing Stroke Widths

```typescript
// ❌ Bad: Inconsistent stroke widths
<Home strokeWidth={1} />
<User strokeWidth={2.5} />

// ✅ Good: Consistent stroke width
const STROKE_WIDTH = 2
<Home strokeWidth={STROKE_WIDTH} />
<User strokeWidth={STROKE_WIDTH} />
```

### 3. Not Using Semantic Icon Names

```typescript
// ❌ Bad: Generic variable names
const Icon1 = Trash2
const Icon2 = Edit

// ✅ Good: Semantic names
const DeleteIcon = Trash2
const EditIcon = Edit
```

### 4. Overusing Fill

```typescript
// ❌ Bad: Filled icons everywhere (Lucide is designed for outlines)
<Heart fill="red" stroke="red" />

// ✅ Good: Use stroke, fill sparingly
<Heart className="text-red-500" strokeWidth={2} />
```

## Resources

### Official Documentation
- [Lucide Docs](https://lucide.dev/)
- [Lucide GitHub](https://github.com/lucide-icons/lucide)
- [Icon Search](https://lucide.dev/icons/)

### Tools
- [Lucide Figma Plugin](https://www.figma.com/community/plugin/939567362549682242)
- [VS Code Extension](https://marketplace.visualstudio.com/items?itemName=lucide-icons.lucide)

### Related Libraries
- [Lucide Vue](https://github.com/lucide-icons/lucide/tree/main/packages/lucide-vue-next)
- [Lucide Angular](https://github.com/lucide-icons/lucide/tree/main/packages/lucide-angular)
- [Lucide Svelte](https://github.com/lucide-icons/lucide/tree/main/packages/lucide-svelte)

### Community
- [GitHub Discussions](https://github.com/lucide-icons/lucide/discussions)
- [Discord](https://discord.gg/EH6nSts)

---

**Next Steps:**
- Explore [React Icons](./react-icons.md) for more icon variety
- Learn [Heroicons](https://heroicons.com/) for Tailwind integration
- Check out [Phosphor Icons](https://phosphoricons.com/) for alternative style
