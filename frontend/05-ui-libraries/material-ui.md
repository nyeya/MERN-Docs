# Material-UI (MUI) - Google's Material Design for React

## Overview

Material-UI (MUI) is a comprehensive React component library that implements Google's Material Design. It provides a robust set of customizable and accessible components that work out of the box, along with powerful theming capabilities. MUI is one of the most popular UI libraries in the React ecosystem, trusted by companies worldwide for building production-ready applications.

**Key Features:**
- 50+ production-ready components
- Material Design implementation
- Comprehensive theming system
- Responsive design utilities
- Accessibility built-in
- TypeScript support
- Customizable styling (CSS-in-JS)
- Icon library included
- Dark mode support
- Extensive documentation

## Why Use Material-UI?

### Problems It Solves

1. **Design Consistency**: Implements Material Design system
2. **Development Speed**: Pre-built, tested components
3. **Accessibility**: WCAG compliant components
4. **Responsive Design**: Mobile-first components
5. **Theming**: Consistent styling across app
6. **Customization**: Highly customizable components

### When to Use Material-UI

- ✅ Enterprise applications
- ✅ Admin dashboards
- ✅ Data-heavy applications
- ✅ When you want Material Design
- ✅ Need comprehensive component library
- ✅ Rapid prototyping
- ✅ Accessibility is important

### When NOT to Use Material-UI

- ❌ Custom design system (not Material Design)
- ❌ Bundle size is critical (<300KB)
- ❌ Simple landing pages
- ❌ When you need minimal styling

## Comparison with Alternatives

### MUI vs Chakra UI

| Feature | MUI | Chakra UI |
|---------|-----|-----------|
| Design System | Material Design | Custom |
| Bundle Size | ~300KB | ~150KB |
| Components | 50+ | 50+ |
| Customization | Moderate | Easy |
| Learning Curve | Moderate | Easy |
| TypeScript | Excellent | Excellent |
| Best For | Material Design | Custom designs |

**Choose MUI if:** You want Material Design
**Choose Chakra if:** You want easier customization

### MUI vs Ant Design

| Feature | MUI | Ant Design |
|---------|-----|------------|
| Design System | Material | Ant Design |
| Components | 50+ | 60+ |
| Enterprise Focus | Good | Excellent |
| Customization | Good | Moderate |
| Best For | Material Design | Enterprise apps |

## Installation & Setup

### Installation

```bash
# npm
npm install @mui/material @emotion/react @emotion/styled

# yarn
yarn add @mui/material @emotion/react @emotion/styled

# pnpm
pnpm add @mui/material @emotion/react @emotion/styled
```

### Install Icons (Optional)

```bash
npm install @mui/icons-material
```

### Roboto Font

```html
<!-- In your index.html -->
<link
  rel="stylesheet"
  href="https://fonts.googleapis.com/css?family=Roboto:300,400,500,700&display=swap"
/>
```

### Basic Setup

```typescript
// App.tsx
import { ThemeProvider, createTheme } from '@mui/material/styles'
import CssBaseline from '@mui/material/CssBaseline'

const theme = createTheme()

function App() {
  return (
    <ThemeProvider theme={theme}>
      <CssBaseline />
      {/* Your app */}
    </ThemeProvider>
  )
}
```

## Core Concepts

### 1. Theme

```typescript
import { createTheme, ThemeProvider } from '@mui/material/styles'

const theme = createTheme({
  palette: {
    primary: {
      main: '#1976d2',
    },
    secondary: {
      main: '#dc004e',
    },
  },
  typography: {
    fontFamily: 'Roboto, Arial, sans-serif',
  },
})

function App() {
  return (
    <ThemeProvider theme={theme}>
      {/* Your app */}
    </ThemeProvider>
  )
}
```

### 2. sx Prop

```typescript
import { Box } from '@mui/material'

function Component() {
  return (
    <Box
      sx={{
        width: 300,
        height: 300,
        backgroundColor: 'primary.main',
        '&:hover': {
          backgroundColor: 'primary.dark',
        },
      }}
    >
      Content
    </Box>
  )
}
```

### 3. Responsive Design

```typescript
<Box
  sx={{
    width: {
      xs: '100%',  // 0-600px
      sm: '50%',   // 600-900px
      md: '33%',   // 900-1200px
      lg: '25%',   // 1200-1536px
      xl: '20%',   // 1536px+
    },
  }}
/>
```

## Basic Usage

### Buttons

