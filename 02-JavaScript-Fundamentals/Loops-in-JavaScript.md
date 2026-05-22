# Loops in JavaScript

## Overview

Loops are one of the most powerful tools in programming. They allow you to execute a block of code **repeatedly** without writing it multiple times. Whether you need to process a list of items, wait for a condition, or repeat an action a specific number of times, loops make it possible.

---

## Why Use Loops?

Imagine you need to print numbers 1 to 100:

```javascript
// ❌ Without loops — tedious and error-prone
console.log(1);
console.log(2);
console.log(3);
// ... 97 more lines!

// ✅ With a loop — clean and scalable
for (let i = 1; i <= 100; i++) {
  console.log(i);
}
```

---

## Types of Loops in JavaScript

JavaScript provides several ways to loop:

1. **`for` loop** — loop with initialization, condition, and increment
2. **`while` loop** — loop while a condition is true
3. **`do...while` loop** — loop at least once, then check condition
4. **`for...of` loop** — iterate over iterable values (arrays, strings, etc.)
5. **`for...in` loop** — iterate over object keys

---

## 1. The `for` Loop

The most common loop. Perfect when you know **how many times** to iterate.

### Syntax

```javascript
for (initialization; condition; increment) {
  // code to repeat
}
```

### How It Works

```javascript
for (let i = 0; i < 5; i++) {
  console.log(i);
}
// Output: 0, 1, 2, 3, 4
```

**Step by step:**
1. `let i = 0` — Initialize counter
2. `i < 5` — Check condition (true? continue; false? exit)
3. `console.log(i)` — Execute loop body
4. `i++` — Increment counter
5. Go back to step 2

### Examples

```javascript
// Count from 1 to 10
for (let i = 1; i <= 10; i++) {
  console.log(i);
}

// Countdown from 10 to 1
for (let i = 10; i >= 1; i--) {
  console.log(i);
}

// Count by 2s
for (let i = 0; i <= 10; i += 2) {
  console.log(i); // 0, 2, 4, 6, 8, 10
}

// Skip every other number with custom increment
for (let i = 1; i <= 20; i += 3) {
  console.log(i); // 1, 4, 7, 10, 13, 16, 19
}
```

### Nested `for` Loops

```javascript
for (let i = 1; i <= 3; i++) {
  for (let j = 1; j <= 3; j++) {
    console.log(`i=${i}, j=${j}`);
  }
}

// Output:
// i=1, j=1
// i=1, j=2
// i=1, j=3
// i=2, j=1
// ... and so on
```

---

## 2. The `while` Loop

Repeats while a condition is `true`. Use when you **don't know** how many iterations you need.

### Syntax

```javascript
while (condition) {
  // code to repeat
}
```

### Examples

```javascript
// Count to 5
let count = 1;
while (count <= 5) {
  console.log(count);
  count++;
}

// User input simulation (loop until valid input)
let password = "";
while (password !== "secret") {
  password = "secret"; // In real code, this would be user input
}
console.log("Access granted!");

// Find first number divisible by 7 and 13
let num = 1;
while (true) {
  if (num % 7 === 0 && num % 13 === 0) {
    console.log(num); // 91
    break;
  }
  num++;
}
```

### Danger: Infinite Loops

```javascript
// ❌ This will run forever!
while (true) {
  console.log("Help! I'm stuck!");
}

// ❌ Counter never changes
let i = 0;
while (i < 10) {
  console.log(i);
  // Forgot to increment i!
}
```

> **Always ensure your loop has a way to terminate!**

---

## 3. The `do...while` Loop

Similar to `while`, but the body executes **at least once** before checking the condition.

### Syntax

```javascript
do {
  // code to repeat
} while (condition);
```

### Example

```javascript
let i = 1;
do {
  console.log(i);
  i++;
} while (i <= 5);
// Output: 1, 2, 3, 4, 5
```

### When `do...while` Is Different

```javascript
let num = 10;

// while loop — body never executes
while (num < 5) {
  console.log("while: " + num);
}

// do...while loop — body executes once
do {
  console.log("do...while: " + num);
} while (num < 5);
// Output: "do...while: 10"
```

> Use `do...when` when you need the code to run at least once (e.g., menu systems).

---

## 4. The `for...of` Loop

Iterates over **values** of an iterable (arrays, strings, Maps, Sets, etc.). Introduced in ES6.

### Syntax

```javascript
for (const item of iterable) {
  // use item
}
```

### With Arrays

```javascript
const fruits = ["apple", "banana", "cherry"];

for (const fruit of fruits) {
  console.log(fruit);
}
// apple
// banana
// cherry
```

### With Strings

```javascript
const word = "Hello";

for (const char of word) {
  console.log(char);
}
// H, e, l, l, o
```

### With Entries (Index + Value)

```javascript
const colors = ["red", "green", "blue"];

for (const [index, color] of colors.entries()) {
  console.log(`${index}: ${color}`);
}
// 0: red
// 1: green
// 2: blue
```

---

## 5. The `for...in` Loop

Iterates over **keys** (property names) of an object. **Do not use with arrays** — use `for...of` or regular `for` instead.

### Syntax

```javascript
for (const key in object) {
  // use key and object[key]
}
```

### Example

```javascript
const person = {
  name: "Alice",
  age: 25,
  city: "New York"
};

for (const key in person) {
  console.log(`${key}: ${person[key]}`);
}
// name: Alice
// age: 25
// city: New York
```

### Why Not Use `for...in` with Arrays?

```javascript
const arr = ["a", "b", "c"];

// It works, but...
for (const index in arr) {
  console.log(index); // "0", "1", "2" — strings, not numbers!
}

// It also iterates over custom properties
arr.customProp = "oops";
for (const index in arr) {
  console.log(index); // "0", "1", "2", "customProp" — unexpected!
}
```

