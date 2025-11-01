# date-fns - Modern JavaScript Date Utility Library

## Overview

date-fns is a modern JavaScript date utility library that provides comprehensive, yet simple and consistent toolset for manipulating JavaScript dates in a browser and Node.js. It's modular, immutable, and tree-shakeable, making it an excellent choice for modern web applications.

**Key Features:**
- Modular and tree-shakeable
- Immutable and pure functions
- TypeScript support
- Consistent API
- I18n support (100+ locales)
- Time zone support
- Small bundle size (when tree-shaken)
- No dependencies
- FP (Functional Programming) support

## Why Use date-fns?

### Problems It Solves

1. **Date Manipulation**: Easy date formatting, parsing, and manipulation
2. **Immutability**: All functions are pure and don't mutate original dates
3. **Bundle Size**: Tree-shakeable - only import what you need
4. **Type Safety**: Excellent TypeScript support
5. **Internationalization**: Built-in support for 100+ locales
6. **Time Zones**: Proper time zone handling with date-fns-tz
7. **Consistency**: Uniform API across all functions

### When to Use date-fns

- ✅ Date formatting and parsing
- ✅ Date arithmetic (add/subtract days, months, etc.)
- ✅ Date comparisons
- ✅ Internationalization
- ✅ Time zone conversions
- ✅ Relative time formatting
- ✅ Calendar calculations

### When NOT to Use date-fns

- ❌ Simple date display (use native Intl.DateTimeFormat)
- ❌ Only need current date/time
- ❌ Very basic date operations

## Comparison with Alternatives

### date-fns vs Moment.js

| Feature | date-fns | Moment.js |
|---------|----------|-----------|
| Bundle Size | ~13KB (tree-shaken) | ~67KB (full) |
| Immutable | Yes | No (mutable) |
| Tree-shakeable | Yes | No |
| TypeScript | Excellent | Good |
| Maintenance | Active | Maintenance mode |
| API Style | Functional | OOP |
| Best For | Modern apps | Legacy apps |

**Choose date-fns if:** You want modern, tree-shakeable, immutable date library
**Choose Moment.js if:** You have legacy code (Moment is in maintenance mode)

### date-fns vs Day.js

| Feature | date-fns | Day.js |
|---------|----------|--------|
| Bundle Size | ~13KB | ~2KB |
| API | Functional | Moment-like |
| Functions | 200+ | ~50 |
| Immutable | Yes | Yes (with plugin) |
| TypeScript | Excellent | Good |
| Best For | Feature-rich | Minimal |

**Choose date-fns if:** You need comprehensive date utilities
**Choose Day.js if:** You want minimal bundle size

## Installation & Setup

### Installation

```bash
# npm
npm install date-fns

# yarn
yarn add date-fns

# pnpm
pnpm add date-fns
```

### For Time Zone Support

```bash
npm install date-fns-tz
```

### TypeScript Setup

date-fns has excellent built-in TypeScript support. No additional types needed!

### Basic Setup

```typescript
import { format, parseISO } from 'date-fns'

const date = new Date()
const formatted = format(date, 'yyyy-MM-dd')
console.log(formatted) // "2025-10-31"
```

## Core Concepts

### 1. Formatting Dates

```typescript
import { format } from 'date-fns'

const date = new Date(2025, 9, 31) // October 31, 2025

// Common formats
format(date, 'yyyy-MM-dd')           // "2025-10-31"
format(date, 'dd/MM/yyyy')           // "31/10/2025"
format(date, 'MMMM dd, yyyy')        // "October 31, 2025"
format(date, 'MMM dd, yyyy')         // "Oct 31, 2025"
format(date, 'EEEE, MMMM dd, yyyy')  // "Friday, October 31, 2025"
format(date, 'HH:mm:ss')             // "14:30:00"
format(date, 'h:mm a')               // "2:30 PM"
```

### 2. Parsing Dates

```typescript
import { parse, parseISO } from 'date-fns'

// Parse ISO string
const date1 = parseISO('2025-10-31')

// Parse custom format
const date2 = parse('31/10/2025', 'dd/MM/yyyy', new Date())
const date3 = parse('Oct 31, 2025', 'MMM dd, yyyy', new Date())
```

### 3. Date Arithmetic

