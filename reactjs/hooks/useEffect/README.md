# useEffect Hook - Complete Guide

> **Quick Path**: What → Why → How → Dependency Arrays → Cleanup → Interview Questions

---

## 🎯 What is useEffect?

Imagine you're building a component that needs to fetch user data from an API when it loads, or update the document title based on a counter value.

**The problem:**

```typescript
// ❌ This doesn't work properly!
function UserProfile({ userId }) {
  const [user, setUser] = useState(null);

  // Where do I fetch the data?
  // Can't do it here - runs on every render!
  fetch(`/api/users/${userId}`)
    .then(res => res.json())
    .then(data => setUser(data)); // Infinite loop! 😱

  return <div>{user?.name}</div>;
}
```

**With useEffect:**

```typescript
// ✅ This works!
function UserProfile({ userId }) {
  const [user, setUser] = useState(null);

  useEffect(() => {
    fetch(`/api/users/${userId}`)
      .then(res => res.json())
      .then(data => setUser(data));
  }, [userId]); // Only runs when userId changes ✅

  return <div>{user?.name}</div>;
}
```

**useEffect is a React Hook that lets you perform side effects in functional components** - like fetching data, subscribing to events, updating the DOM, or setting up timers.

**Side Effect = Anything that affects something outside the component** (API calls, DOM changes, timers, subscriptions).

---

## 💡 Why useEffect? (The Problem It Solves)

### Before Hooks (Class Components)

```typescript
// Old way - Multiple lifecycle methods
class UserProfile extends React.Component {
  componentDidMount() {
    // Runs after first render
    this.fetchUser();
    document.addEventListener("click", this.handleClick);
  }

  componentDidUpdate(prevProps) {
    // Runs after every update
    if (prevProps.userId !== this.props.userId) {
      this.fetchUser();
    }
  }

  componentWillUnmount() {
    // Cleanup
    document.removeEventListener("click", this.handleClick);
  }

  fetchUser() {
    fetch(`/api/users/${this.props.userId}`)
      .then((res) => res.json())
      .then((data) => this.setState({ user: data }));
  }

  // Related logic is split across multiple methods! 😱
}
```

### After Hooks (With useEffect)

```typescript
// New way - All related logic in one place!
function UserProfile({ userId }) {
  const [user, setUser] = useState(null);

  useEffect(() => {
    // Mount + Update logic together
    fetch(`/api/users/${userId}`)
      .then(res => res.json())
      .then(data => setUser(data));

    const handleClick = () => console.log('Clicked');
    document.addEventListener('click', handleClick);

    // Cleanup in the same place!
    return () => {
      document.removeEventListener('click', handleClick);
    };
  }, [userId]);

  return <div>{user?.name}</div>;
}
// All related logic in one useEffect! 🚀
```

**Why useEffect is Better:**

1. ✅ **Collocated Logic** - Related code stays together
2. ✅ **No Class Syntax** - Just functions
3. ✅ **Easier Cleanup** - Return a cleanup function from the same effect
4. ✅ **Fine-Grained Control** - Multiple effects for different concerns
5. ✅ **Dependency Tracking** - Automatic re-run when dependencies change

---

## 🚀 How useEffect Works

### The Syntax

```typescript
useEffect(() => {
  // Side effect code here

  return () => {
    // Cleanup code here (optional)
  };
}, [dependencies]); // Dependency array (optional)
//   ↑
//   When to re-run the effect
```

### The Three Forms

```typescript
// 1. Runs after EVERY render
useEffect(() => {
  console.log("Runs after every render");
}); // No dependency array

// 2. Runs ONCE after initial render (like componentDidMount)
useEffect(() => {
  console.log("Runs only once");
}, []); // Empty dependency array

// 3. Runs when specific values change
useEffect(() => {
  console.log("Runs when count or name changes");
}, [count, name]); // Dependency array with values
```

### The Execution Flow

