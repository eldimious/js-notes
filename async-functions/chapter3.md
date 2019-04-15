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

The sequence of execution is:

```js
1. before start execution
2. promise invoked...
3. continuing...
4. done
```
