---
title: "【Python】グラフをGIFアニメで保存（PIL＆Matplotlib）（imagemagick未使用！）"
emoji: "🤖"
type: "tech"
topics: []
published: false
---

* [内容](#内容)
* [PILのインストール](#PILのインストール)
* [プログラム](#プログラム)
* [実行結果：](#実行結果)

### 内容

前回、**imagemagick**というwriterを使って、GIFアニメーションを作成する方法を紹介しました。  
[shizenkarasuzon.hatenablog.com](https://shizenkarasuzon.hatenablog.com/entry/2019/01/27/170550)

  
ですが、通常のPCにはimagemagickは入っていないため、わざわざインストールしなければなりません。

ですが、**imagemagickを使わずにGIFアニメーションを作成**する方法があります。

  
その時に必要になるのが「**PIL (Pillow)**」というPythonライブラリです。  
PILはimagemagickと違ってpipやcondaで簡単にインストールすることができます。

前回の記事ではimagemagickに頼っていたGIFアニメーションの作成を、PILを駆使して行おうというものです。  
  
### PILのインストール

まだPILが入っていない場合、コマンドプロンプトで以下のコマンドを実行してください。

pip install pillow

これで、PILがインストールされます。  
  
  
### プログラム

以下のプログラムを実行してください。

import numpy as npimport matplotlib.pyplot as pltfrom PIL import Imageclass Rendering:def \_\_init\_\_(self): self.pil\_images = \[\] self.duration = 100 #1フレームの表示時間（ms) self.film\_num = 0 def setFig(self): canvas = plt.get\_current\_fig\_manager().canvas canvas.draw() pil\_image = Image.frombytes('RGB', canvas.get\_width\_height(), canvas.tostring\_rgb()) self.pil\_images.append(pil\_image) self.film\_num += 1 def RenderGIF2(self, fileName = "sample.gif"): self.pil\_images\[0\].save(fileName, save\_all=True, append\_images= self.pil\_images\[1:\], optimize=False, duration=self.duration, loop=0)print("