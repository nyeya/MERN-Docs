# Jest - JavaScript Testing Framework

## Table of Contents
- [What is Jest?](#what-is-jest)
- [Why Use Jest?](#why-use-jest)
- [Jest vs Alternatives](#jest-vs-alternatives)
- [Installation & Setup](#installation--setup)
- [Writing Tests](#writing-tests)
- [Matchers](#matchers)
- [Async Testing](#async-testing)
- [Mocking](#mocking)
- [Testing Express APIs](#testing-express-apis)
- [Best Practices](#best-practices)

## What is Jest?

Jest is a delightful JavaScript testing framework with a focus on simplicity. Created by Facebook, it works with projects using Babel, TypeScript, Node, React, Angular, Vue, and more. It provides everything needed for testing in one package.

### Key Features
- **Zero Config**: Works out of the box
- **Snapshot Testing**: Capture component output
- **Isolated Tests**: Each test runs in its own environment
- **Great API**: Intuitive and well-documented
- **Fast**: Parallel test execution
- **Code Coverage**: Built-in coverage reports
- **Mocking**: Powerful mocking capabilities

## Why Use Jest?

### 1. **Zero Configuration**

**Other frameworks:**
```bash
npm install mocha chai sinon nyc
# Configure test runner
# Configure assertion library
# Configure mocking library
# Configure coverage tool
```

**Jest:**
```bash
npm install --save-dev jest
# That's it! Ready to test
```

### 2. **Everything Included**

```javascript
// Jest includes:
// - Test runner
// - Assertion library
// - Mocking
// - Code coverage
// - Snapshot testing

describe('User', () => {
  it('should create user', () => {
    expect(user.name).toBe('Alice');
  });
  
  it('should mock function', () => {
    const mockFn = jest.fn();
    mockFn();
    expect(mockFn).toHaveBeenCalled();
  });
});
```

### 3. **Great Developer Experience**

```javascript
// Clear error messages
expect(2 + 2).toBe(5);
// Error:
// Expected: 5
// Received: 4

// Watch mode
// npm test -- --watch
// Automatically reruns tests on file changes

// Interactive CLI
// Press 'a' to run all tests
// Press 'f' to run only failed tests
// Press 'p' to filter by filename
```

### 4. **Fast Execution**

```javascript
// Parallel test execution
// Tests run in isolated environments
// Smart test selection in watch mode
// Only runs tests related to changed files
```

## Jest vs Alternatives

### Jest vs Mocha

| Feature | Jest | Mocha |
|---------|------|-------|
| **Setup** | Zero config | Requires setup |
| **Assertions** | Built-in | Requires Chai |
| **Mocking** | Built-in | Requires Sinon |
| **Coverage** | Built-in | Requires NYC |
| **Snapshot Testing** | Built-in | Not available |
| **Speed** | Fast (parallel) | Slower |
| **Popularity** | Very high | High |

**When to choose Jest:**
- Want zero configuration
- Need snapshot testing
- Prefer all-in-one solution
- React/Vue/Angular projects

**When to choose Mocha:**
- Need maximum flexibility
- Prefer modular approach
- Existing Mocha codebase

### Jest vs Vitest

| Feature | Jest | Vitest |
|---------|------|--------|
| **Speed** | Fast | Faster |
| **Vite Integration** | No | Native |
| **ESM Support** | Good | Excellent |
| **API** | Established | Jest-compatible |
| **Maturity** | Very mature | Newer |

**When to choose Jest:**
- Established projects
- Maximum stability
- Large ecosystem

**When to choose Vitest:**
- Using Vite
- Need fastest execution
- Modern ESM projects

## Installation & Setup

### Installation

```bash
npm install --save-dev jest
```

### Basic Setup

**package.json:**
```json
{
  "scripts": {
    "test": "jest",
    "test:watch": "jest --watch",
    "test:coverage": "jest --coverage"
  }
}
```

### Jest Configuration

**jest.config.js:**
```javascript
module.exports = {
  // Test environment
  testEnvironment: 'node',
  
  // Coverage
  collectCoverageFrom: [
    'src/**/*.js',
    '!src/**/*.test.js'
  ],
  
  // Test match patterns
  testMatch: [
    '**/__tests__/**/*.js',
    '**/*.test.js',
    '**/*.spec.js'
  ],
  
  // Setup files
  setupFilesAfterEnv: ['<rootDir>/tests/setup.js'],
  
  // Module paths
  moduleDirectories: ['node_modules', 'src'],
  
  // Verbose output
  verbose: true
};
```

### TypeScript Setup

```bash
npm install --save-dev @types/jest ts-jest
```

**jest.config.js:**
```javascript
module.exports = {
  preset: 'ts-jest',
  testEnvironment: 'node',
  roots: ['<rootDir>/src'],
  testMatch: ['**/__tests__/**/*.ts', '**/*.test.ts'],
  moduleFileExtensions: ['ts', 'js', 'json']
};
```

## Writing Tests

### Basic Test Structure

```javascript
// sum.js
function sum(a, b) {
  return a + b;
}

module.exports = sum;

// sum.test.js
const sum = require('./sum');

test('adds 1 + 2 to equal 3', () => {
  expect(sum(1, 2)).toBe(3);
});
```

### Describe Blocks

```javascript
describe('Calculator', () => {
  describe('addition', () => {
    test('adds positive numbers', () => {
      expect(sum(1, 2)).toBe(3);
    });
    
    test('adds negative numbers', () => {
      expect(sum(-1, -2)).toBe(-3);
    });
  });
  
  describe('subtraction', () => {
    test('subtracts numbers', () => {
      expect(subtract(5, 3)).toBe(2);
    });
  });
});
```

### Setup and Teardown

```javascript
describe('Database', () => {
  // Runs once before all tests
  beforeAll(async () => {
    await database.connect();
  });
  
  // Runs before each test
  beforeEach(async () => {
    await database.clear();
  });
  
  // Runs after each test
  afterEach(async () => {
    await database.cleanup();
  });
  
  // Runs once after all tests
  afterAll(async () => {
    await database.disconnect();
  });
  
  test('creates user', async () => {
    const user = await database.createUser({ name: 'Alice' });
    expect(user.name).toBe('Alice');
  });
});
```

### Test Organization

```javascript
// Good organization
describe('User Model', () => {
  describe('create', () => {
    it('should create user with valid data', () => {});
    it('should reject invalid email', () => {});
    it('should hash password', () => {});
  });
  
  describe('update', () => {
    it('should update user fields', () => {});
    it('should not update email to existing', () => {});
  });
  
  describe('delete', () => {
    it('should delete user', () => {});
    it('should cascade delete related data', () => {});
  });
});
```

## Matchers

### Common Matchers

```javascript
// Equality
expect(2 + 2).toBe(4);                    // Strict equality (===)
expect({ name: 'Alice' }).toEqual({ name: 'Alice' }); // Deep equality

// Truthiness
expect(true).toBeTruthy();
expect(false).toBeFalsy();
expect(null).toBeNull();
expect(undefined).toBeUndefined();
expect('value').toBeDefined();

// Numbers
expect(4).toBeGreaterThan(3);
expect(4).toBeGreaterThanOrEqual(4);
expect(3).toBeLessThan(4);
expect(3).toBeLessThanOrEqual(3);
expect(0.1 + 0.2).toBeCloseTo(0.3);      // Floating point

// Strings
expect('team').toMatch(/tea/);
expect('team').toContain('ea');

// Arrays
expect([1, 2, 3]).toContain(2);
expect([1, 2, 3]).toHaveLength(3);
expect(['a', 'b']).toEqual(expect.arrayContaining(['a']));

// Objects
expect({ name: 'Alice', age: 25 }).toHaveProperty('name');
expect({ name: 'Alice', age: 25 }).toHaveProperty('name', 'Alice');
expect({ name: 'Alice' }).toMatchObject({ name: 'Alice' });

// Exceptions
expect(() => throwError()).toThrow();
expect(() => throwError()).toThrow(Error);
expect(() => throwError()).toThrow('error message');

// Negation
expect(2 + 2).not.toBe(5);
```

### Custom Matchers

```javascript
expect.extend({
  toBeWithinRange(received, floor, ceiling) {
    const pass = received >= floor && received <= ceiling;
    if (pass) {
      return {
        message: () => `expected ${received} not to be within range ${floor} - ${ceiling}`,
        pass: true
      };
    } else {
      return {
        message: () => `expected ${received} to be within range ${floor} - ${ceiling}`,
        pass: false
      };
    }
  }
});

test('is within range', () => {
  expect(100).toBeWithinRange(90, 110);
});
```

## Async Testing

### Promises

```javascript
test('fetches user data', () => {
  return fetchUser(1).then(user => {
    expect(user.name).toBe('Alice');
  });
});

// Or with resolves
test('fetches user data', () => {
  return expect(fetchUser(1)).resolves.toEqual({ name: 'Alice' });
});

// Or with rejects
test('fails to fetch invalid user', () => {
  return expect(fetchUser(-1)).rejects.toThrow('Invalid ID');
});
```

### Async/Await

```javascript
test('fetches user data', async () => {
  const user = await fetchUser(1);
  expect(user.name).toBe('Alice');
});

test('fails to fetch invalid user', async () => {
  await expect(fetchUser(-1)).rejects.toThrow('Invalid ID');
});

// Or with try/catch
test('fails to fetch invalid user', async () => {
  try {
    await fetchUser(-1);
  } catch (error) {
    expect(error.message).toBe('Invalid ID');
  }
});
```

### Callbacks

```javascript
test('fetches user data', done => {
  fetchUser(1, (error, user) => {
    expect(user.name).toBe('Alice');
    done();
  });
});

test('handles error', done => {
  fetchUser(-1, (error, user) => {
    expect(error).toBeDefined();
    done();
  });
});
```

## Mocking

### Mock Functions

```javascript
// Create mock function
const mockFn = jest.fn();

// Call it
mockFn('arg1', 'arg2');

// Assert
expect(mockFn).toHaveBeenCalled();
expect(mockFn).toHaveBeenCalledWith('arg1', 'arg2');
expect(mockFn).toHaveBeenCalledTimes(1);

// Mock return value
const mockFn = jest.fn().mockReturnValue(42);
expect(mockFn()).toBe(42);

// Mock resolved value
const mockFn = jest.fn().mockResolvedValue('success');
const result = await mockFn();
expect(result).toBe('success');

// Mock implementation
const mockFn = jest.fn((a, b) => a + b);
expect(mockFn(1, 2)).toBe(3);
```

### Mock Modules

```javascript
// userService.js
const axios = require('axios');

async function getUser(id) {
  const response = await axios.get(`/api/users/${id}`);
  return response.data;
}

// userService.test.js
jest.mock('axios');
const axios = require('axios');
const { getUser } = require('./userService');

test('fetches user', async () => {
  const mockUser = { id: 1, name: 'Alice' };
  axios.get.mockResolvedValue({ data: mockUser });
  
  const user = await getUser(1);
  
  expect(user).toEqual(mockUser);
  expect(axios.get).toHaveBeenCalledWith('/api/users/1');
});
```

### Partial Mocks

```javascript
// Mock specific functions
jest.mock('./userService', () => ({
  ...jest.requireActual('./userService'),
  getUser: jest.fn()
}));
```

### Spy on Methods

```javascript
const user = {
  getName: () => 'Alice'
};

const spy = jest.spyOn(user, 'getName');

user.getName();

expect(spy).toHaveBeenCalled();

// Restore original
spy.mockRestore();
```

### Clear/Reset Mocks

```javascript
beforeEach(() => {
  // Clear call history
  jest.clearAllMocks();
  
  // Reset implementation
  jest.resetAllMocks();
  
  // Restore original implementation
  jest.restoreAllMocks();
});
```

## Testing Express APIs

### Setup with Supertest

```bash
npm install --save-dev supertest
```

### Basic API Test

```javascript
// app.js
const express = require('express');
const app = express();

app.use(express.json());

app.get('/api/users', (req, res) => {
  res.json({ users: [] });
});

app.post('/api/users', (req, res) => {
  res.status(201).json(req.body);
});

module.exports = app;

// app.test.js
const request = require('supertest');
const app = require('./app');

describe('GET /api/users', () => {
  it('should return users array', async () => {
    const response = await request(app)
      .get('/api/users')
      .expect(200)
      .expect('Content-Type', /json/);
    
    expect(response.body).toHaveProperty('users');
    expect(Array.isArray(response.body.users)).toBe(true);
  });
});

describe('POST /api/users', () => {
  it('should create user', async () => {
    const userData = { name: 'Alice', email: 'alice@example.com' };
    
    const response = await request(app)
      .post('/api/users')
      .send(userData)
      .expect(201);
    
    expect(response.body).toMatchObject(userData);
  });
});
```

### Testing with Database

```javascript
const request = require('supertest');
const mongoose = require('mongoose');
const app = require('./app');
const User = require('./models/User');

beforeAll(async () => {
  await mongoose.connect(process.env.TEST_DATABASE_URL);
});

afterAll(async () => {
  await mongoose.connection.close();
});

beforeEach(async () => {
  await User.deleteMany({});
});

describe('User API', () => {
  describe('POST /api/users', () => {
    it('should create user', async () => {
      const userData = {
        email: 'alice@example.com',
        password: 'password123',
        name: 'Alice'
      };
      
      const response = await request(app)
        .post('/api/users')
        .send(userData)
        .expect(201);
      
      expect(response.body.user).toHaveProperty('id');
      expect(response.body.user.email).toBe(userData.email);
      
      // Verify in database
      const user = await User.findOne({ email: userData.email });
      expect(user).toBeDefined();
      expect(user.name).toBe(userData.name);
    });
    
    it('should reject duplicate email', async () => {
      await User.create({
        email: 'alice@example.com',
        password: 'password123',
        name: 'Alice'
      });
      
      const response = await request(app)
        .post('/api/users')
        .send({
          email: 'alice@example.com',
          password: 'password456',
          name: 'Alice 2'
        })
        .expect(400);
      
      expect(response.body).toHaveProperty('error');
    });
  });
  
  describe('GET /api/users/:id', () => {
    it('should return user', async () => {
      const user = await User.create({
        email: 'alice@example.com',
        password: 'password123',
        name: 'Alice'
      });
      
      const response = await request(app)
        .get(`/api/users/${user.id}`)
        .expect(200);
      
      expect(response.body.email).toBe(user.email);
    });
    
    it('should return 404 for non-existent user', async () => {
      const fakeId = new mongoose.Types.ObjectId();
      
      await request(app)
        .get(`/api/users/${fakeId}`)
        .expect(404);
    });
  });
});
```

### Testing Authentication

```javascript
describe('Protected Routes', () => {
  let authToken;
  
  beforeEach(async () => {
    // Create user and get token
    const user = await User.create({
      email: 'alice@example.com',
      password: 'password123',
      name: 'Alice'
    });
    
    const response = await request(app)
      .post('/api/login')
      .send({ email: 'alice@example.com', password: 'password123' });
    
    authToken = response.body.token;
  });
  
  it('should access protected route with token', async () => {
    await request(app)
      .get('/api/profile')
      .set('Authorization', `Bearer ${authToken}`)
      .expect(200);
  });
  
  it('should reject without token', async () => {
    await request(app)
      .get('/api/profile')
      .expect(401);
  });
  
  it('should reject with invalid token', async () => {
    await request(app)
      .get('/api/profile')
      .set('Authorization', 'Bearer invalid-token')
      .expect(401);
  });
});
```

## Best Practices

### 1. Test Structure (AAA Pattern)

```javascript
test('creates user', async () => {
  // Arrange
  const userData = {
    email: 'alice@example.com',
    name: 'Alice'
  };
  
  // Act
  const user = await User.create(userData);
  
  // Assert
  expect(user.email).toBe(userData.email);
  expect(user.name).toBe(userData.name);
});
```

### 2. Descriptive Test Names

```javascript
// Bad
test('user test', () => {});

// Good
test('should create user with valid data', () => {});
test('should reject user with invalid email', () => {});
test('should hash password before saving', () => {});
```

### 3. One Assertion Per Test (When Possible)

```javascript
// Good
test('should have correct email', () => {
  expect(user.email).toBe('alice@example.com');
});

test('should have correct name', () => {
  expect(user.name).toBe('Alice');
});

// Acceptable for related assertions
test('should create user with correct data', () => {
  expect(user.email).toBe('alice@example.com');
  expect(user.name).toBe('Alice');
  expect(user.id).toBeDefined();
});
```

### 4. Use Test Fixtures

```javascript
// tests/fixtures/users.js
module.exports = {
  validUser: {
    email: 'alice@example.com',
    password: 'password123',
    name: 'Alice'
  },
  invalidUser: {
    email: 'invalid-email',
    password: '123'
  }
};

// tests/user.test.js
const { validUser, invalidUser } = require('./fixtures/users');

test('creates user', async () => {
  const user = await User.create(validUser);
  expect(user.email).toBe(validUser.email);
});
```

### 5. Isolate Tests

```javascript
// Bad - Tests depend on each other
let userId;

test('creates user', async () => {
  const user = await User.create({ name: 'Alice' });
  userId = user.id; // Shared state
});

test('gets user', async () => {
  const user = await User.findById(userId); // Depends on previous test
  expect(user.name).toBe('Alice');
});

// Good - Independent tests
test('creates user', async () => {
  const user = await User.create({ name: 'Alice' });
  expect(user.name).toBe('Alice');
});

test('gets user', async () => {
  const created = await User.create({ name: 'Alice' });
  const user = await User.findById(created.id);
  expect(user.name).toBe('Alice');
});
```

### 6. Test Edge Cases

```javascript
describe('User age validation', () => {
  it('should accept valid age', () => {
    expect(validateAge(25)).toBe(true);
  });
  
  it('should reject negative age', () => {
    expect(validateAge(-1)).toBe(false);
  });
  
  it('should reject zero age', () => {
    expect(validateAge(0)).toBe(false);
  });
  
  it('should reject age over 120', () => {
    expect(validateAge(121)).toBe(false);
  });
  
  it('should handle boundary values', () => {
    expect(validateAge(18)).toBe(true);  // Min
    expect(validateAge(120)).toBe(true); // Max
  });
});
```

### 7. Use Coverage Reports

```bash
npm test -- --coverage

# View detailed report
npm test -- --coverage --coverageReporters=html
# Open coverage/index.html
```

## Summary

Jest is the modern standard for JavaScript testing:

✅ **Zero Config** - Works out of the box  
✅ **All-in-One** - Runner, assertions, mocking, coverage  
✅ **Fast** - Parallel execution  
✅ **Great DX** - Excellent error messages and watch mode  
✅ **Widely Adopted** - Industry standard  

**Key Takeaways:**
- Write descriptive test names
- Use AAA pattern (Arrange, Act, Assert)
- Keep tests isolated and independent
- Test edge cases and error conditions
- Use mocks to isolate units
- Aim for high code coverage
- Run tests in CI/CD pipeline

Jest makes testing enjoyable and helps build reliable applications.
