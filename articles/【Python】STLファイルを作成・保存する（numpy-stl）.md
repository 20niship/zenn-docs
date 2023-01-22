---
title: "【Python】STLファイルを作成・保存する（numpy-stl）"
emoji: "🤖"
type: "tech"
topics: []
published: false
---

stlファイルとは、3Ｄオブジェクトを保存ファイルで、メッシュ形式で保存できます。
  
  
Pythonで3Ｄモデルを作成し、STLファイルに変換して保存する方法です。

* [STLファイルとは](#STLファイルとは)
* [ライブラリのインストール](#ライブラリのインストール)
* [プログラム](#プログラム)
* [実行結果：](#実行結果)
* [解説](#解説)
* [物体を回転](#物体を回転)

### STLファイルとは

3Ｄモデルを扱うファイルは、他にも**.obj**ファイルなどたくさんありますが、stlファイルには以下のような特徴があります。

まず、「stl」とは「**Standard Triangulated Language**」の略で、その名前の通り、メッシュデータを**三角形ポリゴン**の集合として表現します。  
ポリゴンとは面のようなもので、この面を複数組み合わせることで物体の形状を表すことができます。

よって、STLファイルで曲面や曲がった部分のあるオブジェクトを正確に表現することができません。曲面をSTLに変換するときには、多くの三角形で近似する必要があります。  
  
  
### ライブラリのインストール

STLファイルを作成するために、「**numpy-stl**」というライブラリを使用します。  
インストール方法は、コマンドプロンプトを起動して

pip install  numpy-stl

を実行するだけです。  
  
  
### プログラム

以下のプログラムを実行してください。

import numpy as np
from stl import mesh

# 物体の頂点を定義する
vertices = np.array([\
    [3, 0, 0],
    [0, 3, 0],
    [0, 0, 0],
    [0, 0, 3]])

# 三角形ポリゴンを構成する3つの頂点を選ぶ
faces = np.array([\
    [0,1,2],
    [0,1,3],
    [0,2,3],
    [1,2,3]])

# メッシュ（物体）作成
obj= mesh.Mesh(np.zeros(faces.shape[0], dtype=mesh.Mesh.dtype))
for i, f in enumerate(faces):
    for j in range(3):
        obj.vectors[i][j] = vertices[f[j],:]


#Y軸方向に90度回転
#obj.rotate([0.0, 1.0, 0.0], math.radians(90))

# 保存
obj.save('sample.stl')

### 実行結果：

上のプログラムを実行すると、「sample.stl」というファイルが作成されます。  
そして、その中には以下のような三角錐の物体が作成されているはずです。  
![f:id:pythonjacascript:20190223143845j:plain](/images/ppythonjacascript2019022320190223143845.jpg "f:id:pythonjacascript:20190223143845j:plain")  
  
（上の写真の三角錐の色は、表示ソフトによって異なります。STLファイルは物体の形のみを表すファイルで、色情報を表すことはできません。）

### 解説

特に解説といえるほどたいそうなことはしていませんが、一応書いておきます。

まず、3D物体を作成するのに必要なデータは、「頂点データ」と、「三角形ポリゴンデータ」です。  
「ポリゴンデータ」は、三角形のポリゴンを作成するときに、「頂点データ」の中の、どの頂点を3つ結んでポリゴンを作るか？、を表しています。

上のプログラムでは、三角錐を作成しています。

まず、頂点データはverticesという名前で作成しました。

vertices = np.array([\
    [3, 0, 0],
    [0, 3, 0],
    [0, 0, 0],
    [0, 0, 3]])

三角錐なので、頂点は4つあります。  
**\[x座標、ｙ座標、ｚ座標\]**の順で書いていきます。

  
次に、「ポリゴンデータ」です。

faces = np.array([\
    [0,1,2],
    [0,1,3],
    [0,2,3],
    [1,2,3]])

facesは、三角形の面（ポリゴン）の集合を表しています。  
例えば、faces配列の一つ目の要素、「 **\[0,1,2\]**」は、「vertices の0番目の頂点、1番目の頂点、2番目の頂点の3点を結んで三角形ポリゴンを作成」ということを表します。

  
そして、あとはこの二つの配列を使ってSTLファイルを作成するだけです。

obj= mesh.Mesh(np.zeros(faces.shape[0], dtype=mesh.Mesh.dtype))
for i, f in enumerate(faces):
    for j in range(3):
        obj.vectors[i][j] = vertices[f[j],:]

# 保存
obj.save('sample.stl')

  
### 物体を回転

下のように書くと、作成した物体を回転させることができます。

obj= mesh.Mesh(np.zeros(faces.shape[0], dtype=mesh.Mesh.dtype))

#Y軸方向に90度回転
obj.rotate([0.0, 1.0, 0.0], math.radians(90))

  
回転中心は原点（0,0,0）です。回転行列を使って回転処理を行っています。  
![R_x(\theta) = \begin{bmatrix}1&0& 0 \\0 & cos\theta & -sin\theta \\0 & sin\theta &cos\theta \end{bmatrix}](https://chart.apis.google.com/chart?cht=tx&chl=R_x%28%5Ctheta%29%20%3D%20%5Cbegin%7Bbmatrix%7D1%260%26%200%20%5C%5C0%20%26%20cos%5Ctheta%20%26%20-sin%5Ctheta%20%5C%5C0%20%26%20sin%5Ctheta%20%26cos%5Ctheta%20%5Cend%7Bbmatrix%7D)

![R_y(\theta) = \begin{bmatrix}cos\theta&0&sin\theta\\0 & 1 &0\\ -sin\theta &0&cos\theta \end{bmatrix}](https://chart.apis.google.com/chart?cht=tx&chl=R_y%28%5Ctheta%29%20%3D%20%5Cbegin%7Bbmatrix%7Dcos%5Ctheta%260%26sin%5Ctheta%5C%5C0%20%26%201%20%260%5C%5C%20-sin%5Ctheta%20%260%26cos%5Ctheta%20%5Cend%7Bbmatrix%7D)

![R_Z(\theta) = \begin{bmatrix}cos\theta & -sin\theta & 0 \\sin\theta & cos\theta & 0 \\0 & 0 &1 \end{bmatrix}](https://chart.apis.google.com/chart?cht=tx&chl=R_Z%28%5Ctheta%29%20%3D%20%5Cbegin%7Bbmatrix%7Dcos%5Ctheta%20%26%20-sin%5Ctheta%20%26%200%20%5C%5Csin%5Ctheta%20%26%20cos%5Ctheta%20%26%200%20%5C%5C0%20%26%200%20%261%20%5Cend%7Bbmatrix%7D)

この一行を追加して実行すると、以下のような物体ができます。

![f:id:pythonjacascript:20190223145912j:plain](/images/ppythonjacascript2019022320190223145912.jpg "f:id:pythonjacascript:20190223145912j:plain")

一枚目の画像と比べると、ｙ軸を中心に90度回転していることがわかります。