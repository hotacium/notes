# アルゴリズムとデータ構造のあれこれ

## ロブ・パイクのプログラミング5か条
> Rob Pike's 5 Rules of Programming
> - Rule 1. You can't tell where a program is going to spend its time. Bottlenecks occur in surprising places, so don't try to second guess and put in a speed hack until you've proven that's where the bottleneck is.
> - Rule 2. Measure. Don't tune for speed until you've mesured, and even then don't unless one part of the code overwhelms the rest.
> - Rule 3. Fancy algorithms are slow when n is small, and n is usually small. Fancy algorithms have big constants. Until you know that n is frequently going to be big, don't get fancy. (Even if n does get big, use Rule 2 first.)
> - Rule 4. Fancy algorithms are buggier than simple ones, and they're much harder to implement. Use simple algorimthms as well as simple data structures.
> - Rule 5. Data dominates. If you've chosen the right data structures and organized things well, the algorithms will almost always be self-evident. Data structures, not algorithms, are central to programming.

from [Rob Pike's 5 Rules of Programming](http://users.ece.utexas.edu/~adnan/pike.html)

- その1. プログラムがどこで時間を使うかはわからない. ボトルネックは驚くような場所で起こるものだから、ボトルネックがどこに存在するか発見するまでは、長考して速くしようとしないこと.
- その2. **計測せよ**. 計測するまで、そしてかつ、ある部分が他の部分のコードを圧倒するようでなければ、スピードを追い求めるようなことはするな.
- その3. 複雑なアルゴリズムは n が小さいときは遅いもので、そして大抵の場合 n は小さい. 複雑なアルゴリズムには大きな制約がある. n が頻繁に大きくなるとわかるまでは複雑にするな. (仮に n が大きくなった場合でも、まず その 2 に従え.)
- その4. 複雑なアルゴリズムは単純なものに比べてバグりやすく、はるかに実装が難しい. 単純なアルゴリズムと単純なデータ構造を使え.
- その5. データこそが優先される. 正しいデータ構造を選んでうまく整理すれば、アルゴリズムはほとんど自明になる. **アルゴリズムではなくデータ構造をプログラミングの中心に据えよ**.

---
## ソート

比較ソートは O(n log n) のマージソート、クイックソート、ヒープソートを覚える.

並列ソートの流れを理解できない場合は、[ソーティングネットワーク](https://ja.wikipedia.org/wiki/%E3%82%BD%E3%83%BC%E3%83%86%E3%82%A3%E3%83%B3%E3%82%B0%E3%83%8D%E3%83%83%E3%83%88%E3%83%AF%E3%83%BC%E3%82%AF)を使うと理解が容易になる. 


#### マージソート
#### クイックソート
#### ヒープソート

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
- Rust だと `Option<Box<T>>` とか `Option<Rc<RefCell<T>>>` とかでリンクを表現するので処理が面倒. C++ とかのほうがよい. 


#### 問題
- [Add Two Numbers](https://leetcode.com/problems/add-two-numbers)

---
## HashMap
- 存在を問いたいとき (e.g. ある文字が以前に出現したか)
- 対応する値を得たいとき (e.g. DNA のある塩基に対する対応塩基)
- 速さを得た代わりにメモリ消費量が多い

速さに関しては、確かに `HashMap` を用いた探索は O(1) であるが、そもそも hashing 自体が遅いようなことを聞いたこともあるため、要検証.

#### 問題
- [Longest Substring Without Repeating Characters](https://leetcode.com/problems/longest-substring-without-repeating-characters/)

---
## Two pointer アルゴリズム (尺取り法)