```typescript
import { Button, Stack } from '@mui/material'
import DeleteIcon from '@mui/icons-material/Delete'
import SendIcon from '@mui/icons-material/Send'

function Buttons() {
  return (
    <Stack spacing={2} direction="row">
      <Button variant="text">Text</Button>
      <Button variant="contained">Contained</Button>
      <Button variant="outlined">Outlined</Button>
      <Button variant="contained" color="error">
        Error
      </Button>
      <Button variant="contained" startIcon={<SendIcon />}>
        Send
      </Button>
      <Button variant="outlined" endIcon={<DeleteIcon />}>
        Delete
      </Button>
    </Stack>
  )
}
```

### Forms

```typescript
import { TextField, Button, Stack } from '@mui/material'
import { useState } from 'react'

function LoginForm() {
  const [email, setEmail] = useState('')
  const [password, setPassword] = useState('')

  const handleSubmit = (e: React.FormEvent) => {
    e.preventDefault()
    console.log({ email, password })
  }

  return (
    <form onSubmit={handleSubmit}>
      <Stack spacing={2} sx={{ width: 300 }}>
        <TextField
          label="Email"
          type="email"
          value={email}
          onChange={(e) => setEmail(e.target.value)}
          required
          fullWidth
        />
        <TextField
          label="Password"
          type="password"
          value={password}
          onChange={(e) => setPassword(e.target.value)}
          required
          fullWidth
        />
        <Button type="submit" variant="contained" fullWidth>
          Login
        </Button>
      </Stack>
    </form>
  )
}
```

### Layout

```typescript
import { Container, Grid, Paper, Box } from '@mui/material'

function Layout() {
  return (
    <Container maxWidth="lg">
      <Grid container spacing={3}>
        <Grid item xs={12} md={8}>
          <Paper sx={{ p: 2 }}>
            <h2>Main Content</h2>
          </Paper>
        </Grid>
        <Grid item xs={12} md={4}>
          <Paper sx={{ p: 2 }}>
            <h2>Sidebar</h2>
          </Paper>
        </Grid>
      </Grid>
    </Container>
  )
}
```

## Advanced Usage

### 1. Custom Theme

```typescript
import { createTheme, ThemeProvider } from '@mui/material/styles'

const theme = createTheme({
  palette: {
    mode: 'dark',
    primary: {
      main: '#90caf9',
    },
    secondary: {
      main: '#f48fb1',
    },
    background: {
      default: '#121212',
      paper: '#1e1e1e',
    },
  },
  typography: {
    h1: {
      fontSize: '2.5rem',
      fontWeight: 600,
    },
    button: {
      textTransform: 'none',
    },
  },
  components: {
    MuiButton: {
      styleOverrides: {
        root: {
          borderRadius: 8,
        },
      },
      defaultProps: {
        disableElevation: true,
      },
    },
  },
})
```

### 2. Data Table

```typescript
import {
  Table,
  TableBody,
  TableCell,
  TableContainer,
  TableHead,
  TableRow,
  Paper,
} from '@mui/material'

interface User {
  id: number
  name: string
  email: string
  role: string
}

function UserTable({ users }: { users: User[] }) {
  return (
    <TableContainer component={Paper}>
      <Table>
        <TableHead>
          <TableRow>
            <TableCell>ID</TableCell>
            <TableCell>Name</TableCell>
            <TableCell>Email</TableCell>
            <TableCell>Role</TableCell>
          </TableRow>
        </TableHead>
        <TableBody>
          {users.map((user) => (
            <TableRow key={user.id}>
              <TableCell>{user.id}</TableCell>
              <TableCell>{user.name}</TableCell>
              <TableCell>{user.email}</TableCell>
              <TableCell>{user.role}</TableCell>
            </TableRow>
          ))}
        </TableBody>
      </Table>
    </TableContainer>
  )
}
```

### 3. Dialog/Modal

```typescript
import {
  Dialog,
  DialogTitle,
  DialogContent,
  DialogActions,
  Button,
  TextField,
} from '@mui/material'
import { useState } from 'react'

function UserDialog() {
  const [open, setOpen] = useState(false)
  const [name, setName] = useState('')

  const handleClose = () => {
    setOpen(false)
    setName('')
  }

  const handleSave = () => {
    console.log('Saving:', name)
    handleClose()
  }

  return (
    <>
      <Button variant="contained" onClick={() => setOpen(true)}>
        Open Dialog
      </Button>

      <Dialog open={open} onClose={handleClose} maxWidth="sm" fullWidth>
        <DialogTitle>Add User</DialogTitle>
        <DialogContent>
          <TextField
            autoFocus
            margin="dense"
            label="Name"
            fullWidth
            value={name}
            onChange={(e) => setName(e.target.value)}
          />
        </DialogContent>
        <DialogActions>
          <Button onClick={handleClose}>Cancel</Button>
          <Button onClick={handleSave} variant="contained">
            Save
          </Button>
        </DialogActions>
      </Dialog>
    </>
  )
}
```

### 4. Snackbar/Notifications

