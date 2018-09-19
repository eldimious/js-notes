# Prototype Inheritance + Concatenative Inheritance(cloning/mixing) + Function Factory

In this section we will try to combine the 3 ways of making inheritance in JS. So we will mix:

1. **Prototype Delegation**: best for shared methods, better in memory consumption

2. **Concatenative inheritance(mixins)**: best for saving states using Object.assign.

3. **Functional Inheritance**: best for encapsulating private data using closures.


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
