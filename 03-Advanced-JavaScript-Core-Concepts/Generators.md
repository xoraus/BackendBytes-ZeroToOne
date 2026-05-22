# Generators in JavaScript

## Overview

**Generators** are a special type of function that can pause and resume execution. They provide a cleaner, more intuitive way to write iterators and manage asynchronous flows. Introduced in ES6, generators use the `function*` syntax and the `yield` keyword to produce a sequence of values on demand.

---

## What is a Generator?

A generator function returns a **generator object** that conforms to both the **iterator** and **iterable** protocols. Unlike regular functions that run to completion, generators can yield multiple values over time.

```javascript
function* simpleGenerator() {
  yield 1;
  yield 2;
  yield 3;
}

const gen = simpleGenerator();

console.log(gen.next()); // { value: 1, done: false }
console.log(gen.next()); // { value: 2, done: false }
console.log(gen.next()); // { value: 3, done: false }
console.log(gen.next()); // { value: undefined, done: true }
```

**Key differences from regular functions:**
1. Declared with `function*` (note the asterisk)
2. Use `yield` to pause and return a value
3. Execution can be resumed from where it left off
4. Returns a generator object, not a single value

---

## Generator Syntax

### Basic Generator

```javascript
function* countUpTo(max) {
  let count = 1;
  while (count <= max) {
    yield count;
    count++;
  }
}

const counter = countUpTo(5);

for (const num of counter) {
  console.log(num); // 1, 2, 3, 4, 5
}
```

### Generator as an Iterable

```javascript
function* alphabet() {
  yield "a";
  yield "b";
  yield "c";
}

// Can use for...of
for (const letter of alphabet()) {
  console.log(letter); // a, b, c
}

// Can use spread
console.log([...alphabet()]); // ["a", "b", "c"]

// Can destructure
const [first, second] = alphabet();
console.log(first, second); // a, b
```

---

## How Generators Work

### The Pause-and-Resume Model

```javascript
function* pauseAndResume() {
  console.log("Start");
  yield "First pause";

  console.log("Resumed");
  yield "Second pause";

  console.log("Resumed again");
  return "Done";
}

const gen = pauseAndResume();

console.log("--- Calling next() ---");
console.log(gen.next());
// "Start"
// { value: "First pause", done: false }

console.log("--- Calling next() ---");
console.log(gen.next());
// "Resumed"
// { value: "Second pause", done: false }

console.log("--- Calling next() ---");
console.log(gen.next());
// "Resumed again"
// { value: "Done", done: true }

console.log("--- Calling next() ---");
console.log(gen.next());
// { value: undefined, done: true }
```

**Execution flow:**
1. Calling `generator()` doesn't execute the body — it returns a generator object
2. Each `next()` call runs the generator until the next `yield`
3. The generator's state (local variables) is preserved between calls
4. When `return` is hit (or the function ends), `done` becomes `true`

---

## Practical Generator Examples

### Infinite Sequence

```javascript
function* infiniteCounter(start = 0) {
  let count = start;
  while (true) {
    yield count++;
  }
}

const counter = infiniteCounter(1);

console.log(counter.next().value); // 1
console.log(counter.next().value); // 2
console.log(counter.next().value); // 3
// Can keep calling forever...

// Use with for...of (must break manually!)
for (const num of infiniteCounter(10)) {
  if (num > 15) break;
  console.log(num); // 10, 11, 12, 13, 14, 15
}
```

### Fibonacci Generator

```javascript
function* fibonacci() {
  let a = 0, b = 1;

  while (true) {
    yield a;
    [a, b] = [b, a + b];
  }
}

const fib = fibonacci();

for (let i = 0; i < 10; i++) {
  console.log(fib.next().value);
}
// 0, 1, 1, 2, 3, 5, 8, 13, 21, 34
```

### Range Generator

```javascript
function* range(start, end, step = 1) {
  for (let i = start; i <= end; i += step) {
    yield i;
  }
}

console.log([...range(1, 10)]);       // [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
console.log([...range(0, 20, 5)]);    // [0, 5, 10, 15, 20]
console.log([...range(10, 1, -1)]);   // [10, 9, 8, 7, 6, 5, 4, 3, 2, 1]
```

### ID Generator

```javascript
function* idGenerator(prefix = "ID") {
  let count = 1;
  while (true) {
    yield `${prefix}-${String(count).padStart(4, "0")}`;
    count++;
  }
}

const userIds = idGenerator("USER");

console.log(userIds.next().value); // "USER-0001"
console.log(userIds.next().value); // "USER-0002"
console.log(userIds.next().value); // "USER-0003"
```

