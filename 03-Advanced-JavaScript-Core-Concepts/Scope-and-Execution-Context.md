# Scope and Execution Context in JavaScript

## Overview

To truly understand JavaScript, you need to grasp two foundational concepts: **Scope** (where variables are accessible) and **Execution Context** (how code runs). These concepts explain why variables behave the way they do, what `this` refers to, and how functions can "remember" their birthplace. This tutorial demystifies the JavaScript engine's inner workings.

---

## What is Execution Context?

Every time JavaScript runs code, it does so inside an **Execution Context**. Think of it as a container that holds all the information needed to execute a piece of code.

### Types of Execution Contexts

1. **Global Execution Context (GEC)** — Created when the script starts. There's only one.
2. **Function Execution Context (FEC)** — Created every time a function is called.
3. **Eval Execution Context** — Created when `eval()` is used (avoid this).

---

## The Execution Context Lifecycle

Each execution context goes through two phases:

### Phase 1: Creation (Memory Allocation)

Before any code runs, JavaScript scans the context and:
- Creates the `global` or `arguments` object
- Sets up `this`
- Allocates memory for variables and functions (hoisting)

### Phase 2: Execution

Code runs line by line, and variables are assigned their actual values.

---

## The Call Stack

JavaScript is **single-threaded** — it can only do one thing at a time. The **Call Stack** keeps track of which execution context is currently running.

```javascript
function first() {
  console.log("First");
  second();
  console.log("First again");
}

function second() {
  console.log("Second");
  third();
  console.log("Second again");
}

function third() {
  console.log("Third");
}

first();
```

**Call Stack Flow:**
```
1. [Global Context]
2. [first] [Global Context]
3. [second] [first] [Global Context]
4. [third] [second] [first] [Global Context]
5. [second] [first] [Global Context]  ← third finishes
6. [first] [Global Context]           ← second finishes
7. [Global Context]                   ← first finishes
```

**Output:**
```
First
Second
Third
Second again
First again
```

> **Key Insight**: When a function completes, its execution context is popped off the stack, and control returns to the previous context.

---

## What is Scope?

**Scope** determines the accessibility (visibility) of variables. In JavaScript, scope answers the question: *"Where can I use this variable?"*

---

## Types of Scope in JavaScript

### 1. Global Scope

Variables declared outside any function or block are in the global scope and accessible everywhere.

```javascript
const globalVar = "I'm global";

function showGlobal() {
  console.log(globalVar); // ✅ Accessible
}

showGlobal();
console.log(globalVar);   // ✅ Accessible
```

> **Best Practice**: Avoid global variables. They can be accidentally modified from anywhere, leading to bugs that are hard to track.

### 2. Function Scope

Variables declared with `var` inside a function are only accessible within that function.

```javascript
function myFunction() {
  var functionScoped = "I'm inside the function";
  console.log(functionScoped); // ✅ Works
}

myFunction();
// console.log(functionScoped); // ❌ Error! Not defined
```

### 3. Block Scope

Variables declared with `let` and `const` inside `{}` are only accessible within that block.

```javascript
if (true) {
  let blockScoped = "I'm in a block";
  const alsoBlocked = "Me too";
  var notBlocked = "I'm function-scoped";
  console.log(blockScoped); // ✅ Works
}

// console.log(blockScoped);    // ❌ Error!
// console.log(alsoBlocked);    // ❌ Error!
console.log(notBlocked);       // ✅ Works (var ignores blocks)
```

**Block scope applies to:** `if`, `for`, `while`, `switch`, and standalone `{}`.

```javascript
{
  let secret = 42;
}
// console.log(secret); // ❌ Error!
```

### Scope Comparison Table

| Keyword | Scope | Can Reassign? | Can Redeclare? | Hoisting |
|---------|-------|--------------|----------------|----------|
| `var` | Function | ✅ | ✅ | ✅ (initialized `undefined`) |
| `let` | Block | ✅ | ❌ (same block) | ✅ (not initialized) |
| `const` | Block | ❌ | ❌ (same block) | ✅ (not initialized) |

---

## Lexical Scope (Static Scope)

