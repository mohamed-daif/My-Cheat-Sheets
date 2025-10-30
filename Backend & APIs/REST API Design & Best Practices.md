# REST API Design & Best Practices Complete Cheat Sheet

## Table of Contents
- [REST Fundamentals](#rest-fundamentals)
- [API Design Principles](#api-design-principles)
- [HTTP Methods & Status Codes](#http-methods--status-codes)
- [Resource Naming & Structure](#resource-naming--structure)
- [Request & Response Design](#request--response-design)
- [Authentication & Security](#authentication--security)
- [Versioning Strategies](#versioning-strategies)
- [Pagination & Filtering](#pagination--filtering)
- [Error Handling](#error-handling)
- [Documentation](#documentation)
- [Performance & Caching](#performance--caching)
- [Testing & Monitoring](#testing--monitoring)
- [API Evolution](#api-evolution)
- [Best Practices Summary](#best-practices-summary)

## REST Fundamentals

### What is REST?
- **RE**presentational **S**tate **T**ransfer
- Architectural style for distributed systems
- Stateless client-server communication
- Uniform interface for resource manipulation

### REST Constraints
```text
1. Client-Server Architecture
2. Statelessness - No client context stored on server
3. Cacheability - Responses must define cacheability
4. Uniform Interface
5. Layered System
6. Code on Demand (optional)
```

### Richardson Maturity Model
```text
Level 0: The Swamp of POX (Plain Old XML)
Level 1: Resources - Separate URI for each resource
Level 2: HTTP Verbs - Proper use of GET, POST, PUT, DELETE
Level 3: Hypermedia Controls (HATEOAS)
```

## API Design Principles

### Key Design Principles
```text
1. Resource-Oriented - Focus on nouns, not verbs
2. Stateless - Each request contains all necessary information
3. Consistent - Uniform patterns across the API
4. Simple - Intuitive and easy to use
5. Discoverable - Self-descriptive and well-documented
```

### RESTful Maturity Levels
```python
# Level 2 - HTTP Verbs (Most Common)
GET    /users          # List users
POST   /users          # Create user
GET    /users/123      # Get user
PUT    /users/123      # Update user
DELETE /users/123      # Delete user

# Level 3 - Hypermedia (HATEOAS)
{
  "id": 123,
  "name": "John Doe",
  "links": [
    { "rel": "self", "href": "/users/123" },
    { "rel": "orders", "href": "/users/123/orders" }
  ]
}
```

## HTTP Methods & Status Codes

### HTTP Methods (Verbs)
```http
GET     - Retrieve resource(s) - Safe, Idempotent
POST    - Create resource     - Not Safe, Not Idempotent
PUT     - Update/replace      - Not Safe, Idempotent
PATCH   - Partial update      - Not Safe, Not Idempotent
DELETE  - Delete resource     - Not Safe, Idempotent
HEAD    - Headers only        - Safe, Idempotent
OPTIONS - Available methods   - Safe, Idempotent
```

### HTTP Status Codes
```http
# 2xx - Success
200 OK                    # Generic success
201 Created               # Resource created
202 Accepted              # Request accepted, processing
204 No Content            # Success, no response body

# 3xx - Redirection
301 Moved Permanently     # Resource moved permanently
304 Not Modified          # Cached version is valid

# 4xx - Client Errors
400 Bad Request           # Malformed request
401 Unauthorized          # Authentication required
403 Forbidden             # Authenticated but no permission
404 Not Found             # Resource doesn't exist
405 Method Not Allowed    # HTTP method not supported
409 Conflict              # Resource conflict
422 Unprocessable Entity  # Validation errors
429 Too Many Requests     # Rate limiting

# 5xx - Server Errors
500 Internal Server Error # Generic server error
502 Bad Gateway           # Upstream server error
503 Service Unavailable   # Server temporarily unavailable
504 Gateway Timeout       # Upstream server timeout
```

### Proper Status Code Usage
```python
# Successful responses
GET /users/123           -> 200 OK
POST /users              -> 201 Created
PUT /users/123           -> 200 OK or 204 No Content
DELETE /users/123        -> 204 No Content

# Client errors
GET /users/999           -> 404 Not Found
POST /users (invalid)    -> 422 Unprocessable Entity
DELETE /users/999        -> 404 Not Found

# Authentication/Authorization
GET /users (no auth)     -> 401 Unauthorized
GET /admin/users         -> 403 Forbidden
```

## Resource Naming & Structure

### Resource Naming Conventions
```text
Use nouns, not verbs
Use plural form for collections
Use lowercase with hyphens
Be consistent across the API
```

### Resource URL Patterns
```http
# Collections
GET    /users                    # List users
POST   /users                    # Create user

# Individual resources
GET    /users/{id}              # Get user
PUT    /users/{id}              # Update user
DELETE /users/{id}              # Delete user

# Sub-resources
GET    /users/{id}/orders       # Get user's orders
POST   /users/{id}/orders       # Create order for user
GET    /users/{id}/orders/{oid} # Get specific order

# Actions (when no better resource exists)
POST   /users/{id}/activate     # Activate user
POST   /users/{id}/deactivate   # Deactivate user

# Query parameters for filtering
GET    /users?active=true&role=admin
GET    /users?page=2&limit=50
```

### Common Resource Patterns
```python
# Good examples
/users
/users/123
/users/123/orders
/orders/456/items
/products?category=electronics&price_range=100-500

# Avoid these anti-patterns
/getAllUsers                  # Use GET /users
/createUser                   # Use POST /users
/updateUser/123               # Use PUT /users/123
/deleteUser/123               # Use DELETE /users/123
/users/getUserOrders/123      # Use GET /users/123/orders
```

## Request & Response Design

### Request Headers
```http
# Common request headers
Content-Type: application/json
Accept: application/json
Authorization: Bearer <token>
User-Agent: MyApp/1.0.0
X-Request-ID: unique-request-id
If-None-Match: "version-hash"
If-Modified-Since: Thu, 01 Jan 2024 00:00:00 GMT
```

### Response Headers
```http
# Common response headers
Content-Type: application/json
Content-Length: 1024
ETag: "version-hash"
Last-Modified: Thu, 01 Jan 2024 00:00:00 GMT
Cache-Control: max-age=3600
X-RateLimit-Limit: 1000
X-RateLimit-Remaining: 999
X-RateLimit-Reset: 1640995200
```

### Request Body Examples
```json
// POST /users - Create user
{
  "name": "John Doe",
  "email": "john@example.com",
  "role": "user"
}

// PUT /users/123 - Update user
{
  "name": "John Smith",
  "email": "johnsmith@example.com"
}

// PATCH /users/123 - Partial update
{
  "email": "newemail@example.com"
}
```

### Response Body Examples
```json
// GET /users/123 - Single resource
{
  "id": 123,
  "name": "John Doe",
  "email": "john@example.com",
  "created_at": "2024-01-15T10:30:00Z",
  "updated_at": "2024-01-15T10:30:00Z"
}

// GET /users - Collection
{
  "data": [
    {
      "id": 123,
      "name": "John Doe",
      "email": "john@example.com"
    },
    {
      "id": 124,
      "name": "Jane Smith",
      "email": "jane@example.com"
    }
  ],
  "pagination": {
    "page": 1,
    "limit": 20,
    "total": 150,
    "pages": 8
  },
  "links": {
    "self": "/users?page=1&limit=20",
    "next": "/users?page=2&limit=20",
    "last": "/users?page=8&limit=20"
  }
}

// POST /users - Created resource
{
  "id": 125,
  "name": "Bob Wilson",
  "email": "bob@example.com",
  "created_at": "2024-01-15T11:00:00Z",
  "links": {
    "self": "/users/125"
  }
}
```

### HATEOAS (Hypermedia as the Engine of Application State)
```json
{
  "id": 123,
  "name": "John Doe",
  "email": "john@example.com",
  "status": "active",
  "_links": {
    "self": { "href": "/users/123" },
    "orders": { "href": "/users/123/orders" },
    "activate": { 
      "href": "/users/123/activate",
      "method": "POST"
    },
    "deactivate": { 
      "href": "/users/123/deactivate", 
      "method": "POST" 
    }
  }
}
```

## Authentication & Security

### Authentication Methods
```python
# 1. API Keys (Simple but less secure)
Authorization: Api-Key abc123def456

# 2. Bearer Tokens (JWT)
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...

# 3. OAuth 2.0
Authorization: Bearer <access_token>

# 4. Basic Auth (Only over HTTPS)
Authorization: Basic base64(username:password)
```

### Security Best Practices
```python
# Always use HTTPS
# Implement rate limiting
# Validate and sanitize all inputs
# Use proper authentication and authorization
# Implement CORS properly
# Sanitize error messages
# Use security headers
```

### Rate Limiting Headers
```http
# Rate limiting response headers
X-RateLimit-Limit: 1000
X-RateLimit-Remaining: 999
X-RateLimit-Reset: 1640995200
Retry-After: 3600  # When rate limited
```

### CORS Configuration
```python
# Express.js CORS example
app.use(cors({
  origin: ['https://example.com', 'https://app.example.com'],
  methods: ['GET', 'POST', 'PUT', 'DELETE', 'PATCH'],
  allowedHeaders: ['Content-Type', 'Authorization', 'X-Request-ID'],
  exposedHeaders: ['X-RateLimit-Limit', 'X-RateLimit-Remaining'],
  maxAge: 86400,  # 24 hours
  credentials: true  # If using cookies
}))
```

## Versioning Strategies

### Versioning Methods
```python
# 1. URL Path Versioning (Most Common)
/api/v1/users
/api/v2/users

# 2. Query Parameter Versioning
/api/users?version=1
/api/users?v=2

# 3. Header Versioning
Accept: application/vnd.myapi.v1+json
Accept: application/vnd.myapi.v2+json

# 4. Custom Header Versioning
X-API-Version: 1
X-API-Version: 2
```

### Implementation Examples
```python
# URL Path Versioning (Recommended)
@app.route('/api/v1/users')
def get_users_v1():
    return {"users": [], "version": "v1"}

@app.route('/api/v2/users')  
def get_users_v2():
    return {"data": [], "meta": {"version": "v2"}}

# Content Negotiation
@app.route('/api/users')
def get_users():
    version = request.headers.get('Accept', '').split('.v')[-1].split('+')[0]
    if version == '2':
        return v2_response()
    return v1_response()
```

### Versioning Best Practices
```text
1. Version in URL for simplicity and discoverability
2. Maintain backward compatibility when possible
3. Provide clear migration guides
4. Sunset old versions with proper notice
5. Use semantic versioning principles
```

## Pagination & Filtering

### Pagination Strategies
```python
# 1. Offset-Based Pagination
GET /users?page=2&limit=20

# Response
{
  "data": [...],
  "pagination": {
    "page": 2,
    "limit": 20,
    "total": 150,
    "pages": 8
  },
  "links": {
    "first": "/users?page=1&limit=20",
    "prev": "/users?page=1&limit=20", 
    "next": "/users?page=3&limit=20",
    "last": "/users?page=8&limit=20"
  }
}

# 2. Cursor-Based Pagination (Better performance)
GET /users?limit=20&after=cursor123

# Response  
{
  "data": [...],
  "pagination": {
    "limit": 20,
    "has_more": true,
    "total": null  # Often omitted for performance
  },
  "links": {
    "next": "/users?limit=20&after=cursor456"
  }
}
```

### Filtering, Sorting & Searching
```python
# Filtering
GET /users?role=admin&status=active
GET /products?price_min=100&price_max=500
GET /orders?created_after=2024-01-01

# Sorting
GET /users?sort=name&order=asc
GET /users?sort=-created_at  # Descending
GET /users?sort=name,-created_at  # Multiple fields

# Searching
GET /users?q=john
GET /products?search=phone&category=electronics

# Field selection
GET /users?fields=id,name,email
```

### Implementation Example
```python
class UserListAPI(Resource):
    def get(self):
        # Pagination
        page = request.args.get('page', 1, type=int)
        limit = request.args.get('limit', 20, type=int)
        offset = (page - 1) * limit
        
        # Filtering
        query = User.query
        if role := request.args.get('role'):
            query = query.filter_by(role=role)
        if status := request.args.get('status'):
            query = query.filter_by(status=status)
            
        # Sorting
        sort_by = request.args.get('sort', 'id')
        if sort_by.startswith('-'):
            order_by = getattr(User, sort_by[1:]).desc()
        else:
            order_by = getattr(User, sort_by).asc()
        query = query.order_by(order_by)
        
        # Execute query
        users = query.offset(offset).limit(limit).all()
        total = query.count()
        
        return {
            'data': [user.to_dict() for user in users],
            'pagination': {
                'page': page,
                'limit': limit,
                'total': total,
                'pages': math.ceil(total / limit)
            }
        }
```

## Error Handling

### Standard Error Response Format
```json
{
  "error": {
    "code": "validation_error",
    "message": "The request contains validation errors",
    "details": [
      {
        "field": "email",
        "message": "Must be a valid email address",
        "code": "invalid_email"
      },
      {
        "field": "password", 
        "message": "Must be at least 8 characters",
        "code": "password_too_short"
      }
    ]
  },
  "request_id": "req_123456789",
  "timestamp": "2024-01-15T12:00:00Z"
}
```

### Common Error Types
```python
# Validation Errors (422 Unprocessable Entity)
{
  "error": {
    "code": "validation_failed",
    "message": "Request validation failed",
    "details": [...]
  }
}

# Authentication Errors (401 Unauthorized)
{
  "error": {
    "code": "unauthorized",
    "message": "Authentication required"
  }
}

# Authorization Errors (403 Forbidden)  
{
  "error": {
    "code": "forbidden",
    "message": "Insufficient permissions"
  }
}

# Not Found (404 Not Found)
{
  "error": {
    "code": "not_found",
    "message": "User not found"
  }
}

# Rate Limited (429 Too Many Requests)
{
  "error": {
    "code": "rate_limited",
    "message": "Too many requests",
    "retry_after": 3600
  }
}
```

### Error Handling Implementation
```python
from flask import jsonify
from werkzeug.exceptions import HTTPException

class APIError(Exception):
    def __init__(self, message, status_code=400, error_code=None, details=None):
        self.message = message
        self.status_code = status_code
        self.error_code = error_code
        self.details = details

@app.errorhandler(APIError)
def handle_api_error(error):
    response = jsonify({
        'error': {
            'code': error.error_code or 'api_error',
            'message': error.message,
            'details': error.details
        }
    })
    response.status_code = error.status_code
    return response

@app.errorhandler(404)
def not_found(error):
    return jsonify({
        'error': {
            'code': 'not_found',
            'message': 'The requested resource was not found'
        }
    }), 404

@app.errorhandler(500)
def internal_error(error):
    return jsonify({
        'error': {
            'code': 'internal_error', 
            'message': 'An internal server error occurred'
        }
    }), 500

# Usage
def get_user(user_id):
    user = User.query.get(user_id)
    if not user:
        raise APIError('User not found', 404, 'user_not_found')
    return user
```

## Documentation

### OpenAPI/Swagger Specification
```yaml
# openapi.yaml
openapi: 3.0.0
info:
  title: User API
  description: User management API
  version: 1.0.0
  contact:
    name: API Support
    email: support@example.com

servers:
  - url: https://api.example.com/v1
    description: Production server

paths:
  /users:
    get:
      summary: List users
      description: Retrieve a paginated list of users
      parameters:
        - in: query
          name: page
          schema:
            type: integer
            minimum: 1
            default: 1
          description: Page number
        - in: query  
          name: limit
          schema:
            type: integer
            minimum: 1
            maximum: 100
            default: 20
          description: Number of users per page
      responses:
        '200':
          description: Successful response
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/UserList'
        '400':
          description: Bad request
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/Error'

components:
  schemas:
    User:
      type: object
      properties:
        id:
          type: integer
          format: int64
        name:
          type: string
        email:
          type: string
          format: email
    UserList:
      type: object
      properties:
        data:
          type: array
          items:
            $ref: '#/components/schemas/User'
        pagination:
          $ref: '#/components/schemas/Pagination'
    Error:
      type: object
      properties:
        error:
          type: object
          properties:
            code:
              type: string
            message:
              type: string
```

### API Documentation Best Practices
```text
1. Use OpenAPI/Swagger for machine-readable docs
2. Provide interactive documentation (Swagger UI)
3. Include code examples for multiple languages
4. Document authentication methods
5. Provide error code explanations
6. Include rate limiting information
7. Provide SDKs/libraries when possible
8. Keep documentation updated with API changes
```

## Performance & Caching

### Caching Strategies
```python
# HTTP Caching Headers
@app.route('/users/<int:user_id>')
def get_user(user_id):
    user = User.query.get_or_404(user_id)
    
    response = jsonify(user.to_dict())
    
    # Set cache headers
    response.headers['Cache-Control'] = 'public, max-age=300'  # 5 minutes
    response.headers['ETag'] = generate_etag(user)
    response.headers['Last-Modified'] = user.updated_at
    
    return response

# Conditional Requests
@app.route('/users/<int:user_id>')
def get_user_conditional(user_id):
    user = User.query.get_or_404(user_id)
    
    # Check If-None-Match
    if_none_match = request.headers.get('If-None-Match')
    if if_none_match == generate_etag(user):
        return '', 304  # Not Modified
    
    # Check If-Modified-Since  
    if_modified_since = request.headers.get('If-Modified-Since')
    if if_modified_since and user.updated_at <= parse_date(if_modified_since):
        return '', 304
    
    return jsonify(user.to_dict())
```

### Performance Optimization
```python
# Field selection to reduce payload
@app.route('/users')
def get_users():
    fields = request.args.get('fields', '').split(',')
    users = User.query.all()
    
    # Only include requested fields
    data = []
    for user in users:
        user_data = {}
        for field in fields:
            if hasattr(user, field):
                user_data[field] = getattr(user, field)
        data.append(user_data)
    
    return jsonify({'data': data})

# Database optimization with pagination
@app.route('/users')
def get_users_optimized():
    page = request.args.get('page', 1, type=int)
    limit = request.args.get('limit', 20, type=int)
    
    # Use database-level pagination
    users = User.query.paginate(
        page=page, 
        per_page=limit,
        error_out=False
    )
    
    return jsonify({
        'data': [user.to_dict() for user in users.items],
        'pagination': {
            'page': page,
            'limit': limit,
            'total': users.total,
            'pages': users.pages
        }
    })
```

### Compression
```python
# Enable gzip compression
@app.after_request
def compress_response(response):
    response.direct_passthrough = False
    
    if (response.status_code < 200 or 
        response.status_code >= 300 or 
        'Content-Encoding' in response.headers):
        return response
    
    accept_encoding = request.headers.get('Accept-Encoding', '')
    
    if 'gzip' in accept_encoding.lower():
        compressed = gzip.compress(response.get_data())
        response.set_data(compressed)
        response.headers['Content-Encoding'] = 'gzip'
        response.headers['Content-Length'] = len(compressed)
    
    return response
```

## Testing & Monitoring

### API Testing Strategy
```python
# pytest tests for REST API
import pytest
import json

class TestUserAPI:
    def test_get_users(self, client):
        response = client.get('/api/v1/users')
        assert response.status_code == 200
        
        data = json.loads(response.data)
        assert 'data' in data
        assert 'pagination' in data
    
    def test_create_user(self, client):
        user_data = {
            'name': 'Test User',
            'email': 'test@example.com'
        }
        
        response = client.post(
            '/api/v1/users',
            data=json.dumps(user_data),
            content_type='application/json'
        )
        
        assert response.status_code == 201
        data = json.loads(response.data)
        assert data['name'] == user_data['name']
    
    def test_invalid_user_creation(self, client):
        user_data = {
            'name': 'Test User'
            # Missing required email field
        }
        
        response = client.post(
            '/api/v1/users', 
            data=json.dumps(user_data),
            content_type='application/json'
        )
        
        assert response.status_code == 422
        data = json.loads(response.data)
        assert 'error' in data
```

### Monitoring & Metrics
```python
# Request logging middleware
@app.before_request
def log_request_info():
    request.start_time = time.time()
    request.id = str(uuid.uuid4())
    
    logger.info('Request started', extra={
        'request_id': request.id,
        'method': request.method,
        'path': request.path,
        'user_agent': request.headers.get('User-Agent')
    })

@app.after_request
def log_response_info(response):
    if hasattr(request, 'start_time'):
        duration = time.time() - request.start_time
        
        logger.info('Request completed', extra={
            'request_id': getattr(request, 'id', 'unknown'),
            'method': request.method,
            'path': request.path,
            'status_code': response.status_code,
            'duration': duration,
            'response_size': response.content_length
        })
    
    return response

# Health check endpoint
@app.route('/health')
def health_check():
    # Check database connection
    try:
        db.session.execute('SELECT 1')
        db_status = 'healthy'
    except Exception:
        db_status = 'unhealthy'
    
    # Check external dependencies
    redis_status = 'healthy' if redis.ping() else 'unhealthy'
    
    overall_status = 'healthy' if all([
        db_status == 'healthy',
        redis_status == 'healthy'
    ]) else 'unhealthy'
    
    status_code = 200 if overall_status == 'healthy' else 503
    
    return jsonify({
        'status': overall_status,
        'timestamp': datetime.utcnow().isoformat(),
        'checks': {
            'database': db_status,
            'redis': redis_status
        }
    }), status_code
```

### API Analytics
```python
# Track API usage
@app.before_request
def track_usage():
    if request.endpoint and request.endpoint != 'static':
        endpoint = request.endpoint
        method = request.method
        user_id = getattr(g, 'user_id', None)
        
        # Log to analytics service
        analytics.track(
            user_id=user_id,
            event='api_request',
            properties={
                'endpoint': endpoint,
                'method': method,
                'path': request.path,
                'user_agent': request.headers.get('User-Agent')
            }
        )
```

## API Evolution

### Breaking vs Non-Breaking Changes
```python
# Non-breaking changes (safe)
- Adding new endpoints
- Adding optional request parameters
- Adding new response fields
- Adding new error codes

# Breaking changes (require versioning)
- Removing or renaming endpoints
- Removing or renaming response fields
- Changing data types
- Changing required parameters
- Changing authentication methods
```

### Deprecation Strategy
```python
# Deprecation headers
@app.route('/api/v1/old-endpoint')
def old_endpoint():
    response = jsonify({...})
    
    # Add deprecation warning
    response.headers['Deprecation'] = 'true'
    response.headers['Sunset'] = 'Mon, 01 Jul 2024 00:00:00 GMT'
    response.headers['Link'] = '</api/v2/new-endpoint>; rel="successor-version"'
    
    return response

# Usage in client
def make_request(url):
    response = requests.get(url)
    
    if 'Deprecation' in response.headers:
        warning = response.headers.get('Warning', '')
        sunset = response.headers.get('Sunset', '')
        
        logger.warning(f'API endpoint deprecated: {warning}. Sunset: {sunset}')
    
    return response
```

### Migration Guide Example
```markdown
# API Migration Guide v1 to v2

## Changes
- `GET /users` now returns `data` array instead of direct array
- `POST /users` requires `role` field
- All dates now use ISO 8601 format

## Timeline
- Jan 2024: v2 released, v1 still supported
- Jul 2024: v1 deprecated, sunset headers added  
- Jan 2025: v1 retired, returns 410 Gone

## Migration Steps
1. Update base URL to `/v2/`
2. Handle new response format
3. Add required fields to requests
4. Update date parsing logic
```

## Best Practices Summary

### API Design Checklist
```text
✅ Use nouns for resources, not verbs
✅ Use consistent naming conventions
✅ Proper HTTP methods and status codes
✅ Version your API from the start
✅ Implement pagination for collections
✅ Provide filtering, sorting, searching
✅ Consistent error response format
✅ Comprehensive documentation
✅ Proper authentication and authorization
✅ Rate limiting and throttling
✅ Request/response logging
✅ Health check endpoints
✅ Caching headers where appropriate
✅ CORS configuration
✅ Input validation and sanitization
```

### Security Checklist
```text
✅ Use HTTPS everywhere
✅ Validate and sanitize all inputs
✅ Implement proper authentication
✅ Use principle of least privilege
✅ Rate limiting to prevent abuse
✅ Secure headers (CSP, HSTS, etc.)
✅ No sensitive data in URLs
✅ Proper CORS configuration
✅ Regular security audits
✅ Dependency vulnerability scanning
```

### Performance Checklist
```text
✅ Implement pagination
✅ Enable compression (gzip)
✅ Use caching headers
✅ Database query optimization
✅ Field selection to reduce payload
✅ Connection pooling
✅ CDN for static assets
✅ Monitor response times
✅ Load testing
```

### Example Complete API Implementation
```python
from flask import Flask, request, jsonify
from flask_restful import Api, Resource
from functools import wraps
import time
import logging

app = Flask(__name__)
api = Api(app)

# Logging setup
logging.basicConfig(level=logging.INFO)
logger = logging.getLogger(__name__)

# Middleware
@app.before_request
def before_request():
    request.start_time = time.time()
    request.id = str(uuid.uuid4())
    
    logger.info('Request started', extra={
        'request_id': request.id,
        'method': request.method,
        'path': request.path
    })

@app.after_request
def after_request(response):
    if hasattr(request, 'start_time'):
        duration = time.time() - request.start_time
        
        logger.info('Request completed', extra={
            'request_id': request.id,
            'status_code': response.status_code,
            'duration': duration
        })
    
    # Security headers
    response.headers['X-Content-Type-Options'] = 'nosniff'
    response.headers['X-Frame-Options'] = 'DENY'
    
    return response

# Error handling
class APIError(Exception):
    pass

@app.errorhandler(APIError)
def handle_api_error(error):
    return jsonify({
        'error': {
            'code': 'api_error',
            'message': str(error)
        }
    }), 400

# Resources
class UserList(Resource):
    def get(self):
        """Get paginated list of users"""
        page = request.args.get('page', 1, type=int)
        limit = request.args.get('limit', 20, type=int)
        
        # Implementation would query database
        users = []  # Mock data
        
        return {
            'data': users,
            'pagination': {
                'page': page,
                'limit': limit,
                'total': 0,  # Would be actual count
                'pages': 0
            }
        }
    
    def post(self):
        """Create a new user"""
        data = request.get_json()
        
        # Validation would happen here
        if not data.get('email'):
            raise APIError('Email is required')
        
        # Create user logic
        user = {'id': 1, **data}  # Mock
        
        return {
            'data': user,
            'links': {
                'self': f'/users/{user["id"]}'
            }
        }, 201

class UserDetail(Resource):
    def get(self, user_id):
        """Get user by ID"""
        user = {'id': user_id, 'name': 'Test User'}  # Mock
        return {'data': user}
    
    def put(self, user_id):
        """Update user"""
        data = request.get_json()
        user = {'id': user_id, **data}  # Mock
        return {'data': user}
    
    def delete(self, user_id):
        """Delete user"""
        return '', 204

# Register resources
api.add_resource(UserList, '/api/v1/users')
api.add_resource(UserDetail, '/api/v1/users/<int:user_id>')

# Health check
@app.route('/health')
def health():
    return jsonify({'status': 'healthy'})

if __name__ == '__main__':
    app.run(debug=True)
```

---

## Quick Reference Card

### HTTP Methods & Their Meaning
```http
GET     - Read (Safe, Idempotent)
POST    - Create (Unsafe, Non-idempotent)  
PUT     - Update/Replace (Unsafe, Idempotent)
PATCH   - Partial Update (Unsafe, Non-idempotent)
DELETE  - Delete (Unsafe, Idempotent)
```

### Common Status Codes
```http
200 - OK
201 - Created
204 - No Content
400 - Bad Request
401 - Unauthorized
403 - Forbidden
404 - Not Found
422 - Unprocessable Entity
429 - Too Many Requests
500 - Internal Server Error
```

### URL Design Patterns
```http
/users                  # Collection
/users/{id}             # Specific resource
/users/{id}/orders      # Sub-resource collection
/users?page=2&limit=50  # Pagination
/users?role=admin       # Filtering
/users?sort=-created_at # Sorting
```

### Response Structure
```json
{
  "data": {...},
  "pagination": {...},
  "links": {...},
  "meta": {...}
}
```

### Error Response Structure
```json
{
  "error": {
    "code": "error_code",
    "message": "Human readable message", 
    "details": [...]
  }
}
```

### Pro Tips
1. **Design for the consumer** - Make your API intuitive
2. **Be consistent** across all endpoints
3. **Use proper HTTP semantics** - status codes, methods, headers
4. **Version from day one** - it's hard to add later
5. **Document everything** - good docs are crucial
6. **Handle errors gracefully** - helpful error messages
7. **Think about performance** - pagination, caching, compression
8. **Security first** - validate inputs, use HTTPS, rate limiting
9. **Monitor and measure** - track usage and performance
10. **Plan for evolution** - have a deprecation strategy

---

*Remember: A well-designed REST API is intuitive, consistent, and follows established conventions. Focus on making your API easy to understand and use, and your consumers will thank you!*