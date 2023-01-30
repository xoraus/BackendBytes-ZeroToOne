## Function Expression

```js
var teacher = "Sanket"; // global
function fun() { // global
    console.log(teacher); // no error will be given
    // console.log(content); // throws an error
    var teacher = "Pulkit"; // scope of fun
    let content = "JS"; // content will be access only post declaration
    if(content == "JS") {
        let hours = "120+";
        console.log(content,hours);
    }
    console.log(teacher, content);
}

fun();
console.log(teacher);
// console.log(content);
```

- `let` in javascript
	- The **`let`** declaration declares a block-scoped local variable, optionally initializing it to a value.
- How do you create a block?
	- { }
- Scope determines the accessibility (visibility) of variables.
	- JavaScript has 3 types of scope:
		-   **Block scope**
		-   **Function scope**
		-   **Global scope**
	- Function scope is within the function.  
	- Block scope is within curly brackets.

```js
var character4 = 
function foo() {
    if(true) {
        var character1 = "Robin"      //function scope
        let character2 = "Ted"        //block scope
        const character3 = "Barney"   //block scope
    }
    console.log(character1)  //Robin
    console.log(character2)  //not defined
    console.log(character3). //not defined
}
```

- Difference between function scope and block scope?
	- **Function Scope**: When a variable is declared inside a function, it is only accessible within that function and cannot be used outside that function.
	- **Block Scope**: A variable when declared inside the if or switch conditions or inside for or while loops, are accessible within that particular condition or loop. To be concise, the variables declared inside the curly braces are called within the block scope.
