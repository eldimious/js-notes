# Introduction

One of JavaScript best features is the ability to create and inherit from objects **without classes and class inheritance**. Its a **combination** of **delegate prototypes, runtime object extension, and closures** which allows you to express three distinct kinds of prototypes in JavaScript, which provide significant advantages over classical inheritance. In this chapter we will discover the *ways we can use to implement inheritance in JS*.


# A) Prototype Delegation
A prototype is a **working object instance**. Objects inherit directly from other objects. So in prototypal inheritance, instances inherit from other instances. Using delegate prototypes we are setting the prototype of one instance to refer to an examplar object.<br />
In JavaScript, an object may have a **link to a prototype for delegation**. If a property is **not found on** the object, the lookup is delegated to the delegate prototype, which may have a link to its own delegate prototype, and so on up the chain until you arrive at `Object.prototype`, which is the root delegate. So using, delegate prototypes (setting the prototype of one instance to refer to an examplar object), it’s literally **Objects Linking to Other Objects**, or OLOO, as Kyle Simpson calls it.<br />
We can achieve a prototype delegation using: <br />

1. ES5 constructor function.

2. ES6 class (sugar style of constructor function).

3. Function factory using Object.create().


**Pos of using Prototype Delegation**

1. Micro-optimization performance benefit. Method delegation can  **preserve memory resources** because you only need one copy of each method to be shared by all instances.

2. Really good to **store public/shared methods** on a shared prototype. Thats way, you have just one copy shared among all instances. If you add a method in prototype, it will affect all the instances.


**Cos of using Prototype Delegation**

1. One major drawback to delegation is that it’s **NOT** very good for **storing states**(properties). If you try to store state as *objects or arrays*, mutating any member of the object or array will *mutate the member for every instance that shares the prototype*. In order to preserve instance safety, you need to make a copy of the state for each object. In order to preserve instance safety, you need to make a copy of the state for each object. <br />
So, a good approach is to **store in prototype only shared methods among instances** and use the Concatenative inheritance(mixins), using for example Object.assign, to store states for each instance.<br />

```js
Object.assign(Object.create(protoObj),
  // here we can store states with safety as the instances and the "source" 
  // object and the instances among them do not retain a reference.
  // So, changing user.surname will affect only the current instance, and 
  // not the other ones.
  {
    user: {
      surname: surname,
    }
  }
);
```


### ES5 constructor function version(Function Constructor):
	
**Pos of using Function Constructor & Class**

1. *this* refers to the new object.

2. Micro-optimization performance benefit

**Cos of using Function Constructor & Class**

1. Required `new`. Constructors that aren’t running in strict mode can be  dangerous. If a caller forgets `new` and you’re not using strict mode or ES6 classes, anything you assign to `this` will pollute the global namespace.

2. *Constructors violate the open/closed principle* because they couple all callers to the details of how your object gets instantiated.

3. *Constructors break the Open / Closed Principle*. API should be open for extension, but closed for modification.

4. Using Constructors Enables the Deceptive `instanceof`.  instanceof does an identity check comparing the object’s `[[Prototype]]` object to the `Constructor.prototype` property.


```javascript
function Animal (name) {
  this.name = name;
}

Animal.prototype.hello = function hello () {
  return 'Hello, my name is ' + this.name;
}

const mouse = new Animal('mouse');

console.log(mouse); // Has as it's own property the name and as prototype method the hello()
  // {
  //  name: "mouse"
  //  __proto__:
  //  hello: ƒ hello()
  //  constructor: ƒ Animal(name)
  //  }
console.log(mouse.hello()); // Hello, my name is mouse
delete mouse.name;
console.log(mouse.hello()); // Hello, my name is undefined
```


### ES6 constructor function version('Class' -> sugar style of Function Constructor): ###

**Cos of using Class**

1. Temptation for users to create problematic class hierarchies using the extends keyword. Drive to the fragile base class problem, the gorilla banana problem, the duplication by necessity problem. "You wanted a banana but what you got was a gorilla holding the banana and the entire jungle.” ~ Joe Armstrong

2. The Tight Coupling Problem: The coupling between a child class and its parent is the tightest form of coupling in OO design. That’s the opposite of reusable, modular code.

```javascript
class Animal {
  constructor (name) {
    this.name = name;
  }
  hello () {
    return `Hello, my name is ${ this.name }`;
  }
}

const mouse = new Animal('mouse');

console.log(mouse.hello()); // Hello, my name is mouse
```

### Factory function and Object.create()

`Object.create()` is an ES5 feature that was championed by Douglas Crockford so that we could attach delegate prototypes without using constructors and the `new` keyword.

**Pos of Using Factories**

