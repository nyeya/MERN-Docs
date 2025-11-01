# Recharts - Composable Charting Library for React

## Overview

Recharts is a composable charting library built on React components. It provides a declarative API for building charts using React components, making it intuitive for React developers. Built on D3.js, Recharts offers a good balance between customization and ease of use, with responsive charts out of the box.

**Key Features:**
- Composable React components
- Responsive charts
- Built on D3.js
- TypeScript support
- Customizable and themeable
- Animation support
- Touch-friendly
- SSR compatible
- Lightweight API
- Good documentation

## Why Use Recharts?

### Problems It Solves

1. **React-First**: Native React component API
2. **Composability**: Build complex charts from simple components
3. **Responsiveness**: Automatic responsive behavior
4. **Customization**: Easy to customize with props
5. **Type Safety**: TypeScript support
6. **Learning Curve**: Intuitive for React developers
7. **Animation**: Built-in smooth animations

### When to Use Recharts

- ✅ React applications
- ✅ Standard chart types (line, bar, pie, area)
- ✅ Responsive dashboards
- ✅ Data visualization
- ✅ Real-time data charts
- ✅ Interactive charts
- ✅ TypeScript projects

### When NOT to Use Recharts

- ❌ Complex custom visualizations (use D3.js or Visx)
- ❌ 3D charts
- ❌ Very large datasets (performance issues)
- ❌ Advanced statistical charts

## Comparison with Alternatives

### Recharts vs Chart.js

| Feature | Recharts | Chart.js |
|---------|----------|----------|
| API | React components | Imperative |
| Customization | High | Moderate |
| Bundle Size | ~400KB | ~200KB |
| React Integration | Native | Wrapper needed |
| Learning Curve | Easy (React devs) | Easy |
| Best For | React apps | Any framework |

**Choose Recharts if:** You want React-first declarative API
**Choose Chart.js if:** You want smaller bundle and framework-agnostic

### Recharts vs Visx

| Feature | Recharts | Visx |
|---------|----------|------|
| Abstraction Level | High | Low |
| Customization | Good | Excellent |
| Learning Curve | Easy | Moderate |
| Bundle Size | Larger | Smaller |
| Flexibility | Limited | Unlimited |
| Best For | Standard charts | Custom viz |

## Installation & Setup

### Installation

```bash
# npm
npm install recharts

# yarn
yarn add recharts

# pnpm
pnpm add recharts
```

### TypeScript Setup

```bash
npm install @types/recharts --save-dev
```

### Basic Setup

```typescript
import { LineChart, Line, XAxis, YAxis, CartesianGrid, Tooltip, Legend } from 'recharts'

const data = [
  { name: 'Jan', value: 400 },
  { name: 'Feb', value: 300 },
  { name: 'Mar', value: 600 }
]

function Chart() {
  return (
    <LineChart width={600} height={300} data={data}>
      <CartesianGrid strokeDasharray="3 3" />
      <XAxis dataKey="name" />
      <YAxis />
      <Tooltip />
      <Legend />
      <Line type="monotone" dataKey="value" stroke="#8884d8" />
    </LineChart>
  )
}
```

## Core Concepts

### 1. Chart Types

```typescript
import {
  LineChart,
  BarChart,
  AreaChart,
  PieChart,
  ScatterChart,
  RadarChart
} from 'recharts'

// Line Chart
<LineChart width={600} height={300} data={data}>
  <Line type="monotone" dataKey="value" stroke="#8884d8" />
</LineChart>

// Bar Chart
<BarChart width={600} height={300} data={data}>
  <Bar dataKey="value" fill="#8884d8" />
</BarChart>

// Area Chart
<AreaChart width={600} height={300} data={data}>
  <Area type="monotone" dataKey="value" fill="#8884d8" />
</AreaChart>
```

### 2. Responsive Container

```typescript
import { ResponsiveContainer, LineChart, Line } from 'recharts'

function ResponsiveChart() {
  return (
    <ResponsiveContainer width="100%" height={400}>
      <LineChart data={data}>
        <Line type="monotone" dataKey="value" stroke="#8884d8" />
      </LineChart>
    </ResponsiveContainer>
  )
}
```

### 3. Multiple Data Series

