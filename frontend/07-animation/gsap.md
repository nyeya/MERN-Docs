# GSAP - Professional-Grade Animation Platform

## Overview

GSAP (GreenSock Animation Platform) is a professional-grade JavaScript animation library that's been the industry standard for over a decade. It's incredibly powerful, performant, and works with any framework including React. GSAP excels at creating complex animation sequences, timelines, and scroll-triggered animations with precise control.

**Key Features:**
- Timeline-based animations
- Scroll-triggered animations (ScrollTrigger)
- Morphing and path animations
- Draggable elements
- Text animations (SplitText)
- Physics-based motion (Inertia)
- Cross-browser compatibility
- Excellent performance
- Rich plugin ecosystem
- Works with SVG, Canvas, WebGL

## Why Use GSAP?

### Problems It Solves

1. **Complex Sequences**: Create intricate animation timelines with ease
2. **Scroll Animations**: Advanced scroll-triggered effects
3. **Performance**: Optimized for 60fps animations
4. **Cross-browser**: Works consistently across all browsers
5. **SVG Animation**: Best-in-class SVG animation support
6. **Timeline Control**: Pause, reverse, seek animations precisely
7. **Professional Features**: Advanced easing, morphing, and effects

### When to Use GSAP

- ✅ Complex animation sequences
- ✅ Scroll-triggered animations
- ✅ SVG animations and morphing
- ✅ Timeline-based animations
- ✅ Professional landing pages
- ✅ Interactive storytelling
- ✅ Game UI animations
- ✅ Award-winning websites

### When NOT to Use GSAP

- ❌ Simple CSS transitions (use CSS)
- ❌ Basic React UI animations (use Framer Motion)
- ❌ When bundle size is critical
- ❌ Simple hover effects

## Comparison with Alternatives

### GSAP vs Framer Motion

| Feature | GSAP | Framer Motion |
|---------|------|---------------|
| API Style | Imperative | Declarative |
| React Integration | Plugin needed | Native |
| Timeline | Advanced | Basic |
| Bundle Size | ~40KB | ~50KB |
| Scroll Animations | ScrollTrigger | Manual |
| Learning Curve | Moderate | Easy |
| Best For | Complex sequences | React UI |

**Choose GSAP if:** You need advanced timelines and scroll effects
**Choose Framer Motion if:** You want React-first declarative API

### GSAP vs React Spring

| Feature | GSAP | React Spring |
|---------|------|--------------|
| Physics | Easing-based | Spring-based |
| Timeline | Advanced | Basic |
| React Integration | Plugin needed | Native |
| Bundle Size | ~40KB | ~30KB |
| API | Imperative | Hooks |
| Best For | Sequences | Physics motion |

## Installation & Setup

### Installation

```bash
# npm
npm install gsap

# yarn
yarn add gsap

# pnpm
pnpm add gsap
```

### TypeScript Setup

```bash
npm install @types/gsap --save-dev
```

### Basic Setup

```typescript
import { useEffect, useRef } from 'react'
import gsap from 'gsap'

function App() {
  const boxRef = useRef<HTMLDivElement>(null)

  useEffect(() => {
    gsap.to(boxRef.current, {
      x: 100,
      duration: 1,
      ease: 'power2.out'
    })
  }, [])

  return <div ref={boxRef}>Animated Box</div>
}
```

### ScrollTrigger Plugin

```typescript
import { useEffect, useRef } from 'react'
import gsap from 'gsap'
import { ScrollTrigger } from 'gsap/ScrollTrigger'

gsap.registerPlugin(ScrollTrigger)

function ScrollAnimation() {
  const boxRef = useRef<HTMLDivElement>(null)

  useEffect(() => {
    gsap.to(boxRef.current, {
      scrollTrigger: {
        trigger: boxRef.current,
        start: 'top center',
        end: 'bottom center',
        scrub: true,
      },
      x: 400,
    })
  }, [])

  return <div ref={boxRef}>Scroll to animate</div>
}
```

