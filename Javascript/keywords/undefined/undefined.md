# JavaScript: `undefined` vs "not defined" - Complete Guide

> **Quick Path**: What is undefined → What is not defined → Key Differences → Common Patterns → Interview Questions

---

## 🎯 What Is `undefined`?

Let's start with a simple scenario:

```javascript
let name;
console.log(name); // undefined

function greet(message) {
  console.log(message);
}
greet(); // undefined (no argument passed)
```

**`undefined` is a primitive value in JavaScript that represents "a variable has been declared but not assigned a value yet" or "a value that doesn't exist".**

Think of it as: **"I know about this thing, but it doesn't have a value yet."**

### `undefined` is Actually Two Things

```javascript
// 1. A PRIMITIVE VALUE (the actual value)
let x;
console.log(x); // undefined (the value)
console.log(typeof x); // "undefined" (the type)

// 2. A GLOBAL PROPERTY
console.log(undefined); // undefined
console.log(window.undefined); // undefined (in browser)
console.log(global.undefined); // undefined (in Node.js)
```

---

## 💡 What Is "not defined"?

Now, a different scenario:

```javascript
console.log(age); // ReferenceError: age is not defined

// OR

let name;
console.log(nam); // ReferenceError: nam is not defined (typo!)
```

**"not defined" means the variable has NEVER been declared in any scope accessible to the code.** It's a **ReferenceError**, not a value!

Think of it as: **"I have no idea what you're talking about. This thing doesn't exist at all."**

---

## 🔥 The KEY Difference

This is the most important concept:

```javascript
// SCENARIO 1: undefined (variable exists, no value)
let name;
console.log(name); // undefined ✅ No error, just undefined value

// SCENARIO 2: not defined (variable doesn't exist)
console.log(age); // ReferenceError: age is not defined ❌ THROWS ERROR!
```

**Visual Comparison:**

```
┌─────────────────────────────────────────────────┐
│           undefined                             │
├─────────────────────────────────────────────────┤
│                                                 │
│  Variable: EXISTS ✅                            │
│  Value: NO VALUE (undefined)                    │
│  Behavior: Returns undefined                    │
│  Error: NO                                      │
│                                                 │
│  Example:                                       │
│  let x;                                         │
│  console.log(x); // undefined                   │
│                                                 │
└─────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────┐
│           not defined                           │
├─────────────────────────────────────────────────┤
│                                                 │
│  Variable: DOESN'T EXIST ❌                     │
│  Value: N/A (can't access)                      │
│  Behavior: Throws ReferenceError                │
│  Error: YES                                     │
│                                                 │
│  Example:                                       │
│  console.log(y); // ReferenceError!             │
│                                                 │
└─────────────────────────────────────────────────┘
```

---

## 🚀 When Do You Get `undefined`?

### 1. Variable Declared But Not Assigned

```javascript
let name;
console.log(name); // undefined

var age;
console.log(age); // undefined

// But NOT with const (must initialize)
const city; // SyntaxError: Missing initializer
```

### 2. Function Returns Nothing

```javascript
function doSomething() {
  console.log("Hello");
  // No return statement
}

const result = doSomething();
console.log(result); // undefined
```

### 3. Missing Function Parameters

```javascript
function greet(name, age) {
  console.log(name); // "John"
  console.log(age); // undefined (not passed)
}

greet("John"); // age is undefined
```

### 4. Object Property Doesn't Exist

```javascript
const user = {
  name: "John",
  age: 25,
};

console.log(user.name); // "John"
console.log(user.address); // undefined (property doesn't exist)
```

### 5. Array Element Doesn't Exist

```javascript
const arr = [1, 2, 3];
console.log(arr[0]); // 1
console.log(arr[5]); // undefined (index doesn't exist)
```

### 6. Hoisting (var declarations)

```javascript
console.log(x); // undefined (hoisted but not initialized)
var x = 5;
console.log(x); // 5

// Behind the scenes:
// var x; // Declaration hoisted
// console.log(x); // undefined
// x = 5; // Assignment stays here
```

### 7. Function Hoisting

```javascript
console.log(myFunc); // undefined (var hoisting)
var myFunc = function () {
  return "Hello";
};

// VS

console.log(myFunc2); // [Function: myFunc2] (function declaration hoisted)
function myFunc2() {
  return "Hello";
}
```

---

## ⚠️ When Do You Get "not defined" Error?

### 1. Variable Never Declared

```javascript
console.log(username); // ReferenceError: username is not defined
```

### 2. Typo in Variable Name

```javascript
let firstName = "John";
console.log(firstNam); // ReferenceError: firstNam is not defined
//                                                ^^^^^^^^ typo!
```

