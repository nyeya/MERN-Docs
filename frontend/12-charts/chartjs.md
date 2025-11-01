# Chart.js with React - Simple Yet Flexible Charting

## Overview

Chart.js is a simple yet flexible JavaScript charting library that uses HTML5 Canvas for rendering. With react-chartjs-2, you can easily integrate Chart.js into React applications. It's known for its simplicity, performance, and beautiful default styling, making it perfect for quickly adding charts to your application.

**Key Features:**
- 8 chart types out of the box
- Canvas-based rendering (fast)
- Responsive and animated
- Small bundle size (~200KB)
- Extensive plugin ecosystem
- Good documentation
- Easy to use
- Customizable
- Framework agnostic

## Why Use Chart.js?

### Problems It Solves

1. **Simplicity**: Easy to get started with minimal code
2. **Performance**: Canvas rendering for better performance
3. **Bundle Size**: Smaller than most alternatives
4. **Flexibility**: Highly customizable
5. **Plugins**: Rich ecosystem of plugins
6. **Cross-browser**: Works everywhere
7. **Beautiful Defaults**: Great out-of-the-box styling

### When to Use Chart.js

- ✅ Quick prototyping
- ✅ Standard chart types
- ✅ Performance-critical apps
- ✅ Small bundle size needed
- ✅ Beautiful default styling
- ✅ Any JavaScript framework
- ✅ Canvas-based rendering preferred

### When NOT to Use Chart.js

- ❌ Complex custom visualizations
- ❌ Need SVG-based charts
- ❌ React-first API preferred (use Recharts)
- ❌ Very large datasets

## Comparison with Alternatives

### Chart.js vs Recharts

| Feature | Chart.js | Recharts |
|---------|----------|----------|
| Rendering | Canvas | SVG |
| API | Imperative | Declarative |
| Bundle Size | ~200KB | ~400KB |
| React Integration | Wrapper | Native |
| Performance | Better | Good |
| Customization | Good | Better |
| Best For | Performance | React apps |

**Choose Chart.js if:** You want performance and smaller bundle
**Choose Recharts if:** You prefer React component API

## Installation & Setup

### Installation

```bash
# npm
npm install chart.js react-chartjs-2

# yarn
yarn add chart.js react-chartjs-2

# pnpm
pnpm add chart.js react-chartjs-2
```

### TypeScript Setup

```bash
npm install @types/chart.js --save-dev
```

### Basic Setup

```typescript
import { Chart as ChartJS, CategoryScale, LinearScale, PointElement, LineElement, Title, Tooltip, Legend } from 'chart.js'
import { Line } from 'react-chartjs-2'

// Register Chart.js components
ChartJS.register(
  CategoryScale,
  LinearScale,
  PointElement,
  LineElement,
  Title,
  Tooltip,
  Legend
)

const data = {
  labels: ['Jan', 'Feb', 'Mar', 'Apr', 'May'],
  datasets: [
    {
      label: 'Sales',
      data: [12, 19, 3, 5, 2],
      borderColor: 'rgb(59, 130, 246)',
      backgroundColor: 'rgba(59, 130, 246, 0.5)',
    }
  ]
}

function Chart() {
  return <Line data={data} />
}
```

## Core Concepts

### 1. Chart Types

```typescript
import { Line, Bar, Pie, Doughnut, Radar, PolarArea, Bubble, Scatter } from 'react-chartjs-2'

// Line Chart
<Line data={data} />

// Bar Chart
<Bar data={data} />

// Pie Chart
<Pie data={data} />

// Doughnut Chart
<Doughnut data={data} />

// Radar Chart
<Radar data={data} />
```

### 2. Data Structure

```typescript
const data = {
  labels: ['January', 'February', 'March'],
  datasets: [
    {
      label: 'Dataset 1',
      data: [65, 59, 80],
      borderColor: 'rgb(59, 130, 246)',
      backgroundColor: 'rgba(59, 130, 246, 0.5)',
    }
  ]
}
```

### 3. Options

