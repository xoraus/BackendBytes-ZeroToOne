# Objects in JavaScript

## Overview

**Objects** are the foundation of JavaScript. Almost everything in JavaScript is an object — arrays, functions, dates, and even primitive wrappers. Understanding objects deeply is essential before diving into Object-Oriented Programming. This tutorial covers everything from basic object creation to advanced property manipulation, ensuring you have a rock-solid grasp of JavaScript's most important data structure.

> **Key Insight**: In JavaScript, objects are collections of **key-value pairs** where keys are strings (or Symbols) and values can be any data type, including other objects and functions.

---

## Creating Objects

### 1. Object Literal (Most Common)

```javascript
const person = {
  firstName: "Alice",
  lastName: "Johnson",
  age: 30,
  isEmployed: true,
  greet: function() {
    return `Hello, I'm ${this.firstName}`;
  }
};

console.log(person.greet()); // "Hello, I'm Alice"
```

**Shorthand Properties** (ES6):
```javascript
const firstName = "Alice";
const age = 30;

const person = { firstName, age }; // { firstName: "Alice", age: 30 }
```

**Shorthand Methods** (ES6):
```javascript
const person = {
  firstName: "Alice",
  greet() {  // No "function" keyword needed
    return `Hello, I'm ${this.firstName}`;
  }
};
```

**Computed Property Names** (ES6):
```javascript
const key = "dynamicKey";
const obj = {
  [key]: "value",           // "dynamicKey": "value"
  ["prop" + 1]: "hello",   // "prop1": "hello"
  [2 + 2]: "four"          // "4": "four"
};
```

### 2. Object Constructor

```javascript
const obj = new Object();
obj.name = "Alice";
obj.age = 30;
```

> **Avoid this pattern**. Object literals are shorter, clearer, and more performant.

### 3. Object.create()

Creates an object with a specified prototype:

```javascript
const personProto = {
  greet() {
    return `Hello, I'm ${this.name}`;
  }
};

const alice = Object.create(personProto);
alice.name = "Alice";
alice.age = 30;

console.log(alice.greet()); // "Hello, I'm Alice"
```

### 4. Factory Functions

```javascript
function createPerson(name, age) {
  return {
    name,
    age,
    greet() {
      return `Hello, I'm ${this.name}`;
    }
  };
}

const bob = createPerson("Bob", 25);
console.log(bob.greet()); // "Hello, I'm Bob"
```

### 5. Constructor Functions

```javascript
function Person(name, age) {
  this.name = name;
  this.age = age;
  this.greet = function() {
    return `Hello, I'm ${this.name}`;
  };
}

const charlie = new Person("Charlie", 35);
console.log(charlie.greet()); // "Hello, I'm Charlie"
```

> We'll explore constructor functions and `new` in detail in the **Prototypes** tutorial.

---

## Accessing and Modifying Properties

### Dot Notation

```javascript
const person = { name: "Alice", age: 30 };

console.log(person.name);  // "Alice"
person.age = 31;
```

### Bracket Notation

Required when:
- Property name has spaces or special characters
- Property name is stored in a variable
- Property name is a number

```javascript
const person = {
  "first name": "Alice",
  123: "numeric key"
};

console.log(person["first name"]); // "Alice"
console.log(person[123]);          // "numeric key"

const prop = "first name";
console.log(person[prop]);         // "Alice"
```

### Property Existence Check

```javascript
const person = { name: "Alice", age: 0 };

// Using in operator (checks own AND inherited properties)
console.log("name" in person);     // true
console.log("toString" in person); // true (inherited from Object.prototype)

// Using hasOwnProperty (checks OWN properties only)
console.log(person.hasOwnProperty("name"));     // true
console.log(person.hasOwnProperty("toString")); // false

// Common mistake with falsy values
console.log(person.age ? "Has age" : "No age");        // "No age" (wrong!)
console.log("age" in person ? "Has age" : "No age");   // "Has age" (correct)
```

### Deleting Properties

```javascript
const person = { name: "Alice", age: 30, temp: "value" };

delete person.temp;
console.log(person.temp); // undefined
console.log("temp" in person); // false
```

> `delete` only works on own properties. It does NOT affect inherited properties.

---

## Property Descriptors

Every property in JavaScript has a **property descriptor** that controls its behavior.

### Getting Property Descriptors

```javascript
const person = { name: "Alice" };

