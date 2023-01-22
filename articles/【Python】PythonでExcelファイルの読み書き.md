---
title: "【Python】PythonでExcelファイルの読み書き"
emoji: ""
type: ""
topics: []
published: false
---

Pythonって何でもできるんだなあ、と思います。

今回はPythonを使ってエクセルファイルの読み取り、書き込みなどを行ってみました。

尚、使用しているPythonのバージョンは、**Python3.5**です！  
  
* [Excelファイルの拡張子について](#Excelファイルの拡張子について)
* [1.ライブラリの種類](#1ライブラリの種類)
* [インストール](#インストール)  
   * [xlwtのインストール](#xlwtのインストール)  
   * [xlrdのインストール](#xlrdのインストール)  
   * [openpyxl のインストール](#openpyxl-のインストール)
* [Excel文書作成](#Excel文書作成)
* [セルに文字や数字を書く](#セルに文字や数字を書く)
* [セルに文字や数字を書く 2](#セルに文字や数字を書く-2)
* [シートの名前を取得](#シートの名前を取得)
* [指定したセルの値を読む](#指定したセルの値を読む)
* [シート全体のセルを読む](#シート全体のセルを読む)
* [参考文献](#参考文献)

### Excelファイルの拡張子について

Excelファイルには、「.xlsx」と、「.xls」という2種類の拡張子があります。どちらもオフィスのExcelを使って開けるのですが（Office 2016で確認済み）、次のような違いがあります。

・**.xlsx** \= **現在**主要なExcelファイル  
・**.xls** \= **1997から2003年までの間**使われていたExcelファイル

xlsxの方が高圧縮率なので同じ中身でもファイルサイズは小さくなるようです。（ソースは[ここ](http://holmes.hatenablog.com/entry/2013/03/22/113300)）  
  
  
### 1.ライブラリの種類

PythonでExcelファイルを扱うことのできるライブラリは、主に以下の3つがあります。  
  
**・openpyxl** 
**・xlwt** 
**・xlrd**

それぞれのライブラリは、次のようなことができます。

| ライブラリ名   | 利用可能な拡張子   | 読み込み | 書き込み |
| -------- | ---------- | ---- | ---- |
| xlrd     | .xlsx、.xls | 〇    | ×    |
| xlwt     | .xls       | ×    | 〇    |
| openpyxl | .xls、.xlsx | 〇    | 〇    |

よって、僕は**xlwt よりもopenpyxl を使うべき**だと思います。  
一応、両方のインストール方法を書きますが、使用法は**openpyxlのみについて記載しています**。  
  
### インストール

両方、**pip** を使ってインストールすることができます。

#### xlwtのインストール

コマンドプロンプトで以下の命令を実行してください。

pip install xlwt

その後、Pythonで

import xlwt

を実行し、エラーが出なければ成功です。  
  
#### xlrdのインストール

コマンドプロンプトで以下の命令を実行してください。

pip install xlrd

その後、Pythonで

import xlrd

を実行し、エラーが出なければ成功です。  
  
#### openpyxl のインストール

コマンドプロンプトで以下の命令を実行してください。

pip install openpyxl 

その後、Pythonで

import openpyxl

を実行し、エラーが出なければ成功です。
  
  
**ここからは、openpyxlについてのみ説明を行います。**  
  
  
### Excel文書作成

import openpyxl as px
book = px.Workbook()
book.save('sample.xlsx')

たったこの3行で、sample.xlsxという名前のEXCELファイルを作成することができます。保存場所はPythonファイルとおなじディレクトリです。

book.save(r'C:\Users\Owner\Desktop\sample2.xlsx')

3行目をこのように書くことによって、指定の場所に保存することもできます。  
  
### セルに文字や数字を書く

import openpyxl as px
wb = px.Workbook()
ws = wb.active

#Sheetの名前を設定
ws.title = "Sheet_1"

# セルに値を入れる
ws["A1"] = "ABCDE"

# 日本語もOK
ws["B1"] = "あいうえお"

# 数字もOK
ws["A3"] = 13

# 保存
wb.save('sample2.xlsx')
  
  
### セルに文字や数字を書く 2

もう一つ、このような書き方もあります。for文などを使って、一度に大量のセルにデータを書き込む場合は、こちらの書き方の方がおすすめです。  
**row**が縦軸の座標（EXCEL表記でいう1、2、3...の部分）を表していて、**column**が横軸の座標（EXCEL表記でいうA、B、C...の部分）を表しています。  
valueに代入された文字列または数値が、指定のセルに入力されます。

import openpyxl as px
wb = px.Workbook()
ws = wb.active

#Sheetの名前を設定
ws.title = "Sheet_1"

# セルに値を入れる
# row = 縦軸　（1、2、3...）
# column = 横軸　（A、B、C...）
ws.cell(row = 1, column = 1, value = 5)
ws.cell(row = 2, column = 3, value = 23)
ws.cell(row = 4, column = 6, value = "This is F4")

# 保存
wb.save('xample2.xlsx')

### シートの名前を取得

import openpyxl as px
wb = px.load_workbook('example2.xlsx')

#シート名を取得
print(wb.sheetnames)
#['Sheet_1']

EXCELファイルのセルの値を読むには、まずシートの名前を取得する必要があります。  
例えば、**Sheet\_1**という名前のシートを作ったエクセルファイルがあるとします。  
![f:id:pythonjacascript:20181124174123j:plain:h350](/images/ppythonjacascript2018112420181124174123.jpg "f:id:pythonjacascript:20181124174123j:plain:h350")

上のプログラムを実行すると、

Sheet_1

と、シートの名前が出力されます。  
  
  
### 指定したセルの値を読む

import openpyxl as px
wb = px.load_workbook('sample2.xlsx')

#シート名を取得
print(wb.sheetnames)
#['Sheet_1']

sheet = wb['Sheet_1']

#A1セルの値を表示
cell = sheet['A1']
print(cell.value)
#ABCDEF

print(sheet.cell(column=1, row=1).value)
#ABCDEF

#何も入力がないセルはNoneになる
print(sheet.cell(column=10, row=10).value)
#None

このプログラムを実行する前に、

1. sample2.xlsxというExcelファイルを作成し、
2. Sheet\_1という名前で新しくシートを作成し、
3. セルA1に何か文字を入力してください。

このような画像になると思います。  
![f:id:pythonjacascript:20181124174123j:plain:h350](/images/ppythonjacascript2018112420181124174123.jpg "f:id:pythonjacascript:20181124174123j:plain:h350")

また、このExcelファイルを、実行するPythonファイルと同じディレクトリにおいて実行しいてください。

上の写真のような設定で実行した場合、

ABCDEF
ABCDEF
None

と出力されていればOKです。  
  
  
### シート全体のセルを読む

![f:id:pythonjacascript:20181124175216j:plain](/images/ppythonjacascript2018112420181124175216.jpg "f:id:pythonjacascript:20181124175216j:plain")  
次に、このようなシートを作成し、次のプログラムを実行してください。  
シートの中に書き込まれているセルの値がすべてprintされるはずです。

import openpyxl as px book = px.load\_workbook('sample2.xlsx') active\_sheet = book.activefor column in active\_sheet.columns:print('