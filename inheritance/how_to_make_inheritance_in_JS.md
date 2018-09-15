One of JavaScript its best features is the ability to create and inherit from objects without classes and class inheritance. Its a **combination** of **delegate prototypes, runtime object extension, and closures** which allows you to express three distinct kinds of prototypes in JavaScript, which provide significant advantages over classical inheritance.


# Prototype Delegation
A prototype is a **working object instance**. Objects inherit directly from other objects.
In JavaScript, an object may have a link to a prototype for delegation. If a property is **not found on** the object, the lookup is delegated to the delegate prototype. So using, delegate prototypes (setting the prototype of one instance to refer to an examplar object), it’s literally **Objects Linking to Other Objects**, or OLOO, as Kyle Simpson calls it

## ES5 constructor function version(Function Constructor):
	
**Pos of using Function Constructor & Class**

1. *this* refers to the new object.

2. micro-optimization performance benefit

**Cos of using Function Constructor & Class**

1. Required `new`. Constructors that aren’t running in strict mode can be  dangerous. If a caller forgets `new` and you’re not using strict mode or ES6 classes, anything you assign to `this` will pollute the global namespace.

2. *Constructors violate the open/closed principle* because they couple all callers to the details of how your object gets instantiated.

3. *Constructors break the Open / Closed Principle*. API should be open for extension, but closed for modification.
```javascript
function Animal (name) {
  this.name = name;
}

Animal.prototype.hello = function hello () {
  return 'Hello, my name is ' + this.name;
}

const mouse = new Animal('mouse');

console.log(mouse.hello()); // Hello, my name is mouse
```


## ES6 constructor function version('Class' -> sugar style of Function Constructor): ###

**Cos of using Class**

1. Temptation for users to create problematic class hierarchies using the extends keyword. Drive to the fragile base class problem, the gorilla banana problem, the duplication by necessity problem, 

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

## Factory function and Object.create() ###

`Object.create()` is an ES5 feature that was championed by Douglas Crockford so that we could attach delegate prototypes without using constructors and the `new` keyword.

**Pos of Using Factories**

1. there is not any need of new constructor. No new neeeded.

2. `this` behaves as it normally would, so you can use it to access the parent object. 

3. Can create a closure inside function factory for data privacy. Closures are a common pattern in JavaScript, and they’re commonly used for data privacy.

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

OR example with private data:


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

This way, we use a closure inside factory function to add a "private variable", in order the produced instance not to be able to have access or change to this private variable. The instance can only get the private variable via a method getPrivateData(), The produced instance will not have a property called secret or privateData, but will have a method getPrivateData().

**Drawbacks**

One major drawback to delegation is that it’s not very good for storing state. If you try to store state as objects or arrays, mutating any member of the object or array will mutate the member for every instance that shares the prototype. In order to preserve instance safety, you need to make a copy of the state for each object. <br />
So, a good approach is to **store in prototype only shared methods among instances**
