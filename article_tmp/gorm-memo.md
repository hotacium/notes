###### 2020-10-25 {#date}
# gorm のメモ
###### [Go](/tag/go) / [SQL] (/tag/sql) {#tags}
---
Go の ORM ライブラリである gorm についてのメモです.

## 1. 導入 
#### gorm 本体の導入
まず何も考えずに `go get` します (`-u` オプションにより古いものがあればアップデートされます.) .
```bash
$ go get -u gorm.io/gorm
```
#### SQL ドライバの導入 (例: PostgreSQL)
Go で各種 DBMS を操作するにはドライバが必要です. 今回は gorm が公開している PostgreSQL のドライバを利用します.
```bash
$ go get gorm.io/driver/postgres
```
## 2. ORM
ORM (Object-relational mapping) ライブラリはプログラミング言語のオブジェクトとデータベースのテーブルを紐づける機能を提供します. gorm は Go 言語のリレーショナルマッパーの一つです (Go では他にも sqlx などがあるようです). 

## 3. クエリ
## 参考
gorm の公式サイト
[gorm.io](https://gorm.io)