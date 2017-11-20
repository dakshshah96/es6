# What's new in ES6?

## Table of Contents

1. Variables — `let`, `const`, `var`

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