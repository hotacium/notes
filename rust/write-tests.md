---
title: "テストを書く"
description: "Rust でユニットテストをする"
date: 2021-04-30
tags: ["Rust"]
---


## 実行方法

何も考えずテストの実行
```sh
$ cargo test
```

print デバッグと並行したい場合、
```sh
$ cargo test -- --nocapture
```
`--nocapture` フラッグを用いることで標準出力が表示されるようになる.

## 基本的な書き方

```rust
// cargo test のときのみテストをコンパイルをするよう注釈を書く
#[cfg(test)] 
mod tests {

    // 同ファイル内の機能を test モジュールにインポート
    use super::*; 

    // test 関数を示す注釈 
    // tests モジュールの中にはテスト関数以外の関数をいれることができるため、識別のために必要
    #[test] 
    fn test_something() {
        // do something
    }
    
    // 適切にパニックを起こすかをテスト -> should_panic
    #[test]
    #[should_panic]
    fn test_panic() {
        // do something wrongly
    }
}

```

## `assert` 群
各種 `assert` を用いることでプログラムの実行状態が想定通りかテストできる. 

## 参考
* [Unit testing](https://doc.rust-lang.org/rust-by-example/testing/unit_testing.html)
* 
