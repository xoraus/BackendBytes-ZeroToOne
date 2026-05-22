# Callbacks in JavaScript

## Overview

A **callback** is a function passed as an argument into another function, which is then invoked inside the outer function to complete some kind of action. Callbacks are the foundation of asynchronous programming in JavaScript and one of the most important patterns to master.

---

## What is a Callback?

At its simplest, a callback is just a function that you pass to another function to be executed later.

```javascript
function greet(name, callback) {
  console.log("Hello, " + name);
  callback();
}

function sayGoodbye() {
  console.log("Goodbye!");
}

greet("Alice", sayGoodbye);

// Output:
// Hello, Alice
// Goodbye!
```

The function `sayGoodbye` is passed as a callback to `greet` and is executed after the greeting.

---

## Synchronous Callbacks

These callbacks execute immediately, blocking further execution until complete.

### Array Methods

```javascript
const numbers = [1, 2, 3, 4, 5];

// forEach with callback
numbers.forEach(function(num) {
  console.log(num * 2);
});

// map with callback
const doubled = numbers.map(num => num * 2);

// filter with callback
const evens = numbers.filter(num => num % 2 === 0);

// reduce with callback
const sum = numbers.reduce((acc, num) => acc + num, 0);
```

### Custom Synchronous Callback

```javascript
function processData(data, processor) {
  const results = [];
  for (const item of data) {
    results.push(processor(item));
  }
  return results;
}

const numbers = [1, 2, 3];
const squared = processData(numbers, n => n * n);
console.log(squared); // [1, 4, 9]
```

---

## Asynchronous Callbacks

These callbacks execute after some asynchronous operation completes — a network request finishes, a timer expires, or a user clicks a button.

### setTimeout

```javascript
console.log("Start");

setTimeout(function() {
  console.log("This runs after 2 seconds");
}, 2000);

console.log("End");

// Output:
// Start
// End
// This runs after 2 seconds (after 2 seconds)
```

### Event Listeners

```javascript
document.getElementById("myButton").addEventListener("click", function() {
  console.log("Button clicked!");
});
```

### Reading a File (Node.js)

```javascript
const fs = require("fs");

fs.readFile("file.txt", "utf8", function(error, data) {
  if (error) {
    console.error("Error reading file:", error);
    return;
  }
  console.log("File contents:", data);
});

console.log("Reading file...");
```

---

## The Callback Pattern

A standard callback takes an error as the first argument and the result as the second:

```javascript
function fetchData(callback) {
  // Simulate async operation
  setTimeout(() => {
    const success = Math.random() > 0.5;

    if (success) {
      callback(null, { data: "Some data" });
    } else {
      callback(new Error("Failed to fetch data"), null);
    }
  }, 1000);
}

// Using the callback
fetchData(function(error, result) {
  if (error) {
    console.error("Error:", error.message);
    return;
  }
  console.log("Success:", result);
});
```

This **error-first callback** pattern (also called Node.js style) is the standard convention.

---

## Callback Hell (Pyramid of Doom)

When you have multiple nested asynchronous operations, callbacks can become deeply nested and hard to read:

```javascript
getUserData(userId, function(error, user) {
  if (error) {
    console.error(error);
    return;
  }

  getOrders(user.id, function(error, orders) {
    if (error) {
      console.error(error);
      return;
    }

    getProducts(orders[0].id, function(error, products) {
      if (error) {
        console.error(error);
        return;
      }

      getReviews(products[0].id, function(error, reviews) {
        if (error) {
          console.error(error);
          return;
        }

        console.log(reviews);
      });
    });
  });
});
```

### Problems with Callback Hell

1. **Hard to read** — deep indentation makes code difficult to follow
2. **Error handling duplication** — every level needs error checking
3. **Difficult to maintain** — adding or removing steps is painful
4. **No linear flow** — execution order doesn't match code order

---

## Solving Callback Hell

### Solution 1: Named Functions

