# React Spring - Spring-Physics Based Animation Library for React

## Overview

React Spring is a spring-physics based animation library for React that brings your components to life with natural, fluid animations. Unlike traditional duration-based animations, React Spring uses spring physics to create more realistic and organic motion. It's highly performant, flexible, and works seamlessly with React's component model.

**Key Features:**
- Spring-physics based animations
- Interpolation and animation chaining
- Hooks-based API
- Imperative API support
- Gesture integration
- Auto-cancellation
- TypeScript support
- Small bundle size (~30KB)
- Server-side rendering support
- Native platform support (React Native)

## Why Use React Spring?

### Problems It Solves

1. **Natural Motion**: Creates physics-based animations that feel more natural than easing curves
2. **Performance**: Highly optimized, runs animations off the main thread when possible
3. **Interruption Handling**: Smoothly handles animation interruptions
4. **Complex Sequences**: Easy to create complex animation sequences
5. **Bundle Size**: Smaller than Framer Motion while still feature-rich
6. **Flexibility**: Works with any React renderer (DOM, Three.js, React Native)

### When to Use React Spring

- ✅ Physics-based animations
- ✅ Smooth, natural motion
- ✅ Interactive UI elements
- ✅ 3D animations with Three.js
- ✅ React Native apps
- ✅ When bundle size matters
- ✅ Complex animation orchestration

### When NOT to Use React Spring

- ❌ Simple CSS transitions (use CSS)
- ❌ Timeline-based animations (use GSAP)
- ❌ When you need gesture recognition (use Framer Motion)
- ❌ When you prefer declarative-only API

## Comparison with Alternatives

### React Spring vs Framer Motion

| Feature | React Spring | Framer Motion |
|---------|--------------|---------------|
| API Style | Hooks + Imperative | Declarative |
| Bundle Size | ~30KB | ~50KB |
| Physics | Spring-based | Spring + Easing |
| Gestures | Manual | Built-in |
| Learning Curve | Moderate | Easy |
| Layout Animations | Manual | Automatic |
| Best For | Physics animations | UI animations |

**Choose React Spring if:** You want realistic physics and smaller bundle size
**Choose Framer Motion if:** You want easier API and built-in gestures

### React Spring vs GSAP

| Feature | React Spring | GSAP |
|---------|--------------|------|
| React Integration | Native | Plugin needed |
| Physics | Spring-based | Easing-based |
| Timeline | Basic | Advanced |
| Bundle Size | ~30KB | ~40KB |
| API | Hooks-first | Imperative |
| Best For | React apps | Complex sequences |

## Installation & Setup

### Installation

```bash
# npm
npm install @react-spring/web

# yarn
yarn add @react-spring/web

# pnpm
pnpm add @react-spring/web
```

### For React Native

```bash
npm install @react-spring/native
```

### For Three.js

```bash
npm install @react-spring/three
```

### TypeScript Setup

React Spring has excellent built-in TypeScript support. No additional types needed!

### Basic Setup

```typescript
import { useSpring, animated } from '@react-spring/web'

function App() {
  const springs = useSpring({
    from: { opacity: 0 },
    to: { opacity: 1 },
  })

  return <animated.div style={springs}>Hello World</animated.div>
}
```

## Core Concepts

### 1. Animated Components

```typescript
import { animated } from '@react-spring/web'

// Use animated.* for any HTML element
<animated.div />
<animated.button />
<animated.span />
<animated.img />
<animated.svg />

// Or wrap custom components
const AnimatedCustom = animated(CustomComponent)
```

### 2. useSpring Hook

```typescript
import { useSpring, animated } from '@react-spring/web'

function Component() {
  const springs = useSpring({
    from: { opacity: 0, transform: 'translateY(-40px)' },
    to: { opacity: 1, transform: 'translateY(0px)' },
  })

  return <animated.div style={springs}>Content</animated.div>
}
```

