---
title: "Rust で計算式のコンパイラをつくる"
description: "(2 + 4) * 10 / 5 + 3 を構文解析してコンパイルする"
date: 2021-04-04
tags: ["Rust", "Compiler"]
---

## 参考元
参考: [電卓レベルの言語の作成](https://www.sigbus.info/compilerbook#%E3%82%B9%E3%83%86%E3%83%83%E3%83%975%E5%9B%9B%E5%89%87%E6%BC%94%E7%AE%97%E3%81%AE%E3%81%A7%E3%81%8D%E3%82%8B%E8%A8%80%E8%AA%9E%E3%81%AE%E4%BD%9C%E6%88%90)  

上のページのコードを Rust 実装したときのメモです. 素晴らしい教育素材を提供していただき感謝しかないです. 

コンパイラの理論に関しては上のページを見てください.


## 注意

Rust で実装していますが自分の Rust ぢから不足のため **Rust っぽくない**です. これから直します.

## 加減算のできるコンパイラ
とりあえずのターゲットはこのアセンブリ. これを出力します.
```asm
// 5 + 20 - 4 を実行するアセンブリ
.intel_syntax noprefix
.global main

main:
    mov rax, 5
    add rax, 20
    sub rax, 4
    ret // ret では rax が帰る.
```

以下がそのコードです.
```rust
use std::env;
use std::iter::{Iterator, Peekable};
// use anyhow::{anyhow, Result};

// NO ERROR HUNDLING to simplify the code.
fn compile(exp: &str) {
    let mut iter = exp.chars().peekable();
    println!(".intel_syntax noprefix");
    println!(".global main");
    println!("main:");
    println!("    mov rax, {}", strtou(&mut iter));

    loop {
        match iter.next() {
            Some(val) => {
                match val {
                    '+' => {
                        // add
                        println!("    add rax, {}", strtou(&mut iter));
                    },
                    '-' => {
                        // sub
                        println!("    sub rax, {}", strtou(&mut iter));
                    },
                    _ => {
                        println!("Unexpected operator: use +, -");
                        break;
                    }
                }
            },
            None => {
                break;
            }
        }
    }
    println!("    ret");
}

// alternative of strtol
fn strtou<I: Iterator<Item = char>>(iter: &mut Peekable<I>) -> usize {
    let mut result: usize = 0;
    loop {
        match iter.peek() {
            Some(c) => match c.to_digit(10) {
                Some(i) => result = result * 10 + i as usize,
                None => break,
            },
            None => break,
        }
        iter.next();
    }
    result
}


// -----------------------------------------------
fn main() {
    // read command line arguments
    let args: Vec<String> = env::args().collect();
    if args.len() != 2 {
        println!("Invalid number of command line arguments");
    }

    // compile
    compile(&args[1]);
}

```

今回はコードの簡素化を図るため、エラーハンドリングを行わず panic を起こしています.

Rust にはない C の関数 `strtol` をつくる必要がありました. `libc` から `strtol` を使う手もありましたが、今回は Rust で `strtou` を実装しました.  
[strtol の参考](https://www.tutorialspoint.com/c_standard_library/c_function_strtol.htm)  



## 加減乗除ができるコンパイラ

トークナイザ、木構造、生成文法、再帰降下構文解析など、どの概念もとてもおもしろかったです.

今回の解析目標となる文法規則は、
```
// Extended Backus-Naur Form (Extended BNF, EBNF)
// Example: numeric formula

expr = mul ('+' mul | '-' mul)*
mul = primary ('*' primary | '/' primary)*
primary = num | '(' expr ')'

// expr よりも mul が先に計算されるため乗除が優先される.
// primary は数字か () のいずれかを表すため、() を加減乗除に優越させることができる. 
```
です. 

この簡潔な規則で四則演算が記述できるというのは感動的です.


#### 構文解析部分
```rust
use std::iter::{Iterator, Peekable};
use std::str::Chars;
// use anyhow::{anyhow, Result};

/*
Grammer 四則演算の生成文法

expr = mul ("+" mul | "-" mul)*
mul = primary ("*" primary | "/" primary)*
primary = num | "(" expr ")"
*/

// Node の種類と中身を表す. enum 便利
#[derive(Debug)]
pub enum NodeKind {
    // 演算子
    Op(char),
    // 数字
    Num(usize),
}

// 存在するか否かを表現するため Option
// ポインタとして子ノードを示す Box
pub type Link = Option<Box<Node>>;

// Node. NodeKind が演算子の場合は基本的に lhs (left-hand side) と rhs (right-hand side) をもつ.
pub struct Node {
    kind: NodeKind,
    lhs: Link,
    rhs: Link,
}


impl Node {
    // Node の新規作成
    pub fn new(kind: NodeKind, lhs: Link, rhs: Link) -> Self {
        Self {kind, lhs, rhs}
    }
    
    // 他の Node の要素として link が必要となる.
    // self をとって link にしようとする場合エラーとなるので 引数として Node をとる.
    pub fn link(node: Node) -> Link {
        Some(Box::new(node))
    }
}

// Input makes node tree from string
// Iterator を保持する構造体. Iterator を保持しなくてももっとスマートな方法がある気がする.
// Iterator 化することで入力された文字列のどこを解析すべきかを指定することが可能. 
pub struct Input<'a> {
    input: Peekable<Chars<'a>>,
}

impl<'a> Input<'a> {
    // Input の作成
    pub fn new(input: &'a str) -> Self {
        let iter = input.chars().peekable();
        Self {input: iter}
    }
    
    // expr, mul, primary の wrapper
    pub fn tokenize(&mut self) -> Node {
        let head_node = self.expr();
        head_node
    }
    
    // expr = mul ('+' mul | '-' mul)*
    // expr のコードによる表現
    fn expr(&mut self) -> Node {
        // はじめの mul 部分
        let mut node = self.mul();
        
        // ('+' mul | '-' mul)* の部分
        loop {
            match self.input.peek() {
                Some(&c) => {
                    match c {
                        '+' => {
                            // mul '+' mul を行う
                            self.input.next();
                            node = Node::new(NodeKind::Op('+'), Node::link(node), Node::link(self.mul()));
                        },

                        '-' => {
                            // mul '-' mul を行う
                            self.input.next();
                            node = Node::new(NodeKind::Op('-'), Node::link(node), Node::link(self.mul()));
                        },

                        ' ' => {
                            // 空白文字はスキップ
                            self.input.next();
                        },

                        ')' => {
                            // あとで primary = num | '(' expr ')' の () を処理する場合に必要. (終端である None と同じ扱い)
                            return node;
                        },

                        _ => {
                            panic!("Invalid Operator: {}", c);
                        }
                    }
                },
                None => {
                    // 終端の場合
                    return node;
                }
            }
        }

    }
    
    // mul = primary ('*' primary | '/' primary)*
    fn mul(&mut self) -> Node {
        // 最初の primary 部分
        let mut node = self.primary();
        
        // ('*' primary | '/' primary)* 部分
        loop {
            match self.input.peek() {
                Some(&c) => {
                    match c {
                        '*' => {
                            // primary '*' primary の実行
                            self.input.next();
                            node = Node::new(NodeKind::Op('*'), Node::link(node), Node::link(self.primary()));
                            
                        },
                        '/' => {
                            // primary '/' primary の実行
                            self.input.next();
                            node = Node::new(NodeKind::Op('/'), Node::link(node), Node::link(self.primary()));
                        },

                        ' ' => {
                            // 空白はスキップ
                            self.input.next();
                        }

                        _ => {
                            return node;
                        }
                    }
                },
                None => {
                    return node;
                }
            }
        }
    }
    
    // primary = num | '(' expr ')'
    fn primary(&mut self) -> Node {
        // ' ' をスキップするため loop (非本質な loop なので改良したい)
        loop {
            match self.input.peek() {
                Some(&c) => {
                    match c {
                        '0'..='9' => {
                            // num 部分
                            // strtou で数字を取り込んで node を返す.
                            let num = strtou(&mut self.input);
                            let node = Node::new(NodeKind::Num(num), None, None);
                            return node;
                        },
                        
                        '(' => {
                            // '(' expr ')' 部分
                            // expr 部分を処理して node を返す.
                            self.input.next();
                            let node = self.expr();
                            if *self.input.peek().unwrap() == ')' {
                                self.input.next();
                            } else {
                                panic!("')' not found!");
                            }
                            return node;
                        },

                        ' ' => {
                            // ' ' はスキップ
                            self.input.next();
                        },

                        _ => {
                            panic!("Invalid value: {}", c);
                        }
                    }
                },

                None => {
                    panic!("Expected some value!");
                }
            }
        }
    }
}

// char の Iterator から先頭の数字部分をとって返り値とする関数
fn strtou<I: Iterator<Item = char>>(iter: &mut Peekable<I>) -> usize {
    let mut result: usize = 0;
    loop {
        match iter.peek() {
            Some(c) => match c.to_digit(10) {
                Some(i) => result = result * 10 + i as usize,
                None => break,
            },
            None => break,
        }
        iter.next();
    }
    result
}

```
長いです. 何度も言いますが Rust っぽくないのでまた書き直します.

#### アセンブリ生成部分
```rust
#[derive(Debug, PartialEq)]
enum NodeKind {
    Op(char),
    Num(usize),
}

type Link = Option<Box<Node>>;

struct Node {
    kind: NodeKind,
    lhs: Link,
    rhs: Link,
}

impl Node {
    fn new(kind: NodeKind, lhs: Link, rhs: Link) -> Self {
        Self {kind, lhs, rhs}
    }

    fn link(node: Node) -> Link {
        Some(Box::new(node))
    }

    fn gen(node: Node) {
        // 再帰して node をたどる.
        if let Some(child) = node.lhs {
            Self::gen(*child);
        }
        if let Some(child) = node.rhs {
            Self::gen(*child);
        }
        
        // node の種類に従ってアセンブリを生成.
        match node.kind {
            NodeKind::Num(n) => {
                println!("    push {}", n);
                return;
            },
            NodeKind::Op(op) => {
                println!("    pop rdi");
                println!("    pop rax");
                match op {
                    '+' => {
                        println!("    add rax, rdi");
                    },
                    '-' => {
                        println!("    sub rax, rdi");
                    },
                    '*' => {
                        println!("    imul rax, rdi");
                    },
                    '/' => {
                        println!("    cqo");
                        println!("    idiv rdi");
                    }
                    _ => {
                        panic!("compile error");
                    }
                }
            }
        }

        println!("    push rax");
    }
}


fn compile(s: &str) {
    let mut input = Input::new(s);
    let head_node = input.tokenize();
    println!(".intel_syntax noprefix");
    println!(".global main");
    println!("main:");
    Node::gen(head_node);
    println!("    pop rax");
    println!("    ret");
}

fn main() {
    // read command line arguments
    let args: Vec<String> = env::args().collect();
    if args.len() != 2 {
        println!("Invalid number of command line arguments");
    }

    // compile
    compile(&args[1]);
}
```
移植元の C コードに引っ張られて Rust っぽくない書き方をしている (自戒).


#### 全体のコード
ステップ 7 まで終えた状態です.  
[here](https://github.com/Hmiya6/rust-calc-compiler/commit/f066cbc899044443009982af87967327a43c218a#diff-42cb6807ad74b3e201c5a7ca98b911c5fa08380e942be6e4ac5807f8377f87fc)


## あとがき
低レイヤ面白いです. コードは書き直します.


## 参考
* [低レイヤを知りたい人のためのCコンパイラ作成入門](https://www.sigbus.info/compilerbook)

