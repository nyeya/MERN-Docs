# ESLint - Linting Utility for JavaScript and TypeScript

## Overview

ESLint is a pluggable and configurable linter tool for identifying and reporting on patterns in JavaScript and TypeScript. It helps maintain code quality, enforce coding standards, and catch potential bugs before runtime. ESLint is highly customizable with a vast ecosystem of plugins and shareable configurations, making it the industry standard for JavaScript/TypeScript linting.

**Key Features:**
- Pluggable architecture
- Configurable rules
- Auto-fix capabilities
- TypeScript support
- React/JSX support
- Custom rule creation
- IDE integration
- Pre-commit hooks support
- Shareable configs
- Extensive plugin ecosystem

## Why Use ESLint?

### Problems It Solves

1. **Code Quality**: Catch bugs and code smells early
2. **Consistency**: Enforce coding standards across team
3. **Best Practices**: Enforce JavaScript/React best practices
4. **Type Safety**: Catch type-related issues (with TypeScript)
5. **Formatting**: Basic code formatting (use with Prettier)
6. **Accessibility**: Catch accessibility issues in JSX

### When to Use ESLint

- ✅ All JavaScript/TypeScript projects
- ✅ Team projects
- ✅ React applications
- ✅ When you need code quality enforcement
- ✅ CI/CD pipelines
- ✅ Pre-commit hooks

### When NOT to Use ESLint

- ❌ Non-JavaScript projects
- ❌ Quick prototypes (maybe)
- ❌ When you only need formatting (use Prettier alone)

## Comparison with Alternatives

### ESLint vs Prettier

| Feature | ESLint | Prettier |
|---------|--------|----------|
| Purpose | Code quality | Code formatting |
| Configurable | Highly | Minimally |
| Auto-fix | Some rules | All formatting |
| Performance | Slower | Faster |
| Best For | Linting | Formatting |
| Typical Usage | Use both together |

**Note:** ESLint and Prettier are complementary - use both!

### ESLint vs Biome

| Feature | ESLint | Biome |
|---------|--------|-------|
| Speed | Moderate | Very Fast |
| Ecosystem | Massive | Growing |
| Maturity | Mature | New |
| Plugins | Extensive | Limited |
| Best For | Established projects | New projects |

### ESLint vs TSLint

TSLint is deprecated. Use ESLint with TypeScript plugin instead.

## Installation & Setup

### Installation

```bash
# npm
npm install -D eslint

# yarn
yarn add -D eslint

# pnpm
pnpm add -D eslint
```

### Initialize ESLint

```bash
npm init @eslint/config
```

### For React + TypeScript

```bash
npm install -D eslint @typescript-eslint/parser @typescript-eslint/eslint-plugin eslint-plugin-react eslint-plugin-react-hooks
```

### Basic Configuration

```javascript
// .eslintrc.cjs
module.exports = {
  env: {
    browser: true,
    es2021: true,
    node: true
  },
  extends: [
    'eslint:recommended',
    'plugin:react/recommended',
    'plugin:@typescript-eslint/recommended'
  ],
  parser: '@typescript-eslint/parser',
  parserOptions: {
    ecmaFeatures: {
      jsx: true
    },
    ecmaVersion: 'latest',
    sourceType: 'module'
  },
  plugins: [
    'react',
    '@typescript-eslint'
  ],
  rules: {
    'react/react-in-jsx-scope': 'off',
    '@typescript-eslint/no-unused-vars': 'warn'
  },
  settings: {
    react: {
      version: 'detect'
    }
  }
}
```

### Package.json Scripts

```json
{
  "scripts": {
    "lint": "eslint . --ext .js,.jsx,.ts,.tsx",
    "lint:fix": "eslint . --ext .js,.jsx,.ts,.tsx --fix"
  }
}
```

## Core Concepts

### 1. Rules

```javascript
// .eslintrc.cjs
module.exports = {
  rules: {
    // Error level: "off" or 0, "warn" or 1, "error" or 2
    'no-console': 'warn',
    'no-unused-vars': 'error',
    'prefer-const': 'error',
    'no-var': 'error',
    
    // With options
    'max-len': ['error', { code: 100 }],
    'quotes': ['error', 'single'],
    'semi': ['error', 'never']
  }
}
```