const descriptor = Object.getOwnPropertyDescriptor(person, "name");
console.log(descriptor);
// {
//   value: "Alice",
//   writable: true,
//   enumerable: true,
//   configurable: true
// }
```

### The Four Attributes

| Attribute | Description | Default |
|-----------|-------------|---------|
| `value` | The property's value | `undefined` |
| `writable` | Can the value be changed? | `true` |
| `enumerable` | Does it show up in `for...in` and `Object.keys()`? | `true` |
| `configurable` | Can the descriptor be changed or the property deleted? | `true` |

### Creating Properties with Descriptors

```javascript
const person = {};

Object.defineProperty(person, "name", {
  value: "Alice",
  writable: false,      // Cannot be changed
  enumerable: true,     // Shows in loops
  configurable: false   // Cannot be deleted or reconfigured
});

person.name = "Bob"; // Silently fails in non-strict mode
console.log(person.name); // "Alice"
```

### Multiple Properties at Once

```javascript
const person = {};

Object.defineProperties(person, {
  firstName: {
    value: "Alice",
    writable: true,
    enumerable: true
  },
  lastName: {
    value: "Johnson",
    writable: true,
    enumerable: true
  },
  fullName: {
    get() {
      return `${this.firstName} ${this.lastName}`;
    },
    enumerable: true
  }
});
```

---

## Getters and Setters

Getters and setters let you define computed properties that look like regular properties.

### Basic Getter

```javascript
const person = {
  firstName: "Alice",
  lastName: "Johnson",

  get fullName() {
    return `${this.firstName} ${this.lastName}`;
  }
};

console.log(person.fullName); // "Alice Johnson" (no parentheses!)
```

### Getter + Setter

```javascript
const person = {
  firstName: "Alice",
  lastName: "Johnson",

  get fullName() {
    return `${this.firstName} ${this.lastName}`;
  },

  set fullName(value) {
    [this.firstName, this.lastName] = value.split(" ");
  }
};

person.fullName = "Bob Smith";
console.log(person.firstName); // "Bob"
console.log(person.lastName);  // "Smith"
```

### Validation in Setters

```javascript
const person = {
  _age: 0,

  get age() {
    return this._age;
  },

  set age(value) {
    if (value < 0 || value > 150) {
      throw new Error("Invalid age");
    }
    this._age = value;
  }
};

person.age = 30;      // OK
person.age = -5;      // Error: Invalid age
```

> The `_age` convention indicates a "private" property. In modern JavaScript, use `#private` fields instead (covered in the **Classes** tutorial).

---

## Object Methods and Utilities

### Object.keys(), Object.values(), Object.entries()

```javascript
const person = { name: "Alice", age: 30, city: "NYC" };

Object.keys(person);     // ["name", "age", "city"]
Object.values(person);   // ["Alice", 30, "NYC"]
Object.entries(person);  // [["name", "Alice"], ["age", 30], ["city", "NYC"]]
```

### Iterating Over Objects

```javascript
const person = { name: "Alice", age: 30 };

// for...in (includes inherited enumerable properties)
for (const key in person) {
  if (person.hasOwnProperty(key)) {
    console.log(`${key}: ${person[key]}`);
  }
}

// Object.entries with for...of (recommended)
for (const [key, value] of Object.entries(person)) {
  console.log(`${key}: ${value}`);
}

// forEach with Object.entries
Object.entries(person).forEach(([key, value]) => {
  console.log(`${key}: ${value}`);
});
```

### Object.assign() — Shallow Copy / Merge

```javascript
// Copy
const original = { a: 1, b: 2 };
const copy = Object.assign({}, original);

// Merge multiple objects
const defaults = { theme: "light", fontSize: 14 };
const userPrefs = { fontSize: 16 };
const settings = Object.assign({}, defaults, userPrefs);
// { theme: "light", fontSize: 16 } — userPrefs overrides defaults

// ⚠️ Shallow only!
const obj = { nested: { a: 1 } };
const shallow = Object.assign({}, obj);
shallow.nested.a = 2;
console.log(obj.nested.a); // 2 (also changed!)
```

### Object Spread (ES2018) — Preferred over Object.assign

