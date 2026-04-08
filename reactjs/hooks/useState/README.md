# useState Hook - Complete Guide

> **Quick Path**: What → Why → How → Common Patterns → Interview Questions

---

## 🎯 What is useState?

Imagine you're building a counter button. Every time someone clicks it, the number should increase and display should update.

**The problem without useState:**

```typescript
// ❌ This doesn't work in React!
let count = 0;

function Counter() {
  count = count + 1; // Changes the variable but UI doesn't update!
  return <div>{count}</div>;
}
```

**With useState:**

```typescript
// ✅ This works!
function Counter() {
  const [count, setCount] = useState(0);

  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={() => setCount(count + 1)}>Increment</button>
    </div>
  );
}
// UI automatically updates when state changes! 🎉
```

**useState is a React Hook that lets you add state to functional components** - meaning your component can "remember" values and re-render when they change.

---

## 💡 Why useState? (The Problem It Solves)

### Before Hooks (Class Components Era)

```typescript
// Old way - Class components were required for state
class Counter extends React.Component {
  constructor(props) {
    super(props);
    this.state = { count: 0 }; // Verbose!
  }

  increment = () => {
    this.setState({ count: this.state.count + 1 }); // Complicated!
  }

  render() {
    return <button onClick={this.increment}>{this.state.count}</button>;
  }
}
// 15+ lines for a simple counter! 😱
```

### After Hooks (With useState)

```typescript
// New way - Clean and simple!
function Counter() {
  const [count, setCount] = useState(0);

  return (
    <button onClick={() => setCount(count + 1)}>
      {count}
    </button>
  );
}
// Just 7 lines! 🚀
```

**Why useState is Better:**

1. ✅ **No Classes** - Just functions
2. ✅ **Less Boilerplate** - No constructor, no `this`
3. ✅ **Cleaner Code** - Easy to read and maintain
4. ✅ **Better Logic Organization** - Group related state together
5. ✅ **Easier Testing** - Functions are simpler to test

---

## 🚀 How useState Works

### The Syntax

```typescript
const [state, setState] = useState(initialValue);
//     ↑       ↑              ↑
//   current  updater      initial value
//   value    function
```

### Behind the Scenes

```typescript
// When you call useState(0)
const [count, setCount] = useState(0);

// React does this internally:
// 1. Checks if this component was rendered before
// 2. If FIRST render: stores 0 as initial state
// 3. If RE-RENDER: returns the stored state value (not 0 again!)
// 4. Returns [currentValue, updaterFunction]
```

### The Flow

```
┌─────────────────────────────────────────────────┐
│  1. Component Renders                           │
│     useState(0) is called                       │
│     Returns: [0, setCount]                      │
└────────────────┬────────────────────────────────┘
                 │
                 ▼
┌─────────────────────────────────────────────────┐
│  2. User Interaction                            │
│     User clicks button                          │
│     setCount(1) is called                       │
└────────────────┬────────────────────────────────┘
                 │
                 ▼
┌─────────────────────────────────────────────────┐
│  3. State Update                                │
│     React: "State changed! Need to re-render"   │
│     React schedules re-render                   │
└────────────────┬────────────────────────────────┘
                 │
                 ▼
┌─────────────────────────────────────────────────┐
│  4. Re-render                                   │
│     useState(0) called AGAIN                    │
│     But React returns: [1, setCount]            │
│     (Not [0, setCount]!)                        │
└────────────────┬────────────────────────────────┘
                 │
                 ▼
┌─────────────────────────────────────────────────┐
│  5. UI Updates                                  │
│     Component shows new count: 1                │
└─────────────────────────────────────────────────┘
```

---

## 💪 Common Patterns & Examples

### 1. Simple State (Number, String, Boolean)

```typescript
function Examples() {
  // Number
  const [count, setCount] = useState(0);

  // String
  const [name, setName] = useState('');

  // Boolean
  const [isOpen, setIsOpen] = useState(false);

  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={() => setCount(count + 1)}>+</button>

      <input value={name} onChange={(e) => setName(e.target.value)} />

      <button onClick={() => setIsOpen(!isOpen)}>
        {isOpen ? 'Close' : 'Open'}
      </button>
    </div>
  );
}
```

