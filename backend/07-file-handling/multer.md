# Multer - File Upload Middleware

## Table of Contents
- [What is Multer?](#what-is-multer)
- [Why Use Multer?](#why-use-multer)
- [Installation & Setup](#installation--setup)
- [Storage Options](#storage-options)
- [File Filtering](#file-filtering)
- [File Size Limits](#file-size-limits)
- [Multiple Files](#multiple-files)
- [Error Handling](#error-handling)
- [Best Practices](#best-practices)
- [Real-World Examples](#real-world-examples)

## What is Multer?

Multer is a Node.js middleware for handling `multipart/form-data`, primarily used for uploading files. It's built on top of busboy for maximum efficiency and provides a simple API for file uploads in Express applications.

### Key Features
- **Easy File Uploads**: Simple API for handling files
- **Multiple Storage Options**: Disk, memory, or custom
- **File Filtering**: Accept/reject files based on criteria
- **Size Limits**: Control maximum file sizes
- **Multiple Files**: Handle single or multiple file uploads
- **Field Validation**: Validate form fields
- **TypeScript Support**: Full type definitions

## Why Use Multer?

### 1. **Simplified File Handling**

**Without Multer:**
```javascript
// Complex manual handling
app.post('/upload', (req, res) => {
  let data = '';
  let boundary = req.headers['content-type'].split('boundary=')[1];
  
  req.on('data', chunk => {
    data += chunk;
  });
  
  req.on('end', () => {
    // Parse multipart data manually
    // Extract file data
    // Save to disk
    // Very complex!
  });
});
```

**With Multer:**
```javascript
const upload = multer({ dest: 'uploads/' });

app.post('/upload', upload.single('file'), (req, res) => {
  res.json({ file: req.file });
  // That's it!
});
```

### 2. **Automatic File Processing**

```javascript
// Multer handles:
// - Parsing multipart/form-data
// - Extracting file data
// - Saving to storage
// - Generating unique filenames
// - Providing file metadata
```

### 3. **Built-in Validation**

```javascript
const upload = multer({
  limits: { fileSize: 5 * 1024 * 1024 }, // 5MB
  fileFilter: (req, file, cb) => {
    if (file.mimetype.startsWith('image/')) {
      cb(null, true);
    } else {
      cb(new Error('Only images allowed'));
    }
  }
});
```

## Installation & Setup

### Installation

```bash
npm install multer
```

### Basic Setup

```javascript
const express = require('express');
const multer = require('multer');

const app = express();

// Basic upload configuration
const upload = multer({ dest: 'uploads/' });

// Single file upload
app.post('/upload', upload.single('file'), (req, res) => {
  res.json({
    message: 'File uploaded successfully',
    file: req.file
  });
});

app.listen(3000);
```

### TypeScript Setup

```bash
npm install --save-dev @types/multer
```

```typescript
import express from 'express';
import multer from 'multer';

const app = express();
const upload = multer({ dest: 'uploads/' });

app.post('/upload', upload.single('file'), (req, res) => {
  res.json({ file: req.file });
});
```

## Storage Options

### Disk Storage

```javascript
const storage = multer.diskStorage({
  destination: (req, file, cb) => {
    cb(null, 'uploads/');
  },
  filename: (req, file, cb) => {
    const uniqueSuffix = Date.now() + '-' + Math.round(Math.random() * 1E9);
    cb(null, file.fieldname + '-' + uniqueSuffix + path.extname(file.originalname));
  }
});

const upload = multer({ storage });
```

### Memory Storage

```javascript
const storage = multer.memoryStorage();
const upload = multer({ storage });

app.post('/upload', upload.single('file'), (req, res) => {
  // File is in req.file.buffer
  console.log(req.file.buffer);
  
  // Process in memory or upload to cloud storage
  res.json({ message: 'File received' });
});
```

### Custom Storage

```javascript
const customStorage = {
  _handleFile: (req, file, cb) => {
    // Custom file handling logic
    // Upload to S3, process, etc.
    cb(null, { /* file info */ });
  },
  _removeFile: (req, file, cb) => {
    // Custom file removal logic
    cb(null);
  }
};

const upload = multer({ storage: customStorage });
```

## File Filtering

### By MIME Type

```javascript
const fileFilter = (req, file, cb) => {
  // Accept images only
  if (file.mimetype.startsWith('image/')) {
    cb(null, true);
  } else {
    cb(new Error('Only images are allowed'), false);
  }
};

const upload = multer({
  dest: 'uploads/',
  fileFilter
});
```

### By Extension

```javascript
const path = require('path');

const fileFilter = (req, file, cb) => {
  const allowedExtensions = ['.jpg', '.jpeg', '.png', '.gif'];
  const ext = path.extname(file.originalname).toLowerCase();
  
  if (allowedExtensions.includes(ext)) {
    cb(null, true);
  } else {
    cb(new Error('Invalid file type'), false);
  }
};

const upload = multer({
  dest: 'uploads/',
  fileFilter
});
```

### Multiple Criteria

```javascript
const fileFilter = (req, file, cb) => {
  const allowedMimes = ['image/jpeg', 'image/png', 'image/gif'];
  const maxSize = 5 * 1024 * 1024; // 5MB
  
  if (!allowedMimes.includes(file.mimetype)) {
    return cb(new Error('Invalid file type'));
  }
  
  cb(null, true);
};

const upload = multer({
  dest: 'uploads/',
  limits: { fileSize: 5 * 1024 * 1024 },
  fileFilter
});
```

## File Size Limits

### Basic Limits

```javascript
const upload = multer({
  dest: 'uploads/',
  limits: {
    fileSize: 5 * 1024 * 1024, // 5MB
    files: 5, // Maximum 5 files
    fields: 10, // Maximum 10 non-file fields
    fieldSize: 1024 * 1024 // 1MB per field
  }
});
```

### Custom Error Messages

```javascript
app.post('/upload', (req, res) => {
  upload.single('file')(req, res, (err) => {
    if (err instanceof multer.MulterError) {
      if (err.code === 'LIMIT_FILE_SIZE') {
        return res.status(400).json({ error: 'File too large. Maximum size is 5MB' });
      }
      if (err.code === 'LIMIT_FILE_COUNT') {
        return res.status(400).json({ error: 'Too many files' });
      }
      return res.status(400).json({ error: err.message });
    } else if (err) {
      return res.status(400).json({ error: err.message });
    }
    
    res.json({ file: req.file });
  });
});
```

## Multiple Files

### Single File

```javascript
app.post('/upload', upload.single('file'), (req, res) => {
  // req.file contains the uploaded file
  res.json({ file: req.file });
});
```

### Multiple Files (Same Field)

```javascript
app.post('/upload', upload.array('files', 5), (req, res) => {
  // req.files contains array of uploaded files (max 5)
  res.json({ files: req.files });
});
```

### Multiple Files (Different Fields)

```javascript
app.post('/upload', upload.fields([
  { name: 'avatar', maxCount: 1 },
  { name: 'gallery', maxCount: 8 }
]), (req, res) => {
  // req.files is an object with field names as keys
  res.json({
    avatar: req.files['avatar'],
    gallery: req.files['gallery']
  });
});
```

### Any Files

```javascript
app.post('/upload', upload.any(), (req, res) => {
  // req.files contains all uploaded files
  res.json({ files: req.files });
});
```

### No Files (Text Fields Only)

```javascript
app.post('/submit', upload.none(), (req, res) => {
  // req.body contains text fields
  res.json({ data: req.body });
});
```

## Error Handling

### Multer Errors

```javascript
app.post('/upload', (req, res) => {
  upload.single('file')(req, res, (err) => {
    if (err instanceof multer.MulterError) {
      // Multer error
      switch (err.code) {
        case 'LIMIT_FILE_SIZE':
          return res.status(400).json({ error: 'File too large' });
        case 'LIMIT_FILE_COUNT':
          return res.status(400).json({ error: 'Too many files' });
        case 'LIMIT_UNEXPECTED_FILE':
          return res.status(400).json({ error: 'Unexpected field' });
        default:
          return res.status(400).json({ error: err.message });
      }
    } else if (err) {
      // Custom error (from fileFilter)
      return res.status(400).json({ error: err.message });
    }
    
    if (!req.file) {
      return res.status(400).json({ error: 'No file uploaded' });
    }
    
    res.json({ file: req.file });
  });
});
```

### Global Error Handler

```javascript
app.use((err, req, res, next) => {
  if (err instanceof multer.MulterError) {
    return res.status(400).json({
      error: 'File upload error',
      details: err.message
    });
  }
  next(err);
});
```

## Best Practices

### 1. Validate File Types

```javascript
const fileFilter = (req, file, cb) => {
  const allowedTypes = ['image/jpeg', 'image/png', 'image/gif'];
  
  if (allowedTypes.includes(file.mimetype)) {
    cb(null, true);
  } else {
    cb(new Error('Invalid file type. Only JPEG, PNG and GIF allowed'), false);
  }
};

const upload = multer({
  storage: multer.diskStorage({
    destination: 'uploads/',
    filename: (req, file, cb) => {
      const uniqueSuffix = Date.now() + '-' + Math.round(Math.random() * 1E9);
      cb(null, file.fieldname + '-' + uniqueSuffix + path.extname(file.originalname));
    }
  }),
  fileFilter,
  limits: { fileSize: 5 * 1024 * 1024 }
});
```

### 2. Organize Uploads by Type

```javascript
const storage = multer.diskStorage({
  destination: (req, file, cb) => {
    let folder = 'uploads/';
    
    if (file.mimetype.startsWith('image/')) {
      folder += 'images/';
    } else if (file.mimetype.startsWith('video/')) {
      folder += 'videos/';
    } else {
      folder += 'documents/';
    }
    
    // Ensure folder exists
    fs.mkdirSync(folder, { recursive: true });
    
    cb(null, folder);
  },
  filename: (req, file, cb) => {
    const uniqueSuffix = Date.now() + '-' + Math.round(Math.random() * 1E9);
    cb(null, file.fieldname + '-' + uniqueSuffix + path.extname(file.originalname));
  }
});
```

### 3. Generate Unique Filenames

```javascript
const crypto = require('crypto');

const storage = multer.diskStorage({
  destination: 'uploads/',
  filename: (req, file, cb) => {
    const hash = crypto.randomBytes(16).toString('hex');
    const ext = path.extname(file.originalname);
    cb(null, `${hash}${ext}`);
  }
});
```

### 4. Clean Up on Error

```javascript
app.post('/upload', upload.single('file'), async (req, res) => {
  try {
    // Process file
    await processFile(req.file);
    res.json({ success: true });
  } catch (error) {
    // Delete uploaded file on error
    if (req.file) {
      fs.unlinkSync(req.file.path);
    }
    res.status(500).json({ error: 'Processing failed' });
  }
});
```

### 5. Secure File Access

```javascript
// Don't serve uploads directory directly
// app.use('/uploads', express.static('uploads')); // DON'T DO THIS

// Instead, serve files through a route with authentication
app.get('/files/:filename', authenticate, (req, res) => {
  const filename = req.params.filename;
  const filepath = path.join(__dirname, 'uploads', filename);
  
  // Validate filename to prevent directory traversal
  if (filename.includes('..')) {
    return res.status(400).json({ error: 'Invalid filename' });
  }
  
  // Check if file exists
  if (!fs.existsSync(filepath)) {
    return res.status(404).json({ error: 'File not found' });
  }
  
  // Check permissions
  // ...
  
  res.sendFile(filepath);
});
```

## Real-World Examples

### Example 1: Image Upload with Validation

```javascript
const express = require('express');
const multer = require('multer');
const path = require('path');
const fs = require('fs');

const app = express();

// Ensure upload directory exists
const uploadDir = 'uploads/images';
fs.mkdirSync(uploadDir, { recursive: true });

// Configure storage
const storage = multer.diskStorage({
  destination: uploadDir,
  filename: (req, file, cb) => {
    const uniqueSuffix = Date.now() + '-' + Math.round(Math.random() * 1E9);
    cb(null, 'image-' + uniqueSuffix + path.extname(file.originalname));
  }
});

// File filter
const fileFilter = (req, file, cb) => {
  const allowedTypes = ['image/jpeg', 'image/png', 'image/gif'];
  
  if (allowedTypes.includes(file.mimetype)) {
    cb(null, true);
  } else {
    cb(new Error('Invalid file type. Only JPEG, PNG and GIF allowed'), false);
  }
};

// Configure upload
const upload = multer({
  storage,
  fileFilter,
  limits: {
    fileSize: 5 * 1024 * 1024 // 5MB
  }
});

// Upload endpoint
app.post('/upload/image', (req, res) => {
  upload.single('image')(req, res, (err) => {
    if (err instanceof multer.MulterError) {
      return res.status(400).json({ error: err.message });
    } else if (err) {
      return res.status(400).json({ error: err.message });
    }
    
    if (!req.file) {
      return res.status(400).json({ error: 'No file uploaded' });
    }
    
    res.json({
      message: 'Image uploaded successfully',
      file: {
        filename: req.file.filename,
        size: req.file.size,
        mimetype: req.file.mimetype
      }
    });
  });
});

app.listen(3000);
```

### Example 2: Multiple File Upload

```javascript
app.post('/upload/gallery', (req, res) => {
  upload.array('images', 10)(req, res, (err) => {
    if (err) {
      return res.status(400).json({ error: err.message });
    }
    
    if (!req.files || req.files.length === 0) {
      return res.status(400).json({ error: 'No files uploaded' });
    }
    
    const files = req.files.map(file => ({
      filename: file.filename,
      size: file.size,
      mimetype: file.mimetype
    }));
    
    res.json({
      message: `${files.length} images uploaded successfully`,
      files
    });
  });
});
```

### Example 3: Profile with Avatar

```javascript
app.post('/profile', upload.fields([
  { name: 'avatar', maxCount: 1 },
  { name: 'cover', maxCount: 1 }
]), async (req, res) => {
  try {
    const { name, bio } = req.body;
    const avatar = req.files['avatar']?.[0];
    const cover = req.files['cover']?.[0];
    
    // Save to database
    const profile = await Profile.create({
      name,
      bio,
      avatar: avatar?.filename,
      cover: cover?.filename
    });
    
    res.json({ profile });
  } catch (error) {
    // Clean up uploaded files on error
    if (req.files) {
      Object.values(req.files).flat().forEach(file => {
        fs.unlinkSync(file.path);
      });
    }
    res.status(500).json({ error: 'Profile creation failed' });
  }
});
```

### Example 4: Cloud Storage (S3)

```bash
npm install aws-sdk multer-s3
```

```javascript
const AWS = require('aws-sdk');
const multer = require('multer');
const multerS3 = require('multer-s3');

const s3 = new AWS.S3({
  accessKeyId: process.env.AWS_ACCESS_KEY_ID,
  secretAccessKey: process.env.AWS_SECRET_ACCESS_KEY,
  region: process.env.AWS_REGION
});

const upload = multer({
  storage: multerS3({
    s3: s3,
    bucket: process.env.S3_BUCKET,
    acl: 'public-read',
    metadata: (req, file, cb) => {
      cb(null, { fieldName: file.fieldname });
    },
    key: (req, file, cb) => {
      const uniqueSuffix = Date.now() + '-' + Math.round(Math.random() * 1E9);
      cb(null, 'uploads/' + uniqueSuffix + path.extname(file.originalname));
    }
  }),
  limits: { fileSize: 5 * 1024 * 1024 },
  fileFilter: (req, file, cb) => {
    if (file.mimetype.startsWith('image/')) {
      cb(null, true);
    } else {
      cb(new Error('Only images allowed'), false);
    }
  }
});

app.post('/upload', upload.single('file'), (req, res) => {
  res.json({
    message: 'File uploaded to S3',
    url: req.file.location,
    key: req.file.key
  });
});
```

### Example 5: Complete Upload System

```javascript
const express = require('express');
const multer = require('multer');
const path = require('path');
const fs = require('fs');
const crypto = require('crypto');

const app = express();

// Create upload directories
const uploadDirs = {
  images: 'uploads/images',
  documents: 'uploads/documents',
  videos: 'uploads/videos'
};

Object.values(uploadDirs).forEach(dir => {
  fs.mkdirSync(dir, { recursive: true });
});

// Storage configuration
const storage = multer.diskStorage({
  destination: (req, file, cb) => {
    let folder = uploadDirs.documents;
    
    if (file.mimetype.startsWith('image/')) {
      folder = uploadDirs.images;
    } else if (file.mimetype.startsWith('video/')) {
      folder = uploadDirs.videos;
    }
    
    cb(null, folder);
  },
  filename: (req, file, cb) => {
    const hash = crypto.randomBytes(16).toString('hex');
    const ext = path.extname(file.originalname);
    cb(null, `${hash}${ext}`);
  }
});

// File filter
const fileFilter = (req, file, cb) => {
  const allowedMimes = [
    'image/jpeg',
    'image/png',
    'image/gif',
    'application/pdf',
    'application/msword',
    'application/vnd.openxmlformats-officedocument.wordprocessingml.document'
  ];
  
  if (allowedMimes.includes(file.mimetype)) {
    cb(null, true);
  } else {
    cb(new Error('File type not allowed'), false);
  }
};

// Upload configuration
const upload = multer({
  storage,
  fileFilter,
  limits: {
    fileSize: 10 * 1024 * 1024, // 10MB
    files: 5
  }
});

// Single file upload
app.post('/upload/single', (req, res) => {
  upload.single('file')(req, res, (err) => {
    if (err) {
      return res.status(400).json({ error: err.message });
    }
    
    if (!req.file) {
      return res.status(400).json({ error: 'No file uploaded' });
    }
    
    res.json({
      message: 'File uploaded successfully',
      file: {
        id: req.file.filename,
        originalName: req.file.originalname,
        size: req.file.size,
        mimetype: req.file.mimetype,
        path: req.file.path
      }
    });
  });
});

// Multiple files upload
app.post('/upload/multiple', (req, res) => {
  upload.array('files', 5)(req, res, (err) => {
    if (err) {
      return res.status(400).json({ error: err.message });
    }
    
    if (!req.files || req.files.length === 0) {
      return res.status(400).json({ error: 'No files uploaded' });
    }
    
    const files = req.files.map(file => ({
      id: file.filename,
      originalName: file.originalname,
      size: file.size,
      mimetype: file.mimetype
    }));
    
    res.json({
      message: `${files.length} files uploaded successfully`,
      files
    });
  });
});

// Serve uploaded files (with authentication)
app.get('/files/:id', (req, res) => {
  const fileId = req.params.id;
  
  // Prevent directory traversal
  if (fileId.includes('..') || fileId.includes('/')) {
    return res.status(400).json({ error: 'Invalid file ID' });
  }
  
  // Search in all upload directories
  for (const dir of Object.values(uploadDirs)) {
    const filepath = path.join(dir, fileId);
    if (fs.existsSync(filepath)) {
      return res.sendFile(path.resolve(filepath));
    }
  }
  
  res.status(404).json({ error: 'File not found' });
});

// Delete file
app.delete('/files/:id', (req, res) => {
  const fileId = req.params.id;
  
  if (fileId.includes('..') || fileId.includes('/')) {
    return res.status(400).json({ error: 'Invalid file ID' });
  }
  
  for (const dir of Object.values(uploadDirs)) {
    const filepath = path.join(dir, fileId);
    if (fs.existsSync(filepath)) {
      fs.unlinkSync(filepath);
      return res.json({ message: 'File deleted successfully' });
    }
  }
  
  res.status(404).json({ error: 'File not found' });
});

app.listen(3000);
```

## Summary

Multer is the standard file upload middleware for Express:

✅ **Simple** - Easy file upload handling  
✅ **Flexible** - Multiple storage options  
✅ **Secure** - Built-in validation and limits  
✅ **Efficient** - Handles large files well  
✅ **Standard** - Industry-standard solution  

**Key Takeaways:**
- Always validate file types and sizes
- Use unique filenames to prevent conflicts
- Organize uploads by type
- Clean up files on errors
- Secure file access with authentication
- Use memory storage for cloud uploads
- Handle errors appropriately
- Never trust user input

Multer simplifies file uploads in Express applications.
