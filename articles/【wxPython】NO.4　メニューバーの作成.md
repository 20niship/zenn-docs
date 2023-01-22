---
title: "【wxPython】NO.4　メニューバーの作成"
emoji: ""
type: ""
topics: []
published: false
---

メニューバーとは、こういうやつです。  
![f:id:pythonjacascript:20180923141242j:plain](/images/ppythonjacascript2018092320180923141242.jpg "f:id:pythonjacascript:20180923141242j:plain")

このメニューバーをwxPythonのアプリケーションにつけたいと思います。  
  
  
### サンプルプログラム

import wx

class Window(wx.Frame):
    def __init__(self, parent, id = -1, title = None):
        wx.Frame.__init__(self, parent, id, title)
        panel = wx.Panel(self, wx.ID_ANY)
 
        # メニューバーの生成
        menu_bar = wx.MenuBar()
 
        file = wx.Menu()
        file.Append(1, "New")
        file.Append(2, "Quit")

        edit = wx.Menu()
        edit.Append(3, "Redu")
        edit.Append(4, "Undo")
 
        # メニューをメニューバーへ登録
        menu_bar.Append(file, 'File')
        menu_bar.Append(edit, 'Edit')
 
        # フレームにメニュバー登録
        self.SetMenuBar(menu_bar)
 
        # メニュークリック時のイベント登録
        self.Bind(wx.EVT_MENU, self.click_menu)

        self.text = wx.StaticText(panel, wx.ID_ANY, "押されたメニュー")
        self.Show(True)
 

    def click_menu(self, event):
        event_id = event.GetId()
        msg = ""
        if (event_id == 1):
            msg = "New"
        elif (event_id == 2):
            msg = "Quit"
            self.Close(True)
        elif (event_id == 3):
            msg = "Redu"
        elif (event_id == 4):
            msg = "Undo"
 
        self.text.SetLabel(msg)
 
if __name__ == "__main__":
    app = wx.App()
    Window(None, wx.ID_ANY, "menu-bar")
    app.MainLoop()

### 実行結果

このような画面が表示されます。  
![f:id:pythonjacascript:20180923141652j:plain](/images/ppythonjacascript2018092320180923141652.jpg "f:id:pythonjacascript:20180923141652j:plain")

また、メニューバーをクリック売ると、それに応じてウィンドウの文字が変化するはずです。  
  
### 解説

#### STEP1\. メニューバーの作成

menu_bar = wx.MenuBar()

このように書くと、メニューバーが作成されます。  
  
  
#### STEP2\. メニューバーの要素の追加

file = wx.Menu()
edit = wx.Menu()

このように書くと、メニューバーにメニューが追加されます。これは最初から表示されている親メニュー（？）です。  
「メニュー要素」の部分を適当な名前に変えてください。  
  
  
#### STEP3\. メニューバーの子要素の追加

「子要素」と書きましたが、正式名称かどうかはわかりません。メニューの上にカーソルを持っていくと、下にずらっと現れてくるメニューの要素たちのことです。

file.Append(1, "New")
file.Append(1, "Quit")

上のプログラムの場合、「file」という大きなメニューの中に、「New」と「Quit」という2つのサブメニューを作成します。  
実行するとこのようになります。  
![f:id:pythonjacascript:20180923142858j:plain](/images/ppythonjacascript2018092320180923142858.jpg "f:id:pythonjacascript:20180923142858j:plain")

Append関数の第一引数に「1」とか「2」とかが書いてありますが、これは「識別子」と言います。  
イベント処理でどのボタンが押されたのかを判断するときに使用されるので、それぞれのボタンが違う番号（識別子）を持つようにしてください。  
  
  
#### STEP4\. メニューバーを登録

このままでは、メニューバーは表示されません。  
フレームにメニューバーを登録しなければいけません。ここまですることで、やっとメニューバーを表示できるようになります。

SetMenuBar(menu_bar)
  
  
#### STEP5\. メニューが押されたときのイベントを設定

メニューがクリックされたときに行う動作を設定します。

 #MyAppクラス内

#メニューがクリックされたら click_menu 関数を実行
self.Bind(wx.EVT_MENU, self.click_menu)　
 
    def click_menu(self, event):
        event_id = event.GetId()
        msg = ""
        if (event_id == 1):
            msg = "New"
        elif (event_id == 2):
            msg = "Quit"
            self.Close(True)            

  
どれか一つのメニューがクリックされると、click\_menu関数が実行されます。実行する関数は   
\>|python  
Bind(wx.EVT\_MENU, 関数名)   
|

で指定できます。

また、click\_menu関数が実行されるとき、どのメニューが押されたのかを示す識別子が引数としてeventに渡されます。  
識別子とは、メニューバーの要素を追加するプログラム（↓）

file.Append(1, "New")
file.Append(1, "Quit")

の第一引数（1とか2とか）です。

よってeventの値を if/else 文で調べて分岐させることで、押されたメニューごとに異なる動作を行うことができます。  
  
  
### 参考文献：

<https://www.python-izm.com/gui/wxpython/wxpython%5Fmenubar/>