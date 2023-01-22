---
title: "【python】VPythonのインストールとウィンドウ表示"
emoji: "🤖"
type: "tech"
topics: undefined
published: false
---

**VPython** とは、Pythonで**3Dの描画処理やアニメーション**を行うためのライブラリです。

[VPython](http://vpython.org/)

今回は、このVPythonを使って、簡単なウィンドウ表示までを行ってみます。

* [インストール方法➀](#インストール方法)  
   * [エラー内容](#エラー内容)  
   * [アンインストール方法](#アンインストール方法)
* [インストール方法②](#インストール方法-1)
* [サンプルプログラム](#サンプルプログラム)

### インストール方法➀

この方法は、WinPythonを使っている場合に有効なようです。

pipを使います。コマンドプロンプトで以下のコマンドを実行します。

pip install vpython

これだけです。

もし、この方法を使ってエラーが出た場合（下の様子）、一度、VPythonを次のコマンドでアンインストールして、方法②を試してください。

#### エラー内容

![f:id:pythonjacascript:20181226001127j:plain](/images/ppythonjacascript2018122620181226001127.jpg "f:id:pythonjacascript:20181226001127j:plain")  
このようなエラーが出てしまいます。（Anaconda、Python3.6で実行）

SyntaxError: unexpected EOF while parsing
Traceback (most recent call last):
（省略）
  File "C:\Users\Owner\Anaconda3\lib\ast.py", line 35, in parse
    return compile(source, filename, mode, PyCF_ONLY_AST)
  File "<unknown>", line 9001
    ...}
       ^
SyntaxError: unexpected EOF while parsing

#### アンインストール方法

コマンドプロンプトで以下のコマンドを実行する。

pip uninstall vpython

### インストール方法②

以下のサイトにアクセスします。  
<http://vpython.org/contents/download%5Fwindows.html>

  
「Windows downloads for VPython 6」の下からお使いのPython環境にあったインストーラをダウンロードします。  
  
### サンプルプログラム

import vpython as vp

def main():
    vp.box()

if __name__ == '__main__':
    main()