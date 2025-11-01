# Helmet.js - Secure Your Express Apps

## Table of Contents
- [What is Helmet?](#what-is-helmet)
- [Why Use Helmet?](#why-use-helmet)
- [Helmet vs Alternatives](#helmet-vs-alternatives)
- [Installation & Setup](#installation--setup)
- [How It Works](#how-it-works)
- [Security Headers Explained](#security-headers-explained)
- [Configuration](#configuration)
- [Real-World Examples](#real-world-examples)
- [Best Practices](#best-practices)

## What is Helmet?

Helmet is a collection of 15 smaller middleware functions that set HTTP response headers to help protect your Express applications from common web vulnerabilities. It's essentially a security wrapper that implements industry-standard security practices through HTTP headers.

### Key Features
- **Automatic Security Headers**: Sets multiple security-related HTTP headers
- **Modular Design**: Enable/disable individual protections
- **Zero Configuration**: Works out of the box with sensible defaults
- **Actively Maintained**: Regular updates for new security standards
- **Lightweight**: Minimal performance overhead

## Why Use Helmet?

### 1. **Protection Against Common Attacks**

Without Helmet, your Express app is vulnerable to:
- **Clickjacking**: Malicious sites embedding your pages in iframes
- **XSS Attacks**: Cross-site scripting through reflected content
- **MIME Sniffing**: Browsers executing malicious content
- **Information Leakage**: Exposing technology stack details

**Without Helmet:**
```javascript
const express = require('express');
const app = express();

app.get('/', (req, res) => {
  res.send('<h1>Hello World</h1>');
});

// Response headers:
// X-Powered-By: Express (reveals technology)
// No security headers present
```

**With Helmet:**
```javascript
const express = require('express');
const helmet = require('helmet');
const app = express();

app.use(helmet());

app.get('/', (req, res) => {
  res.send('<h1>Hello World</h1>');
});

// Response headers now include:
// Content-Security-Policy
// X-Content-Type-Options
// X-Frame-Options
// Strict-Transport-Security
// And more...
```

### 2. **Compliance Requirements**

Many security standards require specific HTTP headers:
- **OWASP Top 10**: Addresses several OWASP security risks
- **PCI DSS**: Payment card industry compliance
- **HIPAA**: Healthcare data protection
- **SOC 2**: Security audit requirements

### 3. **Easy Implementation**

One line of code provides comprehensive protection:
```javascript
app.use(helmet()); // That's it!
```

## Helmet vs Alternatives

### Helmet vs Manual Header Setting

| Aspect | Helmet | Manual Headers |
|--------|--------|----------------|
| **Setup Time** | 1 line | 50+ lines |
| **Maintenance** | Auto-updated | Manual updates |
| **Completeness** | 15 protections | Easy to miss some |
| **Best Practices** | Built-in | Requires research |
| **Flexibility** | Highly configurable | Full control |

**Manual Approach:**
```javascript
// Without Helmet - lots of code
app.use((req, res, next) => {
  res.setHeader('X-Content-Type-Options', 'nosniff');
  res.setHeader('X-Frame-Options', 'DENY');
  res.setHeader('X-XSS-Protection', '1; mode=block');
  res.setHeader('Strict-Transport-Security', 'max-age=31536000; includeSubDomains');
  res.setHeader('Content-Security-Policy', "default-src 'self'");
  // ... many more headers
  next();
});
```

**Helmet Approach:**
```javascript
// With Helmet - one line
app.use(helmet());
```

### Helmet vs CORS

These are complementary, not alternatives:
- **Helmet**: Sets security headers (CSP, XSS protection, etc.)
- **CORS**: Controls cross-origin resource sharing

Use both together:
```javascript
app.use(helmet());
app.use(cors());
```

### Helmet vs Express-Security

| Feature | Helmet | Express-Security |
|---------|--------|------------------|
| **Popularity** | 1M+ weekly downloads | Deprecated |
| **Maintenance** | Active | Unmaintained |
| **Features** | 15 middleware | Limited |
| **Community** | Large | Small |

**Recommendation**: Use Helmet. Express-security is no longer maintained.

## Installation & Setup

### Installation

```bash
npm install helmet
```

### Basic Setup

```javascript
const express = require('express');
const helmet = require('helmet');

const app = express();

// Use Helmet with default settings
app.use(helmet());

app.get('/', (req, res) => {
  res.send('Hello World!');
});

app.listen(3000);
```

### TypeScript Setup

```bash
npm install --save-dev @types/helmet
```

```typescript
import express from 'express';
import helmet from 'helmet';

const app = express();

app.use(helmet());
```

## How It Works

Helmet is a collection of 15 smaller middleware functions:

```javascript
// What helmet() actually does:
app.use(helmet.contentSecurityPolicy());
app.use(helmet.crossOriginEmbedderPolicy());
app.use(helmet.crossOriginOpenerPolicy());
app.use(helmet.crossOriginResourcePolicy());
app.use(helmet.dnsPrefetchControl());
app.use(helmet.expectCt());
app.use(helmet.frameguard());
app.use(helmet.hidePoweredBy());
app.use(helmet.hsts());
app.use(helmet.ieNoOpen());
app.use(helmet.noSniff());
app.use(helmet.originAgentCluster());
app.use(helmet.permittedCrossDomainPolicies());
app.use(helmet.referrerPolicy());
app.use(helmet.xssFilter());
```

Each middleware sets specific HTTP response headers before sending responses to clients.

## Security Headers Explained

### 1. Content-Security-Policy (CSP)

**What it does**: Controls which resources the browser can load (scripts, styles, images, etc.)

**Default**: `default-src 'self'` (only load resources from same origin)

**Why it matters**: Prevents XSS attacks by blocking unauthorized scripts

```javascript
// Custom CSP
app.use(helmet.contentSecurityPolicy({
  directives: {
    defaultSrc: ["'self'"],
    scriptSrc: ["'self'", "'unsafe-inline'", "cdn.example.com"],
    styleSrc: ["'self'", "'unsafe-inline'"],
    imgSrc: ["'self'", "data:", "images.example.com"],
    connectSrc: ["'self'", "api.example.com"],
    fontSrc: ["'self'", "fonts.googleapis.com"],
    objectSrc: ["'none'"],
    mediaSrc: ["'self'"],
    frameSrc: ["'none'"]
  }
}));
```

**Real-world example**: Blocking malicious scripts
```javascript
// Without CSP: This injected script would execute
// <script>alert('XSS Attack!')</script>

// With CSP: Browser blocks the script and logs error
```

### 2. X-Frame-Options

**What it does**: Prevents your site from being embedded in iframes

**Default**: `SAMEORIGIN` (only allow same-origin iframes)

**Why it matters**: Prevents clickjacking attacks

```javascript
// Options:
helmet.frameguard({ action: 'deny' })        // No iframes at all
helmet.frameguard({ action: 'sameorigin' })  // Only same-origin iframes
```

**Attack scenario prevented**:
```html
<!-- Malicious site trying to embed your login page -->
<iframe src="https://yourbank.com/login"></iframe>
<!-- With X-Frame-Options: DENY, this won't work -->
```

### 3. Strict-Transport-Security (HSTS)

**What it does**: Forces browsers to use HTTPS

**Default**: `max-age=15552000` (180 days)

**Why it matters**: Prevents man-in-the-middle attacks

```javascript
app.use(helmet.hsts({
  maxAge: 31536000,              // 1 year in seconds
  includeSubDomains: true,       // Apply to all subdomains
  preload: true                  // Submit to browser preload list
}));
```

**How it works**:
```
1. User visits https://example.com
2. Server sends: Strict-Transport-Security: max-age=31536000
3. For next year, browser automatically converts http:// to https://
4. Even if user types http://example.com, browser uses HTTPS
```

### 4. X-Content-Type-Options

**What it does**: Prevents browsers from MIME-sniffing

**Default**: `nosniff`

**Why it matters**: Stops browsers from executing files with wrong content-type

```javascript
app.use(helmet.noSniff());
```

**Attack prevented**:
```javascript
// Attacker uploads image.jpg that contains JavaScript
// Without noSniff: Browser might execute it as JavaScript
// With noSniff: Browser respects Content-Type and treats it as image
```

### 5. X-XSS-Protection

**What it does**: Enables browser's XSS filter

**Default**: `0` (disabled, CSP is preferred)

**Why it matters**: Legacy protection for older browsers

```javascript
// Modern approach: Use CSP instead
app.use(helmet.contentSecurityPolicy());

// Legacy support:
app.use(helmet.xssFilter());
```

### 6. Referrer-Policy

**What it does**: Controls how much referrer information is sent

**Default**: `no-referrer`

**Why it matters**: Prevents leaking sensitive URLs

```javascript
app.use(helmet.referrerPolicy({
  policy: 'strict-origin-when-cross-origin'
}));
```

**Options**:
- `no-referrer`: Never send referrer
- `same-origin`: Only send to same origin
- `strict-origin`: Only send origin (not full URL)
- `strict-origin-when-cross-origin`: Full URL for same-origin, origin only for cross-origin

### 7. Hide X-Powered-By

**What it does**: Removes `X-Powered-By: Express` header

**Why it matters**: Doesn't reveal your technology stack to attackers

```javascript
app.use(helmet.hidePoweredBy());

// Or manually:
app.disable('x-powered-by');
```

### 8. DNS Prefetch Control

**What it does**: Controls browser DNS prefetching

**Default**: `off`

**Why it matters**: Prevents privacy leaks through DNS requests

```javascript
app.use(helmet.dnsPrefetchControl({ allow: false }));
```

### 9. Cross-Origin Policies

**Cross-Origin-Embedder-Policy (COEP)**:
```javascript
app.use(helmet.crossOriginEmbedderPolicy());
// Prevents loading cross-origin resources without explicit permission
```

**Cross-Origin-Opener-Policy (COOP)**:
```javascript
app.use(helmet.crossOriginOpenerPolicy());
// Isolates your page from cross-origin windows
```

**Cross-Origin-Resource-Policy (CORP)**:
```javascript
app.use(helmet.crossOriginResourcePolicy({ policy: "same-origin" }));
// Controls who can load your resources
```

## Configuration

### Default Configuration

```javascript
// This:
app.use(helmet());

// Is equivalent to:
app.use(helmet({
  contentSecurityPolicy: {
    directives: {
      defaultSrc: ["'self'"],
      baseUri: ["'self'"],
      fontSrc: ["'self'", "https:", "data:"],
      formAction: ["'self'"],
      frameAncestors: ["'self'"],
      imgSrc: ["'self'", "data:"],
      objectSrc: ["'none'"],
      scriptSrc: ["'self'"],
      scriptSrcAttr: ["'none'"],
      styleSrc: ["'self'", "https:", "'unsafe-inline'"],
      upgradeInsecureRequests: []
    }
  },
  crossOriginEmbedderPolicy: true,
  crossOriginOpenerPolicy: true,
  crossOriginResourcePolicy: true,
  dnsPrefetchControl: true,
  frameguard: true,
  hidePoweredBy: true,
  hsts: true,
  ieNoOpen: true,
  noSniff: true,
  originAgentCluster: true,
  permittedCrossDomainPolicies: true,
  referrerPolicy: true,
  xssFilter: true
}));
```

### Customizing Specific Headers

```javascript
app.use(helmet({
  // Disable specific middleware
  contentSecurityPolicy: false,
  
  // Customize specific middleware
  hsts: {
    maxAge: 31536000,
    includeSubDomains: true,
    preload: true
  },
  
  frameguard: {
    action: 'deny'
  },
  
  referrerPolicy: {
    policy: 'strict-origin-when-cross-origin'
  }
}));
```

### Selective Application

```javascript
// Apply Helmet to specific routes
app.use('/api', helmet());

// Apply different configs to different routes
app.use('/public', helmet({
  contentSecurityPolicy: false
}));

app.use('/admin', helmet({
  frameguard: { action: 'deny' },
  hsts: { maxAge: 31536000 }
}));
```

## Real-World Examples

### Example 1: Basic Web Application

```javascript
const express = require('express');
const helmet = require('helmet');

const app = express();

// Basic Helmet setup
app.use(helmet());

app.get('/', (req, res) => {
  res.send(`
    <!DOCTYPE html>
    <html>
      <head><title>Secure App</title></head>
      <body><h1>Hello World</h1></body>
    </html>
  `);
});

app.listen(3000);
```

### Example 2: Application with CDN Resources

```javascript
const express = require('express');
const helmet = require('helmet');

const app = express();

// Allow CDN resources
app.use(helmet({
  contentSecurityPolicy: {
    directives: {
      defaultSrc: ["'self'"],
      scriptSrc: [
        "'self'",
        "cdn.jsdelivr.net",
        "cdnjs.cloudflare.com"
      ],
      styleSrc: [
        "'self'",
        "'unsafe-inline'",
        "cdn.jsdelivr.net",
        "fonts.googleapis.com"
      ],
      fontSrc: [
        "'self'",
        "fonts.gstatic.com"
      ],
      imgSrc: [
        "'self'",
        "data:",
        "images.unsplash.com"
      ]
    }
  }
}));

app.get('/', (req, res) => {
  res.send(`
    <!DOCTYPE html>
    <html>
      <head>
        <link href="https://cdn.jsdelivr.net/npm/bootstrap@5/dist/css/bootstrap.min.css" rel="stylesheet">
      </head>
      <body>
        <h1>App with CDN</h1>
        <script src="https://cdn.jsdelivr.net/npm/bootstrap@5/dist/js/bootstrap.bundle.min.js"></script>
      </body>
    </html>
  `);
});

app.listen(3000);
```

### Example 3: API Server

```javascript
const express = require('express');
const helmet = require('helmet');
const cors = require('cors');

const app = express();

// API doesn't need CSP for HTML
app.use(helmet({
  contentSecurityPolicy: false,
  crossOriginEmbedderPolicy: false
}));

app.use(cors());
app.use(express.json());

app.get('/api/users', (req, res) => {
  res.json({ users: [] });
});

app.listen(3000);
```

### Example 4: Production Configuration

```javascript
const express = require('express');
const helmet = require('helmet');

const app = express();

// Production-ready Helmet config
app.use(helmet({
  contentSecurityPolicy: {
    directives: {
      defaultSrc: ["'self'"],
      baseUri: ["'self'"],
      fontSrc: ["'self'", "https:", "data:"],
      formAction: ["'self'"],
      frameAncestors: ["'none'"],
      imgSrc: ["'self'", "data:", "https:"],
      objectSrc: ["'none'"],
      scriptSrc: ["'self'"],
      scriptSrcAttr: ["'none'"],
      styleSrc: ["'self'", "https:", "'unsafe-inline'"],
      upgradeInsecureRequests: []
    }
  },
  hsts: {
    maxAge: 31536000,
    includeSubDomains: true,
    preload: true
  },
  frameguard: {
    action: 'deny'
  },
  referrerPolicy: {
    policy: 'strict-origin-when-cross-origin'
  }
}));

// Remove X-Powered-By separately for clarity
app.disable('x-powered-by');

app.listen(3000);
```

### Example 5: Development vs Production

```javascript
const express = require('express');
const helmet = require('helmet');

const app = express();

const isDevelopment = process.env.NODE_ENV === 'development';

if (isDevelopment) {
  // Relaxed security for development
  app.use(helmet({
    contentSecurityPolicy: false,
    crossOriginEmbedderPolicy: false
  }));
} else {
  // Strict security for production
  app.use(helmet({
    contentSecurityPolicy: {
      directives: {
        defaultSrc: ["'self'"],
        scriptSrc: ["'self'"],
        styleSrc: ["'self'", "'unsafe-inline'"],
        imgSrc: ["'self'", "data:", "https:"]
      }
    },
    hsts: {
      maxAge: 31536000,
      includeSubDomains: true,
      preload: true
    }
  }));
}

app.listen(3000);
```

## Best Practices

### 1. Always Use Helmet in Production

```javascript
// Minimum setup
app.use(helmet());
```

### 2. Customize CSP for Your Needs

```javascript
// Start strict, then relax as needed
app.use(helmet({
  contentSecurityPolicy: {
    directives: {
      defaultSrc: ["'self'"],
      // Add other sources only when necessary
      scriptSrc: ["'self'", "trusted-cdn.com"],
      styleSrc: ["'self'", "'unsafe-inline'"] // Avoid if possible
    }
  }
}));
```

### 3. Test Your CSP

```javascript
// Use report-only mode first
app.use(helmet({
  contentSecurityPolicy: {
    directives: {
      defaultSrc: ["'self'"]
    },
    reportOnly: true // Logs violations without blocking
  }
}));

// Then switch to enforcement
app.use(helmet({
  contentSecurityPolicy: {
    directives: {
      defaultSrc: ["'self'"]
    },
    reportOnly: false
  }
}));
```

### 4. Enable HSTS Carefully

```javascript
// Start with short max-age
app.use(helmet.hsts({
  maxAge: 86400 // 1 day
}));

// Gradually increase
app.use(helmet.hsts({
  maxAge: 31536000, // 1 year
  includeSubDomains: true,
  preload: true
}));
```

### 5. Monitor CSP Violations

```javascript
app.use(helmet({
  contentSecurityPolicy: {
    directives: {
      defaultSrc: ["'self'"],
      reportUri: '/csp-violation-report'
    }
  }
}));

app.post('/csp-violation-report', express.json({ type: 'application/csp-report' }), (req, res) => {
  console.log('CSP Violation:', req.body);
  res.status(204).send();
});
```

### 6. Document Your Configuration

```javascript
// config/helmet.js
module.exports = {
  contentSecurityPolicy: {
    directives: {
      defaultSrc: ["'self'"],
      // Allow Bootstrap CDN for styling
      styleSrc: ["'self'", "'unsafe-inline'", "cdn.jsdelivr.net"],
      // Allow Google Fonts
      fontSrc: ["'self'", "fonts.gstatic.com"],
      // Allow images from our CDN
      imgSrc: ["'self'", "data:", "cdn.example.com"]
    }
  },
  // Force HTTPS for 1 year
  hsts: {
    maxAge: 31536000,
    includeSubDomains: true
  }
};

// app.js
const helmetConfig = require('./config/helmet');
app.use(helmet(helmetConfig));
```

### 7. Combine with Other Security Measures

```javascript
const express = require('express');
const helmet = require('helmet');
const rateLimit = require('express-rate-limit');
const mongoSanitize = require('express-mongo-sanitize');

const app = express();

// Helmet for headers
app.use(helmet());

// Rate limiting
app.use(rateLimit({
  windowMs: 15 * 60 * 1000,
  max: 100
}));

// Data sanitization
app.use(mongoSanitize());
```

### 8. Regular Updates

```bash
# Check for updates
npm outdated helmet

# Update Helmet
npm update helmet

# Or use npm-check-updates
npx npm-check-updates -u helmet
npm install
```

## Common Issues and Solutions

### Issue 1: CSP Blocking Inline Scripts

**Problem:**
```javascript
// This won't work with strict CSP
res.send('<script>alert("Hello")</script>');
```

**Solution:**
```javascript
// Option 1: Use external scripts
res.send('<script src="/js/app.js"></script>');

// Option 2: Use nonces
app.use((req, res, next) => {
  res.locals.nonce = crypto.randomBytes(16).toString('base64');
  next();
});

app.use(helmet({
  contentSecurityPolicy: {
    directives: {
      scriptSrc: ["'self'", (req, res) => `'nonce-${res.locals.nonce}'`]
    }
  }
}));

res.send(`<script nonce="${res.locals.nonce}">alert("Hello")</script>`);
```

### Issue 2: HSTS Locking Out HTTP

**Problem:** Can't access site over HTTP after enabling HSTS

**Solution:**
```javascript
// Only enable HSTS in production with HTTPS
if (process.env.NODE_ENV === 'production') {
  app.use(helmet.hsts({
    maxAge: 31536000
  }));
}
```

### Issue 3: Frame-Ancestors Blocking Embeds

**Problem:** Need to allow specific sites to embed your content

**Solution:**
```javascript
app.use(helmet({
  contentSecurityPolicy: {
    directives: {
      frameAncestors: ["'self'", "trusted-site.com"]
    }
  }
}));
```

## Summary

Helmet is essential for securing Express applications:

✅ **Use Helmet** - One line provides comprehensive protection  
✅ **Customize CSP** - Tailor to your application's needs  
✅ **Test First** - Use report-only mode before enforcing  
✅ **Keep Updated** - Security standards evolve  
✅ **Combine Tools** - Use with other security middleware  
✅ **Monitor Violations** - Track and fix CSP issues  

Helmet is a must-have for any production Express application. It implements security best practices with minimal effort and provides strong protection against common web vulnerabilities.
