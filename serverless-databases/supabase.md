# Supabase - Complete Guide to Open-Source Firebase Alternative

## Table of Contents
- [What is Supabase?](#what-is-supabase)
- [Why Use Supabase?](#why-use-supabase)
- [Supabase vs Alternatives](#supabase-vs-alternatives)
- [Installation & Setup](#installation--setup)
- [Core Concepts](#core-concepts)
- [Database Operations](#database-operations)
- [Authentication](#authentication)
- [Real-time Subscriptions](#real-time-subscriptions)
- [Storage](#storage)
- [Edge Functions](#edge-functions)
- [Row Level Security](#row-level-security)
- [Real-World Examples](#real-world-examples)
- [Best Practices](#best-practices)

## What is Supabase?

Supabase is an open-source Firebase alternative built on PostgreSQL. It provides a complete backend solution with database, authentication, real-time subscriptions, storage, and edge functions.

### Key Features
- **PostgreSQL Database** - Full-featured relational database
- **Auto-generated APIs** - Instant REST and GraphQL APIs
- **Real-time Subscriptions** - Live data updates via WebSockets
- **Authentication** - Built-in auth with multiple providers
- **Storage** - S3-compatible file storage
- **Edge Functions** - Serverless functions with Deno
- **Row Level Security** - PostgreSQL RLS for data protection
- **Dashboard** - Visual database management
- **Open Source** - Self-hostable and transparent

## Why Use Supabase?

### 1. PostgreSQL Power with Modern DX

**Traditional PostgreSQL:**
```javascript
const { Pool } = require('pg');
const pool = new Pool({
  host: 'localhost',
  database: 'mydb',
  user: 'user',
  password: 'password'
});

app.get('/posts', async (req, res) => {
  const client = await pool.connect();
  try {
    const result = await client.query(
      'SELECT * FROM posts WHERE user_id = $1 ORDER BY created_at DESC',
      [req.user.id]
    );
    res.json(result.rows);
  } finally {
    client.release();
  }
});
```

**With Supabase:**
```javascript
import { createClient } from '@supabase/supabase-js';

const supabase = createClient(SUPABASE_URL, SUPABASE_KEY);

const { data, error } = await supabase
  .from('posts')
  .select('*')
  .eq('user_id', userId)
  .order('created_at', { ascending: false });
```

### 2. Built-in Authentication

```javascript
// Email/Password signup
const { data, error } = await supabase.auth.signUp({
  email: 'user@example.com',
  password: 'secure-password'
});

// OAuth providers
const { data, error } = await supabase.auth.signInWithOAuth({
  provider: 'google'
});

// Magic link
const { data, error } = await supabase.auth.signInWithOtp({
  email: 'user@example.com'
});
```

### 3. Real-time Out of the Box

```javascript
const channel = supabase
  .channel('todos')
  .on('postgres_changes', 
    { event: '*', schema: 'public', table: 'todos' },
    (payload) => {
      console.log('Change received!', payload);
    }
  )
  .subscribe();
```

## Supabase vs Alternatives

### Supabase vs Firebase

| Feature | Supabase | Firebase |
|---------|----------|----------|
| **Database** | PostgreSQL (SQL) | Firestore (NoSQL) |
| **Open Source** | ✅ Yes | ❌ No |
| **Self-Hosting** | ✅ Yes | ❌ No |
| **Pricing** | More generous | Limited |
| **Query Language** | SQL | Firebase queries |
| **Relationships** | Native joins | Manual |
| **Learning Curve** | SQL knowledge | Easier |

**Choose Supabase if:**
- You prefer SQL and relational data
- You want open-source options
- You need complex queries
- You want better pricing

**Choose Firebase if:**
- Building mobile apps primarily
- Prefer NoSQL structure
- Need Google Cloud integration
- Want easiest setup

### Supabase vs Neon

| Feature | Supabase | Neon |
|---------|----------|------|
| **Database** | PostgreSQL + Features | PostgreSQL only |
| **Authentication** | ✅ Built-in | ❌ No |
| **Storage** | ✅ Built-in | ❌ No |
| **Real-time** | ✅ Built-in | ❌ No |
| **Dashboard** | Full-featured | Database only |
| **Branching** | Limited | ✅ Excellent |

## Installation & Setup

### 1. Create Supabase Project

```bash
# Visit https://supabase.com and create account
# Create new project (takes ~2 minutes)
# Note your project URL and anon key
```

### 2. Install Client Library

```bash
npm install @supabase/supabase-js

# For React
npm install @supabase/auth-helpers-react

# For Next.js
npm install @supabase/auth-helpers-nextjs
```

### 3. Environment Variables

**.env:**
```env
VITE_SUPABASE_URL=https://your-project.supabase.co
VITE_SUPABASE_ANON_KEY=your-anon-key
SUPABASE_SERVICE_ROLE_KEY=your-service-role-key
```

### 4. Initialize Client

**lib/supabase.js:**
```javascript
import { createClient } from '@supabase/supabase-js';

const supabaseUrl = import.meta.env.VITE_SUPABASE_URL;
const supabaseAnonKey = import.meta.env.VITE_SUPABASE_ANON_KEY;

export const supabase = createClient(supabaseUrl, supabaseAnonKey);
```

**TypeScript:**
```typescript
import { createClient } from '@supabase/supabase-js';
import { Database } from './database.types';

export const supabase = createClient<Database>(
  import.meta.env.VITE_SUPABASE_URL,
  import.meta.env.VITE_SUPABASE_ANON_KEY
);
```

### 5. Generate TypeScript Types

```bash
npm install -g supabase
supabase login
supabase gen types typescript --project-id your-id > database.types.ts
```

## Core Concepts

### Tables and Schemas

```sql
-- Create table in Supabase SQL Editor
CREATE TABLE posts (
  id UUID DEFAULT gen_random_uuid() PRIMARY KEY,
  title TEXT NOT NULL,
  content TEXT,
  user_id UUID REFERENCES auth.users(id) ON DELETE CASCADE,
  created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
  updated_at TIMESTAMP WITH TIME ZONE DEFAULT NOW()
);

CREATE INDEX posts_user_id_idx ON posts(user_id);
```

### Query Builder

```javascript
// Select specific columns
const { data } = await supabase
  .from('posts')
  .select('id, title, created_at');

// With relationships
const { data } = await supabase
  .from('posts')
  .select(`
    *,
    author:users(name, email),
    comments(*)
  `);

// Filtering
const { data } = await supabase
  .from('posts')
  .select('*')
  .eq('status', 'published')
  .gte('created_at', '2024-01-01')
  .order('created_at', { ascending: false })
  .limit(10);
```

## Database Operations

### Create (Insert)

```javascript
// Single row
const { data, error } = await supabase
  .from('posts')
  .insert({ 
    title: 'My Post',
    content: 'Content',
    user_id: userId 
  })
  .select()
  .single();

// Multiple rows
const { data, error } = await supabase
  .from('posts')
  .insert([
    { title: 'Post 1', content: 'Content 1' },
    { title: 'Post 2', content: 'Content 2' }
  ])
  .select();

// Upsert
const { data, error } = await supabase
  .from('posts')
  .upsert({ id: postId, title: 'Updated' })
  .select();
```

### Read (Select)

```javascript
// Get all
const { data, error } = await supabase
  .from('posts')
  .select('*');

// Get single
const { data, error } = await supabase
  .from('posts')
  .select('*')
  .eq('id', postId)
  .single();

// Complex query
const { data, error } = await supabase
  .from('posts')
  .select(`
    id,
    title,
    author:users!user_id(name, avatar_url),
    comments(count)
  `)
  .eq('status', 'published')
  .order('created_at', { ascending: false })
  .range(0, 9);

// Full-text search
const { data, error } = await supabase
  .from('posts')
  .select('*')
  .textSearch('title', 'javascript', {
    type: 'websearch',
    config: 'english'
  });
```

### Update

```javascript
// Update single
const { data, error } = await supabase
  .from('posts')
  .update({ title: 'New Title' })
  .eq('id', postId)
  .select();

// Update multiple
const { data, error } = await supabase
  .from('posts')
  .update({ status: 'archived' })
  .eq('user_id', userId)
  .select();
```

### Delete

```javascript
// Delete single
const { error } = await supabase
  .from('posts')
  .delete()
  .eq('id', postId);

// Delete multiple
const { error } = await supabase
  .from('posts')
  .delete()
  .eq('status', 'draft')
  .lt('created_at', '2023-01-01');
```

## Authentication

### Email/Password

```javascript
// Sign up
const { data, error } = await supabase.auth.signUp({
  email: 'user@example.com',
  password: 'secure-password',
  options: {
    data: {
      first_name: 'John',
      last_name: 'Doe'
    }
  }
});

// Sign in
const { data, error } = await supabase.auth.signInWithPassword({
  email: 'user@example.com',
  password: 'secure-password'
});

// Sign out
const { error } = await supabase.auth.signOut();

// Get user
const { data: { user } } = await supabase.auth.getUser();

// Get session
const { data: { session } } = await supabase.auth.getSession();
```

### OAuth Providers

```javascript
// Google
const { data, error } = await supabase.auth.signInWithOAuth({
  provider: 'google',
  options: {
    redirectTo: 'http://localhost:3000/auth/callback'
  }
});

// GitHub
const { data, error } = await supabase.auth.signInWithOAuth({
  provider: 'github'
});

// Available: google, github, gitlab, bitbucket, azure,
// facebook, discord, twitter, slack, spotify, twitch, linkedin
```

### Magic Link

```javascript
// Send magic link
const { data, error } = await supabase.auth.signInWithOtp({
  email: 'user@example.com',
  options: {
    emailRedirectTo: 'http://localhost:3000/auth/callback'
  }
});

// Phone OTP
const { data, error } = await supabase.auth.signInWithOtp({
  phone: '+1234567890'
});

// Verify OTP
const { data, error } = await supabase.auth.verifyOtp({
  phone: '+1234567890',
  token: '123456',
  type: 'sms'
});
```

### Password Reset

```javascript
// Request reset
const { data, error } = await supabase.auth.resetPasswordForEmail(
  'user@example.com',
  {
    redirectTo: 'http://localhost:3000/reset-password'
  }
);

// Update password
const { data, error } = await supabase.auth.updateUser({
  password: 'new-secure-password'
});
```

### Auth State (React)

```javascript
import { useEffect, useState } from 'react';
import { supabase } from './lib/supabase';

function App() {
  const [user, setUser] = useState(null);

  useEffect(() => {
    supabase.auth.getSession().then(({ data: { session } }) => {
      setUser(session?.user ?? null);
    });

    const { data: { subscription } } = supabase.auth.onAuthStateChange(
      (_event, session) => {
        setUser(session?.user ?? null);
      }
    );

    return () => subscription.unsubscribe();
  }, []);

  return user ? <Dashboard user={user} /> : <Login />;
}
```

## Real-time Subscriptions

### Database Changes

```javascript
// Subscribe to all changes
const channel = supabase
  .channel('todos-channel')
  .on(
    'postgres_changes',
    { event: '*', schema: 'public', table: 'todos' },
    (payload) => {
      console.log('Change received!', payload);
    }
  )
  .subscribe();

// Specific events
const channel = supabase
  .channel('posts-channel')
  .on(
    'postgres_changes',
    { event: 'INSERT', schema: 'public', table: 'posts' },
    (payload) => console.log('New:', payload.new)
  )
  .on(
    'postgres_changes',
    { event: 'UPDATE', schema: 'public', table: 'posts' },
    (payload) => console.log('Updated:', payload.new)
  )
  .on(
    'postgres_changes',
    { event: 'DELETE', schema: 'public', table: 'posts' },
    (payload) => console.log('Deleted:', payload.old)
  )
  .subscribe();

// Filter by rows
const channel = supabase
  .channel('user-todos')
  .on(
    'postgres_changes',
    {
      event: '*',
      schema: 'public',
      table: 'todos',
      filter: `user_id=eq.${userId}`
    },
    (payload) => console.log('User todo:', payload)
  )
  .subscribe();

// Unsubscribe
channel.unsubscribe();
```

### React Real-time Hook

```javascript
import { useEffect, useState } from 'react';
import { supabase } from './lib/supabase';

function useTodos(userId) {
  const [todos, setTodos] = useState([]);

  useEffect(() => {
    const fetchTodos = async () => {
      const { data } = await supabase
        .from('todos')
        .select('*')
        .eq('user_id', userId)
        .order('created_at', { ascending: false });
      
      setTodos(data || []);
    };

    fetchTodos();

    const channel = supabase
      .channel('todos')
      .on(
        'postgres_changes',
        {
          event: '*',
          schema: 'public',
          table: 'todos',
          filter: `user_id=eq.${userId}`
        },
        (payload) => {
          if (payload.eventType === 'INSERT') {
            setTodos(prev => [payload.new, ...prev]);
          } else if (payload.eventType === 'UPDATE') {
            setTodos(prev =>
              prev.map(todo =>
                todo.id === payload.new.id ? payload.new : todo
              )
            );
          } else if (payload.eventType === 'DELETE') {
            setTodos(prev =>
              prev.filter(todo => todo.id !== payload.old.id)
            );
          }
        }
      )
      .subscribe();

    return () => channel.unsubscribe();
  }, [userId]);

  return todos;
}
```

## Storage

### Upload Files

```javascript
// Upload file
const file = event.target.files[0];
const fileExt = file.name.split('.').pop();
const fileName = `${Math.random()}.${fileExt}`;
const filePath = `${userId}/${fileName}`;

const { data, error } = await supabase.storage
  .from('avatars')
  .upload(filePath, file);

// With options
const { data, error } = await supabase.storage
  .from('avatars')
  .upload(filePath, file, {
    cacheControl: '3600',
    upsert: false,
    contentType: 'image/png'
  });
```

### Download Files

```javascript
// Download
const { data, error } = await supabase.storage
  .from('avatars')
  .download(filePath);

// Public URL
const { data } = supabase.storage
  .from('avatars')
  .getPublicUrl(filePath);

// Signed URL (private)
const { data, error } = await supabase.storage
  .from('private-files')
  .createSignedUrl(filePath, 60); // 60 seconds
```

### Storage Policies

```sql
-- Upload own avatar
CREATE POLICY "Users can upload own avatar"
ON storage.objects FOR INSERT
WITH CHECK (
  bucket_id = 'avatars' AND
  auth.uid()::text = (storage.foldername(name))[1]
);

-- View own avatar
CREATE POLICY "Users can view own avatar"
ON storage.objects FOR SELECT
USING (
  bucket_id = 'avatars' AND
  auth.uid()::text = (storage.foldername(name))[1]
);

-- Public read
CREATE POLICY "Public avatars viewable"
ON storage.objects FOR SELECT
USING (bucket_id = 'avatars');
```

## Row Level Security

### Enable RLS

```sql
ALTER TABLE posts ENABLE ROW LEVEL SECURITY;
```

### Common Policies

```sql
-- View own posts
CREATE POLICY "Users can view own posts"
ON posts FOR SELECT
USING (auth.uid() = user_id);

-- Insert own posts
CREATE POLICY "Users can insert own posts"
ON posts FOR INSERT
WITH CHECK (auth.uid() = user_id);

-- Update own posts
CREATE POLICY "Users can update own posts"
ON posts FOR UPDATE
USING (auth.uid() = user_id)
WITH CHECK (auth.uid() = user_id);

-- Delete own posts
CREATE POLICY "Users can delete own posts"
ON posts FOR DELETE
USING (auth.uid() = user_id);

-- Public posts viewable
CREATE POLICY "Public posts viewable"
ON posts FOR SELECT
USING (status = 'published');

-- Admin access
CREATE POLICY "Admins can do everything"
ON posts FOR ALL
USING (auth.jwt() ->> 'role' = 'admin');
```

## Best Practices

### 1. Use TypeScript

```typescript
import { Database } from './database.types';

type Post = Database['public']['Tables']['posts']['Row'];
type PostInsert = Database['public']['Tables']['posts']['Insert'];
type PostUpdate = Database['public']['Tables']['posts']['Update'];
```

### 2. Error Handling

```javascript
const { data, error } = await supabase
  .from('posts')
  .select('*');

if (error) {
  console.error('Error fetching posts:', error);
  // Handle error appropriately
  return;
}

// Use data safely
console.log(data);
```

### 3. Connection Pooling

```javascript
// Use Supavisor for connection pooling
const supabase = createClient(
  'https://your-project.supabase.co',
  'your-anon-key',
  {
    db: {
      schema: 'public',
    },
    auth: {
      persistSession: true,
    },
  }
);
```

### 4. Optimize Queries

```javascript
// Bad - Multiple queries
const { data: posts } = await supabase.from('posts').select('*');
for (const post of posts) {
  const { data: author } = await supabase
    .from('users')
    .select('*')
    .eq('id', post.user_id)
    .single();
}

// Good - Single query with join
const { data: posts } = await supabase
  .from('posts')
  .select(`
    *,
    author:users(*)
  `);
```

### 5. Use Indexes

```sql
-- Add indexes for frequently queried columns
CREATE INDEX posts_user_id_idx ON posts(user_id);
CREATE INDEX posts_created_at_idx ON posts(created_at DESC);
CREATE INDEX posts_status_idx ON posts(status);
```

### 6. Implement Caching

```javascript
import { useQuery } from '@tanstack/react-query';

function usePosts() {
  return useQuery({
    queryKey: ['posts'],
    queryFn: async () => {
      const { data } = await supabase
        .from('posts')
        .select('*');
      return data;
    },
    staleTime: 5 * 60 * 1000, // 5 minutes
  });
}
```

### 7. Secure API Keys

```javascript
// Never expose service role key in frontend
// Use anon key with RLS policies
const supabase = createClient(
  process.env.VITE_SUPABASE_URL,
  process.env.VITE_SUPABASE_ANON_KEY // Safe for frontend
);

// Use service role only in backend
const supabaseAdmin = createClient(
  process.env.SUPABASE_URL,
  process.env.SUPABASE_SERVICE_ROLE_KEY // Backend only!
);
```

## Resources

- [Official Documentation](https://supabase.com/docs)
- [Supabase GitHub](https://github.com/supabase/supabase)
- [Community Discord](https://discord.supabase.com)
- [YouTube Tutorials](https://www.youtube.com/c/supabase)
- [Example Apps](https://github.com/supabase/supabase/tree/master/examples)
