---
title: "linking with `cc` failed"
description: "リンクエラーの対処"
date: 2021-04-18
tags: ["Rust"]
---


何度か出てきたのでメモを残しておく.

## エラーコード
Rust をコンパイルしているときに
```
error: linking with `cc` failed: exit code 1
```
が出てくることがある.

## 原因と対策
エラーコードを読むとリンクに失敗していることがわかる.

#### 1. リンカがない
ubuntu の場合、
```
sudo apt install gcc # gcc のみをインストール
# or
sudo apt install build-essential # gcc を含んだ開発パッケージ一式
```
で gcc をインストール.

ほかのディストリビューションでも探せば同じようなパッケージがあるはず.

#### 2. Rust のバージョンが古い
Rust のバージョンが古かったために リンクエラーが発生したことがあった. 

```
rustup update
```

で Rust のアップデートをすると解消した.

## 参考
ない