```typescript
import { add, sub, addDays, addMonths, addYears } from 'date-fns'

const date = new Date(2025, 9, 31)

// Add time
addDays(date, 7)                     // 7 days later
addMonths(date, 3)                   // 3 months later
addYears(date, 1)                    // 1 year later

// Add multiple units
add(date, {
  years: 1,
  months: 2,
  days: 3,
  hours: 4,
  minutes: 5,
  seconds: 6
})

// Subtract time
sub(date, { days: 7, months: 2 })
```

### 4. Date Comparisons

```typescript
import { 
  isBefore, 
  isAfter, 
  isEqual, 
  isSameDay,
  isSameMonth,
  isSameYear,
  isWithinInterval
} from 'date-fns'

const date1 = new Date(2025, 9, 31)
const date2 = new Date(2025, 10, 1)

isBefore(date1, date2)               // true
isAfter(date1, date2)                // false
isEqual(date1, date1)                // true
isSameDay(date1, date2)              // false
isSameMonth(date1, date2)            // false

// Check if date is within interval
isWithinInterval(new Date(), {
  start: new Date(2025, 0, 1),
  end: new Date(2025, 11, 31)
})
```

## Basic Usage

### Format Current Date

```typescript
import { format } from 'date-fns'

function CurrentDate() {
  const now = new Date()
  
  return (
    <div>
      <p>Full: {format(now, 'EEEE, MMMM dd, yyyy')}</p>
      <p>Short: {format(now, 'MM/dd/yyyy')}</p>
      <p>Time: {format(now, 'h:mm a')}</p>
    </div>
  )
}
```

### Calculate Age

```typescript
import { differenceInYears } from 'date-fns'

function calculateAge(birthDate: Date): number {
  return differenceInYears(new Date(), birthDate)
}

function AgeDisplay({ birthDate }: { birthDate: Date }) {
  const age = calculateAge(birthDate)
  
  return <p>Age: {age} years old</p>
}
```

### Relative Time

```typescript
import { formatDistance, formatDistanceToNow } from 'date-fns'

function RelativeTime({ date }: { date: Date }) {
  return (
    <div>
      <p>{formatDistanceToNow(date, { addSuffix: true })}</p>
      {/* "3 days ago" or "in 5 hours" */}
    </div>
  )
}
```

## Advanced Usage

### 1. Internationalization

```typescript
import { format } from 'date-fns'
import { es, fr, de, ja } from 'date-fns/locale'

function InternationalDate({ date }: { date: Date }) {
  return (
    <div>
      <p>English: {format(date, 'PPPP')}</p>
      <p>Spanish: {format(date, 'PPPP', { locale: es })}</p>
      <p>French: {format(date, 'PPPP', { locale: fr })}</p>
      <p>German: {format(date, 'PPPP', { locale: de })}</p>
      <p>Japanese: {format(date, 'PPPP', { locale: ja })}</p>
    </div>
  )
}
```

### 2. Time Zone Support

```typescript
import { formatInTimeZone, toZonedTime } from 'date-fns-tz'

function TimeZoneDisplay({ date }: { date: Date }) {
  return (
    <div>
      <p>New York: {formatInTimeZone(date, 'America/New_York', 'yyyy-MM-dd HH:mm:ss zzz')}</p>
      <p>London: {formatInTimeZone(date, 'Europe/London', 'yyyy-MM-dd HH:mm:ss zzz')}</p>
      <p>Tokyo: {formatInTimeZone(date, 'Asia/Tokyo', 'yyyy-MM-dd HH:mm:ss zzz')}</p>
    </div>
  )
}
```

### 3. Date Range Picker Helper

```typescript
import { 
  startOfWeek, 
  endOfWeek, 
  startOfMonth, 
  endOfMonth,
  eachDayOfInterval,
  isSameMonth,
  isToday
} from 'date-fns'

function getCalendarDays(date: Date) {
  const start = startOfWeek(startOfMonth(date))
  const end = endOfWeek(endOfMonth(date))
  
  return eachDayOfInterval({ start, end })
}

function Calendar({ currentMonth }: { currentMonth: Date }) {
  const days = getCalendarDays(currentMonth)
  
  return (
    <div className="calendar">
      {days.map(day => (
        <div
          key={day.toISOString()}
          className={`
            ${!isSameMonth(day, currentMonth) ? 'other-month' : ''}
            ${isToday(day) ? 'today' : ''}
          `}
        >
          {format(day, 'd')}
        </div>
      ))}
    </div>
  )
}
```

### 4. Business Days Calculation

