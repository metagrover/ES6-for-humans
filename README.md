# ES6 para humanos

<br>

### Tabla de contenidos

* [`let`, `const` y ámbito de bloques](#1-let-const-y-ámbito-de-bloques)
* [Funciones flecha](#2-funciones-flecha)
* [Parámetros de funciones por defecto](#3-parámetros-de-funciones-por-defecto)
* [Operador de propagación/soporte](#4-operador-de-propagaciónsoporte)
* [Extensión de objetos literales](#5-extensión-de-objetos-literales)
* [Literales Octales y Binarios](#6-literales-octales-y-binarios)
* [Desestructuración de Arrays y Objetos](#7-desestructuración-de-arrays-y-objetos)
* [super en Objetos](#8-super-en-objetos)
* [Plantillas de Literales y Delimitadores](#9-plantillas-de-literales-y-delimitadores)
* [for...of vs for...in](#10-forof-vs-forin)
* [Map y WeakMap](#11-map-y-weakmap)
* [Set y WeakSet](#12-set-y-weakset)
* [Clases en ES6](#13-clases-en-es6)
* [Symbol](#14-symbol)
* [Iteradores](#15-iteradores)
* [Generadores](#16-generadores)
* [Promesas](#17-promesas)

<br>

### 1. let, const y ámbito de bloques

`let` permite hacer declaraciones ligadas a un bloque, lo que se conoce como ámbito de bloque. En vez de usar `var`, que proporciona un ámbito a nivel de función, se recomienda usar `let` en ES6.

```javascript
var a = 2;
{
    let a = 3;
    console.log(a); // 3
    let a = 5; // TypeError: Identifier 'a' has already been declared
}
console.log(a); // 2
```

Otra manera de crear declaraciones en el ámbito de bloque, es usar `const`, lo cual declara constantes. En ES6, una `const` representa una referencia constante a un valor. Es decir, la asignación a la misma está bloqueado, pero el valor puede cambiar. Aquí tenemos un ejemplo simple:

```javascript
{
    const B = 5;
    B = 10; // TypeError: Assignment to constant variable

    const ARR = [5, 6];
    ARR.push(7);
    console.log(ARR); // [5,6,7]
    ARR = 10; // TypeError: Assignment to constant variable
    ARR[0] = 3; // El valor es mutable
    console.log(ARR); // [3,6,7]
}
```

Unas cuantas cosas a tener en cuenta:

* El hoisting de `let` y `const` cambia con respecto al hoisting tradicional de variables y funciones. Tanto `let` como `const` son "elevadas" (hoisted), pero no son accesibles a causa de la [Temporal Dead Zone - ENG](http://jsrocks.org/2015/01/temporal-dead-zone-tdz-demystified/)
* `let` y `const` están en el ámbito del bloque más cercano que las contiene.
* Cuando uses `const`, usa LETRAS_MAYÚSCULAS.
* A `const` hay que darle un valor cuando se declara.

<br>

### 2. Funciones flecha

Las funciones flecha son un atajo para escribir funciones en ES6. La definición de una función flecha consiste en una lista de parámetros `( ... )`, seguido del símbolo `=>` y seguido el cuerpo de la función.

```javascript
// Forma clásica
let suma = function(a, b) {
    return a + b;
};

// Implementación con funcion flecha
let suma = (a, b) => a + b;
```
Date cuenta que en el ejemplo anterior, la función flecha `suma` está implementada con una notación concisa en el cuerpo de la función, donde no necesitamos llave ({}) ni una declaración explícita con _return_.

Aquí tenemos un ejemplo con el cuerpo habitual de una función:
```javascript
let arr = ['manzana', 'banana', 'naranja'];

let desayuno = arr.map(fruta => {
    return fruta + 's';
});

console.log(desayuno); // ['manzana', 'banana', 'naranja']
```

**¡Espera! Todavía hay más...**

Las funciones flecha no solo hacen nuestro código más corto. Están muy relacionadas con el comportamiento de `this`.

El comportamiento de la palabra clave `this` varía con respecto a las funciones normales. Cada función en Javascript define su propio contexto, al que hace referencia `this`, pero las funciones flecha capturan el valor de `this` del contexto que las contienen. Fíjate en el siguiente código:


```javascript
function Persona() {
    // El constructor Persona() define `this` como una instancia de sí mismo
    this.edad = 0;

    setInterval(function sumaEdad() {
        // En modo no estricto, la función sumaEdad() define `this`
        // como el objeto global, que es difente del `this` definido
        // en el constructor de Persona()
        this.edad++;
    }, 1000);
}
var p = new Persona();
```

En ECMAScript 3/5, este problema se resolvía guardando el valor de `this` en una variable.

```javascript
function Persona() {
    var self = this;
    self.edad = 0;

    setInterval(function sumaEdad() {
        // La variable `self` contiene el valor
        // de `this` esperado
        self.edad++;
    }, 1000);
}
```

Como se ha mencionado arriba, las funciones flecha capturan el valor de `this` del contexto que las contiene, por tanto el siguiente código funciona según lo previsto:

```javascript
function Persona() {
    this.edad = 0;

    setInterval(() => {
        // `this` se refiere correctamente al objeto persona
        this.edad++;
    }, 1000);
}

var p = new Persona();
```
[Lee más acerca del 'this léxico' en funciones flecha - ENG](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Arrow_functions#Lexical_this)

<br>

### 3. Parámetros de funciones por defecto

ES6 te permite asignar valores por defecto a los parámetros en la definición de las funciones. Aquí un ejemplo:

```javascript
let obtenerPrecioFinal = (precio, tasas = 0.7) => precio + precio * tasas;
obtenerPrecioFinal(500); // 850
```

<br>

### 4. Operador de propagación/soporte

El operador `...` se llama operador de propagación (spread) o de soporte (rest), dependiendo de como se use.

Cuando se usa con cualquier iterable actúa como operador de propagación, expandiendo el iterable en elementos individuales:

```javascript
function foo(x, y, z) {
    console.log(x, y, z);
}

let arr = [1, 2, 3];
foo(...arr); // 1 2 3
```

El otro uso común de `...` es agrupar un conjunto de valores en un array. En este caso se le llama operador de soporte:

```javascript
function foo(...args) {
    console.log(args);
}
foo(1, 2, 3, 4, 5); // [1, 2, 3, 4, 5]
```

<br>

### 5. Extensión de objetos literales

ES6 permite declarar objetos literales a través de una sintaxis compacta. Se pueden inicializar propiedades directamente de variables, y definir funciones de manera ágil. Esta sintaxis, también nos da la posibilidad de definir claves del objeto calculadas. Ejemplo:

```javascript
function getCar(marca, modelo, precio) {
    return {
        // Podemos omitir el valor de la
        // propiedad, si coincide con
        // el nombre de la variable
        marca,  // igual que marca: marca
        modelo, // igual que modelo: modelo
        precio, // igual que precio: precio

        // claves calculadas
        ['marca' + marca]: true,

        // Podemos omitir la clave,
        // la palabra `function` y
        // los dos puntos
        bajarPrecio() {
            this.precio -= 2500;
        }
    };
}

let coche = getCar('Kia', 'Sorento', 40000);
console.log(coche);
// {
//     marca: 'Kia',
//     modelo:'Sorento',
//     precio: 40000,
//     makeKia: true,
//     bajarPrecio: function()
// }
```

<br>

### 6. Literales Octales y Binarios

ES6 tiene soporte para literales octales y binarios.
Poniendo delante del número `0o` or `0O`, lo convierte en octal, y `0b` or `0B` en binario. Ejemplo:

```javascript
let oValue = 0o10; // 0o o 0O para octal
console.log(oValue); // 8

let bValue = 0b10; // 0b o 0B para binario
console.log(bValue); // 2
```

<br>

### 7. Desestructuración de Arrays y Objetos

La destructuración nos evita la necesidad de variables temporales cuando trabajamos con objetos y arrays.

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

### 8. super en Objetos

ES6 nos permite usar el método `super` en objetos (sin clase) con prototipos. Aquí podemos ver un ejemplo:

```javascript
var padre = {
    foo() {
        console.log("Hola desde el padre");
    }
}

var hijo = {
    foo() {
        super.foo();
        console.log("Hola desde el hijo");
    }
}

Object.setPrototypeOf(hijo, padre);
hijo.foo(); // Hola desde el padre
             // Hola desde el hijo
```

<br>

### 9. Plantillas de Literales y Delimitadores

ES6 introduce una manera más sencilla de interpolación, donde los valores son evaluado automáticamente.

* <code>\`${ ... }\`</code> se usa para renderizar variables.
* <code>\`</code> La comilla invertida se usa como delimitador.

```javascript
let usuario = 'Kevin';
console.log(`Hola ${usuario}!`); // Hola Kevin!
```

<br>

### 10. for...of vs for...in

* `for...of` itera sobre cualquier iterable.

```javascript
let nombres = ['di', 'boo', 'punkeye'];
nombres.size = 3;
for (let nombre of nombres) {
    console.log(nombre);
}
// di
// boo
// punkeye
```

* `for...in` itera sobre todas las propiedades enumerables de una objeto.

```javascript
let nombres = ['di', 'boo', 'punkeye'];
nombres.size = 3;
for (let nombre in nombres) {
    console.log(nombre);
}
// 0
// 1
// 2
// size
```

<br>

### 11. Map y WeakMap

ES6 introduce un nuevo conjunto de estructuras de datos, `Map` y `WeakMap`. Ahora mismo ya estamos usando Maps en JavaScript todo el tiempo. De hecho, cada objeto puede ser considerado un `Map`.

Un objeto esta compuesto de claves (siempre cadenas) y valores, donde en un `Map`, cualquier valor (ya sean objetos o valores primitivos) puede ser usados como clave o valor. Mira el siguiente código:

```javascript
var myMap = new Map();

var keyString = "una cadena",
    keyObj = {},
    keyFunc = function() {};

// Asignamos los valores
myMap.set(keyString, "valor asociado con 'una cadena'");
myMap.set(keyObj, "valor asociado con keyObj");
myMap.set(keyFunc, "valor asociado keyFunc");

myMap.size; // 3

// getting the values
myMap.get(keyString);    // "valor asociado con 'una cadena'"
myMap.get(keyObj);       // "valor asociado con keyObjj"
myMap.get(keyFunc);      // "valor asociado keyFunc"
```

**WeakMap**

Un `WeakMap` es un Map donde las claves esta referenciadas de forma débil, lo que no previene que sus claves sean recolectadas por el recoletor de basura. Eso significa que no te tienes que preocupar por fugas de memoria.

Otra cosa a tener en cuenta, en `WeakMap`, al contrario que en `Map`, *las claves deben ser un objeto*.

Un `WeakMap` solo tiene cuatro métodos `delete(key)`, `has(key)`, `get(key)` y `set(key, value)`.

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

w.get(o3); // undefined, porque es el valor que hemos asignado

w.has(o1); // true
w.delete(o1);
w.has(o1); // false
```

<br>

### 12. Set y WeakSet

Los objetos `Set` son colecciones de valores únicos. Los valores duplicados son ignorados, ya que la colección solo puede tener valores únicos. Los valores puede ser primitivos o referencias a objetos.

```javascript
let mySet = new Set([1, 1, 2, 2, 3, 3]);
mySet.size; // 3
mySet.has(1); // true
mySet.add('strings');
mySet.add({ a: 1, b:2 });
```

Puedes iterar sobre un Set o bien usando el método `forEach` o el bucle `for...of`.


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

Sets tambien tienen los métodos `delete()` y `clear()`.

**WeakSet**

Similar a `WeakMap`, y `WeakSet` almacena solo objetos y de manera débil. Un objeto en `WeakSet` solo está una vez ya que es único en la colección.

```javascript
var ws = new WeakSet();
var obj = {};
var foo = {};

ws.add(window);
ws.add(obj);

ws.has(window); // true
ws.has(foo);    // false, foo todavía no se ha añadio al set

ws.delete(window); // se quita window del set
ws.has(window);    // false, window ya no está en el set
```

<br>

### 13. Clases en ES6

ES6 introduce una nueva sintaxis para clases. Hay que tener en consideración que las clases en ES6 no define un nuevo modelo de herencia orientada a objetos. Simplemente sirve como azucar sintáctico sobre la existente herencia basada en prototipos de JavaScript.

Una manera de ver las clases en ES6, es considerarlas una nueva manera de trabajar con prototipos y funciones constructoras tal como haríamos con ES5.

Funciones definidas usando la palabra `static` implementan funciones estática en la clase.


```javascript
class Tarea {
    constructor() {
        console.log("Tarea instanciada!");
    }

    mostrarId() {
        console.log(23);
    }

    static cargarTodas() {
        console.log("Cargando tareas..");
    }
}

console.log(typeof Tarea); // function
let task = new Tarea(); // "Tarea instanciada!"
task.mostrarId(); // 23
task.cargarTodas(); // "Cargando tareas.."
```

**extends y super en clases**

Tenemos el siguiente código:

```javascript
class Coche {
    constructor() {
        console.log("Creando un nuevo coche");
    }
}

class Porsche extends Coche {
    constructor() {
        super();
        console.log("Creando un Porsche");
    }
}

let c = new Porsche();
// Creando un nuevo coche
// Creando un Porsche
```

`extends` permite a clases hijas heredar de clases padres en ES6. Es importante decir, que el constructor derivado debe llamar a super().

Además, también puedes llamar a un método del padre desde una clase hija usando `super.metodoDelPadre()`

[Lee más sobre clases en ES6 - ENG](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Classes)

Un par de cosas a tener en cuenta:

* Las declaraciones de clases no son elevadas (hoisted). Primero tiene que declarar la clase y despues usarla, si no se obtendrá un ReferenceError
* No hay necesidad de usar la palabra `function` cuando se define un método dentro de una clase.

<br>

### 14. Symbol

Un `symbol`, es un tipo de dato único e inmutable nuevo en ES6. El propósito de symbol es generar un identificador único, aunque no puedes acceder a ese identificador.

Así es como creas un symbol:

```javascript
var sym = Symbol("descripción opcional");
console.log(typeof sym); // symbol
```

Date cuenta que no puedes usar `new` con `Symbol(...)`.

Si un symbol es usado como una propiedad/clave de un objeto, se guarda de una manera especial, de tal manera que la propiedad no se mostrará de manera habitual cuando se enumeren las propiedades del objeto.

```javascript
var o = {
    val: 10,
    [Symbol("random")]: "Soy un symbol",
};

console.log(Object.getOwnPropertyNames(o)); // val
```

Para obtener los symbols de un objeto, usa `Object.getOwnPropertySymbols(o)`.

<br>

### 15. Iteradores

Un iterador accede a los elementos de una colección uno por uno, a la vez que guarda la posición actual en la secuencia. Expone el método `next()` que devuelve el siguiente elemento de la secuencia. Este método devuelve un objeto con dos propiedades: done y value.

ES6 tiene `Symbol.iterator`, el cual especifica el iterador por defecto para un objeto. Cuando un objeto necesita ser iterado (por ejemplo al principio de un bucle for..of), su método @@iterador es llamado sin argumentos, y el iterador que devuelve se usa para obtener los valores a ser iterados.

Vamos a ver un array, que es un iterable, y el iterador que puede producir para consumir sus valores:

```javascript
var arr = [11,12,13];
var itr = arr[Symbol.iterator]();

itr.next(); // { value: 11, done: false }
itr.next(); // { value: 12, done: false }
itr.next(); // { value: 13, done: false }

itr.next(); // { value: undefined, done: true }
```

Date cuenta que puede escribir tu propio iterador definiendo `obj[Symbol.iterator]()` en la definición del objeto.

<br>

### 16. Generadores

Las funciones generadoras son una nueva funcionalidad en ES6 que permite a una función generar varios valores a lo largo del tiempo, devolviendo un objeto que puede ser iterado para obtener valores uno por uno.

Una función generadora devuelve un **objeto iterable** cuando es llamada.

Se declara escribiendo la nueva sintaxis con `*` y `yield`, introducidos en ES6.

```javascript
function *numerosInfinitos() {
    var n = 1;
    while (true) {
        yield n++;
    }
}

var numbers = numerosInfinitos(); // Devuelve un objeto iterador

numbers.next(); // { value: 1, done: false }
numbers.next(); // { value: 2, done: false }
numbers.next(); // { value: 3, done: false }
```
Cada vez que se ejecuta yield, el valor con el que se llama se convierte en el siguiente valor de la secuencia.

Date cuenta que los generadores calculan los valores bajo demanda, lo que permite representar secuencias que son costosas de calcular (incluso infintas) de manera eficiente.

<br>

### 17. Promesas

ES6 tiene soporte nativo para promesas. Una promesa es un objeto que está esperando a que una operación asíncrona termine, y cuando la operación termine, la promesa estará resuelta o rechazada.

La manera estandar de crear una promesa es ejecutando el constructo `new Promise()`, que acepta un callback que tiene dos funciones como parámetro. El primer parámetro (normalmente se le llama `resolve`) es la función que hay que llamar en el futuro cuando el valor esté listo, y el segundo parámetro (nomalmente llamado `reject`) es la funcion que hay que llamar para rechazar la promesa, si no se puede resolver la operación que estamos operando.

```javascript
var p = new Promise(function(resolve, reject) {
    if (/* condicion */) {
        resolve(/* value */);  // Todo OK
    } else {
        reject(/* reason */);  // error, rechazada
    }
});
```

Todas las promesas tienen un método llamada `then`, que recibe dos callbacks. El primero se llama si la promesa ha sido resuelta, y el segundo cuando la promesa ha sido rechazada.


```javascript
p.then((val) => console.log("Promesa resuelta", val),
       (err) => console.log("Promesa rechazada", err));
```

Devolver un valor desde un callback de `then`, pasará el valor al siguiente callback.


```javascript
var hello = new Promise(function(resolve, reject) {
    resolve("Hola");
});

hello.then((str) => `${str} Mundo`)
     .then((str) => `${str}!`)
     .then((str) => console.log(str)) // Hola Mundo!
```

Cuando se devuelve una promesa, el valor resuelto de la promesa se pasará al siguiente callback. Eso permite encadenarlos correctamente. Es un técnica sencilla para evitar el "callback hell"

```javascript
var p = new Promise(function(resolve, reject) {
    resolve(1);
});

var suma1 = (val) => {
    return new Promise(function(resolve, reject){
        resolve(val + 1);
    });
}

p.then(eventuallyAdd1)
 .then(eventuallyAdd1)
 .then((val) => console.log(val)) // 3
```
