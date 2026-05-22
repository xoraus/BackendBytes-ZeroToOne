# Conditionals in JavaScript

## Overview

Programs need to make decisions. Should a user be allowed to log in? Is the temperature too high? Which discount applies? **Conditional statements** allow your code to execute different blocks based on whether certain conditions are `true` or `false`. This is the foundation of logical programming.

---

## The `if` Statement

The simplest conditional. It runs code only if a condition is true.

```javascript
let temperature = 30;

if (temperature > 25) {
  console.log("It's hot outside! 🌞");
}
```

### Syntax

```javascript
if (condition) {
  // code to run if condition is true
}
```

The condition must evaluate to a boolean. JavaScript will **coerce** non-boolean values:

```javascript
let username = "alice";

if (username) {
  console.log("Welcome, " + username);
}
// Works because non-empty strings are "truthy"
```

---

## The `if...else` Statement

Provides an alternative path when the condition is false.

```javascript
let age = 16;

if (age >= 18) {
  console.log("You can vote! 🗳️");
} else {
  console.log("You are too young to vote.");
}
```

### Syntax

```javascript
if (condition) {
  // true path
} else {
  // false path
}
```

---

## The `if...else if...else` Statement

Chains multiple conditions together.

```javascript
let score = 85;

if (score >= 90) {
  console.log("Grade: A 🌟");
} else if (score >= 80) {
  console.log("Grade: B 👍");
} else if (score >= 70) {
  console.log("Grade: C ✅");
} else if (score >= 60) {
  console.log("Grade: D ⚠️");
} else {
  console.log("Grade: F ❌");
}
```

**Important**: Conditions are checked **top to bottom**. As soon as one matches, the rest are skipped!

```javascript
let number = 50;

if (number > 10) {
  console.log("Greater than 10"); // ✓ This runs
} else if (number > 40) {
  console.log("Greater than 40"); // ✗ Never checked!
}
```

---

## Truthy and Falsy Values

In JavaScript, any value can be used in a boolean context. These values are **falsy** (treated as `false`):

| Value | Description |
|-------|-------------|
| `false` | The boolean false |
| `0` | The number zero |
| `""` | Empty string |
| `null` | No value |
| `undefined` | Not initialized |
| `NaN` | Not a Number |
| `0n` | BigInt zero |

**Everything else is truthy**, including:
- `"0"` (string with zero)
- `"false"` (string with text "false")
- `[]` (empty array)
- `{}` (empty object)
- `-1` (negative numbers)

```javascript
let userInput = "";

if (userInput) {
  console.log("Input received");
} else {
  console.log("No input provided"); // This runs
}
```

---

## Comparison Operators in Conditions

### Equality Operators

```javascript
// Loose equality (avoids — performs type coercion)
console.log(5 == "5");   // true
console.log(0 == false); // true
console.log(null == undefined); // true

// Strict equality (recommended — no type coercion)
console.log(5 === "5");  // false
console.log(0 === false); // false
console.log(null === undefined); // false
```

> **Always use `===` and `!==`** to avoid unexpected type coercion bugs.

### Relational Operators

```javascript
console.log(10 > 5);   // true
console.log(10 < 5);   // false
console.log(5 >= 5);   // true
console.log(5 <= 4);   // false
```

### String Comparisons

JavaScript compares strings **lexicographically** (dictionary order), character by character using Unicode values:

```javascript
console.log("apple" < "banana");  // true
console.log("Zebra" > "apple");   // false (uppercase < lowercase)
console.log("10" < "2");          // true (compares first char: "1" < "2")
```

---

## Logical Operators in Conditions

Combine multiple conditions using logical operators.

### AND (`&&`)
Both conditions must be true.

```javascript
let age = 25;
let hasLicense = true;

if (age >= 18 && hasLicense) {
  console.log("You can drive! 🚗");
}
```

**Truth Table:**

| A | B | A && B |
|---|---|--------|
| true | true | true |
| true | false | false |
| false | true | false |
| false | false | false |

### OR (`||`)
At least one condition must be true.

```javascript
let isWeekend = true;
let isHoliday = false;

if (isWeekend || isHoliday) {
  console.log("No work today! 🎉");
}
```

**Truth Table:**

| A | B | A \|\| B |
|---|---|----------|
| true | true | true |
| true | false | true |
| false | true | true |
| false | false | false |

### NOT (`!`)
Inverts a boolean value.

```javascript
let isLoggedOut = true;

if (!isLoggedOut) {
  console.log("Welcome back!");
} else {
  console.log("Please log in."); // This runs
}
```

### Combining Logical Operators

```javascript
let age = 20;
let hasTicket = true;
let isVIP = false;

if ((age >= 18 && hasTicket) || isVIP) {
  console.log("Entry granted! 🎫");
}
```

Use parentheses to clarify precedence. JavaScript evaluates `!` first, then `&&`, then `||`.

---

## Short-Circuit Evaluation

JavaScript uses **short-circuit evaluation** — it stops evaluating as soon as the result is known.

### AND Short-Circuit

```javascript
let user = null;

// If user is null, the second part is NOT evaluated
if (user && user.name === "admin") {
  console.log("Admin access");
}
// Safe! No error even though user is null
```

### OR Short-Circuit

```javascript
let username = "";
let displayName = username || "Guest";

console.log(displayName); // "Guest"
```

### Nullish Coalescing Operator (`??`)

