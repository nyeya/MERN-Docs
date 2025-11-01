# Zod - TypeScript-First Schema Validation

## Overview

Zod is a TypeScript-first schema declaration and validation library. It allows you to build schemas that validate data at runtime while providing static TypeScript types. Zod is designed to be developer-friendly with zero dependencies, a small bundle size, and excellent TypeScript integration. It's perfect for validating form data, API responses, environment variables, and more.

**Key Features:**
- TypeScript-first design
- Zero dependencies
- Small bundle size (~8KB)
- Composable schemas
- Type inference
- Custom error messages
- Async validation
- Transform and refine data
- Works with React Hook Form
- Excellent DX

## Why Use Zod?

### Problems It Solves

1. **Runtime Validation**: Validate data at runtime with TypeScript types
2. **Type Safety**: Automatic TypeScript type inference
3. **Form Validation**: Perfect for form validation
4. **API Validation**: Validate API responses and requests
5. **Environment Variables**: Type-safe env vars
6. **Data Transformation**: Parse and transform data

### When to Use Zod

- ✅ Form validation
- ✅ API request/response validation
- ✅ Environment variable validation
- ✅ Configuration validation
- ✅ TypeScript projects
- ✅ When you need runtime type checking
- ✅ Data parsing and transformation

### When NOT to Use Zod

- ❌ Non-TypeScript projects (use Yup)
- ❌ When bundle size is critical
- ❌ Simple validation (use native HTML5)
- ❌ When you don't need type inference

## Comparison with Alternatives

### Zod vs Yup

| Feature | Zod | Yup |
|---------|-----|-----|
| TypeScript | First-class | Good |
| Type Inference | Excellent | Manual |
| Bundle Size | ~8KB | ~15KB |
| Dependencies | 0 | Several |
| API | Modern | Traditional |
| Best For | TypeScript | JavaScript |

**Choose Zod if:** You use TypeScript and want type inference
**Choose Yup if:** You use JavaScript or need wider ecosystem

### Zod vs Joi

| Feature | Zod | Joi |
|---------|-----|-----|
| Environment | Browser + Node | Node-focused |
| TypeScript | Native | Plugin needed |
| Bundle Size | Small | Large |
| Best For | Frontend | Backend |

## Installation & Setup

### Installation

```bash
# npm
npm install zod

# yarn
yarn add zod

# pnpm
pnpm add zod
```

### Basic Setup

```typescript
import { z } from 'zod'

// Define schema
const userSchema = z.object({
  name: z.string(),
  email: z.string().email(),
  age: z.number().min(18)
})

// Infer TypeScript type
type User = z.infer<typeof userSchema>

// Validate data
const result = userSchema.safeParse({
  name: 'John',
  email: 'john@example.com',
  age: 25
})

if (result.success) {
  console.log(result.data)
} else {
  console.log(result.error)
}
```

## Core Concepts

### 1. Primitive Types

```typescript
import { z } from 'zod'

// String
const nameSchema = z.string()
const emailSchema = z.string().email()
const urlSchema = z.string().url()
const uuidSchema = z.string().uuid()

// Number
const ageSchema = z.number()
const positiveSchema = z.number().positive()
const intSchema = z.number().int()
const rangeSchema = z.number().min(0).max(100)

// Boolean
const isActiveSchema = z.boolean()

// Date
const dateSchema = z.date()
const futureDate = z.date().min(new Date())

// Literal
const roleSchema = z.literal('admin')
const statusSchema = z.enum(['pending', 'approved', 'rejected'])
```

### 2. Objects

```typescript
const userSchema = z.object({
  id: z.string().uuid(),
  name: z.string().min(2).max(50),
  email: z.string().email(),
  age: z.number().int().positive().optional(),
  role: z.enum(['user', 'admin']),
  createdAt: z.date()
})

type User = z.infer<typeof userSchema>
```

### 3. Arrays

```typescript
// Array of strings
const tagsSchema = z.array(z.string())

// Array with constraints
const numbersSchema = z.array(z.number()).min(1).max(10)

// Non-empty array
const itemsSchema = z.array(z.string()).nonempty()

// Array of objects
const usersSchema = z.array(
  z.object({
    id: z.string(),
    name: z.string()
  })
)
```

### 4. Optional and Nullable

```typescript
// Optional (can be undefined)
const schema1 = z.object({
  name: z.string().optional()
})

// Nullable (can be null)
const schema2 = z.object({
  name: z.string().nullable()
})

// Both
const schema3 = z.object({
  name: z.string().nullish() // null | undefined
})

// With default
const schema4 = z.object({
  name: z.string().default('Anonymous')
})
```

