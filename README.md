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
10. [Maps and Sets](#maps-and-sets)
11. [Iterables and iterators](#iterables-and-iterators)
12. [Generators](#generators)
13. [Data Structures](#data-structures)
14. [Sorting Algorithms](#sorting-algorithms)
15. [Coding Problems](#coding-problems)

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
### Template Literals
Template literals are string literals allowing embedded expressions. You can use multi-line strings and string interpolation features with them. They were called "template strings" in prior editions of the ES6 specification.

Template literals are enclosed by the back-tick (` `)  (grave accent) character instead of double or single quotes. Template literals can contain placeholders. These are indicated by the dollar sign and curly braces (`${expression}`). The expressions in the placeholders and the text between them get passed to a function. The default function just concatenates the parts into a single string. 
```
const firstName = 'Jane';
console.log(`Hello ${firstName}!
How are you
today?`);

// Output:
// Hello Jane!
// How are you
// today?
```
### Tagged templates
A more advanced form of template literals are tagged templates. Tags allow you to parse template literals with a function. The first argument of a tag function contains an array of string values. The remaining arguments are related to the expressions. In the end, your function can return your manipulated string (or it can return something completely different as described in the next example). The name of the function used for the tag can be whatever you want.
```
var person = 'Mike';
var age = 28;

function myTag(strings, personExp, ageExp) {
  var str0 = strings[0]; // "That "
  var str1 = strings[1]; // " is a "

  // There is technically a string after
  // the final expression (in our example),
  // but it is empty (""), so disregard.
  // var str2 = strings[2];

  var ageStr;
  if (ageExp > 99){
    ageStr = 'centenarian';
  } else {
    ageStr = 'youngster';
  }

  // We can even return a string built using a template literal
  return `${str0}${personExp}${str1}${ageStr}`;
}

var output = myTag`That ${ person } is a ${ age }`;

console.log(output);
// That Mike is a youngster
```
### `for-of` loop
`for-of` is a new loop in ES6 that replaces both `for-in` and `forEach()` and supports the new iteration protocol.
Use it to loop over iterable objects (Arrays, strings, Maps, Sets, etc.):
```
const iterable = ['a', 'b'];
for (const x of iterable) {
    console.log(x);
}

// Output:
// a
// b
```
### Symbols
ECMAScript 6 introduces a new primitive type: symbols. They are tokens that serve as unique IDs. You create symbols via the factory function `Symbol()`.
```
const symbol1 = Symbol();
```
`Symbol()` has an optional string-valued parameter that lets you give the newly created Symbol a description. That description is used when the symbol is converted to a string (via `toString()` or `String()`):
```
const symbol2 = Symbol('symbol2');
String(symbol2)
'Symbol(symbol2)'
```
Symbols are mainly used as unique property keys – a symbol never clashes with any other property key (symbol or string). For example, you can make an object iterable (usable via the `for-of` loop and other language mechanisms), by using the symbol stored in `Symbol.iterator` as the key of a method.
### `async` and `await` function
The `async` function declaration defines an asynchronous function, which returns an `AsyncFunction` object. An asynchronous function is a function which operates asynchronously via the event loop, using an implicit Promise to return its result. 

An `async` function can contain an `await` expression that pauses the execution of the async function and waits for the passed Promise's resolution, and then resumes the async function's execution and returns the resolved value.

Remember, the `await` keyword is only valid inside async functions. If you use it outside of an `async` function's body, you will get a SyntaxError.
```
function resolveAfter2Seconds() {
  return new Promise(resolve => {
    setTimeout(() => {
      resolve('resolved');
    }, 2000);
  });
}

async function asyncCall() {
  console.log('calling');
  var result = await resolveAfter2Seconds();
  console.log(result);
  // expected output: 'resolved'
}

asyncCall();
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
## Maps and Sets
Among others, the following four data structures are new in ECMAScript 6: `Map`, `WeakMap`, `Set` and `WeakSet`.

### Map
The `Map` object holds key-value pairs and remembers the original insertion order of the keys. Any value (both objects and primitive values) may be used as either a key or a value.
```
var myMap = new Map();

var keyString = 'a string',
    keyObj = {},
    keyFunc = function() {};

// setting the values
myMap.set(keyString, "value associated with 'a string'");
myMap.set(keyObj, 'value associated with keyObj');
myMap.set(keyFunc, 'value associated with keyFunc');

myMap.size; // 3

// getting the values
myMap.get(keyString);    // "value associated with 'a string'"
myMap.get(keyObj);       // "value associated with keyObj"
myMap.get(keyFunc);      // "value associated with keyFunc"

myMap.get('a string');   // "value associated with 'a string'"
                         // because keyString === 'a string'
myMap.get({});           // undefined, because keyObj !== {}
myMap.get(function() {}) // undefined, because keyFunc !== function () {}
```
Objects and Maps are similar in many ways. However, there are important differences that make using a Map preferable in certain cases:
* The keys of an Object are Strings and Symbols, whereas they can be any value for a Map, including functions, objects, and any primitive.
* The keys in Map are ordered while keys added to object are not. 
* You can get the size of a Map easily with the size property, while the number of properties in an Object must be determined manually.
* An Object has a prototype, so there are default keys in the map that could collide with your keys if you're not careful.

### WeakMap 
`WeakMap` holds "weak" references to key objects, which means that they do not prevent garbage collection in case there would be no other reference to the key object.  This also avoids preventing garbage collection of values in the map.  Native WeakMaps can be particularly useful constructs when mapping keys to information about the key that is valuable only if the key has not been garbage collected.

Because of references being weak, WeakMap keys are not enumerable (i.e. there is no method giving you a list of the keys). If they were, the list would depend on the state of garbage collection, introducing non-determinism. If you want to have a list of keys, you should use a Map.
```
var wm1 = new WeakMap();
var o1 = {};
wm1.set(o1, 37);
```
### Set
Set objects are collections of values. Also the `Set` object lets you store unique values of any type, whether primitive values or object references.
```
var mySet = new Set();

mySet.add(1); // Set [ 1 ]
mySet.add(5); // Set [ 1, 5 ]
mySet.add(5); // Set [ 1, 5 ]
mySet.add('some text'); // Set [ 1, 5, 'some text' ]
var o = {a: 1, b: 2};
mySet.add(o);

mySet.size; // 4

mySet.delete(5); // removes 5 from the set
mySet.has(5);    // false, 5 has been removed
```
### WeakSet
A `WeakSet` is a Set that doesn’t prevent its elements from being garbage-collected. Consult the section on WeakMap for an explanation of why WeakSets don’t allow iteration, looping and clearing.
```
var ws = new WeakSet();
var foo = {};
var bar = {};

ws.add(foo);
ws.add(bar);

ws.has(foo);    // true
ws.has(bar);    // true
```

## Iterables and iterators
ES6 introduces a new mechanism for traversing data: iteration. Two concepts are central to iteration:
* An iterable is a data structure that wants to make its elements accessible to the public. It does so by implementing a method whose key is Symbol.iterator. That method is a factory for iterators.
* An iterator is a pointer for traversing the elements of a data structure (think cursors in databases).

`String`, `Array`, `TypedArray`, `Map` and `Set` are all built-in iterables, because each of their prototype objects implements an `@@iterator` method.

### User-defined iterables
We can make our own iterables like this:
```
var myIterable = {};
myIterable[Symbol.iterator] = function* () {
    yield 1;
    yield 2;
    yield 3;
};
[...myIterable]; // [1, 2, 3]
```
### Simple Iterator for an Array
```
function makeIterator(array) {
    var nextIndex = 0;
    
    return {
       next: function() {
           return nextIndex < array.length ?
               {value: array[nextIndex++], done: false} :
               {done: true};
       }
    };
}

var it = makeIterator(['yo', 'ya']);

console.log(it.next().value); // 'yo'
console.log(it.next().value); // 'ya'
console.log(it.next().done);  // true
```

## Generators
Generators are functions that can be paused and resumed (think cooperative multitasking or coroutines), which enables a variety of applications.
### A simple generator
The `function*` declaration (function keyword followed by an asterisk) defines a generator function, which returns a Generator object.
```
function* idMaker() {
    var index = 0;
    while(true)
        yield index++;
}

var gen = idMaker(); // "Generator { }"

console.log(gen.next().value); // 0
console.log(gen.next().value); // 1
console.log(gen.next().value); // 2
// ...
```
The objects returned by generators are iterable; each `yield` contributes to the sequence of iterated values. Therefore, you can use generators to implement iterables, which can be consumed by various ES6 language mechanisms: `for-of` loop, spread operator (`...`), etc.
### Kinds of Generators
There are four kinds of generators:
1. Generator function declarations:
```
function* genFunc() { ··· }
const genObj = genFunc();
```
2. Generator function expressions:
```
const genFunc = function* () { ··· };
const genObj = genFunc();
```
3. Generator method definitions in object literals:
```
const obj = {
     * generatorMethod() {
         ···
     }
 };
```
4. Generator method definitions in class definitions (class declarations or class expressions):
```
class MyClass {
     * generatorMethod() {
         ···
     }
 }
```
### Terminate a Generator
`return()` performs a return at the location of the `yield` that led to the last suspension of the generator. 
```
function* genFunc1() {
    try {
        console.log('Started');
        yield; // (A)
    } finally {
        console.log('Exiting');
    }
}
```
In the following interaction, we first use `next()` to start the generator and to proceed until the yield in line A. Then we return from that location via `return()`.
```
const genObj1 = genFunc1();

genObj1.next()
Started
{ value: undefined, done: false }

genObj1.return('Result')
Exiting
{ value: 'Result', done: true }
```
## Data Structures
In computer science, a data structure is a particular way of organizing data in a computer so that it can be used efficiently. 
### Hash Table
A Hash Table (Hash Map) is a data structure used to implement an associative array, a structure that can map keys to values. A Hash Table uses a hash function to compute an index into an array of buckets or slots, from which the desired value can be found.
```
function HashTable(size) {
  this.values = {};
  this.numberOfValues = 0;
  this.size = size;
}

HashTable.prototype.add = function(key, value) {
  var hash = this.calculateHash(key);
  if(!this.values.hasOwnProperty(hash)) {
    this.values[hash] = {};
  }
  if(!this.values[hash].hasOwnProperty(key)) {
    this.numberOfValues++;
  }
  this.values[hash][key] = value;
};

HashTable.prototype.remove = function(key) {
  var hash = this.calculateHash(key);
  if(this.values.hasOwnProperty(hash) && this.values[hash].hasOwnProperty(key)) {
    delete this.values[hash][key];
    this.numberOfValues--;
  }
};

HashTable.prototype.calculateHash = function(key) {
  return key.toString().length % this.size;
};

HashTable.prototype.search = function(key) {
  var hash = this.calculateHash(key);
  if(this.values.hasOwnProperty(hash) && this.values[hash].hasOwnProperty(key)) {
    return this.values[hash][key];
  } else {
    return null;
  }
};

HashTable.prototype.length = function() {
  return this.numberOfValues;
};

HashTable.prototype.print = function() {
  var string = '';
  for(var value in this.values) {
    for(var key in this.values[value]) {
      string += this.values[value][key] + ' ';
    }
  }
  console.log(string.trim());
};

var hashTable = new HashTable(3);
hashTable.add('first', 1);
hashTable.add('second', 2);
hashTable.add('third', 3);
hashTable.add('fourth', 4);
hashTable.add('fifth', 5);
hashTable.print(); // => 2 4 1 3 5
console.log('length gives 5:', hashTable.length()); // => 5
console.log('search second gives 2:', hashTable.search('second')); // => 2
hashTable.remove('fourth');
hashTable.remove('first');
hashTable.print(); // => 2 3 5
console.log('length gives 3:', hashTable.length()); // => 3
```
### Set
A Set is an abstract data type that can store certain values, without any particular order, and no repeated values. It is a computer implementation of the mathematical concept of a finite Set. 
```
function Set() {
  this.values = [];
  this.numberOfValues = 0;
}

Set.prototype.add = function(value) {
  if(!~this.values.indexOf(value)) {
    this.values.push(value);
    this.numberOfValues++;
  }
};

Set.prototype.remove = function(value) {
  var index = this.values.indexOf(value);
  if(~index) {
    this.values.splice(index, 1);
    this.numberOfValues--;
  }
};

Set.prototype.contains = function(value) {
  return this.values.indexOf(value) !== -1;
};

Set.prototype.union = function(set) {
  var newSet = new Set();
  set.values.forEach(function(value) {
    newSet.add(value);
  });
  this.values.forEach(function(value) {
    newSet.add(value);
  });
  return newSet;
};

Set.prototype.intersect = function(set) {
  var newSet = new Set();
  this.values.forEach(function(value) {
    if(set.contains(value)) {
      newSet.add(value);
    }
  });
  return newSet;
};

Set.prototype.difference = function(set) {
  var newSet = new Set();
  this.values.forEach(function(value) {
    if(!set.contains(value)) {
      newSet.add(value);
    }
  });
  return newSet;
};

Set.prototype.isSubset = function(set) {
  return set.values.every(function(value) {
    return this.contains(value);
  }, this);
};

Set.prototype.length = function() {
  return this.numberOfValues;
};

Set.prototype.print = function() {
  console.log(this.values.join(' '));
};

var set = new Set();
set.add(1);
set.add(2);
set.add(3);
set.add(4);
set.print(); // => 1 2 3 4
set.remove(3);
set.print(); // => 1 2 4
console.log('contains 4 is true:', set.contains(4)); // => true
console.log('contains 3 is false:', set.contains(3)); // => false
```
### Singly Linked List
A Singly Linked List is a linear collection of data elements, called nodes pointing to the next node by means of pointer. It is a data structure consisting of a group of nodes which together represent a sequence. Under the simplest form, each node is composed of data and a reference (in other words, a link) to the next node in the sequence.
```
function Node(data) {
  this.data = data;
  this.next = null;
}

function SinglyLinkedList() {
  this.head = null;
  this.tail = null;
  this.numberOfValues = 0;
}

SinglyLinkedList.prototype.add = function(data) {
  var node = new Node(data);
  if(!this.head) {
    this.head = node;
    this.tail = node;
  } else {
    this.tail.next = node;
    this.tail = node;
  }
  this.numberOfValues++;
};

SinglyLinkedList.prototype.remove = function(data) {
  var previous = this.head;
  var current = this.head;
  while(current) {
    if(current.data === data) {
      if(current === this.head) {
        this.head = this.head.next;
      }
      if(current === this.tail) {
        this.tail = previous;
      }
      previous.next = current.next;
      this.numberOfValues--;
    } else {
      previous = current;
    }
    current = current.next;
  }
};

SinglyLinkedList.prototype.insertAfter = function(data, toNodeData) {
  var current = this.head;
  while(current) {
    if(current.data === toNodeData) {
      var node = new Node(data);
      if(current === this.tail) {
        this.tail.next = node;
        this.tail = node;
      } else {
        node.next = current.next;
        current.next = node;
      }
      this.numberOfValues++;
    }
    current = current.next;
  }
};

SinglyLinkedList.prototype.traverse = function(fn) {
  var current = this.head;
  while(current) {
    if(fn) {
      fn(current);
    }
    current = current.next;
  }
};

SinglyLinkedList.prototype.length = function() {
  return this.numberOfValues;
};

SinglyLinkedList.prototype.print = function() {
  var string = '';
  var current = this.head;
  while(current) {
    string += current.data + ' ';
    current = current.next;
  }
  console.log(string.trim());
};

var singlyLinkedList = new SinglyLinkedList();
singlyLinkedList.print(); // => ''
singlyLinkedList.add(1);
singlyLinkedList.add(2);
singlyLinkedList.add(3);
singlyLinkedList.add(4);
singlyLinkedList.print(); // => 1 2 3 4
console.log('length is 4:', singlyLinkedList.length()); // => 4
```
### Doubly Linked List
A Doubly Linked List is a linked data structure that consists of a set of sequentially linked records called nodes. Each node contains two fields, called links, that are references to the previous and to the next node in the sequence of nodes.
```
function Node(data) {
  this.data = data;
  this.previous = null;
  this.next = null;
}

function DoublyLinkedList() {
  this.head = null;
  this.tail = null;
  this.numberOfValues = 0;
}

DoublyLinkedList.prototype.add = function (data) {
  var node = new Node(data);
  if(!this.head) {
    this.head = node;
    this.tail = node;
  } else {
    node.previous = this.tail;
    this.tail.next = node;
    this.tail = node;
  }
  this.numberOfValues++;
};

DoublyLinkedList.prototype.remove = function(data) {
  var current = this.head;
  while(current) {
    if(current.data === data) {
      if(current === this.head && current === this.tail) {
        this.head = null;
        this.tail = null;
      } else if(current === this.head) {
        this.head = this.head.next;
        this.head.previous = null;
      } else if(current === this.tail) {
        this.tail = this.tail.previous;
        this.tail.next = null;
      } else {
        current.previous.next = current.next;
        current.next.previous = current.previous;
      }
      this.numberOfValues--;
    }
    current = current.next;
  }
};

DoublyLinkedList.prototype.insertAfter = function(data, toNodeData) {
  var current = this.head;
  while(current) {
    if(current.data === toNodeData) {
      var node = new Node(data);
      if(current === this.tail) {
        this.add(data);
      } else {
        current.next.previous = node;
        node.previous = current;
        node.next = current.next;
        current.next = node;
        this.numberOfValues++;
      }
    }
    current = current.next;
  }
};

DoublyLinkedList.prototype.traverse = function(fn) {
  var current = this.head;
  while(current) {
    if(fn) {
      fn(current);
    }
    current = current.next;
  }
};

DoublyLinkedList.prototype.traverseReverse = function(fn) {
  var current = this.tail;
  while(current) {
    if(fn) {
      fn(current);
    }
    current = current.previous;
  }
};

DoublyLinkedList.prototype.length = function() {
  return this.numberOfValues;
};

DoublyLinkedList.prototype.print = function() {
  var string = '';
  var current = this.head;
  while(current) {
    string += current.data + ' ';
    current = current.next;
  }
  console.log(string.trim());
};

var doublyLinkedList = new DoublyLinkedList();
doublyLinkedList.print(); // => ''
doublyLinkedList.add(1);
doublyLinkedList.add(2);
doublyLinkedList.add(3);
doublyLinkedList.add(4);
doublyLinkedList.print(); // => 1 2 3 4
console.log('length is 4:', doublyLinkedList.length()); // => 4
```
### Stack 
A Stack is an abstract data type that serves as a collection of elements, with two principal operations: push, which adds an element to the collection, and pop, which removes the most recently added element that was not yet removed. The order in which elements come off a Stack gives rise to its alternative name, LIFO (for last in, first out).
```
function Stack() {
  this.stack = [];
}

Stack.prototype.push = function(value) {
  this.stack.push(value);
};

Stack.prototype.pop = function() {
  return this.stack.pop();
};

Stack.prototype.peek = function() {
  return this.stack[this.stack.length - 1];
};

Stack.prototype.length = function() {
  return this.stack.length;
};

Stack.prototype.print = function() {
  console.log(this.stack.join(' '));
};

var stack = new Stack();
stack.push(1);
stack.push(2);
stack.push(3);
stack.print(); // => 1 2 3
console.log('length is 3:', stack.length()); // => 3
```
### Queue 
A Queue is a particular kind of abstract data type or collection in which the entities in the collection are kept in order and the principal operations are the addition of entities to the rear terminal position, known as enqueue, and removal of entities from the front terminal position, known as dequeue. This makes the Queue a First-In-First-Out (FIFO) data structure. In a FIFO data structure, the first element added to the Queue will be the first one to be removed. 
```
function Queue() {
  this.queue = [];
}

Queue.prototype.enqueue = function(value) {
  this.queue.push(value);
};

Queue.prototype.dequeue = function() {
  return this.queue.shift();
};

Queue.prototype.peek = function() {
  return this.queue[0];
};

Queue.prototype.length = function() {
  return this.queue.length;
};

Queue.prototype.print = function() {
  console.log(this.queue.join(' '));
};

var queue = new Queue();
queue.enqueue(1);
queue.enqueue(2);
queue.enqueue(3);
queue.print(); // => 1 2 3
console.log('length is 3:', queue.length()); // => 3
```
### Tree
A Tree is a widely used data structure that simulates a hierarchical tree structure, with a root value and subtrees of children with a parent node. A tree data structure can be defined recursively as a collection of nodes (starting at a root node), where each node is a data structure consisting of a value, together with a list of references to nodes (the “children”), with the constraints that no reference is duplicated, and none points to the root node.
```
function Node(data) {
  this.data = data;
  this.children = [];
}

function Tree() {
  this.root = null;
}

Tree.prototype.add = function(data, toNodeData) {
  var node = new Node(data);
  var parent = toNodeData ? this.findBFS(toNodeData) : null;
  if(parent) {
    parent.children.push(node);
  } else {
    if(!this.root) {
      this.root = node;
    } else {
      return 'Root node is already assigned';
    }
  }
};

Tree.prototype.remove = function(data) {
  if(this.root.data === data) {
    this.root = null;
  }

  var queue = [this.root];
  while(queue.length) {
    var node = queue.shift();
    for(var i = 0; i < node.children.length; i++) {
      if(node.children[i].data === data) {
        node.children.splice(i, 1);
      } else {
        queue.push(node.children[i]);
      }
    }
  }
};

Tree.prototype.contains = function(data) {
  return this.findBFS(data) ? true : false;
};

Tree.prototype.findBFS = function(data) {
  var queue = [this.root];
  while(queue.length) {
    var node = queue.shift();
    if(node.data === data) {
      return node;
    }
    for(var i = 0; i < node.children.length; i++) {
      queue.push(node.children[i]);
    }
  }
  return null;
};

Tree.prototype._preOrder = function(node, fn) {
  if(node) {
    if(fn) {
      fn(node);
    }
    for(var i = 0; i < node.children.length; i++) {
      this._preOrder(node.children[i], fn);
    }
  }
};

Tree.prototype._postOrder = function(node, fn) {
  if(node) {
    for(var i = 0; i < node.children.length; i++) {
      this._postOrder(node.children[i], fn);
    }
    if(fn) {
      fn(node);
    }
  }
};

Tree.prototype.traverseDFS = function(fn, method) {
  var current = this.root;
  if(method) {
    this['_' + method](current, fn);
  } else {
    this._preOrder(current, fn);
  }
};

Tree.prototype.traverseBFS = function(fn) {
  var queue = [this.root];
  while(queue.length) {
    var node = queue.shift();
    if(fn) {
      fn(node);
    }
    for(var i = 0; i < node.children.length; i++) {
      queue.push(node.children[i]);
    }
  }
};

Tree.prototype.print = function() {
  if(!this.root) {
    return console.log('No root node found');
  }
  var newline = new Node('|');
  var queue = [this.root, newline];
  var string = '';
  while(queue.length) {
    var node = queue.shift();
    string += node.data.toString() + ' ';
    if(node === newline && queue.length) {
      queue.push(newline);
    }
    for(var i = 0; i < node.children.length; i++) {
      queue.push(node.children[i]);
    }
  }
  console.log(string.slice(0, -2).trim());
};

Tree.prototype.printByLevel = function() {
  if(!this.root) {
    return console.log('No root node found');
  }
  var newline = new Node('\n');
  var queue = [this.root, newline];
  var string = '';
  while(queue.length) {
    var node = queue.shift();
    string += node.data.toString() + (node.data !== '\n' ? ' ' : '');
    if(node === newline && queue.length) {
      queue.push(newline);
    }
    for(var i = 0; i < node.children.length; i++) {
      queue.push(node.children[i]);
    }
  }
  console.log(string.trim());
};

var tree = new Tree();
tree.add('ceo');
tree.add('cto', 'ceo');
tree.add('dev1', 'cto');
tree.add('dev2', 'cto');
tree.add('dev3', 'cto');
tree.add('cfo', 'ceo');
tree.add('accountant', 'cfo');
tree.add('cmo', 'ceo');
tree.print(); // => ceo | cto cfo cmo | dev1 dev2 dev3 accountant
tree.printByLevel();  // => ceo \n cto cfo cmo \n dev1 dev2 dev3 accountant
console.log('tree contains dev1 is true:', tree.contains('dev1')); // => true
console.log('tree contains dev4 is false:', tree.contains('dev4')); // => false
```

## Sorting Algorithms
Sorting is considered to be an important concept in many programming languages as it helps us locate elements in a faster and easier manner.
### Bubble Sort
How it works:
1. you compare the first item with the second. If the first item is bigger than the second item. you swap them so that the bigger one stays in the second position.

2. And then compare second with third item. if second item is bigger than the third, we swap them. otherwise, they stayed in their position. Hence, the biggest among first three is in the third position.

3. We keep doing it. until we hit the last element of the array. In that way we bubble up the biggest item of the array to the right most position of the array.

4. Look at the inner loop in the code below.

5. We repeat this process, starting from the last item of the array. look at the outer loop in the code below. We do this way, so that after finishing the first inner loop, the biggest one will be in the last item of the array.

6. And then we move backward inside the outer loop.
```
function bubbleSort(arr){
   var len = arr.length;
   for (var i = len-1; i >= 0; i--){
     for(var j = 1; j<=i; j++){
       if(arr[j-1] > arr[j]){
           var temp = arr[j-1];
           arr[j-1] = arr[j];
           arr[j] = temp;
        }
     }
   }
   return arr;
}
bubbleSort([7,5,2,4,3,9]); //[2, 3, 4, 5, 7, 9]
bubbleSort([9,7,5,4,3,1]); //[1, 3, 4, 5, 7, 9]
bubbleSort([1,2,3,4,5,6]); //[1, 2, 3, 4, 5, 6]
```
### Selection Sort
This is very simple. Go through the array, find the index of the lowest element swap the lowest element with the first element. Hence first element is the lowest element in the array.

Now go through the rest of the array (excluding the first element) and find the index of the lowest and swap it with the second element.

thats how it continues to select (find out) the lowest element of the array and putting it on the left until it hits the last element.
```
function selectionSort(arr){
  var minIdx, temp, 
      len = arr.length;
  for(var i = 0; i < len; i++){
    minIdx = i;
    for(var  j = i+1; j < len; j++){
       if(arr[j] < arr[minIdx]){
          minIdx = j;
       }
    }
    temp = arr[i];
    arr[i] = arr[minIdx];
    arr[minIdx] = temp;
  }
  return arr;
}
selectionSort([7,5,2,4,3,9]); //[2, 3, 4, 5, 7, 9]
```
### Insertion sort
It starts with the second element. Pick the second element to be inserted and then compare to the previous element. If the first one is bigger, move the first one to second position and second one at first.

Now first and second item is sorted.

Then, pick the third element and check whether the second element is bigger than the third. keep going similar way until you hit the first element or a element smaller than the element you are comparing with. When you get an item smaller than the picked item, you insert it.
```
function insertionSort(arr) {
  var i, len = arr.length, el, j;

  for(i = 1; i < len; i++){
    el = arr[i];
    j = i;

    while(j > 0 && arr[j-1] > toInsert){
      arr[j] = arr[j-1];
      j--;
   }
   arr[j] = el;
  }
  return arr;
}    
```
### Merge Sort
Merge sort has two parts. Main part does divide or breaks down and second part is merging/combining parts. At the time of combining, parts are combined together.

Divide: the first function named as mergeSort is actually a divide function. where an array is divided into two.

merge: this is just merging two sorted array. Just be careful this two array could be in different size

```
function mergeSort(arr){
   var len = arr.length;
   if(len <2)
      return arr;
   var mid = Math.floor(len/2),
       left = arr.slice(0,mid),
       right =arr.slice(mid);
   //send left and right to the mergeSort to broke it down into pieces
   //then merge those
   return merge(mergeSort(left),mergeSort(right));
}

function merge(left, right){
  var result = [],
      lLen = left.length,
      rLen = right.length,
      l = 0,
      r = 0;
  while(l < lLen && r < rLen){
     if(left[l] < right[r]){
       result.push(left[l++]);
     }
     else{
       result.push(right[r++]);
    }
  }  
  //remaining part needs to be addred to the result
  return result.concat(left.slice(l)).concat(right.slice(r));
}
```
### Quick sort
Quicksort is a divide and conquer algorithm. Quicksort first divides a large array into two smaller sub-arrays: the low elements and the high elements. Quicksort can then recursively sort the sub-arrays

The steps are:

1. Pick an element, called a pivot, from the array.
2. Partitioning: reorder the array so that all elements with values less than the pivot come before the pivot, while all elements with values greater than the pivot come after it (equal values can go either way). After this partitioning, the pivot is in its final position. This is called the partition operation.
3. Recursively apply the above steps to the sub-array of elements with smaller values and separately to the sub-array of elements with greater values.
```
function quickSort(arr, left, right){
   var len = arr.length, 
   pivot,
   partitionIndex;


  if(left < right){
    pivot = right;
    partitionIndex = partition(arr, pivot, left, right);
    
   //sort left and right
   quickSort(arr, left, partitionIndex - 1);
   quickSort(arr, partitionIndex + 1, right);
  }
  return arr;
}

function partition(arr, pivot, left, right){
   var pivotValue = arr[pivot],
       partitionIndex = left;

   for(var i = left; i < right; i++){
    if(arr[i] < pivotValue){
      swap(arr, i, partitionIndex);
      partitionIndex++;
    }
  }
  swap(arr, right, partitionIndex);
  return partitionIndex;
}

function swap(arr, i, j){
   var temp = arr[i];
   arr[i] = arr[j];
   arr[j] = temp;
}
```
### Heap sort
Heapsort is a comparison-based sorting algorithm. Heapsort can be thought of as an improved selection sort: like that algorithm, it divides its input into a sorted and an unsorted region, and it iteratively shrinks the unsorted region by extracting the largest element and moving that to the sorted region. The improvement consists of the use of a heap data structure rather than a linear-time search to find the maximum.

```
function heapSort(arr){
  var len = arr.length,
      end = len-1;

  heapify(arr, len);
  
  while(end > 0){
   swap(arr, end--, 0);
   siftDown(arr, 0, end);
  }
  return arr;
}

function heapify(arr, len){
   // break the array into root + two sides, to create tree (heap)
   var mid = Math.floor((len-2)/2);
   while(mid >= 0){
    siftDown(arr, mid--, len-1);    
  }
}
function siftDown(arr, start, end){
   var root = start,
       child = root*2 + 1,
       toSwap = root;
   while(child <= end){
      if(arr[toSwap] < arr[child]){
        swap(arr, toSwap, child);
      }
      if(child+1 <= end && arr[toSwap] < arr[child+1]){
        swap(arr, toSwap, child+1)
      }
      if(toSwap != root){
         swap(arr, root, toSwap);
         root = toSwap;
      }
      else{
         return; 
      }
      toSwap = root;
      child = root*2+1
  }
}
function swap(arr, i, j){
  var temp = arr[i];
  arr[i] = arr[j];
  arr[j] = temp;
}
```
## Coding Problems
### Check prime number
A prime number is only divisible by itself and 1.
```
function isPrime(num) {
  for(var i = 2; i < num; i++)
    if(num % i === 0) return false;
  return num !== 1 && num !== 0;
}

isPrime(137); // true
```
### Check prime number another way
You can decrease complexity of the function if you run the loop until square root of number:
```
function isPrime (num) {
    for(let i = 2, s = Math.sqrt(num); i <= s; i++) {
        if(num % i === 0) return false; 
    }
    return num !== 1 && num !== 0;
}
```
### Find all the prime factors of the given number
```
function findPrimeFactors (num) {

    var primeFactors = [];
    while (num % 2 === 0) {
        primeFactors.push(2);
        num = num / 2;
    }
    
    var sqrtNum = Math.sqrt(num);
    for (var i = 3; i <= sqrtNum; i++) {
        while (num % i === 0) {
            primeFactors.push(i);
            num = num / i;
        }
    }

    if (num > 2) {
        primeFactors.push(num);
    }
    return primeFactors;
}

console.log(findPrimeFactors(10)); // [2, 5]
console.log(findPrimeFactors(11)); // [11]
```
### Find the nth Fibonacci number
```
function fibonacci(n){
  if(n<=1)
    return n;
  else
    return fibonacci(n-1) + fibonacci (n-2);  
}

fibonacci(12); // 144
```
### Find nth index value of fibonacci
Given a number N return the index value of the Fibonacci sequence, where the sequence is:
```
1, 1, 2, 3, 5, 8, 13, 21, 34, 55, 89, 144, ...
```
The solution is:
```
function fib(n){
  var x = 0;
  var y = 1;
  if(n <= 2){
    return n-1;
  }
  for(var i = 0; i < n; i++){
    var tempY = y;
    y = tempY + x;
    x = tempY;
      
  }
  return y;
}

fib(4)
// 5
```
To better performance, we can use memoization.
```
function fib(num, memo) {
  memo = memo || {};

  if (memo[num]) return memo[num];
  if (num <= 1) return 1;

  return memo[num] = fib(num - 1, memo) + fib(num - 2, memo);
}
```
### Find Greatest Common Divisor
Using Euclidean algorithm:
```
function greatestCommonDivisor(a, b){
   if(b == 0) {
     return a;
    }
   else {
     return greatestCommonDivisor(b, a%b);
    }
}

greatestCommonDivisor(14, 21); // 7
```
The Euclidean algorithm is based on the principle that the greatest common divisor of two numbers does not change if the larger number is replaced by its difference with the smaller number. For example, 21 is the GCD of 252 and 105 (as 252 = 21 × 12 and 105 = 21 × 5), and the same number 21 is also the GCD of 105 and 252 − 105 = 147. Since this replacement reduces the larger of the two numbers, repeating this process gives successively smaller pairs of numbers until the two numbers become equal. When that occurs, they are the GCD of the original two numbers.
### Remove duplicate element from an array
Using `Array.filter` method, check each element's index is equal to the `indexOf` value of the array.
```
function removeDuplicate(arr) {
	return arr.filter((element, index, array) => array.indexOf(element) === index);
}
```
Or in ES6 using `set`:
```
function removeDuplicate(arr) {
	return Array.from(new Set(arr))
}
```
### Merge two sorted arrays
Given two arrays of same length, merge them into sing array in ascending order.
```
function mergeSortedArray(arr1, arr2) {
    return [...arr1, ...arr2].sort((a, b) => a - b)
}

mergeSortedArray([2,5,6,9], [1,2,3,29]);
//  [1, 2, 2, 3, 5, 6, 9, 29]
```
### Swap two variables without temp
```
function swapNumb(a, b){
  b = b - a;
  a = a + b;
  b = a - b;
}
```
Using ES6:
```
function swapNumb(a, b) {
  console.log('before swap: ' + a + ' ' + b);
  [a, b] = [b, a];
  console.log('after swap: ' + a + ' ' + b);
}
```
### String reverse
Given a string, print the reverse of the string (ex: javascript becomes tpircsavaj).

Without native methods:
```
function reverse(str){
  var rtnStr = [];
  if(!str || typeof str != 'string' || str.length < 2 ) return str;
  
  for(var i = str.length-1; i>=0;i--){
    rtnStr.push(str[i]);
  }
  return rtnStr.join('');
}
```
Using recursion:
```
function reverse (str) {
    if (str === "") {
        return "";
    } else {
        return reverse(str.substr(1)) + str.charAt(0);
    }
}
```
Using native methods:
```
function reverse(str){
  if(!str || str.length <2) return str;
  
  return str.split('').reverse().join('');
}
```
### Reverse in Place
Given a string, reverse the letters of word but preserve the word order (ex: "I am the good boy" becomes "I ma eht doog yob")
```
function reverseInPlace(str){
  return str.split(' ').reverse().join(' ').split('').reverse().join('');
}
```
### Print the first non repeating character in a string
Given a string, print the first non-repeating letter in the string.
```
function firstNonRepeatedCharacter(string) {
  for (var i = 0; i < string.length; i++) {
    var c = string.charAt(i);
    if (string.indexOf(c) == i && string.indexOf(c, i + 1) == -1) {
      return c;
    }
  }
  return null;
}
```
The `indexOf` method takes one additional optional parameter `start` at which index start the search. So check if the character is found after the current index.
### Check if a given string is palindrom
A string is palindrom if the reverse order is same as original.
```
function checkPalindrom(str) {
    return str == str.split('').reverse().join('');
}
```
### Generate random number between two given
```
function generateRandom(start, end) {
    return Math.floor(Math.random() * end) + start;  
}
```
### Find the missing number
Given a unsorted array of numbers 1 to 100 excluding one number, find the missing number.

The sum of a linear series of n numbers is equal to n*(n+1)/2.

```
function missingNumber(arr){
  var n = arr.length+1, 
  sum = 0,
  expectedSum = n* (n+1)/2;
  
  sum = arr.reduce((total, num) => total + num);
  return expectedSum - sum;
}

missingNumber([5, 2, 6, 1, 3]);
// 4
```
### Find the largest and smallest number in an array
Given an unsorted array, find the largest and smallest number.
```
function findLargestAndSmallest(arr) {
    arr = arr.sort((a, b) => a - b);
    return [arr[0], arr[arr.length - 1]];
}

findLargestAndSmallest([1, 100, 2, -1, 2, 0])
// [-1, 100]
```
Using native mehtods and `apply`:
```
function findLargestAndSmallest(arr) {
    let smallest = Math.min.apply(null, arr);
    let largest = Math.max.apply(null, arr);
	return [smallest, largest];
}
```
### Permutations of a string
Get all permutations of a string
```
function permut(string) {
    if (string.length < 2) return string; // This is our break condition

    var permutations = []; // This array will hold our permutations

    for (var i=0; i<string.length; i++) {
        var char = string[i];

        // Cause we don't want any duplicates:
        if (string.indexOf(char) != i) // if char was used already
            continue;           // skip it this time

        var remainingString = string.slice(0,i) + string.slice(i+1,string.length);

        for (var subPermutation of permut(remainingString))
            permutations.push(char + subPermutation)

    }
    return permutations;
}

let permutations = permut('xyz');
// ["xyz", "xzy", "yxz", "yzx", "zxy", "zyx"]
```
### Implement currying
Curring is partial invocation of a function. Currying means first few arguments of a function is pre-processed and a function is returned. The returning function can add more arguments to the curried function. It's like if you have given one or two spice to the curry and cooked little bit, still you can add further spice to it. A simple example will look like
```
function addBase(base){
  return function(num){
    return base + num;
  }
}

var addTen = addBase(10);
addTen(5); //15
addTen(80); //90
addTen(-5); //5
```
### Check sum of two
From a unsorted array, check whether there are any two numbers that will sum up to a given number.
```
function sumFinder(arr, sum){
  var len = arr.length;
  
  for(var i =0; i<len-1; i++){  
     for(var j = i+1;j<len; j++){
        if (arr[i] + arr[j] == sum)
            return true;
     }
  }
  return false;
}

sumFinder([6,4,3,2,1,7], 9);
// true
sumFinder([6,4,3,2,1,7], 2);
// false
```
Another way of doing, have an object where we will store the difference of sum and element. And then when we get to a new element and if we find the difference is the object, then we have a pair that sums up to the desired sum.
```
function sumFinder(arr, sum){
  var differ = {}, 
      len = arr.length,
      substract;
  
  for(var i =0; i<len; i++){
     substract = sum - arr[i];

     if(differ[substract])
       return true;       
     else
       differ[arr[i]] = true;
  }
  return false;
}
sumFinder([6,4,3,2,1,7], 9);
// true
sumFinder([6,4,3,2,1,7], 2);
// false
```
### Identiy the output
Explain what will the following code snippet output
```
var a = Person('a');
var b = new Person('b');
var c = Person;
function Person(name) {
     this.first_name = name;
}

console.log(a.first_name);
console.log(b.first_name);
console.log(c.first_name);
```
First of all, the function will be hoisted to top. The variable `a` will be `undefiend` and will throw error if we attempt to read `a.first_name`. Because executing the function `Person` without `new` returns `undefined`. 

`b` is the right way to do this. It has the instantiated object and prints `b`.

`c` just has the reference to the function and returns `undefined` because the function has no `first_name` property attached to it.
### Most common word in a string
Given a string of words, return the most common used word in it.
```
function mostCommonWord(inputString) {
  let wordsCounter = {};
  let mostCommonCounter = 0;
  let mostCommonWord = "";
  inputString.split(" ").forEach((word)=>{
    wordsCounter[word] = wordsCounter[word] || 0;
    wordsCounter[word]++;
  });
  Object.keys(wordsCounter).forEach((word)=>{
    if (wordsCounter[word] > mostCommonCounter) {
      mostCommonWord = word;
      mostCommonCounter = wordsCounter[word];
    }
  });
  return mostCommonWord;
}
```
### Creating array with ascending integers
You can use `Array.from()` to map, if you provide a mapping function as its second parameter.
```
Array.from(new Array(3), (x, i) => i)
// [ 0, 1, 2 ]
```
Creating an arbitrary range of integers:
```
const START = 2, END = 5;
Array.from(new Array(END - START), (x, i) => i + START)
// [ 2, 3, 4 ]
```
Another way of creating an Array with ascending integers is via `.keys()`, which also treats holes as if they were `undefined` elements:
```
[...new Array(3).keys()]
// [ 0, 1, 2 ]
```
### Brackets match
For the given string, determine if the strings of brackets in the input is valid or invalid by these criteria.
```
"([)]" // false
"()" // true
```
The solution is
```
function isBalanced(str) {
  var i, ch;

  var bracketsMap = new Map();
  bracketsMap.set(']', '[');
  bracketsMap.set('}', '{');
  bracketsMap.set(')', '(');

  // Use the spread operator to transform a map into a 2D key-value Array.
  var closingBrackets = [...bracketsMap.keys()];
  var openingBrackets = [...bracketsMap.values()];

  var temp = [];
  var len = str.length;

  for (i = 0; i < len; i++) {
    ch = str[i];

    if (openingBrackets.indexOf(ch) > -1) {
      temp.push(ch);
    } else if (closingBrackets.indexOf(ch) > -1) {

      var expectedBracket = bracketsMap.get(ch);
      if (temp.length === 0 || (temp.pop() !== expectedBracket)) {
        return false;
      }

    } else {
      // Ignore the characters which do not match valid Brackets symbol
      continue;
    }
  }

  return (temp.length === 0);
}
```
