# Prototypes in JavaScript

## Overview

**Prototypes** are the mechanism by which JavaScript objects inherit features from one another. Unlike class-based languages where inheritance is defined at compile time, JavaScript uses **prototypal inheritance** — objects can inherit directly from other objects. Understanding prototypes is essential because even ES6 classes are syntactic sugar over this prototype-based system. Every JavaScript developer must understand prototypes to truly master the language.

> **Key Insight**: When you access a property on an object, JavaScript first looks at the object itself. If not found, it looks at the object's prototype, then the prototype's prototype, and so on — this is called the **prototype chain**.

---

## What is a Prototype?

Every JavaScript object has an internal property called `[[Prototype]]` (accessible via `__proto__` or `Object.getPrototypeOf()`). This property is a reference to another object that serves as the prototype.

```javascript
const obj = {};
console.log(Object.getPrototypeOf(obj) === Object.prototype); // true
```

```
┌─────────────────┐
│      obj        │
│  [[Prototype]]──┼──→┌──────────────────┐
└─────────────────┘   │  Object.prototype │
                      │  - toString()     │
                      │  - valueOf()      │
                      │  - hasOwnProperty()│
                      │  [[Prototype]]────┼──→ null
                      └──────────────────┘
```

> The chain ends when `[[Prototype]]` is `null`. `Object.prototype`'s prototype is `null`.

---

## `__proto__` vs `prototype`

These are often confused but serve completely different purposes:

### `__proto__` — The Instance's Prototype Link

- Exists on **every object**
- Points to the object's prototype
- Used for **looking up** inherited properties

```javascript
const animal = { eats: true };
const rabbit = { jumps: true };

rabbit.__proto__ = animal; // rabbit inherits from animal

console.log(rabbit.jumps); // true (own property)
console.log(rabbit.eats);  // true (inherited from animal)
```

### `prototype` — The Constructor's Template

- Exists **only on functions** (used as constructors)
- The object that will become the `__proto__` of instances
- Used when creating objects with `new`

```javascript
function Animal(name) {
  this.name = name;
}

Animal.prototype.eat = function() {
  console.log(`${this.name} is eating`);
};

const dog = new Animal("Rex");
console.log(dog.__proto__ === Animal.prototype); // true
dog.eat(); // "Rex is eating" (found on Animal.prototype)
```

### Visual Summary

```
Function: Animal
├─ prototype ──→ Animal.prototype
│                  ├─ constructor ──→ Animal
│                  └─ eat: function()
│
└─ [[Prototype]] ──→ Function.prototype

Instance: dog
├─ name: "Rex"
└─ [[Prototype]] ──→ Animal.prototype
```

| | `__proto__` | `prototype` |
|---|-------------|-------------|
| Where | On all objects | On functions only |
| Purpose | Lookup chain | Template for instances |
| Set by | `Object.setPrototypeOf`, `__proto__` | Direct assignment |
| Used with | Any object | Constructor + `new` |

---

## The Prototype Chain

When you access a property, JavaScript walks up the prototype chain until it finds the property or reaches `null`.

```javascript
const grandparent = { grandparentProp: true };
const parent = { parentProp: true };
const child = { childProp: true };

parent.__proto__ = grandparent;
child.__proto__ = parent;

console.log(child.childProp);      // true (own)
console.log(child.parentProp);     // true (from parent)
console.log(child.grandparentProp); // true (from grandparent)
console.log(child.toString);       // function (from Object.prototype)
console.log(child.nonExistent);    // undefined (not found anywhere)
```

```
child → parent → grandparent → Object.prototype → null
  │       │          │              │
  │       │          │              └─ toString()
  │       │          │              └─ valueOf()
  │       │          │              └─ hasOwnProperty()
  │       │          └─ grandparentProp
  │       └─ parentProp
  └─ childProp
```

### Property Shadowing

If a child object has a property with the same name as one on its prototype, the child's property **shadows** the inherited one:

```javascript
const parent = { name: "Parent" };
const child = { name: "Child" };
child.__proto__ = parent;

console.log(child.name); // "Child" (own property wins)
```

### Checking the Prototype Chain

```javascript
const animal = { eats: true };
const rabbit = { jumps: true };
rabbit.__proto__ = animal;

// Check if object is in prototype chain
console.log(animal.isPrototypeOf(rabbit)); // true

// Get prototype
console.log(Object.getPrototypeOf(rabbit) === animal); // true

// Check if own property
console.log(rabbit.hasOwnProperty("jumps")); // true
console.log(rabbit.hasOwnProperty("eats"));  // false
```

---

## Constructor Functions and Prototypes

Before ES6 classes, constructor functions were the primary way to create object "types."

### Creating a Constructor

```javascript
function Person(name, age) {
  // `this` refers to the new instance
  this.name = name;
  this.age = age;
}

// Methods go on the prototype (shared across instances)
Person.prototype.greet = function() {
  return `Hello, I'm ${this.name}`;
};

Person.prototype.haveBirthday = function() {
  this.age++;
  return this.age;
};

const alice = new Person("Alice", 30);
const bob = new Person("Bob", 25);