```typescript
const data = [
  { name: 'Jan', sales: 400, revenue: 240 },
  { name: 'Feb', sales: 300, revenue: 139 },
  { name: 'Mar', sales: 600, revenue: 380 }
]

<LineChart width={600} height={300} data={data}>
  <XAxis dataKey="name" />
  <YAxis />
  <Tooltip />
  <Legend />
  <Line type="monotone" dataKey="sales" stroke="#8884d8" />
  <Line type="monotone" dataKey="revenue" stroke="#82ca9d" />
</LineChart>
```

## Basic Usage

### Simple Line Chart

```typescript
import { LineChart, Line, XAxis, YAxis, CartesianGrid, Tooltip, Legend, ResponsiveContainer } from 'recharts'

const data = [
  { month: 'Jan', users: 400 },
  { month: 'Feb', users: 300 },
  { month: 'Mar', users: 600 },
  { month: 'Apr', users: 800 },
  { month: 'May', users: 700 }
]

function UserGrowthChart() {
  return (
    <ResponsiveContainer width="100%" height={400}>
      <LineChart data={data}>
        <CartesianGrid strokeDasharray="3 3" />
        <XAxis dataKey="month" />
        <YAxis />
        <Tooltip />
        <Legend />
        <Line 
          type="monotone" 
          dataKey="users" 
          stroke="#3b82f6" 
          strokeWidth={2}
          dot={{ r: 4 }}
          activeDot={{ r: 6 }}
        />
      </LineChart>
    </ResponsiveContainer>
  )
}
```

### Bar Chart

```typescript
import { BarChart, Bar, XAxis, YAxis, CartesianGrid, Tooltip, Legend, ResponsiveContainer } from 'recharts'

const data = [
  { category: 'Electronics', sales: 4000 },
  { category: 'Clothing', sales: 3000 },
  { category: 'Food', sales: 2000 },
  { category: 'Books', sales: 2780 }
]

function SalesChart() {
  return (
    <ResponsiveContainer width="100%" height={400}>
      <BarChart data={data}>
        <CartesianGrid strokeDasharray="3 3" />
        <XAxis dataKey="category" />
        <YAxis />
        <Tooltip />
        <Legend />
        <Bar dataKey="sales" fill="#3b82f6" />
      </BarChart>
    </ResponsiveContainer>
  )
}
```

### Pie Chart

```typescript
import { PieChart, Pie, Cell, Tooltip, Legend, ResponsiveContainer } from 'recharts'

const data = [
  { name: 'Desktop', value: 400 },
  { name: 'Mobile', value: 300 },
  { name: 'Tablet', value: 200 }
]

const COLORS = ['#3b82f6', '#10b981', '#f59e0b']

function DeviceChart() {
  return (
    <ResponsiveContainer width="100%" height={400}>
      <PieChart>
        <Pie
          data={data}
          cx="50%"
          cy="50%"
          labelLine={false}
          label={({ name, percent }) => `${name}: ${(percent * 100).toFixed(0)}%`}
          outerRadius={120}
          fill="#8884d8"
          dataKey="value"
        >
          {data.map((entry, index) => (
            <Cell key={`cell-${index}`} fill={COLORS[index % COLORS.length]} />
          ))}
        </Pie>
        <Tooltip />
        <Legend />
      </PieChart>
    </ResponsiveContainer>
  )
}
```

## Advanced Usage

### 1. Custom Tooltip

```typescript
import { LineChart, Line, Tooltip, ResponsiveContainer } from 'recharts'

interface CustomTooltipProps {
  active?: boolean
  payload?: any[]
  label?: string
}

function CustomTooltip({ active, payload, label }: CustomTooltipProps) {
  if (active && payload && payload.length) {
    return (
      <div className="bg-white p-4 border rounded shadow-lg">
        <p className="font-bold">{label}</p>
        <p className="text-blue-600">
          Value: {payload[0].value}
        </p>
      </div>
    )
  }
  return null
}

function ChartWithCustomTooltip() {
  return (
    <ResponsiveContainer width="100%" height={400}>
      <LineChart data={data}>
        <Line dataKey="value" stroke="#3b82f6" />
        <Tooltip content={<CustomTooltip />} />
      </LineChart>
    </ResponsiveContainer>
  )
}
```

### 2. Stacked Bar Chart

