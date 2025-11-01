# TanStack Table - Headless UI for Building Tables

## Overview

TanStack Table (formerly React Table) is a headless UI library for building powerful tables and datagrids. It provides the logic and state management for tables while giving you complete control over the markup and styling. This makes it incredibly flexible and perfect for building custom table experiences.

**Key Features:**
- Headless UI (bring your own markup)
- Sorting, filtering, pagination
- Column resizing and reordering
- Row selection and expansion
- Virtualization support
- TypeScript support
- Framework agnostic (React, Vue, Solid, Svelte)
- Tree-shakeable
- Extensible with plugins
- Excellent performance

## Why Use TanStack Table?

### Problems It Solves

1. **Flexibility**: Complete control over UI
2. **Performance**: Optimized for large datasets
3. **Features**: Comprehensive table functionality
4. **Type Safety**: Excellent TypeScript support
5. **Customization**: No opinionated styling
6. **State Management**: Built-in state handling
7. **Extensibility**: Plugin architecture

### When to Use TanStack Table

- ✅ Need custom table UI
- ✅ Complex table requirements
- ✅ Large datasets
- ✅ Advanced features (sorting, filtering, etc.)
- ✅ TypeScript projects
- ✅ Full control over styling
- ✅ Performance-critical tables

### When NOT to Use TanStack Table

- ❌ Simple static tables (use HTML table)
- ❌ Need pre-styled components (use AG Grid)
- ❌ Quick prototyping
- ❌ No advanced features needed

## Comparison with Alternatives

### TanStack Table vs AG Grid

| Feature | TanStack Table | AG Grid |
|---------|----------------|---------|
| UI | Headless | Pre-styled |
| Customization | Unlimited | Limited |
| Bundle Size | ~15KB | ~500KB |
| Learning Curve | Moderate | Easy |
| Features | Excellent | Excellent |
| Pricing | Free | Free/Paid |
| Best For | Custom UI | Quick setup |

**Choose TanStack Table if:** You want complete UI control
**Choose AG Grid if:** You want pre-built UI components

## Installation & Setup

### Installation

```bash
# npm
npm install @tanstack/react-table

# yarn
yarn add @tanstack/react-table

# pnpm
pnpm add @tanstack/react-table
```

### TypeScript Setup

TanStack Table has built-in TypeScript support. No additional types needed!

### Basic Setup

```typescript
import {
  useReactTable,
  getCoreRowModel,
  flexRender,
  createColumnHelper
} from '@tanstack/react-table'

interface Person {
  firstName: string
  lastName: string
  age: number
}

const data: Person[] = [
  { firstName: 'John', lastName: 'Doe', age: 30 },
  { firstName: 'Jane', lastName: 'Smith', age: 25 }
]

const columnHelper = createColumnHelper<Person>()

const columns = [
  columnHelper.accessor('firstName', {
    header: 'First Name',
  }),
  columnHelper.accessor('lastName', {
    header: 'Last Name',
  }),
  columnHelper.accessor('age', {
    header: 'Age',
  }),
]

function BasicTable() {
  const table = useReactTable({
    data,
    columns,
    getCoreRowModel: getCoreRowModel(),
  })

  return (
    <table>
      <thead>
        {table.getHeaderGroups().map(headerGroup => (
          <tr key={headerGroup.id}>
            {headerGroup.headers.map(header => (
              <th key={header.id}>
                {flexRender(header.column.columnDef.header, header.getContext())}
              </th>
            ))}
          </tr>
        ))}
      </thead>
      <tbody>
        {table.getRowModel().rows.map(row => (
          <tr key={row.id}>
            {row.getVisibleCells().map(cell => (
              <td key={cell.id}>
                {flexRender(cell.column.columnDef.cell, cell.getContext())}
              </td>
            ))}
          </tr>
        ))}
      </tbody>
    </table>
  )
}
```

## Core Concepts

### 1. Column Definitions

```typescript
import { createColumnHelper } from '@tanstack/react-table'

const columnHelper = createColumnHelper<Person>()

const columns = [
  // Accessor column
  columnHelper.accessor('firstName', {
    header: 'First Name',
    cell: info => info.getValue(),
  }),
  
  // Display column
  columnHelper.display({
    id: 'actions',
    header: 'Actions',
    cell: props => <button>Edit</button>,
  }),
  
  // Group column
  columnHelper.group({
    header: 'Name',
    columns: [
      columnHelper.accessor('firstName', { header: 'First' }),
      columnHelper.accessor('lastName', { header: 'Last' }),
    ],
  }),
]
```

### 2. Table Instance