```typescript
const options = {
  responsive: true,
  plugins: {
    legend: {
      position: 'top' as const,
    },
    title: {
      display: true,
      text: 'Chart Title'
    }
  },
  scales: {
    y: {
      beginAtZero: true
    }
  }
}

<Line data={data} options={options} />
```

## Basic Usage

### Simple Line Chart

```typescript
import { Chart as ChartJS, CategoryScale, LinearScale, PointElement, LineElement, Title, Tooltip, Legend } from 'chart.js'
import { Line } from 'react-chartjs-2'

ChartJS.register(CategoryScale, LinearScale, PointElement, LineElement, Title, Tooltip, Legend)

function LineChart() {
  const data = {
    labels: ['Jan', 'Feb', 'Mar', 'Apr', 'May', 'Jun'],
    datasets: [
      {
        label: 'Revenue',
        data: [12000, 19000, 15000, 25000, 22000, 30000],
        borderColor: 'rgb(59, 130, 246)',
        backgroundColor: 'rgba(59, 130, 246, 0.5)',
        tension: 0.4
      }
    ]
  }

  const options = {
    responsive: true,
    plugins: {
      legend: {
        position: 'top' as const,
      },
      title: {
        display: true,
        text: 'Monthly Revenue'
      }
    }
  }

  return <Line data={data} options={options} />
}
```

### Bar Chart

```typescript
import { Chart as ChartJS, CategoryScale, LinearScale, BarElement, Title, Tooltip, Legend } from 'chart.js'
import { Bar } from 'react-chartjs-2'

ChartJS.register(CategoryScale, LinearScale, BarElement, Title, Tooltip, Legend)

function BarChart() {
  const data = {
    labels: ['Product A', 'Product B', 'Product C', 'Product D'],
    datasets: [
      {
        label: 'Sales',
        data: [12, 19, 3, 5],
        backgroundColor: [
          'rgba(59, 130, 246, 0.8)',
          'rgba(16, 185, 129, 0.8)',
          'rgba(245, 158, 11, 0.8)',
          'rgba(239, 68, 68, 0.8)',
        ],
      }
    ]
  }

  const options = {
    responsive: true,
    plugins: {
      legend: {
        display: false
      },
      title: {
        display: true,
        text: 'Product Sales'
      }
    }
  }

  return <Bar data={data} options={options} />
}
```

### Pie Chart

```typescript
import { Chart as ChartJS, ArcElement, Tooltip, Legend } from 'chart.js'
import { Pie } from 'react-chartjs-2'

ChartJS.register(ArcElement, Tooltip, Legend)

function PieChart() {
  const data = {
    labels: ['Desktop', 'Mobile', 'Tablet'],
    datasets: [
      {
        label: 'Users',
        data: [300, 150, 100],
        backgroundColor: [
          'rgba(59, 130, 246, 0.8)',
          'rgba(16, 185, 129, 0.8)',
          'rgba(245, 158, 11, 0.8)',
        ],
        borderColor: [
          'rgb(59, 130, 246)',
          'rgb(16, 185, 129)',
          'rgb(245, 158, 11)',
        ],
        borderWidth: 1,
      }
    ]
  }

  return <Pie data={data} />
}
```

## Advanced Usage

### 1. Multi-Dataset Chart

```typescript
import { Line } from 'react-chartjs-2'

function MultiLineChart() {
  const data = {
    labels: ['Jan', 'Feb', 'Mar', 'Apr', 'May', 'Jun'],
    datasets: [
      {
        label: 'Revenue',
        data: [12000, 19000, 15000, 25000, 22000, 30000],
        borderColor: 'rgb(59, 130, 246)',
        backgroundColor: 'rgba(59, 130, 246, 0.5)',
      },
      {
        label: 'Expenses',
        data: [8000, 12000, 10000, 15000, 14000, 18000],
        borderColor: 'rgb(239, 68, 68)',
        backgroundColor: 'rgba(239, 68, 68, 0.5)',
      }
    ]
  }

  const options = {
    responsive: true,
    interaction: {
      mode: 'index' as const,
      intersect: false,
    },
    plugins: {
      title: {
        display: true,
        text: 'Revenue vs Expenses'
      }
    }
  }

  return <Line data={data} options={options} />
}
```

### 2. Custom Tooltip

