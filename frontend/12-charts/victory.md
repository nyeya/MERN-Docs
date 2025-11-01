# Victory - Composable React Components for Charts

## Overview

Victory is an opinionated but fully overridable collection of composable React components for building interactive data visualizations. Created by Formidable Labs, Victory provides a set of modular charting components that work seamlessly together, with excellent animation support and a focus on developer experience.

**Key Features:**
- Fully composable components
- Excellent animation support
- React Native support
- TypeScript support
- Modular architecture
- Flexible and customizable
- Responsive charts
- Accessible
- Well-documented
- Active maintenance

## Why Use Victory?

### Problems It Solves

1. **Composability**: Build complex charts from simple components
2. **Animation**: Smooth, configurable animations
3. **Cross-platform**: Works on web and React Native
4. **Flexibility**: Highly customizable
5. **Type Safety**: TypeScript support
6. **Consistency**: Uniform API across components
7. **Developer Experience**: Intuitive component-based API

### When to Use Victory

- ✅ React and React Native apps
- ✅ Need smooth animations
- ✅ Complex interactive charts
- ✅ Cross-platform development
- ✅ Composable architecture preferred
- ✅ TypeScript projects
- ✅ Custom visualizations

### When NOT to Use Victory

- ❌ Simple static charts (use Chart.js)
- ❌ Very large datasets
- ❌ Need smallest bundle size
- ❌ Non-React projects

## Comparison with Alternatives

### Victory vs Recharts

| Feature | Victory | Recharts |
|---------|---------|----------|
| Composability | Excellent | Good |
| Animation | Excellent | Good |
| React Native | Yes | No |
| Bundle Size | ~300KB | ~400KB |
| Customization | Excellent | Good |
| Learning Curve | Moderate | Easy |
| Best For | Animations | Standard charts |

**Choose Victory if:** You need animations and React Native support
**Choose Recharts if:** You want simpler API for standard charts

## Installation & Setup

### Installation

```bash
# npm
npm install victory

# yarn
yarn add victory

# pnpm
pnpm add victory
```

### For React Native

```bash
npm install victory-native
```

### TypeScript Setup

Victory has built-in TypeScript support. No additional types needed!

### Basic Setup

```typescript
import { VictoryChart, VictoryLine, VictoryAxis } from 'victory'

const data = [
  { x: 1, y: 2 },
  { x: 2, y: 3 },
  { x: 3, y: 5 },
  { x: 4, y: 4 },
  { x: 5, y: 7 }
]

function Chart() {
  return (
    <VictoryChart>
      <VictoryLine data={data} />
      <VictoryAxis />
    </VictoryChart>
  )
}
```

## Core Concepts

### 1. Chart Components

```typescript
import {
  VictoryChart,
  VictoryLine,
  VictoryBar,
  VictoryArea,
  VictoryPie,
  VictoryScatter
} from 'victory'

// Line Chart
<VictoryChart>
  <VictoryLine data={data} />
</VictoryChart>

// Bar Chart
<VictoryChart>
  <VictoryBar data={data} />
</VictoryChart>

// Area Chart
<VictoryChart>
  <VictoryArea data={data} />
</VictoryChart>

// Pie Chart
<VictoryPie data={data} />
```

### 2. Data Format

```typescript
// Simple format
const data = [
  { x: 1, y: 2 },
  { x: 2, y: 3 },
  { x: 3, y: 5 }
]

// With labels
const data = [
  { x: 'Jan', y: 100 },
  { x: 'Feb', y: 150 },
  { x: 'Mar', y: 200 }
]

// Custom accessors
<VictoryLine
  data={customData}
  x="month"
  y="revenue"
/>
```

### 3. Styling

```typescript
<VictoryLine
  style={{
    data: { stroke: '#3b82f6', strokeWidth: 2 },
    parent: { border: '1px solid #ccc' }
  }}
  data={data}
/>
```

## Basic Usage

### Simple Line Chart

```typescript
import { VictoryChart, VictoryLine, VictoryAxis, VictoryTheme } from 'victory'

function LineChart() {
  const data = [
    { month: 'Jan', revenue: 4000 },
    { month: 'Feb', revenue: 3000 },
    { month: 'Mar', revenue: 6000 },
    { month: 'Apr', revenue: 8000 },
    { month: 'May', revenue: 7000 }
  ]

  return (
    <VictoryChart
      theme={VictoryTheme.material}
      width={600}
      height={400}
    >
      <VictoryLine
        data={data}
        x="month"
        y="revenue"
        style={{
          data: { stroke: '#3b82f6', strokeWidth: 2 }
        }}
      />
      <VictoryAxis />
      <VictoryAxis dependentAxis />
    </VictoryChart>
  )
}
```