### 3. Out of Scope

```javascript
function outer() {
  let x = 10;
}

console.log(x); // ReferenceError: x is not defined
//                    x only exists inside outer()
```

### 4. Temporal Dead Zone (let/const)

```javascript
console.log(name); // ReferenceError: Cannot access 'name' before initialization
let name = "John";

// Different from:
console.log(age); // undefined (var hoisting)
var age = 25;
```

### 5. Accessing Before Import

```javascript
console.log(myModule); // ReferenceError: myModule is not defined
import myModule from "./module.js"; // Import must come first
```

---

## 💪 Common Patterns & Examples

### Pattern 1: Checking for undefined

```javascript
let name;

// ❌ Bad - Loose equality checks both undefined and null
if (name == undefined) {
  console.log("Name is undefined or null");
}

// ✅ Good - Strict equality checks only undefined
if (name === undefined) {
  console.log("Name is strictly undefined");
}

// ✅ Also good - typeof is safe (won't throw error)
if (typeof name === "undefined") {
  console.log("Name is undefined");
}

// ✅ Best - Truthiness check (handles undefined, null, 0, "", false)
if (!name) {
  console.log("Name is falsy");
}
```

### Pattern 2: Checking if Variable Exists (avoiding "not defined" error)

```javascript
// ❌ Throws error if variable doesn't exist
if (someVariable) {
  console.log(someVariable);
}

// ✅ Safe way - typeof doesn't throw error
if (typeof someVariable !== "undefined") {
  console.log(someVariable);
}

// ✅ Also safe - try/catch
try {
  console.log(someVariable);
} catch (error) {
  console.log("Variable doesn't exist");
}

// ✅ For global variables only
if ("someVariable" in window) {
  // browser
  console.log(window.someVariable);
}
```

### Pattern 3: Default Parameters

```javascript
// ❌ Old way
function greet(name) {
  name = name || "Guest"; // Buggy! Fails for falsy values like ""
  console.log("Hello, " + name);
}

// ⚠️ Better but verbose
function greet(name) {
  if (name === undefined) {
    name = "Guest";
  }
  console.log("Hello, " + name);
}

// ✅ Best - ES6 default parameters
function greet(name = "Guest") {
  console.log("Hello, " + name);
}

greet(); // "Hello, Guest"
greet("John"); // "Hello, John"
greet(undefined); // "Hello, Guest"
greet(""); // "Hello, " (empty string is valid!)
```

### Pattern 4: Object Property Access

```javascript
const user = {
  name: "John",
  address: {
    city: "New York",
  },
};

// ❌ Crashes if intermediate property doesn't exist
// console.log(user.profile.age); // TypeError!

// ✅ Safe access
console.log(user.profile && user.profile.age); // undefined (no error)

// ✅ Best - Optional chaining (ES2020)
console.log(user.profile?.age); // undefined (no error)
console.log(user.address?.city); // "New York"
```

### Pattern 5: Nullish Coalescing

```javascript
let count;

// ❌ OR operator treats 0 as falsy
const value1 = count || 10;
console.log(value1); // 10 (but what if count should be 0?)

// ✅ Nullish coalescing - only triggers for null/undefined
const value2 = count ?? 10;
console.log(value2); // 10

count = 0;
const value3 = count ?? 10;
console.log(value3); // 0 ✅ Preserves 0

count = "";
const value4 = count ?? "default";
console.log(value4); // "" ✅ Preserves empty string
```

---

## 🔍 Typeof Behavior

```javascript
// undefined (value)
let x;
console.log(typeof x); // "undefined"

// undefined (primitive)
console.log(typeof undefined); // "undefined"

// Variable doesn't exist - typeof is SAFE!
console.log(typeof someRandomVariable); // "undefined" (no error!)

// Direct access throws error
console.log(someRandomVariable); // ReferenceError: someRandomVariable is not defined
```

**Why `typeof` is safe:**

```javascript
// This is SAFE - no error
if (typeof jQuery !== "undefined") {
  // Use jQuery
}

// This THROWS ERROR if jQuery not defined
if (jQuery) {
  // Use jQuery
}
```

---

## 🎨 Real-World Examples

### Example 1: API Response Handling

```javascript
async function fetchUser(id) {
  const response = await fetch(`/api/users/${id}`);
  const data = await response.json();

  // Handle optional fields
  const username = data.username ?? "Anonymous";
  const bio = data.bio ?? "No bio available";
  const profilePic = data.profilePic ?? "/default-avatar.png";

  return { username, bio, profilePic };
}
```

### Example 2: Configuration Objects