```typescript
import { Snackbar, Alert } from '@mui/material'
import { useState } from 'react'

function Notifications() {
  const [open, setOpen] = useState(false)

  return (
    <>
      <Button onClick={() => setOpen(true)}>Show Notification</Button>

      <Snackbar
        open={open}
        autoHideDuration={6000}
        onClose={() => setOpen(false)}
        anchorOrigin={{ vertical: 'bottom', horizontal: 'right' }}
      >
        <Alert onClose={() => setOpen(false)} severity="success">
          Operation successful!
        </Alert>
      </Snackbar>
    </>
  )
}
```

### 5. Dark Mode Toggle

```typescript
import { createTheme, ThemeProvider } from '@mui/material/styles'
import { useState, useMemo } from 'react'
import { IconButton } from '@mui/material'
import Brightness4Icon from '@mui/icons-material/Brightness4'
import Brightness7Icon from '@mui/icons-material/Brightness7'

function App() {
  const [mode, setMode] = useState<'light' | 'dark'>('light')

  const theme = useMemo(
    () =>
      createTheme({
        palette: {
          mode,
        },
      }),
    [mode]
  )

  return (
    <ThemeProvider theme={theme}>
      <IconButton onClick={() => setMode(mode === 'light' ? 'dark' : 'light')}>
        {mode === 'dark' ? <Brightness7Icon /> : <Brightness4Icon />}
      </IconButton>
      {/* Your app */}
    </ThemeProvider>
  )
}
```

## Real-World Examples

### Dashboard Layout

```typescript
import {
  AppBar,
  Box,
  Drawer,
  List,
  ListItem,
  ListItemButton,
  ListItemIcon,
  ListItemText,
  Toolbar,
  Typography,
} from '@mui/material'
import DashboardIcon from '@mui/icons-material/Dashboard'
import PeopleIcon from '@mui/icons-material/People'
import SettingsIcon from '@mui/icons-material/Settings'

const drawerWidth = 240

function DashboardLayout({ children }: { children: React.ReactNode }) {
  return (
    <Box sx={{ display: 'flex' }}>
      <AppBar position="fixed" sx={{ zIndex: (theme) => theme.zIndex.drawer + 1 }}>
        <Toolbar>
          <Typography variant="h6" noWrap component="div">
            Dashboard
          </Typography>
        </Toolbar>
      </AppBar>

      <Drawer
        variant="permanent"
        sx={{
          width: drawerWidth,
          flexShrink: 0,
          '& .MuiDrawer-paper': {
            width: drawerWidth,
            boxSizing: 'border-box',
          },
        }}
      >
        <Toolbar />
        <Box sx={{ overflow: 'auto' }}>
          <List>
            <ListItem disablePadding>
              <ListItemButton>
                <ListItemIcon>
                  <DashboardIcon />
                </ListItemIcon>
                <ListItemText primary="Dashboard" />
              </ListItemButton>
            </ListItem>
            <ListItem disablePadding>
              <ListItemButton>
                <ListItemIcon>
                  <PeopleIcon />
                </ListItemIcon>
                <ListItemText primary="Users" />
              </ListItemButton>
            </ListItem>
            <ListItem disablePadding>
              <ListItemButton>
                <ListItemIcon>
                  <SettingsIcon />
                </ListItemIcon>
                <ListItemText primary="Settings" />
              </ListItemButton>
            </ListItem>
          </List>
        </Box>
      </Drawer>

      <Box component="main" sx={{ flexGrow: 1, p: 3 }}>
        <Toolbar />
        {children}
      </Box>
    </Box>
  )
}
```

### Form with Validation

