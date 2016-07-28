# ES6 for Humans

<br>

### Table of Contents

* [`let`, `const` and block scoping](#1-let-const-and-block-scoping)
* [Arrow Functions](#2-arrow-functions)
* [Default Function Parameters](#3-default-function-parameters)
* [Spread/Rest Operator](#4-spread--rest-operator)
* [Object Literal Extensions](#5-object-literal-extensions)
* [Octal and Binary Literals](#6-octal-and-binary-literals)
* [Array and Object Destructuring](#7-array-and-object-destructuring)
* [super in Objects](#8-super-in-objects)
* [Template Literal and Delimiters](#9-template-literal-and-delimiters)
* [for...of vs for...in](#10-forof-vs-forin)
* [Map and WeakMap](#11-map-and-weakmap)
* [Set and WeakSet](#12-set-and-weakset)
* [Classes in ES6](#13-classes-in-es6)
* [Symbol](#14-symbol)
* [Iterators](#15-iterators)
* [Generators](#16-generators)
* [Promises](#17-promises)

<br>

### Languages

* [Chinese Version (Thanks to barretlee)](http://www.barretlee.com/blog/2016/07/09/a-kickstarter-guide-to-writing-es6/)
* [Portuguese Version (Thanks to alexmoreno)](https://github.com/alexmoreno/ES6-para-humanos)
* [Russian Version (Thanks to etnolover)](https://github.com/etnolover/ES6-for-humans-translation)
* [Korean Version (Thanks to scarfunk)](https://github.com/metagrover/ES6-for-humans/tree/korean-version)

<br>

### 1. let, const and block scoping

`let` allows you to create declarations which are bound to any block, called block scoping. Instead of using `var`, which provides function scope, it is recommended to use `let` in ES6.

```javascript
var a = 2;
{
    let a = 3;
    console.log(a); // 3
    let a = 5; // TypeError: Identifier 'a' has already been declared
}
console.log(a); // 2
```

Another form of block-scoped declaration is the `const`, which creates constants. In ES6, a `const` represents a constant reference to a value. In other words, the value is not frozen, just the assignment of it. Here's a simple example:

```javascript
{
    const B = 5;
    B = 10; // TypeError: Assignment to constant variable

    const ARR = [5, 6];
    ARR.push(7);
    console.log(ARR); // [5,6,7]
    ARR = 10; // TypeError: Assignment to constant variable
    ARR[0] = 3; // value is mutable
    console.log(ARR); // [3,6,7]
}
```

A few things to keep in mind:

* Hoisting of `let` and `const` vary from the traditional hoisting of variables and functions. Both `let` and `const` are hoisted, but cannot be accessed before their declaration, because of [Temporal Dead Zone](http://jsrocks.org/2015/01/temporal-dead-zone-tdz-demystified/) 
* `let` and `const` are scoped to the nearest enclosing block.
* When using `const`, use CAPITAL_CASING (this is a common convention).
* `const` has to be defined with its declaration.

<br>

### 2. Arrow Functions

Arrow Functions are a short-hand notation for writing functions in ES6. The arrow function definition consists of a parameter list `( ... )`, followed by the `=>` marker and a function body.

```javascript
// Classical Function Expression
let addition = function(a, b) {
    return a + b;
};

// Implementation with Arrow Function
let addition = (a, b) => a + b;
```
Note that in the above example, the `addition` arrow function is implemented with "concise body" which does not need an explicit return statement.

Here is an example with the usual "block body"

```javascript
let arr = ['apple', 'banana', 'orange'];

let breakfast = arr.map(fruit => {
    return fruit + 's';
});

console.log(breakfast); // ['apples', 'bananas', 'oranges']
```

**Behold! There is more...**

Arrow functions don't just make the code shorter. They are closely related to `this` binding behavior.

Arrow functions behavior with `this` keyword varies from that of normal functions. Each function in JavaScript defines its own `this` context but Arrow functions capture the `this` value of the enclosing context. Check out the following code:

```javascript
function Person() {
    // The Person() constructor defines `this` as an instance of itself.
    this.age = 0;

    setInterval(function growUp() {
        // In non-strict mode, the growUp() function defines `this`
        // as the global object, which is different from the `this`
        // defined by the Person() constructor.
        this.age++;
    }, 1000);
}
var p = new Person();
```

In ECMAScript 3/5, this issue was fixed by assigning the value in `this` to a variable that could be closed over.

```javascript
function Person() {
    var self = this;
    self.age = 0;

    setInterval(function growUp() {
        // The callback refers to the `self` variable of which
        // the value is the expected object.
        self.age++;
    }, 1000);
}
```

As mentioned above, Arrow functions capture the this value of the enclosing context, so the following code works as expected.

```javascript
function Person() {
    this.age = 0;

    setInterval(() => {
        this.age++; // `this` properly refers to the person object
    }, 1000);
}

var p = new Person();
```
[Read more about 'Lexical this' in arrow functions here](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Arrow_functions#Lexical_this)

<br>

### 3. Default Function Parameters

ES6 allows you to set default parameters in function definitions. Here is a simple illustration.

```javascript
let getFinalPrice = (price, tax = 0.7) => price + price * tax;
getFinalPrice(500); // 850
```

<br>

### 4. Spread / Rest Operator

`...` operator is referred to as spread or rest operator, depending on how and where it is used.

When used with any iterable, it acts as to "spread" it into individual elements:

```javascript
function foo(x, y, z) {
    console.log(x, y, z);
}

let arr = [1, 2, 3];
foo(...arr); // 1 2 3
```

The other common usage of `...` is gathering a set of values together into an array. This is referred as "rest" operator.

```javascript
function foo(...args) {
    console.log(args);
}
foo(1, 2, 3, 4, 5); // [1, 2, 3, 4, 5]
```

<br>

### 5. Object Literal Extensions

ES6 allows declaring object literals by providing shorthand syntax for initializing properties from variables and defining function methods. It also enables the ability to have computed property keys in an object literal definition.

```javascript
function getCar(make, model, value) {
    return {
        // with property value shorthand
        // syntax, you can omit the property
        // value if key matches variable
        // name
        make,  // same as make: make
        model, // same as model: model
        value, // same as value: value

        // computed values now work with
        // object literals
        ['make' + make]: true,

        // Method definition shorthand syntax
        // omits `function` keyword & colon
        depreciate() {
            this.value -= 2500;
        }
    };
}

let car = getCar('Kia', 'Sorento', 40000);
console.log(car);
// {
//     make: 'Kia',
//     model:'Sorento',
//     value: 40000,
//     makeKia: true,
//     depreciate: function()
// }
```

<br>

### 6. Octal and Binary Literals

ES6 has new support for octal and binary literals.
Prependending a number with `0o` or `0O` would convert it into octal value. Have a look at the following code:

```javascript
let oValue = 0o10;
console.log(oValue); // 8

let bValue = 0b10; // 0b or 0B for binary
console.log(bValue); // 2
```

<br>

### 7. Array and Object Destructuring

Destructuring helps in avoiding the need for temp variables when dealing with object and arrays.

```javascript
function foo() {
    return [1, 2, 3];
}
let arr = foo(); // [1,2,3]

let [a, b, c] = foo();
console.log(a, b, c); // 1 2 3

function bar() {
    return {
        x: 4,
        y: 5,
        z: 6
    };
}
let { x: a, y: b, z: c } = bar();
console.log(a, b, c); // 4 5 6
```

<br>

### 8. super in Objects

ES6 allows to use `super` method in (classless) objects with prototypes. Following is a simple example:

```javascript
var parent = {
    foo() {
        console.log("Hello from the Parent");
    }
}

var child = {
    foo() {
        super.foo();
        console.log("Hello from the Child");
    }
}

Object.setPrototypeOf(child, parent);
child.foo(); // Hello from the Parent
             // Hello from the Child
```

<br>

### 9. Template Literal and Delimiters

ES6 introduces an easier way to add interpolation which are evaluated automatically.

* <code>\`${ ... }\`</code> is used for rendering the variables.
* <code>\`</code> Backtick is used as delimiter.

```javascript
let user = 'Kevin';
console.log(`Hi ${user}!`); // Hi Kevin!
```

<br>

### 10. for...of vs for...in
* `for...of` iterates over iterable objects, such as array.

```javascript
let nicknames = ['di', 'boo', 'punkeye'];
nicknames.size = 3;
for (let nickname of nicknames) {
    console.log(nickname);
}
// di
// boo
// punkeye
```

* `for...in` iterates over all enumerable properties of an object.

```javascript
let nicknames = ['di', 'boo', 'punkeye'];
nicknames.size = 3;
for (let nickname in nicknames) {
    console.log(nickname);
}
// 0
// 1
// 2
// size
```

<br>

### 11. Map and WeakMap

ES6 introduces new set of data structures called `Map` and `WeakMap`. Now, we actually use maps in JavaScript all the time. In fact every object can be considered as a `Map`.

An object is made of keys (always strings) and values, whereas in `Map`, any value (both objects and primitive values) may be used as either a key or a value. Have a look at this piece of code:

```javascript
var myMap = new Map();

var keyString = "a string",
    keyObj = {},
    keyFunc = function() {};

// setting the values
myMap.set(keyString, "value associated with 'a string'");
myMap.set(keyObj, "value associated with keyObj");
myMap.set(keyFunc, "value associated with keyFunc");

myMap.size; // 3

// getting the values
myMap.get(keyString);    // "value associated with 'a string'"
myMap.get(keyObj);       // "value associated with keyObj"
myMap.get(keyFunc);      // "value associated with keyFunc"
```

**WeakMap**

A `WeakMap` is a Map in which the keys are weakly referenced, that doesn’t prevent its keys from being garbage-collected. That means you don't have to worry about memory leaks.

Another thing to note here- in `WeakMap` as opposed to `Map` *every key must be an object*.

A `WeakMap` only has four methods `delete(key)`, `has(key)`, `get(key)` and `set(key, value)`.

```javascript
let w = new WeakMap();
w.set('a', 'b');
// Uncaught TypeError: Invalid value used as weak map key

var o1 = {},
    o2 = function(){},
    o3 = window;

w.set(o1, 37);
w.set(o2, "azerty");
w.set(o3, undefined);

w.get(o3); // undefined, because that is the set value

w.has(o1); // true
w.delete(o1);
w.has(o1); // false
```

<br>

### 12. Set and WeakSet

Set objects are collections of unique values. Duplicate values are ignored, as the collection must have all unique values. The values can be primitive types or object references.

```javascript
let mySet = new Set([1, 1, 2, 2, 3, 3]);
mySet.size; // 3
mySet.has(1); // true
mySet.add('strings');
mySet.add({ a: 1, b:2 });
```

You can iterate over a set by insertion order using either the `forEach` method or the `for...of` loop.

```javascript
mySet.forEach((item) => {
    console.log(item);
    // 1
    // 2
    // 3
    // 'strings'
    // Object { a: 1, b: 2 }
});

for (let value of mySet) {
    console.log(value);
    // 1
    // 2
    // 3
    // 'strings'
    // Object { a: 1, b: 2 }
}
```
Sets also have the `delete()` and `clear()` methods.

**WeakSet**

Similar to `WeakMap`, the `WeakSet` object lets you store weakly held *objects* in a collection. An object in the `WeakSet` occurs only once; it is unique in the WeakSet's collection.

```javascript
var ws = new WeakSet();
var obj = {};
var foo = {};

ws.add(window);
ws.add(obj);

ws.has(window); // true
ws.has(foo);    // false, foo has not been added to the set

ws.delete(window); // removes window from the set
ws.has(window);    // false, window has been removed
```

<br>

### 13. Classes in ES6

ES6 introduces new class syntax. One thing to note here is that ES6 class is not a new object-oriented inheritance model. They just serve as a syntactical sugar over JavaScript's existing prototype-based inheritance.

One way to look at a class in ES6 is just a new syntax to work with prototypes and contructor functions that we'd use in ES5.

Functions defined using the `static` keyword implement static/class functions on the class.

```javascript
class Task {
    constructor() {
        console.log("task instantiated!");
    }
    
    showId() {
        console.log(23);
    }
    
    static loadAll() {
        console.log("Loading all tasks..");
    }
}

console.log(typeof Task); // function
let task = new Task(); // "task instantiated!"
task.showId(); // 23
Task.loadAll(); // "Loading all tasks.."
```

**extends and super in classes**

Consider the following code:

```javascript
class Car {
    constructor() {
        console.log("Creating a new car");
    }
}

class Porsche extends Car {
    constructor() {
        super();
        console.log("Creating Porsche");
    }
}

let c = new Porsche();
// Creating a new car
// Creating Porsche
```

`extends` allow child class to inherit from parent class in ES6. It is important to note that the derived constructor must call super().

Also, you can call parent class's method in child class's methods using `super.parentMethodName()`

[Read more about classes here](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Classes)

A few things to keep in mind:

* Class declarations are not hoisted. You first need to declare your class and then access it, otherwise ReferenceError will be thrown.
* There is no need to use `function` keyword when defining functions inside a class definition.

<br>

### 14. Symbol

A symbol is a unique and immutable data type introduced in ES6. The purpose of a symbol is to generate a unique identifier but you can never get any access to that identifier.

Here’s how you create a symbol:

```javascript
var sym = Symbol("some optional description");
console.log(typeof sym); // symbol
```

Note that you cannot use `new` with `Symbol(…)`.

If a symbol is used as a property/key of an object, it’s stored in a special way that the property will not show up in a normal enumeration of the object’s properties.

```javascript
var o = {
    val: 10,
    [Symbol("random")]: "I'm a symbol",
};

console.log(Object.getOwnPropertyNames(o)); // val
```

To retrieve an object’s symbol properties, use `Object.getOwnPropertySymbols(o)`


<br>

### 15. Iterators

An iterator accesses the items from a collection one at a time, while keeping track of its current position within that sequence. It provides a `next()` method which returns the next item in the sequence. This method returns an object with two properties: done and value.

ES6 has `Symbol.iterator` which specifies the default iterator for an object. Whenever an object needs to be iterated (such as at the beginning of a for..of loop), its @@iterator method is called with no arguments, and the returned iterator is used to obtain the values to be iterated.

Let’s look at an array, which is an iterable, and the iterator it can produce to consume its values:

```javascript
var arr = [11,12,13];
var itr = arr[Symbol.iterator]();

itr.next(); // { value: 11, done: false }
itr.next(); // { value: 12, done: false }
itr.next(); // { value: 13, done: false }

itr.next(); // { value: undefined, done: true }
```

Note that you can write custom iterators by defining `obj[Symbol.iterator]()` with the object definition.

<br>

### 16. Generators

Generator functions are a new feature in ES6 that allow a function to generate many values over time by returning an object which can be iterated over to pull values from the function one value at a time.

A generator function returns an **iterable object** when it's called.
It is written using the new `*` syntax as well as the new `yield` keyword introduced in ES6.

```javascript
function *infiniteNumbers() {
    var n = 1;
    while (true) {
        yield n++;
    }
}

var numbers = infiniteNumbers(); // returns an iterable object

numbers.next(); // { value: 1, done: false }
numbers.next(); // { value: 2, done: false }
numbers.next(); // { value: 3, done: false }
```

Each time yield is called, the yielded value becomes the next value in the sequence.

Also, note that generators compute their yielded values on demand, which allows them to efficiently represent sequences that are expensive to compute, or even infinite sequences.

<br>

### 17. Promises

ES6 has native support for promises. A promise is an object that is waiting for an asynchronous operation to complete, and when that operation completes, the promise is either fulfilled(resolved) or rejected.

The standard way to create a Promise is by using the `new Promise()` constructor which accepts a handler that is given two functions as parameters. The first handler (typically named `resolve`) is a function to call with the future value when it's ready; and the second handler (typically named `reject`) is a function to call to reject the Promise if it can't resolve the future value.

```javascript
var p = new Promise(function(resolve, reject) {  
    if (/* condition */) {
        resolve(/* value */);  // fulfilled successfully
    } else {
        reject(/* reason */);  // error, rejected
    }
});
```

Every Promise has a method named `then` which takes a pair of callbacks. The first callback is called if the promise is resolved, while the second is called if the promise is rejected.

```javascript
p.then((val) => console.log("Promise Resolved", val),
       (err) => console.log("Promise Rejected", err));
```

Returning a value from `then` callbacks will pass the value to the next `then` callback.

```javascript
var hello = new Promise(function(resolve, reject) {  
    resolve("Hello");
});

hello.then((str) => `${str} World`)
     .then((str) => `${str}!`)
     .then((str) => console.log(str)) // Hello World!
```

When returning a promise, the resolved value of the promise will get passed to the next callback to effectively chain them together.
This is a simple technique to avoid "callback hell".

```javascript
var p = new Promise(function(resolve, reject) {  
    resolve(1);
});

var eventuallyAdd1 = (val) => {
    return new Promise(function(resolve, reject){
        resolve(val + 1);
    });
}

p.then(eventuallyAdd1)
 .then(eventuallyAdd1)
 .then((val) => console.log(val)) // 3
```
