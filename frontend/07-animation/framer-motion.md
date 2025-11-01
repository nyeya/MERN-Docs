# Framer Motion - Production-Ready Animation Library for React

## Overview

Framer Motion is a production-ready motion library for React that makes creating animations simple and intuitive. Built by the team behind Framer, it provides a declarative API for animations, gestures, and layout transitions. Framer Motion is designed to be powerful yet easy to use, with excellent TypeScript support and performance optimization out of the box.

**Key Features:**
- Declarative animation API
- Spring animations
- Gesture recognition (drag, tap, hover)
- Layout animations
- SVG path animations
- Scroll-triggered animations
- Variants for complex animations
- Exit animations
- Server-side rendering support
- Excellent TypeScript support

## Why Use Framer Motion?

### Problems It Solves

1. **Complex Animations**: Simplifies creating complex animation sequences
2. **Gesture Handling**: Built-in drag, tap, and hover gestures
3. **Layout Animations**: Automatic layout transitions
4. **Performance**: Optimized for 60fps animations
5. **Developer Experience**: Intuitive API with great TypeScript support
6. **Accessibility**: Respects user's motion preferences

### When to Use Framer Motion

- ✅ Interactive UI animations
- ✅ Page transitions
- ✅ Drag and drop interfaces
- ✅ Scroll-based animations
- ✅ Complex animation sequences
- ✅ Gesture-driven interactions
- ✅ SVG animations

### When NOT to Use Framer Motion

- ❌ Simple CSS transitions (use CSS)
- ❌ Game animations (use GSAP or canvas)
- ❌ Very performance-critical animations
- ❌ When bundle size is critical (<50KB gzipped)

## Comparison with Alternatives

### Framer Motion vs React Spring

| Feature | Framer Motion | React Spring |
|---------|--------------|--------------|
| API Style | Declarative | Imperative/Declarative |
| Bundle Size | ~50KB | ~30KB |
| Learning Curve | Easy | Moderate |
| Gestures | Built-in | Manual |
| Layout Animations | Excellent | Manual |
| Spring Physics | Good | Excellent |
| Best For | UI animations | Physics-based |

**Choose Framer Motion if:** You want an easy-to-use, feature-rich animation library
**Choose React Spring if:** You need advanced spring physics and smaller bundle

### Framer Motion vs GSAP

| Feature | Framer Motion | GSAP |
|---------|--------------|------|
| React Integration | Native | Plugin needed |
| API | React-first | Imperative |
| Bundle Size | ~50KB | ~40KB |
| Features | React-focused | Comprehensive |
| Timeline | Basic | Advanced |
| Best For | React apps | Complex sequences |

## Installation & Setup

### Installation

```bash
# npm
npm install framer-motion

# yarn
yarn add framer-motion

# pnpm
pnpm add framer-motion
```

### TypeScript Setup

Framer Motion has excellent built-in TypeScript support. No additional types needed!

### Basic Setup

```typescript
import { motion } from 'framer-motion'

function App() {
  return (
    <motion.div
      initial={{ opacity: 0 }}
      animate={{ opacity: 1 }}
      transition={{ duration: 0.5 }}
    >
      Hello World
    </motion.div>
  )
}
```

## Core Concepts

### 1. Motion Components

```typescript
import { motion } from 'framer-motion'

// Any HTML element can be animated
<motion.div />
<motion.button />
<motion.span />
<motion.img />
<motion.svg />

// Basic animation
<motion.div
  initial={{ x: -100 }}
  animate={{ x: 0 }}
  transition={{ duration: 0.5 }}
>
  Slides in from left
</motion.div>
```

### 2. Animation Props

```typescript
// initial: Starting state
// animate: Target state
// exit: Exit state (requires AnimatePresence)
// transition: Animation configuration

<motion.div
  initial={{ opacity: 0, scale: 0.5 }}
  animate={{ opacity: 1, scale: 1 }}
  exit={{ opacity: 0, scale: 0.5 }}
  transition={{ duration: 0.3 }}
/>
```

### 3. Variants

