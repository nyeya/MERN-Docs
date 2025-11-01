# Visx - Low-Level Visualization Components

## Overview

Visx (formerly vx) is a collection of low-level visualization primitives for React. Created by Airbnb, it provides the building blocks for creating custom data visualizations without prescribing a specific chart structure. Built on D3.js, Visx gives you full control while handling the React integration complexities.

**Key Features:**
- Low-level primitives
- Built on D3.js
- Full customization
- TypeScript support
- Modular packages
- Tree-shakeable
- React-first
- No opinions on structure
- Excellent performance
- Active maintenance

## Why Use Visx?

### Problems It Solves

1. **Customization**: Complete control over visualization
2. **Flexibility**: Build any chart type
3. **Performance**: Optimized React + D3 integration
4. **Type Safety**: Excellent TypeScript support
5. **Modularity**: Import only what you need
6. **D3 Power**: Access to D3's capabilities
7. **React Integration**: Proper React patterns

### When to Use Visx

- ✅ Custom visualizations
- ✅ Need full control
- ✅ Complex data viz
- ✅ D3 experience
- ✅ TypeScript projects
- ✅ Unique chart requirements
- ✅ Performance-critical apps

### When NOT to Use Visx

- ❌ Standard charts (use Recharts)
- ❌ Quick prototyping
- ❌ No D3 knowledge
- ❌ Simple visualizations

## Comparison with Alternatives

### Visx vs Recharts

| Feature | Visx | Recharts |
|---------|------|----------|
| Abstraction | Low-level | High-level |
| Customization | Unlimited | Limited |
| Learning Curve | Steep | Easy |
| Bundle Size | Smaller | Larger |
| Flexibility | Maximum | Good |
| Development Speed | Slower | Faster |
| Best For | Custom viz | Standard charts |

**Choose Visx if:** You need complete customization
**Choose Recharts if:** You want standard charts quickly

### Visx vs D3.js

| Feature | Visx | D3.js |
|---------|------|-------|
| React Integration | Native | Manual |
| API | React components | Imperative |
| TypeScript | Excellent | Good |
| Learning Curve | Moderate | Steep |
| Flexibility | High | Maximum |
| Best For | React apps | Any framework |

## Installation & Setup

### Installation

```bash
# Install specific packages you need
npm install @visx/scale @visx/axis @visx/shape @visx/group

# Or install all packages
npm install @visx/visx
```

### TypeScript Setup

Visx has built-in TypeScript support. No additional types needed!

### Basic Setup

```typescript
import { Group } from '@visx/group'
import { Bar } from '@visx/shape'
import { scaleLinear, scaleBand } from '@visx/scale'

const data = [
  { label: 'A', value: 10 },
  { label: 'B', value: 20 },
  { label: 'C', value: 30 }
]

function BarChart({ width, height }: { width: number; height: number }) {
  const xScale = scaleBand({
    domain: data.map(d => d.label),
    range: [0, width],
    padding: 0.2
  })

  const yScale = scaleLinear({
    domain: [0, Math.max(...data.map(d => d.value))],
    range: [height, 0]
  })

  return (
    <svg width={width} height={height}>
      <Group>
        {data.map(d => (
          <Bar
            key={d.label}
            x={xScale(d.label)}
            y={yScale(d.value)}
            width={xScale.bandwidth()}
            height={height - yScale(d.value)}
            fill="#3b82f6"
          />
        ))}
      </Group>
    </svg>
  )
}
```

## Core Concepts

### 1. Scales

```typescript
import { scaleLinear, scaleBand, scaleTime } from '@visx/scale'

// Linear scale
const yScale = scaleLinear({
  domain: [0, 100],
  range: [height, 0]
})

// Band scale (for bar charts)
const xScale = scaleBand({
  domain: ['A', 'B', 'C'],
  range: [0, width],
  padding: 0.2
})

// Time scale
const timeScale = scaleTime({
  domain: [new Date('2025-01-01'), new Date('2025-12-31')],
  range: [0, width]
})
```

### 2. Shapes

```typescript
import { Bar, Line, Circle, Arc } from '@visx/shape'

// Bar
<Bar
  x={0}
  y={0}
  width={100}
  height={50}
  fill="#3b82f6"
/>

// Line
<Line
  from={{ x: 0, y: 0 }}
  to={{ x: 100, y: 100 }}
  stroke="#3b82f6"
/>

// Circle
<Circle
  cx={50}
  cy={50}
  r={20}
  fill="#3b82f6"
/>
```

### 3. Axes