## Basic Usage

### Form Validation

```typescript
import { z } from 'zod'

const loginSchema = z.object({
  email: z.string().email('Invalid email address'),
  password: z.string().min(8, 'Password must be at least 8 characters')
})

type LoginForm = z.infer<typeof loginSchema>

function validateLogin(data: unknown) {
  const result = loginSchema.safeParse(data)
  
  if (!result.success) {
    // Get formatted errors
    const errors = result.error.format()
    console.log(errors.email?._errors) // ['Invalid email address']
    return { success: false, errors: result.error.flatten() }
  }
  
  return { success: true, data: result.data }
}
```

### With React Hook Form

```typescript
import { useForm } from 'react-hook-form'
import { zodResolver } from '@hookform/resolvers/zod'
import { z } from 'zod'

const schema = z.object({
  name: z.string().min(2, 'Name must be at least 2 characters'),
  email: z.string().email('Invalid email'),
  age: z.number().min(18, 'Must be 18 or older')
})

type FormData = z.infer<typeof schema>

function MyForm() {
  const {
    register,
    handleSubmit,
    formState: { errors }
  } = useForm<FormData>({
    resolver: zodResolver(schema)
  })

  const onSubmit = (data: FormData) => {
    console.log(data)
  }

  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <input {...register('name')} />
      {errors.name && <span>{errors.name.message}</span>}

      <input {...register('email')} />
      {errors.email && <span>{errors.email.message}</span>}

      <input {...register('age', { valueAsNumber: true })} type="number" />
      {errors.age && <span>{errors.age.message}</span>}

      <button type="submit">Submit</button>
    </form>
  )
}
```

### API Response Validation

```typescript
const userResponseSchema = z.object({
  data: z.object({
    id: z.string(),
    name: z.string(),
    email: z.string().email()
  }),
  status: z.number(),
  message: z.string()
})

async function fetchUser(id: string) {
  const response = await fetch(`/api/users/${id}`)
  const json = await response.json()
  
  // Validate response
  const result = userResponseSchema.safeParse(json)
  
  if (!result.success) {
    throw new Error('Invalid API response')
  }
  
  return result.data
}
```

## Advanced Usage

### 1. Refinements (Custom Validation)

```typescript
const passwordSchema = z
  .string()
  .min(8)
  .refine((val) => /[A-Z]/.test(val), {
    message: 'Password must contain at least one uppercase letter'
  })
  .refine((val) => /[0-9]/.test(val), {
    message: 'Password must contain at least one number'
  })

// Object refinement
const signupSchema = z
  .object({
    password: z.string().min(8),
    confirmPassword: z.string()
  })
  .refine((data) => data.password === data.confirmPassword, {
    message: 'Passwords do not match',
    path: ['confirmPassword']
  })
```

### 2. Transformations

```typescript
// Transform string to number
const numberSchema = z.string().transform((val) => parseInt(val, 10))

// Transform and validate
const emailSchema = z
  .string()
  .transform((val) => val.toLowerCase())
  .pipe(z.string().email())

// Complex transformation
const userSchema = z
  .object({
    name: z.string(),
    email: z.string().email()
  })
  .transform((data) => ({
    ...data,
    emailDomain: data.email.split('@')[1]
  }))
```

### 3. Union Types

```typescript
// Simple union
const idSchema = z.union([z.string(), z.number()])

// Discriminated union
const responseSchema = z.discriminatedUnion('status', [
  z.object({
    status: z.literal('success'),
    data: z.object({ id: z.string(), name: z.string() })
  }),
  z.object({
    status: z.literal('error'),
    error: z.string()
  })
])

type Response = z.infer<typeof responseSchema>
// { status: 'success', data: { id: string, name: string } } |
// { status: 'error', error: string }
```

### 4. Recursive Types

```typescript
interface Category {
  name: string
  subcategories: Category[]
}

const categorySchema: z.ZodType<Category> = z.lazy(() =>
  z.object({
    name: z.string(),
    subcategories: z.array(categorySchema)
  })
)
```

### 5. Async Validation

```typescript
const usernameSchema = z.string().refine(
  async (username) => {
    const response = await fetch(`/api/check-username?username=${username}`)
    const { available } = await response.json()
    return available
  },
  { message: 'Username is already taken' }
)

// Use with parseAsync
const result = await usernameSchema.safeParseAsync('john123')
```

### 6. Partial and Pick

