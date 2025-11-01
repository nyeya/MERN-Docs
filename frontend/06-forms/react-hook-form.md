# React Hook Form - Performant, Flexible Forms with Easy Validation

## Overview

React Hook Form is a performant, flexible, and extensible form library for React with easy-to-use validation. It minimizes re-renders, reduces the amount of code you need to write, and provides better performance compared to traditional form libraries. Built with React Hooks, it embraces uncontrolled components and native HTML form validation.

**Key Features:**
- Minimal re-renders for better performance
- Built-in validation with schema support (Zod, Yup, Joi)
- Small bundle size (~9KB)
- No dependencies
- TypeScript support
- Easy integration with UI libraries
- DevTools for debugging
- Native HTML validation
- Async validation support

## Why Use React Hook Form?

### Problems It Solves

1. **Performance Issues**: Eliminates unnecessary re-renders on every keystroke
2. **Boilerplate Code**: Reduces form code by up to 70%
3. **Complex Validation**: Built-in validation with schema libraries
4. **Form State Management**: Handles form state, errors, and submission
5. **Accessibility**: Works with native HTML validation
6. **Bundle Size**: Much smaller than alternatives like Formik

### When to Use React Hook Form

- ✅ Any form in your React application
- ✅ Complex forms with validation
- ✅ Forms with dynamic fields
- ✅ Multi-step forms
- ✅ Forms with file uploads
- ✅ Performance-critical forms
- ✅ Forms with conditional fields

### When NOT to Use React Hook Form

- ❌ Extremely simple forms (single input)
- ❌ When you need controlled components for every field
- ❌ Legacy projects with established form patterns

## Comparison with Alternatives

### React Hook Form vs Formik

| Feature | React Hook Form | Formik |
|---------|----------------|--------|
| Bundle Size | ~9KB | ~13KB |
| Re-renders | Minimal | More frequent |
| Performance | Excellent | Good |
| Validation | Schema-based | Schema or manual |
| Learning Curve | Easy | Easy |
| TypeScript | Excellent | Good |
| Maintenance | Active | Less active |
| Best For | Modern apps | Legacy apps |

**Choose React Hook Form if:** You want better performance and less code
**Choose Formik if:** You have existing Formik forms or prefer controlled components

### React Hook Form vs Native Forms

| Feature | React Hook Form | Native Forms |
|---------|----------------|--------------|
| Validation | Rich | Basic |
| Error Handling | Built-in | Manual |
| TypeScript | Yes | No |
| Schema Validation | Yes | No |
| Dev Experience | Excellent | Basic |
| Best For | Complex forms | Simple forms |

### React Hook Form vs Controlled Components

| Feature | React Hook Form | Controlled |
|---------|----------------|------------|
| Re-renders | Minimal | Every keystroke |
| Performance | Excellent | Can be slow |
| Code Amount | Less | More |
| Flexibility | High | High |
| Best For | Most forms | Real-time validation |

## Installation & Setup

### Installation

```bash
# npm
npm install react-hook-form

# yarn
yarn add react-hook-form

# pnpm
pnpm add react-hook-form
```

### With Zod (Recommended for Validation)

```bash
npm install zod @hookform/resolvers
```

### With TypeScript

React Hook Form has excellent TypeScript support out of the box!

### Basic Setup

```typescript
import { useForm } from 'react-hook-form'

function MyForm() {
  const { register, handleSubmit, formState: { errors } } = useForm()

  const onSubmit = (data) => {
    console.log(data)
  }

  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <input {...register('firstName')} />
      <button type="submit">Submit</button>
    </form>
  )
}
```

## Core Concepts

### 1. useForm Hook

The main hook that provides form methods:

```typescript
const {
  register,        // Register input
  handleSubmit,    // Handle form submission
  formState,       // Form state (errors, isDirty, etc.)
  watch,          // Watch input values
  reset,          // Reset form
  setValue,       // Set field value
  getValues,      // Get field values
  trigger,        // Trigger validation
} = useForm()
```

### 2. register

Registers an input and applies validation:

```typescript
<input {...register('email', {
  required: 'Email is required',
  pattern: {
    value: /^[A-Z0-9._%+-]+@[A-Z0-9.-]+\.[A-Z]{2,}$/i,
    message: 'Invalid email address'
  }
})} />
```