```typescript
const variants = {
  hidden: { opacity: 0, y: 50 },
  visible: { opacity: 1, y: 0 },
  exit: { opacity: 0, y: -50 }
}

<motion.div
  variants={variants}
  initial="hidden"
  animate="visible"
  exit="exit"
/>

// Variants with children
const containerVariants = {
  hidden: { opacity: 0 },
  visible: {
    opacity: 1,
    transition: {
      staggerChildren: 0.1
    }
  }
}

const itemVariants = {
  hidden: { opacity: 0, y: 20 },
  visible: { opacity: 1, y: 0 }
}

<motion.ul variants={containerVariants} initial="hidden" animate="visible">
  <motion.li variants={itemVariants}>Item 1</motion.li>
  <motion.li variants={itemVariants}>Item 2</motion.li>
  <motion.li variants={itemVariants}>Item 3</motion.li>
</motion.ul>
```

### 4. Gestures

```typescript
<motion.button
  whileHover={{ scale: 1.1 }}
  whileTap={{ scale: 0.95 }}
  whileFocus={{ outline: '2px solid blue' }}
>
  Click me
</motion.button>

// Drag
<motion.div
  drag
  dragConstraints={{ left: -100, right: 100, top: -100, bottom: 100 }}
  dragElastic={0.2}
  onDragStart={() => console.log('Drag started')}
  onDragEnd={() => console.log('Drag ended')}
/>
```

## Basic Usage

### Simple Fade In Animation

```typescript
import { motion } from 'framer-motion'

function FadeIn() {
  return (
    <motion.div
      initial={{ opacity: 0 }}
      animate={{ opacity: 1 }}
      transition={{ duration: 0.5 }}
    >
      I fade in!
    </motion.div>
  )
}
```

### Button with Hover Effect

```typescript
function AnimatedButton() {
  return (
    <motion.button
      whileHover={{ 
        scale: 1.05,
        boxShadow: '0px 5px 10px rgba(0,0,0,0.2)'
      }}
      whileTap={{ scale: 0.95 }}
      transition={{ type: 'spring', stiffness: 400, damping: 17 }}
      style={{
        padding: '12px 24px',
        fontSize: '16px',
        border: 'none',
        borderRadius: '8px',
        background: 'linear-gradient(to right, #667eea, #764ba2)',
        color: 'white',
        cursor: 'pointer'
      }}
    >
      Hover me
    </motion.button>
  )
}
```

### List Animation with Stagger

```typescript
const container = {
  hidden: { opacity: 0 },
  show: {
    opacity: 1,
    transition: {
      staggerChildren: 0.1
    }
  }
}

const item = {
  hidden: { opacity: 0, x: -20 },
  show: { opacity: 1, x: 0 }
}

function List() {
  const items = ['Item 1', 'Item 2', 'Item 3', 'Item 4']

  return (
    <motion.ul
      variants={container}
      initial="hidden"
      animate="show"
    >
      {items.map((text, index) => (
        <motion.li key={index} variants={item}>
          {text}
        </motion.li>
      ))}
    </motion.ul>
  )
}
```

## Advanced Usage

### 1. AnimatePresence for Exit Animations

```typescript
import { motion, AnimatePresence } from 'framer-motion'
import { useState } from 'react'

function Modal() {
  const [isOpen, setIsOpen] = useState(false)

  return (
    <>
      <button onClick={() => setIsOpen(true)}>Open Modal</button>

      <AnimatePresence>
        {isOpen && (
          <>
            <motion.div
              initial={{ opacity: 0 }}
              animate={{ opacity: 1 }}
              exit={{ opacity: 0 }}
              onClick={() => setIsOpen(false)}
              style={{
                position: 'fixed',
                inset: 0,
                background: 'rgba(0,0,0,0.5)',
                zIndex: 50
              }}
            />
            <motion.div
              initial={{ opacity: 0, scale: 0.75, y: -100 }}
              animate={{ opacity: 1, scale: 1, y: 0 }}
              exit={{ opacity: 0, scale: 0.75, y: -100 }}
              transition={{ type: 'spring', damping: 25, stiffness: 300 }}
              style={{
                position: 'fixed',
                top: '50%',
                left: '50%',
                transform: 'translate(-50%, -50%)',
                background: 'white',
                padding: '32px',
                borderRadius: '12px',
                zIndex: 51
              }}
            >
              <h2>Modal Title</h2>
              <p>Modal content goes here</p>
              <button onClick={() => setIsOpen(false)}>Close</button>
            </motion.div>
          </>
        )}
      </AnimatePresence>
    </>
  )
}
```

