---
title: "Rust のエラーハンドリング"
description: "std::error::Error を使う"
date: 2021-05-07
tags: ["Rust"]
---

Rust でエラーハンドリングするときのメモ.

## `std::error::Error` を利用したエラーハンドリング

Rust で自分でエラーを定義することがある. そのような場合、 `std::error::Error` を実装することで他のエラーと統一してハンドリングできて楽.

`Error` トレイトは `Debug` トレイトと `Display` トレイトを前提とするので、それも実装する必要がある.

```rust
use std::fmt;
use std::error;
use std::time;

// ## エラーの実装例
// ------------------------------------

// 自分のエラーは enum で実装するのがよい.
#[derive(Debug)] // `Debug` トレイトの簡単な実装 (`Display` のように内部の関数を実装することも可能)
enum MyError {
	NoFoo,
	NoBar
}

// `Display` トレイトの実装
impl fmt::Display for MyError {
	fn fmt(&self, f: &mut fmt::Formatter<'_>) -> fmt::Result {
		match self {
			Self::NoFoo => write!(f, "Error: no foo"),
			Self::NoBar => write!(f, "Error: no bar"),	
		}
	}
}

// `Error` トレイトの簡単な実装.
// 自分で内部の関数を実装することも可能.
impl error::Error for MyError {}

// ----------------------------------

// ## 実際の呼び出し
// ----------------------------------

// #### 1. 単一種類のエラーを返す場合
// `MyError` を用いてエラーを返す関数
fn ret_myerror() -> Result<(), MyError> {
	Err(MyError::NoFoo)
}

// #### 2. 複数種類のエラーを返す場合
// `MyError` は `Error` トレイトを実装しているので、他のエラーとまとめて返すことが可能.
// 
// `Error` トレイトを実装した任意の型を返すため、返すエラーは `Box<dyn error::Error>`.
// `Box` を使うのは 「トレイトを実装した任意の型」のサイズがコンパイル時に確定しないため.
// `dyn` でトレイトであることを明示.
// (`Box<dyn Error>` を用いるのは `Error` トレイトで他のエラーとまとめるときだけでよい)
fn do_something() -> Result<(), Box<dyn error::Error>> {
	// 雑なランダム整数 (0 から 9)
	let simple_rand = time::SystemTime::now()
		.duration_since(time::SystemTime::UNIX_EPOCH)? // <- may return Error
		.as_secs() % 10;
	
	if simple_rand == 0 {
		ret_myerror()?; // <- return Error
	}
		
	Ok(())
}


fn main() {
	do_something();
}

```
[Playground](https://play.rust-lang.org/?version=stable&mode=debug&edition=2018&gist=f0f42e74a5cdd795827393d977fed6c5)

#### まとめ
自分で定義したエラーに対して `std::error::Error` トレイトを実装することで柔軟なエラーハンドリングが可能になる.

#### 参考
- [std::error::Error](https://doc.rust-lang.org/std/error/trait.Error.html)
- [Custom Error Types](https://learning-rust.github.io/docs/e7.custom_error_types.html)

## 外部クレートを用いたエラーハンドリング

`anyhow` や `thiserror` を使う.  
やる気があれば書く

#### 参考 (ここを見れば ok)
- [anyhow](https://github.com/dtolnay/anyhow)
- [thiserror](https://github.com/dtolnay/thiserror)