```typescript
import { TextField, Button, Stack, Alert } from '@mui/material'
import { useState } from 'react'

interface FormData {
  name: string
  email: string
  password: string
}

interface FormErrors {
  name?: string
  email?: string
  password?: string
}

function RegistrationForm() {
  const [formData, setFormData] = useState<FormData>({
    name: '',
    email: '',
    password: '',
  })
  const [errors, setErrors] = useState<FormErrors>({})
  const [success, setSuccess] = useState(false)

  const validate = (): boolean => {
    const newErrors: FormErrors = {}

    if (!formData.name) newErrors.name = 'Name is required'
    if (!formData.email) newErrors.email = 'Email is required'
    else if (!/\S+@\S+\.\S+/.test(formData.email)) newErrors.email = 'Email is invalid'
    if (!formData.password) newErrors.password = 'Password is required'
    else if (formData.password.length < 8) newErrors.password = 'Password must be at least 8 characters'

    setErrors(newErrors)
    return Object.keys(newErrors).length === 0
  }

  const handleSubmit = (e: React.FormEvent) => {
    e.preventDefault()
    if (validate()) {
      console.log('Form submitted:', formData)
      setSuccess(true)
    }
  }

  const handleChange = (field: keyof FormData) => (e: React.ChangeEvent<HTMLInputElement>) => {
    setFormData({ ...formData, [field]: e.target.value })
    if (errors[field]) {
      setErrors({ ...errors, [field]: undefined })
    }
  }

  return (
    <form onSubmit={handleSubmit}>
      <Stack spacing={3} sx={{ maxWidth: 400 }}>
        {success && <Alert severity="success">Registration successful!</Alert>}

        <TextField
          label="Name"
          value={formData.name}
          onChange={handleChange('name')}
          error={!!errors.name}
          helperText={errors.name}
          fullWidth
        />

        <TextField
          label="Email"
          type="email"
          value={formData.email}
          onChange={handleChange('email')}
          error={!!errors.email}
          helperText={errors.email}
          fullWidth
        />

        <TextField
          label="Password"
          type="password"
          value={formData.password}
          onChange={handleChange('password')}
          error={!!errors.password}
          helperText={errors.password}
          fullWidth
        />

        <Button type="submit" variant="contained" size="large" fullWidth>
          Register
        </Button>
      </Stack>
    </form>
  )
}
```

## Best Practices

### 1. Use sx Prop for Styling

```typescript
// ✅ Good: sx prop
<Box sx={{ mt: 2, p: 3, bgcolor: 'primary.main' }} />

// ❌ Bad: Inline styles
<Box style={{ marginTop: 16, padding: 24 }} />
```

### 2. Leverage Theme

```typescript
// ✅ Good: Use theme values
<Box sx={{ color: 'primary.main', spacing: 2 }} />

// ❌ Bad: Hardcoded values
<Box sx={{ color: '#1976d2', margin: '16px' }} />
```

### 3. Use Proper Component Variants

```typescript
// ✅ Good: Semantic variants
<Button variant="contained" color="primary">Primary Action</Button>
<Button variant="outlined" color="secondary">Secondary Action</Button>

// ❌ Bad: Custom styling everything
<Button style={{ background: 'blue' }}>Button</Button>
```

### 4. Responsive Design

```typescript
// ✅ Good: Responsive values
<Grid container spacing={{ xs: 2, md: 3 }}>
  <Grid item xs={12} md={6}>Content</Grid>
</Grid>

// ❌ Bad: Fixed values
<Grid container spacing={3}>
  <Grid item md={6}>Content</Grid>
</Grid>
```

### 5. Accessibility

```typescript
// ✅ Good: Proper labels and ARIA
<TextField label="Email" aria-label="Email address" />
<IconButton aria-label="delete">
  <DeleteIcon />
</IconButton>

// ❌ Bad: No labels
<TextField />
<IconButton><DeleteIcon /></IconButton>
```

## Common Pitfalls

### 1. Not Using CssBaseline

```typescript
// ❌ Bad: No CssBaseline
<ThemeProvider theme={theme}>
  <App />
</ThemeProvider>

// ✅ Good: Include CssBaseline
<ThemeProvider theme={theme}>
  <CssBaseline />
  <App />
</ThemeProvider>
```

### 2. Overriding Too Much

```typescript
// ❌ Bad: Fighting the design system
<Button sx={{ borderRadius: 0, textTransform: 'uppercase' }}>

// ✅ Good: Configure theme instead
const theme = createTheme({
  components: {
    MuiButton: {
      styleOverrides: {
        root: { borderRadius: 0 }
      }
    }
  }
})
```

### 3. Not Using Grid Properly

```typescript
// ❌ Bad: Nested containers
<Grid container>
  <Grid container>
    <Grid item>Content</Grid>
  </Grid>
</Grid>

// ✅ Good: Proper structure
<Grid container spacing={2}>
  <Grid item xs={12} md={6}>Content</Grid>
</Grid>
```

## Resources

### Official Documentation
- [MUI Docs](https://mui.com/)
- [Component API](https://mui.com/material-ui/api/button/)
- [GitHub](https://github.com/mui/material-ui)

### Tutorials
- [MUI Getting Started](https://mui.com/material-ui/getting-started/)
- [Templates](https://mui.com/material-ui/getting-started/templates/)

### Tools
- [MUI Theme Creator](https://bareynol.github.io/mui-theme-creator/)
- [MUI Treasury](https://mui-treasury.com/)

### Community
- [Discord](https://discord.gg/material-ui)
- [Stack Overflow](https://stackoverflow.com/questions/tagged/material-ui)

---

**Next Steps:**
- Explore [Chakra UI](./chakra-ui.md) for easier customization
- Learn [shadcn/ui](./shadcn-ui.md) for headless components
- Check out [Tailwind CSS](../04-styling/tailwind-css.md) for utility-first styling