```
┌─────────────────────────────────────────────────┐
│  1. Component Renders                           │
│     JSX is returned, DOM is updated             │
└────────────────┬────────────────────────────────┘
                 │
                 ▼
┌─────────────────────────────────────────────────┐
│  2. Browser Paints the Screen                   │
│     User sees the updated UI                    │
└────────────────┬────────────────────────────────┘
                 │
                 ▼
┌─────────────────────────────────────────────────┐
│  3. useEffect Runs                              │
│     Side effects execute AFTER painting         │
│     (Doesn't block visual updates)              │
└────────────────┬────────────────────────────────┘
                 │
                 ▼
┌─────────────────────────────────────────────────┐
│  4. Dependency Check on Re-render               │
│     Did dependencies change?                    │
│     Yes → Run cleanup, then effect again        │
│     No  → Skip effect                           │
└─────────────────────────────────────────────────┘
```

### Behind the Scenes

```typescript
const [count, setCount] = useState(0);

useEffect(() => {
  console.log("Count changed:", count);
}, [count]);

// What React does internally:
// Render 1: count = 0
//   → Store [0] as previous dependencies
//   → Run effect
//   → Console: "Count changed: 0"

// Render 2: count = 1
//   → Compare: [1] vs [0] → Different!
//   → Run cleanup (if exists)
//   → Run effect
//   → Console: "Count changed: 1"

// Render 3: count = 1 (same)
//   → Compare: [1] vs [1] → Same!
//   → Skip effect
//   → Nothing happens
```

---

## 💪 Dependency Array Patterns

### 1. No Dependency Array (Rarely Used)

```typescript
// ❌ Usually not what you want
useEffect(() => {
  console.log("Runs after EVERY render");
  // Including re-renders from parent!
}); // Dangerous - can cause performance issues
```

### 2. Empty Dependency Array (Common)

```typescript
// ✅ Run once on mount
useEffect(() => {
  console.log("Component mounted");

  // Perfect for:
  // - Initial data fetching
  // - Setting up subscriptions
  // - Adding event listeners

  return () => {
    console.log("Component unmounting");
  };
}, []); // Runs once, cleanup on unmount
```

### 3. With Dependencies (Most Common)

```typescript
// ✅ Run when specific values change
useEffect(() => {
  console.log("Count or name changed");
  // Refetch data when these change
}, [count, name]); // Only re-runs when count or name changes
```

### 4. ESLint Exhaustive Deps Rule

```typescript
function UserProfile({ userId }) {
  const [user, setUser] = useState(null);
  const apiUrl = "https://api.example.com";

  // ⚠️ ESLint warning: Missing dependencies!
  useEffect(() => {
    fetch(`${apiUrl}/users/${userId}`)
      .then((res) => res.json())
      .then((data) => setUser(data));
  }, []); // Should include: apiUrl, userId

  // ✅ Correct
  useEffect(() => {
    fetch(`${apiUrl}/users/${userId}`)
      .then((res) => res.json())
      .then((data) => setUser(data));
  }, [apiUrl, userId]); // Include all used values!
}
```

### 5. Functions as Dependencies

```typescript
// ❌ Problem - Function recreated every render
function UserProfile({ userId }) {
  const fetchUser = () => {
    fetch(`/api/users/${userId}`)
      .then((res) => res.json())
      .then((data) => setUser(data));
  };

  useEffect(() => {
    fetchUser();
  }, [fetchUser]); // fetchUser changes every render! Infinite loop!
}

// ✅ Solution 1: Move function inside effect
useEffect(() => {
  const fetchUser = () => {
    fetch(`/api/users/${userId}`)
      .then((res) => res.json())
      .then((data) => setUser(data));
  };

  fetchUser();
}, [userId]); // Only userId as dependency

// ✅ Solution 2: Use useCallback
const fetchUser = useCallback(() => {
  fetch(`/api/users/${userId}`)
    .then((res) => res.json())
    .then((data) => setUser(data));
}, [userId]); // Memoized function

useEffect(() => {
  fetchUser();
}, [fetchUser]); // Safe now!
```

---

## 🧹 Cleanup Functions

### Why Cleanup Matters

```typescript
// ❌ Without cleanup - Memory leak!
useEffect(() => {
  const interval = setInterval(() => {
    console.log("Tick");
  }, 1000);

  // No cleanup → interval keeps running even after unmount! 😱
}, []);

// ✅ With cleanup - Proper cleanup
useEffect(() => {
  const interval = setInterval(() => {
    console.log("Tick");
  }, 1000);

  return () => {
    clearInterval(interval); // Cleanup on unmount
  };
}, []);
```

