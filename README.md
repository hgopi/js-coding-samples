# Javascript Code samples

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