### 2. Object State

```typescript
function UserForm() {
  const [user, setUser] = useState({
    name: '',
    email: '',
    age: 0
  });

  // ❌ Wrong way - Direct mutation
  const handleNameChangeWrong = (name: string) => {
    user.name = name; // Doesn't trigger re-render!
    setUser(user);
  };

  // ✅ Correct way - Create new object
  const handleNameChange = (name: string) => {
    setUser({ ...user, name }); // Spread existing, update one field
  };

  const handleEmailChange = (email: string) => {
    setUser(prev => ({ ...prev, email })); // Using prev state
  };

  return (
    <div>
      <input
        value={user.name}
        onChange={(e) => handleNameChange(e.target.value)}
      />
      <input
        value={user.email}
        onChange={(e) => handleEmailChange(e.target.value)}
      />
    </div>
  );
}
```

### 3. Array State

```typescript
function TodoList() {
  const [todos, setTodos] = useState<string[]>([]);
  const [input, setInput] = useState('');

  // Add item
  const addTodo = () => {
    setTodos([...todos, input]); // Spread existing array, add new item
    setInput('');
  };

  // Remove item
  const removeTodo = (index: number) => {
    setTodos(todos.filter((_, i) => i !== index));
  };

  // Update item
  const updateTodo = (index: number, newValue: string) => {
    setTodos(todos.map((todo, i) => i === index ? newValue : todo));
  };

  return (
    <div>
      <input value={input} onChange={(e) => setInput(e.target.value)} />
      <button onClick={addTodo}>Add</button>

      {todos.map((todo, index) => (
        <div key={index}>
          {todo}
          <button onClick={() => removeTodo(index)}>Delete</button>
        </div>
      ))}
    </div>
  );
}
```

### 4. Functional Updates (Important!)

```typescript
function Counter() {
  const [count, setCount] = useState(0);

  // ❌ Problem - Multiple updates in same render
  const incrementThreeTimes = () => {
    setCount(count + 1); // count is 0, sets to 1
    setCount(count + 1); // count is still 0, sets to 1
    setCount(count + 1); // count is still 0, sets to 1
    // Final result: count = 1 (not 3!) 😱
  };

  // ✅ Solution - Use functional update
  const incrementThreeTimesCorrect = () => {
    setCount(prev => prev + 1); // takes previous value, adds 1
    setCount(prev => prev + 1); // takes previous value, adds 1
    setCount(prev => prev + 1); // takes previous value, adds 1
    // Final result: count = 3 ✅
  };

  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={incrementThreeTimesCorrect}>+3</button>
    </div>
  );
}
```

### 5. Lazy Initialization (Performance Optimization)

```typescript
function ExpensiveComponent() {
  // ❌ Bad - Function runs on every render
  const [data, setData] = useState(expensiveCalculation());

  // ✅ Good - Function runs only once (initial render)
  const [data, setData] = useState(() => expensiveCalculation());

  // Example
  const [todos, setTodos] = useState(() => {
    // This only runs once!
    const savedTodos = localStorage.getItem('todos');
    return savedTodos ? JSON.parse(savedTodos) : [];
  });

  return <div>{/* ... */}</div>;
}

function expensiveCalculation() {
  console.log('Computing...');
  let result = 0;
  for (let i = 0; i < 1000000; i++) {
    result += i;
  }
  return result;
}
```

---

## ⚠️ Common Mistakes & How to Fix Them

### Mistake 1: Directly Mutating State

```typescript
// ❌ Wrong
const [user, setUser] = useState({ name: "John", age: 25 });
user.name = "Jane"; // Direct mutation - doesn't trigger re-render!
setUser(user);

// ✅ Correct
setUser({ ...user, name: "Jane" }); // Create new object
```

### Mistake 2: Using State Value Immediately After Update

