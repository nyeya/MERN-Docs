# Nodemon - Development Auto-Restart

## Table of Contents
- [What is Nodemon?](#what-is-nodemon)
- [Why Use Nodemon?](#why-use-nodemon)
- [Installation & Setup](#installation--setup)
- [Basic Usage](#basic-usage)
- [Configuration](#configuration)
- [Advanced Features](#advanced-features)
- [Best Practices](#best-practices)

## What is Nodemon?

Nodemon is a utility that monitors for changes in your Node.js application and automatically restarts the server. It's an essential development tool that eliminates the need to manually stop and restart your server after every code change.

### Key Features
- **Auto-Restart**: Automatically restarts on file changes
- **Watch Specific Files**: Monitor only what you need
- **Ignore Files**: Exclude files/folders from watching
- **Execute Any Script**: Not just Node.js
- **Delay Restart**: Configurable restart delay
- **Event Hooks**: Run commands on events

## Why Use Nodemon?

### 1. **Eliminates Manual Restarts**

**Without Nodemon:**
```bash
# Start server
node server.js

# Make code change
# Ctrl+C to stop server
# node server.js to restart
# Repeat for every change... 😫
```

**With Nodemon:**
```bash
# Start with nodemon
nodemon server.js

# Make code changes
# Server automatically restarts! 🎉
# Keep coding without interruption
```

### 2. **Faster Development**

```javascript
// Save time on every change
// Without nodemon: ~10 seconds per restart
// With nodemon: ~1 second automatic restart

// Over 100 changes per day:
// Without: 16+ minutes wasted
// With: Instant, no time wasted
```

### 3. **Better Developer Experience**

```bash
# Clear feedback
[nodemon] starting `node server.js`
Server running on port 3000

[nodemon] restarting due to changes...
[nodemon] starting `node server.js`
Server running on port 3000

# Know exactly when server is ready
```

## Installation & Setup

### Global Installation

```bash
# Install globally
npm install -g nodemon

# Use anywhere
nodemon server.js
```

### Local Installation (Recommended)

```bash
# Install as dev dependency
npm install --save-dev nodemon

# Add to package.json scripts
```

**package.json:**
```json
{
  "scripts": {
    "start": "node server.js",
    "dev": "nodemon server.js"
  },
  "devDependencies": {
    "nodemon": "^3.0.0"
  }
}
```

```bash
# Run in development
npm run dev
```

## Basic Usage

### Simple Start

```bash
# Start with nodemon
nodemon server.js

# Or with npm script
npm run dev
```

### With Arguments

```bash
# Pass arguments to your script
nodemon server.js --port 3000

# Pass arguments to node
nodemon --inspect server.js
```

### Execute Different Files

```bash
# JavaScript
nodemon app.js

# TypeScript (with ts-node)
nodemon --exec ts-node src/server.ts

# ES Modules
nodemon --experimental-modules server.mjs

# Any executable
nodemon --exec python script.py
```

### Watch Specific Extensions

```bash
# Watch .js and .json files
nodemon --ext js,json server.js

# Watch all files
nodemon --ext '*' server.js
```

## Configuration

### Command Line Options

```bash
# Watch specific directory
nodemon --watch src server.js

# Ignore specific files
nodemon --ignore tests/ --ignore '*.test.js' server.js

# Delay restart
nodemon --delay 2.5 server.js

# Verbose output
nodemon --verbose server.js

# Legacy watch (for network drives)
nodemon --legacy-watch server.js

# Execute command
nodemon --exec "npm test"
```

### Configuration File

**nodemon.json:**
```json
{
  "watch": ["src", "config"],
  "ext": "js,json",
  "ignore": ["src/**/*.test.js", "node_modules"],
  "delay": 2500,
  "env": {
    "NODE_ENV": "development",
    "PORT": "3000"
  },
  "verbose": true,
  "execMap": {
    "js": "node --harmony"
  },
  "events": {
    "restart": "echo 'Server restarted!'",
    "crash": "echo 'Server crashed!'"
  }
}
```

### Package.json Configuration

```json
{
  "name": "my-app",
  "scripts": {
    "dev": "nodemon"
  },
  "nodemonConfig": {
    "watch": ["src"],
    "ext": "js,json",
    "ignore": ["src/**/*.test.js"],
    "delay": 1000,
    "env": {
      "NODE_ENV": "development"
    }
  }
}
```

### Environment-Specific Config

```json
{
  "scripts": {
    "dev": "nodemon --config nodemon.dev.json",
    "dev:debug": "nodemon --config nodemon.debug.json"
  }
}
```

**nodemon.dev.json:**
```json
{
  "watch": ["src"],
  "ext": "js,json",
  "env": {
    "NODE_ENV": "development",
    "DEBUG": "app:*"
  }
}
```

**nodemon.debug.json:**
```json
{
  "watch": ["src"],
  "ext": "js,json",
  "exec": "node --inspect",
  "env": {
    "NODE_ENV": "development",
    "DEBUG": "*"
  }
}
```

## Advanced Features

### Watch Specific Directories

```json
{
  "watch": [
    "src",
    "config",
    "routes",
    "models"
  ],
  "ignore": [
    "src/**/*.test.js",
    "src/temp/**"
  ]
}
```

### Custom Extensions

```json
{
  "ext": "js,ts,json,graphql",
  "execMap": {
    "ts": "ts-node"
  }
}
```

### Delay Restart

```json
{
  "delay": 2500,
  "restartable": "rs"
}
```

```bash
# Type 'rs' and press Enter to manually restart
rs
```

### Event Hooks

```json
{
  "events": {
    "start": "echo 'Starting...'",
    "restart": "echo 'Restarting...'",
    "crash": "echo 'Crashed!' && npm run notify",
    "exit": "echo 'Exiting...'"
  }
}
```

### Execute Scripts on Events

```json
{
  "events": {
    "restart": "npm run lint",
    "crash": "npm run send-alert"
  }
}
```

### Multiple Processes

```bash
# Run multiple processes
nodemon --exec "npm run server & npm run worker"

# Or use concurrently
npm install --save-dev concurrently
```

**package.json:**
```json
{
  "scripts": {
    "server": "nodemon server.js",
    "worker": "nodemon worker.js",
    "dev": "concurrently \"npm run server\" \"npm run worker\""
  }
}
```

## Best Practices

### 1. Use Local Installation

```json
{
  "scripts": {
    "dev": "nodemon server.js"
  },
  "devDependencies": {
    "nodemon": "^3.0.0"
  }
}
```

### 2. Configure in package.json

```json
{
  "nodemonConfig": {
    "watch": ["src"],
    "ext": "js,json",
    "ignore": ["**/*.test.js", "node_modules"],
    "env": {
      "NODE_ENV": "development"
    }
  }
}
```

### 3. Ignore Unnecessary Files

```json
{
  "ignore": [
    "**/*.test.js",
    "**/*.spec.js",
    "node_modules",
    "dist",
    "build",
    "coverage",
    "*.log",
    ".git"
  ]
}
```

### 4. Watch Only Source Files

```json
{
  "watch": ["src", "config"],
  "ignore": ["src/**/*.test.js"]
}
```

### 5. Use Appropriate Delay

```json
{
  "delay": 1000
}
```

### 6. Environment Variables

```json
{
  "env": {
    "NODE_ENV": "development",
    "DEBUG": "app:*",
    "PORT": "3000"
  }
}
```

### 7. TypeScript Configuration

```json
{
  "watch": ["src"],
  "ext": "ts",
  "ignore": ["src/**/*.spec.ts"],
  "exec": "ts-node",
  "env": {
    "TS_NODE_PROJECT": "tsconfig.json"
  }
}
```

### 8. Debugging Configuration

```json
{
  "scripts": {
    "dev": "nodemon",
    "dev:debug": "nodemon --inspect"
  },
  "nodemonConfig": {
    "watch": ["src"],
    "ext": "js"
  }
}
```

## Common Patterns

### Pattern 1: Express Development

```json
{
  "scripts": {
    "start": "node src/server.js",
    "dev": "nodemon src/server.js"
  },
  "nodemonConfig": {
    "watch": ["src"],
    "ext": "js,json",
    "ignore": ["src/**/*.test.js"],
    "env": {
      "NODE_ENV": "development",
      "PORT": "3000"
    }
  }
}
```

### Pattern 2: TypeScript Project

```json
{
  "scripts": {
    "dev": "nodemon"
  },
  "nodemonConfig": {
    "watch": ["src"],
    "ext": "ts",
    "ignore": ["src/**/*.spec.ts"],
    "exec": "ts-node src/server.ts",
    "env": {
      "NODE_ENV": "development"
    }
  }
}
```

### Pattern 3: With Build Step

```json
{
  "scripts": {
    "build": "tsc",
    "dev": "nodemon --exec 'npm run build && node dist/server.js'"
  }
}
```

### Pattern 4: Multiple Environments

```json
{
  "scripts": {
    "dev": "nodemon --config nodemon.dev.json",
    "dev:prod": "nodemon --config nodemon.prod.json"
  }
}
```

**nodemon.dev.json:**
```json
{
  "watch": ["src"],
  "ext": "js",
  "env": {
    "NODE_ENV": "development",
    "DEBUG": "*"
  }
}
```

**nodemon.prod.json:**
```json
{
  "watch": ["src"],
  "ext": "js",
  "env": {
    "NODE_ENV": "production"
  }
}
```

### Pattern 5: With Docker

**Dockerfile:**
```dockerfile
FROM node:18

WORKDIR /app

COPY package*.json ./
RUN npm install

COPY . .

# Use nodemon in development
CMD ["npm", "run", "dev"]
```

**docker-compose.yml:**
```yaml
version: '3.8'

services:
  app:
    build: .
    ports:
      - "3000:3000"
    volumes:
      - .:/app
      - /app/node_modules
    environment:
      - NODE_ENV=development
    command: npm run dev
```

### Pattern 6: With Linting

```json
{
  "scripts": {
    "dev": "nodemon",
    "lint": "eslint src"
  },
  "nodemonConfig": {
    "watch": ["src"],
    "ext": "js",
    "events": {
      "restart": "npm run lint"
    }
  }
}
```

## Complete Example

**Project Structure:**
```
my-app/
├── src/
│   ├── server.js
│   ├── routes/
│   ├── models/
│   └── config/
├── tests/
├── nodemon.json
└── package.json
```

**package.json:**
```json
{
  "name": "my-app",
  "version": "1.0.0",
  "scripts": {
    "start": "node src/server.js",
    "dev": "nodemon",
    "dev:debug": "nodemon --inspect",
    "test": "jest",
    "lint": "eslint src"
  },
  "nodemonConfig": {
    "watch": ["src", "config"],
    "ext": "js,json",
    "ignore": [
      "**/*.test.js",
      "node_modules",
      "coverage"
    ],
    "delay": 1000,
    "env": {
      "NODE_ENV": "development",
      "PORT": "3000",
      "DEBUG": "app:*"
    },
    "events": {
      "restart": "echo '🔄 Server restarted'",
      "crash": "echo '💥 Server crashed'"
    }
  },
  "devDependencies": {
    "nodemon": "^3.0.0",
    "eslint": "^8.0.0",
    "jest": "^29.0.0"
  }
}
```

**src/server.js:**
```javascript
require('dotenv').config();
const express = require('express');

const app = express();
const PORT = process.env.PORT || 3000;

app.use(express.json());

app.get('/', (req, res) => {
  res.json({ message: 'Hello World' });
});

app.listen(PORT, () => {
  console.log(`Server running on port ${PORT}`);
  console.log(`Environment: ${process.env.NODE_ENV}`);
});
```

**Usage:**
```bash
# Development with auto-restart
npm run dev

# Development with debugging
npm run dev:debug

# Production (no auto-restart)
npm start
```

## Troubleshooting

### Issue 1: Too Many Restarts

**Problem:** Server restarts constantly

**Solution:**
```json
{
  "ignore": [
    "node_modules",
    "*.log",
    "dist",
    "build"
  ],
  "delay": 2000
}
```

### Issue 2: Not Detecting Changes

**Problem:** Changes not triggering restart

**Solution:**
```json
{
  "legacyWatch": true,
  "watch": ["src"],
  "ext": "js,json"
}
```

### Issue 3: Slow Restart

**Problem:** Takes too long to restart

**Solution:**
```json
{
  "watch": ["src"],
  "ignore": ["node_modules", "tests"]
}
```

## Summary

Nodemon is essential for Node.js development:

✅ **Auto-Restart** - No manual server restarts  
✅ **Fast** - Quick restart on changes  
✅ **Configurable** - Watch what you need  
✅ **Simple** - Easy to set up and use  
✅ **Standard** - Industry-standard tool  

**Key Takeaways:**
- Install as dev dependency
- Configure in package.json
- Watch only necessary files
- Ignore test files and node_modules
- Use appropriate restart delay
- Set environment variables
- Use event hooks for automation

Nodemon dramatically improves development workflow by eliminating manual server restarts.