1. There is not any need of `new` constructor. No new neeeded. There’s no risk of using it in the “wrong” way as it does not require `new`, so there is not any risk of pollute the global namespace.

2. `this` behaves as it normally would, so you can use it to access the parent object. 

3. Can create a closure inside function factory for data privacy. Closures are a common pattern in JavaScript, and they’re commonly used for data privacy.

4.  The prototype property isn’t used(for example mouse.prototype = Animal;), so there will be no instanceof link between the function and the objects it creates. It is simply a function that happens to create objects.

**Cos of Using Factories**

1. `this` doesn’t refer to the new object inside the factory.

2. It may perform slower than a constructor function in micro-optimization benchmarks.

```javascript

const animal = {
  hello () {
    return `Hello, my name is ${ this.name }`;
  }
};

const mouseFactory = (name) => Object.assign(Object.create(animal), {
  name
});

const mouse = mouseFactory('mouse');

console.log(mouse.hello()); // Hello, my name is mouse
```

**OR example with private data:**

This way, we use a closure inside factory function to add a "private variable", in order the produced instance not to be able to have access or change to this private variable. The instance can only get the private variable via a method getPrivateData(), The produced instance will not have a property called secret or privateData, but will have a method getPrivateData().

```javascript

const animal = {
  hello () {
    return `Hello, my name is ${ this.name }`;
  }
};

const mouseFactory = function mouseFactory(name, secret) {
	const privateData = secret;
	return Object.assign(Object.create(animal), {
		name,
		getPrivateData() {
			return privateData;
		}
	})
};

const mouse = mouseFactory('mouse', 'secret');

console.log(mouse.hello()); // Hello, my name is mouse
console.log(mouse.getPrivateData()); // secret
```

# B) Concatenative inheritance(mixins):

Concatenative inheritance is the process of **copying the properties** from one object to another, **without retaining a reference** between the two objects. <br /> 
This process is commonly achieved using `Object.assign()`.It will copy all of the enumerable own properties by assignment from the source objects to the destination objects with last in priority.  <br /> 
Concatenative inheritance is possible because of a feature in JavaScript known as dynamic object extension: the ability to add to an object after it has been instantiated.
So, using concatenative inheritance, you just copy properties from an exemplar object to a new instance, without retaining a reference among them.

**Pos of using Concatenative inheritance**

1. Is really good to save states as the instances and the "source" object and the instances among them do not retain a reference. So for example if the state is an object/array and we mutate an object value **will NOT affect  the other instances**. 

**Cos of using Concatenative inheritance**

1. Perhaps more slow from Prototype Delegation, as you need to copy each method property for each instance.

```js
const animal = {
  hello() {
    return `Hello, my name is ${ this.name }`;
  }
};

const mouse = Object.assign({}, 
  animal,
  {
    name: 'mouse',
  }
);

console.log(mouse.hello()); // Hello, my name is mouse
```

# C) Functional Inheritance

Coined by Douglas Crockford in “JavaScript: The Good Parts”. In JavaScript, any function can create an object. When that function is not a constructor (or `class`), it’s called a factory function. <br /> 
Functional inheritance makes use of a **factory function**, and then tacks on new properties **using concatenative inheritance**. So it works by **producing an object from a factory**, and **extending the produced object by assigning properties to it directly (using concatenative inheritance-> Object.assign)**.Functions created for the purpose of extending existing objects are commonly referred to as functional mixins

**Pos of using Concatenative inheritance**

1. Allows you to use the function **closure** to encapsulate **private data**. In other words, you can enforce private state.

2. Compibing it with concatenative inheritance(Object.assign), we can **store states** as we copy properties from an exemplar object to a new instance, without retaining a reference among them.

**Cos of using Concatenative inheritance**

1. Perhaps more slow from Prototype Delegation, as you need to copy each method property for each instance. If you’re creating a hundreds of thousands of objects and you need your app to perform smoothly at or near realtime (think game engines, realtime signal processors etc), delegating calls to methods(adding shared methods in a prototype obj) can save you from a lot of manual memory management.


```js
const animal = function (secret) {
  const privateData = secret;
  const attrs = {};

  return Object.assign(this, {
    hello(name) {
      return `Hello, my name is ${ this.name }`;;
    },
    set(name, value) {
      attrs[name] = value;
    },
    get (name) {
      return attrs[name];
    }
    getPrivateData() {
      return privateData;
    }
  });
};

const mouse = animal('secret');
console.log(mouse.hello('mouse')); // Hello, my name is mouse
```

Conclusions:

1. No need of using classes in JavaScript.

2. Avoid `new` constructor. We can use Object.create() to inherit from prototype.

3. With dynamic object extension(`Object.assign` for states), object literals and `Object.create()`(for shared methods), we have everything we need. And `this` behaves just like it does in any other function.
