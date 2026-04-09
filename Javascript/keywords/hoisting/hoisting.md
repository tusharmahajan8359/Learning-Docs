# JavaScript Hoisting - Complete Guide

> **Quick Path**: What → Why → How → var vs let/const → Temporal Dead Zone → Interview Questions

---

## 🎯 What Is Hoisting?

Imagine this code works in JavaScript:

```javascript
console.log(greeting); // undefined (not an error!)
var greeting = "Hello";

sayHi(); // "Hi there!" (works before definition!)
function sayHi() {
  console.log("Hi there!");
}
```

**Wait, what?** We're using variables and functions BEFORE they're declared! 🤯

**Hoisting is JavaScript's behavior of moving declarations to the top of their scope before code execution.** It's like JavaScript reads your entire code first, takes note of all declarations, and then runs the code.

Think of it as: **"JavaScript scouts ahead, remembers where things are declared, then executes your code."**

---

## 💡 Why Does Hoisting Exist?

Hoisting wasn't designed as a feature - it's a consequence of how JavaScript's execution context works!

### The Two-Phase Process

JavaScript executes code in **two phases**:

```javascript
// ====== PHASE 1: CREATION PHASE ======
// JavaScript scans code and sets up memory for:
// - Variables (initialized as undefined)
// - Functions (stored completely)

// ====== PHASE 2: EXECUTION PHASE ======
// JavaScript runs code line by line
```

**Example:**

```javascript
console.log(name); // undefined (memory allocated in Phase 1)
var name = "John";
console.log(name); // "John" (assignment happened in Phase 2)

// What JavaScript actually does:
// PHASE 1 (Creation):
// var name = undefined; // Memory allocated

// PHASE 2 (Execution):
// console.log(name); // undefined
// name = "John"; // Assignment
// console.log(name); // "John"
```

**Why this design?**

1. **Allows forward references** - Functions can call each other regardless of order
2. **Mutual recursion** - Functions can be mutually recursive
3. **Flexibility** - Code organization flexibility (declarations can be anywhere)

---

## 🚀 How Hoisting Works: The Complete Picture

### Visual Understanding

```
┌─────────────────────────────────────────────────┐
│              YOUR CODE (what you write)         │
├─────────────────────────────────────────────────┤
│                                                 │
│  console.log(x);  // Line 1                    │
│  var x = 5;       // Line 2                    │
│  console.log(x);  // Line 3                    │
│                                                 │
└─────────────────────────────────────────────────┘
                      ⬇ Hoisting
┌─────────────────────────────────────────────────┐
│        HOW JAVASCRIPT SEES IT (conceptually)    │
├─────────────────────────────────────────────────┤
│                                                 │
│  var x;           // Declaration hoisted        │
│  console.log(x);  // undefined                  │
│  x = 5;           // Assignment stays here      │
│  console.log(x);  // 5                          │
│                                                 │
└─────────────────────────────────────────────────┘
```

---

## 🔥 Variable Hoisting: `var` vs `let` vs `const`

### 1. `var` - Hoisted and Initialized

```javascript
console.log(name); // undefined (hoisted, initialized to undefined)
var name = "John";
console.log(name); // "John"

// How JavaScript interprets it:
var name; // Hoisted and initialized to undefined
console.log(name); // undefined
name = "John"; // Assignment
console.log(name); // "John"
```

**Key Points:**

- ✅ Declaration is hoisted
- ✅ Initialized with `undefined`
- ✅ No error when accessed before declaration
- ⚠️ Can cause bugs (accessing before intended initialization)

### 2. `let` - Hoisted but NOT Initialized (Temporal Dead Zone)

```javascript
console.log(age); // ReferenceError: Cannot access 'age' before initialization
let age = 25;
console.log(age); // 25

// How JavaScript interprets it:
// let age; // Hoisted but NOT initialized
// ⚠️ TDZ (Temporal Dead Zone) from here...
// console.log(age); // ❌ Error! In TDZ
// ...to here
// age = 25; // TDZ ends, initialization happens
// console.log(age); // ✅ Works
```

**Key Points:**