```javascript
const original = { a: 1, b: 2 };
const copy = { ...original };

// Merge
const merged = { ...defaults, ...userPrefs };

// Add/override properties
const extended = { ...original, c: 3, a: 10 };
// { a: 10, b: 2, c: 3 }
```

### Object.freeze() and Object.seal()

```javascript
// Seal: can't add/remove properties, but can modify existing ones
const sealed = { name: "Alice" };
Object.seal(sealed);
sealed.name = "Bob";   // ✅ Works
sealed.age = 30;       // ❌ Silently ignored
delete sealed.name;    // ❌ Silently ignored

// Freeze: can't do anything — fully immutable (shallowly)
const frozen = { name: "Alice", nested: { a: 1 } };
Object.freeze(frozen);
frozen.name = "Bob";   // ❌ Ignored
frozen.age = 30;       // ❌ Ignored
delete frozen.name;    // ❌ Ignored
frozen.nested.a = 2;   // ✅ Works! (shallow freeze)
```

| Method | Add | Delete | Modify | Reconfigure Descriptor |
|--------|-----|--------|--------|----------------------|
| Normal | ✅ | ✅ | ✅ | ✅ |
| `Object.seal()` | ❌ | ❌ | ✅ | ❌ |
| `Object.freeze()` | ❌ | ❌ | ❌ | ❌ |

### Object.fromEntries()

```javascript
const entries = [["name", "Alice"], ["age", 30]];
const obj = Object.fromEntries(entries);
// { name: "Alice", age: 30 }

// Transform an object
const prices = { apple: 1.5, banana: 0.8 };
const doubled = Object.fromEntries(
  Object.entries(prices).map(([k, v]) => [k, v * 2])
);
// { apple: 3, banana: 1.6 }
```

---

## Deep vs Shallow Copies

### The Problem

```javascript
const original = {
  name: "Alice",
  address: {
    city: "NYC",
    zip: "10001"
  }
};

const shallow = { ...original };
shallow.address.city = "LA";

console.log(original.address.city); // "LA" 😱
```

### Solutions for Deep Copy

**1. JSON Method (Limited)**
```javascript
const deep = JSON.parse(JSON.stringify(original));
// Loses: functions, undefined, Dates, Maps, Sets, circular references
```

**2. Structured Clone (Modern)**
```javascript
const deep = structuredClone(original);
// Better: handles Dates, Maps, Sets, ArrayBuffers
// Still loses: functions, DOM nodes, circular refs throw
```

**3. Manual Recursive Function**
```javascript
function deepClone(obj) {
  if (obj === null || typeof obj !== "object") return obj;
  if (obj instanceof Date) return new Date(obj);
  if (obj instanceof Array) return obj.map(item => deepClone(item));
  if (obj instanceof Object) {
    return Object.fromEntries(
      Object.entries(obj).map(([k, v]) => [k, deepClone(v)])
    );
  }
}
```

---

## Destructuring Objects

### Basic Destructuring

```javascript
const person = { firstName: "Alice", lastName: "Johnson", age: 30 };

const { firstName, age } = person;
console.log(firstName); // "Alice"
console.log(age);       // 30
```

### Renaming

```javascript
const { firstName: fName, lastName: lName } = person;
console.log(fName); // "Alice"
```

### Default Values

```javascript
const { firstName, middleName = "N/A" } = person;
console.log(middleName); // "N/A"
```

### Nested Destructuring

```javascript
const user = {
  name: "Alice",
  address: {
    city: "NYC",
    country: "USA"
  }
};

const { address: { city, country } } = user;
console.log(city); // "NYC"
```

### Rest in Destructuring

```javascript
const { firstName, ...rest } = person;
console.log(rest); // { lastName: "Johnson", age: 30 }
```

### Function Parameter Destructuring

```javascript
function greet({ firstName, lastName = "Doe" }) {
  return `Hello, ${firstName} ${lastName}`;
}

greet({ firstName: "Alice" }); // "Hello, Alice Doe"
```

---

## Common Mistakes

### Mistake 1: Modifying Objects in Functions

```javascript
// ❌ Mutating the original
function addRole(user) {
  user.role = "admin"; // Mutates the original!
  return user;
}

// ✅ Return a new object
function addRole(user) {
  return { ...user, role: "admin" };
}
```

