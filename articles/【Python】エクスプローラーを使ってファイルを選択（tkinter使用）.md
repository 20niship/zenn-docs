---
title: "【Python】エクスプローラーを使ってファイルを選択（tkinter使用）"
emoji: ""
type: ""
topics: []
published: false
---

### 内容

TkinterのGUI上で、特定のボタンを押したらエクスプローラーが開き、ファイルを選択できるようにします。  
  
### プログラム

# -*- coding: utf-8 -*-
import os, tkinter, tkinter.filedialog, tkinter.messagebox

class MyApp1(tkinter.Frame):
    def __init__(self, master=None):
        super().__init__(master)
        self.pack()
        self.Button1 = tkinter.Button(self, bg='#000000', fg='#ffffff', width=12, height = 5)
        self.Button1["text"] = "SELECT FILE" #ボタンのテキスト
        self.Button1["command"] = self.Button1_Func #割り込み関数
        self.Button1.pack(side="top")
        
        self.ButtonQuit = tkinter.Button(self, bg='#000000', fg='#ffffff', width=12, height = 5)
        self.ButtonQuit["text"] = "QUIT"
        self.ButtonQuit["command"] = self.QuitApp
        self.ButtonQuit.pack(side="top")
        self.selected_file_path = ""
        
    def Button1_Func(self):
        fTyp = [("","*")]
        iDir = os.path.abspath(os.path.dirname(__file__))
        self.selected_file_path = tkinter.filedialog.askopenfilename(filetypes = fTyp,initialdir = iDir)
        print(self.selected_file_path)
        
    def QuitApp(self):
        print("quit this App")
        self.master.destroy()
        
        
root = tkinter.Tk()
root.geometry("100x200")
app = MyApp1(master=root)
app.mainloop()
  
  
### 実行結果

上のプログラムを実行すると、下の様なGUIが登場します。  
![f:id:pythonjacascript:20190325085131j:plain](/images/ppythonjacascript2019032520190325085131.jpg "f:id:pythonjacascript:20190325085131j:plain")  

ここで、「SELECT FILE」ボタンを押すと、下のようにエクスプローラーが開きます。  
![f:id:pythonjacascript:20190325085234j:plain](/images/ppythonjacascript2019032520190325085234.jpg "f:id:pythonjacascript:20190325085234j:plain")  
※エクスプローラーの色が黒なのはWindowsOS側でカスタマイズしているだけで初期状態では、エクスプローラーの背景色は白です。

  
そして、エクスプローラーでファイルを選択し、「開く」を押すと、コマンドライン上に選択したファイル名が表示されます。  
![f:id:pythonjacascript:20190325085416j:plain](/images/ppythonjacascript2019032520190325085416.jpg "f:id:pythonjacascript:20190325085416j:plain")  
  
  
### 拡張子を指定

選択する拡張子を指定するには、↑のプログラムの

fTyp = [("","*")]

を

fTyp = [('テキストファイル','*.txt'), ('動画ファイル', '*.mp4')]

に変更してください。

  
下のようになるはずです。  
![f:id:pythonjacascript:20190325090054j:plain](/images/ppythonjacascript2019032520190325090054.jpg "f:id:pythonjacascript:20190325090054j:plain")