- ✅ Declaration is hoisted
- ❌ NOT initialized (no value)
- ❌ Accessing before declaration = ReferenceError
- ✅ Safer than `var` (catches bugs early)

### 3. `const` - Same as `let` but Requirements

```javascript
console.log(PI); // ReferenceError: Cannot access 'PI' before initialization
const PI = 3.14;
console.log(PI); // 3.14

// Must initialize on declaration
const VALUE; // SyntaxError: Missing initializer in const declaration
const VALUE = 42; // ✅ Correct
```

**Key Points:**

- ✅ Declaration is hoisted
- ❌ NOT initialized (Temporal Dead Zone)
- ❌ Accessing before declaration = ReferenceError
- ⚠️ MUST be initialized on declaration

### Comparison Table

| Feature                       | var                | let            | const          |
| ----------------------------- | ------------------ | -------------- | -------------- |
| **Hoisted?**                  | ✅ Yes             | ✅ Yes         | ✅ Yes         |
| **Initialized on hoist?**     | ✅ Yes (undefined) | ❌ No          | ❌ No          |
| **Temporal Dead Zone?**       | ❌ No              | ✅ Yes         | ✅ Yes         |
| **Access before declaration** | undefined          | ReferenceError | ReferenceError |
| **Scope**                     | Function           | Block          | Block          |
| **Redeclaration**             | ✅ Allowed         | ❌ Not allowed | ❌ Not allowed |
| **Reassignment**              | ✅ Allowed         | ✅ Allowed     | ❌ Not allowed |

---

## 🎪 Function Hoisting

### Function Declarations - Fully Hoisted

```javascript
// ✅ This works!
sayHello(); // "Hello!"

function sayHello() {
  console.log("Hello!");
}

// How JavaScript sees it:
function sayHello() {
  // Entire function hoisted
  console.log("Hello!");
}
sayHello(); // "Hello!"
```

**Key Point:** Function declarations are **fully hoisted** - both declaration AND definition!

### Function Expressions - NOT Fully Hoisted

```javascript
// ❌ This doesn't work!
sayHi(); // TypeError: sayHi is not a function

var sayHi = function () {
  console.log("Hi!");
};

// How JavaScript sees it:
var sayHi; // Variable hoisted, initialized to undefined
sayHi(); // TypeError! (undefined is not a function)
sayHi = function () {
  // Assignment happens here
  console.log("Hi!");
};
```

### Arrow Functions - Same as Function Expressions

```javascript
// ❌ Doesn't work
greet(); // ReferenceError (if let/const) or TypeError (if var)

const greet = () => {
  console.log("Greetings!");
};

// With var:
sayBye(); // TypeError: sayBye is not a function
var sayBye = () => console.log("Bye!");
```

### Function Comparison

```javascript
// ✅ Function Declaration - Works
doSomething1(); // "Declaration works!"
function doSomething1() {
  console.log("Declaration works!");
}

// ❌ Function Expression (var) - TypeError
doSomething2(); // TypeError: doSomething2 is not a function
var doSomething2 = function () {
  console.log("Expression");
};

// ❌ Function Expression (let/const) - ReferenceError
doSomething3(); // ReferenceError: Cannot access before initialization
const doSomething3 = function () {
  console.log("Expression");
};

// ❌ Arrow Function - ReferenceError
doSomething4(); // ReferenceError
const doSomething4 = () => console.log("Arrow");
```

---

## ⚡ Temporal Dead Zone (TDZ) - Deep Dive

**The TDZ is the time between entering a scope and the variable being declared.**

### What is the TDZ?

```javascript
// ⚠️ TDZ starts here for 'name'
console.log(typeof name); // ReferenceError! In TDZ

let name = "John"; // TDZ ends here
// ✅ Can use 'name' safely from here
console.log(name); // "John"
```

### TDZ Visualization

