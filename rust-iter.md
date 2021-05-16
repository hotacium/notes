
# [Rust] Iterator のメソッド達

メソッドつなげるの楽しい

## 要素を調べる
- 要素が存在するか調べる
- 要素の位置を調べる
- 要素の値を調べる
- 条件にあう要素を調べる

### `all` / `any`

- `all`: イテレータ内部の**すべての要素**が条件を満たしていれば `true` を返す. 
- `any`: イテレータ内部の要素のうち**一つでも**条件を満たしていれば `true` を返す.

```rust
fn main() {
    let mut iter = [1, 2, 3, 4, 5].iter();

    // 要素 (elem) すべてが 0 より大きければ true
    assert!(iter.all(|&elem| elem > 0));

    // 要素 (elem) すべてが 3 より小さければ true
    assert!(!iter.all(|&elem| elem < 3));

    // 要素 (elem) のうち一つでも 0 より小さければ true
    assert!(!iter.any(|&elem| elem < 0));

    // 要素 (elem) のうち一つでも 3 より小さければ true
    assert!(iter.any(|&elem| elem < 3));
}
```

### `find`

- `find`: イテレータ要素のうち条件をみたす**最初**のもの(の参照)を返す

```rust
fn main() {
    let mut iter = [1, 2, 3, 4, 5].iter();
    
    // 2 より大きい最初の要素を返す
    assert_eq!(iter.find(|&&elem| elem > 2), Some(&3));

    // 2 より大きい最初の要素を返す
    assert_eq!(iter.find(|&&elem| elem > 2) ,Some(&4));

    // `find` はイテレータを進めながら要素を調べるため `next` をすると次の要素になる
    assert_eq!(iter.next(), Some(&5));
}
```

### `nth`

- `nth`: イテレータの N 番目の要素を返す

```rust
fn main() {
    let mut iter = [1, 2, 3, 4, 5].iter();
    
    // 1 番目の要素を返す
    assert_eq!(iter.nth(1), Some(&2));

    // `nth` はイテレータを進めるため、`next` では `Some(&3)` が返される
    assert_eq!(iter.next(), Some(&3));

    // 現時点の状態での 1 番目の要素を返す
    assert_eq!(iter.nth(1), Some(&5));

    assert_eq!(iter.next(), None);
}
```
### `position` / `rposition`

- `position`: 条件にあう最初の要素の**インデックス**を返す
- `rposition`: **後ろ** (右) から要素を調べて 条件を満たす最も右側の要素のインデックスを返す

```rust
fn main() {
    let mut iter = [1, 2, 3, 4, 5].iter();
    
    // 1 より大きい最初のインデックスを返す
    assert_eq!(iter.position(|&elem| elem > 1), Some(1));

    // `position` はイテレータを進める
    assert_eq!(iter.next(), Some(&3));

    // 1 より大きい最も右側の要素のインデックスを返す
    assert_eq!(iter.rposition(|&elem| elem > 1), Some(1));
    
    // `rposition` はイテレータを進めない
    assert_eq!(iter.next(), Some(&4));
    
    // ただし、 `rposition` で返された要素は消えるので `Some(&5)` はない
    assert_eq!(iter.next(), None);
}
```

### `max` / `min` / `max_by` / `max_by_key` / `min_by` / `min_by_key`

- `max`: 
- `min`:
- `max_by`: 
- `min_by`: 
- `max_by_key`: 
- `min_by_key`: 

## イテレータ自体を操作する
- イテレータをつなげる・分割する
- イテレータ全体を一括で変換する
- イテレータに新しい性質を付け加える

### `chain` / `zip` / `unzip`
### `skip` / `skip_while`
### `take` / `take_while`
### `enumerate`
### `rev`
### `peekable`
### `cycle`
### `cloned` / `copied`
### `flatten`

## イテレータの要素を操作する
イテレータ内部の要素を操作してイテレータを変化させる.  
(ただし `map` は `map` 系統として別に書いてあります.)

### `for_each` / `try_for_each`
### `filter`
### `inspect`
### `by_ref`
### `scan`

## `map` 系統
`map` と他の操作を一度に行う.

### `map` / `map_while`
### `flat_map`
### `find_map`
### `filter_map`

## イテレータを一つの値にする
イテレータの要素から一つの値を作る (?)

### `sum`
### `product` 
### `collect`
### `fold` / `try_fold`
### `reduce`

## イテレータを進める

### `step_by`
### `advance_by`

## その他
### `partition` / `partition_in_place` / `is_partitioned`
### `is_sorted` / `is_sorted_by` / `is_sorted_by_key`