### 3. Spring Configuration

```typescript
// Default config
const springs = useSpring({
  to: { opacity: 1 },
  config: { tension: 170, friction: 26 }
})

// Preset configs
import { config } from '@react-spring/web'

const springs = useSpring({
  to: { opacity: 1 },
  config: config.default  // default, gentle, wobbly, stiff, slow, molasses
})

// Custom config
const springs = useSpring({
  to: { opacity: 1 },
  config: {
    mass: 1,
    tension: 180,
    friction: 12,
    clamp: false,
    precision: 0.01,
    velocity: 0,
    easing: (t) => t
  }
})
```

### 4. useSprings Hook (Multiple Springs)

```typescript
import { useSprings, animated } from '@react-spring/web'

function List() {
  const items = ['Item 1', 'Item 2', 'Item 3']
  
  const springs = useSprings(
    items.length,
    items.map((_, i) => ({
      from: { opacity: 0, transform: 'translateX(-40px)' },
      to: { opacity: 1, transform: 'translateX(0px)' },
      delay: i * 100,
    }))
  )

  return (
    <>
      {springs.map((style, i) => (
        <animated.div key={i} style={style}>
          {items[i]}
        </animated.div>
      ))}
    </>
  )
}
```

## Basic Usage

### Simple Fade In

```typescript
import { useSpring, animated } from '@react-spring/web'

function FadeIn() {
  const springs = useSpring({
    from: { opacity: 0 },
    to: { opacity: 1 },
  })

  return <animated.div style={springs}>I fade in!</animated.div>
}
```

### Toggle Animation

```typescript
import { useSpring, animated } from '@react-spring/web'
import { useState } from 'react'

function Toggle() {
  const [flip, setFlip] = useState(false)

  const springs = useSpring({
    to: {
      opacity: flip ? 1 : 0,
      transform: flip ? 'translateY(0px)' : 'translateY(-40px)',
    },
  })

  return (
    <div>
      <animated.div style={springs}>
        I will fade in and out
      </animated.div>
      <button onClick={() => setFlip(!flip)}>Toggle</button>
    </div>
  )
}
```

### Number Animation

```typescript
import { useSpring, animated } from '@react-spring/web'
import { useEffect, useState } from 'react'

function Counter() {
  const [count, setCount] = useState(0)

  const { number } = useSpring({
    from: { number: 0 },
    number: count,
    config: { tension: 180, friction: 12 },
  })

  return (
    <div>
      <animated.div>
        {number.to(n => n.toFixed(0))}
      </animated.div>
      <button onClick={() => setCount(count + 100)}>
        Increment
      </button>
    </div>
  )
}
```

## Advanced Usage

### 1. useTrail (Staggered Animations)

```typescript
import { useTrail, animated } from '@react-spring/web'
import { useState } from 'react'

function Trail() {
  const [open, setOpen] = useState(true)
  const items = ['Item 1', 'Item 2', 'Item 3', 'Item 4']

  const trail = useTrail(items.length, {
    config: { mass: 5, tension: 2000, friction: 200 },
    opacity: open ? 1 : 0,
    x: open ? 0 : 20,
    height: open ? 80 : 0,
    from: { opacity: 0, x: 20, height: 0 },
  })

  return (
    <div>
      <button onClick={() => setOpen(!open)}>Toggle</button>
      <div>
        {trail.map((style, index) => (
          <animated.div
            key={index}
            style={{
              ...style,
              transform: style.x.to(x => `translate3d(0,${x}px,0)`),
            }}
          >
            {items[index]}
          </animated.div>
        ))}
      </div>
    </div>
  )
}
```

### 2. useChain (Sequential Animations)

