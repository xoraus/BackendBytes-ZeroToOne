# Inheritance in JavaScript

## Overview

**Inheritance** is one of the four pillars of OOP, allowing one class or object to acquire the properties and methods of another. JavaScript supports inheritance through both its **prototypal inheritance** model and the more familiar **class-based syntax** (ES6+). However, inheritance is just one tool for code reuse. Modern JavaScript favors **composition over inheritance** — building complex behavior by combining simpler objects rather than creating deep class hierarchies.

> **Key Insight**: Inheritance is about "is-a" relationships (a Dog is an Animal). Composition is about "has-a" relationships (a Car has an Engine). Favor composition when relationships are complex or multiple.

---

## Prototypal Inheritance

JavaScript's core inheritance mechanism is prototype-based. Objects inherit directly from other objects.

### Setting Up Prototypal Inheritance

```javascript
// Parent object
const animalPrototype = {
  eat() {
    console.log(`${this.name} is eating`);
  },

  sleep() {
    console.log(`${this.name} is sleeping`);
  }
};

// Child object inheriting from parent
const dog = Object.create(animalPrototype);
dog.name = "Rex";
dog.bark = function() {
  console.log(`${this.name} says: Woof!`);
};

dog.eat();   // Inherited: "Rex is eating"
dog.bark();  // Own: "Rex says: Woof!"
```

```
dog
├─ name: "Rex"
├─ bark: function()
└─ [[Prototype]] ──→ animalPrototype
                     ├─ eat: function()
                     ├─ sleep: function()
                     └─ [[Prototype]] ──→ Object.prototype
```

### Constructor Function Inheritance (Pre-ES6)

```javascript
function Animal(name) {
  this.name = name;
}

Animal.prototype.eat = function() {
  console.log(`${this.name} is eating`);
};

// Dog inherits from Animal
function Dog(name, breed) {
  Animal.call(this, name); // Inherit own properties (super equivalent)
  this.breed = breed;
}

// Set up prototype chain
Dog.prototype = Object.create(Animal.prototype);
Dog.prototype.constructor = Dog; // Fix constructor reference

Dog.prototype.bark = function() {
  console.log(`${this.name} says: Woof!`);
};

const rex = new Dog("Rex", "Labrador");
rex.eat();  // "Rex is eating" (from Animal)
rex.bark(); // "Rex says: Woof!" (from Dog)

console.log(rex instanceof Dog);    // true
console.log(rex instanceof Animal); // true
console.log(rex instanceof Object); // true
```

> This pattern is the foundation of what ES6 classes do automatically.

---

## Class Inheritance (ES6)

### Basic Class Inheritance

```javascript
class Animal {
  constructor(name) {
    this.name = name;
    this.energy = 100;
  }

  eat() {
    this.energy += 10;
    console.log(`${this.name} is eating. Energy: ${this.energy}`);
  }

  sleep() {
    this.energy = 100;
    console.log(`${this.name} is sleeping. Energy restored.`);
  }
}

class Dog extends Animal {
  constructor(name, breed) {
    super(name);        // Call parent constructor
    this.breed = breed;
  }

  bark() {
    console.log(`${this.name} says: Woof!`);
  }

  fetch() {
    this.energy -= 5;
    console.log(`${this.name} is fetching. Energy: ${this.energy}`);
  }
}

class Cat extends Animal {
  constructor(name, color) {
    super(name);
    this.color = color;
  }

  meow() {
    console.log(`${this.name} says: Meow!`);
  }

  climb() {
    this.energy -= 3;
    console.log(`${this.name} is climbing. Energy: ${this.energy}`);
  }
}

const rex = new Dog("Rex", "Labrador");
const whiskers = new Cat("Whiskers", "Orange");

rex.eat();      // Inherited from Animal
rex.bark();     // Defined in Dog
whiskers.meow(); // Defined in Cat
```

### The `super` Keyword

`super` serves two purposes in derived classes:

**1. `super()` — Call Parent Constructor**

```javascript
class Employee {
  constructor(name, salary) {
    this.name = name;
    this.salary = salary;
  }
}

class Manager extends Employee {
  constructor(name, salary, department) {
    super(name, salary); // MUST be called before using `this`
    this.department = department;
  }
}
```

**Rules for `super()`:**
- Must be called in derived class constructors
- Must be called before accessing `this`
- Can only be used once per constructor

**2. `super.method()` — Call Parent Method**

```javascript
class Rectangle {
  constructor(width, height) {
    this.width = width;
    this.height = height;
  }

  area() {
    return this.width * this.height;
  }

  describe() {
    return `Rectangle: ${this.width} x ${this.height}`;
  }
}

class Square extends Rectangle {
  constructor(side) {
    super(side, side);
  }

  describe() {
    // Call parent method
    return `Square (${super.describe()})`;
  }
}

const sq = new Square(5);
console.log(sq.area());      // 25 (from Rectangle)
console.log(sq.describe());  // "Square (Rectangle: 5 x 5)"
```

