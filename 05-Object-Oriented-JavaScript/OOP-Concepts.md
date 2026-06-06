# OOP Concepts

## Overview

**Object-Oriented Programming (OOP)** is a programming paradigm centered around objects rather than functions. JavaScript supports OOP through its **prototypal inheritance** model, and since ES6, through **class syntax** that provides a more familiar interface. Understanding OOP concepts is crucial for building scalable, maintainable applications — from simple UI components to complex backend systems.

> **Key Insight**: JavaScript is a **multi-paradigm** language. While it supports OOP, it also embraces functional programming. The best JavaScript code often combines both paradigms.

---

## What is Object-Oriented Programming?

OOP organizes code into **objects** — self-contained units that combine:
- **Data** (properties/attributes)
- **Behavior** (methods/functions)

Instead of writing a series of procedural steps, you model your program as interacting objects that represent real-world or conceptual entities.

### Procedural vs OOP

**Procedural Approach:**
```javascript
// Data
const userName = "Alice";
const userEmail = "alice@example.com";

// Functions operating on data
function sendEmail(email, message) {
  console.log(`Sending to ${email}: ${message}`);
}

sendEmail(userEmail, "Welcome!");
```

**OOP Approach:**
```javascript
class User {
  constructor(name, email) {
    this.name = name;
    this.email = email;
  }

  sendMessage(message) {
    console.log(`Sending to ${this.email}: ${message}`);
  }
}

const alice = new User("Alice", "alice@example.com");
alice.sendMessage("Welcome!");
```

> The OOP version encapsulates data and behavior together, making the code more organized and reusable.

---

## The Four Pillars of OOP

### 1. Encapsulation

**Encapsulation** bundles data and the methods that operate on that data within a single unit (object), and restricts direct access to some of the object's components.

```javascript
class BankAccount {
  #balance; // Private field

  constructor(initialBalance) {
    this.#balance = initialBalance;
  }

  deposit(amount) {
    if (amount <= 0) {
      throw new Error("Deposit amount must be positive");
    }
    this.#balance += amount;
    return this.#balance;
  }

  withdraw(amount) {
    if (amount > this.#balance) {
      throw new Error("Insufficient funds");
    }
    this.#balance -= amount;
    return this.#balance;
  }

  getBalance() {
    return this.#balance;
  }
}

const account = new BankAccount(1000);
account.deposit(500);
console.log(account.getBalance()); // 1500

// account.#balance is not accessible from outside!
```

**Benefits of Encapsulation:**
- **Data hiding** — internal state is protected
- **Validation** — data can be validated before modification
- **Flexibility** — internal implementation can change without affecting consumers

### 2. Abstraction

**Abstraction** hides complex implementation details and shows only the essential features. It reduces complexity by providing a simplified interface.

```javascript
class Car {
  #engineRunning = false;
  #fuelLevel = 100;

  start() {
    if (this.#fuelLevel <= 0) {
      console.log("Cannot start: no fuel");
      return;
    }
    this.#engineRunning = true;
    this.#igniteFuel();
    this.#engageStarter();
    console.log("Engine started");
  }

  stop() {
    this.#engineRunning = false;
    console.log("Engine stopped");
  }

  // Private implementation details — users don't need to know
  #igniteFuel() {
    // Complex ignition logic
  }

  #engageStarter() {
    // Complex starter motor logic
  }
}

const myCar = new Car();
myCar.start(); // Simple interface — no need to know how the engine works
```

**Real-World Analogy:**
You drive a car using the steering wheel, pedals, and gear stick. You don't need to understand the internal combustion engine, transmission system, or electrical circuits.

### 3. Inheritance

**Inheritance** allows a class to inherit properties and methods from another class, promoting code reuse and establishing a natural hierarchy.

