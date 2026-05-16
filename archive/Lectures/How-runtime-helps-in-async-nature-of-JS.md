
![js-runtime](https://github.com/xoraus/Backend-Specialization/blob/main/attachments/js.png)

- JavaScript is sync in nature.
- JavaScript is single threaded.

```js
console.log("Hi we are starting");

for(let i = 0; i < 10000000000; i++) {
    // some task
}

console.log("Done");
```

- What is the meaning of Asynchronous programming?
	- Asynchronous programming is a technique that enables your program to start a potentially long-running task and still be able to be responsive to other events while that task runs, rather than having to wait until that task has finished. Once that task has finished, your program is presented with the result.
- [**Synchronous**](https://stackoverflow.com/questions/10570246/what-is-non-blocking-or-asynchronous-i-o-in-node-js) (or sync) execution usually refers to code executing in sequence. In sync programming, the program is executed line by line, one line at a time. Each time a function is called, the program execution waits until that function returns before continuing to the next line of code.
- **Asynchronous** (or async) execution refers to execution that doesn’t run in the sequence it appears in the code. In async programming the program doesn’t wait for the task to complete and can move on to the next task.

```js
console.log("hi");
setTimeout(function () { console.log("time done"); });
console.log("by");

// hi
// by
// time done
```

 - JS is not that powerful, as people claim it to be.
	 - A runtime is required to run JS in browser.
 - JS + Runtime = Hulk of Programming World.
 - JS in sync in nature for the piece of code which is native to JS.
 - Runtime provides a lot of functionalities to JS.
 - What is Runtime?
	 - A runtime is the environment in which a programming language executes. The runtime system facilitates storing functions, variables, and managing memory by using data structures such as queues, heaps and stacks (more on this later).
	 - The JavaScript runtime environment provides access to built-in libraries and objects that are available to a program so that it can interact with the outside world and make the code work.
 - What is a Call Stack?
	 - When you write a program, you may compose it of multiple functions. The call stack keeps track all function calls during throughout the life time of the program and execute them in the reverse order that they are called.
	- Hence why crashing a program with never ending recursive function calls is said to be a stack/buffer overflow. The stack had so many function calls that it ran out of memory space.
- What are Threads?
	- In an OS you can run a program which can be comprised of processes. A process can then be comprised of multiple threads. A thread is the smallest unit of computation that can be individually scheduled.
- What is Multithreading?
	- Computers with multiple cores can process multiple threads at the same time. Some programming languages support multithreading by allowing your program to spawn child threads to perform a task then return the result to the parent. Such runtime would provide multiple call stacks. Each call stack is delegated to a thread.
- What makes JavaScript's runtime so special?
	- By design the JavaScript interpreter is single-threaded, this is a good thing because it makes it easier to implement browsers for all kinds of devices, consoles, watches fridges etc.
	- But then you may wonder how do web apps work if the interpreter can only do one thing at a time? Well even though JavaScript is single threaded, it executes tasks in a concurrent fashion.
	- Simply put, concurrency is breaking up tasks and switching between so quickly that they all appear to progress at the same time. Contrast this with parallelism which is performing tasks simultaneously.
- In the context of a browser this is comprised of the following elements:
	- The JavaScript engine (which in turn is made up of the heap and the call stack)
	- Web APIs
	- The callback queue
	- The event loop
- The purpose of the JavaScript engine is to translate source code that developers write into machine code that allows a computer to perform specific tasks.
- Let's focus on the V8 JavaScript engine.
	- **The heap**
		- The heap, also called the ‘memory heap’, is a section of unstructured memory that is used for the allocation of objects and variables.
	- **The call stack**
		- The call stack is a data structure that keeps track of where we are in the program and runs in a last-in, first-out way. Each entry in the stack is called a stack frame. This means that the frame at the top of the stack is the one the engine is focused on, and it will not move on to the next function unless the function above it has been removed from the stack.
		- As the JS engine steps into a function, it is pushed onto the stack. When a function returns a value or gets sent to the Web APIs, it is popped off the stack. If a function doesn’t explicitly return a value then the engine will return undefined and also pop the function off the stack. This is what is meant by the term “JavaScript runs synchronously”; it is single-threaded, so can only do one thing at a time.
	- **Web APIs**
		- The Web APIs are not a part of the JavaScript engine, but they are part of the runtime environment provided by the browser. There are a large number of APIs available in modern browsers that allow us to a wide variety of things. 
	- **The callback queue**
		- The callback queue stores the callback functions sent from the Web APIs in the order in which they were added. This queue is a data structure that runs first in, first out. The queue uses the array push method to add a new callback function to the end of the queue and the array shift method to remove the first item in the queue.
		- Callback functions will sit in the queue until the call stack is empty, they are then moved into the stack by the event loop.
	- **The event loop**
		- The job of the event loop is to constantly monitor the state of the call stack and the callback queue. If the stack is empty it will grab a callback from the callback queue and put it onto the call stack, scheduling it for execution.
		-  This is why JavaScript often gets described as being able to run asynchronously, even though it is a single-threaded language. JavaScript can only execute one function at a time, so this means it is synchronous, but as we can push callbacks from the Web APIs to the callback queue and in turn, the event loop can constantly add those callback to the call stack, we think of JavaScript as being able to run asynchronously.

```js
function timeConsumingByLoop() {
    console.log("loop starts");
    for(let i = 0; i < 1000000000; i++) {
        // some task
    }
    console.log("loop ends");
} 
function timeConsumingByRuntimeFeature0() {
    console.log("Starting timer");
    setTimeout(function exec() {
        console.log("Completed the timer0");
        for(let i = 0; i < 1000000000; i++) {
            // some task
        }
    }, 5000); // 5 sec timer
}
function timeConsumingByRuntimeFeature1() {
    console.log("Starting timer");
    setTimeout(function exec() {
        console.log("Completed the timer1");
        // while(true) {}
    }, 200); // 0 s timer
}
function timeConsumingByRuntimeFeature2() {
    console.log("Starting timer");
    setTimeout(function exec() {
        console.log("Completed the timer2");
    }, 200); // 200 ms timer
}
console.log("Hi");
timeConsumingByLoop();
timeConsumingByRuntimeFeature0();
timeConsumingByRuntimeFeature1();
timeConsumingByRuntimeFeature2();
timeConsumingByLoop();
console.log("By");
```

- The code demonstrates the asynchronous nature of the JavaScript runtime. In JavaScript, the `setTimeout` function is an example of an asynchronous function. This means that the function returns immediately and does not wait for the specified time to pass before executing the callback function. In this code, the `timeConsumingByRuntimeFeature0`, `timeConsumingByRuntimeFeature1`, and `timeConsumingByRuntimeFeature2` functions set timers using the `setTimeout` function, each with a different waiting time. The function returns immediately, allowing the code to continue executing the `timeConsumingByLoop` function and the next `setTimeout` function without waiting for the timer to finish. Meanwhile, the JavaScript runtime is executing the timers in the background and executing the corresponding callback functions when the timers are finished. In contrast, the `timeConsumingByLoop` function runs synchronously, blocking the rest of the code until it finishes.
- we never pause a sync piece of code.
- The moment runtime's done with the task it gets added to the event queue.
- Event Loop
	- Infinite Loop
	- It keeps on checking whether the call stack is empty or not & no global code is left.
	- Ready to be executed callback functions (coming from the events)
	- The job of event loop is to continuously check if an event occurred, like mouse click or keyboard stroke so that it can send that to call stack. Of course, your mouse click will be given higher priority for execution than an image load.
- In a nutshell, the asynchronous implementation in Javascript is done through a call stack, call back queue and Web API and event loop.

**Summary**

-   The runtime environment is what makes JavaScript code work, and in a browser in consists of the JS engine, a lot of Web APIs, a callback queue and the event loop
-   The JS engine translates source code into machine code that allows a computer to perform specific tasks at the hardware level
-   Web APIs extend the JS language and push callback functions to the callback queue once actions are complete and data has been received
-   The callback queue stores callback functions in order, ready to be executed
-   The event loop is constantly monitoring the call stack and the callback queue; if the call stack is empty it will move the callback function at the front of the queue to the call stack, scheduling it for execution

**References**
- [What is the JavaScript runtime? - DEV Community 👩‍💻👨‍💻](https://dev.to/snickdx/what-is-the-javascript-runtime-4n09)
- [Understanding the JavaScript runtime environment | by Gemma Croad | Medium](https://medium.com/@gemma.croad/understanding-the-javascript-runtime-environment-4dd8f52f6fca)
- [What does it mean by Javascript is single threaded language | by Sharjeel Siddique | The Startup | Medium](https://medium.com/swlh/what-does-it-mean-by-javascript-is-single-threaded-language-f4130645d8a9)
