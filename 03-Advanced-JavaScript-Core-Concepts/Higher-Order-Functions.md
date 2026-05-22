# Higher-Order Functions in JavaScript

## Overview

Higher-Order Functions (HOFs) are one of JavaScript's most powerful features. A function is called "higher-order" if it either **takes a function as an argument** or **returns a function as its result**. Understanding HOFs is essential for writing clean, modular, and functional-style JavaScript code.

---

## What Makes a Function "Higher-Order"?

In JavaScript, functions are **first-class citizens**. This means:
- Functions can be assigned to variables
- Functions can be stored in arrays and objects
- Functions can be passed as arguments to other functions
- Functions can be returned from other functions

```javascript
// A regular function
function regular() {
  return 42;
}

// A higher-order function (takes a function)
function hofTakes(fn) {
  return fn();
}

// A higher-order function (returns a function)
function hofReturns() {
  return function() {
    return "Hello!";
  };
}

console.log(hofTakes(regular));    // 42
console.log(hofReturns()());       // "Hello!"
```

---

## Functions as Arguments (Callbacks)

The most common use of HOFs is passing functions as arguments — these passed functions are called **callbacks**.

### Basic Example

```javascript
function greet(name, formatter) {
  return formatter(name);
}

function uppercase(str) {
  return str.toUpperCase();
}

function excited(str) {
  return str + "!!!";
}

console.log(greet("alice", uppercase)); // "ALICE"
console.log(greet("bob", excited));     // "bob!!!"
```

### Practical: Custom Logger

```javascript
function logMessage(message, logger) {
  const timestamp = new Date().toISOString();
  logger(`[${timestamp}] ${message}`);
}

logMessage("Server started", console.log);
logMessage("Error occurred", console.error);
```

---

## Functions Returning Functions

Functions that return other functions create powerful patterns like **function factories** and **partial application**.

### Function Factory

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

### Partial Application

Fix some arguments of a function, producing another function with fewer arguments:

```javascript
function greet(greeting, name) {
  return `${greeting}, ${name}!`;
}

function partial(fn, ...fixedArgs) {
  return function(...remainingArgs) {
    return fn(...fixedArgs, ...remainingArgs);
  };
}

const sayHello = partial(greet, "Hello");
console.log(sayHello("Alice")); // "Hello, Alice!"
console.log(sayHello("Bob"));   // "Hello, Bob!"
```

---

## Built-in Higher-Order Functions

JavaScript arrays come with powerful built-in HOFs.

### `forEach` — Execute for Each Element

```javascript
const fruits = ["apple", "banana", "cherry"];

fruits.forEach(function(fruit, index) {
  console.log(`${index}: ${fruit}`);
});

// With arrow function
fruits.forEach((fruit, index) => {
  console.log(`${index}: ${fruit}`);
});
```

### `map` — Transform Each Element

Creates a new array by applying a function to every element.

```javascript
const numbers = [1, 2, 3, 4, 5];

const doubled = numbers.map(n => n * 2);
console.log(doubled); // [2, 4, 6, 8, 10]

const names = ["alice", "bob", "charlie"];
const capitalized = names.map(name => name.charAt(0).toUpperCase() + name.slice(1));
console.log(capitalized); // ["Alice", "Bob", "Charlie"]
```

### `filter` — Select Matching Elements

Creates a new array with elements that pass a test.

```javascript
const numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10];

const evens = numbers.filter(n => n % 2 === 0);
console.log(evens); // [2, 4, 6, 8, 10]

const users = [
  { name: "Alice", age: 25 },
  { name: "Bob", age: 17 },
  { name: "Charlie", age: 30 }
];

const adults = users.filter(user => user.age >= 18);
console.log(adults);
// [{ name: "Alice", age: 25 }, { name: "Charlie", age: 30 }]
```

### `reduce` — Reduce to a Single Value

```javascript
const numbers = [1, 2, 3, 4, 5];

const sum = numbers.reduce((acc, curr) => acc + curr, 0);
console.log(sum); // 15

// Finding maximum
const max = numbers.reduce((max, n) => n > max ? n : max, numbers[0]);
console.log(max); // 5

// Flatten array
const nested = [[1, 2], [3, 4], [5, 6]];
const flat = nested.reduce((acc, arr) => [...acc, ...arr], []);
console.log(flat); // [1, 2, 3, 4, 5, 6]

// Count occurrences
const words = ["apple", "banana", "apple", "cherry", "banana", "apple"];
const count = words.reduce((acc, word) => {
  acc[word] = (acc[word] || 0) + 1;
  return acc;
}, {});
console.log(count); // { apple: 3, banana: 2, cherry: 1 }
```

