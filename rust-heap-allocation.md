# Rust のスマートポインタ

[The Rust Programming Language 日本語版](https://doc.rust-jp.rs/book-ja/ch15-00-smart-pointers.html) を読んだまとめ. 


## スマートポインタ全般について
> スマートポインタは、ポインタとして振る舞うだけでなく、追加のメタデータと能力があるデータ構造
[引用](https://doc.rust-jp.rs/book-ja/ch15-00-smart-pointers.html)

スマートポインタの役割
1. ポインタとしてヒープに確保した領域のデータを参照
2. メタデータやポインタ以外の機能を保持する

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
> 参照外し型強制はそのような型 (訳者注: `Deref` トレイトを実装している型) を他の型の参照へ変換します. 例えば、参照外し型強制は `&String` を `&str` へ変換できますが、これは `String` が `str` を返すような `Deref` トレイトを実装しているためです.


#### `Deref` まとめ
- `Deref` は、参照外しだけでなく、型変換にも用いられる.   
- 参照外しのときは 保持する値を返すために用いられる.   
- 型変換 (参照外し型強制) のときは、参照外しに用いられる型変換 (例: `fn deref(&self) -> &T`) を応用して、関数やメソッドの引数の型をよしなにしてくれる.

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


## `Rc` 参照カウンタ (Reference Counter)
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

## `Arc` 

## `Cell` 

## `RefCell`

## `Rc<RefCell<T>>`

## idioms
