---
title: "データ収集"
description: "コンピュータ内部のデータを収集する"
date: 2021-02-21
tags: ["Linux", "Security"]
---

## データの例
* ログファイル
* コマンド履歴
* 一時ファイル
* ユーザデータ
* ブラウザ履歴
* Windows レジストリ

## `cut`
ファイルの指定した位置を抽出するために使用する. 与えられたファイルを 1 行ずつ読み取り、オプションで条件をして抽出をする.  

`cut --help` 抜粋
```
  -b, --bytes=LIST        select only these bytes
  -c, --characters=LIST   select only these characters
  -d, --delimiter=DELIM   use DELIM instead of TAB for field delimiter
  -f, --fields=LIST       select only these fields;  also print any line
                            that contains no delimiter character, unless
                            the -s option is specified
  -n                      (ignored)
      --complement        complement the set of selected bytes, characters
                            or fields
  -s, --only-delimited    do not print lines not containing delimiters
      --output-delimiter=STRING  use STRING as the output delimiter
                            the default is to use the input delimiter
  -z, --zero-terminated    line delimiter is NUL, not newline
      --help     display this help and exit
      --version  output version information and exit
```

## `file`
ファイルのファイル形式を特定するために使用する.
