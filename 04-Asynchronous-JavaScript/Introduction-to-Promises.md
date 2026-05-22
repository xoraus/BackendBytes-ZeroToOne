# Introduction to Promises

## Overview

**Promises** are objects that represent the eventual completion (or failure) of an asynchronous operation and its resulting value. They provide a cleaner, more manageable way to handle async operations compared to callbacks. Promises were introduced in ES6 and have become the standard for async programming in JavaScript.

---

## The Problem with Callbacks

Before Promises, asynchronous code relied heavily on callbacks, leading to **Callback Hell**:

```javascript
getData(function(a) {
  getMoreData(a, function(b) {
    getMoreData(b, function(c) {
      getMoreData(c, function(d) {
        console.log(d);
      });
    });
  });
});
```

Promises solve this by:
- Flattening nested code
- Providing better error handling
- Enabling composition and chaining

---

## What is a Promise?

A Promise is an object representing a value that may not exist yet but will be resolved at some point in the future.

### Promise States

A Promise is always in one of three states:

| State | Description |
|-------|-------------|
| **Pending** | Initial state, neither fulfilled nor rejected |
| **Fulfilled** | The operation completed successfully |
| **Rejected** | The operation failed |

```
Pending → Fulfilled (with a value)
   ↓
Pending → Rejected (with a reason/error)
```

> **Important**: Once a Promise is fulfilled or rejected, its state cannot change. This is called **immutability**.

---

## Creating a Promise

### The Promise Constructor

```javascript
const promise = new Promise((resolve, reject) => {
  // Async operation
  if (success) {
    resolve(value);   // Fulfill the promise
  } else {
    reject(error);    // Reject the promise
  }
});
```

The constructor takes a single function (the **executor**) with two parameters:
- `resolve(value)` — transitions the promise to fulfilled state
- `reject(reason)` — transitions the promise to rejected state

### Simple Example

```javascript
const myPromise = new Promise((resolve, reject) => {
  setTimeout(() => {
    const success = true;

    if (success) {
      resolve("Operation completed!");
    } else {
      reject(new Error("Something went wrong"));
    }
  }, 1000);
});
```

### Wrapping a Callback

```javascript
function readFileAsync(filePath) {
  return new Promise((resolve, reject) => {
    fs.readFile(filePath, "utf8", (error, data) => {
      if (error) {
        reject(error);
      } else {
        resolve(data);
      }
    });
  });
}
```

---

## Consuming a Promise

### `.then()` — Handle Success

```javascript
myPromise.then((value) => {
  console.log(value); // "Operation completed!"
});
```

### `.catch()` — Handle Errors

```javascript
myPromise
  .then((value) => {
    console.log(value);
  })
  .catch((error) => {
    console.error(error.message);
  });
```

### `.finally()` — Cleanup (Always Runs)

```javascript
myPromise
  .then((value) => {
    console.log("Success:", value);
  })
  .catch((error) => {
    console.error("Error:", error);
  })
  .finally(() => {
    console.log("Cleanup: This always runs");
  });
```

### Complete Example

```javascript
function fetchUserData(userId) {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      const users = {
        1: { name: "Alice", age: 25 },
        2: { name: "Bob", age: 30 }
      };

      const user = users[userId];

      if (user) {
        resolve(user);
      } else {
        reject(new Error(`User ${userId} not found`));
      }
    }, 1000);
  });
}

fetchUserData(1)
  .then(user => {
    console.log("Found user:", user);
  })
  .catch(error => {
    console.error("Failed:", error.message);
  })
  .finally(() => {
    console.log("Fetch attempt complete");
  });
```

---

## Promise.resolve() and Promise.reject()

### Quick Fulfillment

```javascript
const fulfilled = Promise.resolve(42);

fulfilled.then(value => {
  console.log(value); // 42
});
```

### Quick Rejection

```javascript
const rejected = Promise.reject(new Error("Oops!"));

rejected.catch(error => {
  console.error(error.message); // "Oops!"
});
```

### Wrapping Values

```javascript
function getValue(maybePromise) {
  return Promise.resolve(maybePromise);
}

getValue(42).then(v => console.log(v));           // 42
getValue(Promise.resolve(42)).then(v => console.log(v)); // 42
```

---

## Promises vs Callbacks: Side by Side

### Callback Version

```javascript
function getUser(callback) {
  setTimeout(() => callback(null, { id: 1, name: "Alice" }), 1000);
}

function getOrders(userId, callback) {
  setTimeout(() => callback(null, [{ id: 101 }]), 1000);
}

getUser((error, user) => {
  if (error) {
    console.error(error);
    return;
  }
  getOrders(user.id, (error, orders) => {
    if (error) {
      console.error(error);
      return;
    }
    console.log(orders);
  });
});
```

### Promise Version

```javascript
function getUser() {
  return new Promise(resolve => {
    setTimeout(() => resolve({ id: 1, name: "Alice" }), 1000);
  });
}

function getOrders(userId) {
  return new Promise(resolve => {
    setTimeout(() => resolve([{ id: 101 }]), 1000);
  });
}

getUser()
  .then(user => getOrders(user.id))
  .then(orders => console.log(orders))
  .catch(error => console.error(error));
```

---

## The Promise Microtask Queue

Promise callbacks (`.then`, `.catch`, `.finally`) are placed in the **Microtask Queue**, which has higher priority than the regular Callback Queue.

