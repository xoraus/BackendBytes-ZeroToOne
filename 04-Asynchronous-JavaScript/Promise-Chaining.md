# Promise Chaining

## Overview

One of the most powerful features of Promises is the ability to **chain** them together. Promise chaining allows you to execute a sequence of asynchronous operations where each step waits for the previous one to complete. This creates flat, readable code that flows from top to bottom — a dramatic improvement over nested callbacks.

---

## The Basics of Chaining

A `.then()` handler can return a value, and that value is passed to the next `.then()` in the chain.

```javascript
Promise.resolve(5)
  .then(value => {
    console.log(value); // 5
    return value * 2;
  })
  .then(value => {
    console.log(value); // 10
    return value + 3;
  })
  .then(value => {
    console.log(value); // 13
  });
```

### What Happens in Each Step

1. `Promise.resolve(5)` creates a fulfilled promise with value `5`
2. First `.then()` receives `5`, logs it, and returns `10`
3. Second `.then()` receives `10`, logs it, and returns `13`
4. Third `.then()` receives `13`, logs it

---

## Returning Promises from .then()

The real power of chaining emerges when each step returns a new Promise. JavaScript automatically waits for the Promise to resolve before passing its value to the next `.then()`.

```javascript
function fetchUser(userId) {
  return new Promise(resolve => {
    setTimeout(() => resolve({ id: userId, name: "Alice" }), 500);
  });
}

function fetchOrders(userId) {
  return new Promise(resolve => {
    setTimeout(() => resolve([{ id: 101, total: 50 }]), 500);
  });
}

function fetchProducts(orderId) {
  return new Promise(resolve => {
    setTimeout(() => resolve([{ name: "Laptop", price: 50 }]), 500);
  });
}

// Beautiful flat chain!
fetchUser(1)
  .then(user => {
    console.log("User:", user);
    return fetchOrders(user.id);
  })
  .then(orders => {
    console.log("Orders:", orders);
    return fetchProducts(orders[0].id);
  })
  .then(products => {
    console.log("Products:", products);
  })
  .catch(error => {
    console.error("Error:", error);
  });
```

---

## Error Propagation

Errors in a Promise chain automatically propagate down to the nearest `.catch()`. You don't need error handling at every step.

```javascript
fetchUser(1)
  .then(user => fetchOrders(user.id))
  .then(orders => fetchProducts(orders[0].id))
  .then(products => console.log(products))
  .catch(error => {
    // Catches ANY error in the entire chain!
    console.error("Something failed:", error);
  });
```

### Where Errors Are Caught

```javascript
Promise.resolve()
  .then(() => {
    console.log("Step 1");
    throw new Error("Error in Step 1!");
  })
  .then(() => {
    console.log("Step 2"); // Skipped!
  })
  .then(() => {
    console.log("Step 3"); // Skipped!
  })
  .catch(error => {
    console.error("Caught:", error.message); // "Error in Step 1!"
  })
  .then(() => {
    console.log("Recovery step"); // Runs after catch!
  });
```

> **Important**: After a `.catch()`, the chain resumes. The `.catch()` itself can return a value that flows to the next `.then()`.

---

## Recovery from Errors

You can recover from errors in a `.catch()` and continue the chain:

```javascript
fetchUser(999) // User doesn't exist
  .catch(error => {
    console.warn("User not found, using default");
    return { id: 0, name: "Guest" }; // Recovery value
  })
  .then(user => {
    console.log("Proceeding with:", user); // { id: 0, name: "Guest" }
    return fetchOrders(user.id);
  })
  .then(orders => console.log(orders));
```

---

## Multiple .then() on the Same Promise

You can attach multiple `.then()` handlers to the same promise. They run independently and receive the same value.

```javascript
const promise = Promise.resolve("Hello");

promise.then(value => console.log("A:", value));
promise.then(value => console.log("B:", value));
promise.then(value => console.log("C:", value));

// Output:
// A: Hello
// B: Hello
// C: Hello
```

> This is different from chaining, where each `.then()` receives the return value of the previous one.

---

## Chaining vs Nesting

### ❌ Nested (Promise Hell)

```javascript
fetchUser(1)
  .then(user => {
    fetchOrders(user.id)
      .then(orders => {
        fetchProducts(orders[0].id)
          .then(products => {
            console.log(products);
          });
      });
  });
```

