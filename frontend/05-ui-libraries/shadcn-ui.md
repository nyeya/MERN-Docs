# shadcn/ui - Re-usable Components Built with Radix UI and Tailwind CSS

## Overview

shadcn/ui is not a traditional component library. Instead, it's a collection of re-usable components that you can copy and paste into your apps. Built on top of Radix UI for accessibility and Tailwind CSS for styling, it gives you full ownership and control over your components. You don't install it as a dependency; you own the code.

**Key Features:**
- Copy-paste components (not an npm package)
- Built with Radix UI primitives
- Styled with Tailwind CSS
- Fully accessible (ARIA compliant)
- Customizable and themeable
- TypeScript support
- Dark mode ready
- No vendor lock-in

## Why Use shadcn/ui?

### Problems It Solves

1. **Component Ownership**: You own the code, not locked to a library version
2. **Customization**: Fully customizable without fighting library styles
3. **Bundle Size**: Only includes components you use
4. **Accessibility**: Built-in ARIA compliance via Radix UI
5. **Styling Freedom**: Uses Tailwind CSS for easy customization
6. **No Breaking Changes**: Updates don't break your app

### When to Use shadcn/ui

- ✅ Building custom applications with Tailwind CSS
- ✅ When you want full control over components
- ✅ Projects requiring accessible components
- ✅ When you want to avoid npm dependency hell
- ✅ Teams comfortable with Tailwind CSS
- ✅ Modern React projects with TypeScript

### When NOT to Use shadcn/ui

- ❌ Not using Tailwind CSS
- ❌ Need pre-styled components out of the box
- ❌ Want automatic updates via npm
- ❌ Prefer CSS-in-JS solutions
- ❌ Very simple projects (might be overkill)

## Comparison with Alternatives

### shadcn/ui vs Material-UI (MUI)

| Feature | shadcn/ui | Material-UI |
|---------|-----------|-------------|
| Installation | Copy-paste | npm package |
| Styling | Tailwind CSS | Emotion/styled |
| Customization | Full control | Theme-based |
| Bundle Size | Smaller | Larger |
| Updates | Manual | Automatic |
| Design System | Your own | Material Design |
| Best For | Custom designs | Material Design apps |

**Choose shadcn/ui if:** You want full control and use Tailwind
**Choose MUI if:** You want Material Design and automatic updates

### shadcn/ui vs Chakra UI

| Feature | shadcn/ui | Chakra UI |
|---------|-----------|-----------|
| Installation | Copy-paste | npm package |
| Styling | Tailwind | Emotion |
| Props API | Standard | Chakra props |
| Customization | Full | Theme-based |
| Accessibility | Excellent | Excellent |
| Best For | Tailwind users | Chakra ecosystem |

**Choose shadcn/ui if:** You prefer Tailwind and component ownership
**Choose Chakra UI if:** You want a complete design system

### shadcn/ui vs Radix UI

| Feature | shadcn/ui | Radix UI |
|---------|-----------|----------|
| Styling | Included | Unstyled |
| Setup | CLI tool | Manual |
| Components | Pre-styled | Primitives only |
| Learning Curve | Easy | Moderate |
| Best For | Quick start | Full control |

**Choose shadcn/ui if:** You want styled components quickly
**Choose Radix UI if:** You want to build everything from scratch

## Installation & Setup

### Prerequisites

```bash
# Must have a React + Tailwind CSS project
npm create vite@latest my-app -- --template react-ts
cd my-app
npm install -D tailwindcss postcss autoprefixer
npx tailwindcss init -p
```

### Initialize shadcn/ui

```bash
npx shadcn-ui@latest init
```

This will:
1. Install dependencies
2. Configure `tailwind.config.js`
3. Create `components.json`
4. Set up CSS variables for theming

### Configuration

```json
// components.json
{
  "style": "default",
  "rsc": false,
  "tsx": true,
  "tailwind": {
    "config": "tailwind.config.js",
    "css": "src/index.css",
    "baseColor": "slate",
    "cssVariables": true
  },
  "aliases": {
    "components": "@/components",
    "utils": "@/lib/utils"
  }
}
```

### Install Components

```bash
# Install specific components
npx shadcn-ui@latest add button
npx shadcn-ui@latest add card
npx shadcn-ui@latest add dialog

# Install multiple at once
npx shadcn-ui@latest add button card dialog input
```

## Core Concepts

### 1. Component Ownership

Components are copied to your project:

```
src/
  components/
    ui/
      button.tsx
      card.tsx
      dialog.tsx
```

You can modify them as needed!

### 2. Theming with CSS Variables