### 3. handleSubmit

Handles form submission and validation:

```typescript
const onSubmit = (data) => {
  console.log(data) // Only called if validation passes
}

const onError = (errors) => {
  console.log(errors) // Called if validation fails
}

<form onSubmit={handleSubmit(onSubmit, onError)}>
```

### 4. formState

Contains form state information:

```typescript
const { errors, isDirty, isValid, isSubmitting } = formState

// errors: Validation errors
// isDirty: Form has been modified
// isValid: Form is valid
// isSubmitting: Form is being submitted
```

### 5. watch

Watch input values:

```typescript
const email = watch('email') // Watch single field
const { email, password } = watch(['email', 'password']) // Watch multiple
const allValues = watch() // Watch all fields
```

## Basic Usage

### Simple Form

```typescript
import { useForm } from 'react-hook-form'

interface FormData {
  firstName: string
  lastName: string
  email: string
}

export function SimpleForm() {
  const {
    register,
    handleSubmit,
    formState: { errors },
  } = useForm<FormData>()

  const onSubmit = (data: FormData) => {
    console.log(data)
  }

  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <div>
        <label>First Name</label>
        <input
          {...register('firstName', { required: 'First name is required' })}
        />
        {errors.firstName && <span>{errors.firstName.message}</span>}
      </div>

      <div>
        <label>Last Name</label>
        <input
          {...register('lastName', { required: 'Last name is required' })}
        />
        {errors.lastName && <span>{errors.lastName.message}</span>}
      </div>

      <div>
        <label>Email</label>
        <input
          type="email"
          {...register('email', {
            required: 'Email is required',
            pattern: {
              value: /^[A-Z0-9._%+-]+@[A-Z0-9.-]+\.[A-Z]{2,}$/i,
              message: 'Invalid email address',
            },
          })}
        />
        {errors.email && <span>{errors.email.message}</span>}
      </div>

      <button type="submit">Submit</button>
    </form>
  )
}
```

### Form with Validation Rules

```typescript
import { useForm } from 'react-hook-form'

export function ValidationForm() {
  const { register, handleSubmit, formState: { errors } } = useForm()

  return (
    <form onSubmit={handleSubmit((data) => console.log(data))}>
      {/* Required field */}
      <input
        {...register('username', { required: 'Username is required' })}
      />

      {/* Min/Max length */}
      <input
        {...register('password', {
          required: 'Password is required',
          minLength: {
            value: 8,
            message: 'Password must be at least 8 characters',
          },
          maxLength: {
            value: 20,
            message: 'Password must be less than 20 characters',
          },
        })}
      />

      {/* Pattern validation */}
      <input
        {...register('phone', {
          pattern: {
            value: /^[0-9]{10}$/,
            message: 'Phone must be 10 digits',
          },
        })}
      />

      {/* Min/Max value */}
      <input
        type="number"
        {...register('age', {
          min: { value: 18, message: 'Must be at least 18' },
          max: { value: 100, message: 'Must be less than 100' },
        })}
      />

      {/* Custom validation */}
      <input
        {...register('confirmPassword', {
          validate: (value, formValues) =>
            value === formValues.password || 'Passwords must match',
        })}
      />

      <button type="submit">Submit</button>
    </form>
  )
}
```

### Form with Zod Schema

```typescript
import { useForm } from 'react-hook-form'
import { zodResolver } from '@hookform/resolvers/zod'
import * as z from 'zod'

const schema = z.object({
  email: z.string().email('Invalid email address'),
  password: z.string().min(8, 'Password must be at least 8 characters'),
  age: z.number().min(18, 'Must be at least 18'),
  terms: z.boolean().refine((val) => val === true, {
    message: 'You must accept terms and conditions',
  }),
})

type FormData = z.infer<typeof schema>

export function ZodForm() {
  const {
    register,
    handleSubmit,
    formState: { errors },
  } = useForm<FormData>({
    resolver: zodResolver(schema),
  })

  const onSubmit = (data: FormData) => {
    console.log(data)
  }

  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <input {...register('email')} />
      {errors.email && <span>{errors.email.message}</span>}

      <input type="password" {...register('password')} />
      {errors.password && <span>{errors.password.message}</span>}

      <input type="number" {...register('age', { valueAsNumber: true })} />
      {errors.age && <span>{errors.age.message}</span>}

      <input type="checkbox" {...register('terms')} />
      {errors.terms && <span>{errors.terms.message}</span>}

      <button type="submit">Submit</button>
    </form>
  )
}
```

