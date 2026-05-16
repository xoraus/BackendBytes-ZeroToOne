## Callbacks | Inversion of control

### Higher Order Functions

A Higher-Order Function is a regular function that takes one or more functions as arguments and/or returns a function as a value from it.

- higher order function - there are functions which take another function as arguments
- these are called higher order functions

```js
function f(x, fn) {
    /**
     * x -> number
     * fn -> function
     */
    console.log(x);
    console.log(fn);
    fn();
}


f(10, function exec() {
    console.log("I am an expression passed to a HOF");
});

let arr = [1,10,9,100,1000,11,12,13,14,2,3]; // unsorted array

arr.sort(); 
```

- `arr.sort();`  It sorts the given array // [expectation] -> this might arrange elements in inc order
-  default implementation of arr.sort() is going to sort my array in lexicographical order

```js
/**
 * 0 -> A
 * 1 -> B
 * 2 -> C
 * 3 -> D
 * 4 -> E
 * 5 -> F
 * 6 -> G
 * 7 -> H
 * 8 -> I
 * 9 -> J
 * .... 
 * [B, BA, J, BAA, BAAA, BB, BC, BD, BE, C, D] // if we arrange it according to dictionary
 * [B, BA, BAA, BAAA, BB, BC, BD .....]
 */
 console.log(arr);
```

- JS sort function, is a higher order function. The sort function takes a comparator function as argument

```js
let b = [1,10,9,100,1000,11,12,13,14,2,3];

// sort b in increasing order

b.sort(function cmp(a, b) {
    // if a < b -> a - b will be negative -> if cmp function gives negative then a is placed before b (a<b)
    // if a > b -> a - b will be positive -> if cmp function gives positive then b is placed before a (a>b)
    return a < b; 
}); // sort is a HOF ,, the sort function takes a comparator function as argument


console.log(b);
```

- What is Lexicographical order?
	- lexicographical order is alphabetical order. The other type is numerical ordering. Consider the following values, `1, 10, 2`
	- Those values are in lexicographical order. 10 comes after 2 in numerical order, but 10 comes before 2 in "alphabetical" order.
- What are comparator functions?
	- A comparator is **a function that takes two arguments x and y and returns a value indicating the relative order in which x and y should be sorted**.
- What is the worst sorting algorithm?
	- The universally-acclaimed worst sorting algorithm is [Bogosort](https://en.wikipedia.org/wiki/Bogosort), sometimes called Monkey Sort or Random Sort. Bogosort develops from the idea that, in probability theory, if a certain phenomenon is possible, then [it will eventually happen](https://www.baeldung.com/cs/randomness#the-theoretical-bases-of-randomness).

### Callbacks

```js
/**
 * fun -> HOF ? -> it takes fn (which is a function) as argument
 * 
 * x -> number
 * fn -> function
 */

function fun(x, fn) {
    for(let i = 0; i < x; i++) {
        console.log(i);
    }

    fn();
}

fun(10, function exec() { // callback
    console.log("I am executed also");
});
```

-  Higher order functions consume functions as argument and the function that you pass as argument is called as **callback function**.
- What is the biggest problem with the callbacks?
	- Generally you will get the answer as "Callback hell" but
		- "You can improve it with promises" → Bu then there's promise Hell
		- You can improve it with async await" → But there's async await Hell
	- 'callback hell is not the biggest problem'. It is more or less the readability problem.
- There's bigger problem with callbacks, is  **Inversion Control**.
- Refer → [callbackhell.com](callbackhell.com) 

### What is Inversion of Control? in Simple Words.

Read this for amazing answers. → [ What is Inversion of Control? - Stack Overflow](https://stackoverflow.com/questions/3058/what-is-inversion-of-control)

What is Inversion of Control? f

If you follow these simple two steps, you have done inversion of control:

1.  Separate **what**-to-do part from **when**-to-do part.
2.  Ensure that **when** part knows as _little_ as possible about **what** part; and vice versa.

There are several techniques possible for each of these steps based on the technology/language you are using for your implementation.

---

The _inversion_ part of the Inversion of Control (IoC) is the confusing thing; because _inversion_ is the relative term. The best way to understand IoC is to forget about that word!

Inversion of Control is what you get when your program callbacks, e.g. like a gui program.

For example, in an old school menu, you might have:

```
print "enter your name"
read name
print "enter your address"
read address
etc...
store in database
```

thereby controlling the flow of user interaction.

In a GUI program or somesuch, instead we say:

```
when the user types in field a, store it in NAME
when the user types in field b, store it in ADDRESS
when the user clicks the save button, call StoreInDatabase
```

So now control is inverted... instead of the computer accepting user input in a fixed order, the user controls the order in which the data is entered, and when the data is saved in the database.

Basically, **anything** with an event loop, callbacks, or execute triggers falls into this category.

- Benefits of IoC
	- Reduces amount of application code
	- Decreases coupling between classes
	- Makes the application easier to test and maintain

*callbackdemo.js*
```js
/**
 * 1. Inversion of control (Promises can resolve this issue, will discuss it later)
 * 2. Callback hell -> readability problem 
 */


// let arr = [1,10,1000,9,2,3,11];

// arr.sort(function cmp(a, b) {
//     return a - b;
// })

// console.log(arr);


function doTask(fn, x){
    // whole implementation is done by team A

    // fn(x*x); // calling my callback with square of x
    // fn(x*x);
} // team A

// here team b tries to use it
doTask(function exec(num) { // due to callbacks, I am passing control of how exec should be called to doTask
    // this is inversion of control
    console.log("Woah num is", num);
}, 9);
```


