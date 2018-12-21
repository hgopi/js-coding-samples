# Javascript Code samples

1. [Variable scope](#variable-scope)
2. [Context](#context)
3. [Closure](#closure)
4. [call, apply and bind](#call-apply-and-bind)
5. [Functions](#functions)

## Variable scope

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