### 2. Extends

```javascript
module.exports = {
  extends: [
    'eslint:recommended',                    // ESLint recommended rules
    'plugin:react/recommended',              // React recommended rules
    'plugin:@typescript-eslint/recommended', // TypeScript recommended
    'plugin:react-hooks/recommended'         // React Hooks rules
  ]
}
```

### 3. Plugins

```javascript
module.exports = {
  plugins: [
    'react',
    '@typescript-eslint',
    'react-hooks',
    'jsx-a11y'  // Accessibility
  ]
}
```

### 4. Environments

```javascript
module.exports = {
  env: {
    browser: true,  // Browser global variables
    node: true,     // Node.js global variables
    es2021: true,   // ES2021 globals
    jest: true      // Jest global variables
  }
}
```

## Basic Usage

### Common Rules

```javascript
module.exports = {
  rules: {
    // Possible Errors
    'no-console': 'warn',
    'no-debugger': 'error',
    'no-duplicate-case': 'error',
    'no-empty': 'error',
    'no-extra-semi': 'error',
    
    // Best Practices
    'eqeqeq': ['error', 'always'],
    'no-eval': 'error',
    'no-implied-eval': 'error',
    'no-return-await': 'error',
    'prefer-promise-reject-errors': 'error',
    
    // Variables
    'no-unused-vars': ['error', { argsIgnorePattern: '^_' }],
    'no-use-before-define': 'error',
    
    // Stylistic
    'camelcase': ['error', { properties: 'never' }],
    'comma-dangle': ['error', 'always-multiline'],
    'indent': ['error', 2],
    'quotes': ['error', 'single'],
    'semi': ['error', 'never']
  }
}
```

### React-Specific Rules

```javascript
module.exports = {
  extends: ['plugin:react/recommended', 'plugin:react-hooks/recommended'],
  rules: {
    // React
    'react/prop-types': 'off', // Using TypeScript
    'react/react-in-jsx-scope': 'off', // React 17+
    'react/jsx-uses-react': 'off',
    'react/jsx-no-target-blank': 'error',
    'react/jsx-key': 'error',
    
    // React Hooks
    'react-hooks/rules-of-hooks': 'error',
    'react-hooks/exhaustive-deps': 'warn'
  }
}
```

### TypeScript Rules

```javascript
module.exports = {
  extends: ['plugin:@typescript-eslint/recommended'],
  rules: {
    '@typescript-eslint/no-explicit-any': 'warn',
    '@typescript-eslint/no-unused-vars': ['error', { argsIgnorePattern: '^_' }],
    '@typescript-eslint/explicit-module-boundary-types': 'off',
    '@typescript-eslint/no-non-null-assertion': 'warn'
  }
}
```

## Advanced Usage

### 1. Complete React + TypeScript Config

```javascript
// .eslintrc.cjs
module.exports = {
  env: {
    browser: true,
    es2021: true,
    node: true
  },
  extends: [
    'eslint:recommended',
    'plugin:react/recommended',
    'plugin:react-hooks/recommended',
    'plugin:@typescript-eslint/recommended',
    'plugin:jsx-a11y/recommended'
  ],
  parser: '@typescript-eslint/parser',
  parserOptions: {
    ecmaFeatures: {
      jsx: true
    },
    ecmaVersion: 'latest',
    sourceType: 'module',
    project: './tsconfig.json'
  },
  plugins: [
    'react',
    'react-hooks',
    '@typescript-eslint',
    'jsx-a11y'
  ],
  rules: {
    // React
    'react/react-in-jsx-scope': 'off',
    'react/prop-types': 'off',
    'react/jsx-no-target-blank': 'error',
    'react/jsx-key': 'error',
    
    // React Hooks
    'react-hooks/rules-of-hooks': 'error',
    'react-hooks/exhaustive-deps': 'warn',
    
    // TypeScript
    '@typescript-eslint/no-explicit-any': 'warn',
    '@typescript-eslint/no-unused-vars': ['error', { 
      argsIgnorePattern: '^_',
      varsIgnorePattern: '^_'
    }],
    '@typescript-eslint/explicit-module-boundary-types': 'off',
    
    // General
    'no-console': ['warn', { allow: ['warn', 'error'] }],
    'prefer-const': 'error',
    'no-var': 'error'
  },
  settings: {
    react: {
      version: 'detect'
    }
  },
  ignorePatterns: ['dist', 'build', 'node_modules']
}
```

