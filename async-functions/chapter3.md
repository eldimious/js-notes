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

- Example 6:

```js
const resolveAfter2Seconds = () => {
  console.log("starting slow promise");
  return new Promise(resolve => {
    setTimeout(function() {
      resolve(20);
      console.log("slow promise is done");
    }, 2000);
  });
};

const resolveAfter1Second = () => {
  console.log("starting fast promise");
  return new Promise(resolve => {
    setTimeout(function() {
      resolve(10);
      console.log("fast promise is done");
    }, 1000);
  });
};

const sequentialStart = async() =>{
  console.log('==SEQUENTIAL START==');

  // If the value of the expression following the await operator is not a Promise, it's converted to a resolved Promise.
  const slow = await resolveAfter2Seconds();

  const fast = await resolveAfter1Second();
  console.log(slow);
  console.log(fast);
}
sequentialStart();
```

Output:

```js
1. ==SEQUENTIAL START==
2. starting slow promise
3. slow promise is done
4. starting fast promise
5. fast promise is done
6. 20
7. 10
```

- Example 7:

```js
const resolveAfter2Seconds = () => {
  console.log("starting slow promise");
  return new Promise(resolve => {
    setTimeout(function() {
      resolve(20);
      console.log("slow promise is done");
    }, 2000);
  });
};

const resolveAfter1Second = () => {
  console.log("starting fast promise");
  return new Promise(resolve => {
    setTimeout(function() {
      resolve(10);
      console.log("fast promise is done");
    }, 1000);
  });
};

const concurrentStart = async() => {
  console.log('==CONCURRENT START with await==');
  const slow = resolveAfter2Seconds(); // starts timer immediately -> we do not use await to pause
  const fast = resolveAfter1Second();

  console.log(await slow);
  console.log(await fast); // waits for slow to finish, even though fast is already done!
}

concurrentStart();
```

Output:

```js
1. ==CONCURRENT START with await==
2. starting slow promise
3. starting fast promise
4. fast promise is done
5. slow promise is done
6. 20
7. 10
```

- Example 8:

```js
const resolveAfter2Seconds = () => {
  console.log("starting slow promise");
  return new Promise(resolve => {
    setTimeout(function() {
      resolve(20);
      console.log("slow promise is done");
    }, 2000);
  });
};

const resolveAfter1Second = () => {
  console.log("starting fast promise");
  return new Promise(resolve => {
    setTimeout(function() {
      resolve(10);
      console.log("fast promise is done");
    }, 1000);
  });
};

const stillConcurrent = async() => {
  console.log('==CONCURRENT START with Promise.all==');
  Promise.all([resolveAfter2Seconds(), resolveAfter1Second()]).then((messages) => {
    console.log(messages[0]); // slow
    console.log(messages[1]); // fast
  });
}

stillConcurrent();
```

Output:

```js
1. ==CONCURRENT START with Promise.all==
2. starting slow promise/starting fast promise
3. fast promise is done
4. slow promise is done
5. 20
6. 10
```

- Example 9:

```js
const resolveAfter2Seconds = () => {
  console.log("starting slow promise");
  return new Promise(resolve => {
    setTimeout(function() {
      resolve(20);
      console.log("slow promise is done");
    }, 2000);
  });
};

const resolveAfter1Second = () => {
  console.log("starting fast promise");
  return new Promise(resolve => {
    setTimeout(function() {
      resolve(10);
      console.log("fast promise is done");
    }, 1000);
  });
};

const parallel = () => {
  console.log('==PARALLEL with Promise.then==');
  resolveAfter2Seconds().then((message)=>console.log('2sec:', message));
  resolveAfter1Second().then((message)=>console.log('1sec:', message));
};

parallel()
```

Output:

```js
1. ==PARALLEL with Promise.then==
2. starting slow promise
3. starting fast promise
4. fast promise is done
5. 1sec: 10
6. slow promise is done
7. 2sec: 20
```
