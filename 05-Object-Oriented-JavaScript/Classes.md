# Classes in JavaScript

## Overview

Introduced in **ES6 (2015)**, JavaScript classes provide a cleaner, more familiar syntax for creating objects and implementing inheritance. While they look like classes in languages like Java or Python, JavaScript classes are **syntactic sugar** over the existing prototype-based inheritance system. Understanding this distinction is crucial — classes make the code more readable and organized, but the underlying mechanics remain prototypal.

> **Key Insight**: Classes in JavaScript are **first-class citizens** — they are functions under the hood. You can pass them as arguments, return them from functions, and assign them to variables, just like any other function.

---

## Class Declaration

### Basic Syntax

```javascript
class Person {
  constructor(name, age) {
    this.name = name;
    this.age = age;
  }

  greet() {
    return `Hello, I'm ${this.name}`;
  }

  haveBirthday() {
    this.age++;
    return this.age;
  }
}

const alice = new Person("Alice", 30);
console.log(alice.greet());        // "Hello, I'm Alice"
console.log(alice.haveBirthday()); // 31
```

### What Happens Under the Hood

```javascript
// class Person { ... } is roughly equivalent to:
function Person(name, age) {
  this.name = name;
  this.age = age;
}

Person.prototype.greet = function() {
  return `Hello, I'm ${this.name}`;
};

Person.prototype.haveBirthday = function() {
  this.age++;
  return this.age;
};
```

```
Person (function/class)
├─ prototype ──→ Person.prototype
│                  ├─ constructor ──→ Person
│                  ├─ greet()
│                  └─ haveBirthday()
│
alice (instance)
├─ name: "Alice"
├─ age: 30
└─ [[Prototype]] ──→ Person.prototype
```

### Class Expressions

```javascript
// Named class expression
const Person = class NamedPerson {
  constructor(name) {
    this.name = name;
  }
};

// Anonymous class expression
const Animal = class {
  constructor(name) {
    this.name = name;
  }
};

// Class passed as an argument
function createInstance(ClassConstructor, ...args) {
  return new ClassConstructor(...args);
}

const person = createInstance(Person, "Alice");
```

---

## The Constructor

The `constructor` method is called automatically when a new instance is created with `new`. There can only be **one** constructor per class.

```javascript
class Rectangle {
  constructor(width, height) {
    this.width = width;
    this.height = height;
  }
}

const rect = new Rectangle(10, 5);
```

### Default Constructor

If you don't define a constructor, JavaScript provides a default empty one:

```javascript
class Empty {
  // Implicit: constructor() {}
}
```

### Constructor Return Value

```javascript
class Special {
  constructor() {
    this.value = 42;
    // Normally, `this` is returned automatically
    // But you can return a different object:
    return { overridden: true };
  }
}

const s = new Special();
console.log(s); // { overridden: true }
```

> Returning a primitive from the constructor is ignored — `this` is still returned.

---

## Instance Methods

Methods defined inside a class body are automatically added to the prototype.

```javascript
class Calculator {
  constructor(value = 0) {
    this.value = value;
  }

  add(n) {
    this.value += n;
    return this; // Enables method chaining
  }

  subtract(n) {
    this.value -= n;
    return this;
  }

  multiply(n) {
    this.value *= n;
    return this;
  }

  getValue() {
    return this.value;
  }
}

const calc = new Calculator(10);
const result = calc.add(5).subtract(3).multiply(2).getValue();
console.log(result); // 24
```

### Computed Method Names

```javascript
const methodName = "greet";

class Person {
  constructor(name) {
    this.name = name;
  }

  [methodName]() {
    return `Hello, ${this.name}`;
  }

  ["say" + "Bye"]() {
    return `Goodbye, ${this.name}`;
  }
}
```

---

## Static Methods and Properties

Static methods belong to the **class itself**, not to instances. They're utility functions that don't require an instance.

```javascript
class MathUtils {
  static add(a, b) {
    return a + b;
  }

  static multiply(a, b) {
    return a * b;
  }

  static PI = 3.14159; // Static property (ES2022)
}

console.log(MathUtils.add(2, 3));       // 5
console.log(MathUtils.multiply(4, 5));  // 20
console.log(MathUtils.PI);              // 3.14159