### 2. Layout Animations

```typescript
import { motion } from 'framer-motion'
import { useState } from 'react'

function LayoutAnimation() {
  const [isExpanded, setIsExpanded] = useState(false)

  return (
    <motion.div
      layout
      onClick={() => setIsExpanded(!isExpanded)}
      style={{
        background: 'linear-gradient(to right, #667eea, #764ba2)',
        borderRadius: '12px',
        padding: '20px',
        cursor: 'pointer'
      }}
    >
      <motion.h2 layout>Expandable Card</motion.h2>
      {isExpanded && (
        <motion.div
          initial={{ opacity: 0 }}
          animate={{ opacity: 1 }}
          exit={{ opacity: 0 }}
        >
          <p>This content appears when expanded</p>
        </motion.div>
      )}
    </motion.div>
  )
}
```

### 3. Drag and Drop

```typescript
import { motion } from 'framer-motion'
import { useRef } from 'react'

function DragBox() {
  const constraintsRef = useRef(null)

  return (
    <div ref={constraintsRef} style={{ width: '400px', height: '400px', border: '2px solid #ccc' }}>
      <motion.div
        drag
        dragConstraints={constraintsRef}
        dragElastic={0.1}
        dragTransition={{ bounceStiffness: 600, bounceDamping: 20 }}
        whileDrag={{ scale: 1.1, cursor: 'grabbing' }}
        style={{
          width: '100px',
          height: '100px',
          background: 'linear-gradient(to right, #667eea, #764ba2)',
          borderRadius: '12px',
          cursor: 'grab'
        }}
      />
    </div>
  )
}
```

### 4. Scroll Animations

```typescript
import { motion, useScroll, useTransform } from 'framer-motion'
import { useRef } from 'react'

function ScrollAnimation() {
  const ref = useRef(null)
  const { scrollYProgress } = useScroll({
    target: ref,
    offset: ['start end', 'end start']
  })

  const opacity = useTransform(scrollYProgress, [0, 0.5, 1], [0, 1, 0])
  const scale = useTransform(scrollYProgress, [0, 0.5, 1], [0.8, 1, 0.8])

  return (
    <motion.div
      ref={ref}
      style={{ opacity, scale }}
    >
      <h2>Scroll to see animation</h2>
    </motion.div>
  )
}
```

### 5. Path Animations

```typescript
import { motion } from 'framer-motion'

function PathAnimation() {
  const draw = {
    hidden: { pathLength: 0, opacity: 0 },
    visible: {
      pathLength: 1,
      opacity: 1,
      transition: {
        pathLength: { type: 'spring', duration: 1.5, bounce: 0 },
        opacity: { duration: 0.01 }
      }
    }
  }

  return (
    <motion.svg
      width="200"
      height="200"
      viewBox="0 0 200 200"
      initial="hidden"
      animate="visible"
    >
      <motion.circle
        cx="100"
        cy="100"
        r="80"
        stroke="#764ba2"
        strokeWidth="4"
        fill="transparent"
        variants={draw}
      />
    </motion.svg>
  )
}
```

### 6. useAnimation Hook

```typescript
import { motion, useAnimation } from 'framer-motion'
import { useEffect } from 'react'

function ControlledAnimation() {
  const controls = useAnimation()

  useEffect(() => {
    controls.start({
      x: 100,
      transition: { duration: 2 }
    })
  }, [controls])

  const handleClick = async () => {
    await controls.start({ scale: 1.5 })
    await controls.start({ scale: 1 })
    await controls.start({ rotate: 360 })
  }

  return (
    <div>
      <motion.div
        animate={controls}
        style={{
          width: '100px',
          height: '100px',
          background: '#667eea',
          borderRadius: '12px'
        }}
      />
      <button onClick={handleClick}>Animate</button>
    </div>
  )
}
```

## Real-World Examples

### Page Transition