### `find` — Find First Match

```javascript
const users = [
  { id: 1, name: "Alice" },
  { id: 2, name: "Bob" },
  { id: 3, name: "Charlie" }
];

const user = users.find(u => u.id === 2);
console.log(user); // { id: 2, name: "Bob" }
```

### `some` and `every`

```javascript
const numbers = [2, 4, 6, 8, 9];

console.log(numbers.some(n => n % 2 !== 0)); // true (at least one odd)
console.log(numbers.every(n => n % 2 === 0)); // false (not all even)
```

### `sort` with Comparator

```javascript
const numbers = [3, 1, 4, 1, 5, 9, 2];

// Ascending
console.log(numbers.sort((a, b) => a - b)); // [1, 1, 2, 3, 4, 5, 9]

// Descending
console.log(numbers.sort((a, b) => b - a)); // [9, 5, 4, 3, 2, 1, 1]

// Sort objects by property
const users = [
  { name: "Charlie", age: 30 },
  { name: "Alice", age: 25 },
  { name: "Bob", age: 35 }
];

users.sort((a, b) => a.age - b.age);
console.log(users.map(u => u.name)); // ["Alice", "Charlie", "Bob"]
```

---

## Chaining Higher-Order Functions

HOFs become incredibly powerful when chained together:

```javascript
const users = [
  { name: "alice", age: 25, active: true },
  { name: "bob", age: 17, active: false },
  { name: "charlie", age: 30, active: true },
  { name: "diana", age: 22, active: true }
];

// Get names of active adult users, capitalized
const result = users
  .filter(user => user.active)           // Keep only active users
  .filter(user => user.age >= 18)        // Keep only adults
  .map(user => user.name)                // Extract names
  .map(name => name.charAt(0).toUpperCase() + name.slice(1)) // Capitalize
  .sort();                               // Sort alphabetically

console.log(result); // ["Alice", "Charlie", "Diana"]
```

---

## Custom Higher-Order Functions

### Custom `map`

```javascript
function myMap(array, callback) {
  const result = [];
  for (let i = 0; i < array.length; i++) {
    result.push(callback(array[i], i, array));
  }
  return result;
}

console.log(myMap([1, 2, 3], n => n * 2)); // [2, 4, 6]
```

### Custom `filter`

```javascript
function myFilter(array, callback) {
  const result = [];
  for (let i = 0; i < array.length; i++) {
    if (callback(array[i], i, array)) {
      result.push(array[i]);
    }
  }
  return result;
}

console.log(myFilter([1, 2, 3, 4, 5], n => n > 2)); // [3, 4, 5]
```

### Custom `once` — Run a Function Only Once

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
initialize(); // No output, returns cached result
initialize(); // No output, returns cached result
```

### Custom `debounce` — Delay Execution

```javascript
function debounce(fn, delay) {
  let timeoutId;

  return function(...args) {
    clearTimeout(timeoutId);
    timeoutId = setTimeout(() => {
      fn.apply(this, args);
    }, delay);
  };
}

const search = debounce((query) => {
  console.log(`Searching for: ${query}`);
}, 300);

search("a");
search("ab");
search("abc"); // Only this executes after 300ms
```

### Custom `throttle` — Limit Execution Rate

```javascript
function throttle(fn, limit) {
  let inThrottle;

  return function(...args) {
    if (!inThrottle) {
      fn.apply(this, args);
      inThrottle = true;
      setTimeout(() => inThrottle = false, limit);
    }
  };
}

const handleScroll = throttle(() => {
  console.log("Scroll event handled");
}, 1000);

// handleScroll can only run once per second
```

### Custom `memoize` — Cache Results

```javascript
function memoize(fn) {
  const cache = new Map();

  return function(...args) {
    const key = JSON.stringify(args);
    if (cache.has(key)) {
      return cache.get(key);
    }
    const result = fn.apply(this, args);
    cache.set(key, result);
    return result;
  };
}

const slowFib = memoize(function fib(n) {
  if (n <= 1) return n;
  return fib(n - 1) + fib(n - 2);
});

console.log(slowFib(40)); // Fast due to caching!
```

---

## Functional Programming Patterns

### Composition

Combine functions where the output of one is the input of another:

```javascript
const compose = (...fns) => (value) =>
  fns.reduceRight((acc, fn) => fn(acc), value);

const pipe = (...fns) => (value) =>
  fns.reduce((acc, fn) => fn(acc), value);