```css
/* src/index.css */
@layer base {
  :root {
    --background: 0 0% 100%;
    --foreground: 222.2 84% 4.9%;
    --primary: 222.2 47.4% 11.2%;
    --primary-foreground: 210 40% 98%;
  }

  .dark {
    --background: 222.2 84% 4.9%;
    --foreground: 210 40% 98%;
    --primary: 210 40% 98%;
    --primary-foreground: 222.2 47.4% 11.2%;
  }
}
```

### 3. Utility Function (cn)

```typescript
// lib/utils.ts
import { clsx, type ClassValue } from "clsx"
import { twMerge } from "tailwind-merge"

export function cn(...inputs: ClassValue[]) {
  return twMerge(clsx(inputs))
}
```

This merges Tailwind classes properly.

## Basic Usage

### Button Component

```tsx
import { Button } from "@/components/ui/button"

export function ButtonDemo() {
  return (
    <div className="space-x-2">
      <Button>Default</Button>
      <Button variant="secondary">Secondary</Button>
      <Button variant="destructive">Destructive</Button>
      <Button variant="outline">Outline</Button>
      <Button variant="ghost">Ghost</Button>
      <Button variant="link">Link</Button>
    </div>
  )
}

// With sizes
<Button size="sm">Small</Button>
<Button size="default">Default</Button>
<Button size="lg">Large</Button>
<Button size="icon">🔥</Button>
```

### Card Component

```tsx
import { Card, CardHeader, CardTitle, CardDescription, CardContent, CardFooter } from "@/components/ui/card"
import { Button } from "@/components/ui/button"

export function CardDemo() {
  return (
    <Card className="w-[350px]">
      <CardHeader>
        <CardTitle>Create project</CardTitle>
        <CardDescription>Deploy your new project in one-click.</CardDescription>
      </CardHeader>
      <CardContent>
        <form>
          <div className="grid w-full items-center gap-4">
            <div className="flex flex-col space-y-1.5">
              <label htmlFor="name">Name</label>
              <input id="name" placeholder="Name of your project" />
            </div>
          </div>
        </form>
      </CardContent>
      <CardFooter className="flex justify-between">
        <Button variant="outline">Cancel</Button>
        <Button>Deploy</Button>
      </CardFooter>
    </Card>
  )
}
```

### Dialog (Modal) Component

```tsx
import { Button } from "@/components/ui/button"
import {
  Dialog,
  DialogContent,
  DialogDescription,
  DialogFooter,
  DialogHeader,
  DialogTitle,
  DialogTrigger,
} from "@/components/ui/dialog"
import { Input } from "@/components/ui/input"
import { Label } from "@/components/ui/label"

export function DialogDemo() {
  return (
    <Dialog>
      <DialogTrigger asChild>
        <Button variant="outline">Edit Profile</Button>
      </DialogTrigger>
      <DialogContent className="sm:max-w-[425px]">
        <DialogHeader>
          <DialogTitle>Edit profile</DialogTitle>
          <DialogDescription>
            Make changes to your profile here. Click save when you're done.
          </DialogDescription>
        </DialogHeader>
        <div className="grid gap-4 py-4">
          <div className="grid grid-cols-4 items-center gap-4">
            <Label htmlFor="name" className="text-right">
              Name
            </Label>
            <Input id="name" value="Pedro Duarte" className="col-span-3" />
          </div>
          <div className="grid grid-cols-4 items-center gap-4">
            <Label htmlFor="username" className="text-right">
              Username
            </Label>
            <Input id="username" value="@peduarte" className="col-span-3" />
          </div>
        </div>
        <DialogFooter>
          <Button type="submit">Save changes</Button>
        </DialogFooter>
      </DialogContent>
    </Dialog>
  )
}
```

### Form with Input

```tsx
import { Button } from "@/components/ui/button"
import { Input } from "@/components/ui/input"
import { Label } from "@/components/ui/label"

export function FormDemo() {
  return (
    <div className="grid w-full max-w-sm items-center gap-1.5">
      <Label htmlFor="email">Email</Label>
      <Input type="email" id="email" placeholder="Email" />
      <Button className="mt-2">Submit</Button>
    </div>
  )
}
```

## Advanced Usage

### 1. Form with React Hook Form

```bash
npx shadcn-ui@latest add form
```