```typescript
// ❌ Wrong
const [count, setCount] = useState(0);

const handleClick = () => {
  setCount(count + 1);
  console.log(count); // Still prints 0! State updates are async
};

// ✅ Correct - Use useEffect
const handleClick = () => {
  const newCount = count + 1;
  setCount(newCount);
  console.log(newCount); // Prints correct value
};

// Or use useEffect to react to state changes
useEffect(() => {
  console.log("Count changed:", count);
}, [count]);
```

### Mistake 3: Forgetting Functional Updates

```typescript
// ❌ Wrong - In async or loops
const [count, setCount] = useState(0);

setTimeout(() => {
  setCount(count + 1); // Uses stale count value!
}, 1000);

// ✅ Correct
setTimeout(() => {
  setCount((prev) => prev + 1); // Uses latest value
}, 1000);
```

---

## 🎨 Real-World Examples

### Example 1: Form with Validation

```typescript
function LoginForm() {
  const [email, setEmail] = useState('');
  const [password, setPassword] = useState('');
  const [errors, setErrors] = useState({ email: '', password: '' });

  const validate = () => {
    const newErrors = { email: '', password: '' };

    if (!email.includes('@')) {
      newErrors.email = 'Invalid email';
    }

    if (password.length < 6) {
      newErrors.password = 'Password too short';
    }

    setErrors(newErrors);
    return !newErrors.email && !newErrors.password;
  };

  const handleSubmit = (e: React.FormEvent) => {
    e.preventDefault();
    if (validate()) {
      console.log('Form valid!', { email, password });
    }
  };

  return (
    <form onSubmit={handleSubmit}>
      <input
        type="email"
        value={email}
        onChange={(e) => setEmail(e.target.value)}
        placeholder="Email"
      />
      {errors.email && <span className="error">{errors.email}</span>}

      <input
        type="password"
        value={password}
        onChange={(e) => setPassword(e.target.value)}
        placeholder="Password"
      />
      {errors.password && <span className="error">{errors.password}</span>}

      <button type="submit">Login</button>
    </form>
  );
}
```

### Example 2: Toggle Switch

```typescript
function ToggleSwitch() {
  const [isOn, setIsOn] = useState(false);

  return (
    <div>
      <button
        onClick={() => setIsOn(!isOn)}
        style={{
          backgroundColor: isOn ? 'green' : 'gray',
          color: 'white'
        }}
      >
        {isOn ? 'ON' : 'OFF'}
      </button>
    </div>
  );
}
```

### Example 3: Shopping Cart

```typescript
interface CartItem {
  id: number;
  name: string;
  price: number;
  quantity: number;
}

function ShoppingCart() {
  const [cart, setCart] = useState<CartItem[]>([]);

  const addItem = (item: Omit<CartItem, 'quantity'>) => {
    const existing = cart.find(i => i.id === item.id);

    if (existing) {
      // Increase quantity if item exists
      setCart(cart.map(i =>
        i.id === item.id
          ? { ...i, quantity: i.quantity + 1 }
          : i
      ));
    } else {
      // Add new item
      setCart([...cart, { ...item, quantity: 1 }]);
    }
  };

  const removeItem = (id: number) => {
    setCart(cart.filter(item => item.id !== id));
  };

  const updateQuantity = (id: number, quantity: number) => {
    setCart(cart.map(item =>
      item.id === id ? { ...item, quantity } : item
    ));
  };

  const total = cart.reduce((sum, item) =>
    sum + (item.price * item.quantity), 0
  );

  return (
    <div>
      <h2>Shopping Cart</h2>
      {cart.map(item => (
        <div key={item.id}>
          <span>{item.name} - ${item.price}</span>
          <input
            type="number"
            value={item.quantity}
            onChange={(e) => updateQuantity(item.id, Number(e.target.value))}
          />
          <button onClick={() => removeItem(item.id)}>Remove</button>
        </div>
      ))}
      <h3>Total: ${total}</h3>
    </div>
  );
}
```

---

