# JavaScript & TypeScript Complete Cheat Sheet

## Table of Contents
- [JavaScript Basics](#javascript-basics)
- [ES6+ Modern JavaScript](#es6-modern-javascript)
- [Functions & Scope](#functions--scope)
- [Arrays & Iteration](#arrays--iteration)
- [Objects & Classes](#objects--classes)
- [Asynchronous JavaScript](#asynchronous-javascript)
- [DOM Manipulation](#dom-manipulation)
- [TypeScript Basics](#typescript-basics)
- [TypeScript Advanced](#typescript-advanced)
- [TypeScript with React](#typescript-with-react)
- [Error Handling](#error-handling)
- [Modern Patterns](#modern-patterns)
- [Performance & Best Practices](#performance--best-practices)

## JavaScript Basics

### Variables & Data Types
```javascript
// Variable declarations
var oldVariable = "function scoped"; // Avoid using
let mutableVariable = "can change"; // Block scoped
const constantVariable = "cannot change"; // Block scoped

// Primitive data types
const string = "Hello World";
const number = 42;
const bigInt = 9007199254740991n;
const boolean = true;
const nullValue = null;
const undefinedValue = undefined;
const symbol = Symbol('unique');

// Type checking
typeof variable; // "string", "number", "boolean", "undefined", "object", "function"
Array.isArray(variable); // Check if array
```

### Operators
```javascript
// Arithmetic
let x = 10 + 5;    // 15
x = 10 - 5;        // 5
x = 10 * 5;        // 50
x = 10 / 5;        // 2
x = 10 % 3;        // 1 (remainder)
x = 10 ** 2;       // 100 (exponentiation)

// Comparison
10 == "10";        // true (loose equality)
10 === "10";       // false (strict equality)
10 != "10";        // false
10 !== "10";       // true
5 > 3;             // true
5 < 3;             // false
5 >= 5;            // true

// Logical
true && false;     // false (AND)
true || false;     // true (OR)
!true;             // false (NOT)

// Ternary operator
const result = age >= 18 ? "Adult" : "Minor";
```

### Control Flow
```javascript
// If-else
if (condition) {
    // code
} else if (anotherCondition) {
    // code
} else {
    // code
}

// Switch
switch (value) {
    case 1:
        // code
        break;
    case 2:
        // code
        break;
    default:
        // code
}

// Loops
for (let i = 0; i < 5; i++) {
    console.log(i);
}

while (condition) {
    // code
}

do {
    // code
} while (condition);

// Loop control
break;        // Exit loop
continue;     // Skip to next iteration
```

## ES6+ Modern JavaScript

### Destructuring
```javascript
// Array destructuring
const [first, second, ...rest] = [1, 2, 3, 4, 5];
// first = 1, second = 2, rest = [3, 4, 5]

// Object destructuring
const person = { name: "John", age: 30, city: "New York" };
const { name, age, ...details } = person;
// name = "John", age = 30, details = { city: "New York" }

// Function parameter destructuring
function greet({ name, age }) {
    return `Hello ${name}, you are ${age} years old`;
}

// Default values in destructuring
const { name = "Anonymous", age = 0 } = person;
```

### Spread & Rest Operators
```javascript
// Spread operator (...)
const arr1 = [1, 2, 3];
const arr2 = [4, 5, 6];
const combined = [...arr1, ...arr2]; // [1, 2, 3, 4, 5, 6]

const obj1 = { a: 1, b: 2 };
const obj2 = { c: 3, d: 4 };
const merged = { ...obj1, ...obj2 }; // { a: 1, b: 2, c: 3, d: 4 }

// Rest parameters
function sum(...numbers) {
    return numbers.reduce((total, num) => total + num, 0);
}
sum(1, 2, 3, 4); // 10
```

### Template Literals
```javascript
const name = "John";
const age = 30;

// Basic template literal
const message = `Hello, my name is ${name} and I am ${age} years old.`;

// Multi-line strings
const multiLine = `
  This is a
  multi-line
  string
`;

// Tagged templates
function highlight(strings, ...values) {
    return strings.reduce((result, str, i) => 
        `${result}${str}<strong>${values[i] || ''}</strong>`, '');
}

const result = highlight`Hello ${name}, you are ${age} years old.`;
```

## Functions & Scope

### Function Types
```javascript
// Function declaration (hoisted)
function add(a, b) {
    return a + b;
}

// Function expression
const multiply = function(a, b) {
    return a * b;
};

// Arrow function (ES6+)
const divide = (a, b) => a / b;

// Arrow function with block body
const complexOperation = (a, b) => {
    const result = a * b;
    return result + 10;
};

// Immediately Invoked Function Expression (IIFE)
(function() {
    console.log("IIFE executed");
})();

// Generator function
function* numberGenerator() {
    yield 1;
    yield 2;
    yield 3;
}
```

### Parameters & Defaults
```javascript
// Default parameters
function greet(name = "Anonymous", age = 0) {
    return `Hello ${name}, you are ${age} years old`;
}

// Rest parameters
function logMessages(...messages) {
    messages.forEach(message => console.log(message));
}

// Named parameters with destructuring
function createUser({ name, age, email = "default@email.com" }) {
    return { name, age, email };
}
```

### Scope & Closures
```javascript
// Lexical scope
function outer() {
    const outerVar = "I'm outside";
    
    function inner() {
        console.log(outerVar); // Can access outerVar
    }
    
    return inner;
}

// Closure example
function createCounter() {
    let count = 0;
    
    return {
        increment: () => ++count,
        decrement: () => --count,
        getCount: () => count
    };
}

const counter = createCounter();
counter.increment(); // 1
counter.increment(); // 2
```

## Arrays & Iteration

### Array Methods
```javascript
const numbers = [1, 2, 3, 4, 5];

// Transformation
const doubled = numbers.map(num => num * 2); // [2, 4, 6, 8, 10]
const evens = numbers.filter(num => num % 2 === 0); // [2, 4]

// Reduction
const sum = numbers.reduce((total, num) => total + num, 0); // 15
const max = numbers.reduce((max, num) => num > max ? num : max, -Infinity); // 5

// Searching
const found = numbers.find(num => num > 3); // 4
const foundIndex = numbers.findIndex(num => num > 3); // 3
const hasEven = numbers.some(num => num % 2 === 0); // true
const allEven = numbers.every(num => num % 2 === 0); // false

// Iteration
numbers.forEach(num => console.log(num));
```

### Advanced Array Operations
```javascript
const users = [
    { id: 1, name: "John", age: 30 },
    { id: 2, name: "Jane", age: 25 },
    { id: 3, name: "Bob", age: 35 }
];

// Chaining array methods
const adultNames = users
    .filter(user => user.age >= 30)
    .map(user => user.name)
    .sort(); // ["Bob", "John"]

// Flat and flatMap
const nested = [1, [2, 3], [4, [5, 6]]];
const flat = nested.flat(2); // [1, 2, 3, 4, 5, 6]

const sentences = ["Hello world", "Good morning"];
const words = sentences.flatMap(sentence => sentence.split(" ")); // ["Hello", "world", "Good", "morning"]
```

### Iteration Protocols
```javascript
// for...of loop (arrays, strings, maps, sets)
for (const item of array) {
    console.log(item);
}

// for...in loop (object properties)
for (const key in object) {
    console.log(key, object[key]);
}

// Array entries
for (const [index, value] of array.entries()) {
    console.log(index, value);
}

// Object entries
for (const [key, value] of Object.entries(object)) {
    console.log(key, value);
}
```

## Objects & Classes

### Object Creation & Methods
```javascript
// Object literal
const person = {
    name: "John",
    age: 30,
    
    // Method shorthand
    greet() {
        return `Hello, I'm ${this.name}`;
    },
    
    // Computed property names
    ["prop" + "Name"]: "computed value"
};

// Object methods
Object.keys(person);    // ["name", "age", "greet"]
Object.values(person);  // ["John", 30, function...]
Object.entries(person); // [["name", "John"], ["age", 30], ...]

// Object copying and merging
const clone = { ...person };
const merged = Object.assign({}, person, { city: "NYC" });

// Property descriptors
Object.defineProperty(person, 'hidden', {
    value: "secret",
    writable: false,
    enumerable: false
});
```

### Classes & Inheritance
```javascript
// Class declaration
class Person {
    constructor(name, age) {
        this.name = name;
        this.age = age;
    }
    
    // Instance method
    greet() {
        return `Hello, I'm ${this.name}`;
    }
    
    // Static method
    static createAnonymous() {
        return new Person("Anonymous", 0);
    }
    
    // Getter
    get description() {
        return `${this.name} is ${this.age} years old`;
    }
    
    // Setter
    set updateAge(newAge) {
        if (newAge > 0) {
            this.age = newAge;
        }
    }
}

// Inheritance
class Employee extends Person {
    constructor(name, age, jobTitle) {
        super(name, age); // Call parent constructor
        this.jobTitle = jobTitle;
    }
    
    // Method overriding
    greet() {
        return `${super.greet()} and I'm a ${this.jobTitle}`;
    }
}

// Private fields (ES2022)
class BankAccount {
    #balance = 0; // Private field
    
    deposit(amount) {
        this.#balance += amount;
    }
    
    getBalance() {
        return this.#balance;
    }
}
```

## Asynchronous JavaScript

### Promises
```javascript
// Creating promises
const fetchData = new Promise((resolve, reject) => {
    setTimeout(() => {
        const success = Math.random() > 0.5;
        success ? resolve("Data received") : reject("Error occurred");
    }, 1000);
});

// Promise consumption
fetchData
    .then(data => {
        console.log("Success:", data);
        return processData(data);
    })
    .then(processedData => {
        console.log("Processed:", processedData);
    })
    .catch(error => {
        console.error("Error:", error);
    })
    .finally(() => {
        console.log("Operation completed");
    });

// Promise methods
Promise.all([promise1, promise2, promise3]) // Waits for all
    .then(results => console.log(results));

Promise.race([promise1, promise2]) // First to settle
    .then(result => console.log(result));

Promise.any([promise1, promise2]) // First to fulfill
    .then(result => console.log(result));
```

### Async/Await
```javascript
// Async function
async function fetchUserData(userId) {
    try {
        const response = await fetch(`/api/users/${userId}`);
        if (!response.ok) {
            throw new Error('Network response was not ok');
        }
        const user = await response.json();
        return user;
    } catch (error) {
        console.error('Failed to fetch user:', error);
        throw error; // Re-throw to let caller handle
    }
}

// Using async functions
async function processUsers() {
    try {
        const user1 = await fetchUserData(1);
        const user2 = await fetchUserData(2);
        
        // Parallel execution
        const [user3, user4] = await Promise.all([
            fetchUserData(3),
            fetchUserData(4)
        ]);
        
        return [user1, user2, user3, user4];
    } catch (error) {
        console.error('Error processing users:', error);
    }
}

// Async IIFE
(async function() {
    const result = await fetchUserData(1);
    console.log(result);
})();
```

### Fetch API & HTTP Requests
```javascript
// GET request
async function getData() {
    const response = await fetch('https://api.example.com/data', {
        method: 'GET',
        headers: {
            'Content-Type': 'application/json',
            'Authorization': 'Bearer token123'
        }
    });
    
    if (!response.ok) throw new Error('HTTP error!');
    return await response.json();
}

// POST request
async function postData(data) {
    const response = await fetch('https://api.example.com/data', {
        method: 'POST',
        headers: {
            'Content-Type': 'application/json'
        },
        body: JSON.stringify(data)
    });
    
    return await response.json();
}

// Handling different response types
async function fetchVariousData() {
    const jsonResponse = await fetch('/api/data.json');
    const jsonData = await jsonResponse.json();
    
    const textResponse = await fetch('/api/data.txt');
    const textData = await textResponse.text();
    
    const blobResponse = await fetch('/api/image.png');
    const blobData = await blobResponse.blob();
}
```

## DOM Manipulation

### Selecting & Manipulating Elements
```javascript
// Selecting elements
const element = document.getElementById('myId');
const elements = document.querySelectorAll('.myClass');
const firstElement = document.querySelector('.myClass');

// Creating elements
const newDiv = document.createElement('div');
newDiv.textContent = 'Hello World';
newDiv.className = 'my-class';
newDiv.setAttribute('data-custom', 'value');

// Modifying elements
element.innerHTML = '<strong>Bold text</strong>';
element.textContent = 'Plain text';
element.style.color = 'red';
element.classList.add('new-class');
element.classList.remove('old-class');
element.classList.toggle('active');

// Traversing DOM
const parent = element.parentElement;
const children = element.children;
const nextSibling = element.nextElementSibling;
const previousSibling = element.previousElementSibling;
```

### Events
```javascript
// Event listeners
element.addEventListener('click', function(event) {
    console.log('Element clicked', event.target);
    event.preventDefault(); // Prevent default behavior
    event.stopPropagation(); // Stop event bubbling
});

// Event delegation
document.addEventListener('click', function(event) {
    if (event.target.matches('.delete-btn')) {
        event.target.parentElement.remove();
    }
});

// Common events
element.addEventListener('click', handler);
element.addEventListener('dblclick', handler);
element.addEventListener('mouseenter', handler);
element.addEventListener('mouseleave', handler);
element.addEventListener('keydown', handler);
element.addEventListener('submit', handler);
element.addEventListener('input', handler);
element.addEventListener('change', handler);

// Custom events
const customEvent = new CustomEvent('myEvent', {
    detail: { message: 'Custom event data' }
});
element.dispatchEvent(customEvent);
```

### Forms & Validation
```javascript
// Form handling
const form = document.getElementById('myForm');

form.addEventListener('submit', async (event) => {
    event.preventDefault();
    
    const formData = new FormData(form);
    const data = Object.fromEntries(formData);
    
    // Client-side validation
    if (!data.email.includes('@')) {
        showError('Please enter a valid email');
        return;
    }
    
    try {
        const response = await fetch('/api/submit', {
            method: 'POST',
            body: JSON.stringify(data)
        });
        
        if (response.ok) {
            showSuccess('Form submitted successfully');
            form.reset();
        }
    } catch (error) {
        showError('Submission failed');
    }
});

// Real-time validation
const emailInput = document.getElementById('email');
emailInput.addEventListener('input', (event) => {
    const isValid = event.target.value.includes('@');
    event.target.classList.toggle('invalid', !isValid);
});
```

## TypeScript Basics

### Basic Types
```typescript
// Primitive types
let name: string = "John";
let age: number = 30;
let isActive: boolean = true;
let nothing: null = null;
let notDefined: undefined = undefined;
let unique: symbol = Symbol('id');

// Array types
let numbers: number[] = [1, 2, 3];
let strings: Array<string> = ["a", "b", "c"];
let mixed: (string | number)[] = [1, "hello", 2, "world"];

// Tuple - fixed length array
let person: [string, number] = ["John", 30];

// Enum
enum Color {
    Red = "RED",
    Green = "GREEN",
    Blue = "BLUE"
}

// Any - avoid when possible
let flexible: any = "can be anything";
flexible = 42;
flexible = true;

// Unknown - better than any
let unknownValue: unknown = "hello";
if (typeof unknownValue === "string") {
    console.log(unknownValue.toUpperCase()); // Safe
}

// Void - no return value
function logMessage(): void {
    console.log("Hello");
}

// Never - function never returns
function throwError(message: string): never {
    throw new Error(message);
}
```

### Interfaces & Type Aliases
```typescript
// Interface for objects
interface User {
    readonly id: number;        // Cannot be modified after creation
    name: string;
    age?: number;               // Optional property
    email: string;
    
    // Method signature
    greet(): string;
}

// Extending interfaces
interface Admin extends User {
    permissions: string[];
}

// Type alias
type Point = {
    x: number;
    y: number;
};

// Union types
type Status = "pending" | "success" | "error";
type ID = number | string;

// Intersection types
type Employee = User & {
    employeeId: number;
    department: string;
};

// Index signatures
interface StringArray {
    [index: number]: string;
}

// Function types
interface SearchFunc {
    (source: string, subString: string): boolean;
}
```

### Functions in TypeScript
```typescript
// Function with types
function add(a: number, b: number): number {
    return a + b;
}

// Arrow function
const multiply = (a: number, b: number): number => a * b;

// Optional parameters
function greet(name: string, title?: string): string {
    return title ? `Hello, ${title} ${name}` : `Hello, ${name}`;
}

// Default parameters
function createUser(name: string, age: number = 18): User {
    return { id: Date.now(), name, age };
}

// Rest parameters
function sum(...numbers: number[]): number {
    return numbers.reduce((total, num) => total + num, 0);
}

// Function overloading
function processInput(input: string): string[];
function processInput(input: number): number[];
function processInput(input: string | number): string[] | number[] {
    if (typeof input === "string") {
        return input.split("");
    } else {
        return [input, input * 2, input * 3];
    }
}
```

## TypeScript Advanced

### Generics
```typescript
// Generic function
function identity<T>(arg: T): T {
    return arg;
}

const result1 = identity<string>("hello");
const result2 = identity<number>(42); // Type inference works

// Generic interfaces
interface ApiResponse<T> {
    data: T;
    status: number;
    message: string;
}

// Generic classes
class Repository<T> {
    private items: T[] = [];
    
    add(item: T): void {
        this.items.push(item);
    }
    
    getById(id: number): T | undefined {
        return this.items[id];
    }
}

// Constraints
interface HasId {
    id: number;
}

function getById<T extends HasId>(items: T[], id: number): T | undefined {
    return items.find(item => item.id === id);
}

// Generic constraints with keyof
function getProperty<T, K extends keyof T>(obj: T, key: K): T[K] {
    return obj[key];
}
```

### Utility Types
```typescript
interface User {
    id: number;
    name: string;
    age: number;
    email: string;
}

// Partial - all properties optional
type PartialUser = Partial<User>;

// Required - all properties required
type RequiredUser = Required<User>;

// Readonly - all properties readonly
type ReadonlyUser = Readonly<User>;

// Pick - select specific properties
type UserNameAndEmail = Pick<User, 'name' | 'email'>;

// Omit - remove specific properties
type UserWithoutEmail = Omit<User, 'email'>;

// Record - object with specific key/value types
type UserMap = Record<string, User>;

// Extract - extract union types that are assignable
type T0 = Extract<"a" | "b" | "c", "a" | "f">; // "a"

// Exclude - exclude union types that are assignable
type T1 = Exclude<"a" | "b" | "c", "a" | "f">; // "b" | "c"

// NonNullable - exclude null and undefined
type T2 = NonNullable<string | number | null | undefined>; // string | number
```

### Advanced Types
```typescript
// Conditional types
type IsString<T> = T extends string ? true : false;
type A = IsString<string>; // true
type B = IsString<number>; // false

// Mapped types
type OptionalFields<T> = {
    [K in keyof T]?: T[K];
};

type ReadonlyFields<T> = {
    readonly [K in keyof T]: T[K];
};

// Template literal types
type EventName = "click" | "scroll" | "mousemove";
type HandlerName = `on${EventName}`; // "onclick" | "onscroll" | "onmousemove"

// Infer keyword
type ReturnType<T> = T extends (...args: any[]) => infer R ? R : never;

// Type guards
function isString(value: unknown): value is string {
    return typeof value === "string";
}

function processValue(value: string | number) {
    if (isString(value)) {
        // TypeScript knows value is string here
        console.log(value.toUpperCase());
    } else {
        // TypeScript knows value is number here
        console.log(value.toFixed(2));
    }
}
```

## TypeScript with React

### Components & Props
```typescript
import React from 'react';

// Functional component with props
interface UserProps {
    name: string;
    age: number;
    isActive?: boolean;
    onUpdate?: (name: string) => void;
}

const User: React.FC<UserProps> = ({ 
    name, 
    age, 
    isActive = true,
    onUpdate 
}) => {
    return (
        <div>
            <h2>{name}</h2>
            <p>Age: {age}</p>
            <p>Status: {isActive ? 'Active' : 'Inactive'}</p>
            {onUpdate && (
                <button onClick={() => onUpdate(name)}>Update</button>
            )}
        </div>
    );
};

// Component with children
interface ContainerProps {
    children: React.ReactNode;
    className?: string;
}

const Container: React.FC<ContainerProps> = ({ children, className }) => {
    return <div className={className}>{children}</div>;
};

// Using components
const App: React.FC = () => {
    const handleUpdate = (name: string) => {
        console.log(`Updating ${name}`);
    };
    
    return (
        <Container className="app">
            <User name="John" age={30} onUpdate={handleUpdate} />
        </Container>
    );
};
```

### Hooks with TypeScript
```typescript
import React, { useState, useEffect, useReducer, useCallback } from 'react';

// useState with TypeScript
const [count, setCount] = useState<number>(0);
const [user, setUser] = useState<User | null>(null);
const [items, setItems] = useState<string[]>([]);

// useEffect
useEffect(() => {
    fetch('/api/user')
        .then(res => res.json())
        .then((user: User) => setUser(user));
}, []);

// useReducer
interface State {
    count: number;
}

type Action = 
    | { type: 'increment' }
    | { type: 'decrement' }
    | { type: 'reset'; payload: number };

const reducer = (state: State, action: Action): State => {
    switch (action.type) {
        case 'increment':
            return { count: state.count + 1 };
        case 'decrement':
            return { count: state.count - 1 };
        case 'reset':
            return { count: action.payload };
        default:
            return state;
    }
};

const [state, dispatch] = useReducer(reducer, { count: 0 });

// useCallback with types
const handleClick = useCallback((id: number) => {
    console.log(`Clicked ${id}`);
}, []);

// Custom hook with TypeScript
interface UseApiResult<T> {
    data: T | null;
    loading: boolean;
    error: string | null;
}

function useApi<T>(url: string): UseApiResult<T> {
    const [data, setData] = useState<T | null>(null);
    const [loading, setLoading] = useState(true);
    const [error, setError] = useState<string | null>(null);
    
    useEffect(() => {
        fetch(url)
            .then(res => res.json())
            .then((data: T) => {
                setData(data);
                setLoading(false);
            })
            .catch(err => {
                setError(err.message);
                setLoading(false);
            });
    }, [url]);
    
    return { data, loading, error };
}
```

### Forms & Events
```typescript
import React, { FormEvent, ChangeEvent } from 'react';

const ContactForm: React.FC = () => {
    const [formData, setFormData] = useState({
        name: '',
        email: '',
        message: ''
    });
    
    // Typing form events
    const handleSubmit = (e: FormEvent<HTMLFormElement>) => {
        e.preventDefault();
        console.log('Form submitted:', formData);
    };
    
    // Typing input change events
    const handleChange = (e: ChangeEvent<HTMLInputElement | HTMLTextAreaElement>) => {
        const { name, value } = e.target;
        setFormData(prev => ({
            ...prev,
            [name]: value
        }));
    };
    
    return (
        <form onSubmit={handleSubmit}>
            <input
                type="text"
                name="name"
                value={formData.name}
                onChange={handleChange}
                placeholder="Name"
            />
            <input
                type="email"
                name="email"
                value={formData.email}
                onChange={handleChange}
                placeholder="Email"
            />
            <textarea
                name="message"
                value={formData.message}
                onChange={handleChange}
                placeholder="Message"
            />
            <button type="submit">Submit</button>
        </form>
    );
};
```

## Error Handling

### Try/Catch & Custom Errors
```javascript
// Basic error handling
try {
    // Code that might throw an error
    const data = JSON.parse(invalidJson);
} catch (error) {
    console.error('Parsing failed:', error.message);
} finally {
    console.log('This always runs');
}

// Custom error classes
class ValidationError extends Error {
    constructor(field, message) {
        super(`Validation error in ${field}: ${message}`);
        this.name = 'ValidationError';
        this.field = field;
        this.timestamp = new Date();
    }
}

class NetworkError extends Error {
    constructor(url, status) {
        super(`Network error for ${url}: ${status}`);
        this.name = 'NetworkError';
        this.url = url;
        this.status = status;
    }
}

// Using custom errors
function validateUser(user) {
    if (!user.email) {
        throw new ValidationError('email', 'Email is required');
    }
    if (!user.email.includes('@')) {
        throw new ValidationError('email', 'Invalid email format');
    }
}

// Error handling with async/await
async function fetchWithRetry(url, retries = 3) {
    for (let i = 0; i < retries; i++) {
        try {
            const response = await fetch(url);
            if (!response.ok) {
                throw new NetworkError(url, response.status);
            }
            return await response.json();
        } catch (error) {
            if (i === retries - 1) throw error;
            console.log(`Retry ${i + 1} after error:`, error.message);
            await new Promise(resolve => setTimeout(resolve, 1000 * (i + 1)));
        }
    }
}
```

## Modern Patterns

### Module Patterns
```javascript
// ES6 Modules
// math.js
export const PI = 3.14159;

export function add(a, b) {
    return a + b;
}

export default function multiply(a, b) {
    return a * b;
}

// app.js
import multiply, { PI, add } from './math.js';
import * as MathUtils from './math.js';

// Dynamic imports
async function loadModule() {
    const module = await import('./dynamic-module.js');
    module.someFunction();
}

// CommonJS (Node.js)
module.exports = { add, multiply };
exports.PI = 3.14159;

const math = require('./math');
```

### Design Patterns
```javascript
// Singleton pattern
class Database {
    constructor() {
        if (Database.instance) {
            return Database.instance;
        }
        this.connection = "database connection";
        Database.instance = this;
    }
    
    static getInstance() {
        if (!Database.instance) {
            Database.instance = new Database();
        }
        return Database.instance;
    }
}

// Factory pattern
class UserFactory {
    static createUser(type, data) {
        switch (type) {
            case 'admin':
                return new AdminUser(data);
            case 'customer':
                return new CustomerUser(data);
            default:
                throw new Error('Invalid user type');
        }
    }
}

// Observer pattern
class EventEmitter {
    constructor() {
        this.events = {};
    }
    
    on(event, listener) {
        if (!this.events[event]) {
            this.events[event] = [];
        }
        this.events[event].push(listener);
    }
    
    emit(event, data) {
        if (this.events[event]) {
            this.events[event].forEach(listener => listener(data));
        }
    }
    
    off(event, listenerToRemove) {
        if (this.events[event]) {
            this.events[event] = this.events[event].filter(
                listener => listener !== listenerToRemove
            );
        }
    }
}
```

### Functional Programming
```javascript
// Pure functions
function add(a, b) {
    return a + b; // No side effects, same input = same output
}

// Higher-order functions
function withLogging(fn) {
    return function(...args) {
        console.log(`Calling function with args:`, args);
        const result = fn(...args);
        console.log(`Function returned:`, result);
        return result;
    };
}

// Function composition
const compose = (...fns) => x => fns.reduceRight((acc, fn) => fn(acc), x);
const pipe = (...fns) => x => fns.reduce((acc, fn) => fn(acc), x);

// Currying
const curry = (fn) => {
    return function curried(...args) {
        if (args.length >= fn.length) {
            return fn.apply(this, args);
        } else {
            return function(...args2) {
                return curried.apply(this, args.concat(args2));
            };
        }
    };
};

const add = curry((a, b, c) => a + b + c);
const addFive = add(2, 3);
console.log(addFive(5)); // 10
```

## Performance & Best Practices

### Performance Optimization
```javascript
// Debounce and throttle
function debounce(func, wait) {
    let timeout;
    return function executedFunction(...args) {
        const later = () => {
            clearTimeout(timeout);
            func(...args);
        };
        clearTimeout(timeout);
        timeout = setTimeout(later, wait);
    };
}

function throttle(func, limit) {
    let inThrottle;
    return function(...args) {
        if (!inThrottle) {
            func.apply(this, args);
            inThrottle = true;
            setTimeout(() => inThrottle = false, limit);
        }
    };
}

// Memoization
function memoize(fn) {
    const cache = new Map();
    return function(...args) {
        const key = JSON.stringify(args);
        if (cache.has(key)) {
            return cache.get(key);
        }
        const result = fn.apply(this, args);
        cache.set(key, result);
        return result;
    };
}

// Lazy loading
class LazyLoader {
    constructor(loader) {
        this.loader = loader;
        this.loaded = false;
        this.value = null;
    }
    
    get() {
        if (!this.loaded) {
            this.value = this.loader();
            this.loaded = true;
        }
        return this.value;
    }
}
```

### Modern JavaScript Features
```javascript
// Optional chaining (?.)
const street = user?.address?.street; // undefined if any part is null/undefined

// Nullish coalescing (??)
const defaultValue = input ?? "default"; // Only if input is null or undefined

// Logical assignment
a ||= b; // a = a || b
a &&= b; // a = a && b
a ??= b; // a = a ?? b

// Promise.allSettled
const promises = [promise1, promise2, promise3];
const results = await Promise.allSettled(promises);
results.forEach(result => {
    if (result.status === 'fulfilled') {
        console.log('Value:', result.value);
    } else {
        console.log('Reason:', result.reason);
    }
});

// GlobalThis
console.log(globalThis === window); // In browsers
console.log(globalThis === global); // In Node.js

// Dynamic import
const module = await import('/modules/my-module.js');
```

### Best Practices
```javascript
// Use const by default, let when needed
const immutable = "cannot change";
let mutable = "can change";

// Prefer arrow functions for callbacks
[1, 2, 3].map(x => x * 2);

// Use template literals for strings
const message = `Hello ${name}, welcome!`;

// Destructure objects and arrays
const { name, age } = user;
const [first, second] = array;

// Use async/await over promise chains
async function fetchData() {
    try {
        const response = await fetch(url);
        const data = await response.json();
        return data;
    } catch (error) {
        console.error('Error:', error);
    }
}

// Use modules for better organization
// Export only what's needed
// Use default exports for main functionality

// Write pure functions when possible
// Avoid side effects
// Use immutable data patterns
```

---

## Quick Reference Card

### Most Used JavaScript
```javascript
// Array methods
array.map(x => transform(x))
array.filter(x => condition(x))
array.reduce((acc, x) => acc + x, 0)
array.find(x => condition(x))
array.some(x => condition(x))
array.every(x => condition(x))

// Object methods
Object.keys(obj)
Object.values(obj)
Object.entries(obj)
Object.assign({}, obj, update)
{ ...obj, ...update }

// Promise patterns
Promise.all([p1, p2, p3])
Promise.race([p1, p2])
Promise.resolve(value)
Promise.reject(error)
```

### Most Used TypeScript
```typescript
// Common types
string, number, boolean, any, unknown, void, never
string[], Array<number>, [string, number] // Tuple
{ key: string } // Object type
(key: string) => void // Function type

// Utility types
Partial<T>, Required<T>, Readonly<T>
Pick<T, K>, Omit<T, K>
Record<K, T>, Exclude<T, U>, Extract<T, U>

// Type guards
typeof x === "string"
x instanceof MyClass
"property" in obj
```

### Essential Patterns
```javascript
// Async error handling
async function safeFetch() {
    try {
        const response = await fetch(url);
        return await response.json();
    } catch (error) {
        console.error('Fetch failed:', error);
        return null;
    }
}

// Event delegation
document.addEventListener('click', (e) => {
    if (e.target.matches('.button')) {
        // Handle button click
    }
});

// Debounced search
const search = debounce((query) => {
    // Perform search
}, 300);
```

### Pro Tips
1. **Use TypeScript** for better code quality and developer experience
2. **Prefer const over let** for immutable variables
3. **Use async/await** for cleaner asynchronous code
4. **Leverage array methods** instead of for loops when possible
5. **Destructure objects and arrays** for cleaner code
6. **Use optional chaining and nullish coalescing** for safer code
7. **Write pure functions** without side effects
8. **Use functional programming patterns** for better code organization
9. **Implement proper error handling** with try/catch and custom errors
10. **Keep functions small and focused** on single responsibility

---

*Remember: JavaScript and TypeScript are constantly evolving. Stay updated with new ECMAScript features and TypeScript releases to write modern, efficient, and maintainable code!*