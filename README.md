# ES6 pour Humans

<br>

### Table des matières

* [`let`, `const` et les portées de bloc](#1-let-const-et-les-portées-de-bloc)
* [Fonctions fléchées](#2-fonctions-fléchées)
* [Paramètres par défaut de fonction](#3-paramètres-par-défaut-de-fonction)
* [L'opérateur de décomposition](#4-lopérateur-de-décomposition)
* [Évolution des objects littéraux](#5-Évolution-des-objects-littéraux)
* [Octal et Binaire littéraux](#6-octal-et-binaire-littéraux)
* [Destructuration de tableau et d'objet](#7-destructuration-de-tableau-et-dobjet)
* [Le mot clé super dans les objets](#8-le-mot-clé-super-dans-les-objets)
* [Modèle littéral et délimitateurs](#9-modèle-littéral-et-délimitateurs)
* [for...of vs for...in](#10-forof-vs-forin)
* [Map et WeakMap](#11-map-et-weakmap)
* [Set et WeakSet](#12-set-et-weakset)
* [Les classes en ES6](#13-les-classes-en-es6)
* [Symbol](#14-symbol)
* [Itérateurs](#15-itérateurs)
* [Générateurs](#16-générateurs)
* [Promesses](#17-promesses)

<br>

### Langues

* [Version Chinoise (merci à barretlee)](http://www.barretlee.com/blog/2016/07/09/a-kickstarter-guide-to-writing-es6/)
* [Version Portugaise (merci à alexmoreno)](https://github.com/alexmoreno/ES6-para-humanos)
* [Version Russe (merci à etnolover)](https://github.com/etnolover/ES6-for-humans-translation)
* [Version coréen (merci à scarfunk)](https://github.com/metagrover/ES6-for-humans/tree/korean-version)

<br>

### 1. `let`, `const` et les portées de bloc

`let` vous permet de créer des déclarations qui sont liées à un bloc, appelée portée de bloc. Au lieu d'utiliser `var`, qui fournit soit une portée globale, soit une portée de fonction, il est recommendé d'utiliser `let` en ES6.

```javascript
var a = 2;
{
    let a = 3;
    console.log(a); // 3
    let a = 5; // TypeError: 'a' identifié comme déjà déclaré
}
console.log(a); // 2
```

Une autre forme de déclaration induisant une portée de bloc est `const`, qui crée des constantes. En ES6, un `const` represente une constante reférence à une valeur. En d'autres mots, la valeur n'est pas modifiable, juste l'affectation de celle-ci suffit. Voici un exemple simple:

```javascript
{
    const B = 5;
    B = 10; // TypeError: Assignement à une variable constante

    const ARR = [5, 6];
    ARR.push(7);
    console.log(ARR); // [5,6,7]
    ARR = 10; // TypeError: Assignement à une variable constante
    ARR[0] = 3; // valeur muable
    console.log(ARR); // [3,6,7]
}
```

quelques particularités à garder à l'esprit:

* La remontée de `let` et `const` varie de la remontée traditionelle des variables et des functions. `let` et `const` sont remontés, mais ne peuvent être accessible avant leur déclaration, dû à la [Zone morte temporaire](http://jsrocks.org/2015/01/temporal-dead-zone-tdz-demystified/) 
* `let` et `const` ont une portée limité au bloc courant.
* Lorsque vous utilisez ` const` , utilisez entièrement les MAJUSCULES (ceci est un standard).
* `const` doit être definit lors de sa déclaration.

<br>

### 2. Fonctions fléchées

Les fonctions fléchées ou fonctions lambda sont des raccourcis pour écrire des fonctions en ES6. la definition d'une fonction fléchée consiste en une liste de paramètre `( ... )`, suivi par le marqueur `=>` et le corps de la fonction.

```javascript
// Expression de function classique
let addition = function(a, b) {
    return a + b;
};

// Implémentation avec fonction fléchée
let addition = (a, b) => a + b;
```
Notez que dans l'exemple ci-dessus, la fonction lambda `addition` est implementé avec "un corps concis" qui n'a pas besoin d'une instruction *return* explicite.

Voici un exemple avec le "corps de bloc" usuelle.

```javascript
let arr = ['pomme', 'banane', 'orange'];

let breakfast = arr.map(fruit => {
    return fruit + 's';
});

console.log(breakfast); // ['pommes', 'bananes', 'oranges']
```

**Ceci dit, Il y a plus...**

Les fonctions fléchées ne reduisent pas juste la longueur du code, elles sont étroitement reliées à l'entité de liaison `this`.

Ce comportement des fonctions fléchées avec le mot clé `this` se distingue de celui des fonctions normales. Chaque fonction en JavaScript definit son propre contexte `this` mais les fonctions fléchées capturent la valeur `this` du contexte courant. Soit à Consulter le code suivant:

```javascript
function Person() {
    // Le constructeur Person() definit `this` comme une instance de lui même.
    this.age = 0;

    setInterval(function growUp() {
        // En mode non-strict, la fonction growUp() definit `this`
        // comme l'objet global, qui est different du `this`
        // definit par le constructeur Person().
        this.age++;
    }, 1000);
}
var p = new Person();
```

En ECMAScript 3/5, ce probleme était resolu en assignant la valeur en `this` à une variable qui pourrait en reférer.

```javascript
function Person() {
    var self = this;
    self.age = 0;

    setInterval(function growUp() {
        // La fonction de rappel refère à la variable `self` dont
        // la valeur est l'objet espéré.
        self.age++;
    }, 1000);
}
```

Comme mentionée ci-dessus, les fonctions fléchées capturent la valeur `this` du contexte courant, donc le code suivant fonctionne comme prévu, même avec des fonctions fléchées imbriquées.

```javascript
function Person() {
    this.age = 0;

    setInterval(() => {
        setTimeout(() => {
            this.age++; // `this` referre proprement à l'objet person
        }, 1000);
    }, 1000);
}

var p = new Person();
```
[En savoir plus sur 'le `this` lexicale' des fonctions fléchées ici](https://developer.mozilla.org/fr/docs/Web/JavaScript/Reference/Fonctions/Fonctions_fl%C3%A9ch%C3%A9es#this_lexical)

<br>

### 3. Paramètres par défaut de fonction

ES6 vous permet de définir des paramètres par défaut dans les définitions de fonction. Voici une illustration simple:

```javascript
let getFinalPrice = (price, tax = 0.7) => price + price * tax;
getFinalPrice(500); // 850
```

<br>

### 4. L'opérateur de décomposition

L'opérateur `...` fait reférence à l'opérateur de décomposition, dependant de comment il est utilisé.

Lorsqu'il est utilisé avec un itérable, il agit comme un «propagateur» en éléments décomposés individuellement:

```javascript
function foo(x, y, z) {
    console.log(x, y, z);
}

let arr = [1, 2, 3];
foo(...arr); // 1 2 3
```

L'autre usage commun de `...` est la collecte d'un ensemble de valeurs contenu dans un tableau. Dans ce cas, il agit en tant qu'opérateur «de repos».

```javascript
function foo(...args) {
    console.log(args);
}
foo(1, 2, 3, 4, 5); // [1, 2, 3, 4, 5]
```

<br>

### 5. Évolution des objects littéraux

ES6 permet de déclarer les objets littéraux en fournissant une syntaxe abrégée pour initialiser les propriétés à partir des variables et définir des méthodes. Il permet également la possibilité d' avoir des clés de propriété calculées dans une définition littérale de l'objet.

```javascript
function getCar(make, model, value) {
    return {
        // avec la syntaxe abrégée de l'assignation 
        // de la valeur d'une propriété,
        // vous pouvez omettre cette assignation
        // si la valeur à associer à la clé est la même 
        // que celle de l'argument de même nom
        make,  // équivaut à  make: make
        model, // équivaut à model: model
        value, // équivaut à value: value

        // les valeurs calculées fonctionnent maintenant avec
        // objets littéraux
        ['make' + make]: true,

        // la syntaxe abrégée de la définition d'une méthode
        // omet le mot clé `function` et le (`:`) utilisé comme séparateur
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

### 6. Octal et Binaire littéraux

ES6 a un nouveau support pour octal et binaire littéraux.
Prefixer un nombre avec `0o` ou `0O` le convertira en valeur octal. Jetez un coup d'oeil sur le code suivant:

```javascript
let oValue = 0o10;
console.log(oValue); // 8

let bValue = 0b10; // 0b or 0B pour le binaire
console.log(bValue); // 2
```

<br>

### 7. Destructuration de tableau et d'objet

L'affectation par décomposition (*Destructuring* en anglais) de tableau et d'objet aide à éviter la nécessité des variables temporaires lorsque vous traitez avec les objets et les tableaux.

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

### 8. Le mot clé super dans les objets

ES6 permet d'utiliser la méthode `super` dans les objets (normaux) avec des prototypes. Voici un exemple simple:

```javascript
var parent = {
    foo() {
        console.log("Hello de la part du Parent");
    }
}

var child = {
    foo() {
        super.foo();
        console.log("Hello de la part du fils");
    }
}

Object.setPrototypeOf(child, parent);
child.foo(); // Hello de la part du Parent
             // Hello de la part du fils
```

<br>

### 9. Modèle littéral et délimitateurs

ES6 introduit un moyen plus facile d'ajouter des interpolations qui sont évaluées automatiquement.

* <code>\`${ ... }\`</code> est utilisé pour évaluer les variables.
* <code>\`</code> le tick retour est utilisé comme séparateur.

```javascript
let user = 'Kevin';
console.log(`Hey ${user}!`); // Hey Kevin!
```

<br>

### 10. for...of vs for...in
* `for...of` itère sur des objets itérables, tels que les tableaux.

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

* `for...in` itère sur toutes les propriétés énumérables d'un objet.

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

### 11. Map et WeakMap

ES6 introduit une nouvelle série de structures de données appelée `Map` et `WeakMap`. Actuellement, nous utilisons les «collections» en JavaScript tout le temps. En fait, chaque objet peut être considéré comme un `Map`.

Un objet est composé de clé (particulièrement en chaines de caractère) et de valeur, alors que avec `Map`, toute valeur (objets et valeurs primitives) peut être utilisé comme une clé ou une valeur. Jetez un oeil à ce morceau de code:

```javascript
var myMap = new Map();

var keyString = "une chaine",
    keyObj = {},
    keyFunc = function() {};

// setting the values
myMap.set(keyString, "valeur associée avec 'une chaine'");
myMap.set(keyObj, "valeur associée avec keyObj");
myMap.set(keyFunc, "valeur associée avec keyFunc");

myMap.size; // 3

// getting the values
myMap.get(keyString);    // "valeur associée avec 'une chaine'"
myMap.get(keyObj);       // "valeur associée avec keyObj"
myMap.get(keyFunc);      // "valeur associée avec keyFunc"
```

**WeakMap**

Un `WeakMap` est un Map dans lequel les clés sont faiblement référencées, ce qui n'empêche pas ses clés d'être favorable au «ramasse-miettes» (*garbage-collected* en anglais). Cela signifie que vous n'avez pas à vous soucier des fuites de mémoire (*mémoire surutilisé suite à des zone non libérée*).

Une autre chose à noter avec `WeakMap` est que, à l'opposé de `Map` *chaque clé doit être un objet*.

Un `WeakMap` a seulement quatre méthodes `delete(key)`, `has(key)`, `get(key)` et `set(key, value)`.

```javascript
let w = new WeakMap();
w.set('a', 'b');
// Uncaught TypeError: valeur invalide utilisé comme clé de weakMap

var o1 = {},
    o2 = function(){},
    o3 = window;

w.set(o1, 37);
w.set(o2, "azerty");
w.set(o3, undefined);

w.get(o3); // undefined, parceque c'est une valeur contenue dans w

w.has(o1); // true
w.delete(o1);
w.has(o1); // false
```

<br>

### 12. Set et WeakSet

Les objets `Set` sont des collections de valeur unique. Les valeurs en double sont ignorées, puisque toutes les valeurs de la collection doivent être uniques. Les valeurs peuvent être de types primitifs ou des reférences d'objets.

```javascript
let mySet = new Set([1, 1, 2, 2, 3, 3]);
mySet.size; // 3
mySet.has(1); // true
mySet.add('chaines');
mySet.add({ a: 1, b:2 });
```

Vous pouvez parcourir un ensemble (`Set`) par ordre d'insertion en utilisant soit la méthode `forEach` ou la boucle `for...of`.

```javascript
mySet.forEach((item) => {
    console.log(item);
    // 1
    // 2
    // 3
    // 'chaines'
    // Object { a: 1, b: 2 }
});

for (let value of mySet) {
    console.log(value);
    // 1
    // 2
    // 3
    // 'chaines'
    // Object { a: 1, b: 2 }
}
```
Un *Set* à aussi les méthodes `delete()` et `clear()`.

**WeakSet**

Similaire à `WeakMap`, les objets `WeakSet` vous permettent de stocker les *objets* faiblement organisés dans une collection. Un objet dans un `WeakSet`  ne s'y trouve q'une seule fois; il est unique dans la collection.

```javascript
var ws = new WeakSet();
var obj = {};
var foo = {};

ws.add(window);
ws.add(obj);

ws.has(window); // true
ws.has(foo);    // false, foo n'a pas été ajouté à ws

ws.delete(window); // supprimer window de ws
ws.has(window);    // false, window à été retiré de ws
```

<br>

### 13. Les classes en ES6

ES6 introduit une nouvelle syntaxe, les classes. Une chose à noter ici est qu'une classe en ES6 n'est pas un nouveau modèle d'héritage orienté objet. elles servent de syntaxe beaucoup plus adaptée au prototype d'héritage existant à la base en JavaScript.

Une façon de voir ce qu'est une classe en ES6 est qu'elle est juste une nouvelle syntaxe pour travailler avec les prototypes et les contructeurs propre aux fonctions que nous utilisions en ES5.

Les fonctions definient en utilisant le mot clé `static` dans une classe sont considérées comme des  fonctions statiques ou de classes.

```javascript
class Task {
    constructor() {
        console.log("tache instanciée!");
    }
    
    showId() {
        console.log(23);
    }
    
    static loadAll() {
        console.log("chargement de tout les taches..");
    }
}

console.log(typeof Task); // function
let task = new Task(); // "tache instanciée!"
task.showId(); // 23
Task.loadAll(); // "chargement de tout les taches.."
```

**_extends_ et _super_ dans les classes**

Considérant le code suivant:

```javascript
class Car {
    constructor() {
        console.log("Création d'une nouvelle voiture");
    }
}

class Porsche extends Car {
    constructor() {
        super();
        console.log("Création d'une Porsche");
    }
}

let c = new Porsche();
// Création d'une nouvelle voiture
// Création d'une Porsche
```

`extends` permet à la classe enfant d'hériter de la classe parent en ES6. Il est important de noter que le constructeur dérivé doit appeler `super()`.

De même, vous pouvez appeler la méthode de la classe parent dans les méthodes de la classe enfant à l'aide de `super.methodeParente()`

[En savoir plus sur les classes ici](https://developer.mozilla.org/fr/docs/Web/JavaScript/Reference/Classes)

quelques particularités à garder à l'esprit:

* Les déclarations des classes ne sont pas remontées. Vous devez d'abord déclarer votre classe, puis y accéder, sinon une erreur de reférence (`ReferenceError`) sera lévée.
* Il n'est pas necessaire d'utiliser le mot clé `function` pour définir une fonction à l'intérieur de la définition d'une classe.

<br>

### 14. Symbol

Un `symbol` (symbole) est un type de données uniques et immuables introduit en ES6. Le but d'un symbole est de générer un identifiant unique, mais on ne peut avoir accès à cet identifiant.

Voici comment vous créez un symbole:

```javascript
var sym = Symbol("une description optionnelle");
console.log(typeof sym); // symbol
```

Notez que vous ne pouvez pas utiliser `new` avec `Symbol(…)`.

Si un symbole est utilisé comme une propriété/clé d'un objet, il est stocké d'une manière spéciale que la propriété ne sera pas affiché dans une énumération normale des propriétés de l'objet.

```javascript
var o = {
    val: 10,
    [Symbol("random")]: "Je suis un symbole",
};

console.log(Object.getOwnPropertyNames(o)); // val
```

Pour récupérer les propriétés symboles d'un objet, utilisez `Object.getOwnPropertySymbols(o)`


<br>

### 15. Itérateurs

Un itérateur accède aux éléments d'une collection un à la fois, tout en gardant une trace de sa position actuelle au sein de cette séquence. Il fournit une methode `next()` qui retourne l'élément suivant dans la séquence. Cette méthode retourne un objet avec deux propriétés: *done* et *value*.

ES6 a `Symbol.iterator` qui spécifie l'itérateur par défaut pour un objet. À chaque fois qu'un objet doit être itéré (par exemple, au début d'une boucle *for..of*), sa methode *@@iterator* est appelée sans arguments, et l'itérateur retourné est utilisé pour obtenir les valeurs à itérer.

Regardons un tableau, qui est un itérable, et l'itérateur qu'il peut produire pour parcourir ses valeurs:

```javascript
var arr = [11,12,13];
var itr = arr[Symbol.iterator]();

itr.next(); // { value: 11, done: false }
itr.next(); // { value: 12, done: false }
itr.next(); // { value: 13, done: false }

itr.next(); // { value: undefined, done: true }
```

Notez que vous pouvez écrire des itérateurs personalisés en définissant `obj[Symbol.iterator]()` avec la définition de l'object.

<br>

### 16. Générateurs

Les fonctions génératrices sont une nouvelle fonctionnalité en ES6 qui permettent à une fonction de générer de nombreuses valeurs au fil du temps en retournant un objet qui peut être itéré pour recupérer des valeurs de la fonction une valeur à la fois.

une fonction genératrice retourne un **objet itérable** quand elle est appelée.
Elle est écrite en utilisant la nouvelle syntaxe `*` ainsi que le nouveau mot clé `yield` (rendement) introduit en ES6.

```javascript
function *infiniteNumbers() {
    var n = 1;
    while (true) {
        yield n++;
    }
}

var numbers = infiniteNumbers(); // retourne une objet itérable

numbers.next(); // { value: 1, done: false }
numbers.next(); // { value: 2, done: false }
numbers.next(); // { value: 3, done: false }
```

Chaque fois que *yield* est appelé, la valeur rendu devient la valeur suivante dans la séquence.

Notez également que les générateurs calculent leurs valeurs obtenues à la demande, ce qui leur permet de représenter efficacement les séquences qui sont coûteux à calculer, ou même les séquences infinies.

<br>

### 17. Promesses

ES6 a un support natif pour les promesses (*Promises* en anglais). Une promesse est un objet qui est en attente d'une opération asynchrone pour terminer, et lorsque cette opération est terminée, la promesse est soit remplie (résolue) ou rejetée.

La méthode standard pour créer une promesse est l'utilisation du constructeur `new Promise()` qui accepte un gestionnaire à qui est donné deux fonctions en tant que paramètres. La première fonction (typicallement nommée `resolve`) est une fonction à appeler quand la valeur future est prêt; et la seconde (typicallement nommée `reject`) est une fonction à appeler pour rejeter la promesse si elle ne peut resoudre la valeur future.

```javascript
var p = new Promise(function(resolve, reject) {  
    if (/* condition */) {
        resolve(/* value */);  // résolu avec succès
    } else {
        reject(/* reason */);  // erreur, rejetée
    }
});
```

Toute promesse a une méthode nommée `then` qui prend une paire de fonction de rappel. La première est appelée si la promesse est résolue, alors que la seconde est appelée si la promesse est rejetée.

```javascript
p.then((val) => console.log("Promise Resolved", val),
       (err) => console.log("Promise Rejected", err));
```

Une valeur de la fonction de rappel retournée via `then` sera passée au prochain `then` pour sa fonction de rappel.

```javascript
var hello = new Promise(function(resolve, reject) {  
    resolve("Bonjour");
});

hello.then((str) => `${str} le Monde`)
     .then((str) => `${str}!`)
     .then((str) => console.log(str)) // Bonjour le Monde!
```

Lors du retour d'une promesse, la valeur résolue de la promesse sera passé à la prochaine fonction de rappel pour effectivement créer une chaine entre eux.
Ceci est une technique simple pour éviter «l'utilisation des fonctions de rappel qui n'en finit pas».

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