## Advanced Usage

### 1. Dynamic Fields

```typescript
import { useForm, useFieldArray } from 'react-hook-form'

interface FormData {
  users: { name: string; email: string }[]
}

export function DynamicForm() {
  const { register, control, handleSubmit } = useForm<FormData>({
    defaultValues: {
      users: [{ name: '', email: '' }],
    },
  })

  const { fields, append, remove } = useFieldArray({
    control,
    name: 'users',
  })

  return (
    <form onSubmit={handleSubmit((data) => console.log(data))}>
      {fields.map((field, index) => (
        <div key={field.id}>
          <input {...register(`users.${index}.name`)} placeholder="Name" />
          <input {...register(`users.${index}.email`)} placeholder="Email" />
          <button type="button" onClick={() => remove(index)}>
            Remove
          </button>
        </div>
      ))}

      <button
        type="button"
        onClick={() => append({ name: '', email: '' })}
      >
        Add User
      </button>

      <button type="submit">Submit</button>
    </form>
  )
}
```

### 2. Controlled Components (Controller)

```typescript
import { useForm, Controller } from 'react-hook-form'
import Select from 'react-select'

export function ControlledForm() {
  const { control, handleSubmit } = useForm()

  return (
    <form onSubmit={handleSubmit((data) => console.log(data))}>
      <Controller
        name="country"
        control={control}
        rules={{ required: 'Country is required' }}
        render={({ field, fieldState: { error } }) => (
          <>
            <Select
              {...field}
              options={[
                { value: 'us', label: 'United States' },
                { value: 'uk', label: 'United Kingdom' },
              ]}
            />
            {error && <span>{error.message}</span>}
          </>
        )}
      />

      <button type="submit">Submit</button>
    </form>
  )
}
```

### 3. Multi-Step Form

```typescript
import { useState } from 'react'
import { useForm } from 'react-hook-form'

interface FormData {
  // Step 1
  firstName: string
  lastName: string
  // Step 2
  email: string
  phone: string
  // Step 3
  address: string
  city: string
}

export function MultiStepForm() {
  const [step, setStep] = useState(1)
  const { register, handleSubmit, formState: { errors }, trigger } = useForm<FormData>()

  const nextStep = async () => {
    let fieldsToValidate: (keyof FormData)[] = []
    
    if (step === 1) fieldsToValidate = ['firstName', 'lastName']
    if (step === 2) fieldsToValidate = ['email', 'phone']
    
    const isValid = await trigger(fieldsToValidate)
    if (isValid) setStep(step + 1)
  }

  const onSubmit = (data: FormData) => {
    console.log(data)
  }

  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      {step === 1 && (
        <div>
          <h2>Step 1: Personal Info</h2>
          <input
            {...register('firstName', { required: 'First name is required' })}
            placeholder="First Name"
          />
          {errors.firstName && <span>{errors.firstName.message}</span>}

          <input
            {...register('lastName', { required: 'Last name is required' })}
            placeholder="Last Name"
          />
          {errors.lastName && <span>{errors.lastName.message}</span>}

          <button type="button" onClick={nextStep}>
            Next
          </button>
        </div>
      )}

      {step === 2 && (
        <div>
          <h2>Step 2: Contact Info</h2>
          <input
            {...register('email', { required: 'Email is required' })}
            placeholder="Email"
          />
          {errors.email && <span>{errors.email.message}</span>}

          <input
            {...register('phone', { required: 'Phone is required' })}
            placeholder="Phone"
          />
          {errors.phone && <span>{errors.phone.message}</span>}

          <button type="button" onClick={() => setStep(1)}>
            Back
          </button>
          <button type="button" onClick={nextStep}>
            Next
          </button>
        </div>
      )}

      {step === 3 && (
        <div>
          <h2>Step 3: Address</h2>
          <input
            {...register('address', { required: 'Address is required' })}
            placeholder="Address"
          />
          {errors.address && <span>{errors.address.message}</span>}

          <input
            {...register('city', { required: 'City is required' })}
            placeholder="City"
          />
          {errors.city && <span>{errors.city.message}</span>}

          <button type="button" onClick={() => setStep(2)}>
            Back
          </button>
          <button type="submit">Submit</button>
        </div>
      )}
    </form>
  )
}
```