> **Rule of thumb**: `for...in` for objects, `for...of` for arrays.

---

## Loop Control Statements

### `break` — Exit the Loop

Immediately terminates the loop:

```javascript
for (let i = 1; i <= 100; i++) {
  if (i === 5) {
    break; // Exit the loop completely
  }
  console.log(i); // 1, 2, 3, 4
}
```

### `continue` — Skip to Next Iteration

Skips the rest of the current iteration and moves to the next:

```javascript
for (let i = 1; i <= 10; i++) {
  if (i % 2 === 0) {
    continue; // Skip even numbers
  }
  console.log(i); // 1, 3, 5, 7, 9
}
```

### Labels (Advanced)

Label loops to `break` or `continue` an outer loop from an inner one:

```javascript
outerLoop: for (let i = 1; i <= 3; i++) {
  for (let j = 1; j <= 3; j++) {
    if (i === 2 && j === 2) {
      break outerLoop; // Breaks the outer loop, not just inner
    }
    console.log(`i=${i}, j=${j}`);
  }
}
```

---

## Loop Comparison

| Loop | Best For | Know Iterations? | Access |
|------|----------|------------------|--------|
| `for` | Counting, indexing | Yes | Index-based |
| `while` | Unknown iterations | No | Manual control |
| `do...while` | At least once | No | Manual control |
| `for...of` | Array/string values | Yes | Direct values |
| `for...in` | Object properties | Yes | Keys only |

---

## Common Patterns

### Sum an Array

```javascript
const numbers = [10, 20, 30, 40, 50];
let sum = 0;

for (const num of numbers) {
  sum += num;
}

console.log(sum); // 150
```

### Find Maximum

```javascript
const scores = [45, 82, 67, 91, 55];
let max = scores[0];

for (let i = 1; i < scores.length; i++) {
  if (scores[i] > max) {
    max = scores[i];
  }
}

console.log(max); // 91
```

### Reverse an Array

```javascript
const original = [1, 2, 3, 4, 5];
const reversed = [];

for (let i = original.length - 1; i >= 0; i--) {
  reversed.push(original[i]);
}

console.log(reversed); // [5, 4, 3, 2, 1]
```

### Filter Even Numbers

```javascript
const numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10];
const evens = [];

for (const num of numbers) {
  if (num % 2 === 0) {
    evens.push(num);
  }
}

console.log(evens); // [2, 4, 6, 8, 10]
```

---

## Common Mistakes

### Mistake 1: Off-by-One Errors

```javascript
// ❌ Prints undefined for the last element
const arr = ["a", "b", "c"];
for (let i = 0; i <= arr.length; i++) {
  console.log(arr[i]); // a, b, c, undefined
}

// ✅ Correct: use < not <=
for (let i = 0; i < arr.length; i++) {
  console.log(arr[i]); // a, b, c
}
```

### Mistake 2: Modifying Array While Iterating

```javascript
// ❌ Skips elements!
const nums = [1, 2, 3, 4, 5];
for (let i = 0; i < nums.length; i++) {
  if (nums[i] % 2 === 0) {
    nums.splice(i, 1); // Removes element, shifts others!
  }
}
console.log(nums); // [1, 3, 5] — but wait, did we check all?

// ✅ Better: iterate backwards
for (let i = nums.length - 1; i >= 0; i--) {
  if (nums[i] % 2 === 0) {
    nums.splice(i, 1);
  }
}
```

### Mistake 3: `const` in `for` Loop Header

```javascript
// ❌ Error: can't reassign const
for (const i = 0; i < 5; i++) {
  console.log(i);
}

// ✅ Use let
for (let i = 0; i < 5; i++) {
  console.log(i);
}

// ✅ But const is fine in for...of
for (const num of [1, 2, 3]) {
  console.log(num);
}
```

### Mistake 4: Infinite Loops with Floats

```javascript
// ❌ Might never equal exactly 1.0 due to floating point
for (let i = 0; i !== 1; i += 0.1) {
  console.log(i); // Runs forever!
}

// ✅ Use < or > for floats
for (let i = 0; i < 1; i += 0.1) {
  console.log(i.toFixed(1));
}
```

---

## Practice Exercises

### Exercise 1: Multiplication Table

Print the multiplication table for a given number:

```javascript
// Input: 5
// Output:
// 5 × 1 = 5
// 5 × 2 = 10
// ...
// 5 × 10 = 50
```

### Exercise 2: Factorial

Calculate factorial using a loop:

```javascript
function factorial(n) {
  // Return n! using a loop
}

console.log(factorial(5)); // 120
```

### Exercise 3: Prime Numbers

Print all prime numbers between 1 and 100.

### Exercise 4: Pattern Printing

Print this pattern:

```
*
**
***
****
*****
```

Then print:

```
    *
   ***
  *****
 *******
*********
```

### Exercise 5: Array Statistics

Given an array of numbers, calculate:
- Sum
- Average
- Minimum
- Maximum
- Count of even numbers

All using loops (no built-in array methods).

---

## Summary

- **`for`**: Best when you know the number of iterations
- **`while`**: Best when iterations depend on a dynamic condition
- **`do...while`**: Best when the body must run at least once
- **`for...of`**: Best for iterating array/string values
- **`for...in`**: Best for iterating object keys (avoid with arrays)
- **`break`**: Exits a loop immediately
- **`continue`**: Skips to the next iteration
- Always ensure loops have a termination condition

---

## Next Steps

After mastering loops, learn about:
- **Arrays** — storing and manipulating collections
- **Functions** — organizing code into reusable blocks
- **Problem Solving with Loops** — applying loops to real problems

Happy coding! 🚀
