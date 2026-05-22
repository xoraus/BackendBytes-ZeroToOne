# Async/Await in JavaScript

## Overview

**Async/Await** is syntactic sugar built on top of Promises that makes asynchronous code look and behave more like synchronous code. Introduced in ES2017 (ES8), it has become the standard way to write asynchronous JavaScript. If you understand Promises, async/await is easy to learn — it simply provides a cleaner syntax for the same underlying mechanism.

---

## The `async` Keyword

Placing `async` before a function declaration makes it automatically return a Promise.

```javascript
async function greet() {
  return "Hello!";
}

// Equivalent to:
function greet() {
  return Promise.resolve("Hello!");
}

greet().then(message => console.log(message)); // "Hello!"
```

### async with Arrow Functions

```javascript
const greet = async () => "Hello!";
const fetchUser = async (id) => ({ id, name: "Alice" });
```

### async with Object Methods

```javascript
const api = {
  async getUser(id) {
    return { id, name: "Alice" };
  }
};
```

---

## The `await` Keyword

`await` can only be used inside an `async` function. It pauses execution until the Promise resolves, then returns its value.

```javascript
async function getData() {
  const response = await fetch("https://api.example.com/data");
  const data = await response.json();
  return data;
}
```

**What happens:**
1. `fetch()` returns a Promise
2. `await` pauses `getData()` execution
3. When the Promise resolves, `await` returns the response
4. Execution continues to the next line
5. `response.json()` returns another Promise
6. `await` pauses again
7. When resolved, `data` contains the parsed JSON

> **Important**: While `await` pauses the function execution, it does NOT block the main thread. Other code can run during the wait.

---

## Async/Await vs Promises

### Promise Version

```javascript
function fetchUserData(userId) {
  return fetchUser(userId)
    .then(user => fetchOrders(user.id))
    .then(orders => fetchProducts(orders[0].id))
    .then(products => ({ user, orders, products }))
    .catch(error => {
      console.error("Error:", error);
      throw error;
    });
}
```

### Async/Await Version

```javascript
async function fetchUserData(userId) {
  try {
    const user = await fetchUser(userId);
    const orders = await fetchOrders(user.id);
    const products = await fetchProducts(orders[0].id);
    return { user, orders, products };
  } catch (error) {
    console.error("Error:", error);
    throw error;
  }
}
```

**Benefits of async/await:**
- Reads like synchronous code
- Easier to debug (stack traces are clearer)
- Error handling with `try/catch` feels natural
- No nesting or chaining needed

---

## Error Handling with try/catch

### Basic Error Handling

```javascript
async function getUser(id) {
  try {
    const response = await fetch(`/api/users/${id}`);

    if (!response.ok) {
      throw new Error(`HTTP ${response.status}`);
    }

    const user = await response.json();
    return user;
  } catch (error) {
    console.error("Failed to fetch user:", error.message);
    return null;
  }
}
```

### Re-throwing Errors

```javascript
async function processPayment(orderId) {
  try {
    const order = await fetchOrder(orderId);
    const payment = await chargeCustomer(order.total);
    return payment;
  } catch (error) {
    // Log and re-throw for caller to handle
    console.error("Payment failed:", error);
    throw new PaymentError("Could not process payment", { cause: error });
  }
}
```

### Multiple try/catch Blocks

```javascript
async function complexOperation() {
  let user;
  try {
    user = await fetchUser(1);
  } catch (error) {
    console.error("User fetch failed, using default");
    user = { id: 0, name: "Guest" };
  }

  let orders;
  try {
    orders = await fetchOrders(user.id);
  } catch (error) {
    console.error("Orders fetch failed");
    orders = [];
  }

  return { user, orders };
}
```

---

## Sequential vs Parallel Execution

### Sequential (One After Another)

```javascript
async function sequentialFetch() {
  const user = await fetchUser(1);      // Wait 1s
  const orders = await fetchOrders(1);   // Wait 1s
  const products = await fetchProducts(1); // Wait 1s
  // Total: ~3 seconds
  return { user, orders, products };
}
```

### Parallel (All at Once)

