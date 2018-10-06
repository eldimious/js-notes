# Introduction

Async functions allow you to write promise-based code as if it were synchronous, but without blocking the main thread. Async functions work like this:

A) Fulfilling the Promise of an async function:

```js
async function asyncFunc() {
    return 123;
}
```

which is equivalent to:

```js
function asyncFunc() {
    return Promise.resolve(123);
}
```
or

```js
function asyncFunc() {
    return new Promise((resolve, reject) => {
    	return resolve(123);
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

####Conclusion 1: 
**An Async functions always return Promises**. The async keyword  declares that the asyncFunc() function return value is guaranteed **to be a promise**, so that callers can call asyncFunc().then(...) or await asyncFunc() safely. 