- `let` is accessible in sub blocks.
- [Demystifying JavaScript Variable Scope and Hoisting — SitePoint](https://www.sitepoint.com/demystifying-javascript-variable-scope-hoisting/)
	- In JavaScript, variables with the same name can be specified at multiple layers of nested scope. In such case local variables gain priority over global variables. If you declare a local variable and a global variable with the same name, the local variable will take precedence when you use it inside a function. This type of behavior is called shadowing. Simply put, the inner variable shadows the outer.
- [Is this an example of variable shadowing in JavaScript? - Stack Overflow](https://stackoverflow.com/a/11901489/6375464)
	- In computer programming, variable shadowing occurs when a variable declared within a certain scope (decision block, method, or inner class) has the same name as a variable declared in an outer scope. This outer variable is said to be shadowed...
- What is Temporal Dead Zone?
	- Temporal Dead Zone is **the period of time during which the let and const declarations cannot be accessed**. Temporal Dead Zone starts when the code execution enters the block which contains the let or const declaration and continues until the declaration has executed.
	- It the region before the declaration of the block scope variable.
	- Suppose you attempt to access a variable before its complete initialization. In such a case, JavaScript will throw a `ReferenceError`.

**Where Exactly Is the Scope of a Temporal Dead Zone?**

A block’s temporal dead zone starts at the beginning of the block’s local scope. It ends when the computer fully initializes your variable with a value.

**Here’s an example:**

```js
{
  // bestFood’s TDZ starts here (at the beginning of this block’s local scope)
  // bestFood’s TDZ continues here
  // bestFood’s TDZ continues here
  // bestFood’s TDZ continues here
  console.log(bestFood); // returns ReferenceError because bestFood’s TDZ continues here
  // bestFood’s TDZ continues here
  // bestFood’s TDZ continues here
  let bestFood = "Vegetable Fried Rice"; // bestFood’s TDZ ends here
  // bestFood’s TDZ does not exist here
  // bestFood’s TDZ does not exist here
  // bestFood’s TDZ does not exist here
}
```

Now, consider this example:

```js
{
  // TDZ starts here (at the beginning of this block’s local scope)
  // bestFood’s TDZ continues here
  // bestFood’s TDZ continues here
  // bestFood’s TDZ continues here
  // bestFood’s TDZ continues here
  // bestFood’s TDZ continues here
  // bestFood’s TDZ continues here
  let bestFood; // bestFood’s TDZ ends here
  console.log(bestFood); // returns undefined because bestFood’s TDZ does not exist here
  bestFood = "Vegetable Fried Rice"; // bestFood’s TDZ does not exist here
  console.log(bestFood); // returns "Vegetable Fried Rice" because bestFood’s TDZ does not exist here
}
```

**How Does Var’s TDZ Differ from Let and Const Variables?**

The main difference between the temporal dead zone of a `var`, `let`, and `const` variable is when their TDZ ends.

For instance, consider this code:

```js
{
  // bestFood’s TDZ starts and ends here
  console.log(bestFood); // returns undefined because bestFood’s TDZ does not exist here
  var bestFood = "Vegetable Fried Rice"; // bestFood’s TDZ does not exist here
  console.log(bestFood); // returns "Vegetable Fried Rice" because bestFood’s TDZ does not exist here
  // bestFood’s TDZ does not exist here
  // bestFood’s TDZ does not exist here
}
```

- In parsing we do scope resolution, printing happens in execution phase.

- Difference between `let` and `var`
	- The keywords `let` and `var` both declare new variables in JavaScript. The difference between `let` and `var` is in the scope of the variables they create:
		-   Variables declared by `let` are only available inside the block where they’re defined.
		-   Variables declared by `var` are available throughout the function in which they’re declared.

Consider the difference between these two JavaScript functions:

```javascript
function varScoping() {
  var x = 1;

  if (true) {
    var x = 2;
    console.log(x); // will print 2
  }

  console.log(x); // will print 2
}

function letScoping() {
  let x = 1;

  if (true) {
    let x = 2;
    console.log(x); // will print 2
  }

  console.log(x); // will print 1
}
```

In `varScoping()`, one `x` variable is used throughout the function, even though an `x` variable is declared in two different places with different values.

In `letScoping()`, two distinct `x` variables are used – one appears in the main function body and another in the `if` block.

- rule of thumb
	- var - function scope
	- let - block scope

---

**Var**

- The var in JavaScript is used to store information it can take any value for example **integer**, **float**, or **string**. We call it a declaration of a variable when the var is created. The value stored in var is empty after the declaration. When we store any value in var it is called initializing the value for var in JavaScript.
- The var has a global scope which means that even if it is declared in a block the variable can be accessed outside the block too.
- As we have already seen that var is global scoped which means we can declare var anywhere in the code not necessarily on the top. Using the variable before it is declared is called hoisting.

**var_in_block.js**
```js
function fun() {
    var i = 5;
    while(i < 10) {
        var x = i; 
        i++;
    }
    console.log(x);
}
fun(); 

let i = 1;
console.log(y); // undefined here
while(i < 5) {
    var y = 10; 
    i++;
}
console.log(y); // 10
// redeclaration is not allowed with let, but it is allowed with var
// let x = 9;
// let x = 10;
```

- `var x` & `var y` will avoid the while loop and it will get the outside enclosing scope whether it's function scope in case if first while loop (above example) or global scope (2nd while loop).

**usecasevar.js**
```js
function fun(x) {
    let i; // var i
    if(x %2 == 0) {
        i = 0;
    } else {
        i = 1;
    }
}

function gun(x) {
    if(x %2 == 0) {
        var i = 0;
    } else {
        var i = 1;
    }
    console.log(i); 
}

gun(10); // 0
```

- gun()  is aclean implementation of fun()

**usecaselet.js**
```js
function fun() {
    for(let i = 0; i < 10; i++) {
        // do something
    }
    console.log(i);
}

function process(x, y) {
    if(x > y) {
        // var temp = x;
        let temp = x;
        x = y;
        y = temp;
    }
    return y - x;
}

fun();
```

- If a variable is not going to be used outside a block then just make it `let` instead of `var`. Let's not give the user to use or misuse it.
-  There are use cases for both let and var, you need to make the decision.
- var allows redeclaration.
- let does not allows redeclaration.

---

**Const**

- The const declaration creates block-scoped constants, much like variables declared using the let keyword. 
- The value of a const can't be changed through reassignment (i.e. by using the assignment operator), and it can't be redeclared (i.e. through a variable declaration). 
- However, if a constant is an object or array, its properties or items can be updated or removed.
**Properties of Const**
	-   Cannot be reassigned.
	-   It’s Block Scope
	-   It can be assigned to the variable on the declaration line.
	-   It’s a Primitive value.
	-   The property of a const object can be changed but it cannot be changed to a reference to the new object
	-   The values inside the const array can be changed, it can add new items to const arrays but it cannot reference a new array.
	-   Re-declaring of a const variable inside different block scopes is allowed.
	-   Cannot be Hoisted.
	-   Creates only read-only references to value.

```js
const x = 10
x = 9 // this will throw error
```

```js
const obj = {x: 10};
obj.y = 9 // this is fine - adding a new property to the object.

obj = {} // this will throw an error
```

- `constant` stops reassignment, it does not stop updates to an object or an array.
- `const { bar } = foo;` This declaration creates a constant whose scope can be either global 4r local to the block in which is declared.
- *Task* - Does the Temporal dead zone exist for `const` ?
- `const y; // This is not allowed` - You cannot have uninitialized `const`

```js
const x = 10;

x.val = 0;

console.log(x) // 10
```
- momentarily it will be treated as an object.
- Concept of Boxing / Auto Boxing / Unboxing.
	- The conversion of primitive data types to object data types, i.e., boxing and it's vice-versa, known as unboxing.
	- In JavaScript and other languages which are based on object-oriented programming, primitive values don't have any properties or methods. If you want to use them, you need to use a wrapper to convert them into object data types.
	- Boxing and unboxing are important and regularly used practices. But in different languages or different implementations, the wrapper can consume more memory and take more time to run the code than primitive types. But, if we talk about higher-level data structures, the wrappers are very useful. That also increases flexibility in your code.
	- It is preferred to use boxing implicitly because browser engines are optimized.
	- AutoBoxing
		- Boxing is the process in which a primitive value is wrapped in an Object. When a primitive type is treated as an object, e.g., calling the toUpperCase() function, JavaScript would automatically wrap the primitive type into the corresponding object type. This new object type is then linked to the related built-in <.prototype>, so you can use prototype methods on primitive types.
	- Manual Boxing
		- AutoBoxing is quite easy to implement, but it has some issues too. It's not usually a good idea to directly use a boxed object wrapper because sometimes that generates results that are not expected at all.
	- Unboxing
		- Unboxing is converting the reference or object types to basic or primitive data types. It is the opposite of boxing or packing. 
		- One of the easiest ways to convert the object wrapper to the respective primitive data type is to use the valueOf() method.

**Note** - Whenever new scope comes, either a block scope or function scope and in that you have a formal declaration then that formal declaration will be prioritized.

---

### Function Expression

**What are JavaScript functions?**

Functions are essential building blocks in JavaScript, they are packed with logic for performing tasks. A function can be likened to a procedure that performs a task or calculates a value. However, for this procedure to be considered as a function, it needs to take some input and return an output.

Refer this [Different Ways to Declare Functions - Beginner JavaScript - Wes Bos](https://wesbos.com/javascript/02-functions/different-ways-to-declare-functions)

**different ways a function can be declared in JavaScript:**

-   Function Expression
	- A function can be declared using a function expression. It is declared quite differently from the general syntax because it uses a variable to denote the name of the function. This variable comes before the keyword `function`. 
-   Anonymous Functions
	- Anonymous function declaration allows function names to appear hidden in the declaration itself. In the general function declaration syntax, the function name is attached to the function keyword, but an anonymous function is declared using only the function keyword and a parenthesis.
-   Immediately Invoked Function Expression
	- IIFE are functions that can be stated as expressions or normal declarations and use the anonymous property of the function expression to execute its code. If you want to execute a function immediately after the declaration, use IIFE. This is executed by wrapping the anonymous function in parentheses and ending it with a semicolon:
-   Constructor Functions
-   Getter Functions
-   Hoisting
-   Arrow Functions
	- This is a feature available in the ES6 version of JavaScript and as such has not stayed in the space for as long as the other features in the function declaration. It is generally a cleaner way of creating JavaScript functions and it is similar to the function expression.

**functionexpression.js**
```js
function fun() { // function declaration
    // some impl
}

let f = function gun() { // named function expression
    // some impl
}

let a = function() { // anonymous function expression
    // okk some more impl
}

(function x() {
    // can you stop it ?
}) // function expression

(function (){
    // i am done
})

let y = () => { // Arrow function

}
```

- There are key differences in all of the above.
- What is function declaration in Javascript?
	- starts with `function`
- What id named function function expression?
	- starts with `let`

**Function Expression**
```js
let x = function () {
    console.log('HI');
}
console.log(x);
x();
```

- What is an IFFE?
	- Immediately Invoked Function Expression

**Example of IFFE**
```js
(function x(y) {
    console.log("hi", y);
})("Sanket");
```

**Scope of Function Expression.js**
```js
const f = function fun() {
    console.log("How much fun????");
}

console.log(f()); // will give undefined.
// fun();
```
- function `fun`  is available only via `f`

**Function Expressions are of two types**
- named function expression
- anonymous function expression

**Use Case of Named Function Expression**

*usecaseofnamedfunctionexpression.js*
```js
function fun(fn) {
    console.log("Welcome to fun");
    fn();
}

fun(function () {
    console.log("Wow so much fun");
    console.trace();
});


// for recursive cases named function expression are also helpful
```

-  readability of code increases with named function expression.
- importance of writing good function names.
- for recursive cases named function expression are also helpful
- `console. trace()`
	- anonymous function are hard to debug since the name of the function is not there in the logs.
	- We should use named function expression instead of anonymous function expression.
- arguments.callee, this recursion related regarding in anonymous functions - Read in mozilla docs.
---
**Use Case of IFFE**
- In order to avoid the naming collusions we can use `IFFEs`

```js
function x() {
    console.log("Wow");
}
// 
(function x(y) {
    console.log("hi", y);
})("Sanket");
//
x();

function f() {
    return 1;

}

function g() {
    return 2;
}

var i = 10;

// if(i%2 == 0) {
//     var res = f();
// } else {
//     var res = g();
// }

var res = (function evaluate(i) {
    if(i%2 == 0) return f();
    else return g();
})(i);
console.log(res);
```

---

**Tips**

- **Don't worry about the framework, worry about the Language**
- **Master Binary Search, Graphs and Dynamic programming.**

---

## Extra Resources
- [How JavaScript Blocks work 🧱 - DEV Community 👩‍💻👨‍💻](https://dev.to/js_bits_bill/how-javascript-blocks-work-js-bits-aha)
- [Temporal Dead Zone (TDZ) and Hoisting in JavaScript – Explained with Examples](https://www.freecodecamp.org/news/javascript-temporal-dead-zone-and-hoisting-explained/)
- [Difference between \`let\` and \`var\` in JavaScript | Sentry](https://sentry.io/answers/difference-between-let-and-var-in-javascript/)
- [ecmascript 6 - What is the use case for var in ES6? - Stack Overflow](https://stackoverflow.com/questions/31836796/what-is-the-use-case-for-var-in-es6)
- [3 reasons to use 'var' in JavaScript - DEV Community 👩‍💻👨‍💻](https://dev.to/paritho/3-reasons-to-use-var-in-javascript-1hoe)
- [Site Unreachable](https://www.freecodecamp.org/news/understanding-let-const-and-var-keywords/)
- [Different Ways to Declare Functions - Beginner JavaScript - Wes Bos](https://wesbos.com/javascript/02-functions/different-ways-to-declare-functions)
