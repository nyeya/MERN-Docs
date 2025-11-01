# CORS - Cross-Origin Resource Sharing

## Table of Contents
- [What is CORS?](#what-is-cors)
- [Why Use CORS Middleware?](#why-use-cors-middleware)
- [CORS vs Alternatives](#cors-vs-alternatives)
- [Installation & Setup](#installation--setup)
- [Configuration Options](#configuration-options)
- [Common Scenarios](#common-scenarios)
- [Security Best Practices](#security-best-practices)
- [Troubleshooting](#troubleshooting)

## What is CORS?

CORS (Cross-Origin Resource Sharing) is a security feature implemented by browsers that restricts web pages from making requests to a different domain than the one serving the web page. The `cors` npm package is Express middleware that enables CORS with various configuration options.

### Key Concepts
- **Same-Origin Policy**: Browser security that blocks cross-origin requests
- **CORS Headers**: HTTP headers that allow cross-origin requests
- **Preflight Requests**: OPTIONS requests browsers send before actual requests
- **Credentials**: Cookies and authentication headers in cross-origin requests

### How It Works

**Without CORS:**
```javascript
// Frontend (http://localhost:3000)
fetch('http://localhost:4000/api/users')
  .then(res => res.json())
  .catch(err => console.error(err));

// Browser blocks request:
// "Access to fetch at 'http://localhost:4000/api/users' from origin
// 'http://localhost:3000' has been blocked by CORS policy"
```

**With CORS:**
```javascript
// Backend (http://localhost:4000)
const cors = require('cors');
app.use(cors());

// Now frontend can make requests successfully
```

## Why Use CORS Middleware?

### 1. **Enable Cross-Origin Requests**

```javascript
// Without CORS middleware - manual setup
app.use((req, res, next) => {
  res.header('Access-Control-Allow-Origin', '*');
  res.header('Access-Control-Allow-Methods', 'GET, POST, PUT, DELETE');
  res.header('Access-Control-Allow-Headers', 'Content-Type, Authorization');
  if (req.method === 'OPTIONS') {
    return res.sendStatus(200);
  }
  next();
});

// With CORS middleware - one line
app.use(cors());
```

### 2. **Flexible Configuration**

```javascript
// Simple
app.use(cors());

// Configured
app.use(cors({
  origin: 'https://example.com',
  credentials: true,
  methods: ['GET', 'POST'],
  allowedHeaders: ['Content-Type', 'Authorization']
}));
```

### 3. **Dynamic Origin Validation**

```javascript
const corsOptions = {
  origin: function (origin, callback) {
    const whitelist = ['https://example.com', 'https://app.example.com'];
    if (whitelist.indexOf(origin) !== -1 || !origin) {
      callback(null, true);
    } else {
      callback(new Error('Not allowed by CORS'));
    }
  }
};

app.use(cors(corsOptions));
```

## CORS vs Alternatives

### CORS Middleware vs Manual Headers

| Feature | CORS Middleware | Manual Headers |
|---------|----------------|----------------|
| **Setup** | One line | Multiple lines |
| **Preflight** | Automatic | Manual handling |
| **Configuration** | Rich options | Custom logic |
| **Maintenance** | Easy | Error-prone |
| **Best Practices** | Built-in | Must implement |

### CORS vs Proxy

| Approach | CORS | Proxy |
|----------|------|-------|
| **Setup** | Backend config | Additional server |
| **Performance** | Direct | Extra hop |
| **Development** | Simple | Complex |
| **Production** | Standard | Sometimes needed |

**When to use CORS:**
- Standard API access
- Multiple frontend origins
- Public APIs

**When to use Proxy:**
- Development only
- Third-party API without CORS
- Legacy systems

## Installation & Setup

### Installation

```bash
npm install cors
npm install --save-dev @types/cors  # TypeScript
```

### Basic Setup

```javascript
const express = require('express');
const cors = require('cors');

const app = express();

// Enable CORS for all routes
app.use(cors());

app.get('/api/users', (req, res) => {
  res.json({ users: [] });
});

app.listen(4000);
```

### TypeScript Setup

```typescript
import express from 'express';
import cors from 'cors';

const app = express();

app.use(cors());
```

## Configuration Options

### Allow All Origins (Development)

```javascript
// Allow all origins (not recommended for production)
app.use(cors());

// Equivalent to:
app.use(cors({
  origin: '*'
}));
```

### Specific Origin

```javascript
// Single origin
app.use(cors({
  origin: 'https://example.com'
}));

// Multiple origins
app.use(cors({
  origin: ['https://example.com', 'https://app.example.com']
}));
```

### Dynamic Origin

```javascript
const corsOptions = {
  origin: function (origin, callback) {
    // Allow requests with no origin (mobile apps, Postman, etc.)
    if (!origin) return callback(null, true);
    
    const allowedOrigins = [
      'https://example.com',
      'https://app.example.com'
    ];
    
    if (allowedOrigins.includes(origin)) {
      callback(null, true);
    } else {
      callback(new Error('Not allowed by CORS'));
    }
  }
};

app.use(cors(corsOptions));
```

### Environment-Based Origins

```javascript
const allowedOrigins = process.env.NODE_ENV === 'production'
  ? ['https://example.com', 'https://app.example.com']
  : ['http://localhost:3000', 'http://localhost:3001'];

app.use(cors({
  origin: allowedOrigins
}));
```

### Methods

```javascript
app.use(cors({
  methods: ['GET', 'POST', 'PUT', 'DELETE', 'PATCH']
}));

// Or as string
app.use(cors({
  methods: 'GET,POST,PUT,DELETE'
}));
```

### Allowed Headers

```javascript
app.use(cors({
  allowedHeaders: ['Content-Type', 'Authorization', 'X-Requested-With']
}));
```

### Exposed Headers

```javascript
// Headers that frontend can access
app.use(cors({
  exposedHeaders: ['X-Total-Count', 'X-Page-Number']
}));

// Frontend can now access these headers
fetch('/api/users')
  .then(res => {
    const total = res.headers.get('X-Total-Count');
    console.log(total);
  });
```

### Credentials

```javascript
// Allow cookies and authentication
app.use(cors({
  origin: 'https://example.com',
  credentials: true
}));

// Frontend must also set credentials
fetch('http://api.example.com/users', {
  credentials: 'include'
});
```

### Max Age

```javascript
// Cache preflight request for 1 hour
app.use(cors({
  maxAge: 3600
}));
```

### Preflight Continue

```javascript
// Pass preflight to next handler
app.use(cors({
  preflightContinue: true
}));

app.options('*', (req, res) => {
  // Custom preflight handling
  res.sendStatus(200);
});
```

### Success Status

```javascript
// Status code for successful OPTIONS request
app.use(cors({
  optionsSuccessStatus: 200 // Some legacy browsers (IE11) choke on 204
}));
```

## Common Scenarios

### Scenario 1: Development Setup

```javascript
// Allow all origins in development
const corsOptions = {
  origin: process.env.NODE_ENV === 'development' ? '*' : 'https://example.com',
  credentials: true
};

app.use(cors(corsOptions));
```

### Scenario 2: Multiple Environments

```javascript
const getOrigins = () => {
  switch (process.env.NODE_ENV) {
    case 'production':
      return ['https://example.com', 'https://www.example.com'];
    case 'staging':
      return ['https://staging.example.com'];
    case 'development':
      return ['http://localhost:3000', 'http://localhost:3001'];
    default:
      return [];
  }
};

app.use(cors({
  origin: getOrigins(),
  credentials: true
}));
```

### Scenario 3: Per-Route CORS

```javascript
// Enable CORS for specific routes
app.get('/api/public', cors(), (req, res) => {
  res.json({ message: 'Public endpoint' });
});

// Different CORS config for different routes
const adminCors = cors({
  origin: 'https://admin.example.com',
  credentials: true
});

app.get('/api/admin', adminCors, (req, res) => {
  res.json({ message: 'Admin endpoint' });
});
```

### Scenario 4: Preflight Requests

```javascript
// Handle preflight for all routes
app.options('*', cors());

// Or specific route
app.options('/api/users', cors());

app.post('/api/users', cors(), (req, res) => {
  res.json({ created: true });
});
```

### Scenario 5: API with Authentication

```javascript
const corsOptions = {
  origin: ['https://example.com', 'https://app.example.com'],
  credentials: true,
  allowedHeaders: ['Content-Type', 'Authorization'],
  exposedHeaders: ['X-Total-Count'],
  maxAge: 600
};

app.use(cors(corsOptions));

app.get('/api/users', authenticate, (req, res) => {
  res.setHeader('X-Total-Count', users.length);
  res.json(users);
});
```

### Scenario 6: Subdomain Support

```javascript
const corsOptions = {
  origin: function (origin, callback) {
    if (!origin) return callback(null, true);
    
    // Allow all subdomains of example.com
    if (origin.match(/^https:\/\/([a-z0-9-]+\.)?example\.com$/)) {
      callback(null, true);
    } else {
      callback(new Error('Not allowed by CORS'));
    }
  },
  credentials: true
};

app.use(cors(corsOptions));
```

### Scenario 7: Mobile App + Web

```javascript
const corsOptions = {
  origin: function (origin, callback) {
    // Allow requests with no origin (mobile apps)
    if (!origin) return callback(null, true);
    
    // Allow web origins
    const webOrigins = ['https://example.com', 'https://app.example.com'];
    if (webOrigins.includes(origin)) {
      callback(null, true);
    } else {
      callback(new Error('Not allowed by CORS'));
    }
  },
  credentials: true
};

app.use(cors(corsOptions));
```

## Security Best Practices

### 1. Never Use Wildcard with Credentials

```javascript
// BAD - Security risk!
app.use(cors({
  origin: '*',
  credentials: true
}));

// GOOD - Specific origins
app.use(cors({
  origin: ['https://example.com'],
  credentials: true
}));
```

### 2. Whitelist Specific Origins

```javascript
// BAD - Too permissive
app.use(cors({ origin: '*' }));

// GOOD - Explicit whitelist
const whitelist = [
  'https://example.com',
  'https://app.example.com',
  'https://mobile.example.com'
];

app.use(cors({
  origin: function (origin, callback) {
    if (!origin || whitelist.includes(origin)) {
      callback(null, true);
    } else {
      callback(new Error('Not allowed by CORS'));
    }
  }
}));
```

### 3. Validate Origin Format

```javascript
const corsOptions = {
  origin: function (origin, callback) {
    if (!origin) return callback(null, true);
    
    // Validate origin format
    try {
      const url = new URL(origin);
      
      // Only allow HTTPS in production
      if (process.env.NODE_ENV === 'production' && url.protocol !== 'https:') {
        return callback(new Error('Only HTTPS allowed'));
      }
      
      // Check domain
      if (url.hostname.endsWith('.example.com')) {
        callback(null, true);
      } else {
        callback(new Error('Not allowed by CORS'));
      }
    } catch (err) {
      callback(new Error('Invalid origin'));
    }
  }
};

app.use(cors(corsOptions));
```

### 4. Limit Allowed Methods

```javascript
// Only allow necessary methods
app.use(cors({
  methods: ['GET', 'POST', 'PUT', 'DELETE'],
  // Don't allow TRACE, CONNECT, etc.
}));
```

### 5. Limit Allowed Headers

```javascript
// Only allow necessary headers
app.use(cors({
  allowedHeaders: [
    'Content-Type',
    'Authorization',
    'X-Requested-With'
  ]
}));
```

### 6. Use Environment Variables

```javascript
// .env
// ALLOWED_ORIGINS=https://example.com,https://app.example.com

const allowedOrigins = process.env.ALLOWED_ORIGINS?.split(',') || [];

app.use(cors({
  origin: allowedOrigins,
  credentials: true
}));
```

### 7. Log CORS Errors

```javascript
const corsOptions = {
  origin: function (origin, callback) {
    const whitelist = ['https://example.com'];
    
    if (!origin || whitelist.includes(origin)) {
      callback(null, true);
    } else {
      console.error(`CORS blocked origin: ${origin}`);
      callback(new Error('Not allowed by CORS'));
    }
  }
};

app.use(cors(corsOptions));
```

## Troubleshooting

### Issue 1: CORS Error Despite Middleware

**Problem:**
```
Access to fetch at 'http://localhost:4000/api/users' from origin
'http://localhost:3000' has been blocked by CORS policy
```

**Solutions:**

```javascript
// 1. Ensure CORS middleware is before routes
app.use(cors()); // Must be before routes
app.get('/api/users', (req, res) => { ... });

// 2. Check origin is allowed
app.use(cors({
  origin: 'http://localhost:3000' // Must match exactly
}));

// 3. Handle preflight
app.options('*', cors());
```

### Issue 2: Credentials Not Working

**Problem:** Cookies not sent with requests

**Solution:**

```javascript
// Backend
app.use(cors({
  origin: 'http://localhost:3000',
  credentials: true // Must be true
}));

// Frontend
fetch('http://localhost:4000/api/users', {
  credentials: 'include' // Must include
});
```

### Issue 3: Custom Headers Blocked

**Problem:** Custom headers not allowed

**Solution:**

```javascript
app.use(cors({
  allowedHeaders: ['Content-Type', 'Authorization', 'X-Custom-Header']
}));
```

### Issue 4: Response Headers Not Accessible

**Problem:** Can't read response headers in frontend

**Solution:**

```javascript
// Backend - expose headers
app.use(cors({
  exposedHeaders: ['X-Total-Count', 'X-Page-Number']
}));

// Frontend - now accessible
const total = response.headers.get('X-Total-Count');
```

### Issue 5: Preflight Failing

**Problem:** OPTIONS request returns error

**Solution:**

```javascript
// Explicitly handle OPTIONS
app.options('*', cors());

// Or for specific route
app.options('/api/users', cors());

app.post('/api/users', cors(), (req, res) => {
  // Handle POST
});
```

### Issue 6: Port Mismatch

**Problem:** Origin includes port but config doesn't

**Solution:**

```javascript
// Include port in origin
app.use(cors({
  origin: [
    'http://localhost:3000', // Include port
    'http://localhost:3001'
  ]
}));
```

## Complete Example

```javascript
const express = require('express');
const cors = require('cors');

const app = express();

// Environment-based configuration
const isDevelopment = process.env.NODE_ENV === 'development';

const corsOptions = {
  origin: function (origin, callback) {
    // Allow requests with no origin (mobile apps, Postman)
    if (!origin) return callback(null, true);
    
    const allowedOrigins = isDevelopment
      ? ['http://localhost:3000', 'http://localhost:3001']
      : ['https://example.com', 'https://app.example.com'];
    
    if (allowedOrigins.includes(origin)) {
      callback(null, true);
    } else {
      console.error(`CORS blocked: ${origin}`);
      callback(new Error('Not allowed by CORS'));
    }
  },
  credentials: true,
  methods: ['GET', 'POST', 'PUT', 'DELETE', 'PATCH'],
  allowedHeaders: ['Content-Type', 'Authorization'],
  exposedHeaders: ['X-Total-Count'],
  maxAge: 600,
  optionsSuccessStatus: 200
};

// Apply CORS middleware
app.use(cors(corsOptions));

// Handle preflight
app.options('*', cors(corsOptions));

// Routes
app.get('/api/users', (req, res) => {
  res.setHeader('X-Total-Count', '100');
  res.json({ users: [] });
});

app.post('/api/users', (req, res) => {
  res.status(201).json({ created: true });
});

// Error handling
app.use((err, req, res, next) => {
  if (err.message === 'Not allowed by CORS') {
    res.status(403).json({ error: 'CORS policy violation' });
  } else {
    next(err);
  }
});

app.listen(4000, () => {
  console.log('Server running on port 4000');
});
```

## Summary

CORS middleware is essential for modern web applications:

✅ **Simple Setup** - One line enables CORS  
✅ **Flexible** - Rich configuration options  
✅ **Secure** - Proper origin validation  
✅ **Standard** - Follows CORS specification  
✅ **Production-Ready** - Handles edge cases  

**Key Takeaways:**
- Use specific origins in production, not wildcards
- Never combine wildcard origin with credentials
- Handle preflight requests properly
- Validate origins dynamically when needed
- Use environment variables for configuration
- Log CORS violations for debugging
- Test with actual frontend, not just Postman

CORS middleware makes cross-origin requests secure and manageable in Express applications.