### Common Cleanup Scenarios

```typescript
// 1. Event Listeners
useEffect(() => {
  const handleResize = () => console.log("Resized");
  window.addEventListener("resize", handleResize);

  return () => {
    window.removeEventListener("resize", handleResize);
  };
}, []);

// 2. Subscriptions
useEffect(() => {
  const subscription = eventEmitter.subscribe("event", handler);

  return () => {
    subscription.unsubscribe();
  };
}, []);

// 3. Timers
useEffect(() => {
  const timer = setTimeout(() => console.log("Hello"), 1000);

  return () => {
    clearTimeout(timer);
  };
}, []);

// 4. Fetch Abort
useEffect(() => {
  const controller = new AbortController();

  fetch("/api/data", { signal: controller.signal })
    .then((res) => res.json())
    .then((data) => setState(data))
    .catch((err) => {
      if (err.name !== "AbortError") {
        console.error(err);
      }
    });

  return () => {
    controller.abort(); // Cancel fetch on unmount
  };
}, []);
```

---

## 📋 Common Patterns & Examples

### Pattern 1: Data Fetching

```typescript
function UserProfile({ userId }: { userId: string }) {
  const [user, setUser] = useState<User | null>(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState<string | null>(null);

  useEffect(() => {
    // Reset state when userId changes
    setLoading(true);
    setError(null);

    fetch(`/api/users/${userId}`)
      .then(res => {
        if (!res.ok) throw new Error('Failed to fetch');
        return res.json();
      })
      .then(data => {
        setUser(data);
        setLoading(false);
      })
      .catch(err => {
        setError(err.message);
        setLoading(false);
      });
  }, [userId]); // Refetch when userId changes

  if (loading) return <div>Loading...</div>;
  if (error) return <div>Error: {error}</div>;
  return <div>{user?.name}</div>;
}
```

### Pattern 2: Document Title Update

```typescript
function Counter() {
  const [count, setCount] = useState(0);

  useEffect(() => {
    document.title = `Count: ${count}`;
  }, [count]); // Update title when count changes

  return (
    <button onClick={() => setCount(count + 1)}>
      Increment {count}
    </button>
  );
}
```

### Pattern 3: LocalStorage Sync

```typescript
function TodoApp() {
  const [todos, setTodos] = useState<string[]>(() => {
    // Load from localStorage on mount
    const saved = localStorage.getItem('todos');
    return saved ? JSON.parse(saved) : [];
  });

  // Save to localStorage whenever todos change
  useEffect(() => {
    localStorage.setItem('todos', JSON.stringify(todos));
  }, [todos]);

  return <TodoList todos={todos} onUpdate={setTodos} />;
}
```

### Pattern 4: Window Event Listener

```typescript
function MouseTracker() {
  const [position, setPosition] = useState({ x: 0, y: 0 });

  useEffect(() => {
    const handleMouseMove = (e: MouseEvent) => {
      setPosition({ x: e.clientX, y: e.clientY });
    };

    window.addEventListener('mousemove', handleMouseMove);

    return () => {
      window.removeEventListener('mousemove', handleMouseMove);
    };
  }, []); // Only setup once

  return <div>X: {position.x}, Y: {position.y}</div>;
}
```

### Pattern 5: WebSocket Connection

