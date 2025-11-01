# Prisma - Next-Generation ORM

## Table of Contents
- [What is Prisma?](#what-is-prisma)
- [Why Use Prisma?](#why-use-prisma)
- [Prisma vs Alternatives](#prisma-vs-alternatives)
- [Installation & Setup](#installation--setup)
- [Prisma Schema](#prisma-schema)
- [Prisma Client](#prisma-client)
- [CRUD Operations](#crud-operations)
- [Relations](#relations)
- [Advanced Queries](#advanced-queries)
- [Migrations](#migrations)
- [Best Practices](#best-practices)

## What is Prisma?

Prisma is a next-generation ORM (Object-Relational Mapping) that consists of three main tools:

1. **Prisma Client**: Auto-generated and type-safe database client
2. **Prisma Migrate**: Declarative data modeling and migration system
3. **Prisma Studio**: GUI to view and edit data in your database

### Key Features
- **Type-Safe**: Full TypeScript support with auto-completion
- **Auto-Generated Client**: Database client generated from schema
- **Declarative Schema**: Define models in intuitive schema language
- **Database Agnostic**: Works with PostgreSQL, MySQL, SQLite, MongoDB, etc.
- **Migration System**: Version control for your database schema
- **Intuitive API**: Clean and predictable query interface

## Why Use Prisma?

### 1. **Type Safety**

**Traditional ORMs:**
```javascript
// No type safety
const user = await User.findOne({ where: { email: 'test@example.com' } });
console.log(user.emial); // Typo! No error until runtime
```

**With Prisma:**
```typescript
// Full type safety
const user = await prisma.user.findUnique({
  where: { email: 'test@example.com' }
});
console.log(user.emial); // TypeScript error: Property 'emial' does not exist
```

### 2. **Auto-Completion**

```typescript
const users = await prisma.user.findMany({
  where: {
    // IDE suggests: id, email, name, createdAt, etc.
    email: { contains: '@example.com' }
  },
  include: {
    // IDE suggests: posts, profile, comments, etc.
    posts: true
  }
});
```

### 3. **Single Source of Truth**

```prisma
// schema.prisma - One place to define your data model
model User {
  id        Int      @id @default(autoincrement())
  email     String   @unique
  name      String?
  posts     Post[]
  createdAt DateTime @default(now())
}

model Post {
  id        Int      @id @default(autoincrement())
  title     String
  content   String?
  published Boolean  @default(false)
  author    User     @relation(fields: [authorId], references: [id])
  authorId  Int
}
```

### 4. **Database Migrations**

```bash
# Create migration from schema changes
npx prisma migrate dev --name add_user_profile

# Apply migrations in production
npx prisma migrate deploy
```

### 5. **Visual Database Browser**

```bash
# Open Prisma Studio
npx prisma studio
# Opens GUI at http://localhost:5555
```

## Prisma vs Alternatives

### Prisma vs TypeORM

| Feature | Prisma | TypeORM |
|---------|--------|---------|
| **Type Safety** | Excellent (generated) | Good (decorators) |
| **Learning Curve** | Gentle | Moderate |
| **Schema Definition** | Prisma Schema | TypeScript decorators |
| **Query Builder** | Fluent API | QueryBuilder + Repository |
| **Migrations** | Built-in | Built-in |
| **Performance** | Excellent | Good |
| **Bundle Size** | Smaller | Larger |

**When to choose Prisma:**
- TypeScript-first projects
- Want auto-generated types
- Prefer declarative schema
- Need excellent DX (Developer Experience)

**When to choose TypeORM:**
- Need Active Record pattern
- Want decorators in code
- Complex legacy database
- Need DataMapper pattern

### Prisma vs Sequelize

| Feature | Prisma | Sequelize |
|---------|--------|-----------|
| **Type Safety** | Native TypeScript | Via @types |
| **API Style** | Modern Promise-based | Callback/Promise hybrid |
| **Schema** | Declarative file | JavaScript models |
| **Migrations** | Declarative | Imperative |
| **Documentation** | Excellent | Good |
| **Community** | Growing fast | Established |

**When to choose Prisma:**
- New projects
- TypeScript required
- Want modern API
- Need better DX

**When to choose Sequelize:**
- Existing Sequelize project
- JavaScript-only project
- Need specific Sequelize features

### Prisma vs Mongoose

| Feature | Prisma | Mongoose |
|---------|--------|----------|
| **Database** | SQL + MongoDB | MongoDB only |
| **Schema** | Prisma Schema | JavaScript schemas |
| **Type Safety** | Excellent | Via TypeScript |
| **Validation** | Database-level | Application-level |
| **Relations** | Native support | Manual population |

**When to choose Prisma:**
- Want type safety
- Need SQL support
- Prefer declarative schema

**When to choose Mongoose:**
- MongoDB-only project
- Need schema validation
- Want middleware hooks
- Established Mongoose codebase

### Prisma vs Drizzle

| Feature | Prisma | Drizzle |
|---------|--------|---------|
| **Type Safety** | Generated | Native TypeScript |
| **Bundle Size** | Larger | Smaller |
| **Learning Curve** | Easy | Easy |
| **Migrations** | Automatic | Manual/Automatic |
| **Query Builder** | Fluent API | SQL-like |
| **Edge Runtime** | Limited | Full support |

**When to choose Prisma:**
- Want generated client
- Need Prisma Studio
- Prefer fluent API

**When to choose Drizzle:**
- Need edge runtime
- Want smaller bundle
- Prefer SQL-like syntax

## Installation & Setup

### Prerequisites

```bash
node --version  # v16.13+
npm --version   # v8+
```

### Installation

```bash
# Create new project
mkdir my-prisma-app
cd my-prisma-app
npm init -y

# Install Prisma
npm install prisma --save-dev
npm install @prisma/client

# Initialize Prisma
npx prisma init
```

This creates:
```
my-prisma-app/
├── prisma/
│   └── schema.prisma
├── .env
├── .gitignore
└── package.json
```

### Database Configuration

**.env:**
```env
# PostgreSQL
DATABASE_URL="postgresql://user:password@localhost:5432/mydb?schema=public"

# MySQL
DATABASE_URL="mysql://user:password@localhost:3306/mydb"

# SQLite
DATABASE_URL="file:./dev.db"

# MongoDB
DATABASE_URL="mongodb+srv://user:password@cluster.mongodb.net/mydb"
```

### Initial Schema

**prisma/schema.prisma:**
```prisma
generator client {
  provider = "prisma-client-js"
}

datasource db {
  provider = "postgresql"  // or "mysql", "sqlite", "mongodb"
  url      = env("DATABASE_URL")
}

model User {
  id        Int      @id @default(autoincrement())
  email     String   @unique
  name      String?
  createdAt DateTime @default(now())
  updatedAt DateTime @updatedAt
}
```

### Generate Client

```bash
# Generate Prisma Client
npx prisma generate

# Create and apply migration
npx prisma migrate dev --name init
```

### TypeScript Setup

**tsconfig.json:**
```json
{
  "compilerOptions": {
    "target": "ES2020",
    "module": "commonjs",
    "lib": ["ES2020"],
    "outDir": "./dist",
    "rootDir": "./src",
    "strict": true,
    "esModuleInterop": true,
    "skipLibCheck": true,
    "forceConsistentCasingInFileNames": true
  }
}
```

**package.json:**
```json
{
  "scripts": {
    "dev": "ts-node src/index.ts",
    "build": "tsc",
    "start": "node dist/index.js",
    "prisma:generate": "prisma generate",
    "prisma:migrate": "prisma migrate dev",
    "prisma:studio": "prisma studio"
  },
  "devDependencies": {
    "@types/node": "^20.0.0",
    "prisma": "^5.0.0",
    "ts-node": "^10.9.0",
    "typescript": "^5.0.0"
  },
  "dependencies": {
    "@prisma/client": "^5.0.0"
  }
}
```

## Prisma Schema

### Basic Model Definition

```prisma
model User {
  id        Int      @id @default(autoincrement())
  email     String   @unique
  name      String?
  age       Int
  isActive  Boolean  @default(true)
  role      Role     @default(USER)
  createdAt DateTime @default(now())
  updatedAt DateTime @updatedAt
}

enum Role {
  USER
  ADMIN
  MODERATOR
}
```

### Field Types

```prisma
model Example {
  // Numbers
  intField      Int
  bigIntField   BigInt
  floatField    Float
  decimalField  Decimal
  
  // Strings
  stringField   String
  textField     String  @db.Text  // For long text
  
  // Boolean
  boolField     Boolean
  
  // DateTime
  dateField     DateTime
  
  // JSON
  jsonField     Json
  
  // Bytes
  bytesField    Bytes
  
  // Optional fields
  optional      String?
  
  // Arrays (PostgreSQL only)
  tags          String[]
  numbers       Int[]
}
```

### Attributes

```prisma
model User {
  // Primary key
  id        Int      @id @default(autoincrement())
  
  // Unique constraint
  email     String   @unique
  
  // Default value
  isActive  Boolean  @default(true)
  createdAt DateTime @default(now())
  
  // Auto-update timestamp
  updatedAt DateTime @updatedAt
  
  // Database-specific type
  bio       String   @db.VarChar(500)
  
  // Multiple attributes
  username  String   @unique @db.VarChar(50)
  
  // Composite unique constraint
  @@unique([email, username])
  
  // Index
  @@index([email])
  
  // Composite index
  @@index([email, username])
}
```

### Relations

```prisma
// One-to-Many
model User {
  id    Int    @id @default(autoincrement())
  posts Post[]
}

model Post {
  id       Int  @id @default(autoincrement())
  author   User @relation(fields: [authorId], references: [id])
  authorId Int
}

// One-to-One
model User {
  id      Int      @id @default(autoincrement())
  profile Profile?
}

model Profile {
  id     Int  @id @default(autoincrement())
  user   User @relation(fields: [userId], references: [id])
  userId Int  @unique
}

// Many-to-Many
model Post {
  id         Int        @id @default(autoincrement())
  categories Category[]
}

model Category {
  id    Int    @id @default(autoincrement())
  posts Post[]
}

// Explicit Many-to-Many
model Post {
  id                Int                @id @default(autoincrement())
  postCategories    PostCategory[]
}

model Category {
  id                Int                @id @default(autoincrement())
  postCategories    PostCategory[]
}

model PostCategory {
  post       Post     @relation(fields: [postId], references: [id])
  postId     Int
  category   Category @relation(fields: [categoryId], references: [id])
  categoryId Int
  assignedAt DateTime @default(now())
  
  @@id([postId, categoryId])
}
```

## Prisma Client

### Initialization

```typescript
// lib/prisma.ts
import { PrismaClient } from '@prisma/client';

const prisma = new PrismaClient({
  log: ['query', 'info', 'warn', 'error'],
});

export default prisma;
```

### With Express

```typescript
// app.ts
import express from 'express';
import prisma from './lib/prisma';

const app = express();
app.use(express.json());

// Use prisma in routes
app.get('/users', async (req, res) => {
  const users = await prisma.user.findMany();
  res.json(users);
});

// Graceful shutdown
process.on('beforeExit', async () => {
  await prisma.$disconnect();
});

app.listen(3000);
```

### Connection Pooling

```typescript
const prisma = new PrismaClient({
  datasources: {
    db: {
      url: process.env.DATABASE_URL,
    },
  },
  log: ['query'],
});

// Connection pool is managed automatically
```

## CRUD Operations

### Create

```typescript
// Create single record
const user = await prisma.user.create({
  data: {
    email: 'alice@example.com',
    name: 'Alice',
    age: 25
  }
});

// Create with relations
const user = await prisma.user.create({
  data: {
    email: 'bob@example.com',
    name: 'Bob',
    posts: {
      create: [
        { title: 'First Post', content: 'Hello World' },
        { title: 'Second Post', content: 'Prisma is awesome' }
      ]
    }
  },
  include: {
    posts: true
  }
});

// Create many
const users = await prisma.user.createMany({
  data: [
    { email: 'user1@example.com', name: 'User 1' },
    { email: 'user2@example.com', name: 'User 2' },
    { email: 'user3@example.com', name: 'User 3' }
  ],
  skipDuplicates: true // Skip records that violate unique constraints
});
```

### Read

```typescript
// Find unique
const user = await prisma.user.findUnique({
  where: { email: 'alice@example.com' }
});

// Find first
const user = await prisma.user.findFirst({
  where: { age: { gte: 18 } }
});

// Find many
const users = await prisma.user.findMany({
  where: {
    age: { gte: 18 },
    isActive: true
  },
  orderBy: { createdAt: 'desc' },
  take: 10,
  skip: 0
});

// Find with relations
const user = await prisma.user.findUnique({
  where: { id: 1 },
  include: {
    posts: true,
    profile: true
  }
});

// Select specific fields
const users = await prisma.user.findMany({
  select: {
    id: true,
    email: true,
    name: true
  }
});

// Count
const count = await prisma.user.count({
  where: { isActive: true }
});
```

### Update

```typescript
// Update single
const user = await prisma.user.update({
  where: { id: 1 },
  data: {
    name: 'Alice Updated',
    age: 26
  }
});

// Update many
const result = await prisma.user.updateMany({
  where: { isActive: false },
  data: { isActive: true }
});

// Upsert (update or create)
const user = await prisma.user.upsert({
  where: { email: 'alice@example.com' },
  update: { name: 'Alice Updated' },
  create: {
    email: 'alice@example.com',
    name: 'Alice'
  }
});

// Increment/Decrement
const post = await prisma.post.update({
  where: { id: 1 },
  data: {
    views: { increment: 1 }
  }
});
```

### Delete

```typescript
// Delete single
const user = await prisma.user.delete({
  where: { id: 1 }
});

// Delete many
const result = await prisma.user.deleteMany({
  where: {
    createdAt: {
      lt: new Date('2023-01-01')
    }
  }
});

// Delete all
const result = await prisma.user.deleteMany({});
```

## Relations

### Nested Writes

```typescript
// Create with nested relations
const user = await prisma.user.create({
  data: {
    email: 'alice@example.com',
    name: 'Alice',
    posts: {
      create: [
        { title: 'Post 1', content: 'Content 1' },
        { title: 'Post 2', content: 'Content 2' }
      ]
    },
    profile: {
      create: {
        bio: 'Software developer',
        avatar: 'avatar.jpg'
      }
    }
  }
});

// Update with nested relations
const user = await prisma.user.update({
  where: { id: 1 },
  data: {
    posts: {
      create: { title: 'New Post', content: 'Content' },
      update: {
        where: { id: 1 },
        data: { title: 'Updated Title' }
      },
      delete: { id: 2 }
    }
  }
});

// Connect existing relations
const post = await prisma.post.create({
  data: {
    title: 'New Post',
    content: 'Content',
    author: {
      connect: { id: 1 }
    }
  }
});

// Disconnect relations
const user = await prisma.user.update({
  where: { id: 1 },
  data: {
    profile: {
      disconnect: true
    }
  }
});
```

### Querying Relations

```typescript
// Include relations
const users = await prisma.user.findMany({
  include: {
    posts: true,
    profile: true
  }
});

// Nested include
const users = await prisma.user.findMany({
  include: {
    posts: {
      include: {
        comments: true
      }
    }
  }
});

// Filter relations
const users = await prisma.user.findMany({
  include: {
    posts: {
      where: { published: true },
      orderBy: { createdAt: 'desc' },
      take: 5
    }
  }
});

// Select specific relation fields
const users = await prisma.user.findMany({
  select: {
    id: true,
    name: true,
    posts: {
      select: {
        id: true,
        title: true
      }
    }
  }
});
```

## Advanced Queries

### Filtering

```typescript
// Comparison operators
const users = await prisma.user.findMany({
  where: {
    age: { gt: 18 },        // greater than
    age: { gte: 18 },       // greater than or equal
    age: { lt: 65 },        // less than
    age: { lte: 65 },       // less than or equal
    age: { not: 25 },       // not equal
    age: { in: [18, 25, 30] }, // in array
    age: { notIn: [18, 25] }   // not in array
  }
});

// String filters
const users = await prisma.user.findMany({
  where: {
    email: { contains: '@example.com' },
    name: { startsWith: 'A' },
    name: { endsWith: 'son' },
    email: { mode: 'insensitive' } // case-insensitive
  }
});

// Logical operators
const users = await prisma.user.findMany({
  where: {
    AND: [
      { age: { gte: 18 } },
      { isActive: true }
    ]
  }
});

const users = await prisma.user.findMany({
  where: {
    OR: [
      { email: { contains: '@example.com' } },
      { email: { contains: '@test.com' } }
    ]
  }
});

const users = await prisma.user.findMany({
  where: {
    NOT: {
      email: { contains: '@spam.com' }
    }
  }
});

// Relation filters
const users = await prisma.user.findMany({
  where: {
    posts: {
      some: {
        published: true
      }
    }
  }
});

const users = await prisma.user.findMany({
  where: {
    posts: {
      every: {
        published: true
      }
    }
  }
});

const users = await prisma.user.findMany({
  where: {
    posts: {
      none: {
        published: false
      }
    }
  }
});
```

### Pagination

```typescript
// Offset pagination
const page = 2;
const pageSize = 10;

const users = await prisma.user.findMany({
  skip: (page - 1) * pageSize,
  take: pageSize,
  orderBy: { createdAt: 'desc' }
});

// Cursor-based pagination
const users = await prisma.user.findMany({
  take: 10,
  cursor: {
    id: lastUserId
  },
  skip: 1, // Skip the cursor
  orderBy: { id: 'asc' }
});
```

### Aggregation

```typescript
// Count
const count = await prisma.user.count();

const count = await prisma.user.count({
  where: { isActive: true }
});

// Aggregate
const result = await prisma.post.aggregate({
  _count: true,
  _avg: { views: true },
  _sum: { views: true },
  _min: { views: true },
  _max: { views: true }
});

// Group by
const result = await prisma.post.groupBy({
  by: ['authorId'],
  _count: true,
  _avg: { views: true },
  having: {
    views: {
      _avg: { gt: 100 }
    }
  }
});
```

### Transactions

```typescript
// Sequential operations
const [user, post] = await prisma.$transaction([
  prisma.user.create({ data: { email: 'alice@example.com', name: 'Alice' } }),
  prisma.post.create({ data: { title: 'Post', content: 'Content', authorId: 1 } })
]);

// Interactive transactions
const result = await prisma.$transaction(async (tx) => {
  const user = await tx.user.create({
    data: { email: 'alice@example.com', name: 'Alice' }
  });
  
  const post = await tx.post.create({
    data: {
      title: 'Post',
      content: 'Content',
      authorId: user.id
    }
  });
  
  return { user, post };
});

// With timeout and isolation level
const result = await prisma.$transaction(
  async (tx) => {
    // Operations
  },
  {
    maxWait: 5000,
    timeout: 10000,
    isolationLevel: 'Serializable'
  }
);
```

### Raw Queries

```typescript
// Raw query
const users = await prisma.$queryRaw`
  SELECT * FROM "User" WHERE age > ${18}
`;

// Raw execute
const result = await prisma.$executeRaw`
  UPDATE "User" SET isActive = true WHERE age > ${18}
`;

// Unsafe (use with caution)
const users = await prisma.$queryRawUnsafe(
  'SELECT * FROM "User" WHERE email = $1',
  'alice@example.com'
);
```

## Migrations

### Development Workflow

```bash
# 1. Update schema.prisma
# 2. Create migration
npx prisma migrate dev --name add_user_profile

# 3. Prisma automatically:
#    - Creates migration files
#    - Applies migration to database
#    - Generates Prisma Client
```

### Migration Files

```sql
-- CreateTable
CREATE TABLE "User" (
    "id" SERIAL NOT NULL,
    "email" TEXT NOT NULL,
    "name" TEXT,
    "createdAt" TIMESTAMP(3) NOT NULL DEFAULT CURRENT_TIMESTAMP,

    CONSTRAINT "User_pkey" PRIMARY KEY ("id")
);

-- CreateIndex
CREATE UNIQUE INDEX "User_email_key" ON "User"("email");
```

### Production Deployment

```bash
# Deploy migrations
npx prisma migrate deploy

# Check migration status
npx prisma migrate status

# Reset database (development only!)
npx prisma migrate reset
```

### Seeding

**prisma/seed.ts:**
```typescript
import { PrismaClient } from '@prisma/client';

const prisma = new PrismaClient();

async function main() {
  const alice = await prisma.user.upsert({
    where: { email: 'alice@example.com' },
    update: {},
    create: {
      email: 'alice@example.com',
      name: 'Alice',
      posts: {
        create: [
          { title: 'First Post', content: 'Hello World', published: true },
          { title: 'Second Post', content: 'Prisma is great', published: true }
        ]
      }
    }
  });

  console.log({ alice });
}

main()
  .catch((e) => {
    console.error(e);
    process.exit(1);
  })
  .finally(async () => {
    await prisma.$disconnect();
  });
```

**package.json:**
```json
{
  "prisma": {
    "seed": "ts-node prisma/seed.ts"
  }
}
```

```bash
# Run seed
npx prisma db seed
```

## Best Practices

### 1. Single Prisma Instance

```typescript
// lib/prisma.ts
import { PrismaClient } from '@prisma/client';

const globalForPrisma = global as unknown as { prisma: PrismaClient };

export const prisma =
  globalForPrisma.prisma ||
  new PrismaClient({
    log: process.env.NODE_ENV === 'development' ? ['query', 'error', 'warn'] : ['error'],
  });

if (process.env.NODE_ENV !== 'production') globalForPrisma.prisma = prisma;

export default prisma;
```

### 2. Error Handling

```typescript
import { Prisma } from '@prisma/client';

try {
  const user = await prisma.user.create({
    data: { email: 'alice@example.com', name: 'Alice' }
  });
} catch (error) {
  if (error instanceof Prisma.PrismaClientKnownRequestError) {
    if (error.code === 'P2002') {
      console.log('Unique constraint violation');
    }
  }
  throw error;
}
```

### 3. Middleware

```typescript
prisma.$use(async (params, next) => {
  const before = Date.now();
  const result = await next(params);
  const after = Date.now();
  
  console.log(`Query ${params.model}.${params.action} took ${after - before}ms`);
  
  return result;
});

// Soft delete middleware
prisma.$use(async (params, next) => {
  if (params.model === 'User') {
    if (params.action === 'delete') {
      params.action = 'update';
      params.args['data'] = { deletedAt: new Date() };
    }
    if (params.action === 'findMany' || params.action === 'findFirst') {
      params.args.where = { ...params.args.where, deletedAt: null };
    }
  }
  return next(params);
});
```

### 4. Repository Pattern

```typescript
// repositories/userRepository.ts
import prisma from '../lib/prisma';

export class UserRepository {
  async findById(id: number) {
    return prisma.user.findUnique({ where: { id } });
  }

  async findByEmail(email: string) {
    return prisma.user.findUnique({ where: { email } });
  }

  async create(data: { email: string; name: string }) {
    return prisma.user.create({ data });
  }

  async update(id: number, data: Partial<{ email: string; name: string }>) {
    return prisma.user.update({ where: { id }, data });
  }

  async delete(id: number) {
    return prisma.user.delete({ where: { id } });
  }
}

export const userRepository = new UserRepository();
```

### 5. Testing

```typescript
// tests/user.test.ts
import { PrismaClient } from '@prisma/client';

const prisma = new PrismaClient();

beforeAll(async () => {
  await prisma.$connect();
});

afterAll(async () => {
  await prisma.$disconnect();
});

beforeEach(async () => {
  await prisma.user.deleteMany();
});

test('create user', async () => {
  const user = await prisma.user.create({
    data: { email: 'test@example.com', name: 'Test' }
  });
  
  expect(user.email).toBe('test@example.com');
});
```

### 6. Environment-Specific Configuration

```typescript
// config/database.ts
export const getDatabaseConfig = () => {
  if (process.env.NODE_ENV === 'production') {
    return {
      log: ['error'],
      errorFormat: 'minimal',
    };
  }
  
  return {
    log: ['query', 'info', 'warn', 'error'],
    errorFormat: 'pretty',
  };
};

// lib/prisma.ts
import { PrismaClient } from '@prisma/client';
import { getDatabaseConfig } from '../config/database';

const prisma = new PrismaClient(getDatabaseConfig());
```

## Summary

Prisma is the modern choice for database access in Node.js:

✅ **Type-Safe** - Full TypeScript support with auto-completion  
✅ **Developer Experience** - Intuitive API and excellent tooling  
✅ **Migrations** - Built-in migration system  
✅ **Database Agnostic** - Works with multiple databases  
✅ **Performance** - Optimized queries and connection pooling  
✅ **Prisma Studio** - Visual database browser  

Prisma significantly improves productivity and reduces bugs through its type-safe approach and excellent developer experience.
