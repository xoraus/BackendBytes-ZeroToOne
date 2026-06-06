# Unit Testing with Jest

## Overview

**Testing** is not an optional step in software development — it is a fundamental practice that ensures your code works correctly, remains stable during refactoring, and serves as living documentation. **Jest** is the most popular JavaScript testing framework, developed and maintained by Meta (formerly Facebook). It is used to test everything from small utility functions to entire React applications and Node.js backends. This tutorial will take you from understanding why testing matters to writing production-grade test suites with mocking, coverage, and best practices.

> **Key Insight**: Tests are not just about finding bugs. They are about **confidence** — the confidence to refactor, deploy on Fridays, and sleep soundly knowing your code behaves as expected. A good test suite is the safety net that enables fast, fearless development.

---

## Why Write Tests?

### The Cost of Untested Code

```
Development Phase          Bug Discovery Cost
─────────────────────────────────────────────
Coding                     1x (fix immediately)
Code Review                2x (context switch)
Testing (QA)               5x (reproduce, report)
Production                 10-100x (downtime, reputation)
```

### Benefits of Testing

| Benefit | Description |
|---------|-------------|
| **Confidence** | Deploy knowing critical paths work |
| **Documentation** | Tests show how code is intended to be used |
| **Refactoring** | Change internals without fear of breaking behavior |
| **Design Feedback** | Hard-to-test code often signals poor design |
| **Regression Prevention** | Catch bugs before they reach production |
| **Collaboration** | New developers understand code through tests |

### Types of Tests

```
┌─────────────────────────────────────────────┐
│           End-to-End (E2E) Tests            │  ← Few, slow, high confidence
│     (User journey: login → checkout)        │     "Does the whole system work?"
├─────────────────────────────────────────────┤
│           Integration Tests                 │  ← Some, medium speed
│     (API + Database working together)       │     "Do components integrate?"
├─────────────────────────────────────────────┤
│           Unit Tests                        │  ← Many, fast, focused
│     (Single function in isolation)          │     "Does this function work?"
└─────────────────────────────────────────────┘

The Testing Pyramid: More unit tests, fewer E2E tests
```

---

## Setting Up Jest

### Installation

```bash
# For a Node.js project
npm install --save-dev jest

# For TypeScript support
npm install --save-dev jest ts-jest @types/jest

# For DOM testing (React, etc.)
npm install --save-dev jest-environment-jsdom
```

### Configuration

Jest works with **zero configuration**, but you can customize it via `jest.config.js`:

```javascript
// jest.config.js
module.exports = {
  // Test environment
  testEnvironment: "node",        // or "jsdom" for browser code

  // File patterns to test
  testMatch: ["**/__tests__/**/*.test.js", "**/?(*.)+(spec|test).js"],

  // Setup files
  setupFilesAfterEnv: ["./jest.setup.js"],

  // Coverage configuration
  collectCoverageFrom: [
    "src/**/*.js",
    "!src/**/*.test.js",
    "!src/index.js"
  ],
  coverageThreshold: {
    global: {
      branches: 80,
      functions: 80,
      lines: 80,
      statements: 80
    }
  },

  // Module path aliases
  moduleNameMapper: {
    "^@/(.*)$": "<rootDir>/src/$1"
  },

  // Transform files
  transform: {
    "^.+\\.js$": "babel-jest",
    "^.+\\.ts$": "ts-jest"
  }
};
```

### Package.json Scripts

```json
{
  "scripts": {
    "test": "jest",
    "test:watch": "jest --watch",
    "test:coverage": "jest --coverage",
    "test:verbose": "jest --verbose"
  }
}
```

### Running Tests

```bash
npm test                          # Run all tests once
npm test -- --watch             # Watch mode (rerun on file changes)
npm test -- --coverage          # Generate coverage report
npm test -- --testNamePattern="login"  # Run tests matching pattern
npm test -- src/utils           # Run tests in specific directory
npm test -- --runInBand         # Run sequentially (for CI)
```

---

## Writing Your First Test

### Basic Structure

```javascript
// sum.js
function sum(a, b) {
  return a + b;
}

module.exports = { sum };
```

```javascript
// sum.test.js
const { sum } = require("./sum");

test("adds 1 + 2 to equal 3", () => {
  expect(sum(1, 2)).toBe(3);
});
```

### Test and Describe Blocks