## 📚 Quick Reference

### Basic Syntax

```typescript
// Primitive values
const [state, setState] = useState(initialValue);

// With TypeScript
const [count, setCount] = useState<number>(0);
const [name, setName] = useState<string>("");
const [user, setUser] = useState<User | null>(null);

// Update state
setState(newValue); // Direct value
setState((prev) => newValue); // Functional update
```

### Rules of Hooks

1. ✅ **Only call at top level** - Not in loops, conditions, or nested functions
2. ✅ **Only call in React functions** - Components or custom hooks
3. ✅ **Always in same order** - React relies on call order to track state

```typescript
// ❌ Wrong
function BadComponent({ condition }) {
  if (condition) {
    const [state, setState] = useState(0); // Conditional hook!
  }
  return <div>Bad</div>;
}

// ✅ Correct
function GoodComponent({ condition }) {
  const [state, setState] = useState(0); // Always called

  if (condition) {
    // Use the state conditionally instead
  }

  return <div>Good</div>;
}
```

---

## 🎤 Interview Questions & Answers

### Q1: Why does useState return an array instead of an object?

**Answer:**

```typescript
// If useState returned an object:
const state = useState(0);
// You'd have to use: state.value and state.setValue
// Problem: Can't rename easily!

const state1 = useState(0);
const state2 = useState(0);
// Now you have state1.value and state2.value - confusing!

// With array destructuring:
const [count, setCount] = useState(0);
const [age, setAge] = useState(25);
// You can name them whatever you want! 🎉

// Array destructuring = flexible naming
// Object destructuring = fixed property names
```

**Key Reason:** Arrays allow you to name the returned values whatever you want, making it easy to have multiple states with meaningful names.

### Q2: What happens if you call setState with the same value?

**Answer:**

```typescript
const [count, setCount] = useState(0);

setCount(0); // Same as current value

// React's behavior:
// 1. Compares new value with current using Object.is()
// 2. If same, React BAILS OUT (doesn't re-render)
// 3. Performance optimization!

// Exception: First time you call it still triggers render
```

**Key Point:** React uses `Object.is()` comparison. If the value is the same, React skips re-rendering (optimization).

### Q3: Why do we need functional updates (prev => prev + 1)?

**Answer:**

```typescript
const [count, setCount] = useState(0);

// Scenario: Multiple updates in same event
const handleClick = () => {
  setCount(count + 1); // count = 0, queues update to 1
  setCount(count + 1); // count = 0 (still!), queues update to 1
  setCount(count + 1); // count = 0 (still!), queues update to 1
  // Result: count becomes 1 (not 3!)
};

// With functional update:
const handleClickCorrect = () => {
  setCount((prev) => prev + 1); // gets 0, returns 1
  setCount((prev) => prev + 1); // gets 1, returns 2
  setCount((prev) => prev + 1); // gets 2, returns 3
  // Result: count becomes 3! ✅
};
```

**Key Reason:** State updates are batched and asynchronous. Functional updates guarantee you're using the latest state value, not a stale closure value.

### Q4: Can you call useState conditionally?

**Answer:**

```typescript
// ❌ NEVER do this!
function BadComponent({ showCount }) {
  if (showCount) {
    const [count, setCount] = useState(0); // ERROR!
  }
  return <div>Bad</div>;
}

// Why? React tracks hooks by call ORDER
// Render 1: showCount = true  → useState called (position 1)
// Render 2: showCount = false → useState NOT called
// React: "Wait, where's hook at position 1?" 💥

// ✅ Always call hooks at top level
function GoodComponent({ showCount }) {
  const [count, setCount] = useState(0); // Always called

  return (
    <div>
      {showCount && <p>{count}</p>} {/* Conditionally RENDER */}
    </div>
  );
}
```

**Key Rule:** Hooks must be called in the same order on every render. React relies on call order to match hooks with their state.

### Q5: What's the difference between setState(value) and setState(prev => value)?

**Answer:**

