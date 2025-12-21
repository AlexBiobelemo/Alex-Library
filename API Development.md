# API Development: Comprehensive Guide for Python Backend

## Table of Contents

- [Introduction](#introduction)
- [RESTful API Design Principles](#restful-api-design-principles)
- [GraphQL Implementation](#graphql-implementation)
- [Authentication: JWT and OAuth2](#authentication-jwt-and-oauth2)
- [API Documentation with OpenAPI](#api-documentation-with-openapi)
- [Rate Limiting and Pagination](#rate-limiting-and-pagination)
- [Key Takeaways](#key-takeaways)
- [Further Resources](#further-resources)

---

## Introduction

The development of robust, scalable, and maintainable Application Programming Interfaces (APIs) is a cornerstone of modern software architecture, particularly within Python backend ecosystems. This comprehensive guide explores the fundamental concepts, best practices, and practical implementations of API development, focusing on RESTful design, GraphQL, security with JWT and OAuth2, documentation standards, and essential strategies for rate limiting and pagination.

---

## RESTful API Design Principles

### Introduction to REST

REST (Representational State Transfer) represents a fundamental shift in how we architect web services. Rather than defining arbitrary protocols or complex message formats, REST leverages the existing infrastructure of the web itself. This architectural style, introduced by Roy Fielding in his 2000 doctoral dissertation as part of the HTTP 1.1 specification, has become the dominant paradigm for web API design.

**The Core Philosophy**

REST is not a protocol, standard, or specification—it's an architectural style based on a set of constraints. This distinction is crucial: REST provides principles and guidelines rather than rigid rules. This flexibility allows developers to adapt REST to various contexts while maintaining its core benefits: simplicity, scalability, and reliability.

The fundamental concept is treating everything as a "resource"—a user, a product, an order, or any piece of data or functionality. Each resource has:
- A unique identifier (URI)
- A representation (usually JSON)
- A set of operations (HTTP methods)
- State transitions triggered by those operations

**Why REST Matters for Python Backend Development**

Python's ecosystem has embraced REST wholeheartedly. Frameworks like **Django REST Framework (DRF)**, **Flask-RESTful**, **Flask-RESTX**, and **FastAPI** provide sophisticated tooling for building RESTful APIs. These frameworks handle the complexities of HTTP, serialization, authentication, and validation, allowing developers to focus on business logic.

The synergy between REST and Python is particularly powerful because:
1. **Simplicity**: Both emphasize readability and straightforward design
2. **Ecosystem**: Rich libraries for database integration, authentication, and data validation
3. **Scalability**: Python's async capabilities (asyncio, FastAPI) align well with REST's stateless nature
4. **Developer Experience**: Strong typing (via Pydantic, dataclasses) enhances API reliability

**Historical Context**

Before REST, web services were dominated by:
- **RPC (Remote Procedure Call)**: Function-oriented, tightly coupled
- **SOAP (Simple Object Access Protocol)**: XML-heavy, complex, rigid contracts

REST emerged as a simpler alternative, leveraging HTTP's existing semantics. Its adoption exploded in the mid-2000s, driven by companies like Amazon, Google, and Twitter who needed scalable, accessible APIs for their platforms. The rise of mobile applications and JavaScript frameworks further accelerated REST adoption—these clients needed lightweight, flexible data access.

Today, REST remains the default choice for most web APIs, though alternatives like GraphQL and gRPC have emerged for specific use cases. Understanding REST's principles is foundational for any backend developer, as even these alternatives build upon or react to REST's concepts.

### The Six Core Principles of REST

#### 1. Client-Server Architecture

Complete separation of concerns between client and server:
- **Client**: Responsible for UI/UX
- **Server**: Handles data storage, business logic, API endpoints

**Benefits:**
- Portability across platforms
- Independent scalability
- Simplified maintenance

**Python Implementation:**
```python
# Your Django/Flask app focuses solely on API endpoints
from flask import Flask, jsonify

app = Flask(__name__)

@app.route('/api/users', methods=['GET'])
def get_users():
    """API endpoint - client agnostic"""
    users = [
        {"id": 1, "name": "Alice"},
        {"id": 2, "name": "Bob"}
    ]
    return jsonify(users)
```

#### 2. Statelessness

Every request must contain all necessary information. The server stores no client context between requests.

**Benefits:**
- Horizontal scalability
- Improved reliability
- Simplified server architecture

**Implementation: Token-Based Authentication**
```python
from flask import Flask, request, jsonify
import jwt

app = Flask(__name__)
SECRET_KEY = 'your-secret-key'

@app.route('/api/protected', methods=['GET'])
def protected_route():
    """Stateless authentication using JWT"""
    token = request.headers.get('Authorization', '').replace('Bearer ', '')
    
    try:
        payload = jwt.decode(token, SECRET_KEY, algorithms=['HS256'])
        user_id = payload['user_id']
        return jsonify({"message": f"Hello user {user_id}!"})
    except jwt.InvalidTokenError:
        return jsonify({"error": "Invalid token"}), 401
```

#### 3. Cacheability

Responses must indicate whether they can be cached.

**HTTP Caching Headers:**
```python
from flask import Flask, jsonify, make_response
from datetime import datetime, timedelta

app = Flask(__name__)

@app.route('/api/products/<int:product_id>')
def get_product(product_id):
    """Cacheable response example"""
    product = {"id": product_id, "name": "Sample Product", "price": 99.99}
    
    response = make_response(jsonify(product))
    
    # Cache for 1 hour
    response.headers['Cache-Control'] = 'public, max-age=3600'
    
    # Set expiry time
    expires = datetime.utcnow() + timedelta(hours=1)
    response.headers['Expires'] = expires.strftime('%a, %d %b %Y %H:%M:%S GMT')
    
    # ETag for validation
    response.headers['ETag'] = f'"{product_id}-v1"'
    
    return response
```

#### 4. Uniform Interface

The most crucial constraint, defining standardized client-resource interaction.

**Sub-principles:**

**a) Resource Identification**
```python
# Good - nouns, not verbs
GET    /api/users
GET    /api/users/123
POST   /api/products
DELETE /api/orders/456

# Bad - verbs in URIs
GET    /api/getUsers
POST   /api/createProduct
DELETE /api/deleteOrder
```

**b) Resource Manipulation through Representations**
```python
from flask import Flask, request, jsonify

app = Flask(__name__)

users = {
    "1": {"id": "1", "name": "Alice", "email": "alice@example.com"}
}

@app.route('/api/users/<user_id>', methods=['GET'])
def get_user(user_id):
    """Return resource representation"""
    user = users.get(user_id)
    if user:
        return jsonify(user)
    return jsonify({"error": "User not found"}), 404

@app.route('/api/users/<user_id>', methods=['PUT'])
def update_user(user_id):
    """Update resource using representation"""
    if user_id not in users:
        return jsonify({"error": "User not found"}), 404
    
    data = request.json
    users[user_id].update(data)
    return jsonify(users[user_id])
```

**c) Self-Descriptive Messages**
```python
from flask import Flask, jsonify, request

app = Flask(__name__)

@app.route('/api/data', methods=['POST'])
def create_data():
    """Self-descriptive with content-type"""
    content_type = request.headers.get('Content-Type')
    
    if content_type != 'application/json':
        return jsonify({
            "error": "Content-Type must be application/json"
        }), 415
    
    data = request.json
    # Process data
    
    response = jsonify({"id": 1, "status": "created"})
    response.headers['Content-Type'] = 'application/json'
    return response, 201
```

**d) HATEOAS (Hypermedia as the Engine of Application State)**
```python
@app.route('/api/users/<user_id>')
def get_user_with_links(user_id):
    """Include hypermedia links"""
    user = {
        "id": user_id,
        "name": "Alice",
        "email": "alice@example.com",
        "_links": {
            "self": f"/api/users/{user_id}",
            "orders": f"/api/users/{user_id}/orders",
            "friends": f"/api/users/{user_id}/friends",
            "update": {
                "href": f"/api/users/{user_id}",
                "method": "PUT"
            },
            "delete": {
                "href": f"/api/users/{user_id}",
                "method": "DELETE"
            }
        }
    }
    return jsonify(user)
```

#### 5. Layered System

Clients cannot tell if connected directly to end server or intermediary.

```
Client → CDN → Load Balancer → API Gateway → Backend Server → Database
```

**Benefits:**
- Scalability through load balancing
- Security through firewalls
- Caching at multiple layers

#### 6. Code-on-Demand (Optional)

Servers can temporarily extend client functionality by transferring executable code. Rarely implemented in modern APIs.

### Practical Design Considerations

#### 1. Resource Naming

```python
# Good naming conventions
GET    /api/v1/users                    # Collection
GET    /api/v1/users/123                # Single resource
GET    /api/v1/users/123/orders         # Nested resource
POST   /api/v1/products                 # Create
PUT    /api/v1/products/456             # Update
PATCH  /api/v1/products/456             # Partial update
DELETE /api/v1/products/456             # Delete

# Use plural nouns
GET    /api/v1/orders (not /api/v1/order)

# Use hyphens for readability
GET    /api/v1/user-profiles (not /api/v1/userProfiles)
```

#### 2. HTTP Methods

```python
from flask import Flask, request, jsonify

app = Flask(__name__)

products = {}
next_id = 1

# GET - Retrieve (Safe, Idempotent)
@app.route('/api/products', methods=['GET'])
def get_products():
    """Retrieve all products"""
    return jsonify(list(products.values()))

@app.route('/api/products/<int:product_id>', methods=['GET'])
def get_product(product_id):
    """Retrieve specific product"""
    product = products.get(product_id)
    if product:
        return jsonify(product)
    return jsonify({"error": "Product not found"}), 404

# POST - Create (Not Idempotent)
@app.route('/api/products', methods=['POST'])
def create_product():
    """Create new product"""
    global next_id
    data = request.json
    
    product = {
        "id": next_id,
        "name": data.get('name'),
        "price": data.get('price')
    }
    products[next_id] = product
    next_id += 1
    
    return jsonify(product), 201

# PUT - Update/Replace (Idempotent)
@app.route('/api/products/<int:product_id>', methods=['PUT'])
def update_product(product_id):
    """Update entire product"""
    if product_id not in products:
        # Can create if doesn't exist
        data = request.json
        products[product_id] = {
            "id": product_id,
            "name": data.get('name'),
            "price": data.get('price')
        }
        return jsonify(products[product_id]), 201
    
    data = request.json
    products[product_id] = {
        "id": product_id,
        "name": data.get('name'),
        "price": data.get('price')
    }
    return jsonify(products[product_id])

# PATCH - Partial Update (Not necessarily Idempotent)
@app.route('/api/products/<int:product_id>', methods=['PATCH'])
def partial_update_product(product_id):
    """Update specific fields"""
    if product_id not in products:
        return jsonify({"error": "Product not found"}), 404
    
    data = request.json
    product = products[product_id]
    
    if 'name' in data:
        product['name'] = data['name']
    if 'price' in data:
        product['price'] = data['price']
    
    return jsonify(product)

# DELETE - Remove (Idempotent)
@app.route('/api/products/<int:product_id>', methods=['DELETE'])
def delete_product(product_id):
    """Delete product"""
    if product_id in products:
        del products[product_id]
        return '', 204  # No Content
    return jsonify({"error": "Product not found"}), 404

# HEAD - Same as GET but no body
@app.route('/api/products/<int:product_id>', methods=['HEAD'])
def head_product(product_id):
    """Check if product exists"""
    if product_id in products:
        return '', 200
    return '', 404

# OPTIONS - Describe communication options
@app.route('/api/products/<int:product_id>', methods=['OPTIONS'])
def options_product(product_id):
    """Return allowed methods"""
    response = jsonify()
    response.headers['Allow'] = 'GET, PUT, PATCH, DELETE, HEAD, OPTIONS'
    return response
```

#### 3. HTTP Status Codes

```python
from flask import Flask, jsonify

app = Flask(__name__)

# 2xx Success
@app.route('/api/success-examples')
def success_examples():
    """Status code examples"""
    # 200 OK - General success
    # 201 Created - Resource created
    # 204 No Content - Success with no response body
    # 202 Accepted - Request accepted for processing
    pass

# 3xx Redirection
@app.route('/api/old-endpoint')
def old_endpoint():
    """Redirect to new endpoint"""
    return jsonify({"message": "Moved"}), 301, {
        'Location': '/api/new-endpoint'
    }

# 4xx Client Errors
@app.route('/api/client-errors')
def client_errors():
    """Client error examples"""
    return jsonify({
        "400": "Bad Request - Invalid syntax",
        "401": "Unauthorized - Authentication required",
        "403": "Forbidden - No permission",
        "404": "Not Found - Resource doesn't exist",
        "405": "Method Not Allowed - Wrong HTTP method",
        "409": "Conflict - State conflict",
        "422": "Unprocessable Entity - Validation failed",
        "429": "Too Many Requests - Rate limit exceeded"
    })

# 5xx Server Errors
@app.errorhandler(500)
def internal_error(error):
    """Internal server error"""
    return jsonify({"error": "Internal server error"}), 500

@app.errorhandler(503)
def service_unavailable(error):
    """Service unavailable"""
    return jsonify({"error": "Service temporarily unavailable"}), 503
```

#### 4. Versioning

```python
from flask import Flask, jsonify, request

app = Flask(__name__)

# URI Versioning (Most Common)
@app.route('/api/v1/users')
def get_users_v1():
    """Version 1 of users endpoint"""
    return jsonify([{"id": 1, "name": "Alice"}])

@app.route('/api/v2/users')
def get_users_v2():
    """Version 2 with additional fields"""
    return jsonify([{
        "id": 1,
        "name": "Alice",
        "email": "alice@example.com",
        "created_at": "2024-01-01"
    }])

# Header Versioning
@app.route('/api/users')
def get_users_header():
    """Version via Accept header"""
    version = request.headers.get('Accept', 'application/vnd.api.v1+json')
    
    if 'v2' in version:
        return jsonify([{
            "id": 1,
            "name": "Alice",
            "email": "alice@example.com"
        }])
    
    # Default to v1
    return jsonify([{"id": 1, "name": "Alice"}])

# Query Parameter Versioning
@app.route('/api/users')
def get_users_query():
    """Version via query parameter"""
    version = request.args.get('version', 'v1')
    
    if version == 'v2':
        return jsonify([{
            "id": 1,
            "name": "Alice",
            "email": "alice@example.com"
        }])
    
    return jsonify([{"id": 1, "name": "Alice"}])
```

#### 5. Filtering, Sorting, and Searching

```python
from flask import Flask, request, jsonify

app = Flask(__name__)

products = [
    {"id": 1, "name": "Laptop", "price": 999, "category": "electronics", "in_stock": True},
    {"id": 2, "name": "Mouse", "price": 25, "category": "electronics", "in_stock": True},
    {"id": 3, "name": "Desk", "price": 299, "category": "furniture", "in_stock": False},
    {"id": 4, "name": "Chair", "price": 199, "category": "furniture", "in_stock": True},
]

@app.route('/api/products')
def get_products_filtered():
    """
    Filtering, sorting, and searching
    Examples:
    - /api/products?category=electronics
    - /api/products?min_price=100&max_price=500
    - /api/products?in_stock=true
    - /api/products?sort_by=price&order=asc
    - /api/products?search=laptop
    """
    result = products.copy()
    
    # Filtering
    category = request.args.get('category')
    if category:
        result = [p for p in result if p['category'] == category]
    
    min_price = request.args.get('min_price', type=float)
    if min_price:
        result = [p for p in result if p['price'] >= min_price]
    
    max_price = request.args.get('max_price', type=float)
    if max_price:
        result = [p for p in result if p['price'] <= max_price]
    
    in_stock = request.args.get('in_stock')
    if in_stock:
        in_stock_bool = in_stock.lower() == 'true'
        result = [p for p in result if p['in_stock'] == in_stock_bool]
    
    # Searching
    search = request.args.get('search', '').lower()
    if search:
        result = [p for p in result if search in p['name'].lower()]
    
    # Sorting
    sort_by = request.args.get('sort_by', 'id')
    order = request.args.get('order', 'asc')
    
    reverse = order.lower() == 'desc'
    result = sorted(result, key=lambda x: x.get(sort_by, 0), reverse=reverse)
    
    return jsonify({
        "count": len(result),
        "results": result
    })
```

#### 6. Error Handling

```python
from flask import Flask, jsonify, request
from werkzeug.exceptions import HTTPException

app = Flask(__name__)

class APIError(Exception):
    """Base API exception"""
    def __init__(self, message, status_code=400, payload=None):
        super().__init__()
        self.message = message
        self.status_code = status_code
        self.payload = payload
    
    def to_dict(self):
        rv = dict(self.payload or ())
        rv['error'] = {
            'code': self.status_code,
            'message': self.message
        }
        return rv

class ValidationError(APIError):
    """Validation error"""
    def __init__(self, message, field=None):
        super().__init__(message, status_code=422)
        if field:
            self.payload = {'field': field}

class NotFoundError(APIError):
    """Resource not found"""
    def __init__(self, resource):
        super().__init__(f'{resource} not found', status_code=404)

@app.errorhandler(APIError)
def handle_api_error(error):
    """Handle custom API errors"""
    response = jsonify(error.to_dict())
    response.status_code = error.status_code
    return response

@app.errorhandler(HTTPException)
def handle_http_exception(e):
    """Handle HTTP exceptions"""
    return jsonify({
        "error": {
            "code": e.code,
            "message": e.description
        }
    }), e.code

@app.errorhandler(Exception)
def handle_unexpected_error(error):
    """Handle unexpected errors"""
    app.logger.error(f'Unexpected error: {error}')
    return jsonify({
        "error": {
            "code": 500,
            "message": "Internal server error"
        }
    }), 500

@app.route('/api/users/<int:user_id>')
def get_user_with_error_handling(user_id):
    """Example using error handling"""
    users = {1: {"id": 1, "name": "Alice"}}
    
    if user_id not in users:
        raise NotFoundError('User')
    
    return jsonify(users[user_id])

@app.route('/api/users', methods=['POST'])
def create_user_with_validation():
    """Example with validation error"""
    data = request.json
    
    if not data:
        raise ValidationError('Request body is required')
    
    if 'name' not in data:
        raise ValidationError('Name is required', field='name')
    
    if len(data['name']) < 3:
        raise ValidationError('Name must be at least 3 characters', field='name')
    
    # Create user
    user = {"id": 1, "name": data['name']}
    return jsonify(user), 201
```

### Complete RESTful API Example

```python
from flask import Flask, request, jsonify
from datetime import datetime
import jwt
from functools import wraps

app = Flask(__name__)
app.config['SECRET_KEY'] = 'your-secret-key'

# In-memory database
users = {
    1: {"id": 1, "username": "alice", "email": "alice@example.com", "created_at": "2024-01-01"},
    2: {"id": 2, "username": "bob", "email": "bob@example.com", "created_at": "2024-01-02"}
}
next_user_id = 3

# Authentication decorator
def require_auth(f):
    @wraps(f)
    def decorated(*args, **kwargs):
        token = request.headers.get('Authorization', '').replace('Bearer ', '')
        
        if not token:
            return jsonify({"error": "Authentication required"}), 401
        
        try:
            payload = jwt.decode(token, app.config['SECRET_KEY'], algorithms=['HS256'])
            request.user_id = payload['user_id']
        except jwt.InvalidTokenError:
            return jsonify({"error": "Invalid token"}), 401
        
        return f(*args, **kwargs)
    return decorated

# Authentication endpoint
@app.route('/api/v1/auth/login', methods=['POST'])
def login():
    """Login and get JWT token"""
    data = request.json
    
    # Simplified authentication (in production, verify password hash)
    user = next((u for u in users.values() if u['username'] == data.get('username')), None)
    
    if not user:
        return jsonify({"error": "Invalid credentials"}), 401
    
    token = jwt.encode({
        'user_id': user['id'],
        'exp': datetime.utcnow().timestamp() + 3600  # 1 hour
    }, app.config['SECRET_KEY'], algorithm='HS256')
    
    return jsonify({"access_token": token})

# User endpoints
@app.route('/api/v1/users', methods=['GET'])
@require_auth
def list_users():
    """List all users with filtering and pagination"""
    # Filtering
    search = request.args.get('search', '').lower()
    result = list(users.values())
    
    if search:
        result = [u for u in result if search in u['username'].lower() or search in u['email'].lower()]
    
    # Sorting
    sort_by = request.args.get('sort_by', 'id')
    order = request.args.get('order', 'asc')
    result = sorted(result, key=lambda x: x.get(sort_by), reverse=(order == 'desc'))
    
    # Pagination
    page = request.args.get('page', 1, type=int)
    per_page = request.args.get('per_page', 10, type=int)
    
    start = (page - 1) * per_page
    end = start + per_page
    paginated_result = result[start:end]
    
    return jsonify({
        "data": paginated_result,
        "meta": {
            "page": page,
            "per_page": per_page,
            "total": len(result)
        }
    })

@app.route('/api/v1/users/<int:user_id>', methods=['GET'])
@require_auth
def get_user(user_id):
    """Get specific user"""
    user = users.get(user_id)
    
    if not user:
        return jsonify({"error": "User not found"}), 404
    
    return jsonify(user)

@app.route('/api/v1/users', methods=['POST'])
def create_user():
    """Create new user"""
    global next_user_id
    
    data = request.json
    
    # Validation
    if not data.get('username'):
        return jsonify({"error": "Username is required"}), 422
    
    if not data.get('email'):
        return jsonify({"error": "Email is required"}), 422
    
    # Check uniqueness
    if any(u['username'] == data['username'] for u in users.values()):
        return jsonify({"error": "Username already exists"}), 409
    
    # Create user
    user = {
        "id": next_user_id,
        "username": data['username'],
        "email": data['email'],
        "created_at": datetime.utcnow().isoformat()
    }
    
    users[next_user_id] = user
    next_user_id += 1
    
    return jsonify(user), 201

@app.route('/api/v1/users/<int:user_id>', methods=['PUT'])
@require_auth
def update_user(user_id):
    """Update user (full replacement)"""
    if user_id not in users:
        return jsonify({"error": "User not found"}), 404
    
    data = request.json
    
    # Validation
    if not data.get('username') or not data.get('email'):
        return jsonify({"error": "Username and email are required"}), 422
    
    users[user_id] = {
        "id": user_id,
        "username": data['username'],
        "email": data['email'],
        "created_at": users[user_id]['created_at']
    }
    
    return jsonify(users[user_id])

@app.route('/api/v1/users/<int:user_id>', methods=['PATCH'])
@require_auth
def partial_update_user(user_id):
    """Partially update user"""
    if user_id not in users:
        return jsonify({"error": "User not found"}), 404
    
    data = request.json
    user = users[user_id]
    
    if 'username' in data:
        user['username'] = data['username']
    if 'email' in data:
        user['email'] = data['email']
    
    return jsonify(user)

@app.route('/api/v1/users/<int:user_id>', methods=['DELETE'])
@require_auth
def delete_user(user_id):
    """Delete user"""
    if user_id not in users:
        return jsonify({"error": "User not found"}), 404
    
    del users[user_id]
    return '', 204

if __name__ == '__main__':
    app.run(debug=True)
```

---

## GraphQL Implementation

### Introduction

GraphQL is a query language for APIs and a runtime for fulfilling those queries, developed by Facebook in 2012 and open-sourced in 2015. Unlike REST's resource-oriented approach, GraphQL allows clients to request exactly the data they need.

### Background and History

**Evolution Timeline:**
- **1970s-80s**: RPC (Remote Procedure Calls)
- **Late 1990s**: SOAP (Simple Object Access Protocol)
- **Early 2000s**: REST becomes dominant
- **2012**: Facebook develops GraphQL internally
- **2015**: GraphQL open-sourced
- **Present**: Widely adopted alongside REST

**Key Problems GraphQL Solves:**
- Over-fetching (getting more data than needed)
- Under-fetching (multiple requests for related data)
- API versioning complexity
- Rigid REST endpoints

### Core GraphQL Concepts

#### Schema Definition Language (SDL)

```graphql
# Object Type
type User {
  id: ID!
  name: String!
  email: String!
  posts: [Post!]!
  createdAt: DateTime!
}

type Post {
  id: ID!
  title: String!
  content: String!
  author: User!
  published: Boolean!
  publishedAt: DateTime
}

# Input Type for mutations
input CreateUserInput {
  name: String!
  email: String!
}

# Enum
enum UserRole {
  ADMIN
  USER
  GUEST
}

# Interface
interface Node {
  id: ID!
}

# Union
union SearchResult = User | Post

# Query (read operations)
type Query {
  user(id: ID!): User
  users(limit: Int, offset: Int): [User!]!
  post(id: ID!): Post
  posts(authorId: ID): [Post!]!
  search(query: String!): [SearchResult!]!
}

# Mutation (write operations)
type Mutation {
  createUser(input: CreateUserInput!): User!
  updateUser(id: ID!, input: CreateUserInput!): User!
  deleteUser(id: ID!): Boolean!
  createPost(title: String!, content: String!, authorId: ID!): Post!
}

# Subscription (real-time updates)
type Subscription {
  postAdded: Post!
  userUpdated(userId: ID!): User!
}
```

### Python Implementation with Graphene

**Installation:**
```bash
pip install graphene flask flask-graphql
```

**Complete Example:**

```python
import graphene
from graphene import relay
from flask import Flask
from flask_graphql import GraphQLView
import uuid
from datetime import datetime

# --- Data Layer (simulated database) ---
users_db = {}
posts_db = {}

def seed_data():
    """Seed initial data"""
    user1_id = str(uuid.uuid4())
    user2_id = str(uuid.uuid4())
    
    users_db[user1_id] = {
        "id": user1_id,
        "name": "Alice Johnson",
        "email": "alice@example.com",
        "created_at": datetime(2024, 1, 1)
    }
    
    users_db[user2_id] = {
        "id": user2_id,
        "name": "Bob Smith",
        "email": "bob@example.com",
        "created_at": datetime(2024, 1, 2)
    }
    
    post1_id = str(uuid.uuid4())
    post2_id = str(uuid.uuid4())
    
    posts_db[post1_id] = {
        "id": post1_id,
        "title": "GraphQL Basics",
        "content": "Learn GraphQL fundamentals",
        "author_id": user1_id,
        "published": True,
        "published_at": datetime(2024, 1, 15)
    }
    
    posts_db[post2_id] = {
        "id": post2_id,
        "title": "Advanced Python",
        "content": "Deep dive into Python",
        "author_id": user2_id,
        "published": False,
        "published_at": None
    }

seed_data()

# --- GraphQL Types ---

class User(graphene.ObjectType):
    """User type"""
    id = graphene.ID()
    name = graphene.String()
    email = graphene.String()
    created_at = graphene.DateTime()
    posts = graphene.List(lambda: Post)
    
    def resolve_posts(self, info):
        """Resolve user's posts"""
        return [posts_db[post_id] for post_id in posts_db 
                if posts_db[post_id]['author_id'] == self.id]

class Post(graphene.ObjectType):
    """Post type"""
    id = graphene.ID()
    title = graphene.String()
    content = graphene.String()
    published = graphene.Boolean()
    published_at = graphene.DateTime()
    author = graphene.Field(User)
    
    def resolve_author(self, info):
        """Resolve post's author"""
        return users_db.get(self.author_id)

# --- Queries ---

class Query(graphene.ObjectType):
    """Root query"""
    user = graphene.Field(User, id=graphene.ID(required=True))
    users = graphene.List(User, limit=graphene.Int(), offset=graphene.Int())
    post = graphene.Field(Post, id=graphene.ID(required=True))
    posts = graphene.List(Post, author_id=graphene.ID())
    
    def resolve_user(self, info, id):
        """Get user by ID"""
        return users_db.get(id)
    
    def resolve_users(self, info, limit=None, offset=None):
        """Get all users with pagination"""
        result = list(users_db.values())
        if offset:
            result = result[offset:]
        if limit:
            result = result[:limit]
        return result
    
    def resolve_post(self, info, id):
        """Get post by ID"""
        return posts_db.get(id)
    
    def resolve_posts(self, info, author_id=None):
        """Get posts, optionally filtered by author"""
        result = list(posts_db.values())
        if author_id:
            result = [p for p in result if p['author_id'] == author_id]
        return result

# --- Mutations ---

class CreateUser(graphene.Mutation):
    """Create user mutation"""
    class Arguments:
        name = graphene.String(required=True)
        email = graphene.String(required=True)
    
    user = graphene.Field(User)
    
    def mutate(self, info, name, email):
        """Create new user"""
        user_id = str(uuid.uuid4())
        user = {
            "id": user_id,
            "name": name,
            "email": email,
            "created_at": datetime.utcnow()
        }
        users_db[user_id] = user
        return CreateUser(user=User(**user))

class UpdateUser(graphene.Mutation):
    """Update user mutation"""
    class Arguments:
        id = graphene.ID(required=True)
        name = graphene.String()
        email = graphene.String()
    
    user = graphene.Field(User)
    
    def mutate(self, info, id, name=None, email=None):
        """Update existing user"""
        if id not in users_db:
            raise Exception(f"User {id} not found")
        
        user = users_db[id]
        if name:
            user['name'] = name
        if email:
            user['email'] = email
        
        return UpdateUser(user=User(**user))

class CreatePost(graphene.Mutation):
    """Create post mutation"""
    class Arguments:
        title = graphene.String(required=True)
        content = graphene.String(required=True)
        author_id = graphene.ID(required=True)
        published = graphene.Boolean()
    
    post = graphene.Field(Post)
    
    def mutate(self, info, title, content, author_id, published=False):
        """Create new post"""
        if author_id not in users_db:
            raise Exception(f"Author {author_id} not found")
        
        post_id = str(uuid.uuid4())
        post = {
            "id": post_id,
            "title": title,
            "content": content,
            "author_id": author_id,
            "published": published,
            "published_at": datetime.utcnow() if published else None
        }
        posts_db[post_id] = post
        return CreatePost(post=Post(**post))

class Mutation(graphene.ObjectType):
    """Root mutation"""
    create_user = CreateUser.Field()
    update_user = UpdateUser.Field()
    create_post = CreatePost.Field()

# --- Schema ---

schema = graphene.Schema(query=Query, mutation=Mutation)

# --- Flask Application ---

app = Flask(__name__)

app.add_url_rule(
    '/graphql',
    view_func=GraphQLView.as_view(
        'graphql',
        schema=schema,
        graphiql=True  # Enable GraphiQL IDE
    )
)

@app.route('/')
def index():
    return 'GraphQL API. Visit /graphql for GraphiQL interface.'

if __name__ == '__main__':
    app.run(debug=True)
```

### Example Queries

**Query all users:**
```graphql
query {
  users {
    id
    name
    email
    posts {
      title
      published
    }
  }
}
```

**Query specific user with nested data:**
```graphql
query GetUserWithPosts($userId: ID!) {
  user(id: $userId) {
    name
    email
    posts {
      id
      title
      content
      published
      publishedAt
    }
  }
}

# Variables:
# {
#   "userId": "your-user-id-here"
# }
```

**Create user mutation:**
```graphql
mutation CreateNewUser {
  createUser(name: "Charlie Brown", email: "charlie@example.com") {
    user {
      id
      name
      email
      createdAt
    }
  }
}
```

**Create post mutation:**
```graphql
mutation CreateNewPost($authorId: ID!) {
  createPost(
    title: "My First Post"
    content: "This is the content of my first post"
    authorId: $authorId
    published: true
  ) {
    post {
      id
      title
      author {
        name
      }
      publishedAt
    }
  }
}
```

### GraphQL vs REST Comparison

| Feature | REST | GraphQL |
|:--------|:-----|:--------|
| **Endpoints** | Multiple (`/users`, `/posts`) | Single (`/graphql`) |
| **Data Fetching** | Fixed structure per endpoint | Client defines exact needs |
| **Over-fetching** | Common | Eliminated |
| **Under-fetching** | Requires multiple requests | Single request for nested data |
| **Versioning** | URL-based (`/v1`, `/v2`) | Schema evolution |
| **Caching** | HTTP caching (simple) | Complex (requires normalized cache) |
| **Learning Curve** | Lower | Higher |
| **Tooling** | Mature | Growing rapidly |
| **Best For** | Simple CRUD, public APIs | Complex data graphs, mobile apps |

### Best Practices

1. **Schema Design**:
   - Design around client needs, not database structure
   - Use descriptive names
   - Leverage interfaces and unions for flexibility

2. **N+1 Problem**:
   ```python
   from promise import Promise
   from promise.dataloader import DataLoader
   
   class UserLoader(DataLoader):
       def batch_load_fn(self, user_ids):
           """Batch load users"""
           users = [users_db.get(uid) for uid in user_ids]
           return Promise.resolve(users)
   
   # Use in resolver
   def resolve_author(self, info):
       return info.context['user_loader'].load(self.author_id)
   ```

3. **Authentication**:
   ```python
   def get_context():
       token = request.headers.get('Authorization', '').replace('Bearer ', '')
       # Verify token and return user
       return {'user': verify_token(token)}
   
   # In resolver
   def resolve_protected_data(self, info):
       user = info.context.get('user')
       if not user:
           raise Exception("Authentication required")
       return protected_data
   ```

4. **Query Complexity Limiting**:
   - Implement query depth limiting
   - Implement query complexity analysis
   - Set timeouts

---

## Authentication: JWT and OAuth2

### Introduction

Authentication verifies *who* a user is, while authorization determines *what* they can do. JWT (JSON Web Tokens) and OAuth2 are foundational technologies for securing modern APIs.

### JSON Web Tokens (JWT)

#### Structure

```
header.payload.signature
```

**Example JWT:**
```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c
```

**Decoded:**

*Header:*
```json
{
  "alg": "HS256",
  "typ": "JWT"
}
```

*Payload:*
```json
{
  "sub": "1234567890",
  "name": "John Doe",
  "iat": 1516239022,
  "exp": 1516242622
}
```

*Signature:*
```
HMACSHA256(
  base64UrlEncode(header) + "." + base64UrlEncode(payload),
  secret
)
```

#### Python Implementation

```python
from flask import Flask, request, jsonify
from functools import wraps
import jwt
from datetime import datetime, timedelta
from werkzeug.security import generate_password_hash, check_password_hash

app = Flask(__name__)
app.config['SECRET_KEY'] = 'your-secret-key-change-in-production'

# Simulated user database
users_db = {
    "alice": {
        "username": "alice",
        "password_hash": generate_password_hash("password123"),
        "email": "alice@example.com",
        "roles": ["user"]
    },
    "admin": {
        "username": "admin",
        "password_hash": generate_password_hash("admin123"),
        "email": "admin@example.com",
        "roles": ["user", "admin"]
    }
}

def create_token(user_data):
    """Create JWT token"""
    payload = {
        'sub': user_data['username'],
        'email': user_data['email'],
        'roles': user_data['roles'],
        'iat': datetime.utcnow(),
        'exp': datetime.utcnow() + timedelta(hours=24)
    }
    return jwt.encode(payload, app.config['SECRET_KEY'], algorithm='HS256')

def verify_token(token):
    """Verify JWT token"""
    try:
        payload = jwt.decode(token, app.config['SECRET_KEY'], algorithms=['HS256'])
        return payload
    except jwt.ExpiredSignatureError:
        raise Exception("Token has expired")
    except jwt.InvalidTokenError:
        raise Exception("Invalid token")

def require_auth(f):
    """Authentication decorator"""
    @wraps(f)
    def decorated(*args, **kwargs):
        token = request.headers.get('Authorization', '').replace('Bearer ', '')
        
        if not token:
            return jsonify({"error": "Token required"}), 401
        
        try:
            payload = verify_token(token)
            request.user = payload
        except Exception as e:
            return jsonify({"error": str(e)}), 401
        
        return f(*args, **kwargs)
    return decorated

def require_role(role):
    """Role-based authorization decorator"""
    def decorator(f):
        @wraps(f)
        def decorated(*args, **kwargs):
            if role not in request.user.get('roles', []):
                return jsonify({"error": "Insufficient permissions"}), 403
            return f(*args, **kwargs)
        return decorated
    return decorator

@app.route('/api/auth/login', methods=['POST'])
def login():
    """Login endpoint"""
    data = request.json
    username = data.get('username')
    password = data.get('password')
    
    if not username or not password:
        return jsonify({"error": "Username and password required"}), 400
    
    user = users_db.get(username)
    if not user or not check_password_hash(user['password_hash'], password):
        return jsonify({"error": "Invalid credentials"}), 401
    
    token = create_token(user)
    
    return jsonify({
        "access_token": token,
        "token_type": "Bearer",
        "expires_in": 86400  # 24 hours in seconds
    })

@app.route('/api/auth/me', methods=['GET'])
@require_auth
def get_current_user():
    """Get current user info"""
    return jsonify({
        "username": request.user['sub'],
        "email": request.user['email'],
        "roles": request.user['roles']
    })

@app.route('/api/protected', methods=['GET'])
@require_auth
def protected_route():
    """Protected route"""
    return jsonify({"message": f"Hello {request.user['sub']}!"})

@app.route('/api/admin', methods=['GET'])
@require_auth
@require_role('admin')
def admin_route():
    """Admin-only route"""
    return jsonify({"message": "Admin access granted"})

if __name__ == '__main__':
    app.run(debug=True)
```

### OAuth2 Implementation

**OAuth2 Authorization Code Flow with PKCE:**

```python
from flask import Flask, request, jsonify, redirect, session
from authlib.integrations.flask_client import OAuth
import secrets

app = Flask(__name__)
app.secret_key = 'your-secret-key'

# Configure OAuth
oauth = OAuth(app)

# Register OAuth provider (e.g., Google)
google = oauth.register(
    name='google',
    client_id='YOUR_GOOGLE_CLIENT_ID',
    client_secret='YOUR_GOOGLE_CLIENT_SECRET',
    access_token_url='https://accounts.google.com/o/oauth2/token',
    access_token_params=None,
    authorize_url='https://accounts.google.com/o/oauth2/auth',
    authorize_params=None,
    api_base_url='https://www.googleapis.com/oauth2/v1/',
    client_kwargs={'scope': 'openid email profile'}
)

@app.route('/login')
def login():
    """Initiate OAuth login"""
    # Generate PKCE code verifier and challenge
    code_verifier = secrets.token_urlsafe(32)
    session['code_verifier'] = code_verifier
    
    redirect_uri = 'http://localhost:5000/auth/callback'
    return google.authorize_redirect(redirect_uri, code_verifier=code_verifier)

@app.route('/auth/callback')
def callback():
    """OAuth callback"""
    code_verifier = session.pop('code_verifier', None)
    
    try:
        token = google.authorize_access_token(code_verifier=code_verifier)
        
        # Get user info
        resp = google.get('userinfo', token=token)
        user_info = resp.json()
        
        # Create session or JWT
        session['user'] = user_info
        
        return jsonify({
            "message": "Login successful",
            "user": user_info
        })
    
    except Exception as e:
        return jsonify({"error": str(e)}), 400

@app.route('/logout')
def logout():
    """Logout"""
    session.pop('user', None)
    return jsonify({"message": "Logged out"})

if __name__ == '__main__':
    app.run(debug=True)
```

### Security Best Practices

1. **Token Storage**:
   - Never store in localStorage (vulnerable to XSS)
   - Use httpOnly cookies for refresh tokens
   - Store access tokens in memory

2. **Token Expiration**:
   - Short-lived access tokens (15-30 minutes)
   - Long-lived refresh tokens (7-30 days)
   - Implement token rotation

3. **Secure Communication**:
   - Always use HTTPS
   - Validate token signature
   - Check expiration and issuer

4. **Refresh Token Example**:
```python
@app.route('/api/auth/refresh', methods=['POST'])
def refresh_token():
    """Refresh access token"""
    refresh_token = request.json.get('refresh_token')
    
    if not refresh_token:
        return jsonify({"error": "Refresh token required"}), 400
    
    try:
        # Verify refresh token
        payload = verify_token(refresh_token)
        
        # Check if refresh token (not access token)
        if payload.get('type') != 'refresh':
            return jsonify({"error": "Invalid token type"}), 400
        
        # Generate new access token
        user = users_db.get(payload['sub'])
        new_access_token = create_token(user)
        
        return jsonify({"access_token": new_access_token})
    
    except Exception as e:
        return jsonify({"error": str(e)}), 401
```

---

## API Documentation with OpenAPI

### Introduction

OpenAPI (formerly Swagger) is a specification for describing RESTful APIs in a machine-readable format.

### OpenAPI Specification

```yaml
openapi: 3.0.3
info:
  title: User API
  description: API for managing users
  version: 1.0.0
  contact:
    name: API Support
    email: support@example.com

servers:
  - url: https://api.example.com/v1
    description: Production server
  - url: https://staging-api.example.com/v1
    description: Staging server

paths:
  /users:
    get:
      summary: List users
      description: Retrieve a list of all users
      tags:
        - Users
      parameters:
        - in: query
          name: page
          schema:
            type: integer
            default: 1
          description: Page number
        - in: query
          name: limit
          schema:
            type: integer
            default: 10
            maximum: 100
          description: Items per page
      responses:
        '200':
          description: Successful response
          content:
            application/json:
              schema:
                type: object
                properties:
                  data:
                    type: array
                    items:
                      $ref: '#/components/schemas/User'
                  meta:
                    $ref: '#/components/schemas/PaginationMeta'
    
    post:
      summary: Create user
      description: Create a new user
      tags:
        - Users
      security:
        - BearerAuth: []
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/CreateUserInput'
      responses:
        '201':
          description: User created
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/User'
        '422':
          description: Validation error
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/Error'

  /users/{userId}:
    get:
      summary: Get user
      description: Retrieve a specific user by ID
      tags:
        - Users
      parameters:
        - in: path
          name: userId
          required: true
          schema:
            type: integer
          description: User ID
      responses:
        '200':
          description: Successful response
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/User'
        '404':
          description: User not found
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/Error'

components:
  securitySchemes:
    BearerAuth:
      type: http
      scheme: bearer
      bearerFormat: JWT

  schemas:
    User:
      type: object
      required:
        - id
        - username
        - email
      properties:
        id:
          type: integer
          example: 1
        username:
          type: string
          example: alice
        email:
          type: string
          format: email
          example: alice@example.com
        createdAt:
          type: string
          format: date-time
          example: '2024-01-01T00:00:00Z'

    CreateUserInput:
      type: object
      required:
        - username
        - email
      properties:
        username:
          type: string
          minLength: 3
          maxLength: 50
          example: alice
        email:
          type: string
          format: email
          example: alice@example.com
        password:
          type: string
          minLength: 8
          example: password123

    PaginationMeta:
      type: object
      properties:
        page:
          type: integer
          example: 1
        limit:
          type: integer
          example: 10
        total:
          type: integer
          example: 100

    Error:
      type: object
      required:
        - error
      properties:
        error:
          type: object
          required:
            - code
            - message
          properties:
            code:
              type: integer
              example: 404
            message:
              type: string
              example: User not found
            field:
              type: string
              example: email
```

### Python Implementation with Flask-RESTX

```python
from flask import Flask
from flask_restx import Api, Resource, fields
from werkzeug.middleware.proxy_fix import ProxyFix

app = Flask(__name__)
app.wsgi_app = ProxyFix(app.wsgi_app)

# Initialize API with Swagger documentation
api = Api(
    app,
    version='1.0',
    title='User API',
    description='API for managing users',
    doc='/docs'
)

# Create namespace
ns = api.namespace('users', description='User operations')

# Define models for documentation
user_model = api.model('User', {
    'id': fields.Integer(required=True, description='User ID'),
    'username': fields.String(required=True, description='Username'),
    'email': fields.String(required=True, description='Email address'),
    'created_at': fields.DateTime(description='Creation timestamp')
})

create_user_model = api.model('CreateUser', {
    'username': fields.String(required=True, description='Username', min_length=3),
    'email': fields.String(required=True, description='Email address'),
    'password': fields.String(required=True, description='Password', min_length=8)
})

pagination_parser = api.parser()
pagination_parser.add_argument('page', type=int, default=1, help='Page number')
pagination_parser.add_argument('limit', type=int, default=10, help='Items per page')

# In-memory database
users = {}
next_id = 1

@ns.route('/')
class UserList(Resource):
    @ns.doc('list_users')
    @ns.expect(pagination_parser)
    @ns.marshal_list_with(user_model)
    def get(self):
        """List all users"""
        args = pagination_parser.parse_args()
        page = args['page']
        limit = args['limit']
        
        start = (page - 1) * limit
        end = start + limit
        
        return list(users.values())[start:end]
    
    @ns.doc('create_user')
    @ns.expect(create_user_model)
    @ns.marshal_with(user_model, code=201)
    def post(self):
        """Create a new user"""
        global next_id
        
        data = api.payload
        user = {
            'id': next_id,
            'username': data['username'],
            'email': data['email']
        }
        users[next_id] = user
        next_id += 1
        
        return user, 201

@ns.route('/<int:id>')
@ns.response(404, 'User not found')
@ns.param('id', 'The user identifier')
class User(Resource):
    @ns.doc('get_user')
    @ns.marshal_with(user_model)
    def get(self, id):
        """Get a user by ID"""
        if id not in users:
            api.abort(404, f"User {id} not found")
        return users[id]
    
    @ns.doc('delete_user')
    @ns.response(204, 'User deleted')
    def delete(self, id):
        """Delete a user"""
        if id not in users:
            api.abort(404, f"User {id} not found")
        del users[id]
        return '', 204
    
    @ns.doc('update_user')
    @ns.expect(create_user_model)
    @ns.marshal_with(user_model)
    def put(self, id):
        """Update a user"""
        if id not in users:
            api.abort(404, f"User {id} not found")
        
        data = api.payload
        users[id].update({
            'username': data['username'],
            'email': data['email']
        })
        return users[id]

if __name__ == '__main__':
    app.run(debug=True)
```

---

## Rate Limiting and Pagination

### Rate Limiting

Rate limiting controls the number of requests a client can make within a defined timeframe, protecting APIs from abuse and ensuring fair resource allocation.

#### Rate Limiting Algorithms

**1. Fixed Window Counter**

```python
from flask import Flask, jsonify
from functools import wraps
import time

app = Flask(__name__)

# Simple in-memory rate limiter
rate_limit_store = {}

def rate_limit(max_requests=5, window_seconds=60):
    """Fixed window rate limiter decorator"""
    def decorator(f):
        @wraps(f)
        def decorated(*args, **kwargs):
            # Get client identifier (IP address)
            client_id = request.remote_addr
            current_time = int(time.time())
            window_start = (current_time // window_seconds) * window_seconds
            
            key = f"{client_id}:{window_start}"
            
            # Initialize or increment counter
            if key not in rate_limit_store:
                rate_limit_store[key] = 0
            
            rate_limit_store[key] += 1
            
            # Check limit
            if rate_limit_store[key] > max_requests:
                response = jsonify({
                    "error": "Rate limit exceeded",
                    "retry_after": window_seconds - (current_time % window_seconds)
                })
                response.status_code = 429
                response.headers['X-RateLimit-Limit'] = str(max_requests)
                response.headers['X-RateLimit-Remaining'] = '0'
                response.headers['X-RateLimit-Reset'] = str(window_start + window_seconds)
                response.headers['Retry-After'] = str(window_seconds - (current_time % window_seconds))
                return response
            
            # Add rate limit headers
            result = f(*args, **kwargs)
            if isinstance(result, tuple):
                response = result[0]
            else:
                response = result
            
            response.headers['X-RateLimit-Limit'] = str(max_requests)
            response.headers['X-RateLimit-Remaining'] = str(max_requests - rate_limit_store[key])
            response.headers['X-RateLimit-Reset'] = str(window_start + window_seconds)
            
            return result
        
        return decorated
    return decorator

@app.route('/api/limited')
@rate_limit(max_requests=5, window_seconds=60)
def limited_endpoint():
    """Rate limited endpoint"""
    return jsonify({"message": "Success"})
```

**2. Token Bucket Algorithm**

```python
import time
from threading import Lock

class TokenBucket:
    """Token bucket rate limiter"""
    
    def __init__(self, capacity, refill_rate):
        """
        Args:
            capacity: Maximum tokens in bucket
            refill_rate: Tokens added per second
        """
        self.capacity = capacity
        self.refill_rate = refill_rate
        self.tokens = capacity
        self.last_refill = time.time()
        self.lock = Lock()
    
    def consume(self, tokens=1):
        """Try to consume tokens from bucket"""
        with self.lock:
            self._refill()
            
            if self.tokens >= tokens:
                self.tokens -= tokens
                return True
            return False
    
    def _refill(self):
        """Refill tokens based on time elapsed"""
        now = time.time()
        elapsed = now - self.last_refill
        tokens_to_add = elapsed * self.refill_rate
        
        self.tokens = min(self.capacity, self.tokens + tokens_to_add)
        self.last_refill = now

# Usage
buckets = {}

def get_bucket(client_id):
    """Get or create bucket for client"""
    if client_id not in buckets:
        buckets[client_id] = TokenBucket(capacity=10, refill_rate=1)
    return buckets[client_id]

@app.route('/api/token-limited')
def token_limited():
    """Token bucket rate limited endpoint"""
    client_id = request.remote_addr
    bucket = get_bucket(client_id)
    
    if not bucket.consume():
        return jsonify({"error": "Rate limit exceeded"}), 429
    
    return jsonify({"message": "Success"})
```

**3. Using Flask-Limiter**

```python
from flask import Flask, jsonify
from flask_limiter import Limiter
from flask_limiter.util import get_remote_address

app = Flask(__name__)

# Initialize limiter
limiter = Limiter(
    app=app,
    key_func=get_remote_address,
    default_limits=["200 per day", "50 per hour"],
    storage_uri="redis://localhost:6379"  # Use Redis for distributed systems
)

@app.route('/api/strict')
@limiter.limit("5 per minute")
def strict_endpoint():
    """Strict rate limit: 5 requests per minute"""
    return jsonify({"message": "Success"})

@app.route('/api/flexible')
@limiter.limit("10 per minute;100 per hour;1000 per day")
def flexible_endpoint():
    """Multiple rate limits"""
    return jsonify({"message": "Success"})

@app.route('/api/dynamic')
@limiter.limit(lambda: "10 per hour" if is_premium_user() else "3 per hour")
def dynamic_endpoint():
    """Dynamic rate limit based on user tier"""
    return jsonify({"message": "Success"})

@app.route('/api/exempt')
@limiter.exempt
def exempt_endpoint():
    """Exempt from rate limiting"""
    return jsonify({"message": "Unlimited access"})

# Custom error handler
@app.errorhandler(429)
def ratelimit_handler(e):
    """Custom rate limit error response"""
    return jsonify({
        "error": "Rate limit exceeded",
        "message": str(e.description)
    }), 429

if __name__ == '__main__':
    app.run(debug=True)
```

### Pagination

Pagination divides large result sets into manageable chunks, improving performance and user experience.

#### 1. Offset/Limit Pagination

```python
from flask import Flask, request, jsonify

app = Flask(__name__)

# Sample data
products = [{"id": i, "name": f"Product {i}", "price": i * 10} 
            for i in range(1, 101)]

@app.route('/api/products')
def get_products_offset():
    """
    Offset/limit pagination
    Example: /api/products?page=2&per_page=10
    """
    page = request.args.get('page', 1, type=int)
    per_page = request.args.get('per_page', 10, type=int)
    
    # Validate
    if page < 1:
        return jsonify({"error": "Page must be >= 1"}), 400
    if per_page < 1 or per_page > 100:
        return jsonify({"error": "Per page must be 1-100"}), 400
    
    # Calculate offset
    offset = (page - 1) * per_page
    
    # Get page data
    page_data = products[offset:offset + per_page]
    
    # Calculate pagination metadata
    total_items = len(products)
    total_pages = (total_items + per_page - 1) // per_page
    
    return jsonify({
        "data": page_data,
        "meta": {
            "page": page,
            "per_page": per_page,
            "total_items": total_items,
            "total_pages": total_pages,
            "has_next": page < total_pages,
            "has_prev": page > 1
        },
        "links": {
            "self": f"/api/products?page={page}&per_page={per_page}",
            "first": f"/api/products?page=1&per_page={per_page}",
            "last": f"/api/products?page={total_pages}&per_page={per_page}",
            "next": f"/api/products?page={page + 1}&per_page={per_page}" if page < total_pages else None,
            "prev": f"/api/products?page={page - 1}&per_page={per_page}" if page > 1 else None
        }
    })
```

#### 2. Cursor-Based Pagination

```python
import base64
import json
from flask import Flask, request, jsonify

app = Flask(__name__)

products = [{"id": i, "name": f"Product {i}", "created_at": f"2024-01-{i:02d}"} 
            for i in range(1, 51)]

def encode_cursor(data):
    """Encode cursor data"""
    json_str = json.dumps(data)
    return base64.b64encode(json_str.encode()).decode()

def decode_cursor(cursor):
    """Decode cursor data"""
    try:
        json_str = base64.b64decode(cursor.encode()).decode()
        return json.loads(json_str)
    except:
        return None

@app.route('/api/products/cursor')
def get_products_cursor():
    """
    Cursor-based pagination
    Example: /api/products/cursor?limit=10&after=eyJpZCI6MTB9
    """
    limit = request.args.get('limit', 10, type=int)
    after_cursor = request.args.get('after')
    
    # Validate limit
    if limit < 1 or limit > 100:
        return jsonify({"error": "Limit must be 1-100"}), 400
    
    # Determine starting point
    start_id = 0
    if after_cursor:
        cursor_data = decode_cursor(after_cursor)
        if cursor_data:
            start_id = cursor_data.get('id', 0)
    
    # Filter products after cursor
    filtered = [p for p in products if p['id'] > start_id]
    
    # Get page data
    page_data = filtered[:limit]
    
    # Generate next cursor
    next_cursor = None
    if len(filtered) > limit:
        last_item = page_data[-1]
        next_cursor = encode_cursor({"id": last_item['id']})
    
    return jsonify({
        "data": page_data,
        "paging": {
            "cursors": {
                "after": next_cursor
            },
            "next": f"/api/products/cursor?limit={limit}&after={next_cursor}" if next_cursor else None
        }
    })
```

#### 3. Django REST Framework Pagination

```python
from rest_framework.pagination import PageNumberPagination, CursorPagination
from rest_framework import generics
from .models import Product
from .serializers import ProductSerializer

# Page Number Pagination
class StandardResultsSetPagination(PageNumberPagination):
    page_size = 10
    page_size_query_param = 'page_size'
    max_page_size = 100

class ProductListView(generics.ListAPIView):
    queryset = Product.objects.all()
    serializer_class = ProductSerializer
    pagination_class = StandardResultsSetPagination

# Cursor Pagination
class ProductCursorPagination(CursorPagination):
    page_size = 10
    ordering = '-created_at'  # Must be unique, indexed field

class ProductCursorListView(generics.ListAPIView):
    queryset = Product.objects.all()
    serializer_class = ProductSerializer
    pagination_class = ProductCursorPagination

# Limit/Offset Pagination
from rest_framework.pagination import LimitOffsetPagination

class ProductLimitOffsetPagination(LimitOffsetPagination):
    default_limit = 10
    max_limit = 100

class ProductLimitOffsetView(generics.ListAPIView):
    queryset = Product.objects.all()
    serializer_class = ProductSerializer
    pagination_class = ProductLimitOffsetPagination
```

### Best Practices

#### Rate Limiting

1. **Clear Communication**:
```python
@app.route('/api/info')
def api_info():
    """Provide rate limit information"""
    return jsonify({
        "rate_limits": {
            "authenticated": "100 requests per hour",
            "anonymous": "10 requests per hour"
        },
        "headers": {
            "X-RateLimit-Limit": "Maximum requests allowed",
            "X-RateLimit-Remaining": "Requests remaining",
            "X-RateLimit-Reset": "Time when limit resets (Unix timestamp)",
            "Retry-After": "Seconds to wait before retrying"
        }
    })
```

2. **Graceful Degradation**:
```python
@app.route('/api/critical')
@limiter.limit("100 per hour", error_message="Rate limit exceeded. Critical endpoint.")
def critical_endpoint():
    """Critical endpoint with clear error message"""
    return jsonify({"data": "Important data"})
```

3. **Different Limits for Different Users**:
```python
def get_rate_limit_key():
    """Custom key function for user-based limiting"""
    if hasattr(request, 'user') and request.user.is_premium:
        return f"premium:{request.user.id}"
    return f"standard:{get_remote_address()}"

limiter = Limiter(
    app=app,
    key_func=get_rate_limit_key
)
```

#### Pagination

1. **Consistent Ordering**:
```python
# Always specify order
@app.route('/api/products')
def get_products():
    # Bad: No consistent order
    # products = Product.query.all()
    
    # Good: Consistent order
    products = Product.query.order_by(Product.id).all()
    return paginate(products)
```

2. **Include Metadata**:
```python
def create_pagination_response(items, page, per_page, total):
    """Create consistent pagination response"""
    return {
        "data": items,
        "meta": {
            "page": page,
            "per_page": per_page,
            "total": total,
            "pages": (total + per_page - 1) // per_page
        },
        "links": {
            "self": f"?page={page}",
            "first": "?page=1",
            "last": f"?page={((total + per_page - 1) // per_page)}",
            "next": f"?page={page + 1}" if page * per_page < total else None,
            "prev": f"?page={page - 1}" if page > 1 else None
        }
    }
```

3. **Performance Optimization**:
```python
from functools import lru_cache

@lru_cache(maxsize=128)
def get_total_count(query_hash):
    """Cache expensive count queries"""
    return Product.query.count()

@app.route('/api/products')
def get_products_optimized():
    """Optimized pagination"""
    page = request.args.get('page', 1, type=int)
    per_page = 10
    
    # Use query hash for cache key
    query_hash = hash(str(request.args))
    
    # Get total with caching
    total = get_total_count(query_hash)
    
    # Efficient query with limit/offset
    products = Product.query.order_by(Product.id)\
        .limit(per_page)\
        .offset((page - 1) * per_page)\
        .all()
    
    return create_pagination_response(
        [p.to_dict() for p in products],
        page,
        per_page,
        total
    )
```

### Comparison Matrix

| Feature | Offset/Limit | Cursor-Based |
|:--------|:-------------|:-------------|
| **Implementation** | Simple | More complex |
| **Performance (shallow)** | Good | Good |
| **Performance (deep)** | Poor | Excellent |
| **Consistency** | Poor (data changes) | Good |
| **Jump to page** | Yes | No |
| **Use Case** | Small datasets, UI with page numbers | Large datasets, infinite scroll |

---

## Key Takeaways

### RESTful APIs
- REST is an architectural style, not a protocol
- Six core principles guide design: Client-Server, Statelessness, Cacheability, Uniform Interface, Layered System, Code-on-Demand
- Use proper HTTP methods and status codes
- Implement versioning from the start
- Comprehensive error handling is crucial

### GraphQL
- Solves over-fetching and under-fetching problems
- Single endpoint with client-defined queries
- Requires careful N+1 problem mitigation (DataLoader)
- Best for complex data graphs and mobile applications
- Not a replacement for REST, but a powerful alternative

### Authentication & Authorization
- JWT provides stateless authentication
- OAuth2 handles delegated authorization
- Always use HTTPS for token transmission
- Implement token expiration and refresh mechanisms
- Store tokens securely (httpOnly cookies for refresh tokens)

### API Documentation
- OpenAPI (Swagger) is the industry standard
- Auto-generated documentation improves developer experience
- Keep documentation in sync with code
- Provide interactive documentation (Swagger UI)

### Rate Limiting & Pagination
- Rate limiting protects against abuse and ensures fair usage
- Choose algorithm based on requirements (Fixed Window, Token Bucket)
- Pagination is essential for large datasets
- Cursor-based pagination offers better performance for deep pagination
- Always include clear metadata and navigation links

---

## Further Resources

### Books
- "RESTful Web Services" by Leonard Richardson and Sam Ruby
- "Designing Web APIs" by Arnaud Lauret
- "API Design Patterns" by JJ Geewax
- "Building Microservices" by Sam Newman

### Online Documentation
- **REST**: [REST API Tutorial](https://restfulapi.net/)
- **GraphQL**: [Official GraphQL Docs](https://graphql.org/)
- **OAuth2**: [OAuth 2.0 RFC](https://oauth.net/2/)
- **JWT**: [JWT.io](https://jwt.io/)
- **OpenAPI**: [OpenAPI Specification](https://swagger.io/specification/)

### Python Libraries
- **Flask**: https://flask.palletsprojects.com/
- **Django REST Framework**: https://www.django-rest-framework.org/
- **FastAPI**: https://fastapi.tiangolo.com/
- **Graphene**: https://docs.graphene-python.org/
- **Flask-Limiter**: https://flask-limiter.readthedocs.io/
- **PyJWT**: https://pyjwt.readthedocs.io/
- **Authlib**: https://docs.authlib.org/

### Tools
- **Postman**: API development and testing
- **Insomnia**: REST and GraphQL client
- **Swagger Editor**: OpenAPI specification editor
- **GraphiQL**: GraphQL IDE
- **httpie**: Command-line HTTP client

### Communities
- Stack Overflow: `python`, `rest`, `graphql`, `oauth`, `jwt` tags
- Reddit: r/Python, r/flask, r/django, r/webdev, r/api
- Discord: Python Discord, FastAPI Discord
- GitHub Discussions: Framework-specific repositories

---

*Last Updated: December 2025*
*Alex Alagoa Biobelemo*
