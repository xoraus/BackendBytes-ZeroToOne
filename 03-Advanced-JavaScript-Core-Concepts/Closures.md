# Closures in JavaScript

## Overview

A **closure** is a function that remembers and accesses variables from its outer scope even after the outer function has finished executing. Closures are one of JavaScript's most powerful and frequently misunderstood features. They enable data privacy, function factories, and many advanced patterns.

---

## What is a Closure?

In simple terms: **A closure is a function bundled with its surrounding state (lexical environment).**

```javascript
function outer() {
  const message = "Hello from outer!";

  function inner() {
    console.log(message); // inner() remembers message
  }

  return inner;
}

const closureFn = outer();
closureFn(); // "Hello from outer!"
```

Even though `outer()` has finished executing and its execution context is gone, `closureFn()` still has access to `message`. That's a closure!

---

## How Closures Work

When a function is defined, JavaScript captures its **lexical environment** (the variables in scope at the time of definition). This environment is "closed over" and travels with the function wherever it goes.

```javascript
function makeCounter() {
  let count = 0; // Variable in outer scope

  return {
    increment: function() {
      count++;     // Closure accesses count
      return count;
    },
    decrement: function() {
      count--;
      return count;
    },
    getCount: function() {
      return count;
    }
  };
}

const counter = makeCounter();

console.log(counter.increment()); // 1
console.log(counter.increment()); // 2
console.log(counter.decrement()); // 1
console.log(counter.getCount());  // 1

// count is NOT accessible directly!
// console.log(counter.count); // undefined
```

**What's happening:**
1. `makeCounter` creates a `count` variable
2. It returns an object with three functions
3. Each function forms a closure over `count`
4. Even after `makeCounter` finishes, the returned functions retain access to `count`
5. `count` is "private" — it can't be accessed directly

---

## Practical Uses of Closures

### 1. Data Privacy (Private Variables)

JavaScript doesn't have true private class fields (pre-ES2022), but closures achieve the same effect:

```javascript
function createBankAccount(initialBalance) {
  let balance = initialBalance; // Private variable

  return {
    deposit(amount) {
      if (amount > 0) {
        balance += amount;
        return `Deposited $${amount}. New balance: $${balance}`;
      }
      return "Invalid amount";
    },
    withdraw(amount) {
      if (amount > 0 && amount <= balance) {
        balance -= amount;
        return `Withdrawn $${amount}. New balance: $${balance}`;
      }
      return "Insufficient funds or invalid amount";
    },
    getBalance() {
      return `Current balance: $${balance}`;
    }
  };
}

const account = createBankAccount(1000);
console.log(account.getBalance());   // "Current balance: $1000"
console.log(account.deposit(500));   // "Deposited $500. New balance: $1500"
console.log(account.withdraw(200));  // "Withdrawn $200. New balance: $1300"
// console.log(account.balance);     // undefined — can't access directly!
```

### 2. Function Factories

```javascript
function makeMultiplier(factor) {
  return function(number) {
    return number * factor;
  };
}

const double = makeMultiplier(2);
const triple = makeMultiplier(3);
const quadruple = makeMultiplier(4);

console.log(double(5));    // 10
console.log(triple(5));    // 15
console.log(quadruple(5)); // 20
```

Each returned function "closes over" its own `factor` value.

### 3. Maintaining State in Event Handlers

```javascript
function setupButton(buttonId) {
  let clickCount = 0;

  document.getElementById(buttonId).addEventListener("click", function() {
    clickCount++;
    console.log(`Button clicked ${clickCount} times`);
  });
}

// Each button gets its own clickCount
setupButton("btn1");
setupButton("btn2");
```

### 4. Memoization with Closures

