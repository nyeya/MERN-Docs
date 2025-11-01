# Morgan - HTTP Request Logger

## Table of Contents
- [What is Morgan?](#what-is-morgan)
- [Why Use Morgan?](#why-use-morgan)
- [Morgan vs Alternatives](#morgan-vs-alternatives)
- [Installation & Setup](#installation--setup)
- [Predefined Formats](#predefined-formats)
- [Custom Formats](#custom-formats)
- [Tokens](#tokens)
- [Integration](#integration)
- [Best Practices](#best-practices)

## What is Morgan?

Morgan is HTTP request logger middleware for Node.js. It simplifies the process of logging requests to your application, providing useful information about each request including method, URL, status code, response time, and more.

### Key Features
- **Simple Setup**: One line of code
- **Predefined Formats**: Ready-to-use log formats
- **Custom Formats**: Create your own format
- **Tokens**: Access request/response data
- **Stream Support**: Log to files or custom streams
- **Conditional Logging**: Log based on conditions
- **Lightweight**: Minimal overhead

## Why Use Morgan?

### 1. **Request Visibility**

**Without Morgan:**
```javascript
app.use((req, res, next) => {
  console.log(`${req.method} ${req.url}`);
  next();
});

// Output: GET /api/users
// Missing: status, response time, size, etc.
```

**With Morgan:**
```javascript
app.use(morgan('combined'));

// Output: ::1 - - [01/Jan/2024:12:00:00 +0000] "GET /api/users HTTP/1.1" 200 1234 "-" "Mozilla/5.0..."
// Includes: IP, timestamp, method, URL, status, size, referrer, user-agent
```

### 2. **Performance Monitoring**

```javascript
app.use(morgan('dev'));

// Output: GET /api/users 200 45.123 ms - 1234
// Shows response time for each request
```

### 3. **Debugging**

```javascript
// See exactly what requests are coming in
app.use(morgan('dev'));

// Output:
// GET /api/users 200 12.345 ms - 1234
// POST /api/users 201 45.678 ms - 567
// GET /api/users/123 404 5.432 ms - 89
```

## Morgan vs Alternatives

### Morgan vs Winston HTTP Logging

| Feature | Morgan | Winston |
|---------|--------|---------|
| **Purpose** | HTTP logging only | General logging |
| **Setup** | Simpler | More complex |
| **Formats** | HTTP-focused | General purpose |
| **Flexibility** | HTTP requests | Everything |
| **Performance** | Faster | Slightly slower |

**When to choose Morgan:**
- Only need HTTP logging
- Want simple setup
- Prefer specialized tool

**When to choose Winston:**
- Need general logging
- Want unified logging
- Complex logging requirements

**Best Practice:** Use both together
```javascript
// Morgan for HTTP requests
app.use(morgan('combined', { stream: winston.stream }));

// Winston for application logs
logger.info('Application started');
```

### Morgan vs Custom Middleware

| Feature | Morgan | Custom |
|---------|--------|--------|
| **Setup Time** | Instant | Hours |
| **Maintenance** | None | Ongoing |
| **Features** | Complete | Custom |
| **Testing** | Battle-tested | Need testing |

## Installation & Setup

### Installation

```bash
npm install morgan
```

### Basic Setup

```javascript
const express = require('express');
const morgan = require('morgan');

const app = express();

// Use Morgan
app.use(morgan('dev'));

app.get('/', (req, res) => {
  res.send('Hello World');
});

app.listen(3000);
```

### TypeScript Setup

```bash
npm install --save-dev @types/morgan
```

```typescript
import express from 'express';
import morgan from 'morgan';

const app = express();

app.use(morgan('dev'));
```

## Predefined Formats

### combined

```javascript
app.use(morgan('combined'));

// Output:
// ::1 - - [01/Jan/2024:12:00:00 +0000] "GET /api/users HTTP/1.1" 200 1234 "-" "Mozilla/5.0..."

// Format: :remote-addr - :remote-user [:date[clf]] ":method :url HTTP/:http-version" :status :res[content-length] ":referrer" ":user-agent"
```

### common

```javascript
app.use(morgan('common'));

// Output:
// ::1 - - [01/Jan/2024:12:00:00 +0000] "GET /api/users HTTP/1.1" 200 1234

// Format: :remote-addr - :remote-user [:date[clf]] ":method :url HTTP/:http-version" :status :res[content-length]
```

### dev

```javascript
app.use(morgan('dev'));

// Output:
// GET /api/users 200 12.345 ms - 1234

// Format: :method :url :status :response-time ms - :res[content-length]
// Colorized by status code
```

### short

```javascript
app.use(morgan('short'));

// Output:
// ::1 - GET /api/users HTTP/1.1 200 1234 - 12.345 ms

// Format: :remote-addr :remote-user :method :url HTTP/:http-version :status :res[content-length] - :response-time ms
```

### tiny

```javascript
app.use(morgan('tiny'));

// Output:
// GET /api/users 200 1234 - 12.345 ms

// Format: :method :url :status :res[content-length] - :response-time ms
```

## Custom Formats

### Simple Custom Format

```javascript
app.use(morgan(':method :url :status :response-time ms'));

// Output: GET /api/users 200 12.345 ms
```

### Detailed Custom Format

```javascript
app.use(morgan(':method :url :status :res[content-length] - :response-time ms [:date[clf]]'));

// Output: GET /api/users 200 1234 - 12.345 ms [01/Jan/2024:12:00:00 +0000]
```

### JSON Format

```javascript
morgan.token('body', (req) => JSON.stringify(req.body));

app.use(morgan((tokens, req, res) => {
  return JSON.stringify({
    method: tokens.method(req, res),
    url: tokens.url(req, res),
    status: tokens.status(req, res),
    responseTime: tokens['response-time'](req, res),
    contentLength: tokens.res(req, res, 'content-length'),
    timestamp: new Date().toISOString()
  });
}));

// Output: {"method":"GET","url":"/api/users","status":"200","responseTime":"12.345","contentLength":"1234","timestamp":"2024-01-01T12:00:00.000Z"}
```

## Tokens

### Built-in Tokens

```javascript
// :method - HTTP method
// :url - URL path
// :status - Response status code
// :response-time - Response time in milliseconds
// :res[header] - Response header
// :req[header] - Request header
// :http-version - HTTP version
// :remote-addr - Remote IP address
// :remote-user - Remote user
// :date[format] - Date in specified format
// :referrer - Referrer
// :user-agent - User agent
```

### Custom Tokens

```javascript
// Define custom token
morgan.token('id', (req) => req.id);
morgan.token('user', (req) => req.user?.email || 'anonymous');
morgan.token('body', (req) => JSON.stringify(req.body));

// Use custom tokens
app.use(morgan(':id :method :url :status :user'));

// Output: abc123 GET /api/users 200 user@example.com
```

### Conditional Tokens

```javascript
morgan.token('error', (req, res) => {
  return res.statusCode >= 400 ? 'ERROR' : 'OK';
});

app.use(morgan(':method :url :status :error'));

// Output: GET /api/users 200 OK
// Output: GET /api/users/999 404 ERROR
```

## Integration

### Log to File

```javascript
const fs = require('fs');
const path = require('path');

// Create write stream
const accessLogStream = fs.createWriteStream(
  path.join(__dirname, 'access.log'),
  { flags: 'a' }
);

// Log to file
app.use(morgan('combined', { stream: accessLogStream }));
```

### Log to Multiple Destinations

```javascript
const fs = require('fs');

const accessLogStream = fs.createWriteStream('access.log', { flags: 'a' });

// Log to console
app.use(morgan('dev'));

// Log to file
app.use(morgan('combined', { stream: accessLogStream }));
```

### Integration with Winston

```javascript
const winston = require('winston');
const morgan = require('morgan');

// Create Winston logger
const logger = winston.createLogger({
  transports: [
    new winston.transports.File({ filename: 'combined.log' })
  ]
});

// Create stream for Morgan
logger.stream = {
  write: (message) => logger.info(message.trim())
};

// Use Morgan with Winston
app.use(morgan('combined', { stream: logger.stream }));
```

### Conditional Logging

```javascript
// Only log errors
app.use(morgan('combined', {
  skip: (req, res) => res.statusCode < 400
}));

// Only log in production
app.use(morgan('combined', {
  skip: () => process.env.NODE_ENV !== 'production'
}));

// Only log specific routes
app.use(morgan('combined', {
  skip: (req) => req.url.startsWith('/health')
}));
```

### Immediate Logging

```javascript
// Log when request is received (not when response is sent)
app.use(morgan('dev', { immediate: true }));
```

## Best Practices

### 1. Environment-Based Configuration

```javascript
const isDevelopment = process.env.NODE_ENV === 'development';

if (isDevelopment) {
  // Detailed logs in development
  app.use(morgan('dev'));
} else {
  // Standard logs in production
  const accessLogStream = fs.createWriteStream(
    path.join(__dirname, 'logs', 'access.log'),
    { flags: 'a' }
  );
  app.use(morgan('combined', { stream: accessLogStream }));
}
```

### 2. Skip Health Checks

```javascript
app.use(morgan('combined', {
  skip: (req) => {
    return req.url === '/health' || req.url === '/metrics';
  }
}));
```

### 3. Log Rotation

```bash
npm install rotating-file-stream
```

```javascript
const rfs = require('rotating-file-stream');

const accessLogStream = rfs.createStream('access.log', {
  interval: '1d', // Rotate daily
  path: path.join(__dirname, 'logs'),
  maxFiles: 10
});

app.use(morgan('combined', { stream: accessLogStream }));
```

### 4. Custom Format for Production

```javascript
morgan.token('user-id', (req) => req.user?.id || 'anonymous');

const productionFormat = ':remote-addr - :user-id [:date[clf]] ":method :url HTTP/:http-version" :status :res[content-length] ":referrer" ":user-agent" :response-time ms';

app.use(morgan(productionFormat, {
  stream: accessLogStream
}));
```

### 5. Separate Error Logs

```javascript
const accessLogStream = fs.createWriteStream('access.log', { flags: 'a' });
const errorLogStream = fs.createWriteStream('error.log', { flags: 'a' });

// Log all requests
app.use(morgan('combined', { stream: accessLogStream }));

// Log only errors
app.use(morgan('combined', {
  stream: errorLogStream,
  skip: (req, res) => res.statusCode < 400
}));
```

### 6. Include Request ID

```javascript
const { v4: uuidv4 } = require('uuid');

// Add request ID middleware
app.use((req, res, next) => {
  req.id = uuidv4();
  next();
});

// Create custom token
morgan.token('id', (req) => req.id);

// Use in format
app.use(morgan(':id :method :url :status :response-time ms'));
```

## Complete Examples

### Example 1: Development Setup

```javascript
const express = require('express');
const morgan = require('morgan');

const app = express();

// Development logging
if (process.env.NODE_ENV === 'development') {
  app.use(morgan('dev'));
}

app.get('/', (req, res) => {
  res.send('Hello World');
});

app.listen(3000);
```

### Example 2: Production Setup

```javascript
const express = require('express');
const morgan = require('morgan');
const fs = require('fs');
const path = require('path');
const rfs = require('rotating-file-stream');

const app = express();

// Create logs directory
const logsDir = path.join(__dirname, 'logs');
if (!fs.existsSync(logsDir)) {
  fs.mkdirSync(logsDir);
}

// Create rotating write stream
const accessLogStream = rfs.createStream('access.log', {
  interval: '1d',
  path: logsDir,
  maxFiles: 30
});

const errorLogStream = rfs.createStream('error.log', {
  interval: '1d',
  path: logsDir,
  maxFiles: 30
});

// Custom tokens
morgan.token('user-id', (req) => req.user?.id || 'anonymous');

// Log all requests
app.use(morgan('combined', {
  stream: accessLogStream,
  skip: (req) => req.url === '/health'
}));

// Log errors
app.use(morgan('combined', {
  stream: errorLogStream,
  skip: (req, res) => res.statusCode < 400
}));

app.listen(3000);
```

### Example 3: With Winston

```javascript
const express = require('express');
const morgan = require('morgan');
const winston = require('winston');

const app = express();

// Winston logger
const logger = winston.createLogger({
  level: 'info',
  format: winston.format.json(),
  transports: [
    new winston.transports.File({ filename: 'error.log', level: 'error' }),
    new winston.transports.File({ filename: 'combined.log' })
  ]
});

// Console in development
if (process.env.NODE_ENV !== 'production') {
  logger.add(new winston.transports.Console({
    format: winston.format.simple()
  }));
}

// Morgan stream
logger.stream = {
  write: (message) => logger.http(message.trim())
};

// Use Morgan with Winston
app.use(morgan('combined', { stream: logger.stream }));

app.listen(3000);
```

### Example 4: Custom JSON Format

```javascript
const express = require('express');
const morgan = require('morgan');

const app = express();

// Custom JSON format
morgan.token('body', (req) => JSON.stringify(req.body));

app.use(morgan((tokens, req, res) => {
  const log = {
    timestamp: new Date().toISOString(),
    method: tokens.method(req, res),
    url: tokens.url(req, res),
    status: Number(tokens.status(req, res)),
    responseTime: Number(tokens['response-time'](req, res)),
    contentLength: tokens.res(req, res, 'content-length'),
    userAgent: tokens['user-agent'](req, res),
    ip: tokens['remote-addr'](req, res)
  };
  
  return JSON.stringify(log);
}));

app.listen(3000);
```

### Example 5: Complete Application

```javascript
const express = require('express');
const morgan = require('morgan');
const winston = require('winston');
const rfs = require('rotating-file-stream');
const path = require('path');
const { v4: uuidv4 } = require('uuid');

const app = express();

// Winston logger
const logger = winston.createLogger({
  level: 'info',
  format: winston.format.combine(
    winston.format.timestamp(),
    winston.format.json()
  ),
  transports: [
    new winston.transports.File({ filename: 'logs/error.log', level: 'error' }),
    new winston.transports.File({ filename: 'logs/combined.log' })
  ]
});

// Console in development
if (process.env.NODE_ENV !== 'production') {
  logger.add(new winston.transports.Console({
    format: winston.format.simple()
  }));
}

// Request ID middleware
app.use((req, res, next) => {
  req.id = uuidv4();
  next();
});

// Custom Morgan tokens
morgan.token('id', (req) => req.id);
morgan.token('user-id', (req) => req.user?.id || 'anonymous');

// Morgan format
const morganFormat = ':id :remote-addr - :user-id [:date[clf]] ":method :url HTTP/:http-version" :status :res[content-length] ":referrer" ":user-agent" :response-time ms';

// Development: console logging
if (process.env.NODE_ENV === 'development') {
  app.use(morgan('dev'));
}

// Production: file logging
if (process.env.NODE_ENV === 'production') {
  const accessLogStream = rfs.createStream('access.log', {
    interval: '1d',
    path: path.join(__dirname, 'logs'),
    maxFiles: 30
  });
  
  app.use(morgan(morganFormat, {
    stream: accessLogStream,
    skip: (req) => req.url === '/health'
  }));
}

// Always log to Winston
logger.stream = {
  write: (message) => logger.http(message.trim())
};

app.use(morgan(morganFormat, {
  stream: logger.stream,
  skip: (req) => req.url === '/health'
}));

// Routes
app.get('/', (req, res) => {
  res.send('Hello World');
});

app.get('/health', (req, res) => {
  res.json({ status: 'ok' });
});

app.listen(3000, () => {
  logger.info('Server started on port 3000');
});
```

## Summary

Morgan is the standard HTTP request logger for Express:

✅ **Simple** - One line setup  
✅ **Flexible** - Predefined and custom formats  
✅ **Efficient** - Minimal performance impact  
✅ **Informative** - Complete request details  
✅ **Integrable** - Works with Winston and files  

**Key Takeaways:**
- Use 'dev' format in development
- Use 'combined' format in production
- Log to files in production
- Skip health check endpoints
- Implement log rotation
- Integrate with Winston for unified logging
- Add custom tokens for app-specific data
- Use conditional logging to filter noise

Morgan provides essential HTTP request logging for Express applications.
