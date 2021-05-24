---
title: "References and Borrowing"
date: 2021-02-10
description: "A note about references and borrowing in Rust"
tags: ["Rust"]
---

## References in Rust

References allow you to refer to some value without taking ownership of it
```rust
fn main() {
    let s1 = String::from("hello");
    let len = calculate_length(&s1); // Reference

    println!("The length of '{}' is {}.", s1, len);
}

fn calculate_length(s: &String) -> unsize {

    s.len()
}
```

Just as variables are **immutable** by default, so are references. We're not allowed to modify something we have a reference to.


## Mutable References

```rust
fn main() {
    let mut s = String::from("hello");

    change(&mut s);
}

fn change(some_string: &mut String) {
    some_string.push_str(",world");
}
```

First, we had to change `s` to be `mut`. Then we had to create a **mutable reference** with `&mut s`.

You can have only **one** mutable reference to a particular piece of data in a particular scope.

```rust
let mut s = String::from("hello");

let r1 = &mut s;
let r2 = &mut s; // error: cannot borrow 's' as mutable more than once at a time

println!("{}, {}", s1, s2);
```

The benefit of having this restriction is that Rust can prevent **data races** at compile time. A data race is similar to a race condition and happends whwn these three behaviors occur:

* **Two** or more pointers access the same data **at the same time**.
* At least one of the pointers is being used to **write** to the data.
* There's no mechanism being used to **synchronize** access to the data.


Example code 0:
```rust
let mut s = String::from("hello");

let r1 = &s; // no problem
println!("{}", r1);

let r2 = &mut s; // BIG PROBLEM // error
println!("{}", r1);
```

Example code 1:
```rust
let mut s = String::from("hello");

let r1 = &s; // no problem
println!("{}", r1);

let r2 = &mut s; // no problem
println!("{}", r2);
```

## Reference Articles
* [References and Borrowing](https://doc.rust-lang.org/book/ch04-02-references-and-borrowing.html)