```tsx
import { zodResolver } from "@hookform/resolvers/zod"
import { useForm } from "react-hook-form"
import * as z from "zod"
import { Button } from "@/components/ui/button"
import {
  Form,
  FormControl,
  FormDescription,
  FormField,
  FormItem,
  FormLabel,
  FormMessage,
} from "@/components/ui/form"
import { Input } from "@/components/ui/input"

const formSchema = z.object({
  username: z.string().min(2, {
    message: "Username must be at least 2 characters.",
  }),
  email: z.string().email({
    message: "Please enter a valid email.",
  }),
})

export function ProfileForm() {
  const form = useForm<z.infer<typeof formSchema>>({
    resolver: zodResolver(formSchema),
    defaultValues: {
      username: "",
      email: "",
    },
  })

  function onSubmit(values: z.infer<typeof formSchema>) {
    console.log(values)
  }

  return (
    <Form {...form}>
      <form onSubmit={form.handleSubmit(onSubmit)} className="space-y-8">
        <FormField
          control={form.control}
          name="username"
          render={({ field }) => (
            <FormItem>
              <FormLabel>Username</FormLabel>
              <FormControl>
                <Input placeholder="shadcn" {...field} />
              </FormControl>
              <FormDescription>
                This is your public display name.
              </FormDescription>
              <FormMessage />
            </FormItem>
          )}
        />
        <FormField
          control={form.control}
          name="email"
          render={({ field }) => (
            <FormItem>
              <FormLabel>Email</FormLabel>
              <FormControl>
                <Input placeholder="email@example.com" {...field} />
              </FormControl>
              <FormMessage />
            </FormItem>
          )}
        />
        <Button type="submit">Submit</Button>
      </form>
    </Form>
  )
}
```

### 2. Data Table

```bash
npx shadcn-ui@latest add table
```

```tsx
import {
  Table,
  TableBody,
  TableCaption,
  TableCell,
  TableHead,
  TableHeader,
  TableRow,
} from "@/components/ui/table"

const invoices = [
  { invoice: "INV001", status: "Paid", method: "Credit Card", amount: "$250.00" },
  { invoice: "INV002", status: "Pending", method: "PayPal", amount: "$150.00" },
  { invoice: "INV003", status: "Unpaid", method: "Bank Transfer", amount: "$350.00" },
]

export function TableDemo() {
  return (
    <Table>
      <TableCaption>A list of your recent invoices.</TableCaption>
      <TableHeader>
        <TableRow>
          <TableHead>Invoice</TableHead>
          <TableHead>Status</TableHead>
          <TableHead>Method</TableHead>
          <TableHead className="text-right">Amount</TableHead>
        </TableRow>
      </TableHeader>
      <TableBody>
        {invoices.map((invoice) => (
          <TableRow key={invoice.invoice}>
            <TableCell className="font-medium">{invoice.invoice}</TableCell>
            <TableCell>{invoice.status}</TableCell>
            <TableCell>{invoice.method}</TableCell>
            <TableCell className="text-right">{invoice.amount}</TableCell>
          </TableRow>
        ))}
      </TableBody>
    </Table>
  )
}
```

### 3. Dropdown Menu

```bash
npx shadcn-ui@latest add dropdown-menu
```

```tsx
import { Button } from "@/components/ui/button"
import {
  DropdownMenu,
  DropdownMenuContent,
  DropdownMenuItem,
  DropdownMenuLabel,
  DropdownMenuSeparator,
  DropdownMenuTrigger,
} from "@/components/ui/dropdown-menu"

export function DropdownMenuDemo() {
  return (
    <DropdownMenu>
      <DropdownMenuTrigger asChild>
        <Button variant="outline">Open Menu</Button>
      </DropdownMenuTrigger>
      <DropdownMenuContent className="w-56">
        <DropdownMenuLabel>My Account</DropdownMenuLabel>
        <DropdownMenuSeparator />
        <DropdownMenuItem>Profile</DropdownMenuItem>
        <DropdownMenuItem>Billing</DropdownMenuItem>
        <DropdownMenuItem>Team</DropdownMenuItem>
        <DropdownMenuItem>Subscription</DropdownMenuItem>
      </DropdownMenuContent>
    </DropdownMenu>
  )
}
```

### 4. Toast Notifications

```bash
npx shadcn-ui@latest add toast
```

```tsx
import { Button } from "@/components/ui/button"
import { useToast } from "@/components/ui/use-toast"

export function ToastDemo() {
  const { toast } = useToast()

  return (
    <Button
      onClick={() => {
        toast({
          title: "Scheduled: Catch up",
          description: "Friday, February 10, 2023 at 5:57 PM",
        })
      }}
    >
      Show Toast
    </Button>
  )
}

// Add Toaster to your app
import { Toaster } from "@/components/ui/toaster"

export default function App() {
  return (
    <>
      {/* Your app */}
      <Toaster />
    </>
  )
}
```

### 5. Select Component

```bash
npx shadcn-ui@latest add select
```

```tsx
import {
  Select,
  SelectContent,
  SelectItem,
  SelectTrigger,
  SelectValue,
} from "@/components/ui/select"

export function SelectDemo() {
  return (
    <Select>
      <SelectTrigger className="w-[180px]">
        <SelectValue placeholder="Select a fruit" />
      </SelectTrigger>
      <SelectContent>
        <SelectItem value="apple">Apple</SelectItem>
        <SelectItem value="banana">Banana</SelectItem>
        <SelectItem value="orange">Orange</SelectItem>
      </SelectContent>
    </Select>
  )
}
```

