# Javascript Code samples

1. [Variable scope](#variable-scope)
2. [Context](#context)
3. [Closure](#closure)
4. [call, apply and bind](#call-apply-and-bind)
5. [Functions](#functions)
6. [Objects](#objects)
7. [ES6](#es6)
8. [Class](#class)
9. [Promises](#promises)

## Variable scope

The scope is the current context of execution. The context in which values and expressions are "visible," or can be referenced. If a variable or other expression is not "in the current scope," then it is unavailable for use. Scopes can also be layered in a hierarchy, so that child scopes have access to parent scopes, but not vice versa.

### Global scope
A variable declared outside of all the function or without a `var` keyword is a global variable in Javascript. This variable can be accessed from anywhere in the program.

```
// global scope
var a = 1;
b = 2;

function one() {
  alert(a); // alerts '1'
  alert(b); // alerts '2'
}
```
If you use a `this` keyword outside an object, it resolves to `window` object which is a global object in Javascript.
```
// global scope
this.a = 1;
b = 2;
console.log(this.a);   // 1
console.log(window.a); // 1
console.log(window.b); // 2
```
### Local scope
If the variable is declared inside a function it takes the functions as its scope. This variable can be accessed within the function and not from outside the function. 
```
// global scope
var a = 1;

function one() {
  // local scope
  var a = 2;
  alert(a); // alerts '2'
}
alert(a); // alerts '1'
```
If you omit the `var` keyword wihtin the function it is declared as a global variable.
```
function one() {
  // not local but global, 
  a = 2;
  alert(a); // alerts '2'
}
one();
alert(a); // alerts '2'
```
If a variable resolved in a local scope, it will not check upwards towards global scope. For example, if the function `one` has a variable `a` declared it will take precedence over the global scope within the function.
### Block scope
ES6 introduced `let` and `const` which declares variables known only to the _enclosing_ parenthesis `{}`. For example, `for` loop or `if` condition.
```
for (let i = 0; i < 10; i++) {
  console.log(i);
}
console.log(i); // undefined
```
### Catch clause Scope
There is also one special scope in Javascript which like `let`. It is the variable in the `catch` clause. This shodows other global and local variables in the same name, but only available within the `catch` block.
```
try { 
  throw 10;
} catch(e) {
  console.log(e); // 10
}
console.log(e) // Uncaught ReferenceError: e is not defined
```
## Context
Often scope and context are confused among the developers. Scope refers to the visibility of the variable and context refers to the value of the `this` keyword.

### `this` in Global scope
In a global scope, `this` always refers to the `window` object. All global JavaScript objects, functions, and variables automatically become members of the `window` object.
```
console.log(this);
// logs: Window {postMessage: f, blur: f, focus: f, frames: Window, …}
```
### `this` with new
If scope is in the method of an object, context will be the object the method is part of.
```
function User() { 
  console.log(this);
}
new User(); // User {}
```
If we call a function using the `new` keyword, the context will then be set to the instance of the called function.

### `this` with bind
If we bind a function explicitly with some object, then the `this` inside function will be set to the object we bind.
```
function greet() { 
  console.log('Hello ' + this.name);
}
var obj = { name: 'John Doe' };
var greetFn = greet.bind(obj);
greetFn(); // Hello John Doe

// calling greet function normally will take window object for this.
var name = 'Albert';
greet(); // Hello Albert
```
### `this` inside method
If we call a method of an object, then `this` will be automatically set to the current object.
```
var obj = {
  name: 'John Doe',
  greet: function() {
    console.log('Hello ' + this.name);
  }
};
obj.greet(); // Hello John Doe
```

## Closure
### What is Closure?
 A Closure is created when an inner function tries to access the scope chain of its outer function meaning the variables outside of the immediate lexical scope. It means the inner function can access the outer function's variables.
 ```
 function greet() {
    name = 'John Doe';
    return function () {
        console.log('Hi ' + name);
    }
}
greetLetter = greet();
greetLetter(); // logs 'Hi John Doe'
```
 A closure can not only access the variables defined in its outer function but also the arguments of the outer function.
 ### Closure access outer scope
 Closures store references to the outer function's variables; they do not store the actual value. Closures get more interesting when the value of the outer function's variable changes before the closure is called. And this powerful feature can be harnessed in creative ways, such as this private variables.
 ```
 function userid () {
    var id = 999;
    // We are returning an object with some inner functions
    // All the inner functions have access to the outer function's variables
    return {
        getID: function ()  {
            // This inner function will return the UPDATED id variable
            // It will return the current value of id, even after the function changes it
          return id;
        },
        setID: function (theNewID)  {
            // This inner function will change the outer function's variable anytime
            id = theNewID;
        }
    }

}

var mjID = userid (); // At this juncture, the celebrityID outer function has returned.
mjID.getID(); // 999
mjID.setID(567); // Changes the outer function's variable
mjID.getID(); // 567: It returns the updated id variable
```

## `call`, `apply` and `bind`
Call and Apply functions are used to change the context while calling a function. To use the call or apply function, you just need to call it on the function instead of invoking the function using a pair of parenthesis and pass the context as the first argument. The function's own arguments can be passed after the context.
### using `call`
Instead of calling the function directly, add `call` after the function name and pass the context as the first parameter.
```
function greet(greeter) { 
  console.log(greeter + ' ' + this.name);
}
var obj = { name: 'John Doe' };
greet.call(obj, 'Hello');
```
### using `apply`
The difference between `.call()` and `.apply()` is that in Call, we pass the rest of the arguments as a list separated by a comma while `apply` allows us to pass the arguments in an array.
```
function greet(greeter) { 
  console.log(greeter + ' ' + this.name);
}
var obj = { name: 'John Doe' };
greet.apply(obj, ['Hello']);
```
We can also pass the context value as `null` if it is not used inside the function.
```
Math.max.apply(null, [2, 4, 6]); // 6
```
_Call_ is slightly faster in performance than _Apply_.
### Using `bind`
Unlike Call and Apply, Bind doesn't itself call the function, it can only be used to bind the value of context and other arguments before calling the function.
```
function greet() { 
  console.log('Hello ' + this.name);
}
var obj = { name: 'John Doe' };
var greetFn = greet.bind(obj);
greetFn(); // Hello John Doe
```
## Functions
Functions are the main “building blocks” of the program. They allow the code to be called many times without repetition.

### Function declaration
A function declaration looks like this:
```
function greet() {
  alert( 'Hello everyone!' );
}
```
The `function` keyword goes first, then goes the name of the function, then a list of parameters between the parentheses (empty in the example above) and finally the code of the function, also named “the function body”, between curly braces.

There are few points to be noted: A variable declared inside a function is only visible inside that function. A function can access an outer variable as well. But the outer variable is only used if there’s no local one. A function can return a value back into the calling code as the result.

```
function sum(a, b) {
  return a + b;
}
let result = sum(1, 2);
alert( result ); // 3
```
The directive `return` can be in any place of the function. When the execution reaches it, the function stops, and the value is returned to the calling code (assigned to result above). It would not continue further once `return` is reaced.
### Function declaration hoisting
Function declarations in JavaScript are hoisted to the top of the enclosing function or global scope. It means they are moved to top of the execution context. You can use the function before you declared it:
```
hoisted(); // logs "foo"

function hoisted() {
  console.log('foo');
}
```
### Function parameters
We can pass arbitrary data to functions using parameters (also called function arguments) .
```
function greet(from, text) { // arguments: from, text
  alert(from + ': ' + text);
}
greet('Ann', 'Hello!'); // Ann: Hello
```
If a parameter is not provided, then its value becomes `undefined`. If we want to use a “default” text in this case, then we can specify it after `=` in ES6:
```
function greet(from, text = "Hi") {
  alert( from + ": " + text );
}
greet("Ann"); // Ann: Hi
```
The alternate way to provide default parameter is as follow:
```
function greet(from, text) {
  if (text === undefined) {
    text = 'no text given';
  }
  alert( from + ": " + text );
}
```
### Function expression
A function expression is very similar to function statement. The main difference between a function expression and a function statement is the function name, which can be omitted in function expressions to create anonymous functions.
```
var greet = function(name) {
    return "Hello " + name;
}
```
Function expressions in JavaScript are not hoisted, unlike function declarations. You can't use function expressions before you define them:
```
console.log(notHoisted) // undefined 
//even though the variable name is hoisted, the definition isn't. so it's undefined.
notHoisted(); // TypeError: notHoisted is not a function

var notHoisted = function() {
   console.log('bar');
};
```

###  IIFE (Immediately Invoked Function Expression)
An IIFE (Immediately Invoked Function Expression) is a JavaScript function that runs as soon as it is defined.
```
(function () {
    statements
})();
```
It is a design pattern which is also known as a Self-Executing Anonymous Function and contains two major parts. The first is the anonymous function with lexical scope enclosed within the Grouping Operator `()`. This prevents accessing variables within the IIFE as well as polluting the global scope.

The second part creates the immediately executing function expression `()` through which the JavaScript engine will directly interpret the function.
### Named function expression
If you want to refer to the current function inside the function body, you need to create a named function expression. This name is then local only to the function body (scope). 
```
var math = {
  'factit': function factorial(n) {
    console.log(n)
    if (n <= 1) {
      return 1;
    }
    return n * factorial(n - 1);
  }
};

math.factit(3) //3;2;1;
```
## Objects
Objects in JavaScript, just as in many other programming languages, can be compared to objects in real life. The concept of objects in JavaScript can be understood with real life, tangible objects.

In JavaScript, an object is a standalone entity, with properties and type. Compare it with a cup, for example. A cup is an object, with properties. A cup has a color, a design, weight, a material it is made of, etc. The same way, JavaScript objects can have properties, which define their characteristics.

A JavaScript object has properties associated with it. A property of an object can be explained as a variable that is attached to the object. Object properties are basically the same as ordinary JavaScript variables, except for the attachment to objects. The properties of an object define the characteristics of the object. You access the properties of an object with a simple dot-notation

```
objectName.propertyName
```
JavaScript has a number of predefined objects. In addition, you can create your own objects. There are some ways from which we can create new objects.
### Object declration using initializers
You can create objects using an object initializer. Using object initializers is sometimes referred to as creating objects with literal notation.

The syntax for an object using an object initializer is:
```
var x = {greeting: 'hi there'};
var honda = {color: 'red', wheels: 4, engine: {cylinders: 4, size: 2.2}};
```
### Object declration using constructor function
Alternatively, you can create an object with these two steps:
1. Define the object type by writing a constructor function. There is a strong convention, with good reason, to use a capital initial letter.
2. Create an instance of the object with new.

To define an object type, create a function for the object type that specifies its name, properties, and methods. For example, suppose you want to create an object type for cars. You want this type of object to be called Car, and you want it to have properties for make, model, and year. To do this, you would write the following function:
```
function Car(make, model, year) {
  this.make = make;
  this.model = model;
  this.year = year;
}
```
Now you can create an object called mycar as follows:
```
var mycar = new Car('Eagle', 'Talon TSi', 1993);
```

### Object declration using `Object.create` method
Objects can also be created using the Object.create() method. This method can be very useful, because it allows you to choose the prototype object for the object you want to create, without having to define a constructor function.
```
// Animal properties and method encapsulation
var Animal = {
  type: 'Invertebrates', // Default value of properties
  displayType: function() {  // Method which will display type of Animal
    console.log(this.type);
  }
};

// Create new animal type called animal1 
var animal1 = Object.create(Animal);
```
### Object declration With the `class` keyword
ES6 introduced a new set of keywords implementing classes. The new keywords include `class`, `constructor`, `static`, `extends`, and `super`.
```
'use strict';

class Polygon {
  constructor(height, width) {
    this.height = height;
    this.width = width;
  }
}

class Square extends Polygon {
  constructor(sideLength) {
    super(sideLength, sideLength);
  }
  get area() {
    return this.height * this.width;
  }
  set sideLength(newLength) {
    this.height = newLength;
    this.width = newLength;
  }
}

var square = new Square(2);
```
### Defining getters and setters
A _getter_ is a method that gets the value of a specific property. A _setter_ is a method that sets the value of a specific property. You can define getters and setters on any predefined core object or user-defined object that supports the addition of new properties. The syntax for defining getters and setters uses the object literal syntax.

The following illustrates how getters and setters could work for a user-defined object `o`.
```
var o = {
  a: 7,
  get b() { 
    return this.a + 1;
  },
  set c(x) {
    this.a = x / 2;
  }
};

console.log(o.a); // 7
console.log(o.b); // 8
o.c = 50;
console.log(o.a); // 25
```
### Deleting properties
You can remove a non-inherited property by using the delete operator. The following code shows how to remove a property.
```
var myobj = new Object;
myobj.a = 5;
myobj.b = 12;

delete myobj.a;
console.log ('a' in myobj); // yields "false"
```
### Comparing Objects
In JavaScript, objects are a reference type. Two distinct objects are never equal, even if they have the same properties. Only comparing the same object reference with itself yields true.
```
// Two variables, two distinct objects with the same properties
var fruit = {name: 'apple'};
var fruitbear = {name: 'apple'};

fruit == fruitbear; // return false
fruit === fruitbear; // return false
```
```
// Two variables, a single object
var fruit = {name: 'apple'};
var fruitbear = fruit;  // assign fruit object reference to fruitbear

// here fruit and fruitbear are pointing to same object
fruit == fruitbear; // return true
fruit === fruitbear; // return true
```
### Inheritance
When it comes to inheritance, JavaScript only has one construct: objects. Each object has a private property which holds a link to another object called its **prototype**. That prototype object has a prototype of its own, and so on until an object is reached with `null` as its prototype. By definition, `null` has no prototype, and acts as the final link in this prototype chain.

Nearly all objects in JavaScript are instances of Object which sits on the top of a prototype chain.
```
// Let's create an object o from function f with its own properties a and b:
let f = function () {
   this.a = 1;
   this.b = 2;
}
let o = new f(); // {a: 1, b: 2}

// add properties in f function's prototype
f.prototype.b = 3;
f.prototype.c = 4;

console.log(o.a); // 1
// Is there an 'a' own property on o? Yes, and its value is 1.

console.log(o.b); // 2
// Is there a 'b' own property on o? Yes, and its value is 2.
// The prototype also has a 'b' property, but it's not visited. 
// This is called Property Shadowing

console.log(o.c); // 4
// Is there a 'c' own property on o? No, check its prototype.
// Is there a 'c' own property on o.[[Prototype]]? Yes, its value is 4.

console.log(o.d); // undefined
// Is there a 'd' own property on o? No, check its prototype.
// Is there a 'd' own property on o.[[Prototype]]? No, check its prototype.
// o.[[Prototype]].[[Prototype]] is Object.prototype and there is no 'd' property by default, check its prototype.
// o.[[Prototype]].[[Prototype]].[[Prototype]] is null, stop searching,
// no property found, return undefined.
```
### Using `Object.defineProperty()`
This method allows a precise addition to or modification of a property on an object. Normal property addition through assignment creates properties which show up during property enumeration (`for...in` loop or `Object.keys` method), whose values may be changed, and which may be deleted. This method allows these extra details to be changed from their defaults. By default, values added using `Object.defineProperty()` are immutable.

1. **configurable**: The configurable attribute controls at the same time whether the property can be deleted from the object and whether its attributes (other than `value` and `writable`) can be changed.
2. **enumerable**: The enumerable property attribute defines whether the property is picked by `Object.assign()` or spread operator. For non-Symbols properties it also defines whether it shows up in a `for...in` loop and `Object.keys()` or not.
3. **value**: The value associated with the property. Can be any valid JavaScript value (number, object, function, etc).
4. **writable**: true if and only if the value associated with the property may be changed with an assignment operator.
### Using `hasOwnProperty`
Every object descended from `Object` inherits the `hasOwnProperty` method. This method can be used to determine whether an object has the specified property as a direct property of that object; unlike the in operator, this method does not check down the object's prototype chain.
```
o = new Object();
o.propOne = null;
o.hasOwnProperty('propOne');   // returns true
o.propTwo = undefined;  
o.hasOwnProperty('propTwo');   // returns true
```
### Using `getOwnPropertyNames`
The Object.getOwnPropertyNames() method returns an array of all properties (including non-enumerable properties except for those which use Symbol) found directly upon a given object.
```
const object1 = {
  a: 1,
  b: 2,
  c: 3
};

console.log(Object.getOwnPropertyNames(object1));
// expected output: Array ["a", "b", "c"]
```
### Using `Object.keys`
The `Object.keys()` method returns an array of a given object's own property names, in the same order as we get with a normal loop.
```
const object1 = {
  a: 'somestring',
  b: 42,
  c: false
};

console.log(Object.keys(object1));
// expected output: Array ["a", "b", "c"]
```
### Using `Object.entries()`
`Object.entries()` returns an array whose elements are arrays corresponding to the enumerable property `[key, value]` pairs found directly upon object. The ordering of the properties is the same as that given by looping over the property values of the object manually.
```
const obj = { foo: 'bar', baz: 42 };
console.log(Object.entries(obj)); // [ ['foo', 'bar'], ['baz', 42] ]
```
### Using `Object.seal()`
The `Object.seal()` method seals an object, preventing new properties from being added to it and marking all existing properties as non-configurable. Values of present properties can still be changed as long as they are writable.

```
const object1 = {
  property1: 42
};

Object.seal(object1);
object1.property1 = 33;
console.log(object1.property1);
// expected output: 33

delete object1.property1; // cannot delete when sealed
console.log(object1.property1);
// expected output: 33
```
By default, objects are extensible (new properties can be added to them). Sealing an object prevents new properties from being added and marks all existing properties as non-configurable. This has the effect of making the set of properties on the object fixed and immutable. Making all properties non-configurable also prevents them from being converted from data properties to accessor properties and vice versa, but it does not prevent the values of data properties from being changed. 

Attempting to delete or add properties to a sealed object, or to convert a data property to accessor or vice versa, will fail, either silently or by throwing a TypeError (most commonly, although not exclusively, when in strict mode code).

The `Object.isSealed()` method determines if an object is sealed.

### Using `Object.freeze()`
The `Object.freeze()` method freezes an object. A frozen object can no longer be changed; freezing an object prevents new properties from being added to it, existing properties from being removed, prevents changing the enumerability, configurability, or writability of existing properties, and prevents the values of existing properties from being changed. In addition, freezing an object also prevents its prototype from being changed. `freeze()` returns the same object that was passed in.
```
const object1 = {
  property1: 42
};

const object2 = Object.freeze(object1);

object2.property1 = 33;
// Throws an error in strict mode

console.log(object2.property1);
// expected output: 42
```
The `Object.isFrozen()` determines if an object is frozen.
## ES6
The ES6 is the sixth edition of the language and was released on June 2015. This edition includes many new features like class, modules, iterators, for/of loop, arrow functions, typed arrays, promises and reflection.
### Arrow functions
An arrow function expression has a shorter syntax than a function expression and does not have its own `this`, `arguments`, `super`, or `new.target`. These function expressions are best suited for non-method functions, and they cannot be used as constructors.
```
var materials = [
  'Hydrogen',
  'Helium',
  'Lithium',
  'Beryllium'
];

console.log(materials.map(material => material.length));
// expected output: Array [8, 6, 7, 9]
```
Until arrow functions, every new function defined its own this value (based on how function was called, a new object in the case of a constructor, `undefined` in strict mode function calls, the base object if the function is called as an "object method", etc.). This proved to be less than ideal with an object-oriented style of programming.
```
function Person() {
  // The Person() constructor defines `this` as an instance of itself.
  this.age = 0;

  setInterval(function growUp() {
    // In non-strict mode, the growUp() function defines `this` 
    // as the global object (because it's where growUp() is executed.), 
    // which is different from the `this`
    // defined by the Person() constructor. 
    this.age++;
  }, 1000);
}

var p = new Person();
```
Arrow functions cannot be used as constructors and will throw an error when used with `new`.
```
var Foo = () => {};
var foo = new Foo(); // TypeError: Foo is not a constructor
```
### Default function parameters 
Default function parameters allow named parameters to be initialized with default values if no value or undefined is passed.
```
function multiply(a, b = 1) {
  return a * b;
}

console.log(multiply(5, 2));
// expected output: 10

console.log(multiply(5));
// expected output: 5
```
Parameters defined beforehand (to the left) are available to later default parameters:
```
function greet(name, greeting, message = greeting + ' ' + name) {
    return [name, greeting, message];
}

greet('David', 'Hi');  // ["David", "Hi", "Hi David"]
greet('David', 'Hi', 'Happy Birthday!');  // ["David", "Hi", "Happy Birthday!"]
```
### Shorted method definitions
Starting with ES6, a shorter syntax for method definitions on objects initializers is introduced. It is a shorthand for a function assigned to the method's name.
```
var obj = {
  foo() {
    return 'bar';
  }
}

console.log(obj.foo());
// expected output: "bar"
```
### Spread (`...`) parameter
The rest parameter syntax allows us to represent an indefinite number of arguments as an array.
```
function sum(...theArgs) {
  return theArgs.reduce((previous, current) => {
    return previous + current;
  });
}

console.log(sum(1, 2, 3));
// expected output: 6

console.log(sum(1, 2, 3, 4));
// expected output: 10
```
The destructuring assignment syntax is a JavaScript expression that makes it possible to unpack values from arrays, or properties from objects, into distinct variables.
```
[a, b] = [10, 20];

console.log(a);
// expected output: 10

console.log(b);
// expected output: 20

[a, b, ...rest] = [10, 20, 30, 40, 50];

console.log(rest);
// expected output: [30,40,50]
```

### Proxy Object
The Proxy object is used to define custom behavior for fundamental operations (e.g. property lookup, assignment, enumeration, function invocation, etc). The syntax is:
```
var p = new Proxy(target, handler);
```
A target object to wrap with Proxy. It can be any sort of object, including a native array, a function or even another proxy. `handler` is an object whose properties are functions which define the behavior of the proxy when an operation is performed on it.

In the below example the number 37 gets returned as the default value when the property name is not in the object. It is using the get handler.

```
var handler = {
    get: function(obj, prop) {
        return prop in obj ?
            obj[prop] :
            37;
    }
};

var p = new Proxy({}, handler);
p.a = 1;
p.b = undefined;

console.log(p.a, p.b); // 1, undefined
console.log('c' in p, p.c); // false, 37
```
### Reflect
Reflect is a built-in object that provides methods for interceptable JavaScript operations. The methods are the same as those of proxy handlers. Reflect is not a function object, so it's not constructible.

For example, the static `Reflect.apply()` method calls a target function with arguments as specified
```
console.log(Reflect.apply(Math.floor, undefined, [1.75]));
// expected output: 1

console.log(Reflect.apply(String.fromCharCode, undefined, [104, 101, 108, 108, 111]));
// expected output: "hello"

console.log(Reflect.apply(RegExp.prototype.exec, /ab/, ['confabulation']).index);
// expected output: 4

console.log(Reflect.apply(''.charAt, 'ponies', [3]));
// expected output: "i"
```
Another static method, `Reflect.construct()` method acts like the `new` operator, but as a function. It is equivalent to calling `new target(...args)`. It gives also the added option to specify a different prototype.
```
function func1(a, b, c) {
  this.sum = a + b + c;
}

const args = [1, 2, 3];
const object1 = new func1(...args);
const object2 = Reflect.construct(func1, args);

console.log(object2.sum);
// expected output: 6

console.log(object1.sum);
// expected output: 6
```
## Class
JavaScript classes, introduced in ES6, are primarily syntactical sugar over JavaScript's existing prototype-based inheritance. The class syntax does not introduce a new object-oriented inheritance model to JavaScript.
### Class Declarations
To declare a class, you use the class keyword with the name of the class
```
class Rectangle {
  constructor(height, width) {
    this.height = height;
    this.width = width;
  }
}
```
An important difference between function declarations and class declarations is that function declarations are hoisted and class declarations are not. You first need to declare your class and then access it, otherwise code like the following will throw a `ReferenceError`:
```
const p = new Rectangle(); // ReferenceError
class Rectangle {}
```
We can declare methods and `getter` as follow:
```
class Rectangle {
  constructor(height, width) {
    this.height = height;
    this.width = width;
  }
  // Getter
  get area() {
    return this.calcArea();
  }
  // Method
  calcArea() {
    return this.height * this.width;
  }
}

const square = new Rectangle(10, 10);

console.log(square.area); // 100
```
### Constructors
The `constructor` method is a special method for creating and initializing an object created within a `class`. There can be only one special method with the name "constructor" in a class. Having more than one occurrence of a constructor method in a class will throw a SyntaxError error.
```
class Polygon {
  constructor() {
    this.name = "Polygon";
  }
}

var poly1 = new Polygon();

console.log(poly1.name);
// expected output: "Polygon"
```
A constructor can use the super keyword to call the constructor of a parent class. If you do not specify a constructor method, a default constructor is used.
### Static methods
The `static` keyword defines a static method for a class. Static methods are called without instantiating their class and cannot be called through a class instance. Static methods are often used to create utility functions for an application.
```
class Point {
  constructor(x, y) {
    this.x = x;
    this.y = y;
  }

  static distance(a, b) {
    const dx = a.x - b.x;
    const dy = a.y - b.y;

    return Math.hypot(dx, dy);
  }
}

const p1 = new Point(5, 5);
const p2 = new Point(10, 10);

console.log(Point.distance(p1, p2)); // 7.0710678118654755
```
### Sub classing with extends
The `extends` keyword is used in class declarations or class expressions to create a class as a child of another class.
```
class Animal { 
  constructor(name) {
    this.name = name;
  }
  
  speak() {
    console.log(this.name + ' makes a noise.');
  }
}

class Dog extends Animal {
  constructor(name) {
    super(name); // call the super class constructor and pass in the name parameter
  }

  speak() {
    console.log(this.name + ' barks.');
  }
}

let d = new Dog('Mitzie');
d.speak(); // Mitzie barks.
```
If there is a constructor present in the subclass, it needs to first call `super()` before using "this".
### Default constructors for classes 
If you don’t specify a constructor for a base class, the following definition is used:
```
constructor() {}
```
For derived classes, the following default constructor is used:
```
constructor(...args) {
    super(...args);
}
```
### Referring to superproperties in methods 
The following ES6 code makes a supermethod call in line B.
```
class Person {
    constructor(name) {
        this.name = name;
    }
    toString() { // (A)
        return `Person named ${this.name}`;
    }
}

class Employee extends Person {
    constructor(name, title) {
        super(name);
        this.title = title;
    }
    toString() {
        return `${super.toString()} (${this.title})`; // (B)
    }
}

const jane = new Employee('Jane', 'CTO');
console.log(jane.toString()); // Person named Jane (CTO)
```
### Instantiate a class, given an Array of arguments
What is the analog of `Function.prototype.apply()` for classes? That is, if I have a class `TheClass` and an Array `args` of arguments, how do I instantiate `TheClass`?

One way of doing so is via the spread operator (`..`):
```
function instantiate(TheClass, args) {
    return new TheClass(...args);
}
```
Another option is to use Reflect.construct():
```
function instantiate(TheClass, args) {
    return Reflect.construct(TheClass, args);
}
```

## Promises
The Promise object represents the eventual completion (or failure) of an asynchronous operation, and its resulting value.

A **Promise** is a proxy for a value not necessarily known when the promise is created. It allows you to associate handlers with an asynchronous action's eventual success value or failure reason. This lets asynchronous methods return values like synchronous methods: instead of immediately returning the final value, the asynchronous method returns a promise to supply the value at some point in the future.

A Promise is in one of these states:
* pending: initial state, neither fulfilled nor rejected.
* fulfilled: meaning that the operation completed successfully.
* rejected: meaning that the operation failed.

### Creating Promise using Constructor
A Promise object is created using the `new` keyword and its constructor.  This constructor takes as its argument a function, called the "executor function". This function should take two functions as parameters. The first of these functions (resolve) is called when the asynchronous task completes successfully and returns the results of the task as a value. The second (reject) is called when the task fails, and returns the reason for failure, which is typically an error object.
```
var promise1 = new Promise(function(resolve, reject) {
  setTimeout(function() {
    resolve('foo');
  }, 300);
});

promise1.then(function(value) {
  console.log(value);
  // expected output: "foo"
});

console.log(promise1);
// expected output: [object Promise]
```
### Promising HTTP requests:
The following is a Promise-based function that performs an HTTP GET via the event-based `XMLHttpRequest` API:
```
function httpGetRequest(url) {
    return new Promise(
        function (resolve, reject) {
            const request = new XMLHttpRequest();
            request.onload = function () {
                if (this.status === 200) {
                    // Success
                    resolve(this.response);
                } else {
                    // Something went wrong (404 etc.)
                    reject(new Error(this.statusText));
                }
            };
            request.onerror = function () {
                reject(new Error(
                    'XMLHttpRequest Error: '+this.statusText));
            };
            request.open('GET', url);
            request.send();
        });
}
```
To use this:
```
httpGetRequest('http://example.com/file.txt')
.then(
    function (value) {
        console.log('Contents: ' + value);
    },
    function (reason) {
        console.error('Something went wrong', reason);
    });
```
### Chaining Promises
A promise can be chained by returning another promise. The flat version looks like this:
```
asyncFunc1()
.then(function (value1) {
    return asyncFunc2();
})
.then(function (value2) {
    ···
})
```
### Chaining and errors 
There can be one or more `then()` method calls that don’t have error handlers. Then the error is passed on until there is an error handler.
```
asyncFunc1()
.then(asyncFunc2)
.then(asyncFunc3)
.catch(function (reason) {
    // Something went wrong above
});
```
### Forking and joining via `Promise.all()`
`Promise.all(iterable)` takes an iterable promises and returns a single Promise that resolves when all of the promises in the iterable argument have resolved or when the iterable argument contains no promises. It rejects with the reason of the first promise that rejects.
```
var promise1 = Promise.resolve(3);
var promise2 = 42;
var promise3 = new Promise(function(resolve, reject) {
  setTimeout(resolve, 100, 'foo');
});

Promise.all([promise1, promise2, promise3]).then(function(values) {
  console.log(values);
});
// expected output: Array [3, 42, "foo"]
```
### Timing out via `Promise.race()`
The `Promise.race(iterable)` method returns a promise that resolves or rejects as soon as one of the promises in the iterable resolves or rejects, with the value or reason from that promise.
```
var promise1 = new Promise(function(resolve, reject) {
    setTimeout(resolve, 500, 'one');
});

var promise2 = new Promise(function(resolve, reject) {
    setTimeout(resolve, 100, 'two');
});

Promise.race([promise1, promise2]).then(function(value) {
  console.log(value);
  // Both resolve, but promise2 is faster
});
// expected output: "two"
```
### Finally `finally`
Sometimes you want to perform an action independently of whether an error happened or not. For example, to clean up after you are done with a resource. That’s what the Promise method `finally()` is for, which works much like the finally clause in exception handling. Its callback receives no arguments, but is notified of either a resolution or a rejection.
```
createResource(···)
.then(function (value1) {
    // Use resource
})
.then(function (value2) {
    // Use resource
})
.finally(function () {
    // Clean up
});
```