```typescript
import { 
  addBusinessDays, 
  isWeekend, 
  differenceInBusinessDays 
} from 'date-fns'

function calculateDeliveryDate(orderDate: Date, businessDays: number): Date {
  return addBusinessDays(orderDate, businessDays)
}

function DeliveryEstimate() {
  const orderDate = new Date()
  const deliveryDate = calculateDeliveryDate(orderDate, 5)
  
  return (
    <div>
      <p>Order Date: {format(orderDate, 'MMM dd, yyyy')}</p>
      <p>Estimated Delivery: {format(deliveryDate, 'MMM dd, yyyy')}</p>
      <p>Business Days: {differenceInBusinessDays(deliveryDate, orderDate)}</p>
    </div>
  )
}
```

### 5. Date Validation

```typescript
import { isValid, isPast, isFuture, isDate } from 'date-fns'

function validateDate(dateString: string): boolean {
  const date = new Date(dateString)
  return isValid(date)
}

function DateValidator({ dateString }: { dateString: string }) {
  const date = new Date(dateString)
  
  return (
    <div>
      <p>Valid: {isValid(date) ? 'Yes' : 'No'}</p>
      {isValid(date) && (
        <>
          <p>Is Past: {isPast(date) ? 'Yes' : 'No'}</p>
          <p>Is Future: {isFuture(date) ? 'Yes' : 'No'}</p>
        </>
      )}
    </div>
  )
}
```

### 6. Custom Formatting

```typescript
import { format, formatDuration, intervalToDuration } from 'date-fns'

function DurationDisplay({ start, end }: { start: Date; end: Date }) {
  const duration = intervalToDuration({ start, end })
  
  return (
    <div>
      <p>
        {formatDuration(duration, {
          format: ['years', 'months', 'days', 'hours', 'minutes']
        })}
      </p>
    </div>
  )
}

// Custom format tokens
function CustomFormat({ date }: { date: Date }) {
  return (
    <div>
      <p>Quarter: Q{format(date, 'Q')} {format(date, 'yyyy')}</p>
      <p>Week: Week {format(date, 'w')} of {format(date, 'yyyy')}</p>
      <p>Day of Year: Day {format(date, 'D')} of {format(date, 'yyyy')}</p>
    </div>
  )
}
```

## Real-World Examples

### Event Countdown Timer

```typescript
import { formatDistanceToNow, isPast } from 'date-fns'
import { useState, useEffect } from 'react'

function EventCountdown({ eventDate }: { eventDate: Date }) {
  const [timeLeft, setTimeLeft] = useState('')
  
  useEffect(() => {
    const timer = setInterval(() => {
      if (isPast(eventDate)) {
        setTimeLeft('Event has passed')
        clearInterval(timer)
      } else {
        setTimeLeft(formatDistanceToNow(eventDate, { addSuffix: true }))
      }
    }, 1000)
    
    return () => clearInterval(timer)
  }, [eventDate])
  
  return <div className="countdown">{timeLeft}</div>
}
```

### Date Range Filter

```typescript
import { isWithinInterval, startOfDay, endOfDay } from 'date-fns'

interface Item {
  id: number
  name: string
  date: Date
}

function DateRangeFilter({ 
  items, 
  startDate, 
  endDate 
}: { 
  items: Item[]
  startDate: Date
  endDate: Date
}) {
  const filteredItems = items.filter(item =>
    isWithinInterval(item.date, {
      start: startOfDay(startDate),
      end: endOfDay(endDate)
    })
  )
  
  return (
    <div>
      <h3>Items from {format(startDate, 'MMM dd')} to {format(endDate, 'MMM dd')}</h3>
      {filteredItems.map(item => (
        <div key={item.id}>
          {item.name} - {format(item.date, 'MMM dd, yyyy')}
        </div>
      ))}
    </div>
  )
}
```

### Recurring Events

```typescript
import { addWeeks, addMonths, isSameDay, startOfDay } from 'date-fns'

type RecurrenceType = 'daily' | 'weekly' | 'monthly'

function getNextOccurrence(
  startDate: Date,
  recurrence: RecurrenceType,
  count: number = 1
): Date {
  const today = startOfDay(new Date())
  let nextDate = startDate
  
  while (nextDate < today) {
    switch (recurrence) {
      case 'daily':
        nextDate = addDays(nextDate, 1)
        break
      case 'weekly':
        nextDate = addWeeks(nextDate, 1)
        break
      case 'monthly':
        nextDate = addMonths(nextDate, 1)
        break
    }
  }
  
  return nextDate
}

function RecurringEvent({ 
  startDate, 
  recurrence 
}: { 
  startDate: Date
  recurrence: RecurrenceType
}) {
  const nextOccurrence = getNextOccurrence(startDate, recurrence)
  
  return (
    <div>
      <p>Next occurrence: {format(nextOccurrence, 'EEEE, MMMM dd, yyyy')}</p>
      <p>{formatDistanceToNow(nextOccurrence, { addSuffix: true })}</p>
    </div>
  )
}
```

