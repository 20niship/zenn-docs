---
title: "【行列】物体の位置と姿勢を決定する（回転行列）"
emoji: "🤖"
type: "tech"
topics: undefined
published: false
---

物体の位置と姿勢を数式で表すにはどうすればよいでしょうか？

位置の指定は、位置ベクトル ![p=\left( x,y,z\right) ^{T}](https://chart.apis.google.com/chart?cht=tx&chl=p%3D%5Cleft%28%20x%2Cy%2Cz%5Cright%29%20%5E%7BT%7D) を使えば指定することができます。

では、姿勢（傾き）を表すにはどうしたらいいでしょうか？

それにはいくつかの方法があります。それを一つずつ見ていきましょう。

| 方法          | 変数の数 |
| ----------- | ---- |
| 回転行列        | 9    |
| 有顔ベクトル      | 6    |
| ピッチ、ロール、ヨー角 | 3    |
| オイラー角       | 3    |

* [1.回転行列](#1回転行列)  
   * [回転行列とは](#回転行列とは)  
   * [考え方](#考え方)

### 1.回転行列

**基準となる姿勢から一定角度だけ回転することで、現在の姿勢を一通りに決定する**ことができるのではないか、という考えに基づいて作られました。  
基準からの回転を行列を行列で表したものが「回転行列（姿勢行列）」です。  
  
#### 回転行列とは

まず、物体が姿勢を変えることにより相対座標系での ![ x](https://chart.apis.google.com/chart?cht=tx&chl=%20x) 軸、 ![y](https://chart.apis.google.com/chart?cht=tx&chl=y) 軸、 ![z](https://chart.apis.google.com/chart?cht=tx&chl=z)軸の単位ベクトルを ![ i = (1, 0, 0) ^{T}](https://chart.apis.google.com/chart?cht=tx&chl=%20i%20%3D%20%281%2C%200%2C%200%29%20%5E%7BT%7D) 、 ![ i = (0, 1, 0) ^{T}](https://chart.apis.google.com/chart?cht=tx&chl=%20i%20%3D%20%280%2C%201%2C%200%29%20%5E%7BT%7D) ![i = (0, 0, 1) ^{T} ](https://chart.apis.google.com/chart?cht=tx&chl=i%20%3D%20%280%2C%200%2C%201%29%20%5E%7BT%7D%20) になったとします。  
その時、回転行列 ![ R](https://chart.apis.google.com/chart?cht=tx&chl=%20R) は次のように書くことができます。

![Rw\left( 0\right) =\begin{bmatrix} r_{11} & r_{12} & r_{13} \\ r_{21} & r_{22} & r_{23} \\ r_{31} & r_{32} & r_{33} \end{bmatrix}](https://chart.apis.google.com/chart?cht=tx&chl=Rw%5Cleft%28%200%5Cright%29%20%3D%5Cbegin%7Bbmatrix%7D%20r_%7B11%7D%20%26%20r_%7B12%7D%20%26%20r_%7B13%7D%20%5C%5C%20r_%7B21%7D%20%26%20r_%7B22%7D%20%26%20r_%7B23%7D%20%5C%5C%20r_%7B31%7D%20%26%20r_%7B32%7D%20%26%20r_%7B33%7D%20%5Cend%7Bbmatrix%7D) 

回転行列を使って物体の姿勢を表すには、このように9個の変数が必要になります。  
  
#### 考え方

![ z](https://chart.apis.google.com/chart?cht=tx&chl=%20z) 軸まわりの回転行列

例えば、各軸の単位ベクトルを ![ z](https://chart.apis.google.com/chart?cht=tx&chl=%20z) 軸まわりに![ /theta](https://chart.apis.google.com/chart?cht=tx&chl=%20%2Ftheta) 回転すると、このようになります。