---
title: "Data Types in Rust"
date: 2021-02-10
description: "A note about data types in Rust"
tags: [Rust]
---

## Integer Types
```rust
fn main() {
    let i = -42; // default type: i32
    let u : u32 = 42;
    let i = 100_000; // _ as a visual separator
    let x = 0xffff; // hex
    let o = 0o777; // octal
    let b = 0b1010_0101; // binary
    let byte = b'A'; // byte (u8 only)
}
```
8-bit, 16-bit, 32-bit, 64-bit, 128-bit length integer are available. Additionaly, the `isize` and `usize` types depend on the **architecture** of running machine. `i32` is the **default** integer type: this type is generally the fastest.

You can use `_` as a visual separator and Rust allow a type suffix, such as `0x`, `0o` and `0b`.

## Floating-Point Types

The default type of floating-point types is `f64` because on modern CPUs it's roughly the same speed as `f32` but is capable of more precision.

```rust
fn main() {
    let x = 2.0; // f64 // double-precision float
    let y : f32 = 2.0; // f32 // single-precision float
}
```

## The Boolen Type
```rust
fn main() {
    let t = true;
    let f: bool = false; // with explicit type annotation
}
```

## The Character Type
Rust's `char` type is four bytes in size and represents a **Unicode Scalar Values**. They range from `U+0000` to `U+D7FF` and `U+E000` to `U+10FFFF`.

```rust
fn main() {
    let c = 'z'; // U+007A
    let z = 'Җ'; // U+0496
    let thinking = '🤔'; // U+1F914
}
```

## The Tuple Type
A tuple is a general way of grouping together a number of values with a **variety** of types into **one compound** type.

```rust
fn main() {
    let tup: (i32, f64, bool) = (42, 3.14, true);
    
    // accessing to each elements
    let (i, pi, b) = tup; // destructing through pattern matching
    let int = tup.0; // using period (.) followed by the index
}
```
We can access a tuple element by destructing through pattern matching or using a period (.).

## The Array Type
Arrays are useful 
1. when you want your data allocated on the **stack** rather than the heap or 
2. when you want to ensure you always have a **fixed** number of elements.
An array isn't as flexible as the vector type, though.

```rust
fn main() {
    let a = [0, 1, 2, 3, 4];
    let a: [i32; 5] = [3, 3, 3, 3, 3]; // [type; num]
    let a = [7; 3]; // == [7, 7, 7] // [val; num]

    // accessing elements
    let first = a[0];
    let second = a[1];
}
```

## Reference
* [Data Types](https://doc.rust-lang.org/book/ch03-02-data-types.html)







































