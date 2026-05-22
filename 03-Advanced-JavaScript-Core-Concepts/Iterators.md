# Iterators in JavaScript

## Overview

An **iterator** is an object that defines a sequence and potentially a return value upon its termination. It provides a standard way to access elements from a collection one at a time, without exposing the underlying representation. Iterators are the backbone of JavaScript's `for...of` loops, spread syntax, and destructuring.

---

## The Iterator Protocol

JavaScript iterators follow a simple protocol. An object is an iterator when it implements a `next()` method with the following signature:

```javascript
iterator.next() // Returns: { value: any, done: boolean }
```

### Iterator Result Object

| Property | Description |
|----------|-------------|
| `value` | The current value in the sequence |
| `done` | `true` if the iterator has reached the end, `false` otherwise |

### Simple Iterator Example

```javascript
function createCounterIterator(start, end) {
  let current = start;

  return {
    next: function() {
      if (current <= end) {
        return { value: current++, done: false };
      }
      return { value: undefined, done: true };
    }
  };
}

const counter = createCounterIterator(1, 3);

console.log(counter.next()); // { value: 1, done: false }
console.log(counter.next()); // { value: 2, done: false }
console.log(counter.next()); // { value: 3, done: false }
console.log(counter.next()); // { value: undefined, done: true }
```

---

## The Iterable Protocol

An **iterable** is an object that defines its own iteration behavior. It must implement a `[Symbol.iterator]` method that returns an iterator.

```javascript
const iterable = {
  [Symbol.iterator]: function() {
    let step = 0;
    return {
      next: function() {
        step++;
        if (step === 1) return { value: "Hello", done: false };
        if (step === 2) return { value: "World", done: false };
        return { value: undefined, done: true };
      }
    };
  }
};

// Now we can use for...of!
for (const item of iterable) {
  console.log(item);
}
// "Hello"
// "World"
```

### Built-in Iterables

JavaScript has several built-in iterables:

| Object | Iterates Over |
|--------|--------------|
| `String` | Characters |
| `Array` | Elements |
| `Set` | Unique values |
| `Map` | `[key, value]` pairs |
| `TypedArray` | Elements |
| `arguments` | Arguments passed to a function |
| `NodeList` | DOM nodes |

```javascript
// String
for (const char of "Hello") {
  console.log(char); // H, e, l, l, o
}

// Array
for (const item of [10, 20, 30]) {
  console.log(item); // 10, 20, 30
}

// Set
for (const item of new Set([1, 2, 2, 3])) {
  console.log(item); // 1, 2, 3
}

// Map
for (const [key, value] of new Map([["a", 1], ["b", 2]])) {
  console.log(`${key}: ${value}`); // a: 1, b: 2
}
```

---

## Creating Custom Iterables

### Range Iterable

```javascript
class Range {
  constructor(start, end, step = 1) {
    this.start = start;
    this.end = end;
    this.step = step;
  }

  [Symbol.iterator]() {
    let current = this.start;
    const end = this.end;
    const step = this.step;

    return {
      next() {
        if (current <= end) {
          const value = current;
          current += step;
          return { value, done: false };
        }
        return { value: undefined, done: true };
      }
    };
  }
}

const range = new Range(1, 5);

for (const num of range) {
  console.log(num); // 1, 2, 3, 4, 5
}

// With spread
console.log([...new Range(10, 20, 5)]); // [10, 15, 20]
```

### Infinite Iterator (with safety)

```javascript
function createFibonacciIterator() {
  let a = 0, b = 1;

  return {
    next: function() {
      const current = a;
      a = b;
      b = current + b;
      return { value: current, done: false };
    }
  };
}

const fib = createFibonacciIterator();

for (let i = 0; i < 10; i++) {
  console.log(fib.next().value);
}
// 0, 1, 1, 2, 3, 5, 8, 13, 21, 34
```

---