## Core Concepts

### 1. Tweens

```typescript
import gsap from 'gsap'

// .to() - Animate TO these values
gsap.to('.box', { x: 100, duration: 1 })

// .from() - Animate FROM these values
gsap.from('.box', { opacity: 0, duration: 1 })

// .fromTo() - Animate FROM and TO
gsap.fromTo('.box', 
  { opacity: 0, x: -100 },
  { opacity: 1, x: 0, duration: 1 }
)

// .set() - Set values immediately (no animation)
gsap.set('.box', { x: 100, opacity: 0 })
```

### 2. Timeline

```typescript
import gsap from 'gsap'

const tl = gsap.timeline()

tl.to('.box1', { x: 100, duration: 1 })
  .to('.box2', { y: 100, duration: 1 })
  .to('.box3', { rotation: 360, duration: 1 })

// With position parameter
const tl2 = gsap.timeline()

tl2.to('.box1', { x: 100, duration: 1 })
   .to('.box2', { y: 100, duration: 1 }, '-=0.5') // Start 0.5s before previous ends
   .to('.box3', { rotation: 360, duration: 1 }, '<') // Start at same time as previous
```

### 3. Easing

```typescript
import gsap from 'gsap'

// Power eases
gsap.to('.box', { x: 100, ease: 'power1.out' })  // power1, power2, power3, power4
gsap.to('.box', { x: 100, ease: 'power2.inOut' })

// Back eases (overshoot)
gsap.to('.box', { x: 100, ease: 'back.out(1.7)' })

// Elastic eases
gsap.to('.box', { x: 100, ease: 'elastic.out(1, 0.3)' })

// Bounce
gsap.to('.box', { y: 100, ease: 'bounce.out' })

// Custom ease
gsap.to('.box', { x: 100, ease: 'steps(12)' })
```

### 4. Special Properties

```typescript
// Stagger
gsap.to('.box', {
  x: 100,
  duration: 1,
  stagger: 0.1  // 0.1s delay between each element
})

// Stagger with advanced options
gsap.to('.box', {
  x: 100,
  duration: 1,
  stagger: {
    each: 0.1,
    from: 'center',  // start, center, end, edges, random
    grid: 'auto',
    ease: 'power2.inOut'
  }
})

// Repeat
gsap.to('.box', {
  x: 100,
  duration: 1,
  repeat: 2,        // repeat 2 times (total 3 plays)
  repeatDelay: 0.5, // delay between repeats
  yoyo: true        // reverse on alternate repeats
})
```

## Basic Usage

### Simple Animation with React

```typescript
import { useEffect, useRef } from 'react'
import gsap from 'gsap'

function FadeIn() {
  const boxRef = useRef<HTMLDivElement>(null)

  useEffect(() => {
    gsap.from(boxRef.current, {
      opacity: 0,
      y: 50,
      duration: 1,
      ease: 'power3.out'
    })
  }, [])

  return (
    <div ref={boxRef} style={{ padding: '20px', background: '#667eea' }}>
      I fade in!
    </div>
  )
}
```

### Hover Animation

```typescript
import { useRef } from 'react'
import gsap from 'gsap'

function HoverButton() {
  const buttonRef = useRef<HTMLButtonElement>(null)

  const handleMouseEnter = () => {
    gsap.to(buttonRef.current, {
      scale: 1.1,
      duration: 0.3,
      ease: 'power2.out'
    })
  }

  const handleMouseLeave = () => {
    gsap.to(buttonRef.current, {
      scale: 1,
      duration: 0.3,
      ease: 'power2.out'
    })
  }

  return (
    <button
      ref={buttonRef}
      onMouseEnter={handleMouseEnter}
      onMouseLeave={handleMouseLeave}
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
    </button>
  )
}
```

### Staggered List Animation

