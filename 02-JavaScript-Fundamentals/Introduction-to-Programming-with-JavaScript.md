# Introduction to Programming with JavaScript

## Overview

JavaScript is one of the most popular programming languages in the world. Originally created to make web pages interactive, it has evolved into a versatile language used everywhere — from websites and mobile apps to servers and even robotics. This tutorial will take you from absolute basics to writing your first meaningful JavaScript programs.

---

## What is Programming?

Programming is the process of giving instructions to a computer. Just like you follow a recipe to bake a cake, a computer follows your code (instructions) to perform tasks. These instructions must be precise, unambiguous, and written in a language the computer understands.

### Why JavaScript?

- **Ubiquitous**: Runs in every modern web browser
- **Versatile**: Can be used for frontend, backend (Node.js), mobile, and desktop apps
- **Beginner-friendly**: Flexible syntax with immediate visual feedback
- **Huge ecosystem**: Millions of libraries and an enormous community
- **Career opportunities**: One of the most in-demand programming skills

---

## Setting Up Your Environment

You don't need to install anything to start writing JavaScript. Here are three ways to run JavaScript code:

### 1. Browser Console
- Open any browser (Chrome, Firefox, Edge)
- Press `F12` or right-click → Inspect → Console tab
- Type JavaScript directly and press Enter

