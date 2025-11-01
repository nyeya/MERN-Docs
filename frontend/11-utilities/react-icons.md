# React Icons - Popular Icons as React Components

## Overview

React Icons is a comprehensive library that includes popular icon packs as React components. It provides a unified interface to use icons from Font Awesome, Material Design, Ionicons, and many other icon libraries. With over 40,000 icons available, it's a one-stop solution for all your icon needs in React applications.

**Key Features:**
- 40,000+ icons from 20+ icon packs
- Tree-shakeable (only imports used icons)
- Consistent API across all icon sets
- Customizable size and color
- TypeScript support
- Zero dependencies
- SSR compatible
- Accessible by default

## Why Use React Icons?

### Problems It Solves

1. **Icon Variety**: Access to multiple icon libraries in one package
2. **Bundle Size**: Tree-shakeable - only bundle icons you use
3. **Consistency**: Uniform API across different icon sets
4. **Customization**: Easy to style with props
5. **Type Safety**: Full TypeScript support
6. **Performance**: SVG-based, no font loading
7. **Accessibility**: Built-in ARIA attributes

### When to Use React Icons

- ✅ Need icons from multiple libraries
- ✅ Want tree-shakeable imports
- ✅ Prefer SVG over icon fonts
- ✅ Need consistent API
- ✅ TypeScript projects
- ✅ Quick prototyping
- ✅ Various icon styles needed

### When NOT to Use React Icons

- ❌ Only need one specific icon set (use that library directly)
- ❌ Need animated icons
- ❌ Custom icon set only
- ❌ Bundle size is extremely critical

## Comparison with Alternatives

### React Icons vs Lucide React

| Feature | React Icons | Lucide React |
|---------|-------------|--------------|
| Icon Count | 40,000+ | 1,000+ |
| Icon Packs | 20+ | 1 |
| Bundle Size | Varies | ~1KB per icon |
| Consistency | Varies by pack | Highly consistent |
| Customization | Basic | Advanced |
| Best For | Variety | Consistency |

**Choose React Icons if:** You need access to multiple icon libraries
**Choose Lucide React if:** You want consistent, beautiful icons

### React Icons vs Icon Fonts

| Feature | React Icons | Icon Fonts |
|---------|-------------|------------|
| Type | SVG Components | Font files |
| Bundle Size | Tree-shakeable | Full font file |
| Customization | Props | CSS |
| Loading | Instant | Font load delay |
| Accessibility | Better | Requires work |
| Best For | Modern apps | Legacy support |

## Installation & Setup

### Installation

```bash
# npm
npm install react-icons

# yarn
yarn add react-icons

# pnpm
pnpm add react-icons
```

### TypeScript Setup

React Icons has built-in TypeScript support. No additional types needed!

### Basic Setup

```typescript
import { FaBeer } from 'react-icons/fa'
import { AiFillHeart } from 'react-icons/ai'
import { MdSettings } from 'react-icons/md'

function App() {
  return (
    <div>
      <FaBeer />
      <AiFillHeart />
      <MdSettings />
    </div>
  )
}
```

## Available Icon Packs

### Popular Icon Sets

```typescript
// Font Awesome
import { FaHome, FaUser, FaHeart } from 'react-icons/fa'

// Material Design
import { MdHome, MdSettings, MdSearch } from 'react-icons/md'

// Ant Design
import { AiFillHome, AiOutlineUser } from 'react-icons/ai'

// Bootstrap Icons
import { BsHouse, BsPerson, BsGear } from 'react-icons/bs'

// Heroicons
import { HiHome, HiUser, HiCog } from 'react-icons/hi'

// Feather Icons
import { FiHome, FiUser, FiSettings } from 'react-icons/fi'

// Ionicons
import { IoHome, IoPerson, IoSettings } from 'react-icons/io5'

// Remix Icon
import { RiHome2Line, RiUserLine } from 'react-icons/ri'
```

## Core Concepts

### 1. Basic Icon Usage

```typescript
import { FaHeart } from 'react-icons/fa'

function LikeButton() {
  return (
    <button>
      <FaHeart />
      Like
    </button>
  )
}
```

### 2. Customizing Size

```typescript
import { FaHeart } from 'react-icons/fa'

function Icons() {
  return (
    <div>
      <FaHeart size={16} />
      <FaHeart size={24} />
      <FaHeart size={32} />
      <FaHeart size="2em" />
    </div>
  )
}
```

