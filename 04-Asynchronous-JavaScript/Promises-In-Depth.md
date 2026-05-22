# Promises In Depth

## Overview

Now that you understand the basics of Promises and chaining, it's time to explore advanced patterns, edge cases, and production-ready techniques. This tutorial covers everything from anti-patterns to sophisticated async flows that you'll encounter in real-world applications.

---

## The Promise Lifecycle (Revisited)

```
Pending → Fulfilled (value)
   ↓
Pending → Rejected (reason)
```

Once settled, a Promise's state is **immutable**. You cannot change a fulfilled promise to rejected or vice versa.

```javascript
const promise = new Promise((resolve, reject) => {
  resolve("Success!");
  reject(new Error("Too late!")); // Ignored — promise already fulfilled
});

promise.then(value => console.log(value)); // "Success!"
```

---

## Executor Function Execution

The executor function passed to `new Promise()` runs **synchronously** and **immediately**:

```javascript
console.log("Before promise");

new Promise((resolve) => {
  console.log("Inside executor"); // Runs immediately!
  resolve("done");
});

console.log("After promise");

// Output:
// Before promise
// Inside executor
// After promise
```

> The `.then()` callback, however, runs **asynchronously** as a microtask.

---

## Advanced Error Handling

### Errors in Executors

```javascript
new Promise((resolve, reject) => {
  throw new Error("Executor error");
}).catch(error => {
  console.error(error.message); // "Executor error"
});
```

### Errors in .then() Handlers

```javascript
Promise.resolve("ok")
  .then(value => {
    throw new Error("Then error");
  })
  .catch(error => {
    console.error(error.message); // "Then error"
  });
```

### Catching Specific Errors

```javascript
class NetworkError extends Error {}
class ValidationError extends Error {}

fetchData()
  .catch(error => {
    if (error instanceof NetworkError) {
      console.error("Network issue:", error.message);
      return retryFetch();
    }
    if (error instanceof ValidationError) {
      console.error("Invalid data:", error.message);
      return { data: [] };
    }
    throw error; // Re-throw unexpected errors
  });
```

---

## Promise Anti-Patterns

### Anti-Pattern 1: The Promise Constructor Anti-Pattern

```javascript
// ❌ Wrapping a Promise in another Promise
function badFetch(url) {
  return new Promise((resolve, reject) => {
    fetch(url)
      .then(response => resolve(response))
      .catch(error => reject(error));
  });
}

// ✅ Just return the Promise directly
function goodFetch(url) {
  return fetch(url);
}

// ❌ Same with async functions
async function badFetch2(url) {
  return new Promise((resolve, reject) => {
    fetch(url).then(resolve).catch(reject);
  });
}

// ✅ async already returns a Promise
async function goodFetch2(url) {
  return fetch(url);
}
```

### Anti-Pattern 2: Forgetting to Return

```javascript
// ❌ Missing return breaks the chain
function processUser(userId) {
  return fetchUser(userId)
    .then(user => {
      fetchOrders(user.id); // Not returned!
    })
    .then(orders => {
      console.log(orders); // undefined
    });
}

// ✅ Return every Promise
function processUser(userId) {
  return fetchUser(userId)
    .then(user => fetchOrders(user.id))
    .then(orders => {
      console.log(orders);
      return orders;
    });
}
```

### Anti-Pattern 3: Mixing Callbacks and Promises

```javascript
// ❌ Mixing paradigms
function mixedApproach(userId, callback) {
  fetchUser(userId)
    .then(user => {
      callback(null, user); // Still using callbacks!
    })
    .catch(error => {
      callback(error);
    });
}

// ✅ Return a Promise
function purePromise(userId) {
  return fetchUser(userId);
}
```

### Anti-Pattern 4: Nested Promises (Promise Hell)

```javascript
// ❌ Still nested!
fetchUser(1)
  .then(user => {
    return fetchOrders(user.id).then(orders => {
      return fetchProducts(orders[0].id).then(products => {
        console.log(products);
      });
    });
  });

// ✅ Flat chain
fetchUser(1)
  .then(user => fetchOrders(user.id))
  .then(orders => fetchProducts(orders[0].id))
  .then(products => console.log(products));
```

---

## Advanced Patterns

### Sequential Execution with Reduce