// Cannot call on instance
const utils = new MathUtils();
// utils.add(2, 3); // TypeError: utils.add is not a function
```

### Static Factory Methods

```javascript
class User {
  constructor(name, email) {
    this.name = name;
    this.email = email;
    this.createdAt = new Date();
  }

  static fromJSON(json) {
    const data = JSON.parse(json);
    return new User(data.name, data.email);
  }

  static createGuest() {
    return new User("Guest", "guest@example.com");
  }
}

const user = User.fromJSON('{"name":"Alice","email":"alice@example.com"}');
const guest = User.createGuest();
```

### Static Block (ES2022)

```javascript
class Config {
  static settings = {};

  static {
    // Runs once when class is loaded
    this.settings.apiUrl = "https://api.example.com";
    this.settings.timeout = 5000;
    console.log("Config initialized");
  }
}
```

---

## Getters and Setters

Classes support getter and setter methods that look like properties from the outside.

```javascript
class Circle {
  constructor(radius) {
    this.radius = radius;
  }

  get diameter() {
    return this.radius * 2;
  }

  set diameter(value) {
    this.radius = value / 2;
  }

  get area() {
    return Math.PI * this.radius ** 2;
  }
}

const c = new Circle(5);
console.log(c.diameter); // 10 (getter — no parentheses!)
console.log(c.area);     // 78.54...

c.diameter = 20;         // setter
console.log(c.radius);   // 10
```

### Validation in Setters

```javascript
class Temperature {
  #celsius; // Private field (see next section)

  constructor(celsius) {
    this.celsius = celsius;
  }

  get celsius() {
    return this.#celsius;
  }

  set celsius(value) {
    if (value < -273.15) {
      throw new Error("Temperature below absolute zero is not possible");
    }
    this.#celsius = value;
  }