## Iterator Methods on Built-in Objects

### Array Iterator Methods

```javascript
const arr = ["a", "b", "c"];

// keys() — returns iterator for indices
for (const key of arr.keys()) {
  console.log(key); // 0, 1, 2
}

// values() — returns iterator for values
for (const value of arr.values()) {
  console.log(value); // a, b, c
}

// entries() — returns iterator for [index, value] pairs
for (const [index, value] of arr.entries()) {
  console.log(`${index}: ${value}`);
  // 0: a, 1: b, 2: c
}
```

### Map Iterator Methods

```javascript
const map = new Map([["a", 1], ["b", 2], ["c", 3]]);

// keys()
for (const key of map.keys()) {
  console.log(key); // a, b, c
}

// values()
for (const value of map.values()) {
  console.log(value); // 1, 2, 3
}

// entries()
for (const entry of map.entries()) {
  console.log(entry); // ["a", 1], ["b", 2], ["c", 3]
}
```

### Set Iterator Methods

```javascript
const set = new Set([1, 2, 3]);

for (const value of set.values()) {
  console.log(value); // 1, 2, 3
}

// Sets don't have keys — keys() returns the same as values()
for (const key of set.keys()) {
  console.log(key); // 1, 2, 3
}
```

---

## Manual Iteration

You don't have to use `for...of`. You can manually call `next()`:

```javascript
const arr = [10, 20, 30];
const iterator = arr[Symbol.iterator]();

let result = iterator.next();
while (!result.done) {
  console.log(result.value);
  result = iterator.next();
}
// 10, 20, 30
```

This is useful when you need more control over the iteration process.

---

## Iterators and Destructuring

Destructuring uses the iterator protocol under the hood:

```javascript
const arr = [1, 2, 3, 4, 5];

// Array destructuring
const [first, second, ...rest] = arr;
console.log(first, second, rest); // 1, 2, [3, 4, 5]

// String destructuring
const [a, b, c] = "Hello";
console.log(a, b, c); // H, e, l

// With custom iterables
const range = new Range(100, 500, 100);
const [hundreds, twoHundreds, threeHundreds] = range;
console.log(hundreds, twoHundreds, threeHundreds); // 100, 200, 300
```

---

## Iterators and Spread Syntax

```javascript
const range = new Range(1, 5);

// Spread into array
const arr = [...range];
console.log(arr); // [1, 2, 3, 4, 5]

// Spread into function arguments
console.log(Math.max(...new Range(1, 10))); // 10

// Combine iterables
const combined = [..."ab", ...new Range(1, 3)];
console.log(combined); // ["a", "b", 1, 2, 3]
```

---

## Iterators with Destructuring in Real Code

```javascript
const userMap = new Map([
  ["id", 1],
  ["name", "Alice"],
  ["role", "admin"]
]);

// Convert map to object
const user = Object.fromEntries(userMap);
console.log(user); // { id: 1, name: "Alice", role: "admin" }

// Iterate with destructuring
for (const [key, value] of userMap) {
  console.log(`${key}: ${value}`);
}
```

---

## Making Objects Iterable

Objects are **not** iterable by default. You can make them iterable by adding `[Symbol.iterator]`:

```javascript
const team = {
  lead: "Alice",
  developer: "Bob",
  designer: "Charlie",

  [Symbol.iterator]() {
    const roles = Object.keys(this);
    const values = Object.values(this);
    let index = 0;

    return {
      next: () => {
        if (index < values.length) {
          return {
            value: { role: roles[index], name: values[index++] },
            done: false
          };
        }
        return { done: true };
      }
    };
  }
};

for (const member of team) {
  console.log(`${member.role}: ${member.name}`);
}
// lead: Alice
// developer: Bob
// designer: Charlie
```

---

## Iterator Return and Throw

Advanced iterators can implement `return()` and `throw()` methods:

### `return()` — Cleanup When Iteration Ends Early