---

## Method Overriding

When a child class defines a method with the same name as a parent method, the child's version **overrides** the parent's.

```javascript
class Notification {
  constructor(message) {
    this.message = message;
  }

  send() {
    console.log(`Sending notification: ${this.message}`);
  }

  format() {
    return this.message;
  }
}

class EmailNotification extends Notification {
  constructor(message, recipient) {
    super(message);
    this.recipient = recipient;
  }

  // Override send()
  send() {
    console.log(`Sending email to ${this.recipient}: ${this.message}`);
  }

  // Override and extend format()
  format() {
    const base = super.format();
    return `Email: ${base}`;
  }
}

class SMSNotification extends Notification {
  constructor(message, phoneNumber) {
    super(message);
    this.phoneNumber = phoneNumber;
  }

  send() {
    console.log(`Sending SMS to ${this.phoneNumber}: ${this.message}`);
  }
}

// Polymorphism in action
const notifications = [
  new EmailNotification("Hello!", "alice@example.com"),
  new SMSNotification("Hello!", "+1234567890")
];

notifications.forEach(n => n.send());
// "Sending email to alice@example.com: Hello!"
// "Sending SMS to +1234567890: Hello!"
```

### Overriding Getters and Setters

```javascript
class Person {
  constructor(firstName, lastName) {
    this.firstName = firstName;
    this.lastName = lastName;
  }

  get fullName() {
    return `${this.firstName} ${this.lastName}`;
  }
}

class Employee extends Person {
  constructor(firstName, lastName, id) {
    super(firstName, lastName);
    this.id = id;
  }

  get fullName() {
    return `[${this.id}] ${super.fullName}`;
  }
}

const emp = new Employee("Alice", "Johnson", "E123");
console.log(emp.fullName); // "[E123] Alice Johnson"
```

---

## Inheritance Chain Inspection

```javascript
class Animal {}
class Mammal extends Animal {}
class Dog extends Mammal {}

const rex = new Dog();

// instanceof checks the entire chain
console.log(rex instanceof Dog);     // true
console.log(rex instanceof Mammal);  // true
console.log(rex instanceof Animal);  // true
console.log(rex instanceof Object);  // true

// Getting the prototype chain
console.log(Object.getPrototypeOf(rex) === Dog.prototype);           // true
console.log(Object.getPrototypeOf(Dog.prototype) === Mammal.prototype); // true
console.log(Object.getPrototypeOf(Mammal.prototype) === Animal.prototype); // true

// isPrototypeOf
console.log(Dog.prototype.isPrototypeOf(rex));     // true
console.log(Mammal.prototype.isPrototypeOf(rex));  // true
```

---

## Composition Over Inheritance

### The Problem with Deep Inheritance

```javascript
// ❌ Fragile, rigid hierarchy
class Entity { /* ... */ }
class LivingEntity extends Entity { /* ... */ }
class Animal extends LivingEntity { /* ... */ }
class Mammal extends Animal { /* ... */ }
class Dog extends Mammal { /* ... */ }
class ServiceDog extends Dog { /* ... */ }
```

Problems:
- **Tight coupling** — changes to a parent affect all children
- **Inflexibility** — what if a `RobotDog` needs `Dog` behavior but not `Mammal`?
- **Duplication** — shared behavior across unrelated branches requires copying

### Composition: Building with Behaviors

```javascript
// Reusable behavior objects
const CanFly = {
  fly() {
    console.log(`${this.name} is flying`);
  }
};

const CanSwim = {
  swim() {
    console.log(`${this.name} is swimming`);
  }
};

const CanWalk = {
  walk() {
    console.log(`${this.name} is walking`);
  }
};

// Mix behaviors into classes
class Bird {
  constructor(name) {
    this.name = name;
  }
}
Object.assign(Bird.prototype, CanFly, CanWalk);

class Fish {
  constructor(name) {
    this.name = name;
  }
}
Object.assign(Fish.prototype, CanSwim);

class Duck {
  constructor(name) {
    this.name = name;
  }
}
Object.assign(Duck.prototype, CanFly, CanSwim, CanWalk);

const duck = new Duck("Donald");
duck.fly();   // "Donald is flying"
duck.swim();  // "Donald is swimming"
duck.walk();  // "Donald is walking"
```

### Composition with Class Fields

```javascript
class CanFly {
  fly() {
    console.log(`${this.name} is flying at ${this.speed} mph`);
  }
}

class CanSwim {
  swim() {
    console.log(`${this.name} is swimming`);
  }
}

class Duck {
  name = "Duck";
  speed = 50;

  flyer = new CanFly();
  swimmer = new CanSwim();

  fly() {
    return this.flyer.fly.call(this);
  }

  swim() {
    return this.swimmer.swim.call(this);
  }
}
```

