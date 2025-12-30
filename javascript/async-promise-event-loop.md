# Async JavaScript, Promise & Event Loop

> This document explains **how JavaScript handles asynchronous code**.
> The goal is to understand **what really happens behind the scenes**, not just how to use async syntax.

---

## 1. Synchronous vs Asynchronous Code

### What I Thought Initially
- JavaScript executes code line by line.
- `setTimeout` waits and then continues.

### What I Learned
- JavaScript is **single-threaded**.
- Async code does **not block** the main thread.
- Asynchronous behavior is achieved using the **event loop**.

- JS executes everything in a synchronous manner, **line by line**.
- Async comes into play later (event loop).
---

## 2. Call Stack (Execution Context)

### Key Idea
- JavaScript uses a **call stack** to execute functions.
- Only **one function** can run at a time.
- Every function in JS is added to the **Call Stack** when called.
- Nested functions are executed one after the other, **LIFO**(Last In, First Out).
- Removed from the stack after the function **finishes**.

### Example
````js
function f1() {
  console.log('f1')
}
function f2() {
  f1();
  console.log('f2')
}
function f3() {
  f2();
  console.log('f3')
}
f3();
````
---

## 3. Web APIs (Browser Environment)
### What I Learned

- setTimeout, fetch, and DOM events are not part of JavaScript itself.
- They are provided by the **browser (Web APIs)**.

### Example
````js
setTimeout(() => {
  console.log("Async");
}, 1000);
````

## 4. Callback Queue (Task Queue)
### Key Idea

- When an async task **finishes**, its callback is pushed into a queue.
- It does not execute immediately.
- JS maintains a **queue** for calling functions → FIFO(First In, First Out).
- First function in, first function executed.
- Event loop: checks if the stack is **empty**.
- If it is empty, the function is moved from the queue to the stack and executed.

## 5. Event Loop (The Heart of Async JS)
### Core Rule

#### The event loop checks:
- Is the call stack empty?
- If yes → push the first task from the queue into the stack.

#### Note : 
1. First all **microtasks** are executed from the **Job Queue**
2. Then one of the **macrotasks** is executed from the **Callback Queue**

### Mental Model

**Stack** = execution

**Queue** = waiting room

**Event Loop** = traffic controller

## 6. setTimeout Is NOT a Timer Guarantee
### Common Misconception

***setTimeout(fn, 0) does not mean immediate execution.***

### Example
````js
function f1() { console.log('f1'); }
function f2() { console.log('f2'); }
function main() {
  console.log('main');
  setTimeout(f1, 0);
  f2();
}
main();

````
#### Note: Even with setTimeout(..., 0), f1 will be executed after f2
1. Callback is queued, stack is emptied first then executed.
2. Event Loop always empty the stack and push the callback onto the stack after.

##  7. Promises (Microtask Queue)
### What I Thought Initially

> Promises are just cleaner callbacks.

### What I Learned

- Promises use the **Job Queue (microtask queue)**.
- Microtasks have **higher priority** than callbacks.
- Event Loop When Stack is Empty :
1. **First** executes all Job Queue (microtasks)
2. **Then** goes to Callback Queue (macrotasks)

### Example
````js
function f1() {
 console.log('f1');
}

function f2() { 
    console.log('f2');
}

function f3() { 
    console.log('f3');
}

function main() {
  console.log('main');

  setTimeout(f1, 50);
  setTimeout(f3, 30);

  new Promise((resolve, reject) =>
    resolve('I am a Promise, right after f1 and f3! Really?')
  ).then(resolve => console.log(resolve));

  new Promise((resolve, reject) =>
    resolve('I am a Promise after Promise!')
  ).then(resolve => console.log(resolve));

  f2();
}

main();
````

## 8. async / await (Syntactic Sugar)
### Key Idea

- async / await is built on top of **Promises**.
- It does not make code synchronous.

### Example
````js
async function fetchData() {
  const result = await fetch("/api");
  console.log(result);
}
````

## 9. Common Interview Traps
### Mistakes

- Thinking JavaScript is multi-threaded
- Thinking await blocks the call stack
- Not understanding the difference between microtasks and macrotasks

## 10. Mental Model Summary

### 1.  Call Stack (Function Execution Stack)

- JavaScript uses a **stack** to execute functions
- Functions are executed one by one, **line by line**
- When the function completes, it is removed from the Stack

### 2. Browser/Web APIs and Callback Queue

- When an Async operation such as setTimeout or event is executed, its callback goes to the Callback Queue
- **Event Loop** later pulls it from the queue and executes it

### 3. Promises and Job Queue (Microtask Queue)

- The executor of a Promise function goes to the Job Queue

- Microtasks have **higher priority** than macrotasks (callback queue)

### 4. Event Loop

- Continuously checks the Stack

- If the Stack is empty:

1. **First** all microtasks from the Job Queue are executed

2. **Then** one of the macrotasks from the Callback Queue is executed

### Note: Always respect the order So that nothing is missed

### Key point:

- **Synchronous = line by line in Stack**

- **Asynchronous = Queue + Event Loop**

- **Microtasks always run before macrotasks, even with zero latency**