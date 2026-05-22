# Type Coercion and Abstract Operations in JavaScript

## Overview

JavaScript is a **dynamically typed** language, which means variables can hold values of any type and those types can change. **Type coercion** is JavaScript's automatic or implicit conversion of values from one data type to another. Understanding when and how coercion happens is critical for writing predictable, bug-free code — and for acing technical interviews.

---

## Types in JavaScript

JavaScript has **8 built-in types**:

| Type | Examples |
|------|----------|
| `undefined` | `undefined` |
| `null` | `null` |
| `boolean` | `true`, `false` |
| `number` | `42`, `3.14`, `NaN`, `Infinity` |
| `bigint` | `9007199254740991n` |
| `string` | `"hello"`, `'world'` |
| `symbol` | `Symbol("id")` |
| `object` | `{}`, `[]`, `function(){}` |

> Note: `typeof null` returns `"object"` — this is a historical bug in JavaScript that cannot be fixed without breaking existing code.

---

## Two Kinds of Type Conversion

### 1. Explicit Coercion (Type Casting)

You intentionally convert a value from one type to another.

```javascript
String(42);          // "42"
Number("3.14");      // 3.14
Boolean(1);          // true

// Alternative syntax
(42).toString();     // "42"
+"42";               // 42 (unary plus)
!!"hello";           // true (double NOT)
```

### 2. Implicit Coercion (Automatic)

JavaScript automatically converts types when an operation requires it.

```javascript
"5" + 3;             // "53" (number coerced to string)
"5" - 3;             // 2 (string coerced to number)
if ("hello") { ... } // "hello" coerced to true
```

---

## Abstract Operations

The ECMAScript specification defines **abstract operations** — internal algorithms that describe how type coercion works. Understanding these helps explain JavaScript's seemingly "weird" behavior.

### ToPrimitive

Converts a non-primitive value to a primitive.

```javascript
// Objects can define how they're converted
const obj = {
  valueOf() { return 42; },
  toString() { return "hello"; }
};

console.log(obj + 1);      // 43 (uses valueOf in numeric context)
console.log(String(obj));  // "hello" (uses toString in string context)
```

**Hint types:**
- `number` (default for mathematical operations)
- `string` (for string concatenation)
- `default` (rare, treated like `number`)

**Order:**
1. If hint is `string`: try `toString()`, then `valueOf()`
2. If hint is `number`: try `valueOf()`, then `toString()`

### ToString

Converts any value to a string:

```javascript
String(null);        // "null"
String(undefined);   // "undefined"
String(true);        // "true"
String(42);          // "42"
String([1, 2, 3]);   // "1,2,3"
String({});          // "[object Object]"
```

### ToNumber

Converts any value to a number:

```javascript
Number("42");        // 42
Number("3.14");      // 3.14
Number("");          // 0
Number("   ");       // 0
Number("42px");      // NaN
Number(true);        // 1
Number(false);       // 0
Number(null);        // 0
Number(undefined);   // NaN
```

### ToBoolean

Converts any value to a boolean. This is the simplest — it's purely based on whether the value is **truthy** or **falsy**.

**Falsy values** (coerce to `false`):
- `undefined`
- `null`
- `false`
- `+0`, `-0`, `NaN`
- `""` (empty string)

**Everything else is truthy**, including:
- `"0"` (string with zero)
- `"false"` (string with text "false")
- `[]` (empty array)
- `{}` (empty object)
- `function(){}` (any function)

---

## The `==` vs `===` Difference

### Strict Equality (`===`)

No coercion. Types must match, and values must be identical.

```javascript
5 === 5;             // true
5 === "5";           // false (different types)
null === undefined;  // false
NaN === NaN;         // false (special case!)
```

### Loose Equality (`==`)

Performs type coercion before comparison. The rules are complex:

```javascript
5 == "5";            // true (string "5" → number 5)
0 == false;          // true (both coerce to 0)
"" == false;         // true (both coerce to 0)
null == undefined;   // true (special case)

// The weird ones
[] == false;         // true ([] → "" → 0, false → 0)
[] == ![];           // true ([] → 0, ![] → false → 0)
"" == 0;             // true
"" == false;         // true
0 == false;          // true

// But...
[] == [];            // false (different objects)
{} == {};            // false (different objects)
NaN == NaN;          // false
```