```javascript
console.log("1");

setTimeout(() => console.log("2"), 0);

Promise.resolve().then(() => console.log("3"));

console.log("4");

// Output:
// 1
// 4
// 3
// 2
```

**Why?**
1. `console.log("1")` and `console.log("4")` execute synchronously
2. `setTimeout` goes to the Callback Queue (macrotask)
3. Promise `.then` goes to the Microtask Queue
4. Microtasks run before macrotasks, so "3" prints before "2"

---

## Common Promise Patterns

### Delay Function

```javascript
function delay(ms) {
  return new Promise(resolve => setTimeout(resolve, ms));
}

delay(1000).then(() => {
  console.log("1 second passed");
});
```

### Timeout Wrapper

```javascript
function withTimeout(promise, ms) {
  const timeout = new Promise((_, reject) => {
    setTimeout(() => reject(new Error("Timeout!")), ms);
  });

  return Promise.race([promise, timeout]);
}
```

### Retry Logic

```javascript
function retry(fn, maxAttempts) {
  return new Promise((resolve, reject) => {
    const attempt = (n) => {
      fn()
        .then(resolve)
        .catch(error => {
          if (n >= maxAttempts) {
            reject(error);
          } else {
            attempt(n + 1);
          }
        });
    };

    attempt(1);
  });
}
```

---

## Common Mistakes

### Mistake 1: Forgetting to Return a Promise

```javascript
// ❌ Missing return — breaks the chain!
getUser()
  .then(user => {
    getOrders(user.id); // Not returned!
  })
  .then(orders => {
    console.log(orders); // undefined!
  });

// ✅ Return the promise
getUser()
  .then(user => {
    return getOrders(user.id);
  })
  .then(orders => {
    console.log(orders); // Works!
  });

// ✅ Or use implicit return with arrow function
getUser()
  .then(user => getOrders(user.id))
  .then(orders => console.log(orders));
```

### Mistake 2: Not Catching Errors

```javascript
// ❌ Unhandled promise rejection!
fetch("/api/data")
  .then(response => response.json())
  .then(data => console.log(data));
// If fetch fails, the error is silently swallowed!

// ✅ Always add catch
fetch("/api/data")
  .then(response => response.json())
  .then(data => console.log(data))
  .catch(error => console.error("Fetch failed:", error));
```

### Mistake 3: Throwing in `.then()` Without Catch

```javascript
// ❌ Error in then() without catch
Promise.resolve(5)
  .then(value => {
    throw new Error("Oops!");
  });
// Unhandled promise rejection!

// ✅ Catch errors anywhere in the chain
Promise.resolve(5)
  .then(value => {
    throw new Error("Oops!");
  })
  .catch(error => console.error(error.message));
```

### Mistake 4: Nested Promises (Promise Hell)

```javascript
// ❌ Still nested!
getUser()
  .then(user => {
    getOrders(user.id)
      .then(orders => {
        console.log(orders);
      });
  });

// ✅ Flat chain
getUser()
  .then(user => getOrders(user.id))
  .then(orders => console.log(orders));
```

---

## Practice Exercises

### Exercise 1: Create a Delayed Promise

Write a function that returns a Promise which resolves after a given delay with a given value.

```javascript
function delayedResolve(value, delayMs) {
  // Your code
}

delayedResolve("Hello!", 1000).then(console.log); // "Hello!" after 1s
```

### Exercise 2: Promise-based File Reader

Convert Node.js `fs.readFile` to return a Promise.

```javascript
function readFilePromise(path) {
  // Your code
}

readFilePromise("file.txt")
  .then(data => console.log(data))
  .catch(error => console.error(error));
```

### Exercise 3: Fake API Call

Create a fake API function that randomly succeeds or fails.

```javascript
function fakeApiCall(endpoint) {
  // 70% chance of success, 30% chance of failure
  // Return a Promise
}

fakeApiCall("/users")
  .then(data => console.log("Success:", data))
  .catch(error => console.error("Failed:", error));
```

### Exercise 4: Sequential Delay

Use Promises to log numbers 1 to 5, waiting 1 second between each.

```javascript
function logWithDelay(message, delayMs) {
  // Your code
}

// Output: 1 (wait 1s) 2 (wait 1s) 3 ...
```

### Exercise 5: Promise State Inspector

Write a function that logs the current state of a Promise.

```javascript
function inspectPromise(promise) {
  // Log whether the promise is pending, fulfilled, or rejected
}
```

---

## Summary

- A **Promise** represents a value that may not exist yet but will be resolved later
- Promises have three states: **Pending**, **Fulfilled**, **Rejected**
- Use `new Promise((resolve, reject) => { ... })` to create promises
- Use `.then()` for success, `.catch()` for errors, `.finally()` for cleanup
- Promise callbacks run as **microtasks** (higher priority than `setTimeout`)
- Always **return** promises in `.then()` to maintain the chain
- Always add **`.catch()`** to handle potential errors
- `Promise.resolve()` and `Promise.reject()` create immediately settled promises

---

## Next Steps

Master Promises further with:
- **Promise Chaining** — composing multiple async operations
- **Promise.all / race** — running promises in parallel
- **Async/Await** — synchronous-looking async code
- **Converting Callbacks to Promises** — modernizing legacy code

Happy coding! 🚀
