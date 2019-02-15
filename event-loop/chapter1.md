 # Introduction

 ## Javascript under the hood

 JavaScript unlike other programming languages, it’s **single threaded**. That means, code execution will be done one at a time. so there is just one thing happening at a time. Since code execution is done sequentially, any code that takes longer time to execute, will block anything that needs to be executed. Hence sometimes you see below screen while using Google Chrome. Same on Node.js as JavaScript code runs on a single thread too.

 ![](https://cdn-images-1.medium.com/max/1000/0*w2rEwv9mE9xVPhRy.png)

 You just need to pay attention to how you write your code and avoid anything that could block the thread, like synchronous network calls or infinite loops.


 ## Basic Architecture

 ![Overview of major components in a browser](https://cdn-images-1.medium.com/max/1200/1*7GXoHZiIUhlKuKGT22gHmA.png)

 ![Overview of major components in a nodeJS](https://i.stack.imgur.com/QRePV.jpg)

 ![NodeJS architecture](https://dotnettricksweb.blob.core.windows.net/img/nodejs/node.js-architecture.png)

 Like any other programming language, JavaScript runtime has one stack and one heap storage.

 - **Heap** - Objects are allocated in a heap which is just a name to denote a large mostly unstructured region of memory

 - **Stack** - This represents the single thread provided for JavaScript code execution. Function calls form a stack of frames. Stack is LIFO (last in, first out) data storage which store current function execution context of a program.

 - **Browser or Web APIs** are built into your web browser, and are able to expose data from the browser and surrounding computer environment and do useful complex things with it. They are not part of the JavaScript language itself, rather they are built on top of the core JavaScript language, providing you with extra superpowers to use in your JavaScript code. If you’re a Node.js developer, these are the C++ APIs.