### ✅ Flat Chain

```javascript
fetchUser(1)
  .then(user => fetchOrders(user.id))
  .then(orders => fetchProducts(orders[0].id))
  .then(products => console.log(products))
  .catch(error => console.error(error));
```

---

## Promise.all — Parallel Execution

Execute multiple promises simultaneously and wait for all to complete.

```javascript
const userPromise = fetchUser(1);
const ordersPromise = fetchOrders(1);
const productsPromise = fetchProducts(1);

Promise.all([userPromise, ordersPromise, productsPromise])
  .then(([user, orders, products]) => {
    console.log("User:", user);
    console.log("Orders:", orders);
    console.log("Products:", products);
  })
  .catch(error => {
    // Fails if ANY promise rejects
    console.error("One failed:", error);
  });
```

### Practical Example

```javascript
const urls = [
  "https://api.example.com/users",
  "https://api.example.com/posts",
  "https://api.example.com/comments"
];

Promise.all(urls.map(url => fetch(url)))
  .then(responses => Promise.all(responses.map(res => res.json())))
  .then(([users, posts, comments]) => {
    console.log(`Loaded ${users.length} users`);
    console.log(`Loaded ${posts.length} posts`);
    console.log(`Loaded ${comments.length} comments`);
  });
```

---

## Promise.race — First to Settle

Returns the first promise that settles (fulfills or rejects).

```javascript
const fast = new Promise(resolve => setTimeout(() => resolve("Fast!"), 100));
const slow = new Promise(resolve => setTimeout(() => resolve("Slow!"), 500));

Promise.race([fast, slow])
  .then(winner => console.log(winner)); // "Fast!"
```

### Timeout Pattern

```javascript
function fetchWithTimeout(url, timeoutMs) {
  return Promise.race([
    fetch(url),
    new Promise((_, reject) =>
      setTimeout(() => reject(new Error("Request timeout")), timeoutMs)
    )
  ]);
}
```

---

## Promise.allSettled — Wait for All (Never Rejects)

Waits for all promises to settle, regardless of whether they fulfill or reject.

```javascript
const promises = [
  Promise.resolve("success"),
  Promise.reject("error"),
  Promise.resolve("another success")
];

Promise.allSettled(promises)
  .then(results => {
    results.forEach(result => {
      if (result.status === "fulfilled") {
        console.log("Success:", result.value);
      } else {
        console.log("Failed:", result.reason);
      }
    });
  });

// Output:
// Success: success
// Failed: error
// Success: another success
```

---

## Promise.any — First Success

Returns the first fulfilled promise. Only rejects if ALL promises reject.

```javascript
const promises = [
  Promise.reject("Error 1"),
  Promise.resolve("First success!"),
  Promise.resolve("Second success!")
];

Promise.any(promises)
  .then(firstSuccess => console.log(firstSuccess)) // "First success!"
  .catch(error => console.error("All failed:", error));
```

---

## Sequential vs Parallel Execution

### Sequential (One after another)

```javascript
// Total time: 1s + 1s + 1s = 3 seconds
fetchUser(1)
  .then(user => fetchOrders(user.id))
  .then(orders => fetchProducts(orders[0].id))
  .then(products => console.log(products));
```

### Parallel (All at once)

```javascript
// Total time: ~1 second (all run simultaneously)
Promise.all([fetchUser(1), fetchOrders(1), fetchProducts(1)])
  .then(([user, orders, products]) => {
    console.log(user, orders, products);
  });
```

### Sequential Array Processing

```javascript
const userIds = [1, 2, 3, 4, 5];

// Sequential: one user at a time
userIds.reduce((promise, id) => {
  return promise.then(() => fetchUser(id).then(user => console.log(user)));
}, Promise.resolve());

// Or with async/await (covered later):
// for (const id of userIds) {
//   const user = await fetchUser(id);
//   console.log(user);
// }
```

---

## Common Mistakes

### Mistake 1: Forgetting to Return

```javascript
// ❌ Missing return — the next then() gets undefined
fetchUser(1)
  .then(user => {
    fetchOrders(user.id); // Not returned!
  })
  .then(orders => {
    console.log(orders); // undefined
  });

// ✅ Return the promise
fetchUser(1)
  .then(user => {
    return fetchOrders(user.id);
  })
  .then(orders => {
    console.log(orders); // Correct!
  });

// ✅ Implicit return with arrow
fetchUser(1)
  .then(user => fetchOrders(user.id))
  .then(orders => console.log(orders));
```