```typescript
import { useEffect, useRef } from 'react'
import gsap from 'gsap'

function StaggeredList() {
  const listRef = useRef<HTMLUListElement>(null)

  useEffect(() => {
    const items = listRef.current?.querySelectorAll('li')
    
    gsap.from(items, {
      opacity: 0,
      y: 20,
      duration: 0.5,
      stagger: 0.1,
      ease: 'power2.out'
    })
  }, [])

  return (
    <ul ref={listRef}>
      <li>Item 1</li>
      <li>Item 2</li>
      <li>Item 3</li>
      <li>Item 4</li>
    </ul>
  )
}
```

## Advanced Usage

### 1. Timeline with React

```typescript
import { useEffect, useRef } from 'react'
import gsap from 'gsap'

function TimelineAnimation() {
  const containerRef = useRef<HTMLDivElement>(null)
  const tlRef = useRef<gsap.core.Timeline>()

  useEffect(() => {
    const ctx = gsap.context(() => {
      tlRef.current = gsap.timeline()
        .from('.title', {
          opacity: 0,
          y: -50,
          duration: 0.8,
          ease: 'power3.out'
        })
        .from('.subtitle', {
          opacity: 0,
          y: -30,
          duration: 0.6,
          ease: 'power2.out'
        }, '-=0.4')
        .from('.button', {
          opacity: 0,
          scale: 0.5,
          duration: 0.5,
          ease: 'back.out(1.7)'
        }, '-=0.2')
    }, containerRef)

    return () => ctx.revert()
  }, [])

  const handleReplay = () => {
    tlRef.current?.restart()
  }

  return (
    <div ref={containerRef}>
      <h1 className="title">Welcome</h1>
      <p className="subtitle">This is a timeline animation</p>
      <button className="button" onClick={handleReplay}>
        Replay
      </button>
    </div>
  )
}
```

### 2. ScrollTrigger

```typescript
import { useEffect, useRef } from 'react'
import gsap from 'gsap'
import { ScrollTrigger } from 'gsap/ScrollTrigger'

gsap.registerPlugin(ScrollTrigger)

function ScrollAnimation() {
  const sectionRef = useRef<HTMLDivElement>(null)
  const boxRef = useRef<HTMLDivElement>(null)

  useEffect(() => {
    const ctx = gsap.context(() => {
      gsap.to(boxRef.current, {
        x: 400,
        rotation: 360,
        scrollTrigger: {
          trigger: sectionRef.current,
          start: 'top center',
          end: 'bottom center',
          scrub: 1,
          markers: true, // Remove in production
          pin: true,
        }
      })
    }, sectionRef)

    return () => ctx.revert()
  }, [])

  return (
    <div style={{ height: '200vh' }}>
      <div ref={sectionRef} style={{ height: '100vh' }}>
        <div
          ref={boxRef}
          style={{
            width: 100,
            height: 100,
            background: '#667eea',
            borderRadius: 12
          }}
        />
      </div>
    </div>
  )
}
```

### 3. Parallax Effect

```typescript
import { useEffect, useRef } from 'react'
import gsap from 'gsap'
import { ScrollTrigger } from 'gsap/ScrollTrigger'

gsap.registerPlugin(ScrollTrigger)

function Parallax() {
  const containerRef = useRef<HTMLDivElement>(null)

  useEffect(() => {
    const ctx = gsap.context(() => {
      gsap.to('.layer-1', {
        y: -100,
        scrollTrigger: {
          trigger: containerRef.current,
          start: 'top top',
          end: 'bottom top',
          scrub: true
        }
      })

      gsap.to('.layer-2', {
        y: -200,
        scrollTrigger: {
          trigger: containerRef.current,
          start: 'top top',
          end: 'bottom top',
          scrub: true
        }
      })
    }, containerRef)

    return () => ctx.revert()
  }, [])

  return (
    <div ref={containerRef} style={{ height: '200vh', position: 'relative' }}>
      <div className="layer-1" style={{ position: 'absolute', top: 0 }}>
        <h1>Layer 1</h1>
      </div>
      <div className="layer-2" style={{ position: 'absolute', top: 100 }}>
        <h2>Layer 2</h2>
      </div>
    </div>
  )
}
```