---

## Two-Way Communication with yield

Generators can both produce and consume values. The argument passed to `next()` becomes the value of the previous `yield` expression.

```javascript
function* twoWay() {
  const name = yield "What is your name?";
  const age = yield `Hello ${name}! How old are you?`;
  yield `${name}, you will be ${parseInt(age) + 1} next year!`;
}

const gen = twoWay();

console.log(gen.next().value);           // "What is your name?"
console.log(gen.next("Alice").value);    // "Hello Alice! How old are you?"
console.log(gen.next("25").value);       // "Alice, you will be 26 next year!"
```

**How it works:**
1. First `next()` starts the generator, runs until first `yield`
2. Second `next("Alice")` resumes, assigning "Alice" to the first `yield` expression
3. Third `next("25")` resumes, assigning "25" to the second `yield` expression

### Practical Example: Task Runner

```javascript
function* taskRunner() {
  const task1 = yield "Start task 1";
  console.log(`Task 1 result: ${task1}`);

  const task2 = yield "Start task 2";
  console.log(`Task 2 result: ${task2}`);

  const task3 = yield "Start task 3";
  console.log(`Task 3 result: ${task3}`);

  return "All tasks complete";
}

const runner = taskRunner();

let result = runner.next();
while (!result.done) {
  console.log(result.value);
  // Simulate async task completion
  result = runner.next(`Completed: ${result.value}`);
}
console.log(result.value);
```

---

## Delegating to Another Generator (`yield*`)

Use `yield*` to delegate iteration to another iterable (generator, array, string, etc.).

```javascript
function* generatorA() {
  yield "A1";
  yield "A2";
}

function* generatorB() {
  yield "B1";
  yield* generatorA(); // Delegate to generatorA
  yield "B2";
}

for (const value of generatorB()) {
  console.log(value);
}
// B1
// A1
// A2
// B2
```

### Delegating to Arrays

```javascript
function* combined() {
  yield "Start";
  yield* [1, 2, 3];
  yield "Middle";
  yield* "abc";
  yield "End";
}

console.log([...combined()]);
// ["Start", 1, 2, 3, "Middle", "a", "b", "c", "End"]
```

### Traversing a Tree Structure

```javascript
class TreeNode {
  constructor(value, children = []) {
    this.value = value;
    this.children = children;
  }
}

function* traverseDepthFirst(node) {
  yield node.value;
  for (const child of node.children) {
    yield* traverseDepthFirst(child);
  }
}

const tree = new TreeNode("root", [
  new TreeNode("child1", [
    new TreeNode("grandchild1"),
    new TreeNode("grandchild2")
  ]),
  new TreeNode("child2")
]);

for (const value of traverseDepthFirst(tree)) {
  console.log(value);
}
// root, child1, grandchild1, grandchild2, child2
```

---

## Generator Methods

### `return()` — Early Termination

```javascript
function* counter() {
  try {
    yield 1;
    yield 2;
    yield 3;
  } finally {
    console.log("Cleanup!");
  }
}

const gen = counter();
console.log(gen.next());    // { value: 1, done: false }
console.log(gen.return(99)); // { value: 99, done: true }
console.log("Cleanup!");     // Runs due to finally
console.log(gen.next());    // { value: undefined, done: true }
```

### `throw()` — Inject an Error

```javascript
function* errorHandler() {
  try {
    yield "Start";
    yield "Middle"; // This line is never reached
  } catch (error) {
    yield `Caught: ${error.message}`;
  }
}

const gen = errorHandler();
console.log(gen.next());                    // { value: "Start", done: false }
console.log(gen.throw(new Error("Oops!"))); // { value: "Caught: Oops!", done: false }
```

---

## Generators vs Regular Iterators

| Feature | Regular Iterator | Generator |
|---------|-----------------|-----------|
| Syntax | Manual `next()` method | `function*` + `yield` |
| State management | Manual | Automatic |
| Code readability | Verbose | Clean and linear |
| Two-way communication | Possible | Built-in |
| Error handling | Manual | `try...catch` with `throw()` |
| Use case | Simple iteration | Complex sequences, async flows |

### Iterator Version (Verbose)

```javascript
function createRangeIterator(start, end) {
  let current = start;
  return {
    next() {
      if (current <= end) {
        return { value: current++, done: false };
      }
      return { done: true };
    }
  };
}
```

### Generator Version (Clean)

```javascript
function* range(start, end) {
  for (let i = start; i <= end; i++) {
    yield i;
  }
}
```

---

## Practical Use Cases

### 1. Lazy Evaluation

Generators compute values on demand, saving memory:

```javascript
function* largeDataset() {
  for (let i = 0; i < 1000000; i++) {
    yield expensiveComputation(i);
  }
}

// Only computes what's needed
for (const item of largeDataset()) {
  if (item > 100) break;
}
```

### 2. Paginated API Results

```javascript
function* paginatedFetch(url) {
  let page = 1;
  let hasMore = true;

  while (hasMore) {
    const response = yield fetch(`${url}?page=${page}`);
    const data = yield response.json();

    yield* data.items;

    hasMore = data.hasMore;
    page++;
  }
}
```

### 3. Unique ID Generation

```javascript
function* uniqueIdGenerator() {
  let id = 0;
  while (true) {
    yield ++id;
  }
}

const ids = uniqueIdGenerator();
const users = [
  { id: ids.next().value, name: "Alice" },
  { id: ids.next().value, name: "Bob" },
  { id: ids.next().value, name: "Charlie" }
];
```

---

## Common Mistakes

### Mistake 1: Forgetting Parentheses

```javascript
function* gen() { yield 1; }

// ❌ Missing parentheses — returns generator function, not generator
for (const val of gen) { ... }

// ✅ Correct — calling the function returns the generator object
for (const val of gen()) { ... }
```

### Mistake 2: Not Calling next() Before Sending Values

```javascript
function* echo() {
  const value = yield "Ready";
  console.log(value);
}

const gen = echo();

// ❌ First next() must have no argument
// gen.next("Hello"); // Would assign "Hello" before any yield!

// ✅ First next() starts the generator
gen.next();        // { value: "Ready", done: false }
gen.next("Hello"); // Logs "Hello"
```

### Mistake 3: Expecting Return Value in for...of

```javascript
function* withReturn() {
  yield 1;
  yield 2;
  return 3; // for...of ignores this!
}

const values = [...withReturn()];
console.log(values); // [1, 2] — 3 is excluded!
```

### Mistake 4: Infinite Generators Without Break

```javascript
function* infinite() {
  let i = 0;
  while (true) yield i++;
}

// ❌ This hangs forever!
// console.log([...infinite()]);

// ✅ Always provide an exit condition
for (const num of infinite()) {
  if (num >= 10) break;
  console.log(num);
}
```

---

## Practice Exercises

### Exercise 1: Reverse Generator

Create a generator that yields array elements in reverse.

```javascript
function* reverse(arr) {
  // Your code
}

console.log([...reverse([1, 2, 3, 4, 5])]); // [5, 4, 3, 2, 1]
```

### Exercise 2: Chunk Generator

Create a generator that yields chunks of an array.

```javascript
function* chunks(arr, size) {
  // Your code
}

console.log([...chunks([1, 2, 3, 4, 5, 6], 2)]);
// [[1, 2], [3, 4], [5, 6]]
```

### Exercise 3: Interleave Generators

Combine two generators by alternating their values.

```javascript
function* interleave(gen1, gen2) {
  // Your code
}

const a = function* () { yield* [1, 3, 5]; };
const b = function* () { yield* [2, 4, 6]; };

console.log([...interleave(a(), b())]);
// [1, 2, 3, 4, 5, 6]
```

### Exercise 4: Prime Generator

Create an infinite generator that yields prime numbers.

```javascript
function* primes() {
  // Your code
}

const primeGen = primes();
for (let i = 0; i < 10; i++) {
  console.log(primeGen.next().value);
}
// 2, 3, 5, 7, 11, 13, 17, 19, 23, 29
```

### Exercise 5: State Machine Generator

Use a generator to implement a simple state machine.

```javascript
function* trafficLight() {
  // States: green → yellow → red → green
  // Yield each state, advance on next()
}

const light = trafficLight();
console.log(light.next().value); // "green"
console.log(light.next().value); // "yellow"
console.log(light.next().value); // "red"
console.log(light.next().value); // "green" (cycles back)
```

---

## Summary

- Generators use `function*` syntax and `yield` to produce sequences
- They **pause** at `yield` and **resume** on `next()`
- Generator objects conform to both **iterator** and **iterable** protocols
- Values can be **sent into** generators via `next(value)`
- `yield*` delegates iteration to another iterable
- `return()` terminates early; `throw()` injects errors
- Generators provide **cleaner syntax** than manual iterators
- Perfect for **lazy evaluation**, **infinite sequences**, and **complex state machines**

---

## Next Steps

Generators connect to:
- **Async Generators** — `async function*` for async iteration
- **Iterators** — the underlying protocol generators use
- **Promises/Async-Await** — combining with async operations

Happy coding! 🚀
