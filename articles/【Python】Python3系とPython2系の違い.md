---
title: "【Python】Python3系とPython2系の違い"
emoji: ""
type: ""
topics: []
published: false
---

Pythonには、Python2シリーズと、Python3シリーズの二つがあります。もちろん、Python3が後に発表されたPythonで、それぞれ文法や下記化tなどが微妙に異なっています。

この記事では、Python2系とPython3系の違いをまとめてみました。  
  
* [print関数](#print関数)
* [input関数とraw-input関数](#input関数とraw-input関数)
* [ライブラリ](#ライブラリ)
* [Python2のコードをPython3に書き換える](#Python2のコードをPython3に書き換える)
* [参考文献](#参考文献)

### print関数

Python2では、

print "Hello, World!"

と書いてもエラーは出ずに、「Hello,World！」と出力されましたが、Python3では上の文はエラーになります。  
代わりにかっこを使って

print("Hello, World!")

と書く必要があります。

尚、Python2で

print("Hello, World!")

と書いてもまったく問題はありません。  
  
### input関数とraw-input関数

input()関数は、キーボードからの入力を受け取る関数です。そして、同じ機能の関数で、raw\_input()関数があります。  
この2つの違いは、キーボードからの入力を文字式とみるか、Python式とみるかです。

例えば、

input()
#この関数をPython3で実行し、
#キーボードで「1+2」と打つと、
#「1+2」と文字列で表示される

このように、**input()関数は入力された文字列をそのまま（文字列として）返します。**

一方、

raw_input()
#この関数をPython2で実行し、
#キーボードで「1+2」と打つと、
#「3」と計算結果で表示される

このように、**raw\_input()関数は入力された文字列をPython式として返します**。

  
また、それぞれの関数は、下の表のように動いたり動かなかったりします。

| 関数名          | Python2系で実行可能？ | Python3系で実行可能？ |
| ------------ | -------------- | -------------- |
| raw\_input() | **〇**          | ×              |
| input()      | ×              | **〇**          |

  
もし、Python2でも3でも動くようなコードを書きたい場合は、

try:
    input = raw_input
except NameError:
    pass

と書いてください。  
  
### ライブラリ

たいていのライブラリはPython2用のものとPython3用のものの両方が用意されています。  
しかし、一部のライブラリはまだ**Python3未対応**のライブラリがあります。

Python2のみ対応しているライブラリは以下のようなものがあります。  
・PyODE  
・PyOpenGL  
・VPython

記事作成から時間がたって、上のようなライブラリもPython3対応になっているかもしれません。  
このサイトでPython3系のライブラリがあるかを確認してみてください。  
[Python Extension Packages for Windows - Christoph Gohlke](https://www.lfd.uci.edu/~gohlke/pythonlibs/)

  
### Python2のコードをPython3に書き換える

「**2to3.exe**」を使うと、Python2のコードをPython3に自動書き換えすることができます。

使い方の例としては、

2to3 -w (Python3に変換する.pyファイル)

のように書きます。「**\-w**」は「**上書きせずに、新しくファイルを作る**」という意味です。

なお、2to3.exeを使うには、「**○○\\Anaconda\\Scripts**」（2to3.exeが存在するディレクトリ）を**環境変数のPathに追加**していなければなりません。

環境変数の追加方法については、  
[shizenkarasuzon.hatenablog.com](https://shizenkarasuzon.hatenablog.com/entry/2018/10/29/220446#3%E7%92%B0%E5%A2%83%E5%A4%89%E6%95%B0%E3%81%AE%E8%A8%AD%E5%AE%9A)  
を見てください。  
  
  
### 参考文献

[stackoverflow.com](https://stackoverflow.com/questions/21731043/use-of-input-raw-input-in-python-2-and-3)