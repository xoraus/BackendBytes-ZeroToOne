# Unary Operators in JavaScript

## Overview

Unary operators are operators that work with only **one operand** (a single value or variable). While binary operators like `+`, `-`, `*`, `/` need two values (e.g., `5 + 3`), unary operators need just one (e.g., `-5`, `++x`). Understanding unary operators is essential for writing concise and efficient JavaScript code.

---

## Table of Unary Operators

| Operator | Name | Description |
|----------|------|-------------|
| `+` | Unary Plus | Converts operand to a number |
| `-` | Unary Minus | Negates the operand |
| `++` | Increment | Increases value by 1 |
| `--` | Decrement | Decreases value by 1 |
| `!` | Logical NOT | Inverts a boolean value |
| `~` | Bitwise NOT | Inverts all bits |
| `typeof` | Typeof | Returns the data type |
| `void` | Void | Evaluates expression, returns `undefined` |
| `delete` | Delete | Removes a property from an object |

---

## 1. Unary Plus (`+`)

Converts its operand into a number. It's a quick way to coerce a value to a number type.

```javascript
console.log(+"5");       // 5 (string → number)
console.log(+true);      // 1 (boolean → number)
console.log(+false);     // 0 (boolean → number)
console.log(+null);      // 0 (null → number)
console.log(+"");        // 0 (empty string → number)
console.log(+"hello");   // NaN (non-numeric string)
console.log(+undefined); // NaN

// Practical use: converting an array of strings to numbers
let strNumbers = ["1", "2", "3"];
let numbers = strNumbers.map(Number); // [1, 2, 3]
// Or equivalently:
let numbers2 = strNumbers.map(n => +n); // [1, 2, 3]
```

### Unary Plus vs Number()

```javascript
console.log(+"  42  ");       // 42 (trims whitespace)
console.log(Number("  42  ")); // 42

console.log(+"");             // 0
console.log(Number(""));      // 0

console.log(+null);           // 0
console.log(Number(null));    // 0
```

---

## 2. Unary Minus (`-`)

Negates a number. If the operand is not a number, it first converts it to a number, then negates it.

```javascript
console.log(-5);         // -5
console.log(-(-5));      // 5 (double negation)
console.log(-"10");      // -10 (string → number, then negated)
console.log(-true);      // -1
console.log(-false);     // -0
console.log(-"hello");   // NaN
```

### Practical Example

```javascript
let temperature = 25;
console.log(-temperature); // -25

// Converting positive to negative and vice versa
let balance = -100;
console.log(-balance); // 100
```

---

## 3. Increment (`++`)

Increases a variable's value by 1. This operator can be used in two positions:

### Pre-Increment (`++x`)
- Increments the value **first**, then returns the new value

```javascript
let x = 5;
console.log(++x); // 6 (x is incremented, then logged)
console.log(x);   // 6
```

### Post-Increment (`x++`)
- Returns the **current value first**, then increments

```javascript
let y = 5;
console.log(y++); // 5 (current value is logged first)
console.log(y);   // 6 (then y is incremented)
```

### Visual Comparison

```javascript
let a = 10;
let b = 10;

let result1 = ++a; // a becomes 11, result1 = 11
let result2 = b++; // result2 = 10, b becomes 11

console.log(a, result1); // 11, 11
console.log(b, result2); // 11, 10
```

### Common Use Case: Loops

```javascript
for (let i = 0; i < 5; i++) {
  console.log(i); // 0, 1, 2, 3, 4
}
```

### Pitfall: Using in Expressions

```javascript
let count = 0;
let result = count++ + ++count;

// Step by step:
// count++ returns 0, count becomes 1
// ++count increments count to 2, returns 2
// result = 0 + 2 = 2
console.log(result); // 2
console.log(count);  // 2
```

> **Best Practice**: Avoid using `++` inside complex expressions. Use it as a standalone statement for clarity.

---

## 4. Decrement (`--`)

Decreases a variable's value by 1. Like increment, it has two forms:

### Pre-Decrement (`--x`)

```javascript
let x = 5;
console.log(--x); // 4 (decrement first, then use)
console.log(x);   // 4
```

### Post-Decrement (`x--`)

```javascript
let y = 5;
console.log(y--); // 5 (use first, then decrement)
console.log(y);   // 4
```

### Example: Countdown

```javascript
let countdown = 3;

while (countdown > 0) {
  console.log(countdown--); // Prints 3, 2, 1
}
console.log("Blast off! 🚀");
```

---

## 5. Logical NOT (`!`)

Inverts a boolean value. If the value is truthy, it becomes `false`; if falsy, it becomes `true`.

```javascript
console.log(!true);   // false
console.log(!false);  // true

// With non-boolean values (coerced to boolean first)
console.log(!0);          // true (0 is falsy)
console.log(!"");         // true (empty string is falsy)
console.log(!null);       // true (null is falsy)
console.log(!undefined);  // true (undefined is falsy)
console.log(!NaN);        // true (NaN is falsy)

console.log(!"hello");    // false (non-empty string is truthy)
console.log(!42);         // false (non-zero number is truthy)
console.log(![]);         // false (array is truthy)
console.log(!{});         // false (object is truthy)
```

### Double NOT (`!!`) — Convert to Boolean

```javascript
console.log(!!"hello");  // true
console.log(!!0);        // false
console.log(!!"");       // false
console.log(!!null);     // false
console.log(!!1);        // true

// Equivalent to Boolean()
console.log(Boolean("hello")); // true
```

### Practical Use Case

```javascript
let username = "";

if (!username) {
  console.log("Username is required!"); // This runs
}

let isLoggedIn = false;
if (!isLoggedIn) {
  console.log("Please log in first."); // This runs
}
```