```typescript
const [count, setCount] = useState(0);

// Direct value:
setCount(5);
// Uses: When new value doesn't depend on previous state
// Example: Setting from API response, resetting to default

// Functional update:
setCount((prev) => prev + 1);
// Uses: When new value depends on previous state
// Guarantees you get the latest state value

// Real scenario:
const incrementAsync = () => {
  setTimeout(() => {
    setCount(count + 1); // ❌ Uses stale count value
    setCount((prev) => prev + 1); // ✅ Uses latest count value
  }, 1000);
};
```

**Key Difference:**

- Direct: Use when new value is independent
- Functional: Use when new value depends on previous state (always safe!)

### Q6: Why does my state not update immediately after setState?

**Answer:**

```typescript
const [count, setCount] = useState(0);

const handleClick = () => {
  setCount(5);
  console.log(count); // Prints 0, not 5! 😱
};

// Why? setState is ASYNCHRONOUS
// React batches state updates for performance
// Component re-renders after the event handler completes

// To use the new value immediately:
const handleClickCorrect = () => {
  const newCount = 5;
  setCount(newCount);
  console.log(newCount); // Prints 5 ✅
};

// To react to state changes, use useEffect:
useEffect(() => {
  console.log("Count updated:", count);
}, [count]);
```

**Key Point:** `setState` is asynchronous and batched. State updates don't happen immediately within the same function call.

### Q7: How do you update object/array state correctly?

**Answer:**

```typescript
// ❌ Wrong - Direct mutation
const [user, setUser] = useState({ name: "John", age: 25 });
user.age = 26; // Mutation!
setUser(user); // Same object reference, React might not re-render

// ✅ Correct - Create new object/array
setUser({ ...user, age: 26 }); // New object with spread

// Arrays
const [items, setItems] = useState([1, 2, 3]);

// Add item
setItems([...items, 4]); // ✅
items.push(4); // ❌

// Remove item
setItems(items.filter((item) => item !== 2)); // ✅

// Update item
setItems(items.map((item) => (item === 2 ? 20 : item))); // ✅
```

**Key Rule:** Always create a new object/array. Never mutate state directly. React compares by reference for objects/arrays.

### Q8: What is lazy initialization and when to use it?

**Answer:**

```typescript
// ❌ Without lazy initialization
const [data, setData] = useState(expensiveFunction());
// Problem: expensiveFunction() runs on EVERY render!

// ✅ With lazy initialization
const [data, setData] = useState(() => expensiveFunction());
// Solution: expensiveFunction() runs ONLY on first render!

// Real example:
const [todos, setTodos] = useState(() => {
  // This only runs once
  const saved = localStorage.getItem("todos");
  return saved ? JSON.parse(saved) : [];
});

// Use lazy initialization when:
// 1. Initial value requires computation
// 2. Reading from localStorage/sessionStorage
// 3. Expensive calculations
// 4. Initial value depends on props (but be careful!)
```

**Key Point:** Pass a function to `useState` to defer expensive initialization until it's actually needed (first render only).

---

## 🎯 Key Takeaways

1. **useState = State in Functional Components**
   - Add state without classes
   - Returns array: `[currentValue, updateFunction]`

2. **Array Destructuring**
   - Flexible naming for multiple states
   - `const [count, setCount] = useState(0)`

3. **Immutability**
   - Never mutate state directly
   - Always create new objects/arrays
   - `setUser({ ...user, name: 'Jane' })`

4. **Functional Updates**
   - Use when new value depends on previous state
   - Guarantees latest state value
   - `setState(prev => prev + 1)`

5. **Async Nature**
   - State updates don't happen immediately
   - React batches updates for performance
   - Use `useEffect` to react to state changes

---

**That's useState!** 🎉

You now know:

- ✅ What useState is and why we need it
- ✅ How it works under the hood
- ✅ Common patterns and best practices
- ✅ How to avoid common mistakes
- ✅ Interview questions and answers

**Next:** Explore other hooks like `useEffect`, `useContext`, `useReducer`! 🚀

---

_Last Updated: April 8, 2026_