```typescript
import { Bar } from 'react-chartjs-2'

function ChartWithCustomTooltip() {
  const options = {
    responsive: true,
    plugins: {
      tooltip: {
        callbacks: {
          label: function(context: any) {
            let label = context.dataset.label || ''
            if (label) {
              label += ': '
            }
            if (context.parsed.y !== null) {
              label += new Intl.NumberFormat('en-US', {
                style: 'currency',
                currency: 'USD'
              }).format(context.parsed.y)
            }
            return label
          }
        }
      }
    }
  }

  return <Bar data={data} options={options} />
}
```

### 3. Gradient Fill

```typescript
import { useEffect, useRef } from 'react'
import { Line } from 'react-chartjs-2'

function GradientChart() {
  const chartRef = useRef<any>(null)

  const data = {
    labels: ['Jan', 'Feb', 'Mar', 'Apr', 'May'],
    datasets: [
      {
        label: 'Sales',
        data: [12, 19, 3, 5, 2],
        borderColor: 'rgb(59, 130, 246)',
        backgroundColor: (context: any) => {
          const ctx = context.chart.ctx
          const gradient = ctx.createLinearGradient(0, 0, 0, 400)
          gradient.addColorStop(0, 'rgba(59, 130, 246, 0.8)')
          gradient.addColorStop(1, 'rgba(59, 130, 246, 0)')
          return gradient
        },
        fill: true,
      }
    ]
  }

  return <Line ref={chartRef} data={data} />
}
```

### 4. Real-Time Data

```typescript
import { useState, useEffect } from 'react'
import { Line } from 'react-chartjs-2'

function RealTimeChart() {
  const [data, setData] = useState({
    labels: [],
    datasets: [
      {
        label: 'Value',
        data: [],
        borderColor: 'rgb(59, 130, 246)',
        backgroundColor: 'rgba(59, 130, 246, 0.5)',
      }
    ]
  })

  useEffect(() => {
    const interval = setInterval(() => {
      setData(prevData => {
        const newLabels = [...prevData.labels, new Date().toLocaleTimeString()]
        const newData = [...prevData.datasets[0].data, Math.random() * 100]

        // Keep last 20 points
        if (newLabels.length > 20) {
          newLabels.shift()
          newData.shift()
        }

        return {
          labels: newLabels,
          datasets: [
            {
              ...prevData.datasets[0],
              data: newData
            }
          ]
        }
      })
    }, 1000)

    return () => clearInterval(interval)
  }, [])

  const options = {
    responsive: true,
    animation: {
      duration: 0
    },
    scales: {
      y: {
        beginAtZero: true,
        max: 100
      }
    }
  }

  return <Line data={data} options={options} />
}
```

### 5. Mixed Chart Types

```typescript
import { Chart as ChartJS, CategoryScale, LinearScale, BarElement, LineElement, PointElement, Title, Tooltip, Legend } from 'chart.js'
import { Chart } from 'react-chartjs-2'

ChartJS.register(CategoryScale, LinearScale, BarElement, LineElement, PointElement, Title, Tooltip, Legend)

function MixedChart() {
  const data = {
    labels: ['Jan', 'Feb', 'Mar', 'Apr', 'May'],
    datasets: [
      {
        type: 'bar' as const,
        label: 'Sales',
        data: [12, 19, 3, 5, 2],
        backgroundColor: 'rgba(59, 130, 246, 0.5)',
      },
      {
        type: 'line' as const,
        label: 'Target',
        data: [15, 15, 15, 15, 15],
        borderColor: 'rgb(239, 68, 68)',
        borderDash: [5, 5],
      }
    ]
  }

  return <Chart type='bar' data={data} />
}
```

## Real-World Examples

### Dashboard with Multiple Charts

