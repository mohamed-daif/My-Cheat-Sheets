# Node.js & Express.js Complete Cheat Sheet

## Table of Contents
- [Node.js Fundamentals](#nodejs-fundamentals)
- [Express.js Basics](#expressjs-basics)
- [Routing & HTTP Methods](#routing--http-methods)
- [Middleware](#middleware)
- [Template Engines](#template-engines)
- [Database Integration](#database-integration)
- [Authentication & Security](#authentication--security)
- [Error Handling](#error-handling)
- [File Handling](#file-handling)
- [API Development](#api-development)
- [Testing](#testing)
- [Performance & Optimization](#performance--optimization)
- [Deployment](#deployment)
- [Best Practices](#best-practices)

## Node.js Fundamentals

### Core Modules
```javascript
// Importing core modules
const http = require('http');
const fs = require('fs');
const path = require('path');
const os = require('os');
const events = require('events');
const util = require('util');

// ES6 imports (package.json type: "module")
import http from 'http';
import fs from 'fs/promises';
```

### File System Operations
```javascript
const fs = require('fs');
const fsPromises = require('fs').promises;

// Synchronous file operations
const data = fs.readFileSync('file.txt', 'utf8');
fs.writeFileSync('output.txt', 'Hello World');

// Asynchronous callback style
fs.readFile('file.txt', 'utf8', (err, data) => {
    if (err) throw err;
    console.log(data);
});

// Asynchronous with promises (modern approach)
async function readFiles() {
    try {
        const data = await fsPromises.readFile('file.txt', 'utf8');
        await fsPromises.writeFile('output.txt', data);
        console.log('File processed successfully');
    } catch (error) {
        console.error('Error:', error);
    }
}

// File operations examples
fs.appendFile('log.txt', 'New log entry\n'); // Append to file
fs.unlink('file.txt'); // Delete file
fs.mkdir('new-folder'); // Create directory
fs.readdir('./', (err, files) => { // Read directory
    if (err) throw err;
    console.log(files);
});
```

### HTTP Server (Without Express)
```javascript
const http = require('http');
const url = require('url');

// Basic HTTP server
const server = http.createServer((req, res) => {
    const { method, url: reqUrl } = req;
    const parsedUrl = url.parse(reqUrl, true);
    
    // Set response headers
    res.setHeader('Content-Type', 'application/json');
    
    // Route handling
    if (method === 'GET' && parsedUrl.pathname === '/api/users') {
        res.statusCode = 200;
        res.end(JSON.stringify({ users: [] }));
    } 
    else if (method === 'POST' && parsedUrl.pathname === '/api/users') {
        let body = '';
        req.on('data', chunk => body += chunk);
        req.on('end', () => {
            res.statusCode = 201;
            res.end(JSON.stringify({ message: 'User created' }));
        });
    }
    else {
        res.statusCode = 404;
        res.end(JSON.stringify({ error: 'Not found' }));
    }
});

server.listen(3000, () => {
    console.log('Server running on port 3000');
});
```

### Events & EventEmitter
```javascript
const EventEmitter = require('events');

// Create custom event emitter
class MyEmitter extends EventEmitter {}

const myEmitter = new MyEmitter();

// Register event listeners
myEmitter.on('event', (data) => {
    console.log('Event occurred:', data);
});

myEmitter.once('first-time', () => {
    console.log('This will run only once');
});

// Emit events
myEmitter.emit('event', { message: 'Hello' });
myEmitter.emit('first-time');
myEmitter.emit('first-time'); // Won't trigger

// Error events
myEmitter.on('error', (err) => {
    console.error('Error:', err);
});
```

### Streams
```javascript
const fs = require('fs');
const { Transform } = require('stream');

// Readable stream
const readable = fs.createReadStream('input.txt', 'utf8');

// Writable stream  
const writable = fs.createWriteStream('output.txt');

// Transform stream
const upperCaseTransform = new Transform({
    transform(chunk, encoding, callback) {
        this.push(chunk.toString().toUpperCase());
        callback();
    }
});

// Pipe streams
readable.pipe(upperCaseTransform).pipe(writable);

// Handle stream events
readable.on('data', (chunk) => {
    console.log('Received chunk:', chunk.length);
});

readable.on('end', () => {
    console.log('No more data');
});

readable.on('error', (err) => {
    console.error('Stream error:', err);
});
```

## Express.js Basics

### Basic Application Setup
```javascript
const express = require('express');
const app = express();
const PORT = process.env.PORT || 3000;

// Basic middleware
app.use(express.json()); // Parse JSON bodies
app.use(express.urlencoded({ extended: true })); // Parse URL-encoded bodies

// Simple routes
app.get('/', (req, res) => {
    res.send('Hello World!');
});

app.get('/api/health', (req, res) => {
    res.json({ status: 'OK', timestamp: new Date().toISOString() });
});

// Start server
app.listen(PORT, () => {
    console.log(`Server running on port ${PORT}`);
});

// Handle graceful shutdown
process.on('SIGTERM', () => {
    console.log('SIGTERM received, shutting down gracefully');
    server.close(() => {
        console.log('Process terminated');
    });
});
```

### Advanced Application Structure
```javascript
// app.js
const express = require('express');
const helmet = require('helmet');
const cors = require('cors');
const compression = require('compression');
const rateLimit = require('express-rate-limit');

const app = express();

// Security middleware
app.use(helmet());
app.use(cors({
    origin: process.env.ALLOWED_ORIGINS?.split(',') || ['http://localhost:3000'],
    credentials: true
}));

// Performance middleware
app.use(compression());

// Rate limiting
const limiter = rateLimit({
    windowMs: 15 * 60 * 1000, // 15 minutes
    max: 100, // limit each IP to 100 requests per windowMs
    message: 'Too many requests from this IP'
});
app.use('/api/', limiter);

// Body parsing
app.use(express.json({ limit: '10mb' }));
app.use(express.urlencoded({ extended: true, limit: '10mb' }));

// Static files
app.use(express.static('public'));

// Routes
app.use('/api/users', require('./routes/users'));
app.use('/api/auth', require('./routes/auth'));

// 404 handler
app.use('*', (req, res) => {
    res.status(404).json({ error: 'Route not found' });
});

// Error handler
app.use(require('./middleware/errorHandler'));

module.exports = app;

// server.js
const app = require('./app');
const PORT = process.env.PORT || 3000;

app.listen(PORT, () => {
    console.log(`Server running in ${process.env.NODE_ENV} mode on port ${PORT}`);
});
```

## Routing & HTTP Methods

### Basic Routing
```javascript
const express = require('express');
const router = express.Router();

// GET routes
router.get('/', (req, res) => {
    res.json({ message: 'Get all items' });
});

router.get('/:id', (req, res) => {
    const { id } = req.params;
    res.json({ message: `Get item ${id}` });
});

// POST route
router.post('/', (req, res) => {
    const { body } = req;
    res.status(201).json({ 
        message: 'Item created', 
        data: body 
    });
});

// PUT route
router.put('/:id', (req, res) => {
    const { id } = req.params;
    const { body } = req;
    res.json({ 
        message: `Item ${id} updated`, 
        data: body 
    });
});

// PATCH route
router.patch('/:id', (req, res) => {
    const { id } = req.params;
    const { body } = req;
    res.json({ 
        message: `Item ${id} partially updated`, 
        updates: body 
    });
});

// DELETE route
router.delete('/:id', (req, res) => {
    const { id } = req.params;
    res.status(204).send(); // No content
});

module.exports = router;
```

### Advanced Routing Patterns
```javascript
// Route with query parameters
router.get('/search', (req, res) => {
    const { q, page = 1, limit = 10 } = req.query;
    
    res.json({
        query: q,
        page: parseInt(page),
        limit: parseInt(limit),
        results: [] // Your search logic here
    });
});

// Route with multiple parameters
router.get('/:category/:id', (req, res) => {
    const { category, id } = req.params;
    res.json({ category, id });
});

// Route with regex constraints
router.get('/user/:id(\\d+)', (req, res) => {
    // Only matches numeric IDs
    res.json({ userId: req.params.id });
});

// Chained route handlers
router.route('/items')
    .get((req, res) => {
        res.json({ action: 'get items' });
    })
    .post((req, res) => {
        res.json({ action: 'create item' });
    })
    .put((req, res) => {
        res.json({ action: 'replace items' });
    });

// Using router.param for parameter validation
router.param('id', (req, res, next, id) => {
    // Validate ID
    if (!/^\d+$/.test(id)) {
        return res.status(400).json({ error: 'Invalid ID format' });
    }
    req.itemId = parseInt(id);
    next();
});
```

### Route Organization
```javascript
// routes/users.js
const express = require('express');
const router = express.Router();
const userController = require('../controllers/userController');

// Apply middleware to specific routes
router.use('/:id', (req, res, next) => {
    console.log('User ID route accessed:', req.params.id);
    next();
});

router.get('/', userController.getAllUsers);
router.get('/:id', userController.getUserById);
router.post('/', userController.createUser);
router.put('/:id', userController.updateUser);
router.delete('/:id', userController.deleteUser);

// Nested routes
router.use('/:userId/orders', require('./orders'));

module.exports = router;

// routes/orders.js
const express = require('express');
const router = express.Router({ mergeParams: true }); // Access parent params

router.get('/', (req, res) => {
    const { userId } = req.params;
    res.json({ message: `Get orders for user ${userId}` });
});

module.exports = router;
```

## Middleware

### Custom Middleware
```javascript
// Logging middleware
const requestLogger = (req, res, next) => {
    const timestamp = new Date().toISOString();
    console.log(`${timestamp} - ${req.method} ${req.originalUrl} - ${req.ip}`);
    next();
};

// Authentication middleware
const authenticate = (req, res, next) => {
    const token = req.headers.authorization?.replace('Bearer ', '');
    
    if (!token) {
        return res.status(401).json({ error: 'Authentication required' });
    }
    
    try {
        // Verify token (using JWT for example)
        const decoded = jwt.verify(token, process.env.JWT_SECRET);
        req.user = decoded;
        next();
    } catch (error) {
        return res.status(401).json({ error: 'Invalid token' });
    }
};

// Authorization middleware
const authorize = (...roles) => {
    return (req, res, next) => {
        if (!req.user || !roles.includes(req.user.role)) {
            return res.status(403).json({ error: 'Insufficient permissions' });
        }
        next();
    };
};

// Usage
app.use(requestLogger);
app.get('/api/admin', authenticate, authorize('admin'), (req, res) => {
    res.json({ message: 'Admin access granted' });
});
```

### Error Handling Middleware
```javascript
// Async error wrapper (eliminates try-catch blocks)
const asyncHandler = (fn) => (req, res, next) => {
    Promise.resolve(fn(req, res, next)).catch(next);
};

// Custom error class
class AppError extends Error {
    constructor(message, statusCode) {
        super(message);
        this.statusCode = statusCode;
        this.isOperational = true;
        Error.captureStackTrace(this, this.constructor);
    }
}

// 404 middleware
const notFound = (req, res, next) => {
    const error = new AppError(`Not found - ${req.originalUrl}`, 404);
    next(error);
};

// Global error handler
const errorHandler = (err, req, res, next) => {
    let error = { ...err };
    error.message = err.message;

    // Log error
    console.error(err);

    // Mongoose bad ObjectId
    if (err.name === 'CastError') {
        const message = 'Resource not found';
        error = new AppError(message, 404);
    }

    // Mongoose duplicate key
    if (err.code === 11000) {
        const message = 'Duplicate field value entered';
        error = new AppError(message, 400);
    }

    // Mongoose validation error
    if (err.name === 'ValidationError') {
        const messages = Object.values(err.errors).map(val => val.message);
        const message = messages.join(', ');
        error = new AppError(message, 400);
    }

    res.status(error.statusCode || 500).json({
        success: false,
        error: error.message || 'Server Error',
        ...(process.env.NODE_ENV === 'development' && { stack: err.stack })
    });
};

// Usage in app
app.use(notFound);
app.use(errorHandler);
```

### Third-Party Middleware
```javascript
const express = require('express');
const morgan = require('morgan');
const helmet = require('helmet');
const cors = require('cors');
const compression = require('compression');
const cookieParser = require('cookie-parser');

const app = express();

// Logging
app.use(morgan('combined')); // 'combined', 'common', 'dev', 'tiny'

// Security
app.use(helmet({
    contentSecurityPolicy: {
        directives: {
            defaultSrc: ["'self'"],
            styleSrc: ["'self'", "'unsafe-inline'"],
            scriptSrc: ["'self'"],
            imgSrc: ["'self'", "data:", "https:"],
        },
    },
}));

// CORS
app.use(cors({
    origin: process.env.CLIENT_URL || 'http://localhost:3000',
    credentials: true
}));

// Compression
app.use(compression());

// Cookie parsing
app.use(cookieParser());

// Body parsing
app.use(express.json({ limit: '10mb' }));
app.use(express.urlencoded({ extended: true, limit: '10mb' }));

// File uploads (using multer)
const multer = require('multer');
const upload = multer({ 
    dest: 'uploads/',
    limits: {
        fileSize: 5 * 1024 * 1024 // 5MB
    },
    fileFilter: (req, file, cb) => {
        if (file.mimetype.startsWith('image/')) {
            cb(null, true);
        } else {
            cb(new Error('Only image files are allowed'), false);
        }
    }
});

app.post('/upload', upload.single('image'), (req, res) => {
    res.json({ filename: req.file.filename });
});
```

## Template Engines

### EJS Templates
```javascript
// Setup
app.set('view engine', 'ejs');
app.set('views', path.join(__dirname, 'views'));

// Basic route with template
app.get('/', (req, res) => {
    res.render('index', {
        title: 'Home Page',
        user: { name: 'John Doe' },
        products: ['Product 1', 'Product 2']
    });
});

// EJS template example (views/index.ejs)
/*
<!DOCTYPE html>
<html>
<head>
    <title><%= title %></title>
</head>
<body>
    <h1>Welcome, <%= user.name %></h1>
    
    <% if (products.length > 0) { %>
        <ul>
            <% products.forEach(product => { %>
                <li><%= product %></li>
            <% }) %>
        </ul>
    <% } else { %>
        <p>No products available</p>
    <% } %>
    
    <%- include('partials/footer') %>
</body>
</html>
*/
```

### Handlebars Templates
```javascript
const exphbs = require('express-handlebars');

// Setup
app.engine('handlebars', exphbs.engine({
    defaultLayout: 'main',
    helpers: {
        eq: (a, b) => a === b,
        formatDate: (date) => new Date(date).toLocaleDateString()
    }
}));
app.set('view engine', 'handlebars');

// Route
app.get('/profile', (req, res) => {
    res.render('profile', {
        user: {
            name: 'Jane Doe',
            email: 'jane@example.com',
            joined: new Date('2020-01-01')
        }
    });
});
```

### Pug Templates
```javascript
// Setup
app.set('view engine', 'pug');
app.set('views', path.join(__dirname, 'views'));

// Pug template example (views/layout.pug)
/*
doctype html
html
    head
        title= title
        link(rel='stylesheet', href='/styles.css')
    body
        header
            h1 My Website
        main
            block content
        footer
            p Copyright © 2024
*/

// views/index.pug
/*
extends layout

block content
    h2 Welcome to #{title}
    
    if user
        p Hello, #{user.name}!
    
    ul
        each product in products
            li= product
*/
```

## Database Integration

### MongoDB with Mongoose
```javascript
const mongoose = require('mongoose');

// Connection
mongoose.connect(process.env.MONGODB_URI, {
    useNewUrlParser: true,
    useUnifiedTopology: true,
})
.then(() => console.log('MongoDB connected'))
.catch(err => console.error('MongoDB connection error:', err));

// Schema definition
const userSchema = new mongoose.Schema({
    name: {
        type: String,
        required: [true, 'Name is required'],
        trim: true,
        maxlength: [50, 'Name cannot exceed 50 characters']
    },
    email: {
        type: String,
        required: [true, 'Email is required'],
        unique: true,
        lowercase: true,
        validate: {
            validator: function(email) {
                return /^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(email);
            },
            message: 'Please provide a valid email'
        }
    },
    password: {
        type: String,
        required: true,
        minlength: 6,
        select: false // Don't return in queries by default
    },
    role: {
        type: String,
        enum: ['user', 'admin'],
        default: 'user'
    },
    createdAt: {
        type: Date,
        default: Date.now
    }
}, {
    timestamps: true, // Adds createdAt and updatedAt
    toJSON: { virtuals: true },
    toObject: { virtuals: true }
});

// Virtuals
userSchema.virtual('profileUrl').get(function() {
    return `/users/${this._id}`;
});

// Instance methods
userSchema.methods.getPublicProfile = function() {
    const user = this.toObject();
    delete user.password;
    return user;
};

// Static methods
userSchema.statics.findByEmail = function(email) {
    return this.findOne({ email: email.toLowerCase() });
};

// Pre and post hooks
userSchema.pre('save', async function(next) {
    if (!this.isModified('password')) return next();
    
    // Hash password before saving
    this.password = await bcrypt.hash(this.password, 12);
    next();
});

// Indexes
userSchema.index({ email: 1 });
userSchema.index({ createdAt: -1 });

const User = mongoose.model('User', userSchema);

// Usage in controllers
exports.createUser = async (req, res, next) => {
    try {
        const user = await User.create(req.body);
        res.status(201).json({
            success: true,
            data: user.getPublicProfile()
        });
    } catch (error) {
        next(error);
    }
};
```

### PostgreSQL with Sequelize
```javascript
const { Sequelize, DataTypes } = require('sequelize');

// Connection
const sequelize = new Sequelize(process.env.DATABASE_URL, {
    dialect: 'postgres',
    logging: process.env.NODE_ENV === 'development' ? console.log : false,
    pool: {
        max: 5,
        min: 0,
        acquire: 30000,
        idle: 10000
    }
});

// Model definition
const User = sequelize.define('User', {
    id: {
        type: DataTypes.UUID,
        defaultValue: DataTypes.UUIDV4,
        primaryKey: true
    },
    name: {
        type: DataTypes.STRING,
        allowNull: false,
        validate: {
            notEmpty: true,
            len: [2, 50]
        }
    },
    email: {
        type: DataTypes.STRING,
        allowNull: false,
        unique: true,
        validate: {
            isEmail: true
        }
    },
    password: {
        type: DataTypes.STRING,
        allowNull: false
    }
}, {
    tableName: 'users',
    timestamps: true,
    hooks: {
        beforeCreate: async (user) => {
            if (user.password) {
                user.password = await bcrypt.hash(user.password, 12);
            }
        }
    }
});

// Associations
User.hasMany(Post, { foreignKey: 'userId' });
Post.belongsTo(User, { foreignKey: 'userId' });

// Usage
exports.getUsers = async (req, res) => {
    try {
        const users = await User.findAll({
            attributes: { exclude: ['password'] },
            include: [{
                model: Post,
                attributes: ['id', 'title']
            }]
        });
        res.json(users);
    } catch (error) {
        res.status(500).json({ error: error.message });
    }
};
```

### Redis Integration
```javascript
const redis = require('redis');
const client = redis.createClient({
    url: process.env.REDIS_URL
});

client.on('error', (err) => console.log('Redis Client Error', err));
client.on('connect', () => console.log('Redis connected'));

await client.connect();

// Caching middleware
const cache = (key, expire = 3600) => { // 1 hour default
    return async (req, res, next) => {
        const cacheKey = key || req.originalUrl;
        
        try {
            const cached = await client.get(cacheKey);
            if (cached) {
                return res.json(JSON.parse(cached));
            }
            
            // Store original send method
            const originalSend = res.send;
            res.send = function(data) {
                // Cache the response
                client.setEx(cacheKey, expire, data);
                originalSend.call(this, data);
            };
            
            next();
        } catch (error) {
            console.error('Cache error:', error);
            next();
        }
    };
};

// Usage
app.get('/api/products', cache('products', 1800), async (req, res) => {
    const products = await Product.find();
    res.json(products);
});

// Rate limiting with Redis
const rateLimit = require('express-rate-limit');
const RedisStore = require('rate-limit-redis');

const limiter = rateLimit({
    store: new RedisStore({
        sendCommand: (...args) => client.sendCommand(args),
    }),
    windowMs: 15 * 60 * 1000, // 15 minutes
    max: 100, // limit each IP to 100 requests per windowMs
    message: 'Too many requests from this IP'
});
```

## Authentication & Security

### JWT Authentication
```javascript
const jwt = require('jsonwebtoken');
const bcrypt = require('bcryptjs');

// Generate JWT token
const generateToken = (userId) => {
    return jwt.sign(
        { userId }, 
        process.env.JWT_SECRET, 
        { expiresIn: process.env.JWT_EXPIRES_IN || '7d' }
    );
};

// Send token in response
const sendTokenResponse = (user, statusCode, res) => {
    const token = generateToken(user._id);
    
    const options = {
        expires: new Date(Date.now() + 7 * 24 * 60 * 60 * 1000), // 7 days
        httpOnly: true,
        secure: process.env.NODE_ENV === 'production'
    };
    
    res.status(statusCode)
        .cookie('token', token, options)
        .json({
            success: true,
            token,
            data: user.getPublicProfile()
        });
};

// Auth controller
exports.register = async (req, res, next) => {
    try {
        const { name, email, password } = req.body;
        
        const user = await User.create({ name, email, password });
        sendTokenResponse(user, 201, res);
    } catch (error) {
        next(error);
    }
};

exports.login = async (req, res, next) => {
    try {
        const { email, password } = req.body;
        
        if (!email || !password) {
            return next(new AppError('Please provide email and password', 400));
        }
        
        const user = await User.findOne({ email }).select('+password');
        
        if (!user || !(await bcrypt.compare(password, user.password))) {
            return next(new AppError('Invalid credentials', 401));
        }
        
        sendTokenResponse(user, 200, res);
    } catch (error) {
        next(error);
    }
};

exports.logout = (req, res) => {
    res.cookie('token', 'none', {
        expires: new Date(Date.now()),
        httpOnly: true
    });
    
    res.json({
        success: true,
        message: 'Logged out successfully'
    });
};
```

### Passport.js Strategies
```javascript
const passport = require('passport');
const LocalStrategy = require('passport-local').Strategy;
const JwtStrategy = require('passport-jwt').Strategy;
const ExtractJwt = require('passport-jwt').ExtractJwt;

// Local strategy
passport.use(new LocalStrategy({
    usernameField: 'email'
}, async (email, password, done) => {
    try {
        const user = await User.findOne({ email }).select('+password');
        
        if (!user) {
            return done(null, false, { message: 'Invalid credentials' });
        }
        
        const isMatch = await bcrypt.compare(password, user.password);
        
        if (!isMatch) {
            return done(null, false, { message: 'Invalid credentials' });
        }
        
        return done(null, user);
    } catch (error) {
        return done(error);
    }
}));

// JWT strategy
passport.use(new JwtStrategy({
    jwtFromRequest: ExtractJwt.fromAuthHeaderAsBearerToken(),
    secretOrKey: process.env.JWT_SECRET
}, async (payload, done) => {
    try {
        const user = await User.findById(payload.userId);
        
        if (!user) {
            return done(null, false);
        }
        
        return done(null, user);
    } catch (error) {
        return done(error);
    }
}));

// Protected route
app.get('/profile', 
    passport.authenticate('jwt', { session: false }), 
    (req, res) => {
        res.json(req.user);
    }
);
```

### Security Best Practices
```javascript
const express = require('express');
const helmet = require('helmet');
const cors = require('cors');
const rateLimit = require('express-rate-limit');
const mongoSanitize = require('express-mongo-sanitize');
const xss = require('xss-clean');
const hpp = require('hpp');

const app = express();

// Security headers
app.use(helmet());

// CORS
app.use(cors({
    origin: process.env.ALLOWED_ORIGINS?.split(',') || true,
    credentials: true
}));

// Rate limiting
const limiter = rateLimit({
    windowMs: 15 * 60 * 1000, // 15 minutes
    max: process.env.RATE_LIMIT_MAX || 100,
    message: 'Too many requests from this IP'
});
app.use('/api', limiter);

// Data sanitization
app.use(mongoSanitize()); // NoSQL injection protection
app.use(xss()); // XSS protection

// Prevent parameter pollution
app.use(hpp({
    whitelist: ['price', 'rating'] // Parameters that can have multiple values
}));

// Security headers middleware
app.use((req, res, next) => {
    res.setHeader('X-Content-Type-Options', 'nosniff');
    res.setHeader('X-Frame-Options', 'DENY');
    res.setHeader('X-XSS-Protection', '1; mode=block');
    res.setHeader('Strict-Transport-Security', 'max-age=31536000; includeSubDomains');
    next();
});
```

## Error Handling

### Custom Error Classes
```javascript
class AppError extends Error {
    constructor(message, statusCode) {
        super(message);
        this.statusCode = statusCode;
        this.status = `${statusCode}`.startsWith('4') ? 'fail' : 'error';
        this.isOperational = true;
        
        Error.captureStackTrace(this, this.constructor);
    }
}

class ValidationError extends AppError {
    constructor(errors) {
        super('Validation failed', 422);
        this.errors = errors;
    }
}

class NotFoundError extends AppError {
    constructor(resource = 'Resource') {
        super(`${resource} not found`, 404);
    }
}

class UnauthorizedError extends AppError {
    constructor(message = 'Not authorized') {
        super(message, 401);
    }
}
```

### Global Error Handler
```javascript
const errorHandler = (err, req, res, next) => {
    err.statusCode = err.statusCode || 500;
    err.status = err.status || 'error';

    let error = { ...err };
    error.message = err.message;

    // Development vs production errors
    if (process.env.NODE_ENV === 'development') {
        res.status(err.statusCode).json({
            status: err.status,
            error: err,
            message: err.message,
            stack: err.stack
        });
    } else {
        // Production error response
        if (err.name === 'CastError') {
            error = new AppError('Resource not found', 404);
        }
        
        if (err.code === 11000) {
            const field = Object.keys(err.keyValue)[0];
            error = new AppError(`${field} already exists`, 400);
        }
        
        if (err.name === 'ValidationError') {
            const errors = Object.values(err.errors).map(e => e.message);
            error = new AppError(`Invalid input: ${errors.join(', ')}`, 400);
        }
        
        if (err.name === 'JsonWebTokenError') {
            error = new AppError('Invalid token', 401);
        }
        
        if (err.name === 'TokenExpiredError') {
            error = new AppError('Token expired', 401);
        }

        res.status(error.statusCode).json({
            status: error.status,
            message: error.message || 'Something went wrong'
        });
    }
};

// Async error wrapper
const catchAsync = (fn) => {
    return (req, res, next) => {
        fn(req, res, next).catch(next);
    };
};

// Usage in controllers
exports.getUser = catchAsync(async (req, res, next) => {
    const user = await User.findById(req.params.id);
    
    if (!user) {
        return next(new NotFoundError('User'));
    }
    
    res.json({
        status: 'success',
        data: user
    });
});
```

## File Handling

### Multer Configuration
```javascript
const multer = require('multer');
const path = require('path');
const fs = require('fs');

// Ensure upload directory exists
const uploadDir = 'uploads/';
if (!fs.existsSync(uploadDir)) {
    fs.mkdirSync(uploadDir, { recursive: true });
}

// Storage configuration
const storage = multer.diskStorage({
    destination: (req, file, cb) => {
        cb(null, uploadDir);
    },
    filename: (req, file, cb) => {
        const uniqueSuffix = Date.now() + '-' + Math.round(Math.random() * 1E9);
        const ext = path.extname(file.originalname);
        cb(null, file.fieldname + '-' + uniqueSuffix + ext);
    }
});

// File filter
const fileFilter = (req, file, cb) => {
    const allowedTypes = {
        'image/jpeg': true,
        'image/jpg': true,
        'image/png': true,
        'image/gif': true,
        'application/pdf': true
    };
    
    if (allowedTypes[file.mimetype]) {
        cb(null, true);
    } else {
        cb(new Error('Invalid file type'), false);
    }
};

const upload = multer({
    storage,
    limits: {
        fileSize: 5 * 1024 * 1024 // 5MB
    },
    fileFilter
});

// Usage in routes
app.post('/upload-single', upload.single('avatar'), (req, res) => {
    res.json({
        message: 'File uploaded successfully',
        file: req.file
    });
});

app.post('/upload-multiple', upload.array('documents', 5), (req, res) => {
    res.json({
        message: 'Files uploaded successfully',
        files: req.files
    });
});

app.post('/upload-fields', upload.fields([
    { name: 'avatar', maxCount: 1 },
    { name: 'gallery', maxCount: 5 }
]), (req, res) => {
    res.json({
        message: 'Files uploaded successfully',
        avatar: req.files.avatar,
        gallery: req.files.gallery
    });
});
```

### File Processing & Cloud Storage
```javascript
const sharp = require('sharp');
const { S3Client, PutObjectCommand } = require('@aws-sdk/client-s3');

const s3Client = new S3Client({
    region: process.env.AWS_REGION,
    credentials: {
        accessKeyId: process.env.AWS_ACCESS_KEY_ID,
        secretAccessKey: process.env.AWS_SECRET_ACCESS_KEY
    }
});

// Image processing middleware
const processImage = catchAsync(async (req, res, next) => {
    if (!req.file) return next();
    
    try {
        // Resize and optimize image
        const processedImage = await sharp(req.file.buffer)
            .resize(800, 600, {
                fit: 'inside',
                withoutEnlargement: true
            })
            .jpeg({ quality: 80 })
            .toBuffer();
        
        // Upload to S3
        const key = `images/${Date.now()}-${req.file.originalname}`;
        
        const uploadParams = {
            Bucket: process.env.AWS_BUCKET_NAME,
            Key: key,
            Body: processedImage,
            ContentType: 'image/jpeg'
        };
        
        await s3Client.send(new PutObjectCommand(uploadParams));
        
        // Store file info in request
        req.file.location = `https://${process.env.AWS_BUCKET_NAME}.s3.${process.env.AWS_REGION}.amazonaws.com/${key}`;
        req.file.key = key;
        
        next();
    } catch (error) {
        next(error);
    }
});

// Usage
app.post('/upload-avatar', 
    upload.single('avatar'),
    processImage,
    (req, res) => {
        res.json({
            message: 'Image uploaded and processed',
            imageUrl: req.file.location
        });
    }
);
```

## API Development

### RESTful API Structure
```javascript
// controllers/userController.js
exports.getAllUsers = catchAsync(async (req, res, next) => {
    const features = new APIFeatures(User.find(), req.query)
        .filter()
        .sort()
        .limitFields()
        .paginate();
    
    const users = await features.query;
    
    res.json({
        status: 'success',
        results: users.length,
        data: users
    });
});

exports.getUser = catchAsync(async (req, res, next) => {
    const user = await User.findById(req.params.id);
    
    if (!user) {
        return next(new NotFoundError('User'));
    }
    
    res.json({
        status: 'success',
        data: user
    });
});

exports.createUser = catchAsync(async (req, res, next) => {
    const user = await User.create(req.body);
    
    res.status(201).json({
        status: 'success',
        data: user
    });
});

exports.updateUser = catchAsync(async (req, res, next) => {
    const user = await User.findByIdAndUpdate(
        req.params.id,
        req.body,
        {
            new: true,
            runValidators: true
        }
    );
    
    if (!user) {
        return next(new NotFoundError('User'));
    }
    
    res.json({
        status: 'success',
        data: user
    });
});

exports.deleteUser = catchAsync(async (req, res, next) => {
    const user = await User.findByIdAndDelete(req.params.id);
    
    if (!user) {
        return next(new NotFoundError('User'));
    }
    
    res.status(204).json({
        status: 'success',
        data: null
    });
});
```

### API Features Class (Filtering, Sorting, Pagination)
```javascript
class APIFeatures {
    constructor(query, queryString) {
        this.query = query;
        this.queryString = queryString;
    }
    
    filter() {
        const queryObj = { ...this.queryString };
        const excludedFields = ['page', 'sort', 'limit', 'fields'];
        excludedFields.forEach(field => delete queryObj[field]);
        
        // Advanced filtering
        let queryStr = JSON.stringify(queryObj);
        queryStr = queryStr.replace(/\b(gte|gt|lte|lt)\b/g, match => `$${match}`);
        
        this.query = this.query.find(JSON.parse(queryStr));
        return this;
    }
    
    sort() {
        if (this.queryString.sort) {
            const sortBy = this.queryString.sort.split(',').join(' ');
            this.query = this.query.sort(sortBy);
        } else {
            this.query = this.query.sort('-createdAt');
        }
        return this;
    }
    
    limitFields() {
        if (this.queryString.fields) {
            const fields = this.queryString.fields.split(',').join(' ');
            this.query = this.query.select(fields);
        } else {
            this.query = this.query.select('-__v');
        }
        return this;
    }
    
    paginate() {
        const page = parseInt(this.queryString.page, 10) || 1;
        const limit = parseInt(this.queryString.limit, 10) || 100;
        const skip = (page - 1) * limit;
        
        this.query = this.query.skip(skip).limit(limit);
        return this;
    }
}
```

### WebSocket Integration
```javascript
const { Server } = require('socket.io');
const http = require('http');

const app = express();
const server = http.createServer(app);
const io = new Server(server, {
    cors: {
        origin: process.env.CLIENT_URL,
        methods: ['GET', 'POST']
    }
});

// Socket.io connection handling
io.on('connection', (socket) => {
    console.log('User connected:', socket.id);
    
    // Join room
    socket.on('join-room', (roomId) => {
        socket.join(roomId);
        console.log(`User ${socket.id} joined room ${roomId}`);
    });
    
    // Handle chat messages
    socket.on('send-message', (data) => {
        socket.to(data.roomId).emit('receive-message', {
            ...data,
            socketId: socket.id,
            timestamp: new Date()
        });
    });
    
    // Handle disconnection
    socket.on('disconnect', () => {
        console.log('User disconnected:', socket.id);
    });
});

// Express route that emits socket events
app.post('/api/messages', async (req, res) => {
    try {
        const message = await Message.create(req.body);
        
        // Emit to all clients in the room
        io.to(message.roomId).emit('new-message', message);
        
        res.status(201).json(message);
    } catch (error) {
        res.status(400).json({ error: error.message });
    }
});

module.exports = { app, server, io };
```

## Testing

### Jest Test Setup
```javascript
// __tests__/user.test.js
const request = require('supertest');
const mongoose = require('mongoose');
const app = require('../app');
const User = require('../models/User');

describe('User API', () => {
    beforeAll(async () => {
        await mongoose.connect(process.env.TEST_DATABASE_URL);
    });
    
    afterAll(async () => {
        await mongoose.connection.dropDatabase();
        await mongoose.connection.close();
    });
    
    beforeEach(async () => {
        await User.deleteMany();
    });
    
    describe('POST /api/users', () => {
        it('should create a new user', async () => {
            const userData = {
                name: 'Test User',
                email: 'test@example.com',
                password: 'password123'
            };
            
            const response = await request(app)
                .post('/api/users')
                .send(userData)
                .expect(201);
            
            expect(response.body.data.name).toBe(userData.name);
            expect(response.body.data.email).toBe(userData.email);
            expect(response.body.data.password).toBeUndefined();
        });
        
        it('should not create user with invalid data', async () => {
            const response = await request(app)
                .post('/api/users')
                .send({ name: 'Test' })
                .expect(400);
            
            expect(response.body.error).toBeDefined();
        });
    });
    
    describe('GET /api/users', () => {
        it('should get all users', async () => {
            await User.create([
                { name: 'User 1', email: 'user1@test.com', password: 'pass123' },
                { name: 'User 2', email: 'user2@test.com', password: 'pass123' }
            ]);
            
            const response = await request(app)
                .get('/api/users')
                .expect(200);
            
            expect(response.body.data.length).toBe(2);
        });
    });
});
```

### Integration Tests
```javascript
// __tests__/auth.integration.test.js
describe('Authentication Flow', () => {
    let authToken;
    
    beforeAll(async () => {
        // Create test user
        await User.create({
            name: 'Test User',
            email: 'auth@test.com',
            password: 'password123'
        });
    });
    
    it('should login and get token', async () => {
        const response = await request(app)
            .post('/api/auth/login')
            .send({
                email: 'auth@test.com',
                password: 'password123'
            })
            .expect(200);
        
        expect(response.body.token).toBeDefined();
        authToken = response.body.token;
    });
    
    it('should access protected route with token', async () => {
        const response = await request(app)
            .get('/api/auth/me')
            .set('Authorization', `Bearer ${authToken}`)
            .expect(200);
        
        expect(response.body.data.email).toBe('auth@test.com');
    });
    
    it('should not access protected route without token', async () => {
        await request(app)
            .get('/api/auth/me')
            .expect(401);
    });
});
```

### Mocking and Stubs
```javascript
// __tests__/user.service.test.js
const userService = require('../services/userService');
const User = require('../models/User');

jest.mock('../models/User');

describe('User Service', () => {
    beforeEach(() => {
        jest.clearAllMocks();
    });
    
    it('should create user with hashed password', async () => {
        const userData = {
            name: 'Test User',
            email: 'test@example.com',
            password: 'plainpassword'
        };
        
        User.create.mockResolvedValue({
            ...userData,
            _id: '507f1f77bcf86cd799439011',
            password: 'hashedpassword'
        });
        
        const result = await userService.createUser(userData);
        
        expect(User.create).toHaveBeenCalledWith(
            expect.objectContaining({
                name: userData.name,
                email: userData.email
            })
        );
        expect(result.password).toBe('hashedpassword');
    });
});
```

## Performance & Optimization

### Caching Strategies
```javascript
const redis = require('redis');
const client = redis.createClient(process.env.REDIS_URL);

// Cache middleware
const cache = (key, expire = 3600) => {
    return async (req, res, next) => {
        const cacheKey = key || req.originalUrl;
        
        try {
            const cached = await client.get(cacheKey);
            if (cached) {
                return res.json(JSON.parse(cached));
            }
            
            const originalSend = res.send;
            res.send = function(data) {
                client.setEx(cacheKey, expire, data);
                originalSend.call(this, data);
            };
            
            next();
        } catch (error) {
            next();
        }
    };
};

// Database query caching
const getUsersWithCache = async () => {
    const cacheKey = 'users:all';
    
    try {
        const cached = await client.get(cacheKey);
        if (cached) {
            return JSON.parse(cached);
        }
        
        const users = await User.find().lean();
        await client.setEx(cacheKey, 1800, JSON.stringify(users)); // 30 minutes
        
        return users;
    } catch (error) {
        // Fallback to database if cache fails
        return await User.find().lean();
    }
};
```

### Compression and Optimization
```javascript
const compression = require('compression');
const helmet = require('helmet');

app.use(compression({
    level: 6, // Compression level (0-9)
    threshold: 1024, // Only compress responses larger than 1KB
    filter: (req, res) => {
        if (req.headers['x-no-compression']) {
            return false;
        }
        return compression.filter(req, res);
    }
}));

// Static file optimization
app.use(express.static('public', {
    maxAge: '1d', // Cache static files for 1 day
    etag: false, // Disable etag for better performance
    setHeaders: (res, path) => {
        if (path.endsWith('.html')) {
            res.setHeader('Cache-Control', 'no-cache');
        }
    }
}));

// Response time middleware
app.use((req, res, next) => {
    const start = Date.now();
    
    res.on('finish', () => {
        const duration = Date.now() - start;
        console.log(`${req.method} ${req.originalUrl} - ${duration}ms`);
        
        // Log slow requests
        if (duration > 1000) {
            console.warn(`Slow request: ${req.originalUrl} took ${duration}ms`);
        }
    });
    
    next();
});
```

### Database Optimization
```javascript
// Indexing for performance
userSchema.index({ email: 1 });
userSchema.index({ createdAt: -1 });
userSchema.index({ location: '2dsphere' }); // For geospatial queries

// Query optimization
const getUsersOptimized = async (filters = {}) => {
    return await User.find(filters)
        .select('name email createdAt') // Only select needed fields
        .lean() // Return plain JavaScript objects (faster)
        .limit(100) // Limit results
        .sort({ createdAt: -1 }) // Sort by creation date
        .exec();
};

// Aggregation pipeline for complex queries
const getUserStats = async () => {
    return await User.aggregate([
        {
            $group: {
                _id: '$role',
                count: { $sum: 1 },
                averageAge: { $avg: '$age' }
            }
        },
        {
            $sort: { count: -1 }
        }
    ]);
};
```

## Deployment

### Production Configuration
```javascript
// config/production.js
module.exports = {
    port: process.env.PORT || 3000,
    database: {
        url: process.env.MONGODB_URI,
        options: {
            useNewUrlParser: true,
            useUnifiedTopology: true,
            poolSize: 10, // Connection pool size
            bufferMaxEntries: 0 // Disable buffering
        }
    },
    redis: {
        url: process.env.REDIS_URL
    },
    security: {
        jwtSecret: process.env.JWT_SECRET,
        bcryptRounds: 12
    },
    logging: {
        level: 'warn', // Only log warnings and errors in production
        format: 'combined' // Standard Apache combined log format
    }
};
```

### PM2 Ecosystem File
```javascript
// ecosystem.config.js
module.exports = {
    apps: [{
        name: 'my-app',
        script: './server.js',
        instances: 'max', // Use all available CPU cores
        exec_mode: 'cluster', // Enable clustering
        env: {
            NODE_ENV: 'development',
            PORT: 3000
        },
        env_production: {
            NODE_ENV: 'production',
            PORT: 3000
        },
        // Logging
        log_file: './logs/combined.log',
        out_file: './logs/out.log',
        error_file: './logs/error.log',
        // Monitoring
        max_memory_restart: '1G', // Restart if memory exceeds 1GB
        watch: false, // Don't watch for file changes in production
        ignore_watch: ['node_modules', 'logs'],
        // Graceful shutdown
        kill_timeout: 5000, // Wait 5 seconds before force killing
        listen_timeout: 3000 // Wait 3 seconds for app to start listening
    }]
};
```

### Docker Configuration
```dockerfile
# Dockerfile
FROM node:18-alpine

# Install dependencies
RUN apk add --no-cache \
    python3 \
    make \
    g++

# Create app directory
WORKDIR /usr/src/app

# Copy package files
COPY package*.json ./

# Install dependencies
RUN npm ci --only=production

# Copy app source
COPY . .

# Create non-root user
RUN addgroup -g 1001 -S nodejs
RUN adduser -S nextjs -u 1001

# Change ownership
RUN chown -R nextjs:nodejs /usr/src/app
USER nextjs

# Expose port
EXPOSE 3000

# Health check
HEALTHCHECK --interval=30s --timeout=3s --start-period=5s --retries=3 \
    CMD node health-check.js

# Start app
CMD [ "node", "server.js" ]
```

```yaml
# docker-compose.yml
version: '3.8'
services:
  app:
    build: .
    ports:
      - "3000:3000"
    environment:
      - NODE_ENV=production
      - MONGODB_URI=mongodb://mongo:27017/myapp
      - REDIS_URL=redis://redis:6379
    depends_on:
      - mongo
      - redis
    restart: unless-stopped

  mongo:
    image: mongo:6
    volumes:
      - mongo_data:/data/db
    environment:
      - MONGO_INITDB_ROOT_USERNAME=admin
      - MONGO_INITDB_ROOT_PASSWORD=password
    restart: unless-stopped

  redis:
    image: redis:7-alpine
    volumes:
      - redis_data:/data
    restart: unless-stopped

volumes:
  mongo_data:
  redis_data:
```

## Best Practices

### Code Organization
```javascript
// Recommended project structure
/*
project/
├── config/           # Configuration files
│   ├── database.js
│   └── production.js
├── controllers/      # Route controllers
│   ├── userController.js
│   └── authController.js
├── models/          # Database models
│   └── User.js
├── routes/          # Route definitions
│   ├── users.js
│   └── auth.js
├── middleware/      # Custom middleware
│   ├── errorHandler.js
│   └── auth.js
├── services/        # Business logic
│   └── userService.js
├── utils/           # Utility functions
│   ├── appError.js
│   └── catchAsync.js
├── public/          # Static files
├── tests/           # Test files
├── app.js           # Express app setup
└── server.js        # Server entry point
*/
```

### Environment Configuration
```javascript
// config/env.js
require('dotenv').config();

const env = process.env.NODE_ENV || 'development';

const baseConfig = {
    env,
    port: process.env.PORT || 3000,
    secrets: {
        jwt: process.env.JWT_SECRET,
        jwtExp: process.env.JWT_EXPIRES_IN
    },
    db: {
        url: process.env.MONGODB_URI
    }
};

const envConfig = {
    development: {
        logging: 'dev',
        db: {
            debug: true
        }
    },
    production: {
        logging: 'combined',
        db: {
            debug: false
        }
    },
    test: {
        port: 3001,
        db: {
            url: process.env.TEST_MONGODB_URI
        }
    }
};

module.exports = { ...baseConfig, ...envConfig[env] };
```

### Security Checklist
```javascript
// Security middleware stack
app.use(helmet()); // Security headers
app.use(cors(corsOptions)); // CORS configuration
app.use(rateLimit(limiter)); // Rate limiting
app.use(express.json({ limit: '10mb' })); // Body parser with limit
app.use(express.urlencoded({ extended: true, limit: '10mb' }));
app.use(mongoSanitize()); // NoSQL injection protection
app.use(xss()); // XSS protection
app.use(hpp()); // HTTP parameter pollution protection

// Additional security headers
app.use((req, res, next) => {
    res.removeHeader('X-Powered-By'); // Remove Express header
    res.setHeader('X-Content-Type-Options', 'nosniff');
    res.setHeader('X-Frame-Options', 'DENY');
    res.setHeader('X-XSS-Protection', '1; mode=block');
    next();
});
```

### Performance Checklist
```javascript
// Performance optimizations
app.use(compression()); // Response compression
app.use(express.static('public', staticOptions)); // Static file caching

// Database optimizations
mongoose.set('bufferCommands', false); // Disable command buffering
mongoose.set('debug', process.env.NODE_ENV === 'development'); // Query logging

// Cluster mode for multi-core CPUs
if (cluster.isMaster && process.env.NODE_ENV === 'production') {
    const numCPUs = os.cpus().length;
    
    for (let i = 0; i < numCPUs; i++) {
        cluster.fork();
    }
    
    cluster.on('exit', (worker) => {
        console.log(`Worker ${worker.process.pid} died`);
        cluster.fork();
    });
} else {
    app.listen(PORT, () => {
        console.log(`Server running on port ${PORT}`);
    });
}
```

---

## Quick Reference Card

### Essential NPM Packages
```bash
# Security
npm install helmet cors express-rate-limit
npm install express-mongo-sanitize xss-clean hpp

# Development
npm install morgan nodemon

# Database
npm install mongoose sequelize redis

# Authentication
npm install bcryptjs jsonwebtoken passport

# File handling
npm install multer sharp

# Testing
npm install jest supertest

# Deployment
npm install compression pm2
```

### Common Middleware Stack
```javascript
app.use(helmet());
app.use(cors());
app.use(compression());
app.use(express.json());
app.use(express.urlencoded({ extended: true }));
app.use(cookieParser());
app.use(express.static('public'));
```

### Error Handling Pattern
```javascript
// Throw errors in controllers
if (!user) {
    throw new AppError('User not found', 404);
}

// Wrap async functions
exports.getUser = catchAsync(async (req, res, next) => {
    const user = await User.findById(req.params.id);
    if (!user) {
        return next(new AppError('User not found', 404));
    }
    res.json(user);
});

// Global error handler
app.use((err, req, res, next) => {
    err.statusCode = err.statusCode || 500;
    res.status(err.statusCode).json({
        status: err.status,
        message: err.message
    });
});
```

### Pro Tips
1. **Use environment variables** for configuration
2. **Implement proper error handling** from day one
3. **Use middleware** for cross-cutting concerns
4. **Validate input** at the route level
5. **Use async/await** with proper error handling
6. **Implement rate limiting** in production
7. **Use compression** for better performance
8. **Enable clustering** for multi-core CPUs
9. **Monitor your application** with proper logging
10. **Write tests** for critical functionality

---

*Remember: Node.js and Express.js provide a powerful foundation for building web applications. Focus on writing clean, maintainable code and follow security best practices to build robust and scalable applications!*