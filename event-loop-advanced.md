# JavaScript Runtime, Event Loop, and Execution Order

A JavaScript runtime environment is a platform that provides all the necessary components for executing JavaScript code. It acts as an ecosystem where JavaScript code can be interpreted and run. The Key components of Javascript Runtime Environment are:

- Javascript Engine
- Web APIs
- Event Loop
- Call stack
- Call back Queue
- Microtask Queue
- Memory heap

## What is the JavaScript call stack and how it processes synchronous code

The call stack is a stack data structure used by the JavaScript engine to keep track of function calls (especially the synchronous ones) — what’s currently running and what to return to.

### How the Call Stack Works:

- JavaScript is single-threaded, so it can do one thing at a time.
- When a function is called, it’s pushed onto the call stack.
- When the function finishes, it’s popped off.
- This continues in Last-In-First-Out (LIFO) order.

## What are microtasks and macrotasks, and how they are scheduled

**Microtasks** - Microtasks are small, high-priority asynchronous operations that are scheduled to run immediately after the current JavaScript execution finishes, but before any macrotasks (like setTimeout).

**Macrotasks** - Macrotasks are asynchronous tasks scheduled to run after the current call stack and all microtasks have completed. They are placed in the callback queue and managed by the event loop. Eg.: setTimeOut

### How They're Scheduled (Event Loop Order):

- Execute current call stack (synchronous code).
- Process all microtasks from the microtask queue.
- Then, execute one macrotask from the callback queue.
- Repeat the cycle.

## Why Promises, queueMicrotask(), and setTimeout() behave differently in execution order

Promises, queueMicrotask() -> Microtasks (High Priority, that is runs after synchronous code)

setTimeout() -> Macrotask (Low Priority)

Based on the execution order of event loop in javascript, it behaves differently in execution order.

## How the event loop decides what runs when

- JS runs all synchronous code first (Call Stack - Executes synchronous code line-by-line).
- After the stack is empty, the event loop:
  - Runs all microtasks (in order). Microtask Queue – For tasks like: Promise.then(), Promise.catch(), queueMicrotask()
  - Then runs one macrotask from the queue. Macrotask (Callback) Queue – For tasks like: setTimeout(), setInterval(), setImmediate()
- Repeat from Step 2.

---

# Exercise 1 – Microtasks vs Macrotasks

```js
console.log("1");

setTimeout(() => {
  console.log("2");
}, 0);

Promise.resolve().then(() => {
  console.log("3");
});

queueMicrotask(() => {
  console.log("4");
});

console.log("5");
```

### Predict the output order

1
5
3
4
2

### Explain why each line executes when it does

- console.log('1'); It’s synchronous, so it goes directly to the call stack and Runs immediately

- setTimeout(() => {console.log('2');}, 0); It's a macrotask (placed in the callback queue). It waits until the call stack is empty, and all microtasks are done.

- Promise.resolve().then(() => {console.log('3');}); A microtask. It's scheduled to run after the synchronous code, before any macrotask.

- queueMicrotask(() => {console.log('4');}); Also a microtask. Queued after the Promise .then()

- console.log('5'); Runs immediately (synchronous). Goes into the call stack.

### Identify which parts are:

- Synchronous

  - console.log('1');
  - console.log('5');

- Microtasks

  - Promise.resolve().then(() => {
    console.log('3');
    });
  - queueMicrotask(() => {
    console.log('4');
    });

- Macrotasks

  - setTimeout(() => {
    console.log('2');
    }, 0);

### Describe how the event loop handles each of them

- Runs all synchronous code first. _(1 and 5)_
- Then empties the microtask queue (in FIFO order). _(3 and 4)_
- Then runs the first macrotask from the macrotask queue. _(2)_
- Repeats this cycle.

---

# Exercise 2 – Nested and Sequential Tasks

```js
console.log("start");

setTimeout(() => {
  console.log("setTimeout 1");
  Promise.resolve().then(() => console.log("Promise 1"));
}, 0);

setTimeout(() => {
  console.log("setTimeout 2");
}, 0);

Promise.resolve().then(() => console.log("Promise 2"));

console.log("end");
```

### Predict the output order

start

end

Promise 2

setTimeout 1

Promise 1

setTimeout 2

### Explain your reasoning step by step

- console.log('start'); It’s synchronous, so it goes directly to the call stack and Runs immediately

- setTimeout(() => {
  console.log('setTimeout 1');
  Promise.resolve().then(() => console.log('Promise 1'));
  }, 0); It's a macrotask in which it contains a microtask. So while executing the macrotask, the microtask will also be executed.

- setTimeout(() => {
  console.log('setTimeout 2');
  }, 0); It's a macrotask (placed in the callback queue). It waits until the call stack is empty, and all microtasks are done.

- Promise.resolve().then(() => console.log('Promise 2')); A microtask. It's scheduled to run after the synchronous code, before any macrotask.

- console.log('end'); It’s synchronous, so it goes directly to the call stack and Runs immediately

### Explain what happens during each event loop tick

**Tick 1**

- console.log('start'); -> Synchronous code runs in the call stack.
- setTimeout(() => { ... }, 0); -> Registered in the callback queue.
- setTimeout(() => { ... }, 0); -> Registered in the callback queue.
- Promise.resolve().then(() => console.log('Promise 2')); -> Pushed to microtask queue.
- console.log('end'); -> Synchronous code so runs immediately.
- Runs the code in the microtask queue.

**Tick 2**

- First macrotask

  Executes "setTimeout 1"

  Then the microtask (promise) inside that.

**Tick 3**

- Second macrotask

  Executes "setTimeout 2"

### Identify which parts are:

- Synchronous

  - console.log('start');
  - console.log('end');

- Microtasks

  - Promise.resolve().then(() => console.log('Promise 2'));

- Macrotasks
  - setTimeout(() => {
    console.log('setTimeout 1');
    Promise.resolve().then(() => console.log('Promise 1'));
    }, 0);
  - setTimeout(() => {
    console.log('setTimeout 2');
    }, 0);

# Key Takeaways

## Execution order

Synchronous Code -> Microtasks from Microtask queue (In FIFO order) -> Macrotasks from Task queue (In FIFO order)

## Async behavior

Asynchronous behavior in JavaScript refers to the execution of code in a non-blocking manner, allowing certain operations to be initiated and completed independently without halting the execution of other code. Simply Async means "Don't wait here, Do other work".

## How it affects real projects

- It prevents the freezing of UI and loads it smoothly.
- Instead of blocking users while uploading files, it allows to upload in the background.

Async behavior makes the app faster and smoother by NOT waiting for slow things like servers or files.