```javascript
class Animal {
  constructor(name) {
    this.name = name;
  }

  eat() {
    console.log(`${this.name} is eating`);
  }

  sleep() {
    console.log(`${this.name} is sleeping`);
  }
}

class Dog extends Animal {
  bark() {
    console.log(`${this.name} says: Woof!`);
  }
}

class Cat extends Animal {
  meow() {
    console.log(`${this.name} says: Meow!`);
  }
}

const dog = new Dog("Rex");
dog.eat();   // Inherited from Animal
dog.bark();  // Defined in Dog

const cat = new Cat("Whiskers");
cat.eat();   // Inherited from Animal
cat.meow();  // Defined in Cat
```

> We'll explore inheritance in depth in the **Inheritance** tutorial.

### 4. Polymorphism

**Polymorphism** means "many forms." Objects of different classes can be treated as objects of a common parent class, and they can respond to the same method call in different ways.

```javascript
class Shape {
  area() {
    throw new Error("Subclasses must implement area()");
  }

  describe() {
    return `A shape with area ${this.area()}`;
  }
}

class Rectangle extends Shape {
  constructor(width, height) {
    super();
    this.width = width;
    this.height = height;
  }

  area() {
    return this.width * this.height;
  }
}

class Circle extends Shape {
  constructor(radius) {
    super();
    this.radius = radius;
  }

  area() {
    return Math.PI * this.radius ** 2;
  }
}

// Polymorphism in action
const shapes = [new Rectangle(10, 5), new Circle(7)];

shapes.forEach(shape => {
  console.log(shape.describe()); // Works for both Rectangle and Circle!
});
```

**Method Overriding** is a form of polymorphism:
```javascript
class Notification {
  send(message) {
    console.log(`Sending: ${message}`);
  }
}

class EmailNotification extends Notification {
  send(message) {
    console.log(`Sending email: ${message}`);
  }
}

class SMSNotification extends Notification {
  send(message) {
    console.log(`Sending SMS: ${message}`);
  }
}

function notifyUser(notification, message) {
  notification.send(message); // Same call, different behavior
}

notifyUser(new EmailNotification(), "Hello!"); // "Sending email: Hello!"
notifyUser(new SMSNotification(), "Hello!");   // "Sending SMS: Hello!"
```

---

## OOP in JavaScript: Prototypal vs Classical

### Classical OOP (Java, C++)

```
Classes are blueprints → Objects are instances
     Class: Animal
        ↓
   Instance: dog
```

- Classes are defined first
- Objects are created from classes
- Inheritance is class-based

### Prototypal OOP (JavaScript)

```
Objects link to other objects (prototypes)
     dog → Animal.prototype → Object.prototype
```

- Objects can be created directly
- Objects inherit from other objects (prototypes)
- Classes are syntactic sugar over prototypes

```javascript
// Prototypal approach
const animalPrototype = {
  eat() {
    console.log(`${this.name} is eating`);
  }
};

const dog = Object.create(animalPrototype);
dog.name = "Rex";
dog.eat(); // "Rex is eating"

// Class syntax (sugar over prototypes)
class Animal {
  eat() {
    console.log(`${this.name} is eating`);
  }
}

const cat = new Animal();
cat.name = "Whiskers";
cat.eat(); // "Whiskers is eating"
```

> **ES6 Classes** provide a cleaner, more familiar syntax, but under the hood, JavaScript still uses prototypal inheritance.

---

## SOLID Principles

SOLID is a set of five design principles for writing maintainable and scalable OOP code.

### S — Single Responsibility Principle

> A class should have only one reason to change.

```javascript
// ❌ Violates SRP — handles user data AND email AND logging
class UserManager {
  createUser(data) { /* ... */ }
  sendWelcomeEmail(user) { /* ... */ }
  logActivity(action) { /* ... */ }
}

// ✅ Each class has one responsibility
class UserService {
  createUser(data) { /* ... */ }
}

class EmailService {
  sendWelcomeEmail(user) { /* ... */ }
}

class Logger {
  logActivity(action) { /* ... */ }
}
```

### O — Open/Closed Principle

> Classes should be open for extension but closed for modification.