```
┌─────────────────────────────────────────────────┐
│  Scope Starts                                   │
│  ⬇                                              │
│  ┌─────────────────────────────────────────┐   │
│  │  TEMPORAL DEAD ZONE (TDZ)               │   │
│  │  ❌ Cannot access 'name'                │   │
│  │  ❌ ReferenceError if accessed          │   │
│  └─────────────────────────────────────────┘   │
│  ⬇                                              │
│  let name = "John";  ← Declaration              │
│  ⬇                                              │
│  ✅ Can use 'name' from here                    │
└─────────────────────────────────────────────────┘
```

### TDZ in Action

```javascript
function example() {
  // TDZ for 'x' starts

  console.log(a); // undefined (var has no TDZ)
  console.log(x); // ReferenceError! (in TDZ)

  var a = 1;
  let x = 2; // TDZ for 'x' ends

  console.log(x); // 2 ✅
}
```

### Why TDZ Exists

```javascript
// TDZ catches bugs!

// ❌ Without TDZ (var behavior):
console.log(user); // undefined (confusing!)
var user = "John";

// ✅ With TDZ (let/const behavior):
console.log(user); // ReferenceError (clear error!)
let user = "John";
```

**Benefits of TDZ:**

1. ✅ Catches bugs early (using variable before initialization)
2. ✅ Makes `const` possible (ensures initialization before use)
3. ✅ Better code quality (forces proper ordering)

---

## 📋 Common Hoisting Patterns

### Pattern 1: Loop Variables

```javascript
// ❌ Problematic with var
for (var i = 0; i < 3; i++) {
  setTimeout(() => console.log(i), 100);
}
// Output: 3, 3, 3 (all print 3!)

// ✅ Fixed with let (block scoped)
for (let i = 0; i < 3; i++) {
  setTimeout(() => console.log(i), 100);
}
// Output: 0, 1, 2 (correct!)

// Why? var is function-scoped, let is block-scoped
```

### Pattern 2: Conditional Declarations

```javascript
// ❌ var hoists out of block
if (true) {
  var x = 10;
}
console.log(x); // 10 (accessible outside!)

// How JavaScript sees it:
var x;
if (true) {
  x = 10;
}
console.log(x); // 10

// ✅ let stays in block
if (true) {
  let y = 20;
}
console.log(y); // ReferenceError: y is not defined
```

### Pattern 3: Function in Loop

```javascript
// ❌ Common mistake with var
var funcs = [];
for (var i = 0; i < 3; i++) {
  funcs.push(function () {
    console.log(i);
  });
}
funcs[0](); // 3 (expected 0)
funcs[1](); // 3 (expected 1)
funcs[2](); // 3 (expected 2)

// ✅ Fixed with let
var funcs = [];
for (let i = 0; i < 3; i++) {
  funcs.push(function () {
    console.log(i);
  });
}
funcs[0](); // 0 ✅
funcs[1](); // 1 ✅
funcs[2](); // 2 ✅
```

### Pattern 4: Hoisting with Classes

```javascript
// ❌ Classes are NOT hoisted like functions
const myInstance = new MyClass(); // ReferenceError!

class MyClass {
  constructor() {
    this.name = "Class";
  }
}

// ✅ Declare class first
class MyClass {
  constructor() {
    this.name = "Class";
  }
}
const myInstance = new MyClass(); // Works!
```

---

## 🎨 Real-World Examples

### Example 1: Module Pattern (Old Style)

```javascript
// Why this works:
var myModule = (function () {
  // Private variables
  var privateVar = "I'm private";

  // Private function (hoisted)
  function privateFunc() {
    return privateVar;
  }

  // Public API
  return {
    getPrivate: function () {
      return privateFunc(); // Can call hoisted function
    },
  };
})();

console.log(myModule.getPrivate()); // "I'm private"
// console.log(privateVar); // ReferenceError (private)
```

### Example 2: Mutual Recursion

```javascript
// Function hoisting allows mutual recursion
function isEven(n) {
  if (n === 0) return true;
  return isOdd(n - 1); // Can call isOdd before it's declared
}

function isOdd(n) {
  if (n === 0) return false;
  return isEven(n - 1); // Can call isEven
}

console.log(isEven(4)); // true
console.log(isOdd(5)); // true
```

### Example 3: Configuration Objects

