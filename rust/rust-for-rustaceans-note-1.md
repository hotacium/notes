---
title: "Rust for Rustaceans のノート 1"
description: ""
date: 2021-06-12
tags: ["Rust"]

---

# Rust For Rustaceans のノート

# 2. 基本
Rust のプリムティブを概観.

## メモリの話

プログラムは以下のメモリへアクセス可能:
- スタック
- ヒープ
- レジスタ
- テキストセグメント
- メモリマップされたレジスタ
- メモリマップされたファイル
- 不揮発 RAM (Random Access Memory)

どのメモリを使うかによって
- 何を保存するか
- どの程度の期間情報が残り続けるか
- どのような方法でアクセスするか
がわかる

### メモリの専門用語 terminology

Rust における, 
- 値 value
- 変数 variable
- ポインタ pointer

値とは **1. 型と, 2. その型が取りうる値の集合の一要素 の組み合わせ**.

型の **表現 (representation)** によってバイト列に変換される. 普通に扱う分には難しく考える必要はない.

値は **場 (place)** に保存される. **place** は Rust の用語で "値を保存する場所". place はスタック上にもヒープ上にも他の場所の上にもなりうる. 最もよくある値の保存場所は **変数 (variable)** で, 変数はスタック上に名前のついた値のスロットである.

**ポインタ (pointer)** はメモリ領域のアドレスを保持する値, つまりポインタは場所 (place) を指す. ポインタは そのポインタが指すメモリ領域に保存されている値へアクセスするため参照外しができる.

同じポインタは複数の変数で保存でき, ゆえに間接的にメモリの同じ場所を指す複数の変数が存在可能.


```rust
let string = "Hello world";
```
文字列値を `string` 変数へ代入するが, 変数の **本当** の値は 文字列値 `"Hello world"` の最初の値へのポインタ (文字列値それ自体ではない). 

### 変数を掘り下げる

更に複雑なコードに直面すると, さらに正確な頭の中のモデルが必要となる.

2 つの頭の中のモデル
- high-level model 高レベルなモデル
- low-level model 低レベルなモデル


- high-level model はコードを lifetime や borrow のレベルで考えるときに便利.
- low-level model は unsafe や raw pointer について考えるのによい.

### 高レベルモデル High-Level Model 

高レベルで考えるとき, 変数についてバイト列を保持する場 place として捉えない.  
変数は インスタンス化され, move され (moved), プログラムを通して使われる, 名前の与えられた値. 

変数に値を代入すると, その値は変数によって名前をつけられることになる.  
あとで変数にアクセスされると, 前のアクセスから新しいアクセスへの一本の線を描くことができる. 先は 2 つのアクセスの間の依存関係が確立される. もし変数内の値が move されれば, それらの線s はそれ以上描かれることはない. 


borrow checker に引っかかるコード:
```rust
let mut x;

// このアクセスはだめ. どこからも flow が描かれていないから
// assert_eq!(x, 42);
x = 42; // ... (1)

let y = &x; // ... (2)

x = 43; // ... (3)

assert_eq!(*y, 42); // ... (4)
```

borrow checker で違反が発生する理由:
- (1) から (3) の, 1 つの排他的な (`&mut`) の flow
- (1) - (2) - (4) の 1 つの非排他的な (shared) (`&`) の flow

があり, (排他的な flow 以外にも flow が存在 -> ) incompatible flows が同時に存在するから.

今のコードの flows
```
(1)
 |\ 
 | \
(2) \
 |  |  <- exclusive flow
 | (3)
 |
 | <- shared flow
(4)
```

これなら OK.
```
(1)
 |\
 | \
(2) \
 |  |
... <--- (3) の前に shared flow が消える
 |  |
 X  | <- exclusive flow
   /
  /
 |
(3)
```

新しい変数が前の変数と同じ名前で宣言されたときは, 全く別の変数として (コンパイラには) 考えられる. これは **shadowing** と呼ばれ, あとの変数が同じ名前の前の変数を shadow する. 

### 低レベルモデル Low-Level Model

変数は legal な値を保持していたりしていなかったりするメモリの場所に名前をつける. 変数は「値のスロット」と考えることができる. 
そこに値を代入すると, スロットは埋まり, 古い値 (もしあれば) は drop され置き換わる. 
変数にアクセスすると, コンパイラはスロットが空でないかをチェックする, これは変数が初期化されていないかその値がすでに move されたことを意味するから. 
変数へのポインタは変数の後ろにあるメモリを参照 (refer) しており, その値を得るために「参照を外す」(dereference) することが可能である. 
例えば, `let x: usize` という statement では, 変数 `x` は (値 value のために `usize` のサイズを保持するものの well-definded な値を持たない) スタック上のメモリ領域の名前である. 