```typescript
import { AxisBottom, AxisLeft } from '@visx/axis'

<AxisBottom
  top={height}
  scale={xScale}
  stroke="#333"
  tickStroke="#333"
/>

<AxisLeft
  scale={yScale}
  stroke="#333"
  tickStroke="#333"
/>
```

## Basic Usage

### Simple Bar Chart

```typescript
import { Group } from '@visx/group'
import { Bar } from '@visx/shape'
import { scaleBand, scaleLinear } from '@visx/scale'
import { AxisBottom, AxisLeft } from '@visx/axis'

interface DataPoint {
  label: string
  value: number
}

interface BarChartProps {
  width: number
  height: number
  data: DataPoint[]
}

function BarChart({ width, height, data }: BarChartProps) {
  const margin = { top: 20, right: 20, bottom: 40, left: 40 }
  const innerWidth = width - margin.left - margin.right
  const innerHeight = height - margin.top - margin.bottom

  const xScale = scaleBand({
    domain: data.map(d => d.label),
    range: [0, innerWidth],
    padding: 0.2
  })

  const yScale = scaleLinear({
    domain: [0, Math.max(...data.map(d => d.value))],
    range: [innerHeight, 0]
  })

  return (
    <svg width={width} height={height}>
      <Group left={margin.left} top={margin.top}>
        {data.map(d => (
          <Bar
            key={d.label}
            x={xScale(d.label)}
            y={yScale(d.value)}
            width={xScale.bandwidth()}
            height={innerHeight - yScale(d.value)}
            fill="#3b82f6"
          />
        ))}
        <AxisBottom
          top={innerHeight}
          scale={xScale}
        />
        <AxisLeft
          scale={yScale}
        />
      </Group>
    </svg>
  )
}
```

### Line Chart

```typescript
import { Group } from '@visx/group'
import { LinePath } from '@visx/shape'
import { scaleTime, scaleLinear } from '@visx/scale'
import { AxisBottom, AxisLeft } from '@visx/axis'
import { curveMonotoneX } from '@visx/curve'

interface DataPoint {
  date: Date
  value: number
}

function LineChart({ width, height, data }: { width: number; height: number; data: DataPoint[] }) {
  const margin = { top: 20, right: 20, bottom: 40, left: 40 }
  const innerWidth = width - margin.left - margin.right
  const innerHeight = height - margin.top - margin.bottom

  const xScale = scaleTime({
    domain: [Math.min(...data.map(d => d.date.getTime())), Math.max(...data.map(d => d.date.getTime()))],
    range: [0, innerWidth]
  })

  const yScale = scaleLinear({
    domain: [0, Math.max(...data.map(d => d.value))],
    range: [innerHeight, 0]
  })

  return (
    <svg width={width} height={height}>
      <Group left={margin.left} top={margin.top}>
        <LinePath
          data={data}
          x={d => xScale(d.date)}
          y={d => yScale(d.value)}
          stroke="#3b82f6"
          strokeWidth={2}
          curve={curveMonotoneX}
        />
        <AxisBottom
          top={innerHeight}
          scale={xScale}
        />
        <AxisLeft
          scale={yScale}
        />
      </Group>
    </svg>
  )
}
```

## Advanced Usage

### 1. Responsive Chart with ParentSize

```typescript
import { ParentSize } from '@visx/responsive'

function ResponsiveChart() {
  return (
    <div style={{ width: '100%', height: '400px' }}>
      <ParentSize>
        {({ width, height }) => (
          <BarChart width={width} height={height} data={data} />
        )}
      </ParentSize>
    </div>
  )
}
```

### 2. Tooltip

```typescript
import { useTooltip, TooltipWithBounds, defaultStyles } from '@visx/tooltip'
import { localPoint } from '@visx/event'

function ChartWithTooltip() {
  const {
    tooltipData,
    tooltipLeft,
    tooltipTop,
    tooltipOpen,
    showTooltip,
    hideTooltip
  } = useTooltip()

  const handleMouseMove = (event: React.MouseEvent) => {
    const point = localPoint(event)
    if (!point) return

    showTooltip({
      tooltipData: 'Tooltip content',
      tooltipLeft: point.x,
      tooltipTop: point.y
    })
  }

  return (
    <div>
      <svg
        width={width}
        height={height}
        onMouseMove={handleMouseMove}
        onMouseLeave={hideTooltip}
      >
        {/* Chart content */}
      </svg>
      {tooltipOpen && (
        <TooltipWithBounds
          top={tooltipTop}
          left={tooltipLeft}
          style={defaultStyles}
        >
          {tooltipData}
        </TooltipWithBounds>
      )}
    </div>
  )
}
```

