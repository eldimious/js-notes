# Loops

The following code make two asynchronous function calls, asyncFunc1() and asyncFunc2().

```js
async function foo() {
    const result1 = await asyncFunc1();
    const result2 = await asyncFunc2();
}
```

This code will:

- `await asyncFunc1()` will wait until asyncFunc1() returns.

- Then `await asyncFunc2()` will be called.


Notice that asyncFunc2() does not depend on the result of asyncFunc1() and in fact they can be called in parallel!  However by using await here these two calls become **sequential** and the total execution time will be much longer than the parallel version.

## Parallel operation:

#### A) Iterate over array of promises:

We can use Promise.all() to do it. Instead of awaiting two Promises, we are now awaiting a Promise for an Array with two elements.

```js

async function foo() {
  const [result1, result2] = await Promise.all([
    asyncFunc1(),
    asyncFunc2(),
  ]);
}
```

#### B) Iterate over array:

Let's say that we have an array of items and we want for each element run a promise function:

 - *Solution 1 (Native):*

```js
const arr = [1, 2, 3, 4, 5];

async function asyncFunc(item) {
  return item;
}

(async() => {
  const results = await Promise.all(arr.map(async (item) => {
    const res = await asyncFunc(item);
    return res;
  }));
  console.log('results', results); // Output: [1, 2, 3, 4, 5]
})();
```

 - *Solution 2 (Bluebird):*

There is another solution for it if you are not using native Promises but `Bluebird`. You could also try using `Promise.map()`, which is mixing the array.map and Promise.all

```js
const Promise = require('Bluebird')
const arr = [1, 2, 3, 4, 5];

async function asyncFunc(item) {
  return item;
}

(async() => {
  const results = await Promise.map(async (item) => {
    const res = await asyncFunc(item);
    return res;
  });
  console.log('results', results); // Output: [1, 2, 3, 4, 5]
})();
```

## Sequential operation:

#### A) Call different functions:

```js
async function foo() {
  const result1 = await asyncFunc1();
  const result2 = await asyncFunc2();
}
```

#### A) Iterate over array:

- *Solution 1 (Native):*

```js
const arr = [1, 2, 3, 4, 5];

async function asyncFunc(item) {
  return item;
}
(async() => {
  const results = await arr.reduce(async (promise, item) => {
    // This line will wait for the last async function to finish.
    // The first iteration uses an already resolved Promise
    // so, it will immediately continue.
    const accumulator = await promise;
    const result = await asyncFunc(item)
    return [...accumulator, result]
  }, Promise.resolve([]));    
  console.log('results', results); // Output: [1, 2, 3, 4, 5]
})();
```