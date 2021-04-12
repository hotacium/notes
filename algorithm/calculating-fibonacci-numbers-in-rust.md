---
title: "Fibonacci numbers"
description: "Fibonacci numbers in Rust"
date: 2021-02-15
tags: ["Algorithm", "Rust"]
---

## Recursive Procedures

```rust
fn main() {

    let num = 10;
    
    let ans = fib(num);
    
    println!("fib({}) = {}", num, ans); 
}

fn fib(num: i32) -> i32 {
    // recursive processing
    fib(num - 1) + fib(num - 2)
}
```

`fib1` keeps running forever (**infinite loop**).

So we have an error when we try running the code.
```
thread 'main' has overflowed its stack
fatal runtime error: stack overflow
[1]    233046 abort (core dumped)  cargo run
```

## Base Case
We need base cases to stop recursion. In the case of the Fibonacci numbers, we have two base case `fib(0) = 0` and `fib(1) = 1`.

```rust
fn fib(num: i32) -> i32 {
    match num {
        // base cases (no more recursion)
        0 => 0,
        1 => 1,
        // recursive procedure
        _ => fib1(num - 1) + fib1(num - 2),
    }
}
```

## Memoization 
The `fib()` returns correct results when it is called with small numbers. However, it will never finish running when we try calling `fib(100)`.

This is because every call to `fib()` calls two more `fib()`. The call tree grow **exponentially**.

**Memoization** is a technique in which you store the result of computaional tasks. When we need them again, we can look them up instead of computing them.

We can use `HashMap` for mamoization purposes in Rust. `HashMap`s store keys with associated values. The average time to search for a key in a `HashMap` is **O(1)** (far faster than calculating the value).

```rust
use std::collections::HashMap;


fn main() {
    // hashmap
    let mut memo = HashMap::<i32, i128>::new();
        
    let num = 100;
    
    let ans = fib(num, &mut memo);
    
    println!("fib({}) = {}", num, ans);
}

fn fib(num: i32, memo: &mut HashMap<i32, i128>) -> i128 {
    match memo.get(&num) {
        // if the hash map contains the inserted value with num:
        Some(value) => {
            *value
        }
        // else, calculate the fibonacci number.
        _ => calc_fib(num, memo)
    }
}

fn calc_fib(num: i32, memo: &mut HashMap<i32, i128>) -> i128 {
    match num {
        // base cases (no more recursion)
        0 => 0,
        1 => 1,
        // recursive procedure
        _ => {
            let res = fib(num - 1, memo) + fib(num - 2, memo);
            memo.insert(num, res);
            res
        }
    }
}
```


#### Illustration
```
We need fib(n). 

If we have a memo about fib(n):
    -> return the known fib(n)

Else:
    -> compute and return the unknown fib(n)
    -> store the fib(n) for future use
```
## References
* Introduction to Algorithms, Third Edition
* Classic Computer Science Problems in Python