`x` に何を代入しても, `&x` は変わらない.

もし複数の変数を同じ名前で宣言しても, それらは違うメモリとなる.

以下は legal なコード
```rust
fn main() {
    let x = 42;
    let y = &x;
    let x = 43;
    assert_eq!(*y, 42);
    // println!("{}", *y);
    assert_eq!(x, 43);
    // println!("{}", x);
}
```

## メモリ領域

メモリとは本来何なのか. 
メモリには多くの異なった領域がある.
- スタック stack
- ヒープ heap
- 静的メモリ static memory

### The Stack

**スタック** は 「プログラムが関数呼び出しのために scratch space として使うメモリの一部分」である.
関数が呼ばれるたびに, **フレーム** と呼ばれる連続したメモリのチャンクがスタックの上に配置される. 
スタックの最下部の近くには, `main` 関数用のフレームがあり,　他の関数を呼び出す関数と他のフレームがスタックに積まれていく.
関数のフレームは関数内のすべての変数が含まれているほか, 関数が取るすべての引数も含まれる.
関数が返るとき, スタックフレームは回収される.

関数のローカル変数の値を構成するバイト bytes は即時に消去されるわけではない. が, 再宣言されたフレームで自身のフレームをオーバラップする続く関数呼出によって上書きされた可能性があるので, アクセスするのは安全でない. 

以下 deepl
> 関数のローカル変数の値を構成するバイトは、すぐには消去されませんが、再生 (reclaim) されたフレームと重なっている後続の関数呼び出しによって上書きされている可能性があるため、アクセスすることは安全ではありません。

以下 google 翻訳
> 関数のローカル変数の値を構成するバイトはすぐには消去されませんが、フレームが再利用されたものと重複する後続の関数呼び出しによって上書きされる可能性があるため、それらにアクセスすることは安全ではありません。 

上書きされていないにしても, そのバイトは, 関数が返るときに move された値などといった使用してはならない値を含んでる可能性がある.

---
### スタックと関数呼び出しの補足

- `EIP`: これから実行する命令の位置 = アドレス.
- `ESP`: 現在のスタックの一番上
- `EBP`: 現在のスタックの一番下

呼出規約の一つ, `stdcall`

呼出元:
```
push arg2
push arg1
call label
mov retval, eax
```

呼出先:
```
label:
    push ebp
    mov ebp, esp
    sub esp, buflength
    mov eax, [ebp+0x8]
    mov ebx, [ebp+0xC]
    (processing)
    mov esp, ebp
    pop ebp
    ret
```

#### calling
1. `arg1`, `arg2` を push したあと
スタックの様子
```
// EIP = `call label`

===== <- EBP
.
.
-----
arg2
-----
arg1 
===== <- ESP

```

2. `call label` のあと
`call` 命令は, `EIP` の値を `push` してから `EIP` を `label` で指定したアドレスに更新する命令.

スタックの様子
```
// EIP = `push ebp`

================ <- EBP
.
.
----------------
arg2
----------------
arg1
----------------
リターンアドレス (呼出元の EIP)
================ <- ESP
```

3. `push ebp` の後
古い `ebp` を退避する

スタックの様子
```
// EIP = `mov ebp, esp`

================ <- EBP
.
.
----------------
arg2
----------------
arg1
----------------
retaddr
----------------
ebp のバックアップ (古い ebp)
================ <- ESP
```

4. `mov ebp, esp` の後
ebp を esp に合わせて新しいスタックを開始

スタックの様子
```
// EIP = `esp, buflength`

================
.
.
----------------
arg2
----------------
arg1
----------------
retaddr
----------------
ebp のバックアップ (古い ebp)
================ <- ESP, EBP
```
#### returning

1. `mov esp, ebp` の後
スタックをクリア

スタックの様子
```
// EIP = `pop ebp`

================
.
.
----------------
arg2
----------------
arg1
----------------
retaddr
----------------
ebp のバックアップ (古い ebp)
================ <- ESP, EBP
```

2. `pop ebp` のあと
ebp を復元

スタックの様子
```
// EIP = `ret`

================ <- EBP
.
.
----------------
arg2
----------------
arg1
----------------
retaddr
================ <- ESP
```

3. `ret` のあと
`ret` 命令はスタックの先頭要素を `EIP` に書く格納して, `ESP` を 1 つ小さくする.