### Bar Chart

```typescript
import { VictoryChart, VictoryBar, VictoryAxis, VictoryTheme } from 'victory'

function BarChart() {
  const data = [
    { category: 'A', sales: 12 },
    { category: 'B', sales: 19 },
    { category: 'C', sales: 3 },
    { category: 'D', sales: 5 }
  ]

  return (
    <VictoryChart
      theme={VictoryTheme.material}
      domainPadding={20}
    >
      <VictoryBar
        data={data}
        x="category"
        y="sales"
        style={{
          data: { fill: '#3b82f6' }
        }}
      />
      <VictoryAxis />
      <VictoryAxis dependentAxis />
    </VictoryChart>
  )
}
```

### Pie Chart

```typescript
import { VictoryPie } from 'victory'

function PieChart() {
  const data = [
    { x: 'Desktop', y: 35 },
    { x: 'Mobile', y: 40 },
    { x: 'Tablet', y: 25 }
  ]

  return (
    <VictoryPie
      data={data}
      colorScale={['#3b82f6', '#10b981', '#f59e0b']}
      labelRadius={100}
      style={{ labels: { fontSize: 14, fill: 'white' } }}
    />
  )
}
```

## Advanced Usage

### 1. Multiple Data Series

```typescript
import { VictoryChart, VictoryLine, VictoryLegend, VictoryAxis } from 'victory'

function MultiLineChart() {
  const revenueData = [
    { x: 1, y: 4000 },
    { x: 2, y: 3000 },
    { x: 3, y: 6000 }
  ]

  const expensesData = [
    { x: 1, y: 2000 },
    { x: 2, y: 2500 },
    { x: 3, y: 3000 }
  ]

  return (
    <VictoryChart>
      <VictoryLegend
        x={125}
        y={10}
        orientation="horizontal"
        gutter={20}
        data={[
          { name: 'Revenue', symbol: { fill: '#3b82f6' } },
          { name: 'Expenses', symbol: { fill: '#ef4444' } }
        ]}
      />
      <VictoryLine
        data={revenueData}
        style={{ data: { stroke: '#3b82f6' } }}
      />
      <VictoryLine
        data={expensesData}
        style={{ data: { stroke: '#ef4444' } }}
      />
      <VictoryAxis />
      <VictoryAxis dependentAxis />
    </VictoryChart>
  )
}
```

### 2. Animated Chart

```typescript
import { useState } from 'react'
import { VictoryChart, VictoryLine, VictoryAxis } from 'victory'

function AnimatedChart() {
  const [data, setData] = useState([
    { x: 1, y: 2 },
    { x: 2, y: 3 },
    { x: 3, y: 5 }
  ])

  const updateData = () => {
    setData(data.map(point => ({
      ...point,
      y: Math.random() * 10
    })))
  }

  return (
    <div>
      <VictoryChart animate={{ duration: 500 }}>
        <VictoryLine data={data} />
        <VictoryAxis />
        <VictoryAxis dependentAxis />
      </VictoryChart>
      <button onClick={updateData}>Update Data</button>
    </div>
  )
}
```

### 3. Stacked Bar Chart

```typescript
import { VictoryChart, VictoryStack, VictoryBar, VictoryAxis } from 'victory'

function StackedBarChart() {
  return (
    <VictoryChart domainPadding={20}>
      <VictoryStack colorScale={['#3b82f6', '#10b981', '#f59e0b']}>
        <VictoryBar
          data={[
            { x: 'Q1', y: 2 },
            { x: 'Q2', y: 3 },
            { x: 'Q3', y: 5 }
          ]}
        />
        <VictoryBar
          data={[
            { x: 'Q1', y: 1 },
            { x: 'Q2', y: 4 },
            { x: 'Q3', y: 2 }
          ]}
        />
        <VictoryBar
          data={[
            { x: 'Q1', y: 3 },
            { x: 'Q2', y: 2 },
            { x: 'Q3', y: 3 }
          ]}
        />
      </VictoryStack>
      <VictoryAxis />
      <VictoryAxis dependentAxis />
    </VictoryChart>
  )
}
```

### 4. Custom Tooltips

```typescript
import { VictoryChart, VictoryLine, VictoryTooltip, VictoryVoronoiContainer } from 'victory'

function ChartWithTooltip() {
  return (
    <VictoryChart
      containerComponent={
        <VictoryVoronoiContainer
          labels={({ datum }) => `${datum.x}: ${datum.y}`}
          labelComponent={
            <VictoryTooltip
              style={{ fontSize: 10 }}
              flyoutStyle={{ fill: 'white', stroke: '#3b82f6' }}
            />
          }
        />
      }
    >
      <VictoryLine
        data={[
          { x: 1, y: 2 },
          { x: 2, y: 3 },
          { x: 3, y: 5 }
        ]}
      />
    </VictoryChart>
  )
}
```