### 3. Customizing Color

```typescript
import { FaHeart } from 'react-icons/fa'

function ColoredIcons() {
  return (
    <div>
      <FaHeart color="red" />
      <FaHeart color="#3b82f6" />
      <FaHeart style={{ color: 'var(--primary-color)' }} />
    </div>
  )
}
```

### 4. Icon Context Provider

```typescript
import { IconContext } from 'react-icons'
import { FaHeart, FaStar, FaThumbsUp } from 'react-icons/fa'

function App() {
  return (
    <IconContext.Provider value={{ color: 'blue', size: '2em' }}>
      <div>
        <FaHeart />
        <FaStar />
        <FaThumbsUp />
      </div>
    </IconContext.Provider>
  )
}
```

## Basic Usage

### Navigation Icons

```typescript
import { FaHome, FaUser, FaCog, FaBell } from 'react-icons/fa'

function Navigation() {
  return (
    <nav className="flex space-x-4">
      <a href="/" className="flex items-center gap-2">
        <FaHome />
        <span>Home</span>
      </a>
      <a href="/profile" className="flex items-center gap-2">
        <FaUser />
        <span>Profile</span>
      </a>
      <a href="/settings" className="flex items-center gap-2">
        <FaCog />
        <span>Settings</span>
      </a>
      <a href="/notifications" className="flex items-center gap-2">
        <FaBell />
        <span>Notifications</span>
      </a>
    </nav>
  )
}
```

### Social Media Icons

```typescript
import { 
  FaFacebook, 
  FaTwitter, 
  FaInstagram, 
  FaLinkedin,
  FaGithub 
} from 'react-icons/fa'

function SocialLinks() {
  return (
    <div className="flex gap-4">
      <a href="https://facebook.com" target="_blank">
        <FaFacebook size={24} color="#1877f2" />
      </a>
      <a href="https://twitter.com" target="_blank">
        <FaTwitter size={24} color="#1da1f2" />
      </a>
      <a href="https://instagram.com" target="_blank">
        <FaInstagram size={24} color="#e4405f" />
      </a>
      <a href="https://linkedin.com" target="_blank">
        <FaLinkedin size={24} color="#0077b5" />
      </a>
      <a href="https://github.com" target="_blank">
        <FaGithub size={24} color="#333" />
      </a>
    </div>
  )
}
```

### Action Buttons

```typescript
import { FaEdit, FaTrash, FaSave, FaTimes } from 'react-icons/fa'

function ActionButtons() {
  return (
    <div className="flex gap-2">
      <button className="btn btn-primary">
        <FaEdit /> Edit
      </button>
      <button className="btn btn-danger">
        <FaTrash /> Delete
      </button>
      <button className="btn btn-success">
        <FaSave /> Save
      </button>
      <button className="btn btn-secondary">
        <FaTimes /> Cancel
      </button>
    </div>
  )
}
```

## Advanced Usage

### 1. Dynamic Icon Selection

```typescript
import { IconType } from 'react-icons'
import { FaHome, FaUser, FaCog } from 'react-icons/fa'

interface MenuItem {
  label: string
  icon: IconType
  href: string
}

const menuItems: MenuItem[] = [
  { label: 'Home', icon: FaHome, href: '/' },
  { label: 'Profile', icon: FaUser, href: '/profile' },
  { label: 'Settings', icon: FaCog, href: '/settings' }
]

function Menu() {
  return (
    <nav>
      {menuItems.map((item) => {
        const Icon = item.icon
        return (
          <a key={item.href} href={item.href}>
            <Icon />
            {item.label}
          </a>
        )
      })}
    </nav>
  )
}
```

### 2. Icon with Badge

```typescript
import { FaBell } from 'react-icons/fa'

interface IconWithBadgeProps {
  count: number
}

function NotificationIcon({ count }: IconWithBadgeProps) {
  return (
    <div className="relative inline-block">
      <FaBell size={24} />
      {count > 0 && (
        <span className="absolute -top-2 -right-2 bg-red-500 text-white text-xs rounded-full w-5 h-5 flex items-center justify-center">
          {count > 99 ? '99+' : count}
        </span>
      )}
    </div>
  )
}
```