スタックの様子
```
// EIP = `mov retval, eax`

================ <- EBP
.
.
----------------
arg2
----------------
arg1
================ <- ESP
```
---

### ヒープ the heap

**ヒープ (heap)** はプログラムの現在の呼び出しスタック (call stack) に結び付けられていないメモリのプール.
ヒープメモリにある値は 明示的に deallocate されるまで生存する.
これは 現在の関数のフレームの lifetime を超えて値を生存させたいときに便利.
値が関数の返り値である場合は, 呼び出し元の関数はそのスタックに呼出先関数が返る前にその値 (返り値) を書き込むためのスペースを残しておくことが可能.
しかし, 例えば 返り値を別のスレッドに送りたい場合はヒープに保存する.

ヒープによって 明示的に継続的な (contiguous) メモリ区画の割り当て allocate が可能となる (cf. スタックによるメモリ区画は, 関数の return によって値が保証されなくなる).
継続的なメモリ区画は後で deallocate するまで確保されている. この deallocate することは **freeing** と呼ばれる (C の標準ライブラリより).
ヒープからの割り当て allocation は関数が返っても消えないため, メモリを値のために一つの場所に割り当てることができ, その値へのポインタを別のスレッドに渡すことで, スレッドが安全にその値で操作を行わせることができる.

<!--
- allocation
- assiginment
-->

heap を使う第一の方法は `Box` を使うこと. 
`Box::new(value)` でヒープに値を置くことができ, `Box::new(value)` で返されるのはヒープ上の値へのポインタ.
`Box` が drop されるとメモリが free される.



ヒープメモリを deallocate し忘れると, ずっと残り続け, ゆくゆくはマシン上のすべてのメモリを食い尽くす.
これは メモリリーク **leaking memory** と呼ばれ, 避けたいもの. 
ただ, 明示的に (意図的に) メモリをリークさせたいときがある. 
例えば, プログラム全体がアクセス可能な read-only な設定 (configuration) があるとする.
ヒープにそれをおいて, 明示的にリークさせる (`Box::leak`) ことで `'static` reference を得ることができる.


### 静的メモリ static memory
静的メモリとはプログラムがコンパイルされる先のファイルに存在するいくつかの密接に関連した領域の包括的な名称.

deepl.com
> 静的メモリとは, プログラムをコンパイルしたファイルの中にある, いくつかの密接に関連した領域の総称.
これらの領域は実行時に自動的にプログラムのメモリへロードされる. 静的メモリの値 values はプログラムの実行の全体で生存する.
<!--
違うかも

> Static Memory Allocation: Static Memory is allocated for declared variables by the compiler. The address can be found using the address of operator and can be assigned to a pointer. The memory is allocated during compile time.

