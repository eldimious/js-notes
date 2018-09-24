# Best practice in inheritance

In this section we will try to combine the 3 ways of making inheritance in JS. So we will mix:

1. **Prototype Delegation**: 
  
  1. best for shared methods. 

  2. better in memory consumption.


2. **Concatenative inheritance(mixins)**: 


  1. best for saving states using Object.assign.


3. **Functional Inheritance**: 

  1. best for encapsulating private data using closures.


### Example:

```js
const animal = {
  hello() {
    return `Hello, my name is ${ this.info.name }`;
  }
};

const mouseFactory = function mouseFactory(info, secret) {
  // closure to handle private data
  const privateData = secret;
  return Object.assign(Object.create(animal),
    // here we can store states with safety as the instances and the 
    // "source" 
    // object and the instances among them do not retain a reference.
    // So, changing user.surname will affect only the current instance, and 
    // not the other ones.
    {
      info: {
        name: info.name,
        surname: info.surname,
      },
      getPrivateData() {
        return privateData;
      }
    }
  )
};

const mouse = mouseFactory({ name: 'micky', surname: 'mouse'}, 'disney');
const cat = mouseFactory({ name: 'perseas', surname: 'nasikas'}, 'pet');
```

```js
console.log('mouse', mouse);
console.log('cat', cat);
console.log(mouse.info); // -> { name: 'micky', surname: 'mouse'}
console.log(cat.info); // -> {name: "perseas", surname: "nasikas"}
console.log(mouse.hello()); // -> Hello, my name is micky
console.log(cat.hello()) // -> Hello, my name is perseas
```

#### Now we will mutate the object state (info) in an instance to see if this change will affect the other instance.

```js
mouse.info.name = 'mutated';
console.log(mouse.info); // -> {name: "mutated", surname: "mouse"}
console.log(cat.info); // -> {name: "perseas", surname: "nasikas"}
```

#### Lets check if we can access the private data.

```js
console.log(mouse.privateData); // -> undefined
console.log(mouse.secret); // -> undefined
console.log(mouse.getPrivateData()); // -> disney
mouse.secret = 'mutated'
console.log(mouse.getPrivateData()); // -> disney
```

### Conclusions:

  1. Mutating any member of the object or array does NOT affect the other instances, because we use **Object.assign()** to save states, so it copis all of the enumerable own properties by assignment from the source objects to the destination objects with last in priority and the the **instances do not retain a reference.**

  2. Using function factory with closure we can encapsulate private data(secret). The produced instance has not any direct access to this private data. It can only **access it using the method getPrivateData()**. Also, it is **not possible to change the private data directly** (for example using: mouse.secret=...). In order to be able to change the private data, we have to define a new method for example: ` setPrivateData(newSecret) { privateData = newSecret; return this; }` and the call `mouse.settPrivateData('changed')`.

  3. We use a prototype object only for **shaving public/shared methods**.
