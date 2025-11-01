# Socket.io - Real-Time Bidirectional Communication

## Table of Contents
- [What is Socket.io?](#what-is-socketio)
- [Why Use Socket.io?](#why-use-socketio)
- [Socket.io vs Alternatives](#socketio-vs-alternatives)
- [Installation & Setup](#installation--setup)
- [Basic Concepts](#basic-concepts)
- [Events](#events)
- [Rooms & Namespaces](#rooms--namespaces)
- [Authentication](#authentication)
- [Broadcasting](#broadcasting)
- [Best Practices](#best-practices)
- [Real-World Examples](#real-world-examples)

## What is Socket.io?

Socket.io is a library that enables real-time, bidirectional, and event-based communication between web clients and servers. It consists of a Node.js server and a JavaScript client library for the browser (or Node.js client).

### Key Features
- **Real-Time**: Instant bidirectional communication
- **Event-Based**: Custom event system
- **Automatic Reconnection**: Handles connection drops
- **Binary Support**: Send any data type
- **Rooms & Namespaces**: Organize connections
- **Fallback Support**: Works even without WebSocket
- **Broadcasting**: Send to multiple clients
- **Acknowledgements**: Callback-based responses

## Why Use Socket.io?

### 1. **Real-Time Communication**

**Without Socket.io (HTTP Polling):**
```javascript
// Client polls server every second
setInterval(async () => {
  const response = await fetch('/api/messages');
  const messages = await response.json();
  updateUI(messages);
}, 1000);

// Problems:
// - Wastes bandwidth
// - High latency
// - Server load
// - Not truly real-time
```

**With Socket.io:**
```javascript
// Server pushes updates instantly
socket.on('new-message', (message) => {
  updateUI(message);
});

// Benefits:
// - Instant updates
// - Low bandwidth
// - Efficient
// - True real-time
```

### 2. **Bidirectional Communication**

```javascript
// Client to Server
socket.emit('send-message', { text: 'Hello' });

// Server to Client
socket.emit('new-message', { text: 'Hi there!' });

// Both directions simultaneously
```

### 3. **Automatic Reconnection**

```javascript
// Handles connection issues automatically
socket.on('disconnect', () => {
  console.log('Disconnected, will retry...');
});

socket.on('reconnect', () => {
  console.log('Reconnected!');
});

// No manual reconnection logic needed
```

### 4. **Broadcasting**

```javascript
// Send to all clients
io.emit('announcement', 'Server maintenance in 5 minutes');

// Send to all except sender
socket.broadcast.emit('user-joined', { username: 'Alice' });

// Send to specific room
io.to('room1').emit('message', 'Hello room!');
```

## Socket.io vs Alternatives

### Socket.io vs WebSocket (Native)

| Feature | Socket.io | Native WebSocket |
|---------|-----------|------------------|
| **Fallback** | Automatic | None |
| **Reconnection** | Automatic | Manual |
| **Events** | Custom events | Message only |
| **Rooms** | Built-in | Manual |
| **Broadcasting** | Built-in | Manual |
| **Binary** | Automatic | Manual |
| **Setup** | Simple | More code |

**When to choose Socket.io:**
- Need automatic fallback
- Want event-based API
- Need rooms/namespaces
- Prefer simpler API

**When to choose Native WebSocket:**
- Need minimal overhead
- Maximum performance
- Simple use case
- Full control needed

### Socket.io vs Server-Sent Events (SSE)

| Feature | Socket.io | SSE |
|---------|-----------|-----|
| **Direction** | Bidirectional | Server to Client only |
| **Browser Support** | Excellent | Good (no IE) |
| **Binary** | Yes | No |
| **Reconnection** | Automatic | Automatic |
| **Complexity** | Moderate | Simple |

**When to choose Socket.io:**
- Need bidirectional communication
- Complex real-time features
- Chat, games, collaboration

**When to choose SSE:**
- Only need server → client
- Simple notifications
- Live updates/feeds

### Socket.io vs WebRTC

| Feature | Socket.io | WebRTC |
|---------|-----------|--------|
| **Purpose** | General real-time | Peer-to-peer media |
| **Use Case** | Chat, updates | Video/audio calls |
| **Setup** | Simple | Complex |
| **Latency** | Low | Very low |
| **Server** | Required | Optional (signaling) |

**When to choose Socket.io:**
- Chat applications
- Real-time updates
- Collaborative tools

**When to choose WebRTC:**
- Video/audio calls
- Peer-to-peer file transfer
- Screen sharing

## Installation & Setup

### Installation

```bash
# Server
npm install socket.io

# Client (if using Node.js client)
npm install socket.io-client
```

### Basic Server Setup

```javascript
// server.js
const express = require('express');
const { createServer } = require('http');
const { Server } = require('socket.io');

const app = express();
const httpServer = createServer(app);
const io = new Server(httpServer);

io.on('connection', (socket) => {
  console.log('User connected:', socket.id);
  
  socket.on('disconnect', () => {
    console.log('User disconnected:', socket.id);
  });
});

httpServer.listen(3000, () => {
  console.log('Server running on port 3000');
});
```

### Basic Client Setup

```html
<!-- index.html -->
<!DOCTYPE html>
<html>
<head>
  <title>Socket.io Client</title>
</head>
<body>
  <h1>Socket.io Demo</h1>
  
  <script src="/socket.io/socket.io.js"></script>
  <script>
    const socket = io();
    
    socket.on('connect', () => {
      console.log('Connected:', socket.id);
    });
    
    socket.on('disconnect', () => {
      console.log('Disconnected');
    });
  </script>
</body>
</html>
```

### With CORS

```javascript
const io = new Server(httpServer, {
  cors: {
    origin: 'http://localhost:3001',
    methods: ['GET', 'POST'],
    credentials: true
  }
});
```

### TypeScript Setup

```bash
npm install --save-dev @types/socket.io
```

```typescript
import express from 'express';
import { createServer } from 'http';
import { Server, Socket } from 'socket.io';

const app = express();
const httpServer = createServer(app);
const io = new Server(httpServer);

io.on('connection', (socket: Socket) => {
  console.log('User connected:', socket.id);
});
```

## Basic Concepts

### Connection Lifecycle

```javascript
// Server
io.on('connection', (socket) => {
  console.log('Connected:', socket.id);
  
  // Handle events
  socket.on('message', (data) => {
    console.log('Received:', data);
  });
  
  // Handle disconnection
  socket.on('disconnect', (reason) => {
    console.log('Disconnected:', reason);
  });
});

// Client
const socket = io();

socket.on('connect', () => {
  console.log('Connected');
});

socket.on('disconnect', () => {
  console.log('Disconnected');
});

socket.on('connect_error', (error) => {
  console.error('Connection error:', error);
});
```

### Emitting Events

```javascript
// Server to Client
socket.emit('welcome', { message: 'Welcome!' });

// Client to Server
socket.emit('send-message', { text: 'Hello' });

// With acknowledgement
socket.emit('message', data, (response) => {
  console.log('Server acknowledged:', response);
});
```

### Listening to Events

```javascript
// Server
socket.on('send-message', (data) => {
  console.log('Message:', data);
});

// Client
socket.on('new-message', (data) => {
  console.log('New message:', data);
});

// With acknowledgement
socket.on('message', (data, callback) => {
  console.log('Received:', data);
  callback({ status: 'ok' });
});
```

## Events

### Custom Events

```javascript
// Server
io.on('connection', (socket) => {
  // Listen for custom events
  socket.on('chat-message', (message) => {
    console.log('Chat message:', message);
  });
  
  socket.on('typing', (data) => {
    socket.broadcast.emit('user-typing', data);
  });
  
  socket.on('stop-typing', (data) => {
    socket.broadcast.emit('user-stop-typing', data);
  });
});

// Client
socket.emit('chat-message', {
  text: 'Hello everyone!',
  timestamp: Date.now()
});

socket.emit('typing', { username: 'Alice' });

socket.on('user-typing', (data) => {
  console.log(`${data.username} is typing...`);
});
```

### Built-in Events

```javascript
// Connection events
socket.on('connect', () => {});
socket.on('disconnect', (reason) => {});
socket.on('connect_error', (error) => {});
socket.on('reconnect', (attemptNumber) => {});
socket.on('reconnect_attempt', (attemptNumber) => {});
socket.on('reconnect_error', (error) => {});
socket.on('reconnect_failed', () => {});

// Server-side only
socket.on('disconnecting', (reason) => {
  console.log('About to disconnect:', socket.rooms);
});
```

### Event Acknowledgements

```javascript
// Server
socket.on('save-data', (data, callback) => {
  // Process data
  saveToDatabase(data)
    .then(() => {
      callback({ success: true });
    })
    .catch((error) => {
      callback({ success: false, error: error.message });
    });
});

// Client
socket.emit('save-data', { name: 'Alice' }, (response) => {
  if (response.success) {
    console.log('Data saved successfully');
  } else {
    console.error('Error:', response.error);
  }
});
```

### Volatile Events

```javascript
// Won't be buffered if client is disconnected
socket.volatile.emit('position', { x: 100, y: 200 });

// Useful for real-time data that becomes stale quickly
```

## Rooms & Namespaces

### Rooms

```javascript
// Join room
socket.join('room1');

// Join multiple rooms
socket.join(['room1', 'room2']);

// Leave room
socket.leave('room1');

// Send to room
io.to('room1').emit('message', 'Hello room!');

// Send to multiple rooms
io.to('room1').to('room2').emit('message', 'Hello!');

// Send to room except sender
socket.to('room1').emit('message', 'Hello others!');

// Get rooms
console.log(socket.rooms); // Set { socketId, 'room1', 'room2' }

// Get all sockets in room
const sockets = await io.in('room1').fetchSockets();
console.log(sockets.length);
```

### Namespaces

```javascript
// Server - Create namespaces
const chatNamespace = io.of('/chat');
const adminNamespace = io.of('/admin');

chatNamespace.on('connection', (socket) => {
  console.log('Chat namespace connection');
  
  socket.on('message', (data) => {
    chatNamespace.emit('new-message', data);
  });
});

adminNamespace.on('connection', (socket) => {
  console.log('Admin namespace connection');
});

// Client - Connect to namespace
const chatSocket = io('/chat');
const adminSocket = io('/admin');

chatSocket.emit('message', 'Hello chat!');
```

### Dynamic Namespaces

```javascript
// Create dynamic namespaces
io.of(/^\/dynamic-\w+$/).on('connection', (socket) => {
  const namespace = socket.nsp;
  console.log('Connected to:', namespace.name);
});

// Client connects to dynamic namespace
const socket = io('/dynamic-room1');
```

## Authentication

### Token-Based Authentication

```javascript
// Server
const io = new Server(httpServer, {
  cors: {
    origin: 'http://localhost:3001',
    credentials: true
  }
});

io.use((socket, next) => {
  const token = socket.handshake.auth.token;
  
  if (!token) {
    return next(new Error('Authentication error'));
  }
  
  try {
    const decoded = jwt.verify(token, process.env.JWT_SECRET);
    socket.userId = decoded.userId;
    next();
  } catch (err) {
    next(new Error('Authentication error'));
  }
});

io.on('connection', (socket) => {
  console.log('Authenticated user:', socket.userId);
});

// Client
const socket = io({
  auth: {
    token: 'your-jwt-token'
  }
});
```

### Session-Based Authentication

```javascript
// Server with Express session
const session = require('express-session');
const sharedsession = require('express-socket.io-session');

const sessionMiddleware = session({
  secret: 'your-secret',
  resave: false,
  saveUninitialized: false
});

app.use(sessionMiddleware);

io.use(sharedsession(sessionMiddleware, {
  autoSave: true
}));

io.on('connection', (socket) => {
  const session = socket.handshake.session;
  console.log('User:', session.userId);
});
```

### Custom Authentication

```javascript
io.use(async (socket, next) => {
  const { username, password } = socket.handshake.auth;
  
  try {
    const user = await authenticateUser(username, password);
    socket.user = user;
    next();
  } catch (err) {
    next(new Error('Invalid credentials'));
  }
});
```

## Broadcasting

### Broadcast Patterns

```javascript
// To all clients
io.emit('announcement', 'Server message');

// To all except sender
socket.broadcast.emit('user-joined', { username: 'Alice' });

// To specific room
io.to('room1').emit('message', 'Hello room!');

// To room except sender
socket.to('room1').emit('message', 'Hello others!');

// To multiple rooms
io.to('room1').to('room2').emit('message', 'Hello!');

// To all in namespace
io.of('/chat').emit('announcement', 'Chat announcement');

// Chaining
socket.to('room1').to('room2').emit('message', 'Hello!');
```

### Conditional Broadcasting

```javascript
io.on('connection', (socket) => {
  socket.on('message', async (data) => {
    // Get all sockets in room
    const sockets = await io.in('room1').fetchSockets();
    
    // Broadcast to specific users
    for (const s of sockets) {
      if (s.userId !== socket.userId) {
        s.emit('new-message', data);
      }
    }
  });
});
```

## Best Practices

### 1. Error Handling

```javascript
// Server
io.on('connection', (socket) => {
  socket.on('error', (error) => {
    console.error('Socket error:', error);
  });
  
  socket.on('message', async (data) => {
    try {
      await processMessage(data);
      socket.emit('message-success');
    } catch (error) {
      socket.emit('message-error', { error: error.message });
    }
  });
});

// Client
socket.on('connect_error', (error) => {
  console.error('Connection error:', error);
});

socket.on('message-error', (data) => {
  console.error('Message error:', data.error);
});
```

### 2. Rate Limiting

```javascript
const rateLimit = new Map();

io.on('connection', (socket) => {
  socket.on('message', (data) => {
    const now = Date.now();
    const userLimit = rateLimit.get(socket.id) || { count: 0, resetTime: now + 60000 };
    
    if (now > userLimit.resetTime) {
      userLimit.count = 0;
      userLimit.resetTime = now + 60000;
    }
    
    if (userLimit.count >= 10) {
      socket.emit('rate-limit-exceeded');
      return;
    }
    
    userLimit.count++;
    rateLimit.set(socket.id, userLimit);
    
    // Process message
    io.emit('new-message', data);
  });
});
```

### 3. Validation

```javascript
const { z } = require('zod');

const messageSchema = z.object({
  text: z.string().min(1).max(500),
  timestamp: z.number()
});

io.on('connection', (socket) => {
  socket.on('message', (data) => {
    try {
      const validated = messageSchema.parse(data);
      io.emit('new-message', validated);
    } catch (error) {
      socket.emit('validation-error', { error: error.errors });
    }
  });
});
```

### 4. Connection Management

```javascript
const activeUsers = new Map();

io.on('connection', (socket) => {
  activeUsers.set(socket.id, {
    userId: socket.userId,
    connectedAt: Date.now()
  });
  
  io.emit('user-count', activeUsers.size);
  
  socket.on('disconnect', () => {
    activeUsers.delete(socket.id);
    io.emit('user-count', activeUsers.size);
  });
});
```

### 5. Graceful Shutdown

```javascript
const gracefulShutdown = () => {
  console.log('Shutting down gracefully...');
  
  io.emit('server-shutdown', { message: 'Server is restarting' });
  
  io.close(() => {
    console.log('All connections closed');
    process.exit(0);
  });
  
  setTimeout(() => {
    console.error('Forcing shutdown');
    process.exit(1);
  }, 10000);
};

process.on('SIGTERM', gracefulShutdown);
process.on('SIGINT', gracefulShutdown);
```

## Real-World Examples

### Example 1: Chat Application

```javascript
// server.js
const express = require('express');
const { createServer } = require('http');
const { Server } = require('socket.io');

const app = express();
const httpServer = createServer(app);
const io = new Server(httpServer);

const messages = [];
const users = new Map();

io.on('connection', (socket) => {
  console.log('User connected:', socket.id);
  
  // User joins
  socket.on('join', (username) => {
    users.set(socket.id, username);
    socket.emit('previous-messages', messages);
    io.emit('user-joined', { username, userCount: users.size });
  });
  
  // New message
  socket.on('send-message', (data) => {
    const message = {
      id: Date.now(),
      username: users.get(socket.id),
      text: data.text,
      timestamp: Date.now()
    };
    
    messages.push(message);
    io.emit('new-message', message);
  });
  
  // Typing indicator
  socket.on('typing', () => {
    socket.broadcast.emit('user-typing', {
      username: users.get(socket.id)
    });
  });
  
  socket.on('stop-typing', () => {
    socket.broadcast.emit('user-stop-typing', {
      username: users.get(socket.id)
    });
  });
  
  // Disconnect
  socket.on('disconnect', () => {
    const username = users.get(socket.id);
    users.delete(socket.id);
    io.emit('user-left', { username, userCount: users.size });
  });
});

httpServer.listen(3000);
```

**Client:**
```html
<!DOCTYPE html>
<html>
<head>
  <title>Chat App</title>
  <style>
    #messages { list-style: none; padding: 0; }
    #messages li { padding: 10px; border-bottom: 1px solid #ddd; }
    #typing { color: #888; font-style: italic; }
  </style>
</head>
<body>
  <div id="chat">
    <ul id="messages"></ul>
    <div id="typing"></div>
    <input id="message-input" placeholder="Type a message..." />
    <button id="send-btn">Send</button>
  </div>
  
  <script src="/socket.io/socket.io.js"></script>
  <script>
    const socket = io();
    const username = prompt('Enter your username:');
    
    const messagesList = document.getElementById('messages');
    const messageInput = document.getElementById('message-input');
    const sendBtn = document.getElementById('send-btn');
    const typingDiv = document.getElementById('typing');
    
    let typingTimer;
    
    // Join chat
    socket.emit('join', username);
    
    // Previous messages
    socket.on('previous-messages', (messages) => {
      messages.forEach(addMessage);
    });
    
    // New message
    socket.on('new-message', addMessage);
    
    function addMessage(message) {
      const li = document.createElement('li');
      li.textContent = `${message.username}: ${message.text}`;
      messagesList.appendChild(li);
    }
    
    // Send message
    sendBtn.addEventListener('click', sendMessage);
    messageInput.addEventListener('keypress', (e) => {
      if (e.key === 'Enter') sendMessage();
    });
    
    function sendMessage() {
      const text = messageInput.value.trim();
      if (text) {
        socket.emit('send-message', { text });
        messageInput.value = '';
        socket.emit('stop-typing');
      }
    }
    
    // Typing indicator
    messageInput.addEventListener('input', () => {
      socket.emit('typing');
      clearTimeout(typingTimer);
      typingTimer = setTimeout(() => {
        socket.emit('stop-typing');
      }, 1000);
    });
    
    socket.on('user-typing', (data) => {
      typingDiv.textContent = `${data.username} is typing...`;
    });
    
    socket.on('user-stop-typing', () => {
      typingDiv.textContent = '';
    });
    
    // User events
    socket.on('user-joined', (data) => {
      addMessage({
        username: 'System',
        text: `${data.username} joined (${data.userCount} users online)`
      });
    });
    
    socket.on('user-left', (data) => {
      addMessage({
        username: 'System',
        text: `${data.username} left (${data.userCount} users online)`
      });
    });
  </script>
</body>
</html>
```

### Example 2: Real-Time Notifications

```javascript
// server.js
io.on('connection', (socket) => {
  // User subscribes to notifications
  socket.on('subscribe', (userId) => {
    socket.join(`user:${userId}`);
  });
  
  // Send notification to specific user
  socket.on('send-notification', (data) => {
    io.to(`user:${data.userId}`).emit('notification', {
      title: data.title,
      message: data.message,
      timestamp: Date.now()
    });
  });
});

// Trigger from elsewhere in your app
function sendNotification(userId, notification) {
  io.to(`user:${userId}`).emit('notification', notification);
}
```

### Example 3: Collaborative Editing

```javascript
// server.js
const documents = new Map();

io.on('connection', (socket) => {
  socket.on('join-document', (docId) => {
    socket.join(docId);
    
    // Send current document state
    const doc = documents.get(docId) || { content: '' };
    socket.emit('document-state', doc);
  });
  
  socket.on('edit', (data) => {
    const { docId, content, cursorPosition } = data;
    
    // Update document
    documents.set(docId, { content });
    
    // Broadcast to others in document
    socket.to(docId).emit('remote-edit', {
      content,
      cursorPosition,
      userId: socket.userId
    });
  });
  
  socket.on('cursor-move', (data) => {
    socket.to(data.docId).emit('remote-cursor', {
      userId: socket.userId,
      position: data.position
    });
  });
});
```

## Summary

Socket.io is the standard for real-time communication:

✅ **Real-Time** - Instant bidirectional communication  
✅ **Easy to Use** - Simple, intuitive API  
✅ **Reliable** - Automatic reconnection and fallback  
✅ **Feature-Rich** - Rooms, namespaces, broadcasting  
✅ **Production-Ready** - Battle-tested and scalable  

**Key Takeaways:**
- Use for chat, notifications, live updates
- Implement authentication middleware
- Use rooms for organizing connections
- Validate all incoming data
- Implement rate limiting
- Handle errors gracefully
- Test reconnection scenarios
- Monitor active connections

Socket.io makes building real-time features simple and reliable.
