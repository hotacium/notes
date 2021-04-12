---
title: "コマンドラインのメモ"
description: "bash/zsh を使うときに忘れるので"
date: 2021-02-21
tag: ["Linux", "Zsh", "Bash"]
---

## stdin stdout stderr

プロセスは 3 つの異なる入出力のファイルディスクリプタを持つ.  
* 標準入力 (stdin): スクリプトの入力元. ユーザーによる直接入力だけでなく、ファイルなどからの入力にも対応.
* 標準出力 (stdout): スクリプトの出力先. ファイルなどへの出力も可能.
* 標準エラー出力 (stderr): エラーの出力先.

## リダイレクトとパイプ

stdin, stdout, stderr を入出力先を変更
```sh
# example コマンド

# < で標準入力を変更
# > で標準出力を変更
# 2> で標準エラー出力を変更
# &1 でファイルディスクリプタを 1 へ変更 (今回の場合 data.out へ)
example < data.in > data.out 2> &1

# &> で標準出力、標準エラー出力を統合
example &> data.out

# 追記する (append)
example >> data.out

# 標準出力を破棄
example > /dev/null

# パイプで複数のコマンドの入出力を接続
# 先行するスクリプトの出力を | 以降のスクリプトの標準入力とする
example | grep "hello"

```

## sh変数

```sh
# 変数の登録
VAR = "オラーーーーーーー！！！！"

# 置換
VERB = 'I said "$VAR"'

# 出力を変数へ格納
OUT = $(echo $VERB)
```

## パラメータ

`parameters.sh`
```sh
#! /bin/bash

echo $#
echo $0
echo $1
echo $2
echo $3
```

```sh
$ sh parameters.sh Hello World !
3
parameters.sh
Hello
World
!
```

## 値の評価

#### `if` 構文
パイプを用いている場合は最後の値を評価
```sh
# pdf がなくても true
# grep 自体は成功しているため
if ls | grep pdf # == true
```

#### `[[]]` を用いた値の評価
* ファイルの属性を評価する場合
* 値の比較を行う場合

#### ファイルの属性
```sh
# ファイルの属性
if [[ -e $FILENAME ]]
then
	echo $FILENAME does exist.
fi
```
* `-d`: ディレクトリか否か
* `-e`: ファイルか否か
* `-r`: 読み取り可能か
* `-w`: 書き込み可能か
* `-x`: 実行可能か

#### 値の比較
```sh
# 値の比較
if [[ $VAL -eq 42 ]]
then
	echo "VAL is equal to 42"
fi
```
* `-eq`: equal to
* `-lt`: less than
* `-gt`: greater than

`(())` を用いて値の比較も可能
```sh
# 値の比較
if (( VAL == 42))
then
	echo "VAL is equal to 42"
fi
```
#### `&&` と `||`
* `&&`: 前のコマンドが成功した場合にのみ次のコマンドを実行
* `||`: 前のコマンドが失敗した場合にのみ次のコマンドを実行

```sh
# echo の実行如何に拘らず exit
[[ -d $DIR ]] || echo "$DIR is not found."; exit
# echo が実行された場合にのみ exit
[[ -d $DIR ]] || { echo "$DIR is not found."; exit }
```

## `for`/`while` ループ
#### `for` ループ
```sh
for (( i=0; i < 100; i++))
do
	echo $i
done
```
```sh
for VAL in $(ls | grep .sh)
do
	echo $VAL
done
```
#### `while` ループ
```sh
i=0
while (( i < 10))
do 
	echo $i
	let i++
done
```

## パターンマッチング
* `*`: 任意の文字数の任意の文字にマッチ
* `?`: 任意の一文字にマッチ
* `[]`: []内の任意の一文字にマッチ
* `[^]`, `[!]`: [^]内以外の任意の一文字にマッチ
* `[:alnum:]`: 英数字
* `[:alpha:]`: 英字
* `[:ascii:]`: ASCII 文字
* その他: [Pattern Matching](http://www.gnu.org/savannah-checkouts/gnu/bash/manual/bash.html#Pattern-Matching)

\* 正規表現ではない.


