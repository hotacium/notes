---
title: "Understanding Ownership"
description: "A note about ownership in Rust"
date: 2021-02-10
tags: ["Rust"]
---

## What Is Ownership?
All programs have to manage the way they use a computer's memory while running.

The way of managing a memory:
* **Garbage collection** that constantly looks for no longer used memory as the program runs
* **Explicitly** allocating/freeing of the memory
* A system of **ownership** with a set of rules that the compiler checks at compile time


None of the ownership features slow down your program while it's running.

## The Stack and the Heap
All data stored on the stack must have a **known, fixed** size. Data with an **unknown** size at compule time or a size that might change must be stored on the heap instead.

Pushing to the stack is **faster** than allocating on the heap because the allocator never has to search for a place to store new data. Comparatively, allocating space on the heap requires more work, because the allocator must first find a big enough space to hold the data and then perform bookkeeping to prepare for the next allocation. 

When your code calls a function, the values passed into the funtion and the function's loacl variables get pushed onto the stack. When the function is over, those values get popped off the stack.


#### Problmes that ownership address are:
* Keeping track of what poarts of code are using what data on the heap,
* Minimizing the amount of duplicate data on the heap, and 
* Cleaning up unused data on the heap so you don't run out of space 

```markdown
Summary

#### The Stack
* **Last in, first out** structure
* **Push** to add data, and **pop** to remove data.
* Stored data must have a **known, fixed** size.
* A **pointer** is a known, fixed size, so it is stored on the stack. 

#### The Heap
* Store data with an **unknown size** at compile time or a size that might **change**.
* How the heap works (memory allocation): When you put data on the heap, you request a certain amount of space. The memory allocator finds an empty spot in the heap that is big enough, marks it as being in use, and returns a **pointer**, which is the address of that location. This process is called **allocating** on the heap.
```

## Ownership Rules
Keep these rules in mind:

* Each value in Rust has a variable that's called its **owner**.
* There can only be **one** owner at a time.
* When the owner goes out of scope, the value will be dropped.

## Memory and Allocation

In Rust, the memory is **automatically** returned once the variable that owns it goes out of scope.
```rust
{
    let s = String::from("hello"); // s is valid from this point forward

    // do stuff with s

} // this scope is now over, and s is no longer valid
```

## Ways Variables and Data Interact: Move (Shallow Copy)

```rust
let s1 = String::from("hello");
let s2 = s1; // move
```
When we assign `s1` to `s2`, the `String` data is copied, meaning we copy the **pointer**, the **length**, and the **capacity** that are on the **stack**. We DO NOT copy the data on the heap. It is called as **shallow copy**, **move** in Rust. 

To ensure memory safety, Rust considers `s1` to no longer be valid, and `s2` gets the ownership.

```rust
let s1 = String::from("hello");
let s2 = s1; // the owenership moved to s2

println!("{}, world!", s1); // error: borrow of moved value
```

## Ways Variables and Data Interact: Clone (Deep Copy)

If we do want to deeply copy the **heap** data of the `String`, not just the stack data, we can use a common method called `clone`.

```rust
let s1 = String::from("hello");
let s2 = s1.clone(); // clone

println!("s1 = {}, s2 = {}", s1, s2); // both are valid.
```

## Stack-Only Data: Copy

```rust
let x = 5;
let y = x; // stack copy = deep copy

println!("x = {}, y = {}", x, y); // both are valid
```

Types (such as integers) that have a **known** size at compile time are stored entirely on the **stack**, copies of the actual values are quick to make.

Here are some of the types that are `Copy`:
* All the integer types
* The Boolen type
* All the floaing-point types
* The character type
* Tuples, if they **only** contain types that are also `Copy`. For example, `(i32, i32)` is `Copy`, but `(i32, String)` is not.



## Ownership and Functions

```rust
fn main() {
    let s = String::from("hello"); // s comes into scope

    takes_ownership(s); // s's value moves into the function ...
                        // ... and so is no longer valid

    let x = 5; // x comes into scope

    makes_copy(x); // x would move into the function,
                   // but i32 is Copy, so still valid
} // x goes out of scope, `drop` is called. (s was already moved, so it is not dropped)

fn take_ownership(some_string: String) { // some_string comes into scope
    println!("{}", some_string);
} // some_string goes out of scope 
  // and `drop` is called. The backing memory is freed.

fn make_copy(some_integer: i32) { // some_integer comes into scope
    println!("{}", some_integer);
} // Here, some_integer goes out of scope
```

## References
* [What is Ownership?](https://doc.rust-lang.org/book/ch04-02-references-and-borrowing.html)
