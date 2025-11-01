# AG Grid - Feature-Rich Data Grid

## Overview

AG Grid is a feature-rich, enterprise-grade data grid for React applications. It provides a comprehensive set of features out of the box, including sorting, filtering, grouping, pivoting, and more. AG Grid is known for its excellent performance with large datasets and extensive customization options.

**Key Features:**
- Enterprise-grade features
- Excellent performance
- Virtual scrolling
- Cell editing
- Row grouping and aggregation
- Pivoting
- Excel export
- Charting integration
- Themes and styling
- TypeScript support

## Why Use AG Grid?

### Problems It Solves

1. **Performance**: Handles millions of rows efficiently
2. **Features**: Comprehensive built-in functionality
3. **Enterprise**: Production-ready for business apps
4. **Customization**: Highly customizable
5. **Excel Integration**: Import/export Excel files
6. **Charting**: Built-in charting capabilities
7. **Support**: Commercial support available

### When to Use AG Grid

- ✅ Enterprise applications
- ✅ Large datasets (100k+ rows)
- ✅ Complex grid requirements
- ✅ Excel-like functionality needed
- ✅ Need commercial support
- ✅ Financial/data-heavy apps
- ✅ Advanced features required

### When NOT to Use AG Grid

- ❌ Simple tables (use TanStack Table)
- ❌ Need complete UI control
- ❌ Bundle size is critical
- ❌ Free features insufficient

## Comparison with Alternatives

### AG Grid vs TanStack Table

| Feature | AG Grid | TanStack Table |
|---------|---------|----------------|
| UI | Pre-styled | Headless |
| Features | Extensive | Good |
| Bundle Size | ~500KB | ~15KB |
| Performance | Excellent | Excellent |
| Customization | Good | Unlimited |
| Pricing | Free/Paid | Free |
| Best For | Enterprise | Custom UI |

**Choose AG Grid if:** You need enterprise features quickly
**Choose TanStack Table if:** You want complete UI control

## Installation & Setup

### Installation

```bash
# Community (Free)
npm install ag-grid-react ag-grid-community

# Enterprise (Paid)
npm install ag-grid-react ag-grid-enterprise
```

### TypeScript Setup

AG Grid has built-in TypeScript support. No additional types needed!

### Basic Setup

```typescript
import { AgGridReact } from 'ag-grid-react'
import 'ag-grid-community/styles/ag-grid.css'
import 'ag-grid-community/styles/ag-theme-alpine.css'

interface Row {
  make: string
  model: string
  price: number
}

function BasicGrid() {
  const rowData: Row[] = [
    { make: 'Toyota', model: 'Celica', price: 35000 },
    { make: 'Ford', model: 'Mondeo', price: 32000 },
    { make: 'Porsche', model: 'Boxster', price: 72000 }
  ]

  const columnDefs = [
    { field: 'make' },
    { field: 'model' },
    { field: 'price' }
  ]

  return (
    <div className="ag-theme-alpine" style={{ height: 400, width: 600 }}>
      <AgGridReact
        rowData={rowData}
        columnDefs={columnDefs}
      />
    </div>
  )
}
```

## Core Concepts

### 1. Column Definitions

```typescript
import { ColDef } from 'ag-grid-community'

const columnDefs: ColDef[] = [
  {
    field: 'make',
    headerName: 'Make',
    sortable: true,
    filter: true,
    width: 150
  },
  {
    field: 'price',
    headerName: 'Price',
    valueFormatter: params => `$${params.value.toLocaleString()}`,
    cellStyle: { textAlign: 'right' }
  }
]
```

### 2. Grid Options

```typescript
const gridOptions = {
  defaultColDef: {
    sortable: true,
    filter: true,
    resizable: true
  },
  pagination: true,
  paginationPageSize: 10,
  rowSelection: 'multiple',
  animateRows: true
}

<AgGridReact
  rowData={rowData}
  columnDefs={columnDefs}
  {...gridOptions}
/>
```

### 3. Grid API

