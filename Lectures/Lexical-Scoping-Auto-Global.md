## Lexical Scoping I Auto Global |

```javascript
"use strict";
var teacher = "Sanket";
function fun() {
    var teacher = "Pulkit";
    // content = "JS";
    console.log(teacher);
    // console.log(content);
}
function gun() {
    var student = "Sarthak";
    console.log(student);
}
fun();
gun();
console.log(teacher);
// console.log(content);

const o = { p: 1, p: 2 }; 
```
-   auto globals
-   You can execute the code in two modes
	-   strict mode
	-   sloppy mode
-   What is strict mode?
	-   JavaScript's strict mode a is a way to opt in to a restricted variant of JavaScript, thereby implicitly opting-out of "sloppy mode".
	-   Strict mode makes several changes to normal JavaScript semantics:
		-   1. Eliminates some JavaScript silent errors by changing them to throw errors.
		-   2. Fixes mistakes that make it difficult for JavaScript engines to perform optimizations: strict mode code can sometimes be made to run faster than identical code that's not strict mode.
		-   3. Prohibits some syntax likely to be defined in future versions of ECMAScript.
-   to invoke strict mode ⇒ use strict for a whole file or a function
-   Assigning to undeclared variables
    -   Strict mode makes it impossible to accidentally create global variables. In sloppy mode, mistyping a variable in an assignment creates a new property on the global object and continues to "work". Assignments which would accidentally create global variables throw an error in strict mode:
-   strict mode stops the creation of auto globals
-   auto global - can be a huge problem for production level codebases.
-   strict mode can be useful for some performance. - Design Decision
-   _> Refer - strict mode documentation page_
-   Interview
    -   asked language specific things
- There is no syntactical error
-   there is no lo function here.
-   This fails in execution
	- console.log("hi");
	- console..log("hello");
	- console.log("bye");
    -   There is syntactical error in the above code.
    -   dot dot doesn't exist error
    -   This fails in execution
-   Parse Tree
- console.log(10.. toString()); works fine
    - console.log(5..toString(2));
    -   from number to string
    -   does the boxing here (primitive to non primitive)
-   Why does 10..toString() work, but 10.toString() does not? [stackoverflow]
-   Boxing
-   The lexer (aka "tokenizer") when reading a new token, and upon first finding digit, will keep consuming characters (i.e. digits or one dot) until it sees a character that is not part of a legal number.
-   Parse tree - compiler design
-   Nested Scope
	-   when you're calling the variable inside a function it will try to look outside.
-   mazurov.github.io/eslevels-demo/ to visualize scopes
-   Lexical vs Dynamic Scoping
-   Javascript uses lexical scoping