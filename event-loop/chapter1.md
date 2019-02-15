# Introduction

## Javascript under the hood

JavaScript unlike other programming languages, it’s **single threaded**. That means, code execution will be done one at a time. so there is just one thing happening at a time. Since code execution is done sequentially, any code that takes longer time to execute, will block anything that needs to be executed. Hence sometimes you see below screen while using Google Chrome. Same on Node.js as JavaScript code runs on a single thread too.

![](https://cdn-images-1.medium.com/max/1000/0*w2rEwv9mE9xVPhRy.png)

You just need to pay attention to how you write your code and avoid anything that could block the thread, like synchronous network calls or infinite loops.
E.g. 
```javascript
while(true){}
```

Any code after above statement won’t be executed as while loop will loop infinitely until system is out of resources. This can also happen in infinitely recursive function call.

## Basic Architecture

![Overview of major components in a browser](https://cdn-images-1.medium.com/max/1200/1*7GXoHZiIUhlKuKGT22gHmA.png)

![Overview of major components in a nodeJS](https://i.stack.imgur.com/QRePV.jpg)

![NodeJS architecture](https://dotnettricksweb.blob.core.windows.net/img/nodejs/node.js-architecture.png)

Like any other programming language, JavaScript runtime has one stack and one heap storage.

- **Heap** - Objects are allocated in a heap which is just a name to denote a large mostly unstructured region of memory

- **Stack** - This represents the single thread provided for JavaScript code execution. Function calls form a stack of frames. Stack is LIFO (last in, first out) data storage which store current function execution context of a program.

- **Browser or Web APIs** are built into your web browser, and are able to expose data from the browser and surrounding computer environment and do useful complex things with it. They are not part of the JavaScript language itself, rather they are built on top of the core JavaScript language, providing you with extra superpowers to use in your JavaScript code. If you’re a Node.js developer, these are the C++ APIs.

In general, in most browsers there is an event loop for every browser tab, to make every process isolated and avoid a web page with infinite loops or heavy processing to block your entire browser.

The environment manages multiple concurrent event loops, to handle API calls for example. Web Workers run in their own event loop as well.

You mainly need to be concerned that your code will run on a single event loop, and write code with this thing in mind to avoid blocking it.


## Call stack - Event Loop

### Digging in the Call Stack

The **call stack** is a LIFO queue (Last In, First Out). The **event loop** continuously checks the call stack to see if there’s any function that needs to run. To visualize, how JavaScript executes a program, we need to understand JavaScript runtime.

![Javascript Runtime Enviroment](https://cdn-images-1.medium.com/max/1000/1*ocCc8yEvUEeOtGU2LNQnpA.png)

Hence, first stack entry is foo(). Since foo function calls bar function, second stack entry is bar(). Since bar function calls baz function, third stack entry is baz(). And finally, baz function calls console.log, fourth stack entry is console.log('Hello from baz'). **Until a function returns something (while function is executing), it won’t be popped out from the stack.** Stack will pop entries one by one as soon as that entry (function) returns some value, and it will continue pending function executions.

![Stack frames](https://cdn-images-1.medium.com/max/1000/1*rRoLpv-Zrmpa-srNhwlbvA.gif)

Let’s pick another one example:

```js
const bar = () => console.log('bar')

const baz = () => console.log('baz')

const foo = () => {
  console.log('foo')
  bar()
  baz()
}

foo()
```

This code prints:

```js
foo
bar
baz
```

At this point the call stack looks like this:

![Call stack frames](https://flaviocopes.com/node-event-loop/call-stack-first-example.png)

The event loop on every iteration looks if there’s something in the call stack, and executes it **until the call stack is empty**:

![](https://flaviocopes.com/node-event-loop/execution-order-first-example.png)

### Digging in the Event Loop

*So what is the event loop after all?*

Browser comes with a JavaScript engine which provides JavaScript runtime environment. For example, Google chrome uses V8 JavaScript engine, developed by them. But guess what, browser uses more than just a JavaScript engine. This is what browser under the hood looks like.

![Browser under the hood](https://cdn-images-1.medium.com/max/1000/1*lZ-KXoVNUSOwaq7q8zUBDg.png)

JavaScript runtime actually consist of 2 more components viz. **event loop** and **callback queue**. Callback queue is also called as message queue or task queue.

![Event Loop](https://cdn-images-1.medium.com/max/1000/1*KGBiAxjeD9JT2j6KDo0zUg.png)

The **Event Loop** continuously checks the call stack to see if there’s any function that needs to run. So it has one simple job — *to monitor the Call Stack and the Callback Queue.* If the Call Stack is empty, it will take the first event from the queue and will push it to the Call Stack, which effectively runs it.
Such an iteration is called a tick in the Event Loop.

### Queuing Function Execution

The above example looks normal, there’s nothing special about it: JavaScript finds things to execute, runs them in order. Let’s see how to defer a function until the stack is clear. The use case of setTimeout(() => {}), 5000) is to call a function, but execute it once every other function in the code has executed.


```js

console.log('Hi');
setTimeout(function cb1() { 
  console.log('cb1');
}, 5000);
console.log('Bye');
```

Let’s “execute” this code and see what happens:
1. The state is clear. The browser console is clear, and the Call Stack is empty.
2. `console.log('Hi')` is added to the Call Stack.
3. `console.log('Hi')` is executed.
4. `console.log('Hi')` is removed from the Call Stack.
5. `setTimeout(function cb1() { ... })` is added to the Call Stack.
6. `setTimeout(function cb1() { ... })` is executed. The browser creates a timer as part of the Web APIs. It is going to handle the countdown for you.
7. The `setTimeout(function cb1() { ... })` itself is complete and is removed from the Call Stack.
8. `console.log('Bye')` is added to the Call Stack.
9. `console.log('Bye')` is executed.
10. `console.log('Bye')` is removed from the Call Stack.
11. After at least 5000 ms, the timer completes and it pushes the cb1 callback to the Callback Queue.
12. The Event Loop takes cb1 from the Callback Queue and pushes it to the Call Stack(when the **call stack is empty**)
13. `cb1` is executed and adds `console.log('cb1')` to the Call Stack.
14. `console.log('cb1')` is executed.
15. `console.log('cb1')` is removed from the Call Stack.
16. `cb1` is removed from the Call Stack.

![Stack and Event Loop frames](https://cdn-images-1.medium.com/max/1000/1*TozSrkk92l8ho6d8JxqF_w.gif)

#### How setTimeout(…) works
It’s important to note that setTimeout(…) **doesn’t automatically put your callback on the event loop queue**. It sets up a timer. When the timer expires, the environment places your callback into the event loop. That doesn’t mean that `cb1` will be executed in 5,000 ms but rather that, in 5,000 ms, `cb1` *will be added to the queue*. The queue, however, might have other events that have been added earlier — your callback will have to wait or call stack is not empty to push it there.