### 4. Async Validation

```typescript
import { useForm } from 'react-hook-form'

export function AsyncValidationForm() {
  const { register, handleSubmit, formState: { errors } } = useForm()

  const checkUsernameAvailable = async (username: string) => {
    const response = await fetch(`/api/check-username?username=${username}`)
    const data = await response.json()
    return data.available || 'Username is already taken'
  }

  return (
    <form onSubmit={handleSubmit((data) => console.log(data))}>
      <input
        {...register('username', {
          required: 'Username is required',
          validate: checkUsernameAvailable,
        })}
        placeholder="Username"
      />
      {errors.username && <span>{errors.username.message}</span>}

      <button type="submit">Submit</button>
    </form>
  )
}
```

### 5. Form with File Upload

```typescript
import { useForm } from 'react-hook-form'

interface FormData {
  avatar: FileList
  documents: FileList
}

export function FileUploadForm() {
  const { register, handleSubmit, watch } = useForm<FormData>()

  const avatar = watch('avatar')

  const onSubmit = (data: FormData) => {
    const formData = new FormData()
    formData.append('avatar', data.avatar[0])
    
    Array.from(data.documents).forEach((file) => {
      formData.append('documents', file)
    })

    // Send formData to server
    console.log(formData)
  }

  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <div>
        <label>Avatar</label>
        <input
          type="file"
          {...register('avatar', {
            required: 'Avatar is required',
            validate: {
              fileSize: (files) =>
                files[0]?.size < 2000000 || 'File must be less than 2MB',
              fileType: (files) =>
                ['image/jpeg', 'image/png'].includes(files[0]?.type) ||
                'Only JPEG and PNG files are allowed',
            },
          })}
        />
        {avatar?.[0] && (
          <img
            src={URL.createObjectURL(avatar[0])}
            alt="Preview"
            style={{ width: 100 }}
          />
        )}
      </div>

      <div>
        <label>Documents</label>
        <input
          type="file"
          multiple
          {...register('documents')}
        />
      </div>

      <button type="submit">Submit</button>
    </form>
  )
}
```

### 6. Dependent Fields

```typescript
import { useForm } from 'react-hook-form'

export function DependentFieldsForm() {
  const { register, handleSubmit, watch } = useForm()

  const hasOtherAddress = watch('hasOtherAddress')
  const country = watch('country')

  return (
    <form onSubmit={handleSubmit((data) => console.log(data))}>
      <input {...register('country')} placeholder="Country" />

      {country === 'US' && (
        <input {...register('state')} placeholder="State" />
      )}

      <label>
        <input type="checkbox" {...register('hasOtherAddress')} />
        Different billing address
      </label>

      {hasOtherAddress && (
        <div>
          <input {...register('billingAddress')} placeholder="Billing Address" />
          <input {...register('billingCity')} placeholder="Billing City" />
        </div>
      )}

      <button type="submit">Submit</button>
    </form>
  )
}
```

### 7. Integration with UI Libraries

```typescript
// With Material-UI
import { TextField } from '@mui/material'
import { useForm, Controller } from 'react-hook-form'

export function MUIForm() {
  const { control, handleSubmit } = useForm()

  return (
    <form onSubmit={handleSubmit((data) => console.log(data))}>
      <Controller
        name="email"
        control={control}
        rules={{ required: 'Email is required' }}
        render={({ field, fieldState: { error } }) => (
          <TextField
            {...field}
            label="Email"
            error={!!error}
            helperText={error?.message}
          />
        )}
      />

      <button type="submit">Submit</button>
    </form>
  )
}

// With Chakra UI
import { Input, FormControl, FormLabel, FormErrorMessage } from '@chakra-ui/react'

export function ChakraForm() {
  const { register, handleSubmit, formState: { errors } } = useForm()

  return (
    <form onSubmit={handleSubmit((data) => console.log(data))}>
      <FormControl isInvalid={!!errors.email}>
        <FormLabel>Email</FormLabel>
        <Input {...register('email', { required: 'Email is required' })} />
        <FormErrorMessage>{errors.email?.message}</FormErrorMessage>
      </FormControl>

      <button type="submit">Submit</button>
    </form>
  )
}
```

