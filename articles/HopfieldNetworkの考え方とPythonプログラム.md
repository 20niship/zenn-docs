---
title: "Hopfield Networkの考え方とPythonプログラム"
emoji: ""
type: ""
topics: undefined
published: false
---

* [Hopfield Networkとは](#Hopfield-Networkとは)  
   * [非同期型ネットワーク](#非同期型ネットワーク)
* [動作原理](#動作原理)
* [プログラム](#プログラム)
* [実行結果](#実行結果)
* [参考文献](#参考文献)

### Hopfield Networkとは

ニューラルネットワークの一モデルである。

アメリカ合衆国の物理学者であるジョン・ホップフィールド (J.J. Hopfield) が提唱した\[1\]。ユニット（ニューロン）間に対称的な相互作用がある非同期型ネットワークであり、自然な操作によってネットワークのエネルギーが極小値をとる。元はスピンの安定条件をもとめるモデルとして発想されたものであったが、ネットワークによる連想記憶のモデルとして歓迎され、ニューラルネットブームの火付け役の一つとなり、また後のボルツマンマシンの元ともなった。これは統計的な変動をもちいて、エネルギーが極小値ではなく最小値をとることを目指すモデルである。

米国の物理学者、J.Hopfield（カリフォルニア工科大）が1982年に提唱したニューラルネットワークで、最適化問題に応用出来る事で有名になった。  
相互結合型ネットワークで、i番目の細胞からj番目の細胞への結合係数wjiと、j番目の細胞からi番目の細胞への結合係数wijは常に等しい（対称性。或る細胞間の双方向の結合係数が等しい）。  
また、自己結合（wii等、或る細胞の出力がその細胞への入力となる結合）係数は0という条件を持ち、これら2つは連想記憶モデル（アソシアトロン）と同じとなる。  
連想記憶モデルと異なるのは、状態の更新が非同期である事と、ネットワークのエネルギーという概念を導入している点だ。

#### 非同期型ネットワーク

連想記憶モデル（アソシアトロン）では、全ての細胞が同時一斉に、其々の他の細胞からの入力刺激の総和を計算し、状態更新した。これを同期型ネットワークという。  
これに対し、或る時刻には一つの細胞だけが他の細胞からの入力刺激の総和に基づき状態更新（出力値の変化。出力値が結果として同じ場合も含む）を行なうものを非同期型ネットワークという。  
或る時刻に一つの細胞だけが状態更新し、その間、他の細胞の出力値はそのままとなる。  
ホップフィールド・ネットワークでは、図のようにその時々にランダムに選ばれた細胞が状態更新する事を繰り返していく（灰色が状態更新する細胞の例）。  
  
  
### 動作原理

### プログラム

今回、プログラムは  
[sinhrks.hatenablog.com](http://sinhrks.hatenablog.com/entry/2014/12/30/221538)  
さんのものを参考にさせていただきました。

また、学習用画像の読み込みには以下のような関数を使っています。

def get_data(number = 2):
    data = []
    picture_names = ["6", "7", "1",  "4", "5", "3"]
    # 画像の読み込み
    for name in range(min(number, len(picture_names))):
        img = np.array(Image.open(picture_names[name] + ".jpg"))
        img_g = np.array(img[...,1])
        img_g2 = np.reshape(img_g, (28*28))
        data.append(img_g2)
    return data
  
  
### 実行結果

以下の2枚の画像を試しました。  
![f:id:pythonjacascript:20190223180051j:plain](/images/ppythonjacascript2019022320190223180051.jpg "f:id:pythonjacascript:20190223180051j:plain")![f:id:pythonjacascript:20190223180053j:plain](/images/ppythonjacascript2019022320190223180053.jpg "f:id:pythonjacascript:20190223180053j:plain")

手書きの「Windows」と「MAC」です。Linuxの皆さんすみません、忘れてました。

  
プログラムを実行すると、以下のような画像が得られます。  
![f:id:pythonjacascript:20190223180009j:plain](/images/ppythonjacascript2019022320190223180009.jpg "f:id:pythonjacascript:20190223180009j:plain")

左の二枚が学習データ、中央二枚がノイズを加えた入力データ、右側に枚が入力データに基づいて想起した出力データです。

このように、学習時の画像を復元できていることがわかります。  
  
  
### 参考文献

[ホップフィールド・ネットワーク](http://www.sist.ac.jp/~kanakubo/research/neuro/hopfieldnetwork.html)  
[Theano で Deep Learning <6の準備>: ホップフィールドネットワーク - StatsFragments](http://sinhrks.hatenablog.com/entry/2014/12/30/221538)