JavaScript uses **lexical scoping**, meaning a function's scope is determined by **where it is written in the code**, not where it is called.

```javascript
const outerVar = "I'm outer";

function outer() {
  const innerVar = "I'm inner";

  function inner() {
    console.log(outerVar); // ✅ Can access outer scope
    console.log(innerVar); // ✅ Can access parent's scope
  }

  inner();
}

outer();
```

**The Scope Chain**: When looking for a variable, JavaScript:
1. Checks the current scope
2. If not found, checks the parent scope
3. Continues up the chain until the global scope
4. If still not found → `ReferenceError`

```javascript
const a = "global";

function first() {
  const b = "first";

  function second() {
    const c = "second";
    console.log(a); // Found in global
    console.log(b); // Found in first()
    console.log(c); // Found in second()
  }

  second();
}

first();
```

---

## Hoisting

Hoisting is JavaScript's behavior of moving declarations to the top of their scope during the creation phase.

### Variable Hoisting

```javascript
console.log(hoistedVar); // undefined (not an error!)
var hoistedVar = 5;

// JavaScript sees this as:
var hoistedVar;
console.log(hoistedVar); // undefined
hoistedVar = 5;
```

### Function Declaration Hoisting

```javascript
sayHello(); // ✅ Works! Function declarations are fully hoisted

function sayHello() {
  console.log("Hello!");
}
```

### Function Expression Hoisting

```javascript
// sayHi(); // ❌ Error! Not a function
var sayHi = function() {
  console.log("Hi!");
};

// JavaScript sees this as:
var sayHi;        // hoisted as undefined
// sayHi();       // Error: sayHi is not a function
sayHi = function() { ... };
```

### `let` and `const` Hoisting (The Temporal Dead Zone)

```javascript
// console.log(x); // ❌ ReferenceError!
let x = 10;

// Variables exist in the "temporal dead zone" from the start of
// the block until the declaration is encountered.
```

---

## The `this` Keyword

`this` is a special keyword that refers to the **execution context's owner**. Its value depends on **how** a function is called, not where it's defined.

### 1. Global Context

```javascript
console.log(this); // Window (browser) or global (Node.js)
```

### 2. Inside an Object Method

```javascript
const user = {
  name: "Alice",
  greet() {
    console.log(`Hello, I'm ${this.name}`);
  }
};

user.greet(); // "Hello, I'm Alice" — this = user object
```

### 3. Standalone Function

```javascript
function showThis() {
  console.log(this);
}

showThis(); // Window (non-strict) or undefined (strict mode)
```

### 4. Constructor Function

```javascript
function Person(name) {
  this.name = name;
}

const alice = new Person("Alice");
console.log(alice.name); // "Alice" — this = new object
```

### 5. Arrow Functions

Arrow functions **do not have their own `this`**. They inherit `this` from the surrounding scope.

```javascript
const user = {
  name: "Bob",
  regularFunction: function() {
    console.log(this.name); // "Bob"
  },
  arrowFunction: () => {
    console.log(this.name); // undefined (inherits from global)
  }
};

user.regularFunction();
user.arrowFunction();
```

### 6. Explicit Binding with `call`, `apply`, `bind`

```javascript
function introduce() {
  console.log(`Hi, I'm ${this.name}`);
}

const person = { name: "Charlie" };

introduce.call(person);   // "Hi, I'm Charlie"
introduce.apply(person);  // "Hi, I'm Charlie"

const boundIntroduce = introduce.bind(person);
boundIntroduce();         // "Hi, I'm Charlie"
```

| Method | Usage |
|--------|-------|
| `call(this, arg1, arg2)` | Calls function with given `this` and individual arguments |
| `apply(this, [arg1, arg2])` | Calls function with given `this` and array of arguments |
| `bind(this)` | Returns a new function permanently bound to `this` |

---

## Strict Mode

`"use strict"` enables a restricted variant of JavaScript that catches common mistakes.

```javascript
"use strict";

x = 10; // ❌ Error! Must declare variables