## Real-World Examples

### Complete Registration Form

```typescript
import { useForm } from 'react-hook-form'
import { zodResolver } from '@hookform/resolvers/zod'
import * as z from 'zod'
import { useMutation } from '@tanstack/react-query'

const registrationSchema = z.object({
  firstName: z.string().min(2, 'First name must be at least 2 characters'),
  lastName: z.string().min(2, 'Last name must be at least 2 characters'),
  email: z.string().email('Invalid email address'),
  password: z.string()
    .min(8, 'Password must be at least 8 characters')
    .regex(/[A-Z]/, 'Password must contain at least one uppercase letter')
    .regex(/[a-z]/, 'Password must contain at least one lowercase letter')
    .regex(/[0-9]/, 'Password must contain at least one number'),
  confirmPassword: z.string(),
  phone: z.string().regex(/^[0-9]{10}$/, 'Phone must be 10 digits'),
  dateOfBirth: z.string().refine((date) => {
    const age = new Date().getFullYear() - new Date(date).getFullYear()
    return age >= 18
  }, 'You must be at least 18 years old'),
  terms: z.boolean().refine((val) => val === true, {
    message: 'You must accept the terms and conditions',
  }),
}).refine((data) => data.password === data.confirmPassword, {
  message: 'Passwords do not match',
  path: ['confirmPassword'],
})

type RegistrationData = z.infer<typeof registrationSchema>

export function RegistrationForm() {
  const {
    register,
    handleSubmit,
    formState: { errors, isSubmitting },
    reset,
  } = useForm<RegistrationData>({
    resolver: zodResolver(registrationSchema),
  })

  const mutation = useMutation({
    mutationFn: async (data: RegistrationData) => {
      const response = await fetch('/api/register', {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify(data),
      })
      if (!response.ok) throw new Error('Registration failed')
      return response.json()
    },
    onSuccess: () => {
      alert('Registration successful!')
      reset()
    },
  })

  const onSubmit = (data: RegistrationData) => {
    mutation.mutate(data)
  }

  return (
    <form onSubmit={handleSubmit(onSubmit)} className="registration-form">
      <h2>Create Account</h2>

      <div className="form-row">
        <div className="form-group">
          <label>First Name</label>
          <input {...register('firstName')} />
          {errors.firstName && <span className="error">{errors.firstName.message}</span>}
        </div>

        <div className="form-group">
          <label>Last Name</label>
          <input {...register('lastName')} />
          {errors.lastName && <span className="error">{errors.lastName.message}</span>}
        </div>
      </div>

      <div className="form-group">
        <label>Email</label>
        <input type="email" {...register('email')} />
        {errors.email && <span className="error">{errors.email.message}</span>}
      </div>

      <div className="form-group">
        <label>Phone</label>
        <input type="tel" {...register('phone')} />
        {errors.phone && <span className="error">{errors.phone.message}</span>}
      </div>

      <div className="form-group">
        <label>Date of Birth</label>
        <input type="date" {...register('dateOfBirth')} />
        {errors.dateOfBirth && <span className="error">{errors.dateOfBirth.message}</span>}
      </div>

      <div className="form-group">
        <label>Password</label>
        <input type="password" {...register('password')} />
        {errors.password && <span className="error">{errors.password.message}</span>}
      </div>

      <div className="form-group">
        <label>Confirm Password</label>
        <input type="password" {...register('confirmPassword')} />
        {errors.confirmPassword && <span className="error">{errors.confirmPassword.message}</span>}
      </div>

      <div className="form-group">
        <label>
          <input type="checkbox" {...register('terms')} />
          I accept the terms and conditions
        </label>
        {errors.terms && <span className="error">{errors.terms.message}</span>}
      </div>

      <button type="submit" disabled={isSubmitting || mutation.isPending}>
        {isSubmitting || mutation.isPending ? 'Registering...' : 'Register'}
      </button>

      {mutation.isError && (
        <div className="error">Registration failed. Please try again.</div>
      )}
    </form>
  )
}
```