```javascript
function createServer(config = {}) {
  // Provide defaults for undefined properties
  const port = config.port ?? 3000;
  const host = config.host ?? "localhost";
  const debug = config.debug ?? false;

  console.log(`Server running on ${host}:${port}`);
  if (debug) console.log("Debug mode enabled");
}

createServer(); // Uses all defaults
createServer({ port: 8080 }); // Uses port 8080, other defaults
createServer({ port: 0 }); // Port 0 is valid! (finds available port)
```

### Example 3: Function Parameter Validation

```javascript
function processUser(name, age, email) {
  // Check for required parameters
  if (name === undefined) {
    throw new Error("Name is required");
  }

  if (age === undefined) {
    throw new Error("Age is required");
  }

  // Optional parameter
  const userEmail = email ?? "no-email@example.com";

  return { name, age, email: userEmail };
}

// OR better with default parameters
function processUser(
  name = throwError("Name is required"),
  age = throwError("Age is required"),
  email = "no-email@example.com",
) {
  return { name, age, email };
}

function throwError(message) {
  throw new Error(message);
}
```

### Example 4: Safe Navigation

```javascript
// API response might have nested structure
const userData = {
  user: {
    profile: {
      address: {
        city: "New York",
      },
    },
  },
};

// ❌ Unsafe - might crash
// const city = userData.user.profile.address.city;

// ✅ Safe with optional chaining
const city = userData?.user?.profile?.address?.city;
console.log(city); // "New York"

const state = userData?.user?.profile?.address?.state;
console.log(state); // undefined (not an error!)
```

---

## 🎤 Interview Questions & Answers

### Q1: What's the difference between `undefined` and "not defined"?

**Answer:**

```javascript
// undefined - Variable exists, but has no value
let x;
console.log(x); // undefined (no error)
console.log(typeof x); // "undefined"

// not defined - Variable doesn't exist at all
console.log(y); // ReferenceError: y is not defined (throws error!)

// Key differences:
// 1. undefined is a VALUE
// 2. "not defined" is an ERROR (ReferenceError)
// 3. undefined can be checked safely
// 4. "not defined" crashes your code if not handled
```

**Summary:**

- **undefined** = "Variable exists but has no value" (safe)
- **not defined** = "Variable doesn't exist" (throws ReferenceError)

---

### Q2: What's the difference between `undefined` and `null`?

**Answer:**

```javascript
// undefined - "No value assigned" (unintentional absence)
let x;
console.log(x); // undefined
console.log(typeof x); // "undefined"

// null - "Intentional absence of value" (deliberate)
let y = null;
console.log(y); // null
console.log(typeof y); // "object" (this is a JavaScript bug!)

// Comparison
console.log(undefined == null); // true (loose equality)
console.log(undefined === null); // false (strict equality)

// Usage:
let user; // undefined - forgot to set value
let user = null; // null - intentionally empty
```

**Key Differences:**
| Aspect | undefined | null |
|--------|-----------|------|
| **Meaning** | Absence of value (unintentional) | Absence of value (intentional) |
| **Type** | `undefined` | `object` (historical bug) |
| **Assigned by** | JavaScript (automatically) | Developer (explicitly) |
| **Use case** | Default uninitialized state | Deliberately empty value |

---

### Q3: Why does `typeof null` return "object"?

**Answer:**

```javascript
console.log(typeof null); // "object" 🤯

// This is a HISTORICAL BUG in JavaScript from 1995!
// In the original JavaScript implementation:
// - Objects were represented with tag 000
// - null was represented as NULL pointer (all zeros)
// - typeof checked the first 3 bits (000 = object)
// - Result: typeof null === "object"

// This bug was never fixed because:
// 1. Too much code relies on it
// 2. Would break backward compatibility
// 3. Web would break everywhere

// How to properly check for null:
const value = null;

if (value === null) {
  // ✅ Correct way
  console.log("It's null");
}

if (typeof value === "object" && value === null) {
  // ✅ Explicit
  console.log("It's null");
}
```

---

### Q4: How do you safely check if a variable exists?

**Answer:**

```javascript
// ❌ Wrong - Throws ReferenceError if doesn't exist
if (myVariable) {
  console.log(myVariable);
}

// ✅ Solution 1: typeof (safest, works for undefined variables)
if (typeof myVariable !== "undefined") {
  console.log(myVariable);
}

// ✅ Solution 2: try/catch
try {
  console.log(myVariable);
} catch (error) {
  console.log("Variable doesn't exist");
}

// ✅ Solution 3: For global variables only
if ("myVariable" in window) {
  // browser
  console.log(window.myVariable);
}

if ("myVariable" in global) {
  // Node.js
  console.log(global.myVariable);
}

// Why typeof is best:
// - No error even if variable doesn't exist
// - Works in all scopes
// - Most concise
```