```javascript
// math.test.js
const { sum, multiply, divide } = require("./math");

describe("Math operations", () => {
  describe("sum", () => {
    test("adds positive numbers", () => {
      expect(sum(2, 3)).toBe(5);
    });

    test("adds negative numbers", () => {
      expect(sum(-2, -3)).toBe(-5);
    });

    test("adds zero", () => {
      expect(sum(5, 0)).toBe(5);
    });
  });

  describe("multiply", () => {
    test("multiplies positive numbers", () => {
      expect(multiply(4, 5)).toBe(20);
    });
  });

  describe("divide", () => {
    test("divides numbers correctly", () => {
      expect(divide(10, 2)).toBe(5);
    });

    test("throws on division by zero", () => {
      expect(() => divide(10, 0)).toThrow("Cannot divide by zero");
    });
  });
});
```

### Output

```
PASS  ./math.test.js
  Math operations
    sum
      ✓ adds positive numbers
      ✓ adds negative numbers
      ✓ adds zero
    multiply
      ✓ multiplies positive numbers
    divide
      ✓ divides numbers correctly
      ✓ throws on division by zero

Test Suites: 1 passed, 1 total
Tests:       6 passed, 6 total
```

---

## Matchers

Matchers are methods that let you test values in different ways.

### Common Matchers

```javascript
test("common matchers", () => {
  // Exact equality (for primitives)
  expect(2 + 2).toBe(4);

  // Deep equality (for objects/arrays)
  expect({ a: 1 }).toEqual({ a: 1 });
  expect([1, 2, 3]).toEqual([1, 2, 3]);

  // Not
  expect(2 + 2).not.toBe(5);

  // Truthiness
  expect(null).toBeNull();
  expect(undefined).toBeUndefined();
  expect(1).toBeDefined();
  expect(true).toBeTruthy();
  expect(0).toBeFalsy();

  // Numbers
  expect(4).toBeGreaterThan(3);
  expect(4).toBeGreaterThanOrEqual(4);
  expect(3).toBeLessThan(4);
  expect(3.14159).toBeCloseTo(3.14, 2); // 2 decimal places

  // Strings
  expect("team").toMatch(/tea/);
  expect("hello world").toContain("world");

  // Arrays
  expect([1, 2, 3]).toContain(2);
  expect([1, 2, 3]).toHaveLength(3);

  // Objects
  expect({ name: "Alice", age: 30 }).toHaveProperty("name");
  expect({ name: "Alice", age: 30 }).toHaveProperty("age", 30);
});
```

### Object Matchers

```javascript
test("object matchers", () => {
  const user = {
    name: "Alice",
    address: {
      city: "NYC",
      zip: "10001"
    },
    hobbies: ["coding", "reading"]
  };

  // Partial matching
  expect(user).toMatchObject({
    name: "Alice",
    address: { city: "NYC" }
  });

  // Strict object matching
  expect(user).toStrictEqual({
    name: "Alice",
    address: {
      city: "NYC",
      zip: "10001"
    },
    hobbies: ["coding", "reading"]
  });

  // Anything matchers (useful for dynamic values)
  expect(user).toEqual({
    name: expect.any(String),
    address: expect.any(Object),
    hobbies: expect.arrayContaining(["coding"])
  });
});
```

### Custom Matchers

```javascript
// Extend Jest with custom matchers
expect.extend({
  toBeWithinRange(received, floor, ceiling) {
    const pass = received >= floor && received <= ceiling;
    if (pass) {
      return {
        message: () =>
          `expected ${received} not to be within range ${floor} - ${ceiling}`,
        pass: true
      };
    } else {
      return {
        message: () =>
          `expected ${received} to be within range ${floor} - ${ceiling}`,
        pass: false
      };
    }
  }
});

test("custom matcher", () => {
  expect(5).toBeWithinRange(1, 10);
  expect(15).not.toBeWithinRange(1, 10);
});
```

---

## Testing Asynchronous Code

### Promises

```javascript
// fetchData.js
function fetchData() {
  return Promise.resolve("peanut butter");
}

function fetchDataWithError() {
  return Promise.reject(new Error("network error"));
}

module.exports = { fetchData, fetchDataWithError };
```