```javascript
// ❌ Violates OCP — must modify PaymentProcessor for each new method
class PaymentProcessor {
  process(type, amount) {
    if (type === "credit") {
      // Process credit card
    } else if (type === "paypal") {
      // Process PayPal
    }
  }
}

// ✅ Extend with new classes, don't modify existing ones
class PaymentProcessor {
  process(paymentMethod, amount) {
    return paymentMethod.pay(amount);
  }
}

class CreditCardPayment {
  pay(amount) { /* ... */ }
}

class PayPalPayment {
  pay(amount) { /* ... */ }
}
```

### L — Liskov Substitution Principle

> Objects of a superclass should be replaceable with objects of a subclass without breaking the program.

```javascript
// ❌ Violates LSP — Penguin can't fly but Bird says it can
class Bird {
  fly() { /* ... */ }
}

class Penguin extends Bird {
  fly() {
    throw new Error("Penguins can't fly!");
  }
}

// ✅ Better design
class Bird {
  move() { /* ... */ }
}

class FlyingBird extends Bird {
  fly() { /* ... */ }
}

class Penguin extends Bird {
  move() {
    this.swim();
  }
}
```

### I — Interface Segregation Principle

> Clients should not be forced to depend on methods they don't use.

```javascript
// ❌ Violates ISP — Printer is forced to implement scan()
class MultiFunctionDevice {
  print() {}
  scan() {}
  fax() {}
}

class Printer extends MultiFunctionDevice {
  scan() {
    throw new Error("I don't scan!");
  }
}

// ✅ Split into smaller interfaces
class Printer {
  print() {}
}

class Scanner {
  scan() {}
}

class AllInOnePrinter {
  print() {}
  scan() {}
}
```

### D — Dependency Inversion Principle

> Depend on abstractions, not concrete implementations.

```javascript
// ❌ Violates DIP — UserService depends directly on MySQLDatabase
class UserService {
  constructor() {
    this.db = new MySQLDatabase();
  }
}

// ✅ Depends on abstraction (interface)
class UserService {
  constructor(database) {
    this.db = database; // Could be MySQL, PostgreSQL, MongoDB...
  }
}
```

---

## When to Use OOP in JavaScript

### Good Use Cases

| Scenario | Why OOP Fits |
|----------|-------------|
| UI Components | Each component has state + behavior |
| Game Entities | Player, Enemy, Item — natural object mapping |
| API Models | User, Order, Product — data + validation logic |
| Streaming Data | Connections with lifecycle management |
| Domain Models | Business entities with rules and relationships |

### When to Prefer Functional Programming

| Scenario | Why FP Fits Better |
|----------|-------------------|
| Data Transformations | Pipeline of pure functions |
| Event Handling | Composable handlers |
| Utility Functions | Stateless, reusable logic |
| Configuration | Immutable data structures |

> **Best Practice**: JavaScript shines when you combine both paradigms. Use OOP for entities with state and behavior, and FP for data transformations and utilities.

---

## Common Mistakes

### Mistake 1: God Objects

```javascript
// ❌ God Object — does everything
class App {
  constructor() {
    this.users = [];
    this.products = [];
    this.orders = [];
    this.config = {};
  }

  createUser() { /* ... */ }
  deleteUser() { /* ... */ }
  createProduct() { /* ... */ }
  processOrder() { /* ... */ }
  sendEmail() { /* ... */ }
  connectDatabase() { /* ... */ }
}

// ✅ Separate concerns
class UserService { /* ... */ }
class ProductService { /* ... */ }
class OrderService { /* ... */ }
class EmailService { /* ... */ }
class Database { /* ... */ }
```

### Mistake 2: Deep Inheritance Hierarchies

```javascript
// ❌ Fragile base class problem
class Entity { /* ... */ }
class LivingEntity extends Entity { /* ... */ }
class Animal extends LivingEntity { /* ... */ }
class Mammal extends Animal { /* ... */ }
class Dog extends Mammal { /* ... */ }
class GoldenRetriever extends Dog { /* ... */ }

// ✅ Prefer composition over deep inheritance
class Dog {
  constructor() {
    this.behavior = new CanineBehavior();
    this.movement = new QuadrupedMovement();
  }
}
```

