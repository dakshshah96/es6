# What's new in ES6?

## Table of Contents

1. Variables — `let`, `const`, `var`
2. Arrow functions
3. Template strings
4. New string methods
5. Destructuring assignment
6. The for-of loop
7. New array methods
8. Spread operator (...)
9. Rest parameters (...)
10. Object literal upgrades

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
function readThere (){
    // there used before it is declared (it is not executed)
    return there;
}
let there = 'dragons';
console.log(readThere());
// <- 'dragons'
```

#### Best practices

* Use `const` by default
* Only use `let` if rebinding is needed
* `var` shouldn't be used in ES6
* Use `Object.freeze(obj);` to make an object immutable. **Not** `const`.

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

```js
<p>I'm p 01</p>
<p>I'm p 02</p>
<p>I'm p 03</p>
<p>I'm p 04</p>
<p>I'm p 05</p>

const ps = document.querySelectorAll('p');
for (const paragraph of ps) {
    paragraph.addEventListener('click', function() {
        console.log(this.textContent);
    });
}
```

#### Iterating over objects

* for-of cannot be used to iterate directly over objects.
* Alternative methods such as a for-in loop has to be used.
* `Object.entries()` will be implemented in ES2017 making it possible to iterate over objects just like you would over an array.

### 7. New array methods

#### `Array.from()` and `Array.of()`

* They're on `Array` and not on the array prototype.
* The `Array.from()` method creates a new `Array` instance from an array-like or iterable object:

```js
<div class="people">
    <p>Daksh</p>
    <p>Mark</p>
    <p>Jeff</p>
</div>

// people is an array-like NodeList but doesn't have all prototype methods of an array
const people = document.querySelectorAll('.people p');
// peopleArray is a true Array now
// Array.from() also takes a map function as an optional argument
const peopleArray = Array.from(people, person => {
    console.log(person);
    return person.textContent;
});
```

* The `Array.of()` method creates a new `Array` instance with a variable number of arguments, regardless of number or type of the arguments:

```js
const ages = Array.of(12, 5, 23, 67, 89);
console.log(ages);
// -> [12, 5, 23, 67, 89]
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