## Best Practices

### 1. Use Schema Validation

```typescript
// ✅ Good: Use Zod for validation
const schema = z.object({
  email: z.string().email(),
  password: z.string().min(8),
})

const { register } = useForm({
  resolver: zodResolver(schema),
})

// ❌ Bad: Inline validation
const { register } = useForm()
<input {...register('email', { 
  required: true,
  pattern: /email regex/
})} />
```

### 2. Type Your Forms

```typescript
// ✅ Good: TypeScript types
interface FormData {
  email: string
  password: string
}

const { register } = useForm<FormData>()

// ❌ Bad: No types
const { register } = useForm()
```

### 3. Handle Errors Properly

```typescript
// ✅ Good: Display all errors
{errors.email && <span>{errors.email.message}</span>}

// ❌ Bad: Silent errors
const { register } = useForm()
```

### 4. Use Default Values

```typescript
// ✅ Good: Set defaults
const { register } = useForm({
  defaultValues: {
    email: '',
    newsletter: true,
  },
})

// ❌ Bad: No defaults (uncontrolled)
```

### 5. Reset Form After Submission

```typescript
// ✅ Good: Reset on success
const onSubmit = async (data) => {
  await api.submit(data)
  reset()
}

// ❌ Bad: Leave form dirty
```

## Common Pitfalls

### 1. Not Using formState Correctly

```typescript
// ❌ Bad: Destructuring too early
const { errors } = useForm().formState

// ✅ Good: Destructure formState first
const { formState } = useForm()
const { errors } = formState
```

### 2. Forgetting valueAsNumber

```typescript
// ❌ Bad: Number as string
<input type="number" {...register('age')} />

// ✅ Good: Convert to number
<input type="number" {...register('age', { valueAsNumber: true })} />
```

### 3. Not Handling Async Errors

```typescript
// ❌ Bad: No error handling
const onSubmit = async (data) => {
  await api.submit(data)
}

// ✅ Good: Handle errors
const onSubmit = async (data) => {
  try {
    await api.submit(data)
  } catch (error) {
    setError('root', { message: error.message })
  }
}
```

### 4. Using Controlled Components Unnecessarily

```typescript
// ❌ Bad: Controlled when not needed
const [value, setValue] = useState('')
<input value={value} onChange={(e) => setValue(e.target.value)} />

// ✅ Good: Use register
<input {...register('field')} />
```

## Resources

### Official Documentation
- [React Hook Form Docs](https://react-hook-form.com/)
- [GitHub Repository](https://github.com/react-hook-form/react-hook-form)
- [API Reference](https://react-hook-form.com/api)

### Tutorials & Articles
- [React Hook Form Tutorial](https://www.robinwieruch.de/react-hook-form/)
- [Form Validation with Zod](https://react-hook-form.com/get-started#SchemaValidation)
- [Advanced Patterns](https://react-hook-form.com/advanced-usage)

### Video Tutorials
- [React Hook Form Crash Course](https://www.youtube.com/results?search_query=react+hook+form+crash+course)
- [Official YouTube Channel](https://www.youtube.com/c/ReactHookForm)

### Community
- [GitHub Discussions](https://github.com/react-hook-form/react-hook-form/discussions)
- [Discord](https://discord.gg/yYv7GZ8)
- [Stack Overflow](https://stackoverflow.com/questions/tagged/react-hook-form)

### Tools
- [DevTools](https://react-hook-form.com/dev-tools)
- [Form Builder](https://react-hook-form.com/form-builder)
- [Resolvers](https://github.com/react-hook-form/resolvers)

---

**Next Steps:**
- Learn about [Zod](./zod.md) for schema validation
- Explore [Formik](./formik.md) as an alternative
- Check out [TanStack Query](../02-data-fetching/tanstack-query.md) for form submissions
