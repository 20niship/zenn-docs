---
title: "【Python】wxPython を使って、GUIアプリを作る"
emoji: "🤖"
type: "tech"
topics: []
published: false
---

**wxPython** というライブラリを使用すると、PythonでGUIアプリケーションを作ることができます。

公式サイト：[Welcome to wxPython! | wxPython](https://wxpython.org/)

  
### 1.インストール

**➀コマンドプロンプトを開きます。**

②インストール

pip install wxPython

と入力します。  
![f:id:pythonjacascript:20180920002210j:plain:h200](/images/ppythonjacascript2018092020180920002210.jpg "f:id:pythonjacascript:20180920002210j:plain:h200")

  
### 2.サンプルプログラム1

以下のプログラムを実行してみてください。

import wx

app = wx.App(False)
frame = wx.Frame(None, -1, 'Window Title')
frame.Show()
app.MainLoop()

### サンプルプログラム2

上のプログラムは、クラスを使って次のように書くこともできます。

import wx
class Window(wx.Frame):
    def __init__(self, parent, id = -1, title = None):
        wx.Frame.__init__(self, parent, id, title)
        
        self.Show(True)
        
    def CloseWindow(self):
        self.close(True)
 
if __name__ == "__main__":
    app = wx.App()
    Window(None, wx.ID_ANY, "Window Title")
    app.MainLoop()

### 3.実行

上のプログラムを実行します。

ただし、**Spyder**を使っている場合は次のようなエラーが出ると思います。

 The wx.App object must be created first!

これは、Spyder が wxPython で動いているからだそうです。  
なので、実行するときは Spyder の**「ファイルを実行（F5）」ではなく、エクスプローラから「（ファイル名）.py」をクリックして実行**してください。

このような画面が作成されれば成功です。  
![f:id:pythonjacascript:20180922164446j:plain](/images/ppythonjacascript2018092220180922164446.jpg "f:id:pythonjacascript:20180922164446j:plain")