```typescript
const userSchema = z.object({
  id: z.string(),
  name: z.string(),
  email: z.string().email(),
  age: z.number()
})

// Make all fields optional
const partialUserSchema = userSchema.partial()

// Pick specific fields
const userPreviewSchema = userSchema.pick({ id: true, name: true })

// Omit fields
const userWithoutIdSchema = userSchema.omit({ id: true })

// Make specific fields optional
const updateUserSchema = userSchema.partial({ age: true, email: true })
```

## Real-World Examples

### Complete Registration Form

```typescript
import { z } from 'zod'
import { useForm } from 'react-hook-form'
import { zodResolver } from '@hookform/resolvers/zod'

const registrationSchema = z
  .object({
    username: z
      .string()
      .min(3, 'Username must be at least 3 characters')
      .max(20, 'Username must be at most 20 characters')
      .regex(/^[a-zA-Z0-9_]+$/, 'Username can only contain letters, numbers, and underscores'),
    email: z.string().email('Invalid email address'),
    password: z
      .string()
      .min(8, 'Password must be at least 8 characters')
      .regex(/[A-Z]/, 'Password must contain at least one uppercase letter')
      .regex(/[a-z]/, 'Password must contain at least one lowercase letter')
      .regex(/[0-9]/, 'Password must contain at least one number'),
    confirmPassword: z.string(),
    age: z
      .number({ invalid_type_error: 'Age must be a number' })
      .int('Age must be a whole number')
      .min(18, 'You must be at least 18 years old')
      .max(120, 'Please enter a valid age'),
    terms: z.boolean().refine((val) => val === true, {
      message: 'You must accept the terms and conditions'
    })
  })
  .refine((data) => data.password === data.confirmPassword, {
    message: 'Passwords do not match',
    path: ['confirmPassword']
  })

type RegistrationForm = z.infer<typeof registrationSchema>

function RegistrationForm() {
  const {
    register,
    handleSubmit,
    formState: { errors, isSubmitting }
  } = useForm<RegistrationForm>({
    resolver: zodResolver(registrationSchema)
  })

  const onSubmit = async (data: RegistrationForm) => {
    try {
      const response = await fetch('/api/register', {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify(data)
      })
      
      if (!response.ok) throw new Error('Registration failed')
      
      console.log('Registration successful')
    } catch (error) {
      console.error(error)
    }
  }

  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <div>
        <input {...register('username')} placeholder="Username" />
        {errors.username && <span>{errors.username.message}</span>}
      </div>

      <div>
        <input {...register('email')} type="email" placeholder="Email" />
        {errors.email && <span>{errors.email.message}</span>}
      </div>

      <div>
        <input {...register('password')} type="password" placeholder="Password" />
        {errors.password && <span>{errors.password.message}</span>}
      </div>

      <div>
        <input {...register('confirmPassword')} type="password" placeholder="Confirm Password" />
        {errors.confirmPassword && <span>{errors.confirmPassword.message}</span>}
      </div>

      <div>
        <input {...register('age', { valueAsNumber: true })} type="number" placeholder="Age" />
        {errors.age && <span>{errors.age.message}</span>}
      </div>

      <div>
        <label>
          <input {...register('terms')} type="checkbox" />
          I accept the terms and conditions
        </label>
        {errors.terms && <span>{errors.terms.message}</span>}
      </div>

      <button type="submit" disabled={isSubmitting}>
        {isSubmitting ? 'Registering...' : 'Register'}
      </button>
    </form>
  )
}
```

### Environment Variables Validation

```typescript
import { z } from 'zod'

const envSchema = z.object({
  NODE_ENV: z.enum(['development', 'production', 'test']),
  API_URL: z.string().url(),
  API_KEY: z.string().min(1),
  PORT: z.string().transform((val) => parseInt(val, 10)).pipe(z.number().int().positive()),
  DATABASE_URL: z.string().url(),
  ENABLE_ANALYTICS: z
    .string()
    .transform((val) => val === 'true')
    .pipe(z.boolean())
})

// Validate and export
export const env = envSchema.parse(process.env)

// Now you have type-safe environment variables
console.log(env.PORT) // number
console.log(env.ENABLE_ANALYTICS) // boolean
```

### API Client with Validation

