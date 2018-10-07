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

#### A) Iterate on array of promises:

We can use Promise.all() to do so:

```js

async function foo() {
    const [result1, result2] = await Promise.all([
        asyncFunc1(),
        asyncFunc2(),
    ]);
}
```