```javascript
// ❌ Problematic order with var
console.log(config.apiUrl); // undefined.apiUrl → Error!
var config = {
  apiUrl: "https://api.example.com",
  timeout: 5000,
};

// ✅ Better with const (catches error early)
console.log(config.apiUrl); // ReferenceError (TDZ)
const config = {
  apiUrl: "https://api.example.com",
  timeout: 5000,
};

// ✅ Best practice: declare at top
const config = {
  apiUrl: "https://api.example.com",
  timeout: 5000,
};
console.log(config.apiUrl); // Works!
```

### Example 4: Event Handlers

```javascript
// ❌ Problem: handler not yet defined
document.getElementById("btn").addEventListener("click", handleClick);

// var - undefined at registration (TypeError on click)
var handleClick = function () {
  console.log("Clicked!");
};

// ✅ Solution 1: Function declaration (hoisted)
document.getElementById("btn").addEventListener("click", handleClick);

function handleClick() {
  console.log("Clicked!");
}

// ✅ Solution 2: Define before use
const handleClick = function () {
  console.log("Clicked!");
};
document.getElementById("btn").addEventListener("click", handleClick);
```

---

## 🎤 Interview Questions & Answers

### Q1: What is hoisting in JavaScript?

**Answer:**

```javascript
// Hoisting = JavaScript's behavior of moving declarations to the top

// Example 1: Variable hoisting
console.log(name); // undefined (not an error!)
var name = "John";

// Behind the scenes:
// var name; // Declaration hoisted
// console.log(name); // undefined
// name = "John"; // Assignment stays

// Example 2: Function hoisting
greet(); // "Hello!" (works before declaration!)
function greet() {
  console.log("Hello!");
}

// Behind the scenes:
// function greet() { // Entire function hoisted
//   console.log("Hello!");
// }
// greet();
```

**Key Points:**

- Hoisting moves **declarations** to the top, not assignments
- Functions are fully hoisted (declaration + definition)
- Variables (var) are hoisted but initialized to undefined
- let/const are hoisted but not initialized (TDZ)

---

### Q2: What's the difference between hoisting with var, let, and const?

**Answer:**

```javascript
// var - Hoisted and initialized to undefined
console.log(a); // undefined ✅
var a = 1;

// let - Hoisted but NOT initialized (TDZ)
console.log(b); // ReferenceError ❌
let b = 2;

// const - Same as let, but must initialize on declaration
console.log(c); // ReferenceError ❌
const c = 3;

// Summary table:
```

| Aspect                | var          | let            | const          |
| --------------------- | ------------ | -------------- | -------------- |
| Hoisted?              | ✅ Yes       | ✅ Yes         | ✅ Yes         |
| Initialized on hoist? | ✅ undefined | ❌ No          | ❌ No          |
| TDZ?                  | ❌ No        | ✅ Yes         | ✅ Yes         |
| Before declaration    | undefined    | ReferenceError | ReferenceError |

---

### Q3: What is the Temporal Dead Zone (TDZ)?

**Answer:**

```javascript
// TDZ = Time between scope start and variable declaration

{
  // TDZ starts for 'name'
  console.log(name); // ReferenceError! In TDZ
  let name = "John"; // TDZ ends
  console.log(name); // "John" ✅ After TDZ
}

// Why it exists:
// 1. Catches bugs (using variable before initialization)
// 2. Makes const work (ensures initialization before use)
// 3. Better code quality

// var has NO TDZ:
{
  console.log(x); // undefined (no TDZ)
  var x = 5;
}

// let/const have TDZ:
{
  console.log(y); // ReferenceError (in TDZ)
  let y = 10;
}
```

**TDZ Duration:**

- Starts: When scope is entered
- Ends: When variable is declared and initialized

---

### Q4: How does function hoisting differ from variable hoisting?

**Answer:**

