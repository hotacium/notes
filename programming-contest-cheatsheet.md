# アルゴリズムの選定

---
## 動的計画法
- 漸化式のような性質をもつとき
- 1つ or 2つ前の結果を使うと楽なとき
- 他の試行結果を使うと楽なとき

#### 解説
- [典型的な  DP](https://qiita.com/drken/items/a5e6fe22863b7992efdb)

#### 問題
- [Longest Palindromic Substring](https://leetcode.com/problems/longest-palindromic-substring)

---
## 再帰
- ループを簡単に回せないとき、ループだと無駄が多いとき

#### 問題
- [Add Two Numbers](https://leetcode.com/problems/add-two-numbers)

----
## Linked-List
- Rust だと `Option<Box<T>>` とか `Option<Rc<RefCell<T>>>` とかでリンクするので処理が面倒. C++ とかのほうがよい. 


#### 問題
- [Add Two Numbers](https://leetcode.com/problems/add-two-numbers)

---
## HashMap
- 存在を問いたいとき (e.g. ある文字が以前に出現したか. )
- 対応する値を得たいとき (e.g. DNA のある塩基に対する対応塩基)
- 速さを得た代わりにメモリ消費量が多い

#### 問題
- [Longest Substring Without Repeating Characters](https://leetcode.com/problems/longest-substring-without-repeating-characters/)

---
## 