```typescript
import { motion, AnimatePresence } from 'framer-motion'
import { useLocation } from 'react-router-dom'

const pageVariants = {
  initial: {
    opacity: 0,
    x: -100
  },
  animate: {
    opacity: 1,
    x: 0
  },
  exit: {
    opacity: 0,
    x: 100
  }
}

const pageTransition = {
  type: 'tween',
  ease: 'anticipate',
  duration: 0.5
}

function AnimatedRoutes() {
  const location = useLocation()

  return (
    <AnimatePresence mode="wait">
      <motion.div
        key={location.pathname}
        variants={pageVariants}
        initial="initial"
        animate="animate"
        exit="exit"
        transition={pageTransition}
      >
        {/* Your page content */}
      </motion.div>
    </AnimatePresence>
  )
}
```

### Notification Toast

```typescript
import { motion, AnimatePresence } from 'framer-motion'
import { useState } from 'react'

interface Toast {
  id: number
  message: string
  type: 'success' | 'error' | 'info'
}

function ToastContainer() {
  const [toasts, setToasts] = useState<Toast[]>([])

  const addToast = (message: string, type: Toast['type']) => {
    const id = Date.now()
    setToasts(prev => [...prev, { id, message, type }])
    setTimeout(() => removeToast(id), 3000)
  }

  const removeToast = (id: number) => {
    setToasts(prev => prev.filter(toast => toast.id !== id))
  }

  return (
    <div style={{ position: 'fixed', top: 20, right: 20, zIndex: 1000 }}>
      <AnimatePresence>
        {toasts.map(toast => (
          <motion.div
            key={toast.id}
            initial={{ opacity: 0, y: -50, scale: 0.3 }}
            animate={{ opacity: 1, y: 0, scale: 1 }}
            exit={{ opacity: 0, scale: 0.5, transition: { duration: 0.2 } }}
            style={{
              background: toast.type === 'success' ? '#10b981' : 
                         toast.type === 'error' ? '#ef4444' : '#3b82f6',
              color: 'white',
              padding: '16px 24px',
              borderRadius: '8px',
              marginBottom: '12px',
              boxShadow: '0 4px 6px rgba(0,0,0,0.1)'
            }}
          >
            {toast.message}
          </motion.div>
        ))}
      </AnimatePresence>

      <button onClick={() => addToast('Success!', 'success')}>
        Show Success
      </button>
    </div>
  )
}
```

### Accordion

```typescript
import { motion, AnimatePresence } from 'framer-motion'
import { useState } from 'react'

interface AccordionItem {
  id: number
  title: string
  content: string
}

function Accordion() {
  const [expandedId, setExpandedId] = useState<number | null>(null)

  const items: AccordionItem[] = [
    { id: 1, title: 'Section 1', content: 'Content for section 1' },
    { id: 2, title: 'Section 2', content: 'Content for section 2' },
    { id: 3, title: 'Section 3', content: 'Content for section 3' }
  ]

  return (
    <div>
      {items.map(item => (
        <div key={item.id} style={{ marginBottom: '8px' }}>
          <motion.button
            onClick={() => setExpandedId(expandedId === item.id ? null : item.id)}
            style={{
              width: '100%',
              padding: '16px',
              background: '#f3f4f6',
              border: 'none',
              borderRadius: '8px',
              textAlign: 'left',
              cursor: 'pointer',
              fontSize: '16px',
              fontWeight: 'bold'
            }}
          >
            {item.title}
          </motion.button>

          <AnimatePresence>
            {expandedId === item.id && (
              <motion.div
                initial={{ height: 0, opacity: 0 }}
                animate={{ height: 'auto', opacity: 1 }}
                exit={{ height: 0, opacity: 0 }}
                transition={{ duration: 0.3 }}
                style={{ overflow: 'hidden' }}
              >
                <div style={{ padding: '16px', background: 'white' }}>
                  {item.content}
                </div>
              </motion.div>
            )}
          </AnimatePresence>
        </div>
      ))}
    </div>
  )
}
```

## Best Practices

### 1. Use Variants for Complex Animations

```typescript
// ✅ Good: Variants for reusability
const variants = {
  hidden: { opacity: 0, y: 20 },
  visible: { opacity: 1, y: 0 }
}

<motion.div variants={variants} initial="hidden" animate="visible" />

// ❌ Bad: Inline values everywhere
<motion.div initial={{ opacity: 0, y: 20 }} animate={{ opacity: 1, y: 0 }} />
```

