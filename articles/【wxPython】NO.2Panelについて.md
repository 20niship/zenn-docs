---
title: "【wxPython】NO.2   Panel について"
emoji: "🤖"
type: "tech"
topics: []
published: false
---

前回の記事で、wxPythonを使って、GUIアプリの最も基本的なウィンドウの作成を行いました。

[shizenkarasuzon.hatenablog.com](https://shizenkarasuzon.hatenablog.com/entry/2018/09/22/164615)

  
この記事では、このウィンドウの中に**ボタンやテキストなどを表示するために必要**な、**Panel** という考え方についてまとめています。  
  
* [1.Panelとは](#1Panelとは)
* [2.サンプルプログラム](#2サンプルプログラム)
* [3.実行結果](#3実行結果)
* [プログラム解説](#プログラム解説)
* [1.パネルの作成](#1パネルの作成)  
   * [2.描画](#2描画)
* [2.パネルの見た目の設定](#2パネルの見た目の設定)  
   * [背景色の設定](#背景色の設定)  
   * [パネルの表示位置の変更](#パネルの表示位置の変更)  
   * [パネルの大きさの変更](#パネルの大きさの変更)
* [参考文献：](#参考文献)

### 1.Panelとは

パネルとは、ボタンやテキストなどを自由に配置するためのウィンドウです。  
前回の記事で作ったあのフレームの中に配置・描画されます。なので、パネルを複数作ることで、ウィンドウ画面を分割することもできます。

  
百聞は一見に如かず。次のプログラムを実行してください。  
  
  
### 2.サンプルプログラム

import wx

class MyApp(wx.Frame):
    def __init__(self, parent, id = -1, title = None):
        wx.Frame.__init__(self, parent, id, title)

        self.SetTitle('Panel')
        self.SetSize((300, 300))

        #パネルの作成
        panel_G = wx.Panel(self, id, pos=(10, 10), size=(130, 280))
        panel_G.SetBackgroundColour((0, 255, 0))

        panel_B = wx.Panel(self, id, pos=(160, 10), size=(130,280))
        panel_B.SetBackgroundColour((0, 0, 255))

        #描画
        self.Show(True)
        
    def CloseWindow(self):
        self.close(True)

if __name__ == "__main__":
    app = wx.App()
    MyApp(None, wx.ID_ANY, "タイトル")
    app.MainLoop()

  
### 3.実行結果

このように、ウィンドウが二つに分割されたと思います。  
![f:id:pythonjacascript:20180923124902j:plain](/images/ppythonjacascript2018092320180923124902.jpg "f:id:pythonjacascript:20180923124902j:plain")

  
### プログラム解説

簡単なプログラムの説明です。  
※ここで説明しているのは、パネルに関するプログラムのみです。  
  
  
### 1.パネルの作成

パネルの作成は、次のように行います。

panel_G = wx.Panel(self, id, pos=(10, 10), size=(130, 280))
panel_G.SetBackgroundColour((0, 255, 0))

  
Documentationを見ると、次のように書かれています。

Panel()
Panel(parent, id=ID_ANY, pos=DefaultPosition, size=DefaultSize,
        style=TAB_TRAVERSAL, name=PanelNameStr)

**parent**は必須の引数ですが、あとは勝手に設定してくれるみたいです。

また、パネルのサイズなどは、wx.Panel() を実行した後から変更することも可能です。次はその方法を見ていきます。

#### 2.描画

パネルの作成が完了したら、それらをディスプレイに表示します。  
そのためには以下の関数を実行します。  
**MyApp**クラスの**\_\_init\_\_()**関数内にあります。

        self.Show(True) #描画
  
  
### 2.パネルの見た目の設定

#### 背景色の設定

panel.SetBackgroundColour(wx.WHITE)

この一文を、下のように付け足します。

panel_B = wx.Panel(self, id)
panel_B.SetBackgroundColour((0, 0, 255))

こんな感じです。引数で色を指定しますが、このようにRGBでもwxPythonで用意された色を使ってもどちらでも構いません。  
  
#### パネルの表示位置の変更

panel_B = wx.Panel(self, id)
panel_B.SetPosition((20,30))

#### パネルの大きさの変更

panel_B = wx.Panel(self, id)
panel_B.SetSize((50,100))

### 参考文献：

<https://wxpython.org/Phoenix/docs/html/wx.Panel.html>  
[wxPythonで、画面を分割する（SplitterWindow） - naritoブログ](https://torina.top/detail/204/)