### 2. VS Code with Node.js
- Install [Node.js](https://nodejs.org/) (LTS version recommended)
- Create a file named `app.js`
- Write code and run with: `node app.js`

### 3. Online Editors
- [JSFiddle](https://jsfiddle.net/)
- [CodePen](https://codepen.io/)
- [Replit](https://replit.com/)

---

## Your First Program: Hello World

```javascript
console.log("Hello, World!");
```

**Output:**
```
Hello, World!
```

`console.log()` is a function that prints text to the console. The text inside quotes is called a **string**.

---

## Basic Building Blocks

### 1. Statements
A statement is a single instruction. JavaScript programs are collections of statements.

```javascript
console.log("Statement 1");
console.log("Statement 2");
console.log("Statement 3");
```

Each statement typically ends with a semicolon (`;`). In modern JavaScript, semicolons are often optional due to **Automatic Semicolon Insertion (ASI)**, but using them is a good habit.

### 2. Comments
Comments are notes for humans. The computer ignores them.

```javascript
// This is a single-line comment

/*
  This is a
  multi-line comment
*/
```

---

## Variables: Storing Data

Variables are containers for storing data values.

### Declaring Variables

JavaScript has three ways to declare variables:

```javascript
// var - old way, function-scoped (avoid in modern code)
var name = "Alice";

// let - modern way, block-scoped, can be reassigned
let age = 25;
age = 26; // ✅ Works fine

// const - modern way, block-scoped, cannot be reassigned
const PI = 3.14159;
PI = 3.14; // ❌ Error: Assignment to constant variable
```

### Variable Naming Rules

- Must start with a letter, underscore (`_`), or dollar sign (`$`)
- Can contain letters, numbers, underscores, and dollar signs
- Cannot use reserved keywords (`let`, `const`, `if`, `for`, etc.)
- Case-sensitive: `name` and `Name` are different variables

```javascript
let userName;      // ✅ camelCase (recommended)
let _private;      // ✅
let $element;      // ✅
let firstName1;    // ✅
let 1stPlace;      // ❌ Cannot start with number
let my-name;       // ❌ Hyphens not allowed
```

### Naming Conventions

Use **camelCase** for variables and functions:
```javascript
let firstName = "John";
let totalScore = 95;
let isActive = true;
```

---

## Data Types

JavaScript is **dynamically typed** — you don't need to declare the type of a variable; JavaScript figures it out automatically.

### Primitive Types

#### 1. String
Text data enclosed in single quotes, double quotes, or backticks.

```javascript
let firstName = "Alice";
let lastName = 'Smith';
let greeting = `Hello, ${firstName}!`; // Template literal

console.log(greeting); // Hello, Alice!
```

#### 2. Number
Both integers and floating-point numbers.

```javascript
let age = 25;          // Integer
let price = 19.99;     // Float
let negative = -10;    // Negative
let infinity = 1 / 0;  // Infinity
let notANumber = "abc" / 2; // NaN (Not a Number)
```

#### 3. Boolean
Logical values: `true` or `false`.

```javascript
let isLoggedIn = true;
let hasPermission = false;
```

#### 4. Undefined
A variable that has been declared but not assigned a value.

```javascript
let score;
console.log(score); // undefined
```

#### 5. Null
Represents "no value" or "empty" intentionally.

```javascript
let user = null; // "I want this to be empty"
```

#### 6. Symbol
Unique identifiers (advanced, used less frequently).

```javascript
const id = Symbol("id");
```

#### 7. BigInt
For very large integers.

```javascript
const hugeNumber = 123456789012345678901234567890n;
```

### typeof Operator

Check the type of a value:

```javascript
console.log(typeof "Hello");    // "string"
console.log(typeof 42);         // "number"
console.log(typeof true);       // "boolean"
console.log(typeof undefined);  // "undefined"
console.log(typeof null);       // "object" (historical bug in JS)
console.log(typeof {});         // "object"
```

---

## Operators

### Arithmetic Operators

```javascript
let a = 10;
let b = 3;

console.log(a + b);  // 13 - Addition
console.log(a - b);  // 7  - Subtraction
console.log(a * b);  // 30 - Multiplication
console.log(a / b);  // 3.333... - Division
console.log(a % b);  // 1  - Modulo (remainder)
console.log(a ** b); // 1000 - Exponentiation (10^3)
```

### Assignment Operators

```javascript
let x = 10;

x += 5;  // x = x + 5  → 15
x -= 3;  // x = x - 3  → 12
x *= 2;  // x = x * 2  → 24
x /= 4;  // x = x / 4  → 6
x %= 4;  // x = x % 4  → 2
```

### Comparison Operators

```javascript
console.log(5 == "5");   // true  - loose equality (type coercion)
console.log(5 === "5");  // false - strict equality (same type AND value)
console.log(5 != "5");   // false - loose inequality
console.log(5 !== "5");  // true  - strict inequality
console.log(10 > 5);     // true  - greater than
console.log(10 < 5);     // false - less than
console.log(5 >= 5);     // true  - greater than or equal
console.log(5 <= 4);     // false - less than or equal
```

> **Best Practice**: Always use `===` and `!==` instead of `==` and `!=` to avoid unexpected type coercion.

### Logical Operators

```javascript
let hasID = true;
let isAdult = true;

console.log(hasID && isAdult); // true - AND (both must be true)
console.log(hasID || isAdult); // true - OR (at least one true)
console.log(!hasID);           // false - NOT (inverts boolean)
```

### String Concatenation

```javascript
let firstName = "John";
let lastName = "Doe";

// Using + operator
let fullName = firstName + " " + lastName;
console.log(fullName); // "John Doe"

// Using template literals (preferred)
let greeting = `Hello, ${firstName} ${lastName}!`;
console.log(greeting); // "Hello, John Doe!"
```

---

## Input and Output

### Output

```javascript
console.log("General output");
console.error("Error message");   // Red text in console
console.warn("Warning message");  // Yellow text
console.table([{name: "Alice", age: 25}, {name: "Bob", age: 30}]);
```

### Input (with Node.js)

```javascript
// Using readline
const readline = require('readline');

const rl = readline.createInterface({
  input: process.stdin,
  output: process.stdout
});

rl.question('What is your name? ', (name) => {
  console.log(`Hello, ${name}!`);
  rl.close();
});
```

### Input (in Browser)

```javascript
let name = prompt("What is your name?");
console.log("Hello, " + name);
```

---

## Type Conversion

### Explicit Conversion

```javascript
// String to Number
let str = "42";
let num = Number(str);      // 42
let num2 = parseInt("42");  // 42
let num3 = parseFloat("3.14"); // 3.14

// Number to String
let count = 100;
let strCount = String(count);  // "100"
let strCount2 = count.toString(); // "100"

// To Boolean
console.log(Boolean(1));     // true
console.log(Boolean(0));     // false
console.log(Boolean(""));    // false
console.log(Boolean("hi"));  // true
```

### Implicit Conversion (Type Coercion)

JavaScript sometimes converts types automatically:

```javascript
console.log("5" + 3);    // "53" (number converted to string)
console.log("5" - 3);    // 2 (string converted to number)
console.log("5" * "2");  // 10 (both strings converted to numbers)
console.log(true + 1);   // 2 (true becomes 1)
console.log(false + 1);  // 1 (false becomes 0)
```

> Be careful with implicit coercion — it can lead to bugs!

---

## Your First Complete Program

Let's write a program that calculates the area of a rectangle:

```javascript
// Rectangle Area Calculator

// Step 1: Define dimensions
let length = 10;
let width = 5;

// Step 2: Calculate area
let area = length * width;

// Step 3: Display result
console.log(`Length: ${length}`);
console.log(`Width: ${width}`);
console.log(`Area of rectangle: ${area}`);
```

**Output:**
```
Length: 10
Width: 5
Area of rectangle: 50
```

---

## Common Mistakes and How to Avoid Them

| Mistake | Why It Happens | Solution |
|---------|---------------|----------|
| `console.log(x)` when `x` is undefined | Variable not declared or assigned | Always initialize variables before use |
| `if (x = 5)` instead of `if (x === 5)` | Using assignment `=` instead of comparison `===` | Use `===` for comparisons |
| `"5" + 3 = "53"` | Unexpected string concatenation | Use `Number()` to convert before math |
| Missing quotes around strings | JavaScript thinks it's a variable | Wrap text in quotes |
| Case sensitivity errors | `myVariable` ≠ `myvariable` | Be consistent with naming |

---

## Practice Exercises

### Exercise 1: Personal Greeting
Write a program that stores your name and age in variables, then prints:  
`Hello! My name is [name] and I am [age] years old.`

### Exercise 2: Swap Two Variables
Swap the values of two variables `a` and `b` without directly assigning the values.

```javascript
let a = 5;
let b = 10;
// Your code here
console.log(a); // Should print 10
console.log(b); // Should print 5
```

### Exercise 3: Temperature Converter
Convert Celsius to Fahrenheit using the formula: `F = (C × 9/5) + 32`

### Exercise 4: Simple Calculator
Create variables for two numbers and an operator (`+`, `-`, `*`, `/`), then compute and display the result.

### Exercise 5: BMI Calculator
Calculate BMI using: `BMI = weight(kg) / (height(m))²`

---

## Summary

- **JavaScript** is a versatile, beginner-friendly programming language
- **Variables** store data using `let` (changeable) and `const` (fixed)
- **Data types** include string, number, boolean, undefined, null, symbol, and bigint
- **Operators** perform arithmetic, assignment, comparison, and logical operations
- Always prefer `===` over `==` for comparisons
- Use `console.log()` to output data and see what's happening in your code

---

## Next Steps

Now that you understand the basics, you're ready to learn about:
- Conditional statements (if/else, switch)
- Loops (for, while)
- Functions
- Arrays and objects

Happy coding! 🚀