### 2. Respect User Preferences

```typescript
// ✅ Good: Respect prefers-reduced-motion
import { useReducedMotion } from 'framer-motion'

function Component() {
  const shouldReduceMotion = useReducedMotion()

  return (
    <motion.div
      initial={{ opacity: 0 }}
      animate={{ opacity: 1 }}
      transition={{ duration: shouldReduceMotion ? 0 : 0.5 }}
    />
  )
}
```

### 3. Use Layout Animations Wisely

```typescript
// ✅ Good: Layout prop for smooth transitions
<motion.div layout>
  {items.map(item => <Item key={item.id} />)}
</motion.div>

// ❌ Bad: Manual position animations
```

### 4. Optimize Performance

```typescript
// ✅ Good: Use transform and opacity
<motion.div
  animate={{ x: 100, opacity: 0.5 }}
/>

// ❌ Bad: Animating layout properties
<motion.div
  animate={{ left: '100px', top: '50px' }}
/>
```

### 5. Use AnimatePresence for Exit Animations

```typescript
// ✅ Good: Wrap with AnimatePresence
<AnimatePresence>
  {isVisible && <motion.div exit={{ opacity: 0 }} />}
</AnimatePresence>

// ❌ Bad: Exit animation won't work
{isVisible && <motion.div exit={{ opacity: 0 }} />}
```

## Common Pitfalls

### 1. Forgetting AnimatePresence

```typescript
// ❌ Bad: Exit animation won't work
{show && <motion.div exit={{ opacity: 0 }}>Content</motion.div>}

// ✅ Good: Wrap with AnimatePresence
<AnimatePresence>
  {show && <motion.div exit={{ opacity: 0 }}>Content</motion.div>}
</AnimatePresence>
```

### 2. Animating Non-Transform Properties

```typescript
// ❌ Bad: Poor performance
<motion.div animate={{ width: '100px', height: '100px' }} />

// ✅ Good: Use transform
<motion.div animate={{ scale: 1.5 }} />
```

### 3. Not Using Keys in Lists

```typescript
// ❌ Bad: No key
<AnimatePresence>
  {items.map(item => <motion.div>{item}</motion.div>)}
</AnimatePresence>

// ✅ Good: Unique keys
<AnimatePresence>
  {items.map(item => <motion.div key={item.id}>{item}</motion.div>)}
</AnimatePresence>
```

### 4. Overusing Animations

```typescript
// ❌ Bad: Too many animations
<motion.div
  whileHover={{ scale: 1.1, rotate: 5, x: 10, y: -5 }}
  whileTap={{ scale: 0.9, rotate: -5 }}
  animate={{ opacity: [0, 1, 0.5, 1] }}
/>

// ✅ Good: Subtle, purposeful animations
<motion.div whileHover={{ scale: 1.05 }} whileTap={{ scale: 0.95 }} />
```

## Resources

### Official Documentation
- [Framer Motion Docs](https://www.framer.com/motion/)
- [Framer Motion GitHub](https://github.com/framer/motion)
- [API Reference](https://www.framer.com/motion/component/)

### Tutorials & Examples
- [Framer Motion Examples](https://www.framer.com/motion/examples/)
- [Motion One](https://motion.dev/) - Web Animations API wrapper by same team

### Video Tutorials
- [Framer Motion Crash Course](https://www.youtube.com/results?search_query=framer+motion+crash+course)
- [Advanced Framer Motion](https://www.youtube.com/results?search_query=framer+motion+advanced)

### Community
- [Framer Discord](https://www.framer.com/community/)
- [GitHub Discussions](https://github.com/framer/motion/discussions)
- [Stack Overflow](https://stackoverflow.com/questions/tagged/framer-motion)

### Tools & Resources
- [Framer](https://www.framer.com/) - Design tool with Motion integration
- [Motion DevTools](https://www.framer.com/motion/devtools/)

---

**Next Steps:**
- Explore [React Spring](./react-spring.md) for physics-based animations
- Learn [GSAP](./gsap.md) for complex timeline animations
- Check out [Tailwind CSS](../04-styling/tailwind-css.md) for styling
