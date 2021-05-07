# LeetCode Cheatsheet (Rust)

##  所有権

## 文字操作・文字列操作
---
#### `&str` から `String`

```rust
let s_str = "string here";

let s_string = String::from(s_str);
let s_string = s_str.to_string();
```

---
#### `String` の文字がほしい
`chars` メソッドを使う. イテレータをベクタにすることも可能.
```rust
let s = String::from("hello");

let chs = s.chars(); // iterator

// assert_eq!(Some('h'), chs.next());
// assert_eq!(Some('e'), chs.next());

for val in chs {
	match val {
		Some(c) => {
			// do something with c
		},
		None => {
		}
	}
}
```

`char_indices` メソッドを使うと、`char` とインデックスを保持するイテレータをつくる.
```rust
let s = String::from("hello");

let chs = s.char_indices();
assert_eq!(Some((0, 'h')), chs.next());
assert_eq!(Some((1, 'e')), chs.next());
```

`char` のベクタがほしい場合
```rust
let s = String::from("hello");

let chs = s.chars().collect::<Vec<char>>();
assert_eq!('h', chs[0]);
```
---
#### `String` の中の数字がほしい
以前に書いた関数を参照.
```rust
// chars().peekable() で

fn strtou(s: &str) -> usize {
	let mut iter = s.chars().peekable();
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
---
#### `String` を分割したい
---
#### `String` を結合したい
`format!` を使う. 万能だが低速.
```rust

```

`push_str` を使って文字列を追加する
```rust
```
---
## 数学的操作

#### 累乗
`pow` で可能. 
```rust
let i = 42;
let n = i32::pow(i, 2); // i64, isize, u32, u64, usize でも
```

## `map` を使う

## `Vec` 操作

## イテレータ操作
[Iterator のドキュメント](https://doc.rust-lang.org/std/iter/trait.Iterator.html)  

かなりの種類の操作を行うことができる. イテレータをどうにかしたいときはドキュメントを見るとよい. 

#### インデックスと要素どちらもほしい
`enumerate` メソッドを使う.

```rust
let a = ['a', 'b', 'c'];
let mut iter = a.iter().enumerate();

assert_eq!(iter.next(), Some((0, &'a')));
assert_eq!(iter.next(), Some((0, &'b')));
assert_eq!(iter.next(), Some((0, &'c')));
```

#### イテレータの長さ
`count` メソッドを使う.

```rust
let count = iter.count();
```

#### イテレータを反転させる
`rev` メソッドを使う

```rust

```

#### n 番目の要素がほしい
`nth` メソッドを使う. 範囲外の参照に対しては `None` を返す.

```rust
let a = [1, 2, 3];
let iter = a.iter();

assert_eq!(iter.nth(0), Some(&2));
assert_eq!(iter.nth(19), None);
```

#### 関数 (クロージャ) からイテレータをつくる
```rust
iter::repeat_with(|| 0);

```


## Range
#### Range を反転させる
```rust
(0..end).rev();
```
## `HashMap` を使う

## `Option<Box<T>>` をなんとかしたい.
[参考: 再帰の場合](https://leetcode.com/problems/add-two-numbers/discuss/469977/Simple-Rust-solution-less0ms-2.1MBgreater)