```javascript
// fetchData.test.js
const { fetchData, fetchDataWithError } = require("./fetchData");

// Return the promise
test("fetchData resolves with peanut butter", () => {
  return fetchData().then(data => {
    expect(data).toBe("peanut butter");
  });
});

// Use resolves/rejects matchers
test("fetchData resolves with peanut butter", () => {
  return expect(fetchData()).resolves.toBe("peanut butter");
});

test("fetchDataWithError rejects with error", () => {
  return expect(fetchDataWithError()).rejects.toThrow("network error");
});

// Async/await (cleanest)
test("fetchData resolves with peanut butter", async () => {
  const data = await fetchData();
  expect(data).toBe("peanut butter");
});

test("fetchDataWithError rejects with error", async () => {
  await expect(fetchDataWithError()).rejects.toThrow("network error");
});

test("fetchDataWithError rejects with error (try/catch)", async () => {
  expect.assertions(1); // Ensure assertion is called
  try {
    await fetchDataWithError();
  } catch (error) {
    expect(error.message).toBe("network error");
  }
});
```

> **Always use `expect.assertions(n)` when testing exceptions with try/catch** to ensure the test fails if the exception is not thrown.

### Callbacks

```javascript
function fetchDataCallback(callback) {
  setTimeout(() => {
    callback(null, "peanut butter");
  }, 100);
}

test("fetchDataCallback returns peanut butter", done => {
  function callback(error, data) {
    if (error) {
      done(error);
      return;
    }
    expect(data).toBe("peanut butter");
    done();
  }

  fetchDataCallback(callback);
});
```

---

## Setup and Teardown

### Before and After Each Test

```javascript
describe("User database", () => {
  let db;

  beforeEach(() => {
    // Runs before each test
    db = new Database();
    db.connect();
  });

  afterEach(() => {
    // Runs after each test
    db.disconnect();
    db = null;
  });

  test("can create user", () => {
    const user = db.createUser("Alice");
    expect(user.name).toBe("Alice");
  });

  test("can find user", () => {
    db.createUser("Bob");
    const user = db.findUser("Bob");
    expect(user).toBeDefined();
  });
});
```

### Before and After All Tests

```javascript
describe("Database integration", () => {
  let db;

  beforeAll(() => {
    // Runs once before all tests
    db = new Database();
    return db.connect(); // Can return a promise
  });

  afterAll(() => {
    // Runs once after all tests
    return db.disconnect();
  });

  test("can query users", async () => {
    const users = await db.query("SELECT * FROM users");
    expect(users).toBeInstanceOf(Array);
  });
});
```

### Scoping

```javascript
describe("outer", () => {
  beforeAll(() => console.log("1 - beforeAll"));
  afterAll(() => console.log("1 - afterAll"));
  beforeEach(() => console.log("1 - beforeEach"));
  afterEach(() => console.log("1 - afterEach"));

  test("", () => console.log("1 - test"));

  describe("inner", () => {
    beforeAll(() => console.log("2 - beforeAll"));
    afterAll(() => console.log("2 - afterAll"));
    beforeEach(() => console.log("2 - beforeEach"));
    afterEach(() => console.log("2 - afterEach"));

    test("", () => console.log("2 - test"));
  });
});

// Output:
// 1 - beforeAll
// 1 - beforeEach
// 1 - test
// 1 - afterEach
// 2 - beforeAll
// 1 - beforeEach
// 2 - beforeEach
// 2 - test
// 2 - afterEach
// 1 - afterEach
// 2 - afterAll
// 1 - afterAll
```

---

## Mocking

Mocking replaces real implementations with controlled substitutes, enabling you to test in isolation.

### Mocking Functions with `jest.fn()`

```javascript
// Simple mock
const mockFn = jest.fn();
mockFn("arg1", "arg2");

expect(mockFn).toHaveBeenCalled();
expect(mockFn).toHaveBeenCalledWith("arg1", "arg2");
expect(mockFn).toHaveBeenCalledTimes(1);

// Mock with return value
const mockAdd = jest.fn().mockReturnValue(10);
expect(mockAdd(2, 3)).toBe(10); // Returns 10 regardless of input

// Mock with different return values per call
const mockFn = jest.fn()
  .mockReturnValueOnce("first")
  .mockReturnValueOnce("second")
  .mockReturnValue("default");

mockFn(); // "first"
mockFn(); // "second"
mockFn(); // "default"
mockFn(); // "default"

// Mock with implementation
const mockDivide = jest.fn((a, b) => {
  if (b === 0) throw new Error("Cannot divide by zero");
  return a / b;
});

expect(mockDivide(10, 2)).toBe(5);
expect(() => mockDivide(10, 0)).toThrow("Cannot divide by zero");

// Async mock
const mockFetch = jest.fn().mockResolvedValue({ data: [] });
// or
const mockFetch = jest.fn().mockRejectedValue(new Error("Failed"));
```