```typescript
import { useSpring, useChain, useSpringRef, animated } from '@react-spring/web'
import { useState } from 'react'

function Chain() {
  const [open, setOpen] = useState(false)

  const springRef = useSpringRef()
  const springs = useSpring({
    ref: springRef,
    from: { width: '0%' },
    to: { width: open ? '100%' : '0%' },
  })

  const trailRef = useSpringRef()
  const trail = useSpring({
    ref: trailRef,
    from: { opacity: 0 },
    to: { opacity: open ? 1 : 0 },
  })

  // Chain animations: springs first, then trail
  useChain(open ? [springRef, trailRef] : [trailRef, springRef], [0, 0.3])

  return (
    <div>
      <button onClick={() => setOpen(!open)}>Toggle</button>
      <animated.div style={springs}>
        <animated.div style={trail}>Content</animated.div>
      </animated.div>
    </div>
  )
}
```

### 3. useTransition (Enter/Exit Animations)

```typescript
import { useTransition, animated } from '@react-spring/web'
import { useState } from 'react'

interface Item {
  id: number
  text: string
}

function TransitionList() {
  const [items, setItems] = useState<Item[]>([
    { id: 1, text: 'Item 1' },
    { id: 2, text: 'Item 2' },
  ])

  const transitions = useTransition(items, {
    from: { opacity: 0, transform: 'translateX(-40px)' },
    enter: { opacity: 1, transform: 'translateX(0px)' },
    leave: { opacity: 0, transform: 'translateX(40px)' },
    keys: item => item.id,
  })

  const addItem = () => {
    const id = Date.now()
    setItems([...items, { id, text: `Item ${id}` }])
  }

  const removeItem = (id: number) => {
    setItems(items.filter(item => item.id !== id))
  }

  return (
    <div>
      <button onClick={addItem}>Add Item</button>
      {transitions((style, item) => (
        <animated.div style={style}>
          {item.text}
          <button onClick={() => removeItem(item.id)}>Remove</button>
        </animated.div>
      ))}
    </div>
  )
}
```

### 4. Interpolation

```typescript
import { useSpring, animated } from '@react-spring/web'
import { useState } from 'react'

function Interpolation() {
  const [flip, setFlip] = useState(false)

  const { x } = useSpring({
    x: flip ? 1 : 0,
    config: { duration: 1000 },
  })

  return (
    <div>
      <animated.div
        style={{
          opacity: x.to({ range: [0, 1], output: [0.3, 1] }),
          transform: x
            .to({
              range: [0, 0.25, 0.5, 0.75, 1],
              output: [0, -20, 0, 20, 0],
            })
            .to(x => `translateY(${x}px)`),
          scale: x.to({ range: [0, 1], output: [1, 1.5] }),
        }}
      >
        Interpolated Animation
      </animated.div>
      <button onClick={() => setFlip(!flip)}>Toggle</button>
    </div>
  )
}
```

### 5. Imperative API

```typescript
import { useSpringValue, animated } from '@react-spring/web'

function Imperative() {
  const opacity = useSpringValue(0)

  const handleClick = async () => {
    await opacity.start(1)
    await opacity.start(0.5)
    await opacity.start(1)
  }

  return (
    <div>
      <animated.div style={{ opacity }}>
        Click the button
      </animated.div>
      <button onClick={handleClick}>Animate</button>
    </div>
  )
}
```

### 6. Gestures with useSpring

```typescript
import { useSpring, animated } from '@react-spring/web'
import { useDrag } from '@use-gesture/react'

function Draggable() {
  const [{ x, y }, api] = useSpring(() => ({ x: 0, y: 0 }))

  const bind = useDrag(({ offset: [ox, oy] }) => {
    api.start({ x: ox, y: oy })
  })

  return (
    <animated.div
      {...bind()}
      style={{
        x,
        y,
        width: 100,
        height: 100,
        background: 'linear-gradient(to right, #667eea, #764ba2)',
        borderRadius: 12,
        touchAction: 'none',
      }}
    />
  )
}
```

## Real-World Examples

### Modal with Spring Animation

