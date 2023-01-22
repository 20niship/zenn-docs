---
title: "【機械学習】NO.1 Pythonで多項式フィッティングと過学習"
emoji: ""
type: ""
topics: []
published: false
---

今回から、機械学習シリーズです。

* [1.課題](#1課題)
* [2.方法](#2方法)
* [2.近似プログラム](#2近似プログラム)
* [近似値を求めるプログラム](#近似値を求めるプログラム)
* [実行結果](#実行結果)  
   * [一次関数で近似](#一次関数で近似)  
   * [二次関数で近似](#二次関数で近似)  
   * [三次関数で近似](#三次関数で近似)  
   * [四次関数で近似](#四次関数で近似)
* [過学習](#過学習)
* [今後のデータを予測できるのか（過学習していないかどうか）を確かめる](#今後のデータを予測できるのか過学習していないかどうかを確かめる)  
   * [サンプルプログラム（データ予測）](#サンプルプログラムデータ予測)  
   * [予測結果](#予測結果)
* [結論](#結論)

### 1.課題

![y = x^3 - 15x^2 + 66x - 78 + 20sin(x) + 10cos(x)](https://chart.apis.google.com/chart?cht=tx&chl=y%20%3D%20x%5E3%20-%2015x%5E2%20%2B%2066x%20-%2078%20%2B%2020sin%28x%29%20%2B%2010cos%28x%29)のグラフは、![0<=x<=10](https://chart.apis.google.com/chart?cht=tx&chl=0%3C%3Dx%3C%3D10)の範囲で、以下のようになグラフになります。

![f:id:pythonjacascript:20190101171440j:plain](/images/ppythonjacascript2019010120190101171440.jpg "f:id:pythonjacascript:20190101171440j:plain")

これに、0～10の範囲で**ノイズを加える**と、このような散布図で表すことできます。  
![f:id:pythonjacascript:20190101171542j:plain](/images/ppythonjacascript2019010120190101171542.jpg "f:id:pythonjacascript:20190101171542j:plain")

  
参考までに、↑のグラフの描画プログラム：

import numpy as np
import matplotlib.pyplot as plt 

def f(x):
    return x**3 - 15* x**2 + 66 * x - 78 + np.sin(x) * 20 + np.cos(x)*10
    
x_list = np.arange(0, 10, 0.1)
y_list = f(x_list) + np.random.rand(100) * 30
plt.scatter(x_list, y_list)
plt.show()

今回は、**このランダムな数列から、元の関数を予測（近似）する**という問題について考えてみます。

このように、**複雑なもの（関数や、現実世界の現象）を単純化して近似したもの**を「**モデル**」と言います。つまり、この記事では元の![y=f(x)+(誤差）](https://chart.apis.google.com/chart?cht=tx&chl=y%3Df%28x%29%2B%28%E8%AA%A4%E5%B7%AE%EF%BC%89)という複雑なものから、その関数の根底にある部分「![y=f(x)](https://chart.apis.google.com/chart?cht=tx&chl=y%3Df%28x%29)」を取り出そう、というものです。  
  
  
### 2.方法

近似モデルを作ったとして、どれほど近似できているかは、どのやって判断するのでしょうか？

それは、**誤差の最小化**です。複雑なものを単純にしているので、細かい誤差は発生してしまいます。そこで、この「誤差」を手掛かりにして無数のモデルの中から正しいモデルを選択できるのです。

  
今回の誤差は、与えられたデータとモデルが予想した値の「**ユークリッド平方距離**」の合計を誤差とします。

  
![f(x) = x^3 - 15x^2 + 66x - 78 + 20sin(x) + 10cos(x) + (誤差）](https://chart.apis.google.com/chart?cht=tx&chl=f%28x%29%20%3D%20x%5E3%20-%2015x%5E2%20%2B%2066x%20-%2078%20%2B%2020sin%28x%29%20%2B%2010cos%28x%29%20%2B%20%28%E8%AA%A4%E5%B7%AE%EF%BC%89)として、ｙをモデルの予測値とします。

そのとき、ユーグリッド平方距離によって誤差は以下の式で求められます。

![erro = \sum_{k=1}^{n} ||f(x) - y||](https://chart.apis.google.com/chart?cht=tx&chl=erro%20%3D%20%5Csum_%7Bk%3D1%7D%5E%7Bn%7D%20%7C%7Cf%28x%29%20-%20y%7C%7C)  

Pythonで書くとこんな感じです。

def error(f, x, y):
    return np.sum((f(x) - y)**2)
  
  
### 2.近似プログラム

ここで、扱う関数![f(x)](https://chart.apis.google.com/chart?cht=tx&chl=f%28x%29)が一次関数で表せるとしましょう。  
そうすると、考えるべき問題は、**↑のグラフ上で直線をどこに置いたら上記error()関数の値、つまり誤差が最も小さくなるのか？**ということです。

ここで、**Scipy**というPythonの行列計算などに特化したライブラリの出番です。  
Scipyには、**polyfit()関数**というものがあり、それを使うと近似した関数を一発で求めることができます。

fp1, _, _, _, _ = sp.polyfit(x_list, y_list, 1, full = True)

polyfit()関数に、![x](https://chart.apis.google.com/chart?cht=tx&chl=x)のリストと、それに対応する![y](https://chart.apis.google.com/chart?cht=tx&chl=y)（＝f(x)）のリストを与えると、近似した関数![fp](https://chart.apis.google.com/chart?cht=tx&chl=fp)を返してくれます[\*1](#f-196c7962 "正確には、関数の係数のリストです")。  
因みに、3つ目の引数の「1」は「一次関数で近似してくれ」ということを表しています。

  
では、実際にプログラムを動かしてみましょう。  
  
  
### 近似値を求めるプログラム

import numpy as np
import matplotlib.pyplot as plt 

def f(x):
    return x**3 - 15* x**2 + 66 * x - 78 + np.sin(x) * 20 + np.cos(x)*10
    
x_list = np.arange(0, 10, 0.1)
y_list = f(x_list) + np.random.rand(100) * 30

plt.scatter(x_list, y_list)

import scipy as sp

def error(f, x, y):
    return np.sum((f(x) - y)**2)
    
fp1, _, _, _, _ = sp.polyfit(x_list, y_list, 1, full = True)
f1 = sp.poly1d(fp1)
print(error(f1, x_list, y_list))

x_list2 = np.arange(0, 10, 0.1)
fx = sp.linspace(0, x_list2[-1], 100)
plt.plot(fx, f1(fx), color = 'red', linewidth = 3)
plt.show()
  
  
### 実行結果

このプログラムを実行すると、以下のようなグラフが出力されます。

##### 一次関数で近似

![f:id:pythonjacascript:20190101202347j:plain](/images/ppythonjacascript2019010120190101202347.jpg "f:id:pythonjacascript:20190101202347j:plain")  
青い点々が学習データ、赤線が近似値を表しています。

そこで、

fp1, _, _, _, _ = sp.polyfit(x_list, y_list, 1, full = True)

の3つ目の引数「1」を2や3に変えてみてください。  
そうすることによって、近似曲線を一次関数ではなく、二次関数、三次関数で近似してくれるようになります。  
  
##### 二次関数で近似

![f:id:pythonjacascript:20190101202652j:plain](/images/ppythonjacascript2019010120190101202652.jpg "f:id:pythonjacascript:20190101202652j:plain")  

##### 三次関数で近似

![f:id:pythonjacascript:20190101202720j:plain](/images/ppythonjacascript2019010120190101202720.jpg "f:id:pythonjacascript:20190101202720j:plain")  

##### 四次関数で近似

![f:id:pythonjacascript:20190101202746j:plain](/images/ppythonjacascript2019010120190101202746.jpg "f:id:pythonjacascript:20190101202746j:plain")

  
このように、**次数が増えるにつれて、パラメータ（近似するために変更できる変数）の数が増え、より正確な近似ができるようになります。**  
次数が高ければ、近似モデルはより細かい部分まで捉えられるのです。  
  
  
### 過学習

だったら、百次関数みたいに、次数をとてつもない数に増やしたらいいんじゃないか？  
と考えるかもしれませんが、そうすると、「**過学習**」と呼ばれる問題が出てきます。

  
この記事の最初の![f(x)](https://chart.apis.google.com/chart?cht=tx&chl=f%28x%29)の式を見てもらえるとわかるように、近似したい値には**ノイズ**が含まれます。近似モデルに必要なことは、  
「**そのモデルが、もともとのデータの特徴をとらえていること**」であって、「**学習したデータに含まれていた誤差をとらえ、複雑な模型を作ること**」ではありません。

  
**確かに、次数を上げればデータの複雑で細かい部分もとらえるようになります。しかし、モデルが敏感すぎると、今度はデータに含まれるノイズ（データ特有の癖や固有の値）まで学習してしまうのです。**

そうすると、何が問題になるかというと、「誤差に気を取られるようになって、データの本質が見えなくなる」のです。

  
実際に確認してみましょう。  
  
### 今後のデータを予測できるのか（過学習していないかどうか）を確かめる

今回のデータは、このグラフでしたね。  
![f:id:pythonjacascript:20190101171542j:plain](/images/ppythonjacascript2019010120190101171542.jpg "f:id:pythonjacascript:20190101171542j:plain")

これは、![x](https://chart.apis.google.com/chart?cht=tx&chl=x)の値が0～10までしかありません。しかし、**近似モデルがデータの本質を捉えているのならば、![x](https://chart.apis.google.com/chart?cht=tx&chl=x)が10以上の時も予測できるはず**です。

  
では、そのことを確かめてみましょう。  
  
#### サンプルプログラム（データ予測）

このプログラムは、**ｘが10～12のときにデータがどうなりそうか？**を近似曲線（直線）で予測するプログラムです。

import numpy as np
import matplotlib.pyplot as plt 

def f(x):
    return x**3 - 15* x**2 + 66 * x - 78 + np.sin(x) * 20 + np.cos(x)*10
    
x_list = np.arange(0, 10, 0.1)
y_list = f(x_list) + np.random.rand(100) * 30

plt.scatter(x_list, y_list)

import scipy as sp

def error(f, x, y):
    return np.sum((f(x) - y)**2)
    
fp1, _, _, _, _ = sp.polyfit(x_list, y_list, 5, full = True)
f1 = sp.poly1d(fp1)
print(error(f1, x_list, y_list))

x_list2 = np.arange(0, 12, 0.1)
fx = sp.linspace(0, x_list2[-1], 100)
plt.plot(fx, f1(fx), color = 'red', linewidth = 3)
plt.xlim(0, 12)
plt.show()

#### 予測結果

まず、一次関数での近似の時：  
![f:id:pythonjacascript:20190101204310j:plain](/images/ppythonjacascript2019010120190101204310.jpg "f:id:pythonjacascript:20190101204310j:plain")  
まあ、当然と言えば当然の結果でしょう。関数が大きい目で見たら増加傾向にあるということをつかめています。

  
続いて、二次関数で近似すると、このようなグラフになります。  
![f:id:pythonjacascript:20190101204405j:plain](/images/ppythonjacascript2019010120190101204405.jpg "f:id:pythonjacascript:20190101204405j:plain")  
ん～...。一次関数とあんまり変わらない気がするけど、まいっか。

  
三次関数：  
![f:id:pythonjacascript:20190101204447j:plain](/images/ppythonjacascript2019010120190101204447.jpg "f:id:pythonjacascript:20190101204447j:plain")  
これはきれいです。データの行く末を正確に予測できているのではないか？と思います。

どんどん行きます。四次関数：  
![f:id:pythonjacascript:20190101204537j:plain](/images/ppythonjacascript2019010120190101204537.jpg "f:id:pythonjacascript:20190101204537j:plain")  
ん？？ここら辺から予測がおかしくなってきましたよ。ｘが10以降の時に予測グラフが急激に減少しますぞ...！？  
普通に青丸だけ見たらそのまま増加しそうなんだけど。

  
ひとつ飛ばして6次関数：  
![f:id:pythonjacascript:20190101204654j:plain](/images/ppythonjacascript2019010120190101204654.jpg "f:id:pythonjacascript:20190101204654j:plain")  
値が一気に上昇すると予測したようですが、う～ん...。

興味半分でやってみた、30次関数：  
![f:id:pythonjacascript:20190101204810j:plain](/images/ppythonjacascript2019010120190101204810.jpg "f:id:pythonjacascript:20190101204810j:plain")  
え？！、これは見当違いな予測をしておりますな、はい。  
  
### 結論

このように、  
**次数が低いと学習データの大まかな部分を捉えることができる。**

**次数がちょうどいいと、学習データの特徴を適切に捉えることができ、データの予測もきちんとできる。**

**反対に次数が高いと、特徴に敏感になりすぎて、誤差までとらえてしまい、近似モデルとして使い物にならなくなる。**  
ということがわかりました。

このように、学習しすぎて、データに含まれるノイズもとらえてしまうことを**「過学習」（Over Fitting)**と言います。

[\*1](#fn-196c7962):正確には、関数の係数のリストです