# Bcrypt - Password Hashing

## Table of Contents
- [What is Bcrypt?](#what-is-bcrypt)
- [Why Use Bcrypt?](#why-use-bcrypt)
- [Bcrypt vs Alternatives](#bcrypt-vs-alternatives)
- [Installation & Setup](#installation--setup)
- [Hashing Passwords](#hashing-passwords)
- [Comparing Passwords](#comparing-passwords)
- [Salt Rounds](#salt-rounds)
- [Best Practices](#best-practices)
- [Common Patterns](#common-patterns)

## What is Bcrypt?

Bcrypt is a password-hashing function designed for securely storing passwords. It incorporates a salt to protect against rainbow table attacks and is intentionally slow to make brute-force attacks impractical.

### Key Features
- **Salted Hashing**: Automatic salt generation
- **Adaptive**: Configurable work factor (cost)
- **Slow by Design**: Resistant to brute-force attacks
- **Industry Standard**: Widely trusted and used
- **Cross-Platform**: Works on all major platforms

## Why Use Bcrypt?

### 1. **Never Store Plain Passwords**

**BAD - Plain Text:**
```javascript
// NEVER DO THIS!
const user = {
  email: 'user@example.com',
  password: 'mypassword123' // Stored as plain text
};

// If database is compromised, all passwords are exposed
```

**GOOD - Hashed with Bcrypt:**
```javascript
const bcrypt = require('bcrypt');

const hashedPassword = await bcrypt.hash('mypassword123', 10);
const user = {
  email: 'user@example.com',
  password: hashedPassword // Stored as hash
};

// Even if database is compromised, passwords are protected
```

### 2. **Protection Against Rainbow Tables**

```javascript
// Same password, different hashes (due to salt)
const hash1 = await bcrypt.hash('password123', 10);
// $2b$10$N9qo8uLOickgx2ZMRZoMyeIjZAgcfl7p92ldGxad68LJZdL17lhWy

const hash2 = await bcrypt.hash('password123', 10);
// $2b$10$bvIG6Nmid91Mu9RcmmWZfO5HJIMCT8riNW0hEp8f6/FuA2/mHZFpe

// Different hashes for same password = rainbow tables useless
```

### 3. **Slow by Design**

```javascript
// Bcrypt is intentionally slow
const start = Date.now();
await bcrypt.hash('password', 10);
const end = Date.now();
console.log(`Took ${end - start}ms`); // ~100ms

// This slowness protects against brute-force attacks
// Attacker would need years to try billions of passwords
```

## Bcrypt vs Alternatives

### Bcrypt vs Plain MD5/SHA

| Feature | Bcrypt | MD5/SHA |
|---------|--------|---------|
| **Salt** | Automatic | Manual |
| **Speed** | Slow (good) | Fast (bad) |
| **Brute-Force Resistant** | Yes | No |
| **Rainbow Table Resistant** | Yes | No (without salt) |
| **Recommended** | Yes | No for passwords |

**Why MD5/SHA are bad for passwords:**
```javascript
// MD5 is fast = easy to brute force
const crypto = require('crypto');
const hash = crypto.createHash('md5').update('password').digest('hex');
// Attacker can try billions of passwords per second

// Bcrypt is slow = hard to brute force
const hash = await bcrypt.hash('password', 10);
// Attacker can only try thousands of passwords per second
```

### Bcrypt vs Argon2

| Feature | Bcrypt | Argon2 |
|---------|--------|--------|
| **Maturity** | Very mature (1999) | Newer (2015) |
| **Memory-Hard** | No | Yes |
| **Configurable** | Rounds | Time, Memory, Parallelism |
| **Winner** | - | Password Hashing Competition 2015 |
| **Adoption** | Widespread | Growing |

**When to choose Bcrypt:**
- Proven track record
- Simpler configuration
- Widespread support
- Good enough for most use cases

**When to choose Argon2:**
- Need memory-hard algorithm
- Maximum security required
- Modern applications
- Resistance to GPU attacks

### Bcrypt vs PBKDF2

| Feature | Bcrypt | PBKDF2 |
|---------|--------|--------|
| **Algorithm** | Blowfish-based | HMAC-based |
| **GPU Resistance** | Better | Worse |
| **Simplicity** | Simpler | More complex |
| **Standards** | Industry standard | NIST standard |

**When to choose Bcrypt:**
- Better GPU resistance
- Simpler implementation
- Industry preference

**When to choose PBKDF2:**
- NIST compliance required
- Already using HMAC

## Installation & Setup

### Installation

```bash
npm install bcrypt
```

### Basic Setup

```javascript
const bcrypt = require('bcrypt');

// Hash a password
const password = 'mypassword123';
const saltRounds = 10;
const hash = await bcrypt.hash(password, saltRounds);

// Compare password
const isMatch = await bcrypt.compare(password, hash);
console.log(isMatch); // true
```

### TypeScript Setup

```bash
npm install --save-dev @types/bcrypt
```

```typescript
import bcrypt from 'bcrypt';

const hash: string = await bcrypt.hash('password', 10);
const isMatch: boolean = await bcrypt.compare('password', hash);
```

## Hashing Passwords

### Async Hashing (Recommended)

```javascript
const bcrypt = require('bcrypt');

// Hash with auto-generated salt
const saltRounds = 10;
const hash = await bcrypt.hash('mypassword', saltRounds);

// Hash is ready to store
console.log(hash);
// $2b$10$N9qo8uLOickgx2ZMRZoMyeIjZAgcfl7p92ldGxad68LJZdL17lhWy
```

### Sync Hashing (Not Recommended)

```javascript
// Blocks event loop - avoid in production
const hash = bcrypt.hashSync('mypassword', 10);
```

### Manual Salt Generation

```javascript
// Generate salt
const salt = await bcrypt.genSalt(10);
console.log(salt); // $2b$10$N9qo8uLOickgx2ZMRZoMye

// Hash with salt
const hash = await bcrypt.hash('mypassword', salt);
```

### Hash Structure

```
$2b$10$N9qo8uLOickgx2ZMRZoMyeIjZAgcfl7p92ldGxad68LJZdL17lhWy
│  │  │                      │
│  │  │                      └─ Hash (31 chars)
│  │  └─ Salt (22 chars)
│  └─ Cost factor (10 = 2^10 iterations)
└─ Algorithm version (2b)
```

## Comparing Passwords

### Async Compare (Recommended)

```javascript
const password = 'mypassword';
const hash = '$2b$10$N9qo8uLOickgx2ZMRZoMyeIjZAgcfl7p92ldGxad68LJZdL17lhWy';

const isMatch = await bcrypt.compare(password, hash);

if (isMatch) {
  console.log('Password correct');
} else {
  console.log('Password incorrect');
}
```

### Sync Compare (Not Recommended)

```javascript
const isMatch = bcrypt.compareSync(password, hash);
```

### Login Example

```javascript
app.post('/login', async (req, res) => {
  const { email, password } = req.body;
  
  // Find user
  const user = await User.findOne({ email });
  if (!user) {
    return res.status(401).json({ error: 'Invalid credentials' });
  }
  
  // Compare password
  const isMatch = await bcrypt.compare(password, user.password);
  if (!isMatch) {
    return res.status(401).json({ error: 'Invalid credentials' });
  }
  
  // Generate token
  const token = generateToken(user);
  res.json({ token });
});
```

## Salt Rounds

### Understanding Salt Rounds

Salt rounds determine the computational cost:

```javascript
// Cost = 2^rounds iterations

// 10 rounds = 2^10 = 1,024 iterations (~100ms)
await bcrypt.hash('password', 10);

// 12 rounds = 2^12 = 4,096 iterations (~400ms)
await bcrypt.hash('password', 12);

// 14 rounds = 2^14 = 16,384 iterations (~1600ms)
await bcrypt.hash('password', 14);
```

### Choosing Salt Rounds

```javascript
// Development: 10 rounds (fast)
const saltRounds = process.env.NODE_ENV === 'production' ? 12 : 10;

// Production: 12 rounds (recommended)
// Higher security: 14 rounds (slower)
```

### Benchmarking

```javascript
async function benchmarkRounds(rounds) {
  const start = Date.now();
  await bcrypt.hash('password', rounds);
  const end = Date.now();
  console.log(`${rounds} rounds: ${end - start}ms`);
}

// Test different rounds
await benchmarkRounds(8);  // ~25ms
await benchmarkRounds(10); // ~100ms
await benchmarkRounds(12); // ~400ms
await benchmarkRounds(14); // ~1600ms
```

### Recommended Rounds

```javascript
// 2024 recommendations:
// - Minimum: 10 rounds
// - Recommended: 12 rounds
// - High security: 14 rounds
// - Maximum: 15-16 rounds (very slow)

const SALT_ROUNDS = 12; // Good balance
```

## Best Practices

### 1. Use Environment Variables

```javascript
// .env
BCRYPT_ROUNDS=12

// config.js
module.exports = {
  bcryptRounds: parseInt(process.env.BCRYPT_ROUNDS) || 10
};

// Usage
const { bcryptRounds } = require('./config');
const hash = await bcrypt.hash(password, bcryptRounds);
```

### 2. Hash in Mongoose Pre-Save Hook

```javascript
const mongoose = require('mongoose');
const bcrypt = require('bcrypt');

const userSchema = new mongoose.Schema({
  email: { type: String, required: true, unique: true },
  password: { type: String, required: true }
});

// Hash password before saving
userSchema.pre('save', async function(next) {
  // Only hash if password is modified
  if (!this.isModified('password')) {
    return next();
  }
  
  try {
    const hash = await bcrypt.hash(this.password, 12);
    this.password = hash;
    next();
  } catch (error) {
    next(error);
  }
});

// Add compare method
userSchema.methods.comparePassword = async function(candidatePassword) {
  return await bcrypt.compare(candidatePassword, this.password);
};

module.exports = mongoose.model('User', userSchema);
```

### 3. Never Log or Expose Hashes

```javascript
// BAD
console.log('User password hash:', user.password);
res.json({ user }); // Includes password hash

// GOOD
const userResponse = {
  id: user.id,
  email: user.email,
  name: user.name
  // No password field
};
res.json({ user: userResponse });

// Or use Mongoose transform
userSchema.set('toJSON', {
  transform: function(doc, ret) {
    delete ret.password;
    return ret;
  }
});
```

### 4. Use Async Methods

```javascript
// BAD - Blocks event loop
const hash = bcrypt.hashSync(password, 10);

// GOOD - Non-blocking
const hash = await bcrypt.hash(password, 10);
```

### 5. Handle Errors Properly

```javascript
app.post('/register', async (req, res) => {
  try {
    const { email, password } = req.body;
    
    // Validate password
    if (password.length < 8) {
      return res.status(400).json({
        error: 'Password must be at least 8 characters'
      });
    }
    
    // Hash password
    const hash = await bcrypt.hash(password, 12);
    
    // Create user
    const user = await User.create({
      email,
      password: hash
    });
    
    res.status(201).json({ user: { id: user.id, email: user.email } });
  } catch (error) {
    console.error('Registration error:', error);
    res.status(500).json({ error: 'Registration failed' });
  }
});
```

### 6. Rate Limit Login Attempts

```javascript
const rateLimit = require('express-rate-limit');

const loginLimiter = rateLimit({
  windowMs: 15 * 60 * 1000, // 15 minutes
  max: 5, // 5 attempts
  message: 'Too many login attempts, please try again later'
});

app.post('/login', loginLimiter, async (req, res) => {
  // Login logic
});
```

### 7. Constant-Time Comparison

```javascript
// Bcrypt.compare already uses constant-time comparison
// This prevents timing attacks

// BAD - Timing attack vulnerable
if (password === storedPassword) { ... }

// GOOD - Constant-time
const isMatch = await bcrypt.compare(password, storedHash);
```

## Common Patterns

### Registration Flow

```javascript
app.post('/register', async (req, res) => {
  try {
    const { email, password, name } = req.body;
    
    // Validate input
    if (!email || !password || !name) {
      return res.status(400).json({ error: 'All fields required' });
    }
    
    if (password.length < 8) {
      return res.status(400).json({
        error: 'Password must be at least 8 characters'
      });
    }
    
    // Check if user exists
    const existingUser = await User.findOne({ email });
    if (existingUser) {
      return res.status(400).json({ error: 'Email already registered' });
    }
    
    // Hash password
    const hashedPassword = await bcrypt.hash(password, 12);
    
    // Create user
    const user = await User.create({
      email,
      password: hashedPassword,
      name
    });
    
    // Generate token
    const token = jwt.sign({ userId: user.id }, process.env.JWT_SECRET);
    
    res.status(201).json({
      user: { id: user.id, email: user.email, name: user.name },
      token
    });
  } catch (error) {
    console.error('Registration error:', error);
    res.status(500).json({ error: 'Registration failed' });
  }
});
```

### Login Flow

```javascript
app.post('/login', async (req, res) => {
  try {
    const { email, password } = req.body;
    
    // Validate input
    if (!email || !password) {
      return res.status(400).json({ error: 'Email and password required' });
    }
    
    // Find user
    const user = await User.findOne({ email });
    if (!user) {
      return res.status(401).json({ error: 'Invalid credentials' });
    }
    
    // Compare password
    const isMatch = await bcrypt.compare(password, user.password);
    if (!isMatch) {
      return res.status(401).json({ error: 'Invalid credentials' });
    }
    
    // Generate token
    const token = jwt.sign(
      { userId: user.id, email: user.email },
      process.env.JWT_SECRET,
      { expiresIn: '7d' }
    );
    
    res.json({
      user: { id: user.id, email: user.email, name: user.name },
      token
    });
  } catch (error) {
    console.error('Login error:', error);
    res.status(500).json({ error: 'Login failed' });
  }
});
```

### Password Change

```javascript
app.post('/change-password', authenticate, async (req, res) => {
  try {
    const { currentPassword, newPassword } = req.body;
    
    // Validate input
    if (!currentPassword || !newPassword) {
      return res.status(400).json({ error: 'Both passwords required' });
    }
    
    if (newPassword.length < 8) {
      return res.status(400).json({
        error: 'New password must be at least 8 characters'
      });
    }
    
    // Get user
    const user = await User.findById(req.user.userId);
    
    // Verify current password
    const isMatch = await bcrypt.compare(currentPassword, user.password);
    if (!isMatch) {
      return res.status(401).json({ error: 'Current password incorrect' });
    }
    
    // Hash new password
    const hashedPassword = await bcrypt.hash(newPassword, 12);
    
    // Update password
    user.password = hashedPassword;
    await user.save();
    
    res.json({ message: 'Password changed successfully' });
  } catch (error) {
    console.error('Password change error:', error);
    res.status(500).json({ error: 'Password change failed' });
  }
});
```

### Password Reset

```javascript
app.post('/reset-password', async (req, res) => {
  try {
    const { token, newPassword } = req.body;
    
    // Validate input
    if (!token || !newPassword) {
      return res.status(400).json({ error: 'Token and password required' });
    }
    
    if (newPassword.length < 8) {
      return res.status(400).json({
        error: 'Password must be at least 8 characters'
      });
    }
    
    // Verify reset token
    const decoded = jwt.verify(token, process.env.JWT_SECRET);
    
    // Get user
    const user = await User.findById(decoded.userId);
    if (!user) {
      return res.status(404).json({ error: 'User not found' });
    }
    
    // Hash new password
    const hashedPassword = await bcrypt.hash(newPassword, 12);
    
    // Update password
    user.password = hashedPassword;
    await user.save();
    
    res.json({ message: 'Password reset successfully' });
  } catch (error) {
    if (error.name === 'JsonWebTokenError') {
      return res.status(400).json({ error: 'Invalid reset token' });
    }
    console.error('Password reset error:', error);
    res.status(500).json({ error: 'Password reset failed' });
  }
});
```

### User Model with Bcrypt

```javascript
// models/User.js
const mongoose = require('mongoose');
const bcrypt = require('bcrypt');

const userSchema = new mongoose.Schema({
  email: {
    type: String,
    required: true,
    unique: true,
    lowercase: true,
    trim: true
  },
  password: {
    type: String,
    required: true,
    minlength: 8
  },
  name: {
    type: String,
    required: true
  }
}, {
  timestamps: true
});

// Hash password before saving
userSchema.pre('save', async function(next) {
  if (!this.isModified('password')) {
    return next();
  }
  
  try {
    const hash = await bcrypt.hash(this.password, 12);
    this.password = hash;
    next();
  } catch (error) {
    next(error);
  }
});

// Compare password method
userSchema.methods.comparePassword = async function(candidatePassword) {
  return await bcrypt.compare(candidatePassword, this.password);
};

// Remove password from JSON
userSchema.set('toJSON', {
  transform: function(doc, ret) {
    delete ret.password;
    return ret;
  }
});

module.exports = mongoose.model('User', userSchema);
```

## Summary

Bcrypt is the industry standard for password hashing:

✅ **Secure** - Salted and slow by design  
✅ **Proven** - Battle-tested since 1999  
✅ **Simple** - Easy to use API  
✅ **Adaptive** - Configurable work factor  
✅ **Standard** - Widely adopted and trusted  

**Key Takeaways:**
- Never store plain text passwords
- Use 12 rounds for production
- Always use async methods
- Hash in pre-save hooks
- Never expose password hashes
- Rate limit login attempts
- Use constant-time comparison (built-in)
- Validate password strength

Bcrypt is essential for any application that handles user authentication.