Similar to `||`, but only checks for `null` or `undefined` (not other falsy values):

```javascript
let count = 0;

console.log(count || 10);  // 10 (0 is falsy)
console.log(count ?? 10);  // 0 (0 is not null/undefined)

let name = null;
console.log(name ?? "Anonymous"); // "Anonymous"
```

---

## The Ternary Operator (`?:`)

A shorthand for simple `if...else` statements.

```javascript
let age = 20;
let message = (age >= 18) ? "Adult" : "Minor";

console.log(message); // "Adult"
```

### Syntax

```javascript
condition ? valueIfTrue : valueIfFalse;
```

### Nested Ternary (Use Sparingly)

```javascript
let score = 85;
let grade = score >= 90 ? "A" :
            score >= 80 ? "B" :
            score >= 70 ? "C" : "F";

console.log(grade); // "B"
```

> **Warning**: Deeply nested ternaries reduce readability. Prefer `if...else if` for complex logic.

---

## Optional Chaining (`?.`)

Safely access nested properties without crashing if an intermediate value is `null` or `undefined`.

```javascript
let user = {
  profile: {
    name: "Alice",
    address: {
      city: "New York"
    }
  }
};

// Without optional chaining
console.log(user.profile.address.city);        // "New York"
// console.log(user.settings.theme);           // ❌ Error!

// With optional chaining
console.log(user?.profile?.name);              // "Alice"
console.log(user?.settings?.theme);            // undefined (no error!)
console.log(user?.profile?.address?.zipcode);  // undefined
```

---

## Common Patterns

### Guard Clause

Return early to reduce nesting:

```javascript
// ❌ Deeply nested
function processUser(user) {
  if (user) {
    if (user.isActive) {
      if (user.hasPermission) {
        return "Processing...";
      }
    }
  }
}

// ✅ Guard clauses (flat structure)
function processUser(user) {
  if (!user) return "No user";
  if (!user.isActive) return "User inactive";
  if (!user.hasPermission) return "No permission";
  
  return "Processing...";
}
```

### Checking Multiple Values

```javascript
let status = "pending";

if (status === "pending" || status === "processing" || status === "review") {
  console.log("Order is active");
}

// Cleaner with includes()
let activeStatuses = ["pending", "processing", "review"];
if (activeStatuses.includes(status)) {
  console.log("Order is active");
}
```

---

## Common Mistakes

### Mistake 1: Assignment Instead of Comparison

```javascript
let x = 10;

if (x = 5) {    // ❌ Assignment! x is now 5, condition is truthy
  console.log("This always runs!");
}

if (x === 5) {  // ✅ Comparison
  console.log("x is 5");
}
```

### Mistake 2: Floating Point Comparison

```javascript
console.log(0.1 + 0.2 === 0.3); // false!

// Solution: use a small tolerance
let sum = 0.1 + 0.2;
if (Math.abs(sum - 0.3) < 0.0001) {
  console.log("Approximately equal");
}
```

### Mistake 3: Comparing Objects and Arrays

```javascript
let arr1 = [1, 2, 3];
let arr2 = [1, 2, 3];

console.log(arr1 === arr2); // false (different references!)
console.log(arr1 === arr1); // true (same reference)
```

### Mistake 4: Multiple Conditions Without Proper Grouping

```javascript
let age = 15;
let hasConsent = true;

if (age >= 18 || age >= 13 && hasConsent) {
  // Due to precedence, this is: age >= 18 || (age >= 13 && hasConsent)
  console.log("Access granted");
}

// Better: be explicit with parentheses
if ((age >= 18) || (age >= 13 && hasConsent)) {
  console.log("Access granted");
}
```

---

## Practice Exercises

### Exercise 1: Number Classifier

Write a program that takes a number and prints whether it's positive, negative, or zero.

### Exercise 2: Leap Year Checker

A leap year is divisible by 4, except if divisible by 100, unless also divisible by 400.

```javascript
function isLeapYear(year) {
  // Your code here
}

console.log(isLeapYear(2000)); // true
console.log(isLeapYear(1900)); // false
console.log(isLeapYear(2024)); // true
```

### Exercise 3: Login System

Implement a simple login check:
- If username is empty → "Username required"
- If password is empty → "Password required"
- If both provided but wrong → "Invalid credentials"
- If both correct → "Login successful"

### Exercise 4: Day of Week

Given a number (1-7), print the corresponding day. Handle invalid inputs.

### Exercise 5: BMI Calculator with Categories

Calculate BMI and categorize:
- Under 18.5: "Underweight"
- 18.5 - 24.9: "Normal weight"
- 25 - 29.9: "Overweight"
- 30+: "Obese"

---

## Summary

- **`if`** runs code when a condition is true
- **`if...else`** provides an alternative path
- **`if...else if...else`** chains multiple conditions
- Use **`===`** and **`!==`** for safe comparisons
- **`&&`** requires both conditions to be true
- **`||`** requires at least one to be true
- **`!`** inverts a boolean
- **`??`** provides defaults for `null`/`undefined` only
- **Ternary operator** `?:` is a compact `if...else`
- **Optional chaining** `?.` safely accesses nested properties
- Use **guard clauses** to keep code flat and readable

---

## Next Steps

Now that you can make decisions in your code, learn about:
- **Switch statements** for multiple fixed-value comparisons
- **Loops** for repeating actions
- **Functions** for organizing reusable logic

Happy coding! 🚀