```javascript
function createResourceIterator() {
  let resource = { open: true, data: [1, 2, 3] };
  let index = 0;

  return {
    next() {
      if (index < resource.data.length) {
        return { value: resource.data[index++], done: false };
      }
      return { done: true };
    },
    return() {
      resource.open = false;
      console.log("Resource cleaned up!");
      return { done: true };
    }
  };
}

const iterable = {
  [Symbol.iterator]: createResourceIterator
};

for (const value of iterable) {
  console.log(value);
  if (value === 2) break; // Triggers return()!
}
// 1
// 2
// Resource cleaned up!
```

---

## Common Mistakes

### Mistake 1: Modifying Collection During Iteration

```javascript
const arr = [1, 2, 3, 4, 5];

for (const num of arr) {
  if (num === 3) {
    arr.push(6); // ❌ Don't modify during iteration!
  }
  console.log(num);
}
```

### Mistake 2: Expecting Objects to Be Iterable

```javascript
const obj = { a: 1, b: 2 };

// ❌ Error! Objects are not iterable
// for (const item of obj) { ... }

// ✅ Use Object.keys(), Object.values(), or Object.entries()
for (const [key, value] of Object.entries(obj)) {
  console.log(`${key}: ${value}`);
}
```

### Mistake 3: Creating New Iterator Each Time

```javascript
const range = new Range(1, 3);

// ✅ Each for...of creates a fresh iterator
for (const num of range) console.log(num); // 1, 2, 3
for (const num of range) console.log(num); // 1, 2, 3

// ❌ Manual iterator is consumed
const iterator = range[Symbol.iterator]();
iterator.next(); // { value: 1, done: false }
iterator.next(); // { value: 2, done: false }
// iterator is now at position 3
```

---

## Practice Exercises

### Exercise 1: Reverse Iterator

Create an iterable that iterates an array in reverse.

```javascript
const arr = [1, 2, 3, 4, 5];

for (const item of reverse(arr)) {
  console.log(item); // 5, 4, 3, 2, 1
}
```

### Exercise 2: Take N Iterator

Create a function that takes only the first N values from an iterator.

```javascript
const fib = createFibonacciIterator();

for (const num of take(fib, 5)) {
  console.log(num); // 0, 1, 1, 2, 3
}
```

### Exercise 3: Zip Iterators

Combine two iterables element by element.

```javascript
const names = ["Alice", "Bob", "Charlie"];
const ages = [25, 30, 35];

for (const [name, age] of zip(names, ages)) {
  console.log(`${name} is ${age}`);
}
```

### Exercise 4: Filter Iterator

Create a filtered iterator that only yields matching values.

```javascript
const numbers = [1, 2, 3, 4, 5, 6];

for (const num of filter(numbers, n => n % 2 === 0)) {
  console.log(num); // 2, 4, 6
}
```

### Exercise 5: Paginated Iterator

Create an iterable that simulates paginated API results.

```javascript
const pages = createPaginatedIterator("/api/users", 10);

for (const page of pages) {
  console.log(`Loaded ${page.length} users`);
}
```

---

## Summary

- An **iterator** is an object with a `next()` method returning `{ value, done }`
- An **iterable** is an object with a `[Symbol.iterator]` method returning an iterator
- Built-in iterables: `String`, `Array`, `Set`, `Map`, `TypedArray`
- `for...of`, spread syntax, and destructuring all use the iterator protocol
- You can create **custom iterables** by implementing `[Symbol.iterator]`
- Iterators can implement `return()` for cleanup when iteration ends early
- Objects are **not** iterable by default — use `Object.entries()` or add `[Symbol.iterator]`

---

## Next Steps

Iterators lead naturally into:
- **Generators** — cleaner syntax for creating iterators
- **Async Iterators** — iterating over async data sources
- **Functional Programming** — map, filter, reduce with iterables

Happy coding! 🚀