from [here](https://www.geeksforgeeks.org/difference-between-static-and-dynamic-memory-allocation-in-c/)

> 静的メモリ: 静的メモリはコンパイラによって宣言された変数を割り当てられる. そのアドレスは操作アドレス (the address of operator) を使って見ることができ, ポインタへ割り当てられる可能性がある. 静的メモリはコンパイル時に割り当てられる.
-->
プログラムの静的メモリにはプログラムのバイナリコードが含まれ, バイナリコードは通常 read-only でマップされる.
プログラムが実行されるとき, プログラムはテキストセグメントのバイナリコードを 1 命令ごとに進んでいき, 関数が呼ばれると jump する. 
静的メモリは, 文字列のようないくつかの定数と, `static` と共に宣言された変数のためのメモリも保持する.

特別な lifetime `'static` は 静的メモリ static memory に由来し, 参照を「静的メモリが存在する期間」, すなわちプログラムが終了するまで, 有効であるようにマークする.
静的変数 (static variable) のメモリはプログラムが始まるときに allocate されるため, 静的メモリにある変数への参照は, その定義から, `'static` となる.
逆は真でない the inverse is not true -- 静的メモリを指していない `'static` 参照もありえる.

`T: 'static` のようなトレイト境界は, 型パラメータ `T` が, その値をどの期間生存させようとも, プログラムの残りの実行時間生存し続けることを意味する.

本質的には, この境界は `T` が所有されていること, 自立している self-sufficient こと, つまり, 他の非静的な値を借りていないか, 借りているすべての値が `'static` でプログラムの最後まで生存していることのいずれかが成り立っている必要がある.
`'static` の境界としての良い例は 新しいスレッドをつくる `std::thread::spawn` 関数. 新しいスレッドは現在のスレッドよりも長く生存する可能性があるため, 新しいスレッドは古いスレッドのスタックにあるすべてのものを参照できない. 
新しいスレッドは そのすべての lifetime で生存可能な値のみを参照可能である.

`const` と `static` の違い: `const` は要素が定数であることを宣言するもの. 定数はコンパイル時に計算されうるかつ, 定数からコンパイル時に計算された値は, その定数への参照をすべてその値で置き換える.

## 所有権 Ownership
!p.7

Rust では, すべての値は単独の **所有者** を有し, ただ一つの場所が値を deallocate する責任をもつ. 
これは borrow checker を通して強制される.
もし値が move されると, 例えば値を新しい変数に割り当てたり, 値を vector に push したり, ヒープにおいたりすると, 値の所有権が古い場所から新しい場所に移動する.
この時点で, 元の所有者から flow した変数を通して値にアクセスすることができない.

型のなかにはこのルールに従わないものもある. 
値の型が `Copy` トレイトを実装している場合, 値が新しいメモリの場所に再割り当て reassigne されても 値が move されたとは考えない (move ではなく, 値が **コピー copy** され, 古い場所も新しい場所もアクセス可能である).
Rust のほとんどの primitive 型 (e.g. 整数, 浮動小数点数, etc) は `Copy`. 
`Copy` でない型を含む型などは `Copy` になれない.


例:
`Box` 型が `Copy` だった場合, `box2 = box1` を実行する.
そうすると `box1`, `box2` の双方は自分自身が box のためにヒープメモリに割り当てられたを所有していると考え, そして双方はスコープ外になったときに free を試みる. メモリの 2重解放は破滅的な結果をもたらしかねない.


値の所有者が値を使う必要がなくなると, **drop** することでその値をクリーンアップする責任を負う. Rust では drop は変数がスコープ外になると自動的に発生する.
Rust はその所有権システムから, メモリを多重解放することはない.

```Rust 
fn main() {
    let x1 = 42;
    let y1 = Box::new(84);
    { // 新しいスコープが開始される.
        let z = (x1, y1);
        // z はスコープ外になり, 値は drop される.
        // z の値が drop されるため, x1, y1 も drop される.
    }
    // x1 の型は Copy なので, z に move されていない.
    let x2 = x1; // OK

    // y1 の型は Copy でないので, z に move された.
    // let y2 = y1; // not OK 
} 
```

## 借用とライフタイム Borrowing and Lifetimes

Rust では参照を通して所有権を移転させることなく値を他に貸し出すことができる. **参照** とは, 参照をどのように扱われるかを追加の約定 (contract) と一緒にしたもの. 追加の約定には, 排他的アクセスか否か, 他の参照が同じ値を指している可能性があるか否か, などがふくまれる.

### 共有参照 Shared References

共有参照 (`&T`): ポインタが共有されている(可能性がある).  
共有参照は `Copy` なので同じものを所有権の移転なしにつくれる.
共有参照の裏にある値は mutable でないので modify/reassign/cast-to-mutable 不可能.

### 可変参照 Mutable References

可変参照 (`&mut T`): read も write も可能. 他のスレッドが参照先の値にアクセスできないようになる.  
-> 排他的 exclusive

```rust
let mut x = 42;
let y = &mut x;
*y = 0;
assert_eq!(x, 0);
```

値の所有権を持つことと可変参照をもつことの違いは, 値を drop する責任がどこにあるか. 所有権のあるところに値 drop の責任がある. その他には, 可変参照の裏にある値を move した場合, その場所 place に別の値を残しておく必要がある. 

```rust
fn replace_with_84(s: &mut Box<i32>) {
    // this is not okay, as *s would be empty:
    // let was = *s;
    // but this is:
    let was = std::mem::take(s);
    // so is this:
    *s = was;
    // wa can exchange values behind &mut:
    let mut r = Box::new(84);
    std::mem::swap(s, &mut r);
    assert_ne!(*r, 84);
}

let mut s = Box::new(42);
replace_with_84(&mut s);
```

### 内部可変性 Interior Mutability

いくつかの型には **内部可変性 interior mutability** があり, これによって 共有参照を通して値を可変にすることができる. 
この型の機能は 追加のメカニズム (e.g. atomic CPU 命令) か, invariants に依存しており, それによって 排他的参照のセマンティクスに頼ることなく安全な可変性を提供している. 

内部可変性の種類
1. 共有参照を通して可変参照を得る: `Mutex`, `RefCell`
同時に一つの可変参照のみが存在するように保証する. `UnsafeCell` がその下にある. 

2. 共有参照を可変にする: `std::sync::atomic`, `std::cell::Cell`
内部の値に可変参照を与えるのではなく, 値を操作するメソッドを与える. 直接参照を得るわけではないが, read と値の replace は可能.

---
`std::cell::Cell` は invariants を通した内部可変性の興味深い例.  
`Cell` は
- スレッド間で共有可能でない
- `Cell` 内部の値に参照を行うことができない
- 値を置き換えることができる
- コピーされた値を返すことができる

内部の値への参照がないため, 自由に move できる. 
`Cell` はスレッド間で共有できないため, 同時に mutate されることがない.

---

### ライフタイム Lifetimes

lifetime は参照が valid であるべき領域を指す名前であり, スコープと一致することも多いが必ずしもそうとは限らない.


### ライフタイムと借用チェッカー Lifetimes and the Borrow Checker

参照が lifetime `'a` と共に用いられた場合, 借用チェッカーは `'a` がまだ生存しているかをチェックする. 
`'a` の開始から 使用されているポイントまでのパスをたどり, その過程で conflict がないかを確認することで check している. 
これによって参照が安全にアクセスできる値を指していることを保証する. 
高レベルなモデルの data flow の考え方に似ている. 

これは OK.
```rust
let mut x = Box::new(42);
let r = &x; // 'a // ... (1)
if rand() > 0.5 {
    *x = 84; // ... (2)
} else {
    println!("{}", r); // 'a // ... (3)
}
// ... (4)

```

同じような例:
```rust
let mut x = Box::new(42);
let r = &x; // ...(0)

// (1) と (2) のどちらかなら可能 
// (1) と (2) どちらも実行するのは不可 (なぜなら, (0) の時点で (2) まで継続する共有参照の flow があるため, (1) で排他参照の flow を生やすことができない)
*x = 84; // ...(1) // (1) のみを使う場合, (0) の共有参照の flow は (1) までに消滅するため, (1) で排他参照を得ることが可能.
// println!("{}", r); // ...(2) // (2) のみを使う場合, (0) の共有参照が (2) まで続き, 同時に排他参照が存在しないため, OK.

```

これも OK:
```rust
let mut x = Box::new(42);
let mut z = &x; // ... (1)
for i in 0..100 {
    println!("{}", z); // ... (2)
    x = Box::new(i); // ... (3)
    z = &x; // ... (4)
}
println!("{}", z);
```

`x` への参照を取得した (1) から lifetime `'a` が始まる. 
(3) で `x` を move out して, lifetime `'a` を終わらせるが, このとき借用チェッカーは `'a` が (2) で終了したと考えてこれを許可する. 
(4) で lifetime `'a` が再開される. 

同じような例 (OK):
```rust
let mut x = Box::new(42); // ... (0)
let mut z = &x; // ... (1)
for i in 0..100 {
    println!("{}", z); // ... (2)
    *x = i; // ... (3)
    z = &x; // ... (4)
}
println!("{}", z);
```
(0)-(1)-(2) で flow は終了するため, (0)-(3) が可能. (0)-(3) で flow が終了するため, (0)-(4) が可能. (0)-(4) で flow が終了するため, ...

### ジェネリックなライフタイム Generic Lifetimes

自分でつくった型に参照を保存する必要が出てくる. その参照が様々なメソッドで使われるときに borrow checker がその正当性 validity を確認するため, その参照には lifetime が必要. 特に, (`self` の参照よりも長く生存する参照を返す)メソッドがほしいときは重要になる.

参考: [Validating References with Lifetimes](https://doc.rust-lang.org/stable/book/ch10-03-lifetime-syntax.html)



例: generics over multiple lifetimes

普通は 1つの lifetime で足りるし, 複数あるのは混乱のもとだからやめたほうがいい.

一つの lifetime のみだけだとイテレータから出てくる値が `document` と `delimiter` の lifetime に紐付けられる. `str_before` の write が不可能になる. これは return type がその関数のローカル変数 (`String` from `to_string()`) に紐付けられてた lifetime を持ち, 借用チェッカーはそれを拒絶する.

```rust
struct StrSplit<'s, 'p> {
    delimiter: &'p str,
    document: &'s str,
}

impl<'s, 'p> Iterator for StrSplit<'s, 'p> {
    type Output = &'s str;
    fn next(&self) -> Option<Self::Output> {
        todo!()
    }
}

fn str_before(s: &str, c: char) -> Option<&str> {
    StrSplit {document: s, delimiter: &c.to_string()}.next()
}

```

MEMO: わからん

### Lifetime Variance
`'a` が `'b` より長く生存する場合, `'a` は `'b` を代用できる. 例えば, 関数の引数に渡すことができる.