```typescript
import { z } from 'zod'

const userSchema = z.object({
  id: z.string(),
  name: z.string(),
  email: z.string().email()
})

const usersListSchema = z.array(userSchema)

class ApiClient {
  private baseUrl: string

  constructor(baseUrl: string) {
    this.baseUrl = baseUrl
  }

  async getUser(id: string) {
    const response = await fetch(`${this.baseUrl}/users/${id}`)
    const json = await response.json()
    return userSchema.parse(json)
  }

  async getUsers() {
    const response = await fetch(`${this.baseUrl}/users`)
    const json = await response.json()
    return usersListSchema.parse(json)
  }

  async createUser(data: z.infer<typeof userSchema>) {
    // Validate input
    const validatedData = userSchema.omit({ id: true }).parse(data)
    
    const response = await fetch(`${this.baseUrl}/users`, {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify(validatedData)
    })
    
    const json = await response.json()
    return userSchema.parse(json)
  }
}
```

## Best Practices

### 1. Reuse Schemas

```typescript
// ✅ Good: Reusable schemas
const emailSchema = z.string().email()
const passwordSchema = z.string().min(8)

const loginSchema = z.object({
  email: emailSchema,
  password: passwordSchema
})

const signupSchema = z.object({
  email: emailSchema,
  password: passwordSchema,
  name: z.string()
})
```

### 2. Use Descriptive Error Messages

```typescript
// ✅ Good: Clear error messages
const ageSchema = z
  .number()
  .min(18, 'You must be at least 18 years old')
  .max(120, 'Please enter a valid age')

// ❌ Bad: Generic errors
const ageSchema = z.number().min(18).max(120)
```

### 3. Type Inference

```typescript
// ✅ Good: Infer types from schema
const userSchema = z.object({
  name: z.string(),
  age: z.number()
})

type User = z.infer<typeof userSchema>

// ❌ Bad: Duplicate type definitions
interface User {
  name: string
  age: number
}
```

### 4. Use safeParse for User Input

```typescript
// ✅ Good: safeParse for user input
const result = schema.safeParse(userInput)
if (!result.success) {
  handleErrors(result.error)
}

// ❌ Bad: parse throws errors
try {
  const data = schema.parse(userInput)
} catch (error) {
  // Error handling
}
```

### 5. Validate at Boundaries

```typescript
// ✅ Good: Validate at API boundaries
async function fetchUser(id: string) {
  const response = await fetch(`/api/users/${id}`)
  const json = await response.json()
  return userSchema.parse(json) // Validate external data
}

// ❌ Bad: Trust external data
async function fetchUser(id: string) {
  const response = await fetch(`/api/users/${id}`)
  return response.json() // No validation
}
```

## Common Pitfalls

### 1. Not Handling Parse Errors

```typescript
// ❌ Bad: parse throws
const data = schema.parse(input) // Throws on invalid data

// ✅ Good: safeParse returns result
const result = schema.safeParse(input)
if (!result.success) {
  console.log(result.error)
}
```

### 2. Overcomplicating Schemas

```typescript
// ❌ Bad: Too complex
const schema = z.object({
  data: z.object({
    user: z.object({
      profile: z.object({
        // Very nested
      })
    })
  })
})

// ✅ Good: Break into smaller schemas
const profileSchema = z.object({ /* ... */ })
const userSchema = z.object({ profile: profileSchema })
const dataSchema = z.object({ user: userSchema })
```

### 3. Not Using Transformations

```typescript
// ❌ Bad: Manual transformation
const result = schema.parse(data)
const transformed = {
  ...result,
  email: result.email.toLowerCase()
}

// ✅ Good: Use transform
const schema = z.object({
  email: z.string().email().transform(val => val.toLowerCase())
})
```

## Resources

### Official Documentation
- [Zod Docs](https://zod.dev/)
- [Zod GitHub](https://github.com/colinhacks/zod)
- [API Reference](https://zod.dev/README)

### Integrations
- [React Hook Form](https://react-hook-form.com/get-started#SchemaValidation)
- [tRPC](https://trpc.io/docs/server/validators)
- [Formik](https://formik.org/docs/guides/validation#validationschema)

### Tutorials
- [Zod Tutorial](https://www.totaltypescript.com/tutorials/zod)
- [Type-Safe Forms](https://react-hook-form.com/get-started#SchemaValidation)

### Community
- [GitHub Discussions](https://github.com/colinhacks/zod/discussions)
- [Discord](https://discord.gg/RcG33DQJdf)

---

**Next Steps:**
- Learn [React Hook Form](./react-hook-form.md) for form handling
- Explore [tRPC](../02-data-fetching/trpc.md) for end-to-end type safety
- Check out [TypeScript](../10-code-quality/typescript.md) for type system basics
