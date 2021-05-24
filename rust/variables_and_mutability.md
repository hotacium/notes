---
title: "Variables and Mutability"
date: 2021-02-09
description: "A note about variables and constants in Rust"
tags:["Rust"]
---

## Differences Between Variables and Constants
```rust
let hoge = 42 // immutable variable
let mut fuga = 42 // mutable variable
const PIYO: u32 = 42 // constant
```
Like immutable variables, constants are values that are bound to a name and are not allowed to change, but there are a few differences between constants and variables.

#### Features of constant
1. Constants are **always** immutable.
2. The type of the value **must** be annotated.
3. Constants can be declared in any scope, including the global scope.
4. Constants may be set only to a constant expression, not the result of a function call or any other value that could only be computed at runtim

## Shadowing
```rust
fn main() {
    let x = 5; // immutable variable
    let x = x + 1; // shadowing
    let x = x * 2; // shadowing
    let x = "Hello"; // shadowing
}
```

In Rust, you can declare a **new** variable with the **same** name as a previous variable, and the new variable **shadows** the previous variable.  

On the other hand, a mutable variable is not allowed to mutate its type:
```rust
let mut spaces = "   ";
spaces = speces.len(); //  Error: mismatched types
```

## Reference
* [Variables and Mutability](https://doc.rust-lang.org/book/ch03-01-variables-and-mutability.html)
