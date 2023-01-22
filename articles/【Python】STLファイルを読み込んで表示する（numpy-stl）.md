---
title: "【Python】STLファイルを読み込んで表示する（numpy-stl）"
emoji: "🤖"
type: "tech"
topics: []
published: false
---

PythonでSTLなどの3Dモデルを表示してみました。  
  
### ライブラリのインストール

STLを表示する（読み込む）ために、「**numpy-stl**」というライブラリを使用します。  
インストール方法は、コマンドプロンプトを起動して

pip install  numpy-stl

を実行するだけです。  
  
### モデル紹介

DesignSparkのサイトからダウンロードしてきたRaspberry piのモデルを使用します。  
![f:id:pythonjacascript:20190119153557j:plain](/images/ppythonjacascript2019011920190119153557.jpg "f:id:pythonjacascript:20190119153557j:plain")  
「sample.stl」と名前を付けて保存します。

<https://pythonhosted.org/numpy-stl/usage.html>  
ここのTutorialを見ながらプログラムを書いています。  
  
### プログラム

以下のプログラムを書き込んだ.pyファイルを「sample.stl」と同じ場所に作成します。

from stl import mesh
from mpl_toolkits import mplot3d
from matplotlib import pyplot

# 描画領域を新規作成
figure = pyplot.figure()
axes = mplot3d.Axes3D(figure)

# STLファイルを読み込み、メッシュデータからプロットデータに変換
your_mesh = mesh.Mesh.from_file('sample.stl')
axes.add_collection3d(mplot3d.art3d.Poly3DCollection(your_mesh.vectors))

# 大きさを自動調整
scale = your_mesh.points.flatten(-1)
axes.auto_scale_xyz(scale, scale, scale)

# 表示
pyplot.show()
  
  
### 実行結果：

このようにRaspberry piが表示されました！！  
![f:id:pythonjacascript:20190119153547j:plain](/images/ppythonjacascript2019011920190119153547.jpg "f:id:pythonjacascript:20190119153547j:plain")  
めでたしめでたし。