```javascript
function handleReviews(error, reviews) {
  if (error) {
    console.error(error);
    return;
  }
  console.log(reviews);
}

function handleProducts(error, products) {
  if (error) {
    console.error(error);
    return;
  }
  getReviews(products[0].id, handleReviews);
}

function handleOrders(error, orders) {
  if (error) {
    console.error(error);
    return;
  }
  getProducts(orders[0].id, handleProducts);
}

function handleUser(error, user) {
  if (error) {
    console.error(error);
    return;
  }
  getOrders(user.id, handleOrders);
}

getUserData(userId, handleUser);
```

This is cleaner but spreads related code across many functions.

### Solution 2: Promises (Modern Approach)

Promises provide a cleaner way to chain asynchronous operations:

```javascript
getUserData(userId)
  .then(user => getOrders(user.id))
  .then(orders => getProducts(orders[0].id))
  .then(products => getReviews(products[0].id))
  .then(reviews => console.log(reviews))
  .catch(error => console.error(error));
```

### Solution 3: Async/Await (Cleanest)

```javascript
async function fetchReviewData(userId) {
  try {
    const user = await getUserData(userId);
    const orders = await getOrders(user.id);
    const products = await getProducts(orders[0].id);
    const reviews = await getReviews(products[0].id);
    console.log(reviews);
  } catch (error) {
    console.error(error);
  }
}
```

> We'll cover Promises and Async/Await in detail in later sections. For now, understand that callbacks are the underlying mechanism.

---

## Higher-Order Functions with Callbacks

Functions that accept callbacks are a form of higher-order function:

```javascript
function repeat(n, action) {
  for (let i = 0; i < n; i++) {
    action(i);
  }
}

repeat(3, console.log);
// 0
// 1
// 2

repeat(3, i => {
  console.log(`Iteration ${i + 1}`);
});
// Iteration 1
// Iteration 2
// Iteration 3
```

---

## Callbacks in Array Methods

### forEach

```javascript
const fruits = ["apple", "banana", "cherry"];

fruits.forEach(function(fruit, index) {
  console.log(`${index}: ${fruit}`);
});
```

### map

```javascript
const users = [
  { firstName: "Alice", lastName: "Smith" },
  { firstName: "Bob", lastName: "Jones" }
];

const fullNames = users.map(function(user) {
  return `${user.firstName} ${user.lastName}`;
});

console.log(fullNames); // ["Alice Smith", "Bob Jones"]
```

### Custom Filter

```javascript
function myFilter(array, callback) {
  const results = [];
  for (let i = 0; i < array.length; i++) {
    if (callback(array[i], i, array)) {
      results.push(array[i]);
    }
  }
  return results;
}

const numbers = [1, 2, 3, 4, 5, 6];
const evens = myFilter(numbers, num => num % 2 === 0);
console.log(evens); // [2, 4, 6]
```

---

## Callback Context (this)

Be careful with `this` inside callbacks:

```javascript
const user = {
  name: "Alice",
  friends: ["Bob", "Charlie"],
  showFriends: function() {
    this.friends.forEach(function(friend) {
      console.log(`${this.name} is friends with ${friend}`);
      // ❌ this.name is undefined!
    });
  }
};

// Fix 1: Arrow function (inherits this)
showFriends: function() {
  this.friends.forEach(friend => {
    console.log(`${this.name} is friends with ${friend}`);
  });
}

// Fix 2: Store this in a variable
showFriends: function() {
  const self = this;
  this.friends.forEach(function(friend) {
    console.log(`${self.name} is friends with ${friend}`);
  });
}

// Fix 3: bind()
showFriends: function() {
  this.friends.forEach(function(friend) {
    console.log(`${this.name} is friends with ${friend}`);
  }.bind(this));
}
```

---

## Writing Callback-Based Functions

### Basic Async Function

```javascript
function delay(ms, callback) {
  setTimeout(() => {
    callback();
  }, ms);
}

delay(1000, () => {
  console.log("1 second passed");
});
```

### With Error Handling