---

## 6. Bitwise NOT (`~`)

Inverts all the bits of its operand. For any number `n`, `~n === -(n + 1)`.

```javascript
console.log(~5);   // -6
console.log(~0);   // -1
console.log(~-1);  // 0
console.log(~2);   // -3
```

### Practical Use: Checking Array Index

The `indexOf` method returns `-1` when an element is not found. Using `~` with `-1` gives `0` (falsy), while any other index gives a truthy value:

```javascript
let fruits = ["apple", "banana", "cherry"];

// Old pattern (still seen in codebases)
if (~fruits.indexOf("banana")) {
  console.log("Banana found!");
}

// Modern equivalent (preferred)
if (fruits.includes("banana")) {
  console.log("Banana found!");
}
```

> **Note**: Prefer `.includes()` in modern JavaScript. The `~` trick is mostly for reading legacy code.

---

## 7. typeof Operator

Returns a string indicating the type of the operand.

```javascript
console.log(typeof "Hello");     // "string"
console.log(typeof 42);          // "number"
console.log(typeof true);        // "boolean"
console.log(typeof undefined);   // "undefined"
console.log(typeof 123n);        // "bigint"
console.log(typeof Symbol());    // "symbol"
console.log(typeof {});          // "object"
console.log(typeof []);          // "object" (arrays are objects!)
console.log(typeof null);        // "object" (historical bug)
console.log(typeof function(){}); // "function"
```

### Checking for Arrays

```javascript
let arr = [1, 2, 3];

// typeof won't distinguish arrays from objects
console.log(typeof arr); // "object"

// Use Array.isArray() instead
console.log(Array.isArray(arr)); // true
```

### Checking for Null

```javascript
let value = null;

// typeof null is "object" (bug)
console.log(typeof value === "object"); // true, but so are all objects!

// Proper null check
console.log(value === null); // true
```

---

## 8. void Operator

Evaluates an expression and then returns `undefined`. Rarely used in modern JavaScript.

```javascript
console.log(void 0);       // undefined
console.log(void (1 + 2)); // undefined

// Historically used in links to prevent navigation
// <a href="javascript:void(0)">Click me</a>
```

---

## 9. delete Operator

Removes a property from an object. It does **not** delete variables.

```javascript
let person = {
  name: "Alice",
  age: 25
};

console.log(person.age); // 25
delete person.age;
console.log(person.age); // undefined
console.log(person);     // { name: "Alice" }

// Cannot delete variables
let x = 10;
delete x; // SyntaxError in strict mode, silently fails in non-strict
console.log(x); // 10

// Cannot delete array elements (only removes value, keeps slot)
let arr = [1, 2, 3];
delete arr[1];
console.log(arr);      // [1, empty, 3]
console.log(arr.length); // 3 (length unchanged!)
```

---

## Quick Reference Table

| Operator | Example | Before | After | Returns |
|----------|---------|--------|-------|---------|
| `++x` | `let a=5; b=++a;` | a=5 | a=6 | 6 |
| `x++` | `let a=5; b=a++;` | a=5 | a=6 | 5 |
| `--x` | `let a=5; b=--a;` | a=5 | a=4 | 4 |
| `x--` | `let a=5; b=a--;` | a=5 | a=4 | 5 |
| `+x` | `+"5"` | — | — | 5 |
| `-x` | `-"5"` | — | — | -5 |
| `!x` | `!true` | — | — | false |
| `~x` | `~5` | — | — | -6 |

---

## Common Pitfalls

### Pitfall 1: Post vs Pre Increment Confusion

```javascript
let i = 0;
let arr = [10, 20, 30];

console.log(arr[i++]); // 10 (uses i=0, then increments to 1)
console.log(arr[i]);   // 20 (i is now 1)
console.log(arr[++i]); // 30 (increments to 2, then uses)
```

### Pitfall 2: Increment on Non-Numbers

```javascript
let x = "5";
x++; // JavaScript coerces to number first
console.log(x); // 6 (number!)

let y = "hello";
y++; // NaN
console.log(y); // NaN
```

### Pitfall 3: Chained Increment/Decrement

```javascript
let a = 1;
let b = 1;
let c = a++ + ++b; // 1 + 2 = 3
console.log(a, b, c); // 2, 2, 3
```

---

## Practice Exercises

### Exercise 1: Predict the Output

```javascript
let x = 5;
let y = ++x + x++ + --x;
// What is the value of y? Walk through step by step.
```

### Exercise 2: Boolean Toggle

Use the `!` operator to toggle a boolean variable between `true` and `false`.

```javascript
let isActive = true;
// Toggle isActive using a unary operator
```

### Exercise 3: Coercion Check

What will these print? Explain why.

```javascript
console.log(+"");
console.log(+"   ");
console.log(+"0x10");
console.log(+"10px");
```

### Exercise 4: Fix the Bug

```javascript
let score = "10";
let bonus = 5;
let total = score + bonus; // Expected: 15, Actual: "105"
// Fix this using a unary operator
```

### Exercise 5: Truthy/Falsy Check

Write a function using `!!` that returns `true` if a value is truthy and `false` if falsy.

---

## Summary

- **Unary operators** work on a single operand
- `++` and `--` have **prefix** and **postfix** forms with different behaviors
- `+` and `-` can convert values to numbers
- `!` inverts booleans; `!!` converts any value to boolean
- `typeof` returns the type of a value (but beware of `null` being `"object"`)
- `delete` removes object properties, not variables
- Prefer simple, standalone use of `++`/`--` over complex expressions

---

## Next Steps

Now that you understand unary operators, you're ready to explore:
- Conditional statements (if/else, switch)
- Loops and iteration
- Functions and scope

Happy coding! 🚀
