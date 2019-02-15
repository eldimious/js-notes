 # Introduction

 ## Javascript under the hood

 JavaScript unlike other programming languages, it’s **single threaded**. That means, code execution will be done one at a time. so there is just one thing happening at a time. Since code execution is done sequentially, any code that takes longer time to execute, will block anything that needs to be executed. Hence sometimes you see below screen while using Google Chrome. Same on Node.js as JavaScript code runs on a single thread too.

 ![](https://cdn-images-1.medium.com/max/1000/0*w2rEwv9mE9xVPhRy.png)

 You just need to pay attention to how you write your code and avoid anything that could block the thread, like synchronous network calls or infinite loops.


 ## Basic Architecture

 ![Overview of major components in a browser](https://cdn-images-1.medium.com/max/1200/1*7GXoHZiIUhlKuKGT22gHmA.png)

 ![Overview of major components in a nodeJS](https://i.stack.imgur.com/QRePV.jpg)
