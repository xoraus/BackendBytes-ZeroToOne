## Introduction to Promises

- 💡 What is Promise?  
	- Promise object is a placeholder for certain period of time until we receive value from asynchronous operation.
	- A container for a future value.
	- **A Promise is an object representing the eventual completion or failure of an asynchronous operation.**

Imagine that you’re a top singer, and fans ask day and night for your upcoming song.

To get some relief, you promise to send it to them when it’s published. You give your fans a list. They can fill in their email addresses, so that when the song becomes available, all subscribed parties instantly receive it. And even if something goes very wrong, say, a fire in the studio, so that you can’t publish the song, they will still be notified.

Everyone is happy: you, because the people don’t crowd you anymore, and fans, because they won’t miss the song.

> **_Benefits of Promises_**

1.  Improves Code Readability
2.  Better handling of asynchronous operations
3.  Better flow of control definition in asynchronous logic
4.  Better Error Handling

- Promises increases the readability of code.
- Promises helps us in solving the problem of **Inversion of Control**.
- In JS, Promises are special type of objects that get returned immediately when we call them.
- Promises acts as placeholder for the data as we hope to get back sometime in future
- `x = fetch("http://www.xyz.com")`
	- assume fetch is written using promise then, it will immediately return a promise object as a placeholder.
- In these promise objects we can attach the functionality we want to execute once the future task is done.
- once the future task is done, promises will automatically executes attached functionality.
- Promise
	- maybe we fulfill the promise 
	- maybe we don't fulfill the promise.
- How we can create a promise?
	- creation of a promise object is sync in nature.
- How can we consume a promise?
-  A Promise is an object that is used as a placeholder for the eventual results of a deferred (and possibly asynchronous) computation.
- Any Promise object is in one of three mutually exclusive states: fulfilled, rejected, and pending.
	- pending → when we create a new promise object this is the default state. It represents work in progress
	- fulfilled → if the operation is completed successfully.
	- rejected → if operation was not successful.
- ```new Promise(f)```

```js
let promise = new Promise(function(resolve, reject) {
  // inside the function we can write our time consuming task.
});
```

- `resolve` and `reject` are normal functions.
- Whenever in the implementation of executor callback you call the resolve function, the promise goes to a fullfilled state.
- If you call reject func, it goes to a rejected state and if you don't call anything, promise remains in pending state.
- In pending state the `value` prop of either (resolve or reject)remains undefined.

```js
function createPromiseWithTimeout() {
  return new Promise(function executor(resolve, reject) {
    setTimeout(function() {
      let num = getRandomInt(10);
      if (num % 2 == 0) {
        // if the random number is even we fulfill
        resolve(num);
      } else {
        // if the random number is odd we reject
        reject(num);
      }
    }, 10000);
  });
}

let y = createPromiseWithTimeout();
console.log(y);
```

The code defines a function called `createPromiseWithTimeout` that returns a Promise object that resolves or rejects after a specified timeout.

The Promise object is created using the Promise constructor, which takes an executor function as its argument. The executor function is passed two callback functions, `resolve` and `reject`, which are used to resolve or reject the Promise.

The executor function starts a timer using the `setTimeout` function, which waits for 10 seconds (specified as the second argument to `setTimeout`) before executing its callback function. The callback function generates a random integer between 0 and 9 using the `getRandomInt` function. If the integer is even, the Promise is fulfilled with the `resolve` function and the random even integer as the argument. If the integer is odd, the Promise is rejected with the `reject` function and the random odd integer as the argument.

After the Promise is created, the code assigns the result of calling the `createPromiseWithTimeout` function to the variable `y`, and logs the Promise object to the console.

Note that since the Promise returned by `createPromiseWithTimeout` is asynchronous, the value of `y` logged to the console will be a Promise object, not the resolved value of the Promise.
