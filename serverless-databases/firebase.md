# Firebase - Complete Google Backend Platform Guide

## Table of Contents
- [What is Firebase?](#what-is-firebase)
- [Why Use Firebase?](#why-use-firebase)
- [Firebase vs Alternatives](#firebase-vs-alternatives)
- [Installation & Setup](#installation--setup)
- [Firestore Database](#firestore-database)
- [Authentication](#authentication)
- [Cloud Storage](#cloud-storage)
- [Cloud Functions](#cloud-functions)
- [Real-time Database](#real-time-database)
- [Hosting](#hosting)
- [Best Practices](#best-practices)

## What is Firebase?

Firebase is Google's comprehensive app development platform providing backend services including database, authentication, storage, hosting, analytics, and more.

### Key Features
- **Firestore** - NoSQL document database
- **Authentication** - Multi-provider auth system
- **Cloud Storage** - File storage with CDN
- **Cloud Functions** - Serverless functions
- **Hosting** - Fast web hosting
- **Analytics** - User behavior tracking
- **Cloud Messaging** - Push notifications
- **Remote Config** - Dynamic app configuration

## Why Use Firebase?

### 1. Fastest Setup

```javascript
// Traditional backend setup
// - Set up server
// - Configure database
// - Implement auth
// - Set up file storage
// - Deploy infrastructure
// Hours/days of work

// Firebase setup
import { initializeApp } from 'firebase/app';
import { getFirestore } from 'firebase/firestore';
import { getAuth } from 'firebase/auth';

const app = initializeApp(firebaseConfig);
const db = getFirestore(app);
const auth = getAuth(app);
// Ready in minutes!
```

### 2. Real-time by Default

```javascript
import { collection, onSnapshot } from 'firebase/firestore';

// Listen to real-time updates
onSnapshot(collection(db, 'messages'), (snapshot) => {
  snapshot.docChanges().forEach((change) => {
    if (change.type === 'added') {
      console.log('New message:', change.doc.data());
    }
  });
});
```

### 3. Offline Support

```javascript
// Works offline automatically
// Data syncs when connection returns
import { enableIndexedDbPersistence } from 'firebase/firestore';

enableIndexedDbPersistence(db);
```

## Firebase vs Alternatives

### Firebase vs Supabase

| Feature | Firebase | Supabase |
|---------|----------|----------|
| **Database** | NoSQL (Firestore) | PostgreSQL (SQL) |
| **Open Source** | ❌ No | ✅ Yes |
| **Mobile SDKs** | ✅ Excellent | ⚠️ Basic |
| **Offline Support** | ✅ Built-in | ⚠️ Limited |
| **Learning Curve** | Easy | Moderate |
| **Pricing** | Pay-as-you-go | More generous free tier |
| **Vendor Lock-in** | High | Low |

**Choose Firebase if:**
- Building mobile apps
- Need offline-first
- Want fastest setup
- Prefer NoSQL

**Choose Supabase if:**
- Prefer SQL/PostgreSQL
- Want open-source
- Need complex queries
- Want to avoid vendor lock-in

## Installation & Setup

### 1. Create Firebase Project

```bash
# Visit https://console.firebase.google.com
# Click "Add project"
# Follow setup wizard
# Enable services you need
```

### 2. Install Firebase SDK

```bash
# For web
npm install firebase

# Firebase CLI
npm install -g firebase-tools
```

### 3. Initialize Firebase

```bash
firebase login
firebase init
```

### 4. Configuration

**firebase-config.js:**
```javascript
import { initializeApp } from 'firebase/app';
import { getFirestore } from 'firebase/firestore';
import { getAuth } from 'firebase/auth';
import { getStorage } from 'firebase/storage';

const firebaseConfig = {
  apiKey: import.meta.env.VITE_FIREBASE_API_KEY,
  authDomain: import.meta.env.VITE_FIREBASE_AUTH_DOMAIN,
  projectId: import.meta.env.VITE_FIREBASE_PROJECT_ID,
  storageBucket: import.meta.env.VITE_FIREBASE_STORAGE_BUCKET,
  messagingSenderId: import.meta.env.VITE_FIREBASE_MESSAGING_SENDER_ID,
  appId: import.meta.env.VITE_FIREBASE_APP_ID
};

const app = initializeApp(firebaseConfig);
export const db = getFirestore(app);
export const auth = getAuth(app);
export const storage = getStorage(app);
```

**.env:**
```env
VITE_FIREBASE_API_KEY=your-api-key
VITE_FIREBASE_AUTH_DOMAIN=your-app.firebaseapp.com
VITE_FIREBASE_PROJECT_ID=your-project-id
VITE_FIREBASE_STORAGE_BUCKET=your-app.appspot.com
VITE_FIREBASE_MESSAGING_SENDER_ID=123456789
VITE_FIREBASE_APP_ID=1:123456789:web:abcdef
```

## Firestore Database

### Data Structure

```javascript
// Firestore uses collections and documents
// Collection -> Document -> Subcollection -> Document

// Structure example:
users (collection)
  └── userId (document)
      ├── name: "John"
      ├── email: "john@example.com"
      └── posts (subcollection)
          └── postId (document)
              ├── title: "My Post"
              └── content: "..."
```

### Create Documents

```javascript
import { collection, addDoc, setDoc, doc } from 'firebase/firestore';

// Add with auto-generated ID
const docRef = await addDoc(collection(db, 'posts'), {
  title: 'My Post',
  content: 'Post content',
  createdAt: new Date(),
  userId: auth.currentUser.uid
});

console.log('Document ID:', docRef.id);

// Set with custom ID
await setDoc(doc(db, 'posts', 'custom-id'), {
  title: 'My Post',
  content: 'Content'
});

// Merge with existing
await setDoc(doc(db, 'posts', postId), {
  views: 100
}, { merge: true });
```

### Read Documents

```javascript
import { collection, doc, getDoc, getDocs, query, where, orderBy, limit } from 'firebase/firestore';

// Get single document
const docSnap = await getDoc(doc(db, 'posts', postId));
if (docSnap.exists()) {
  console.log('Data:', docSnap.data());
}

// Get all documents
const querySnapshot = await getDocs(collection(db, 'posts'));
querySnapshot.forEach((doc) => {
  console.log(doc.id, doc.data());
});

// Query with filters
const q = query(
  collection(db, 'posts'),
  where('userId', '==', currentUserId),
  where('status', '==', 'published'),
  orderBy('createdAt', 'desc'),
  limit(10)
);

const snapshot = await getDocs(q);
const posts = snapshot.docs.map(doc => ({
  id: doc.id,
  ...doc.data()
}));
```

### Update Documents

```javascript
import { doc, updateDoc, increment, arrayUnion, serverTimestamp } from 'firebase/firestore';

// Update fields
await updateDoc(doc(db, 'posts', postId), {
  title: 'Updated Title',
  updatedAt: serverTimestamp()
});

// Increment counter
await updateDoc(doc(db, 'posts', postId), {
  views: increment(1)
});

// Add to array
await updateDoc(doc(db, 'posts', postId), {
  tags: arrayUnion('javascript', 'firebase')
});

// Remove from array
await updateDoc(doc(db, 'posts', postId), {
  tags: arrayRemove('old-tag')
});
```

### Delete Documents

```javascript
import { doc, deleteDoc, deleteField } from 'firebase/firestore';

// Delete document
await deleteDoc(doc(db, 'posts', postId));

// Delete field
await updateDoc(doc(db, 'posts', postId), {
  obsoleteField: deleteField()
});
```

### Real-time Listeners

```javascript
import { collection, doc, onSnapshot } from 'firebase/firestore';

// Listen to document
const unsubscribe = onSnapshot(doc(db, 'posts', postId), (doc) => {
  console.log('Current data:', doc.data());
});

// Listen to collection
const unsubscribe = onSnapshot(
  collection(db, 'posts'),
  (snapshot) => {
    snapshot.docChanges().forEach((change) => {
      if (change.type === 'added') {
        console.log('New:', change.doc.data());
      }
      if (change.type === 'modified') {
        console.log('Modified:', change.doc.data());
      }
      if (change.type === 'removed') {
        console.log('Removed:', change.doc.data());
      }
    });
  }
);

// Stop listening
unsubscribe();
```

### React Hook Example

```javascript
import { useState, useEffect } from 'react';
import { collection, query, where, onSnapshot } from 'firebase/firestore';

function usePosts(userId) {
  const [posts, setPosts] = useState([]);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    const q = query(
      collection(db, 'posts'),
      where('userId', '==', userId)
    );

    const unsubscribe = onSnapshot(q, (snapshot) => {
      const postsData = snapshot.docs.map(doc => ({
        id: doc.id,
        ...doc.data()
      }));
      setPosts(postsData);
      setLoading(false);
    });

    return () => unsubscribe();
  }, [userId]);

  return { posts, loading };
}
```

## Authentication

### Email/Password

```javascript
import { 
  createUserWithEmailAndPassword,
  signInWithEmailAndPassword,
  signOut,
  sendEmailVerification,
  sendPasswordResetEmail
} from 'firebase/auth';

// Sign up
const userCredential = await createUserWithEmailAndPassword(
  auth,
  email,
  password
);
const user = userCredential.user;

// Send verification email
await sendEmailVerification(user);

// Sign in
const userCredential = await signInWithEmailAndPassword(
  auth,
  email,
  password
);

// Sign out
await signOut(auth);

// Password reset
await sendPasswordResetEmail(auth, email);
```

### OAuth Providers

```javascript
import { 
  signInWithPopup,
  signInWithRedirect,
  GoogleAuthProvider,
  GithubAuthProvider,
  FacebookAuthProvider
} from 'firebase/auth';

// Google Sign-In
const provider = new GoogleAuthProvider();
const result = await signInWithPopup(auth, provider);
const user = result.user;

// GitHub Sign-In
const githubProvider = new GithubAuthProvider();
const result = await signInWithPopup(auth, githubProvider);

// Facebook Sign-In
const facebookProvider = new FacebookAuthProvider();
const result = await signInWithPopup(auth, facebookProvider);

// Redirect method (better for mobile)
await signInWithRedirect(auth, provider);
```

### Phone Authentication

```javascript
import { 
  RecaptchaVerifier,
  signInWithPhoneNumber
} from 'firebase/auth';

// Set up reCAPTCHA
window.recaptchaVerifier = new RecaptchaVerifier(
  'recaptcha-container',
  { size: 'invisible' },
  auth
);

// Send code
const confirmationResult = await signInWithPhoneNumber(
  auth,
  phoneNumber,
  window.recaptchaVerifier
);

// Verify code
const result = await confirmationResult.confirm(code);
const user = result.user;
```

### Auth State Observer

```javascript
import { onAuthStateChanged } from 'firebase/auth';

onAuthStateChanged(auth, (user) => {
  if (user) {
    console.log('User signed in:', user.uid);
  } else {
    console.log('User signed out');
  }
});
```

### React Auth Hook

```javascript
import { useState, useEffect } from 'react';
import { onAuthStateChanged } from 'firebase/auth';

function useAuth() {
  const [user, setUser] = useState(null);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    const unsubscribe = onAuthStateChanged(auth, (user) => {
      setUser(user);
      setLoading(false);
    });

    return unsubscribe;
  }, []);

  return { user, loading };
}
```

## Cloud Storage

### Upload Files

```javascript
import { ref, uploadBytes, uploadBytesResumable, getDownloadURL } from 'firebase/storage';

// Simple upload
const file = event.target.files[0];
const storageRef = ref(storage, `images/${file.name}`);
await uploadBytes(storageRef, file);

// Get download URL
const url = await getDownloadURL(storageRef);

// Upload with progress
const uploadTask = uploadBytesResumable(storageRef, file);

uploadTask.on('state_changed',
  (snapshot) => {
    const progress = (snapshot.bytesTransferred / snapshot.totalBytes) * 100;
    console.log('Upload is ' + progress + '% done');
  },
  (error) => {
    console.error('Upload error:', error);
  },
  async () => {
    const url = await getDownloadURL(uploadTask.snapshot.ref);
    console.log('File available at', url);
  }
);
```

### Download Files

```javascript
import { ref, getDownloadURL, getBlob } from 'firebase/storage';

// Get download URL
const url = await getDownloadURL(ref(storage, 'images/photo.jpg'));

// Download as blob
const blob = await getBlob(ref(storage, 'images/photo.jpg'));
```

### Delete Files

```javascript
import { ref, deleteObject } from 'firebase/storage';

await deleteObject(ref(storage, 'images/photo.jpg'));
```

### List Files

```javascript
import { ref, listAll } from 'firebase/storage';

const listRef = ref(storage, 'images');
const result = await listAll(listRef);

result.items.forEach((itemRef) => {
  console.log('File:', itemRef.name);
});
```

## Cloud Functions

### HTTP Functions

```javascript
// functions/index.js
const functions = require('firebase-functions');
const admin = require('firebase-admin');

admin.initializeApp();

// HTTP endpoint
exports.helloWorld = functions.https.onRequest((req, res) => {
  res.json({ message: 'Hello from Firebase!' });
});

// Callable function
exports.addMessage = functions.https.onCall(async (data, context) => {
  // Check authentication
  if (!context.auth) {
    throw new functions.https.HttpsError(
      'unauthenticated',
      'Must be logged in'
    );
  }

  const message = data.text;
  await admin.firestore().collection('messages').add({
    text: message,
    userId: context.auth.uid,
    createdAt: admin.firestore.FieldValue.serverTimestamp()
  });

  return { success: true };
});
```

### Firestore Triggers

```javascript
// Trigger on document create
exports.onPostCreate = functions.firestore
  .document('posts/{postId}')
  .onCreate(async (snap, context) => {
    const post = snap.data();
    const postId = context.params.postId;

    // Send notification
    console.log('New post created:', postId);
  });

// Trigger on document update
exports.onPostUpdate = functions.firestore
  .document('posts/{postId}')
  .onUpdate(async (change, context) => {
    const before = change.before.data();
    const after = change.after.data();

    if (before.status !== after.status) {
      console.log('Status changed');
    }
  });

// Trigger on document delete
exports.onPostDelete = functions.firestore
  .document('posts/{postId}')
  .onDelete(async (snap, context) => {
    // Clean up related data
    console.log('Post deleted:', context.params.postId);
  });
```

### Auth Triggers

```javascript
// Trigger on user create
exports.onUserCreate = functions.auth.user().onCreate(async (user) => {
  // Create user profile
  await admin.firestore().collection('users').doc(user.uid).set({
    email: user.email,
    createdAt: admin.firestore.FieldValue.serverTimestamp()
  });
});

// Trigger on user delete
exports.onUserDelete = functions.auth.user().onDelete(async (user) => {
  // Clean up user data
  await admin.firestore().collection('users').doc(user.uid).delete();
});
```

### Call from Client

```javascript
import { getFunctions, httpsCallable } from 'firebase/functions';

const functions = getFunctions();
const addMessage = httpsCallable(functions, 'addMessage');

const result = await addMessage({ text: 'Hello!' });
console.log(result.data);
```

## Security Rules

### Firestore Rules

```javascript
// firestore.rules
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    // Users can read/write their own data
    match /users/{userId} {
      allow read, write: if request.auth != null && request.auth.uid == userId;
    }

    // Posts
    match /posts/{postId} {
      // Anyone can read published posts
      allow read: if resource.data.status == 'published';
      
      // Only owner can write
      allow create: if request.auth != null && 
                      request.resource.data.userId == request.auth.uid;
      allow update, delete: if request.auth != null && 
                              resource.data.userId == request.auth.uid;
    }

    // Comments
    match /posts/{postId}/comments/{commentId} {
      allow read: if true;
      allow create: if request.auth != null;
      allow update, delete: if request.auth != null && 
                              resource.data.userId == request.auth.uid;
    }
  }
}
```

### Storage Rules

```javascript
// storage.rules
rules_version = '2';
service firebase.storage {
  match /b/{bucket}/o {
    // User avatars
    match /avatars/{userId}/{fileName} {
      allow read: if true;
      allow write: if request.auth != null && request.auth.uid == userId;
    }

    // Public images
    match /public/{allPaths=**} {
      allow read: if true;
      allow write: if request.auth != null;
    }

    // Private files
    match /private/{userId}/{allPaths=**} {
      allow read, write: if request.auth != null && request.auth.uid == userId;
    }
  }
}
```

## Best Practices

### 1. Use Batched Writes

```javascript
import { writeBatch, doc } from 'firebase/firestore';

const batch = writeBatch(db);

batch.set(doc(db, 'posts', 'post1'), { title: 'Post 1' });
batch.update(doc(db, 'posts', 'post2'), { views: 100 });
batch.delete(doc(db, 'posts', 'post3'));

await batch.commit();
```

### 2. Use Transactions

```javascript
import { runTransaction, doc } from 'firebase/firestore';

await runTransaction(db, async (transaction) => {
  const postDoc = await transaction.get(doc(db, 'posts', postId));
  
  if (!postDoc.exists()) {
    throw new Error('Post does not exist!');
  }

  const newViews = postDoc.data().views + 1;
  transaction.update(doc(db, 'posts', postId), { views: newViews });
});
```

### 3. Optimize Queries

```javascript
// Bad - No index
const q = query(
  collection(db, 'posts'),
  where('status', '==', 'published'),
  orderBy('createdAt', 'desc')
);

// Good - Create composite index in Firebase Console
// Or use firebase.indexes.json
```

### 4. Denormalize Data

```javascript
// Instead of joining, duplicate data
await setDoc(doc(db, 'posts', postId), {
  title: 'My Post',
  author: {
    id: userId,
    name: userName,
    avatar: userAvatar
  }
});
```

### 5. Use Security Rules

```javascript
// Never trust client
// Always validate in security rules
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /posts/{postId} {
      allow create: if request.auth != null &&
                      request.resource.data.userId == request.auth.uid &&
                      request.resource.data.title.size() > 0 &&
                      request.resource.data.title.size() < 100;
    }
  }
}
```

### 6. Handle Offline

```javascript
import { enableIndexedDbPersistence } from 'firebase/firestore';

try {
  await enableIndexedDbPersistence(db);
} catch (err) {
  if (err.code == 'failed-precondition') {
    console.log('Multiple tabs open');
  } else if (err.code == 'unimplemented') {
    console.log('Browser not supported');
  }
}
```

## Resources

- [Firebase Documentation](https://firebase.google.com/docs)
- [Firebase Console](https://console.firebase.google.com)
- [Firebase YouTube](https://www.youtube.com/firebase)
- [Firebase Blog](https://firebase.blog)
- [Firestore Data Modeling](https://firebase.google.com/docs/firestore/manage-data/structure-data)