```javascript
function memoize(fn) {
  const cache = {}; // Private cache

  return function(...args) {
    const key = JSON.stringify(args);

    if (key in cache) {
      console.log("Returning from cache");
      return cache[key];
    }

    console.log("Computing result");
    const result = fn.apply(this, args);
    cache[key] = result;
    return result;
  };
}

const slowAdd = memoize(function(a, b) {
  // Simulate expensive computation
  for (let i = 0; i < 100000000; i++) {}
  return a + b;
});

console.log(slowAdd(5, 3)); // Computing... 8
console.log(slowAdd(5, 3)); // Returning from cache... 8
console.log(slowAdd(2, 7)); // Computing... 9
```

### 5. Module Pattern

Before ES6 modules, closures provided a way to create encapsulated modules:

```javascript
const myModule = (function() {
  // Private variables and functions
  let privateVar = 0;

  function privateMethod() {
    console.log("I'm private");
  }

  // Public API
  return {
    publicMethod: function() {
      privateVar++;
      privateMethod();
      console.log(`Private var is now: ${privateVar}`);
    },
    getPrivateVar: function() {
      return privateVar;
    }
  };
})();

myModule.publicMethod(); // "I'm private", "Private var is now: 1"
console.log(myModule.getPrivateVar()); // 1
// myModule.privateMethod(); // Error!
```

---

## Closures in Loops: The Classic Gotcha

This is the most common closure interview question:

```javascript
// ❌ WRONG — All functions log the same value
for (var i = 0; i < 3; i++) {
  setTimeout(function() {
    console.log(i); // 3, 3, 3 (not 0, 1, 2!)
  }, 100);
}
```

**Why?** All three closures share the same `i` variable from the global scope. By the time the timeouts run, `i` is 3.

### Solution 1: Use `let` (Block Scope)

```javascript
// ✅ CORRECT — Each iteration gets its own i
for (let i = 0; i < 3; i++) {
  setTimeout(function() {
    console.log(i); // 0, 1, 2
  }, 100);
}
```

### Solution 2: IIFE (Immediately Invoked Function Expression)

```javascript
// ✅ CORRECT — Old-school fix for var
for (var i = 0; i < 3; i++) {
  (function(capturedI) {
    setTimeout(function() {
      console.log(capturedI); // 0, 1, 2
    }, 100);
  })(i);
}
```

### Solution 3: Separate Function

```javascript
function createTimeout(value) {
  setTimeout(function() {
    console.log(value);
  }, 100);
}

for (var i = 0; i < 3; i++) {
  createTimeout(i); // 0, 1, 2
}
```

---

## Closure Scope Chain

Closures have access to variables from multiple levels of nested scopes:

```javascript
function grandparent() {
  const grandparentVar = "Grandpa";

  function parent() {
    const parentVar = "Dad";

    function child() {
      const childVar = "Son";
      console.log(grandparentVar); // "Grandpa" — from grandparent scope
      console.log(parentVar);      // "Dad" — from parent scope
      console.log(childVar);       // "Son" — from own scope
    }

    return child;
  }

  return parent;
}

const parentFn = grandparent();
const childFn = parentFn();
childFn();
```

**Scope Chain in child():**
```
child() scope → parent() scope → grandparent() scope → global scope
```

---

## Closures and Memory

Closures keep variables alive in memory as long as the closure function exists:

```javascript
function createHeavyData() {
  const hugeArray = new Array(1000000).fill("data");

  return {
    getLength: function() {
      return hugeArray.length;
    }
  };
}

const data = createHeavyData();
// hugeArray remains in memory because data.getLength is a closure

// To free memory, remove the reference:
data = null; // Now hugeArray can be garbage collected
```

> Be mindful of closures holding references to large objects unnecessarily.

---

## Practical Patterns

### Once Function

```javascript
function once(fn) {
  let called = false;
  let result;

  return function(...args) {
    if (!called) {
      called = true;
      result = fn.apply(this, args);
    }
    return result;
  };
}

const initialize = once(() => {
  console.log("Initializing...");
  return { status: "ready" };
});

initialize(); // "Initializing..."
initialize(); // Silent, returns cached result
```

### Rate Limiter