console.log(alice.greet()); // "Hello, I'm Alice"
console.log(bob.greet());   // "Hello, I'm Bob"

// Both instances share the same method
console.log(alice.greet === bob.greet); // true
```

### How `new` Works

When you call `new Person("Alice", 30)`, this happens:

1. Create a new empty object: `{}`
2. Set its `[[Prototype]]` to `Person.prototype`
3. Call `Person` with `this` bound to the new object
4. Return the new object (unless the constructor returns a non-primitive)

```javascript
// What `new` does under the hood
function myNew(Constructor, ...args) {
  const obj = Object.create(Constructor.prototype);
  const result = Constructor.apply(obj, args);
  return (result !== null && (typeof result === "object" || typeof result === "function"))
    ? result
    : obj;
}

const alice = myNew(Person, "Alice", 30);
```

### Instance Properties vs Prototype Properties

```javascript
function Car(make, model) {
  // Instance properties — unique to each instance
  this.make = make;
  this.model = model;
  this.odometer = 0;
}

// Shared prototype property
Car.prototype.wheels = 4;

// Shared prototype method
Car.prototype.drive = function(miles) {
  this.odometer += miles;
};

const car1 = new Car("Toyota", "Camry");
const car2 = new Car("Honda", "Civic");

car1.drive(100);
console.log(car1.odometer); // 100
car2.drive(50);
console.log(car2.odometer); // 50

// Shared property
console.log(car1.wheels); // 4
console.log(car2.wheels); // 4

// Modifying prototype affects all instances
Car.prototype.wheels = 3; // Don't do this in practice!
console.log(car1.wheels); // 3
```

---

## Object.create()

`Object.create()` creates a new object with a specified prototype — the purest form of prototypal inheritance.

```javascript
const animalPrototype = {
  eat() {
    console.log(`${this.name} is eating`);
  },

  sleep() {
    console.log(`${this.name} is sleeping`);
  }
};

const dog = Object.create(animalPrototype);
dog.name = "Rex";
dog.eat();   // "Rex is eating"
dog.sleep(); // "Rex is sleeping"
```

### With Property Descriptors

```javascript
const person = Object.create(Object.prototype, {
  name: {
    value: "Alice",
    writable: true,
    enumerable: true
  },
  age: {
    value: 30,
    writable: true,
    enumerable: true
  }
});
```

### Creating Objects Without a Prototype

```javascript
const dict = Object.create(null);

// No inherited properties at all!
console.log(dict.toString); // undefined
console.log("toString" in dict); // false

// Safe to use any string as a key
dict["__proto__"] = "value"; // Works fine
```

> `Object.create(null)` is useful for dictionaries/hash maps where you don't want inherited methods interfering.

---

## The `instanceof` Operator

`instanceof` checks if an object's prototype chain contains a constructor's `prototype`.

```javascript
function Animal() {}
function Dog() {}

Dog.prototype = Object.create(Animal.prototype);
Dog.prototype.constructor = Dog;

const rex = new Dog();

console.log(rex instanceof Dog);     // true
console.log(rex instanceof Animal);  // true
console.log(rex instanceof Object);  // true
```

```
rex → Dog.prototype → Animal.prototype → Object.prototype → null
       ↑                ↑
   instanceof checks these
```

### How instanceof Works

```javascript
function myInstanceof(obj, Constructor) {
  let proto = Object.getPrototypeOf(obj);

  while (proto !== null) {
    if (proto === Constructor.prototype) {
      return true;
    }
    proto = Object.getPrototypeOf(proto);
  }

  return false;
}

myInstanceof(rex, Dog); // true
```

> `instanceof` only works with constructor functions/classes. For plain objects, use `isPrototypeOf()`.

---

## Modifying Built-in Prototypes (Anti-Pattern)

```javascript
// ❌ NEVER DO THIS IN PRODUCTION
String.prototype.reverse = function() {
  return this.split("").reverse().join("");
};

"hello".reverse(); // "olleh"
```

**Why this is dangerous:**
- Can break third-party libraries
- Name collisions with future language features
- Hard to debug issues across the codebase

**If you must extend, use a utility function:**
```javascript
// ✅ Safe approach
const StringUtils = {
  reverse(str) {
    return str.split("").reverse().join("");
  }
};

StringUtils.reverse("hello"); // "olleh"
```

---

## Common Mistakes

### Mistake 1: Forgetting to Reset Constructor

```javascript
// ❌ Constructor is lost
function Parent() {}
function Child() {}
Child.prototype = Object.create(Parent.prototype);

console.log(new Child().constructor === Child); // false! It's Parent

// ✅ Always reset constructor
Child.prototype = Object.create(Parent.prototype);
Child.prototype.constructor = Child;

console.log(new Child().constructor === Child); // true
```

### Mistake 2: Setting Prototype with Object Literal

```javascript
// ❌ Replaces the entire prototype object, losing constructor
function Parent() {}
Parent.prototype = {
  greet() { return "Hello"; }
};

console.log(new Parent().constructor === Parent); // false