const add5 = x => x + 5;
const multiplyBy2 = x => x * 2;
const toString = x => String(x);

const composed = compose(toString, multiplyBy2, add5);
console.log(composed(10)); // "30" (10 + 5 = 15, 15 * 2 = 30, "30")

const piped = pipe(add5, multiplyBy2, toString);
console.log(piped(10)); // "30" (same result, different order)
```

### Currying

Transform a function with multiple arguments into a sequence of functions with single arguments:

```javascript
function curry(fn) {
  return function curried(...args) {
    if (args.length >= fn.length) {
      return fn.apply(this, args);
    }
    return function(...nextArgs) {
      return curried.apply(this, [...args, ...nextArgs]);
    };
  };
}

function sum(a, b, c) {
  return a + b + c;
}

const curriedSum = curry(sum);
console.log(curriedSum(1)(2)(3)); // 6
console.log(curriedSum(1, 2)(3)); // 6
console.log(curriedSum(1)(2, 3)); // 6
```

---

## Common Mistakes

### Mistake 1: Mutating in `map`

```javascript
// ❌ map should not mutate, it should transform
const nums = [1, 2, 3];
nums.map(n => {
  nums.push(n * 2); // Don't do this!
});

// ✅ Return the transformed value
const doubled = nums.map(n => n * 2);
```

### Mistake 2: Forgetting `return` in Arrow Functions

```javascript
// ❌ Implicit return only works without braces
const doubled = [1, 2, 3].map(n => {
  n * 2; // No return!
});
// [undefined, undefined, undefined]

// ✅ Either remove braces or add return
const doubled = [1, 2, 3].map(n => n * 2);
const doubled2 = [1, 2, 3].map(n => { return n * 2; });
```

### Mistake 3: Using `forEach` for Transformation

```javascript
// ❌ forEach doesn't return anything
const result = [1, 2, 3].forEach(n => n * 2);
console.log(result); // undefined

// ✅ Use map for transformations
const doubled = [1, 2, 3].map(n => n * 2);
```

### Mistake 4: Chaining Side Effects

```javascript
// ❌ filter().forEach() is fine, but avoid mutating during chain
const users = [...];
users
  .filter(u => u.active)
  .forEach(u => delete u.password); // Mutating during chain
```

---

## Practice Exercises

### Exercise 1: Custom `flatMap`

Implement a `flatMap` function that maps and flattens in one step.

```javascript
function flatMap(array, callback) {
  // Your code
}

console.log(flatMap([1, 2, 3], n => [n, n * 2]));
// [1, 2, 2, 4, 3, 6]
```

### Exercise 2: Group By

Implement a `groupBy` function using `reduce`.

```javascript
const users = [
  { name: "Alice", role: "admin" },
  { name: "Bob", role: "user" },
  { name: "Charlie", role: "admin" }
];

// Result should be:
// {
//   admin: [{ name: "Alice", role: "admin" }, { name: "Charlie", role: "admin" }],
//   user: [{ name: "Bob", role: "user" }]
// }
```

### Exercise 3: Pipeline

Create a function that pipes data through multiple transformations.

```javascript
const pipeline = [
  x => x + 1,
  x => x * 2,
  x => x - 3
];

// Starting with 5: (5 + 1) * 2 - 3 = 9
```

### Exercise 4: Custom `zipWith`

Combine two arrays using a function.

```javascript
function zipWith(arr1, arr2, fn) {
  // Your code
}

console.log(zipWith([1, 2, 3], [4, 5, 6], (a, b) => a + b));
// [5, 7, 9]
```

### Exercise 5: Async HOF

Create a higher-order function that adds retry logic to any async function.

```javascript
function withRetry(fn, maxAttempts) {
  // Return a function that retries fn up to maxAttempts times
}
```

---

## Summary

- **Higher-Order Functions** take functions as arguments or return functions
- JavaScript treats functions as **first-class citizens**
- Built-in array HOFs: `map`, `filter`, `reduce`, `find`, `some`, `every`, `sort`
- **Chaining** HOFs creates readable data transformation pipelines
- Custom HOFs like `debounce`, `throttle`, `memoize`, and `once` solve real problems
- **Composition** and **currying** are powerful functional programming patterns
- Always return values in `map`; use `forEach` only for side effects

---

## Next Steps

Build on HOFs by learning:
- **Closures** — how returned functions remember their scope
- **Callbacks** — asynchronous patterns with HOFs
- **Array Methods Deep Dive** — mastering every array HOF

Happy coding! 🚀