### 2. With Prettier

```bash
npm install -D eslint-config-prettier eslint-plugin-prettier
```

```javascript
// .eslintrc.cjs
module.exports = {
  extends: [
    'eslint:recommended',
    'plugin:react/recommended',
    'plugin:@typescript-eslint/recommended',
    'prettier' // Must be last
  ],
  plugins: ['prettier'],
  rules: {
    'prettier/prettier': 'error'
  }
}
```

```javascript
// .prettierrc.cjs
module.exports = {
  semi: false,
  singleQuote: true,
  trailingComma: 'es5',
  printWidth: 100,
  tabWidth: 2
}
```

### 3. Ignore Files

```
# .eslintignore
node_modules/
dist/
build/
*.config.js
coverage/
.next/
```

### 4. Override Rules for Specific Files

```javascript
module.exports = {
  rules: {
    'no-console': 'error'
  },
  overrides: [
    {
      files: ['*.test.ts', '*.test.tsx'],
      env: {
        jest: true
      },
      rules: {
        'no-console': 'off',
        '@typescript-eslint/no-explicit-any': 'off'
      }
    },
    {
      files: ['*.config.js', '*.config.ts'],
      rules: {
        '@typescript-eslint/no-var-requires': 'off'
      }
    }
  ]
}
```

### 5. Custom Rules

```javascript
// eslint-plugin-custom/rules/no-console-log.js
module.exports = {
  meta: {
    type: 'problem',
    docs: {
      description: 'Disallow console.log',
      category: 'Best Practices'
    },
    fixable: 'code'
  },
  create(context) {
    return {
      CallExpression(node) {
        if (
          node.callee.type === 'MemberExpression' &&
          node.callee.object.name === 'console' &&
          node.callee.property.name === 'log'
        ) {
          context.report({
            node,
            message: 'Unexpected console.log statement'
          })
        }
      }
    }
  }
}
```

### 6. Pre-commit Hooks with Husky

```bash
npm install -D husky lint-staged
npx husky install
```

```json
// package.json
{
  "lint-staged": {
    "*.{js,jsx,ts,tsx}": [
      "eslint --fix",
      "prettier --write"
    ]
  }
}
```

```bash
# .husky/pre-commit
#!/bin/sh
. "$(dirname "$0")/_/husky.sh"

npx lint-staged
```

## Real-World Examples

### Vite + React + TypeScript Config

```javascript
// .eslintrc.cjs
module.exports = {
  root: true,
  env: {
    browser: true,
    es2020: true
  },
  extends: [
    'eslint:recommended',
    'plugin:@typescript-eslint/recommended',
    'plugin:react-hooks/recommended',
    'plugin:react/recommended',
    'prettier'
  ],
  ignorePatterns: ['dist', '.eslintrc.cjs'],
  parser: '@typescript-eslint/parser',
  parserOptions: {
    ecmaVersion: 'latest',
    sourceType: 'module',
    project: './tsconfig.json'
  },
  plugins: ['react-refresh', '@typescript-eslint', 'react'],
  rules: {
    'react-refresh/only-export-components': [
      'warn',
      { allowConstantExport: true }
    ],
    'react/react-in-jsx-scope': 'off',
    '@typescript-eslint/no-unused-vars': ['error', {
      argsIgnorePattern: '^_',
      varsIgnorePattern: '^_'
    }]
  },
  settings: {
    react: {
      version: 'detect'
    }
  }
}
```

### Next.js Config

```javascript
// .eslintrc.json
{
  "extends": [
    "next/core-web-vitals",
    "plugin:@typescript-eslint/recommended",
    "prettier"
  ],
  "rules": {
    "@typescript-eslint/no-unused-vars": "error",
    "@typescript-eslint/no-explicit-any": "warn",
    "no-console": ["warn", { "allow": ["warn", "error"] }]
  }
}
```

### Monorepo Config

