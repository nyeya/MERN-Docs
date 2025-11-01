# Zod - TypeScript-First Schema Validation

## Table of Contents
- [What is Zod?](#what-is-zod)
- [Why Use Zod?](#why-use-zod)
- [Zod vs Alternatives](#zod-vs-alternatives)
- [Installation & Setup](#installation--setup)
- [Basic Schemas](#basic-schemas)
- [Schema Types](#schema-types)
- [Validation](#validation)
- [Transformations](#transformations)
- [Error Handling](#error-handling)
- [Express Integration](#express-integration)
- [Advanced Patterns](#advanced-patterns)
- [Best Practices](#best-practices)

## What is Zod?

Zod is a TypeScript-first schema declaration and validation library. It allows you to define schemas for your data and automatically infer TypeScript types from those schemas, ensuring type safety at both compile-time and runtime.

### Key Features
- **TypeScript-First**: Built with TypeScript, for TypeScript
- **Zero Dependencies**: Lightweight and fast
- **Type Inference**: Automatic TypeScript type generation
- **Composable**: Build complex schemas from simple ones
- **Immutable**: Schemas are immutable and chainable
- **Rich Error Messages**: Detailed validation errors
- **Transformations**: Parse and transform data

## Why Use Zod?

### 1. **Single Source of Truth**

**Without Zod:**
```typescript
// Define interface
interface User {
  email: string;
  age: number;
  name?: string;
}

// Manually validate
function validateUser(data: any): User {
  if (typeof data.email !== 'string') {
    throw new Error('Invalid email');
  }
  if (typeof data.age !== 'number') {
    throw new Error('Invalid age');
  }
  // ... more validation
  return data as User;
}
```

**With Zod:**
```typescript
import { z } from 'zod';

// Define schema (validation + type)
const UserSchema = z.object({
  email: z.string().email(),
  age: z.number().min(0),
  name: z.string().optional()
});

// Type automatically inferred
type User = z.infer<typeof UserSchema>;

// Validate
const user = UserSchema.parse(data); // Throws on invalid
```

### 2. **Runtime Type Safety**

```typescript
// TypeScript types are erased at runtime
interface User {
  email: string;
}

function processUser(user: User) {
  console.log(user.email.toUpperCase());
}

processUser({ email: 123 } as User); // Compiles but crashes at runtime!

// Zod validates at runtime
const UserSchema = z.object({
  email: z.string()
});

function processUser(data: unknown) {
  const user = UserSchema.parse(data); // Validates!
  console.log(user.email.toUpperCase()); // Safe
}
```

### 3. **Better Error Messages**

```typescript
const UserSchema = z.object({
  email: z.string().email(),
  age: z.number().min(18).max(120),
  password: z.string().min(8)
});

UserSchema.parse({
  email: 'invalid-email',
  age: 15,
  password: 'short'
});

// Error:
// [
//   { path: ['email'], message: 'Invalid email' },
//   { path: ['age'], message: 'Number must be greater than or equal to 18' },
//   { path: ['password'], message: 'String must contain at least 8 character(s)' }
// ]
```

### 4. **API Validation Made Easy**

```typescript
app.post('/users', (req, res) => {
  try {
    const user = UserSchema.parse(req.body);
    // user is now validated and typed
    res.json(user);
  } catch (err) {
    if (err instanceof z.ZodError) {
      res.status(400).json({ errors: err.errors });
    }
  }
});
```

## Zod vs Alternatives

### Zod vs Joi

| Feature | Zod | Joi |
|---------|-----|-----|
| **TypeScript Support** | Native, first-class | Via @types |
| **Type Inference** | Automatic | Manual |
| **Bundle Size** | ~8KB | ~146KB |
| **Dependencies** | Zero | Multiple |
| **API Style** | Chainable | Chainable |
| **Performance** | Excellent | Good |

**When to choose Zod:**
- TypeScript projects
- Want automatic type inference
- Need smaller bundle size
- Modern projects

**When to choose Joi:**
- JavaScript projects
- Existing Joi codebase
- Need specific Joi features

### Zod vs Yup

| Feature | Zod | Yup |
|---------|-----|-----|
| **TypeScript** | Native | Via TypeScript |
| **Type Inference** | Excellent | Good |
| **Bundle Size** | Smaller | Larger |
| **Async Validation** | Supported | Supported |
| **Transformations** | Built-in | Built-in |

**When to choose Zod:**
- TypeScript-first
- Better type inference
- Smaller bundle

**When to choose Yup:**
- React Hook Form integration
- Existing Yup schemas
- Prefer Yup API

### Zod vs class-validator

| Feature | Zod | class-validator |
|---------|-----|-----------------|
| **Approach** | Schema-based | Decorator-based |
| **TypeScript** | Native | Decorators |
| **Dependencies** | Zero | Multiple |
| **Use Case** | Functional | OOP/NestJS |

**When to choose Zod:**
- Functional programming
- Standalone validation
- Smaller bundle

**When to choose class-validator:**
- NestJS projects
- Prefer decorators
- Class-based architecture

### Zod vs express-validator

| Feature | Zod | express-validator |
|---------|-----|-------------------|
| **Framework** | Agnostic | Express-specific |
| **TypeScript** | Native | Via @types |
| **Type Inference** | Automatic | Manual |
| **Reusability** | High | Medium |

**When to choose Zod:**
- Want framework-agnostic validation
- Need type inference
- Use across frontend/backend

**When to choose express-validator:**
- Express-only project
- Prefer middleware approach
- Existing express-validator code

## Installation & Setup

### Installation

```bash
npm install zod
```

### Basic Setup

```typescript
import { z } from 'zod';

// Define schema
const UserSchema = z.object({
  name: z.string(),
  age: z.number()
});

// Infer type
type User = z.infer<typeof UserSchema>;

// Validate
const user = UserSchema.parse({ name: 'Alice', age: 25 });
```

### TypeScript Configuration

**tsconfig.json:**
```json
{
  "compilerOptions": {
    "strict": true,
    "strictNullChecks": true
  }
}
```

## Basic Schemas

### Primitive Types

```typescript
// String
const stringSchema = z.string();

// Number
const numberSchema = z.number();

// Boolean
const booleanSchema = z.boolean();

// BigInt
const bigintSchema = z.bigint();

// Date
const dateSchema = z.date();

// Undefined
const undefinedSchema = z.undefined();

// Null
const nullSchema = z.null();

// Any
const anySchema = z.any();

// Unknown
const unknownSchema = z.unknown();

// Never
const neverSchema = z.never();

// Void
const voidSchema = z.void();
```

### String Validations

```typescript
const schema = z.string()
  .min(3, 'Must be at least 3 characters')
  .max(20, 'Must be at most 20 characters')
  .email('Invalid email')
  .url('Invalid URL')
  .uuid('Invalid UUID')
  .regex(/^[a-z]+$/, 'Must be lowercase letters')
  .startsWith('hello')
  .endsWith('world')
  .includes('test')
  .trim()  // Remove whitespace
  .toLowerCase()
  .toUpperCase();

// Custom validation
const schema = z.string().refine(
  (val) => val.length > 0,
  { message: 'String cannot be empty' }
);
```

### Number Validations

```typescript
const schema = z.number()
  .min(0, 'Must be non-negative')
  .max(100, 'Must be at most 100')
  .int('Must be an integer')
  .positive('Must be positive')
  .negative('Must be negative')
  .nonnegative('Must be non-negative')
  .nonpositive('Must be non-positive')
  .multipleOf(5, 'Must be multiple of 5')
  .finite('Must be finite')
  .safe('Must be safe integer');

// Custom validation
const schema = z.number().refine(
  (val) => val % 2 === 0,
  { message: 'Must be even' }
);
```

### Object Schemas

```typescript
const UserSchema = z.object({
  id: z.number(),
  email: z.string().email(),
  name: z.string(),
  age: z.number().optional(),
  role: z.enum(['user', 'admin']).default('user')
});

type User = z.infer<typeof UserSchema>;
// {
//   id: number;
//   email: string;
//   name: string;
//   age?: number;
//   role: 'user' | 'admin';
// }

// Nested objects
const ProfileSchema = z.object({
  user: UserSchema,
  settings: z.object({
    theme: z.enum(['light', 'dark']),
    notifications: z.boolean()
  })
});
```

### Array Schemas

```typescript
// Array of strings
const stringArray = z.array(z.string());

// Array with constraints
const schema = z.array(z.number())
  .min(1, 'At least one item required')
  .max(10, 'Maximum 10 items')
  .nonempty('Array cannot be empty')
  .length(5, 'Must have exactly 5 items');

// Array of objects
const usersSchema = z.array(UserSchema);

// Non-empty array
const nonEmptyArray = z.string().array().nonempty();
```

### Tuple Schemas

```typescript
// Fixed-length array with specific types
const tuple = z.tuple([
  z.string(),
  z.number(),
  z.boolean()
]);

type Tuple = z.infer<typeof tuple>;
// [string, number, boolean]

// With rest
const tupleWithRest = z.tuple([z.string(), z.number()]).rest(z.boolean());
// [string, number, ...boolean[]]
```

### Enum Schemas

```typescript
// Native enum
enum Role {
  User = 'user',
  Admin = 'admin'
}

const roleSchema = z.nativeEnum(Role);

// Zod enum
const roleSchema = z.enum(['user', 'admin', 'moderator']);

type Role = z.infer<typeof roleSchema>;
// 'user' | 'admin' | 'moderator'
```

### Union & Intersection

```typescript
// Union (OR)
const stringOrNumber = z.union([z.string(), z.number()]);
// or
const stringOrNumber = z.string().or(z.number());

// Discriminated union
const ResponseSchema = z.discriminatedUnion('status', [
  z.object({ status: z.literal('success'), data: z.any() }),
  z.object({ status: z.literal('error'), error: z.string() })
]);

// Intersection (AND)
const BaseSchema = z.object({ id: z.number() });
const NameSchema = z.object({ name: z.string() });
const combined = z.intersection(BaseSchema, NameSchema);
// or
const combined = BaseSchema.and(NameSchema);
```

## Schema Types

### Optional & Nullable

```typescript
// Optional (can be undefined)
const optional = z.string().optional();
type Optional = z.infer<typeof optional>; // string | undefined

// Nullable (can be null)
const nullable = z.string().nullable();
type Nullable = z.infer<typeof nullable>; // string | null

// Both
const nullish = z.string().nullish();
type Nullish = z.infer<typeof nullish>; // string | null | undefined
```

### Default Values

```typescript
const schema = z.object({
  name: z.string(),
  role: z.string().default('user'),
  active: z.boolean().default(true)
});

const result = schema.parse({ name: 'Alice' });
// { name: 'Alice', role: 'user', active: true }
```

### Literal Types

```typescript
const literalSchema = z.literal('hello');
type Literal = z.infer<typeof literalSchema>; // 'hello'

const statusSchema = z.union([
  z.literal('pending'),
  z.literal('approved'),
  z.literal('rejected')
]);
```

### Record & Map

```typescript
// Record (object with string keys)
const recordSchema = z.record(z.string(), z.number());
type Record = z.infer<typeof recordSchema>; // { [key: string]: number }

// Map
const mapSchema = z.map(z.string(), z.number());
type Map = z.infer<typeof mapSchema>; // Map<string, number>

// Set
const setSchema = z.set(z.string());
type Set = z.infer<typeof setSchema>; // Set<string>
```

### Promise & Function

```typescript
// Promise
const promiseSchema = z.promise(z.string());
type Promise = z.infer<typeof promiseSchema>; // Promise<string>

// Function
const functionSchema = z.function()
  .args(z.string(), z.number())
  .returns(z.boolean());

type Function = z.infer<typeof functionSchema>;
// (arg0: string, arg1: number) => boolean
```

## Validation

### Parse vs SafeParse

```typescript
const schema = z.string();

// parse - throws on error
try {
  const result = schema.parse('hello'); // 'hello'
  const result = schema.parse(123); // throws ZodError
} catch (err) {
  console.error(err);
}

// safeParse - returns result object
const result = schema.safeParse('hello');
if (result.success) {
  console.log(result.data); // 'hello'
} else {
  console.log(result.error); // ZodError
}

const result = schema.safeParse(123);
// { success: false, error: ZodError }
```

### Async Validation

```typescript
const schema = z.string().refine(
  async (val) => {
    // Async validation (e.g., check database)
    const exists = await checkDatabase(val);
    return !exists;
  },
  { message: 'Already exists' }
);

// Use parseAsync or safeParseAsync
const result = await schema.parseAsync('test');
const result = await schema.safeParseAsync('test');
```

### Partial & Required

```typescript
const UserSchema = z.object({
  name: z.string(),
  email: z.string(),
  age: z.number()
});

// Make all fields optional
const PartialUser = UserSchema.partial();
type PartialUser = z.infer<typeof PartialUser>;
// { name?: string; email?: string; age?: number; }

// Make specific fields optional
const PartialUser = UserSchema.partial({ age: true });

// Make all fields required
const RequiredUser = UserSchema.required();

// Pick specific fields
const PickedUser = UserSchema.pick({ name: true, email: true });

// Omit specific fields
const OmittedUser = UserSchema.omit({ age: true });
```

### Extend & Merge

```typescript
const BaseSchema = z.object({
  id: z.number(),
  createdAt: z.date()
});

// Extend
const UserSchema = BaseSchema.extend({
  name: z.string(),
  email: z.string()
});

// Merge
const ProfileSchema = z.object({
  bio: z.string(),
  avatar: z.string()
});

const FullProfile = UserSchema.merge(ProfileSchema);
```

## Transformations

### Transform

```typescript
// String to number
const schema = z.string().transform((val) => parseInt(val));
const result = schema.parse('123'); // 123 (number)

// Trim and lowercase
const schema = z.string().transform((val) => val.trim().toLowerCase());

// Parse date string
const schema = z.string().transform((val) => new Date(val));

// Complex transformation
const UserSchema = z.object({
  firstName: z.string(),
  lastName: z.string()
}).transform((data) => ({
  ...data,
  fullName: `${data.firstName} ${data.lastName}`
}));

const result = UserSchema.parse({
  firstName: 'John',
  lastName: 'Doe'
});
// { firstName: 'John', lastName: 'Doe', fullName: 'John Doe' }
```

### Preprocess

```typescript
// Coerce string to number
const schema = z.preprocess(
  (val) => typeof val === 'string' ? parseInt(val) : val,
  z.number()
);

schema.parse('123'); // 123
schema.parse(123); // 123

// Coerce to date
const dateSchema = z.preprocess(
  (val) => typeof val === 'string' ? new Date(val) : val,
  z.date()
);
```

### Coerce

```typescript
// Built-in coercion
const numberSchema = z.coerce.number();
numberSchema.parse('123'); // 123
numberSchema.parse(true); // 1

const dateSchema = z.coerce.date();
dateSchema.parse('2024-01-01'); // Date object

const booleanSchema = z.coerce.boolean();
booleanSchema.parse('true'); // true
booleanSchema.parse(1); // true
```

## Error Handling

### ZodError Structure

```typescript
const UserSchema = z.object({
  email: z.string().email(),
  age: z.number().min(18)
});

try {
  UserSchema.parse({ email: 'invalid', age: 15 });
} catch (err) {
  if (err instanceof z.ZodError) {
    console.log(err.errors);
    // [
    //   {
    //     code: 'invalid_string',
    //     validation: 'email',
    //     path: ['email'],
    //     message: 'Invalid email'
    //   },
    //   {
    //     code: 'too_small',
    //     minimum: 18,
    //     path: ['age'],
    //     message: 'Number must be greater than or equal to 18'
    //   }
    // ]
  }
}
```

### Custom Error Messages

```typescript
const schema = z.string({
  required_error: 'Name is required',
  invalid_type_error: 'Name must be a string'
});

const schema = z.number().min(18, {
  message: 'Must be 18 or older'
});

const schema = z.string().email({
  message: 'Please provide a valid email address'
});
```

### Error Formatting

```typescript
const result = schema.safeParse(data);

if (!result.success) {
  // Flatten errors
  const formatted = result.error.flatten();
  console.log(formatted);
  // {
  //   formErrors: [],
  //   fieldErrors: {
  //     email: ['Invalid email'],
  //     age: ['Must be at least 18']
  //   }
  // }

  // Format for specific field
  const formatted = result.error.format();
  console.log(formatted.email?._errors); // ['Invalid email']
}
```

### Custom Error Map

```typescript
import { z } from 'zod';

const customErrorMap: z.ZodErrorMap = (issue, ctx) => {
  if (issue.code === z.ZodIssueCode.invalid_type) {
    if (issue.expected === 'string') {
      return { message: 'This field must be text' };
    }
  }
  if (issue.code === z.ZodIssueCode.too_small) {
    return { message: `Minimum length is ${issue.minimum}` };
  }
  return { message: ctx.defaultError };
};

z.setErrorMap(customErrorMap);
```

## Express Integration

### Validation Middleware

```typescript
import { z } from 'zod';
import { Request, Response, NextFunction } from 'express';

// Generic validation middleware
function validate(schema: z.ZodSchema) {
  return (req: Request, res: Response, next: NextFunction) => {
    try {
      schema.parse(req.body);
      next();
    } catch (err) {
      if (err instanceof z.ZodError) {
        return res.status(400).json({
          error: 'Validation failed',
          details: err.errors
        });
      }
      next(err);
    }
  };
}

// Usage
const CreateUserSchema = z.object({
  email: z.string().email(),
  password: z.string().min(8),
  name: z.string().min(2)
});

app.post('/users', validate(CreateUserSchema), (req, res) => {
  // req.body is now validated
  const user = req.body;
  res.json(user);
});
```

### Validate Multiple Sources

```typescript
function validateRequest(schemas: {
  body?: z.ZodSchema;
  query?: z.ZodSchema;
  params?: z.ZodSchema;
}) {
  return (req: Request, res: Response, next: NextFunction) => {
    try {
      if (schemas.body) {
        req.body = schemas.body.parse(req.body);
      }
      if (schemas.query) {
        req.query = schemas.query.parse(req.query);
      }
      if (schemas.params) {
        req.params = schemas.params.parse(req.params);
      }
      next();
    } catch (err) {
      if (err instanceof z.ZodError) {
        return res.status(400).json({
          error: 'Validation failed',
          details: err.flatten()
        });
      }
      next(err);
    }
  };
}

// Usage
app.get(
  '/users/:id',
  validateRequest({
    params: z.object({
      id: z.string().regex(/^\d+$/).transform(Number)
    }),
    query: z.object({
      include: z.enum(['posts', 'profile']).optional()
    })
  }),
  (req, res) => {
    const { id } = req.params; // number
    const { include } = req.query; // 'posts' | 'profile' | undefined
    res.json({ id, include });
  }
);
```

### Type-Safe Request Handler

```typescript
import { z } from 'zod';
import { Request, Response, NextFunction } from 'express';

type ValidatedRequest<
  TBody = any,
  TQuery = any,
  TParams = any
> = Request<TParams, any, TBody, TQuery>;

function createHandler<
  TBody extends z.ZodSchema,
  TQuery extends z.ZodSchema,
  TParams extends z.ZodSchema
>(
  schemas: {
    body?: TBody;
    query?: TQuery;
    params?: TParams;
  },
  handler: (
    req: ValidatedRequest<
      z.infer<TBody>,
      z.infer<TQuery>,
      z.infer<TParams>
    >,
    res: Response
  ) => Promise<void> | void
) {
  return async (req: Request, res: Response, next: NextFunction) => {
    try {
      if (schemas.body) {
        req.body = schemas.body.parse(req.body);
      }
      if (schemas.query) {
        req.query = schemas.query.parse(req.query);
      }
      if (schemas.params) {
        req.params = schemas.params.parse(req.params);
      }
      await handler(req as any, res);
    } catch (err) {
      if (err instanceof z.ZodError) {
        return res.status(400).json({
          error: 'Validation failed',
          details: err.errors
        });
      }
      next(err);
    }
  };
}

// Usage with full type safety
app.post(
  '/users',
  createHandler(
    {
      body: z.object({
        email: z.string().email(),
        name: z.string()
      })
    },
    async (req, res) => {
      // req.body is fully typed!
      const { email, name } = req.body;
      res.json({ email, name });
    }
  )
);
```

## Advanced Patterns

### Recursive Schemas

```typescript
interface Category {
  name: string;
  subcategories: Category[];
}

const CategorySchema: z.ZodType<Category> = z.lazy(() =>
  z.object({
    name: z.string(),
    subcategories: z.array(CategorySchema)
  })
);
```

### Conditional Schemas

```typescript
const FormSchema = z.discriminatedUnion('type', [
  z.object({
    type: z.literal('email'),
    email: z.string().email()
  }),
  z.object({
    type: z.literal('phone'),
    phone: z.string().regex(/^\d{10}$/)
  })
]);

// Or using refine
const schema = z.object({
  type: z.enum(['email', 'phone']),
  email: z.string().optional(),
  phone: z.string().optional()
}).refine(
  (data) => {
    if (data.type === 'email') return !!data.email;
    if (data.type === 'phone') return !!data.phone;
    return false;
  },
  { message: 'Email or phone required based on type' }
);
```

### Schema Composition

```typescript
// Base schemas
const TimestampSchema = z.object({
  createdAt: z.date(),
  updatedAt: z.date()
});

const IdSchema = z.object({
  id: z.number()
});

// Compose
const UserSchema = z.object({
  email: z.string().email(),
  name: z.string()
}).merge(IdSchema).merge(TimestampSchema);

// Reusable patterns
const withTimestamps = <T extends z.ZodRawShape>(schema: z.ZodObject<T>) =>
  schema.merge(TimestampSchema);

const UserSchema = withTimestamps(
  z.object({
    email: z.string().email(),
    name: z.string()
  })
);
```

## Best Practices

### 1. Centralize Schemas

```typescript
// schemas/user.schema.ts
export const CreateUserSchema = z.object({
  email: z.string().email(),
  password: z.string().min(8),
  name: z.string()
});

export const UpdateUserSchema = CreateUserSchema.partial();

export const UserIdSchema = z.object({
  id: z.string().regex(/^\d+$/).transform(Number)
});

export type CreateUserInput = z.infer<typeof CreateUserSchema>;
export type UpdateUserInput = z.infer<typeof UpdateUserSchema>;
```

### 2. Reuse Common Patterns

```typescript
// schemas/common.schema.ts
export const emailSchema = z.string().email().toLowerCase();
export const passwordSchema = z.string().min(8).max(100);
export const idSchema = z.number().int().positive();
export const dateStringSchema = z.string().datetime();

// Use in other schemas
const UserSchema = z.object({
  email: emailSchema,
  password: passwordSchema,
  id: idSchema
});
```

### 3. Environment Variables

```typescript
// config/env.ts
import { z } from 'zod';

const envSchema = z.object({
  NODE_ENV: z.enum(['development', 'production', 'test']),
  PORT: z.string().transform(Number),
  DATABASE_URL: z.string().url(),
  JWT_SECRET: z.string().min(32),
  JWT_EXPIRES_IN: z.string().default('7d')
});

export const env = envSchema.parse(process.env);

// Now env is fully typed and validated!
```

### 4. API Response Schemas

```typescript
const SuccessResponseSchema = <T extends z.ZodSchema>(dataSchema: T) =>
  z.object({
    success: z.literal(true),
    data: dataSchema
  });

const ErrorResponseSchema = z.object({
  success: z.literal(false),
  error: z.string(),
  details: z.array(z.any()).optional()
});

const UserResponseSchema = SuccessResponseSchema(UserSchema);

// Usage
app.get('/users/:id', async (req, res) => {
  const user = await getUser(req.params.id);
  const response = UserResponseSchema.parse({
    success: true,
    data: user
  });
  res.json(response);
});
```

### 5. Testing with Zod

```typescript
import { describe, it, expect } from 'vitest';

describe('UserSchema', () => {
  it('should validate correct user data', () => {
    const validUser = {
      email: 'test@example.com',
      name: 'Test User',
      age: 25
    };
    
    expect(() => UserSchema.parse(validUser)).not.toThrow();
  });

  it('should reject invalid email', () => {
    const invalidUser = {
      email: 'invalid-email',
      name: 'Test User',
      age: 25
    };
    
    expect(() => UserSchema.parse(invalidUser)).toThrow(z.ZodError);
  });

  it('should provide correct error messages', () => {
    const result = UserSchema.safeParse({
      email: 'invalid',
      age: 15
    });
    
    expect(result.success).toBe(false);
    if (!result.success) {
      expect(result.error.errors).toHaveLength(2);
    }
  });
});
```

## Summary

Zod is the modern choice for TypeScript validation:

✅ **Type-Safe** - Automatic TypeScript type inference  
✅ **Runtime Validation** - Catch errors at runtime  
✅ **Zero Dependencies** - Lightweight and fast  
✅ **Composable** - Build complex schemas easily  
✅ **Great DX** - Excellent developer experience  
✅ **Framework Agnostic** - Works everywhere  

**Key Takeaways:**
- Use Zod for all data validation in TypeScript projects
- Centralize schemas for reusability
- Leverage type inference for type safety
- Use safeParse for better error handling
- Validate environment variables
- Create reusable validation middleware

Zod provides the best developer experience for schema validation in TypeScript applications.
