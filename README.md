# ES6 for Humans

<br>

### Table of Contents

* [`let`, `const` と ブロックスコープ](#1-let-const-and-block-scoping)
* [アロー関数](#2-arrow-functions)
* [関数のデフォルトパラメーター](#3-default-function-parameters)
* [スプレッド演算子　／レスト演算子](#4-spread--rest-operator)
* [オブジェクトリテラルの拡張](#5-object-literal-extensions)
* [８進数と２進数リテラル](#6-octal-and-binary-literals)
* [配列とオブジェクトの分解](#7-array-and-object-destructuring)
* [オブジェクトにおける`super`の使用](#8-super-in-objects)
* [テンプレートリテラルとデリミタ](#9-template-literal-and-delimiters)
* [for...of vs for...in](#10-forof-vs-forin)
* [Map と WeakMap](#11-map-and-weakmap)
* [Set と WeakSet](#12-set-and-weakset)
* [ES6のクラス](#13-classes-in-es6)
* [Symbol](#14-symbol)
* [Iterators](#15-iterators)
* [Generators](#16-generators)
* [Promises](#17-promises)

<br>

### 他言語

* [中国語 (Thanks to barretlee)](http://www.barretlee.com/blog/2016/07/09/a-kickstarter-guide-to-writing-es6/)
* [ポルトガル語 (Thanks to alexmoreno)](https://github.com/alexmoreno/ES6-para-humanos)
* [ロシア語 (Thanks to etnolover)](https://github.com/etnolover/ES6-for-humans-translation)
* [韓国語 (Thanks to scarfunk)](https://github.com/metagrover/ES6-for-humans/tree/korean-version)
* [フランス語 (Thanks to tnga)](https://github.com/metagrover/ES6-for-humans/tree/french-version)
* [スペイン語 (Thanks to carletex)](https://github.com/metagrover/ES6-for-humans/tree/spanish-version)

<br>

### 1. let, const と ブロックスコープ

宣言文 `let` はブロックスコープと呼ばれるブロックを作ることが出来ます。
ES6では、関数スコープで使用していた`var` の代わりに、`let`の使用が推奨されています。

```javascript
var a = 2;
{
    let a = 3;
    console.log(a); // 3
    let a = 5; // TypeError: Identifier 'a' has already been declared
}
console.log(a); // 2
```

ブロックスコープにおけるもう一つの宣言文は `const` です。`const` は定数を生成します。
ES6の`const`は、値への参照を示す事になります。言い換えれば、この値は凍結されるわけではなく、割り当てているだけなのです。例を見て下さい。

```javascript
{
    const B = 5;
    B = 10; // TypeError: Assignment to constant variable

    const ARR = [5, 6];
    ARR.push(7);
    console.log(ARR); // [5,6,7]
    ARR = 10; // TypeError: Assignment to constant variable
    ARR[0] = 3; // 値は変更可能
    console.log(ARR); // [3,6,7]
}
```

頭の片隅に覚えていてほしいこと:
* `let`、`const`のホイスティングは今までの変数、関数のホイスティングから様変わりしました。`let`も`const`もどちらも、巻き上げるが、その宣言の前にアクセスすることは出来ません。これは、[Temporal Dead Zone(英語)](http://jsrocks.org/2015/01/temporal-dead-zone-tdz-demystified/)のためです。
* `let`と`const`のスコープは最も近い閉じたブロックになります。
* `const`を使用する際は、大文字で記述して下さい(一般的な慣習でもあります)
* `const`は宣言すると同時に定義しなければならなりません。

<br>

### 2. アロー関数

アロー関数はES6で関数を書く際の短縮表記のことです。アロー関数は`=>`に続く関数本体と、`(...)`で表される引数の一覧で定義します。

```javascript
// クラシカルな関数式
let addition = function(a, b) {
    return a + b;
};

// アロー関数で実装
let addition = (a, b) => a + b;
```

上記の例に加えて、アロー関数では`return`文を書く必要がありません。関数本体を簡潔に実装するためです。

これが通常のブロックで関数を記述した例です。

```javascript
let arr = ['apple', 'banana', 'orange'];

let breakfast = arr.map(fruit => {
    return fruit + 's';
});

console.log(breakfast); // ['apples', 'bananas', 'oranges']
```

**ちょっと待って！　もう一つ...**

アロー関数はコードそのものを短くするわけではありません。`this`を束縛する行為と密接に関係しています。

アロー関数の動作は、`this`の動きとともに通常の関数とは異なります。JavaScriptにおける其々の関数は`this`の文脈を定義できます。しかし、アロー関数が捉える`this`は、閉じた文脈となります。次のコードを見てください。

```javascript
function Person() {
    // Person()コンストラクターが定義する`this`はインスタンスそのものだ
    this.age = 0;

    setInterval(function growUp() {
        // strict mode　ではない時、 grouUp() 関数は `this` を 
        // globalオブジェクトとして定義する。Person()コンストラクターが定義した`this`
        // とは異なる
        this.age++;
    }, 1000);
}
var p = new Person();
```

ES3,ES5において、以上の事案に対してはthisを変数に割り当てることで対応してきました。

```javascript
function Person() {
    var self = this;
    self.age = 0;

    setInterval(function growUp() {
        // コールバックが参照する`self`変数は想定しているオブジェクトを指し示す
        self.age++;
    }, 1000);
}
```

上記から、アロー関数は`this`の値を最も近い閉じた文脈を捉えることができるため、ネストしたアロー関数に対しても、以下のコードのように想定通りの動きをすることになります。

```javascript
function Person() {
    this.age = 0;

    setInterval(() => {
        setTimeout(() => {
            this.age++; //　`this` は適切にpersonオブジェクトを参照する
        }, 1000);
    }, 1000);
}

var p = new Person();
```
[アロー関数内の`Lexical this`についてよく知りたければ参照](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Arrow_functions#Lexical_this)

<br>

### 3. 関数のデフォルトパラメーター

ES6では関数定義時にでデフォルトパラメーターを設定することができます。実例をみてください。

```javascript
let getFinalPrice = (price, tax = 0.7) => price + price * tax;
getFinalPrice(500); // 850
```

<br>

### 4. スプレッド演算子　／レスト演算子

`...` 演算子はスプレッド演算子、またはレスト演算子をとして動きます。使用の仕方によって動きが異なります。

イテラブルな何かと使用すると、`...`はスプレッド演算子として働く。

```javascript
function foo(x, y, z) {
    console.log(x, y, z);
}

let arr = [1, 2, 3];
foo(...arr); // 1 2 3
```

`...`のもう一つのよく知られた使い方は値を集めて配列にすることだ。これはレスト演算子として参照される。

```javascript
function foo(...args) {
    console.log(args);
}
foo(1, 2, 3, 4, 5); // [1, 2, 3, 4, 5]
```

<br>

### 5. オブジェクトリテラルの拡張

ES6ではオブジェクトリテラルの宣言の際に、プロパティの初期化と関数メソッドの定義を短縮して記述することができます。これはオブジェクトリテラルの定義からプロパティのキーを計算することができるためです。

```javascript
function getCar(make, model, value) {
    return {
        // プロパティの値を短縮して記述することで、
        // キーと変数名が一致したプロパティになります
        make,  // make: make   と同じ
        model, // model: model と同じ
        value, // value: value と同じ

        // computed values now work with
        // object literals
        // 記述された値はオブジェクトリテラルとして機能します。
        ['make' + make]: true,

        // メソッド定義の短縮形では`function`キーワードとコロンを省略することができます
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

### 6. ８進数と２進数リテラル

ES6では新たに８進数と２進数リテラルをサポートしました。
`0o`か`0O`で始まる number は８進数に変換されます。
以下のコードを見てください。

```javascript
let oValue = 0o10;
console.log(oValue); // 8

let bValue = 0b10; // 0b or 0B for binary
console.log(bValue); // 2
```

<br>

### 7. 配列とオブジェクトの分解

分解によってオブジェクトと配列を扱う際に一時的な変数の使用を避けることができます。

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

### 8. オブジェクトにおける`super`の使用

ES6では`super`関数をプロトタイプと一緒に使用することを可能になりました。

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

### 9. テンプレートリテラルとデリミタ

ES6では文字列への代入をかんたんにできます。代入したと同時に自動的に評価されます。

* <code>\`${ ... }\`</code>は変数をレンダリングできる
* <code>\`</code> `\`バックスラッシュはデリミタとして使用する

```javascript
let user = 'Kevin';
console.log(`Hi ${user}!`); // Hi Kevin!
```

<br>

### 10. for...of vs for...in
* `for...of` 配列のようなイテラブルなオブジェクトをイテレート(順繰りに処理)する

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

* `for...in` はオブジェクトの*数え上げる事ができる*プロパティをイテレートする

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

### 11. Map と WeakMap

ES6には新しいデータ構造の`Map`と`WeakMap`があります。全てのオブジェクトは、`Map`とも考えられるので、私たちはJavaScriptでいつも`Map`を使用しているといえます。

オブジェクトがキー(常に文字である)と値で出来ているのに対し、`Map`では、すべての値(オブジェクトでも、プリミティブな値でも！)をキーにも値にも使用することができるのです。コードを見て下さい。

```javascript
var myMap = new Map();

var keyString = "a string",
    keyObj = {},
    keyFunc = function() {};

// 値をセットします
myMap.set(keyString, "'a string'　に割り当てた値");
myMap.set(keyObj, "keyObj　に割り当てた値");
myMap.set(keyFunc, "keyFunc　に割り当てた値");

myMap.size; // 3

// 値を取得します
myMap.get(keyString);    // "'a string' に割り当てた値"
myMap.get(keyObj);       // "keyObj に割り当てた値"
myMap.get(keyFunc);      // "keyFunc に割り当てた値"
```

**WeakMap**

`WeakMap`はマップです。しかし、ガベージコレクトされる、弱い参照を持つキーになっています。つまり、`WeakMap`ではメモリリークを心配する必要がありません。

また、`Map`に対して、`WeakMap`では、*全てのキーはオブジェクト*でなくてはなりません。

`WeakMap`はたった４つのメソッドしか持ちません。`delete(key)`, `has(key)`, `get(key)` と `set(key, value)`です。

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

*Set* オブジェクトはユニークなコレクションです。重複した値を無視する、全てがユニークな値を保持するコレクションです。*Set*にはプリミティブ値でも、オブジェクトでも格納できます。

```javascript
let mySet = new Set([1, 1, 2, 2, 3, 3]);
mySet.size; // 3
mySet.has(1); // true
mySet.add('strings');
mySet.add({ a: 1, b:2 });
```

`forEach`メソッド、または`for...of`ループを使用して、Setをイテレートし、順番にアクセスすることが出来ます。

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
また、Setは`delete()`メソッドと`clear()`メソッドを保持します。

**WeakSet**

`WeakMap`と同様に、`WeakSet`は弱い*オブジェクト*への参照を持つコレクションです。`WeakSet`はユニークなオブジェクトを持つコレクションであるため、`WeakSet`内にはただ一度だけ現れます。

```javascript
var ws = new WeakSet();
var obj = {};
var foo = {};

ws.add(window);
ws.add(obj);

ws.has(window); // true
ws.has(foo);    // false, fooはsetにまだ加えられていない

ws.delete(window); // setからwindowを取り除く
ws.has(window);    // false, windowを取り除いた
```

<br>

### 13. Classes in ES6

ES6にはクラスのシンタックスが新しく追加されました。注意すべきことは、ES6のクラスはオブジェクト指向の継承モデルではないということです。あくまで既存のプロトタイプベースのJavaScriptのシンタックスシュガー(糖衣構文)に過ぎません。

ES6のクラスは、新しいシンタックスです。今まで使用していたES5のprototype,constructor関数が内部で働きます。

`static`キーワードをもちいることで静的関数をクラス内に定義できます。

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

**クラスにおけるextendsとsuper**

次のコードを見て下さい。

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

`extends`はES6において、親クラスから子クラスへの継承を可能にします。コンストラクターで`super`を呼び出すことを忘れないでください。

また、親クラスのメソッドを子クラスから`super.parentMethodName()`といった形で使用できます。

[クラスについてもっと知りたい](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Classes)

とどめておくべきこと

*クラス宣言はホスティングをしません。はじめにクラスを宣言してからアクセスできます。そうでないとReferenceErrorが投げられます。
*クラス宣言の内部で関数を定義するときに、`function`キーワードは使用する必要はありません。

<br>

### 14. Symbol

シンボル(`Symbol`)は値を変更出来ないデータ型です。シンボルの目的は、ユニークな識別子を生成することです。

シンボルを作成してみましょう。

```javascript
var sym = Symbol("some optional description");
console.log(typeof sym); // symbol
```

`Symbol(…)`を使用する際は、`new`キーワードは不要です。

シンボルはオブジェクトのプロパティ、またはキーとして使用された時、通常の数え上げられるプロパティとしては格納されません。

```javascript
var o = {
    val: 10,
    [Symbol("random")]: "I'm a symbol",
};

console.log(Object.getOwnPropertyNames(o)); // val
```

オブジェクトのシンボルとなったプロパティにアクセスするときは、`Object.getOwnPropertySymbols(o)`を使用して下さい。


<br>

### 15. Iterators

イテレータはコレクションの１つのアイテムに一度アクセスします。そして、コレクションのアクセスした場所を記憶しています。
イテレータにはコレクションの次の順番のアイテムを返す`next()`メソッドがあり、返却されたアイテムであるオブジェクトは、doneとvalueという２つのプロパティを持っています。

ES6は`Symbol.iterator`をもち、オブジェクトのためのイテレータを提供します。オブジェクトはいつでも、for...ofのようなループで数え上げる事ができます。オブジェクトの`@@iterator`メソッドは実行時に引数を持たず、イテレートすることで値を返すイテレータを返します。

配列を見てみましょう。配列はイテラブル(数え上げ可能)です。イテレータは値を出力します。

```javascript
var arr = [11,12,13];
var itr = arr[Symbol.iterator]();

itr.next(); // { value: 11, done: false }
itr.next(); // { value: 12, done: false }
itr.next(); // { value: 13, done: false }

itr.next(); // { value: undefined, done: true }
```

オブジェクトの定義の際に`obj[Symbol.iterator]()` を定義することで、自前のイテレータを記述することもできます。

<br>

### 16. Generators

ジェネレータ関数はES6の新しい特徴の１つです。ジェネレータ関数は何度も値を返します。返り値となるオブジェクトをイテレートし、１度のイテレートで１つ、値を取得します。

ジェネレータ関数は実行された際に、**イテラブルなオブジェクト**を返します。
`*`という新しいシンタックスと`yield`というES6の新しいキーワードによって記述されます。

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

yidldを呼んだ回数分だけ、yieldされた値が次の値になります。

また、ジェネレータはオンデマンドでyieldされた値を計算し、効率よく、連続したシークエンスに要求に応じてアクセスできます。無限に続くシークエンスですら可能です。

<br>

### 17. Promises

ES6ではネイティブでPromiseをサポートしています。プロミスは非同期実行が完了することを待ち続けるオブジェクトです。非同期実行が完了したとき、プロミス(つまり約束)は「遂行(fulfilled/resolved)」されるか、「破棄(rejected)」されます。

Promiseをつくる通常の方法は、`new Promise()`コンストラクターを使用することで、2つの与えられた関数を引数として扱える様になります。第一引数は`resolve`と呼ばれる関数で、**future valueとともに** 実行されることが想定されます。第二引数は`reject`で、Promiseが**future value**をresolveせず、rejectされたときに実行されます。

```javascript
var p = new Promise(function(resolve, reject) {  
    if (/* condition */) {
        resolve(/* value */);  // fulfilled successfully
    } else {
        reject(/* reason */);  // error, rejected
    }
});
```

全てのPromiseは`then`と呼ばれるメソッドをもっています。このメソッドはペアとなるコールバックを保持します。
１つめのコールバックはプロミスが遂行された際に、もう一方はプロミスが破棄された時に呼ばれます。

```javascript
p.then((val) => console.log("Promise Resolved", val),
       (err) => console.log("Promise Rejected", err));
```

`then`のコールバックから返却された値は次の`then`のコールバックに渡されます。

```javascript
var hello = new Promise(function(resolve, reject) {  
    resolve("Hello");
});

hello.then((str) => `${str} World`)
     .then((str) => `${str}!`)
     .then((str) => console.log(str)) // Hello World!
```

Promiseが帰ってきた時、遂行された値は次のコールバックにチェーンして渡していきます。
これはコールバック地獄を避けるシンプルな１つの策です。。

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