```javascript
// packages/app/.eslintrc.cjs
module.exports = {
  extends: ['../../.eslintrc.cjs'],
  parserOptions: {
    project: './tsconfig.json',
    tsconfigRootDir: __dirname
  }
}
```

## Best Practices

### 1. Use Shareable Configs

```javascript
// ✅ Good: Use established configs
extends: [
  'eslint:recommended',
  'plugin:react/recommended',
  'plugin:@typescript-eslint/recommended'
]

// ❌ Bad: Configure everything manually
```

### 2. Disable Rules Sparingly

```javascript
// ✅ Good: Disable with reason
rules: {
  // Disabled because we use TypeScript for prop validation
  'react/prop-types': 'off'
}

// ❌ Bad: Disable without reason
rules: {
  'no-console': 'off',
  '@typescript-eslint/no-explicit-any': 'off'
}
```

### 3. Use Inline Disables Carefully

```typescript
// ✅ Good: Specific disable with reason
// eslint-disable-next-line @typescript-eslint/no-explicit-any -- Legacy code
const data: any = legacyFunction()

// ❌ Bad: Broad disable
/* eslint-disable */
```

### 4. Fix Auto-fixable Issues

```bash
# ✅ Good: Auto-fix when possible
npm run lint:fix

# ❌ Bad: Manual fixes for auto-fixable issues
```

### 5. Run in CI/CD

```yaml
# .github/workflows/ci.yml
name: CI
on: [push, pull_request]
jobs:
  lint:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - uses: actions/setup-node@v3
      - run: npm ci
      - run: npm run lint
```

## Common Pitfalls

### 1. Conflicting with Prettier

```javascript
// ❌ Bad: ESLint formatting rules conflict with Prettier
rules: {
  'indent': ['error', 2],
  'quotes': ['error', 'single']
}

// ✅ Good: Use eslint-config-prettier
extends: ['prettier']
```

### 2. Not Ignoring Build Files

```
# ❌ Bad: Linting build files
# No .eslintignore

# ✅ Good: Ignore build artifacts
# .eslintignore
dist/
build/
node_modules/
```

### 3. Too Strict for Beginners

```javascript
// ❌ Bad: All errors for new team
rules: {
  'no-console': 'error',
  '@typescript-eslint/no-explicit-any': 'error'
}

// ✅ Good: Start with warnings
rules: {
  'no-console': 'warn',
  '@typescript-eslint/no-explicit-any': 'warn'
}
```

### 4. Not Using TypeScript-Aware Rules

```javascript
// ❌ Bad: Using base rule with TypeScript
rules: {
  'no-unused-vars': 'error'
}

// ✅ Good: Use TypeScript version
rules: {
  'no-unused-vars': 'off',
  '@typescript-eslint/no-unused-vars': 'error'
}
```

## Resources

### Official Documentation
- [ESLint Docs](https://eslint.org/)
- [Rules Reference](https://eslint.org/docs/latest/rules/)
- [Configuring ESLint](https://eslint.org/docs/latest/use/configure/)

### Plugins
- [@typescript-eslint](https://typescript-eslint.io/)
- [eslint-plugin-react](https://github.com/jsx-eslint/eslint-plugin-react)
- [eslint-plugin-react-hooks](https://www.npmjs.com/package/eslint-plugin-react-hooks)
- [eslint-plugin-jsx-a11y](https://github.com/jsx-eslint/eslint-plugin-jsx-a11y)

### Shareable Configs
- [Airbnb Style Guide](https://github.com/airbnb/javascript)
- [Standard JS](https://standardjs.com/)
- [Google Style Guide](https://github.com/google/eslint-config-google)

### Tools
- [ESLint Playground](https://eslint.org/play/)
- [ESLint Config Inspector](https://github.com/eslint/config-inspector)

### Community
- [GitHub Discussions](https://github.com/eslint/eslint/discussions)
- [Discord](https://eslint.org/chat)
- [Stack Overflow](https://stackoverflow.com/questions/tagged/eslint)

---

**Next Steps:**
- Set up [Prettier](./prettier.md) for code formatting
- Learn [TypeScript](./typescript.md) for type checking
- Explore [Biome](./biome.md) as an all-in-one alternative