### 4. SVG Path Animation

```typescript
import { useEffect, useRef } from 'react'
import gsap from 'gsap'

function PathAnimation() {
  const pathRef = useRef<SVGPathElement>(null)

  useEffect(() => {
    const path = pathRef.current
    if (!path) return

    const length = path.getTotalLength()
    
    gsap.set(path, {
      strokeDasharray: length,
      strokeDashoffset: length
    })

    gsap.to(path, {
      strokeDashoffset: 0,
      duration: 2,
      ease: 'power2.inOut'
    })
  }, [])

  return (
    <svg width="200" height="200" viewBox="0 0 200 200">
      <path
        ref={pathRef}
        d="M 10 100 Q 100 10, 190 100 T 190 100"
        stroke="#764ba2"
        strokeWidth="4"
        fill="transparent"
      />
    </svg>
  )
}
```

### 5. Text Animation (SplitText alternative)

```typescript
import { useEffect, useRef } from 'react'
import gsap from 'gsap'

function TextReveal() {
  const textRef = useRef<HTMLHeadingElement>(null)

  useEffect(() => {
    const text = textRef.current
    if (!text) return

    // Split text into characters
    const chars = text.textContent?.split('') || []
    text.innerHTML = chars.map(char => 
      `<span style="display: inline-block">${char === ' ' ? '&nbsp;' : char}</span>`
    ).join('')

    gsap.from(text.children, {
      opacity: 0,
      y: 50,
      rotationX: -90,
      stagger: 0.05,
      duration: 0.8,
      ease: 'back.out(1.7)'
    })
  }, [])

  return <h1 ref={textRef}>Animated Text</h1>
}
```

### 6. Draggable with GSAP

```typescript
import { useEffect, useRef } from 'react'
import gsap from 'gsap'
import { Draggable } from 'gsap/Draggable'

gsap.registerPlugin(Draggable)

function DraggableBox() {
  const boxRef = useRef<HTMLDivElement>(null)

  useEffect(() => {
    Draggable.create(boxRef.current, {
      type: 'x,y',
      bounds: window,
      inertia: true,
      onDragEnd: function() {
        gsap.to(this.target, {
          scale: 1,
          duration: 0.3
        })
      },
      onDrag: function() {
        gsap.to(this.target, {
          scale: 1.1,
          duration: 0.3
        })
      }
    })
  }, [])

  return (
    <div
      ref={boxRef}
      style={{
        width: 100,
        height: 100,
        background: 'linear-gradient(to right, #667eea, #764ba2)',
        borderRadius: 12,
        cursor: 'grab'
      }}
    />
  )
}
```

## Real-World Examples

### Hero Section Animation

```typescript
import { useEffect, useRef } from 'react'
import gsap from 'gsap'

function HeroSection() {
  const heroRef = useRef<HTMLDivElement>(null)

  useEffect(() => {
    const ctx = gsap.context(() => {
      const tl = gsap.timeline({ defaults: { ease: 'power3.out' } })

      tl.from('.hero-title', {
        opacity: 0,
        y: 100,
        duration: 1
      })
      .from('.hero-subtitle', {
        opacity: 0,
        y: 50,
        duration: 0.8
      }, '-=0.5')
      .from('.hero-cta', {
        opacity: 0,
        scale: 0.5,
        duration: 0.6,
        ease: 'back.out(1.7)'
      }, '-=0.4')
      .from('.hero-image', {
        opacity: 0,
        x: 100,
        duration: 1
      }, '-=0.8')
    }, heroRef)

    return () => ctx.revert()
  }, [])

  return (
    <div ref={heroRef} style={{ padding: '100px 20px' }}>
      <h1 className="hero-title">Welcome to Our Product</h1>
      <p className="hero-subtitle">The best solution for your needs</p>
      <button className="hero-cta">Get Started</button>
      <img className="hero-image" src="/hero.png" alt="Hero" />
    </div>
  )
}
```

