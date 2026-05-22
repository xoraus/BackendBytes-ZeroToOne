# Functions in JavaScript

## Overview

Functions are the building blocks of any JavaScript program. They allow you to wrap a piece of code into a reusable block, give it a name, and execute it whenever you need it. Functions help organize code, reduce repetition, and make programs easier to understand and maintain.

---

## What is a Function?

A function is a set of instructions that performs a specific task. Think of it like a recipe — you write it once, and you can use it many times.

```javascript
function greet() {
  console.log("Hello, World! 👋");
}

// Calling (invoking) the function
greet(); // "Hello, World! 👋"
greet(); // "Hello, World! 👋"
```

---

## Declaring Functions

### 1. Function Declaration

The most common way to define a function. Function declarations are **hoisted** (can be called before they're defined in the code).

```javascript
function sayHello() {
  console.log("Hello!");
}

sayHello(); // "Hello!"
```

### 2. Function Expression

Assigning a function to a variable. These are **not hoisted**.

```javascript
const sayGoodbye = function() {
  console.log("Goodbye!");
};

sayGoodbye(); // "Goodbye!"
```

Functions stored in variables are called **anonymous functions** when they don't have a name after the `function` keyword.

### 3. Arrow Function (ES6+)

A shorter, modern syntax for writing functions.

```javascript
const add = (a, b) => {
  return a + b;
};

console.log(add(3, 5)); // 8
```

#### Arrow Function Shorthand

```javascript
// If the body is a single expression, you can omit {} and return
const multiply = (a, b) => a * b;
console.log(multiply(4, 5)); // 20

// Single parameter: parentheses are optional
const square = x => x * x;
console.log(square(5)); // 25

// No parameters: parentheses are required
const getRandom = () => Math.random();
```

### Comparing the Three Styles

```javascript
// Function Declaration
function add1(a, b) {
  return a + b;
}

// Function Expression
const add2 = function(a, b) {
  return a + b;
};

// Arrow Function
const add3 = (a, b) => a + b;
```

> All three produce the same result. Choose based on context and team conventions.

---

## Function Parameters and Arguments

### Parameters vs Arguments

- **Parameters** are the variables listed in the function definition
- **Arguments** are the actual values passed when calling the function

```javascript
function greet(name) {        // 'name' is a parameter
  console.log("Hello, " + name);
}

greet("Alice");               // "Alice" is an argument
greet("Bob");                 // "Bob" is an argument
```

### Multiple Parameters

```javascript
function introduce(firstName, lastName, age) {
  console.log(`Hi, I'm ${firstName} ${lastName} and I'm ${age} years old.`);
}

introduce("Alice", "Smith", 25);
// "Hi, I'm Alice Smith and I'm 25 years old."
```

### Default Parameters (ES6+)

```javascript
function greet(name = "Guest") {
  console.log(`Hello, ${name}!`);
}

greet("Alice");  // "Hello, Alice!"
greet();         // "Hello, Guest!"
```

### Rest Parameters (`...`)

Capture any number of arguments into an array:

```javascript
function sum(...numbers) {
  let total = 0;
  for (let num of numbers) {
    total += num;
  }
  return total;
}

console.log(sum(1, 2, 3));     // 6
console.log(sum(10, 20));      // 30
console.log(sum());            // 0
```

### The `arguments` Object (Legacy)

In non-arrow functions, `arguments` is an array-like object containing all passed arguments:

```javascript
function showArgs() {
  console.log(arguments);
  console.log(arguments[0]);
  console.log(arguments.length);
}

showArgs("a", "b", "c");
// ["a", "b", "c"]
// "a"
// 3
```

> **Note**: `arguments` doesn't exist in arrow functions. Prefer rest parameters (`...args`) in modern code.

---

## Return Values

Functions can send data back using the `return` statement. Once `return` executes, the function stops immediately.

```javascript
function add(a, b) {
  return a + b;
}

let result = add(10, 20);
console.log(result); // 30
```

### Functions Without Return

If a function doesn't explicitly return anything, it returns `undefined`:

```javascript
function logMessage(msg) {
  console.log(msg);
}

let output = logMessage("Hello");
console.log(output); // undefined
```

### Early Returns

Use `return` to exit a function early:

```javascript
function divide(a, b) {
  if (b === 0) {
    return "Cannot divide by zero!";
  }
  return a / b;
}

console.log(divide(10, 2));  // 5
console.log(divide(10, 0));  // "Cannot divide by zero!"
```

---

## Scope

**Scope** determines where variables are accessible in your code.

### Function Scope

Variables declared with `var` inside a function are only accessible within that function:

```javascript
function example() {
  var message = "I'm inside the function";
  console.log(message); // Works
}

example();
// console.log(message); // ❌ Error! Not accessible outside
```

### Block Scope

Variables declared with `let` and `const` are scoped to the nearest `{}` block:

```javascript
function demo() {
  if (true) {
    let blockVar = "I'm block-scoped";
    const alsoBlock = "Me too";
    var functionVar = "I'm function-scoped";
  }

  // console.log(blockVar);    // ❌ Error!
  // console.log(alsoBlock);   // ❌ Error!
  console.log(functionVar);   // ✅ Works!
}
```

### Global Scope

Variables declared outside any function or block are globally accessible:

```javascript
let globalVar = "I'm global";

function showGlobal() {
  console.log(globalVar); // ✅ Accessible
}

showGlobal();
console.log(globalVar);   // ✅ Accessible
```

> **Best Practice**: Avoid global variables. They can be accidentally modified from anywhere, leading to bugs.

---

## Higher-Order Functions

Functions that operate on other functions — either by taking them as arguments or returning them.

### Functions as Arguments (Callbacks)

```javascript
function executeOperation(a, b, operation) {
  return operation(a, b);
}

function add(x, y) {
  return x + y;
}

function multiply(x, y) {
  return x * y;
}

console.log(executeOperation(5, 3, add));      // 8
console.log(executeOperation(5, 3, multiply)); // 15

// With arrow function (inline)
console.log(executeOperation(5, 3, (x, y) => x - y)); // 2
```

### Functions Returning Functions

```javascript
function makeMultiplier(factor) {
  return function(number) {
    return number * factor;
  };
}

const double = makeMultiplier(2);
const triple = makeMultiplier(3);

console.log(double(5));  // 10
console.log(triple(5));  // 15
```

---

## Immediately Invoked Function Expressions (IIFE)

Functions that run immediately after being defined. Useful for creating private scope.

```javascript
(function() {
  let privateVar = "I'm private";
  console.log(privateVar);
})();

// console.log(privateVar); // ❌ Error! Not accessible

// Arrow function IIFE
(() => {
  console.log("IIFE with arrow function");
})();
```

---

## Recursion

A function that calls itself. Must have a **base case** to prevent infinite loops.

```javascript
function factorial(n) {
  // Base case
  if (n <= 1) {
    return 1;
  }
  // Recursive case
  return n * factorial(n - 1);
}

console.log(factorial(5)); // 120 (5 × 4 × 3 × 2 × 1)
```

### How Recursion Works

```javascript
factorial(5)
→ 5 * factorial(4)
→ 5 * (4 * factorial(3))
→ 5 * (4 * (3 * factorial(2)))
→ 5 * (4 * (3 * (2 * factorial(1))))
→ 5 * (4 * (3 * (2 * 1)))
→ 120
```

### Another Example: Countdown

```javascript
function countdown(n) {
  if (n <= 0) {
    console.log("Blast off! 🚀");
    return;
  }
  console.log(n);
  countdown(n - 1);
}

countdown(5);
// 5, 4, 3, 2, 1, "Blast off! 🚀"
```

---

## Pure Functions

A **pure function**:
1. Always returns the same output for the same input
2. Has no side effects (doesn't modify external state)

```javascript
// ✅ Pure function
function add(a, b) {
  return a + b;
}

// ❌ Impure function (side effect)
let total = 0;
function addToTotal(value) {
  total += value; // Modifies external variable
}

// ❌ Impure function (different output for same input)
function getRandomNumber() {
  return Math.random(); // Different every time!
}
```

---

## Common Patterns

### Pattern 1: Validation Function

```javascript
function isValidEmail(email) {
  if (!email || email.length === 0) {
    return false;
  }
  if (!email.includes("@")) {
    return false;
  }
  return true;
}

console.log(isValidEmail("alice@example.com")); // true
console.log(isValidEmail("invalid"));            // false
```

### Pattern 2: Guard Clauses

```javascript
function processPayment(amount, cardNumber) {
  if (amount <= 0) return "Invalid amount";
  if (!cardNumber) return "Card required";
  if (cardNumber.length !== 16) return "Invalid card";

  return `Processing $${amount}...`;
}
```

### Pattern 3: Configuration Objects

```javascript
function createUser(config) {
  const defaults = {
    name: "Guest",
    role: "user",
    isActive: true
  };

  const user = { ...defaults, ...config };
  return user;
}

console.log(createUser({ name: "Alice" }));
// { name: "Alice", role: "user", isActive: true }
```

---

## Common Mistakes

### Mistake 1: Forgetting `return`

```javascript
function double(x) {
  x * 2; // ❌ No return!
}

console.log(double(5)); // undefined

// ✅ Fixed
function double(x) {
  return x * 2;
}
```

### Mistake 2: Calling vs Referencing

```javascript
function greet() {
  return "Hello!";
}

console.log(greet);    // [Function: greet] — the function itself
console.log(greet());  // "Hello!" — the result of calling it
```

### Mistake 3: Parameter Mutation

```javascript
function addToArray(arr, value) {
  arr.push(value); // ❌ Mutates the original array!
  return arr;
}

// ✅ Better: return a new array
function addToArraySafe(arr, value) {
  return [...arr, value];
}
```

### Mistake 4: Too Many Parameters

```javascript
// ❌ Hard to read and use
function createUser(name, email, age, city, country, zip, isAdmin, role) {
  // ...
}

// ✅ Use an object
function createUser(options) {
  const { name, email, age, city, country, zip, isAdmin, role } = options;
  // ...
}
```

---

## Practice Exercises

### Exercise 1: Temperature Converter

Write functions to convert between Celsius and Fahrenheit:

```javascript
function celsiusToFahrenheit(c) {
  // F = (C × 9/5) + 32
}

function fahrenheitToCelsius(f) {
  // C = (F - 32) × 5/9
}
```

### Exercise 2: Is Prime

Write a function that checks if a number is prime.

```javascript
function isPrime(n) {
  // Return true if n is prime, false otherwise
}

console.log(isPrime(7));   // true
console.log(isPrime(10));  // false
console.log(isPrime(1));   // false
```

### Exercise 3: Fibonacci

Write a recursive function to find the nth Fibonacci number.

```javascript
function fibonacci(n) {
  // fib(0) = 0, fib(1) = 1
  // fib(n) = fib(n-1) + fib(n-2)
}

console.log(fibonacci(6)); // 8 (0, 1, 1, 2, 3, 5, 8)
```

### Exercise 4: Calculator with Callbacks

```javascript
function calculate(a, b, operation) {
  // operation is a callback function
}

calculate(10, 5, (x, y) => x + y); // 15
calculate(10, 5, (x, y) => x * y); // 50
```

### Exercise 5: Function Composition

```javascript
function compose(f, g) {
  // Return a new function that is f(g(x))
}

const add1 = x => x + 1;
const double = x => x * 2;

const addThenDouble = compose(double, add1);
console.log(addThenDouble(5)); // 12 (5 + 1 = 6, then 6 * 2 = 12)
```

---

## Summary

- **Function Declaration**: `function name() {}` — hoisted
- **Function Expression**: `const name = function() {}` — not hoisted
- **Arrow Function**: `const name = () => {}` — concise, no own `this`
- **Parameters** are in the definition; **arguments** are passed when calling
- Use **`return`** to send values back from a function
- **Scope** controls where variables are accessible: global, function, block
- **Higher-order functions** take or return other functions
- **Recursion** is a function calling itself with a base case
- **Pure functions** have no side effects and consistent outputs

---

## Next Steps

Now that you understand functions, explore:
- **Arrays** — storing and manipulating collections
- **Objects** — grouping related data and functions
- **Closures** — advanced scope behavior
- **Callbacks and Async** — functions that run later

Happy coding! 🚀