```typescript
function ChatRoom({ roomId }: { roomId: string }) {
  const [messages, setMessages] = useState<string[]>([]);

  useEffect(() => {
    const ws = new WebSocket(`wss://chat.example.com/${roomId}`);

    ws.onmessage = (event) => {
      setMessages(prev => [...prev, event.data]);
    };

    ws.onerror = (error) => {
      console.error('WebSocket error:', error);
    };

    return () => {
      ws.close(); // Cleanup: close connection on unmount
    };
  }, [roomId]); // Reconnect when room changes

  return (
    <div>
      {messages.map((msg, i) => <div key={i}>{msg}</div>)}
    </div>
  );
}
```

### Pattern 6: Debounced Search

```typescript
function SearchBox() {
  const [query, setQuery] = useState('');
  const [results, setResults] = useState<string[]>([]);

  useEffect(() => {
    // Debounce: wait 500ms after user stops typing
    const timer = setTimeout(() => {
      if (query) {
        fetch(`/api/search?q=${query}`)
          .then(res => res.json())
          .then(data => setResults(data));
      } else {
        setResults([]);
      }
    }, 500);

    return () => {
      clearTimeout(timer); // Cancel previous timer on each keystroke
    };
  }, [query]); // Re-run when query changes

  return (
    <div>
      <input
        value={query}
        onChange={(e) => setQuery(e.target.value)}
      />
      {results.map(result => <div key={result}>{result}</div>)}
    </div>
  );
}
```

### Pattern 7: Interval/Timer

```typescript
function Timer() {
  const [seconds, setSeconds] = useState(0);
  const [isRunning, setIsRunning] = useState(false);

  useEffect(() => {
    if (!isRunning) return; // Don't start interval if not running

    const interval = setInterval(() => {
      setSeconds(prev => prev + 1);
    }, 1000);

    return () => {
      clearInterval(interval); // Cleanup on unmount or when isRunning changes
    };
  }, [isRunning]); // Re-run when isRunning changes

  return (
    <div>
      <p>Seconds: {seconds}</p>
      <button onClick={() => setIsRunning(!isRunning)}>
        {isRunning ? 'Pause' : 'Start'}
      </button>
    </div>
  );
}
```

---

## ⚠️ Common Mistakes & How to Fix Them

### Mistake 1: Missing Dependencies

```typescript
// ❌ Wrong - Missing dependency
function UserGreeting({ userId }) {
  const [name, setName] = useState('');

  useEffect(() => {
    fetch(`/api/users/${userId}`)
      .then(res => res.json())
      .then(data => setName(data.name));
  }, []); // Missing userId! Stale data on userId change

  return <div>Hello, {name}</div>;
}

// ✅ Correct - Include all dependencies
useEffect(() => {
  fetch(`/api/users/${userId}`)
    .then(res => res.json())
    .then(data => setName(data.name));
}, [userId]); // Now refetches when userId changes
```

### Mistake 2: Updating State That Causes Re-render Loop

```typescript
// ❌ Wrong - Infinite loop!
const [count, setCount] = useState(0);

useEffect(() => {
  setCount(count + 1); // Causes re-render → effect runs again → infinite loop!
});

// ✅ Correct - Add dependency array or condition
useEffect(() => {
  if (count < 10) {
    setCount(count + 1);
  }
}, [count]); // Controlled re-execution
```

### Mistake 3: Forgetting Cleanup

```typescript
// ❌ Wrong - Memory leak!
useEffect(() => {
  const subscription = eventEmitter.subscribe("event", handler);
  // No cleanup! Subscription stays active after unmount
}, []);

// ✅ Correct - Cleanup subscription
useEffect(() => {
  const subscription = eventEmitter.subscribe("event", handler);

  return () => {
    subscription.unsubscribe();
  };
}, []);
```

### Mistake 4: Using Async Function Directly

```typescript
// ❌ Wrong - Can't use async directly
useEffect(async () => {
  const data = await fetch("/api/data");
  // TypeScript error: useEffect expects sync function or cleanup function
}, []);

// ✅ Correct - Define async function inside
useEffect(() => {
  const fetchData = async () => {
    const response = await fetch("/api/data");
    const data = await response.json();
    setState(data);
  };

  fetchData();
}, []);

