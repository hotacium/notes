---
title: "ソースコードを分割する"
description: "ソースコードを複数ファイルに分割したい"
date: 2021-04-23
tags: ["Rust"]
---


例:
```sh
% tree src 
src
├── codegenerator.rs
├── lexer.rs
├── main.rs
├── node.rs
└── utils.rs
```

ソースコードを複数に分割する

## 1. 使うモジュールを宣言
`main.rs` か `lib.rs` に `mod` を宣言.

#### 例
main.rs
```rust
// main.rs

pub mod lexer;
pub mod node;
pub mod codegenerator;
pub mod utils;
```

ファイルBでファイルAのコードを使う場合 (e.g., `codegenerator.rs` で `node.rs` のコードを使う)、 `pub mod` で公開する必要がある. `mod` のみの場合、使えるのは `main.rs` (or `lib.rs`) の内部だけとなる.

## 2. `use` でインポート
使うものをインポートする. インポートするときは `use crate::{module name}::Foo;` のように、 `crate` とモジュールの名前が必要. `crate` は内部のモジュールであることを示す.

#### 例

main.rs
```rust
// main.rs

pub mod lexer;
pub mod node;
pub mod codegenerator;
pub mod utils;

// 内部クレートの中の codegenerator の中の CodeGenerator を使う.
use crate::codegenerator::CodeGenerator;

// 以下略 *snip*
```

codegenerator.rs
```rust
// codegenerator.rs

// main.rs で pub mod をしたため、codegenerator.rs からでも node.rs, lexer.rs のコードを使うことができる.
use crate::node::{Node, NodeKind};
use crate::lexer::Input;

// 以下略 *snip*
```


## モジュールをディレクトリに分割したい

下のようにモジュールを更に分割したい.
```sh
% tree src/
src/
├── http
│   ├── client.rs
│   ├── request.rs
│   ├── response.rs
│   └── url.rs
├── http.rs
├── main.rs
├── renderer
│   └── html_parser.rs
├── renderer.rs
├── utils
│   └── consumer.rs
└── utils.rs

3 directories, 10 files
```

#### Example: `src/http/*.rs` を認識させる.

含めたいモジュール名 (= ディレクトリ名) を宣言.

main.rs
```rust
pub mod http;
pub mod utils;

// 以下略 *snip*
```


ディレクトリと同名の `.rs` ファイルをつくり、そこにディレクトリ内のファイル名 (= モジュール名) を宣言.

http.rs
```rust
pub mod client;
pub mod request;
pub mod response;
pub mod url;

// 以下略 *snip*
```


## 参考
- [Separating Modules into Different Files](https://doc.rust-lang.org/book/ch07-05-separating-modules-into-different-files.html)

もっと詳しい解説がありました.
- [Rustのモジュールの使い方 2018 Edition版](https://keens.github.io/blog/2018/12/08/rustnomoju_runotsukaikata_2018_editionhan/)