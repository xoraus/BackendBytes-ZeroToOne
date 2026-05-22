# Miscellaneous Fundamentals in JavaScript

## Overview

Beyond variables, operators, conditionals, loops, and functions, JavaScript has several fundamental concepts that every developer should understand early. This tutorial covers essential topics like template literals, type conversion, the Math object, the Date object, and basic error handling — tools you'll use daily in your programming journey.

---

## Template Literals (ES6+)

Template literals provide a more powerful and readable way to work with strings using backticks (`` ` ``).

### Basic Interpolation

```javascript
const name = "Alice";
const age = 25;

// Old way (string concatenation)
const oldGreeting = "Hello, " + name + ". You are " + age + " years old.";

// Template literal
const newGreeting = `Hello, ${name}. You are ${age} years old.`;

console.log(newGreeting);
// "Hello, Alice. You are 25 years old."
```

### Expression Evaluation

```javascript
const a = 10;
const b = 20;

console.log(`The sum is: ${a + b}`);        // "The sum is: 30"
console.log(`Is a > b? ${a > b}`);          // "Is a > b? false"
console.log(`Double: ${a * 2}`);            // "Double: 20"

// Calling functions inside templates
function capitalize(str) {
  return str.charAt(0).toUpperCase() + str.slice(1);
}

console.log(`${capitalize("hello")} World!`); // "Hello World!"
```

### Multi-line Strings

```javascript
// Old way (using \n or string concatenation)
const oldPoem = "Roses are red,\nViolets are blue.";

// Template literal — natural line breaks
const newPoem = `
Roses are red,
Violets are blue,
JavaScript is awesome,
And so are you!
`;

console.log(newPoem);
```

### Tagged Template Literals (Advanced)

```javascript
function highlight(strings, ...values) {
  return strings.reduce((result, str, i) => {
    const value = values[i] ? `**${values[i]}**` : "";
    return result + str + value;
  }, "");
}

const name = "Alice";
const score = 95;

console.log(highlight`Player ${name} scored ${score} points!`);
// "Player **Alice** scored **95** points!"
```

---

## Type Conversion and Coercion

JavaScript is a dynamically typed language, which means variables can hold values of any type, and types can change automatically.

### Explicit Conversion (Manual)

#### To String

```javascript
String(42);          // "42"
String(true);        // "true"
String(null);        // "null"
String(undefined);   // "undefined"

// Alternative
(42).toString();     // "42"
```

#### To Number

```javascript
Number("42");        // 42
Number("3.14");      // 3.14
Number("");          // 0
Number("hello");     // NaN
Number(true);        // 1
Number(false);       // 0
Number(null);        // 0
Number(undefined);   // NaN

// Alternatives
parseInt("42px");    // 42 (stops at non-digit)
parseInt("1010", 2); // 10 (binary to decimal)
parseFloat("3.14");  // 3.14
+"42";               // 42 (unary plus)
```

#### To Boolean

```javascript
Boolean(1);          // true
Boolean(0);          // false
Boolean("hello");    // true
Boolean("");         // false
Boolean(null);       // false
Boolean(undefined);  // false
Boolean([]);         // true (empty array is truthy!)
Boolean({});         // true (empty object is truthy!)

// Double NOT shortcut
!!"hello";           // true
!!0;                 // false
```

### Implicit Coercion (Automatic)

JavaScript sometimes converts types automatically, which can lead to surprising behavior.

```javascript
// String + anything = string
"5" + 3;             // "53"
"5" + true;          // "5true"

// Number - string = number (if possible)
"5" - 3;             // 2
"10" * "2";          // 20
"10" / "2";          // 5

// Comparisons
"5" == 5;            // true (loose equality with coercion)
"5" === 5;           // false (strict equality, no coercion)

// Logical operators
"hello" && 42;       // 42 (returns last truthy value)
0 || "default";      // "default" (returns first truthy value)
null ?? "fallback";  // "fallback" (nullish coalescing)
```

### Truthy and Falsy Values

**Falsy values** (coerce to `false`):
- `false`
- `0`, `-0`, `0n`
- `""` (empty string)
- `null`
- `undefined`
- `NaN`

**Everything else is truthy**, including:
- `"0"` (string with zero)
- `"false"` (string with text)
- `[]` (empty array)
- `{}` (empty object)
- `-1` (negative numbers)

---

## The `Math` Object

JavaScript's built-in mathematical constants and functions.

### Constants

```javascript
Math.PI;           // 3.141592653589793
Math.E;            // 2.718281828459045 (Euler's number)
Math.LN2;          // 0.6931471805599453 (natural log of 2)
Math.LN10;         // 2.302585092994046 (natural log of 10)
Math.SQRT2;        // 1.4142135623730951 (square root of 2)
```

### Rounding Methods

```javascript
Math.round(3.7);   // 4 (round to nearest integer)
Math.round(3.2);   // 3

Math.ceil(3.1);    // 4 (round up)
Math.floor(3.9);   // 3 (round down)

Math.trunc(3.9);   // 3 (remove decimal part)
Math.trunc(-3.9);  // -3
```

### Min and Max

```javascript
Math.min(10, 5, 8, 3);  // 3
Math.max(10, 5, 8, 3);  // 10

// With arrays (using spread)
const nums = [10, 5, 8, 3];
Math.min(...nums);      // 3
Math.max(...nums);      // 10
```

### Random Numbers

```javascript
Math.random();          // Random decimal between 0 (inclusive) and 1 (exclusive)

// Random integer between min and max (inclusive)
function getRandomInt(min, max) {
  return Math.floor(Math.random() * (max - min + 1)) + min;
}

console.log(getRandomInt(1, 6));   // Dice roll: 1 to 6
console.log(getRandomInt(0, 100)); // Random percentage
```

### Other Useful Methods

```javascript
Math.abs(-5);        // 5 (absolute value)
Math.pow(2, 3);      // 8 (2^3)
Math.sqrt(16);       // 4 (square root)
Math.cbrt(27);       // 3 (cube root)

// Trigonometry
Math.sin(Math.PI / 2);   // 1
Math.cos(0);             // 1
Math.tan(Math.PI / 4);   // ~1

// Logarithms
Math.log(Math.E);        // 1 (natural log)
Math.log10(100);         // 2 (base-10 log)
Math.log2(8);            // 3 (base-2 log)

// Sign
Math.sign(-5);           // -1
Math.sign(5);            // 1
Math.sign(0);            // 0
```

---

## The `Date` Object

Working with dates and times in JavaScript.

### Creating Dates

```javascript
// Current date and time
const now = new Date();

// From a string
const birthday = new Date("1998-05-15");
const alsoBirthday = new Date("May 15, 1998");

// From components (year, monthIndex, day, hour, minute, second)
// Month is 0-indexed! January = 0, December = 11
const specific = new Date(2024, 0, 1, 12, 0, 0);
// January 1, 2024, 12:00:00

// From timestamp (milliseconds since Jan 1, 1970)
const fromTimestamp = new Date(1704067200000);
```

### Getting Date Components

```javascript
const date = new Date("2024-03-15T10:30:00");

date.getFullYear();    // 2024
date.getMonth();       // 2 (March — 0-indexed!)
date.getDate();        // 15 (day of month)
date.getDay();         // 5 (Friday — 0=Sunday)
date.getHours();       // 10
date.getMinutes();     // 30
date.getSeconds();     // 0
date.getMilliseconds(); // 0

// UTC versions
date.getUTCFullYear();
date.getUTCMonth();
// ... etc
```

### Setting Date Components

```javascript
const date = new Date();

date.setFullYear(2025);
date.setMonth(11);      // December
date.setDate(25);       // Christmas
date.setHours(0, 0, 0, 0); // Midnight
```

### Useful Date Operations

```javascript
const now = new Date();
const future = new Date("2025-12-31");

// Difference in milliseconds
const diff = future - now;

// Convert to days
const daysLeft = Math.ceil(diff / (1000 * 60 * 60 * 24));
console.log(`${daysLeft} days remaining!`);

// Formatting dates
function formatDate(date) {
  const year = date.getFullYear();
  const month = String(date.getMonth() + 1).padStart(2, "0");
  const day = String(date.getDate()).padStart(2, "0");
  return `${year}-${month}-${day}`;
}

console.log(formatDate(new Date())); // "2024-03-15"
```

### Date.now() — Current Timestamp

```javascript
const timestamp = Date.now(); // Milliseconds since epoch

// Measuring execution time
const start = Date.now();
// ... some operation
const end = Date.now();
console.log(`Took ${end - start}ms`);
```

---

## Error Handling with try...catch

### Basic Syntax

```javascript
try {
  // Code that might throw an error
  const result = riskyOperation();
  console.log(result);
} catch (error) {
  // Handle the error
  console.log("Something went wrong:", error.message);
}
```

### Practical Example

```javascript
function parseJSON(jsonString) {
  try {
    const data = JSON.parse(jsonString);
    return data;
  } catch (error) {
    console.error("Invalid JSON:", error.message);
    return null;
  }
}

console.log(parseJSON('{"name":"Alice"}')); // { name: "Alice" }
console.log(parseJSON("not json"));           // null (no crash!)
```

### finally Block

The `finally` block always runs, whether an error occurred or not:

```javascript
try {
  console.log("Trying...");
  throw new Error("Oops!");
} catch (error) {
  console.log("Caught:", error.message);
} finally {
  console.log("Cleanup code always runs");
}

// Output:
// Trying...
// Caught: Oops!
// Cleanup code always runs
```

### Throwing Custom Errors

```javascript
function divide(a, b) {
  if (b === 0) {
    throw new Error("Cannot divide by zero!");
  }
  if (typeof a !== "number" || typeof b !== "number") {
    throw new TypeError("Both arguments must be numbers");
  }
  return a / b;
}

try {
  console.log(divide(10, 0));
} catch (error) {
  console.log(error.name);    // "Error"
  console.log(error.message); // "Cannot divide by zero!"
}
```

---

## The `typeof` Operator

```javascript
typeof "hello";      // "string"
typeof 42;           // "number"
typeof true;         // "boolean"
typeof undefined;    // "undefined"
typeof Symbol();     // "symbol"
typeof 123n;         // "bigint"
typeof {};           // "object"
typeof [];           // "object" (arrays are objects!)
typeof null;         // "object" (historical bug)
typeof function(){}; // "function"
```

### Checking for Arrays

```javascript
const arr = [1, 2, 3];

console.log(typeof arr);           // "object" — not helpful!
console.log(Array.isArray(arr));   // true — correct way
console.log(Array.isArray("abc")); // false
```

### Checking for Null

```javascript
const value = null;

console.log(typeof value);         // "object" — bug!
console.log(value === null);       // true — correct way
```

---

## The `JSON` Object

JSON (JavaScript Object Notation) is a lightweight data format used for data exchange.

### JSON.stringify() — Object to String

```javascript
const user = {
  name: "Alice",
  age: 25,
  hobbies: ["reading", "coding"]
};

const jsonString = JSON.stringify(user);
console.log(jsonString);
// '{"name":"Alice","age":25,"hobbies":["reading","coding"]}'

// Pretty print
const pretty = JSON.stringify(user, null, 2);
console.log(pretty);
// {
//   "name": "Alice",
//   "age": 25,
//   "hobbies": [
//     "reading",
//     "coding"
//   ]
// }
```

### JSON.parse() — String to Object

```javascript
const jsonString = '{"name":"Bob","score":100}';
const data = JSON.parse(jsonString);

console.log(data.name);  // "Bob"
console.log(data.score); // 100
```

---

## `setTimeout` and `setInterval`

### setTimeout — Run Once After Delay

```javascript
console.log("Start");

setTimeout(() => {
  console.log("This runs after 2 seconds");
}, 2000);

console.log("End");

// Output:
// Start
// End
// This runs after 2 seconds (after 2 seconds)
```

### clearTimeout — Cancel Scheduled Execution

```javascript
const timeoutId = setTimeout(() => {
  console.log("This won't run");
}, 5000);

clearTimeout(timeoutId);
```

### setInterval — Run Repeatedly

```javascript
let count = 0;

const intervalId = setInterval(() => {
  count++;
  console.log(`Tick ${count}`);

  if (count >= 5) {
    clearInterval(intervalId);
    console.log("Stopped!");
  }
}, 1000);
```

---

## Practice Exercises

### Exercise 1: Random Password Generator

Generate a random password with letters, numbers, and symbols.

```javascript
function generatePassword(length) {
  // Return a random password string
}

console.log(generatePassword(12));
```

### Exercise 2: Days Until Birthday

Calculate how many days until the next birthday.

```javascript
function daysUntilBirthday(birthDate) {
  // Return number of days
}

console.log(daysUntilBirthday("1998-05-15"));
```

### Exercise 3: Safe Division

Write a function that divides two numbers safely using try...catch.

```javascript
function safeDivide(a, b) {
  // Return result or error message
}

console.log(safeDivide(10, 2));  // 5
console.log(safeDivide(10, 0));  // "Error: Cannot divide by zero"
console.log(safeDivide("10", 2)); // "Error: Invalid input"
```

### Exercise 4: Number Formatter

Format a number with commas as thousands separators.

```javascript
function formatNumber(num) {
  // Return "1,234,567"
}

console.log(formatNumber(1234567)); // "1,234,567"
```

### Exercise 5: Deep Clone (JSON Method)

Create a deep copy of an object using JSON methods.

```javascript
function deepClone(obj) {
  // Return a deep copy
}

const original = { a: 1, b: { c: 2 } };
const copy = deepClone(original);
copy.b.c = 999;
console.log(original.b.c); // Should still be 2
```

---

## Summary

- **Template literals** use backticks for interpolation and multi-line strings
- **Type coercion** happens automatically; use `===` to avoid surprises
- **Truthy/falsy** values determine boolean context behavior
- **`Math`** provides constants and functions for mathematical operations
- **`Date`** handles dates and times (months are 0-indexed!)
- **`try...catch`** prevents crashes by handling errors gracefully
- **`typeof`** checks types but beware of `null` being `"object"`
- **`JSON.stringify/parse`** convert between objects and JSON strings
- **`setTimeout/setInterval`** schedule delayed or repeated execution

---

## Next Steps

These fundamentals pair well with:
- **Objects** — deeper data structures
- **Arrays** — collections and methods
- **Problem Solving** — applying these tools to real challenges

Happy coding! 🚀