```typescript
import { BarChart, Bar, XAxis, YAxis, CartesianGrid, Tooltip, Legend, ResponsiveContainer } from 'recharts'

const data = [
  { month: 'Jan', desktop: 400, mobile: 240, tablet: 100 },
  { month: 'Feb', desktop: 300, mobile: 139, tablet: 80 },
  { month: 'Mar', desktop: 600, mobile: 380, tablet: 150 }
]

function StackedBarChart() {
  return (
    <ResponsiveContainer width="100%" height={400}>
      <BarChart data={data}>
        <CartesianGrid strokeDasharray="3 3" />
        <XAxis dataKey="month" />
        <YAxis />
        <Tooltip />
        <Legend />
        <Bar dataKey="desktop" stackId="a" fill="#3b82f6" />
        <Bar dataKey="mobile" stackId="a" fill="#10b981" />
        <Bar dataKey="tablet" stackId="a" fill="#f59e0b" />
      </BarChart>
    </ResponsiveContainer>
  )
}
```

### 3. Area Chart with Gradient

```typescript
import { AreaChart, Area, XAxis, YAxis, CartesianGrid, Tooltip, ResponsiveContainer } from 'recharts'

function GradientAreaChart() {
  return (
    <ResponsiveContainer width="100%" height={400}>
      <AreaChart data={data}>
        <defs>
          <linearGradient id="colorValue" x1="0" y1="0" x2="0" y2="1">
            <stop offset="5%" stopColor="#3b82f6" stopOpacity={0.8}/>
            <stop offset="95%" stopColor="#3b82f6" stopOpacity={0}/>
          </linearGradient>
        </defs>
        <CartesianGrid strokeDasharray="3 3" />
        <XAxis dataKey="name" />
        <YAxis />
        <Tooltip />
        <Area 
          type="monotone" 
          dataKey="value" 
          stroke="#3b82f6" 
          fillOpacity={1} 
          fill="url(#colorValue)" 
        />
      </AreaChart>
    </ResponsiveContainer>
  )
}
```

### 4. Real-Time Data Chart

```typescript
import { useState, useEffect } from 'react'
import { LineChart, Line, XAxis, YAxis, ResponsiveContainer } from 'recharts'

function RealTimeChart() {
  const [data, setData] = useState<Array<{ time: string; value: number }>>([])

  useEffect(() => {
    const interval = setInterval(() => {
      setData(prevData => {
        const newData = [
          ...prevData,
          {
            time: new Date().toLocaleTimeString(),
            value: Math.random() * 100
          }
        ]
        return newData.slice(-20) // Keep last 20 points
      })
    }, 1000)

    return () => clearInterval(interval)
  }, [])

  return (
    <ResponsiveContainer width="100%" height={400}>
      <LineChart data={data}>
        <XAxis dataKey="time" />
        <YAxis />
        <Line 
          type="monotone" 
          dataKey="value" 
          stroke="#3b82f6" 
          dot={false}
          isAnimationActive={false}
        />
      </LineChart>
    </ResponsiveContainer>
  )
}
```

### 5. Composed Chart

```typescript
import { 
  ComposedChart, 
  Line, 
  Bar, 
  Area,
  XAxis, 
  YAxis, 
  CartesianGrid, 
  Tooltip, 
  Legend,
  ResponsiveContainer 
} from 'recharts'

const data = [
  { month: 'Jan', revenue: 4000, profit: 2400, growth: 24 },
  { month: 'Feb', revenue: 3000, profit: 1398, growth: 22 },
  { month: 'Mar', revenue: 6000, profit: 3800, growth: 29 }
]

function DashboardChart() {
  return (
    <ResponsiveContainer width="100%" height={400}>
      <ComposedChart data={data}>
        <CartesianGrid strokeDasharray="3 3" />
        <XAxis dataKey="month" />
        <YAxis />
        <Tooltip />
        <Legend />
        <Area type="monotone" dataKey="revenue" fill="#3b82f6" stroke="#3b82f6" />
        <Bar dataKey="profit" fill="#10b981" />
        <Line type="monotone" dataKey="growth" stroke="#f59e0b" />
      </ComposedChart>
    </ResponsiveContainer>
  )
}
```

## Real-World Examples

### Analytics Dashboard