// ✅ Also correct - Use .then()
useEffect(() => {
  fetch("/api/data")
    .then((res) => res.json())
    .then((data) => setState(data));
}, []);
```

---

## 🎨 Real-World Examples

### Example 1: Dark Mode Toggle with LocalStorage

```typescript
function App() {
  const [darkMode, setDarkMode] = useState(() => {
    const saved = localStorage.getItem('darkMode');
    return saved === 'true';
  });

  useEffect(() => {
    // Update localStorage
    localStorage.setItem('darkMode', String(darkMode));

    // Update document class
    if (darkMode) {
      document.body.classList.add('dark');
    } else {
      document.body.classList.remove('dark');
    }
  }, [darkMode]);

  return (
    <button onClick={() => setDarkMode(!darkMode)}>
      {darkMode ? '☀️ Light' : '🌙 Dark'}
    </button>
  );
}
```

### Example 2: Infinite Scroll with Intersection Observer

```typescript
function InfiniteScroll() {
  const [items, setItems] = useState<string[]>([]);
  const [page, setPage] = useState(1);
  const loaderRef = useRef<HTMLDivElement>(null);

  useEffect(() => {
    // Fetch items for current page
    fetch(`/api/items?page=${page}`)
      .then(res => res.json())
      .then(newItems => setItems(prev => [...prev, ...newItems]));
  }, [page]);

  useEffect(() => {
    const observer = new IntersectionObserver((entries) => {
      if (entries[0].isIntersecting) {
        setPage(prev => prev + 1); // Load next page
      }
    });

    if (loaderRef.current) {
      observer.observe(loaderRef.current);
    }

    return () => {
      observer.disconnect();
    };
  }, []);

  return (
    <div>
      {items.map(item => <div key={item}>{item}</div>)}
      <div ref={loaderRef}>Loading more...</div>
    </div>
  );
}
```

### Example 3: Online/Offline Status

```typescript
function OnlineStatus() {
  const [isOnline, setIsOnline] = useState(navigator.onLine);

  useEffect(() => {
    const handleOnline = () => setIsOnline(true);
    const handleOffline = () => setIsOnline(false);

    window.addEventListener('online', handleOnline);
    window.addEventListener('offline', handleOffline);

    return () => {
      window.removeEventListener('online', handleOnline);
      window.removeEventListener('offline', handleOffline);
    };
  }, []);

  return (
    <div>
      {isOnline ? '🟢 Online' : '🔴 Offline'}
    </div>
  );
}
```

---

## 📚 Quick Reference

### Basic Syntax

```typescript
// Run on every render (rarely used)
useEffect(() => {
  // Effect code
});

// Run once on mount
useEffect(() => {
  // Setup code
  return () => {
    // Cleanup code
  };
}, []);

// Run when dependencies change
useEffect(() => {
  // Effect code
}, [dep1, dep2]);
```

### Common Patterns Cheatsheet

| Pattern         | When to Use               | Cleanup Needed?   |
| --------------- | ------------------------- | ----------------- |
| Data fetching   | Load data on mount/update | Sometimes (abort) |
| Event listeners | Window/document events    | Yes               |
| Subscriptions   | WebSockets, observables   | Yes               |
| Timers          | setTimeout, setInterval   | Yes               |
| LocalStorage    | Persist state             | No                |
| Document title  | Update page title         | No                |

---

## 🎤 Interview Questions & Answers

### Q1: What is the purpose of useEffect?

**Answer:**

```typescript
// useEffect handles side effects in functional components

// Side effects are operations that affect things outside the component:
// ✅ API calls (fetch)
// ✅ DOM manipulation (document.title)
// ✅ Subscriptions (WebSocket, event listeners)
// ✅ Timers (setTimeout, setInterval)
// ✅ Logging

function Example() {
  useEffect(() => {
    // This is a side effect - affects external state
    document.title = 'New Title';
  });

  // This is NOT a side effect - just rendering
  return <div>Hello</div>;
}
```

**Key Point:** useEffect runs AFTER render, so it doesn't block the UI. Side effects run asynchronously after the browser paints.

### Q2: What's the difference between useEffect and useLayoutEffect?

**Answer:**

```typescript
// useEffect: Runs AFTER browser paints (most common)
useEffect(() => {
  console.log("Runs after paint");
  // User sees the update first, then this runs
  // Non-blocking, better performance
}, []);

// useLayoutEffect: Runs BEFORE browser paints (rare)
useLayoutEffect(() => {
  console.log("Runs before paint");
  // This runs first, then user sees update
  // Blocking, use only when necessary
}, []);

// Execution order:
// 1. Component renders
// 2. useLayoutEffect runs (sync, blocking)
// 3. Browser paints (user sees update)
// 4. useEffect runs (async, non-blocking)
```

**When to use useLayoutEffect:**

- Measuring DOM elements (getBoundingClientRect)
- Preventing visual flicker
- Synchronous DOM mutations

**When to use useEffect:**

- Everything else (99% of cases)
- Data fetching
- Subscriptions
- Logging

### Q3: How does the dependency array work?

**Answer:**

```typescript
const [count, setCount] = useState(0);
const [name, setName] = useState("John");

// No dependency array → Runs after EVERY render
useEffect(() => {
  console.log("Every render");
});

