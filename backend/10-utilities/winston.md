# Winston - Professional Logging Library

## Table of Contents
- [What is Winston?](#what-is-winston)
- [Why Use Winston?](#why-use-winston)
- [Winston vs Alternatives](#winston-vs-alternatives)
- [Installation & Setup](#installation--setup)
- [Log Levels](#log-levels)
- [Transports](#transports)
- [Formatting](#formatting)
- [Error Handling](#error-handling)
- [Best Practices](#best-practices)
- [Real-World Examples](#real-world-examples)

## What is Winston?

Winston is a versatile logging library for Node.js designed to be simple, universal, and extremely flexible. It supports multiple transports (storage/output methods), custom log levels, and various formatting options.

### Key Features
- **Multiple Transports**: Console, file, HTTP, databases, etc.
- **Log Levels**: Customizable severity levels
- **Formatting**: JSON, colorized, custom formats
- **Profiling**: Performance measurement
- **Query**: Search through logs
- **Streaming**: Real-time log streaming
- **Exception Handling**: Catch uncaught exceptions

## Why Use Winston?

### 1. **Better Than console.log**

**With console.log:**
```javascript
console.log('User logged in');
console.log('Error:', error);
console.log('Debug info:', data);

// Problems:
// - No log levels
// - No timestamps
// - No file output
// - No filtering
// - Hard to search
```

**With Winston:**
```javascript
logger.info('User logged in');
logger.error('Error:', error);
logger.debug('Debug info:', data);

// Benefits:
// - Structured logs
// - Automatic timestamps
// - Multiple outputs
// - Filterable by level
// - Searchable
```

### 2. **Multiple Outputs**

```javascript
// Log to console AND file simultaneously
const logger = winston.createLogger({
  transports: [
    new winston.transports.Console(),
    new winston.transports.File({ filename: 'app.log' })
  ]
});

// Different files for different levels
const logger = winston.createLogger({
  transports: [
    new winston.transports.File({ filename: 'error.log', level: 'error' }),
    new winston.transports.File({ filename: 'combined.log' })
  ]
});
```

### 3. **Production-Ready**

```javascript
// Automatic log rotation
const logger = winston.createLogger({
  transports: [
    new winston.transports.File({
      filename: 'app.log',
      maxsize: 5242880, // 5MB
      maxFiles: 5
    })
  ]
});

// Error tracking
logger.exceptions.handle(
  new winston.transports.File({ filename: 'exceptions.log' })
);
```

### 4. **Structured Logging**

```javascript
logger.info('User action', {
  userId: 123,
  action: 'login',
  ip: '192.168.1.1',
  timestamp: Date.now()
});

// Easy to parse and analyze
// Perfect for log aggregation tools
```

## Winston vs Alternatives

### Winston vs console.log

| Feature | Winston | console.log |
|---------|---------|-------------|
| **Log Levels** | Yes | No |
| **Timestamps** | Automatic | Manual |
| **File Output** | Built-in | Manual |
| **Formatting** | Flexible | Limited |
| **Filtering** | Yes | No |
| **Production** | Yes | No |

### Winston vs Pino

| Feature | Winston | Pino |
|---------|---------|------|
| **Performance** | Good | Excellent |
| **Flexibility** | Very flexible | Less flexible |
| **Transports** | Many built-in | Separate packages |
| **Ecosystem** | Large | Growing |
| **Learning Curve** | Moderate | Easy |

**When to choose Winston:**
- Need maximum flexibility
- Want many built-in transports
- Complex logging requirements
- Established ecosystem

**When to choose Pino:**
- Performance critical
- Simple use case
- Prefer JSON logs
- Minimal overhead

### Winston vs Bunyan

| Feature | Winston | Bunyan |
|---------|---------|--------|
| **Active** | Very active | Less active |
| **Flexibility** | More flexible | More opinionated |
| **JSON** | Optional | Required |
| **CLI** | Basic | Advanced |

## Installation & Setup

### Installation

```bash
npm install winston
```

### Basic Setup

```javascript
const winston = require('winston');

const logger = winston.createLogger({
  level: 'info',
  format: winston.format.json(),
  transports: [
    new winston.transports.Console(),
    new winston.transports.File({ filename: 'app.log' })
  ]
});

logger.info('Hello Winston!');
```

### TypeScript Setup

```bash
npm install --save-dev @types/winston
```

```typescript
import winston from 'winston';

const logger = winston.createLogger({
  level: 'info',
  format: winston.format.json(),
  transports: [
    new winston.transports.Console()
  ]
});
```

## Log Levels

### Default Levels

```javascript
// npm log levels (default)
{
  error: 0,
  warn: 1,
  info: 2,
  http: 3,
  verbose: 4,
  debug: 5,
  silly: 6
}

// Usage
logger.error('Error message');
logger.warn('Warning message');
logger.info('Info message');
logger.http('HTTP request');
logger.verbose('Verbose message');
logger.debug('Debug message');
logger.silly('Silly message');
```

### Custom Levels

```javascript
const customLevels = {
  levels: {
    fatal: 0,
    error: 1,
    warn: 2,
    info: 3,
    debug: 4,
    trace: 5
  },
  colors: {
    fatal: 'red',
    error: 'red',
    warn: 'yellow',
    info: 'green',
    debug: 'blue',
    trace: 'gray'
  }
};

const logger = winston.createLogger({
  levels: customLevels.levels,
  transports: [
    new winston.transports.Console()
  ]
});

winston.addColors(customLevels.colors);
```

### Level Filtering

```javascript
// Only log 'info' and above
const logger = winston.createLogger({
  level: 'info',
  transports: [
    new winston.transports.Console()
  ]
});

// Different levels for different transports
const logger = winston.createLogger({
  transports: [
    new winston.transports.Console({ level: 'debug' }),
    new winston.transports.File({ filename: 'error.log', level: 'error' }),
    new winston.transports.File({ filename: 'combined.log', level: 'info' })
  ]
});
```

## Transports

### Console Transport

```javascript
new winston.transports.Console({
  level: 'info',
  format: winston.format.combine(
    winston.format.colorize(),
    winston.format.simple()
  )
});
```

### File Transport

```javascript
new winston.transports.File({
  filename: 'app.log',
  level: 'info',
  maxsize: 5242880, // 5MB
  maxFiles: 5,
  tailable: true
});
```

### Multiple Files

```javascript
const logger = winston.createLogger({
  transports: [
    // Error logs
    new winston.transports.File({
      filename: 'logs/error.log',
      level: 'error',
      maxsize: 5242880,
      maxFiles: 5
    }),
    // Combined logs
    new winston.transports.File({
      filename: 'logs/combined.log',
      maxsize: 5242880,
      maxFiles: 5
    }),
    // Console
    new winston.transports.Console({
      format: winston.format.simple()
    })
  ]
});
```

### HTTP Transport

```javascript
new winston.transports.Http({
  host: 'localhost',
  port: 8080,
  path: '/logs'
});
```

### Daily Rotate File

```bash
npm install winston-daily-rotate-file
```

```javascript
const DailyRotateFile = require('winston-daily-rotate-file');

const transport = new DailyRotateFile({
  filename: 'logs/application-%DATE%.log',
  datePattern: 'YYYY-MM-DD',
  maxSize: '20m',
  maxFiles: '14d'
});

const logger = winston.createLogger({
  transports: [transport]
});
```

### MongoDB Transport

```bash
npm install winston-mongodb
```

```javascript
require('winston-mongodb');

const logger = winston.createLogger({
  transports: [
    new winston.transports.MongoDB({
      db: 'mongodb://localhost:27017/logs',
      collection: 'application_logs',
      level: 'info'
    })
  ]
});
```

## Formatting

### Built-in Formats

```javascript
const { format } = winston;

// JSON format
format.json()

// Simple format
format.simple()

// Colorize
format.colorize()

// Timestamp
format.timestamp()

// Pretty print
format.prettyPrint()

// Align
format.align()

// Errors
format.errors({ stack: true })
```

### Combine Formats

```javascript
const logger = winston.createLogger({
  format: format.combine(
    format.timestamp({ format: 'YYYY-MM-DD HH:mm:ss' }),
    format.errors({ stack: true }),
    format.splat(),
    format.json()
  ),
  transports: [
    new winston.transports.File({ filename: 'app.log' })
  ]
});
```

### Custom Format

```javascript
const customFormat = format.printf(({ level, message, timestamp, ...metadata }) => {
  let msg = `${timestamp} [${level}]: ${message}`;
  
  if (Object.keys(metadata).length > 0) {
    msg += ` ${JSON.stringify(metadata)}`;
  }
  
  return msg;
});

const logger = winston.createLogger({
  format: format.combine(
    format.timestamp(),
    customFormat
  ),
  transports: [
    new winston.transports.Console()
  ]
});
```

### Colorized Console

```javascript
const logger = winston.createLogger({
  format: format.combine(
    format.colorize(),
    format.timestamp({ format: 'HH:mm:ss' }),
    format.printf(({ timestamp, level, message }) => {
      return `${timestamp} ${level}: ${message}`;
    })
  ),
  transports: [
    new winston.transports.Console()
  ]
});
```

### Production vs Development

```javascript
const isDevelopment = process.env.NODE_ENV === 'development';

const logger = winston.createLogger({
  level: isDevelopment ? 'debug' : 'info',
  format: format.combine(
    format.timestamp(),
    isDevelopment
      ? format.combine(
          format.colorize(),
          format.simple()
        )
      : format.json()
  ),
  transports: [
    new winston.transports.Console()
  ]
});
```

## Error Handling

### Exception Handling

```javascript
const logger = winston.createLogger({
  transports: [
    new winston.transports.File({ filename: 'app.log' })
  ],
  exceptionHandlers: [
    new winston.transports.File({ filename: 'exceptions.log' })
  ]
});

// Or add later
logger.exceptions.handle(
  new winston.transports.File({ filename: 'exceptions.log' })
);
```

### Rejection Handling

```javascript
const logger = winston.createLogger({
  transports: [
    new winston.transports.File({ filename: 'app.log' })
  ],
  rejectionHandlers: [
    new winston.transports.File({ filename: 'rejections.log' })
  ]
});
```

### Exit on Error

```javascript
const logger = winston.createLogger({
  exitOnError: false, // Don't exit on handled exceptions
  transports: [
    new winston.transports.File({ filename: 'app.log' })
  ]
});
```

## Best Practices

### 1. Centralized Logger

```javascript
// logger.js
const winston = require('winston');

const logger = winston.createLogger({
  level: process.env.LOG_LEVEL || 'info',
  format: winston.format.combine(
    winston.format.timestamp(),
    winston.format.errors({ stack: true }),
    winston.format.json()
  ),
  defaultMeta: { service: 'my-app' },
  transports: [
    new winston.transports.File({ filename: 'logs/error.log', level: 'error' }),
    new winston.transports.File({ filename: 'logs/combined.log' })
  ]
});

if (process.env.NODE_ENV !== 'production') {
  logger.add(new winston.transports.Console({
    format: winston.format.combine(
      winston.format.colorize(),
      winston.format.simple()
    )
  }));
}

module.exports = logger;

// Usage in other files
const logger = require('./logger');
logger.info('Application started');
```

### 2. Structured Logging

```javascript
// Good - Structured
logger.info('User login', {
  userId: user.id,
  email: user.email,
  ip: req.ip,
  userAgent: req.get('user-agent')
});

// Bad - Unstructured
logger.info(`User ${user.email} logged in from ${req.ip}`);
```

### 3. Log Metadata

```javascript
const logger = winston.createLogger({
  defaultMeta: {
    service: 'my-app',
    environment: process.env.NODE_ENV,
    version: process.env.APP_VERSION
  },
  transports: [
    new winston.transports.File({ filename: 'app.log' })
  ]
});

// All logs will include defaultMeta
logger.info('User action', { userId: 123 });
// { service: 'my-app', environment: 'production', version: '1.0.0', userId: 123, message: 'User action' }
```

### 4. Child Loggers

```javascript
const logger = winston.createLogger({
  transports: [
    new winston.transports.Console()
  ]
});

// Create child logger with additional metadata
const userLogger = logger.child({ userId: 123 });
userLogger.info('User action'); // Includes userId: 123

const requestLogger = logger.child({ requestId: 'abc-123' });
requestLogger.info('Request processed'); // Includes requestId: 'abc-123'
```

### 5. Express Integration

```javascript
const express = require('express');
const logger = require('./logger');

const app = express();

// Request logging middleware
app.use((req, res, next) => {
  const start = Date.now();
  
  res.on('finish', () => {
    const duration = Date.now() - start;
    logger.info('HTTP Request', {
      method: req.method,
      url: req.url,
      status: res.statusCode,
      duration,
      ip: req.ip,
      userAgent: req.get('user-agent')
    });
  });
  
  next();
});

// Error logging middleware
app.use((err, req, res, next) => {
  logger.error('Express Error', {
    error: err.message,
    stack: err.stack,
    method: req.method,
    url: req.url
  });
  
  res.status(500).json({ error: 'Internal server error' });
});
```

### 6. Environment-Based Configuration

```javascript
// config/logger.js
const winston = require('winston');

const getLogger = () => {
  const isDevelopment = process.env.NODE_ENV === 'development';
  const isProduction = process.env.NODE_ENV === 'production';
  
  const transports = [];
  
  // Console in development
  if (isDevelopment) {
    transports.push(
      new winston.transports.Console({
        format: winston.format.combine(
          winston.format.colorize(),
          winston.format.simple()
        )
      })
    );
  }
  
  // Files in production
  if (isProduction) {
    transports.push(
      new winston.transports.File({
        filename: 'logs/error.log',
        level: 'error',
        maxsize: 5242880,
        maxFiles: 5
      }),
      new winston.transports.File({
        filename: 'logs/combined.log',
        maxsize: 5242880,
        maxFiles: 5
      })
    );
  }
  
  return winston.createLogger({
    level: process.env.LOG_LEVEL || (isDevelopment ? 'debug' : 'info'),
    format: winston.format.combine(
      winston.format.timestamp(),
      winston.format.errors({ stack: true }),
      isProduction ? winston.format.json() : winston.format.simple()
    ),
    transports,
    exceptionHandlers: isProduction ? [
      new winston.transports.File({ filename: 'logs/exceptions.log' })
    ] : []
  });
};

module.exports = getLogger();
```

## Real-World Examples

### Example 1: Complete Application Logger

```javascript
// logger.js
const winston = require('winston');
const DailyRotateFile = require('winston-daily-rotate-file');

const { combine, timestamp, errors, json, printf, colorize } = winston.format;

// Custom format for console
const consoleFormat = printf(({ level, message, timestamp, ...metadata }) => {
  let msg = `${timestamp} [${level}]: ${message}`;
  
  if (Object.keys(metadata).length > 0) {
    msg += `\n${JSON.stringify(metadata, null, 2)}`;
  }
  
  return msg;
});

// Create logger
const logger = winston.createLogger({
  level: process.env.LOG_LEVEL || 'info',
  format: combine(
    timestamp({ format: 'YYYY-MM-DD HH:mm:ss' }),
    errors({ stack: true }),
    json()
  ),
  defaultMeta: {
    service: 'my-app',
    environment: process.env.NODE_ENV
  },
  transports: [
    // Error logs
    new DailyRotateFile({
      filename: 'logs/error-%DATE%.log',
      datePattern: 'YYYY-MM-DD',
      level: 'error',
      maxSize: '20m',
      maxFiles: '14d'
    }),
    // Combined logs
    new DailyRotateFile({
      filename: 'logs/combined-%DATE%.log',
      datePattern: 'YYYY-MM-DD',
      maxSize: '20m',
      maxFiles: '14d'
    })
  ],
  exceptionHandlers: [
    new DailyRotateFile({
      filename: 'logs/exceptions-%DATE%.log',
      datePattern: 'YYYY-MM-DD',
      maxSize: '20m',
      maxFiles: '14d'
    })
  ],
  rejectionHandlers: [
    new DailyRotateFile({
      filename: 'logs/rejections-%DATE%.log',
      datePattern: 'YYYY-MM-DD',
      maxSize: '20m',
      maxFiles: '14d'
    })
  ]
});

// Console in development
if (process.env.NODE_ENV !== 'production') {
  logger.add(new winston.transports.Console({
    format: combine(
      colorize(),
      timestamp({ format: 'HH:mm:ss' }),
      consoleFormat
    )
  }));
}

module.exports = logger;
```

### Example 2: Express Middleware

```javascript
// middleware/logger.js
const logger = require('../logger');

// Request logger
exports.requestLogger = (req, res, next) => {
  const start = Date.now();
  const requestId = req.id || Math.random().toString(36).substr(2, 9);
  
  req.logger = logger.child({ requestId });
  
  res.on('finish', () => {
    const duration = Date.now() - start;
    const logData = {
      method: req.method,
      url: req.url,
      status: res.statusCode,
      duration,
      ip: req.ip,
      userAgent: req.get('user-agent'),
      userId: req.user?.id
    };
    
    if (res.statusCode >= 400) {
      req.logger.warn('HTTP Request', logData);
    } else {
      req.logger.info('HTTP Request', logData);
    }
  });
  
  next();
};

// Error logger
exports.errorLogger = (err, req, res, next) => {
  req.logger.error('Express Error', {
    error: err.message,
    stack: err.stack,
    method: req.method,
    url: req.url,
    body: req.body,
    params: req.params,
    query: req.query
  });
  
  next(err);
};

// Usage in app.js
const { requestLogger, errorLogger } = require('./middleware/logger');

app.use(requestLogger);
// ... routes ...
app.use(errorLogger);
```

### Example 3: Database Query Logging

```javascript
// database.js
const logger = require('./logger');

class Database {
  async query(sql, params) {
    const start = Date.now();
    
    try {
      const result = await this.pool.query(sql, params);
      const duration = Date.now() - start;
      
      logger.debug('Database Query', {
        sql,
        params,
        duration,
        rows: result.rowCount
      });
      
      return result;
    } catch (error) {
      logger.error('Database Error', {
        error: error.message,
        sql,
        params
      });
      throw error;
    }
  }
}
```

### Example 4: Service Layer Logging

```javascript
// services/userService.js
const logger = require('../logger');

class UserService {
  async createUser(userData) {
    logger.info('Creating user', { email: userData.email });
    
    try {
      const user = await User.create(userData);
      
      logger.info('User created successfully', {
        userId: user.id,
        email: user.email
      });
      
      return user;
    } catch (error) {
      logger.error('Failed to create user', {
        error: error.message,
        email: userData.email
      });
      throw error;
    }
  }
  
  async deleteUser(userId) {
    logger.warn('Deleting user', { userId });
    
    try {
      await User.delete(userId);
      logger.info('User deleted', { userId });
    } catch (error) {
      logger.error('Failed to delete user', {
        error: error.message,
        userId
      });
      throw error;
    }
  }
}
```

## Summary

Winston is the professional logging solution for Node.js:

✅ **Flexible** - Multiple transports and formats  
✅ **Production-Ready** - Log rotation, error handling  
✅ **Structured** - JSON logging for analysis  
✅ **Powerful** - Levels, filtering, metadata  
✅ **Standard** - Industry-standard logging  

**Key Takeaways:**
- Use structured logging with metadata
- Configure different transports for different environments
- Implement log rotation for production
- Use appropriate log levels
- Create child loggers for context
- Integrate with Express middleware
- Handle exceptions and rejections
- Never log sensitive data (passwords, tokens)

Winston provides enterprise-grade logging for Node.js applications.
