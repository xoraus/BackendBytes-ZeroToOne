## Is Javascript Compiled? I Lexical Scoping
- What is a Scope?
	- represents visibility → of variables and functions
	- scopes tell you where a thing is visible
- Everything (variables & functions) inside your code will used in one of the following two way
	- Either it will getting some value assigned to it
	- Or some value will be retrieved from it.
- Do you think JavaScript is complied or interpreted?
- Every javascript code is executed in two phases
	- Parsing Phase
		- Scope resolution
	- Execution Phase
- In Javascript, there are three types of scopes.
	- Global 
	- function
	- block
- Scope - Refer MDN
- var
	- The `var` statement declares a function-scoped or globally-scoped variable, optionally initializing it to a value.
	- var declarations, wherever they occur, are processed before any Code is executed. This is called hoisting and is discussed further below.
- let
	- The let declaration declares block-scoped local variable, optionally initializing it to a value.
- Parsing
	- every time we see formal declaration we think about the scope in parsing phase.


-   **Lexical Scoping I Auto Global | Function Expressions**
    
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