### Scroll-Triggered Sections

```typescript
import { useEffect, useRef } from 'react'
import gsap from 'gsap'
import { ScrollTrigger } from 'gsap/ScrollTrigger'

gsap.registerPlugin(ScrollTrigger)

function ScrollSections() {
  const sectionsRef = useRef<HTMLDivElement>(null)

  useEffect(() => {
    const ctx = gsap.context(() => {
      const sections = gsap.utils.toArray<HTMLElement>('.section')

      sections.forEach((section, i) => {
        gsap.from(section, {
          opacity: 0,
          y: 100,
          duration: 1,
          scrollTrigger: {
            trigger: section,
            start: 'top 80%',
            end: 'top 50%',
            scrub: 1,
            toggleActions: 'play none none reverse'
          }
        })
      })
    }, sectionsRef)

    return () => ctx.revert()
  }, [])

  return (
    <div ref={sectionsRef}>
      <div className="section" style={{ height: '100vh', background: '#f0f0f0' }}>
        <h2>Section 1</h2>
      </div>
      <div className="section" style={{ height: '100vh', background: '#e0e0e0' }}>
        <h2>Section 2</h2>
      </div>
      <div className="section" style={{ height: '100vh', background: '#d0d0d0' }}>
        <h2>Section 3</h2>
      </div>
    </div>
  )
}
```

### Loading Animation

```typescript
import { useEffect, useRef, useState } from 'react'
import gsap from 'gsap'

function LoadingAnimation() {
  const [isLoading, setIsLoading] = useState(true)
  const loaderRef = useRef<HTMLDivElement>(null)
  const contentRef = useRef<HTMLDivElement>(null)

  useEffect(() => {
    const ctx = gsap.context(() => {
      const tl = gsap.timeline()

      // Loading animation
      tl.to('.loader-bar', {
        width: '100%',
        duration: 2,
        ease: 'power2.inOut',
        onComplete: () => setIsLoading(false)
      })

      // Exit animation
      if (!isLoading) {
        gsap.to(loaderRef.current, {
          y: '-100%',
          duration: 0.8,
          ease: 'power3.inOut'
        })

        gsap.from(contentRef.current, {
          opacity: 0,
          y: 50,
          duration: 1,
          delay: 0.5,
          ease: 'power3.out'
        })
      }
    })

    return () => ctx.revert()
  }, [isLoading])

  return (
    <>
      {isLoading && (
        <div
          ref={loaderRef}
          style={{
            position: 'fixed',
            inset: 0,
            background: '#667eea',
            display: 'flex',
            alignItems: 'center',
            justifyContent: 'center',
            zIndex: 1000
          }}
        >
          <div style={{ width: '300px', height: '4px', background: 'rgba(255,255,255,0.2)' }}>
            <div
              className="loader-bar"
              style={{ height: '100%', width: '0%', background: 'white' }}
            />
          </div>
        </div>
      )}
      <div ref={contentRef}>
        <h1>Content Loaded!</h1>
      </div>
    </>
  )
}
```

## Best Practices

### 1. Use gsap.context for React

```typescript
// ✅ Good: Use context for automatic cleanup
useEffect(() => {
  const ctx = gsap.context(() => {
    gsap.to('.box', { x: 100 })
  }, containerRef)

  return () => ctx.revert()
}, [])

// ❌ Bad: Manual cleanup
useEffect(() => {
  const tween = gsap.to('.box', { x: 100 })
  return () => tween.kill()
}, [])
```

### 2. Use Refs Instead of Class Selectors