function() {
  "use strict";
  // Strict mode within this function only
}();
```

**Key changes in strict mode:**
- Variables must be declared
- `this` in standalone functions is `undefined`
- Duplicate parameter names are not allowed
- `delete` on variables/functions is not allowed
- Octal syntax is forbidden

---

## The Scope Chain in Practice

```javascript
const global = "global";

function outer() {
  const outerVar = "outer";

  function middle() {
    const middleVar = "middle";

    function inner() {
      const innerVar = "inner";

      console.log(global);    // Found in global scope
      console.log(outerVar);  // Found in outer()
      console.log(middleVar); // Found in middle()
      console.log(innerVar);  // Found in inner()
    }

    inner();
  }

  middle();
}

outer();
```

**Visual Scope Chain:**
```
inner() scope → middle() scope → outer() scope → global scope
```

---

## Common Pitfalls

### Pitfall 1: Loop Variables with `var`

```javascript
for (var i = 0; i < 3; i++) {
  setTimeout(() => console.log(i), 100);
}
// Output: 3, 3, 3 (not 0, 1, 2!)

// Fix: Use let (block-scoped)
for (let i = 0; i < 3; i++) {
  setTimeout(() => console.log(i), 100);
}
// Output: 0, 1, 2
```

### Pitfall 2: `this` in Callbacks

```javascript
const user = {
  name: "Alice",
  friends: ["Bob", "Charlie"],
  showFriends: function() {
    this.friends.forEach(function(friend) {
      console.log(`${this.name} is friends with ${friend}`);
      // ❌ this.name is undefined!
    });
  }
};

// Fix 1: Arrow function (inherits this)
showFriends() {
  this.friends.forEach(friend => {
    console.log(`${this.name} is friends with ${friend}`);
  });
}

// Fix 2: Store this in a variable
showFriends: function() {
  const self = this;
  this.friends.forEach(function(friend) {
    console.log(`${self.name} is friends with ${friend}`);
  });
}
```

### Pitfall 3: Accidental Global Variables

```javascript
function setup() {
  count = 0; // ❌ Forgot let/const/var → creates global variable!
  // In strict mode, this throws an error
}

setup();
console.log(count); // 0 (accessible globally!)
```

---

## Practice Exercises

### Exercise 1: Predict the Output

```javascript
var x = 10;

function foo() {
  console.log(x);
  var x = 20;
}

foo();
```

**Question:** What gets logged and why?

### Exercise 2: Scope Chain Tracing

```javascript
const a = 1;

function one() {
  const b = 2;

  function two() {
    const c = 3;
    console.log(a + b + c);
  }

  two();
}

one();
```

Trace the scope chain for each variable.

### Exercise 3: Fix the `this` Problem

```javascript
const counter = {
  count: 0,
  increment: function() {
    setInterval(function() {
      this.count++;
      console.log(this.count);
    }, 1000);
  }
};
```

The `this.count` is `NaN`. Fix it.

### Exercise 4: Implement `call` Behavior

Without using `.call()`, write code that achieves the same result.

```javascript
function greet(greeting) {
  console.log(`${greeting}, ${this.name}!`);
}

const person = { name: "Alice" };
// Your code here to call greet with person as this
```

### Exercise 5: Hoisting Quiz

What does this code output?

```javascript
console.log(a);
var a = 5;

console.log(b);
let b = 10;

foo();
function foo() { console.log("foo"); }

bar();
var bar = function() { console.log("bar"); };
```

---

## Summary

- **Execution Context** is the environment where code runs (Global or Function)
- **Call Stack** tracks which function is currently executing
- **Scope** determines variable accessibility: Global, Function, or Block
- **`let`/`const`** use block scope; **`var`** uses function scope
- **Lexical scope** means scope is determined by where code is written
- **Hoisting** moves declarations to the top; `var` → `undefined`, `let`/`const` → TDZ
- **`this`** depends on how a function is called, not where it's defined
- **Arrow functions** don't have their own `this`; they inherit from parent
- **`use strict`** prevents common mistakes and silent errors

---

## Next Steps

With scope and execution context understood, you're ready for:
- **Closures** — functions that remember their scope
- **Higher-Order Functions** — functions that operate on other functions
- **Objects and Prototypes** — `this` in object-oriented patterns

Happy coding! 🚀
