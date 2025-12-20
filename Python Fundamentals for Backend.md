# Python Fundamentals for Backend Development

## Table of Contents
- [Introduction](#introduction)
- [Core Data Structures](#core-data-structures)
- [Control Flow](#control-flow)
- [Object-Oriented Programming](#object-oriented-programming)
- [Functional Programming](#functional-programming)
- [Exception Handling](#exception-handling)
- [Modules and Packages](#modules-and-packages)

## Introduction

Backend development hinges on efficiently managing and processing data. Python's rich set of built-in data structures and powerful control flow mechanisms form the bedrock upon which robust and scalable backend applications are built. Understanding these fundamentals is crucial for any developer aiming to master Python for web frameworks like Django, Flask, or FastAPI, and for interacting with databases, APIs, and user inputs.

---

## Core Data Structures

Data structures are fundamental ways of organizing and storing data, enabling efficient access and modification. Python offers several versatile built-in data structures, each with distinct characteristics and optimal use cases in a backend context.

### 1. Lists

Python lists are ordered, mutable collections of items. They are incredibly versatile and can store elements of different data types.

**Characteristics:**
- **Ordered**: Elements maintain their insertion order
- **Mutable**: Elements can be added, removed, or modified after creation
- **Heterogeneous**: Can contain items of different data types (e.g., `[1, "hello", True]`)
- **Dynamic**: Can grow or shrink in size

**Backend Use Cases:**
- Storing collections of objects fetched from databases (PostgreSQL, MySQL)
- Implementing simple queues/stacks for task processing
- Managing log entries before persistence
- Accumulating request data (form fields, query parameters)

**Example:**
```python
# List of user IDs retrieved from a database query
user_ids = [101, 105, 112, 118]
print(f"User IDs: {user_ids}")

# Adding a new user ID
user_ids.append(120)
print(f"Updated User IDs: {user_ids}")

# Simulating a list of API errors
api_errors = [
    "Invalid API key",
    "Missing required field 'name'",
    "User not authorized"
]
print(f"API Errors: {api_errors}")

# List comprehension for data transformation
squared_ids = [id**2 for id in user_ids if id > 110]
print(f"Squared IDs (>110): {squared_ids}")
```

### 2. Dictionaries

Dictionaries are Python's primary way of storing data in key-value pairs. They are highly optimized for retrieving values using their unique keys.

**Characteristics:**
- **Key-Value Pairs**: Each value is associated with a unique key
- **Mutable**: Elements can be added, removed, or modified
- **Keys must be immutable and unique**: strings, numbers, tuples
- **Values can be of any type**: including other dictionaries or lists
- **Ordered (Python 3.7+)**: Insertion order is preserved

**Backend Use Cases:**
- Representing JSON objects in REST APIs (Flask, FastAPI)
- Configuration management (database credentials, API keys)
- User session storage (Django sessions)
- Caching with Redis or in-memory storage
- Parsing HTTP request parameters

**Example:**
```python
# User profile data, typically fetched from a database
user_profile = {
    "id": "abc-123",
    "username": "jane_doe",
    "email": "jane@example.com",
    "roles": ["admin", "editor"],
    "is_active": True
}
print(f"User Profile: {user_profile}")

# Accessing data
print(f"User email: {user_profile['email']}")

# Safe access with get() method
last_login = user_profile.get('last_login', 'Never')
print(f"Last login: {last_login}")

# Updating data
user_profile["email"] = "jane.doe@newdomain.com"
user_profile["last_login"] = "2023-10-27T10:30:00Z"
print(f"Updated Profile: {user_profile}")

# Configuration dictionary for Django ORM
db_config = {
    "ENGINE": "django.db.backends.postgresql",
    "NAME": "mydatabase",
    "USER": "myuser",
    "PASSWORD": "mypassword",
    "HOST": "localhost",
    "PORT": "5432",
}
print(f"Database Configuration: {db_config}")

# Dictionary methods
print(f"Keys: {list(user_profile.keys())}")
print(f"Values: {list(user_profile.values())}")
print(f"Items: {list(user_profile.items())}")
```

### 3. Sets

Sets are unordered collections of unique and immutable elements. They are particularly useful for membership testing and eliminating duplicate entries.

**Characteristics:**
- **Unordered**: Elements do not have a defined order
- **Mutable**: Sets can be modified (elements added/removed)
- **Unique Elements**: Duplicates are automatically removed
- **Support mathematical set operations**: union, intersection, difference, symmetric difference

**Backend Use Cases:**
- Tracking unique visitors/IP addresses
- Filtering duplicate data before database insertion
- Permission management and role checking
- Blacklisting/Whitelisting (JWT tokens, domains)

**Example:**
```python
# Set of unique user roles for an API endpoint
endpoint_roles = {"admin", "developer", "tester"}
print(f"Endpoint Roles: {endpoint_roles}")

# Simulating roles a user has
user_roles = {"developer", "user"}

# Checking if user has a required role
if "admin" in user_roles:
    print("User has admin role.")
else:
    print("User does not have admin role.")

# Finding common roles between endpoint and user
common_roles = endpoint_roles.intersection(user_roles)
print(f"Common roles: {common_roles}")  # Output: {'developer'}

# Storing unique resource IDs accessed (duplicates removed)
accessed_resources = {101, 205, 101, 312, 205}
print(f"Unique Accessed Resources: {accessed_resources}")  # Output: {101, 205, 312}

# Set operations
all_roles = endpoint_roles.union(user_roles)
exclusive_roles = endpoint_roles.symmetric_difference(user_roles)
print(f"All roles: {all_roles}")
print(f"Exclusive roles: {exclusive_roles}")

# Permission checking
required_permissions = {"read", "write"}
user_permissions = {"read", "write", "delete"}
has_required = required_permissions.issubset(user_permissions)
print(f"Has required permissions: {has_required}")
```

### 4. Tuples

Tuples are ordered, immutable collections of items. Once created, their elements cannot be changed.

**Characteristics:**
- **Ordered**: Elements maintain their insertion order
- **Immutable**: Elements cannot be added, removed, or modified after creation
- **Heterogeneous**: Can contain items of different data types
- **Fixed Size**: Cannot grow or shrink

**Backend Use Cases:**
- Returning multiple values from functions
- Dictionary keys (since tuples are immutable)
- Fixed configuration settings
- Database record representation
- Composite identifiers (order_id, item_id)

**Example:**
```python
# Function returning a tuple (status, message, data)
def fetch_user_data(user_id):
    if user_id == 1:
        return (200, "Success", {"id": 1, "name": "Alice"})
    else:
        return (404, "User not found", None)

status, message, data = fetch_user_data(1)
print(f"User 1 Data: Status={status}, Message='{message}', Data={data}")

status, message, data = fetch_user_data(2)
print(f"User 2 Data: Status={status}, Message='{message}', Data={data}")

# Using a tuple as a dictionary key for caching geographical data
cache = {}
location_key = (34.0522, -118.2437)  # (latitude, longitude)
cache[location_key] = {"city": "Los Angeles", "state": "CA"}
print(f"Cached Location: {cache[location_key]}")

# A tuple of allowed HTTP methods for a route
ALLOWED_METHODS = ("GET", "POST", "PUT", "DELETE")
if "GET" in ALLOWED_METHODS:
    print("GET method is allowed.")

# Tuple unpacking
coordinates = (40.7128, -74.0060, "New York")
lat, lon, city = coordinates
print(f"City: {city} at ({lat}, {lon})")

# Named tuples for better readability
from collections import namedtuple
User = namedtuple('User', ['id', 'username', 'email'])
user = User(1, 'john_doe', 'john@example.com')
print(f"User: {user.username}, Email: {user.email}")
```

---

## Control Flow

Control flow statements dictate the order in which instructions are executed within a program. They are essential for implementing logic, handling conditional scenarios, and iterating over data.

### 1. Conditional Statements (if, elif, else)

Conditional statements allow your program to make decisions based on certain conditions.

**Backend Use Cases:**
- Request routing based on HTTP method or URL parameters
- Authentication and authorization (JWT, OAuth2)
- Input validation for incoming request data
- Error handling with customized responses
- Feature flags for A/B testing

**Example:**
```python
# Simulating an API endpoint with authentication and authorization
def handle_request(method, path, user_role, auth_token):
    # Authentication check
    if not auth_token:
        return {"status": 401, "message": "Unauthorized: Missing token"}
    
    # Authorization check
    if user_role != "admin":
        return {"status": 403, "message": "Forbidden: Insufficient permissions"}
    
    # Route handling
    if method == "GET" and path == "/users":
        return {
            "status": 200,
            "data": [
                {"id": 1, "name": "Alice"},
                {"id": 2, "name": "Bob"}
            ]
        }
    elif method == "POST" and path == "/users":
        return {"status": 201, "message": "User created successfully"}
    else:
        return {"status": 404, "message": "Not Found"}

# Test cases
print(handle_request("GET", "/users", "admin", "valid_jwt_token"))
print(handle_request("POST", "/users", "viewer", "valid_jwt_token"))
print(handle_request("GET", "/products", "admin", "valid_jwt_token"))

# Input validation example
def validate_user_input(age, email):
    if not isinstance(age, int):
        return False, "Age must be an integer"
    elif age < 18:
        return False, "User must be 18 or older"
    elif not email or "@" not in email:
        return False, "Invalid email format"
    else:
        return True, "Valid input"

is_valid, message = validate_user_input(25, "user@example.com")
print(f"Validation: {message}")
```

### 2. For Loops

For loops iterate over sequences (lists, tuples, dictionaries, sets, strings) or other iterable objects.

**Backend Use Cases:**
- Processing database query results
- Batch processing of incoming data
- Data serialization/deserialization
- Generating dynamic responses

**Example:**
```python
# Processing a list of database records for an API response
db_records = [
    {"id": 1, "name": "Alice", "status": "active"},
    {"id": 2, "name": "Bob", "status": "inactive"},
    {"id": 3, "name": "Charlie", "status": "active"}
]

active_users = []
for record in db_records:
    if record["status"] == "active":
        active_users.append({
            "user_id": record["id"],
            "user_name": record["name"]
        })

print(f"Active Users for API: {active_users}")

# Iterating over dictionary items for configuration check
config_options = {"DEBUG": True, "HOST": "0.0.0.0", "PORT": 8000}
print("Current Configuration:")
for key, value in config_options.items():
    print(f"- {key}: {value}")

# Using enumerate for index tracking
users = ["Alice", "Bob", "Charlie"]
for index, user in enumerate(users, start=1):
    print(f"{index}. {user}")

# List comprehension (functional approach)
squared_numbers = [x**2 for x in range(1, 6)]
print(f"Squared: {squared_numbers}")

# Dictionary comprehension
user_dict = {f"user_{i}": i*10 for i in range(1, 4)}
print(f"User dict: {user_dict}")
```

### 3. While Loops

While loops repeat a block of code as long as a specified condition is true.

**Backend Use Cases:**
- Polling for task completion (Celery, async tasks)
- Retry logic with exponential backoff
- Message queue consumption
- Long-running background processes

**Example:**
```python
import time

# Simulating retrying an API call
attempt = 0
max_attempts = 3
api_call_success = False

while not api_call_success and attempt < max_attempts:
    attempt += 1
    print(f"Attempting API call... (Attempt {attempt})")
    
    try:
        # Simulate an API call that might fail
        if attempt == 3:  # Succeeds on third attempt
            api_call_success = True
            print("API call successful!")
        else:
            raise ConnectionError("Simulated network issue")
    except (ConnectionError, ValueError) as e:
        print(f"API call failed: {e}. Retrying in 2 seconds...")
        time.sleep(2)  # In production, use exponential backoff

if not api_call_success:
    print("Failed to call API after multiple attempts.")

# Exponential backoff example
def retry_with_backoff(max_retries=5):
    retry_count = 0
    backoff_time = 1
    
    while retry_count < max_retries:
        try:
            # Simulated operation
            if retry_count == 3:
                return "Success"
            raise Exception("Temporary failure")
        except Exception as e:
            retry_count += 1
            print(f"Attempt {retry_count} failed. Waiting {backoff_time}s...")
            time.sleep(backoff_time)
            backoff_time *= 2  # Exponential backoff
    
    return "Failed after all retries"

result = retry_with_backoff()
print(f"Result: {result}")
```

### 4. Loop Control Statements (break, continue)

These statements modify the normal flow of a loop.

**break**: Terminates the current loop entirely and execution resumes at the statement immediately following the loop.

**continue**: Skips the rest of the current iteration and proceeds to the next iteration.

**Backend Use Cases:**
- Early exit when search condition is met
- Skipping invalid entries in batch processing
- Resource management (exit on resource unavailability)
- Conditional processing

**Example:**
```python
# Processing a list of tasks with 'break' and 'continue'
tasks = [
    {"id": 1, "status": "pending", "priority": "high"},
    {"id": 2, "status": "completed", "priority": "medium"},
    {"id": 3, "status": "pending", "priority": "low"},
    {"id": 4, "status": "error", "priority": "critical"},
    {"id": 5, "status": "pending", "priority": "high"}
]

print("Processing tasks:")
for task in tasks:
    if task["status"] == "completed":
        print(f"  Skipping completed task {task['id']}")
        continue  # Move to the next task
    
    if task["status"] == "error":
        print(f"  Critical error on task {task['id']}. Stopping processing.")
        break  # Stop processing all further tasks
    
    print(f"  Processing pending task {task['id']} (Priority: {task['priority']})")

print("Task processing finished.")

# Finding first matching element
def find_user_by_id(users, target_id):
    for user in users:
        if user["id"] == target_id:
            return user  # Early exit
    return None  # Not found

users = [{"id": 1, "name": "Alice"}, {"id": 2, "name": "Bob"}]
user = find_user_by_id(users, 2)
print(f"Found user: {user}")
```

---

## Object-Oriented Programming

Python's object-oriented programming capabilities make it powerful for backend development, allowing developers to model real-world entities and create maintainable, scalable applications.

### Core OOP Concepts

#### 1. Classes and Objects

A class is a blueprint for creating objects, defining their attributes (data) and methods (functions). An object is an instance of a class.

**Example:**
```python
class User:
    """Represents a user in the system"""
    
    def __init__(self, user_id, username, email):
        self.user_id = user_id
        self.username = username
        self.email = email
        self.is_active = True
    
    def authenticate(self, password):
        """Authenticate user with password"""
        # In production, use proper password hashing
        return password == "secret123"
    
    def deactivate(self):
        """Deactivate user account"""
        self.is_active = False
        print(f"User {self.username} deactivated")
    
    def __str__(self):
        return f"User({self.username}, {self.email})"

# Creating instances
user1 = User(1, "alice", "alice@example.com")
user2 = User(2, "bob", "bob@example.com")

print(user1)
print(f"Authentication: {user1.authenticate('secret123')}")
user1.deactivate()
```

#### 2. Encapsulation

Encapsulation bundles data and methods within a class, restricting direct access to some components.

**Example:**
```python
class BankAccount:
    """Bank account with encapsulated balance"""
    
    def __init__(self, account_number, initial_balance=0):
        self.account_number = account_number
        self.__balance = initial_balance  # Private attribute
    
    @property
    def balance(self):
        """Get balance (read-only access)"""
        return self.__balance
    
    def deposit(self, amount):
        """Deposit money"""
        if amount > 0:
            self.__balance += amount
            return True
        return False
    
    def withdraw(self, amount):
        """Withdraw money"""
        if 0 < amount <= self.__balance:
            self.__balance -= amount
            return True
        return False

account = BankAccount("123456", 1000)
print(f"Balance: ${account.balance}")
account.deposit(500)
print(f"After deposit: ${account.balance}")
account.withdraw(200)
print(f"After withdrawal: ${account.balance}")
```

#### 3. Inheritance

Inheritance allows a new class to inherit attributes and methods from an existing class.

**Example:**
```python
class User:
    """Base user class"""
    
    def __init__(self, user_id, username, email):
        self.user_id = user_id
        self.username = username
        self.email = email
    
    def get_info(self):
        return f"User: {self.username}"

class AdminUser(User):
    """Admin user with additional privileges"""
    
    def __init__(self, user_id, username, email, admin_level):
        super().__init__(user_id, username, email)
        self.admin_level = admin_level
    
    def delete_user(self, user_id):
        """Admin privilege to delete users"""
        return f"Admin {self.username} deleted user {user_id}"
    
    def get_info(self):
        """Override parent method"""
        return f"Admin: {self.username} (Level {self.admin_level})"

# Using inheritance
regular_user = User(1, "alice", "alice@example.com")
admin_user = AdminUser(2, "bob", "bob@example.com", admin_level=5)

print(regular_user.get_info())
print(admin_user.get_info())
print(admin_user.delete_user(3))
```

#### 4. Polymorphism

Polymorphism allows different classes to respond to the same method call in their own specific ways.

**Example:**
```python
class DatabaseConnection:
    """Base class for database connections"""
    
    def connect(self):
        raise NotImplementedError("Subclass must implement connect()")
    
    def execute(self, query):
        raise NotImplementedError("Subclass must implement execute()")

class PostgreSQLConnection(DatabaseConnection):
    """PostgreSQL specific implementation"""
    
    def connect(self):
        return "Connected to PostgreSQL"
    
    def execute(self, query):
        return f"Executing PostgreSQL query: {query}"

class MongoDBConnection(DatabaseConnection):
    """MongoDB specific implementation"""
    
    def connect(self):
        return "Connected to MongoDB"
    
    def execute(self, query):
        return f"Executing MongoDB query: {query}"

# Polymorphic behavior
def perform_database_operation(db_connection, query):
    print(db_connection.connect())
    print(db_connection.execute(query))

postgres = PostgreSQLConnection()
mongodb = MongoDBConnection()

perform_database_operation(postgres, "SELECT * FROM users")
perform_database_operation(mongodb, "db.users.find({})")
```

### OOP in Backend Frameworks

#### Django Models Example
```python
from django.db import models

class Post(models.Model):
    """Blog post model"""
    title = models.CharField(max_length=200)
    content = models.TextField()
    author = models.ForeignKey('User', on_delete=models.CASCADE)
    created_at = models.DateTimeField(auto_now_add=True)
    
    class Meta:
        ordering = ['-created_at']
    
    def __str__(self):
        return self.title
```

#### FastAPI with Pydantic Models
```python
from pydantic import BaseModel, EmailStr
from typing import Optional

class UserCreate(BaseModel):
    """User creation schema"""
    username: str
    email: EmailStr
    password: str

class UserResponse(BaseModel):
    """User response schema"""
    id: int
    username: str
    email: EmailStr
    is_active: bool = True
    
    class Config:
        from_attributes = True
```

---

## Functional Programming

Functional Programming emphasizes computation as the evaluation of mathematical functions, avoiding changing state and mutable data.

### Core FP Concepts

#### 1. Pure Functions

Pure functions always return the same output for the same input and have no side effects.

**Example:**
```python
# Pure function
def calculate_total(price, tax_rate):
    """Calculate total price with tax"""
    return price * (1 + tax_rate)

# Impure function (modifies external state)
total = 0
def add_to_total(amount):
    global total
    total += amount
    return total

# Pure functions are predictable and testable
result1 = calculate_total(100, 0.08)
result2 = calculate_total(100, 0.08)
print(f"Results match: {result1 == result2}")  # Always True
```

#### 2. Higher-Order Functions

Functions that take other functions as arguments or return functions.

**Example:**
```python
# map() - applies function to all items
numbers = [1, 2, 3, 4, 5]
squared = list(map(lambda x: x**2, numbers))
print(f"Squared: {squared}")

# filter() - filters items based on condition
even_numbers = list(filter(lambda x: x % 2 == 0, numbers))
print(f"Even numbers: {even_numbers}")

# reduce() - reduces sequence to single value
from functools import reduce
sum_all = reduce(lambda x, y: x + y, numbers)
print(f"Sum: {sum_all}")

# Custom higher-order function
def apply_discount(discount_func):
    """Returns a function that applies discount"""
    def calculate_price(price):
        return discount_func(price)
    return calculate_price

# Using the higher-order function
ten_percent_off = apply_discount(lambda p: p * 0.9)
twenty_percent_off = apply_discount(lambda p: p * 0.8)

print(f"$100 with 10% off: ${ten_percent_off(100)}")
print(f"$100 with 20% off: ${twenty_percent_off(100)}")
```

#### 3. Decorators

Decorators are a powerful application of higher-order functions in Python.

**Example:**
```python
import time
from functools import wraps

def timer(func):
    """Decorator to time function execution"""
    @wraps(func)
    def wrapper(*args, **kwargs):
        start = time.time()
        result = func(*args, **kwargs)
        end = time.time()
        print(f"{func.__name__} took {end - start:.4f} seconds")
        return result
    return wrapper

def require_auth(func):
    """Decorator for authentication"""
    @wraps(func)
    def wrapper(user, *args, **kwargs):
        if not user.get('authenticated'):
            raise PermissionError("User not authenticated")
        return func(user, *args, **kwargs)
    return wrapper

@timer
def fetch_data():
    """Simulated data fetching"""
    time.sleep(1)
    return {"data": "sample"}

@require_auth
def delete_resource(user, resource_id):
    """Delete resource (requires authentication)"""
    return f"Deleted resource {resource_id}"

# Using decorated functions
data = fetch_data()
print(f"Fetched: {data}")

try:
    result = delete_resource({'authenticated': True}, 123)
    print(result)
except PermissionError as e:
    print(f"Error: {e}")
```

### FP in Backend Development

#### Data Transformation Pipeline
```python
# Functional approach to data processing
users = [
    {"name": "Alice", "age": 30, "active": True},
    {"name": "Bob", "age": 25, "active": False},
    {"name": "Charlie", "age": 35, "active": True},
]

# Functional pipeline
active_users = list(filter(lambda u: u['active'], users))
user_names = list(map(lambda u: u['name'], active_users))
sorted_names = sorted(user_names)

print(f"Active users: {sorted_names}")

# Using list comprehension (Pythonic approach)
active_names = sorted([u['name'] for u in users if u['active']])
print(f"Active users (comprehension): {active_names}")
```

---

## Exception Handling

Exception handling is critical for creating resilient backend services that can gracefully recover from unexpected events.

### Basic Exception Handling

**Example:**
```python
def divide_numbers(a, b):
    """Safely divide two numbers"""
    try:
        result = a / b
        return result
    except ZeroDivisionError:
        print("Error: Cannot divide by zero")
        return None
    except TypeError:
        print("Error: Invalid number types")
        return None
    finally:
        print("Division operation completed")

print(divide_numbers(10, 2))
print(divide_numbers(10, 0))
```

### Custom Exceptions

**Example:**
```python
class UserNotFoundException(Exception):
    """Raised when a user is not found"""
    pass

class InvalidPasswordError(Exception):
    """Raised when password validation fails"""
    pass

class DatabaseConnectionError(Exception):
    """Raised when database connection fails"""
    pass

def get_user(user_id):
    """Get user by ID"""
    users = {1: "Alice", 2: "Bob"}
    
    if user_id not in users:
        raise UserNotFoundException(f"User {user_id} not found")
    
    return users[user_id]

def validate_password(password):
    """Validate password strength"""
    if len(password) < 8:
        raise InvalidPasswordError("Password must be at least 8 characters")
    return True

# Using custom exceptions
try:
    user = get_user(3)
    print(f"Found user: {user}")
except UserNotFoundException as e:
    print(f"Error: {e}")

try:
    validate_password("weak")
except InvalidPasswordError as e:
    print(f"Validation error: {e}")
```

### Exception Handling in API Context

**Example:**
```python
import json

def handle_api_request(endpoint, data):
    """Simulate API request handling with comprehensive error handling"""
    try:
        # Validate input
        if not isinstance(data, dict):
            raise ValueError("Data must be a dictionary")
        
        # Simulate database operation
        if endpoint == "/users" and "email" not in data:
            raise KeyError("Missing required field: email")
        
        # Simulate successful operation
        return {
            "status": 200,
            "message": "Success",
            "data": data
        }
    
    except ValueError as e:
        return {"status": 400, "error": str(e)}
    
    except KeyError as e:
        return {"status": 422, "error": f"Validation error: {e}"}
    
    except Exception as e:
        # Catch-all for unexpected errors
        return {"status": 500, "error": "Internal server error"}
    
    finally:
        # Cleanup operations (logging, closing connections, etc.)
        print(f"Request to {endpoint} processed")

# Test error handling
response1 = handle_api_request("/users", {"email": "test@example.com"})
print(json.dumps(response1, indent=2))

response2 = handle_api_request("/users", {"name": "Alice"})
print(json.dumps(response2, indent=2))

response3 = handle_api_request("/users", "invalid data")
print(json.dumps(response3, indent=2))
```

### Context Managers

Context managers handle setup and cleanup automatically using `with` statements.

**Example:**
```python
class DatabaseConnection:
    """Context manager for database connections"""
    
    def __init__(self, db_name):
        self.db_name = db_name
        self.connection = None
    
    def __enter__(self):
        """Setup: establish connection"""
        print(f"Connecting to {self.db_name}...")
        self.connection = f"Connection to {self.db_name}"
        return self.connection
    
    def __exit__(self, exc_type, exc_val, exc_tb):
        """Cleanup: close connection"""
        print(f"Closing connection to {self.db_name}...")
        self.connection = None
        if exc_type:
            print(f"Exception occurred: {exc_val}")
        return False  # Don't suppress exceptions

# Using context manager
with DatabaseConnection("mydatabase") as conn:
    print(f"Using {conn}")
    # Work with connection
    # Connection automatically closed after block
```

---

## Modules and Packages

Modules and packages are essential for organizing code in larger backend projects.

### Modules

A module is a single Python file containing definitions and statements.

**Example Module Structure:**

```python
# file: database_utils.py
"""Database utility functions"""

def connect_to_db(db_name):
    """Connect to database"""
    return f"Connected to {db_name}"

def execute_query(query):
    """Execute database query"""
    return f"Executed: {query}"

def close_connection():
    """Close database connection"""
    return "Connection closed"

# Constants
DB_HOST = "localhost"
DB_PORT = 5432
```

```python
# file: main.py
"""Main application file"""

# Different import styles
import database_utils
from database_utils import connect_to_db, DB_HOST
from database_utils import *  # Not recommended

# Using imported functions
connection = database_utils.connect_to_db("mydb")
print(connection)

# Using specific imports
print(f"Host: {DB_HOST}")
result = connect_to_db("testdb")
print(result)
```

### Packages

A package is a directory containing multiple modules and an `__init__.py` file.

**Example Package Structure:**

```
my_backend/
│
├── __init__.py
├── models/
│   ├── __init__.py
│   ├── user.py
│   └── product.py
│
├── services/
│   ├── __init__.py
│   ├── auth.py
│   └── email.py
│
├── utils/
│   ├── __init__.py
│   ├── validators.py
│   └── helpers.py
│
└── api/
    ├── __init__.py
    ├── routes.py
    └── middleware.py
```

**Example Package Code:**

```python
# my_backend/models/user.py
class User:
    def __init__(self, username, email):
        self.username = username
        self.email = email

# my_backend/services/auth.py
def authenticate_user(username, password):
    """Authenticate user"""
    return username == "admin" and password == "secret"

# my_backend/__init__.py
"""Main package initialization"""
__version__ = "1.0.0"

from .models.user import User
from .services.auth import authenticate_user

# Usage in main.py
from my_backend import User, authenticate_user

user = User("alice", "alice@example.com")
is_authenticated = authenticate_user("admin", "secret")
```

### Best Practices

1. **Use relative imports within packages:**
```python
# Inside my_backend/services/email.py
from ..models.user import User  # Relative import
from .auth import authenticate_user  # Same level import
```

2. **Create clear `__init__.py` files:**
```python
# my_backend/models/__init__.py
from .user import User
from .product import Product

__all__ = ['User', 'Product']
```

3. **Organize by functionality:**
```
backend/
├── api/          # API routes and endpoints
├── models/       # Database models
├── services/     # Business logic
├── utils/        # Helper functions
└── config/       # Configuration files
```

---

## Conclusion

Mastering Python's core fundamentals is essential for building robust backend applications. The combination of:

- **Data Structures**: Lists, dictionaries, sets, and tuples for efficient data management
- **Control Flow**: Conditional statements and loops for implementing logic
- **OOP**: Classes, inheritance, and polymorphism for structured code
- **FP**: Pure functions and higher-order functions for predictable transformations
- **Exception Handling**: Try-except blocks and custom exceptions for resilience
- **Modules & Packages**: Organized code structure for maintainability

These concepts form the foundation for working with frameworks like Django, Flask, and FastAPI, and for building scalable, maintainable backend systems that interact with databases, APIs, and external services.

---

## Additional Resources

- [Python Official Documentation](https://docs.python.org/3/)
- [Real Python Tutorials](https://realpython.com/)
- [Django Documentation](https://docs.djangoproject.com/)
- [FastAPI Documentation](https://fastapi.tiangolo.com/)
- [Flask Documentation](https://flask.palletsprojects.com/)

---

*Last Updated: December 2025*
*Organized by Alex Alagoa Biobelemo*