  get fahrenheit() {
    return (this.#celsius * 9/5) + 32;
  }

  set fahrenheit(value) {
    this.celsius = (value - 32) * 5/9;
  }
}
```

---

## Private Fields and Methods (ES2022)

JavaScript now supports true privacy with the `#` prefix. Private members are **not accessible** from outside the class.

```javascript
class BankAccount {
  #balance;        // Private field
  #transactions;   // Private field

  static #accountCounter = 0; // Private static field

  constructor(initialBalance) {
    this.#balance = initialBalance;
    this.#transactions = [];
    BankAccount.#accountCounter++;
  }

  deposit(amount) {
    if (amount <= 0) {
      throw new Error("Amount must be positive");
    }
    this.#balance += amount;
    this.#transactions.push({ type: "deposit", amount });
    return this.#balance;
  }

  withdraw(amount) {
    if (amount > this.#balance) {
      throw new Error("Insufficient funds");
    }
    this.#balance -= amount;
    this.#transactions.push({ type: "withdrawal", amount });
    return this.#balance;
  }

  getBalance() {
    return this.#balance;
  }

  // Private method
  #logTransaction(transaction) {
    console.log("Transaction:", transaction);
  }

  // Private static method
  static #getNextId() {
    return BankAccount.#accountCounter + 1;
  }
}

const account = new BankAccount(1000);
account.deposit(500);
console.log(account.getBalance()); // 1500

// console.log(account.#balance); // SyntaxError: Private field must be declared
```

### Private vs Closure-Based Privacy

```javascript
// Closure-based (pre-ES2022)
function createCounter() {
  let count = 0; // Truly private via closure
  return {
    increment() { return ++count; },
    decrement() { return --count; }
  };
}

// Class with private fields
class Counter {
  #count = 0;

  increment() {
    return ++this.#count;
  }

  decrement() {
    return --this.#count;
  }
}
```

| Approach | Pros | Cons |
|----------|------|------|
| Closure | Works in all JS versions | Separate instance functions |
| `#private` | Shared prototype methods | ES2022+ only |

---

## Class Fields (Public)

You can declare public fields directly in the class body (ES2022):

```javascript
class User {
  // Public field with default value
  role = "user";

  // Public field initialized in constructor
  createdAt;

  constructor(name) {
    this.name = name;
    this.createdAt = new Date();
  }
}

const u = new User("Alice");
console.log(u.role);      // "user"
console.log(u.createdAt); // Date object
```

### Arrow Functions as Fields (Auto-Binding)

```javascript
class Button {
  constructor(label) {
    this.label = label;
  }

  // Regular method — loses `this` when passed as callback
  handleClickRegular() {
    console.log(this.label);
  }

  // Arrow function field — `this` is bound permanently
  handleClick = () => {
    console.log(this.label);
  };
}

const btn = new Button("Submit");

// Regular method loses context
setTimeout(btn.handleClickRegular, 100); // undefined (or error in strict mode)

// Arrow function preserves context
setTimeout(btn.handleClick, 100); // "Submit"
```

> ⚠️ Arrow function fields create a new function per instance, increasing memory usage. Prefer binding in the constructor or using `.bind()` for performance-critical code.

---

## Class Inheritance

Classes support single inheritance using the `extends` keyword.

```javascript
class Animal {
  constructor(name) {
    this.name = name;
  }

  speak() {
    return `${this.name} makes a sound`;
  }
}

class Dog extends Animal {
  constructor(name, breed) {
    super(name); // Must call before using `this`
    this.breed = breed;
  }

  speak() {
    return `${this.name} barks`;
  }

  fetch() {
    return `${this.name} is fetching`;
  }
}

const rex = new Dog("Rex", "Labrador");
console.log(rex.speak()); // "Rex barks"
console.log(rex.fetch()); // "Rex is fetching"
```

### The `super` Keyword

```javascript
class Employee {
  constructor(name, salary) {
    this.name = name;
    this.salary = salary;
  }

  getDetails() {
    return `${this.name}: $${this.salary}`;
  }
}

class Manager extends Employee {
  constructor(name, salary, department) {
    super(name, salary);     // Call parent constructor
    this.department = department;
  }

  getDetails() {
    // Call parent method
    return `${super.getDetails()} (Manager of ${this.department})`;
  }
}
```

> We'll explore inheritance in much more detail in the **Inheritance** tutorial.

---

## Class vs Constructor Function

| Feature | Constructor Function | ES6 Class |
|---------|---------------------|-----------|
| Syntax | `function Person() {}` | `class Person {}` |
| Methods | `Person.prototype.greet = ...` | Defined in class body |
| Static | `Person.staticMethod = ...` | `static method() {}` |
| Private | Closures or WeakMap | `#private` |
| Hoisting | Hoisted | Not hoisted (TDZ) |
| Callable | `Person()` works | `Person()` throws TypeError |
| typeof | `"function"` | `"function"` |

```javascript
// Constructor function
function PersonOld(name) {
  this.name = name;
}
PersonOld.prototype.greet = function() {
  return `Hello, ${this.name}`;
};

// Equivalent class
class PersonNew {
  constructor(name) {
    this.name = name;
  }

  greet() {
    return `Hello, ${this.name}`;
  }
}
```

---

## Common Mistakes

### Mistake 1: Calling Class Without `new`

```javascript
class Person {
  constructor(name) {
    this.name = name;
  }
}

// ❌ Classes throw when called without new
const p = Person("Alice"); // TypeError: Class constructor cannot be invoked without 'new'

// ✅ Always use new
const p2 = new Person("Alice");
```

### Mistake 2: Using `this` Before `super()`

```javascript
class Parent {
  constructor(value) {
    this.value = value;
  }
}

class Child extends Parent {
  constructor(value, extra) {
    // ❌ Cannot use `this` before super()
    this.extra = extra; // ReferenceError!
    super(value);
  }
}

// ✅ Call super() first
class Child extends Parent {
  constructor(value, extra) {
    super(value);
    this.extra = extra;
  }
}
```

### Mistake 3: Forgetting That Methods Are on Prototype

```javascript
class Counter {
  count = 0;

  increment() {
    this.count++;
  }
}

const c1 = new Counter();
const c2 = new Counter();

console.log(c1.increment === c2.increment); // true (shared)

// But arrow function fields are per-instance
class Counter2 {
  count = 0;
  increment = () => { this.count++; };
}

const c3 = new Counter2();
const c4 = new Counter2();
console.log(c3.increment === c4.increment); // false (separate functions)
```

### Mistake 4: Trying to Access Private Fields from Outside

```javascript
class Secret {
  #password;

  constructor(password) {
    this.#password = password;
  }
}

const s = new Secret("12345");

// ❌ SyntaxError — private fields are truly private
// console.log(s.#password);

// ✅ Use a getter if needed
class Secret {
  #password;

  constructor(password) {
    this.#password = password;
  }

  verify(guess) {
    return guess === this.#password;
  }
}
```

### Mistake 5: Class Hoisting Confusion

```javascript
// ❌ Classes are NOT hoisted
const p = new Person(); // ReferenceError

class Person {
  constructor(name) {
    this.name = name;
  }
}

// ✅ Function declarations ARE hoisted
const p2 = new Person2(); // Works!

function Person2(name) {
  this.name = name;
}
```

---

## Practice Exercises

### Exercise 1: Build a Stack Class

Implement a Stack class with private fields:

```javascript
class Stack {
  // Use #items as private array
  // push(item), pop(), peek(), isEmpty(), size()
}

const stack = new Stack();
stack.push(10);
stack.push(20);
console.log(stack.peek());    // 20
console.log(stack.pop());     // 20
console.log(stack.size());    // 1
// stack.#items should not be accessible
```

### Exercise 2: Static Factory Pattern

Create a `Shape` class with static factory methods:

```javascript
class Shape {
  static createCircle(radius) {
    return new Circle(radius);
  }

  static createRectangle(width, height) {
    return new Rectangle(width, height);
  }
}

class Circle extends Shape {
  // area(), perimeter()
}

class Rectangle extends Shape {
  // area(), perimeter()
}

const c = Shape.createCircle(5);
const r = Shape.createRectangle(4, 6);
```

### Exercise 3: Immutable Class

Create an immutable `Point` class where methods return new instances:

```javascript
class Point {
  constructor(x, y) {
    // Should be immutable
  }

  move(dx, dy) {
    // Return new Point, don't modify this
  }

  // Getters for x and y
}

const p1 = new Point(0, 0);
const p2 = p1.move(5, 10);
console.log(p1.x, p1.y); // 0, 0 (unchanged)
console.log(p2.x, p2.y); // 5, 10
```

### Exercise 4: Event Emitter Class

Implement a simple EventEmitter using classes:

```javascript
class EventEmitter {
  // on(event, listener) — subscribe
  // off(event, listener) — unsubscribe
  // emit(event, ...args) — trigger event
  // once(event, listener) — subscribe for one trigger only
}

const emitter = new EventEmitter();
emitter.on("message", (msg) => console.log(msg));
emitter.emit("message", "Hello!"); // "Hello!"
emitter.emit("message", "Again!"); // "Again!"
```

### Exercise 5: Builder Pattern with Classes

Implement a `QueryBuilder` class using method chaining:

```javascript
class QueryBuilder {
  // select(fields)
  // from(table)
  // where(condition)
  // orderBy(field, direction)
  // build() — returns the query string
}

const query = new QueryBuilder()
  .select(["name", "email"])
  .from("users")
  .where("age > 18")
  .orderBy("name", "asc")
  .build();

console.log(query);
// "SELECT name, email FROM users WHERE age > 18 ORDER BY name asc"
```

---

## Summary

- ES6 **classes** are syntactic sugar over JavaScript's prototype system
- Classes are **not hoisted** — declare them before use
- The `constructor` is called when using `new`
- **Instance methods** are shared via the prototype (efficient)
- **Static methods** belong to the class, not instances
- **Getters/setters** look like properties but execute code
- **Private fields/methods** (`#name`) provide true encapsulation
- **Public fields** can have default values in the class body
- Arrow function fields auto-bind `this` but create per-instance functions
- `extends` creates inheritance; `super()` calls the parent constructor
- Classes **must** be called with `new` — calling them as functions throws
- In derived classes, `super()` must be called before accessing `this`

---

## Next Steps

Now that you understand classes:
- **Inheritance** — master extends, super, method overriding, and composition
- **Prototypes** — understand what happens under the hood
- **OOP Concepts** — review design principles with your class knowledge

Happy coding! 🚀
