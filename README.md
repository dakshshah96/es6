# What's new in ES6?

## Table of Contents

1. Variables — `let`, `const`, `var`
2. Arrow functions

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