```typescript
// ✅ Good: Use refs
const boxRef = useRef(null)
gsap.to(boxRef.current, { x: 100 })

// ❌ Bad: Class selectors in React
gsap.to('.box', { x: 100 })
```

### 3. Optimize ScrollTrigger

```typescript
// ✅ Good: Use scrub for smooth scroll
scrollTrigger: {
  scrub: 1,
  start: 'top center',
  end: 'bottom center'
}

// ❌ Bad: Too many ScrollTriggers
// Create one ScrollTrigger for multiple animations instead
```

### 4. Use Timeline for Sequences

```typescript
// ✅ Good: Timeline for sequences
const tl = gsap.timeline()
tl.to('.box1', { x: 100 })
  .to('.box2', { y: 100 })

// ❌ Bad: Multiple separate tweens with delays
gsap.to('.box1', { x: 100, duration: 1 })
gsap.to('.box2', { y: 100, duration: 1, delay: 1 })
```

## Common Pitfalls

### 1. Not Cleaning Up Animations

```typescript
// ❌ Bad: No cleanup
useEffect(() => {
  gsap.to(boxRef.current, { x: 100 })
}, [])

// ✅ Good: Proper cleanup
useEffect(() => {
  const ctx = gsap.context(() => {
    gsap.to(boxRef.current, { x: 100 })
  }, containerRef)

  return () => ctx.revert()
}, [])
```

### 2. Animating Layout Properties

```typescript
// ❌ Bad: Animating width/height
gsap.to('.box', { width: '200px', height: '200px' })

// ✅ Good: Use scale
gsap.to('.box', { scale: 2 })
```

### 3. Not Registering Plugins

```typescript
// ❌ Bad: Forgot to register
import { ScrollTrigger } from 'gsap/ScrollTrigger'
// ScrollTrigger won't work!

// ✅ Good: Register plugin
import { ScrollTrigger } from 'gsap/ScrollTrigger'
gsap.registerPlugin(ScrollTrigger)
```

### 4. Overusing Animations

```typescript
// ❌ Bad: Too many properties
gsap.to('.box', {
  x: 100,
  y: 100,
  rotation: 360,
  scale: 2,
  opacity: 0.5,
  skewX: 45
})

// ✅ Good: Keep it simple
gsap.to('.box', { x: 100, opacity: 0.5 })
```

## Resources

### Official Documentation
- [GSAP Docs](https://greensock.com/docs/)
- [GSAP GitHub](https://github.com/greensock/GSAP)
- [ScrollTrigger Docs](https://greensock.com/docs/v3/Plugins/ScrollTrigger)

### Tutorials & Examples
- [GSAP CodePen Examples](https://codepen.io/GreenSock)
- [GSAP Learning Center](https://greensock.com/learning/)
- [ScrollTrigger Demos](https://greensock.com/st-demos/)

### Video Tutorials
- [GSAP 3 Tutorial](https://www.youtube.com/results?search_query=gsap+3+tutorial)
- [ScrollTrigger Tutorial](https://www.youtube.com/results?search_query=gsap+scrolltrigger)

### Community
- [GSAP Forums](https://greensock.com/forums/)
- [Stack Overflow](https://stackoverflow.com/questions/tagged/gsap)
- [Discord](https://discord.gg/aZTzRt5)

### Premium Plugins (Paid)
- [DrawSVG](https://greensock.com/drawsvg/) - Advanced SVG path animation
- [MorphSVG](https://greensock.com/morphsvg/) - SVG shape morphing
- [SplitText](https://greensock.com/splittext/) - Text animation
- [Inertia](https://greensock.com/inertia/) - Physics-based motion

---

**Next Steps:**
- Explore [Framer Motion](./framer-motion.md) for React-first animations
- Learn [React Spring](./react-spring.md) for spring-physics animations
- Check out [Lottie](https://airbnb.io/lottie/) for After Effects animations