// Empty array → Runs ONCE (mount only)
useEffect(() => {
  console.log("Mount only");
}, []);

// With dependencies → Runs when dependencies change
useEffect(() => {
  console.log("Count or name changed");
}, [count, name]);

// How React compares dependencies:
// Uses Object.is() comparison (shallow equality)

const obj = { id: 1 };
useEffect(() => {
  console.log("Runs every render!");
}, [obj]); // ❌ New object every render!

// Solution: Use primitive values or memoize
useEffect(() => {
  console.log("Runs when obj.id changes");
}, [obj.id]); // ✅ Primitive value
```

**Key Rule:** Always include ALL values from component scope that are used inside the effect (except setState functions).

### Q4: Why do we need cleanup functions?

**Answer:**

```typescript
// Without cleanup → Memory leaks and bugs!

function BadTimer() {
  useEffect(() => {
    const interval = setInterval(() => {
      console.log("Tick");
    }, 1000);
    // No cleanup! When component unmounts, interval keeps running!
  }, []);
}

// With cleanup → Proper resource management
function GoodTimer() {
  useEffect(() => {
    const interval = setInterval(() => {
      console.log("Tick");
    }, 1000);

    return () => {
      clearInterval(interval); // Cleanup!
    };
  }, []);
}

// When cleanup runs:
// 1. Before effect re-runs (when dependencies change)
// 2. When component unmounts

// Example: Effect runs twice
useEffect(() => {
  console.log("Effect");
  return () => console.log("Cleanup");
}, [count]);

// Logs:
// count = 0: "Effect"
// count = 1: "Cleanup" → "Effect"  (cleanup → new effect)
// Unmount: "Cleanup"
```

**Always cleanup:**

- Event listeners
- Subscriptions
- Timers
- Async operations (fetch abort)

### Q5: Can you call useEffect conditionally?

**Answer:**

```typescript
// ❌ NEVER do this!
function BadComponent({ shouldFetch }) {
  if (shouldFetch) {
    useEffect(() => {
      // Conditional hook - ERROR!
    }, []);
  }
}

// React relies on hook call ORDER
// Conditional hooks break the order
// Same rule as useState!

// ✅ Correct: Condition INSIDE the effect
function GoodComponent({ shouldFetch }) {
  useEffect(() => {
    if (shouldFetch) {
      // Condition inside effect body
      fetch("/api/data");
    }
  }, [shouldFetch]);
}

// ✅ Also correct: Early return
function GoodComponent2({ shouldFetch }) {
  useEffect(() => {
    if (!shouldFetch) return; // Early return

    fetch("/api/data");
  }, [shouldFetch]);
}
```

**Key Rule:** Always call hooks at the top level, in the same order.

### Q6: How do you handle async operations in useEffect?

**Answer:**

```typescript
// ❌ Can't use async directly
useEffect(async () => {
  // Error! useEffect can't return Promise
  const data = await fetch("/api/data");
}, []);

// ✅ Solution 1: IIFE (Immediately Invoked Function Expression)
useEffect(() => {
  (async () => {
    const response = await fetch("/api/data");
    const data = await response.json();
    setState(data);
  })();
}, []);

// ✅ Solution 2: Named async function
useEffect(() => {
  const fetchData = async () => {
    const response = await fetch("/api/data");
    const data = await response.json();
    setState(data);
  };

  fetchData();
}, []);

// ✅ Solution 3: .then() chain
useEffect(() => {
  fetch("/api/data")
    .then((res) => res.json())
    .then((data) => setState(data));
}, []);

// 🔥 Best practice: Handle cleanup/abort
useEffect(() => {
  const controller = new AbortController();

  const fetchData = async () => {
    try {
      const response = await fetch("/api/data", {
        signal: controller.signal,
      });
      const data = await response.json();
      setState(data);
    } catch (error) {
      if (error.name !== "AbortError") {
        console.error(error);
      }
    }
  };

  fetchData();

  return () => {
    controller.abort(); // Cancel on unmount/re-run
  };
}, []);
```

### Q7: What happens if you don't include a dependency that should be included?

**Answer:**

```typescript
function UserProfile({ userId }) {
  const [user, setUser] = useState(null);

  // ❌ Missing userId dependency
  useEffect(() => {
    fetch(`/api/users/${userId}`)
      .then((res) => res.json())
      .then((data) => setUser(data));
  }, []); // Stale closure!

  // Problem:
  // 1. Component mounts with userId = 1
  // 2. Effect fetches user 1
  // 3. userId prop changes to 2
  // 4. Effect doesn't re-run (empty deps)
  // 5. Still showing user 1 data! Bug! 😱

  // ✅ Include all dependencies
  useEffect(() => {
    fetch(`/api/users/${userId}`)
      .then((res) => res.json())
      .then((data) => setUser(data));
  }, [userId]); // Refetches when userId changes
}