```javascript
function readJsonFile(filePath, callback) {
  fs.readFile(filePath, "utf8", (error, data) => {
    if (error) {
      callback(error);
      return;
    }

    try {
      const json = JSON.parse(data);
      callback(null, json);
    } catch (parseError) {
      callback(parseError);
    }
  });
}

readJsonFile("data.json", (error, data) => {
  if (error) {
    console.error("Failed:", error.message);
    return;
  }
  console.log("Data:", data);
});
```

---

## Common Mistakes

### Mistake 1: Calling the Callback Instead of Passing It

```javascript
// ❌ Wrong — calls the function immediately
setTimeout(sayHello(), 1000);

// ✅ Right — passes the function reference
setTimeout(sayHello, 1000);

// ✅ Also right — arrow function wrapping
setTimeout(() => sayHello(), 1000);
```

### Mistake 2: Forgetting Error Handling

```javascript
// ❌ Missing error check
fs.readFile("file.txt", (error, data) => {
  console.log(data); // Could be undefined!
});

// ✅ Always check errors first
fs.readFile("file.txt", (error, data) => {
  if (error) {
    console.error(error);
    return;
  }
  console.log(data);
});
```

### Mistake 3: Callback Not Being Asynchronous

```javascript
// ❌ Confusing — looks async but isn't
function maybeAsync(callback) {
  if (cache.hasData) {
    callback(cache.data); // Synchronous!
  } else {
    fetchData(callback);  // Asynchronous
  }
}

// ✅ Always async (use setTimeout or process.nextTick)
function alwaysAsync(callback) {
  if (cache.hasData) {
    setTimeout(() => callback(null, cache.data), 0);
  } else {
    fetchData(callback);
  }
}
```

### Mistake 4: Calling Callback Multiple Times

```javascript
// ❌ Callback called twice!
function process(data, callback) {
  if (data.valid) {
    callback(null, data);
  }
  callback(new Error("Invalid")); // Always called!
}

// ✅ Return after calling callback
function process(data, callback) {
  if (data.valid) {
    return callback(null, data);
  }
  callback(new Error("Invalid"));
}
```

---

## Practice Exercises

### Exercise 1: Custom forEach

Implement your own `forEach` that accepts a callback.

```javascript
function myForEach(array, callback) {
  // Your code
}

myForEach([1, 2, 3], (item, index) => {
  console.log(`${index}: ${item}`);
});
```

### Exercise 2: Sequential Async

Write a function that runs an array of async operations sequentially using callbacks.

```javascript
function runSequential(tasks, finalCallback) {
  // tasks is an array of functions that take a callback
}

const tasks = [
  cb => setTimeout(() => { console.log("Task 1"); cb(); }, 100),
  cb => setTimeout(() => { console.log("Task 2"); cb(); }, 50),
  cb => setTimeout(() => { console.log("Task 3"); cb(); }, 200)
];

runSequential(tasks, () => console.log("All done!"));
```

### Exercise 3: Retry with Callbacks

Create a function that retries an async operation up to N times.

```javascript
function retry(fn, maxRetries, callback) {
  // fn is a function that takes (error, result) callback
}
```

### Exercise 4: Parallel Async

Write a function that runs async operations in parallel and collects all results.

```javascript
function runParallel(tasks, finalCallback) {
  // Run all tasks simultaneously
  // Call finalCallback(error, results) when all complete
}
```

### Exercise 5: Timeout Wrapper

Wrap a callback-based function with a timeout.

```javascript
function withTimeout(fn, ms) {
  // Return a new function that fails if fn doesn't call back within ms
}
```

---

## Summary

- A **callback** is a function passed as an argument to another function
- **Synchronous callbacks** execute immediately (array methods)
- **Asynchronous callbacks** execute later (timers, events, I/O)
- Use the **error-first pattern**: `callback(error, result)`
- **Callback hell** occurs with deeply nested async operations
- Solutions: named functions, Promises, or async/await
- Be careful with **`this`** context inside callbacks
- Always **check for errors** before using results
- Call callbacks **exactly once** and prefer **always-async** behavior

---

## Next Steps

Callbacks lead naturally into:
- **Promises** — cleaner async handling
- **Async/Await** — synchronous-looking async code
- **Event Loop** — how JavaScript handles async operations

Happy coding! 🚀
