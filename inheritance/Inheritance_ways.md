One of JavaScript its best features is the ability to create and inherit from objects without classes and class inheritance. Its a **combination** of **delegate prototypes, runtime object extension, and closures** which allows you to express three distinct kinds of prototypes in JavaScript, which provide significant advantages over classical inheritance.


### Prototype Delegation
A prototype is a **working object instance**. Objects inherit directly from other objects.
In JavaScript, an object may have a link to a prototype for delegation. If a property is **not found on** the object, the lookup is delegated to the delegate prototype. So using, delegate prototypes (setting the prototype of one instance to refer to an examplar object), it’s literally **Objects Linking to Other Objects**, or OLOO, as Kyle Simpson calls it

*ES5 constructor function version:*

```javascript
function Greeter (name) {
  this.name = name || 'John Doe';
}

Greeter.prototype.hello = function hello () {
  return 'Hello, my name is ' + this.name;
}

var george = new Greeter('George');

var msg = george.hello();

console.log(msg); // Hello, my name is George```