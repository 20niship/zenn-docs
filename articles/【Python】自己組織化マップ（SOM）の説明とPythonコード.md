---
title: "【Python】自己組織化マップ（SOM）の説明とPythonコード"
emoji: "🤖"
type: "tech"
topics: []
published: false
---

* [SOMとは](#SOMとは)
* [この記事で紹介するサンプル](#この記事で紹介するサンプル)
* [アルゴリズム](#アルゴリズム)
* [サンプルプログラム](#サンプルプログラム)
* [実行結果](#実行結果)
* [参考文献](#参考文献)

![f:id:pythonjacascript:20190203170622j:plain](/images/ppythonjacascript2019020320190203170622.jpg "f:id:pythonjacascript:20190203170622j:plain")

### SOMとは

**自己組織化マップ（SOP、Self-organizing maps）** はフィンランドの研究者，T. Kohonenが発明したニューラルネットの一種です。

SOMは教師なし学習を行い、高次元のデータセットを低次元空間（主に二次元）に写像するのに使用されます。

2次元に写像した場合、データ分布が地図（**Topographic Map**）のように可視化されるため、この地図をデータマイニングに用いられることがよくあります。  
  
  
### この記事で紹介するサンプル

上に述べた通り、SOMは多次元のデータを可視化するのに適しています。この記事は「色」という三次元データを分類・可視化してみたいと思います。

パソコンの世界では、**「色」はRed、Blue、Greenの三色の混ぜ方で決まっています。**それぞれ0～255の強さがあります。例えば、（Red, Green, Blue)= (255,0,0)は赤色、(Red, Green, Blue) = (255,255,0)は赤と緑の光の混ぜ合わせなので黄色となります。

つまり、色には三次元のデータあるのです。

さて、この三次元のデータをSOMに組み込もうということです。そして、似た色のデータを近くに配置することで、色を分類することができるのではないか？というのがSOMのアルゴリズムです。  
  
  
### アルゴリズム

では、SOMはどのように動いているのでしょうか。

  
![f:id:pythonjacascript:20190203173218j:plain](/images/ppythonjacascript2019020320190203173218.jpg "f:id:pythonjacascript:20190203173218j:plain")  
SOMは、最初はノードにランダムな色を記憶させておきます。これは、隣同士のノードの関係がよくわからない、つまり学習が行われていない初期段階です。  
![f:id:pythonjacascript:20190203173919j:plain](/images/ppythonjacascript2019020320190203173919.jpg "f:id:pythonjacascript:20190203173919j:plain")
  
  
では、本題の学習（分類）方法です。

まず、SOMには、大量の色データが一つずつ順番に与えられます。それら一つ一つのデータに対して学習していくのですが、その学習アルゴリズムは以下の2STEPです。  
 **➀入力された色データに最も近い色を表しているノードを探す** 
 **②そのノードと、その周辺ノードを、入力された色データの色に近づける**

例えば、入力データが赤色だった場合、赤色のノードとその周りがさらに赤っぽくなります（上図の「入力データ1」）。

この2STEPを繰り返すことで、だんだんとノードがきれいになっていきます。

そして、最終的には、下の画像のように色が二次元マップ上にきれいに分類されます。  
![f:id:pythonjacascript:20190203173959j:plain](/images/ppythonjacascript2019020320190203173959.jpg "f:id:pythonjacascript:20190203173959j:plain")  
  
  
### サンプルプログラム

[swdrsker.hatenablog.com](http://swdrsker.hatenablog.com/entry/2016/12/08/171356)  
こちらのかたのサンプルプログラムを一部抜粋させていただきました。本当にありがとうございます。

以下のPythonプログラムを動かしてください

import numpy as np
import matplotlib.pyplot as plt

Nx = 30
Ny = 30
learntime = 50000
alpha = 0.08
weight = np.random.random([Nx,Ny,3])

def som(ColorVec):
    min_index = np.argmin(((weight-ColorVec)**2).sum(axis=2))
    mini = int(min_index / Ny)
    minj = int(min_index % Ny)
    for i in range(-2,3):
        for j in range(-2,3):
            try:
                weight[mini+i,minj+j] += alpha * (ColorVec - weight[mini+i,minj+j])
            except:
                pass
    
for time in range(learntime):
    ColorVec = np.random.rand(3)
    som(ColorVec)
    
print("imgshow")
im = plt.imshow(weight,interpolation='none')
plt.show()
  
  
### 実行結果

以下のようにグラデーションの利いたカラフルな画像が生成されればOKです。  
![f:id:pythonjacascript:20190203173959j:plain](/images/ppythonjacascript2019020320190203173959.jpg "f:id:pythonjacascript:20190203173959j:plain")  
ただ単に多くのランダムな色データを入力しただけなのに、簡単なアルゴリズムでそれらを分類できることがわかりました。  
  
  
### 参考文献

<http://gaya.jp/spiking%5Fneuron/som.htm>

[SOMとは(1) \[SOM JAPAN\]](http://www.somj.com/what%5Fsom/what%5Fsom01.htm)

[SOM（自己組織化写像）のプログラム (python) - 技術メモ](http://swdrsker.hatenablog.com/entry/2016/12/08/171356)