**The Abstract Equality Comparison Algorithm:**
1. If types are the same, compare values
2. If one is `null` and other is `undefined`, they're equal
3. If one is a number and other is a string, convert string to number
4. If one is a boolean, convert it to number (true→1, false→0)
5. If one is an object and other is a primitive, convert object to primitive
6. Otherwise, not equal

> **Best Practice**: Always use `===` and `!==`. Only use `==` when you explicitly want coercion (rare).

---

## Coercion in Different Operations

### String Concatenation (`+`)

If either operand is a string, the other is converted to a string:

```javascript
"5" + 3;             // "53"
5 + "3";             // "53"
5 + 3;               // 8
"5" + true;          // "5true"
"5" + null;          // "5null"
"5" + undefined;     // "5undefined"
```

### Arithmetic Operations (`-`, `*`, `/`, `%`)

Both operands are converted to numbers:

```javascript
"5" - 3;             // 2
"5" * "2";           // 10
"10" / "2";          // 5
"10" - "abc";        // NaN
```

### Relational Operators (`<`, `>`, `<=`, `>=`)

Both operands are converted to primitives, then compared:

```javascript
5 > "3";             // true ("3" → 3)
"10" > "2";          // false (string comparison: "1" < "2")
"abc" > "def";       // false (lexicographic)
```

### Logical Operators (`&&`, `||`, `!`)

Operands are converted to booleans, but the return value is the **original operand**:

```javascript
// && returns the first falsy value, or the last value
0 && "hello";        // 0
"hello" && "world";  // "world"

// || returns the first truthy value, or the last value
0 || "hello";        // "hello"
"hello" || "world";  // "hello"

// ! always returns boolean
!"hello";            // false
!!"hello";           // true
```

### The Nullish Coalescing Operator (`??`)

Returns the right-hand operand only if the left is `null` or `undefined`:

```javascript
0 ?? "default";      // 0 (0 is not nullish)
"" ?? "default";     // "" (empty string is not nullish)
null ?? "default";   // "default"
undefined ?? "default"; // "default"
```

---

## Coercion Gotchas

### Gotcha 1: Array to Primitive

```javascript
[] + [];             // "" (both arrays → empty strings)
[] + {};             // "[object Object]"
{} + [];             // 0 ({} interpreted as empty block, +[] → 0)
{} + {};             // NaN or "[object Object][object Object]" (context dependent!)
```

### Gotcha 2: `typeof` Quirks

```javascript
typeof null;         // "object" (bug!)
typeof [];           // "object" (arrays are objects)
typeof {};           // "object"
typeof function(){}; // "function" (functions are callable objects)
```

### Gotcha 3: `NaN` is Not Equal to Anything

```javascript
NaN === NaN;         // false
NaN == NaN;          // false
isNaN(NaN);          // true
Number.isNaN(NaN);   // true (preferred)
```

### Gotcha 4: `parseInt` vs `Number`

```javascript
Number("10px");      // NaN
parseInt("10px");    // 10 (stops at non-digit)
parseInt("10", 2);   // 2 (binary!)
parseInt("0x10");    // 16 (hexadecimal!)

// Always specify radix!
parseInt("08", 10);  // 8
```

### Gotcha 5: Boolean Object vs Primitive

```javascript
false == 0;          // true
new Boolean(false) == 0;  // true (object coerced to primitive)
new Boolean(false) == false; // true

// But...
if (new Boolean(false)) {
  console.log("This runs!"); // Objects are always truthy!
}
```

---

## Practical Coercion Rules

### Safe Coercion Patterns

```javascript
// String to Number
const num = Number(input);
const num2 = parseInt(input, 10);
const num3 = +input;

// Number to String
const str = String(num);
const str2 = num.toString();
const str3 = `${num}`;

// Any to Boolean
const bool = Boolean(value);
const bool2 = !!value;

// Nullish check
if (value != null) { }      // Checks for both null and undefined
if (value !== null && value !== undefined) { } // Same, more explicit
```

### Avoiding Unintended Coercion

