# Introduction

## Async function always return Promises 

Async functions allow you to write promise-based code as if it were synchronous, but without blocking the main thread. Async functions work like this:

A) Fulfilling the Promise of an async function:

```js
async function asyncFunc() {
    return 'result';
}
```

which is equivalent to:

```js
function asyncFunc() {
    return Promise.resolve('result');
}
```
or

```js
function asyncFunc() {
    return new Promise((resolve, reject) => {
    	return resolve('result');
   	})
}
```

B) Rejecting the Promise of an async function:

```js
async function asyncFunc() {
    throw new Error('We have error!');
}
```

which is equivalent to:


```js
async function asyncFunc() {
    return Promise.reject(new Error('We have error!'));
}
```

which is equivalent to:

```js
async function asyncFunc() {
    return new Promise((resolve, reject) => {
    	return reject(new Error('We have error!'));
   	})
}
```


## await operator

The await keyword is used to handle Promises inside the async function. So, handling **results and errors** of asynchronous computations takes place via `await`. The operator await (which is only allowed inside async functions) waits for its operand, a Promise, to be settled: It ‘pauses’ the function until fetchUsers is done, and returns the result. The operator await (which is only allowed inside async functions) waits for its operand, a Promise, to be settled (‘pauses’ the function until asyncFunc is fulfilled/rejected, and returns the result/error):

 - If the Promise is fulfilled, the result of await is the fulfillment value.

 - If the Promise is rejected, await throws the rejection value.

```js
async function asyncFunc() {
    const result = await otherAsyncFunc();
    console.log(result);
}
```
```js
// Equivalent to:
function asyncFunc() {
    return otherAsyncFunc()
    .then(result => {
        console.log(result);
    });
}
```

#### Conclusion 1: 
**An Async function always returns Promises**, whether you use await or not. That promise resolves with whatever the async function returns, or rejects with whatever the async function throws. The async keyword  declares that the asyncFunc() function returns value which is guaranteed **to be a promise**, so that callers can call asyncFunc().then(...) or await asyncFunc() safely. So when we use `async` keyword we create an **asynchronous functions that always return a Promise**. This asyncFunc function will now return a Promise, even if it’s only doing synchronous logic.