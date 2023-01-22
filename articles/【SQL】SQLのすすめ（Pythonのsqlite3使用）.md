---
title: "【SQL】SQLのすすめ（Pythonのsqlite3使用）"
emoji: "🤖"
type: "tech"
topics: undefined
published: false
---

* [SQLとMySQLとSQLiteとRDBMSについて](#SQLとMySQLとSQLiteとRDBMSについて)
* [サンプルプログラム](#サンプルプログラム)
* [実行結果](#実行結果)
* [基本操作](#基本操作)
* [テーブルを作成](#テーブルを作成)
* [テーブルを削除する](#テーブルを削除する)
* [テーブルに要素を挿入する](#テーブルに要素を挿入する)
* [テーブルの要素を全て削除する](#テーブルの要素を全て削除する)
* [条件に合う要素を削除する](#条件に合う要素を削除する)
* [全ての要素を順番に取り出す](#全ての要素を順番に取り出す)
* [特定の要素を順番に取り出す](#特定の要素を順番に取り出す)
* [文字列検索](#文字列検索)  
   * [部分一致](#部分一致)  
   * [文字列検索の複数条件](#文字列検索の複数条件)

### SQLとMySQLとSQLiteとRDBMSについて

SQLとは、

> 関係データベース管理システム (RDBMS) において、データの操作や定義を行うためのデータベース言語である

（Wikipediaより）

つまり、データベースを操作する言語です。

一般的にデータベースというと、以下の3種類があります。  
![f:id:pythonjacascript:20200325180105j:plain](/images/ppythonjacascript2020032520200325180105.jpg "f:id:pythonjacascript:20200325180105j:plain")  

* 階層型データベース
* ネットワーク型データベース
* リレーショナルデータベース（RDB）

そして、3つ目のリレーショナルデータベースを管理するシステムを**RDBMS**(relational database management system)その管理を行うのに必要な言語が**SQL**です。

  
そして、SQL言語を使ってデータベースを操作するのに、MySQLやSQLiteといったデータベースシステムがあります。

それぞれの違いについては[ここ](https://teratail.com/questions/106167)がわかりやすいです。

### サンプルプログラム

# -*- coding: utf-8 -*-

import sqlite3 #この行でエラーが出たら、pip instal sqlite3をコマンドプロンプトで実行！

# データベースに接続
conn = sqlite3.connect('example.db')
c = conn.cursor()

# テーブルの作成
c.execute("CREATE TABLE people(id real, name text, age int, place text)")

# データの挿入
c.execute("INSERT INTO people VALUES (1, 'IKARI SHINJI',     14,   'EVA-1')")
c.execute("INSERT INTO people VALUES (2, 'AYANAMI REI',      14,   'EVA-0')")
c.execute("INSERT INTO people VALUES (3, 'SOURYU ASUKA',     14,   'EVA-1')")
c.execute("INSERT INTO people VALUES (4, 'KATSURAGI MISATO', 29,   'NERV')")
c.execute("INSERT INTO people VALUES (5, 'KEEL',             314,  'ZEELE')")

# 挿入した結果を保存
conn.commit()

# 要素の削除
# c.execute("DELETE FROM people;") #テーブル内のすべてのデータを削除

c.execute("DELETE FROM people WHERE age > 15")

# 下の様に AND OR で複数条件を指定できる
# c.execute("DELETE FROM people WHERE id = 1 OR name = 'SOURYU ASUKA'")

conn.commit()


# データベースに接続する
conn = sqlite3.connect('example.db')
c = conn.cursor()

# レコードを生年月日の降順で取得する
for row in c.execute('SELECT * FROM people'):
    print(row)

# 'SELECT * FROM people WHERE age < 15'のようにして条件指定できる


# テーブル削除
c.execute("DROP TABLE people")

# データベースへのアクセスが終わったら close する
conn.close()

### 実行結果

(1.0, 'IKARI SHINJI', 14, 'EVA-1')
(2.0, 'AYANAMI REI', 14, 'EVA-0')
(3.0, 'SOURYU ASUKA', 14, 'EVA-1')
  
  
### 基本操作

**c.execute("\[command\]")** を行うことで、SQLコマンドを実行できます。  
SQLコマンドを使うと、データベースにアクセスし、その中のデータについて  
・データの作成  
・データの削除  
・データの検索  
...などを行うことができます。  
  
### テーブルを作成

CREATE TABLE people(id real, name text, age int, place text)

というコマンドは、peopleというテーブルを作成します。そして、その中に  
・real型（実数）の変数（変数名はid）  
・text型（文字列）の変数 name  
・int型（正負整数）の変数 age  
・text型（文字列）の変数 place  
という4つの値が一つの要素あたりに含まれるよ、ということを示しています。  
  
SQLのデータ型は以下のようなものがあります。

| bigint, int8             | 8バイト符号付き整数                    |
| ------------------------ | ----------------------------- |
| bigserial, serial8       | 自動増分8バイト整数                    |
| bit \[ (n) \]            | 固定長ビット列                       |
| boolean, bool            | 論理値（真/偽）                      |
| bytea                    | バイナリデータ（"バイトの配列（byte array）"） |
| cidr                     | IPv4もしくはIPv6ネットワークアドレス        |
| date                     | 暦の日付（年月日）                     |
| double precision, float8 | 倍精度浮動小数点（8バイト）                |
| inet                     | IPv4もしくはIPv6ホストアドレス           |
| int, int4, integer       | 4バイト符号付き整数                    |
| json                     | テキストのJSONデータ                  |
| macaddr                  | MACアドレス                       |
| money                    | 貨幣金額                          |
| real, float4             | 単精度浮動小数点（4バイト）                |
| smallint, int2           | 2バイト符号付き整数                    |
| smallserial, serial2     | 自動増分2バイト整数                    |
| serial, serial4          | 自動増分4バイト整数                    |
| text                     | 可変長文字列                        |
| time                     | 時刻（時間帯なし）                     |
| xml                      | XMLデータ                        |

  
Transact-SQL または Microsoft .NET Framework で、ユーザー独自のデータ型を定義することもできます。  
[CREATE TYPE (Transact-SQL) - SQL Server | Microsoft Docs](https://docs.microsoft.com/ja-jp/sql/t-sql/statements/create-type-transact-sql?view=sql-server-ver15)  
  
  
### テーブルを削除する

DROP TABLE people

DROP TABLE \[テーブル名\] でテーブルを削除できます  
  
### テーブルに要素を挿入する

INSERT INTO people VALUES (1, 'IKARI SHINJI',     14,   'EVA-1'
INSERT INTO  [テーブル名]  VALUES( hoge, hoge2, hoge3, ......)

  
INSERT INTO を使うことで、テーブルに要素を追加することができます。  
  
### テーブルの要素を全て削除する

DELETE FROM people;

DELETE FROM \[テ-ブル名\]を行うことで、現在そのテーブルに含まれるすべてのデータを削除します。  
  
  
### 条件に合う要素を削除する

#各要素のidの値が3未満の人を削除する
DELETE FROM people WHERE id < 3

# ageの値が30以下　もしくは　名前がSOURYU ASUKAの人を削除する
DELETE FROM people WHERE age < 30 OR name = 'SOURYU ASUKA'"
  
  
### 全ての要素を順番に取り出す

SELECTコマンドは本来条件に合うものを取り出すコマンドですが、「SELECT \* FROM people」の様に書くことで、全ての要素を取得できます

conn = sqlite3.connect('example.db')
c = conn.cursor()

# レコードを生年月日の降順で取得する
for row in c.execute('SELECT * FROM people'):
    print(row)
  
  
### 特定の要素を順番に取り出す

conn = sqlite3.connect('example.db')
c = conn.cursor()

# 15歳以下の一覧を取得する
for row in c.execute('SELECT * FROM people WHERE age < 15'):
    print(row)

### 文字列検索

文字列検索は SELECT と LIKE を使って行います。  
基本的な形は、

SELECT * FROM [データベース名] WHERE [変数名] LIKE 'その変数の中に含まれて欲しい文字’

という感じです

# データベースに接続する
conn = sqlite3.connect('example.db')
c = conn.cursor()

# レコードを生年月日の降順で取得する
for row in c.execute('SELECT * FROM people WHERE name LIKE \'SOURYU ASUKA\';'):
    print(row)

  
一番上のサンプルコードを一部このように（↑）書き直して実行すると、

(3.0, 'SOURYU ASUKA', 14, 'EVA-1')

と表示されます  
  
#### 部分一致

上の

SELECT * FROM people WHERE name LIKE 'SOURYU ASUKA'

は完全一致を表し、

SELECT * FROM people WHERE name = 'SOURYU ASUKA'

と同義です。

部分一致で検索するには、その文字列の前後どちらか（もしくは両方）に％を付けることで0文字以上の文字列があっても検索にヒットします

SELECT * FROM people WHERE name LIKE 'SOU'
# %がないのでSOUの前後に文字がないもののみ検索ヒット
# SOURYU →×、KENSOU→×、SOU→〇

SELECT * FROM people WHERE name LIKE '%SOU%'
# %が前についているので、「SOU」の前に文字があっても検索ヒット
# SOURYU →×、KENSOU→〇、SOU→〇

SELECT * FROM people WHERE name LIKE 'SOU%'
# %が後ろについているので、「SOU」の後ろに文字があっても検索ヒット
# SOURYU →〇、KENSOU→×、SOU→〇

SELECT * FROM people WHERE name LIKE '%SOU%'
# %が前後についているので、「SOU」の前後に文字があっても検索ヒット
# SOURYU →〇、KENSOU→〇、SOU→〇

同じような事が、「＿」にもあります。  
ただし、「％」は0文字以上の任意の文字列だったのに対し、「＿」は**任意の一文字**の代わりとして使います。  
  
#### 文字列検索の複数条件

序長になりますが、下のように書くことで検索キーワードが複数の場合も対応できます  
↓変数nameのなかに「S」と「O」の両方が含まれるものを検索

SELECT * FROM people WHERE name LIKE "%S%" and name LIKE "%O%"