### Mocking Modules

```javascript
// userService.js
const { fetchUser } = require("./api");

async function getUserName(userId) {
  const user = await fetchUser(userId);
  return user.name;
}

module.exports = { getUserName };
```

```javascript
// userService.test.js
jest.mock("./api");

const { fetchUser } = require("./api");
const { getUserName } = require("./userService");

test("returns user name", async () => {
  fetchUser.mockResolvedValue({ name: "Alice" });

  const name = await getUserName(1);
  expect(name).toBe("Alice");
  expect(fetchUser).toHaveBeenCalledWith(1);
});
```

### Manual Mocks

```javascript
// __mocks__/api.js
module.exports = {
  fetchUser: jest.fn(),
  fetchPosts: jest.fn()
};
```

```javascript
// userService.test.js
jest.mock("./api"); // Automatically uses __mocks__/api.js
```

### Spying on Methods

```javascript
const video = {
  play() {
    return true;
  },
  pause() {
    return true;
  }
};

test("spies on video.play", () => {
  const spy = jest.spyOn(video, "play");

  video.play();

  expect(spy).toHaveBeenCalled();
  expect(spy).toHaveReturnedWith(true);

  spy.mockRestore(); // Restore original implementation
});
```

### Partial Mocking

```javascript
jest.mock("./utils", () => ({
  ...jest.requireActual("./utils"), // Keep real implementations
  heavyComputation: jest.fn().mockReturnValue(42) // Mock only this
}));
```

### Timer Mocks

```javascript
jest.useFakeTimers();

test("waits 1 second before resolving", () => {
  const promise = delay(1000);

  jest.advanceTimersByTime(1000);

  return expect(promise).resolves.toBeUndefined();
});

test("setInterval callback", () => {
  const callback = jest.fn();
  setInterval(callback, 1000);

  jest.advanceTimersByTime(5000);

  expect(callback).toHaveBeenCalledTimes(5);
});

// Restore real timers after test
afterEach(() => {
  jest.useRealTimers();
});
```

---

## Snapshot Testing

Snapshot tests capture the output of a component or function and compare it to a saved snapshot.

```javascript
// config.js
function generateConfig(env) {
  return {
    database: {
      host: env === "production" ? "prod.db.com" : "localhost",
      port: 5432,
      pool: {
        min: 2,
        max: env === "production" ? 50 : 10
      }
    },
    cache: {
      enabled: env === "production",
      ttl: 3600
    }
  };
}

module.exports = { generateConfig };
```

```javascript
// config.test.js
const { generateConfig } = require("./config");

test("generates production config", () => {
  const config = generateConfig("production");
  expect(config).toMatchSnapshot();
});

test("generates development config", () => {
  const config = generateConfig("development");
  expect(config).toMatchSnapshot();
});
```

### Inline Snapshots

```javascript
test("formats user", () => {
  const user = { name: "Alice", age: 30 };
  expect(JSON.stringify(user, null, 2)).toMatchInlineSnapshot(`
    "{
      \\"name\\": \\"Alice\\",
      \\"age\\": 30
    }"
  `);
});
```

### Updating Snapshots

```bash
npm test -- --updateSnapshot     # Update all snapshots
npm test -- -u                   # Shorthand
npm test -- --updateSnapshot --testPathPattern=config  # Update specific
```

> **Use snapshots wisely**. They're great for complex objects and UI components, but don't use them for values that change frequently (timestamps, IDs, random values).

---

## Code Coverage

### Generating Coverage Reports

```bash
npm test -- --coverage
```

### Coverage Metrics

```
--------------------|---------|----------|---------|---------|-------------------
File                | % Stmts | % Branch | % Funcs | % Lines | Uncovered Line #s
--------------------|---------|----------|---------|---------|-------------------
All files           |   85.71 |    66.66 |   80.00 |   85.71 |
 utils.js           |  100.00 |   100.00 |  100.00 |  100.00 |
 calculator.js      |   75.00 |    50.00 |   66.66 |   75.00 | 15-20
--------------------|---------|----------|---------|---------|-------------------
```

