# Switch Statements in JavaScript

## Overview

The `switch` statement is a cleaner alternative to long chains of `if...else if...else` when you need to compare a single expression against multiple **fixed values**. It's especially useful for menu selections, status codes, and day/month handling.

---

## Basic Syntax

```javascript
switch (expression) {
  case value1:
    // code to execute if expression === value1
    break;
  case value2:
    // code to execute if expression === value2
    break;
  default:
    // code to execute if no case matches
}
```

### How It Works

1. The `expression` is evaluated once
2. Its value is compared with each `case` value using **strict equality** (`===`)
3. When a match is found, execution starts from that case
4. `break` exits the `switch` block
5. If no match is found, the `default` case runs (if provided)

---

## Simple Example: Day of the Week

```javascript
let day = 3;
let dayName;

switch (day) {
  case 1:
    dayName = "Monday";
    break;
  case 2:
    dayName = "Tuesday";
    break;
  case 3:
    dayName = "Wednesday";
    break;
  case 4:
    dayName = "Thursday";
    break;
  case 5:
    dayName = "Friday";
    break;
  case 6:
    dayName = "Saturday";
    break;
  case 7:
    dayName = "Sunday";
    break;
  default:
    dayName = "Invalid day";
}

console.log(dayName); // "Wednesday"
```

---

## The Importance of `break`

Without `break`, execution **falls through** to the next case — this is called **fall-through behavior**.

```javascript
let fruit = "apple";

switch (fruit) {
  case "apple":
    console.log("Apple is red");
  case "banana":
    console.log("Banana is yellow");
  case "grape":
    console.log("Grape is purple");
  default:
    console.log("Unknown fruit");
}

// Output:
// Apple is red
// Banana is yellow
// Grape is purple
// Unknown fruit
```

**All cases ran!** Because there was no `break`, execution fell through every subsequent case.

### When Fall-Through Is Useful

Fall-through can be intentional when multiple cases share the same code:

```javascript
let month = 4;
let days;

switch (month) {
  case 1: case 3: case 5: case 7: case 8: case 10: case 12:
    days = 31;
    break;
  case 4: case 6: case 9: case 11:
    days = 30;
    break;
  case 2:
    days = 28;
    break;
  default:
    days = -1; // Invalid month
}

console.log(`Month ${month} has ${days} days`); // "Month 4 has 30 days"
```

---

## Grouping Cases

You can group cases more clearly with comments:

```javascript
let grade = "B";

switch (grade) {
  // Excellent grades
  case "A":
  case "A+":
    console.log("Outstanding! 🌟");
    break;

  // Good grades
  case "B":
  case "B+":
    console.log("Well done! 👍");
    break;

  // Average grades
  case "C":
    console.log("Good effort ✅");
    break;

  // Below average
  case "D":
  case "F":
    console.log("Needs improvement 📚");
    break;

  default:
    console.log("Invalid grade");
}
```

---

## Switch with Strings

```javascript
let command = "start";

switch (command) {
  case "start":
    console.log("Starting the server...");
    break;
  case "stop":
    console.log("Stopping the server...");
    break;
  case "restart":
    console.log("Restarting the server...");
    break;
  case "status":
    console.log("Server is running");
    break;
  default:
    console.log(`Unknown command: ${command}`);
}
```

---

## Switch with Boolean Expressions

You can use `true` as the switch expression and put conditions in cases:

```javascript
let age = 25;

switch (true) {
  case age < 13:
    console.log("Child");
    break;
  case age < 20:
    console.log("Teenager");
    break;
  case age < 60:
    console.log("Adult");
    break;
  default:
    console.log("Senior");
}
// Output: "Adult"
```

> **Note**: This pattern works but `if...else if` is generally more readable for ranges.

---

## Switch vs If-Else

### Use `switch` when:
- Comparing one variable against many fixed values
- Values are simple (numbers, strings)
- You want clean, readable code for many cases

### Use `if...else` when:
- Comparing ranges (e.g., `x > 10 && x < 20`)
- Using complex conditions
- Comparing different variables

| Scenario | Better Choice |
|----------|--------------|
| Menu selection (1-5) | `switch` |
| Grade ranges (0-100) | `if...else if` |
| Status codes ("pending", "done") | `switch` |
| Age categories (ranges) | `if...else if` |