### Mistake 2: Not Returning in .catch()

```javascript
// ❌ Catch swallows the error but doesn't recover
fetchUser(999)
  .catch(error => {
    console.error(error); // Logs error
    // Returns undefined!
  })
  .then(user => {
    console.log(user); // undefined
  });

// ✅ Return a recovery value
fetchUser(999)
  .catch(error => {
    console.error(error);
    return { id: 0, name: "Guest" }; // Recovery
  })
  .then(user => {
    console.log(user); // { id: 0, name: "Guest" }
  });
```

### Mistake 3: Throwing in .then() Without Catch

```javascript
// ❌ Unhandled rejection
fetchUser(1)
  .then(user => {
    if (!user.active) {
      throw new Error("User inactive");
    }
    return user;
  });
  // No catch! Error is silently swallowed.

// ✅ Always catch at the end of a chain
fetchUser(1)
  .then(user => {
    if (!user.active) {
      throw new Error("User inactive");
    }
    return user;
  })
  .catch(error => console.error(error));
```

### Mistake 4: Confusing Promise.all with Sequential

```javascript
// ❌ This doesn't run in order!
const ids = [1, 2, 3];
Promise.all(ids.map(id => fetchUser(id)))
  .then(users => console.log(users));
// Users may arrive in any order depending on network!

// ✅ To preserve order with parallel execution:
const ids = [1, 2, 3];
Promise.all(ids.map(id => fetchUser(id)))
  .then(users => console.log(users));
// Actually, Promise.all DOES preserve input order in results!
```

---

## Practice Exercises

### Exercise 1: Chain Transformations

Transform a number through multiple operations using promise chaining.

```javascript
Promise.resolve(5)
  .then(/* multiply by 2 */)
  .then(/* add 10 */)
  .then(/* subtract 3 */)
  .then(result => console.log(result)); // Should be 17
```

### Exercise 2: Parallel Data Fetching

Fetch user profile and user settings in parallel, then display both.

```javascript
function fetchProfile(userId) { /* returns Promise */ }
function fetchSettings(userId) { /* returns Promise */ }

// Fetch both in parallel, then log combined result
```

### Exercise 3: Retry with Promise Chain

Write a function that retries a promise-returning function up to N times.

```javascript
function withRetry(fn, maxRetries) {
  // Return a promise that retries fn up to maxRetries on failure
}

withRetry(() => fetchUser(1), 3)
  .then(user => console.log(user))
  .catch(error => console.error("All retries failed"));
```

### Exercise 4: Waterfall Pattern

Implement a waterfall function that passes the result of each promise to the next.

```javascript
function waterfall(promises) {
  // promises is an array of functions that return promises
  // Each function receives the result of the previous one
}

waterfall([
  () => Promise.resolve(5),
  (prev) => Promise.resolve(prev * 2),
  (prev) => Promise.resolve(prev + 3)
]).then(result => console.log(result)); // 13
```

### Exercise 5: Promise Chain Visualization

Predict and explain the output:

```javascript
Promise.resolve(1)
  .then(x => x + 1)
  .then(x => { throw new Error("Oops"); })
  .catch(() => 5)
  .then(x => x + 1)
  .then(x => console.log(x))
  .catch(err => console.error(err));
```

---

## Summary

- **Promise chaining** creates readable, flat async code
- Each `.then()` receives the **return value** of the previous `.then()`
- Returning a Promise in `.then()` **waits** for it to resolve
- Errors **propagate down** the chain to the nearest `.catch()`
- `.catch()` can **recover** by returning a value
- `Promise.all()` runs promises **in parallel** and waits for all
- `Promise.race()` returns the **first to settle**
- `Promise.allSettled()` waits for **all** regardless of outcome
- `Promise.any()` returns the **first success**
- Always **return** promises in `.then()` handlers
- Always add a **final `.catch()`** to handle unexpected errors

---

## Next Steps

Build on promise chaining with:
- **Promises In Depth** — advanced patterns and edge cases
- **Async/Await** — the modern syntax for promise-based code
- **Converting Callbacks to Promises** — modernizing legacy code

Happy coding! 🚀
