# ES6 for Humans

<br>

:loudspeaker: Hướng dẫn đầy đủ hiện có sẵn trên [Amazon](https://www.amazon.com/ES6-Humans-Latest-Standard-JavaScript/dp/1484226224)

[![ES6 for humans - Apress book](book-cover.jpg)](https://www.amazon.com/ES6-Humans-Latest-Standard-JavaScript/dp/1484226224)

### Mục lục

- [ES6 for Humans](#es6-for-humans)
    - [Mục lục](#m%E1%BB%A5c-l%E1%BB%A5c)
    - [Các ngôn ngữ khác](#c%C3%A1c-ng%C3%B4n-ng%E1%BB%AF-kh%C3%A1c)
    - [1. let, const và block scoping](#1-let-const-v%C3%A0-block-scoping)
    - [2. Arrow Functions](#2-arrow-functions)
    - [3. Default Function Parameters](#3-default-function-parameters)
    - [4. Spread / Rest Operator](#4-spread--rest-operator)
    - [5. Object Literal Extensions](#5-object-literal-extensions)
    - [6. Octal và Binary Literals](#6-octal-v%C3%A0-binary-literals)
    - [7. Array và Object Destructuring](#7-array-v%C3%A0-object-destructuring)
    - [8. Super in Objects](#8-super-in-objects)
    - [9. Template Literal và Delimiters](#9-template-literal-v%C3%A0-delimiters)
    - [10. for...of vs for...in](#10-forof-vs-forin)
    - [11. Map và WeakMap](#11-map-v%C3%A0-weakmap)
    - [12. Set và WeakSet](#12-set-v%C3%A0-weakset)
    - [13. Classes trong ES6](#13-classes-trong-es6)
    - [14. Symbol](#14-symbol)
    - [15. Iterators](#15-iterators)
    - [16. Generators](#16-generators)
    - [17. Promises](#17-promises)

<br>

### Các ngôn ngữ khác

* [Chinese Version (Thanks to barretlee)](http://www.barretlee.com/blog/2016/07/09/a-kickstarter-guide-to-writing-es6/)
* [Portuguese Version (Thanks to alexmoreno)](https://github.com/alexmoreno/ES6-para-humanos)
* [Russian Version (Thanks to etnolover)](https://github.com/etnolover/ES6-for-humans-translation)
* [Korean Version (Thanks to scarfunk)](https://github.com/metagrover/ES6-for-humans/tree/korean-version)
* [French Version (Thanks to tnga)](https://github.com/metagrover/ES6-for-humans/tree/french-version)
* [Spanish Version (Thanks to carletex)](https://github.com/metagrover/ES6-for-humans/tree/spanish-version)
* [Japanese Version (Thanks to isdh)](https://github.com/metagrover/ES6-for-humans/tree/japanese-version)
* [VietNamese Version (Thanks to thaycacac)](https://github.com/metagrover/ES6-for-humans/tree/japanese-version)

<br>

### 1. let, const và block scoping

`let` cho phép bạn tạo khởi tạo khai báo với phạm vi trong block được gọi là block scope. Nó dùng để thay thế `var`, cái mà trước đây phạm vi của nó là function scope, khuyễn khích nên sử dụng biễn trong block scope (`let` or `const`) trong ES6.

```javascript
var a = 2;
{
    let a = 3;
    console.log(a); // 3
    let a = 5; // TypeError: Identifier 'a' has already been declared
}
console.log(a); // 2
```

Một hình thức khai báo khác của block-scoped là `const`, sử dụng khi tạo hằng số. Trong ES6, một `const` được sử dụng khi tham chiếu liên tục đến một giá trị. Cách nói khác, giá trị của `Object` và `Array` có thể thay đổi nhưng không được gán lại biến. Ở đây là một ví dụ đơn giản:

```javascript
{
    const b = 5;
    b = 10; // TypeError: Assignment to constant variable

    const arr = [5, 6];
    arr.push(7);
    console.log(arr); // [5,6,7]
    arr = 10; // TypeError: Assignment to constant variable
    arr[0] = 3; // value is mutable
    console.log(arr); // [3,6,7]
}
```

Một số điều cần ghi nhớ:

* Hoisting (là khái niệm chỉ việc mọi khai báo biến) của `let` và `const` khác nhau cơ bản ở hoisting của biến và hàm. Cả `let` và `const` đều hoisted, nhưng không thể được truy cập trước khi khai vào bời vì [Temporal Dead Zone](http://jsrocks.org/2015/01/temporal-dead-zone-tdz-demystified)
* `let` và `const` được sử dụng trong phạm vi gần dấu mở ngoặc nhất cho tới dấu đóng ngoặc.
* Khi fix một giá trị cụ thế không thay đổi suốt chương trình nên đặt tên dưới dạng caplock (vd: `const PI = 3.14`)
* `const` phải được định nghĩa khi khai báo nó.
* Luôn sử dụng `const` thay vì `let`, trừ khi bạn muốn gắn lại biến.

<br>

### 2. Arrow Functions

Arrow functions là dạng viết ngắn gọn cho một hàm trong ES6. Một arrow function được hình thành bơi một danh sách các parameter `( ... )`, theo sau là `=>` để đánh đấu sau nó là body function. Đối với hàm chỉ có một parameter thì có thể bỏ qua cặp dấu ngoặc tròn.

```javascript
// Classical Function Expression
function addition(a, b) {
    return a + b;
};

// Implementation with arrow function
const addition = (a, b) => a + b;

// With single argument, no parentheses required
const add5 = a => 5 + a;
```

Lưu ý ví dụ ở bên trên, `addition` arrow function được implement với "concise body" có nghĩa là nó khong cần phải đặt trong cặp dấu `{ }` sau `=>` vì phần body của nó chỉ có một câu lệnh. 

Dưới đây là một ví dụ sử dụng "block body" thông thường.

```javascript
const arr = ['apple', 'banana', 'orange'];

const breakfast = arr.map(fruit => {
    return fruit + 's';
});

console.log(breakfast); // ['apples', 'bananas', 'oranges']
```

**Kìa! Còn nhiều hơn thế...**

Arrow functions không chỉ làm code của bạn ngắn hớn. Chúng còn có liên kết chặt chẽ với việc binding `this`.

Mỗi function trong Javascript đều định nghĩa ngữ cảnh của nó là từ khoá `this`. Việc sử dụng arrow function có thể làm thay đổi ngữ cảnh này, nó sẽ thảnh đổi `this` trỏ vào ngữ cảnh gần nó nhất. Hãy kiếm tra đoạn ví dụ dưới đây:

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

Trong ECMAScript 3/5, vấn đề này đã được khắc phục bằng cách gán giá trị trong `this` cho một biến có thể được đóng lại.

```javascript
function Person() {
    const self = this;
    self.age = 0;

    setInterval(function growUp() {
        // The callback refers to the `self` variable of which
        // the value is the expected object.
        self.age++;
    }, 1000);
}
```

Như đã đề cập ở trên, các arrow function nắm bắt giá trị này của ngữ cảnh kèm theo gần nhất, do đó đoạn mã sau hoạt động như mong đợi, ngay cả với các arrow function lồng nhau.

```javascript
function Person() {
    this.age = 0;

    setInterval(() => {
        setTimeout(() => {
            this.age++; // `this` properly refers to the person object
        }, 1000);
    }, 1000);
}

let p = new Person();
```
[Read more about 'Lexical this' in arrow functions here](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Arrow_functions#No_binding_of_this)

<br>

### 3. Default Function Parameters

ES6 cho phép bạn set giá trị mặc định cho parameters khi khai báo hàm 
ES6 allows you to set default parameters in function definitions. Dưới đây là một minh hoạ đơn giản:

```javascript
const getFinalPrice = (price, tax = 0.7) => price + price * tax;
getFinalPrice(500); // 850
```

<br>

### 4. Spread / Rest Operator

`...` là toán tử được gọi là spread hoặc rest operator, tuỳ thuộc vào cách thức và nơi chúng được sử dụng.

Khi sử dụng bất kỳ một iterable nào chúng đều chia như "spread" thành các phần tử riêng lẻ:

```javascript
const makeToast = (breadType, topping1, topping2) => {
  return `I had ${breadType} toast with ${topping1} and ${topping2}`;
};
```

```javascript
const ingredients = ['wheat', 'butter', 'jam'];
makeToast(...ingredients);
// "I had wheat toast with butter and jam"

makeToast(...['sourdough', 'avocado', 'kale']);
// "I had sourdough toast with avocado and kale"
```

Cũng có thể sử dụng spread để lấy shaping từ nột đối tượng mới từ một hoặc nhiều đối tường khác:

```javascript
const defaults = {avatar: 'placeholder.jpg', active: false}
const userData = {username: 'foo', avatar: 'bar.jpg'}

console.log({created: '2017-12-31', ...defaults, ...userData})
// {created: "2017-12-31", avatar: "bar.jpg", active: false, username: "foo"}
```

Mảng cũng có thể như vậy:

```javascript
const arr1 = [1, 2, 3];
const arr2 = [7, 8, 9];
console.log([...arr1, 4, 5, 6, ...arr2]) // [1, 2, 3, 4, 5, 6, 7, 8, 9]
```

Cách sử dụng phổ biến khác của `...` là tập hợp các đối số của mảng, điều này được gọi là toán tử "rest"

```javascript
function foo(...args) {
    console.log(args);
}
foo(1, 2, 3, 4, 5); // [1, 2, 3, 4, 5]
```

<br>

### 5. Object Literal Extensions

ES6 cho phép khai báo các đối tượng bằng cách cung cấp cú pháp ngắn gọn để khởi tạo các thuộc tính từ các biến và xác định các phương thức hàm. Nó cũng có thể có computed property keys trong một object literal.

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

### 6. Octal và Binary Literals

ES6 có hỗ trợ mới cho các số bát phân và nhị phân.
Một số bắt đầu với `0o` hoặc `0O` sẽ được convert sang hệ bát phân. Hãy xem ví dụ dưới đây:

```javascript
let oValue = 0o10;
console.log(oValue); // 8

let bValue = 0b10; // 0b or 0B for binary
console.log(bValue); // 2
```

<br>

### 7. Array và Object Destructuring

Destructuring giúp tránh sự cần thiết của các biến tạm thời khi làm việc với đối tượng và mảng.

```javascript
function foo() {
    return [1, 2, 3];
}
let arr = foo(); // [1,2,3]

let [a, b, c] = foo();
console.log(a, b, c); // 1 2 3

```

```javascript

function getCar() {
    return {
        make: 'Tesla',
        model: 'g95',
        metadata: {
        vin: '123abc',
        miles: '12000'
        }
    };
}

const {make, model} = getCar();
console.log(make, model); // Tesla g95

const {make, metadata: {miles}} = getCar();
console.log(make, miles); // Tesla 12000

```

<br>

### 8. Super in Objects

ES6 sử dụng phương thức `super` trong (classless) đối tượng với prototypes. Theo dõi ví dụ dưới đây:

```javascript
const parent = {
    foo() {
        console.log("Hello from the Parent");
    }
}

const child = {
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

### 9. Template Literal và Delimiters

ES6 giới thiệu một cách dễ dàng hơn để thêm các phép interpolations được đánh giá tự động.

* <code>\`${ ... }\`</code> được sử dụng để hiển thị các biến.
* <code>\`</code> backtick được sử dụng như dấu phân cách.

```javascript
let user = 'Kevin';
console.log(`Hi ${user}!`); // Hi Kevin!
```

<br>

### 10. for...of vs for...in
* `for...of` lặp qua các đối tượng lặp, chẳng hạn như mảng.

```javascript
const nicknames = ['di', 'boo', 'punkeye'];
nicknames.size = 3;
for (let nickname of nicknames) {
    console.log(nickname);
}
// di
// boo
// punkeye
```

* `for...in` lặp qua các thuộc tính enumerable của từng đối tương. Chẳng hạn:

```javascript
const nicknames = ['di', 'boo', 'punkeye'];
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

### 11. Map và WeakMap

ES6 giới thiệu bộ cấu trúc mới gọi là `Map` và `WeakMap`. Bầy giờ, chúng ta hoàn toàn có thể sử dụng map trong Javascript. Thực thế tất cả các object đều có thể xem như một `Map`.

Một đối tượng trong `Map` được tạo bởi key (luôn luôn là strings) và values, mọi value (cả objects và primitive values) có thể được sử dụng làm một key hoặc một value. Hãy nhìn vào đoạn code này:

```javascript
const myMap = new Map();

const keyString = "a string",
    keyObj = {},
    keyFunc = () => {};

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

Một `WeakMap` là một Map với các key với tham chiếu yếu, không ngăn chặn các keys của nó từ garbage-collected. Điều đó có nghĩa là bạn không phải lo lắng về rò rỉ bộ nhớ.

Một số lưu ý trong `WeakMap` trái ngược với `Map` *mọi key phải là một đối tượng*.

Một `WeakMap` chỉ có bốn phương thức `delete(key)`, `has(key)`, `get(key)` và `set(key, value)`.

```javascript
const w = new WeakMap();
w.set('a', 'b');
// Uncaught TypeError: Invalid value used as weak map key

const o1 = {},
    o2 = () => {},
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

### 12. Set và WeakSet

*Set* objects là tập hợp các giá trị duy nhất. Những giá trị trùng lặp sẽ bị bỏ qua, vì tất cả các giá trị trong *Set* phải là duy nhất. Các giá trị có thể là kiểu nguyên thủy hoặc tham chiếu đối tượng.

```javascript
const mySet = new Set([1, 1, 2, 2, 3, 3]);
mySet.size; // 3
mySet.has(1); // true
mySet.add('strings');
mySet.add({ a: 1, b:2 });
```

Bạn cũng có thể lặp qua từng phần tử trong *Set* bằng cách sử dụng phương thức `forEach` hoặc `for...of`.

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
Sets cũng có các phương thức `delete()` và `clear()`.

**WeakSet**

Tương tự `WeakMap`, `WeakSet` đối tượng cho phép bạn lưu trữ yêu *objects* ở trong tập hợp. Một đối tượng ở trong `WeakSet` chỉ xảy ra một lần; nó là duy nhất trong tập của Weakset.

```javascript
const ws = new WeakSet();
const obj = {};
const foo = {};

ws.add(window);
ws.add(obj);

ws.has(window); // true
ws.has(foo);    // false, foo has not been added to the set

ws.delete(window); // removes window from the set
ws.has(window);    // false, window has been removed
```

<br>

### 13. Classes trong ES6

ES6 giới thiệu cú pháp class mới. Cần chú ý răng Class trong ES6 không phải là mô hình thừa kế hướng đối tượng mới. Chúng chỉ đóng vai trò là đường tổng hợp so với kế thừa dựa trên nguyên mẫu hiện có của JavaScript.


Class trong ES6 chỉ sử dụng cú pháp mới để làm việc với prototypes và cấu trúc hàm mà chúng sử dụng trong ES5.

Định nghĩa các hàm sử dụng từ khoá `static` để implement static/class functions trong class.

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
const task = new Task(); // "task instantiated!"
task.showId(); // 23
Task.loadAll(); // "Loading all tasks.."
```

**Thừa kế và super trong classes**

Theo dõi đoạn code dưới đây:

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

`extends` cho phép class con thừa kế từ class cha trong ES6. Điều quan trọng cần lưu ý là hàm tạo dẫn xuất phải gọi `super()`.

Tuy nhiên bạn cũng có thể gọi phương thức của class cha trong class con bằng cách sử dụng phương thức `super.parentMethodName()`

[Read more about classes here](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Classes)

Một vài cần lưu ý:

* Khai báo Class không phải hoisted. Trước tiên bạn cần khai báo lớp của mình và sau đó truy cập vào nó, nếu không nó sẽ ném ra ReferenceError.
* Không cần sử dụng từ khoá `function`khi định nghĩa functions bên trong một định nghĩa class.

<br>

### 14. Symbol

Một `Symbol` là một kiểu dữ liệu duy nhất và bất biến được giới thiệu trong ES6. Mục đích của symbol là tạo ra một mã định danh duy nhất nhưng bạn không bao giờ có thể có quyền truy cập vào mã định danh đó.

Dưới đây là cách tạo symbol:

```javascript
const sym = Symbol("some optional description");
console.log(typeof sym); // symbol
```

Chú ý rằng không được sử dụng `new` với `Symbol(…)`.

Nếu một symbol được sử dụng như một property/key của một đối tượng, nó được lưu trữ theo cách đặc biệt mà ở trong property không hiển thị trong bảng liệt kê của các thuộc tính đối tượng..

```javascript
const o = {
    val: 10,
    [Symbol("random")]: "I'm a symbol",
};

console.log(Object.getOwnPropertyNames(o)); // val
```

Để lấy một thuộc tính ký hiệu đối tượng, sử dụng `Object.getOwnPropertySymbols(o)`


<br>

### 15. Iterators

Một iterator cho phép lặp từng phần tử từ một tập trong cùng một thời điểm, trong khi theo dõi vị trí hiện tại của nó trong chuỗi đó. Iterators cung cấp phương thức `next()` sẽ trả về item tiếp theo trong dãy. Phương thức này sẽ trả về một đối tượng với hai thuộc tính: done và value.

ES6 có `Symbol.iterator` chỉ định iterator mặc định cho một đối tượng. Bất cứ khi nào một đối tượng cần duyệt vòng lặp (tương tự như sử dụng vòng lặp for..of), phương thức *@@iterator* được gọi không có đối số, và iterator trả về được sử dụng để thu được các giá trị được lặp lại.

Hãy nhìn vào mảng dưới đây để hiểu về interator:

```javascript
const arr = [11,12,13];
const itr = arr[Symbol.iterator]();

itr.next(); // { value: 11, done: false }
itr.next(); // { value: 12, done: false }
itr.next(); // { value: 13, done: false }

itr.next(); // { value: undefined, done: true }
```

Chú ý bạn có thể customer interator bơi `obj[Symbol.iterator]()` với định nghĩa đối tượng.

<br>

### 16. Generators

Generator functions là một tính năng mới trong ES6 nó cho phép một hàm có thể tạo ra nhiều giá trị theo thời gian bằng cách trả về một đối tượng có thể được lặp đi lặp lại để kéo các giá trị từ hàm một giá trị tại một thời điểm.

Một generator function trả về một ** đối tượng iterable** khi nó được gọi.
Nó được viết bằng cách sử dụng cú pháp mới `*` syntax cũng như từ khoá `yield` được giới thiệu trong ES6.

```javascript
function *infiniteNumbers() {
    let n = 1;
    while (true) {
        yield n++;
    }
}

const numbers = infiniteNumbers(); // returns an iterable object

numbers.next(); // { value: 1, done: false }
numbers.next(); // { value: 2, done: false }
numbers.next(); // { value: 3, done: false }
```

Mỗi lần *yield* được gọi, giá trị yielded trở thành giá trị tiếp theo trong chuỗi.

<br>

### 17. Promises

ES6 có hỗ trợ riêng cho các promises. Một *promise* là một đối tượng mà nó sẽ chờ cho một hoạt động không đồng bộ hoàn thành, và khi toán tử hoàn thành, promise sẽ trả về hoặc là fulfilled(resolved) hoặc là rejected.

Cách tiêu chuẩn để tạo một promise là sử dụng cấu trúc `new Promise()` và nó sẽ nhận hai parameter. Tham số đầu tiên (thường được đặt tên là `resolve`) là một hàm được gọi khi xử lý xong và thành công, tham số thứ hai (thường được đặt tên là `reject`) được gọi khi xử lý xong và và thất bại:

```javascript
const p = new Promise((resolve, reject) => {
    if (/* condition */) {
        resolve(/* value */);  // fulfilled successfully
    } else {
        reject(/* reason */);  // error, rejected
    }
});
```

Mọi promise đều có một phương thức là `then` nó nhận một cặp của callbacks.

```javascript
p.then((val) => console.log("Promise Resolved", val),
       (err) => console.log("Promise Rejected", err));
```

Giá trị được return bởi `then` trước sẽ chuyển vào parameter của `then` sau. Hãy nhìn ví dụ dưới đây:

```javascript
const hello = new Promise((resolve, reject) => { resolve("Hello") });

hello.then((str) => `${str} World`)
     .then((str) => `${str}!`)
     .then((str) => console.log(str)) // Hello World!
```

Khi trả về một promise, giá trị resolved của promise sẽ được truyền vào call back tiếp theo để nối chúng lại với nhau.
Đây là một kỹ thuật đơn giản để tránh "callback hell".

```javascript
const p = new Promise((resolve, reject) => { resolve(1) });

const eventuallyAdd1 = (val) => new Promise((resolve, reject) => { resolve(val + 1) });

p.then(eventuallyAdd1)
 .then(eventuallyAdd1)
 .then((val) => console.log(val)); // 3
```
