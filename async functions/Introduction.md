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

**Promise is fulfilled:**

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

**Promise is rejected:**


```js
async function asyncFunc() {
    try {
        await otherAsyncFunc();
    } catch (err) {
        console.error(err);
    }
}
```

```js
// Equivalent to:
function asyncFunc() {
    return otherAsyncFunc()
    .catch(err => {
        console.error(err);
    });
}
```

## Error Handling

With promises, an async function have two possible return values: resolved value, and rejected value. And we can use .then() for normal case and .catch() for exceptional case. Let's see how we handle errors in async functions:

#### A) try…catch statement

The most standard way is to use `try...catch` statement. When await a call, any rejected value will be thrown as an exception. Here is an example:

```js
async function asyncFunc() {
    try {
        await otherAsyncFunc();
    } catch (err) {
        console.error(err);
    }
}
```

The catched error is exactly the rejected value. After we caught the exception, we have **several ways to deal** in catch(err) {..} block with it:

 - Handle the exception, and return a normal value. (Not using any return statement in the catch block is equivalent to using return undefined; and is a normal value as well.) `catch(err) { return 'fail' }`

 - Throw it (err), if you want the caller to handle it. `catch (err) { throw err }`

 - Reject it, like return Promise.reject(error). This is equivalent to throw error. `catch (err) { return Promise.reject(err) }`

#### B) Using .catch

As we declare before the functionality of await is to wait for a promise to be fulfilled or rejected. That's why we can handle the `await otherAsyncFunc` as a Promise using `then` or `catch` method (as an async function **always returns a Promise**). So we can write error handling like this:

```js
function asyncFunc() {
    const result = await otherAsyncFunc()
        .catch((error) => { console.log(error); });
}
```

Of course after we caught the exception in `.catch` method we can use the above way to deal it.

## How it works

 - There are Async Functions. These are declared by prepending the word async in their declaration: `async function asyncFunc() {....}`

 - An Async function always returns Promises.

 - Your code can be "paused" waiting for an Async Function with await

 - await returns whatever the async function returns when it is done. It will return result if Promise is fulfilled or will throw an error if Promise is rejected.

 - await can only be used *inside* an async function. If you try to use await operator outside of an async function you will get Error.

 - If an Async Function throws an exception, the exception will bubble up to the parent functions just like in normal
  Javascript, and can be caught with try/catch or we can add .catch() method directly in `await asyncFunc.catch(error => { })` just like in Promises.

#### Conclusion 1: 
**An Async function always returns Promises**, whether you use await or not. That promise resolves with whatever the async function returns, or rejects with whatever the async function throws. The async keyword  declares that the asyncFunc() function returns value which is guaranteed **to be a promise**, so that callers can call asyncFunc().then(...) or await asyncFunc() safely. So when we use `async` keyword we create an **asynchronous functions that always return a Promise**. This asyncFunc function will now return a Promise, even if it’s only doing synchronous logic.