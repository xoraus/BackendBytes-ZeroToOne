# Arrays in JavaScript

## Overview

An **array** is an ordered collection of values. Think of it as a list where you can store multiple items under a single variable name. Arrays are one of the most commonly used data structures in JavaScript and are essential for handling collections of data.

---

## Creating Arrays

### Array Literal (Recommended)

```javascript
const fruits = ["apple", "banana", "cherry"];
const mixed = [1, "hello", true, null, { name: "Alice" }];
const empty = [];
```

### Array Constructor

```javascript
const numbers = new Array(1, 2, 3, 4, 5);
const tenEmptySlots = new Array(10); // [empty × 10]
```

> **Prefer the literal syntax** — it's shorter, clearer, and avoids confusion with single-argument constructor behavior.

---

## Array Basics

### Accessing Elements

Arrays are **zero-indexed**: the first element is at index 0.

```javascript
const colors = ["red", "green", "blue"];

console.log(colors[0]); // "red"
console.log(colors[1]); // "green"
console.log(colors[2]); // "blue"
console.log(colors[3]); // undefined (doesn't exist)
```

### Modifying Elements

```javascript
const scores = [80, 90, 70];
scores[1] = 95; // Change second element

console.log(scores); // [80, 95, 70]
```

### Array Length

```javascript
const items = ["a", "b", "c"];
console.log(items.length); // 3

// Access last element
console.log(items[items.length - 1]); // "c"
```

---

## Adding and Removing Elements

### `push()` — Add to End

```javascript
const arr = [1, 2];
arr.push(3);
console.log(arr); // [1, 2, 3]

arr.push(4, 5);
console.log(arr); // [1, 2, 3, 4, 5]
```

### `pop()` — Remove from End

```javascript
const arr = [1, 2, 3];
const last = arr.pop();

console.log(last);  // 3
console.log(arr);   // [1, 2]
```

### `unshift()` — Add to Beginning

```javascript
const arr = [2, 3];
arr.unshift(1);
console.log(arr); // [1, 2, 3]

arr.unshift(-1, 0);
console.log(arr); // [-1, 0, 1, 2, 3]
```

### `shift()` — Remove from Beginning

```javascript
const arr = [1, 2, 3];
const first = arr.shift();

console.log(first); // 1
console.log(arr);   // [2, 3]
```

### Summary: Stack vs Queue Operations

| Operation | Method | Position | Returns |
|-----------|--------|----------|---------|
| Add | `push()` | End | New length |
| Remove | `pop()` | End | Removed element |
| Add | `unshift()` | Beginning | New length |
| Remove | `shift()` | Beginning | Removed element |

---

## Finding Elements

### `indexOf()` — Find Index

```javascript
const fruits = ["apple", "banana", "cherry", "banana"];

console.log(fruits.indexOf("banana"));      // 1
console.log(fruits.indexOf("grape"));       // -1 (not found)
console.log(fruits.indexOf("banana", 2));   // 3 (start from index 2)
```

### `lastIndexOf()` — Find Last Index

```javascript
console.log(fruits.lastIndexOf("banana")); // 3
```

### `includes()` — Check Existence

```javascript
console.log(fruits.includes("cherry")); // true
console.log(fruits.includes("mango"));  // false
```

### `find()` — Find First Matching Element

```javascript
const users = [
  { id: 1, name: "Alice" },
  { id: 2, name: "Bob" },
  { id: 3, name: "Charlie" }
];

const user = users.find(u => u.id === 2);
console.log(user); // { id: 2, name: "Bob" }
```

### `findIndex()` — Find Index of First Match

```javascript
const index = users.findIndex(u => u.name === "Charlie");
console.log(index); // 2
```

---

## Transforming Arrays

### `map()` — Transform Each Element

Creates a new array by applying a function to each element.

```javascript
const numbers = [1, 2, 3, 4];
const doubled = numbers.map(n => n * 2);

console.log(doubled); // [2, 4, 6, 8]
console.log(numbers); // [1, 2, 3, 4] — original unchanged!
```

### `filter()` — Keep Matching Elements

Creates a new array with elements that pass a test.

```javascript
const numbers = [1, 2, 3, 4, 5, 6];
const evens = numbers.filter(n => n % 2 === 0);

console.log(evens); // [2, 4, 6]
```

### `reduce()` — Reduce to a Single Value

```javascript
const numbers = [1, 2, 3, 4, 5];
const sum = numbers.reduce((acc, curr) => acc + curr, 0);

console.log(sum); // 15

// How it works:
// acc=0, curr=1 → acc=1
// acc=1, curr=2 → acc=3
// acc=3, curr=3 → acc=6
// acc=6, curr=4 → acc=10
// acc=10, curr=5 → acc=15
```

### `reduce()` for More Complex Operations