```javascript
// Function Declaration - FULLY hoisted
sayHi(); // "Hi!" ✅ Works!
function sayHi() {
  console.log("Hi!");
}

// Function Expression - Only variable hoisted
sayBye(); // TypeError: sayBye is not a function ❌
var sayBye = function () {
  console.log("Bye!");
};

// Behind the scenes:
var sayBye; // Only variable hoisted (undefined)
sayBye(); // TypeError! (undefined is not a function)
sayBye = function () {
  // Assignment here
  console.log("Bye!");
};

// Arrow Function - Same as function expression
greet(); // TypeError or ReferenceError
const greet = () => console.log("Hello");
```

**Key Differences:**

- **Function Declaration**: Entire function hoisted (name + body)
- **Function Expression**: Only variable hoisted, value is undefined
- **Arrow Function**: Same as function expression

---

### Q5: Can you explain this hoisting behavior?

```javascript
var x = 1;
function foo() {
  console.log(x);
  var x = 2;
  console.log(x);
}
foo();
```

**Answer:**

```javascript
// Output:
// undefined
// 2

// Why?
// Inside foo(), var x is hoisted to top of function scope

// How JavaScript interprets it:
var x = 1; // Global

function foo() {
  var x; // Hoisted, initialized to undefined (shadows global x)
  console.log(x); // undefined (local x, not global)
  x = 2; // Assignment to local x
  console.log(x); // 2 (local x)
}

foo();

// Key concept: Variable hoisting is FUNCTION-SCOPED for var
// The local 'var x' shadows the global 'x' for entire function
```

---

### Q6: What will this code output?

```javascript
function test() {
  console.log(a);
  console.log(foo());

  var a = 1;
  function foo() {
    return 2;
  }
}
test();
```

**Answer:**

```javascript
// Output:
// undefined
// 2

// Explanation:
function test() {
  // After hoisting:
  var a;
  function foo() {
    return 2;
  }

  console.log(a); // undefined (var hoisted, value not assigned yet)
  console.log(foo()); // 2 (function fully hoisted)

  a = 1; // Assignment
}

// Key points:
// 1. var a hoisted but initialized to undefined
// 2. function foo fully hoisted (can be called anywhere in scope)
```

---

### Q7: Why doesn't this work?

```javascript
for (var i = 0; i < 3; i++) {
  setTimeout(() => console.log(i), 100);
}
// Output: 3, 3, 3 (expected: 0, 1, 2)
```

**Answer:**

```javascript
// Problem: var is function-scoped, not block-scoped
// All setTimeout callbacks share the SAME 'i' variable

// What happens:
var i; // Hoisted to top
for (i = 0; i < 3; i++) {
  setTimeout(() => console.log(i), 100);
  // Callbacks created, but execute LATER
}
// Loop finishes, i = 3
// Now callbacks execute, all print 3

// Solution 1: Use let (block-scoped)
for (let i = 0; i < 3; i++) {
  setTimeout(() => console.log(i), 100);
}
// Output: 0, 1, 2 ✅
// Each iteration has its own 'i'

// Solution 2: Use IIFE with var
for (var i = 0; i < 3; i++) {
  (function (j) {
    setTimeout(() => console.log(j), 100);
  })(i);
}
// Output: 0, 1, 2 ✅

// Solution 3: Use bind
for (var i = 0; i < 3; i++) {
  setTimeout(console.log.bind(console, i), 100);
}
// Output: 0, 1, 2 ✅
```

---

### Q8: Are classes hoisted?

**Answer:**

```javascript
// ❌ Classes are NOT hoisted like functions
const obj = new MyClass(); // ReferenceError!

class MyClass {
  constructor() {
    this.name = "Class";
  }
}

// Classes have TDZ like let/const:
// TDZ starts
console.log(MyClass); // ReferenceError
class MyClass {} // TDZ ends
console.log(MyClass); // [class MyClass] ✅

// Why? Classes are syntactic sugar over functions,
// but behave like let/const declarations

// Function declarations ARE hoisted:
const obj = new MyFunction(); // ✅ Works!
function MyFunction() {
  this.name = "Function";
}
```

---

### Q9: What's the output and why?

```javascript
let a = 1;
{
  console.log(a);
  let a = 2;
}
```

**Answer:**

