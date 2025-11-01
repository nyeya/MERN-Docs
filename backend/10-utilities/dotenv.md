# Dotenv - Environment Variable Management

## Table of Contents
- [What is Dotenv?](#what-is-dotenv)
- [Why Use Dotenv?](#why-use-dotenv)
- [Installation & Setup](#installation--setup)
- [Basic Usage](#basic-usage)
- [Configuration Options](#configuration-options)
- [Best Practices](#best-practices)
- [Common Patterns](#common-patterns)
- [Security](#security)

## What is Dotenv?

Dotenv is a zero-dependency module that loads environment variables from a `.env` file into `process.env`. It helps keep sensitive configuration separate from code and makes it easy to manage different configurations for different environments.

### Key Features
- **Zero Dependencies**: Lightweight and fast
- **Simple API**: Load with one line
- **Environment-Specific**: Different configs for dev/prod
- **Security**: Keep secrets out of code
- **12-Factor App**: Follows best practices

## Why Use Dotenv?

### 1. **Keep Secrets Out of Code**

**BAD - Hardcoded Secrets:**
```javascript
// NEVER DO THIS!
const dbConfig = {
  host: 'db.example.com',
  user: 'admin',
  password: 'super-secret-password', // Exposed in code!
  database: 'myapp'
};

const jwtSecret = 'my-jwt-secret-key'; // Exposed in code!

// If code is committed to Git, secrets are exposed forever
```

**GOOD - Environment Variables:**
```javascript
// .env file (not committed to Git)
DB_HOST=db.example.com
DB_USER=admin
DB_PASSWORD=super-secret-password
DB_NAME=myapp
JWT_SECRET=my-jwt-secret-key

// Code
require('dotenv').config();

const dbConfig = {
  host: process.env.DB_HOST,
  user: process.env.DB_USER,
  password: process.env.DB_PASSWORD,
  database: process.env.DB_NAME
};

const jwtSecret = process.env.JWT_SECRET;
```

### 2. **Different Configs for Different Environments**

```javascript
// .env.development
NODE_ENV=development
PORT=3000
DATABASE_URL=mongodb://localhost:27017/myapp-dev
API_URL=http://localhost:3000

// .env.production
NODE_ENV=production
PORT=8080
DATABASE_URL=mongodb://prod-server:27017/myapp
API_URL=https://api.example.com
```

### 3. **Easy Configuration Management**

```javascript
// Without dotenv - scattered config
const port = 3000;
const dbUrl = 'mongodb://localhost:27017/myapp';
const jwtSecret = 'secret';
// ... config spread across files

// With dotenv - centralized config
require('dotenv').config();

const config = {
  port: process.env.PORT,
  database: process.env.DATABASE_URL,
  jwt: process.env.JWT_SECRET
};
```

## Installation & Setup

### Installation

```bash
npm install dotenv
```

### Basic Setup

**1. Create `.env` file:**
```env
# .env
PORT=3000
DATABASE_URL=mongodb://localhost:27017/myapp
JWT_SECRET=your-secret-key
NODE_ENV=development
```

**2. Load in your app:**
```javascript
// Load as early as possible
require('dotenv').config();

// Now use environment variables
const port = process.env.PORT || 3000;
const dbUrl = process.env.DATABASE_URL;
```

### TypeScript Setup

```typescript
import dotenv from 'dotenv';

dotenv.config();

// Access variables
const port: string = process.env.PORT || '3000';
```

## Basic Usage

### Load Default .env File

```javascript
// Loads .env from project root
require('dotenv').config();

console.log(process.env.PORT); // 3000
console.log(process.env.DATABASE_URL); // mongodb://...
```

### Load Custom Path

```javascript
// Load from custom path
require('dotenv').config({ path: './config/.env' });

// Load different files for different environments
const envFile = process.env.NODE_ENV === 'production'
  ? '.env.production'
  : '.env.development';

require('dotenv').config({ path: envFile });
```

### Preload (Command Line)

```bash
# Load dotenv before running script
node -r dotenv/config your-script.js

# With custom path
node -r dotenv/config your-script.js dotenv_config_path=/custom/path/.env
```

### ES Modules

```javascript
// ES Modules
import 'dotenv/config';

// Or with options
import dotenv from 'dotenv';
dotenv.config({ path: './config/.env' });
```

## Configuration Options

### Path

```javascript
// Custom path
require('dotenv').config({
  path: './config/.env'
});
```

### Encoding

```javascript
// Custom encoding (default: utf8)
require('dotenv').config({
  encoding: 'latin1'
});
```

### Debug

```javascript
// Enable debug output
require('dotenv').config({
  debug: true
});
```

### Override

```javascript
// Override existing environment variables
require('dotenv').config({
  override: true
});

// By default, existing env vars are NOT overridden
```

### Parse

```javascript
// Parse without setting process.env
const fs = require('fs');
const dotenv = require('dotenv');

const envConfig = dotenv.parse(fs.readFileSync('.env'));
console.log(envConfig);
// { PORT: '3000', DATABASE_URL: '...' }
```

## Best Practices

### 1. Never Commit .env Files

**.gitignore:**
```gitignore
# Environment variables
.env
.env.local
.env.*.local

# Keep .env.example
!.env.example
```

### 2. Provide .env.example

**.env.example:**
```env
# Server
PORT=3000
NODE_ENV=development

# Database
DATABASE_URL=mongodb://localhost:27017/myapp

# Authentication
JWT_SECRET=your-secret-key-here
JWT_EXPIRES_IN=7d

# Email
SMTP_HOST=smtp.example.com
SMTP_PORT=587
SMTP_USER=your-email@example.com
SMTP_PASS=your-password

# External APIs
STRIPE_SECRET_KEY=sk_test_...
SENDGRID_API_KEY=SG...
```

### 3. Load Early

```javascript
// Load dotenv first thing
require('dotenv').config();

// Then import other modules
const express = require('express');
const mongoose = require('mongoose');

// Now all modules can access env vars
```

### 4. Validate Environment Variables

```javascript
// config/env.js
require('dotenv').config();

const requiredEnvVars = [
  'PORT',
  'DATABASE_URL',
  'JWT_SECRET'
];

for (const envVar of requiredEnvVars) {
  if (!process.env[envVar]) {
    throw new Error(`Missing required environment variable: ${envVar}`);
  }
}

module.exports = {
  port: parseInt(process.env.PORT),
  database: process.env.DATABASE_URL,
  jwt: {
    secret: process.env.JWT_SECRET,
    expiresIn: process.env.JWT_EXPIRES_IN || '7d'
  }
};
```

### 5. Type-Safe Environment Variables (TypeScript)

```typescript
// config/env.ts
import dotenv from 'dotenv';
import { z } from 'zod';

dotenv.config();

const envSchema = z.object({
  NODE_ENV: z.enum(['development', 'production', 'test']),
  PORT: z.string().transform(Number),
  DATABASE_URL: z.string().url(),
  JWT_SECRET: z.string().min(32),
  JWT_EXPIRES_IN: z.string().default('7d')
});

export const env = envSchema.parse(process.env);

// Now env is fully typed and validated!
// env.PORT is a number
// env.NODE_ENV is 'development' | 'production' | 'test'
```

### 6. Separate Config Module

```javascript
// config/index.js
require('dotenv').config();

module.exports = {
  server: {
    port: parseInt(process.env.PORT) || 3000,
    env: process.env.NODE_ENV || 'development'
  },
  database: {
    url: process.env.DATABASE_URL,
    options: {
      useNewUrlParser: true,
      useUnifiedTopology: true
    }
  },
  jwt: {
    secret: process.env.JWT_SECRET,
    expiresIn: process.env.JWT_EXPIRES_IN || '7d'
  },
  email: {
    host: process.env.SMTP_HOST,
    port: parseInt(process.env.SMTP_PORT),
    user: process.env.SMTP_USER,
    pass: process.env.SMTP_PASS
  }
};

// Usage
const config = require('./config');
app.listen(config.server.port);
```

### 7. Environment-Specific Files

```javascript
// Load environment-specific file
const envFile = `.env.${process.env.NODE_ENV || 'development'}`;
require('dotenv').config({ path: envFile });

// Files:
// .env.development
// .env.production
// .env.test
```

## Common Patterns

### Pattern 1: Multiple Environment Files

```javascript
// Load base .env first
require('dotenv').config();

// Then load environment-specific overrides
const envFile = `.env.${process.env.NODE_ENV}`;
require('dotenv').config({ path: envFile, override: true });

// Files:
// .env - Base configuration
// .env.development - Development overrides
// .env.production - Production overrides
```

### Pattern 2: Validation Helper

```javascript
// utils/validateEnv.js
function validateEnv(requiredVars) {
  const missing = requiredVars.filter(v => !process.env[v]);
  
  if (missing.length > 0) {
    throw new Error(
      `Missing required environment variables: ${missing.join(', ')}`
    );
  }
}

// Usage
require('dotenv').config();
validateEnv(['PORT', 'DATABASE_URL', 'JWT_SECRET']);
```

### Pattern 3: Config with Defaults

```javascript
// config.js
require('dotenv').config();

const config = {
  port: process.env.PORT || 3000,
  database: process.env.DATABASE_URL || 'mongodb://localhost:27017/myapp',
  jwt: {
    secret: process.env.JWT_SECRET || 'dev-secret',
    expiresIn: process.env.JWT_EXPIRES_IN || '7d'
  },
  isDevelopment: process.env.NODE_ENV !== 'production',
  isProduction: process.env.NODE_ENV === 'production'
};

// Validate in production
if (config.isProduction) {
  if (config.jwt.secret === 'dev-secret') {
    throw new Error('JWT_SECRET must be set in production');
  }
}

module.exports = config;
```

### Pattern 4: Docker Integration

**Dockerfile:**
```dockerfile
FROM node:18

WORKDIR /app

COPY package*.json ./
RUN npm install

COPY . .

# Don't copy .env file
# Use environment variables from docker-compose or runtime

CMD ["node", "server.js"]
```

**docker-compose.yml:**
```yaml
version: '3.8'

services:
  app:
    build: .
    ports:
      - "3000:3000"
    environment:
      - PORT=3000
      - DATABASE_URL=mongodb://mongo:27017/myapp
      - JWT_SECRET=${JWT_SECRET}
    env_file:
      - .env
```

### Pattern 5: Testing Environment

```javascript
// test/setup.js
require('dotenv').config({ path: '.env.test' });

// Override for tests
process.env.NODE_ENV = 'test';
process.env.DATABASE_URL = 'mongodb://localhost:27017/myapp-test';

// tests/user.test.js
const config = require('../config');
console.log(config.database); // Uses test database
```

## Security

### 1. Never Commit Secrets

```bash
# Add to .gitignore
echo ".env" >> .gitignore
echo ".env.local" >> .gitignore
echo ".env.*.local" >> .gitignore

# Check if .env is tracked
git ls-files | grep .env

# If tracked, remove from Git
git rm --cached .env
git commit -m "Remove .env from tracking"
```

### 2. Use Strong Secrets

```javascript
// Generate strong secrets
const crypto = require('crypto');

// Generate JWT secret
const jwtSecret = crypto.randomBytes(64).toString('hex');
console.log(`JWT_SECRET=${jwtSecret}`);

// Generate API key
const apiKey = crypto.randomBytes(32).toString('base64');
console.log(`API_KEY=${apiKey}`);
```

### 3. Rotate Secrets Regularly

```env
# Document when secrets were last rotated
# JWT_SECRET - Last rotated: 2024-01-01
JWT_SECRET=...

# API_KEY - Last rotated: 2024-01-15
API_KEY=...
```

### 4. Use Secret Management in Production

```javascript
// For production, use secret management services
// - AWS Secrets Manager
// - Azure Key Vault
// - Google Secret Manager
// - HashiCorp Vault

// Example with AWS Secrets Manager
const AWS = require('aws-sdk');
const secretsManager = new AWS.SecretsManager();

async function getSecret(secretName) {
  const data = await secretsManager.getSecretValue({
    SecretId: secretName
  }).promise();
  
  return JSON.parse(data.SecretString);
}

// Load secrets in production
if (process.env.NODE_ENV === 'production') {
  const secrets = await getSecret('myapp/production');
  process.env.JWT_SECRET = secrets.JWT_SECRET;
  process.env.DATABASE_URL = secrets.DATABASE_URL;
}
```

### 5. Limit Access to .env Files

```bash
# Set restrictive permissions
chmod 600 .env

# Only owner can read/write
# -rw------- 1 user user 256 Jan 1 12:00 .env
```

## Complete Example

**Project Structure:**
```
my-app/
├── .env
├── .env.example
├── .env.development
├── .env.production
├── .gitignore
├── config/
│   ├── index.js
│   └── validate.js
├── src/
│   └── server.js
└── package.json
```

**.env.example:**
```env
# Server Configuration
PORT=3000
NODE_ENV=development

# Database
DATABASE_URL=mongodb://localhost:27017/myapp

# Authentication
JWT_SECRET=your-secret-key-min-32-characters
JWT_EXPIRES_IN=7d

# Email
SMTP_HOST=smtp.example.com
SMTP_PORT=587
SMTP_USER=your-email@example.com
SMTP_PASS=your-password

# External APIs
STRIPE_SECRET_KEY=sk_test_...
SENDGRID_API_KEY=SG...

# Feature Flags
ENABLE_REGISTRATION=true
ENABLE_EMAIL_VERIFICATION=false
```

**config/validate.js:**
```javascript
function validateEnv() {
  const required = [
    'PORT',
    'DATABASE_URL',
    'JWT_SECRET'
  ];
  
  const missing = required.filter(key => !process.env[key]);
  
  if (missing.length > 0) {
    throw new Error(
      `Missing required environment variables:\n${missing.join('\n')}`
    );
  }
  
  // Validate JWT_SECRET length
  if (process.env.JWT_SECRET.length < 32) {
    throw new Error('JWT_SECRET must be at least 32 characters');
  }
  
  // Validate PORT is a number
  if (isNaN(parseInt(process.env.PORT))) {
    throw new Error('PORT must be a number');
  }
}

module.exports = validateEnv;
```

**config/index.js:**
```javascript
require('dotenv').config();
const validateEnv = require('./validate');

// Validate environment variables
validateEnv();

const config = {
  server: {
    port: parseInt(process.env.PORT),
    env: process.env.NODE_ENV || 'development',
    isDevelopment: process.env.NODE_ENV !== 'production',
    isProduction: process.env.NODE_ENV === 'production'
  },
  
  database: {
    url: process.env.DATABASE_URL
  },
  
  jwt: {
    secret: process.env.JWT_SECRET,
    expiresIn: process.env.JWT_EXPIRES_IN || '7d'
  },
  
  email: {
    host: process.env.SMTP_HOST,
    port: parseInt(process.env.SMTP_PORT),
    user: process.env.SMTP_USER,
    pass: process.env.SMTP_PASS
  },
  
  features: {
    registration: process.env.ENABLE_REGISTRATION === 'true',
    emailVerification: process.env.ENABLE_EMAIL_VERIFICATION === 'true'
  }
};

module.exports = config;
```

**src/server.js:**
```javascript
const express = require('express');
const config = require('../config');

const app = express();

app.get('/', (req, res) => {
  res.json({
    environment: config.server.env,
    features: config.features
  });
});

app.listen(config.server.port, () => {
  console.log(`Server running on port ${config.server.port}`);
  console.log(`Environment: ${config.server.env}`);
});
```

**package.json:**
```json
{
  "scripts": {
    "dev": "NODE_ENV=development node src/server.js",
    "start": "NODE_ENV=production node src/server.js",
    "test": "NODE_ENV=test jest"
  }
}
```

## Summary

Dotenv is essential for managing configuration:

✅ **Security** - Keep secrets out of code  
✅ **Flexibility** - Different configs per environment  
✅ **Simple** - One line to load  
✅ **Standard** - 12-factor app methodology  
✅ **Zero Dependencies** - Lightweight  

**Key Takeaways:**
- Never commit .env files to Git
- Always provide .env.example
- Load dotenv as early as possible
- Validate required environment variables
- Use strong, randomly generated secrets
- Separate config into dedicated module
- Use secret management in production
- Document environment variables

Dotenv is a must-have for any Node.js application that needs configuration management.