### Mistake 3: Using OOP for Everything

```javascript
// ❌ Over-engineering a simple data transformation
class NumberAdder {
  constructor(a, b) {
    this.a = a;
    this.b = b;
  }

  add() {
    return this.a + this.b;
  }
}

const result = new NumberAdder(2, 3).add();

// ✅ Simple function is clearer
const add = (a, b) => a + b;
const result = add(2, 3);
```

### Mistake 4: Mutating Shared State

```javascript
// ❌ Shared mutable state
class Counter {
  count = 0;

  increment() {
    this.count++;
  }
}

const c1 = new Counter();
const c2 = new Counter();
c1.increment();
// c2.count is affected if they share reference!

// ✅ Immutable updates
class Counter {
  constructor(count = 0) {
    this.count = count;
  }

  increment() {
    return new Counter(this.count + 1);
  }
}
```

---

## Practice Exercises

### Exercise 1: Design a Library System

Model a library with Books, Members, and Librarians using OOP principles:

```javascript
// Design classes for:
// - Book (title, author, ISBN, availability)
// - Member (name, id, borrowedBooks)
// - Librarian (can add/remove books, manage members)
// Ensure proper encapsulation and single responsibility
```

### Exercise 2: Refactor to OCP

Refactor this code to follow the Open/Closed Principle:

```javascript
class ReportGenerator {
  generate(type, data) {
    if (type === "pdf") {
      return `PDF: ${data}`;
    } else if (type === "csv") {
      return `CSV: ${data}`;
    } else if (type === "json") {
      return `JSON: ${data}`;
    }
  }
}
```

### Exercise 3: Implement a Notification System

Use polymorphism to create a notification system:

```javascript
// Base: Notification
// Subclasses: EmailNotification, SMSNotification, PushNotification
// Each has a send(message) method with different behavior
// Write a function notifyAll(notifications, message) that sends to all
```

### Exercise 4: Apply SRP

Split this class into multiple single-responsibility classes:

```javascript
class OrderProcessor {
  validate(order) { /* ... */ }
  calculateTotal(order) { /* ... */ }
  saveToDatabase(order) { /* ... */ }
  sendConfirmationEmail(order) { /* ... */ }
  generateInvoice(order) { /* ... */ }
}
```

### Exercise 5: Composition vs Inheritance

Convert this inheritance-based design to composition:

```javascript
class Employee { /* ... */ }
class Developer extends Employee { /* codes */ }
class Manager extends Employee { /* manages */ }
class TechLead extends Developer { /* codes + manages */ }
// TechLead inherits from Developer but also needs managing behavior
```

---

## Summary

- **OOP** organizes code into objects that combine data and behavior
- The **Four Pillars**: Encapsulation, Abstraction, Inheritance, Polymorphism
- **Encapsulation** hides internal state and exposes only what's necessary
- **Abstraction** simplifies complex systems by exposing only essential features
- **Inheritance** enables code reuse through parent-child relationships
- **Polymorphism** allows different objects to respond to the same method call differently
- JavaScript uses **prototypal inheritance** — classes are syntactic sugar
- **SOLID principles** guide maintainable OOP design:
  - **S**ingle Responsibility — one reason to change
  - **O**pen/Closed — extend, don't modify
  - **L**iskov Substitution — subclasses must be substitutable
  - **I**nterface Segregation — don't force unused dependencies
  - **D**ependency Inversion — depend on abstractions
- Combine OOP with functional programming for the best results
- Prefer **composition over deep inheritance hierarchies**

---

## Next Steps

Now that you understand OOP concepts:
- **Prototypes** — dive into JavaScript's inheritance mechanism
- **Classes** — learn modern ES6 class syntax
- **Inheritance** — master class hierarchies and composition patterns

Happy coding! 🚀