```typescript
import { Line, Bar, Doughnut } from 'react-chartjs-2'

function Dashboard() {
  const lineData = {
    labels: ['Jan', 'Feb', 'Mar', 'Apr', 'May', 'Jun'],
    datasets: [
      {
        label: 'Revenue',
        data: [12000, 19000, 15000, 25000, 22000, 30000],
        borderColor: 'rgb(59, 130, 246)',
        backgroundColor: 'rgba(59, 130, 246, 0.5)',
      }
    ]
  }

  const barData = {
    labels: ['Product A', 'Product B', 'Product C'],
    datasets: [
      {
        label: 'Sales',
        data: [12, 19, 3],
        backgroundColor: 'rgba(16, 185, 129, 0.8)',
      }
    ]
  }

  const doughnutData = {
    labels: ['Desktop', 'Mobile', 'Tablet'],
    datasets: [
      {
        data: [300, 150, 100],
        backgroundColor: [
          'rgba(59, 130, 246, 0.8)',
          'rgba(16, 185, 129, 0.8)',
          'rgba(245, 158, 11, 0.8)',
        ],
      }
    ]
  }

  return (
    <div className="grid grid-cols-2 gap-6">
      <div className="bg-white p-6 rounded-lg shadow">
        <h3 className="text-lg font-bold mb-4">Revenue Trend</h3>
        <Line data={lineData} />
      </div>
      <div className="bg-white p-6 rounded-lg shadow">
        <h3 className="text-lg font-bold mb-4">Top Products</h3>
        <Bar data={barData} />
      </div>
      <div className="bg-white p-6 rounded-lg shadow">
        <h3 className="text-lg font-bold mb-4">Device Distribution</h3>
        <Doughnut data={doughnutData} />
      </div>
    </div>
  )
}
```

## Best Practices

### 1. Register Components Once

```typescript
// ✅ Good: Register in a separate file
// chartConfig.ts
import { Chart as ChartJS, CategoryScale, LinearScale, PointElement, LineElement } from 'chart.js'

ChartJS.register(CategoryScale, LinearScale, PointElement, LineElement)

// ❌ Bad: Register in every component
```

### 2. Memoize Data and Options

```typescript
import { useMemo } from 'react'

// ✅ Good: Memoized
const data = useMemo(() => ({
  labels: ['Jan', 'Feb', 'Mar'],
  datasets: [{ data: [1, 2, 3] }]
}), [])
```

### 3. Handle Responsive Sizing

```typescript
// ✅ Good: Responsive container
<div style={{ position: 'relative', height: '400px' }}>
  <Line data={data} options={{ maintainAspectRatio: false }} />
</div>
```

## Common Pitfalls

### 1. Not Registering Components

```typescript
// ❌ Bad: Forgot to register
import { Line } from 'react-chartjs-2'
<Line data={data} /> // Error!

// ✅ Good: Register first
import { Chart as ChartJS, CategoryScale, LinearScale, PointElement, LineElement } from 'chart.js'
ChartJS.register(CategoryScale, LinearScale, PointElement, LineElement)
```

### 2. Mutating Data

```typescript
// ❌ Bad: Mutating data
data.datasets[0].data.push(5)

// ✅ Good: Create new object
setData({
  ...data,
  datasets: [{
    ...data.datasets[0],
    data: [...data.datasets[0].data, 5]
  }]
})
```

### 3. Not Handling Empty Data

```typescript
// ✅ Good: Handle empty state
{data.datasets[0].data.length > 0 ? (
  <Line data={data} />
) : (
  <div>No data available</div>
)}
```

## Resources

### Official Documentation
- [Chart.js Docs](https://www.chartjs.org/)
- [react-chartjs-2 Docs](https://react-chartjs-2.js.org/)
- [Chart.js GitHub](https://github.com/chartjs/Chart.js)

### Plugins
- [chartjs-plugin-datalabels](https://chartjs-plugin-datalabels.netlify.app/)
- [chartjs-plugin-zoom](https://www.chartjs.org/chartjs-plugin-zoom/)
- [chartjs-plugin-annotation](https://www.chartjs.org/chartjs-plugin-annotation/)

### Community
- [Stack Overflow](https://stackoverflow.com/questions/tagged/chart.js)
- [GitHub Discussions](https://github.com/chartjs/Chart.js/discussions)

---

**Next Steps:**
- Explore [Recharts](./recharts.md) for React-first API
- Learn [Visx](./visx.md) for custom visualizations
- Check out [D3.js](https://d3js.org/) for advanced charts