```typescript
const table = useReactTable({
  data,
  columns,
  getCoreRowModel: getCoreRowModel(),
  // Add more features
  getSortedRowModel: getSortedRowModel(),
  getFilteredRowModel: getFilteredRowModel(),
  getPaginationRowModel: getPaginationRowModel(),
})
```

### 3. Rendering

```typescript
// Render headers
{table.getHeaderGroups().map(headerGroup => (
  <tr key={headerGroup.id}>
    {headerGroup.headers.map(header => (
      <th key={header.id}>
        {flexRender(header.column.columnDef.header, header.getContext())}
      </th>
    ))}
  </tr>
))}

// Render rows
{table.getRowModel().rows.map(row => (
  <tr key={row.id}>
    {row.getVisibleCells().map(cell => (
      <td key={cell.id}>
        {flexRender(cell.column.columnDef.cell, cell.getContext())}
      </td>
    ))}
  </tr>
))}
```

## Basic Usage

### Table with Sorting

```typescript
import { useReactTable, getCoreRowModel, getSortedRowModel, SortingState } from '@tanstack/react-table'
import { useState } from 'react'

function SortableTable() {
  const [sorting, setSorting] = useState<SortingState>([])

  const table = useReactTable({
    data,
    columns,
    state: {
      sorting,
    },
    onSortingChange: setSorting,
    getCoreRowModel: getCoreRowModel(),
    getSortedRowModel: getSortedRowModel(),
  })

  return (
    <table>
      <thead>
        {table.getHeaderGroups().map(headerGroup => (
          <tr key={headerGroup.id}>
            {headerGroup.headers.map(header => (
              <th key={header.id}>
                {header.isPlaceholder ? null : (
                  <div
                    onClick={header.column.getToggleSortingHandler()}
                    style={{ cursor: 'pointer' }}
                  >
                    {flexRender(header.column.columnDef.header, header.getContext())}
                    {{
                      asc: ' 🔼',
                      desc: ' 🔽',
                    }[header.column.getIsSorted() as string] ?? null}
                  </div>
                )}
              </th>
            ))}
          </tr>
        ))}
      </thead>
      <tbody>
        {table.getRowModel().rows.map(row => (
          <tr key={row.id}>
            {row.getVisibleCells().map(cell => (
              <td key={cell.id}>
                {flexRender(cell.column.columnDef.cell, cell.getContext())}
              </td>
            ))}
          </tr>
        ))}
      </tbody>
    </table>
  )
}
```

### Table with Filtering

```typescript
import { useReactTable, getCoreRowModel, getFilteredRowModel } from '@tanstack/react-table'
import { useState } from 'react'

function FilterableTable() {
  const [globalFilter, setGlobalFilter] = useState('')

  const table = useReactTable({
    data,
    columns,
    state: {
      globalFilter,
    },
    onGlobalFilterChange: setGlobalFilter,
    getCoreRowModel: getCoreRowModel(),
    getFilteredRowModel: getFilteredRowModel(),
  })

  return (
    <div>
      <input
        value={globalFilter ?? ''}
        onChange={e => setGlobalFilter(e.target.value)}
        placeholder="Search all columns..."
      />
      <table>
        {/* Table content */}
      </table>
    </div>
  )
}
```

### Table with Pagination

```typescript
import { useReactTable, getCoreRowModel, getPaginationRowModel } from '@tanstack/react-table'

function PaginatedTable() {
  const table = useReactTable({
    data,
    columns,
    getCoreRowModel: getCoreRowModel(),
    getPaginationRowModel: getPaginationRowModel(),
    initialState: {
      pagination: {
        pageSize: 10,
      },
    },
  })

  return (
    <div>
      <table>
        {/* Table content */}
      </table>
      <div className="flex items-center gap-2">
        <button
          onClick={() => table.setPageIndex(0)}
          disabled={!table.getCanPreviousPage()}
        >
          {'<<'}
        </button>
        <button
          onClick={() => table.previousPage()}
          disabled={!table.getCanPreviousPage()}
        >
          {'<'}
        </button>
        <button
          onClick={() => table.nextPage()}
          disabled={!table.getCanNextPage()}
        >
          {'>'}
        </button>
        <button
          onClick={() => table.setPageIndex(table.getPageCount() - 1)}
          disabled={!table.getCanNextPage()}
        >
          {'>>'}
        </button>
        <span>
          Page {table.getState().pagination.pageIndex + 1} of {table.getPageCount()}
        </span>
      </div>
    </div>
  )
}
```

## Advanced Usage

### 1. Row Selection