```typescript
import { useRef } from 'react'
import { AgGridReact } from 'ag-grid-react'

function GridWithAPI() {
  const gridRef = useRef<AgGridReact>(null)

  const onExportClick = () => {
    gridRef.current?.api.exportDataAsCsv()
  }

  return (
    <div>
      <button onClick={onExportClick}>Export CSV</button>
      <AgGridReact
        ref={gridRef}
        rowData={rowData}
        columnDefs={columnDefs}
      />
    </div>
  )
}
```

## Basic Usage

### Sortable and Filterable Grid

```typescript
import { AgGridReact } from 'ag-grid-react'
import { ColDef } from 'ag-grid-community'
import 'ag-grid-community/styles/ag-grid.css'
import 'ag-grid-community/styles/ag-theme-alpine.css'

function DataGrid() {
  const columnDefs: ColDef[] = [
    { field: 'name', sortable: true, filter: true },
    { field: 'email', sortable: true, filter: true },
    { field: 'age', sortable: true, filter: 'agNumberColumnFilter' },
    { field: 'country', sortable: true, filter: true }
  ]

  const defaultColDef: ColDef = {
    flex: 1,
    minWidth: 100,
    resizable: true
  }

  return (
    <div className="ag-theme-alpine" style={{ height: 500, width: '100%' }}>
      <AgGridReact
        rowData={rowData}
        columnDefs={columnDefs}
        defaultColDef={defaultColDef}
        animateRows={true}
      />
    </div>
  )
}
```

### Grid with Pagination

```typescript
function PaginatedGrid() {
  return (
    <div className="ag-theme-alpine" style={{ height: 500, width: '100%' }}>
      <AgGridReact
        rowData={rowData}
        columnDefs={columnDefs}
        pagination={true}
        paginationPageSize={20}
        paginationPageSizeSelector={[10, 20, 50, 100]}
      />
    </div>
  )
}
```

### Grid with Row Selection

```typescript
import { useState } from 'react'

function SelectableGrid() {
  const [selectedRows, setSelectedRows] = useState([])

  const onSelectionChanged = (event: any) => {
    const selected = event.api.getSelectedRows()
    setSelectedRows(selected)
  }

  return (
    <div>
      <div>Selected: {selectedRows.length} rows</div>
      <div className="ag-theme-alpine" style={{ height: 500, width: '100%' }}>
        <AgGridReact
          rowData={rowData}
          columnDefs={columnDefs}
          rowSelection="multiple"
          onSelectionChanged={onSelectionChanged}
        />
      </div>
    </div>
  )
}
```

## Advanced Usage

### 1. Cell Editing

```typescript
const columnDefs: ColDef[] = [
  { field: 'name', editable: true },
  { field: 'price', editable: true, cellEditor: 'agNumberCellEditor' },
  {
    field: 'status',
    editable: true,
    cellEditor: 'agSelectCellEditor',
    cellEditorParams: {
      values: ['Active', 'Inactive', 'Pending']
    }
  }
]

function EditableGrid() {
  const onCellValueChanged = (event: any) => {
    console.log('Cell value changed:', event.data)
  }

  return (
    <AgGridReact
      rowData={rowData}
      columnDefs={columnDefs}
      onCellValueChanged={onCellValueChanged}
    />
  )
}
```

### 2. Custom Cell Renderer

```typescript
const columnDefs: ColDef[] = [
  {
    field: 'status',
    cellRenderer: (params: any) => {
      const status = params.value
      const color = status === 'Active' ? 'green' : 'red'
      return `<span style="color: ${color}">${status}</span>`
    }
  },
  {
    field: 'actions',
    cellRenderer: (params: any) => {
      return `
        <button onclick="window.editRow(${params.node.id})">Edit</button>
        <button onclick="window.deleteRow(${params.node.id})">Delete</button>
      `
    }
  }
]
```

### 3. Row Grouping