---

## Common Mistakes

### Mistake 1: Forgetting `break`

```javascript
let status = "active";

switch (status) {
  case "active":
    console.log("User is active");
    // ❌ Missing break!
  case "inactive":
    console.log("User is inactive"); // Also runs!
    break;
}
```

### Mistake 2: Using Non-Strict Comparisons

`switch` always uses strict equality (`===`), so type matters:

```javascript
let value = "5";

switch (value) {
  case 5:
    console.log("Number 5"); // ❌ Won't match!
    break;
  case "5":
    console.log("String 5"); // ✓ This runs
    break;
}
```

### Mistake 3: Forgetting `default`

Always include a `default` case to handle unexpected values gracefully:

```javascript
let color = "purple";

switch (color) {
  case "red":
    console.log("Stop");
    break;
  case "yellow":
    console.log("Caution");
    break;
  case "green":
    console.log("Go");
    break;
  // ❌ No default — purple is unhandled!
}
```

---

## Advanced: Returning from Switch

If your `switch` is inside a function, you can use `return` instead of `break`:

```javascript
function getDayType(day) {
  switch (day) {
    case "Saturday":
    case "Sunday":
      return "Weekend 🎉";
    case "Monday":
    case "Tuesday":
    case "Wednesday":
    case "Thursday":
    case "Friday":
      return "Weekday 💼";
    default:
      return "Invalid day";
  }
}

console.log(getDayType("Saturday")); // "Weekend 🎉"
console.log(getDayType("Wednesday")); // "Weekday 💼"
```

---

## Switch with Objects (Modern Alternative)

For simple mappings, an object is often cleaner than `switch`:

```javascript
// Using switch
function getStatusMessage(code) {
  switch (code) {
    case 200: return "OK";
    case 404: return "Not Found";
    case 500: return "Server Error";
    default: return "Unknown Status";
  }
}

// Using object (cleaner for simple mappings)
function getStatusMessage(code) {
  const messages = {
    200: "OK",
    404: "Not Found",
    500: "Server Error"
  };
  return messages[code] || "Unknown Status";
}

console.log(getStatusMessage(404)); // "Not Found"
console.log(getStatusMessage(418)); // "Unknown Status"
```

---

## Practice Exercises

### Exercise 1: Simple Calculator

Use `switch` to implement a calculator:

```javascript
function calculate(a, b, operator) {
  // Use switch on operator: +, -, *, /, %
  // Return result or "Invalid operator"
}

console.log(calculate(10, 5, "+")); // 15
console.log(calculate(10, 5, "/")); // 2
console.log(calculate(10, 5, "^")); // "Invalid operator"
```

### Exercise 2: Season Finder

Given a month number (1-12), return the season:
- Winter: 12, 1, 2
- Spring: 3, 4, 5
- Summer: 6, 7, 8
- Fall: 9, 10, 11

### Exercise 3: HTTP Status Handler

Write a switch that handles HTTP status codes:
- 200: "Success"
- 301, 302: "Redirect"
- 400: "Bad Request"
- 401: "Unauthorized"
- 403: "Forbidden"
- 404: "Not Found"
- 500: "Server Error"
- Default: "Unknown Error"

### Exercise 4: Roman Numeral Converter

Convert digits 1-10 to Roman numerals using a switch statement.

### Exercise 5: Convert Switch to Object

Refactor this switch into an object lookup:

```javascript
function getAnimalSound(animal) {
  switch (animal) {
    case "dog": return "Woof!";
    case "cat": return "Meow!";
    case "cow": return "Moo!";
    case "duck": return "Quack!";
    default: return "Unknown animal";
  }
}
```

---

## Summary

- **`switch`** compares an expression against multiple cases using strict equality (`===`)
- Always use **`break`** to prevent fall-through (unless intentional)
- **`default`** handles unmatched cases — always include it
- Multiple cases can share the same code by stacking them
- For simple key-value mappings, consider using an **object** instead
- Use `switch` for discrete values; use `if...else` for ranges and complex conditions

---

## Next Steps

Now that you understand conditionals and switches, you're ready to learn about:
- **Loops** — repeating actions efficiently
- **Functions** — organizing code into reusable blocks
- **Arrays** — working with collections of data

Happy coding! 🚀
