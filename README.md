
# ES6 for Humans

개인적으로 ES6를 다시 한번 배워보는 좋은 기회라 생각되어, 번역을 처음으로 시도해봅니다. 미숙하지만...번역이 미흡하거나 하면 댓글이나 GitHub issue, PR 모두 환영합니다.

> 주석 부분은 개인적으로 작성한 추가 설명 부분입니다.

원 출처(origin) 는 [여기](https://github.com/metagrover/ES6-for-humans) 입니다.

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

<br>

### 1. let, const and block scoping

`let` 변수 할당은 어떤 block 스코프에도 선언될 수 있습니다. `var`를 사용하는 것 대신에요. 함수 스코프에도 제공됩니다. ES6에서는 `let`을 사용하는 것을 추천합니다.

```javascript
var a = 2;
{
    let a = 3;
    console.log(a); // 3
    let a = 5; // TypeError: Identifier 'a' has already been declared 
    // (새 변수 선언이 불가능 합니다)
}
console.log(a); // 2 (블록 스코핑으로 동작합니다)
```

이번엔 `const`입니다. 상수를 생성합니다. ES6에서, `const`는 value의 상수 참조를 표현합니다. 다르게 말해, value는 mutable(불변) 합니다.

> primitive 타입들은, 할당시에 call by value 방식이므로 그 자체가 복제되어 할당됩니다. 하지만 reference 타입들은 call by reference이므로, var b = a 일 때 a의 변경이 b에도 영향을 미칩니다. 너무 쉬운 건가요?


```javascript
{
    const B = 5;
    B = 10; // TypeError: Assignment to constant variable
    // (상수화된 변수 그 자체에 새롭게 재할당은 불가합니다.)

    const ARR = [5, 6];
    ARR.push(7);
    console.log(ARR); // [5,6,7]
    ARR = 10; // TypeError: Assignment to constant variable
    ARR[0] = 3; // value is mutable (하지만, value를 변경하는것은 가능합니다)
    console.log(ARR); // [3,6,7]
}
```

기억해 둘 점:

* `let`과 `const`의 호이스팅은, 기존의 방식과 다릅니다. 둘 다 호이스팅 되지만, 선언 전에는 접근 할 수 없습니다. [Temporal Dead Zone](http://jsrocks.org/2015/01/temporal-dead-zone-tdz-demystified/) 때문입니다.

```javascript
console.log(x); // throws a ReferenceError
let x = 'hey';
```

* `let` 과 `const`는 가장 가까운 블록에 스코핑을 가집니다.
* `const`를 사용할 때, 대문자 변수를 사용하세요.
* `const`는 선언과 동시에, 할당(정의)되어야 합니다.

<br>

### 2. Arrow Functions

Arrow Functions 은 ES6에서 축약된 함수를 사용할 때 쓰입니다. 이것은 파라미터의 리스트 `( ... )` 부분과 바로 뒤따르는 `=>` 가 함수의 body 부분으로 이루어져 있습니다

```javascript
// Classical Function Expression(ES6 이전 방식)
let addition = function(a, b) {
    return a + b;
};

// Arrow Function 으로 구현
let addition = (a, b) => a + b;
```

위의 예제에서 눈여겨볼 부분은, "간결화된 body" 를 사용할 수 있다는 점입니다. 이것은 return 구문이 필요가 없습니다.

`{}`를 이용한 "블록 body"의 예제는 아래와 같습니다.


```javascript
let arr = ['apple', 'banana', 'orange'];

let breakfast = arr.map(fruit => {
    return fruit + 's'; //return 이 필요합니다.
});

console.log(breakfast); // ['apples', 'bananas', 'oranges']
```

**더 알아야 할것들...**

Arrow function 은 그저 축약을 위한것만이 아닙니다.
`this` 바인딩과 밀접한 연관이 있습니다.

Arrow function 에서의 `this`의 작동법은 이전과 다릅니다. 자바스크립트에서 각 function 은 정의 될때 마다, 새로운 `this`를 가집니다. 하지만, Arrow 에서는 `this`를 context 영역에서 고유하게 가집니다. 

```javascript
function Person() {
    // The Person() 생성자가 `this` 를 정의합니다
    this.age = 0;

    setInterval(function growUp() {
        // In non-strict mode 에서, the growUp()  `this`를 global 에 정의해 버립니다.
        // 이것은 the Person() constructor에 선언된 this.age와 다른것입니다.
        this.age++;
    }, 1000);
}
var p = new Person(); // this.age는 NaN 입니다.
```

ECMAScript 3/5 에서는, 이 이슈를 `this`를 다른변수에 할당하는방법으로 해결 했었습니다.

```javascript
function Person() {
    var self = this;
    self.age = 0;

    setInterval(function growUp() {
        // The callback refers to the `self` variable of which
        // 콜백 펑션은 'self' 변수를 객체안에서 참조 합니다.
    }, 1000);
}
var p = new Person(); // age++ 은 올바르게 증가합니다
```

위에서 말했듯이, Arrow function 은 context 안에서 value 를 잡아두고 있는것이죠. 그래서 아래 코드는 잘 작동 합니다.

```javascript
function Person() {
    this.age = 0;

    setInterval(() => {
        this.age++; // `this` properly refers to the person object
    }, 1000);
}

var p = new Person();
```
[Arrow Function 의 'Lexical this' 에 대해 좀 더 알아 보고 싶다면 여기를 참조하세요](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Arrow_functions#Lexical_this)

<br>

### 3. Default Function Parameters

ES6은 디폴트 파라미트를 지원합니다. 예제를 봅시다.

```javascript
let getFinalPrice = (price, tax = 0.7) => price + price * tax;
getFinalPrice(500); // 850
```
> tax 가 null이라면 0.7이 디폴트로 들어가게 됩니다.

<br>

### 4. Spread / Rest Operator

`...` 연산자는 사용방식에 따라, "spread" 혹은 "rest" 연산으로 나뉩니다.

어떤 iterable 한 객체에 사용될때, 각각의 elements 에 알맞게 나눠준다고 할수 있습니다. 이것을 "spread" 연산이라고 합니다.

```javascript
function foo(x, y, z) {
    console.log(x, y, z);
}

let arr = [1, 2, 3];
foo(...arr); // 1 2 3
```

다른 또 하나는 여러개의 밸류를 하나의 array 로 모으는 방식이 있습니다. 이것을 "rest" 연산이라고 합니다.

```javascript
function foo(...args) {
    console.log(args);
}
foo(1, 2, 3, 4, 5); // [1, 2, 3, 4, 5]
```

> 자바스크립트는 함수의 paremeter 갯수의 제약이 약합니다. 더 많은 param 을 넣을수도, 더 적은 param 을 넣는것도 가능합니다.

<br>

### 5. Object Literal Extensions

리터럴 확장 객체

ES6는 객체에 변수에 속성을 초기화 하거나, 함수 방법을 정의하는 것에 축약 구문을 제공합니다. 또한 속성 키가 합성된 키로 만들어지는것이 가능합니다.

```javascript
function getCar(make, model, value) {
    return {
        // 프로퍼티 축약 구문에 의해,
        // 키가 변수 명과 같다면,
        // 프로퍼티의 value 를 생략 할수 있습니다.
        make,  // make: make 과 같습니다
        model, // model: model 과 같습니다
        value, // value: value 과 같습니다

        // value 를 key 에 넣은 방식도 가능합니다.
        ['make' + make]: true,

        // 함수 정의도 축약이 가능합니다.
        // function 키워드와 세미콜론을 생략합니다.
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

> 이전 방식과 섞어서 쓰는것도 가능합니다.

<br>

### 6. Octal and Binary Literals

ES6는 8진수 및 2진수 에 대한 새로운 기능을 지원하고 있습니다.
` 0o` 또는 ` 0O` 는 8진수 및 2진수로 변환됩니다.

```javascript
let oValue = 0o10;
console.log(oValue); // 8

let bValue = 0b10; // 0b or 0B 둘다 지원합니다.
console.log(bValue); // 2
```

<br>

### 7. Array and Object Destructuring

Destructuring 은 객체 및 배열을 처리 할 때 임시변수의 사용을 피할때 사용됩니다.

```javascript
function foo() {
    return [1, 2, 3];
}
let arr = foo(); // [1,2,3] // 이전의 방식입니다.

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

> 더 자세한 설명을 원한다면 [이곳](http://untangled.io/in-depth-es6-destructuring-with-assembled-avengers/?utm_source=javascriptweekly&utm_medium=email)을 방문해 보세요.

### 8. super in Objects

ES6는 (클래스가 아닌)객체에 프로토타입 `super` 함수을 사용할 수 있습니다. 다음 은 간단한 예입니다 :

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

ES6의 문자열을 쉽게 만드는 방법에 대해 알아봅시다.

* <code>\`${ ... }\`</code> 가 변수의 랜더링에 사용됩니다.
* <code>\`</code> Backtick(백틱) 이 구분기호로 사용됩니다.

```javascript
let user = 'Kevin';
console.log(`Hi ${user}!`); // Hi Kevin!
```

<br>

### 10. for...of vs for...in

* `for...of` 는 배열같은 iterable 객체를 순환 합니다.

```javascript
let nicknames = ['di', 'boo', 'punkeye'];
nicknames.size = 3;
for (let nickname of nicknames) {
    console.log(nickname);
}
// di
// boo
// punkeye
// size 는 할당되었지만, 나오지 않았습니다.
// length 와 혼동되지 마세요 :)
```

* `for...in` 는 모든 객체속의 열거형 프로퍼티를 순환합니다.

```javascript
let nicknames = ['di', 'boo', 'punkeye'];
nicknames.size = 3;
for (let nickname in nicknames) {
    console.log(nickname);
}
// 0 for...of 와 다르게, value 가 아닌, key 값 인점을 주의하세요.
// 1
// 2
// size
```

<br>

### 11. Map and WeakMap

ES6 은 `Map` 과 `WeakMap` 이라는 새로운 데이터 구조가 있습니다. 우리는 자바스크립트를 사용할때, 항상 map 을 사용합니다. 실제로 모든 객체는 `Map` 으로 간주될수 있죠.

객체는 키(항상 문자열)와 밸류로 이루어져 있습니다 반면에 `Map`에서는 어떤 밸류든(객체 혹은 원시 밸류) 키와 밸류로 사용될수 있습니다.

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

`WeakMap` 은 키들이 약한 참조를 가지고 있습니다. 이것은 키들이 GC 되는것을 막지 못합니다. 이것은 메모리 누수 에 걱정할 필요가 없다는 거죠.

`Map`과 반대로 유의 할점은 `WeakMap`은 *항상 key는 객체여야 합니다.*

`WeakMap`은 4가지 method만 가집니다. `delete(key)`, `has(key)`, `get(key)` 과 `set(key, value)` 입니다.


```javascript
let w = new WeakMap();
w.set('a', 'b');
// Uncaught TypeError: Invalid value used as weak map key
// key는 object 여야 합니다.

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

Set객체는 고유한 값의 컬렉션입니다. 중복된 밸류는 무시됩니다. 값들은 원시타입이나 참조 객체가 가능합니다.

```javascript
let mySet = new Set([1, 1, 2, 2, 3, 3]);
mySet.size; // 3 
// Set 과 Map은 size를 가집니다. length가 아니라요. Weak 에는 없습니다 :)
mySet.has(1); // true
mySet.add('strings');
mySet.add({ a: 1, b:2 });
```

`forEach` 함수나 `for...of` 루프 구문으로 set 데이터 구조를 순환 할수 있습니다.

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

Set 은 `delete()` 와 `clear()` 함수도 가지고 있습니다.

**WeakSet**

` WeakMap` 과 비슷하게, ` WeakSet` 객체는 컬렉션의 객체들의 약하게 참조합니다. 그것은 WeakSet 의 컬렉션에서 고유합니다.

```javascript
var ws = new WeakSet();
var obj = {};
var foo = {};

ws.add(window);
ws.add(obj);

ws.has(window); // true
ws.has(foo);    // false, foo 는 set에 저장되지 않았습니다.

ws.delete(window); // window 객체를 없앱니다
ws.has(window);    // false, window has been removed
```

<br>

### 13. Classes in ES6

ES6 는 클래스 구문이 있습니다. 여기서 주의 할 점은 ES6의 클래스는 새로운 객체 지향 상속 모델이 아니라는 것입니다. 단지 자바 스크립트 의 기존 프로토 타입 기반 상속의 역할을 하는것 입니다.

ES6 의 클래스는 우리가 ES5 에서 쓰고 있었던 프로토 타입과 생성자 기능이 새로운 구문으로 바뀌었다는 것입니다.

`static` 키워드로 정의된 함수는 static 함수를 구현합니다.

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
Task.loadAll(); // "Loading all tasks.." 클래스 자체를 사용합니다.
```

**extends and super in classes**

다음의 코드를 보세요:

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

`extends` 는 자식 클래스가 부모로 부터 상속을 받을수 있게 합니다. 여기서 중요한것은, 자식의 생성자는 반드시 super() 메소드를 호출해야 합니다.

> 물론 constructor() 는 작성하지 않아도 됩니다. 하지만 자식 클래스에서 작성한다면, 반드시 super() 를 호출해야 합니다.

물론, 부모 클래스의 함수를 `super.parentMethodName()` 를 통해 호출 할수도 있습니다.

[classes 대해 더 알아봅시다](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Classes)

몇개 더 새겨둡시다:

* 클래스 선언은 호이스트 되지 않습니다. 먼저 선언하고 접근해야합니다. 그렇지 않으면 ReferenceError 를 보게 될것입니다.
* 클래스 내부에서 함수를 선언할 때, `function` 키워드를 사용할 필요가 없습니다.

<br>

### 14. Symbol

ES6의 심볼은 unique 하며, 불가변적인 데이터 타입 입니다. 심볼의 목적은 고유 식별자를 생성하는 것입니다. 하지만 식별자 에 대한 접근은 불가능합니다.

symbol 을 어떻게 생성하는지 봅시다:

```javascript
var sym = Symbol("some optional description");
console.log(typeof sym); // symbol
```

`Symbol` 구문은 `new`키워드를 사용할수 없습니다.

만약 심볼이 객체의 프로퍼티/키 로 사용된다면, 보통의 eunmeration 탐색으로는 나타나지 않는, 특별한 방법으로 저장됩니다.

```javascript
var o = {
    val: 10,
    [Symbol("random")]: "I'm a symbol",
};

console.log(Object.getOwnPropertyNames(o)); // val
```

객체의 심볼 프로퍼티를 찾기 위해서는, `Object.getOwnPropertySymbols(o)` 를 사용하세요.


<br>

### 15. Iterators

iterator 는 현재의 시퀀스를 가지고 위치를 추적하면서, collection 의 아이템을 한번에 하나씩 접근합니다. `next()` 메소드는 다음 순서의 아이템을 반환하며, 리턴된 객체는 2개의 프로퍼티를 가집니다 : done 과 value.

ES6는 객체에 대한 default iterator 를 지정하는 `Symbol.iterator` 가 있습니다. 객체를 반복 할 필요가 있을때마다(예를 들면 for..of 루프를 사용할때) @@iterator 메소드는 arguments 없이 사용되며, 반환된 iterator 는 반복되는 값을 얻는데 사용됩니다.

배열을 가지고 예를 든다면, 배열은 iterable 하며, iterator 는 value 를 꺼내주는 것입니다.

```javascript
var arr = [11,12,13];
var itr = arr[Symbol.iterator]();

itr.next(); // { value: 11, done: false }
itr.next(); // { value: 12, done: false }
itr.next(); // { value: 13, done: false }

itr.next(); // { value: undefined, done: true }
```

`obj[Symbol.iterator]()`로 object 정의할때, custom iterators 를 만들수 있습니다.

> [이터레이션 프로토콜](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Iteration_protocols) 을 공부합시다. generator 와도 많은 연관이 있으니까요.

<br>

### 16. Generators

제네레이터 function 은 ES6의 신기능 중 하나이며, 함수가 많은 값들을 iterable 하게 순환하며 꺼내올수 있는 방법을 가능하게 합니다.

제네레이터 function 은 **iterable object** 를 반환합니다.
ES6에 있는 `yield` 키워드 와 함께, `*` 구문을 사용해서 작성됩니다.

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

yield 가 호출될때 마다, yielded(산출된) 값이 다음 순차의 value 에 나오게 됩니다.

또한, 제네레이터는 무한 시퀀스나 계산이 비싼 시퀀스들을 효율적으로 작성할수 있게 도와줍니다.

<br>

### 17. Promises

ES6는 promise 를 지원합니다. promise는 비동기적인 작업이 완료되는것을 기다려주는 객체입니다. 작업이 완료 되면, promise 는 fulfilled(resolved: 해결) 혹은 rejected(거부) 가 됩니다.

통상적인 Promise 객체를 생성하는 방법은 `new Promise()` 생성자를 이용하는것입니다. 생성자는 2개의 함수를 파라미터로 갖는 핸들러 를 가져야합니다. 첫번째 핸들러는(`resolve`라고 불리우는) 준비된 나중 값을 부를때 사용되는 함수입니다. 그리고 두번째 핸들러는(`reject`) 나중 값을 resolve 할수 없으면 Promise를 거부(reject) 합니다.

```javascript
var p = new Promise(function(resolve, reject) {  
    if (/* condition */) {
        resolve(/* value */);  // fulfilled successfully
    } else {
        reject(/* reason */);  // error, rejected
    }
});
```

모든 Promise 객체는 `then`이라는 메소드를 가지며, 두개의 콜백함수를 파라미터로 받습니다. 첫번째는 resolved 되었을때 실행되며, 두번째는 rejected 되었을때 실행 됩니다.

```javascript
p.then((val) => console.log("Promise Resolved", val),
       (err) => console.log("Promise Rejected", err));
```

`then` 콜백들에서 반환된 값은 다음 `then` 콜백으로 전달 됩니다.

```javascript
var hello = new Promise(function(resolve, reject) {  
    resolve("Hello");
});

hello.then((str) => `${str} World`)
     .then((str) => `${str}!`)
     .then((str) => console.log(str)) // Hello World!
```

함수가 promise를 반환하는 경우는, 효율적으로 함께 chain 할 수 있도록 resolved 된 값을 다음 콜백에 넘깁니다.
이것은 "콜백 지옥"을 피하기 위한 간단한 테크닉 입니다.


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

> [Promise 에 대한 아주 좋은 설명](http://www.html5rocks.com/ko/tutorials/es6/promises/) 이 있습니다
