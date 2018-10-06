# Introduction

Async functions allow you to write promise-based code as if it were synchronous, but without blocking the main thread. Async functions work like this:

```js
async function asyncFunc() {
    return 123;
}
```

it is equivalent to:

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

**An Async functions always return Promises**. The async keyword  declares that the asyncFunc() function return value is guaranteed **to be a promise**, so that callers can call asyncFunc().then(...) or await asyncFunc() safely. 