```javascript
// Output: ReferenceError: Cannot access 'a' before initialization

// Why?
let a = 1; // Outer scope

{
  // TDZ starts for block-scoped 'a'
  console.log(a); // ReferenceError! (TDZ)
  let a = 2; // TDZ ends
}

// The block-scoped 'a' shadows the outer 'a'
// Even though outer 'a' exists, we're in TDZ for inner 'a'
// JavaScript knows 'let a' is declared in this block,
// so it doesn't look at outer scope

// If we used var instead:
let a = 1;
{
  console.log(a); // 1 (no shadowing yet)
  var a = 2; // SyntaxError: Identifier 'a' has already been declared
  // (var is function-scoped, conflicts with outer let)
}
```

---

### Q10: How can you check if a variable is hoisted?

**Answer:**

```javascript
// You can't directly "check" hoisting, but you can understand behavior:

// Test 1: Access before declaration
try {
  console.log(test1); // undefined (var hoisted)
  var test1 = 1;
} catch (e) {
  console.log("Error");
}

try {
  console.log(test2); // ReferenceError (let TDZ)
  let test2 = 2;
} catch (e) {
  console.log("Error caught"); // This runs
}

// Test 2: typeof operator
console.log(typeof varVariable); // "undefined" (safe)
var varVariable = 1;

console.log(typeof letVariable); // ReferenceError! (TDZ)
let letVariable = 2;

// Test 3: Function hoisting
console.log(typeof myFunc); // "function" (hoisted)
function myFunc() {}

console.log(typeof myExpr); // "undefined" (var hoisted)
var myExpr = function () {};

// Best practice: Don't rely on hoisting!
// Declare variables at the top of their scope
```

---

## 🎯 Key Takeaways

### Hoisting Basics

- ✅ Declarations are moved to the top of their scope
- ✅ Assignments stay where they are
- ✅ Function declarations are fully hoisted
- ✅ Happens during creation phase (before execution)

### var vs let/const

```javascript
// var - Avoid in modern code
- Hoisted and initialized (undefined)
- No TDZ
- Function-scoped
- Can access before declaration (returns undefined)

// let/const - Prefer these
- Hoisted but NOT initialized
- Has TDZ (ReferenceError before declaration)
- Block-scoped
- Cannot access before declaration
```

### Function Hoisting

```javascript
// ✅ Function Declaration - Fully hoisted
function foo() {} // Can call before declaration

// ❌ Function Expression - Not fully hoisted
var bar = function () {}; // TypeError if called before
const baz = () => {}; // ReferenceError if called before
```

### Best Practices

```javascript
// ✅ DO:
// 1. Use let/const instead of var
// 2. Declare variables at top of scope
// 3. Initialize variables when declared
// 4. Use function declarations for hoisting benefits

// ❌ DON'T:
// 1. Rely on hoisting (explicit is better)
// 2. Use variables before declaration
// 3. Use var in modern code
// 4. Mix var with let/const in same scope
```

### Quick Reference

| Declaration | Hoisted? | Initialized? | TDZ?   | Access Before Declaration |
| ----------- | -------- | ------------ | ------ | ------------------------- |
| `var`       | ✅ Yes   | ✅ undefined | ❌ No  | undefined                 |
| `let`       | ✅ Yes   | ❌ No        | ✅ Yes | ReferenceError            |
| `const`     | ✅ Yes   | ❌ No        | ✅ Yes | ReferenceError            |
| `function`  | ✅ Yes   | ✅ Yes       | ❌ No  | Works!                    |
| `class`     | ✅ Yes   | ❌ No        | ✅ Yes | ReferenceError            |

---

**You now understand hoisting in JavaScript!** 🎉

Key insights:

- ✅ Hoisting = declarations moved to top, assignments stay
- ✅ var = hoisted + initialized (undefined)
- ✅ let/const = hoisted but TDZ (ReferenceError)
- ✅ Functions = fully hoisted (declaration + body)
- ✅ Best practice: Declare at top, use let/const

**Remember:** Hoisting is a JavaScript mechanism, but good code doesn't rely on it! 💪

---

_Last Updated: April 9, 2026_