```typescript
import { LineChart, Line, BarChart, Bar, PieChart, Pie, Cell, XAxis, YAxis, CartesianGrid, Tooltip, Legend, ResponsiveContainer } from 'recharts'

const trafficData = [
  { date: '2025-01', visitors: 4000, pageViews: 2400 },
  { date: '2025-02', visitors: 3000, pageViews: 1398 },
  { date: '2025-03', visitors: 6000, pageViews: 9800 }
]

const deviceData = [
  { name: 'Desktop', value: 400 },
  { name: 'Mobile', value: 300 },
  { name: 'Tablet', value: 100 }
]

const COLORS = ['#3b82f6', '#10b981', '#f59e0b']

function AnalyticsDashboard() {
  return (
    <div className="grid grid-cols-2 gap-6">
      <div className="bg-white p-6 rounded-lg shadow">
        <h3 className="text-lg font-bold mb-4">Traffic Overview</h3>
        <ResponsiveContainer width="100%" height={300}>
          <LineChart data={trafficData}>
            <CartesianGrid strokeDasharray="3 3" />
            <XAxis dataKey="date" />
            <YAxis />
            <Tooltip />
            <Legend />
            <Line type="monotone" dataKey="visitors" stroke="#3b82f6" />
            <Line type="monotone" dataKey="pageViews" stroke="#10b981" />
          </LineChart>
        </ResponsiveContainer>
      </div>

      <div className="bg-white p-6 rounded-lg shadow">
        <h3 className="text-lg font-bold mb-4">Device Distribution</h3>
        <ResponsiveContainer width="100%" height={300}>
          <PieChart>
            <Pie
              data={deviceData}
              cx="50%"
              cy="50%"
              labelLine={false}
              label={({ name, percent }) => `${name}: ${(percent * 100).toFixed(0)}%`}
              outerRadius={80}
              fill="#8884d8"
              dataKey="value"
            >
              {deviceData.map((entry, index) => (
                <Cell key={`cell-${index}`} fill={COLORS[index % COLORS.length]} />
              ))}
            </Pie>
            <Tooltip />
          </PieChart>
        </ResponsiveContainer>
      </div>
    </div>
  )
}
```

## Best Practices

### 1. Use ResponsiveContainer

```typescript
// ✅ Good: Responsive chart
<ResponsiveContainer width="100%" height={400}>
  <LineChart data={data}>
    <Line dataKey="value" />
  </LineChart>
</ResponsiveContainer>

// ❌ Bad: Fixed dimensions
<LineChart width={600} height={400} data={data}>
  <Line dataKey="value" />
</LineChart>
```

### 2. Memoize Data Transformations

```typescript
import { useMemo } from 'react'

// ✅ Good: Memoized data
const chartData = useMemo(() => 
  rawData.map(item => ({
    name: item.label,
    value: item.count
  })),
  [rawData]
)
```

### 3. Use Proper Color Schemes

```typescript
// ✅ Good: Accessible colors
const COLORS = ['#3b82f6', '#10b981', '#f59e0b', '#ef4444']

// ❌ Bad: Similar colors
const COLORS = ['#3b82f6', '#4b92ff', '#5ba2ff']
```

## Common Pitfalls

### 1. Not Using ResponsiveContainer

```typescript
// ❌ Bad: Chart won't resize
<LineChart width={600} height={400} data={data} />

// ✅ Good: Responsive
<ResponsiveContainer width="100%" height={400}>
  <LineChart data={data} />
</ResponsiveContainer>
```

### 2. Large Datasets Without Optimization

```typescript
// ❌ Bad: Rendering 10,000 points
<LineChart data={hugeDataset} />

// ✅ Good: Sample or paginate data
const sampledData = hugeDataset.filter((_, i) => i % 10 === 0)
<LineChart data={sampledData} />
```

### 3. Not Handling Empty Data

```typescript
// ✅ Good: Handle empty state
{data.length > 0 ? (
  <LineChart data={data}>
    <Line dataKey="value" />
  </LineChart>
) : (
  <div>No data available</div>
)}
```

## Resources

### Official Documentation
- [Recharts Docs](https://recharts.org/)
- [Recharts GitHub](https://github.com/recharts/recharts)
- [API Reference](https://recharts.org/en-US/api)

### Examples
- [Recharts Examples](https://recharts.org/en-US/examples)
- [CodeSandbox Examples](https://codesandbox.io/examples/package/recharts)

### Community
- [GitHub Issues](https://github.com/recharts/recharts/issues)
- [Stack Overflow](https://stackoverflow.com/questions/tagged/recharts)

---

**Next Steps:**
- Explore [Chart.js](./chartjs.md) for canvas-based charts
- Learn [Visx](./visx.md) for custom visualizations
- Check out [D3.js](https://d3js.org/) for advanced data visualization
