---
title: "【Python】matplotlib でグラフ描画（折れ線グラフ編）"
emoji: ""
type: ""
topics: []
published: false
---

matplotlibという、様々なグラフや散布図などを描画することのできるライブラリがあります。

  
今回は、matplotlibを使って折れ線を書くコードについてまとめます。

動作環境は以下の通り：

| OS               | Windows 10 Home  |
| ---------------- | ---------------- |
| Pythonのバージョン     | Python 3.6       |
| matplotlibのバージョン | matplotlib 2.2.3 |

* [1\. Matplotlib のインストール](#1-Matplotlib-のインストール)
* [2.基本的なグラフの描画](#2基本的なグラフの描画)  
   * [解説](#解説)
* [複数のグラフを並べて表示](#複数のグラフを並べて表示)
* [複数のデータを一つのグラフにまとめて表示](#複数のデータを一つのグラフにまとめて表示)
* [マーカーの設定](#マーカーの設定)
* [データラベルの設定](#データラベルの設定)
* [X、Y軸のラベルの表示](#XY軸のラベルの表示)
* [タイトルの表示](#タイトルの表示)
* [グリッドの描画](#グリッドの描画)
* [描画範囲の制限](#描画範囲の制限)

### 1\. Matplotlib のインストール

pipを使って

pip install matplotlib

と打てばいいだけです。

その後、Pythonのインタプリタで

import matplotlib

と打って何もエラーが出なければOKです。  
  
### 2.基本的なグラフの描画

以下のプログラムを実行してください。

import numpy as np
import matplotlib.pyplot  as plt

data_Xaxis = np.array(range(60)) 
data_Yaxis = np.sin(data_Xaxis / 5)

plt.plot(data_Yaxis)   #グラフを描画
plt.show()  #グラフを表示

このように表示されればOKです。  
![f:id:pythonjacascript:20181116174628j:plain](/images/ppythonjacascript2018111620181116174628.jpg "f:id:pythonjacascript:20181116174628j:plain")  
サインカーブを描いてみました。  
  
#### 解説

  
まず、

import matplotlib.pyplot  as plt

のコードで、matplotlibをインポートしてpltとして利用可能にしています。

plt.plot(data_Yaxis)

  
の部分でグラフをプロットしています。このように何も指定しない場合、青色の直線で棒グラフが作成されます。  
引数が一つしかない場合、plot関数の引数として渡された配列の値がｙ軸の値として順番にプロットされます。

X軸の値も指定する場合は、次のように書きます。

plt.plot(data_Xaxis, data_Yaxis)

そして、その次の

plt.show()  #グラフを表示

のコードが実行されて、グラフが初めて描画されます。  
  
  
### 複数のグラフを並べて表示

matplotlibに含まれる**subplot**というモジュールを使用すると、このように複数のグラフを同時に描画することができます。  
![f:id:pythonjacascript:20181116190449p:plain](/images/ppythonjacascript2018111620181116190449.png "f:id:pythonjacascript:20181116190449p:plain")  
  
上のグラフを描画したプログラムです。

import numpy as np
import matplotlib.pyplot  as plt

data_Xaxis = np.array(range(60)) 
data_Yaxis = np.sin(data_Xaxis / 5)
data_Yaxis2 = np.cos(data_Xaxis / 5)

fig = plt.figure(figsize=(10, 10)) #10*10（インチ）に設定

# axesオブジェクトの作成
#複数のグラフのプロットを同時に行う
ax1 = fig.add_subplot(2, 3, 1) #(2行 * 3列)に分割し、1番目の位置
ax2 = fig.add_subplot(2, 3, 2) #(1行 * 3列)に分割し、2番目の位置
ax3 = fig.add_subplot(2, 3, 3) #(1行 * 3列)に分割し、3番目の位置

ax4 = fig.add_subplot(2, 1, 2) #(1行 * 3列)に分割し、2番目の位置

#プロットするデータと、線の色を指定
ax1.plot(data_Yaxis, color = 'blue')
ax2.plot(data_Yaxis, color = 'red')
ax3.plot(data_Yaxis, color = 'black')
ax4.plot(data_Yaxis, color = 'green')

#描画！
fig.show()

まず、

fig = plt.figure(figsize=(10, 10)) #10*10（インチ）に設定

で、figureオブジェクトを作成します。  
**figsize = (10,10)** は描画したグラフのサイズ（インチ）を表しており、デフォルトでは 8 × 6 インチです。

次に、作成したfigureオブジェクトからaxesオブジェクトを作成します。

ax1 = fig.add_subplot(2, 3, 1) #(2行 * 3列)に分割し、1番目の位置

例えば、このプログラムは、「figureオブジェクトの描画領域を **2 × 3 に分割**し、**左上から数えて1番目の部分**にグラフを描画する！」という意味です。

最後にグラフを描画するには、

ax1.plot(data_Yaxis, color = 'blue')

先ほど作成した axesオブジェクトのplot関数を実行するだけです。  
  
  
### 複数のデータを一つのグラフにまとめて表示

下の図のように、複数のデータを一つグラフのプロット領域に並べる方法です。  
![f:id:pythonjacascript:20181116180413j:plain](/images/ppythonjacascript2018111620181116180413.jpg "f:id:pythonjacascript:20181116180413j:plain")  

方法は簡単で、次のようにplot関数を2つつなげれば良いのです。

plt.plot(1つめのデータ配列)
plt.plot(2つめのデータ配列)
plt.show()

  
上のグラフを出力したプログラム全体です。

import numpy as np
import matplotlib.pyplot  as plt

data_Xaxis = np.array(range(60)) 
data_Yaxis = np.sin(data_Xaxis / 5)
data_Yaxis2 = np.cos(data_Xaxis / 5)

plt.plot(data_Yaxis)
plt.plot(data_Yaxis2)

plt.grid()
plt.show()

### マーカーの設定

matplotlibで作成したグラフは、データの位置に**打点**をすることができますが、その点の種類を変更することができます。

下のグラフを見てください。  
![f:id:pythonjacascript:20181116192107j:plain](/images/ppythonjacascript2018111620181116192107.jpg "f:id:pythonjacascript:20181116192107j:plain")  
青色の「no marker」と書かれたグラフがデフォルトです。ですが、下のプログラムのように

plt.plot(data, marker = 'o')

と書くことで、データの打点（マーカー）を行うことができます。

マーカーの種類については、このサイトを参考にしてください。  
[markers — Matplotlib 3.0.2 documentation](https://matplotlib.org/api/markers%5Fapi.html)  
マーカーの色は、折れ線グラフの線と同じ色になります。

上の画像をプロットしたプログラムです。

import numpy as np
import matplotlib.pyplot  as plt

data_Yaxis = np.ones(10)

plt.plot(data_Yaxis, label = 'no marker')
plt.plot(data_Yaxis * 2, marker = 'o', label = 'marker = o')
plt.plot(data_Yaxis * 3, marker = '^', label = 'marker = ^')
plt.plot(data_Yaxis * 4, marker = 'p', label = 'marker = p')

plt.ylim(0, 4)
plt.legend()
plt.show()
  
  
### データラベルの設定

下の画像のように、それぞれの折れ線グラフが何を示しているのか(![ y = sin(x)](https://chart.apis.google.com/chart?cht=tx&chl=%20y%20%3D%20sin%28x%29) など）を表すラベルを表示することもできます。

![f:id:pythonjacascript:20181116193416p:plain](/images/ppythonjacascript2018111620181116193416.png "f:id:pythonjacascript:20181116193416p:plain")

ラベルを表示するための方法は2STEPです。

**STEP1：**

plt.plot(data_Yaxis,  label = 'sin(x)')

のように「**label = "ラベル名"**」という引数を追加する

**STEP2：**

plt.legend()

という一文を **plt.show()** の前に追加して、ラベルを描画する。  
  
### X、Y軸のラベルの表示

X、Y軸のラベルを表示するには、

plt.xlabel("x")
plt.ylabel("y")

というプログラムを追加します。ダブルクォーテーション（”～”）の中がそれぞれの軸のラベルとして描画されます。  
一つ上のグラフが、実際にプロットしたものです。X軸、Y軸の中央部分に「**ｘ**」「**ｙ**」と書かれています。  
  
### タイトルの表示

グラフのタイトルを表示するには、

plt.title("Graph Title")

という一行を**plt.show()** の前に追加してください。

  
以下は、「**データラベルの表示**」「**X、Y軸のラベル表示**」、「**タイトル表示**」を実際に行ったサンプルプログラムです。

import numpy as np
import matplotlib.pyplot  as plt

data_Xaxis = np.array(range(60)) 
data_Yaxis = np.sin(data_Xaxis / 5)
data_Yaxis2 = np.cos(data_Xaxis / 5)
data_Yaxis3 = np.cos(data_Xaxis / 2.5)

plt.plot(data_Yaxis,  label = 'sin(x)')
plt.plot(data_Yaxis2, label = 'cos(x)')
plt.plot(data_Yaxis3, label = 'sin(2x)')

plt.title("Graph Title")
plt.xlabel("x")
plt.ylabel("y")

plt.legend()
plt.show()

  
このプログラムによってプロットしたグラフがこちらです。  
![f:id:pythonjacascript:20181116193416p:plain](/images/ppythonjacascript2018111620181116193416.png "f:id:pythonjacascript:20181116193416p:plain")  
  
  
### グリッドの描画

次のように、グリッドを表示する方法です。  
![f:id:pythonjacascript:20181116180708j:plain](/images/ppythonjacascript2018111620181116180708.jpg "f:id:pythonjacascript:20181116180708j:plain")

次の一行を加えてください。

plt.grid()

サンプルプログラム

plt.grid(which='major',color='black',linestyle='-')
plt.grid(which='minor',color='black',linestyle='-')
  
  
### 描画範囲の制限

plt.xlim(-1, 70)
plt.ylim(-1.5, 1.5)

のような関数を使うことで、X、Y軸のグラフの描画範囲を指定できます。

例えば、このようなプログラムを書いた場合、

import numpy as np
import matplotlib.pyplot  as plt

data_Xaxis = np.array(range(60)) 
data_Yaxis = np.sin(data_Xaxis / 5)

plt.plot(data_Yaxis)

#X軸のプロット範囲を-1～70に制限
plt.xlim(-1, 70)

#Y軸のプロット範囲を-1.5～1.5に制限
plt.ylim(-1.5, 1.5)
plt.show()

X軸のプロット範囲は-1～70に設定されます。また、Y軸のプロット範囲は-1.5～1.5に設定されるので、このようなグラフになります。  
![f:id:pythonjacascript:20181116205041p:plain](/images/ppythonjacascript2018111620181116205041.png "f:id:pythonjacascript:20181116205041p:plain")