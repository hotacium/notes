---
title: "Rust で AtCoder をするための環境構築"
description: "Rust で AtCoder のための環境構築"
date: 2021-06-05
tags: ["Rust"]
---
<!-- # Rust を AtCoder をするための環境構築 -->


## AtCoder のための雛型をつくる

### atcoder-rust-base を使う
AtCoder を Rust で解くときに, 毎回の設定が面倒でした.  
例えば:
- Rust のツールチェーンを 1.42.0 にする.
- 使う外部クレートを `Cargo.toml` に明記する.
- いつも使うスニペットをコピペする.

そこで使えるのが, [atcoder-rust-base](https://github.com/rust-lang-ja/atcoder-rust-base) です.  
上記レポジトリでは AtCoder のテンプレートの雛型を提供しています. 詳しい説明は上記レポジトリの `README.md` に譲りますが, 面倒なコピペが楽になります. 

### 簡単にテンプレートを使う

上のテンプレートは [`cargo-generate`](https://crates.io/crates/cargo-generate) で使われることを想定しています. インストール方法は atcoder-rust-base の `README.md` に書かれています.

このツールは git レポジトリのテンプレートから Rust プロジェクトを生成するのを可能にするもので, 前述の `atcoder-rust-base` のテンプレートは以下のコマンドで呼び出されます:
```sh
$ cargo generate --name abc086c \
    --git https://github.com/rust-lang-ja/atcoder-rust-base \
    --branch ja
```

このコマンドを毎回入力するのも面倒なので, shell で関数 `atcargo` を書いて呼び出しています.

私は fish を使っているので `~/.config/fish/config.fish` に以下を追加:
```fish
function atcargo
  cargo generate \
    --name $argv \
    --git https://github.com/path/to/template \
    --branch template-branch
end
```

bash の場合は `~/.bashrc` に以下を追加:
```bash
atcargo() {
  cargo generate \
    --name $@ \
    --git https://github.com/path/to/template \
    --branch template-branch
}
```

この関数を使うと, 簡単にテンプレートを使うことができるようになります.
```sh
$ source ~/.config/fish/config.fish # 初回のみ更新した `config.fish` の内容を反映
$ atcargo [project_name]
```

### 自分のテンプレート
自分でフォークしたテンプレは [ここ](https://github.com/Hmiya6/atcoder-rust-base) にあります.

## 参考
- [atcoder-rust-base](https://github.com/rust-lang-ja/atcoder-rust-base)
- [AtCoder コンテストに Rust で参加するためのガイドブック](https://doc.rust-jp.rs/atcoder-rust-resources)