```typescript
const columnDefs: ColDef[] = [
  { field: 'country', rowGroup: true, hide: true },
  { field: 'year', rowGroup: true, hide: true },
  { field: 'athlete' },
  { field: 'gold', aggFunc: 'sum' },
  { field: 'silver', aggFunc: 'sum' },
  { field: 'bronze', aggFunc: 'sum' }
]

function GroupedGrid() {
  return (
    <AgGridReact
      rowData={rowData}
      columnDefs={columnDefs}
      groupDefaultExpanded={1}
      animateRows={true}
    />
  )
}
```

### 4. Master-Detail

```typescript
const columnDefs: ColDef[] = [
  { field: 'name', cellRenderer: 'agGroupCellRenderer' },
  { field: 'account' },
  { field: 'calls' }
]

const detailCellRendererParams = {
  detailGridOptions: {
    columnDefs: [
      { field: 'callId' },
      { field: 'direction' },
      { field: 'number' },
      { field: 'duration' }
    ]
  },
  getDetailRowData: (params: any) => {
    params.successCallback(params.data.callRecords)
  }
}

function MasterDetailGrid() {
  return (
    <AgGridReact
      rowData={rowData}
      columnDefs={columnDefs}
      masterDetail={true}
      detailCellRendererParams={detailCellRendererParams}
    />
  )
}
```

### 5. Excel Export

```typescript
import { useRef } from 'react'

function ExportableGrid() {
  const gridRef = useRef<AgGridReact>(null)

  const onBtnExport = () => {
    gridRef.current?.api.exportDataAsExcel({
      fileName: 'data-export.xlsx',
      sheetName: 'Data'
    })
  }

  return (
    <div>
      <button onClick={onBtnExport}>Export to Excel</button>
      <AgGridReact
        ref={gridRef}
        rowData={rowData}
        columnDefs={columnDefs}
      />
    </div>
  )
}
```

## Real-World Examples

### Complete Enterprise Grid

```typescript
import { AgGridReact } from 'ag-grid-react'
import { ColDef } from 'ag-grid-community'
import { useRef, useState, useMemo } from 'react'
import 'ag-grid-community/styles/ag-grid.css'
import 'ag-grid-community/styles/ag-theme-alpine.css'

interface User {
  id: number
  name: string
  email: string
  role: string
  status: string
  lastLogin: string
}

function UserManagementGrid() {
  const gridRef = useRef<AgGridReact>(null)
  const [rowData, setRowData] = useState<User[]>([])

  const columnDefs: ColDef[] = useMemo(() => [
    {
      field: 'id',
      headerName: 'ID',
      width: 80,
      checkboxSelection: true,
      headerCheckboxSelection: true
    },
    {
      field: 'name',
      headerName: 'Name',
      filter: 'agTextColumnFilter',
      editable: true
    },
    {
      field: 'email',
      headerName: 'Email',
      filter: 'agTextColumnFilter'
    },
    {
      field: 'role',
      headerName: 'Role',
      filter: 'agSetColumnFilter',
      cellEditor: 'agSelectCellEditor',
      cellEditorParams: {
        values: ['Admin', 'User', 'Manager']
      }
    },
    {
      field: 'status',
      headerName: 'Status',
      cellRenderer: (params: any) => {
        const color = params.value === 'Active' ? 'green' : 'red'
        return `<span style="color: ${color}; font-weight: bold">${params.value}</span>`
      }
    },
    {
      field: 'lastLogin',
      headerName: 'Last Login',
      filter: 'agDateColumnFilter'
    },
    {
      headerName: 'Actions',
      cellRenderer: () => {
        return `
          <button class="btn-edit">Edit</button>
          <button class="btn-delete">Delete</button>
        `
      }
    }
  ], [])

  const defaultColDef: ColDef = useMemo(() => ({
    sortable: true,
    filter: true,
    resizable: true,
    flex: 1
  }), [])

  const onExportCSV = () => {
    gridRef.current?.api.exportDataAsCsv()
  }

  const onFilterTextChange = (e: React.ChangeEvent<HTMLInputElement>) => {
    gridRef.current?.api.setQuickFilter(e.target.value)
  }

  return (
    <div className="bg-white rounded-lg shadow p-6">
      <div className="flex justify-between items-center mb-4">
        <h2 className="text-2xl font-bold">User Management</h2>
        <div className="flex gap-2">
          <input
            type="text"
            placeholder="Quick filter..."
            onChange={onFilterTextChange}
            className="px-4 py-2 border rounded"
          />
          <button onClick={onExportCSV} className="px-4 py-2 bg-blue-500 text-white rounded">
            Export CSV
          </button>
        </div>
      </div>
      <div className="ag-theme-alpine" style={{ height: 600, width: '100%' }}>
        <AgGridReact
          ref={gridRef}
          rowData={rowData}
          columnDefs={columnDefs}
          defaultColDef={defaultColDef}
          pagination={true}
          paginationPageSize={20}
          rowSelection="multiple"
          animateRows={true}
        />
      </div>
    </div>
  )
}
```