### When to Use What

| Use Inheritance When | Use Composition When |
|---------------------|---------------------|
| Clear "is-a" relationship | "has-a" or "can-do" relationship |
| Single, stable hierarchy | Multiple, mixed behaviors |
| Shared interface with variations | Reusable behaviors across unrelated types |
| Framework base classes | Domain models with diverse capabilities |

---

## Mixins

A **mixin** is a class or object that provides methods to other classes, without being a parent class. It's a pattern for achieving multiple inheritance-like behavior.

### Functional Mixins

```javascript
// Mixin factory function
function TimestampMixin(BaseClass) {
  return class extends BaseClass {
    constructor(...args) {
      super(...args);
      this.createdAt = new Date();
      this.updatedAt = new Date();
    }

    touch() {
      this.updatedAt = new Date();
    }
  };
}

function SerializableMixin(BaseClass) {
  return class extends BaseClass {
    toJSON() {
      return JSON.stringify(this);
    }

    fromJSON(json) {
      return Object.assign(this, JSON.parse(json));
    }
  };
}

// Apply mixins
class User {
  constructor(name) {
    this.name = name;
  }
}

const TimestampedUser = TimestampMixin(User);
const SerializableUser = SerializableMixin(TimestampedUser);

const user = new SerializableUser("Alice");
console.log(user.createdAt); // Date object
user.touch();
console.log(user.toJSON());  // JSON string
```

### Object Mixins

```javascript
const Loggable = {
  log(message) {
    console.log(`[${this.constructor.name}] ${message}`);
  }
};

const Validatable = {
  validate() {
    return this.isValid !== undefined ? this.isValid : true;
  }
};

class Product {
  constructor(name, price) {
    this.name = name;
    this.price = price;
    this.isValid = price > 0;
  }
}

// Apply mixins
Object.assign(Product.prototype, Loggable, Validatable);

const product = new Product("Widget", 10);
product.log("Created");    // "[Product] Created"
console.log(product.validate()); // true
```

---

## Multiple Inheritance Workarounds

JavaScript doesn't support multiple class inheritance directly. Here are workarounds:

### 1. Mixins (Recommended)

```javascript
const MixinA = (Base) => class extends Base { methodA() {} };
const MixinB = (Base) => class extends Base { methodB() {} };

class MyClass extends MixinB(MixinA(Object)) {}
```

### 2. Composition

```javascript
class FeatureA { methodA() {} }
class FeatureB { methodB() {} }

class MyClass {
  constructor() {
    this.a = new FeatureA();
    this.b = new FeatureB();
  }
}
```

### 3. Interface-like Patterns

```javascript
class CanFly {
  fly() { throw new Error("Must implement fly()"); }
}

class CanSwim {
  swim() { throw new Error("Must implement swim()"); }
}

class Duck extends Animal {
  fly() {
    console.log("Flying...");
  }

  swim() {
    console.log("Swimming...");
  }
}
```

---

## Common Mistakes

### Mistake 1: Deep Inheritance Hierarchies

```javascript
// ❌ Brittle and hard to understand
class Entity { }
class LivingEntity extends Entity { }
class Animal extends LivingEntity { }
class Mammal extends Animal { }
class Canine extends Mammal { }
class Dog extends Canine { }

// ✅ Flatten the hierarchy, use composition
class Dog {
  constructor() {
    this.movement = new QuadrupedMovement();
    this.diet = new CarnivoreDiet();
  }
}
```

### Mistake 2: Forgetting to Call `super()`

```javascript
class Animal {
  constructor(name) {
    this.name = name;
  }
}

class Dog extends Animal {
  constructor(name, breed) {
    // ❌ Missing super() — ReferenceError when using `this`
    this.breed = breed;
  }
}

// ✅ Always call super() first
class Dog extends Animal {
  constructor(name, breed) {
    super(name);
    this.breed = breed;
  }
}
```

### Mistake 3: Using `super` in Non-Derived Classes

```javascript
class Animal {
  constructor(name) {
    super(); // ❌ SyntaxError: 'super' keyword unexpected here
    this.name = name;
  }
}

// ✅ Only use super() in classes with extends
class Animal {
  constructor(name) {
    this.name = name;
  }
}
```

### Mistake 4: Incorrect Method Override