```javascript
async function parallelFetch() {
  const [user, orders, products] = await Promise.all([
    fetchUser(1),
    fetchOrders(1),
    fetchProducts(1)
  ]);
  // Total: ~1 second
  return { user, orders, products };
}
```

### Conditional Sequential

```javascript
async function smartFetch(userId) {
  const user = await fetchUser(userId);

  // Only fetch orders if user is active
  let orders = [];
  if (user.isActive) {
    orders = await fetchOrders(userId);
  }

  return { user, orders };
}
```

---

## Looping with Async/Await

### for...of (Sequential)

```javascript
async function processUsers(userIds) {
  const results = [];

  for (const id of userIds) {
    const user = await fetchUser(id);
    results.push(user);
  }

  return results;
}

// Each fetch waits for the previous to complete
```

### Promise.all with map (Parallel)

```javascript
async function processUsersParallel(userIds) {
  const users = await Promise.all(
    userIds.map(id => fetchUser(id))
  );
  return users;
}

// All fetches run simultaneously
```

### forEach with Async (Gotcha!)

```javascript
// ❌ WRONG — forEach doesn't wait for async callbacks
async function badProcess(users) {
  users.forEach(async (user) => {
    await saveUser(user); // Fire and forget!
  });
  console.log("Done!"); // This prints before saves complete
}

// ✅ CORRECT — Use for...of
async function goodProcess(users) {
  for (const user of users) {
    await saveUser(user);
  }
  console.log("Done!"); // This prints after all saves
}

// ✅ CORRECT — Use Promise.all with map
async function alsoGoodProcess(users) {
  await Promise.all(users.map(user => saveUser(user)));
  console.log("Done!");
}
```

---

## Top-Level Await

Modern JavaScript supports `await` at the top level of modules (ES modules):

```javascript
// config.mjs
const config = await fetch("/api/config").then(r => r.json());
export default config;
```

```javascript
// main.mjs
import config from "./config.mjs";
console.log(config); // Already resolved!
```

> In Node.js, use `.mjs` extension or `"type": "module"` in package.json.

---

## Async/Await Patterns

### Retry Pattern

```javascript
async function fetchWithRetry(url, maxRetries = 3) {
  for (let attempt = 1; attempt <= maxRetries; attempt++) {
    try {
      const response = await fetch(url);
      if (!response.ok) throw new Error(`HTTP ${response.status}`);
      return await response.json();
    } catch (error) {
      if (attempt === maxRetries) throw error;
      const delay = 1000 * Math.pow(2, attempt - 1);
      await new Promise(resolve => setTimeout(resolve, delay));
    }
  }
}
```

### Timeout Pattern

```javascript
async function fetchWithTimeout(url, timeoutMs = 5000) {
  const controller = new AbortController();
  const timeout = setTimeout(() => controller.abort(), timeoutMs);

  try {
    const response = await fetch(url, { signal: controller.signal });
    return await response.json();
  } finally {
    clearTimeout(timeout);
  }
}
```

### Polling Pattern

```javascript
async function pollForResult(jobId, intervalMs = 1000, maxAttempts = 30) {
  for (let attempt = 0; attempt < maxAttempts; attempt++) {
    const result = await checkJobStatus(jobId);

    if (result.status === "completed") {
      return result.data;
    }

    if (result.status === "failed") {
      throw new Error("Job failed");
    }

    await new Promise(resolve => setTimeout(resolve, intervalMs));
  }

  throw new Error("Polling timeout");
}
```

### Sequential with Error Handling

```javascript
async function processTasks(tasks) {
  const results = [];
  const errors = [];

  for (const task of tasks) {
    try {
      const result = await task();
      results.push(result);
    } catch (error) {
      errors.push({ task, error });
    }
  }

  return { results, errors };
}
```

---

## Converting Callbacks to Async/Await

```javascript
// Callback version
function getUserCallback(id, callback) {
  setTimeout(() => {
    callback(null, { id, name: "Alice" });
  }, 100);
}

// Promise wrapper
function getUserPromise(id) {
  return new Promise((resolve, reject) => {
    getUserCallback(id, (error, user) => {
      if (error) reject(error);
      else resolve(user);
    });
  });
}

// Async/await usage
async function main() {
  const user = await getUserPromise(1);
  console.log(user);
}
```

