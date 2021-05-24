---
title: "Rust のスマートポインタ"
description: "スマートポインタのメモ. 未完成."
date: 2021-04-30
tags: ["Rust"]
---
スマートポインタ関連がわからなかったときに調べたメモをつなげたものです. (編集中)

---
## スマートポインタ全般について
> スマートポインタは、ポインタとして振る舞うだけでなく、追加のメタデータと能力があるデータ構造
[引用](https://doc.rust-jp.rs/book-ja/ch15-00-smart-pointers.html)

スマートポインタの役割
1. ポインタとしてヒープに確保した領域のデータを参照
2. メタデータやポインタ以外の機能を保持する

---
## `Deref` トレイト
`Deref` トレイトを用いて参照外し演算子 (`*`) の振る舞いを実装・カスタマイズすることができる. 

#### 例: 参照外しが起きた場合に Dereferenced! と出力するスマートポインタ構造体 `MyBox<T>`

```rust
use std::ops::Deref;

struct MyBox<T>(T);

impl<T> MyBox<T> {
    fn new(x: T) -> MyBox<T> {
        MyBox(x)
    }
}

// 参照を外す能力を実装する
impl<T> Deref for MyBox<T> {
    type Target = T;
    
    fn deref(&self) -> &Self::Target {
		println!("Dereferenced!");
        &self.0
    }
}

fn main() {
    let x = 5;
    let y = MyBox::new(x);
    
    assert_eq!(5, x);
    assert_eq!(5, *y); // *y is equivalent to *(y.deref())

}
```
[Playground](https://play.rust-lang.org/?version=stable&mode=debug&edition=2018&gist=9eea072614f044fc3214a99bf89019c6)

参照を外すとき (`*y`) においてコンパイラは `MyBox<T>` の `deref` メソッドを呼び出す. すなわち、 **`*y` は `*(y.deref())` として解釈される**.  

---
## 参照外し型強制
参照外し型強制は、関数やメソッドの**引数**に対して作用する. 参照外し型強制のおかげで簡単に引数へ値を渡すことができる. 

> 参照外し型強制は **`Deref` を実装する型への参照を `Deref` が元の型を変換できる型への参照に変換する**.  (強調は引用者)
[引用](https://doc.rust-jp.rs/book-ja/ch15-02-deref.html#%E9%96%A2%E6%95%B0%E3%82%84%E3%83%A1%E3%82%BD%E3%83%83%E3%83%89%E3%81%A7%E6%9A%97%E9%BB%99%E7%9A%84%E3%81%AA%E5%8F%82%E7%85%A7%E5%A4%96%E3%81%97%E5%9E%8B%E5%BC%B7%E5%88%B6)


#### ゑ？
1. `Deref` を実装する型への参照 (`&MyBox<T>`) を  
2. `Deref` が元の型 (`MyBox<T>`) を (`Deref` を用いて) 変換できる型 (`T`) への参照 (`&T`) に
変換する.

つまり、`Deref` を実装するスマートポインタ (例: `MyBox<T>`) の参照 (例: `&MyBox<T>`) は、`deref` が返す型の参照 (例: `&T`) へ変換される.

つまり、**`&MyBox<T>` は　`&T` へ変換される**.  
同様に、**`Box<T>` は　`&T` へ変換される**.

原文の記述:
> Deref coercion converts such a type into a reference to another type. For example, deref coercion can convert `&String` to `&str` because  `String` implements the `Deref` trait such that it returns `str`.

[引用](https://doc.rust-lang.org/book/ch15-02-deref.html#implicit-deref-coercions-with-functions-and-methods)  

参照外し型強制はそのような型 (訳者注: `Deref` トレイトを実装している型) を他の型の参照へ変換します. 例えば、参照外し型強制は `&String` を `&str` へ変換できますが、これは `String` が `str` を返すような `Deref` トレイトを実装しているためです.


#### `Deref` まとめ
- `Deref` は、参照外しだけでなく、型変換にも用いられる.   
- 参照外しのときは 保持する値を返すために用いられる.   
- 型変換 (参照外し型強制) のときは、参照外しに用いられる型変換 (例: `fn deref(&self) -> &T`) を応用して、関数やメソッドの引数の型をよしなにしてくれる.

---
## `Drop` トレイト

`Drop` トレイトは、ヒープに確保された領域が開放されたときに実行されるプログラムを指定できる. 

```rust
struct MyBox<T>(T);

impl<T> MyBox<T> {
    fn new(x: T) -> MyBox<T> {
        MyBox(x)
    }
}

impl<T> Drop for MyBox<T> {
    fn drop(&mut self) {
        println!("dropping");
    }
}

fn main() {
    let x = MyBox::new(5);
}
```
[Playgroound](https://play.rust-lang.org/?version=stable&mode=debug&edition=2018&gist=7ed3a2a393c2963f2d28e6f9b959b9e8)


---
## `Box<T>`, `Rc<T>`, `RefCell<T>` の違い
- `Rc<T>`: 同じデータを複数の所有者にもたせる. c.f., `Box<T>`, `RefCell<T>` は単独の所有者のみを許容.
- `Box<T>` では、不変借用も可変借用もコンパイル時に精査可能. c.f., `Rc<T>` では不変借用のみがコンパイル時に精査可能、 `RefCell<T>` では、不変借用も可変借用も実行時に精査される.
- `RefCell<T>` の可変借用は実行時に精査されるため、 `RefCell<T>` が不変でも `RefCell<T>` 内の値を可変にすることができる. (不変型が必要で、かつその型の中の値を変更する必要があるときに使う)



#### 参考
- [RefCell\<T\> と内部可変パターン](https://doc.rust-jp.rs/book-ja/ch15-05-interior-mutability.html)

---
## `Box`
`Box` はヒープにデータを格納する.

#### 使用場面
- コンパイル時にはサイズを知ることのできない型を使用する
	-> **再帰的な型** (木構造, linked list) の実装を可能にする
- 多くのデータがあり、所有権を転送したいが、その際にデータがコピーされないようにしたい
	-> データのコピーは重いため、**ポインタのみのコピー**を可能にする
- 値を所有する必要があり、特定の型でなく特定のトレイトを実装する型で有ることのみを気にかけているとき
	-> **トレイトオブジェクト**で異なる型の値を許容することを可能にする.
	
#### 再帰的な型
再帰的な型 = 型の一部として同じ型の他の値を持つもの

例: 二分木 (binary tree)  
[Playground](https://play.rust-lang.org/?version=stable&mode=debug&edition=2018&gist=dceb54a19419e9866cfbef8a04feb0b5)  
	
```rust
type Link<T> = Option<Box<Node<T>>>;

struct Node<T> {
	val: T, 
	lc: Link<T>, // left child
	rc: Link<T>, // right child
}
```

The Rust Programming Language ではコンスリストを実装していた.
[参考: コンスリストの実装](https://doc.rust-jp.rs/book-ja/ch15-01-box.html#%E3%83%9C%E3%83%83%E3%82%AF%E3%82%B9%E3%81%A7%E5%86%8D%E5%B8%B0%E7%9A%84%E3%81%AA%E5%9E%8B%E3%82%92%E5%8F%AF%E8%83%BD%E3%81%AB%E3%81%99%E3%82%8B)

#### 参考
- [ヒープのデータを指すBox\<T\>を使用する](https://doc.rust-jp.rs/book-ja/ch15-01-box.html)


---
## `Rc` 参照カウンタ (Reference Counted)
複数の所有権を可能にする型が `Rc<T>` .  `Rc<T>` 型は値への参照の数を追跡する. 値への参照カウントが 0 になると 値は破棄される. 

`Rc<T>` は、シングルスレッドでのみ使用される. マルチスレッドでの参照カウントは `Arc<T>` を使用する. 

```rust
use std::rc::Rc;

fn main() {
    let s = String::from("hoge");
    let rc1 = Rc::new(s);
    let rc2 = Rc::clone(&rc1);
    
    println!("{}", *rc1);
    println!("{}", *rc2);
}
```

`Rc<RefCell<T>>` (後述) として使われることが多い. 

---
## `Arc` (Atomic Reference Counted)
> When shared ownership between threads is needed, `Arc` (Atomic Reference Counted) can be used.  

[Arc](https://doc.rust-lang.org/stable/rust-by-example/std/arc.html)

スレッド間の共有所有権が必要なときに、`Arc` を使うことができる.

基本的に `Rc` と同じ性質をもつ.

`Arc<Mutex<T>>` として使われることが多い.


---
## `Cell` 

工事中

#### Interior Mutability
interior: 内部の、内側の

> Sometimes a type needs to be mutated while having multiple aliases. In Rust this is achieved using a pattern called *interior mutability*. A type has interior mutability if its internal state can be changed through a shared reference to it.

[Interior Mutability](https://doc.rust-lang.org/reference/interior-mutability.html) 

ときどき、型は複数のエイリアスを保持しながら可変になる必要がある. Rust ではこれを *interior mutability* (内部可変性) と呼ばれるパターンを用いて行っている. 型の内部状態が型への共有された参照を通して変化可能ならば、型は内部可変性をもつ.

#### 参考
- [std::cell](https://doc.rust-lang.org/std/cell/index.html)


---
## `RefCell<T>`
`RefCell<T>` は、内部可変性 (interior mutability) を持つ. 内部可変性とは、そのデータへの不変参照がある時でさえもデータを可変化できる Rust のデザインパターン. 

`Rc<T>` とは異なり、 `RefCell<T>` 型は、保持するデータに対して単独の所有権を表す. 

同様の特徴を持つ型として `Box<T>` があるが、`RefCell<T>` と `Box<T>` では借用規則の強制の時期が異なる. 具体的には `Box<T>` はコンパイル時に借用規則のチェックが行われ、 `RefCell<T>` では実行時にチェックが行われるようになっている. `RefCell<T>` が借用規則に違反した場合は、プログラムがパニックする. 

`RefCell<T>` はコンパイル時点で安全性を確認できないため、 `Box<T>` を使うことが可能なら `Box<T>` を使うべき. ただ、`Box<T>` では解決できないプログラムも存在する.



#### 参考
- [RefCell\<T\> と内部可変パターン](https://doc.rust-jp.rs/book-ja/ch15-05-interior-mutability.html)
- [std::cell::RefCell](https://doc.rust-lang.org/std/cell/struct.RefCell.html)

---
## `Rc<RefCell<T>>`
よくある型. 複数から参照されて、かつ中身の値を変更するような場合に使う.

簡単な例としては、双方向連結リストやグラフ.  

#### 双方向連結リスト
```rust
use std::rc::Rc;
use std::cell::RefCell;

type Link<T> = Rc<RefCell<Node<T>>>;

pub struct Node<T> {
    val: T,
    next: Option<Link<T>>,
    prev: Option<Link<T>>,
}

impl<T> Node<T> {
    pub fn new(val: T) -> Self {
        Self {
            val,
            next: None,
            prev: None,
        }
    }
	
	pub fn set_next(&mut self, next: Link<T>) {
        self.next = Some(next);
    }

    pub fn get_next(&self) -> Option<Link<T>> {
        match &self.next {
            Some(next) => Some(Rc::clone(next)),
            None => None,
        }
    }

    pub fn set_prev(&mut self, prev: Link<T>) {
        self.prev = Some(prev);
    } 

    pub fn get_prev(&self) -> Option<Link<T>> {
        match &self.prev {
            Some(prev) => Some(Rc::clone(prev)),
            None => None,
        }
    }

}

pub struct LinkedList<T> {
    head: Option<Link<T>>,
    tail: Option<Link<T>>,
    length: usize,
}


impl<T> LinkedList<T> {
    
    pub fn new() -> Self {
        Self {
            head: None,
            tail: None,
            length: 0,
        }
    }

    pub fn push(&mut self, item: T) {
        let node = Node::new(item);
        let link = Rc::new(RefCell::new(node));
		
        match self.tail.take() { // Option の take() / replace() は Option のドキュメントを参照.
            Some(old_tail) => { // old_tail: Rc<RefCell<Node<T>>>
				// Rc<RefCell<Node<T>>> 内部(Node<T>)の変更
                old_tail.borrow_mut().set_next(Rc::clone(&link));
            },
            None =>{
                // self.tail が存在しなければ、
				// push した T は tail だけでなく head にもなる
                self.head = Some(Rc::clone(&link));
            },
        }
		
		// tail に追加
        self.tail = Some(Rc::clone(&link));
        self.length += 1;
    }

    pub fn pop(&mut self) -> Option<T> {
        match self.tail.take() {
            Some(tail) => {
                if let Some(prev) = tail.borrow_mut().prev.take() {
                    prev.borrow_mut().set_next(None); // 内部の変更
                    self.tail = Some(prev);
                } else {
                    self.head = None;
                }
                self.length -= 1;
                return Some(
					// Rc<RefCell<Node<T>>> の所有権を得る.
					// Rc に関しては * で参照外しが可能な場合もある.
                    Rc::try_unwrap(tail).ok()
                        .unwrap()
                        .into_inner() // RefCell<Node<T>> から Node<T> を取り出す
                        .val
                );
            }
            None => {
                return None;
            }
        }
    }
}
```


