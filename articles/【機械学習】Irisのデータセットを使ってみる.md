---
title: "【機械学習】Irisのデータセットを使ってみる"
emoji: "🤖"
type: "tech"
topics: undefined
published: false
---

前回、機械学習で多項式フィッティングを行いました。

  
今回は、もう少し難しいことをしてみます。  
  
  
### 1.アイリスとは

[UCI Machine Learning Repository: Iris Data Set](https://archive.ics.uci.edu/ml/datasets/iris)

  
**アイリス（Iris）**というデータセットをご存知でしょうか。アイリス（アヤメ）には、「setosa」、「versicolor」「virginica」という3つの品種があり、それぞれの品種の花弁の形状を数値化したサンプルデータです。  
![f:id:pythonjacascript:20190130230050j:plain](/images/ppythonjacascript2019013020190130230050.jpg "f:id:pythonjacascript:20190130230050j:plain")

  
アイリスのデータセットには、以下のパラメータが含まれています。

| Sepal length | がく片の長さ |
| ------------ | ------ |
| Sepal width  | がく片の幅  |
| Petal length | 花弁の長さ  |
| Petal width  | 花弁の幅   |

  
これらの4つの数値から、元の花の品種を言い当てるという分類器を作成することがよくあります。  
このように、正解の分類方法が与えられた場合で、その分け方を推定する方法を「**クラス分類**」などと言います。  
  
### 2.Pythonでアイリスデータを使ってみる

では、実際にアイリスデータを使ってみたいと思います。

以下のプログラムを実行してください。


from matplotlib import pyplot as plt
from sklearn.datasets import load_iris

import warnings
warnings.filterwarnings('ignore')

data = load_iris()

features = data['data']
feature_names = data['feature_names']
target = data['target']
target_names = data['target_names']
labels = target_names[target]

plt.figure(num=None, figsize=(8,6))
    

fig,axes = plt.subplots(2, 3, figsize=(10,8))
pairs = [(0, 1), (0, 2), (0, 3), (1, 2), (1, 3), (2, 3)]

# 分布図作成時の、プロットデータの点の色と形状を指定
color_markers = [
        ('r', '>'), #Setona
        ('g', 'o'), #Verisocolor
        ('b', 'x'), #Virginica
        ]
for i, (p0, p1) in enumerate(pairs):
    ax = axes.flat[i]

    for t in range(3):
        # Use a different color/marker for each class `t`
        c,marker = color_markers[t]
        ax.scatter(features[target == t, p0], features[
                    target == t, p1], marker=marker, c=c)
    ax.set_xlabel(feature_names[p0])
    ax.set_ylabel(feature_names[p1])
    ax.set_xticks([])
    ax.set_yticks([])