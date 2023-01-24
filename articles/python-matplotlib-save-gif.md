---
title: "【Python】MatplotlibだけでグラフをGIFアニメーションにして保存"
emoji: "🤖"
type: "tech"
topics: []
published: false
---

* [内容](#内容)
* [プログラム](#プログラム)
* [プログラムの中身](#プログラムの中身)
* [imagemagickのエラーについて](#imagemagickのエラーについて)

### 内容

今回は、**PIL**と**Matplotlib**を用いて、次のようなGIFアニメーションを作ってみます。  
![f:id:pythonjacascript:20190127161855g:plain](/images/ppythonjacascript2019012720190127161855.gif "f:id:pythonjacascript:20190127161855g:plain")

matplotlibのArtistAnimationを使う方法  
それぞれ紹介していきます。  
  
### プログラム

まず、matplotlibの**ArtistAnimation**を使う方法です。

グラフをただ表示するだけならば、

import numpy as np
import matplotlib.pyplot as plt
import matplotlib.animation as animation

fig = plt.figure()
ims = []

for i in range(10):
    x = np.array(range(60))
    rand = np.sin(x/5 - i * 5)    
    im = plt.plot(rand)
    ims.append(im)

ani = animation.ArtistAnimation(fig, ims, interval=100)
plt.show()

と書けば、アニメーションを描画することができます。

そして、そのアニメーションを保存するには、

ple.imshow()

を

ani.save("sample.gif", writer="imagemagick")

に変更してください。  
  
### プログラムの中身

GIFアニメーションを作成するには、まず、一つ一つのフレーム画像を作ります。

im = plt.plot(data) 

と書くことで、dataを折れ線グラフでプロットし、その画像データをimに格納することができます。  
折れ線グラフの色の指定等については、こちらをご覧ください。  
[shizenkarasuzon.hatenablog.com](https://shizenkarasuzon.hatenablog.com/entry/2018/11/16/205129)

  
次に、各フレームの画像をリストimsに追加していきます。

ims.append(im)

  
最後に、

ani = animation.ArtistAnimation(fig, ims, interval=100)
plt.show()

と書くことで、アニメーションを描画することができます。  
  
ani.save("sample.gif", writer="writer_name")

というのは、指定のwriterを使ってGIFアニメーションを作成して保存する関数です。  
  
### imagemagickのエラーについて

特定の環境では、

ValueError: Cannot save animation: no writers are available. Please install mencoder or ffmpeg to save animations.

というエラーが出てしまいます。  
このエラーは「**imagemagick**」をダウンロードする必要があることを表しています。

もし、このエラーが出た場合は、  
[ImageMagickのインストール・使い方・コマンド例 - コミックモール](https://comicmall.jp/wiki.cgi?page=ImageMagick)  
のサイトを見ながらインストールを行ってください。