## Best Practices

### 1. Always Use Immutable Operations

```typescript
import { addDays } from 'date-fns'

// ✅ Good: date-fns returns new date
const originalDate = new Date()
const newDate = addDays(originalDate, 7)
// originalDate is unchanged

// ❌ Bad: Mutating date
const date = new Date()
date.setDate(date.getDate() + 7) // Mutates original
```

### 2. Tree-shake by Importing Specific Functions

```typescript
// ✅ Good: Import only what you need
import { format, addDays } from 'date-fns'

// ❌ Bad: Import everything
import * as dateFns from 'date-fns'
```

### 3. Use Consistent Date Reference

```typescript
// ✅ Good: Use reference date for parsing
import { parse } from 'date-fns'

const referenceDate = new Date()
const parsed = parse('31/10/2025', 'dd/MM/yyyy', referenceDate)

// ❌ Bad: No reference date
const parsed = parse('31/10/2025', 'dd/MM/yyyy', new Date(0))
```

### 4. Handle Time Zones Properly

```typescript
import { formatInTimeZone } from 'date-fns-tz'

// ✅ Good: Explicit time zone
const formatted = formatInTimeZone(date, 'America/New_York', 'yyyy-MM-dd HH:mm:ss zzz')

// ❌ Bad: Relying on local time zone
const formatted = format(date, 'yyyy-MM-dd HH:mm:ss')
```

## Common Pitfalls

### 1. Forgetting to Import Locale

```typescript
// ❌ Bad: No locale imported
import { format } from 'date-fns'
format(date, 'PPPP', { locale: es }) // Error: es is not defined

// ✅ Good: Import locale
import { format } from 'date-fns'
import { es } from 'date-fns/locale'
format(date, 'PPPP', { locale: es })
```

### 2. Using Wrong Format Tokens

```typescript
// ❌ Bad: Wrong tokens
format(date, 'DD/MM/YYYY') // DD is day of year, YYYY is week-numbering year

// ✅ Good: Correct tokens
format(date, 'dd/MM/yyyy') // dd is day of month, yyyy is year
```

### 3. Not Validating Dates

```typescript
import { isValid } from 'date-fns'

// ❌ Bad: No validation
const date = new Date(userInput)
format(date, 'yyyy-MM-dd') // Could error if invalid

// ✅ Good: Validate first
const date = new Date(userInput)
if (isValid(date)) {
  format(date, 'yyyy-MM-dd')
}
```

### 4. Comparing Dates Without Normalization

```typescript
import { isSameDay, startOfDay } from 'date-fns'

const date1 = new Date(2025, 9, 31, 10, 30)
const date2 = new Date(2025, 9, 31, 15, 45)

// ❌ Bad: Compares exact timestamps
date1 === date2 // false

// ✅ Good: Compare just the date
isSameDay(date1, date2) // true
```

## Resources

### Official Documentation
- [date-fns Docs](https://date-fns.org/)
- [date-fns GitHub](https://github.com/date-fns/date-fns)
- [API Reference](https://date-fns.org/docs/)

### Tools
- [date-fns Format String Builder](https://date-fns.org/docs/format)
- [Time Zone Database](https://github.com/marnusw/date-fns-tz)

### Tutorials
- [date-fns Guide](https://blog.logrocket.com/date-fns-a-complete-guide/)
- [Migrating from Moment.js](https://date-fns.org/docs/Migrating-from-Moment.js)

### Community
- [GitHub Discussions](https://github.com/date-fns/date-fns/discussions)
- [Stack Overflow](https://stackoverflow.com/questions/tagged/date-fns)

---

**Next Steps:**
- Explore [Luxon](https://moment.github.io/luxon/) for advanced time zone handling
- Learn [Day.js](https://day.js.org/) for minimal bundle size
- Check out [Temporal](https://tc39.es/proposal-temporal/) - upcoming native JavaScript date API
