# What's new in ES6?

## Table of Contents

1. [Variables — `let`, `const`, `var`](#1-variables-let-const-var)
2. [Arrow functions](#2-arrow-functions)
3. [Template strings](#3-template-strings)
4. [New string methods](#4-new-string-methods)
5. [Destructuring assignment](#5-destructuring-assignment)
6. [The for-of loop](#6-the-for-of-loop)
7. [New array methods](#7-new-array-methods)
8. [Spread operator (...)](#8-spread-operator-)
9. [Rest parameters (...)](#9-rest-parameters-)
10. [Object literal upgrades](#10-object-literal-upgrades)
11. [Promises](#11-promises)
12. [Symbols](#12-symbols)
13. [Classes](#13-classes)
14. [Generators](#14-generators)
15. [Proxies](#15-proxies)
16. [Sets](#16-sets)
17. [Maps](#17-maps)
18. [Async Await](#18-async-await)
19. [New object methods](#19-new-object-methods)

## Notes

### 1. Variables — `let`, `const`, `var`

#### `var` variables

* Variables declared with the `var` keyword are function scoped.
* `var` variables can be updated as well as redefined.
* Variables created with `var` for use only in a particular block (for example, _if_ blocks) are leaked outside the block.

#### `let` and `const` variables

* Variables declared with `let` and `const` are block scoped.
* `let` and `const` variables cannot be redeclared in the same scope.

#### `let` vs `const`

* `const` does **not** indicate that a value is immutable. A `const` value can definitely change. The only thing that's immutable here is the binding. `const` assigns a value (`{}`) to a variable name (`foo`), and guarantees that no rebinding will happen. Using an assignment operator or a unary or postfix operator on a `const` variable throws a `TypeError` exception.

```js
// perfectly valid ES6 code
const foo = {};
foo.bar = 42;
console.log(foo.bar);
```

#### Temporal dead zone

* With `var` variables, you can only access them as they are defined. Before they are defined, you cannot access the actual value of them, but *you can access the fact that the variable has been created before*.
* If `let` and `const` variables are accessed before they are created, it'll cause an error and break the code. This is the temporal dead zone, where you cannot access a variable before it is defined. This does not mean that they are not hoisted which is proven by this valid piece of code:

```js
function readThere() {
    // there used before it is declared (it is not executed)
    return there;
}
let there = 'dragons';
console.log(readThere()); // "dragons"
```

#### Best practices

* Use `const` by default.
* Only use `let` if rebinding is needed.
* `var` shouldn't be used in ES6.
* Do **not** use `const` to make an object immutable. Use `Object.freeze(obj);`.

### 2. Arrow functions

* Arrow functions are less verbose than traditional function expressions:

```js
const names = ['Amitabh', 'Jaya', 'Abhishek'];

// the old way
const fullNames = names.map(function(name) {
    return `${name} Bachchan`;
});

// the es6 way
const fullNames = names.map(name => `${name} Bachchan`);
```

* Arrow functions are anonymous, making a stack trace relatively harder.
* Parenthesize the body of function to return an object literal expression:

```js
const race = '100m Dash';
const winners = ['Hunter Gath', 'Singa Song', 'Imda Bos'];

const win = winners.map((winner, i) => ({name: winner, race, place: i + 1}));
```

#### Arrow functions and `this`

* Normal functions in JavaScript bind their own `this` value, however the `this`  value used in arrow functions is actually fetched lexically from the scope it sits inside. It has no `this`, so when you use `this` you’re talking to the outer scope.

```js
const box = document.querySelector('.box');
box.addEventListener('click', function() {
    // have to use function keyword here so this contains 'box'
    // if arrow function is used, this will have 'window'
    console.log(this);

    this.classList.toggle('opening');
    setTimeout(() => {
        // if function keyword was used here, this would contain 'window'
        // arrow function fetches this lexically from the outer scope i.e. 'box'
        console.log(this);
        this.classList.toggle('open');
    });
});
```

### Default function arguments

```js
// tax and tip will be be 0.13 & 0.15 if nothing is passed in
function calculateBill(total, tax = 0.13, tip = 0.15) {
    return total + (total * tax) + (total * tip);
}

const totalBill = calculateBill(100);
console.log(totalBill);
```

### When **not** to use arrow functions

* When you really need `this`:

```js
const button = document.querySelector('#pushy');
button.addEventListener('click', function() {
    console.log(this);
    this.classList.toggle('on');
});
```

* When you need a method to bind to an object:

```js
'use strict';
var obj = {
    i: 10,
    b: () => console.log(this.i, this),
    c: function() {
        console.log(this.i, this);
    }
}
obj.b(); // prints undefined, Window {...} (or the global object)
obj.c(); // prints 10, Object {...}
```

* When you need to add a prototype method:

```js
class Car {
    constructor(make, colour) {
        this.make = make;
        this.colour = colour;
    }
}

const beemer = new Car('bmw', 'blue');
const subie = new Car('Subaru', 'white');

Car.prototype.summarize = () => {
    // this.make and this.colour is undefined because we used arrow function here
    return `This car is a ${this.make} in the colour ${this.colour}`;
};
```

* When you need arguments object

```js
const orderChildren = () => {
    // 'arguments' is not defined for arrow functions
    const children = Array.from(arguments);
    return children.map((child, i) => {
        return `${child} was child #${i + 1}`;
    })
    console.log(arguments);
}
```

### 3. Template strings

#### Pop in variables/expressions

```js
const name = 'Snickers';
const age = 2;
const sentence = `My dog ${name} is ${age * 7} years old.`;
console.log(sentence);
```

#### HTML fragments with template literals

```js
const person = {
    name: 'Daksh',
    job: 'Web Developer',
    city: 'Surat',
    bio: 'Daksh is a really cool guy who loves designing stuff for the web!'
};

const markup = `
    <div class="person">
        <h2>
            ${person.name}
            <span class="job">${person.job}</span>
        </h2>
        <p class="location">${person.city}</p>
        <p class="bio">${person.bio}</p>
    </div>
`;

document.body.innerHTML = markup;
```

#### Nest template strings inside each other

```js
const dogs = [
    { name: 'Snickers', age: 2 },
    { name: 'Hugo', age: 8 },
    { name: 'Sunny', age: 1 }
];

const markup = `
    <ul class="dogs">
        ${dogs.map(dog => `<li>${dog.name} is ${dog.age * 7}</li>`).join('')}
    </ul>
`;

document.body.innerHTML = markup;
```

#### if statements (ternary operator) inside template strings

```js
const song = {
    name: 'Dying to live',
    artist: 'Tupac',
    featuring: 'Biggie Smalls'
};

const markup = `
    <div class="song">
        <p>
            ${song.name} - ${song.artist}
            ${song.featuring ? `(Featuring ${song.featuring})` : ''}
        </p>
    </div>
`;
```

#### Render functions for template strings

```js
const beer = {
    name: 'Belgian Wit',
    brewery: 'Steam Whistle Brewery',
    keywords: ['pale', 'cloudy', 'spiced', 'crisp']
};

function renderKeywords(keywords) {
    return `
        <ul>
            ${keywords.map(keyword => `<li>${keyword}</li>`).join('')}
        </ul>
    `;
}

const markup = `
    <div class="beer">
        <h2>${beer.name}</h2>
        <p class="brewery">${beer.brewery}</p>
        ${renderKeywords(beer.keywords)}
    </div>
`;
```

#### Tagged template literals

* Tags allow you to parse template literals with a function. The first argument of a tag function contains an array of string values. The remaining arguments are related to the expressions.
* In the end, your function can return your manipulated string (or it can return something completely different).
* The name of the function used for the tag can be named whatever you want.

```js
// ability to modify the template before it gets assigned to 'sentence'
function highlight(strings, ...values) {
    let str = '';
    strings.forEach((string, i) => {
        // example: span tag with class h1 around all values
        str += `${string} <span class="h1">${values[i] || ''}</span>`;
    });
    return str;
}

const name = 'Snickers';
const age = 100;
// tagged with highlight
const sentence = highlight`My dog ${name} is ${age} years old.`;
document.body.innerHTML = sentence;
console.log(sentence);
```

### 4. New string methods

#### `.startsWith()`

* The `startsWith()` method determines whether a string begins with the characters of a specified string, returning `true` or `false` as appropriate.

```js
// startswith
var str = 'To be, or not to be, that is the question.';

console.log(str.startsWith('To be'));         // true
console.log(str.startsWith('not to be'));     // false
console.log(str.startsWith('not to be', 10)); // true
```

#### `.endsWith()`

* The `endsWith()` method determines whether a string ends with the characters of a specified string, returning `true` or `false` as appropriate.

```js
// endswith
var str = 'To be, or not to be, that is the question.';

console.log(str.endsWith('question.')); // true
console.log(str.endsWith('to be'));     // false
console.log(str.endsWith('to be', 19)); // true
```

#### `.includes()`

* The `includes()` method determines whether one string may be found within another string, returning `true` or `false` as appropriate.

```js
// includes
var str = 'To be, or not to be, that is the question.';

console.log(str.includes('To be'));       // true
console.log(str.includes('question'));    // true
console.log(str.includes('nonexistent')); // false
console.log(str.includes('To be', 1));    // false
console.log(str.includes('TO BE'));       // false
```

#### `.repeat()`

* The `repeat()` method constructs and returns a new string which contains the specified number of copies of the string on which it was called, concatenated together.

```js
// repeat
'abc'.repeat(-1);   // RangeError
'abc'.repeat(0);    // ''
'abc'.repeat(1);    // 'abc'
'abc'.repeat(2);    // 'abcabc'
'abc'.repeat(3.5);  // 'abcabcabc' (count will be converted to integer)
'abc'.repeat(1/0);  // RangeError
```

### `.padStart()` and `.padEnd()`

* The `padStart()` method pads the current string with another string (repeated, if needed) so that the resulting string reaches the given length. The padding is applied from the start (left) of the current string.
* The `padEnd()` method pads the current string with a given string (repeated, if needed) so that the resulting string reaches a given length. The padding is applied from the end (right) of the current string.

```js
const strings = ['short', 'medium size', 'this is really really long'];
const longestString = strings.sort(str => str.length).map(str => str.length)[0];

// all strings aligned right
strings.forEach(str => console.log(str.padStart(longestString)));
```

### 5. Destructuring assignment

#### Object destructuring

* Basic destructuring

```js
const person = {
    first: 'Daksh',
    last: 'Shah',
    country: 'India',
    city: 'Surat',
    links: {
        social: {
            twitter: '@daksh_shah',
            quora: 'Daksh-Shah'
        },
        web: {
            portfolio: 'https://daksh.me'
        }
    }
};

// old way
const first = person.first;
const last = person.last;
const twitter = person.links.social.twitter;
const quora = person.links.social.quora;
const website = person.links.web.portfolio;

// es6 way
const { first, last } = person;
const { twitter, quora } = person.links.social;
const { portfolio: website } = person.links.web;
```

* Setting default values

```js
const settings = { width: 300, color: 'black' } // height, fontSize missing
// renaming width and height also
const { w: width = 100, h: height = 100, color = 'blue', fontSize = 25 } = settings;
```

#### Array destructuring

* Basic destructuring

```js
const details = ['Daksh Shah', 123, 'daksh.me'];

// old way
const name = details[0];
const id = details[1];
const website = details[2];

// es6 way
const [name, id, website] = details;
```

* Swapping variables

```js
let inRing = 'Hulk Hogan';
let onSide = 'The Rock';

// swap them
[inRing, onSide] = [onSide, inRing];
```

#### Destructuring functions

* Sort of return multiple values from a function.
* Return an object and destructure it while calling the function. You don't need to care about the order or the number of values you need from the returned object.

```js
function convertCurrency(amount) {
    const converted = {
        USD: amount * 0.76,
        GPB: amount * 0.53,
        AUD: amount * 1.01,
        MEX: amount * 13.30
    };
    return converted;
}

// old way
const hundo = convertCurrency(100);

// es6 way — return multiple
const { MEX, USD, AUD } = convertCurrency(100);
```

### 6. The for-of loop

* The `for...of` statement creates a loop iterating over iterable objects (including `Array`, `Map`, `Set`, `String`, `TypedArray`, arguments object and so on), invoking a custom iteration hook with statements to be executed for the value of each distinct property.

```js
const cuts = ['Chuck', 'Brisket', 'Shank', 'Short Rib'];

// the old way
for (let i = 0; i < cuts.length; i++) {
    console.log(cuts[i]);
}

// cannot abort or skip loop
cuts.forEach((cut) => {
    console.log(cut);
    if (cut === 'Brisket') {
        continue;
    }
});

// iterates over everything (prototypes, properties, etc.)
for (const index in cuts) {
    console.log(cuts[index]);
}

// for-of loop (iterates only over items)
for (const cut of cuts) {
    if (cut === 'Brisket') {
        continue;
    }
    console.log(cut);
}
```

#### Iterating over array using `entries()`

```js
const cuts = ['Chuck', 'Brisket', 'Shank', 'Short Rib'];

for (const [i, cut] of cuts.entries()) {
    console.log(`${cut} is the ${i + 1} item`);
}
```

#### Iterating over the arguments object

```js
function addUpNumbers() {
    let total = 0;
    for (num of arguments) {
        total += num;
    }
    return total;
}

addUpNumbers(10, 23, 4, 55, 28, 13, 39, 71);
```

#### Iterating over DOM elements

```html
<p>I'm p 01</p>
<p>I'm p 02</p>
<p>I'm p 03</p>
<p>I'm p 04</p>
<p>I'm p 05</p>

<script>
const ps = document.querySelectorAll('p');
for (const paragraph of ps) {
    paragraph.addEventListener('click', function() {
        console.log(this.textContent);
    });
}
</script>
```

#### Iterating over objects

* for-of cannot be used to iterate directly over objects.
* Alternative methods such as a for-in loop has to be used.
* `Object.entries()` will be implemented in ES2017 making it possible to iterate over objects just like you would over an array.

### 7. New array methods

#### `Array.from()` and `Array.of()`

* They're on `Array` and not on the array prototype.
* The `Array.from()` method creates a new `Array` instance from an array-like or iterable object:

```html
<div class="people">
    <p>Daksh</p>
    <p>Mark</p>
    <p>Jeff</p>
</div>

<script>
// people is an array-like NodeList but doesn't have all prototype methods of an array
const people = document.querySelectorAll('.people p');
// peopleArray is a true Array now
// Array.from() also takes a map function as an optional argument
const peopleArray = Array.from(people, person => {
    console.log(person);
    return person.textContent;
});
</script>
```

* The `Array.of()` method creates a new `Array` instance with a variable number of arguments, regardless of number or type of the arguments:

```js
const ages = Array.of(12, 5, 23, 67, 89);
console.log(ages); // [12, 5, 23, 67, 89]
```

#### `.find()` and `.findIndex()`

* The `find()` method returns the **value** of the first element in the array that satisfies the provided testing function. Otherwise `undefined` is returned:

```js
function isBigEnough(element) {
    return element >= 15;
}

[12, 5, 8, 130, 44].find(isBigEnough); // 130
```

* The `findIndex()` method returns the **index** of the first element in the array that satisfies the provided testing function. Otherwise -1 is returned:

```js
function findFirstLargeNumber (element) {
    return element > 13;
}

[5, 12, 8, 130, 44].findIndex(findFirstLargeNumber); // 3
```

#### `.some()` and `.every()`

* The `some()` method tests whether at least one element in the array passes the test implemented by the provided function:

```js
function isBiggerThan10(element, index, array) {
    return element > 10;
}

[2, 5, 8, 1, 4].some(isBiggerThan10);  // false
[12, 5, 8, 1, 4].some(isBiggerThan10); // true
```

* The `every()` method tests whether all elements in the array pass the test implemented by the provided function:

```js
function isBelowThreshold(currentValue) {
    return currentValue <= 40;
}

[1, 30, 40, 29, 10, 13].every(isBelowThreshold); // true
```

### 8. Spread operator (...)

* The spread operator allows an iterable such as an array expression or string to be expanded in places where zero or more arguments (for function calls) or elements (for array literals) are expected, or an object expression to be expanded in places where zero or more key-value pairs (for object literals) are expected.

#### Spreading strings

* This is the simplest use case for the spread operator:

```js
const name = 'daksh';
console.log(...name); // ['d', 'a', 'k', 's', 'h']
```

#### Spreading arrays

* Using a combination of push, splice, concat, etc. is no longer required to construct a new array from multiple arrays:

```js
const featured = ['Deep Dish', 'Peperoni', 'Hawaiian'];
const speciality = ['Meatzza', 'Spicy Mama', 'Margherita'];

// the old way
let pizzas = [];
pizzas = pizzas.concat(featured);
pizzas.push('veg');
pizzas = pizzas.concat(speciality);
console.log(pizzas);

// the es6 way
const pizzas = [...featured, 'veg', ...speciality];
console.log(pizzas);
```

#### Spreading into a function

* It is common to use `Function.prototype.apply` in cases where you want to use the elements of an array as arguments to a function. The spread operater makes that process easier:

```js
const inventors = ['Einstein', 'Newton', 'Galileo'];
const newInventors = ['Musk', 'Jobs'];

// the wrong way --> ['Einstein', 'Newton', 'Galileo', ['Musk', 'Jobs']]
inventors.push(newInventors);
// the old way --> ['Einstein', 'Newton', 'Galileo', 'Musk', 'Jobs']
inventors.push.apply(inventors, newInventors);
// the es6 way --> ['Einstein', 'Newton', 'Galileo', 'Musk', 'Jobs']
inventors.push(...newInventors);
```

### 9. Rest parameters (...)

* The rest parameter syntax allows us to represent an indefinite number of arguments as an array.
* Rest parameters are only the ones that haven't been given a separate name, while the `arguments` object contains all arguments passed to the function.
* The `arguments` object is not a real array, while rest parameters are `Array` instances.

```js
function convertCurrency(rate, tax, tip, ...amounts) {
    console.log(rate, tax, tip, amounts); // 1.54 10 23 [52, 1, 56]
    return amounts.map(amount => amount * rate);
}

convertCurrency(1.54, 10, 23, 52, 1, 56); // [80.08, 1.54, 86.24]
```

### 10. Object literal upgrades

#### Naming object properties

* You can skip writing the property names when the property and variable names are same.

```js
const first = 'oreo';
const last = 'bow';
const age = 2;
const breed = 'Labrador Retriever';

// the old way
const dog = {
    firstName: first,
    last: last,
    age: age,
    breed: breed,
    pals: ['Hugo', 'Sunny']
};

// the es6 way
const dog = {
    firstName: first,
    last,
    age,
    breed,
    pals: ['Hugo', 'Sunny']
};
```

#### Naming object methods

* You can skip writing the `function` keyword while defining object methods.

```js
// the old way
const modal = {
    create: function(selector) { },
    open: function(content) { },
    close: function(goodbye) { }
};

// the es6 way
const modal = {
    create(selector) { },
    open(content) { },
    close(goodbye) { }
};
```

#### Computed property names

* Property key names can now be computed inside object literals while defining them:

```js
const key = 'pocketColor';
const value = '#ffc600';

const tShirt = {
    [key]: value,
    [`${key}Opposite`]: invertColor(value)
};

console.log(tShirt);
// Object {pocketColor: "#ffc600", pocketColorOpposite: "#0039ff"}
```

### 11. Promises

* A `Promise` is an object representing the eventual completion or failure of an asynchronous operation.

#### Attaching callbacks to promises

* Essentially, a promise is a returned object to which you attach callbacks, instead of passing callbacks into a function:

```js
const usersPromise = fetch('https://reqres.in/api/users');

usersPromise
    // .then runs on successful fetch
    .then(data => data.json())
    .then(data => { console.log(data) })
    // .catch runs in case of an error
    .catch((err) => {
        console.error(err);
    });
```

#### Building promises

* A `Promise` object is created using the `new` keyword and its constructor. This constructor takes as its argument a function, called the "executor function".
* `resolve` is called when the asynchronous task completes successfully and returns the results of the task as a value.
* `reject` is called when the task fails, and returns the reason for failure, which is typically an error object.

```js
// executor function takes two args (resolve, reject)
const p = new Promise((resolve, reject) => {
    setTimeout(() => {
        // resolve('Daksh is cool');
        reject(Error('Err Daksh isn\'t cool'));
    }, 1000);
});

p
    // resolve
    .then(data => {
        console.log(data);
    })
    // reject
    .catch(err => {
        console.error(err);
    });
```

#### Chaining promises + flow control

* A common need is to execute two or more asynchronous operations back to back, where each subsequent operation starts when the previous operation succeeds, with the result from the previous step. We accomplish this by creating a *promise chain*.
* Always return promises up, otherwise callbacks won't chain, and errors won't be caught.

```js
doSomething()
    .then(result => doSomethingElse(result))
    .then(newResult => doThirdThing(newResult))
    .then(finalResult => {
        console.log(`Got the final result: ${finalResult}`);
    })
    .catch(failureCallback);
```

#### Multiple promises with `Promise.all()`

* The `Promise.all()` method returns a single `Promise` that resolves when all of the promises in the `iterable` argument have resolved or when the iterable argument contains no promises.
* It rejects with the reason of the first promise that rejects.

```js
const p1 = Promise.resolve(3);
const p2 = 1337;
const p3 = new Promise((resolve, reject) => {
    setTimeout(resolve, 100, 'foo');
});

Promise.all([p1, p2, p3])
    .then(values => {
        console.log(values); // [3, 1337, "foo"]
    });
```

### 12. Symbols

* New primitive data type added to ES6 in addition to `Boolean`, `Null`, `Undefined`, `Number`, `String`.
* Values of this type can be used to make object properties that are anonymous.  This data type is used as the key for an object property when the property is intended to be private, for the internal use of a class or an object type.
* When a symbol value is used as the identifier in a property assignment, the property (like the symbol) is anonymous; and also is non-enumerable.
* The property can be accessed by using the original symbol value that created it, or by iterating on the result array of `Object.getOwnPropertySymbols()`.

```js
const classRoom = {
    [Symbol('Mark')]: { grade: 50, gender: 'Male' },
    [Symbol('Olivia')]: { grade: 23, gender: 'Female' },
    // unique Olivia
    [Symbol('Olivia')]: { grade: 34, gender: 'Female' }
};

// you get nothing
for (person in classRoom) {
    console.log(person);
}

// grab all symbols off of classRoom object (can't see any data though)
const syms = Object.getOwnPropertySymbols(classRoom);
console.log(syms);
// -> [Symbol(Mark), Symbol(Olivia), Symbol(Olivia)]
const data = syms.map(sym => classRoom[sym]);
console.log(data);
// -> [{grade: 50, gender: "Male"}, {grade: 23, gender: "Female"}, {grade: 34, gender: "Female"}]
```

### 13. Classes

#### Prototypal inheritance review

* In JavaScript, any function can be added to an object in the form of a property.
* An inherited function acts just as any other property, including property shadowing.

```js
function Dog(name, breed) {
    // associated with the instance (snickers, sunny)
    this.name = name;
    this.breed = breed;
}
// method on the prototype (Dog)
Dog.prototype.bark = function() {
    console.log(`Bark Bark! My name is ${this.name}`)
}

const snickers = new Dog('Snickers', 'King Charles');
const sunny = new Dog('Sunny', 'Golden Doodle');

snickers.bark();
// -> Bark Bark! My name is Snickers
```

#### Basics of classes

* Classes in ES6 are primarily syntactical sugar over JavaScript's existing prototype-based inheritance. The class syntax is **not** introducing a new object-oriented inheritance model to JavaScript.
* Class syntax has two components: class expressions and class declarations. Class expressions are
* Static methods are called without instantiating their class and **cannot** be called through a class instance. Static methods are often used to create utility functions for an application.

```js
// class declaration
class Dog {
    constructor(name, breed) {
        this.name = name;
        this.breed = breed;
    }
    bark() {
        console.log(`Bark Bark! My name is ${this.name}`);
    }
    cuddle() {
        console.log(`I love you owner!`);
    }
    // static method -> Dog.info();
    static info() {
        console.log('A dog is better than a cat');
    }
    // string returned when dogProperty.description is looked up
    get description() {
        return `${this.name} is a ${this.breed} type of dog`;
    }
    // called when value is assigned to dogProperty.nicknames
    set nicknames(value) {
        this.nick = value.trim();
    }
    // modified string returned when dogProperty.nicknames is looked up
    get nicknames() {
        return this.nick.toUpperCase();
    }
}

const snickers = new Dog('Snickers', 'King Charles');
const sunny = new Dog('Sunny', 'Golden Doodle');
```

#### Extending classes

* The `extends` keyword is used in class declarations or class expressions to create a class which is a child of another class.

```js
// base class
class Animal {
    constructor(name) {
        this.name = name;
        this.thirst = 100;
        this.belly = [];
    }
    drink() {
        this.thirst -= 10;
        return this.thirst;
    }
    eat(food) {
        this.belly.push(food);
        return this.belly;
    }
}

// Dog extended from Animal
class Dog extends Animal {
    constructor() {
        // make an Animal first
        super(name);
        this.breed = breed;
    }
    // additional method
    bark() {
        console.log('Bark bark I\'m a dog');
    }
}

const rhino = new Animal('Rhiney');
const snickers = new Dog('Snickers', 'King Charles');
// eat() is still available
snickers.eat('plastic');
```

#### Extending arrays with classes

* Arrays in ES6 can be extended/modified to have class-like features such as constructors and methods:

```js
class MovieCollection extends Array {
    constructor(name, ...items) {
        super(...items);
        this.name = name;
    }
    add(movie) {
        this.push(movie);
    }
    topRated(limit = 10) {
        return this.sort((a, b) => (a.stars > b.stars ? -1 : 1)).slice(0, limit);
    }
}

const movies = new MovieCollection('Daksh\'s Fav Movies',
    { name: 'The Shawshank Redemption', stars: 10 },
    { name: 'Green Lantern', stars: 1 },
    { name: 'Justice League', stars: 7 },
    { name: 'Good Will Hunting', stars: 8 }
);

console.log(movies.name); // "Daksh's Fav Movies"
movies.add({ name: 'Titanic', stars: 5 });
movies.topRated();
```

### 14. Generators

* Generators are functions which can be exited and later re-entered. Their context (variable bindings) will be saved across re-entrances.
* `yield` can be considered a temporary `return`.
* A generator which has returned (`return`) will not yield any more values.

```js
const inventors = [
    { first: 'Albert', last: 'Einstein', year: 1879 },
    { first: 'Isaac', last: 'Newton', year: 1643 },
    { first: 'Galileo', last: 'Galilei', year: 1564 },
    { first: 'Marie', last: 'Curie', year: 1867 },
    { first: 'Johannes', last: 'Kepler', year: 1571 },
    { first: 'Nicolaus', last: 'Copernicus', year: 1473 },
    { first: 'Max', last: 'Planck', year: 1858 },
];

// define generator function
function* loop(arr) {
    for (const item of arr) {
        yield item;
    }
}

// Generator object returned
const inventorGen = loop(inventors);
// returns first object in array and done status
inventorGen.next(); {value: Object, done: false}
// returns second object in array and done status
inventorGen.next(); {value: Object, done: false}
// returns third object
inventorGen.next().value(); // {first: 'Galileo', last: 'Galilei', year: 1564}
```

#### Using generators for ajax flow control

* Generators can be used for managing waterfall ajax requests:

```js
function ajax(url) {
    fetch(url)
        .then(data => data.json())
        .then(data => dataGen.next(data))
}

function* steps() {
    const beers = yield ajax('http://api.react.beer/v2/search?q=hops&type=beer');
    const daksh = yield ajax('https://api.github.com/users/dakshshah96');
    const fatJoe = yield ajax('https://api.discogs.com/artists/51988');
}

// Generator object created
const dataGen = steps();
// kick it off (starts with beer api)
dataGen.next();
```

#### Looping generators with for-of

```js
function* lyrics() {
    yield `On a dark desert highway, cool wind in my hair`;
    yield `Warm smell of colitas, rising up through the air`;
    yield `Up ahead in the distance, I saw a shimmering light`;
    yield `My head grew heavy and my sight grew dim`;
    yield `I had to stop for the night.`;
}

const achy = lyrics();

// for-of already has next()
for (const line of achy) {
    console.log(line);
}
```

### 15. Proxies

* The `Proxy` object is used to define custom behavior for fundamental operations (e.g. property lookup, assignment, enumeration, function invocation, etc).

```js
const phoneHandler = {
    // clean phone numbers when someone sets it
    set(target, name, value) {
        target[name] = value.match(/[0-9]/g).join('');
    },
    // consistently format phone numbers on get
    get(target, name) {
        return target[name].replace(/(\d{3})(\d{3})(\d{4})/, '($1)-$2-$3');
    }
}

// empty target object {}
const phoneNumbers = new Proxy({}, phoneHandler);
```

### 16. Sets

* `Set` objects are collections of values. You can iterate through the elements of a set in insertion order. A value in the `Set` may **only occur once**; it is unique in the `Set`'s collection.
* Values are not index based and need to accessed via a for-of loop or a generator.

```js
const brunch = new Set();

// people start coming in
brunch.add('Daksh');
brunch.add('Mark');
brunch.add('Jeff');

// ready to open
const line = brunch.values();

// start seating people
console.log(line.next().value);
console.log(line.next().value);

// new people show up
brunch.add('Larry');
brunch.add('Elon');

// seat people
console.log(line.next().value);
console.log(line.next().value);
console.log(line.next().value);
```

#### `WeakSet`

* The `WeakSet` object lets you store weakly held objects in a collection.
* References to objects in the collection are held weakly. If there is no other reference to an object stored in the `WeakSet`, they can be garbage collected. That also means that there is no list of current objects stored in the collection.
* `WeakSets` are not enumerable.

```js
let dog1 = { name: 'Snickers', age: 3 };
let dog2 = { name: 'Sunny', age: 1 };

const weakSauce = new WeakSet([dog1, dog2]);
console.log(weakSauce);

dog1 = null;
// dog1 has been removed
console.log(weakSauce);
```

### 17. Maps

* The `Map` object holds key-value pairs. Any value (both objects and primitive values) may be used as either a key or a value.
* The keys of an `Object` are `Strings` and `Symbols`, whereas they can be any value for a `Map`, including functions, objects, and any primitive.
* A `Map` is an iterable and can thus be directly iterated, whereas iterating over an `Object` requires obtaining its keys in some fashion and iterating over them.

```js
const clickCounts = new Map();
// get all buttons from HTML
const buttons = document.querySelectorAll('button');

buttons.forEach(button => {
    // the key here is the actual button
    clickCounts.set(button, 0);
    button.addEventListener('click', function() {
        // current count
        const val = clickCounts.get(this);
        // update count
        clickCounts.set(this, val + 1);
    });
});
```

#### `WeakMap`

* The `WeakMap` object is a collection of key/value pairs in which the keys are weakly referenced.  The keys must be objects and the values can be arbitrary values.
* References to key objects in the `WeakMap` are held weakly. If there is no other reference to the key object stored in the `WeakMap`, they can be garbage collected. That also means that there is no list of current object keys stored in the collection.
* `WeakMap` keys are not enumerable.

```js
let dog1 = { name: 'Snickers' };
let dog2 = { name: 'Sunny' };

const strong = new Map();
const weak = new WeakMap();

strong.set(dog1, 'Snickers is the best!');
weak.set(dog2, 'Sunny is the 2nd best!');

// strong still has dog1 in it even though it doesn't exist
dog1 = null;
// weak no longer has dog2 in it (garbage collected)
dog2 = null;
```

### 18. Async Await

* Async/Await is built on top of Promises and is compatible with all existing Promise-based APIs.
* `async` automatically transforms a regular function into a `Promise`. When called, async functions resolve with whatever is returned in their body. They also enable the use of `await`.
* When placed in front of a Promise call, `await` forces the rest of the code to wait until that `Promise` finishes and returns a result. Await works only with Promises, it does not work with callbacks and can only be used inside `async` functions.
* A standard `try`/`catch` block is used to handle errors.

```js
var rp = require('request-promise');
// Encapsulate the solution in an async function
async function solution() {
    try {
        const promiseResult = await Promise.reject('Error');
    } catch (e) {
        console.log(e);
    }

    // Wait for the first HTTP call and print the result
    console.log(await rp('http://example.com/'));

    // Spawn the HTTP calls without waiting for them - run them concurrently
    const call2Promise = rp('http://example.com/');  // Does not wait!
    const call3Promise = rp('http://example.com/');  // Does not wait!

    // After they are both spawned - wait for both of them
    const response2 = await call2Promise;
    const response3 = await call3Promise;

    console.log(response2);
    console.log(response3);
}

// Call the async function
solution().then(() => console.log('Finished'));
```

#### Waiting on multiple promises

```js
async function getData(names) {
    // fetch each user and get all promises
    const promises = names.map(name => fetch(`https://api.github.com/users/${name}`)).then(r => r.json());
    // awaiting all promises to resolve
    const people = await Promise.all(promises);
    console.log(people);
}

// pass array of users to fetch
getData(['dakshshah96', 'addyosmani']);
```

#### Async Await vs Promises

* Async/Await does not make Promises obsolete. When working with Async/Await we are still using Promises under the hood. Using a single promise is straightforward. However, when we need to program complicated asynchronous logic, we may end up combining a few promises. Writing all the `then` clauses and anonymous callbacks can easily get out of hand.
* Async/Await doesn't always cut it. For example, when we need to make multiple independent asynchronous calls and wait for all of them to finish. To send all requests at the same time a `Promise.all()` is required. This will make sure we still have all the results before continuing, but the asynchronous calls will be firing in parallel, not one after another.

### 19. New object methods

* The `Object.values()` method returns an array of a given object's own enumerable property values, in the same order as that provided by a `for...in` loop (the difference being that a for-in loop enumerates properties in the prototype chain as well).
* The `Object.entries()` method returns an array of a given object's own enumerable property [key, value] pairs, in the same order as that provided by a `for...in` loop (the difference being that a for-in loop enumerates properties in the prototype chain as well).

```js
const inventory = {
    backpacks: 10,
    jeans: 23,
    hoodies: 4,
    shoes: 11
};

// using Object.keys for nav
const navKeys = Object.keys(inventory); // ["backpacks", "jeans", "hoodies", "shoes"]
const nav = navKeys.map(item => `<li>${item}</li>`).join('');
console.log(nav);

// using the new Object.values() method
const totalInventoryVals = Object.values(inventory); // [10, 23, 4, 11]
const totalInventory = totalInventoryVals.reduce((a, b) => a + b);
console.log(totalInventory); // 48

// using the new Object.entries() method
const inventoryEntries = Object.entries(inventory); // [["backpacks", 10], ["jeans", 23], ["hoodies", 4], ["shoes", 11]]
inventoryEntries.forEach(([key, val]) => {
    console.log(key, val);
});
```