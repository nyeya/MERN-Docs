# Mongoose - MongoDB Object Modeling

## Table of Contents
- [What is Mongoose?](#what-is-mongoose)
- [Why Use Mongoose?](#why-use-mongoose)
- [Mongoose vs Alternatives](#mongoose-vs-alternatives)
- [Installation & Setup](#installation--setup)
- [Schemas & Models](#schemas--models)
- [CRUD Operations](#crud-operations)
- [Query Building](#query-building)
- [Validation](#validation)
- [Middleware (Hooks)](#middleware-hooks)
- [Population (Relations)](#population-relations)
- [Best Practices](#best-practices)

## What is Mongoose?

Mongoose is an Object Data Modeling (ODM) library for MongoDB and Node.js. It provides a schema-based solution to model application data, including built-in type casting, validation, query building, and business logic hooks.

### Key Features
- **Schema Definition**: Structure for MongoDB documents
- **Type Casting**: Automatic data type conversion
- **Validation**: Built-in and custom validators
- **Middleware**: Pre/post hooks for operations
- **Query Building**: Chainable query API
- **Population**: Reference documents (like SQL joins)
- **Virtuals**: Computed properties
- **Plugins**: Reusable schema functionality

## Why Use Mongoose?

### 1. **Structure for MongoDB**

**Without Mongoose (Native MongoDB Driver):**
```javascript
const { MongoClient } = require('mongodb');

const client = new MongoClient(url);
await client.connect();
const db = client.db('mydb');
const users = db.collection('users');

// No schema - any structure allowed
await users.insertOne({
  name: 'Alice',
  age: '25', // String instead of number - no validation!
  emial: 'alice@example.com' // Typo - no error!
});

// Manual validation
const user = await users.findOne({ _id: userId });
if (!user || typeof user.age !== 'number') {
  throw new Error('Invalid user');
}
```

**With Mongoose:**
```javascript
const userSchema = new mongoose.Schema({
  name: { type: String, required: true },
  age: { type: Number, required: true },
  email: { type: String, required: true }
});

const User = mongoose.model('User', userSchema);

// Automatic validation
const user = new User({
  name: 'Alice',
  age: '25', // Automatically converted to number
  emial: 'alice@example.com' // Error: field not in schema
});

await user.save(); // Validates before saving
```

### 2. **Built-in Validation**

```javascript
const userSchema = new mongoose.Schema({
  email: {
    type: String,
    required: [true, 'Email is required'],
    unique: true,
    lowercase: true,
    trim: true,
    match: [/^\S+@\S+\.\S+$/, 'Invalid email format']
  },
  age: {
    type: Number,
    min: [18, 'Must be at least 18'],
    max: [120, 'Must be at most 120']
  },
  role: {
    type: String,
    enum: ['user', 'admin', 'moderator'],
    default: 'user'
  }
});
```

### 3. **Middleware (Hooks)**

```javascript
// Hash password before saving
userSchema.pre('save', async function(next) {
  if (this.isModified('password')) {
    this.password = await bcrypt.hash(this.password, 10);
  }
  next();
});

// Update timestamp
userSchema.pre('save', function(next) {
  this.updatedAt = new Date();
  next();
});
```

### 4. **Population (Relations)**

```javascript
// Define relationships
const postSchema = new mongoose.Schema({
  title: String,
  author: { type: mongoose.Schema.Types.ObjectId, ref: 'User' }
});

// Query with population
const post = await Post.findById(postId).populate('author');
console.log(post.author.name); // Populated user data
```

## Mongoose vs Alternatives

### Mongoose vs Native MongoDB Driver

| Feature | Mongoose | Native Driver |
|---------|----------|---------------|
| **Schema** | Required | Optional |
| **Validation** | Built-in | Manual |
| **Type Casting** | Automatic | Manual |
| **Middleware** | Built-in | Manual |
| **Query Builder** | Rich API | Basic |
| **Learning Curve** | Moderate | Easy |
| **Performance** | Slight overhead | Faster |
| **Bundle Size** | Larger | Smaller |

**When to choose Mongoose:**
- Need schema validation
- Want middleware hooks
- Prefer structured data
- Building complex applications

**When to choose Native Driver:**
- Maximum performance needed
- Flexible schema required
- Minimal dependencies
- Simple use cases

### Mongoose vs Prisma (MongoDB)

| Feature | Mongoose | Prisma |
|---------|----------|--------|
| **Approach** | Schema in code | Schema file |
| **Type Safety** | Via TypeScript | Native |
| **Middleware** | Extensive | Limited |
| **Maturity** | Very mature | Newer |
| **MongoDB Features** | Full support | Growing |
| **Aggregation** | Native | Limited |

**When to choose Mongoose:**
- MongoDB-specific features needed
- Want extensive middleware
- Established MongoDB project
- Need aggregation pipelines

**When to choose Prisma:**
- TypeScript-first project
- Want auto-generated types
- Multi-database support
- Prefer declarative schema

### Mongoose vs TypeORM (MongoDB)

| Feature | Mongoose | TypeORM |
|---------|----------|---------|
| **MongoDB Focus** | Dedicated | Multi-DB |
| **API Style** | MongoDB-like | SQL-like |
| **Decorators** | No | Yes |
| **Aggregation** | Native | Limited |

## Installation & Setup

### Installation

```bash
npm install mongoose
npm install --save-dev @types/mongoose  # TypeScript
```

### Basic Connection

```javascript
const mongoose = require('mongoose');

// Connect to MongoDB
mongoose.connect('mongodb://localhost:27017/mydb', {
  useNewUrlParser: true,
  useUnifiedTopology: true
});

// Connection events
mongoose.connection.on('connected', () => {
  console.log('MongoDB connected');
});

mongoose.connection.on('error', (err) => {
  console.error('MongoDB connection error:', err);
});

mongoose.connection.on('disconnected', () => {
  console.log('MongoDB disconnected');
});

// Graceful shutdown
process.on('SIGINT', async () => {
  await mongoose.connection.close();
  process.exit(0);
});
```

### Connection with Options

```javascript
const options = {
  useNewUrlParser: true,
  useUnifiedTopology: true,
  serverSelectionTimeoutMS: 5000,
  socketTimeoutMS: 45000,
  family: 4, // Use IPv4
  maxPoolSize: 10,
  minPoolSize: 5
};

mongoose.connect(process.env.MONGODB_URI, options);
```

### TypeScript Setup

```typescript
import mongoose from 'mongoose';

interface IUser {
  name: string;
  email: string;
  age: number;
}

const userSchema = new mongoose.Schema<IUser>({
  name: { type: String, required: true },
  email: { type: String, required: true },
  age: { type: Number, required: true }
});

const User = mongoose.model<IUser>('User', userSchema);
```

## Schemas & Models

### Basic Schema

```javascript
const mongoose = require('mongoose');
const { Schema } = mongoose;

const userSchema = new Schema({
  name: String,
  email: String,
  age: Number,
  isActive: Boolean,
  createdAt: Date
});

const User = mongoose.model('User', userSchema);
```

### Schema with Types

```javascript
const userSchema = new Schema({
  // String
  name: { type: String, required: true },
  
  // Number
  age: { type: Number, min: 0, max: 120 },
  
  // Boolean
  isActive: { type: Boolean, default: true },
  
  // Date
  createdAt: { type: Date, default: Date.now },
  
  // ObjectId
  userId: { type: Schema.Types.ObjectId },
  
  // Array
  tags: [String],
  scores: [Number],
  
  // Nested Object
  address: {
    street: String,
    city: String,
    country: String
  },
  
  // Mixed (any type)
  metadata: Schema.Types.Mixed,
  
  // Buffer
  avatar: Buffer,
  
  // Map
  socialLinks: {
    type: Map,
    of: String
  }
});
```

### Schema Options

```javascript
const userSchema = new Schema({
  name: String,
  email: String
}, {
  timestamps: true, // Adds createdAt and updatedAt
  collection: 'users', // Custom collection name
  versionKey: false, // Disable __v field
  toJSON: { virtuals: true }, // Include virtuals in JSON
  toObject: { virtuals: true }
});
```

### Virtuals

```javascript
const userSchema = new Schema({
  firstName: String,
  lastName: String
});

// Virtual property (not stored in DB)
userSchema.virtual('fullName').get(function() {
  return `${this.firstName} ${this.lastName}`;
});

userSchema.virtual('fullName').set(function(name) {
  const parts = name.split(' ');
  this.firstName = parts[0];
  this.lastName = parts[1];
});

const user = new User({ firstName: 'John', lastName: 'Doe' });
console.log(user.fullName); // 'John Doe'

user.fullName = 'Jane Smith';
console.log(user.firstName); // 'Jane'
```

### Instance Methods

```javascript
userSchema.methods.comparePassword = async function(candidatePassword) {
  return await bcrypt.compare(candidatePassword, this.password);
};

userSchema.methods.generateAuthToken = function() {
  return jwt.sign({ userId: this._id }, process.env.JWT_SECRET);
};

// Usage
const user = await User.findOne({ email });
const isValid = await user.comparePassword(password);
const token = user.generateAuthToken();
```

### Static Methods

```javascript
userSchema.statics.findByEmail = function(email) {
  return this.findOne({ email: email.toLowerCase() });
};

userSchema.statics.findActive = function() {
  return this.find({ isActive: true });
};

// Usage
const user = await User.findByEmail('alice@example.com');
const activeUsers = await User.findActive();
```

## CRUD Operations

### Create

```javascript
// Method 1: new + save
const user = new User({
  name: 'Alice',
  email: 'alice@example.com',
  age: 25
});
await user.save();

// Method 2: create
const user = await User.create({
  name: 'Alice',
  email: 'alice@example.com',
  age: 25
});

// Method 3: insertMany
const users = await User.insertMany([
  { name: 'Alice', email: 'alice@example.com' },
  { name: 'Bob', email: 'bob@example.com' }
]);
```

### Read

```javascript
// Find all
const users = await User.find();

// Find with conditions
const users = await User.find({ age: { $gte: 18 } });

// Find one
const user = await User.findOne({ email: 'alice@example.com' });

// Find by ID
const user = await User.findById(userId);

// Find with select
const users = await User.find().select('name email -_id');

// Find with limit and skip
const users = await User.find()
  .limit(10)
  .skip(20)
  .sort({ createdAt: -1 });

// Count
const count = await User.countDocuments({ isActive: true });

// Exists
const exists = await User.exists({ email: 'alice@example.com' });
```

### Update

```javascript
// Update one
const user = await User.findByIdAndUpdate(
  userId,
  { name: 'Alice Updated', age: 26 },
  { new: true, runValidators: true }
);

// Update many
const result = await User.updateMany(
  { isActive: false },
  { isActive: true }
);

// Find and update
const user = await User.findOneAndUpdate(
  { email: 'alice@example.com' },
  { age: 26 },
  { new: true }
);

// Update with operators
await User.updateOne(
  { _id: userId },
  {
    $set: { name: 'Alice' },
    $inc: { loginCount: 1 },
    $push: { tags: 'new-tag' }
  }
);
```

### Delete

```javascript
// Delete one
await User.findByIdAndDelete(userId);

// Delete many
await User.deleteMany({ isActive: false });

// Find and delete
const user = await User.findOneAndDelete({ email: 'alice@example.com' });
```

## Query Building

### Chainable Queries

```javascript
const users = await User
  .find({ age: { $gte: 18 } })
  .where('isActive').equals(true)
  .select('name email')
  .sort({ createdAt: -1 })
  .limit(10)
  .skip(0)
  .exec();
```

### Query Operators

```javascript
// Comparison
User.find({ age: { $gt: 18 } });      // Greater than
User.find({ age: { $gte: 18 } });     // Greater than or equal
User.find({ age: { $lt: 65 } });      // Less than
User.find({ age: { $lte: 65 } });     // Less than or equal
User.find({ age: { $ne: 25 } });      // Not equal
User.find({ age: { $in: [18, 25, 30] } }); // In array
User.find({ age: { $nin: [18, 25] } }); // Not in array

// Logical
User.find({
  $and: [
    { age: { $gte: 18 } },
    { isActive: true }
  ]
});

User.find({
  $or: [
    { email: 'alice@example.com' },
    { email: 'bob@example.com' }
  ]
});

User.find({
  $nor: [
    { age: { $lt: 18 } },
    { isActive: false }
  ]
});

User.find({
  age: { $not: { $lt: 18 } }
});

// String
User.find({ name: /^Alice/ });        // Regex
User.find({ email: { $regex: '@example.com', $options: 'i' } });

// Array
User.find({ tags: 'javascript' });    // Contains
User.find({ tags: { $all: ['javascript', 'nodejs'] } }); // Contains all
User.find({ tags: { $size: 3 } });    // Array size

// Exists
User.find({ deletedAt: { $exists: false } });
```

### Aggregation

```javascript
const results = await User.aggregate([
  // Match
  { $match: { isActive: true } },
  
  // Group
  {
    $group: {
      _id: '$role',
      count: { $sum: 1 },
      avgAge: { $avg: '$age' }
    }
  },
  
  // Sort
  { $sort: { count: -1 } },
  
  // Limit
  { $limit: 10 },
  
  // Project
  {
    $project: {
      role: '$_id',
      count: 1,
      avgAge: 1,
      _id: 0
    }
  }
]);
```

## Validation

### Built-in Validators

```javascript
const userSchema = new Schema({
  // Required
  name: { type: String, required: true },
  email: { type: String, required: [true, 'Email is required'] },
  
  // String validators
  username: {
    type: String,
    minlength: [3, 'Username must be at least 3 characters'],
    maxlength: [20, 'Username must be at most 20 characters'],
    lowercase: true,
    trim: true,
    match: [/^[a-z0-9_]+$/, 'Username can only contain lowercase letters, numbers, and underscores']
  },
  
  // Number validators
  age: {
    type: Number,
    min: [18, 'Must be at least 18'],
    max: [120, 'Must be at most 120']
  },
  
  // Enum
  role: {
    type: String,
    enum: {
      values: ['user', 'admin', 'moderator'],
      message: '{VALUE} is not a valid role'
    }
  },
  
  // Unique (creates index)
  email: { type: String, unique: true }
});
```

### Custom Validators

```javascript
const userSchema = new Schema({
  email: {
    type: String,
    validate: {
      validator: function(v) {
        return /^\S+@\S+\.\S+$/.test(v);
      },
      message: props => `${props.value} is not a valid email`
    }
  },
  
  password: {
    type: String,
    validate: {
      validator: function(v) {
        return v.length >= 8 && /[A-Z]/.test(v) && /[0-9]/.test(v);
      },
      message: 'Password must be at least 8 characters with uppercase and number'
    }
  },
  
  // Async validator
  username: {
    type: String,
    validate: {
      validator: async function(v) {
        const count = await mongoose.model('User').countDocuments({ username: v });
        return count === 0;
      },
      message: 'Username already taken'
    }
  }
});
```

## Middleware (Hooks)

### Pre Hooks

```javascript
// Before save
userSchema.pre('save', async function(next) {
  // Hash password if modified
  if (this.isModified('password')) {
    this.password = await bcrypt.hash(this.password, 10);
  }
  next();
});

// Before validate
userSchema.pre('validate', function(next) {
  if (this.email) {
    this.email = this.email.toLowerCase();
  }
  next();
});

// Before remove
userSchema.pre('remove', async function(next) {
  // Delete user's posts
  await Post.deleteMany({ author: this._id });
  next();
});

// Before find
userSchema.pre('find', function(next) {
  // Exclude deleted users
  this.where({ deletedAt: null });
  next();
});
```

### Post Hooks

```javascript
// After save
userSchema.post('save', function(doc, next) {
  console.log(`User ${doc.name} was saved`);
  next();
});

// After remove
userSchema.post('remove', function(doc, next) {
  console.log(`User ${doc.name} was removed`);
  next();
});

// Error handling
userSchema.post('save', function(error, doc, next) {
  if (error.name === 'MongoServerError' && error.code === 11000) {
    next(new Error('Email already exists'));
  } else {
    next(error);
  }
});
```

## Population (Relations)

### Basic Population

```javascript
const postSchema = new Schema({
  title: String,
  content: String,
  author: { type: Schema.Types.ObjectId, ref: 'User' }
});

// Populate author
const post = await Post.findById(postId).populate('author');
console.log(post.author.name); // User data

// Populate specific fields
const post = await Post.findById(postId)
  .populate('author', 'name email');

// Populate multiple fields
const post = await Post.findById(postId)
  .populate('author')
  .populate('comments');
```

### Nested Population

```javascript
const post = await Post.findById(postId)
  .populate({
    path: 'comments',
    populate: {
      path: 'author',
      select: 'name'
    }
  });
```

### Conditional Population

```javascript
const post = await Post.findById(postId)
  .populate({
    path: 'author',
    match: { isActive: true },
    select: 'name email'
  });
```

### Virtual Population

```javascript
// User has many posts
userSchema.virtual('posts', {
  ref: 'Post',
  localField: '_id',
  foreignField: 'author'
});

const user = await User.findById(userId).populate('posts');
console.log(user.posts); // Array of posts
```

## Best Practices

### 1. Use Lean Queries

```javascript
// Returns plain JavaScript objects (faster)
const users = await User.find().lean();

// Use when you don't need Mongoose document methods
```

### 2. Index Important Fields

```javascript
const userSchema = new Schema({
  email: { type: String, index: true, unique: true },
  createdAt: { type: Date, index: true }
});

// Compound index
userSchema.index({ email: 1, role: 1 });
```

### 3. Use Select to Limit Fields

```javascript
// Only get needed fields
const users = await User.find().select('name email');

// Exclude fields
const users = await User.find().select('-password -__v');
```

### 4. Handle Errors Properly

```javascript
try {
  const user = await User.create(userData);
} catch (error) {
  if (error.name === 'ValidationError') {
    // Handle validation errors
    const errors = Object.values(error.errors).map(e => e.message);
    return res.status(400).json({ errors });
  }
  if (error.code === 11000) {
    // Handle duplicate key error
    return res.status(400).json({ error: 'Email already exists' });
  }
  throw error;
}
```

### 5. Use Transactions

```javascript
const session = await mongoose.startSession();
session.startTransaction();

try {
  const user = await User.create([userData], { session });
  const profile = await Profile.create([{ user: user[0]._id }], { session });
  
  await session.commitTransaction();
} catch (error) {
  await session.abortTransaction();
  throw error;
} finally {
  session.endSession();
}
```

### 6. Separate Schema Files

```javascript
// models/User.js
const mongoose = require('mongoose');

const userSchema = new mongoose.Schema({
  name: String,
  email: String
});

module.exports = mongoose.model('User', userSchema);

// Usage
const User = require('./models/User');
```

## Summary

Mongoose is the standard ODM for MongoDB in Node.js:

✅ **Schema Validation** - Structure and validate data  
✅ **Middleware** - Pre/post hooks for business logic  
✅ **Type Casting** - Automatic data conversion  
✅ **Query Builder** - Rich, chainable API  
✅ **Population** - Easy document references  
✅ **Mature** - Battle-tested and widely used  

**Key Takeaways:**
- Define schemas for data structure
- Use validation to ensure data integrity
- Leverage middleware for business logic
- Index frequently queried fields
- Use lean() for read-only operations
- Handle errors appropriately
- Use transactions for multi-document operations

Mongoose provides the structure and features needed for production MongoDB applications.