---

### Q5: What happens with hoisting?

**Answer:**

```javascript
// var - Hoisted and initialized with undefined
console.log(x); // undefined (no error)
var x = 5;
console.log(x); // 5

// Behind the scenes:
// var x = undefined; // Hoisted to top
// console.log(x);
// x = 5;

// let/const - Hoisted but NOT initialized (Temporal Dead Zone)
console.log(y); // ReferenceError: Cannot access 'y' before initialization
let y = 10;

// const - Same as let, but must be initialized
console.log(z); // ReferenceError
const z = 15;

// Function declarations - Fully hoisted
console.log(myFunc()); // "Hello" (works!)
function myFunc() {
  return "Hello";
}

// Function expressions - NOT hoisted
console.log(myFunc2()); // TypeError: myFunc2 is not a function
var myFunc2 = function () {
  return "Hello";
};
```

---

### Q6: What's the difference between `==` and `===` with undefined?

**Answer:**

```javascript
let x;
let y = null;

// == (loose equality) - Type coercion
console.log(x == undefined); // true
console.log(x == null); // true (undefined == null is true!)
console.log(y == undefined); // true
console.log(0 == undefined); // false

// === (strict equality) - No type coercion
console.log(x === undefined); // true
console.log(x === null); // false (different types)
console.log(y === undefined); // false
console.log(0 === undefined); // false

// Best practice:
// Use === for undefined checks (strict equality)
if (x === undefined) {
  // Only true if x is strictly undefined
}

// Unless you want to check both undefined and null:
if (x == null) {
  // True for both undefined and null
}
```

---

### Q7: Can you assign `undefined` to a variable?

**Answer:**

```javascript
// Yes, but generally shouldn't

let x = undefined; // Valid but weird
console.log(x); // undefined

// Better approaches:
let y; // Just declare without value (undefined by default)
console.log(y); // undefined

let z = null; // Use null for intentional absence
console.log(z); // null

// When you might use explicit undefined:
function reset(config) {
  return {
    name: config.name ?? "Default",
    value: config.value ?? 0,
    optional: config.optional ?? undefined, // Explicitly undefined
  };
}

// Edge case: Delete object property
const obj = { a: 1, b: 2 };
obj.a = undefined; // Property exists but value is undefined
delete obj.b; // Property completely removed

console.log("a" in obj); // true (property exists)
console.log("b" in obj); // false (property doesn't exist)
```

---

### Q8: What's the "void" operator and how does it relate to undefined?

**Answer:**

```javascript
// void operator evaluates expression and returns undefined
console.log(void 0); // undefined
console.log(void (1 + 1)); // undefined
console.log(void "hello"); // undefined

// Why it exists:
// 1. Reliable way to get undefined (before ES5)
const undefinedValue = void 0; // Always returns undefined

// 2. Used in old JavaScript to prevent page navigation
// <a href="javascript:void(0)">Click</a> // Doesn't navigate

// 3. IIFEs (Immediately Invoked Function Expressions)
void (function () {
  console.log("IIFE");
})();

// Modern usage: Rarely needed
// Just use undefined directly:
const x = undefined; // Same as void 0, more readable
```

---

## 🎯 Key Takeaways

### undefined

- ✅ A primitive value and type
- ✅ Means "variable exists but has no value"
- ✅ Assigned automatically by JavaScript
- ✅ Can be checked safely
- ✅ Use `===` for strict checks
- ✅ Use `typeof` to avoid errors

### not defined

- ❌ Not a value, it's an error (ReferenceError)
- ❌ Means "variable doesn't exist at all"
- ❌ Throws error if accessed directly
- ✅ Use `typeof` to check safely
- ✅ Use try/catch to handle

### Best Practices

```javascript
// ✅ Use strict equality
if (value === undefined) {
}

// ✅ Use nullish coalescing for defaults
const result = value ?? defaultValue;

// ✅ Use optional chaining for nested access
const city = user?.profile?.address?.city;

// ✅ Use typeof for safe checks
if (typeof value !== "undefined") {
}

// ✅ Use default parameters
function greet(name = "Guest") {}

// ❌ Avoid loose equality
if (value == undefined) {
} // Checks both null and undefined

// ❌ Avoid assigning undefined
let x = undefined; // Just use: let x;
```

---

**You now understand `undefined` and "not defined" in JavaScript!** 🎉

Key insights:

- ✅ `undefined` = value (variable exists, no value)
- ✅ "not defined" = error (variable doesn't exist)
- ✅ `typeof` is the safest way to check
- ✅ Use modern features: ??, ?., default parameters
- ✅ Always use strict equality (===)

---

_Last Updated: April 8, 2026_