---

## Common Mistakes

### Mistake 1: Forgetting await

```javascript
// ❌ Missing await
async function getData() {
  const response = fetch("/api/data"); // Returns a Promise!
  const data = response.json(); // Error! Can't call .json() on a Promise
}

// ✅ Correct
async function getData() {
  const response = await fetch("/api/data");
  const data = await response.json();
}
```

### Mistake 2: Mixing await and .then()

```javascript
// ❌ Unnecessary mixing
async function getUser() {
  return await fetchUser(1).then(user => {
    return user.name;
  });
}

// ✅ Cleaner
async function getUser() {
  const user = await fetchUser(1);
  return user.name;
}
```

### Mistake 3: Catching but Not Returning

```javascript
// ❌ Returns undefined on error
async function getUserSafe(id) {
  try {
    return await fetchUser(id);
  } catch (error) {
    console.error(error);
    // Forgot to return anything!
  }
}

// ✅ Return a fallback
async function getUserSafe(id) {
  try {
    return await fetchUser(id);
  } catch (error) {
    console.error(error);
    return { id: 0, name: "Guest" };
  }
}
```

### Mistake 4: Unnecessary async

```javascript
// ❌ Pointless async
async function getNumber() {
  return 42;
}

// ✅ Just return the value
function getNumber() {
  return 42;
}

// ✅ Only use async when there's await inside
async function getUserData(id) {
  const user = await fetchUser(id);
  return user;
}
```

### Mistake 5: Floating Promises in Non-Async Context

```javascript
// ❌ In a regular function, this promise is floating
function process() {
  saveData(); // Promise is created but not handled!
}

// ✅ Handle the promise
function process() {
  saveData().catch(error => console.error(error));
}

// ✅ Or make the function async
async function process() {
  await saveData();
}
```

---

## Practice Exercises

### Exercise 1: Refactor to Async/Await

Convert this Promise chain to async/await:

```javascript
function loadDashboard(userId) {
  return fetchUser(userId)
    .then(user => fetchSettings(user.id).then(settings => ({ user, settings })))
    .then(({ user, settings }) => fetchWidgets(settings.theme).then(widgets => ({ user, settings, widgets })))
    .then(dashboard => {
      console.log("Dashboard loaded");
      return dashboard;
    })
    .catch(error => {
      console.error("Failed to load dashboard:", error);
      return null;
    });
}
```

### Exercise 2: Implement a Delay Function

```javascript
async function delay(ms) {
  // Your code
}

async function main() {
  console.log("Start");
  await delay(1000);
  console.log("After 1 second");
}
```

### Exercise 3: Sequential API Calls

Fetch posts and then fetch comments for each post sequentially.

```javascript
async function loadPostsWithComments() {
  const posts = await fetchPosts();
  // For each post, fetch its comments
  // Return array of posts with comments included
}
```

### Exercise 4: Parallel with Concurrency Limit

Fetch URLs in parallel but limit to 3 concurrent requests.

```javascript
async function fetchWithLimit(urls, concurrency = 3) {
  // Your code
}
```

### Exercise 5: Async Retry with Jitter

Implement retry with random delays to avoid thundering herd.

```javascript
async function fetchWithJitterRetry(url, maxRetries = 3) {
  // Add random delay between retries
}
```

---

## Summary

- `async` makes a function return a Promise automatically
- `await` pauses execution until a Promise resolves
- Async/await code **reads like synchronous code** but doesn't block the main thread
- Use `try/catch` for error handling — it's more intuitive than `.catch()`
- Use `Promise.all()` for parallel execution inside async functions
- Use `for...of` for sequential loops, never `forEach` with async callbacks
- **Always await** Promise-returning functions
- Add `.catch()` to floating promises in non-async contexts
- Top-level `await` is available in ES modules

---

## Next Steps

You're now ready for:
- **Converting Callbacks to Promises/Async** — modernizing legacy code
- **Testing Async Code** — Jest async test patterns
- **Error Handling Patterns** — production-ready strategies

Happy coding! 🚀
