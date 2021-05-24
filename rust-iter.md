
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

- `max` / `min`: 最大/最小の要素を得る
- `max_by` / `min_by`: 比較関数を指定して要素を比較
- `max_by_key` / `min_by_key`: 関数を用いて要素を加工して最大/最小を取得

`max` / `min` の例
```rust
fn main() {
    let iter = [1, 2, 3, 4, 5].iter();
    
    // 単純に最大/最小
    assert_eq!(iter.clone().max(), Some(&5));
    assert_eq!(iter.min(), Some(&1));
}
```

`max_by_key` / `min_by_key` の例
```rust
fn main() {
    let iter = [1, 2, 3, 4, 5].iter();

    // 比較する要素に加工を加えている
    assert_eq!(iter.clone().max_by_key(|&x| -1*x), Some(&1));
    assert_eq!(iter.min_by_key(|&x| -1*x), Some(&5));
}
```

`max_by` / `min_by` の例
```rust
fn main() {
    let iter = [("foo", 1203), ("bar", 1409), ("hoge", 410)].iter();
    
    // 比較の方法を指定している
    let max = iter.clone().max_by(|elem_0, elem_1| {
        let (num_0, num_1) = (elem_0.1, elem_1.1);
        num_0.cmp(&num_1) // `Ordering` を返す
    });
    assert_eq!(max, Some(&("bar", 1409)));

    // 以下の方法でも実装可能
    // let max = iter.clone().max_by_key(|&x| x.1);
    // assert_eq!(max, Some(&("bar", 1409))); // 
    
    let min = iter.min_by(|elem_0, elem_1| {
        let (num_0, num_1) = (elem_0.1, elem_1.1);
        num_0.cmp(&num_1)
    });
    assert_eq!(min, Some(&("hoge", 410)));
}
```

## イテレータ自体を操作する
- イテレータをつなげる・分割する
- イテレータ全体を一括で変換する
- イテレータに新しい性質を付け加える

### `chain` / `zip` / `unzip`
- `chain`: 2つのイテレータを連結する
- `zip` / `unzip`: 2つの要素を持つタプルをもつイテレータをつくる / 分解する

`chain` 例
```rust
fn main() {
    let iter_1 = [1, 2, 3, 4, 5].iter();
    let iter_2 = [2, 2, 2, 4, 4].iter();
    
    let res = iter_1.chain(iter_2).map(|&elem| elem).collect::<Vec<_>>();
    assert_eq!(res, vec![1, 2, 3, 4, 5, 2, 2, 2, 4, 4]);
}
```
`zip` 例
```rust
fn main() {
    let iter_1 = [1, 2, 3].iter();
    let iter_2 = [3, 4, 5, 6].iter(); // 余る要素は省かれる
    
    let res = iter_1.zip(iter_2).map(|x| {
        let (&x1, &x2) = x;
        x1 + x2
    }).collect::<Vec<_>>();
    assert_eq!(res, vec![4, 6, 8]);
}
```
### `skip` / `skip_while`
- `skip`: 任意の数だけスキップしたイテレータを作る
- `skip_while`: false が出現するまでスキップしたイテレータをつくる

```rust
fn main() {
    let iter = [2, 4, 5, 6, 8].iter();
    let mut iter = iter.skip(2);
    
    assert_eq!(iter.next(), Some(&5));
    assert_eq!(iter.next(), Some(&6));
    assert_eq!(iter.next(), Some(&8));
    assert_eq!(iter.next(), None);
    
    let iter = [2, 4, 5, 6].iter();
    // false が出現するまでスキップするが、
    // この例では最初が false なためスキップされない
    let mut iter = iter.skip_while(|&&elem| 5 <= elem && elem <= 6);
    
    assert_eq!(iter.next(), Some(&2));
    assert_eq!(iter.next(), Some(&4));
    assert_eq!(iter.next(), Some(&5));
    assert_eq!(iter.next(), Some(&6));
    assert_eq!(iter.next(), None);
}

```

### `take` / `take_while`
- `take`: 任意の数
-
### `enumerate`
### `rev`
### `peekable`
### `cycle`
### `cloned` / `copied`
### `flatten`
### `by_ref`

## イテレータの要素を操作する
イテレータ内部の要素を操作してイテレータを変化させる.  
(ただし `map` は `map` 系統として別に書いてあります.)

### `for_each` / `try_for_each`
### `filter`
### `inspect`

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
### `scan`
### `reduce`

## イテレータを進める

### `step_by`
### `advance_by`

## その他
### `partition` / `partition_in_place` / `is_partitioned`
### `is_sorted` / `is_sorted_by` / `is_sorted_by_key`