```javascript
const tasks = [
  () => fetchUser(1),
  () => fetchOrders(1),
  () => fetchProducts(1)
];

const results = await tasks.reduce(
  (promise, task) => promise.then(results =>
    task().then(result => [...results, result])
  ),
  Promise.resolve([])
);
```

### Map with Concurrency Limit

```javascript
async function mapWithConcurrency(items, mapper, concurrency) {
  const results = [];

  for (let i = 0; i < items.length; i += concurrency) {
    const batch = items.slice(i, i + concurrency);
    const batchResults = await Promise.all(batch.map(mapper));
    results.push(...batchResults);
  }

  return results;
}

// Fetch max 5 URLs at a time
const pages = await mapWithConcurrency(urls, fetchPage, 5);
```

### Timeout Pattern

```javascript
function withTimeout(promise, ms) {
  const timeout = new Promise((_, reject) => {
    setTimeout(() => reject(new Error(`Timeout after ${ms}ms`)), ms);
  });

  return Promise.race([promise, timeout]);
}

// Usage
const data = await withTimeout(fetchData(), 5000);
```

### Retry with Exponential Backoff

```javascript
async function retry(fn, maxRetries = 3, delay = 1000) {
  for (let attempt = 1; attempt <= maxRetries; attempt++) {
    try {
      return await fn();
    } catch (error) {
      if (attempt === maxRetries) throw error;

      const waitTime = delay * Math.pow(2, attempt - 1);
      console.log(`Attempt ${attempt} failed. Retrying in ${waitTime}ms...`);
      await new Promise(resolve => setTimeout(resolve, waitTime));
    }
  }
}

// Usage
const data = await retry(() => fetchData(), 5, 1000);
```

### Circuit Breaker Pattern

```javascript
function createCircuitBreaker(fn, options = {}) {
  const { failureThreshold = 5, resetTimeout = 60000 } = options;
  let failures = 0;
  let nextAttempt = Date.now();
  let state = "CLOSED"; // CLOSED, OPEN, HALF_OPEN

  return async function(...args) {
    if (state === "OPEN") {
      if (Date.now() < nextAttempt) {
        throw new Error("Circuit breaker is OPEN");
      }
      state = "HALF_OPEN";
    }

    try {
      const result = await fn(...args);
      failures = 0;
      state = "CLOSED";
      return result;
    } catch (error) {
      failures++;
      if (failures >= failureThreshold) {
        state = "OPEN";
        nextAttempt = Date.now() + resetTimeout;
      }
      throw error;
    }
  };
}
```

---

## Working with Multiple Promises

### Promise.allSettled with Filtering

```javascript
const results = await Promise.allSettled([
  fetchUser(1),
  fetchUser(2),
  fetchUser(999) // This might fail
]);

const successful = results
  .filter(r => r.status === "fulfilled")
  .map(r => r.value);

const failed = results
  .filter(r => r.status === "rejected")
  .map(r => r.reason);
```

### Promise.all with Error Handling

```javascript
// ❌ Promise.all fails fast (one failure fails all)
const [users, orders] = await Promise.all([
  fetchUsers(),
  fetchOrders()
]);

// ✅ Handle individual errors
const [usersResult, ordersResult] = await Promise.all([
  fetchUsers().catch(e => ({ error: e })),
  fetchOrders().catch(e => ({ error: e }))
]);
```

---

## Memoization with Promises

Cache async results to avoid redundant calls:

```javascript
function memoizeAsync(fn) {
  const cache = new Map();

  return function(...args) {
    const key = JSON.stringify(args);

    if (cache.has(key)) {
      return cache.get(key);
    }

    const promise = fn.apply(this, args).catch(error => {
      cache.delete(key); // Remove failed promises from cache
      throw error;
    });

    cache.set(key, promise);
    return promise;
  };
}

const fetchUserMemoized = memoizeAsync(fetchUser);

// First call fetches from network
const user1 = await fetchUserMemoized(1);

// Second call returns cached promise
const user2 = await fetchUserMemoized(1);
```

---

## Deferred Promise Pattern

Sometimes you need to create a Promise and resolve it from outside:

```javascript
function createDeferred() {
  let resolve, reject;

  const promise = new Promise((res, rej) => {
    resolve = res;
    reject = rej;
  });

  return { promise, resolve, reject };
}

// Usage
const deferred = createDeferred();

setTimeout(() => {
  deferred.resolve("Done!");
}, 1000);

const result = await deferred.promise;
console.log(result); // "Done!"
```

