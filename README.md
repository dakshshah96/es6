# What's new in ES6?

## Table of Contents

1. The `var` keyword
2. `let` and `const` variables

## Notes

### The `var` keyword

* Variables declared with the `var` keyword are function scoped.
* They can be updated as well as redefined.
* Variables created for use only in a particular block (for example, _if_ blocks) are leaked outside the block.

### `let` and `const` variables

* Variables declared with `let` and `const` are block scoped.
* Such variables cannot be redeclared in the same scope.
* Unlike `let` variables, `const` variables cannot be updated.
* `const` variables are however not immutable (for example, properties of `const` objects can be updated as long as the object itself remains the same).

* You can use `Object.freeze(obj);` to freeze an object.