## Best Practices

### 1. Use useMemo for Column Definitions

```typescript
// ✅ Good: Memoized columns
const columnDefs = useMemo(() => [
  { field: 'name' },
  { field: 'age' }
], [])

// ❌ Bad: Recreates on every render
const columnDefs = [
  { field: 'name' },
  { field: 'age' }
]
```

### 2. Set Grid Height

```typescript
// ✅ Good: Explicit height
<div className="ag-theme-alpine" style={{ height: 500 }}>
  <AgGridReact />
</div>

// ❌ Bad: No height
<div className="ag-theme-alpine">
  <AgGridReact />
</div>
```

### 3. Use Default Column Definitions

```typescript
// ✅ Good: DRY with defaultColDef
const defaultColDef = {
  sortable: true,
  filter: true,
  resizable: true
}

// ❌ Bad: Repeat in every column
const columnDefs = [
  { field: 'name', sortable: true, filter: true, resizable: true },
  { field: 'age', sortable: true, filter: true, resizable: true }
]
```

## Common Pitfalls

### 1. Not Setting Grid Height

```typescript
// ❌ Bad: Grid won't display
<div className="ag-theme-alpine">
  <AgGridReact />
</div>

// ✅ Good: Set height
<div className="ag-theme-alpine" style={{ height: 500 }}>
  <AgGridReact />
</div>
```

### 2. Forgetting to Import Styles

```typescript
// ❌ Bad: No styles
import { AgGridReact } from 'ag-grid-react'

// ✅ Good: Import styles
import { AgGridReact } from 'ag-grid-react'
import 'ag-grid-community/styles/ag-grid.css'
import 'ag-grid-community/styles/ag-theme-alpine.css'
```

### 3. Not Using Grid API Correctly

```typescript
// ❌ Bad: Accessing API directly
gridRef.current.api.exportDataAsCsv()

// ✅ Good: Check if ref exists
gridRef.current?.api.exportDataAsCsv()
```

## Resources

### Official Documentation
- [AG Grid Docs](https://www.ag-grid.com/react-data-grid/)
- [AG Grid GitHub](https://github.com/ag-grid/ag-grid)
- [API Reference](https://www.ag-grid.com/react-data-grid/reference/)

### Examples
- [AG Grid Examples](https://www.ag-grid.com/react-data-grid/examples/)
- [AG Grid Showcase](https://www.ag-grid.com/example/)

### Community
- [AG Grid Forum](https://www.ag-grid.com/forum/)
- [Stack Overflow](https://stackoverflow.com/questions/tagged/ag-grid)

### Pricing
- [Community Edition](https://www.ag-grid.com/react-data-grid/licensing/) - Free
- [Enterprise Edition](https://www.ag-grid.com/react-data-grid/licensing/) - Paid

---

**Next Steps:**
- Explore [TanStack Table](./tanstack-table.md) for headless alternative
- Learn [TanStack Virtual](https://tanstack.com/virtual/) for virtualization
- Check out [AG Grid Enterprise](https://www.ag-grid.com/react-data-grid/licensing/) for advanced features