| Metric | Description |
|--------|-------------|
| **Statements** | Percentage of executable statements run |
| **Branch** | Percentage of conditional branches taken |
| **Functions** | Percentage of functions called |
| **Lines** | Percentage of lines executed |

### Coverage Configuration

```javascript
// jest.config.js
module.exports = {
  collectCoverageFrom: [
    "src/**/*.{js,ts}",
    "!src/**/*.test.{js,ts}",
    "!src/index.js",
    "!src/types/**"
  ],
  coverageThreshold: {
    global: {
      branches: 80,
      functions: 80,
      lines: 80,
      statements: 80
    }
  },
  coverageReporters: ["text", "text-summary", "lcov", "html"]
};
```

### Ignoring Code from Coverage

```javascript
/* istanbul ignore next */
function debugOnly() {
  console.log("This won't be counted in coverage");
}

function complexFunction(condition) {
  if (condition) {
    return doSomething();
  }

  /* istanbul ignore else */
  if (otherCondition) {
    return doSomethingElse();
  }
}
```

---

## Testing Patterns and Best Practices

### AAA Pattern (Arrange, Act, Assert)

```javascript
test("calculates discount for premium users", () => {
  // Arrange
  const user = createUser({ type: "premium" });
  const product = createProduct({ price: 100 });

  // Act
  const price = calculatePrice(user, product);

  // Assert
  expect(price).toBe(80); // 20% discount
});
```

### Test Naming

```javascript
// ❌ Vague
test("user test", () => { ... });

// ✅ Descriptive: what is being tested, under what conditions, expected result
test("returns 20% discount for premium users", () => { ... });
test("throws ValidationError when email is invalid", () => { ... });
test("caches result for 5 minutes to reduce database load", () => { ... });
```

### One Concept Per Test

```javascript
// ❌ Multiple concepts in one test
test("user functions", () => {
  const user = createUser("Alice");
  expect(user.name).toBe("Alice");
  expect(user.email).toBe("alice@example.com");
  expect(user.validate()).toBe(true);
  expect(() => user.setAge(-1)).toThrow();
});

// ✅ Separate tests
describe("User", () => {
  test("sets name on creation", () => { ... });
  test("generates email from name", () => { ... });
  test("validates with correct data", () => { ... });
  test("throws on invalid age", () => { ... });
});
```

### Testing Edge Cases

```javascript
describe("divide", () => {
  test("divides positive numbers", () => {
    expect(divide(10, 2)).toBe(5);
  });

  test("divides negative numbers", () => {
    expect(divide(-10, 2)).toBe(-5);
  });

  test("returns decimal result", () => {
    expect(divide(10, 3)).toBeCloseTo(3.33, 2);
  });

  test("throws on division by zero", () => {
    expect(() => divide(10, 0)).toThrow("Cannot divide by zero");
  });

  test("handles very large numbers", () => {
    expect(divide(1e308, 2)).toBe(5e307);
  });

  test("handles zero numerator", () => {
    expect(divide(0, 5)).toBe(0);
  });
});
```

### Parameterized Tests

```javascript
describe("isValidEmail", () => {
  test.each([
    ["user@example.com", true],
    ["user.name@example.co.uk", true],
    ["invalid", false],
    ["@example.com", false],
    ["user@", false],
    ["", false],
    [null, false]
  ])("validates '%s' as %s", (email, expected) => {
    expect(isValidEmail(email)).toBe(expected);
  });
});
```

---

## Testing Node.js Applications

### Testing Express Routes

```javascript
// app.js
const express = require("express");
const app = express();
app.use(express.json());

app.get("/users/:id", (req, res) => {
  res.json({ id: req.params.id, name: "Alice" });
});

app.post("/users", (req, res) => {
  const { name, email } = req.body;
  res.status(201).json({ id: 1, name, email });
});

module.exports = app;
```

