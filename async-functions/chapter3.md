# Examples

- Example 1:

```js
const axios = require('axios')

async function multipleRequestsAsync() {
   try {
     console.log("promise invoked...");                // 3
     const response1 = await axios.get('https://www.google.gr/'); // 4
     console.log("done");                       // 7
   } catch (error) {
     console.error(error);
   }
}

console.log("before start execution");                   // 1
const promise = multipleRequestsAsync();       // 2
console.log("continuing...");
```

Output:

```js
1. before start execution
2. promise invoked...
3. continuing...
4. done
```


- Example 2:

```js
const p = async(duration) => {
  return new Promise(function(resolve, reject){
    console.log('promise starts execution')
    setTimeout(function(){
      console.log('timeout')
      resolve();
    }, duration)
  });
};

(async () => {
  console.log('start')
  p(3000).then(x => {console.log('resolved')});
  console.log('end')
})();
```

Output:

```js
1. start
2. promise starts execution
3. end -> we do not use "await" to pause the execution of next line -> so code will continue to executed
4. timeout
5. resolved
```

- Example 3:

```js
const p = async(duration) => {
  return new Promise(function(resolve, reject){
    console.log('promise starts execution')
    setTimeout(function(){
      console.log('timeout')
      resolve('result');
    }, duration)
  });
};

(async () => {
  console.log('start')
  const res = await p(3000);
  console.log(res);
  console.log('end')
})();
```

Output:

```js
1. start
2. promise starts execution -> because of await we "pause" the execution of next line until promise is resolved
3. timeout
4. result
5. end
```

- Example 4:

```js
const p = async(duration) => {
  return new Promise(function(resolve, reject){
    console.log('promise starts execution')
    setTimeout(function(){
      console.log('timeout')
      resolve();
    }, duration)
  });
};

(async () => {
  console.log('start')
  p(3000);
  console.log('end')
})();
```

Output:

```js
1. start
2. promise starts execution
3. end -> we dont pause the code using await, so the execution will continue to next line and when promise is resolved will type the result
4. timeout
```

- Example 5:

```js
const pro = () => {
  console.log('promise starts execution')
  return Promise.resolve('resolved')
};

setTimeout(() => {
  console.log('setTimeout1');
}, 0);

console.log('start');

(async () => {
  setTimeout(() => {
    console.log('setTimeout2');
  }, 0);
  console.log('async starts')
  const res = await pro();
  console.log('async completed', res)
  console.log('async ends')
})();

console.log('code next line continues')
```

Output:

```js
1. start
2. async starts
3. promise starts execution
4. code next line continues
4. async completed resolved
5. async ends
6. setTimeout1
7. setTimeout2
```