### Mistake 2: Using Dot Notation with Dynamic Keys

```javascript
const key = "name";
const person = {};

// ❌ Creates a property literally named "key"
person.key = "Alice";

// ✅ Uses the value of key variable
person[key] = "Alice";
```

### Mistake 3: Expecting Object Comparison to Work

```javascript
const a = { x: 1 };
const b = { x: 1 };

// ❌ Objects are compared by reference, not value
console.log(a === b); // false

// ✅ Compare values manually
console.log(JSON.stringify(a) === JSON.stringify(b)); // true (limited)
```

### Mistake 4: Forgetting `hasOwnProperty` in `for...in`

```javascript
// ❌ Iterates over inherited properties too
for (const key in person) {
  console.log(key); // Might include "toString", "valueOf", etc.
}

// ✅ Always check hasOwnProperty
for (const key in person) {
  if (person.hasOwnProperty(key)) {
    console.log(key);
  }
}

// ✅ Better: use Object.keys()
Object.keys(person).forEach(key => console.log(key));
```

### Mistake 5: Using `delete` in Hot Paths

```javascript
// ❌ delete is slow and can deoptimize the object
function process(obj) {
  delete obj.temp;
  return obj;
}

// ✅ Set to undefined or filter instead
function process(obj) {
  const { temp, ...rest } = obj;
  return rest; // Creates new object without temp
}
```

---

## Practice Exercises

### Exercise 1: Create a Config Object

Create a config object with getters/setters that validates values:

```javascript
const config = {
  // Implement port (number, 1-65535)
  // Implement host (string, non-empty)
  // Implement debug (boolean)
};

config.port = 3000;     // OK
config.port = 99999;    // Error: Invalid port
```

### Exercise 2: Deep Merge

Write a function that deeply merges two objects:

```javascript
function deepMerge(target, source) {
  // Your code
}

const a = { user: { name: "Alice", settings: { theme: "dark" } } };
const b = { user: { settings: { fontSize: 14 } } };

deepMerge(a, b);
// { user: { name: "Alice", settings: { theme: "dark", fontSize: 14 } } }
```

### Exercise 3: Object Mapper

Write a function that transforms object keys based on a mapping:

```javascript
function mapKeys(obj, keyMap) {
  // keyMap: { oldKey: "newKey" }
  // Your code
}

const data = { first_name: "Alice", last_name: "Johnson" };
const result = mapKeys(data, { first_name: "firstName", last_name: "lastName" });
// { firstName: "Alice", lastName: "Johnson" }
```

### Exercise 4: Freeze Deep

Write a function that deeply freezes an object:

```javascript
function deepFreeze(obj) {
  // Your code: recursively freeze all nested objects
}

const obj = { user: { name: "Alice", address: { city: "NYC" } } };
deepFreeze(obj);
obj.user.address.city = "LA"; // Should silently fail or throw
```

### Exercise 5: Count Properties

Write a function that counts all own properties, including non-enumerable ones:

```javascript
function countAllProperties(obj) {
  // Your code
}

const obj = { a: 1 };
Object.defineProperty(obj, "b", { value: 2, enumerable: false });
countAllProperties(obj); // 2
```

---

## Summary

- Objects are created with `{}` literals, `new Object()`, `Object.create()`, factory functions, or constructor functions
- Properties are accessed with **dot notation** (`obj.prop`) or **bracket notation** (`obj["prop"]`)
- **Property descriptors** control `value`, `writable`, `enumerable`, and `configurable`
- **Getters and setters** let you define computed properties that behave like regular properties
- `Object.assign()` and the **spread operator** create shallow copies
- `Object.freeze()` prevents all modifications; `Object.seal()` prevents adding/removing but allows modifying
- **Destructuring** extracts values cleanly: `const { a, b } = obj`
- Objects are compared **by reference**, not by value
- Always use `hasOwnProperty` with `for...in` loops, or prefer `Object.keys()` / `Object.entries()`

---

## Next Steps

Now that you understand objects deeply:
- **OOP Concepts** — learn the four pillars of OOP and how they apply in JavaScript
- **Prototypes** — understand JavaScript's inheritance mechanism
- **Classes** — the modern syntax for object-oriented patterns
- **Inheritance** — building object hierarchies in JavaScript

Happy coding! 🚀