### 3. Brush for Zooming

```typescript
import { Brush } from '@visx/brush'
import { Bounds } from '@visx/brush/lib/types'

function ChartWithBrush() {
  const [filteredData, setFilteredData] = useState(data)

  const onBrushChange = (domain: Bounds | null) => {
    if (!domain) return
    const { x0, x1 } = domain
    const filtered = data.filter(d => {
      const x = xScale(d.date)
      return x > x0 && x < x1
    })
    setFilteredData(filtered)
  }

  return (
    <svg width={width} height={height}>
      <Brush
        xScale={xScale}
        yScale={yScale}
        width={innerWidth}
        height={innerHeight}
        onChange={onBrushChange}
      />
    </svg>
  )
}
```

### 4. Gradient Fill

```typescript
import { LinearGradient } from '@visx/gradient'

function GradientChart() {
  return (
    <svg width={width} height={height}>
      <LinearGradient
        id="gradient"
        from="#3b82f6"
        to="#10b981"
      />
      <Bar
        x={0}
        y={0}
        width={100}
        height={100}
        fill="url(#gradient)"
      />
    </svg>
  )
}
```

## Real-World Examples

### Interactive Dashboard

```typescript
import { ParentSize } from '@visx/responsive'
import { Group } from '@visx/group'
import { Bar } from '@visx/shape'
import { scaleBand, scaleLinear } from '@visx/scale'
import { AxisBottom, AxisLeft } from '@visx/axis'
import { useTooltip, TooltipWithBounds } from '@visx/tooltip'

function InteractiveDashboard() {
  const { tooltipData, tooltipLeft, tooltipTop, tooltipOpen, showTooltip, hideTooltip } = useTooltip()

  return (
    <div className="bg-white p-6 rounded-lg shadow">
      <h3 className="text-lg font-bold mb-4">Sales by Category</h3>
      <ParentSize>
        {({ width, height }) => (
          <BarChart
            width={width}
            height={400}
            data={data}
            onMouseMove={showTooltip}
            onMouseLeave={hideTooltip}
          />
        )}
      </ParentSize>
      {tooltipOpen && (
        <TooltipWithBounds top={tooltipTop} left={tooltipLeft}>
          {tooltipData}
        </TooltipWithBounds>
      )}
    </div>
  )
}
```

## Best Practices

### 1. Use ParentSize for Responsiveness

```typescript
// ✅ Good: Responsive
<ParentSize>
  {({ width, height }) => <Chart width={width} height={height} />}
</ParentSize>

// ❌ Bad: Fixed size
<Chart width={600} height={400} />
```

### 2. Memoize Scales

```typescript
import { useMemo } from 'react'

// ✅ Good: Memoized scales
const xScale = useMemo(() => 
  scaleBand({
    domain: data.map(d => d.label),
    range: [0, width]
  }),
  [data, width]
)
```

### 3. Use Proper Margins

```typescript
// ✅ Good: Account for margins
const margin = { top: 20, right: 20, bottom: 40, left: 40 }
const innerWidth = width - margin.left - margin.right
const innerHeight = height - margin.top - margin.bottom
```

## Common Pitfalls

### 1. Not Accounting for Margins

```typescript
// ❌ Bad: No margins
<svg width={width} height={height}>
  <Bar x={0} y={0} width={width} height={height} />
</svg>

// ✅ Good: With margins
<svg width={width} height={height}>
  <Group left={margin.left} top={margin.top}>
    <Bar x={0} y={0} width={innerWidth} height={innerHeight} />
  </Group>
</svg>
```

### 2. Not Handling Empty Data

```typescript
// ✅ Good: Handle empty data
if (data.length === 0) {
  return <div>No data available</div>
}
```

## Resources

### Official Documentation
- [Visx Docs](https://airbnb.io/visx/)
- [Visx GitHub](https://github.com/airbnb/visx)
- [API Reference](https://airbnb.io/visx/docs)

### Examples
- [Visx Gallery](https://airbnb.io/visx/gallery)
- [CodeSandbox Examples](https://codesandbox.io/examples/package/@visx/visx)

### Community
- [GitHub Discussions](https://github.com/airbnb/visx/discussions)
- [Stack Overflow](https://stackoverflow.com/questions/tagged/visx)

---

**Next Steps:**
- Explore [D3.js](https://d3js.org/) for the underlying library
- Learn [Recharts](./recharts.md) for higher-level API
- Check out [Observable](https://observablehq.com/) for data viz notebooks