### 3. Animated Icons

```typescript
import { FaSpinner, FaHeart } from 'react-icons/fa'
import { useState } from 'react'

function LoadingButton() {
  const [loading, setLoading] = useState(false)

  return (
    <button onClick={() => setLoading(!loading)}>
      {loading ? (
        <FaSpinner className="animate-spin" />
      ) : (
        <FaHeart />
      )}
      {loading ? 'Loading...' : 'Like'}
    </button>
  )
}
```

### 4. Icon Button Component

```typescript
import { IconType } from 'react-icons'

interface IconButtonProps {
  icon: IconType
  onClick: () => void
  variant?: 'primary' | 'secondary' | 'danger'
  size?: number
  label?: string
}

function IconButton({ 
  icon: Icon, 
  onClick, 
  variant = 'primary',
  size = 20,
  label 
}: IconButtonProps) {
  return (
    <button
      onClick={onClick}
      className={`icon-button icon-button-${variant}`}
      aria-label={label}
    >
      <Icon size={size} />
    </button>
  )
}

// Usage
import { FaEdit, FaTrash } from 'react-icons/fa'

function Example() {
  return (
    <div>
      <IconButton icon={FaEdit} onClick={() => {}} label="Edit" />
      <IconButton icon={FaTrash} onClick={() => {}} variant="danger" label="Delete" />
    </div>
  )
}
```

### 5. Status Icons

```typescript
import { 
  FaCheckCircle, 
  FaExclamationCircle, 
  FaInfoCircle,
  FaTimesCircle 
} from 'react-icons/fa'

type Status = 'success' | 'error' | 'warning' | 'info'

interface StatusIconProps {
  status: Status
  size?: number
}

const statusConfig = {
  success: { icon: FaCheckCircle, color: '#10b981' },
  error: { icon: FaTimesCircle, color: '#ef4444' },
  warning: { icon: FaExclamationCircle, color: '#f59e0b' },
  info: { icon: FaInfoCircle, color: '#3b82f6' }
}

function StatusIcon({ status, size = 20 }: StatusIconProps) {
  const { icon: Icon, color } = statusConfig[status]
  
  return <Icon size={size} color={color} />
}
```

### 6. Icon Grid/Picker

```typescript
import { IconType } from 'react-icons'
import { 
  FaHome, FaUser, FaCog, FaBell, FaHeart,
  FaStar, FaComment, FaShare, FaBookmark 
} from 'react-icons/fa'

const icons: IconType[] = [
  FaHome, FaUser, FaCog, FaBell, FaHeart,
  FaStar, FaComment, FaShare, FaBookmark
]

function IconPicker({ onSelect }: { onSelect: (icon: IconType) => void }) {
  return (
    <div className="grid grid-cols-3 gap-4">
      {icons.map((Icon, index) => (
        <button
          key={index}
          onClick={() => onSelect(Icon)}
          className="p-4 border rounded hover:bg-gray-100"
        >
          <Icon size={32} />
        </button>
      ))}
    </div>
  )
}
```

## Real-World Examples

### Feature List

```typescript
import { FaCheck, FaTimes } from 'react-icons/fa'

interface Feature {
  name: string
  included: boolean
}

function FeatureList({ features }: { features: Feature[] }) {
  return (
    <ul className="space-y-2">
      {features.map((feature) => (
        <li key={feature.name} className="flex items-center gap-2">
          {feature.included ? (
            <FaCheck className="text-green-500" />
          ) : (
            <FaTimes className="text-red-500" />
          )}
          <span className={feature.included ? '' : 'text-gray-400'}>
            {feature.name}
          </span>
        </li>
      ))}
    </ul>
  )
}
```

### File Type Icons

```typescript
import { 
  FaFilePdf, 
  FaFileWord, 
  FaFileExcel,
  FaFileImage,
  FaFileVideo,
  FaFile 
} from 'react-icons/fa'

function getFileIcon(filename: string) {
  const ext = filename.split('.').pop()?.toLowerCase()
  
  switch (ext) {
    case 'pdf':
      return FaFilePdf
    case 'doc':
    case 'docx':
      return FaFileWord
    case 'xls':
    case 'xlsx':
      return FaFileExcel
    case 'jpg':
    case 'jpeg':
    case 'png':
    case 'gif':
      return FaFileImage
    case 'mp4':
    case 'avi':
    case 'mov':
      return FaFileVideo
    default:
      return FaFile
  }
}

function FileItem({ filename }: { filename: string }) {
  const Icon = getFileIcon(filename)
  
  return (
    <div className="flex items-center gap-2">
      <Icon size={24} />
      <span>{filename}</span>
    </div>
  )
}
```

