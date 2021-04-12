rust_memo_00.md

## mutable and immutable
```rust
let foo = bar;
```
This line creates a new variable named `foo` and binds it to the value of the `bar` variable.  

`foo` という名前の変数を `bar` の値に**束縛**する.

```rust
let foo = 42; // immutable
let mut bar = 42; // mutable
```
In Rust, variables are **immutable** by default.  
You can use `mut` before the variable name to make a variable mutable.  

Rust では変数はデフォルトで**不変** (イミュータブル, immutable).  
変数名の前に `mut` を記述することで変数を**可変** (ミュータブル, mutable) にできる.





## `::`
```rust
let mut str = String::new();
```

The `::` syntax in the `::new` line indicates that `new` is an **associated function** of the `String` **type**.  
An associated function is implemented on a type, in this case `String`, rather than on a particular instance of a `String`. SOme languages call this a **static** method.

The `let mut str = String::new();` line has created a mutable variable that is currently bound to a new, empty instance of a `String`.

## `io::stdin()`
```rust
io::stdin()
	.read_line(&mut str)
	.expect("Failed to read line");
```

The `std::io::stdin` function returns an instance of `std::io::Stdin`, which is a **type** that represents a handle to the standard input for your terminal.  

The next part of the code, `.read_line(&mut guess)`, calls the `read_line` method on the standard input handle to get input from the user.