```javascript
class Rectangle {
  constructor(w, h) {
    this.width = w;
    this.height = h;
  }

  setWidth(w) {
    this.width = w;
  }

  setHeight(h) {
    this.height = h;
  }

  area() {
    return this.width * this.height;
  }
}

// ❌ Violates Liskov Substitution Principle
class Square extends Rectangle {
  setWidth(w) {
    this.width = w;
    this.height = w; // Unexpected side effect!
  }

  setHeight(h) {
    this.width = h;
    this.height = h; // Unexpected side effect!
  }
}

// ✅ Better: Square is NOT a Rectangle in behavior
class Square {
  constructor(side) {
    this.side = side;
  }

  area() {
    return this.side ** 2;
  }
}
```

### Mistake 5: Overusing Inheritance for Code Reuse

```javascript
// ❌ Using inheritance just to share a utility method
class Utils {
  formatDate(date) {
    return date.toISOString();
  }
}

class User extends Utils { /* ... */ } // Wrong! User is not a Utils!

// ✅ Use a module or object instead
const DateUtils = {
  formatDate(date) {
    return date.toISOString();
  }
};

class User {
  constructor() {
    this.createdAt = DateUtils.formatDate(new Date());
  }
}
```

---

## Practice Exercises

### Exercise 1: Shape Hierarchy

Create a shape hierarchy with proper inheritance:

```javascript
class Shape {
  // area() — throw error (abstract)
  // perimeter() — throw error (abstract)
  // describe() — return string description
}

class Rectangle extends Shape {
  // Implement area(), perimeter()
}

class Circle extends Shape {
  // Implement area(), perimeter()
}

class Triangle extends Shape {
  // Implement area(), perimeter()
}

// Test polymorphism
const shapes = [new Rectangle(4, 5), new Circle(3), new Triangle(3, 4, 5)];
shapes.forEach(s => console.log(s.describe()));
```

### Exercise 2: Plugin System with Mixins

Build a plugin system using mixins:

```javascript
function LoggingPlugin(Base) {
  return class extends Base {
    log(action) {
      console.log(`[LOG] ${action} at ${new Date()}`);
    }
  };
}

function ValidationPlugin(Base) {
  return class extends Base {
    validate(data) {
      return data != null && typeof data === "object";
    }
  };
}

class Database {
  save(data) {
    // Should log and validate before saving
  }
}

// Apply plugins
const EnhancedDatabase = ValidationPlugin(LoggingPlugin(Database));
const db = new EnhancedDatabase();
db.save({ name: "Alice" }); // Logs and validates
```

### Exercise 3: Refactor to Composition

Refactor this inheritance-based code to composition:

```javascript
class Vehicle {
  constructor(speed) { this.speed = speed; }
  move() { console.log("Moving"); }
}

class Car extends Vehicle {
  honk() { console.log("Beep!"); }
}

class Boat extends Vehicle {
  anchor() { console.log("Anchoring"); }
}

class AmphibiousVehicle extends Car {
  // Needs both Car AND Boat behavior!
  anchor() { console.log("Anchoring"); }
}
```

### Exercise 4: Method Resolution Order

Trace and predict the output:

```javascript
class A {
  greet() { return "A"; }
}

class B extends A {
  greet() { return super.greet() + "B"; }
}

class C extends A {
  greet() { return super.greet() + "C"; }
}

class D extends B {
  greet() { return super.greet() + "D"; }
}

const d = new D();
console.log(d.greet()); // What does this output?
```

### Exercise 5: Abstract Base Class

Implement an abstract base class pattern in JavaScript:

```javascript
class Animal {
  constructor() {
    if (new.target === Animal) {
      throw new Error("Cannot instantiate abstract class");
    }
  }

  speak() {
    throw new Error("Must implement speak()");
  }
}

class Dog extends Animal {
  speak() { return "Woof!"; }
}

class Cat extends Animal {
  speak() { return "Meow!"; }
}

const dog = new Dog();
console.log(dog.speak()); // "Woof!"

// const animal = new Animal(); // Should throw
```

---

## Summary

- JavaScript uses **prototypal inheritance** — objects inherit from objects
- **Class inheritance** (`extends`) is syntactic sugar over the prototype chain
- `super()` **must** be called in derived constructors before using `this`
- `super.method()` calls a parent class method from an override
- **Method overriding** lets subclasses provide specialized behavior
- **Polymorphism** allows treating different objects uniformly through a common interface
- **Deep inheritance hierarchies** are fragile — prefer flat structures
- **Composition** builds complex behavior by combining simpler objects
- **Mixins** are functions/classes that add behavior without inheritance
- JavaScript doesn't support multiple class inheritance — use mixins or composition
- The **Liskov Substitution Principle** states subclasses should be fully substitutable for their parents
- Only use inheritance for true "is-a" relationships, not for code sharing

---

## Next Steps

Now that you understand inheritance:
- **OOP Concepts** — review the four pillars with practical examples
- **Classes** — dive deeper into advanced class patterns
- **Prototypes** — understand the mechanics under the hood
- **System Design** — learn how OOP patterns apply to larger architectures

Happy coding! 🚀
