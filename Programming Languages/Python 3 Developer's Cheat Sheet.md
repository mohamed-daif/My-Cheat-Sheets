# Python 3 Developer's Complete Cheat Sheet

## Table of Contents
- [Python Basics](#python-basics)
- [Data Structures](#data-structures)
- [Functions & Scope](#functions--scope)
- [Object-Oriented Programming](#object-oriented-programming)
- [Error Handling](#error-handling)
- [File Handling](#file-handling)
- [Modules & Packages](#modules--packages)
- [Functional Programming](#functional-programming)
- [Decorators & Metaclasses](#decorators--metaclasses)
- [Concurrency & Parallelism](#concurrency--parallelism)
- [Web Development](#web-development)
- [Data Science & Analysis](#data-science--analysis)
- [Testing & Debugging](#testing--debugging)
- [Performance & Optimization](#performance--optimization)
- [Best Practices](#best-practices)

## Python Basics

### Variables & Data Types
```python
# Basic data types
integer = 42
float_num = 3.14
complex_num = 2 + 3j
string = "Hello, World!"
boolean = True
none_type = None

# Type checking and conversion
type(variable)                    # Check type
isinstance(variable, int)         # Check instance
int("123")                        # Convert to int
float("3.14")                     # Convert to float
str(123)                          # Convert to string
bool(1)                           # Convert to boolean

# Formatted strings (f-strings)
name = "Alice"
age = 30
message = f"My name is {name} and I am {age} years old"
calculation = f"2 + 2 = {2 + 2}"

# Multiple assignment
a, b, c = 1, 2, 3
x = y = z = 0

# Swapping variables
a, b = b, a
```

### Operators
```python
# Arithmetic operators
result = 10 + 5    # 15
result = 10 - 5    # 5
result = 10 * 5    # 50
result = 10 / 3    # 3.333... (float division)
result = 10 // 3   # 3 (integer division)
result = 10 % 3    # 1 (modulo)
result = 10 ** 2   # 100 (exponentiation)

# Comparison operators
5 == 5             # True
5 != 3             # True
5 > 3              # True
5 < 3              # False
5 >= 5             # True
5 <= 3             # False

# Logical operators
True and False     # False
True or False      # True
not True           # False

# Identity operators
a is b             # Same object
a is not b         # Different objects

# Membership operators
"a" in "abc"       # True
"x" not in "abc"   # True

# Walrus operator (Python 3.8+)
if (n := len([1,2,3])) > 2:
    print(f"List has {n} elements")
```

### Control Flow
```python
# If-elif-else
age = 20
if age < 18:
    print("Minor")
elif age < 65:
    print("Adult")
else:
    print("Senior")

# Ternary operator
status = "Adult" if age >= 18 else "Minor"

# While loop
count = 0
while count < 5:
    print(count)
    count += 1

# For loop
for i in range(5):           # 0 to 4
    print(i)

for i in range(1, 6):        # 1 to 5
    print(i)

for i in range(0, 10, 2):    # 0, 2, 4, 6, 8
    print(i)

# Loop control
for i in range(10):
    if i == 3:
        continue            # Skip iteration
    if i == 7:
        break               # Exit loop
    print(i)
else:
    print("Loop completed") # Executes if loop didn't break
```

## Data Structures

### Lists
```python
# List creation
numbers = [1, 2, 3, 4, 5]
mixed = [1, "hello", 3.14, True]
empty = []
from_range = list(range(5))  # [0, 1, 2, 3, 4]

# List operations
numbers.append(6)            # Add to end
numbers.insert(2, 99)        # Insert at index
numbers.extend([7, 8, 9])    # Extend with another list
numbers.remove(3)            # Remove first occurrence
popped = numbers.pop()       # Remove and return last item
popped = numbers.pop(2)      # Remove and return item at index
del numbers[0]               # Delete item at index
numbers.clear()              # Remove all items

# List methods
len(numbers)                 # Length
numbers.count(2)             # Count occurrences
numbers.index(4)             # Find index of value
numbers.sort()               # Sort in place
numbers.reverse()            # Reverse in place
sorted_numbers = sorted(numbers)  # Return new sorted list

# List slicing
numbers = [0, 1, 2, 3, 4, 5]
numbers[2]                   # 2
numbers[-1]                  # 5 (last element)
numbers[1:4]                 # [1, 2, 3]
numbers[::2]                 # [0, 2, 4] (every 2nd)
numbers[::-1]                # [5, 4, 3, 2, 1, 0] (reverse)

# List comprehension
squares = [x**2 for x in range(10)]
even_squares = [x**2 for x in range(10) if x % 2 == 0]
matrix = [[i*j for j in range(3)] for i in range(3)]
```

### Tuples
```python
# Tuple creation (immutable)
coordinates = (10, 20)
point = 30, 40              # Parentheses optional
single = (5,)               # Comma required for single element
empty = ()

# Tuple operations
x, y = coordinates          # Unpacking
first = coordinates[0]      # Indexing
length = len(coordinates)   # Length
contains = 10 in coordinates # Membership

# Named tuples
from collections import namedtuple
Point = namedtuple('Point', ['x', 'y'])
p = Point(10, 20)
print(p.x, p.y)             # 10 20
```

### Dictionaries
```python
# Dictionary creation
person = {"name": "Alice", "age": 30, "city": "New York"}
person = dict(name="Bob", age=25)  # Alternative syntax
empty = {}

# Dictionary operations
person["name"]              # Access value
person["email"] = "alice@example.com"  # Add/update
del person["age"]           # Delete key
"name" in person            # Check key existence

# Dictionary methods
person.keys()               # View of keys
person.values()             # View of values
person.items()              # View of key-value pairs
person.get("name")          # Safe access (returns None if missing)
person.get("phone", "N/A")  # With default value
person.pop("city")          # Remove and return value
person.update({"age": 31, "country": "USA"})  # Update multiple

# Dictionary comprehension
squares = {x: x**2 for x in range(5)}
swapped = {v: k for k, v in person.items()}
filtered = {k: v for k, v in person.items() if len(k) > 3}
```

### Sets
```python
# Set creation (unique, unordered elements)
numbers = {1, 2, 3, 4, 5}
numbers = set([1, 2, 2, 3, 3, 4])  # {1, 2, 3, 4}
empty = set()

# Set operations
numbers.add(6)              # Add element
numbers.remove(3)           # Remove element (error if missing)
numbers.discard(3)          # Remove element (no error)
popped = numbers.pop()      # Remove and return arbitrary element

# Set operations
a = {1, 2, 3}
b = {3, 4, 5}
a | b                       # Union: {1, 2, 3, 4, 5}
a & b                       # Intersection: {3}
a - b                       # Difference: {1, 2}
a ^ b                       # Symmetric difference: {1, 2, 4, 5}

# Set comprehension
unique_chars = {char for char in "hello world"}  # {'h', 'e', 'l', 'o', ' ', 'w', 'r', 'd'}
```

### Strings
```python
# String operations
text = "Hello, World!"
length = len(text)          # 13
text.upper()                # "HELLO, WORLD!"
text.lower()                # "hello, world!"
text.strip()                # Remove whitespace
text.replace("Hello", "Hi") # "Hi, World!"
text.split(",")             # ['Hello', ' World!']
", ".join(["a", "b", "c"])  # "a, b, c"

# String formatting
name = "Alice"
f"Hello, {name}!"           # f-string (Python 3.6+)
"Hello, {}!".format(name)   # format method
"Hello, %s!" % name         # % formatting

# String methods
text.startswith("Hello")    # True
text.endswith("!")          # True
text.find("World")          # 7 (index, -1 if not found)
text.count("l")             # 3
text.isalpha()              # Check if all alphabetic
text.isdigit()              # Check if all digits

# String slicing (similar to lists)
text[0:5]                   # "Hello"
text[7:]                    # "World!"
text[::-1]                  # "!dlroW ,olleH"
```

## Functions & Scope

### Function Definitions
```python
# Basic function
def greet(name):
    return f"Hello, {name}!"

# Function with default arguments
def power(base, exponent=2):
    return base ** exponent

# Function with type hints (Python 3.5+)
def add(a: int, b: int) -> int:
    return a + b

# Function with variable arguments
def sum_numbers(*args):
    return sum(args)

def print_details(**kwargs):
    for key, value in kwargs.items():
        print(f"{key}: {value}")

def mixed_args(a, b, *args, **kwargs):
    print(f"a={a}, b={b}")
    print(f"args={args}")
    print(f"kwargs={kwargs}")

# Lambda functions
square = lambda x: x ** 2
add = lambda x, y: x + y

# Call functions
result = greet("Alice")
result = power(3)           # 9 (uses default exponent)
result = power(2, 3)        # 8
result = sum_numbers(1, 2, 3, 4)  # 10
print_details(name="Alice", age=30)
```

### Scope & Closures
```python
# Global scope
global_var = "I'm global"

def function():
    # Local scope
    local_var = "I'm local"
    print(global_var)  # Can access global
    
    # Modifying global variable
    global global_var
    global_var = "Modified global"

# Nonlocal for nested functions
def outer():
    count = 0
    
    def inner():
        nonlocal count  # Refer to outer function's variable
        count += 1
        return count
    
    return inner

# Closure example
def make_multiplier(factor):
    def multiplier(x):
        return x * factor
    return multiplier

double = make_multiplier(2)
triple = make_multiplier(3)
print(double(5))  # 10
print(triple(5))  # 15
```

## Object-Oriented Programming

### Classes & Objects
```python
# Basic class
class Dog:
    # Class attribute
    species = "Canis familiaris"
    
    # Initializer
    def __init__(self, name, age):
        # Instance attributes
        self.name = name
        self.age = age
    
    # Instance method
    def description(self):
        return f"{self.name} is {self.age} years old"
    
    def speak(self, sound):
        return f"{self.name} says {sound}"

# Creating objects
buddy = Dog("Buddy", 9)
print(buddy.description())  # Buddy is 9 years old
print(buddy.speak("Woof"))  # Buddy says Woof
```

### Inheritance & Polymorphism
```python
# Inheritance
class GermanShepherd(Dog):
    def __init__(self, name, age, working_dog=False):
        # Call parent initializer
        super().__init__(name, age)
        self.working_dog = working_dog
    
    # Method overriding
    def speak(self, sound="Loud Woof"):
        return super().speak(sound)
    
    # Additional method
    def work(self):
        if self.working_dog:
            return f"{self.name} is working"
        return f"{self.name} is not a working dog"

# Multiple inheritance
class A:
    def method(self):
        return "A"

class B:
    def method(self):
        return "B"

class C(A, B):  # Method Resolution Order (MRO)
    pass

# Abstract Base Classes
from abc import ABC, abstractmethod

class Animal(ABC):
    @abstractmethod
    def speak(self):
        pass

class Cat(Animal):
    def speak(self):
        return "Meow"
```

### Properties & Special Methods
```python
class Rectangle:
    def __init__(self, width, height):
        self._width = width
        self._height = height
    
    # Property (getter)
    @property
    def width(self):
        return self._width
    
    # Property setter
    @width.setter
    def width(self, value):
        if value <= 0:
            raise ValueError("Width must be positive")
        self._width = value
    
    @property
    def area(self):
        return self._width * self._height
    
    # Special methods
    def __str__(self):
        return f"Rectangle({self._width}x{self._height})"
    
    def __repr__(self):
        return f"Rectangle({self._width}, {self._height})"
    
    def __eq__(self, other):
        if isinstance(other, Rectangle):
            return self._width == other._width and self._height == other._height
        return False
    
    def __lt__(self, other):
        if isinstance(other, Rectangle):
            return self.area < other.area
        return NotImplemented

# Using properties
rect = Rectangle(10, 5)
print(rect.area)    # 50 (computed property)
rect.width = 15     # Uses setter
print(rect.area)    # 75
```

### Dataclasses (Python 3.7+)
```python
from dataclasses import dataclass, field
from typing import List

@dataclass
class Person:
    name: str
    age: int
    email: str = ""  # Default value
    hobbies: List[str] = field(default_factory=list)
    
    @property
    def display_name(self):
        return f"{self.name} ({self.age})"

# Automatic __init__, __repr__, __eq__ generated
person = Person("Alice", 30, "alice@example.com")
print(person)  # Person(name='Alice', age=30, email='alice@example.com', hobbies=[])
```

## Error Handling

### Exceptions
```python
# Basic try-except
try:
    result = 10 / 0
except ZeroDivisionError:
    print("Cannot divide by zero!")

# Multiple exceptions
try:
    # Some code that might raise exceptions
    value = int("not a number")
except (ValueError, TypeError) as e:
    print(f"Error: {e}")

# Specific exception handling
try:
    file = open("nonexistent.txt", "r")
except FileNotFoundError:
    print("File not found")
except PermissionError:
    print("Permission denied")
except Exception as e:
    print(f"Unexpected error: {e}")
else:
    print("File opened successfully")
    file.close()
finally:
    print("This always executes")

# Raising exceptions
def validate_age(age):
    if age < 0:
        raise ValueError("Age cannot be negative")
    if age > 150:
        raise ValueError("Age seems unrealistic")
    return age

# Custom exceptions
class CustomError(Exception):
    def __init__(self, message, code):
        super().__init__(message)
        self.code = code

try:
    raise CustomError("Something went wrong", 500)
except CustomError as e:
    print(f"Error {e.code}: {e}")
```

### Context Managers
```python
# Using context managers (with statement)
with open("file.txt", "r") as file:
    content = file.read()
# File automatically closed

# Custom context manager
class Timer:
    def __init__(self, name):
        self.name = name
    
    def __enter__(self):
        self.start = time.time()
        return self
    
    def __exit__(self, exc_type, exc_val, exc_tb):
        self.end = time.time()
        print(f"{self.name} took {self.end - self.start:.2f} seconds")

# Using custom context manager
with Timer("My Operation"):
    time.sleep(1)
    # Some operation

# Context manager with contextlib
from contextlib import contextmanager

@contextmanager
def temporary_change(obj, attr, value):
    original = getattr(obj, attr)
    setattr(obj, attr, value)
    try:
        yield
    finally:
        setattr(obj, attr, original)
```

## File Handling

### Reading & Writing Files
```python
# Reading files
with open("file.txt", "r") as file:
    content = file.read()           # Read entire file
    lines = file.readlines()        # Read all lines into list

# Reading line by line (memory efficient)
with open("large_file.txt", "r") as file:
    for line in file:
        process_line(line)

# Writing files
with open("output.txt", "w") as file:
    file.write("Hello, World!\n")
    file.writelines(["Line 1\n", "Line 2\n"])

# Appending to files
with open("log.txt", "a") as file:
    file.write("New log entry\n")

# Different file modes
# "r" - Read (default)
# "w" - Write (truncates)
# "a" - Append
# "x" - Exclusive creation
# "b" - Binary mode
# "t" - Text mode (default)
# "+" - Reading and writing
```

### Working with Paths
```python
from pathlib import Path

# Creating Path objects
current_dir = Path(".")
home_dir = Path.home()
absolute_path = Path("/usr/bin/python3")

# Path operations
path = Path("folder") / "subfolder" / "file.txt"
path.exists()                    # Check if path exists
path.is_file()                   # Check if it's a file
path.is_dir()                    # Check if it's a directory
path.name                        # "file.txt"
path.parent                      # Path("folder/subfolder")
path.suffix                      # ".txt"
path.stem                        # "file"

# Reading/writing with pathlib
path = Path("data.txt")
content = path.read_text()
path.write_text("New content")

# Working with directories
dir_path = Path("my_directory")
dir_path.mkdir(exist_ok=True)    # Create directory
list(dir_path.iterdir())         # List directory contents
list(dir_path.glob("*.txt"))     # Find files by pattern
list(dir_path.rglob("*.txt"))    # Recursive glob
```

## Modules & Packages

### Creating Modules
```python
# mymodule.py
"""This is a module docstring."""

MODULE_CONSTANT = 42

def module_function():
    return "Hello from module"

class MyClass:
    def __init__(self, value):
        self.value = value
    
    def display(self):
        return f"Value: {self.value}"

# Conditional execution
if __name__ == "__main__":
    # Code that runs only when module is executed directly
    print("Module executed directly")
```

### Importing
```python
# Various import styles
import mymodule
from mymodule import MyClass, module_function
from mymodule import MyClass as MC
import mymodule as mm
from mymodule import *  # Generally discouraged

# Package structure
# mypackage/
#   __init__.py
#   module1.py
#   module2.py
#   subpackage/
#     __init__.py
#     module3.py

# Importing from packages
from mypackage import module1
from mypackage.module2 import some_function
from mypackage.subpackage.module3 import AnotherClass

# Relative imports (within package)
# from . import module1
# from .module2 import function
# from ..otherpackage import something
```

### Virtual Environments & Dependencies
```bash
# Creating virtual environment
python -m venv myenv

# Activating (Windows)
myenv\Scripts\activate

# Activating (Unix/MacOS)
source myenv/bin/activate

# Deactivating
deactivate

# Managing dependencies
pip install requests
pip install requests==2.25.1
pip install -r requirements.txt
pip freeze > requirements.txt
pip uninstall package_name
```

## Functional Programming

### Higher-Order Functions
```python
# Using built-in higher-order functions
numbers = [1, 2, 3, 4, 5]

# map - apply function to all elements
squared = list(map(lambda x: x**2, numbers))  # [1, 4, 9, 16, 25]

# filter - filter elements based on condition
evens = list(filter(lambda x: x % 2 == 0, numbers))  # [2, 4]

# reduce - cumulative operation
from functools import reduce
product = reduce(lambda x, y: x * y, numbers)  # 120

# sorted with custom key
words = ["apple", "banana", "cherry"]
sorted_by_length = sorted(words, key=len)  # ['apple', 'cherry', 'banana']
sorted_case_insensitive = sorted(words, key=str.lower)
```

### Iterators & Generators
```python
# Generator function
def count_up_to(max):
    count = 1
    while count <= max:
        yield count
        count += 1

# Using generator
counter = count_up_to(5)
for num in counter:
    print(num)  # 1, 2, 3, 4, 5

# Generator expression
squares = (x**2 for x in range(10))
for square in squares:
    print(square)

# Infinite generator
def fibonacci():
    a, b = 0, 1
    while True:
        yield a
        a, b = b, a + b

fib = fibonacci()
for _ in range(10):
    print(next(fib))
```

### Functional Tools
```python
from functools import partial, lru_cache

# Partial function application
def power(base, exponent):
    return base ** exponent

square = partial(power, exponent=2)
cube = partial(power, exponent=3)

print(square(5))  # 25
print(cube(3))    # 27

# Memoization with lru_cache
@lru_cache(maxsize=128)
def fibonacci(n):
    if n < 2:
        return n
    return fibonacci(n-1) + fibonacci(n-2)

# Function composition
def compose(f, g):
    return lambda x: f(g(x))

add_one = lambda x: x + 1
multiply_by_two = lambda x: x * 2
add_then_multiply = compose(multiply_by_two, add_one)
print(add_then_multiply(5))  # 12
```

## Decorators & Metaclasses

### Decorators
```python
# Basic decorator
def my_decorator(func):
    def wrapper(*args, **kwargs):
        print("Something is happening before the function is called.")
        result = func(*args, **kwargs)
        print("Something is happening after the function is called.")
        return result
    return wrapper

@my_decorator
def say_hello():
    print("Hello!")

# Decorator with arguments
def repeat(num_times):
    def decorator_repeat(func):
        def wrapper(*args, **kwargs):
            for _ in range(num_times):
                result = func(*args, **kwargs)
            return result
        return wrapper
    return decorator_repeat

@repeat(num_times=3)
def greet(name):
    print(f"Hello {name}")

# Class decorator
def singleton(cls):
    instances = {}
    def get_instance(*args, **kwargs):
        if cls not in instances:
            instances[cls] = cls(*args, **kwargs)
        return instances[cls]
    return get_instance

@singleton
class Database:
    pass

# Using functools.wraps to preserve metadata
from functools import wraps

def debug(func):
    @wraps(func)
    def wrapper(*args, **kwargs):
        print(f"Calling {func.__name__} with {args} {kwargs}")
        return func(*args, **kwargs)
    return wrapper
```

### Metaclasses
```python
# Basic metaclass
class Meta(type):
    def __new__(cls, name, bases, dct):
        # Modify class creation
        dct['created_by'] = 'Meta'
        return super().__new__(cls, name, bases, dct)

class MyClass(metaclass=Meta):
    pass

print(MyClass.created_by)  # 'Meta'

# Singleton with metaclass
class Singleton(type):
    _instances = {}
    
    def __call__(cls, *args, **kwargs):
        if cls not in cls._instances:
            cls._instances[cls] = super().__call__(*args, **kwargs)
        return cls._instances[cls]

class Logger(metaclass=Singleton):
    def __init__(self):
        self.messages = []
    
    def log(self, message):
        self.messages.append(message)
```

## Concurrency & Parallelism

### Threading
```python
import threading
import time

# Basic thread
def print_numbers():
    for i in range(5):
        time.sleep(1)
        print(i)

thread = threading.Thread(target=print_numbers)
thread.start()
thread.join()  # Wait for thread to complete

# Thread with arguments
def worker(name, delay):
    time.sleep(delay)
    print(f"Worker {name} completed")

threads = []
for i in range(3):
    t = threading.Thread(target=worker, args=(f"Thread-{i}", i))
    threads.append(t)
    t.start()

for t in threads:
    t.join()

# Thread synchronization
lock = threading.Lock()
shared_data = 0

def increment():
    global shared_data
    with lock:
        shared_data += 1
```

### Multiprocessing
```python
import multiprocessing
import time

# Basic process
def cpu_intensive_task(n):
    return sum(i * i for i in range(n))

# Sequential execution
start = time.time()
results = [cpu_intensive_task(10**6) for _ in range(4)]
print(f"Sequential: {time.time() - start:.2f}s")

# Parallel execution
start = time.time()
with multiprocessing.Pool(processes=4) as pool:
    results = pool.map(cpu_intensive_task, [10**6] * 4)
print(f"Parallel: {time.time() - start:.2f}s")

# Process with shared memory
def worker(shared_value, lock):
    with lock:
        shared_value.value += 1

shared_value = multiprocessing.Value('i', 0)
lock = multiprocessing.Lock()

processes = []
for _ in range(10):
    p = multiprocessing.Process(target=worker, args=(shared_value, lock))
    processes.append(p)
    p.start()

for p in processes:
    p.join()

print(f"Final value: {shared_value.value}")
```

### Asynchronous Programming
```python
import asyncio

# Basic async function
async def fetch_data():
    print("Start fetching")
    await asyncio.sleep(2)  # Simulate I/O operation
    print("Done fetching")
    return {"data": 1}

async def main():
    # Run sequentially
    await fetch_data()
    await fetch_data()
    
    # Run concurrently
    await asyncio.gather(fetch_data(), fetch_data())

# Using asyncio.run()
asyncio.run(main())

# Async context manager
class AsyncConnection:
    async def __aenter__(self):
        print("Connecting...")
        await asyncio.sleep(1)
        return self
    
    async def __aexit__(self, exc_type, exc_val, exc_tb):
        print("Disconnecting...")
        await asyncio.sleep(0.5)
    
    async def execute(self, query):
        await asyncio.sleep(0.5)
        return f"Result of {query}"

async def use_connection():
    async with AsyncConnection() as conn:
        result = await conn.execute("SELECT * FROM table")
        print(result)
```

## Web Development

### Flask Web Framework
```python
from flask import Flask, request, jsonify, render_template

app = Flask(__name__)

@app.route('/')
def home():
    return "Hello, World!"

@app.route('/user/<name>')
def user_profile(name):
    return f"Hello, {name}!"

@app.route('/api/data', methods=['GET', 'POST'])
def api_data():
    if request.method == 'POST':
        data = request.get_json()
        return jsonify({"status": "success", "received": data})
    else:
        return jsonify({"data": [1, 2, 3, 4, 5]})

@app.route('/template')
def template_example():
    return render_template('index.html', name='Alice')

if __name__ == '__main__':
    app.run(debug=True)
```

### Requests Library
```python
import requests

# GET request
response = requests.get('https://api.github.com/users/octocat')
data = response.json()
status_code = response.status_code

# POST request
payload = {'key1': 'value1', 'key2': 'value2'}
response = requests.post('https://httpbin.org/post', data=payload)

# With authentication
response = requests.get(
    'https://api.github.com/user',
    auth=('username', 'password')
)

# With headers
headers = {'User-Agent': 'my-app/1.0'}
response = requests.get('https://api.example.com', headers=headers)

# Error handling
try:
    response = requests.get('https://api.example.com', timeout=5)
    response.raise_for_status()  # Raises exception for bad status codes
except requests.exceptions.RequestException as e:
    print(f"Request failed: {e}")
```

### FastAPI (Modern Alternative)
```python
from fastapi import FastAPI, HTTPException
from pydantic import BaseModel
from typing import Optional

app = FastAPI()

class Item(BaseModel):
    name: str
    price: float
    is_offer: Optional[bool] = None

@app.get("/")
def read_root():
    return {"Hello": "World"}

@app.get("/items/{item_id}")
def read_item(item_id: int, q: Optional[str] = None):
    return {"item_id": item_id, "q": q}

@app.put("/items/{item_id}")
def update_item(item_id: int, item: Item):
    return {"item_name": item.name, "item_id": item_id}

# Run with: uvicorn main:app --reload
```

## Data Science & Analysis

### NumPy Basics
```python
import numpy as np

# Creating arrays
arr1 = np.array([1, 2, 3, 4, 5])
arr2 = np.arange(0, 10, 2)  # [0, 2, 4, 6, 8]
arr3 = np.linspace(0, 1, 5)  # [0., 0.25, 0.5, 0.75, 1.]
zeros = np.zeros((3, 3))
ones = np.ones((2, 4))
identity = np.eye(3)

# Array operations
arr = np.array([[1, 2, 3], [4, 5, 6]])
arr.shape                    # (2, 3)
arr.ndim                     # 2
arr.size                     # 6
arr.dtype                    # dtype('int64')

# Mathematical operations
arr + 2                     # Element-wise addition
arr * 3                     # Element-wise multiplication
np.sqrt(arr)                # Square root
np.sin(arr)                 # Trigonometric functions
arr.sum()                   # Sum of all elements
arr.mean()                  # Mean
arr.std()                   # Standard deviation

# Indexing and slicing
arr[0, 1]                   # 2
arr[1, :]                   # [4, 5, 6] (second row)
arr[:, 1]                   # [2, 5] (second column)
arr[0:2, 1:3]               # [[2, 3], [5, 6]]
```

### Pandas Basics
```python
import pandas as pd

# Creating DataFrames
df = pd.DataFrame({
    'Name': ['Alice', 'Bob', 'Charlie'],
    'Age': [25, 30, 35],
    'City': ['New York', 'London', 'Tokyo']
})

# From CSV
df = pd.read_csv('data.csv')
df.to_csv('output.csv', index=False)

# Basic operations
df.head()                    # First 5 rows
df.info()                    # DataFrame info
df.describe()                # Statistical summary
df.shape                     # (rows, columns)
df.columns                   # Column names
df.dtypes                    # Data types

# Selection and filtering
df['Name']                   # Single column
df[['Name', 'Age']]          # Multiple columns
df[df['Age'] > 30]           # Filter rows
df[(df['Age'] > 25) & (df['City'] == 'London')]  # Multiple conditions

# Data manipulation
df['Age'] = df['Age'] + 1    # Modify column
df['Salary'] = [50000, 60000, 70000]  # Add new column
df = df.drop('City', axis=1) # Remove column

# Grouping and aggregation
df.groupby('City')['Age'].mean()  # Average age by city
df.groupby('City').agg({
    'Age': ['mean', 'min', 'max'],
    'Salary': 'sum'
})
```

### Matplotlib & Seaborn
```python
import matplotlib.pyplot as plt
import seaborn as sns

# Basic line plot
plt.plot([1, 2, 3, 4], [1, 4, 2, 3])
plt.xlabel('X axis')
plt.ylabel('Y axis')
plt.title('Simple Plot')
plt.show()

# Scatter plot
plt.scatter(df['Age'], df['Salary'])
plt.show()

# Histogram
plt.hist(df['Age'], bins=10)
plt.show()

# Using Seaborn for statistical plots
sns.scatterplot(x='Age', y='Salary', data=df)
sns.boxplot(x='City', y='Age', data=df)
sns.heatmap(df.corr(), annot=True)  # Correlation heatmap

# Subplots
fig, (ax1, ax2) = plt.subplots(1, 2, figsize=(10, 4))
ax1.plot([1, 2, 3, 4], [1, 4, 2, 3])
ax2.scatter([1, 2, 3, 4], [1, 4, 2, 3])
plt.show()
```

## Testing & Debugging

### Unit Testing with unittest
```python
import unittest

def add(a, b):
    return a + b

class TestMathOperations(unittest.TestCase):
    def test_add_positive_numbers(self):
        self.assertEqual(add(2, 3), 5)
    
    def test_add_negative_numbers(self):
        self.assertEqual(add(-1, -1), -2)
    
    def test_add_zero(self):
        self.assertEqual(add(0, 5), 5)
    
    def test_add_type_error(self):
        with self.assertRaises(TypeError):
            add("2", 3)

# Run tests
if __name__ == '__main__':
    unittest.main()
```

### pytest Framework
```python
# test_math.py
import pytest

def add(a, b):
    return a + b

def test_add_positive():
    assert add(2, 3) == 5

def test_add_negative():
    assert add(-1, -1) == -2

@pytest.mark.parametrize("a,b,expected", [
    (1, 2, 3),
    (0, 0, 0),
    (-1, 1, 0),
])
def test_add_parametrized(a, b, expected):
    assert add(a, b) == expected

# Fixtures
@pytest.fixture
def sample_data():
    return [1, 2, 3, 4, 5]

def test_sum(sample_data):
    assert sum(sample_data) == 15
```

### Debugging
```python
# Using pdb debugger
import pdb

def problematic_function(data):
    pdb.set_trace()  # Breakpoint
    result = []
    for item in data:
        processed = item * 2  # This might fail
        result.append(processed)
    return result

# Common pdb commands
# n - next line
# s - step into function
# c - continue
# l - list code
# p variable - print variable
# q - quit

# Logging for debugging
import logging

logging.basicConfig(
    level=logging.DEBUG,
    format='%(asctime)s - %(name)s - %(levelname)s - %(message)s'
)

logger = logging.getLogger(__name__)

def complex_operation(x, y):
    logger.debug(f"Starting operation with x={x}, y={y}")
    try:
        result = x / y
        logger.info(f"Operation successful: {result}")
        return result
    except Exception as e:
        logger.error(f"Operation failed: {e}")
        raise
```

## Performance & Optimization

### Profiling
```python
import cProfile
import time

def slow_function():
    total = 0
    for i in range(1000000):
        total += i
    return total

def fast_function():
    return sum(range(1000000))

# Using cProfile
cProfile.run('slow_function()')
cProfile.run('fast_function()')

# Timeit for small code snippets
import timeit

time_slow = timeit.timeit('slow_function()', 
                         setup='from __main__ import slow_function', 
                         number=100)
time_fast = timeit.timeit('fast_function()', 
                         setup='from __main__ import fast_function', 
                         number=100)

print(f"Slow: {time_slow:.4f}s")
print(f"Fast: {time_fast:.4f}s")
```

### Optimization Techniques
```python
# Use list comprehensions instead of loops
# Slow
result = []
for i in range(1000):
    if i % 2 == 0:
        result.append(i * 2)

# Fast
result = [i * 2 for i in range(1000) if i % 2 == 0]

# Use generator expressions for large data
# Memory efficient
large_sum = sum(x * x for x in range(1000000))

# Use local variables in loops
def slow_loop(data):
    result = []
    for item in data:
        result.append(math.sqrt(item))  # Global lookup each time
    return result

def fast_loop(data):
    result = []
    sqrt = math.sqrt  # Local variable
    for item in data:
        result.append(sqrt(item))  # Fast local lookup
    return result

# Use built-in functions
# Slow
total = 0
for num in numbers:
    total += num

# Fast
total = sum(numbers)
```

### Memory Management
```python
import sys
import gc

# Check memory usage
x = [i for i in range(100000)]
print(f"Memory usage: {sys.getsizeof(x)} bytes")

# Force garbage collection
gc.collect()

# Using weak references for caching
import weakref

class ExpensiveObject:
    def __init__(self, name):
        self.name = name

# Regular dict (strong reference)
cache = {}
obj = ExpensiveObject("test")
cache['key'] = obj  # Prevents obj from being garbage collected

# WeakValueDictionary (weak reference)
weak_cache = weakref.WeakValueDictionary()
weak_cache['key'] = obj  # obj can be garbage collected when no other references exist

# Memory profiling with memory_profiler
# pip install memory-profiler

from memory_profiler import profile

@profile
def memory_intensive_function():
    large_list = [i for i in range(1000000)]
    return sum(large_list)
```

## Best Practices

### Code Style (PEP 8)
```python
# Naming conventions
variable_name = "value"           # snake_case for variables
CONSTANT_NAME = "value"           # UPPERCASE for constants
function_name()                   # snake_case for functions
ClassName                        # PascalCase for classes
module_name.py                   # lowercase for modules

# Import order
import os                         # Standard library
import sys

import requests                   # Third-party packages
import numpy as np

from mymodule import MyClass      # Local imports

# Line length and formatting
# Maximum 79 characters per line
long_variable_name = (
    "This is a very long string that exceeds "
    "the recommended line length limit"
)

# Use meaningful names
# Bad
x = 10
lst = [1, 2, 3]

# Good
max_retries = 10
user_ids = [1, 2, 3]
```

### Documentation
```python
def calculate_area(length, width):
    """
    Calculate the area of a rectangle.
    
    Args:
        length (float): The length of the rectangle
        width (float): The width of the rectangle
    
    Returns:
        float: The area of the rectangle
    
    Raises:
        ValueError: If length or width is negative
    
    Example:
        >>> calculate_area(5, 3)
        15.0
    """
    if length < 0 or width < 0:
        raise ValueError("Length and width must be non-negative")
    return length * width

class BankAccount:
    """A simple bank account class.
    
    Attributes:
        balance (float): The current account balance
        owner (str): The account owner's name
    """
    
    def __init__(self, owner, initial_balance=0):
        self.owner = owner
        self.balance = initial_balance
    
    def deposit(self, amount):
        """Deposit money into the account."""
        if amount <= 0:
            raise ValueError("Deposit amount must be positive")
        self.balance += amount
```

### Error Handling Patterns
```python
# Use specific exceptions
try:
    value = int(user_input)
except ValueError:
    print("Please enter a valid number")

# Don't use bare except
try:
    risky_operation()
except:  # Too broad - catches KeyboardInterrupt, SystemExit, etc.
    pass

# Better
try:
    risky_operation()
except Exception:  # Still broad, but better than bare except
    pass

# Best - catch specific exceptions
try:
    risky_operation()
except (ValueError, TypeError) as e:
    print(f"Operation failed: {e}")

# Use context managers for resource cleanup
with open('file.txt', 'r') as f:
    content = f.read()
# File automatically closed

# Create custom exceptions for your domain
class InsufficientFundsError(Exception):
    """Raised when account has insufficient funds."""
    pass

class BankAccount:
    def withdraw(self, amount):
        if amount > self.balance:
            raise InsufficientFundsError("Insufficient funds")
        self.balance -= amount
```

---

## Quick Reference Card

### Most Used Built-in Functions
```python
len(obj)                        # Length of object
range(stop)                     # Generate sequence
enumerate(iterable)             # Add counter to iterable
zip(*iterables)                 # Aggregate iterables
sorted(iterable)                # Return sorted list
reversed(sequence)              # Return reversed iterator
isinstance(obj, class)          # Check object type
hasattr(obj, 'attr')            # Check attribute existence
getattr(obj, 'attr', default)   # Get attribute with default
setattr(obj, 'attr', value)     # Set attribute
```

### Common String Methods
```python
str.strip()                     # Remove whitespace
str.split(sep)                  # Split into list
str.join(iterable)              # Join iterable with string
str.replace(old, new)           # Replace substring
str.startswith(prefix)          # Check prefix
str.endswith(suffix)            # Check suffix
str.find(sub)                   # Find substring index
str.lower()                     # Convert to lowercase
str.upper()                     # Convert to uppercase
str.isdigit()                   # Check if all digits
```

### Essential List Methods
```python
list.append(x)                  # Add to end
list.extend(iterable)           # Extend with iterable
list.insert(i, x)               # Insert at index
list.remove(x)                  # Remove first occurrence
list.pop([i])                   # Remove and return item
list.sort()                     # Sort in place
list.reverse()                  # Reverse in place
list.index(x)                   # Find index
list.count(x)                   # Count occurrences
```

### Dictionary Methods
```python
dict.keys()                     # View of keys
dict.values()                   # View of values
dict.items()                    # View of key-value pairs
dict.get(key[, default])        # Safe value access
dict.pop(key[, default])        # Remove and return value
dict.update([other])            # Update with another dict
dict.setdefault(key[, default]) # Get or set with default
```

### Pro Tips
1. **Use virtual environments** for project isolation
2. **Follow PEP 8** for consistent code style
3. **Write docstrings** for all public functions and classes
4. **Use type hints** for better code clarity and IDE support
5. **Prefer list comprehensions** over map/filter for simple transformations
6. **Use context managers** for resource management
7. **Leverage generators** for memory-efficient iteration
8. **Write tests** for critical functionality
9. **Use logging** instead of print for production code
10. **Profile before optimizing** - focus on actual bottlenecks

---

*Remember: Python's philosophy emphasizes readability and simplicity. Write code that is easy to understand and maintain, and leverage Python's rich ecosystem of libraries for complex tasks!*