```javascript
// Find maximum
const nums = [3, 1, 4, 1, 5, 9];
const max = nums.reduce((max, n) => n > max ? n : max, nums[0]);
console.log(max); // 9

// Count occurrences
const words = ["apple", "banana", "apple", "cherry", "banana", "apple"];
const count = words.reduce((acc, word) => {
  acc[word] = (acc[word] || 0) + 1;
  return acc;
}, {});

console.log(count); // { apple: 3, banana: 2, cherry: 1 }
```

---

## Iterating Over Arrays

### `for` Loop

```javascript
const arr = ["a", "b", "c"];
for (let i = 0; i < arr.length; i++) {
  console.log(`${i}: ${arr[i]}`);
}
```

### `for...of` Loop

```javascript
for (const item of arr) {
  console.log(item);
}
```

### `forEach()` Method

```javascript
arr.forEach((item, index) => {
  console.log(`${index}: ${item}`);
});
```

> **Note**: `forEach` cannot be broken out of with `break`. Use `for...of` or regular `for` if you need early exit.

---

## Slicing and Splicing

### `slice()` — Copy a Portion

Returns a new array without modifying the original.

```javascript
const arr = ["a", "b", "c", "d", "e"];

console.log(arr.slice(1, 4));  // ["b", "c", "d"] (index 1 to 3)
console.log(arr.slice(2));     // ["c", "d", "e"] (from index 2)
console.log(arr.slice(-2));    // ["d", "e"] (last 2)
console.log(arr);              // ["a", "b", "c", "d", "e"] — unchanged!
```

### `splice()` — Modify in Place

Adds, removes, or replaces elements.

```javascript
const arr = ["a", "b", "c", "d"];

// Remove 2 elements starting at index 1
const removed = arr.splice(1, 2);
console.log(removed); // ["b", "c"]
console.log(arr);     // ["a", "d"]

// Insert elements
arr.splice(1, 0, "x", "y");
console.log(arr); // ["a", "x", "y", "d"]

// Replace elements
arr.splice(1, 2, "z");
console.log(arr); // ["a", "z", "d"]
```

---

## Sorting and Reversing

### `sort()` — Sort Elements

⚠️ **Warning**: `sort()` converts elements to strings by default!

```javascript
const nums = [10, 2, 30, 1];
nums.sort();
console.log(nums); // [1, 10, 2, 30] — NOT numerical order!

// ✅ Correct: provide a compare function
nums.sort((a, b) => a - b);
console.log(nums); // [1, 2, 10, 30]

// Descending order
nums.sort((a, b) => b - a);
console.log(nums); // [30, 10, 2, 1]
```

### Sorting Strings

```javascript
const words = ["banana", "Apple", "cherry"];
words.sort((a, b) => a.localeCompare(b));
console.log(words); // ["Apple", "banana", "cherry"]
```

### `reverse()` — Reverse Order

```javascript
const arr = [1, 2, 3];
arr.reverse();
console.log(arr); // [3, 2, 1]
```

---

## Joining and Splitting

### `join()` — Array to String

```javascript
const words = ["Hello", "world"];
console.log(words.join(" "));  // "Hello world"
console.log(words.join(", ")); // "Hello, world"
console.log(words.join(""));    // "Helloworld"
```

### `split()` — String to Array

```javascript
const sentence = "The quick brown fox";
const words = sentence.split(" ");
console.log(words); // ["The", "quick", "brown", "fox"]

const chars = "hello".split("");
console.log(chars); // ["h", "e", "l", "l", "o"]
```

---

## Other Useful Methods

### `concat()` — Combine Arrays

```javascript
const arr1 = [1, 2];
const arr2 = [3, 4];
const combined = arr1.concat(arr2);

console.log(combined); // [1, 2, 3, 4]
console.log(arr1);     // [1, 2] — unchanged!
```

### `flat()` — Flatten Nested Arrays

```javascript
const nested = [1, [2, 3], [4, [5, 6]]];
console.log(nested.flat());     // [1, 2, 3, 4, [5, 6]]
console.log(nested.flat(2));    // [1, 2, 3, 4, 5, 6]
```

### `fill()` — Fill with Value

```javascript
const arr = new Array(5).fill(0);
console.log(arr); // [0, 0, 0, 0, 0]

arr.fill("x", 1, 3);
console.log(arr); // [0, "x", "x", 0, 0]
```

### `every()` — All Match?

```javascript
const nums = [2, 4, 6, 8];
console.log(nums.every(n => n % 2 === 0)); // true
```

### `some()` — Any Match?

```javascript
const nums = [1, 3, 5, 8];
console.log(nums.some(n => n % 2 === 0)); // true
```

---

## Multidimensional Arrays

Arrays containing other arrays:

```javascript
const matrix = [
  [1, 2, 3],
  [4, 5, 6],
  [7, 8, 9]
];

console.log(matrix[0][0]); // 1
console.log(matrix[1][2]); // 6
console.log(matrix[2][1]); // 8

// Iterate through 2D array
for (let i = 0; i < matrix.length; i++) {
  for (let j = 0; j < matrix[i].length; j++) {
    console.log(matrix[i][j]);
  }
}
```

---

## Array Destructuring

Extract values into variables:

```javascript
const [first, second, third] = ["apple", "banana", "cherry"];
console.log(first);  // "apple"
console.log(second); // "banana"

// Skip elements
const [a, , c] = [1, 2, 3];
console.log(c); // 3

// Rest pattern
const [head, ...tail] = [1, 2, 3, 4];
console.log(head); // 1
console.log(tail); // [2, 3, 4]

// Swap variables
let x = 5, y = 10;
[x, y] = [y, x];
console.log(x, y); // 10, 5
```

---

## Spread Operator with Arrays

```javascript
const arr1 = [1, 2, 3];
const arr2 = [4, 5, 6];

// Copy array
const copy = [...arr1];

// Combine arrays
const combined = [...arr1, ...arr2];

// Add elements
const extended = [0, ...arr1, 4];

console.log(extended); // [0, 1, 2, 3, 4]
```

---

## Common Mistakes

### Mistake 1: Using `const` Incorrectly

```javascript
// ✅ const prevents reassignment of the variable, not mutation
const arr = [1, 2, 3];
arr.push(4);        // ✅ Works!
arr[0] = 10;        // ✅ Works!
// arr = [5, 6];    // ❌ Error!
```

### Mistake 2: Reference vs Copy

```javascript
const original = [1, 2, 3];
const reference = original; // Same array!
const copy = [...original]; // New array

reference.push(4);
console.log(original); // [1, 2, 3, 4] — modified!
console.log(copy);     // [1, 2, 3] — unchanged
```

### Mistake 3: Modifying Array While Iterating

```javascript
const nums = [1, 2, 3, 4, 5];

// ❌ Skips elements, causes bugs
for (let i = 0; i < nums.length; i++) {
  if (nums[i] % 2 === 0) {
    nums.splice(i, 1);
  }
}

// ✅ Filter creates new array
const filtered = nums.filter(n => n % 2 !== 0);
```

### Mistake 4: Default Sort Behavior

```javascript
const nums = [10, 2, 5];
nums.sort();
console.log(nums); // [10, 2, 5] — string sort!

// ✅ Always provide compare function for numbers
nums.sort((a, b) => a - b);
```

---

## Practice Exercises

### Exercise 1: Array Reversal

Write a function that reverses an array **without** using `.reverse()`.

```javascript
function reverseArray(arr) {
  // Return a new reversed array
}

console.log(reverseArray([1, 2, 3, 4])); // [4, 3, 2, 1]
```

### Exercise 2: Remove Duplicates

Write a function that removes duplicate values from an array.

```javascript
function removeDuplicates(arr) {
  // Return array with unique values only
}

console.log(removeDuplicates([1, 2, 2, 3, 3, 3])); // [1, 2, 3]
```

### Exercise 3: Flatten Array

Write a function that flattens a nested array one level deep.

```javascript
function flatten(arr) {
  // Flatten one level
}

console.log(flatten([1, [2, 3], [4, [5]]])); // [1, 2, 3, 4, [5]]
```

### Exercise 4: Chunk Array

Split an array into chunks of a given size.

```javascript
function chunk(arr, size) {
  // Return array of chunks
}

console.log(chunk([1, 2, 3, 4, 5, 6], 2)); // [[1, 2], [3, 4], [5, 6]]
```

### Exercise 5: Array Rotation

Rotate array elements to the right by a given number of positions.

```javascript
function rotateRight(arr, k) {
  // Rotate right by k positions
}

console.log(rotateRight([1, 2, 3, 4, 5], 2)); // [4, 5, 1, 2, 3]
```

---

## Summary

- Arrays store ordered collections of values
- Access elements with **zero-based** indexing: `arr[0]`
- `push()`/`pop()` work at the end; `unshift()`/`shift()` work at the beginning
- `map()`, `filter()`, `reduce()` are powerful transformation methods
- `find()`, `findIndex()`, `includes()` help locate elements
- `slice()` copies; `splice()` modifies
- `sort()` needs a compare function for numbers
- Use the **spread operator** `...` to copy and combine arrays
- Be careful with **references** — arrays are objects!

---

## Next Steps

Now that you understand arrays, explore:
- **Objects** — key-value data structures
- **Higher-Order Functions** — advanced array methods
- **Problem Solving** — applying arrays to real problems

Happy coding! 🚀
