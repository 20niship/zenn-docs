---
title: "【wxPython】NO.3 ボタンの作成"
emoji: "🤖"
type: "tech"
topics: []
published: false
---

前回、wxPythonで、パネルを作成するプログラムを公開しました。

[shizenkarasuzon.hatenablog.com](https://shizenkarasuzon.hatenablog.com/entry/2018/09/23/131037)

今回は、このパネルの上にボタンを作成するプログラムを紹介します。  
  
* [サンプルプログラム](#サンプルプログラム)
* [実行結果](#実行結果)
* [解説](#解説)  
   * [1.ボタンの作成](#1ボタンの作成)  
   * [2.ボタンの大きさの変更](#2ボタンの大きさの変更)  
   * [3.ボタンの表示位置の変更](#3ボタンの表示位置の変更)  
   * [4.ボタンが押されたときのイベントの設定](#4ボタンが押されたときのイベントの設定)  
   * [ボタンの有効/無効の設定](#ボタンの有効無効の設定)  
   * [ボタンの状態取得](#ボタンの状態取得)  
   * [ボタンの状態の設定](#ボタンの状態の設定)

### サンプルプログラム

以下のプログラムを実行してください。

import wx
class MyApp(wx.Frame):
    def __init__(self,parent, id = -1, title = 'Window Title'):
        wx.Frame.__init__(self, parent, id, title)
        self.SetTitle('title')
        self.SetSize((300, 300))
        
        panel = wx.Panel(self)
        
        #ボタンの作成
        self.Button1=wx.Button(panel, label="exit",pos=(130,10),size=(60,60))
        self.Button2=wx.Button(panel, label="print",pos=(130,70),size=(60,60))
    
        #ボタンを割り当て
        self.Bind(wx.EVT_BUTTON, self.close, self.Button1)
        self.Bind(wx.EVT_BUTTON, self.printer, self.Button2)
     
        self.Show(True)

    
    def close(self,event):
        self.Close(True)

    def printer(self, event):
        print("Button2")
        
if __name__=='__main__':
    app=wx.App()
    MyApp(None, wx.ID_ANY, "Buton")
    app.MainLoop()

### 実行結果

![f:id:pythonjacascript:20180923133553j:plain](/images/ppythonjacascript2018092320180923133553.jpg "f:id:pythonjacascript:20180923133553j:plain")  
このような画面と、コンソールが表示されるはずです。

そして、Button1をクリックするとアプリケーションが終了し、Button2をクリックするとコンソール上に「Button2」と表示されます。  
何度もButton2をクリックするとこのようになります。  
![f:id:pythonjacascript:20180923133722j:plain](/images/ppythonjacascript2018092320180923133722.jpg "f:id:pythonjacascript:20180923133722j:plain")
  
  
### 解説

wxPythonでボタンを作成する方法を簡単に説明します。  
  
  
#### 1.ボタンの作成

以下のプログラムでボタンを作成できます。

Button=wx.Button(panel,  label="label", pos=(130,10), size=(60,60))

ボタンを複数作りたい時は、ただこの関数を何行にもわたって書き連ねるだけです。  
  
#### 2.ボタンの大きさの変更

このようにして、ボタンの大きさを指定できます。

Button.SetSize((20,100))

#### 3.ボタンの表示位置の変更

Button.SetPosition((0, 0))

このように書きます。  
  
  
#### 4.ボタンが押されたときのイベントの設定

ボタンが押されたときに、アプリケーションがどのような動作をするのかを設定します。上のサンプルプログラムでは、この部分のことです。

self.Bind(wx.EVT_BUTTON, self.close, self.Button1)
self.Bind(wx.EVT_BUTTON, self.printer, self.Button2)
   
def close(self,event):
    self.Close(True)

def printer(self, event):
    print("Button2")

  
Bind関数を使って、ボタンが押されたときのイベントを設定します。

self.Bind(wx.EVT_BUTTON,  イベント関数,  Button)

と書きます。  
こうすることで、ボタンが押されると、指定したイベント関数が実行されます。  
  
  
#### ボタンの有効/無効の設定

Button.Disable()

や、

Button.Enable()

と書くことで、ボタンを有効化・無効化することができます。

ボタンが無効化されると、そのボタンを押せなくなり、ずっとOFF状態になります。

**サンプルプログラム：**

import wx
 
class MyApp(wx.Frame):
    def __init__(self,parent, id = -1, title = 'Window Title'):
        wx.Frame.__init__(self, parent, id, title)
        self.SetTitle('title')
        self.SetSize((300, 300))
        self.counter = 0
        
        panel = wx.Panel(self)
        
        #ボタンの作成
        self.Button1=wx.Button(panel, label="Button_Disable",pos=(10,10),size=(280,80))
        self.Button2=wx.Button(panel, label="print",pos=(10,110),size=(280,80))
    
        #ボタンを割り当て
        self.Bind(wx.EVT_BUTTON, self.setupButton2, self.Button1)
        self.Bind(wx.EVT_BUTTON, self.printer, self.Button2)
        
        self.Show(True)

    
    def setupButton2(self,event):
        if(self.counter % 2 == 0):
            self.Button2.Disable()
            self.Button1.SetLabel("Button_Enable")
        else:
            self.Button2.Enable()
            self.Button1.SetLabel("Button_Disable")

        self.counter += 1
        
    def printer(self, event):
        print("Button2")
        
if __name__=='__main__':
    app=wx.App()
    MyApp(None, wx.ID_ANY, "Buton")
    app.MainLoop()

上のボタンを押すたびに、下の「print」ボタンが有効化・無効化を繰り返します。  
無効化されたボタンは、このように表示されて、押せなくなります。  
![f:id:pythonjacascript:20180923140032j:plain](/images/ppythonjacascript2018092320180923140032.jpg "f:id:pythonjacascript:20180923140032j:plain")

  
#### ボタンの状態取得

ボタンが押されているのかどうかを確認するには、

Button_pressed = Button.GetValue()

返された値がTrueの時はONの状態（押されている）で、FalseだとOFFです。  
  
  
#### ボタンの状態の設定

ボタンをプログラム側からON/OFFすることもできます。  
このように書きます。

Button.SetValue(True)
Button.SetValue(False)

引数をTrueにするとONに、FalseにするとOFFになります。