```javascript
// ❌ Implicit coercion
if (count == 1) { }
const total = num + "";

// ✅ Explicit coercion
if (count === 1) { }
const total = String(num);

// ❌ Reliance on falsy
if (name) { } // Fails for empty string

// ✅ Explicit check
if (name !== "") { }
if (name != null) { } // null or undefined
```

---

## Coercion in Template Literals

```javascript
const num = 42;
const bool = true;
const obj = { a: 1 };

console.log(`${num}`);       // "42" (number → string)
console.log(`${bool}`);      // "true" (boolean → string)
console.log(`${obj}`);       // "[object Object]"
console.log(`${[1, 2, 3]}`); // "1,2,3"
```

---

## Common Mistakes

### Mistake 1: Comparing Arrays

```javascript
[1, 2, 3] === [1, 2, 3];     // false (different references)
[1, 2, 3] == [1, 2, 3];      // false

// ✅ Compare contents
JSON.stringify([1, 2, 3]) === JSON.stringify([1, 2, 3]); // true
```

### Mistake 2: Checking for Empty Array

```javascript
// ❌ [] is truthy!
if ([]) { console.log("runs!"); } // This runs!

// ✅ Check length
if (arr.length === 0) { }
```

### Mistake 3: Implicit Coercion in Comparisons

```javascript
// ❌ Dangerous
if (userInput == 1) { }

// ✅ Safe
if (Number(userInput) === 1) { }
```

### Mistake 4: Coercion with `+` Operator

```javascript
const a = "5";
const b = 3;

// ❌ Unexpected result
console.log(a + b); // "53"

// ✅ Explicit conversion
console.log(Number(a) + b); // 8
console.log(a + String(b)); // "53" (if string intended)
```

---

## The `Symbol.toPrimitive` Method

You can customize how objects are coerced:

```javascript
const obj = {
  [Symbol.toPrimitive](hint) {
    if (hint === "number") {
      return 42;
    }
    if (hint === "string") {
      return "hello";
    }
    return true;
  }
};

console.log(+obj);       // 42
console.log(`${obj}`);   // "hello"
console.log(obj + 1);    // 43
```

---

## Practice Exercises

### Exercise 1: Predict the Output

What do these evaluate to?

```javascript
"foo" + +"bar";
[] == ![];
typeof NaN;
null == undefined;
0 == "0";
0 === "0";
```

### Exercise 2: Safe Type Checker

Write a function that safely checks if a value is a number.

```javascript
function isNumber(value) {
  // Should return true for 42, 3.14, "42"?
  // Should return false for NaN, null, undefined, "42px"
}
```

### Exercise 3: Coercion Debugger

Explain step-by-step how JavaScript evaluates:

```javascript
[] + {} + [1] + false - null + true
```

### Exercise 4: Safe Equality

Write a `safeEquals(a, b)` function that behaves like `===` but treats `NaN` as equal to `NaN`.

### Exercise 5: Type Validator

Create a function that validates user input types.

```javascript
function validateInput(input, expectedType) {
  // Return true if input matches expectedType
  // Handle: "string", "number", "boolean", "array", "object", "null"
}
```

---

## Summary

| Concept | Key Point |
|---------|-----------|
| **Explicit Coercion** | You intentionally convert: `String()`, `Number()`, `Boolean()` |
| **Implicit Coercion** | JavaScript converts automatically during operations |
| **`===` vs `==`** | `===` is strict (no coercion); `==` allows coercion (avoid!) |
| **Falsy values** | `0`, `""`, `null`, `undefined`, `NaN`, `false` |
| **Truthy values** | Everything else, including `[]`, `{}`, `"0"` |
| **`+` operator** | If any operand is string, concatenation occurs |
| **Arithmetic** | Always converts to numbers |
| **`??` operator** | Only checks for `null`/`undefined`, not all falsy |
| **`typeof null`** | `"object"` — a historical bug |
| **Custom coercion** | Use `Symbol.toPrimitive`, `valueOf()`, `toString()` |

---

## Next Steps

Understanding coercion prepares you for:
- **Objects and Prototypes** — custom `toString` and `valueOf`
- **Strict Mode** — prevents some implicit coercion
- **TypeScript** — adds static typing to avoid coercion bugs

Happy coding! 🚀