---

## Async Iterator Pattern

Process a stream of async data:

```javascript
async function* fetchPages(url) {
  let page = 1;
  let hasMore = true;

  while (hasMore) {
    const response = await fetch(`${url}?page=${page}`);
    const data = await response.json();

    yield data.items;

    hasMore = data.hasMore;
    page++;
  }
}

// Usage
for await (const items of fetchPages("/api/items")) {
  console.log(`Loaded ${items.length} items`);
}
```

---

## Common Mistakes

### Mistake 1: Unhandled Promise Rejections

```javascript
// ❌ Unhandled rejection
const promise = fetchData();
// Forgot to add .catch()!

// ✅ Always handle rejections
fetchData().catch(error => console.error(error));

// ✅ Or use try/catch with async/await
try {
  const data = await fetchData();
} catch (error) {
  console.error(error);
}
```

### Mistake 2: Not Awaiting in Loops

```javascript
// ❌ All requests fire simultaneously, but errors are unhandled
urls.forEach(async (url) => {
  const data = await fetch(url); // Fire and forget!
});

// ✅ Sequential with for...of
for (const url of urls) {
  const data = await fetch(url);
}

// ✅ Parallel with Promise.all
const results = await Promise.all(urls.map(url => fetch(url)));
```

### Mistake 3: Creating Unnecessary Promises

```javascript
// ❌ Wrapping a sync value
async function getValue() {
  return Promise.resolve(42);
}

// ✅ Just return the value
async function getValue() {
  return 42; // async wraps it automatically
}

// ❌ await on non-promise
async function process() {
  const value = await 42; // Pointless await
}

// ✅ Only await promises
async function process() {
  const value = 42;
  const result = await fetchData();
}
```

### Mistake 4: Floating Promises

```javascript
// ❌ Floating promise — no error handling
async function processData() {
  saveToDatabase(data); // Not awaited, errors swallowed!
}

// ✅ Properly handle
async function processData() {
  await saveToDatabase(data);
}

// Or if intentionally fire-and-forget:
saveToDatabase(data).catch(error => {
  console.error("Background save failed:", error);
});
```

---

## Practice Exercises

### Exercise 1: Implement Promise.all from Scratch

```javascript
function myPromiseAll(promises) {
  // Your implementation
}

myPromiseAll([Promise.resolve(1), Promise.resolve(2)])
  .then(results => console.log(results)); // [1, 2]
```

### Exercise 2: Implement Promise.race from Scratch

```javascript
function myPromiseRace(promises) {
  // Your implementation
}
```

### Exercise 3: Timeout with Fallback

Write a function that tries a primary source, but falls back to a secondary source if the primary times out.

```javascript
function fetchWithFallback(primary, fallback, timeoutMs) {
  // Return primary if it completes within timeoutMs
  // Otherwise return fallback
}
```

### Exercise 4: Parallel with Error Tolerance

Write a function that runs tasks in parallel and returns results for successful ones, even if some fail.

```javascript
function parallelWithTolerance(tasks, maxFailures) {
  // Run all tasks in parallel
  // Return results if failures <= maxFailures
  // Otherwise throw aggregate error
}
```

### Exercise 5: Promise Queue

Implement a queue that limits concurrent promise execution.

```javascript
const queue = new PromiseQueue(3); // Max 3 concurrent

queue.add(() => fetchUser(1));
queue.add(() => fetchUser(2));
queue.add(() => fetchUser(3));
queue.add(() => fetchUser(4)); // Waits for a slot
```

---

## Summary

- Promise executors run **synchronously**; `.then()` callbacks run as **microtasks**
- A Promise's state is **immutable** once settled
- **Never wrap Promises in new Promises** — return them directly
- **Always return** Promises in `.then()` to maintain the chain
- Use `Promise.all` for parallel, `reduce` for sequential execution
- `Promise.allSettled` is safest when you need all results regardless of failures
- Implement **timeout**, **retry**, and **circuit breaker** patterns for robustness
- **Memoize async functions** to avoid redundant network calls
- Always handle rejections — unhandled promise rejections crash Node.js apps
- Avoid **floating promises** — always await or attach `.catch()`

---

## Next Steps

Master async JavaScript with:
- **Async/Await** — the modern, clean syntax for Promises
- **Error Handling** — production-ready error management
- **Testing Async Code** — Jest and mocking strategies

Happy coding! 🚀
