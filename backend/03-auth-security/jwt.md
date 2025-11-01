# JWT (JSON Web Tokens) - Complete Authentication Guide

## Table of Contents
- [What is JWT?](#what-is-jwt)
- [Why Use JWT?](#why-use-jwt)
- [JWT vs Alternatives](#jwt-vs-alternatives)
- [Installation & Setup](#installation--setup)
- [JWT Structure](#jwt-structure)
- [Creating Tokens](#creating-tokens)
- [Verifying Tokens](#verifying-tokens)
- [Refresh Tokens](#refresh-tokens)
- [Security Best Practices](#security-best-practices)
- [Real-World Implementation](#real-world-implementation)

## What is JWT?

JSON Web Token (JWT) is an open standard (RFC 7519) for securely transmitting information between parties as a JSON object. JWTs are commonly used for authentication and information exchange in web applications.

### Key Features
- **Self-Contained**: Contains all necessary information about the user
- **Stateless**: No server-side session storage required
- **Compact**: Small size, can be sent via URL, POST parameter, or HTTP header
- **Secure**: Digitally signed to verify authenticity
- **Cross-Domain**: Works across different domains and services

## Why Use JWT?

### 1. **Stateless Authentication**

**Traditional Sessions:**
```javascript
// Server must store session data
const sessions = new Map();

app.post('/login', (req, res) => {
  const user = authenticateUser(req.body);
  const sessionId = generateSessionId();
  sessions.set(sessionId, { userId: user.id });
  res.cookie('sessionId', sessionId);
});

// Must lookup session on every request
app.get('/profile', (req, res) => {
  const sessionId = req.cookies.sessionId;
  const session = sessions.get(sessionId); // Database/memory lookup
  if (!session) return res.status(401).send('Unauthorized');
  // ...
});
```

**With JWT:**
```javascript
// No server-side storage needed
app.post('/login', (req, res) => {
  const user = authenticateUser(req.body);
  const token = jwt.sign({ userId: user.id }, SECRET_KEY);
  res.json({ token });
});

// Verify token without database lookup
app.get('/profile', (req, res) => {
  const token = req.headers.authorization?.split(' ')[1];
  const decoded = jwt.verify(token, SECRET_KEY); // No database needed
  // decoded.userId is available
});
```

### 2. **Scalability**

```javascript
// Works across multiple servers without shared session store
// Server 1, 2, 3 can all verify the same JWT
// No need for sticky sessions or centralized session storage
```

### 3. **Mobile & API Friendly**

```javascript
// Easy to use in mobile apps
fetch('https://api.example.com/profile', {
  headers: {
    'Authorization': `Bearer ${token}`
  }
});
```

### 4. **Cross-Domain Authentication**

```javascript
// Single token works across multiple domains
// api.example.com
// app.example.com
// mobile.example.com
```

## JWT vs Alternatives

### JWT vs Session Cookies

| Feature | JWT | Session Cookies |
|---------|-----|-----------------|
| **Storage** | Client-side | Server-side |
| **Scalability** | Excellent | Requires shared store |
| **Revocation** | Complex | Easy |
| **Size** | Larger | Smaller |
| **Cross-Domain** | Easy | Complex (CORS) |
| **Security** | XSS vulnerable | CSRF vulnerable |

**When to use JWT:**
- Microservices architecture
- Mobile applications
- Cross-domain authentication
- Stateless APIs

**When to use Sessions:**
- Traditional web apps
- Need instant revocation
- Smaller payload size
- Single-domain applications

### JWT vs OAuth2

| Feature | JWT | OAuth2 |
|---------|-----|--------|
| **Purpose** | Token format | Authorization framework |
| **Complexity** | Simple | Complex |
| **Use Case** | Internal auth | Third-party auth |
| **Standards** | RFC 7519 | RFC 6749 |

**Note**: OAuth2 often uses JWT as the token format!

### JWT vs Passport.js

These are complementary, not alternatives:
- **JWT**: Token format and library
- **Passport.js**: Authentication middleware that can use JWT

```javascript
// Using both together
const passport = require('passport');
const JwtStrategy = require('passport-jwt').Strategy;

passport.use(new JwtStrategy(options, verify));
```

## Installation & Setup

### Installation

```bash
npm install jsonwebtoken
npm install --save-dev @types/jsonwebtoken  # TypeScript
```

### Basic Setup

```javascript
const jwt = require('jsonwebtoken');

// Secret key (store in environment variable)
const SECRET_KEY = process.env.JWT_SECRET || 'your-secret-key';

// Generate token
const token = jwt.sign({ userId: 123 }, SECRET_KEY);

// Verify token
const decoded = jwt.verify(token, SECRET_KEY);
```

### Environment Configuration

**.env:**
```env
JWT_SECRET=your-super-secret-key-change-this-in-production
JWT_EXPIRES_IN=7d
JWT_REFRESH_SECRET=your-refresh-token-secret
JWT_REFRESH_EXPIRES_IN=30d
```

**config/jwt.js:**
```javascript
module.exports = {
  secret: process.env.JWT_SECRET,
  expiresIn: process.env.JWT_EXPIRES_IN || '7d',
  refreshSecret: process.env.JWT_REFRESH_SECRET,
  refreshExpiresIn: process.env.JWT_REFRESH_EXPIRES_IN || '30d'
};
```

## JWT Structure

### Three Parts

A JWT consists of three parts separated by dots:
```
header.payload.signature
```

**Example:**
```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VySWQiOjEyMywiaWF0IjoxNjE2MjM5MDIyfQ.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c
```

### 1. Header

```json
{
  "alg": "HS256",
  "typ": "JWT"
}
```

- `alg`: Algorithm used (HS256, RS256, etc.)
- `typ`: Token type (JWT)

### 2. Payload

```json
{
  "userId": 123,
  "email": "user@example.com",
  "role": "admin",
  "iat": 1616239022,
  "exp": 1616325422
}
```

**Standard Claims:**
- `iss`: Issuer
- `sub`: Subject
- `aud`: Audience
- `exp`: Expiration time
- `nbf`: Not before
- `iat`: Issued at
- `jti`: JWT ID

**Custom Claims:**
- Any additional data you want to include

### 3. Signature

```javascript
HMACSHA256(
  base64UrlEncode(header) + "." + base64UrlEncode(payload),
  secret
)
```

## Creating Tokens

### Basic Token Creation

```javascript
const jwt = require('jsonwebtoken');

// Simple token
const token = jwt.sign(
  { userId: 123 },
  process.env.JWT_SECRET
);

// Token with expiration
const token = jwt.sign(
  { userId: 123 },
  process.env.JWT_SECRET,
  { expiresIn: '7d' }
);

// Token with multiple claims
const token = jwt.sign(
  {
    userId: 123,
    email: 'user@example.com',
    role: 'admin'
  },
  process.env.JWT_SECRET,
  {
    expiresIn: '7d',
    issuer: 'myapp.com',
    audience: 'myapp-users'
  }
);
```

### Token Options

```javascript
const token = jwt.sign(
  payload,
  secret,
  {
    algorithm: 'HS256',        // Algorithm (HS256, RS256, etc.)
    expiresIn: '7d',           // Expiration (7d, 24h, 60m, 30s)
    notBefore: '0',            // Not valid before
    audience: 'myapp',         // Intended audience
    issuer: 'myapp.com',       // Token issuer
    jwtid: 'unique-id',        // Unique token ID
    subject: 'user-auth',      // Token subject
    noTimestamp: false,        // Include iat claim
    header: {                  // Additional header fields
      kid: 'key-id'
    }
  }
);
```

### Async Token Creation

```javascript
// Using callback
jwt.sign(payload, secret, options, (err, token) => {
  if (err) {
    console.error(err);
  } else {
    console.log(token);
  }
});

// Using Promise
const util = require('util');
const signAsync = util.promisify(jwt.sign);

try {
  const token = await signAsync(payload, secret, options);
  console.log(token);
} catch (err) {
  console.error(err);
}
```

## Verifying Tokens

### Basic Verification

```javascript
const jwt = require('jsonwebtoken');

try {
  const decoded = jwt.verify(token, process.env.JWT_SECRET);
  console.log(decoded);
  // { userId: 123, iat: 1616239022, exp: 1616325422 }
} catch (err) {
  if (err.name === 'TokenExpiredError') {
    console.log('Token expired');
  } else if (err.name === 'JsonWebTokenError') {
    console.log('Invalid token');
  } else {
    console.log('Token verification failed');
  }
}
```

### Verification Options

```javascript
const decoded = jwt.verify(
  token,
  secret,
  {
    algorithms: ['HS256'],           // Allowed algorithms
    audience: 'myapp',               // Expected audience
    issuer: 'myapp.com',             // Expected issuer
    ignoreExpiration: false,         // Don't ignore expiration
    ignoreNotBefore: false,          // Don't ignore nbf
    clockTolerance: 0,               // Clock skew tolerance (seconds)
    maxAge: '7d',                    // Maximum age
    subject: 'user-auth'             // Expected subject
  }
);
```

### Async Verification

```javascript
// Using callback
jwt.verify(token, secret, (err, decoded) => {
  if (err) {
    console.error(err);
  } else {
    console.log(decoded);
  }
});

// Using Promise
const util = require('util');
const verifyAsync = util.promisify(jwt.verify);

try {
  const decoded = await verifyAsync(token, secret);
  console.log(decoded);
} catch (err) {
  console.error(err);
}
```

### Decode Without Verification

```javascript
// Decode without verifying signature (unsafe for authentication)
const decoded = jwt.decode(token);
console.log(decoded);

// Decode with complete info
const decoded = jwt.decode(token, { complete: true });
console.log(decoded);
// {
//   header: { alg: 'HS256', typ: 'JWT' },
//   payload: { userId: 123, iat: 1616239022 },
//   signature: '...'
// }
```

## Refresh Tokens

### Why Refresh Tokens?

- **Short-lived access tokens**: Minimize damage if stolen
- **Long-lived refresh tokens**: Better user experience
- **Revocation**: Can invalidate refresh tokens

### Implementation

```javascript
// Generate tokens
function generateTokens(user) {
  const accessToken = jwt.sign(
    { userId: user.id, email: user.email },
    process.env.JWT_SECRET,
    { expiresIn: '15m' }  // Short-lived
  );
  
  const refreshToken = jwt.sign(
    { userId: user.id },
    process.env.JWT_REFRESH_SECRET,
    { expiresIn: '7d' }  // Long-lived
  );
  
  return { accessToken, refreshToken };
}

// Login endpoint
app.post('/login', async (req, res) => {
  const user = await authenticateUser(req.body);
  if (!user) {
    return res.status(401).json({ error: 'Invalid credentials' });
  }
  
  const { accessToken, refreshToken } = generateTokens(user);
  
  // Store refresh token in database
  await storeRefreshToken(user.id, refreshToken);
  
  res.json({ accessToken, refreshToken });
});

// Refresh endpoint
app.post('/refresh', async (req, res) => {
  const { refreshToken } = req.body;
  
  if (!refreshToken) {
    return res.status(401).json({ error: 'Refresh token required' });
  }
  
  try {
    // Verify refresh token
    const decoded = jwt.verify(refreshToken, process.env.JWT_REFRESH_SECRET);
    
    // Check if refresh token exists in database
    const storedToken = await getRefreshToken(decoded.userId);
    if (storedToken !== refreshToken) {
      return res.status(401).json({ error: 'Invalid refresh token' });
    }
    
    // Generate new access token
    const user = await getUserById(decoded.userId);
    const accessToken = jwt.sign(
      { userId: user.id, email: user.email },
      process.env.JWT_SECRET,
      { expiresIn: '15m' }
    );
    
    res.json({ accessToken });
  } catch (err) {
    res.status(401).json({ error: 'Invalid refresh token' });
  }
});

// Logout endpoint
app.post('/logout', async (req, res) => {
  const { refreshToken } = req.body;
  
  // Remove refresh token from database
  await deleteRefreshToken(refreshToken);
  
  res.json({ message: 'Logged out successfully' });
});
```

### Refresh Token Rotation

```javascript
// More secure: Issue new refresh token on each refresh
app.post('/refresh', async (req, res) => {
  const { refreshToken } = req.body;
  
  try {
    const decoded = jwt.verify(refreshToken, process.env.JWT_REFRESH_SECRET);
    
    // Verify stored token
    const storedToken = await getRefreshToken(decoded.userId);
    if (storedToken !== refreshToken) {
      // Possible token reuse attack
      await revokeAllTokens(decoded.userId);
      return res.status(401).json({ error: 'Invalid refresh token' });
    }
    
    // Generate new tokens
    const user = await getUserById(decoded.userId);
    const { accessToken, refreshToken: newRefreshToken } = generateTokens(user);
    
    // Replace old refresh token
    await updateRefreshToken(user.id, newRefreshToken);
    
    res.json({ accessToken, refreshToken: newRefreshToken });
  } catch (err) {
    res.status(401).json({ error: 'Invalid refresh token' });
  }
});
```

## Security Best Practices

### 1. Strong Secret Keys

```javascript
// Bad
const SECRET = 'secret';

// Good
const SECRET = crypto.randomBytes(64).toString('hex');
// Store in environment variable

// Generate secure secret
node -e "console.log(require('crypto').randomBytes(64).toString('hex'))"
```

### 2. Short Expiration Times

```javascript
// Access token: 15 minutes to 1 hour
const accessToken = jwt.sign(payload, secret, { expiresIn: '15m' });

// Refresh token: 7 to 30 days
const refreshToken = jwt.sign(payload, refreshSecret, { expiresIn: '7d' });
```

### 3. Don't Store Sensitive Data

```javascript
// Bad
const token = jwt.sign({
  userId: 123,
  password: 'secret123',      // Never!
  creditCard: '1234-5678',    // Never!
  ssn: '123-45-6789'          // Never!
}, secret);

// Good
const token = jwt.sign({
  userId: 123,
  email: 'user@example.com',
  role: 'user'
}, secret);
```

### 4. Use HTTPS

```javascript
// Always use HTTPS in production
if (process.env.NODE_ENV === 'production' && req.protocol !== 'https') {
  return res.redirect('https://' + req.hostname + req.url);
}
```

### 5. Implement Token Blacklist

```javascript
// Store revoked tokens
const blacklist = new Set();

// Revoke token
app.post('/logout', authenticate, (req, res) => {
  blacklist.add(req.token);
  res.json({ message: 'Logged out' });
});

// Check blacklist
function authenticate(req, res, next) {
  const token = req.headers.authorization?.split(' ')[1];
  
  if (blacklist.has(token)) {
    return res.status(401).json({ error: 'Token revoked' });
  }
  
  try {
    const decoded = jwt.verify(token, process.env.JWT_SECRET);
    req.user = decoded;
    req.token = token;
    next();
  } catch (err) {
    res.status(401).json({ error: 'Invalid token' });
  }
}
```

### 6. Validate Token Claims

```javascript
function authenticate(req, res, next) {
  const token = req.headers.authorization?.split(' ')[1];
  
  try {
    const decoded = jwt.verify(token, process.env.JWT_SECRET, {
      algorithms: ['HS256'],
      issuer: 'myapp.com',
      audience: 'myapp-users'
    });
    
    // Additional validation
    if (!decoded.userId || !decoded.email) {
      throw new Error('Invalid token claims');
    }
    
    req.user = decoded;
    next();
  } catch (err) {
    res.status(401).json({ error: 'Invalid token' });
  }
}
```

### 7. Rate Limiting

```javascript
const rateLimit = require('express-rate-limit');

const loginLimiter = rateLimit({
  windowMs: 15 * 60 * 1000, // 15 minutes
  max: 5, // 5 requests per window
  message: 'Too many login attempts'
});

app.post('/login', loginLimiter, async (req, res) => {
  // Login logic
});
```

## Real-World Implementation

### Complete Authentication System

```javascript
const express = require('express');
const jwt = require('jsonwebtoken');
const bcrypt = require('bcrypt');

const app = express();
app.use(express.json());

// Configuration
const JWT_SECRET = process.env.JWT_SECRET;
const JWT_REFRESH_SECRET = process.env.JWT_REFRESH_SECRET;

// In-memory storage (use database in production)
const users = new Map();
const refreshTokens = new Map();

// Helper functions
function generateTokens(user) {
  const accessToken = jwt.sign(
    { userId: user.id, email: user.email, role: user.role },
    JWT_SECRET,
    { expiresIn: '15m' }
  );
  
  const refreshToken = jwt.sign(
    { userId: user.id },
    JWT_REFRESH_SECRET,
    { expiresIn: '7d' }
  );
  
  return { accessToken, refreshToken };
}

// Middleware
function authenticate(req, res, next) {
  const authHeader = req.headers.authorization;
  const token = authHeader && authHeader.split(' ')[1];
  
  if (!token) {
    return res.status(401).json({ error: 'Access token required' });
  }
  
  try {
    const decoded = jwt.verify(token, JWT_SECRET);
    req.user = decoded;
    next();
  } catch (err) {
    if (err.name === 'TokenExpiredError') {
      return res.status(401).json({ error: 'Token expired' });
    }
    res.status(403).json({ error: 'Invalid token' });
  }
}

function authorize(...roles) {
  return (req, res, next) => {
    if (!roles.includes(req.user.role)) {
      return res.status(403).json({ error: 'Insufficient permissions' });
    }
    next();
  };
}

// Routes
app.post('/register', async (req, res) => {
  const { email, password, name } = req.body;
  
  if (users.has(email)) {
    return res.status(400).json({ error: 'User already exists' });
  }
  
  const hashedPassword = await bcrypt.hash(password, 10);
  const user = {
    id: users.size + 1,
    email,
    password: hashedPassword,
    name,
    role: 'user'
  };
  
  users.set(email, user);
  
  const { accessToken, refreshToken } = generateTokens(user);
  refreshTokens.set(user.id, refreshToken);
  
  res.status(201).json({
    user: { id: user.id, email: user.email, name: user.name },
    accessToken,
    refreshToken
  });
});

app.post('/login', async (req, res) => {
  const { email, password } = req.body;
  
  const user = users.get(email);
  if (!user) {
    return res.status(401).json({ error: 'Invalid credentials' });
  }
  
  const validPassword = await bcrypt.compare(password, user.password);
  if (!validPassword) {
    return res.status(401).json({ error: 'Invalid credentials' });
  }
  
  const { accessToken, refreshToken } = generateTokens(user);
  refreshTokens.set(user.id, refreshToken);
  
  res.json({
    user: { id: user.id, email: user.email, name: user.name, role: user.role },
    accessToken,
    refreshToken
  });
});

app.post('/refresh', (req, res) => {
  const { refreshToken } = req.body;
  
  if (!refreshToken) {
    return res.status(401).json({ error: 'Refresh token required' });
  }
  
  try {
    const decoded = jwt.verify(refreshToken, JWT_REFRESH_SECRET);
    
    const storedToken = refreshTokens.get(decoded.userId);
    if (storedToken !== refreshToken) {
      return res.status(403).json({ error: 'Invalid refresh token' });
    }
    
    const user = Array.from(users.values()).find(u => u.id === decoded.userId);
    const accessToken = jwt.sign(
      { userId: user.id, email: user.email, role: user.role },
      JWT_SECRET,
      { expiresIn: '15m' }
    );
    
    res.json({ accessToken });
  } catch (err) {
    res.status(403).json({ error: 'Invalid refresh token' });
  }
});

app.post('/logout', authenticate, (req, res) => {
  refreshTokens.delete(req.user.userId);
  res.json({ message: 'Logged out successfully' });
});

app.get('/profile', authenticate, (req, res) => {
  const user = Array.from(users.values()).find(u => u.id === req.user.userId);
  res.json({
    id: user.id,
    email: user.email,
    name: user.name,
    role: user.role
  });
});

app.get('/admin', authenticate, authorize('admin'), (req, res) => {
  res.json({ message: 'Admin access granted' });
});

app.listen(3000, () => {
  console.log('Server running on port 3000');
});
```

### Express Middleware Package

```javascript
// middleware/auth.js
const jwt = require('jsonwebtoken');

class AuthMiddleware {
  constructor(config) {
    this.secret = config.secret;
    this.refreshSecret = config.refreshSecret;
    this.expiresIn = config.expiresIn || '15m';
    this.refreshExpiresIn = config.refreshExpiresIn || '7d';
  }

  generateTokens(payload) {
    const accessToken = jwt.sign(payload, this.secret, {
      expiresIn: this.expiresIn
    });
    
    const refreshToken = jwt.sign(
      { userId: payload.userId },
      this.refreshSecret,
      { expiresIn: this.refreshExpiresIn }
    );
    
    return { accessToken, refreshToken };
  }

  authenticate() {
    return (req, res, next) => {
      const authHeader = req.headers.authorization;
      const token = authHeader && authHeader.split(' ')[1];
      
      if (!token) {
        return res.status(401).json({ error: 'Access token required' });
      }
      
      try {
        const decoded = jwt.verify(token, this.secret);
        req.user = decoded;
        next();
      } catch (err) {
        if (err.name === 'TokenExpiredError') {
          return res.status(401).json({ error: 'Token expired' });
        }
        res.status(403).json({ error: 'Invalid token' });
      }
    };
  }

  authorize(...roles) {
    return (req, res, next) => {
      if (!req.user) {
        return res.status(401).json({ error: 'Authentication required' });
      }
      
      if (roles.length && !roles.includes(req.user.role)) {
        return res.status(403).json({ error: 'Insufficient permissions' });
      }
      
      next();
    };
  }

  verifyRefreshToken(token) {
    return jwt.verify(token, this.refreshSecret);
  }
}

module.exports = AuthMiddleware;
```

**Usage:**
```javascript
const AuthMiddleware = require('./middleware/auth');

const auth = new AuthMiddleware({
  secret: process.env.JWT_SECRET,
  refreshSecret: process.env.JWT_REFRESH_SECRET,
  expiresIn: '15m',
  refreshExpiresIn: '7d'
});

// Use in routes
app.get('/protected', auth.authenticate(), (req, res) => {
  res.json({ user: req.user });
});

app.get('/admin', auth.authenticate(), auth.authorize('admin'), (req, res) => {
  res.json({ message: 'Admin access' });
});
```

## Summary

JWT is essential for modern authentication:

✅ **Stateless** - No server-side session storage  
✅ **Scalable** - Works across multiple servers  
✅ **Flexible** - Works with web, mobile, and APIs  
✅ **Secure** - Cryptographically signed  
✅ **Standard** - Industry-standard format  

**Key Takeaways:**
- Use short-lived access tokens (15m - 1h)
- Implement refresh token rotation
- Store secrets in environment variables
- Never store sensitive data in tokens
- Always use HTTPS in production
- Implement proper error handling
- Consider token blacklisting for logout

JWT provides a robust, scalable solution for authentication in modern applications.
