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