```typescript
import { useTransition, animated } from '@react-spring/web'
import { useState } from 'react'

function Modal() {
  const [isOpen, setIsOpen] = useState(false)

  const transition = useTransition(isOpen, {
    from: { opacity: 0, transform: 'scale(0.8) translateY(-40px)' },
    enter: { opacity: 1, transform: 'scale(1) translateY(0px)' },
    leave: { opacity: 0, transform: 'scale(0.8) translateY(-40px)' },
    config: { tension: 300, friction: 25 },
  })

  return (
    <div>
      <button onClick={() => setIsOpen(true)}>Open Modal</button>

      {transition((style, item) =>
        item ? (
          <>
            <animated.div
              style={{
                position: 'fixed',
                inset: 0,
                background: 'rgba(0,0,0,0.5)',
                opacity: style.opacity,
              }}
              onClick={() => setIsOpen(false)}
            />
            <animated.div
              style={{
                position: 'fixed',
                top: '50%',
                left: '50%',
                transform: style.transform.to(t => `translate(-50%, -50%) ${t}`),
                opacity: style.opacity,
                background: 'white',
                padding: '32px',
                borderRadius: '12px',
                boxShadow: '0 20px 25px -5px rgba(0,0,0,0.1)',
              }}
            >
              <h2>Modal Title</h2>
              <p>Modal content goes here</p>
              <button onClick={() => setIsOpen(false)}>Close</button>
            </animated.div>
          </>
        ) : null
      )}
    </div>
  )
}
```

### Notification System

```typescript
import { useTransition, animated } from '@react-spring/web'
import { useState } from 'react'

interface Toast {
  id: number
  message: string
  type: 'success' | 'error' | 'info'
}

function ToastContainer() {
  const [toasts, setToasts] = useState<Toast[]>([])

  const transitions = useTransition(toasts, {
    from: { opacity: 0, transform: 'translateX(100%)' },
    enter: { opacity: 1, transform: 'translateX(0%)' },
    leave: { opacity: 0, transform: 'translateX(100%)' },
    keys: toast => toast.id,
  })

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
      {transitions((style, toast) => (
        <animated.div
          style={{
            ...style,
            background: toast.type === 'success' ? '#10b981' : 
                       toast.type === 'error' ? '#ef4444' : '#3b82f6',
            color: 'white',
            padding: '16px 24px',
            borderRadius: '8px',
            marginBottom: '12px',
            boxShadow: '0 4px 6px rgba(0,0,0,0.1)',
          }}
        >
          {toast.message}
        </animated.div>
      ))}

      <button onClick={() => addToast('Success!', 'success')}>
        Show Success
      </button>
    </div>
  )
}
```

### Parallax Scroll Effect

```typescript
import { useSpring, animated } from '@react-spring/web'
import { useEffect, useState } from 'react'

function Parallax() {
  const [scrollY, setScrollY] = useState(0)

  useEffect(() => {
    const handleScroll = () => setScrollY(window.scrollY)
    window.addEventListener('scroll', handleScroll)
    return () => window.removeEventListener('scroll', handleScroll)
  }, [])

  const springs = useSpring({
    y: scrollY * 0.5,
    config: { tension: 280, friction: 60 },
  })

  return (
    <div style={{ height: '200vh' }}>
      <animated.div
        style={{
          position: 'fixed',
          top: 0,
          transform: springs.y.to(y => `translateY(${y}px)`),
        }}
      >
        <h1>Parallax Effect</h1>
      </animated.div>
    </div>
  )
}
```

## Best Practices

### 1. Use Config Presets

```typescript
import { useSpring, animated, config } from '@react-spring/web'

// ✅ Good: Use presets for common animations
const springs = useSpring({
  to: { opacity: 1 },
  config: config.gentle
})

// ❌ Bad: Custom config for simple animations
const springs = useSpring({
  to: { opacity: 1 },
  config: { tension: 170, friction: 26 }
})
```

