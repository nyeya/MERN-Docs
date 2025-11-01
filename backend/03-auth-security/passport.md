# Passport.js - Authentication Middleware

## Table of Contents
- [What is Passport?](#what-is-passport)
- [Why Use Passport?](#why-use-passport)
- [Passport vs Alternatives](#passport-vs-alternatives)
- [Installation & Setup](#installation--setup)
- [Strategies](#strategies)
- [Local Strategy](#local-strategy)
- [JWT Strategy](#jwt-strategy)
- [OAuth Strategies](#oauth-strategies)
- [Session Management](#session-management)
- [Best Practices](#best-practices)
- [Real-World Examples](#real-world-examples)

## What is Passport?

Passport is authentication middleware for Node.js. It's designed to serve a singular purpose: authenticate requests. With over 500+ authentication strategies, Passport provides a simple, unobtrusive way to authenticate users in your application.

### Key Features
- **500+ Strategies**: Local, OAuth, OpenID, and more
- **Unobtrusive**: Doesn't mount routes or assume schemas
- **Modular**: Use only what you need
- **Session Support**: Optional session management
- **Flexible**: Works with any database
- **Middleware-Based**: Integrates seamlessly with Express

## Why Use Passport?

### 1. **Unified Authentication API**

**Without Passport:**
```javascript
// Local authentication
app.post('/login', async (req, res) => {
  const user = await User.findOne({ email: req.body.email });
  if (!user || !await bcrypt.compare(req.body.password, user.password)) {
    return res.status(401).json({ error: 'Invalid credentials' });
  }
  req.session.userId = user.id;
  res.json({ user });
});

// Google OAuth (complex setup)
// Facebook OAuth (different setup)
// Twitter OAuth (another different setup)
// Each requires different implementation
```

**With Passport:**
```javascript
// Local authentication
app.post('/login', passport.authenticate('local'), (req, res) => {
  res.json({ user: req.user });
});

// Google OAuth
app.get('/auth/google', passport.authenticate('google'));

// Facebook OAuth
app.get('/auth/facebook', passport.authenticate('facebook'));

// Same API for all strategies!
```

### 2. **Multiple Authentication Methods**

```javascript
// Support multiple auth methods in one app
passport.use(new LocalStrategy(verifyLocal));
passport.use(new JwtStrategy(jwtOptions, verifyJwt));
passport.use(new GoogleStrategy(googleOptions, verifyGoogle));
passport.use(new FacebookStrategy(facebookOptions, verifyFacebook));

// Use appropriate strategy per route
app.post('/login', passport.authenticate('local'));
app.get('/api/profile', passport.authenticate('jwt'));
app.get('/auth/google', passport.authenticate('google'));
```

### 3. **Proven and Battle-Tested**

```javascript
// Used by thousands of companies
// Extensive documentation
// Large community
// Regular updates
// Security best practices built-in
```

## Passport vs Alternatives

### Passport vs Manual Authentication

| Feature | Passport | Manual |
|---------|----------|--------|
| **Setup Time** | Fast | Slow |
| **OAuth Support** | 500+ providers | Manual each |
| **Security** | Battle-tested | DIY |
| **Maintenance** | Community | You |
| **Flexibility** | High | Complete |

**When to choose Passport:**
- Need multiple auth methods
- Want OAuth support
- Prefer proven solution
- Save development time

**When to choose Manual:**
- Very simple auth only
- Custom requirements
- Learning purposes

### Passport vs Auth0/Firebase Auth

| Feature | Passport | Auth0/Firebase |
|---------|----------|----------------|
| **Hosting** | Self-hosted | Third-party |
| **Cost** | Free | Paid (free tier) |
| **Control** | Complete | Limited |
| **Setup** | Code | Dashboard |
| **Scalability** | DIY | Automatic |

**When to choose Passport:**
- Want full control
- Self-hosted preferred
- No external dependencies
- Custom requirements

**When to choose Auth0/Firebase:**
- Want managed service
- Need quick setup
- Prefer no maintenance
- Built-in features (MFA, etc.)

### Passport vs NextAuth.js

| Feature | Passport | NextAuth.js |
|---------|----------|-------------|
| **Framework** | Express/Any | Next.js only |
| **Setup** | More code | Less code |
| **Flexibility** | Very flexible | Opinionated |
| **Database** | Any | Limited |

## Installation & Setup

### Installation

```bash
# Core
npm install passport

# Strategies (install as needed)
npm install passport-local
npm install passport-jwt
npm install passport-google-oauth20
npm install passport-facebook

# Additional
npm install bcrypt
npm install express-session
```

### Basic Setup

```javascript
const express = require('express');
const passport = require('passport');
const session = require('express-session');

const app = express();

// Middleware
app.use(express.json());
app.use(session({
  secret: 'your-secret-key',
  resave: false,
  saveUninitialized: false
}));

// Initialize Passport
app.use(passport.initialize());
app.use(passport.session());

// Serialize user
passport.serializeUser((user, done) => {
  done(null, user.id);
});

// Deserialize user
passport.deserializeUser(async (id, done) => {
  try {
    const user = await User.findById(id);
    done(null, user);
  } catch (err) {
    done(err);
  }
});

app.listen(3000);
```

## Strategies

### What are Strategies?

Strategies are authentication mechanisms. Each strategy handles authentication differently:

- **Local**: Username/password
- **JWT**: JSON Web Tokens
- **OAuth**: Google, Facebook, Twitter, etc.
- **OpenID**: OpenID Connect
- **SAML**: Enterprise SSO

### Installing Strategies

```bash
# Local authentication
npm install passport-local

# JWT authentication
npm install passport-jwt

# OAuth providers
npm install passport-google-oauth20
npm install passport-facebook
npm install passport-twitter
npm install passport-github2
```

## Local Strategy

### Setup

```bash
npm install passport-local bcrypt
```

### Implementation

```javascript
const passport = require('passport');
const LocalStrategy = require('passport-local').Strategy;
const bcrypt = require('bcrypt');

// Configure strategy
passport.use(new LocalStrategy(
  {
    usernameField: 'email',
    passwordField: 'password'
  },
  async (email, password, done) => {
    try {
      // Find user
      const user = await User.findOne({ email });
      
      if (!user) {
        return done(null, false, { message: 'Invalid credentials' });
      }
      
      // Verify password
      const isValid = await bcrypt.compare(password, user.password);
      
      if (!isValid) {
        return done(null, false, { message: 'Invalid credentials' });
      }
      
      // Success
      return done(null, user);
    } catch (err) {
      return done(err);
    }
  }
));

// Login route
app.post('/login',
  passport.authenticate('local', { session: false }),
  (req, res) => {
    res.json({
      message: 'Login successful',
      user: req.user
    });
  }
);

// Or with custom callback
app.post('/login', (req, res, next) => {
  passport.authenticate('local', (err, user, info) => {
    if (err) {
      return next(err);
    }
    
    if (!user) {
      return res.status(401).json({ error: info.message });
    }
    
    req.logIn(user, (err) => {
      if (err) {
        return next(err);
      }
      
      return res.json({ user });
    });
  })(req, res, next);
});
```

### Registration

```javascript
app.post('/register', async (req, res) => {
  try {
    const { email, password, name } = req.body;
    
    // Check if user exists
    const existingUser = await User.findOne({ email });
    if (existingUser) {
      return res.status(400).json({ error: 'Email already registered' });
    }
    
    // Hash password
    const hashedPassword = await bcrypt.hash(password, 10);
    
    // Create user
    const user = await User.create({
      email,
      password: hashedPassword,
      name
    });
    
    // Auto-login after registration
    req.logIn(user, (err) => {
      if (err) {
        return res.status(500).json({ error: 'Registration successful but login failed' });
      }
      
      res.status(201).json({ user });
    });
  } catch (err) {
    res.status(500).json({ error: 'Registration failed' });
  }
});
```

## JWT Strategy

### Setup

```bash
npm install passport-jwt jsonwebtoken
```

### Implementation

```javascript
const passport = require('passport');
const JwtStrategy = require('passport-jwt').Strategy;
const ExtractJwt = require('passport-jwt').ExtractJwt;
const jwt = require('jsonwebtoken');

const JWT_SECRET = process.env.JWT_SECRET;

// Configure JWT strategy
const jwtOptions = {
  jwtFromRequest: ExtractJwt.fromAuthHeaderAsBearerToken(),
  secretOrKey: JWT_SECRET
};

passport.use(new JwtStrategy(jwtOptions, async (payload, done) => {
  try {
    const user = await User.findById(payload.userId);
    
    if (!user) {
      return done(null, false);
    }
    
    return done(null, user);
  } catch (err) {
    return done(err, false);
  }
}));

// Login route (generate JWT)
app.post('/login', async (req, res) => {
  const { email, password } = req.body;
  
  // Verify credentials
  const user = await User.findOne({ email });
  if (!user || !await bcrypt.compare(password, user.password)) {
    return res.status(401).json({ error: 'Invalid credentials' });
  }
  
  // Generate JWT
  const token = jwt.sign(
    { userId: user.id, email: user.email },
    JWT_SECRET,
    { expiresIn: '7d' }
  );
  
  res.json({ token, user });
});

// Protected route
app.get('/profile',
  passport.authenticate('jwt', { session: false }),
  (req, res) => {
    res.json({ user: req.user });
  }
);
```

### Custom JWT Extraction

```javascript
const jwtOptions = {
  jwtFromRequest: ExtractJwt.fromExtractors([
    ExtractJwt.fromAuthHeaderAsBearerToken(),
    ExtractJwt.fromUrlQueryParameter('token'),
    (req) => req.cookies?.token
  ]),
  secretOrKey: JWT_SECRET
};
```

## OAuth Strategies

### Google OAuth

```bash
npm install passport-google-oauth20
```

```javascript
const GoogleStrategy = require('passport-google-oauth20').Strategy;

passport.use(new GoogleStrategy({
    clientID: process.env.GOOGLE_CLIENT_ID,
    clientSecret: process.env.GOOGLE_CLIENT_SECRET,
    callbackURL: '/auth/google/callback'
  },
  async (accessToken, refreshToken, profile, done) => {
    try {
      // Find or create user
      let user = await User.findOne({ googleId: profile.id });
      
      if (!user) {
        user = await User.create({
          googleId: profile.id,
          email: profile.emails[0].value,
          name: profile.displayName,
          avatar: profile.photos[0].value
        });
      }
      
      return done(null, user);
    } catch (err) {
      return done(err);
    }
  }
));

// Routes
app.get('/auth/google',
  passport.authenticate('google', { scope: ['profile', 'email'] })
);

app.get('/auth/google/callback',
  passport.authenticate('google', { failureRedirect: '/login' }),
  (req, res) => {
    // Successful authentication
    res.redirect('/dashboard');
  }
);
```

### Facebook OAuth

```bash
npm install passport-facebook
```

```javascript
const FacebookStrategy = require('passport-facebook').Strategy;

passport.use(new FacebookStrategy({
    clientID: process.env.FACEBOOK_APP_ID,
    clientSecret: process.env.FACEBOOK_APP_SECRET,
    callbackURL: '/auth/facebook/callback',
    profileFields: ['id', 'emails', 'name', 'picture']
  },
  async (accessToken, refreshToken, profile, done) => {
    try {
      let user = await User.findOne({ facebookId: profile.id });
      
      if (!user) {
        user = await User.create({
          facebookId: profile.id,
          email: profile.emails[0].value,
          name: `${profile.name.givenName} ${profile.name.familyName}`,
          avatar: profile.photos[0].value
        });
      }
      
      return done(null, user);
    } catch (err) {
      return done(err);
    }
  }
));

// Routes
app.get('/auth/facebook',
  passport.authenticate('facebook', { scope: ['email'] })
);

app.get('/auth/facebook/callback',
  passport.authenticate('facebook', { failureRedirect: '/login' }),
  (req, res) => {
    res.redirect('/dashboard');
  }
);
```

### GitHub OAuth

```bash
npm install passport-github2
```

```javascript
const GitHubStrategy = require('passport-github2').Strategy;

passport.use(new GitHubStrategy({
    clientID: process.env.GITHUB_CLIENT_ID,
    clientSecret: process.env.GITHUB_CLIENT_SECRET,
    callbackURL: '/auth/github/callback'
  },
  async (accessToken, refreshToken, profile, done) => {
    try {
      let user = await User.findOne({ githubId: profile.id });
      
      if (!user) {
        user = await User.create({
          githubId: profile.id,
          email: profile.emails[0].value,
          name: profile.displayName,
          username: profile.username,
          avatar: profile.photos[0].value
        });
      }
      
      return done(null, user);
    } catch (err) {
      return done(err);
    }
  }
));

// Routes
app.get('/auth/github',
  passport.authenticate('github', { scope: ['user:email'] })
);

app.get('/auth/github/callback',
  passport.authenticate('github', { failureRedirect: '/login' }),
  (req, res) => {
    res.redirect('/dashboard');
  }
);
```

## Session Management

### With Sessions

```javascript
const session = require('express-session');

app.use(session({
  secret: process.env.SESSION_SECRET,
  resave: false,
  saveUninitialized: false,
  cookie: {
    secure: process.env.NODE_ENV === 'production',
    httpOnly: true,
    maxAge: 24 * 60 * 60 * 1000 // 24 hours
  }
}));

app.use(passport.initialize());
app.use(passport.session());

// Serialize user into session
passport.serializeUser((user, done) => {
  done(null, user.id);
});

// Deserialize user from session
passport.deserializeUser(async (id, done) => {
  try {
    const user = await User.findById(id);
    done(null, user);
  } catch (err) {
    done(err);
  }
});

// Login
app.post('/login',
  passport.authenticate('local'),
  (req, res) => {
    res.json({ user: req.user });
  }
);

// Logout
app.post('/logout', (req, res) => {
  req.logout((err) => {
    if (err) {
      return res.status(500).json({ error: 'Logout failed' });
    }
    res.json({ message: 'Logged out successfully' });
  });
});

// Check authentication
app.get('/profile', (req, res) => {
  if (!req.isAuthenticated()) {
    return res.status(401).json({ error: 'Not authenticated' });
  }
  res.json({ user: req.user });
});
```

### Without Sessions (Stateless)

```javascript
// No session middleware needed
app.use(passport.initialize());

// Use JWT strategy
app.post('/login',
  passport.authenticate('local', { session: false }),
  (req, res) => {
    const token = jwt.sign({ userId: req.user.id }, JWT_SECRET);
    res.json({ token, user: req.user });
  }
);

// Protected routes
app.get('/profile',
  passport.authenticate('jwt', { session: false }),
  (req, res) => {
    res.json({ user: req.user });
  }
);
```

## Best Practices

### 1. Environment Variables

```javascript
// .env
JWT_SECRET=your-super-secret-jwt-key
SESSION_SECRET=your-session-secret
GOOGLE_CLIENT_ID=your-google-client-id
GOOGLE_CLIENT_SECRET=your-google-client-secret

// config/passport.js
require('dotenv').config();

const jwtOptions = {
  jwtFromRequest: ExtractJwt.fromAuthHeaderAsBearerToken(),
  secretOrKey: process.env.JWT_SECRET
};
```

### 2. Separate Configuration

```javascript
// config/passport.js
const passport = require('passport');
const LocalStrategy = require('passport-local').Strategy;
const JwtStrategy = require('passport-jwt').Strategy;

// Local strategy
require('./strategies/local')(passport);

// JWT strategy
require('./strategies/jwt')(passport);

// Google strategy
require('./strategies/google')(passport);

module.exports = passport;

// strategies/local.js
module.exports = (passport) => {
  passport.use(new LocalStrategy(/* ... */));
};
```

### 3. Authentication Middleware

```javascript
// middleware/auth.js
exports.isAuthenticated = (req, res, next) => {
  if (req.isAuthenticated()) {
    return next();
  }
  res.status(401).json({ error: 'Not authenticated' });
};

exports.isAdmin = (req, res, next) => {
  if (req.user && req.user.role === 'admin') {
    return next();
  }
  res.status(403).json({ error: 'Insufficient permissions' });
};

// Usage
app.get('/admin', isAuthenticated, isAdmin, (req, res) => {
  res.json({ message: 'Admin area' });
});
```

### 4. Error Handling

```javascript
app.post('/login', (req, res, next) => {
  passport.authenticate('local', (err, user, info) => {
    if (err) {
      return res.status(500).json({ error: 'Authentication error' });
    }
    
    if (!user) {
      return res.status(401).json({ error: info.message || 'Invalid credentials' });
    }
    
    req.logIn(user, (err) => {
      if (err) {
        return res.status(500).json({ error: 'Login error' });
      }
      
      return res.json({ user });
    });
  })(req, res, next);
});
```

### 5. Multiple Strategies

```javascript
// Allow multiple auth methods
app.get('/profile',
  passport.authenticate(['jwt', 'session'], { session: false }),
  (req, res) => {
    res.json({ user: req.user });
  }
);
```

## Real-World Examples

### Complete Authentication System

```javascript
// app.js
const express = require('express');
const passport = require('./config/passport');
const authRoutes = require('./routes/auth');
const userRoutes = require('./routes/users');

const app = express();

app.use(express.json());
app.use(passport.initialize());

app.use('/auth', authRoutes);
app.use('/api/users', userRoutes);

app.listen(3000);

// config/passport.js
const passport = require('passport');
const LocalStrategy = require('passport-local').Strategy;
const JwtStrategy = require('passport-jwt').Strategy;
const ExtractJwt = require('passport-jwt').ExtractJwt;
const GoogleStrategy = require('passport-google-oauth20').Strategy;
const bcrypt = require('bcrypt');
const User = require('../models/User');

// Local Strategy
passport.use(new LocalStrategy(
  { usernameField: 'email' },
  async (email, password, done) => {
    try {
      const user = await User.findOne({ email });
      if (!user || !await bcrypt.compare(password, user.password)) {
        return done(null, false, { message: 'Invalid credentials' });
      }
      return done(null, user);
    } catch (err) {
      return done(err);
    }
  }
));

// JWT Strategy
passport.use(new JwtStrategy(
  {
    jwtFromRequest: ExtractJwt.fromAuthHeaderAsBearerToken(),
    secretOrKey: process.env.JWT_SECRET
  },
  async (payload, done) => {
    try {
      const user = await User.findById(payload.userId);
      return done(null, user || false);
    } catch (err) {
      return done(err, false);
    }
  }
));

// Google Strategy
passport.use(new GoogleStrategy(
  {
    clientID: process.env.GOOGLE_CLIENT_ID,
    clientSecret: process.env.GOOGLE_CLIENT_SECRET,
    callbackURL: '/auth/google/callback'
  },
  async (accessToken, refreshToken, profile, done) => {
    try {
      let user = await User.findOne({ googleId: profile.id });
      
      if (!user) {
        user = await User.create({
          googleId: profile.id,
          email: profile.emails[0].value,
          name: profile.displayName,
          avatar: profile.photos[0].value
        });
      }
      
      return done(null, user);
    } catch (err) {
      return done(err);
    }
  }
));

module.exports = passport;

// routes/auth.js
const express = require('express');
const passport = require('passport');
const jwt = require('jsonwebtoken');
const bcrypt = require('bcrypt');
const User = require('../models/User');

const router = express.Router();

// Register
router.post('/register', async (req, res) => {
  try {
    const { email, password, name } = req.body;
    
    const existingUser = await User.findOne({ email });
    if (existingUser) {
      return res.status(400).json({ error: 'Email already registered' });
    }
    
    const hashedPassword = await bcrypt.hash(password, 10);
    const user = await User.create({ email, password: hashedPassword, name });
    
    const token = jwt.sign({ userId: user.id }, process.env.JWT_SECRET, { expiresIn: '7d' });
    
    res.status(201).json({ token, user: { id: user.id, email: user.email, name: user.name } });
  } catch (err) {
    res.status(500).json({ error: 'Registration failed' });
  }
});

// Login
router.post('/login', (req, res, next) => {
  passport.authenticate('local', { session: false }, (err, user, info) => {
    if (err) {
      return res.status(500).json({ error: 'Authentication error' });
    }
    
    if (!user) {
      return res.status(401).json({ error: info.message });
    }
    
    const token = jwt.sign({ userId: user.id }, process.env.JWT_SECRET, { expiresIn: '7d' });
    
    res.json({ token, user: { id: user.id, email: user.email, name: user.name } });
  })(req, res, next);
});

// Google OAuth
router.get('/google', passport.authenticate('google', { scope: ['profile', 'email'], session: false }));

router.get('/google/callback',
  passport.authenticate('google', { session: false, failureRedirect: '/login' }),
  (req, res) => {
    const token = jwt.sign({ userId: req.user.id }, process.env.JWT_SECRET, { expiresIn: '7d' });
    res.redirect(`/auth-success?token=${token}`);
  }
);

module.exports = router;

// routes/users.js
const express = require('express');
const passport = require('passport');

const router = express.Router();

const authenticate = passport.authenticate('jwt', { session: false });

router.get('/profile', authenticate, (req, res) => {
  res.json({ user: req.user });
});

router.put('/profile', authenticate, async (req, res) => {
  try {
    const { name } = req.body;
    req.user.name = name;
    await req.user.save();
    res.json({ user: req.user });
  } catch (err) {
    res.status(500).json({ error: 'Update failed' });
  }
});

module.exports = router;
```

## Summary

Passport is the standard authentication middleware for Node.js:

✅ **Flexible** - 500+ authentication strategies  
✅ **Modular** - Use only what you need  
✅ **Proven** - Battle-tested and secure  
✅ **Simple API** - Consistent across strategies  
✅ **Well-Documented** - Extensive guides and examples  

**Key Takeaways:**
- Use appropriate strategy for your needs
- Separate configuration from routes
- Use JWT for stateless APIs
- Use sessions for traditional web apps
- Implement proper error handling
- Store secrets in environment variables
- Support multiple auth methods when needed
- Follow security best practices

Passport simplifies authentication in Node.js applications.