```javascript
function rateLimiter(fn, limitMs) {
  let lastCall = 0;

  return function(...args) {
    const now = Date.now();
    if (now - lastCall >= limitMs) {
      lastCall = now;
      return fn.apply(this, args);
    }
    console.log("Rate limited!");
  };
}

const limitedLog = rateLimiter((msg) => console.log(msg), 1000);

limitedLog("First");   // "First"
limitedLog("Second");  // "Rate limited!"
```

### Counter with Step

```javascript
function makeCounter(start = 0, step = 1) {
  let count = start;

  return {
    next: function() {
      const current = count;
      count += step;
      return current;
    },
    current: function() {
      return count;
    },
    reset: function() {
      count = start;
    }
  };
}

const counter = makeCounter(10, 5);
console.log(counter.next());   // 10
console.log(counter.next());   // 15
console.log(counter.current()); // 20
```

---

## Common Pitfalls

### Pitfall 1: Accidental Shared State

```javascript
function createButtons(n) {
  const buttons = [];

  for (var i = 0; i < n; i++) {
    buttons.push({
      click: function() {
        console.log(`Button ${i} clicked`);
      }
    });
  }

  return buttons;
}

const btns = createButtons(3);
btns[0].click(); // "Button 3 clicked" — all share same i!
btns[1].click(); // "Button 3 clicked"
```

**Fix:** Use `let` or IIFE to capture the current value.

### Pitfall 2: Modifying Closure Variables Unexpectedly

```javascript
function createCounter() {
  let count = 0;

  return {
    increment: () => ++count,
    decrement: () => --count,
    // Accidentally exposing direct access
    get count() { return count; },
    set count(val) { count = val; } // Dangerous!
  };
}
```

### Pitfall 3: Memory Leaks in Closures

```javascript
function attachListeners() {
  const largeData = fetchLargeData();

  document.getElementById("btn").addEventListener("click", function() {
    // This closure holds reference to largeData forever
    console.log(largeData[0]);
  });
}
```

**Fix:** Remove event listeners or avoid capturing large data in closures.

---

## Practice Exercises

### Exercise 1: Private Counter with History

Create a counter that tracks all previous values.

```javascript
const counter = createHistoryCounter();
counter.increment(); // 1
counter.increment(); // 2
counter.increment(); // 3
counter.decrement(); // 2
console.log(counter.getHistory()); // [1, 2, 3, 2]
```

### Exercise 2: Partial Application

Use closures to create a partial application function.

```javascript
function partial(fn, ...fixedArgs) {
  // Your code
}

const add5 = partial((a, b) => a + b, 5);
console.log(add5(10)); // 15
```

### Exercise 3: Retry with Delay

Create a function that retries another function with exponential backoff.

```javascript
function retryWithDelay(fn, maxRetries, delayMs) {
  // Return a function that retries fn up to maxRetries times,
  // doubling the delay each time
}
```

### Exercise 4: Cache with Expiry

Create a memoization function where cached values expire after a certain time.

```javascript
function memoizeWithExpiry(fn, ttlMs) {
  // Cache results, but recompute after ttlMs milliseconds
}
```

### Exercise 5: Event Emitter (Pub-Sub)

Implement a simple event emitter using closures.

```javascript
const emitter = createEmitter();
emitter.on("greet", name => console.log(`Hello, ${name}`));
emitter.emit("greet", "Alice"); // "Hello, Alice"
```

---

## Summary

- A **closure** is a function that retains access to its outer scope's variables
- Closures are created every time a function is defined inside another function
- Closures enable **data privacy**, **state maintenance**, and **function factories**
- The **loop closure gotcha** happens because `var` is function-scoped; use `let` instead
- Closures form a **scope chain** accessing variables from all enclosing scopes
- Be mindful of **memory implications** — closures keep referenced data alive
- The **module pattern** uses closures to create encapsulated APIs

---

## Next Steps

Closures are fundamental to:
- **Callbacks** — passing functions that retain their scope
- **Promises** — `.then()` callbacks form closures
- **Async/Await** — awaited values are captured in closures
- **Functional Programming** — pure functions and higher-order patterns

Happy coding! 🚀