## Real-World Examples

### Complete Login Form

```tsx
import { zodResolver } from "@hookform/resolvers/zod"
import { useForm } from "react-hook-form"
import * as z from "zod"
import { Button } from "@/components/ui/button"
import { Card, CardContent, CardDescription, CardFooter, CardHeader, CardTitle } from "@/components/ui/card"
import { Form, FormControl, FormField, FormItem, FormLabel, FormMessage } from "@/components/ui/form"
import { Input } from "@/components/ui/input"
import { useToast } from "@/components/ui/use-toast"

const loginSchema = z.object({
  email: z.string().email("Invalid email address"),
  password: z.string().min(8, "Password must be at least 8 characters"),
})

export function LoginForm() {
  const { toast } = useToast()
  const form = useForm<z.infer<typeof loginSchema>>({
    resolver: zodResolver(loginSchema),
    defaultValues: {
      email: "",
      password: "",
    },
  })

  async function onSubmit(values: z.infer<typeof loginSchema>) {
    try {
      // API call here
      toast({
        title: "Success",
        description: "You have been logged in.",
      })
    } catch (error) {
      toast({
        variant: "destructive",
        title: "Error",
        description: "Invalid credentials.",
      })
    }
  }

  return (
    <Card className="w-[400px]">
      <CardHeader>
        <CardTitle>Login</CardTitle>
        <CardDescription>Enter your credentials to access your account</CardDescription>
      </CardHeader>
      <CardContent>
        <Form {...form}>
          <form onSubmit={form.handleSubmit(onSubmit)} className="space-y-4">
            <FormField
              control={form.control}
              name="email"
              render={({ field }) => (
                <FormItem>
                  <FormLabel>Email</FormLabel>
                  <FormControl>
                    <Input placeholder="email@example.com" {...field} />
                  </FormControl>
                  <FormMessage />
                </FormItem>
              )}
            />
            <FormField
              control={form.control}
              name="password"
              render={({ field }) => (
                <FormItem>
                  <FormLabel>Password</FormLabel>
                  <FormControl>
                    <Input type="password" {...field} />
                  </FormControl>
                  <FormMessage />
                </FormItem>
              )}
            />
            <Button type="submit" className="w-full">
              Login
            </Button>
          </form>
        </Form>
      </CardContent>
      <CardFooter className="flex justify-center">
        <Button variant="link">Forgot password?</Button>
      </CardFooter>
    </Card>
  )
}
```

## Best Practices

### 1. Customize Components

```tsx
// ✅ Good: Modify components to fit your needs
// components/ui/button.tsx
const buttonVariants = cva(
  "...",
  {
    variants: {
      variant: {
        default: "...",
        // Add your custom variant
        brand: "bg-brand-500 text-white hover:bg-brand-600",
      },
    },
  }
)
```

### 2. Use Composition

```tsx
// ✅ Good: Compose components
<Card>
  <CardHeader>
    <CardTitle>Title</CardTitle>
  </CardHeader>
  <CardContent>Content</CardContent>
</Card>
```

### 3. Leverage CSS Variables

```css
/* ✅ Good: Use CSS variables for theming */
:root {
  --primary: 222.2 47.4% 11.2%;
  --brand: 280 100% 50%;
}
```

## Common Pitfalls

### 1. Not Configuring Path Aliases

```json
// tsconfig.json
{
  "compilerOptions": {
    "paths": {
      "@/*": ["./src/*"]
    }
  }
}
```

### 2. Forgetting Dark Mode Setup

```javascript
// tailwind.config.js
export default {
  darkMode: ["class"],
  // ...
}
```

### 3. Not Installing Dependencies

```bash
# Some components need additional packages
npm install @radix-ui/react-dialog
npm install @radix-ui/react-dropdown-menu
```

## Resources

### Official Documentation
- [shadcn/ui Docs](https://ui.shadcn.com/)
- [GitHub Repository](https://github.com/shadcn-ui/ui)
- [Component Examples](https://ui.shadcn.com/examples)

### Related Libraries
- [Radix UI](https://www.radix-ui.com/)
- [Tailwind CSS](https://tailwindcss.com/)
- [class-variance-authority](https://cva.style/docs)

### Community
- [GitHub Discussions](https://github.com/shadcn-ui/ui/discussions)
- [Twitter](https://twitter.com/shadcn)

---

**Next Steps:**
- Explore [Radix UI](./radix-ui.md) for unstyled primitives
- Learn [Tailwind CSS](../04-styling/tailwind-css.md) for styling
- Check out [React Hook Form](../06-forms/react-hook-form.md) for forms