### 5. Responsive Chart

```typescript
import { VictoryChart, VictoryLine } from 'victory'

function ResponsiveChart() {
  return (
    <div style={{ width: '100%', height: '400px' }}>
      <VictoryChart
        width={600}
        height={400}
        containerComponent={
          <VictoryContainer
            responsive={true}
            style={{ width: '100%', height: '100%' }}
          />
        }
      >
        <VictoryLine data={data} />
      </VictoryChart>
    </div>
  )
}
```

## Real-World Examples

### Dashboard with Multiple Charts

```typescript
import { VictoryChart, VictoryLine, VictoryBar, VictoryPie, VictoryAxis, VictoryTheme } from 'victory'

function Dashboard() {
  const lineData = [
    { month: 'Jan', value: 4000 },
    { month: 'Feb', value: 3000 },
    { month: 'Mar', value: 6000 }
  ]

  const barData = [
    { product: 'A', sales: 12 },
    { product: 'B', sales: 19 },
    { product: 'C', sales: 3 }
  ]

  const pieData = [
    { x: 'Desktop', y: 35 },
    { x: 'Mobile', y: 40 },
    { x: 'Tablet', y: 25 }
  ]

  return (
    <div className="grid grid-cols-2 gap-6">
      <div className="bg-white p-6 rounded-lg shadow">
        <h3 className="text-lg font-bold mb-4">Monthly Trend</h3>
        <VictoryChart theme={VictoryTheme.material}>
          <VictoryLine data={lineData} x="month" y="value" />
          <VictoryAxis />
          <VictoryAxis dependentAxis />
        </VictoryChart>
      </div>

      <div className="bg-white p-6 rounded-lg shadow">
        <h3 className="text-lg font-bold mb-4">Product Sales</h3>
        <VictoryChart theme={VictoryTheme.material} domainPadding={20}>
          <VictoryBar data={barData} x="product" y="sales" />
          <VictoryAxis />
          <VictoryAxis dependentAxis />
        </VictoryChart>
      </div>

      <div className="bg-white p-6 rounded-lg shadow">
        <h3 className="text-lg font-bold mb-4">Device Distribution</h3>
        <VictoryPie
          data={pieData}
          colorScale={['#3b82f6', '#10b981', '#f59e0b']}
        />
      </div>
    </div>
  )
}
```

## Best Practices

### 1. Use Themes for Consistency

```typescript
import { VictoryTheme } from 'victory'

// ✅ Good: Use theme
<VictoryChart theme={VictoryTheme.material}>
  <VictoryLine data={data} />
</VictoryChart>

// ❌ Bad: No theme
<VictoryChart>
  <VictoryLine data={data} />
</VictoryChart>
```

### 2. Memoize Data

```typescript
import { useMemo } from 'react'

// ✅ Good: Memoized data
const chartData = useMemo(() => 
  rawData.map(item => ({ x: item.date, y: item.value })),
  [rawData]
)
```

### 3. Use Proper Padding

```typescript
// ✅ Good: Domain padding for bar charts
<VictoryChart domainPadding={20}>
  <VictoryBar data={data} />
</VictoryChart>
```

## Common Pitfalls

### 1. Not Setting Chart Dimensions

```typescript
// ❌ Bad: No dimensions
<VictoryChart>
  <VictoryLine data={data} />
</VictoryChart>

// ✅ Good: Explicit dimensions
<VictoryChart width={600} height={400}>
  <VictoryLine data={data} />
</VictoryChart>
```

### 2. Forgetting Axes

```typescript
// ❌ Bad: No axes
<VictoryChart>
  <VictoryLine data={data} />
</VictoryChart>

// ✅ Good: Include axes
<VictoryChart>
  <VictoryLine data={data} />
  <VictoryAxis />
  <VictoryAxis dependentAxis />
</VictoryChart>
```

## Resources

### Official Documentation
- [Victory Docs](https://formidable.com/open-source/victory/)
- [Victory GitHub](https://github.com/FormidableLabs/victory)
- [API Reference](https://formidable.com/open-source/victory/docs)

### Examples
- [Victory Gallery](https://formidable.com/open-source/victory/gallery)
- [CodeSandbox Examples](https://codesandbox.io/examples/package/victory)

### Community
- [GitHub Issues](https://github.com/FormidableLabs/victory/issues)
- [Stack Overflow](https://stackoverflow.com/questions/tagged/victory)

---

**Next Steps:**
- Explore [Recharts](./recharts.md) for simpler API
- Learn [Visx](./visx.md) for low-level control
- Check out [D3.js](https://d3js.org/) for advanced visualizations