### Rating Component

```typescript
import { FaStar, FaStarHalfAlt, FaRegStar } from 'react-icons/fa'

interface RatingProps {
  rating: number
  maxRating?: number
  size?: number
}

function Rating({ rating, maxRating = 5, size = 20 }: RatingProps) {
  return (
    <div className="flex gap-1">
      {Array.from({ length: maxRating }, (_, i) => {
        const value = i + 1
        
        if (rating >= value) {
          return <FaStar key={i} size={size} color="#fbbf24" />
        } else if (rating >= value - 0.5) {
          return <FaStarHalfAlt key={i} size={size} color="#fbbf24" />
        } else {
          return <FaRegStar key={i} size={size} color="#d1d5db" />
        }
      })}
    </div>
  )
}
```

## Best Practices

### 1. Import Only What You Need

```typescript
// ✅ Good: Import specific icons
import { FaHome, FaUser } from 'react-icons/fa'

// ❌ Bad: Import entire library
import * as Icons from 'react-icons/fa'
```

### 2. Use IconContext for Consistency

```typescript
// ✅ Good: Set defaults with context
<IconContext.Provider value={{ size: '1.5em', className: 'icon' }}>
  <App />
</IconContext.Provider>

// ❌ Bad: Repeat props everywhere
<FaHome size="1.5em" className="icon" />
<FaUser size="1.5em" className="icon" />
```

### 3. Add Accessibility Labels

```typescript
// ✅ Good: Accessible icon button
<button aria-label="Delete item">
  <FaTrash />
</button>

// ❌ Bad: No label
<button>
  <FaTrash />
</button>
```

### 4. Use Semantic Icon Names

```typescript
// ✅ Good: Descriptive variable names
const DeleteIcon = FaTrash
const EditIcon = FaEdit

// ❌ Bad: Generic names
const Icon1 = FaTrash
const Icon2 = FaEdit
```

## Common Pitfalls

### 1. Not Tree-Shaking Properly

```typescript
// ❌ Bad: Imports entire pack
import { FaHome } from 'react-icons/fa/index.js'

// ✅ Good: Tree-shakeable import
import { FaHome } from 'react-icons/fa'
```

### 2. Forgetting Icon Size in Buttons

```typescript
// ❌ Bad: Icon too small in button
<button>
  <FaHome /> Home
</button>

// ✅ Good: Appropriate size
<button className="flex items-center gap-2">
  <FaHome size={20} /> Home
</button>
```

### 3. Not Handling Icon Color Inheritance

```typescript
// ❌ Bad: Hardcoded color
<FaHome color="blue" />

// ✅ Good: Inherit from parent
<FaHome className="text-blue-500" />
```

### 4. Using Wrong Icon Pack

```typescript
// ❌ Bad: Mixing icon styles inconsistently
import { FaHome } from 'react-icons/fa'
import { MdSettings } from 'react-icons/md'

// ✅ Good: Consistent icon pack
import { FaHome, FaCog } from 'react-icons/fa'
```

## Resources

### Official Documentation
- [React Icons Docs](https://react-icons.github.io/react-icons/)
- [React Icons GitHub](https://github.com/react-icons/react-icons)
- [Icon Search](https://react-icons.github.io/react-icons/search)

### Icon Packs Included
- Font Awesome
- Material Design Icons
- Ionicons
- Feather Icons
- Heroicons
- Bootstrap Icons
- And 15+ more

### Alternatives
- [Lucide React](./lucide-react.md) - Beautiful consistent icons
- [Heroicons](https://heroicons.com/) - Tailwind's icon set
- [Phosphor Icons](https://phosphoricons.com/) - Flexible icon family

---

**Next Steps:**
- Explore [Lucide React](./lucide-react.md) for consistent, beautiful icons
- Learn [Heroicons](https://heroicons.com/) for Tailwind integration
- Check out [Font Awesome](https://fontawesome.com/) for the full library