```javascript
// app.test.js
const request = require("supertest");
const app = require("./app");

describe("GET /users/:id", () => {
  test("returns user by id", async () => {
    const response = await request(app)
      .get("/users/123")
      .expect(200);

    expect(response.body).toEqual({
      id: "123",
      name: "Alice"
    });
  });
});

describe("POST /users", () => {
  test("creates a new user", async () => {
    const response = await request(app)
      .post("/users")
      .send({ name: "Bob", email: "bob@example.com" })
      .expect(201);

    expect(response.body).toMatchObject({
      id: expect.any(Number),
      name: "Bob",
      email: "bob@example.com"
    });
  });

  test("returns 400 for invalid data", async () => {
    await request(app)
      .post("/users")
      .send({ name: "" })
      .expect(400);
  });
});
```

### Testing with Database

```javascript
// userRepository.test.js
const { connect, disconnect } = require("../db");
const UserRepository = require("../repositories/UserRepository");

describe("UserRepository", () => {
  beforeAll(async () => {
    await connect("test_database");
  });

  afterAll(async () => {
    await disconnect();
  });

  beforeEach(async () => {
    await UserRepository.deleteAll();
  });

  test("creates and retrieves user", async () => {
    const created = await UserRepository.create({
      name: "Alice",
      email: "alice@example.com"
    });

    const found = await UserRepository.findById(created.id);
    expect(found).toMatchObject({ name: "Alice", email: "alice@example.com" });
  });
});
```

---

## Common Mistakes

### Mistake 1: Testing Implementation Instead of Behavior

```javascript
// ❌ Brittle test tied to implementation
test("calculatePrice", () => {
  const calculator = new PriceCalculator();
  const spy = jest.spyOn(calculator, "applyDiscount");

  calculator.calculatePrice(user, product);

  expect(spy).toHaveBeenCalled(); // Tests HOW, not WHAT
});

// ✅ Test the outcome
test("applies 20% discount for premium users", () => {
  const calculator = new PriceCalculator();
  const price = calculator.calculatePrice(
    { type: "premium" },
    { basePrice: 100 }
  );

  expect(price).toBe(80);
});
```

### Mistake 2: Tests That Don't Actually Test Anything

```javascript
// ❌ Test always passes
test("fetches data", async () => {
  const data = await fetchData();
  expect(data).toBeDefined(); // Always passes!
});

// ✅ Assert specific values
test("fetches user with correct structure", async () => {
  const user = await fetchUser(1);
  expect(user).toEqual({
    id: 1,
    name: "Alice",
    email: "alice@example.com"
  });
});
```

### Mistake 3: Shared Mutable State Between Tests

```javascript
// ❌ Tests depend on each other
let counter = 0;

test("increments counter", () => {
  counter++;
  expect(counter).toBe(1);
});

test("increments counter again", () => {
  counter++; // counter is now 2 because of previous test!
  expect(counter).toBe(1); // FAILS
});

// ✅ Each test is independent
let counter;

beforeEach(() => {
  counter = 0;
});

test("increments from zero", () => {
  counter++;
  expect(counter).toBe(1);
});

test("starts fresh each time", () => {
  expect(counter).toBe(0);
});
```

### Mistake 4: Not Cleaning Up Mocks

```javascript
// ❌ Mock leaks to other tests
jest.mock("./api");

describe("Service A", () => {
  test("uses mocked API", () => {
    const { fetchData } = require("./api");
    fetchData.mockResolvedValue({ data: "a" });
    // ...test
  });
});

describe("Service B", () => {
  test("expects fresh mock", () => {
    const { fetchData } = require("./api");
    // fetchData still has mock from previous test!
  });
});

// ✅ Clear or reset mocks
describe("Service A", () => {
  afterEach(() => {
    jest.clearAllMocks();    // Clear call history
    // or
    jest.resetAllMocks();     // Reset to undefined
    // or
    jest.restoreAllMocks();   // Restore original implementations
  });
});
```

### Mistake 5: Ignoring Async Test Failures

```javascript
// ❌ Test passes even if promise rejects
test("fetches data", () => {
  fetchData().then(data => {
    expect(data).toBe("expected");
  });
  // Test exits before promise resolves!
});

// ✅ Return the promise
test("fetches data", () => {
  return fetchData().then(data => {
    expect(data).toBe("expected");
  });
});

// ✅ Or use async/await
test("fetches data", async () => {
  const data = await fetchData();
  expect(data).toBe("expected");
});
```

---

## Practice Exercises

### Exercise 1: Write Tests for a Calculator

Implement and test a calculator with these requirements:

```javascript
// Implement and test:
// - add(a, b) — returns sum
// - subtract(a, b) — returns difference
// - multiply(a, b) — returns product
// - divide(a, b) — returns quotient, throws on divide by zero
// - calculate(expression) — parses "2 + 3" and returns 5

describe("Calculator", () => {
  // Write tests covering:
  // - Basic operations with positive, negative, and zero
  // - Division by zero error
  // - Floating point precision
  // - Expression parsing
});
```

### Exercise 2: Mock External Dependencies

Test a weather service that calls an external API:

```javascript
// weatherService.js
const axios = require("axios");

async function getWeather(city) {
  const response = await axios.get(`https://api.weather.com/v1/current?city=${city}`);
  return {
    city,
    temperature: response.data.temp,
    condition: response.data.condition
  };
}

// Test without making real HTTP requests
// Mock axios and verify:
// - Correct URL is called
// - Response is transformed correctly
// - Errors are handled properly
```

### Exercise 3: Test-Driven Development

Practice TDD by writing tests BEFORE implementation:

```javascript
// Task: Implement a PasswordValidator
// Rules:
// - Minimum 8 characters
// - At least one uppercase letter
// - At least one lowercase letter
// - At least one number
// - At least one special character (!@#$%^&*)

// Step 1: Write all tests (they should fail)
// Step 2: Implement PasswordValidator
// Step 3: Refactor while keeping tests green

describe("PasswordValidator", () => {
  test.each([
    ["Short1!", false, "too short"],
    ["nouppercase1!", false, "no uppercase"],
    ["NOLOWERCASE1!", false, "no lowercase"],
    ["NoNumber!", false, "no number"],
    ["NoSpecial123", false, "no special character"],
    ["Valid1!Pass", true, "valid password"]
  ])("validates '%s' as %s (%s)", (password, expected) => {
    expect(PasswordValidator.isValid(password)).toBe(expected);
  });
});
```

### Exercise 4: Test an Express API

Write integration tests for a CRUD API:

```javascript
// Test endpoints:
// GET    /tasks        — list all tasks
// GET    /tasks/:id    — get task by id
// POST   /tasks        — create task
// PUT    /tasks/:id    — update task
// DELETE /tasks/:id    — delete task

// Requirements:
// - Test successful operations
// - Test validation errors (400)
// - Test not found (404)
// - Test with database (use in-memory or test DB)
```

### Exercise 5: Achieve 100% Coverage

Given this module, write tests to achieve 100% statement and branch coverage:

```javascript
// discount.js
function calculateDiscount(user, total) {
  let discount = 0;

  if (user.membership === "gold") {
    discount = total * 0.2;
  } else if (user.membership === "silver") {
    discount = total * 0.1;
  }

  if (total > 1000) {
    discount += total * 0.05;
  }

  return Math.min(discount, total * 0.5); // Max 50% discount
}

module.exports = { calculateDiscount };
```

Run `npm test -- --coverage` and ensure all metrics show 100%.

---

## Summary

- **Testing** provides confidence, documentation, and enables fearless refactoring
- Follow the **Testing Pyramid**: many unit tests, fewer integration tests, minimal E2E tests
- **Jest** is a zero-config testing framework with built-in mocking, coverage, and snapshots
- **Matchers** (`toBe`, `toEqual`, `toContain`, etc.) make assertions expressive and readable
- Always **return promises** or use `async/await` when testing asynchronous code
- Use **`expect.assertions(n)`** to ensure async assertions are called
- **`beforeEach`/`afterEach`** set up and clean up test state; **`beforeAll`/`afterAll`** for expensive one-time setup
- **Mocking** isolates tests from external dependencies using `jest.fn()`, `jest.mock()`, and `jest.spyOn()`
- **Snapshot tests** capture complex output but should be reviewed before committing updates
- **Code coverage** measures test thoroughness; aim for high coverage but focus on meaningful tests
- Write **behavior-focused tests**, not implementation-focused tests
- Keep tests **independent** — never let one test depend on another's state
- Always **clean up mocks** between tests with `clearAllMocks()` or `resetAllMocks()`

---

## Next Steps

- **Backend Development with Node.js** — apply testing to real API development
- **Integration Testing** — test how your application interacts with databases and external services
- **E2E Testing** — learn Playwright or Cypress for full user journey testing
- **Test-Driven Development** — practice writing tests before code
- **Mutation Testing** — verify test quality with tools like Stryker

Happy coding! 🚀
