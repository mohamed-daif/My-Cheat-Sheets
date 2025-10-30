# API Security & Authentication Complete Guide

## Table of Contents
- [Security Fundamentals](#security-fundamentals)
- [Authentication Methods](#authentication-methods)
- [Authorization Strategies](#authorization-strategies)
- [Common Vulnerabilities](#common-vulnerabilities)
- [Security Headers](#security-headers)
- [Input Validation & Sanitization](#input-validation--sanitization)
- [Rate Limiting & Throttling](#rate-limiting--throttling)
- [Data Protection](#data-protection)
- [Security Testing](#security-testing)
- [Monitoring & Logging](#monitoring--logging)
- [Compliance & Standards](#compliance--standards)
- [Best Practices Checklist](#best-practices-checklist)

## Security Fundamentals

### CIA Triad
```text
Confidentiality - Data is protected from unauthorized access
Integrity      - Data cannot be modified without detection
Availability   - Systems are available when needed
```

### Security Principles
```text
1. Defense in Depth - Multiple layers of security
2. Least Privilege - Minimum access required
3. Zero Trust - Never trust, always verify
4. Fail Securely - Default to secure state
```

## Authentication Methods

### API Keys
```javascript
// Simple API Key Authentication
const apiKeyAuth = (req, res, next) => {
    const apiKey = req.headers['x-api-key'] || req.query.api_key;
    
    if (!apiKey) {
        return res.status(401).json({ error: 'API key required' });
    }
    
    // Validate API key
    const isValid = validateApiKey(apiKey);
    if (!isValid) {
        return res.status(401).json({ error: 'Invalid API key' });
    }
    
    // Get user/service associated with API key
    req.user = await getUserByApiKey(apiKey);
    next();
};

// Usage
app.use('/api/', apiKeyAuth);
```

### JWT (JSON Web Tokens)
```javascript
const jwt = require('jsonwebtoken');
const bcrypt = require('bcryptjs');

// JWT Configuration
const JWT_CONFIG = {
    secret: process.env.JWT_SECRET,
    expiresIn: process.env.JWT_EXPIRES_IN || '15m',
    refreshExpiresIn: '7d'
};

// Generate Tokens
const generateTokens = (userId, payload = {}) => {
    const accessToken = jwt.sign(
        { userId, ...payload },
        JWT_CONFIG.secret,
        { expiresIn: JWT_CONFIG.expiresIn }
    );
    
    const refreshToken = jwt.sign(
        { userId, type: 'refresh' },
        JWT_CONFIG.secret,
        { expiresIn: JWT_CONFIG.refreshExpiresIn }
    );
    
    return { accessToken, refreshToken };
};

// JWT Authentication Middleware
const authenticateJWT = (req, res, next) => {
    const authHeader = req.headers.authorization;
    
    if (!authHeader || !authHeader.startsWith('Bearer ')) {
        return res.status(401).json({ error: 'Authentication required' });
    }
    
    const token = authHeader.substring(7);
    
    try {
        const decoded = jwt.verify(token, JWT_CONFIG.secret);
        req.user = decoded;
        next();
    } catch (error) {
        if (error.name === 'TokenExpiredError') {
            return res.status(401).json({ error: 'Token expired' });
        }
        return res.status(401).json({ error: 'Invalid token' });
    }
};

// Refresh Token Endpoint
app.post('/auth/refresh', async (req, res) => {
    const { refreshToken } = req.body;
    
    if (!refreshToken) {
        return res.status(401).json({ error: 'Refresh token required' });
    }
    
    try {
        const decoded = jwt.verify(refreshToken, JWT_CONFIG.secret);
        
        if (decoded.type !== 'refresh') {
            return res.status(401).json({ error: 'Invalid refresh token' });
        }
        
        // Check if refresh token is blacklisted
        const isBlacklisted = await isTokenBlacklisted(refreshToken);
        if (isBlacklisted) {
            return res.status(401).json({ error: 'Token revoked' });
        }
        
        // Generate new tokens
        const tokens = generateTokens(decoded.userId);
        
        // Blacklist old refresh token
        await blacklistToken(refreshToken);
        
        res.json(tokens);
    } catch (error) {
        return res.status(401).json({ error: 'Invalid refresh token' });
    }
});
```

### OAuth 2.0 & OpenID Connect
```javascript
const passport = require('passport');
const { Strategy: OAuth2Strategy } = require('passport-oauth2');
const { Strategy: GoogleStrategy } = require('passport-google-oauth20');

// OAuth 2.0 Strategy
passport.use(new OAuth2Strategy({
    authorizationURL: 'https://provider.com/oauth2/authorize',
    tokenURL: 'https://provider.com/oauth2/token',
    clientID: process.env.OAUTH_CLIENT_ID,
    clientSecret: process.env.OAUTH_CLIENT_SECRET,
    callbackURL: "/auth/oauth2/callback"
}, async (accessToken, refreshToken, profile, done) => {
    try {
        // Find or create user
        let user = await User.findOne({ oauthId: profile.id });
        
        if (!user) {
            user = await User.create({
                oauthId: profile.id,
                email: profile.email,
                name: profile.displayName
            });
        }
        
        return done(null, user);
    } catch (error) {
        return done(error);
    }
}));

// Google OAuth Strategy
passport.use(new GoogleStrategy({
    clientID: process.env.GOOGLE_CLIENT_ID,
    clientSecret: process.env.GOOGLE_CLIENT_SECRET,
    callbackURL: "/auth/google/callback",
    scope: ['profile', 'email']
}, async (accessToken, refreshToken, profile, done) => {
    try {
        const user = await User.findOrCreate({
            where: { googleId: profile.id },
            defaults: {
                email: profile.emails[0].value,
                name: profile.displayName
            }
        });
        
        return done(null, user);
    } catch (error) {
        return done(error);
    }
}));

// Routes
app.get('/auth/google', passport.authenticate('google'));
app.get('/auth/google/callback', 
    passport.authenticate('google', { session: false }),
    (req, res) => {
        // Generate JWT tokens
        const tokens = generateTokens(req.user.id);
        res.json(tokens);
    }
);
```

### Session-Based Authentication
```javascript
const session = require('express-session');
const RedisStore = require('connect-redis')(session);
const redis = require('redis');

const redisClient = redis.createClient({
    url: process.env.REDIS_URL
});

// Session Configuration
app.use(session({
    store: new RedisStore({ client: redisClient }),
    secret: process.env.SESSION_SECRET,
    resave: false,
    saveUninitialized: false,
    cookie: {
        secure: process.env.NODE_ENV === 'production',
        httpOnly: true,
        maxAge: 24 * 60 * 60 * 1000, // 24 hours
        sameSite: 'strict'
    },
    name: 'sessionId' // Don't use default 'connect.sid'
}));

// Session Authentication Middleware
const requireAuth = (req, res, next) => {
    if (!req.session.userId) {
        return res.status(401).json({ error: 'Authentication required' });
    }
    next();
};

// Login endpoint
app.post('/auth/login', async (req, res) => {
    const { email, password } = req.body;
    
    try {
        const user = await User.findOne({ email });
        
        if (!user || !(await bcrypt.compare(password, user.password))) {
            return res.status(401).json({ error: 'Invalid credentials' });
        }
        
        // Regenerate session to prevent fixation
        req.session.regenerate((err) => {
            if (err) {
                return res.status(500).json({ error: 'Session error' });
            }
            
            req.session.userId = user.id;
            req.session.userRole = user.role;
            
            res.json({ message: 'Login successful', user: user.getPublicProfile() });
        });
    } catch (error) {
        res.status(500).json({ error: 'Login failed' });
    }
});

// Logout endpoint
app.post('/auth/logout', (req, res) => {
    req.session.destroy((err) => {
        if (err) {
            return res.status(500).json({ error: 'Logout failed' });
        }
        
        res.clearCookie('sessionId');
        res.json({ message: 'Logout successful' });
    });
});
```

## Authorization Strategies

### Role-Based Access Control (RBAC)
```javascript
// RBAC Implementation
const ROLES = {
    ADMIN: 'admin',
    MODERATOR: 'moderator',
    USER: 'user',
    GUEST: 'guest'
};

const PERMISSIONS = {
    USER_READ: 'user:read',
    USER_WRITE: 'user:write',
    USER_DELETE: 'user:delete',
    ADMIN_READ: 'admin:read',
    ADMIN_WRITE: 'admin:write'
};

const ROLE_PERMISSIONS = {
    [ROLES.ADMIN]: [
        PERMISSIONS.USER_READ,
        PERMISSIONS.USER_WRITE,
        PERMISSIONS.USER_DELETE,
        PERMISSIONS.ADMIN_READ,
        PERMISSIONS.ADMIN_WRITE
    ],
    [ROLES.MODERATOR]: [
        PERMISSIONS.USER_READ,
        PERMISSIONS.USER_WRITE
    ],
    [ROLES.USER]: [
        PERMISSIONS.USER_READ
    ],
    [ROLES.GUEST]: []
};

// Authorization Middleware
const authorize = (requiredPermissions = []) => {
    return (req, res, next) => {
        if (!req.user) {
            return res.status(401).json({ error: 'Authentication required' });
        }
        
        const userPermissions = ROLE_PERMISSIONS[req.user.role] || [];
        
        const hasPermission = requiredPermissions.every(permission => 
            userPermissions.includes(permission)
        );
        
        if (!hasPermission) {
            return res.status(403).json({ error: 'Insufficient permissions' });
        }
        
        next();
    };
};

// Usage
app.get('/api/admin/users', 
    authenticateJWT,
    authorize([PERMISSIONS.ADMIN_READ]),
    userController.getUsers
);

app.delete('/api/users/:id',
    authenticateJWT,
    authorize([PERMISSIONS.USER_DELETE]),
    userController.deleteUser
);
```

### Attribute-Based Access Control (ABAC)
```javascript
// ABAC Implementation
class ABAC {
    constructor() {
        this.policies = [];
    }
    
    addPolicy(policy) {
        this.policies.push(policy);
    }
    
    async evaluate(request) {
        const { user, action, resource, context = {} } = request;
        
        for (const policy of this.policies) {
            const matches = await this.matchesPolicy(policy, user, action, resource, context);
            if (matches) {
                return policy.effect === 'allow';
            }
        }
        
        return false; // Default deny
    }
    
    async matchesPolicy(policy, user, action, resource, context) {
        // Check conditions
        for (const [key, condition] of Object.entries(policy.conditions)) {
            const value = this.getValue(key, { user, resource, context });
            if (!condition(value)) {
                return false;
            }
        }
        
        return true;
    }
    
    getValue(path, data) {
        return path.split('.').reduce((obj, key) => obj?.[key], data);
    }
}

// Policy Definitions
const abac = new ABAC();

// Policy: Users can only access their own data
abac.addPolicy({
    effect: 'allow',
    conditions: {
        'user.id': (value, { resource }) => value === resource.ownerId,
        'user.role': (value) => ['user', 'admin'].includes(value)
    }
});

// Policy: Admins can access any resource during business hours
abac.addPolicy({
    effect: 'allow',
    conditions: {
        'user.role': (value) => value === 'admin',
        'context.time': (value) => {
            const hour = new Date(value).getHours();
            return hour >= 9 && hour <= 17; // 9 AM - 5 PM
        }
    }
});

// ABAC Middleware
const authorizeABAC = (action, resourceType) => {
    return async (req, res, next) => {
        const resource = await getResource(req.params.id, resourceType);
        const context = {
            time: new Date(),
            ip: req.ip,
            userAgent: req.get('User-Agent')
        };
        
        const allowed = await abac.evaluate({
            user: req.user,
            action,
            resource,
            context
        });
        
        if (!allowed) {
            return res.status(403).json({ error: 'Access denied' });
        }
        
        next();
    };
};
```

### Resource-Based Authorization
```javascript
// Resource Ownership Check
const checkResourceOwnership = (resourceType, idParam = 'id') => {
    return async (req, res, next) => {
        const resourceId = req.params[idParam];
        const userId = req.user.id;
        
        try {
            const resource = await getResourceById(resourceType, resourceId);
            
            if (!resource) {
                return res.status(404).json({ error: 'Resource not found' });
            }
            
            // Check ownership
            if (resource.userId !== userId && req.user.role !== 'admin') {
                return res.status(403).json({ error: 'Access denied' });
            }
            
            req.resource = resource;
            next();
        } catch (error) {
            res.status(500).json({ error: 'Authorization check failed' });
        }
    };
};

// Usage
app.put('/api/posts/:id',
    authenticateJWT,
    checkResourceOwnership('posts'),
    postController.updatePost
);

// Advanced ownership with multiple ownership types
const checkMultiOwnership = (ownershipConfig) => {
    return async (req, res, next) => {
        const resourceId = req.params[ownershipConfig.idParam];
        const userId = req.user.id;
        
        try {
            const resource = await getResourceById(ownershipConfig.type, resourceId);
            
            if (!resource) {
                return res.status(404).json({ error: 'Resource not found' });
            }
            
            let isOwner = false;
            
            // Check different ownership fields
            for (const field of ownershipConfig.ownerFields) {
                if (resource[field] === userId) {
                    isOwner = true;
                    break;
                }
            }
            
            // Admin override
            const isAdmin = req.user.role === 'admin';
            
            if (!isOwner && !isAdmin) {
                return res.status(403).json({ error: 'Access denied' });
            }
            
            req.resource = resource;
            next();
        } catch (error) {
            res.status(500).json({ error: 'Authorization check failed' });
        }
    };
};
```

## Common Vulnerabilities

### OWASP Top 10 API Security Risks
```javascript
// 1. Broken Object Level Authorization (BOLA)
// Prevent by implementing proper resource ownership checks
app.get('/api/users/:userId/orders', 
    authenticateJWT,
    checkResourceOwnership('users', 'userId'), // Critical: Verify user can access this user's data
    orderController.getUserOrders
);

// 2. Broken Authentication
const secureAuthentication = {
    // Strong password requirements
    validatePassword: (password) => {
        const minLength = 8;
        const hasUpperCase = /[A-Z]/.test(password);
        const hasLowerCase = /[a-z]/.test(password);
        const hasNumbers = /\d/.test(password);
        const hasSpecialChar = /[!@#$%^&*(),.?":{}|<>]/.test(password);
        
        return password.length >= minLength && 
               hasUpperCase && 
               hasLowerCase && 
               hasNumbers && 
               hasSpecialChar;
    },
    
    // Account lockout
    handleFailedLogin: async (email) => {
        const key = `login_attempts:${email}`;
        const attempts = await redisClient.incr(key);
        
        if (attempts === 1) {
            await redisClient.expire(key, 900); // 15 minutes
        }
        
        if (attempts >= 5) {
            await redisClient.setex(`locked:${email}`, 1800, 'true'); // Lock for 30 minutes
            throw new Error('Account temporarily locked');
        }
    },
    
    // Session management
    enforceSessionSecurity: (req, res, next) => {
        // Regenerate session after login
        req.session.regenerate((err) => {
            if (err) return next(err);
            next();
        });
    }
};

// 3. Excessive Data Exposure
const sanitizeUserData = (user, fieldsToInclude = []) => {
    const publicFields = ['id', 'name', 'email', 'createdAt'];
    const allowedFields = fieldsToInclude.length > 0 ? fieldsToInclude : publicFields;
    
    const sanitized = {};
    allowedFields.forEach(field => {
        if (user[field] !== undefined) {
            sanitized[field] = user[field];
        }
    });
    
    return sanitized;
};

// 4. Lack of Resources & Rate Limiting
const rateLimit = require('express-rate-limit');

const createRateLimiter = (options = {}) => {
    return rateLimit({
        windowMs: options.windowMs || 15 * 60 * 1000, // 15 minutes
        max: options.max || 100, // limit each IP
        message: options.message || 'Too many requests',
        standardHeaders: true,
        legacyHeaders: false,
        keyGenerator: (req) => {
            // Use user ID if authenticated, otherwise IP
            return req.user ? req.user.id : req.ip;
        },
        handler: (req, res) => {
            res.status(429).json({
                error: 'Rate limit exceeded',
                retryAfter: Math.ceil(req.rateLimit.resetTime / 1000)
            });
        }
    });
};

// Different limiters for different endpoints
const authLimiter = createRateLimiter({
    windowMs: 15 * 60 * 1000,
    max: 5, // 5 login attempts per window
    message: 'Too many authentication attempts'
});

const apiLimiter = createRateLimiter({
    windowMs: 15 * 60 * 1000,
    max: 1000 // 1000 requests per window for general API
});

app.use('/auth/', authLimiter);
app.use('/api/', apiLimiter);
```

### SQL Injection Prevention
```javascript
// Using Parameterized Queries (with PostgreSQL example)
const { Pool } = require('pg');
const pool = new Pool();

// UNSAFE - Vulnerable to SQL injection
app.get('/api/users', async (req, res) => {
    const { search } = req.query;
    
    // VULNERABLE: Direct string concatenation
    const query = `SELECT * FROM users WHERE name LIKE '%${search}%'`;
    const result = await pool.query(query); // DON'T DO THIS!
    
    res.json(result.rows);
});

// SAFE - Parameterized queries
app.get('/api/users', async (req, res) => {
    const { search } = req.query;
    
    // SAFE: Parameterized query
    const query = 'SELECT * FROM users WHERE name LIKE $1';
    const values = [`%${search}%`];
    const result = await pool.query(query, values);
    
    res.json(result.rows);
});

// With Mongoose (NoSQL injection prevention)
app.get('/api/users', async (req, res) => {
    const { search, age } = req.query;
    
    // UNSAFE: Direct object assignment
    const filter = JSON.parse(search); // DON'T DO THIS!
    const users = await User.find(filter);
    
    // SAFE: Explicit field filtering
    const safeFilter = {};
    if (search) {
        safeFilter.name = { $regex: search, $options: 'i' };
    }
    if (age) {
        safeFilter.age = parseInt(age);
    }
    
    const users = await User.find(safeFilter);
    res.json(users);
});
```

### XSS Prevention
```javascript
const xss = require('xss');

// XSS Sanitization
const xssOptions = {
    whiteList: {}, // empty, means filter out all tags
    stripIgnoreTag: true, // filter out all HTML not in the whitelist
    stripIgnoreTagBody: ['script'] // filter out only script tags
};

const sanitizeInput = (input) => {
    if (typeof input === 'string') {
        return xss(input, xssOptions);
    }
    
    if (Array.isArray(input)) {
        return input.map(sanitizeInput);
    }
    
    if (typeof input === 'object' && input !== null) {
        const sanitized = {};
        for (const [key, value] of Object.entries(input)) {
            sanitized[key] = sanitizeInput(value);
        }
        return sanitized;
    }
    
    return input;
};

// XSS Protection Middleware
const xssProtection = (req, res, next) => {
    if (req.body) {
        req.body = sanitizeInput(req.body);
    }
    
    if (req.query) {
        req.query = sanitizeInput(req.query);
    }
    
    if (req.params) {
        req.params = sanitizeInput(req.params);
    }
    
    next();
};

app.use(express.json());
app.use(xssProtection); // Apply after body parsing

// Response headers for XSS protection
app.use((req, res, next) => {
    res.setHeader('X-XSS-Protection', '1; mode=block');
    res.setHeader('X-Content-Type-Options', 'nosniff');
    next();
});
```

## Security Headers

### Essential Security Headers
```javascript
const helmet = require('helmet');

// Basic Helmet configuration
app.use(helmet());

// Custom security headers configuration
app.use(helmet({
    contentSecurityPolicy: {
        directives: {
            defaultSrc: ["'self'"],
            styleSrc: ["'self'", "'unsafe-inline'", "https://fonts.googleapis.com"],
            scriptSrc: ["'self'", "'unsafe-inline'"],
            fontSrc: ["'self'", "https://fonts.gstatic.com"],
            imgSrc: ["'self'", "data:", "https:"],
            connectSrc: ["'self'"],
            frameSrc: ["'none'"],
            objectSrc: ["'none'"]
        }
    },
    hsts: {
        maxAge: 31536000,
        includeSubDomains: true,
        preload: true
    },
    noSniff: true,
    referrerPolicy: { policy: 'strict-origin-when-cross-origin' },
    frameguard: { action: 'deny' }
}));

// Additional custom headers
app.use((req, res, next) => {
    // Remove potentially dangerous headers
    res.removeHeader('X-Powered-By');
    
    // Security headers
    res.setHeader('X-Frame-Options', 'DENY');
    res.setHeader('X-Content-Type-Options', 'nosniff');
    res.setHeader('X-XSS-Protection', '1; mode=block');
    res.setHeader('Referrer-Policy', 'strict-origin-when-cross-origin');
    res.setHeader('Permissions-Policy', 'geolocation=(), microphone=(), camera=()');
    
    // Custom headers
    res.setHeader('X-API-Version', '1.0.0');
    res.setHeader('X-Content-Security', 'default-src: self');
    
    next();
});
```

### CORS Security
```javascript
const cors = require('cors');

// Production CORS configuration
const corsOptions = {
    origin: (origin, callback) => {
        const allowedOrigins = process.env.ALLOWED_ORIGINS?.split(',') || [];
        
        // Allow requests with no origin (like mobile apps or curl requests)
        if (!origin) return callback(null, true);
        
        if (allowedOrigins.indexOf(origin) === -1) {
            const msg = 'The CORS policy for this site does not allow access from the specified Origin.';
            return callback(new Error(msg), false);
        }
        
        return callback(null, true);
    },
    credentials: true,
    methods: ['GET', 'POST', 'PUT', 'PATCH', 'DELETE', 'OPTIONS'],
    allowedHeaders: [
        'Content-Type',
        'Authorization',
        'X-Requested-With',
        'X-API-Key',
        'X-Request-ID'
    ],
    exposedHeaders: [
        'X-RateLimit-Limit',
        'X-RateLimit-Remaining',
        'X-RateLimit-Reset'
    ],
    maxAge: 86400, // 24 hours
    preflightContinue: false,
    optionsSuccessStatus: 204
};

app.use(cors(corsOptions));

// Handle preflight requests
app.options('*', cors(corsOptions));
```

## Input Validation & Sanitization

### Request Validation
```javascript
const Joi = require('joi');
const validator = require('express-joi-validation').createValidator({});

// Validation schemas
const userValidation = {
    create: Joi.object({
        name: Joi.string().min(2).max(50).required(),
        email: Joi.string().email().required(),
        password: Joi.string().min(8).pattern(/^(?=.*[a-z])(?=.*[A-Z])(?=.*\d)(?=.*[@$!%*?&])[A-Za-z\d@$!%*?&]/).required(),
        role: Joi.string().valid('user', 'admin').default('user')
    }),
    
    update: Joi.object({
        name: Joi.string().min(2).max(50),
        email: Joi.string().email(),
        password: Joi.string().min(8).pattern(/^(?=.*[a-z])(?=.*[A-Z])(?=.*\d)(?=.*[@$!%*?&])[A-Za-z\d@$!%*?&]/)
    }).min(1), // At least one field required
    
    id: Joi.object({
        id: Joi.string().alphanum().length(24).required() // MongoDB ObjectId
    })
};

// Usage in routes
app.post('/api/users',
    validator.body(userValidation.create),
    userController.createUser
);

app.put('/api/users/:id',
    validator.params(userValidation.id),
    validator.body(userValidation.update),
    userController.updateUser
);

// Custom validation middleware
const validateRequest = (schema) => {
    return (req, res, next) => {
        const { error } = schema.validate(req.body, {
            abortEarly: false,
            allowUnknown: false,
            stripUnknown: true
        });
        
        if (error) {
            const errors = error.details.map(detail => ({
                field: detail.path.join('.'),
                message: detail.message,
                type: detail.type
            }));
            
            return res.status(422).json({
                error: 'Validation failed',
                details: errors
            });
        }
        
        next();
    };
};
```

### NoSQL Injection Prevention
```javascript
const mongoSanitize = require('express-mongo-sanitize');

// MongoDB injection protection
app.use(mongoSanitize({
    replaceWith: '_', // Replace prohibited characters with _
    onSanitize: ({ key, req }) => {
        console.warn(`Sanitized key: ${key} in request to ${req.path}`);
    }
}));

// Custom sanitization for query parameters
const sanitizeQuery = (query) => {
    const sanitized = {};
    
    for (const [key, value] of Object.entries(query)) {
        // Remove dollar signs from keys (MongoDB operators)
        const cleanKey = key.replace(/^\$/, '');
        
        if (typeof value === 'string') {
            // Basic string sanitization
            sanitized[cleanKey] = value.replace(/[${}]/g, '');
        } else if (typeof value === 'object' && value !== null) {
            // Recursively sanitize nested objects
            sanitized[cleanKey] = sanitizeQuery(value);
        } else {
            sanitized[cleanKey] = value;
        }
    }
    
    return sanitized;
};

// Query sanitization middleware
app.use((req, res, next) => {
    if (req.query) {
        req.query = sanitizeQuery(req.query);
    }
    next();
});
```

## Rate Limiting & Throttling

### Advanced Rate Limiting
```javascript
const rateLimit = require('express-rate-limit');
const RedisStore = require('rate-limit-redis');
const redis = require('redis');

const redisClient = redis.createClient({
    url: process.env.REDIS_URL
});

// Different rate limiters for different scenarios
const rateLimiters = {
    // Strict limiter for authentication endpoints
    auth: rateLimit({
        store: new RedisStore({
            sendCommand: (...args) => redisClient.sendCommand(args),
        }),
        windowMs: 15 * 60 * 1000, // 15 minutes
        max: 5, // 5 attempts per window
        message: 'Too many authentication attempts',
        standardHeaders: true,
        skipSuccessfulRequests: true, // Don't count successful logins
        keyGenerator: (req) => `auth:${req.ip}`,
        handler: (req, res) => {
            res.status(429).json({
                error: 'Too many authentication attempts',
                retryAfter: Math.ceil(req.rateLimit.resetTime / 1000)
            });
        }
    }),
    
    // API limiter with different tiers
    api: rateLimit({
        store: new RedisStore({
            sendCommand: (...args) => redisClient.sendCommand(args),
        }),
        windowMs: 15 * 60 * 1000, // 15 minutes
        max: (req) => {
            // Different limits based on user tier
            if (req.user?.tier === 'premium') return 1000;
            if (req.user?.tier === 'basic') return 100;
            return 50; // Free tier
        },
        message: 'Too many API requests',
        standardHeaders: true,
        keyGenerator: (req) => req.user ? `user:${req.user.id}` : `ip:${req.ip}`,
        handler: (req, res) => {
            res.status(429).json({
                error: 'Rate limit exceeded',
                limit: req.rateLimit.limit,
                remaining: req.rateLimit.remaining,
                reset: new Date(req.rateLimit.resetTime).toISOString()
            });
        }
    }),
    
    // Global limiter for DDoS protection
    global: rateLimit({
        store: new RedisStore({
            sendCommand: (...args) => redisClient.sendCommand(args),
        }),
        windowMs: 1 * 60 * 1000, // 1 minute
        max: 1000, // 1000 requests per minute per IP
        message: 'Too many requests',
        standardHeaders: true,
        keyGenerator: (req) => `global:${req.ip}`,
        skip: (req) => {
            // Skip for certain IPs (load balancers, etc.)
            const trustedIPs = process.env.TRUSTED_IPS?.split(',') || [];
            return trustedIPs.includes(req.ip);
        }
    })
};

// Apply rate limiters
app.use(rateLimiters.global);
app.use('/auth/', rateLimiters.auth);
app.use('/api/', rateLimiters.api);

// Dynamic rate limiting based on endpoint complexity
const dynamicRateLimit = (cost = 1) => {
    return rateLimit({
        store: new RedisStore({
            sendCommand: (...args) => redisClient.sendCommand(args),
        }),
        windowMs: 15 * 60 * 1000,
        max: (req) => {
            const baseLimit = req.user?.tier === 'premium' ? 1000 : 100;
            return Math.floor(baseLimit / cost);
        },
        keyGenerator: (req) => req.user ? `user:${req.user.id}` : `ip:${req.ip}`,
        handler: (req, res) => {
            res.status(429).json({
                error: `Rate limit exceeded for this endpoint`,
                cost: cost,
                limit: req.rateLimit.limit,
                remaining: req.rateLimit.remaining
            });
        }
    });
};

// Usage for expensive endpoints
app.get('/api/complex-query',
    dynamicRateLimit(5), // This endpoint costs 5x more
    complexQueryController
);
```

## Data Protection

### Data Encryption
```javascript
const crypto = require('crypto');

// Encryption configuration
const encryptionConfig = {
    algorithm: 'aes-256-gcm',
    key: process.env.ENCRYPTION_KEY, // 32 bytes for AES-256
    ivLength: 16, // 16 bytes for AES
    saltLength: 64, // 64 bytes for key derivation
    tagLength: 16, // 16 bytes for GCM
    keyIterations: 100000
};

// Encrypt sensitive data
const encrypt = (text) => {
    const iv = crypto.randomBytes(encryptionConfig.ivLength);
    const salt = crypto.randomBytes(encryptionConfig.saltLength);
    
    const key = crypto.pbkdf2Sync(
        encryptionConfig.key,
        salt,
        encryptionConfig.keyIterations,
        32,
        'sha512'
    );
    
    const cipher = crypto.createCipheriv(encryptionConfig.algorithm, key, iv);
    
    let encrypted = cipher.update(text, 'utf8', 'hex');
    encrypted += cipher.final('hex');
    
    const tag = cipher.getAuthTag();
    
    return {
        encrypted,
        iv: iv.toString('hex'),
        salt: salt.toString('hex'),
        tag: tag.toString('hex')
    };
};

// Decrypt data
const decrypt = (encryptedData) => {
    const { encrypted, iv, salt, tag } = encryptedData;
    
    const key = crypto.pbkdf2Sync(
        encryptionConfig.key,
        Buffer.from(salt, 'hex'),
        encryptionConfig.keyIterations,
        32,
        'sha512'
    );
    
    const decipher = crypto.createDecipheriv(
        encryptionConfig.algorithm,
        key,
        Buffer.from(iv, 'hex')
    );
    
    decipher.setAuthTag(Buffer.from(tag, 'hex'));
    
    let decrypted = decipher.update(encrypted, 'hex', 'utf8');
    decrypted += decipher.final('utf8');
    
    return decrypted;
};

// Hashing for sensitive data
const hashData = (data, salt = null) => {
    const usedSalt = salt || crypto.randomBytes(16).toString('hex');
    const hash = crypto.pbkdf2Sync(data, usedSalt, 100000, 64, 'sha512').toString('hex');
    
    return {
        hash,
        salt: usedSalt
    };
};

// Verify hash
const verifyHash = (data, hash, salt) => {
    const newHash = crypto.pbkdf2Sync(data, salt, 100000, 64, 'sha512').toString('hex');
    return newHash === hash;
};
```

### Secure Data Storage
```javascript
// Environment variable validation
const validateEnv = () => {
    const required = [
        'JWT_SECRET',
        'ENCRYPTION_KEY',
        'DATABASE_URL',
        'REDIS_URL'
    ];
    
    const missing = required.filter(key => !process.env[key]);
    
    if (missing.length > 0) {
        throw new Error(`Missing required environment variables: ${missing.join(', ')}`);
    }
    
    // Validate JWT secret strength
    if (process.env.JWT_SECRET.length < 32) {
        throw new Error('JWT_SECRET must be at least 32 characters long');
    }
};

// Secure configuration management
class SecureConfig {
    constructor() {
        this.secrets = new Map();
        this.initialized = false;
    }
    
    async init() {
        if (this.initialized) return;
        
        // Load secrets from secure storage (AWS Secrets Manager, HashiCorp Vault, etc.)
        await this.loadSecrets();
        this.initialized = true;
    }
    
    async loadSecrets() {
        // Example: Load from AWS Secrets Manager
        try {
            const secret = await this.getSecretFromAWS();
            this.secrets.set('database', secret.database_url);
            this.secrets.set('jwt', secret.jwt_secret);
            this.secrets.set('encryption', secret.encryption_key);
        } catch (error) {
            console.error('Failed to load secrets:', error);
            throw error;
        }
    }
    
    get(key) {
        if (!this.initialized) {
            throw new Error('SecureConfig not initialized');
        }
        
        const value = this.secrets.get(key);
        if (!value) {
            throw new Error(`Secret not found: ${key}`);
        }
        
        return value;
    }
}

// Data masking for logs
const maskSensitiveData = (data) => {
    const sensitiveFields = [
        'password',
        'token',
        'authorization',
        'credit_card',
        'ssn',
        'email'
    ];
    
    const masked = { ...data };
    
    sensitiveFields.forEach(field => {
        if (masked[field]) {
            masked[field] = '***MASKED***';
        }
    });
    
    return masked;
};

// Secure logging middleware
app.use((req, res, next) => {
    const safeReq = {
        ...req,
        body: maskSensitiveData(req.body),
        query: maskSensitiveData(req.query),
        headers: maskSensitiveData(req.headers)
    };
    
    console.log('Request:', safeReq);
    next();
});
```

## Security Testing

### Security Testing Suite
```javascript
// Security test utilities
const securityTests = {
    // SQL injection test
    testSQLInjection: async (endpoint, method = 'GET') => {
        const payloads = [
            "' OR '1'='1",
            "'; DROP TABLE users; --",
            "1' UNION SELECT 1,2,3 --"
        ];
        
        for (const payload of payloads) {
            const response = await request(app)
                [method.toLowerCase()](endpoint)
                .send({ input: payload });
            
            if (response.status !== 400 && response.status !== 422) {
                throw new Error(`SQL Injection vulnerability detected: ${payload}`);
            }
        }
    },
    
    // XSS test
    testXSS: async (endpoint, method = 'GET') => {
        const payloads = [
            "<script>alert('XSS')</script>",
            "<img src=x onerror=alert('XSS')>",
            "javascript:alert('XSS')"
        ];
        
        for (const payload of payloads) {
            const response = await request(app)
                [method.toLowerCase()](endpoint)
                .send({ input: payload });
            
            // Check if payload is sanitized in response
            if (response.text.includes(payload)) {
                throw new Error(`XSS vulnerability detected: ${payload}`);
            }
        }
    },
    
    // Authentication bypass test
    testAuthBypass: async (protectedEndpoint) => {
        // Test without authentication
        const response1 = await request(app).get(protectedEndpoint);
        if (response1.status !== 401) {
            throw new Error('Authentication bypass possible');
        }
        
        // Test with invalid token
        const response2 = await request(app)
            .get(protectedEndpoint)
            .set('Authorization', 'Bearer invalid-token');
        
        if (response2.status !== 401) {
            throw new Error('Invalid token accepted');
        }
    },
    
    // Rate limiting test
    testRateLimiting: async (endpoint, limit) => {
        const requests = [];
        
        // Make requests up to the limit
        for (let i = 0; i < limit + 5; i++) {
            requests.push(request(app).get(endpoint));
        }
        
        const responses = await Promise.all(requests);
        const rateLimited = responses.filter(r => r.status === 429);
        
        if (rateLimited.length === 0) {
            throw new Error('Rate limiting not working');
        }
    }
};

// Security headers test
const testSecurityHeaders = (res) => {
    const requiredHeaders = {
        'X-Content-Type-Options': 'nosniff',
        'X-Frame-Options': 'DENY',
        'X-XSS-Protection': '1; mode=block',
        'Strict-Transport-Security': expect.any(String)
    };
    
    for (const [header, expected] of Object.entries(requiredHeaders)) {
        expect(res.headers[header.toLowerCase()]).toEqual(expected);
    }
};
```

### Automated Security Scanning
```javascript
// Security scan middleware
const securityScanner = (req, res, next) => {
    const threats = [];
    
    // Check for SQL injection patterns
    const sqlPatterns = [
        /(\bUNION\b.*\bSELECT\b)/i,
        /(\bDROP\b.*\bTABLE\b)/i,
        /(\bINSERT\b.*\bINTO\b)/i,
        /('|\bOR\b.*'1'='1')/i
    ];
    
    // Check for XSS patterns
    const xssPatterns = [
        /<script\b[^<]*(?:(?!<\/script>)<[^<]*)*<\/script>/gi,
        /javascript:/gi,
        /on\w+\s*=/gi
    ];
    
    // Check request body
    const checkObject = (obj, path = '') => {
        for (const [key, value] of Object.entries(obj)) {
            const currentPath = path ? `${path}.${key}` : key;
            
            if (typeof value === 'string') {
                // Check for SQL injection
                sqlPatterns.forEach(pattern => {
                    if (pattern.test(value)) {
                        threats.push({
                            type: 'SQL_INJECTION',
                            path: currentPath,
                            value: value
                        });
                    }
                });
                
                // Check for XSS
                xssPatterns.forEach(pattern => {
                    if (pattern.test(value)) {
                        threats.push({
                            type: 'XSS',
                            path: currentPath,
                            value: value
                        });
                    }
                });
            } else if (typeof value === 'object' && value !== null) {
                checkObject(value, currentPath);
            }
        }
    };
    
    if (req.body) checkObject(req.body);
    if (req.query) checkObject(req.query);
    if (req.params) checkObject(req.params);
    
    // Log threats
    if (threats.length > 0) {
        console.warn('Security threats detected:', {
            ip: req.ip,
            path: req.path,
            threats: threats
        });
        
        // Block request if too many threats
        if (threats.length > 3) {
            return res.status(400).json({
                error: 'Request blocked due to security concerns'
            });
        }
    }
    
    next();
};

app.use(securityScanner);
```

## Monitoring & Logging

### Security Monitoring
```javascript
const winston = require('winston');

// Security-focused logger
const securityLogger = winston.createLogger({
    level: 'info',
    format: winston.format.combine(
        winston.format.timestamp(),
        winston.format.json()
    ),
    transports: [
        new winston.transports.File({ 
            filename: 'logs/security.log',
            level: 'warn'
        }),
        new winston.transports.Console({
            format: winston.format.simple()
        })
    ]
});

// Security event monitoring
class SecurityMonitor {
    static logAuthAttempt(email, success, context = {}) {
        securityLogger.warn('Authentication attempt', {
            event: 'auth_attempt',
            email: this.maskEmail(email),
            success,
            timestamp: new Date().toISOString(),
            ip: context.ip,
            userAgent: context.userAgent
        });
    }
    
    static logSuspiciousActivity(userId, activity, context = {}) {
        securityLogger.error('Suspicious activity detected', {
            event: 'suspicious_activity',
            userId,
            activity,
            timestamp: new Date().toISOString(),
            ip: context.ip,
            userAgent: context.userAgent,
            severity: 'high'
        });
    }
    
    static logRateLimitHit(key, endpoint) {
        securityLogger.warn('Rate limit hit', {
            event: 'rate_limit',
            key,
            endpoint,
            timestamp: new Date().toISOString()
        });
    }
    
    static maskEmail(email) {
        const [local, domain] = email.split('@');
        const maskedLocal = local.substring(0, 2) + '***' + local.substring(local.length - 1);
        return `${maskedLocal}@${domain}`;
    }
}

// Security monitoring middleware
const securityMonitor = (req, res, next) => {
    const startTime = Date.now();
    
    res.on('finish', () => {
        const duration = Date.now() - startTime;
        
        // Log security events
        if (res.statusCode === 401) {
            SecurityMonitor.logAuthAttempt(req.body?.email, false, {
                ip: req.ip,
                userAgent: req.get('User-Agent')
            });
        }
        
        if (res.statusCode === 429) {
            SecurityMonitor.logRateLimitHit(req.ip, req.path);
        }
        
        if (res.statusCode === 403) {
            SecurityMonitor.logSuspiciousActivity(
                req.user?.id, 
                'access_denied', 
                { ip: req.ip, path: req.path }
            );
        }
        
        // Log slow requests (potential DoS)
        if (duration > 5000) { // 5 seconds
            securityLogger.warn('Slow request detected', {
                event: 'slow_request',
                path: req.path,
                duration,
                ip: req.ip
            });
        }
    });
    
    next();
};

app.use(securityMonitor);
```

### Audit Logging
```javascript
// Comprehensive audit logging
class AuditLogger {
    static async logUserAction(userId, action, resource, details = {}) {
        const auditLog = {
            timestamp: new Date(),
            userId,
            action,
            resource,
            details,
            ip: details.ip,
            userAgent: details.userAgent
        };
        
        // Store in database
        await AuditLog.create(auditLog);
        
        // Also log to security log
        securityLogger.info('User action', auditLog);
    }
    
    static async logDataAccess(userId, resourceType, resourceId, action) {
        await this.logUserAction(userId, action, resourceType, {
            resourceId,
            sensitivity: 'high'
        });
    }
    
    static async logAdminAction(adminId, action, details = {}) {
        await this.logUserAction(adminId, action, 'system', {
            ...details,
            sensitivity: 'critical'
        });
    }
}

// Audit logging middleware
const auditLogger = (action, resourceType) => {
    return async (req, res, next) => {
        const originalSend = res.send;
        
        res.send = function(data) {
            // Log after response is sent
            if (res.statusCode < 400) { // Only log successful actions
                AuditLogger.logUserAction(
                    req.user?.id,
                    action,
                    resourceType,
                    {
                        ip: req.ip,
                        userAgent: req.get('User-Agent'),
                        resourceId: req.params.id,
                        method: req.method
                    }
                );
            }
            
            originalSend.call(this, data);
        };
        
        next();
    };
};

// Usage
app.put('/api/users/:id',
    authenticateJWT,
    authorize([PERMISSIONS.USER_WRITE]),
    auditLogger('update', 'user'),
    userController.updateUser
);
```

## Compliance & Standards

### GDPR Compliance
```javascript
// GDPR compliance utilities
const gdprCompliance = {
    // Right to be forgotten
    async deleteUserData(userId) {
        try {
            // Anonymize user data
            await User.findByIdAndUpdate(userId, {
                name: 'Deleted User',
                email: `deleted-${userId}@example.com`,
                phone: null,
                address: null,
                deletedAt: new Date()
            }, { new: true });
            
            // Delete sensitive data
            await SensitiveData.deleteMany({ userId });
            
            // Log deletion
            await AuditLogger.logAdminAction('system', 'gdpr_deletion', { userId });
            
            return true;
        } catch (error) {
            console.error('GDPR deletion failed:', error);
            return false;
        }
    },
    
    // Data portability
    async exportUserData(userId) {
        const user = await User.findById(userId).lean();
        const orders = await Order.find({ userId }).lean();
        const preferences = await UserPreference.find({ userId }).lean();
        
        // Remove internal fields
        delete user.password;
        delete user.__v;
        
        return {
            user,
            orders,
            preferences,
            exportedAt: new Date().toISOString()
        };
    },
    
    // Consent management
    async updateConsent(userId, consents) {
        await UserConsent.findOneAndUpdate(
            { userId },
            { 
                consents,
                updatedAt: new Date(),
                ip: req.ip // Track consent source
            },
            { upsert: true }
        );
        
        await AuditLogger.logUserAction(userId, 'consent_update', 'privacy', { consents });
    }
};

// GDPR compliance middleware
const gdprComplianceCheck = (req, res, next) => {
    // Check if user has given necessary consents
    if (requiresConsent(req.path)) {
        const hasConsent = checkUserConsent(req.user.id, 'necessary');
        
        if (!hasConsent) {
            return res.status(403).json({
                error: 'Consent required',
                consentUrl: '/consent'
            });
        }
    }
    
    next();
};
```

### PCI DSS Compliance
```javascript
// PCI DSS compliance for payment processing
const pciCompliance = {
    // Card data handling
    handleCardData: async (cardData) => {
        // Never store sensitive card data
        const { number, cvv, ...safeData } = cardData;
        
        // Tokenize with payment processor
        const token = await paymentProcessor.tokenize(cardData);
        
        // Store only token and safe data
        return {
            ...safeData,
            last4: number.slice(-4),
            token,
            tokenizedAt: new Date()
        };
    },
    
    // Secure payment processing
    processPayment: async (paymentData) => {
        // Validate payment data
        const validation = validatePaymentData(paymentData);
        if (!validation.isValid) {
            throw new Error(`Payment validation failed: ${validation.errors.join(', ')}`);
        }
        
        // Log payment attempt
        await AuditLogger.logUserAction(
            paymentData.userId,
            'payment_attempt',
            'payment',
            { amount: paymentData.amount, currency: paymentData.currency }
        );
        
        // Process with secure payment gateway
        const result = await paymentGateway.charge(paymentData);
        
        // Log successful payment
        await AuditLogger.logUserAction(
            paymentData.userId,
            'payment_success',
            'payment',
            { 
                amount: paymentData.amount,
                transactionId: result.transactionId,
                maskedCard: `****${paymentData.cardLast4}`
            }
        );
        
        return result;
    },
    
    // Regular security assessments
    async runSecurityAssessment() {
        const assessment = {
            timestamp: new Date(),
            vulnerabilities: [],
            compliance: {}
        };
        
        // Check for known vulnerabilities
        assessment.vulnerabilities = await vulnerabilityScanner.scan();
        
        // Verify encryption
        assessment.compliance.encryption = await this.verifyEncryption();
        
        // Check access controls
        assessment.compliance.accessControls = await this.verifyAccessControls();
        
        return assessment;
    }
};
```

## Best Practices Checklist

### Security Checklist
```javascript
// Security configuration validator
class SecurityValidator {
    static validateConfig() {
        const errors = [];
        
        // Environment variables
        if (!process.env.JWT_SECRET) errors.push('JWT_SECRET not set');
        if (!process.env.ENCRYPTION_KEY) errors.push('ENCRYPTION_KEY not set');
        if (process.env.JWT_SECRET?.length < 32) errors.push('JWT_SECRET too short');
        
        // Database security
        if (process.env.DATABASE_URL?.includes('localhost')) {
            console.warn('Warning: Using local database in production');
        }
        
        // HTTPS enforcement
        if (process.env.NODE_ENV === 'production' && !process.env.FORCE_HTTPS) {
            errors.push('FORCE_HTTPS should be enabled in production');
        }
        
        return errors;
    }
    
    static validateHeaders(headers) {
        const missing = [];
        const required = [
            'X-Content-Type-Options',
            'X-Frame-Options', 
            'X-XSS-Protection',
            'Strict-Transport-Security'
        ];
        
        required.forEach(header => {
            if (!headers[header.toLowerCase()]) {
                missing.push(header);
            }
        });
        
        return missing;
    }
}

// Security health check endpoint
app.get('/security/health', async (req, res) => {
    const health = {
        status: 'healthy',
        checks: {},
        timestamp: new Date().toISOString()
    };
    
    // Config validation
    const configErrors = SecurityValidator.validateConfig();
    health.checks.config = {
        status: configErrors.length === 0 ? 'healthy' : 'unhealthy',
        errors: configErrors
    };
    
    // Headers check
    const testRes = { headers: {} };
    SecurityValidator.validateHeaders(testRes.headers);
    health.checks.headers = {
        status: 'unknown', // Would need actual response test
        message: 'Run actual request to test headers'
    };
    
    // Database security
    health.checks.database = {
        status: 'healthy',
        ssl: process.env.DATABASE_URL?.includes('ssl=true') ? 'enabled' : 'disabled'
    };
    
    // Encryption
    health.checks.encryption = {
        status: 'healthy',
        algorithm: 'AES-256-GCM'
    };
    
    // Update overall status
    const unhealthyChecks = Object.values(health.checks).filter(
        check => check.status === 'unhealthy'
    );
    
    if (unhealthyChecks.length > 0) {
        health.status = 'unhealthy';
    }
    
    res.json(health);
});

// Automated security scan
app.post('/security/scan', authenticateJWT, authorize(['admin:write']), async (req, res) => {
    try {
        const scanResults = {
            timestamp: new Date().toISOString(),
            initiatedBy: req.user.id,
            results: {}
        };
        
        // Run vulnerability scans
        scanResults.results.sqlInjection = await securityTests.testSQLInjection('/api/users');
        scanResults.results.xss = await securityTests.testXSS('/api/search');
        scanResults.results.auth = await securityTests.testAuthBypass('/api/admin');
        
        // Save scan results
        await SecurityScan.create(scanResults);
        
        res.json(scanResults);
    } catch (error) {
        res.status(500).json({ error: 'Security scan failed', details: error.message });
    }
});
```

### Quick Security Reference
```javascript
// Essential security middleware stack
app.use(helmet()); // Security headers
app.use(cors(corsOptions)); // CORS configuration
app.use(compression()); // Response compression
app.use(rateLimit(rateLimitOptions)); // Rate limiting
app.use(express.json({ limit: '10mb' })); // Body parsing with limits
app.use(express.urlencoded({ extended: true, limit: '10mb' }));
app.use(cookieParser()); // Cookie parsing
app.use(mongoSanitize()); // NoSQL injection protection
app.use(xss()); // XSS protection
app.use(hpp()); // Parameter pollution protection
app.use(securityScanner); // Custom security scanning
app.use(securityMonitor); // Security monitoring

// Authentication & Authorization
app.use('/api/', authenticateJWT); // JWT authentication
app.use('/api/admin/', authorize([PERMISSIONS.ADMIN_READ])); // Role-based access

// Input validation
app.use(validator); // Request validation
app.use(xssProtection); // XSS sanitization

// Audit logging
app.use(auditLogger('access', 'api')); // Audit trail
```

### Pro Tips
1. **Never trust user input** - Validate and sanitize everything
2. **Use parameterized queries** - Prevent SQL injection
3. **Implement proper authentication** - Multi-factor when possible
4. **Follow principle of least privilege** - Minimum access required
5. **Encrypt sensitive data** - Both at rest and in transit
6. **Use HTTPS everywhere** - No exceptions
7. **Implement proper session management** - Secure cookies, session expiration
8. **Regular security testing** - Automated scans and penetration testing
9. **Keep dependencies updated** - Regular security patches
10. **Monitor and log everything** - Detect and respond to threats quickly

---

*Remember: API security is not a one-time setup but an ongoing process. Regular security assessments, monitoring, and updates are essential to maintain a secure API environment. Always stay informed about new vulnerabilities and security best practices!*