// The "stale closure" problem:
function Counter() {
  const [count, setCount] = useState(0);

  useEffect(() => {
    const interval = setInterval(() => {
      console.log(count); // Always logs 0! Stale!
      setCount(count + 1); // Always sets to 1!
    }, 1000);

    return () => clearInterval(interval);
  }, []); // Missing count dependency

  // Solution: Use functional update
  useEffect(() => {
    const interval = setInterval(() => {
      setCount((prev) => prev + 1); // Uses latest value
    }, 1000);

    return () => clearInterval(interval);
  }, []); // No count needed - using functional update
}
```

**Key Takeaway:** Always include ALL dependencies. Use ESLint plugin `eslint-plugin-react-hooks` to catch missing dependencies automatically!

### Q8: How many useEffect hooks can you have in a component?

**Answer:**

```typescript
// ✅ You can have as MANY as you need!

function UserDashboard({ userId }) {
  const [user, setUser] = useState(null);
  const [posts, setPosts] = useState([]);
  const [online, setOnline] = useState(false);

  // Effect 1: Fetch user data
  useEffect(() => {
    fetch(`/api/users/${userId}`)
      .then((res) => res.json())
      .then((data) => setUser(data));
  }, [userId]);

  // Effect 2: Fetch user posts
  useEffect(() => {
    fetch(`/api/users/${userId}/posts`)
      .then((res) => res.json())
      .then((data) => setPosts(data));
  }, [userId]);

  // Effect 3: Update document title
  useEffect(() => {
    document.title = user ? `${user.name}'s Dashboard` : "Dashboard";
  }, [user]);

  // Effect 4: Listen to online status
  useEffect(() => {
    const subscription = onlineService.subscribe(userId, setOnline);
    return () => subscription.unsubscribe();
  }, [userId]);

  // Effect 5: Log analytics
  useEffect(() => {
    analytics.log("dashboard_view", { userId });
  }, [userId]);
}

// Benefits of multiple effects:
// ✅ Separation of concerns
// ✅ Each effect has specific dependencies
// ✅ Easier to understand and maintain
// ✅ Independent cleanup

// ❌ Don't do this - mixing concerns:
useEffect(() => {
  // Fetch user
  // Fetch posts
  // Update title
  // Subscribe to status
  // All mixed together - hard to maintain!
}, [userId]);
```

**Best Practice:** Split unrelated logic into separate effects!

---

## 🎯 Key Takeaways

1. **What is useEffect?**
   - Hook for side effects (API calls, subscriptions, DOM updates)
   - Runs AFTER render (doesn't block UI)

2. **Dependency Array**
   - No array → Runs every render
   - `[]` → Runs once (mount)
   - `[dep]` → Runs when dep changes

3. **Cleanup Function**
   - Return function from effect → runs on cleanup
   - Cleanup before re-running effect or unmounting
   - Always cleanup: event listeners, timers, subscriptions

4. **Common Patterns**
   - Data fetching with loading/error states
   - Event listeners with cleanup
   - LocalStorage sync
   - Timers and intervals

5. **Avoid Mistakes**
   - Include ALL dependencies (use ESLint)
   - Can't use async directly (use async function inside)
   - Always cleanup resources
   - Don't call hooks conditionally

---

**That's useEffect!** 🎉

You now know:

- ✅ What useEffect is and why we need it
- ✅ How dependency arrays work
- ✅ When and how to cleanup
- ✅ Common patterns and real-world examples
- ✅ Interview questions and answers

**Next:** Explore `useContext`, `useReducer`, `useMemo`, `useCallback`! 🚀

---

_Last Updated: April 8, 2026_