### 2. Memoize Spring Configs

```typescript
import { useSpring, animated } from '@react-spring/web'
import { useMemo } from 'react'

function Component({ data }) {
  const springConfig = useMemo(() => ({
    from: { opacity: 0 },
    to: { opacity: 1 },
  }), [])

  const springs = useSpring(springConfig)

  return <animated.div style={springs}>{data}</animated.div>
}
```

### 3. Use Refs for Imperative Control

```typescript
import { useSpringRef, useSpring, animated } from '@react-spring/web'

function Component() {
  const springRef = useSpringRef()
  
  const springs = useSpring({
    ref: springRef,
    from: { opacity: 0 },
    to: { opacity: 1 },
  })

  const handleClick = () => {
    springRef.start({ opacity: 0.5 })
  }

  return <animated.div style={springs}>Content</animated.div>
}
```

### 4. Optimize Performance

```typescript
// ✅ Good: Animate transform and opacity
const springs = useSpring({
  to: { transform: 'translateX(100px)', opacity: 0.5 }
})

// ❌ Bad: Animate layout properties
const springs = useSpring({
  to: { left: '100px', width: '200px' }
})
```

## Common Pitfalls

### 1. Not Using animated Components

```typescript
// ❌ Bad: Regular div won't animate
const springs = useSpring({ opacity: 1 })
<div style={springs}>Won't work</div>

// ✅ Good: Use animated.div
<animated.div style={springs}>Will work</animated.div>
```

### 2. Forgetting Keys in Transitions

```typescript
// ❌ Bad: No keys
const transitions = useTransition(items, {
  from: { opacity: 0 },
  enter: { opacity: 1 },
})

// ✅ Good: Provide keys
const transitions = useTransition(items, {
  from: { opacity: 0 },
  enter: { opacity: 1 },
  keys: item => item.id,
})
```

### 3. Overusing Animations

```typescript
// ❌ Bad: Too many animated properties
const springs = useSpring({
  to: {
    opacity: 1,
    transform: 'translateX(100px) rotate(45deg) scale(1.5)',
    color: '#ff0000',
    background: '#00ff00',
  }
})

// ✅ Good: Keep it simple
const springs = useSpring({
  to: { opacity: 1, transform: 'translateX(100px)' }
})
```

### 4. Not Cleaning Up Event Listeners

```typescript
// ✅ Good: Clean up listeners
useEffect(() => {
  const handleScroll = () => setScrollY(window.scrollY)
  window.addEventListener('scroll', handleScroll)
  return () => window.removeEventListener('scroll', handleScroll)
}, [])
```

## Resources

### Official Documentation
- [React Spring Docs](https://www.react-spring.dev/)
- [React Spring GitHub](https://github.com/pmndrs/react-spring)
- [API Reference](https://www.react-spring.dev/docs)

### Tutorials & Examples
- [React Spring Examples](https://www.react-spring.dev/examples)
- [CodeSandbox Examples](https://codesandbox.io/examples/package/@react-spring/web)

### Video Tutorials
- [React Spring Tutorial](https://www.youtube.com/results?search_query=react+spring+tutorial)
- [Advanced React Spring](https://www.youtube.com/results?search_query=react+spring+advanced)

### Community
- [GitHub Discussions](https://github.com/pmndrs/react-spring/discussions)
- [Discord](https://discord.gg/poimandres)
- [Stack Overflow](https://stackoverflow.com/questions/tagged/react-spring)

### Related Libraries
- [@use-gesture/react](https://use-gesture.netlify.app/) - Gesture library that works great with React Spring
- [drei](https://github.com/pmndrs/drei) - React Three Fiber helpers

---

**Next Steps:**
- Explore [Framer Motion](./framer-motion.md) for declarative animations
- Learn [GSAP](./gsap.md) for timeline-based animations
- Check out [Three.js](https://threejs.org/) for 3D animations