// ✅ Extend prototype instead
Parent.prototype.greet = function() {
  return "Hello";
};
```

### Mistake 3: Methods Inside Constructor

```javascript
// ❌ Each instance gets its own function — memory waste
function Person(name) {
  this.name = name;
  this.greet = function() { // New function for EVERY instance
    return `Hello, I'm ${this.name}`;
  };
}

const a = new Person("Alice");
const b = new Person("Bob");
console.log(a.greet === b.greet); // false

// ✅ Put methods on prototype
function Person(name) {
  this.name = name;
}

Person.prototype.greet = function() {
  return `Hello, I'm ${this.name}`;
};

const c = new Person("Charlie");
const d = new Person("Dana");
console.log(c.greet === d.greet); // true (shared)
```

### Mistake 4: Using `__proto__` Instead of Object.create()

```javascript
// ❌ __proto__ is deprecated and slow
const parent = { x: 1 };
const child = {};
child.__proto__ = parent;

// ✅ Use Object.create() or Object.setPrototypeOf()
const child2 = Object.create(parent);
// or
const child3 = {};
Object.setPrototypeOf(child3, parent);
```

### Mistake 5: Confusing Prototype Inheritance with Class Inheritance

```javascript
// ❌ Trying to copy properties manually
function Child() {
  Parent.call(this); // Only copies own properties, not prototype methods
}

// ✅ Proper prototypal inheritance
function Child() {
  Parent.call(this); // Inherit own properties
}
Child.prototype = Object.create(Parent.prototype); // Inherit methods
Child.prototype.constructor = Child;
```

---

## Practice Exercises

### Exercise 1: Implement Inheritance Manually

Create a `Rectangle` and `Square` using constructor functions and prototypes:

```javascript
function Rectangle(width, height) {
  // Your code
}

Rectangle.prototype.area = function() {
  // Your code
};

Rectangle.prototype.perimeter = function() {
  // Your code
};

function Square(side) {
  // Your code — should inherit from Rectangle
}

const rect = new Rectangle(5, 3);
console.log(rect.area());      // 15
console.log(rect.perimeter()); // 16

const square = new Square(4);
console.log(square.area());      // 16
console.log(square.perimeter()); // 16
console.log(square instanceof Rectangle); // true
```

### Exercise 2: Build a Prototype Chain

Create a prototype chain: `Vehicle` → `Car` → `ElectricCar`:

```javascript
// Each level should add properties/methods
// Vehicle: start(), stop()
// Car: honk(), inherits start/stop
// ElectricCar: charge(), inherits everything

const tesla = new ElectricCar("Tesla", "Model 3");
tesla.start();  // "Tesla Model 3 is starting"
tesla.honk();   // "Beep beep!"
tesla.charge(); // "Tesla Model 3 is charging"
```

### Exercise 3: Prototype Inspection

Write a function that prints the entire prototype chain of an object:

```javascript
function printPrototypeChain(obj) {
  // Print each prototype in the chain
  // Format: "obj → Prototype1 → Prototype2 → null"
}

printPrototypeChain([]);
// Expected: "[] → Array.prototype → Object.prototype → null"
```

### Exercise 4: Mixin with Prototypes

Implement a mixin function that copies methods from multiple source objects to a target prototype:

```javascript
function mixin(targetPrototype, ...sources) {
  // Copy all methods from sources to targetPrototype
}

const Flyable = {
  fly() { console.log(`${this.name} is flying`); }
};

const Swimmable = {
  swim() { console.log(`${this.name} is swimming`); }
};

function Duck(name) { this.name = name; }
mixin(Duck.prototype, Flyable, Swimmable);

const duck = new Duck("Donald");
duck.fly();  // "Donald is flying"
duck.swim(); // "Donald is swimming"
```

### Exercise 5: Custom instanceof

Implement a custom `instanceOf` that also works with plain object prototypes:

```javascript
function instanceOf(obj, prototype) {
  // Check if `prototype` is anywhere in obj's prototype chain
  // Should work with: instanceOf(obj, SomeConstructor.prototype)
  // AND: instanceOf(obj, somePlainObject)
}

const animal = { eats: true };
const rabbit = Object.create(animal);
console.log(instanceOf(rabbit, animal)); // true
```

---

## Summary

- Every object has a `[[Prototype]]` (accessed via `__proto__` or `Object.getPrototypeOf()`)
- The **prototype chain** is how JavaScript looks up properties: object → prototype → prototype's prototype → ... → null
- `__proto__` is on **instances** — used for property lookup
- `prototype` is on **functions** — used as the template for `new` instances
- **Constructor functions** + `new` create objects with a specific prototype
- `Object.create(proto)` creates an object with a specified prototype
- `instanceof` checks if a prototype exists in an object's prototype chain
- Always reset `constructor` after reassigning `prototype`
- Put **methods on the prototype** and **data in the constructor** for memory efficiency
- **Never modify built-in prototypes** in production code
- Use `Object.create(null)` for pure dictionary objects

---

## Next Steps

Now that you understand prototypes:
- **Classes** — learn the modern ES6 syntax for the same patterns
- **Inheritance** — master class hierarchies and advanced inheritance patterns
- **OOP Concepts** — review the four pillars with your new prototype knowledge

Happy coding! 🚀