```typescript
import { useReactTable, getCoreRowModel, RowSelectionState } from '@tanstack/react-table'
import { useState } from 'react'

function SelectableTable() {
  const [rowSelection, setRowSelection] = useState<RowSelectionState>({})

  const columns = [
    {
      id: 'select',
      header: ({ table }) => (
        <input
          type="checkbox"
          checked={table.getIsAllRowsSelected()}
          onChange={table.getToggleAllRowsSelectedHandler()}
        />
      ),
      cell: ({ row }) => (
        <input
          type="checkbox"
          checked={row.getIsSelected()}
          onChange={row.getToggleSelectedHandler()}
        />
      ),
    },
    ...otherColumns
  ]

  const table = useReactTable({
    data,
    columns,
    state: {
      rowSelection,
    },
    onRowSelectionChange: setRowSelection,
    getCoreRowModel: getCoreRowModel(),
    enableRowSelection: true,
  })

  return (
    <div>
      <div>{Object.keys(rowSelection).length} rows selected</div>
      <table>
        {/* Table content */}
      </table>
    </div>
  )
}
```

### 2. Column Visibility

```typescript
import { useReactTable, VisibilityState } from '@tanstack/react-table'
import { useState } from 'react'

function TableWithColumnToggle() {
  const [columnVisibility, setColumnVisibility] = useState<VisibilityState>({})

  const table = useReactTable({
    data,
    columns,
    state: {
      columnVisibility,
    },
    onColumnVisibilityChange: setColumnVisibility,
    getCoreRowModel: getCoreRowModel(),
  })

  return (
    <div>
      <div className="flex gap-2 mb-4">
        {table.getAllLeafColumns().map(column => (
          <label key={column.id}>
            <input
              type="checkbox"
              checked={column.getIsVisible()}
              onChange={column.getToggleVisibilityHandler()}
            />
            {column.id}
          </label>
        ))}
      </div>
      <table>
        {/* Table content */}
      </table>
    </div>
  )
}
```

### 3. Expandable Rows

```typescript
import { useReactTable, getExpandedRowModel, ExpandedState } from '@tanstack/react-table'
import { useState } from 'react'

function ExpandableTable() {
  const [expanded, setExpanded] = useState<ExpandedState>({})

  const columns = [
    {
      id: 'expander',
      header: () => null,
      cell: ({ row }) => (
        row.getCanExpand() ? (
          <button onClick={row.getToggleExpandedHandler()}>
            {row.getIsExpanded() ? '👇' : '👉'}
          </button>
        ) : null
      ),
    },
    ...otherColumns
  ]

  const table = useReactTable({
    data,
    columns,
    state: {
      expanded,
    },
    onExpandedChange: setExpanded,
    getCoreRowModel: getCoreRowModel(),
    getExpandedRowModel: getExpandedRowModel(),
    getSubRows: row => row.subRows,
  })

  return <table>{/* Table content */}</table>
}
```

### 4. Custom Cell Rendering

```typescript
const columns = [
  columnHelper.accessor('status', {
    header: 'Status',
    cell: info => {
      const status = info.getValue()
      return (
        <span className={`badge badge-${status}`}>
          {status}
        </span>
      )
    },
  }),
  columnHelper.accessor('amount', {
    header: 'Amount',
    cell: info => {
      const amount = info.getValue()
      return new Intl.NumberFormat('en-US', {
        style: 'currency',
        currency: 'USD'
      }).format(amount)
    },
  }),
]
```

## Real-World Examples

### Complete Data Table

