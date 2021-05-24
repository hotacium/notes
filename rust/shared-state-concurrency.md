---
title: "Shared-State Concurrency in Rust"
date: 2021-02-13
description: "A note about communicating by sharing memory in Rust"
tags: ["Rust"]
---

> Do not communicate by sharing memory
from [Share Mmoery by Communicating](https://blog.golang.org/codelab-share)

Rust provides a safe way to share memory, though it is confusing.
## Example
`Arc` is a thread-sefe reference-counting pointer. It provides shared ownership. When all the `Arc` pointer to a given allocation is destroyed, the value stored in that allocation is also dropped.

`Mutex` is a mechanism that enforces limits on accesss to a resource (in this case, memory) when there are many threads of exection. 

```rust
use std::sync::{Arc, Mutex};
use std::thread;

fn main() {
    /*
        Arc: to share memory among threads (read only)
        Mutex: to lock and edit shared memory safely (write)
    */
    let counter = Arc::new(Mutex::new(0));
    let mut handles = vec![];

    for _ in 0..10 {
        
        // create a new reference using Arc,
        // we need Arc::clone to share memory of counter
        let counter = Arc::clone(&counter);
        
        // 
        let handle = thread::spawn(move || {
            // lock counter's memory to edit the shared memory
            let mut num = counter.lock().unwrap();

            *num += 1;
        });
        handles.push(handle);
    }

    for handle in handles {
        handle.join().unwrap();
    }

    println!("Result: {}", *counter.lock().unwrap());
}

```

Result:
```
Result: 10
```


## References
* [std::sync::Mutex](https://doc.rust-lang.org/std/sync/struct.Mutex.html)
* [std::sync::Arc](https://doc.rust-lang.org/std/sync/struct.Arc.html)
* [Shared-State Concurrency](https://doc.rust-lang.org/book/ch16-03-shared-state.html)
* 
