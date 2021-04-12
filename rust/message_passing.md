---
title: "Message Passing in Rust"
description: "A note about message passing among threads in Rust"
date: 2021-02-13
tag: ["Rust"]
---

Module `std::sync::mpsc` provides message-based communication over **channels**.

The `channel` function will return a `(Sender, Receiver)` tuple where all sends will be **asynchronous**


## Example 0

```
# channel illustration

main --> tx --> a channel --> rx --> a thread
```

* `tx.send()` to send data to the channel
* `rx.recv()` to receive data from the channel

```rust
use std::sync::mpsc;
use std::thread;

fn main() {
    // (sender, receiver) = channel
    // tx is for transmission, and rx is for receiving
    let (tx, rx) = mpsc::channel();
    let handle - thread::spawn(move || {
        let data = rx.recv().unwrap();
        println!("{}", data);
    });

    let _ = tx.send("Hello, world!");

    let _ = handle.join();
}
```

## Example 1

```
# illustration
main --> snd_tx --> a channel --> snd_rx --> a thread
main <-- rcv_rx <-- a channel <-- rcv_tx <-- a thread
```

the below code overwrites `data // == [1; 10]` with `2`s

```rust
use std::sync::mpsc;
use std::thread;

fn main() {
    let mut handles = Vec::new();
    let mut data = vec![1; 10];
    // vec to tie/bind channels together
    let mut snd_channels = Vec::new();
    let mut rcv_channels = Vec::new();

    for _ in 0..10 {
        // main -> each thread (send channel)
        let (snd_tx, snd_rx) = mpsc::channel();
        // each thread -> main (receive channel)
        let (rcv_tx, rcv_rx) = mpsc::channel();
        
        snd_channels.push(snd_tx);
        rcv_channels.push(rcv_rx);

        handles.push(thread::spawn(move || {
            // receive data
            let mut data = snd_rx.recv().unwrap();
            data += 1;
            // send data
            let _ = rcv_tx.send(data);
        }));
    }
    
    // send data to each thread
    for x in 0..10 {
        let _ = snd_channels[x].send(data[x]);
    }
    
    // receive data
    for x in 0..10 {
        data[x] = rcv_channels[x].recv().unwrap();
    }
    
    // wait all threads end
    for handle in handles {
        let _ = handle.join();
    }

    dbg!(data);
}
```