```typescript
import {
  useReactTable,
  getCoreRowModel,
  getSortedRowModel,
  getFilteredRowModel,
  getPaginationRowModel,
  flexRender,
  createColumnHelper
} from '@tanstack/react-table'
import { useState } from 'react'

interface User {
  id: number
  name: string
  email: string
  role: string
  status: 'active' | 'inactive'
}

function UserTable({ data }: { data: User[] }) {
  const [sorting, setSorting] = useState([])
  const [filtering, setFiltering] = useState('')

  const columnHelper = createColumnHelper<User>()

  const columns = [
    columnHelper.accessor('name', {
      header: 'Name',
    }),
    columnHelper.accessor('email', {
      header: 'Email',
    }),
    columnHelper.accessor('role', {
      header: 'Role',
    }),
    columnHelper.accessor('status', {
      header: 'Status',
      cell: info => (
        <span className={`px-2 py-1 rounded ${
          info.getValue() === 'active' ? 'bg-green-100 text-green-800' : 'bg-gray-100 text-gray-800'
        }`}>
          {info.getValue()}
        </span>
      ),
    }),
    columnHelper.display({
      id: 'actions',
      header: 'Actions',
      cell: props => (
        <div className="flex gap-2">
          <button className="text-blue-600">Edit</button>
          <button className="text-red-600">Delete</button>
        </div>
      ),
    }),
  ]

  const table = useReactTable({
    data,
    columns,
    state: {
      sorting,
      globalFilter: filtering,
    },
    onSortingChange: setSorting,
    onGlobalFilterChange: setFiltering,
    getCoreRowModel: getCoreRowModel(),
    getSortedRowModel: getSortedRowModel(),
    getFilteredRowModel: getFilteredRowModel(),
    getPaginationRowModel: getPaginationRowModel(),
  })

  return (
    <div className="bg-white rounded-lg shadow">
      <div className="p-4">
        <input
          value={filtering}
          onChange={e => setFiltering(e.target.value)}
          placeholder="Search..."
          className="px-4 py-2 border rounded-lg"
        />
      </div>
      <table className="w-full">
        <thead className="bg-gray-50">
          {table.getHeaderGroups().map(headerGroup => (
            <tr key={headerGroup.id}>
              {headerGroup.headers.map(header => (
                <th
                  key={header.id}
                  className="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider cursor-pointer"
                  onClick={header.column.getToggleSortingHandler()}
                >
                  {flexRender(header.column.columnDef.header, header.getContext())}
                  {{
                    asc: ' 🔼',
                    desc: ' 🔽',
                  }[header.column.getIsSorted() as string] ?? null}
                </th>
              ))}
            </tr>
          ))}
        </thead>
        <tbody className="bg-white divide-y divide-gray-200">
          {table.getRowModel().rows.map(row => (
            <tr key={row.id}>
              {row.getVisibleCells().map(cell => (
                <td key={cell.id} className="px-6 py-4 whitespace-nowrap">
                  {flexRender(cell.column.columnDef.cell, cell.getContext())}
                </td>
              ))}
            </tr>
          ))}
        </tbody>
      </table>
      <div className="px-6 py-4 flex items-center justify-between border-t">
        <div className="flex gap-2">
          <button
            onClick={() => table.previousPage()}
            disabled={!table.getCanPreviousPage()}
            className="px-4 py-2 border rounded disabled:opacity-50"
          >
            Previous
          </button>
          <button
            onClick={() => table.nextPage()}
            disabled={!table.getCanNextPage()}
            className="px-4 py-2 border rounded disabled:opacity-50"
          >
            Next
          </button>
        </div>
        <span>
          Page {table.getState().pagination.pageIndex + 1} of {table.getPageCount()}
        </span>
      </div>
    </div>
  )
}
```

## Best Practices

### 1. Memoize Columns

```typescript
import { useMemo } from 'react'

// ✅ Good: Memoized columns
const columns = useMemo(() => [
  columnHelper.accessor('name', { header: 'Name' }),
], [])
```

### 2. Use Column Helper

```typescript
// ✅ Good: Type-safe column helper
const columnHelper = createColumnHelper<Person>()
const columns = [
  columnHelper.accessor('firstName', { header: 'First Name' }),
]

// ❌ Bad: Manual column definition
const columns = [
  { accessorKey: 'firstName', header: 'First Name' },
]
```

### 3. Handle Loading and Empty States

```typescript
// ✅ Good: Handle states
{isLoading ? (
  <div>Loading...</div>
) : data.length === 0 ? (
  <div>No data</div>
) : (
  <table>{/* Table content */}</table>
)}
```

## Common Pitfalls

### 1. Not Memoizing Columns

```typescript
// ❌ Bad: Recreates columns on every render
const columns = [
  columnHelper.accessor('name', { header: 'Name' }),
]

// ✅ Good: Memoized
const columns = useMemo(() => [
  columnHelper.accessor('name', { header: 'Name' }),
], [])
```

### 2. Forgetting flexRender

```typescript
// ❌ Bad: Direct render
<th>{header.column.columnDef.header}</th>

// ✅ Good: Use flexRender
<th>{flexRender(header.column.columnDef.header, header.getContext())}</th>
```

## Resources

### Official Documentation
- [TanStack Table Docs](https://tanstack.com/table/)
- [TanStack Table GitHub](https://github.com/TanStack/table)
- [API Reference](https://tanstack.com/table/v8/docs/api/core/table)

### Examples
- [TanStack Table Examples](https://tanstack.com/table/v8/docs/examples/react/basic)
- [CodeSandbox Examples](https://codesandbox.io/examples/package/@tanstack/react-table)

### Community
- [GitHub Discussions](https://github.com/TanStack/table/discussions)
- [Discord](https://discord.com/invite/WrRKjPJ)

---

**Next Steps:**
- Explore [AG Grid](./ag-grid.md) for pre-styled tables
- Learn [TanStack Virtual](https://tanstack.com/virtual/) for virtualization
- Check out [TanStack Query](../02-data-fetching/tanstack-query.md) for data fetching
