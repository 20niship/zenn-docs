---
title: "【現代制御】状態方程式と立式の仕方"
emoji: "🤖"
type: "tech"
topics: undefined
published: false
---

* [現代制御と古典制御](#現代制御と古典制御)  
   * [A. 古典制御](#A-古典制御)  
   * [B. 現代制御](#B-現代制御)  
   * [まとめ](#まとめ)
* [現代制御の例1](#現代制御の例1)
* [現代制御の例2](#現代制御の例2)
* [参考文献](#参考文献)

この記事では基本的に、小文字はベクトル、大文字は行列です。ボールド体は用いていません。  
  
  
### 現代制御と古典制御

まず、古典制御と現代制御の違いについて考えてみます。

#### A. 古典制御

まずは、古典制御です。

古典制御には、「**伝達関数**」という関数があります。これは、一つの入力値（制御量）に対して1つの出力（状態）を持つ関数です。

![f:id:pythonjacascript:20190218200844j:plain](/images/ppythonjacascript2019021820190218200844.jpg "f:id:pythonjacascript:20190218200844j:plain")

この関数![G_{(s)}](https://chart.apis.google.com/chart?cht=tx&chl=G_%7B%28s%29%7D)を導き、その関数から最適な入力値を探していくのが古典制御です。

 **伝達関数**とは1つの入力に対して1つの出力を持つ関数です。入力値と出力値の関係性を表し、その周波数特定を調べることで、その入出力システムの挙動や安定性を考察することが出行きます。

そして、出力を目標値にするために、最適な入力値を与えていくのが 古典制御です。

この古典制御の中で最も有名なのが**PID制御**だと思います。  
PID制御のゲイン調整を考えればわかりますが、古典制御では実際に入力と出力を試行錯誤的に調べて制御していくことが多いです。

言い換えれば、制御対象を数理モデルで表現せずとも、入力と出力の関係がわかればある程度制御が可能な考え方です。制御対の中身はブラックボックスであっても、古典制御の場合は制御可能です。

反対に、理論的に(数式的に）最適な制御器が作れず、チューニングが必要というデメリットもあります（オートチューニングなどの手法もありますが）。  
  
  
#### B. 現代制御

古典制御は、制御対象の内部状態がブラックボックスでも制御できたのに対して、現代制御では内部状態を「状態方程式」という方程式で定義しなければなりません。  
![f:id:pythonjacascript:20190218201116j:plain](/images/ppythonjacascript2019021820190218201116.jpg "f:id:pythonjacascript:20190218201116j:plain")

状態方程式を定義することで、内部状態や入出力の関係を定義することができます。

この状態方程式は行列を使って表されるもので、**他入力、他出力にも対応**しています。

例えば、古典制御の伝達関数は、「プロペラ回転数 ― プロペラ推力」という1対1の対応だったのに対し、  
現代制御の状態方程式では、「プロペラ回転数 & エルロン角度 & フラップ角度 & エレベータ角度 ― 飛行機の位置 & 飛行機の姿勢」のように、複数の入力をもとに、複数の出力を計算できるのです。  
  
  
#### まとめ

古典制御と現代制御を比べると、以下のようになります。

| 項目    | 古典制御               | 現代制御          |
| ----- | ------------------ | ------------- |
| 入出力   | 1入力、1出力のみ          | 複数入力、複数出力に対応  |
| 計算    | 伝達関数               | 状態方程式         |
| 領域    | 周波数領域              | 時間領域          |
| 解析方法  | 入出力を見る             | 内部状態を考慮       |
| 制御方法  | 試行錯誤的な設計           | 評価関数を最小化      |
| 有名な制御 | PID制御、H∞制御         | MPC制御         |
| メリット  | 制御対象の中身がわからなくてもよい。 | 制御対象のモデリングが必要 |
| メリット2 | チューニングが容易          | 正確な制御が可能      |
  
  
### 現代制御の例1

いよいよ、現代制御について詳しく見ていきます。

以下のような状態について状態方程式を立ててみます。  
![f:id:pythonjacascript:20190218201703j:plain](/images/ppythonjacascript2019021820190218201703.jpg "f:id:pythonjacascript:20190218201703j:plain")

質量![m](https://chart.apis.google.com/chart?cht=tx&chl=m)の台車にばね定数![k](https://chart.apis.google.com/chart?cht=tx&chl=k)のばねが付いており、さらに力![u_{(t)}](https://chart.apis.google.com/chart?cht=tx&chl=u_%7B%28t%29%7D)で右側に押します。床と物体の間に摩擦は働かないものとします。

  
この時、台車に対して状態方程式を立ててみよう、という問題です。

  
物体の運動方程式より、  
![m\ddot{x} + kx = u_{(t)}](https://chart.apis.google.com/chart?cht=tx&chl=m%5Cddot%7Bx%7D%20%2B%20kx%20%3D%20u_%7B%28t%29%7D)  
とあらわすことができます。![x](https://chart.apis.google.com/chart?cht=tx&chl=x)は物体の平衡点（ばねの長さが自然長の点）からの振れを表します。

ちなみに、![\dot{x}](https://chart.apis.google.com/chart?cht=tx&chl=%5Cdot%7Bx%7D)のｘの上にあるドット（・）は、微分（この場合は時間微分![\dot{x} = \dfrac{dx}{dt}](https://chart.apis.google.com/chart?cht=tx&chl=%5Cdot%7Bx%7D%20%3D%20%5Cdfrac%7Bdx%7D%7Bdt%7D)）を表します。  
よって、![\dot{x}](https://chart.apis.google.com/chart?cht=tx&chl=%5Cdot%7Bx%7D)は物体の位置の変化量、つまり速さを表しています。

また、![\ddot{x}](https://chart.apis.google.com/chart?cht=tx&chl=%5Cddot%7Bx%7D)は時間の二回微分なので、加速度を表します。

  
上の式を変形すると、  
![\ddot{x} -\dfrac{k}{m}x + \dfrac{1}{m}u_{(t)}](https://chart.apis.google.com/chart?cht=tx&chl=%5Cddot%7Bx%7D%20-%5Cdfrac%7Bk%7D%7Bm%7Dx%20%2B%20%5Cdfrac%7B1%7D%7Bm%7Du_%7B%28t%29%7D)  
となります。

ここで、  
![x_1 = x](https://chart.apis.google.com/chart?cht=tx&chl=x_1%20%3D%20x)  
![x_2 = \dot{x}](https://chart.apis.google.com/chart?cht=tx&chl=x_2%20%3D%20%5Cdot%7Bx%7D)  
とおくと、

![\dot{x} = \begin{bmatrix}0 & 1 \\-\dfrac{k}{m} & 0 \end{bmatrix}x + \begin{bmatrix}0 \\\dfrac{1}{m}\end{bmatrix}u_{(t)}](https://chart.apis.google.com/chart?cht=tx&chl=%5Cdot%7Bx%7D%20%3D%20%5Cbegin%7Bbmatrix%7D0%20%26%201%20%5C%5C-%5Cdfrac%7Bk%7D%7Bm%7D%20%26%200%20%5Cend%7Bbmatrix%7Dx%20%2B%20%5Cbegin%7Bbmatrix%7D0%20%5C%5C%5Cdfrac%7B1%7D%7Bm%7D%5Cend%7Bbmatrix%7Du_%7B%28t%29%7D)  
と書くことができます。

  
右辺を行列の外積の公式に基づいて計算すると、

（右辺）＝![\begin{bmatrix}0 & 1 \\-\dfrac{k}{m} & 0 \end{bmatrix}\begin{bmatrix}x_1\\x_2\end{bmatrix} + \begin{bmatrix}0 \\\dfrac{1}{m}\end{bmatrix}u_{(t)}](https://chart.apis.google.com/chart?cht=tx&chl=%5Cbegin%7Bbmatrix%7D0%20%26%201%20%5C%5C-%5Cdfrac%7Bk%7D%7Bm%7D%20%26%200%20%5Cend%7Bbmatrix%7D%5Cbegin%7Bbmatrix%7Dx_1%5C%5Cx_2%5Cend%7Bbmatrix%7D%20%2B%20%5Cbegin%7Bbmatrix%7D0%20%5C%5C%5Cdfrac%7B1%7D%7Bm%7D%5Cend%7Bbmatrix%7Du_%7B%28t%29%7D)

![=\begin{bmatrix}0\times x_1  + x_2\\-\dfrac{k}{m}x_1+0\times x_1\end{bmatrix}+\begin{bmatrix}0 \\\dfrac{1}{m}u_{(t)}\end{bmatrix}](https://chart.apis.google.com/chart?cht=tx&chl=%3D%5Cbegin%7Bbmatrix%7D0%5Ctimes%20x_1%20%20%2B%20x_2%5C%5C-%5Cdfrac%7Bk%7D%7Bm%7Dx_1%2B0%5Ctimes%20x_1%5Cend%7Bbmatrix%7D%2B%5Cbegin%7Bbmatrix%7D0%20%5C%5C%5Cdfrac%7B1%7D%7Bm%7Du_%7B%28t%29%7D%5Cend%7Bbmatrix%7D)

![= \begin{bmatrix}x_2\\-\dfrac{k}{m}x_1+\dfrac{1}{m}u_{(t)}\end{bmatrix}](https://chart.apis.google.com/chart?cht=tx&chl=%3D%20%5Cbegin%7Bbmatrix%7Dx_2%5C%5C-%5Cdfrac%7Bk%7D%7Bm%7Dx_1%2B%5Cdfrac%7B1%7D%7Bm%7Du_%7B%28t%29%7D%5Cend%7Bbmatrix%7D)

![=\begin{bmatrix}\dot{x_1}\\\dot{x_2}\end{bmatrix}](https://chart.apis.google.com/chart?cht=tx&chl=%3D%5Cbegin%7Bbmatrix%7D%5Cdot%7Bx_1%7D%5C%5C%5Cdot%7Bx_2%7D%5Cend%7Bbmatrix%7D)  
となるからです。

  
また、![y=\begin{bmatrix}1 & 0 \end{bmatrix}x](https://chart.apis.google.com/chart?cht=tx&chl=y%3D%5Cbegin%7Bbmatrix%7D1%20%26%200%20%5Cend%7Bbmatrix%7Dx)とあらわすことができます。
  
  
この、  
![ \begin{bmatrix}\dot{x_1}\\\dot{x_2}\end{bmatrix}= \begin{bmatrix}0 & 1 \\-\dfrac{k}{m} & 0 \end{bmatrix} \begin{bmatrix}x_1\\x_2\end{bmatrix} + \begin{bmatrix}0 \\\dfrac{1}{m}\end{bmatrix}u_{(t)}](https://chart.apis.google.com/chart?cht=tx&chl=%20%5Cbegin%7Bbmatrix%7D%5Cdot%7Bx_1%7D%5C%5C%5Cdot%7Bx_2%7D%5Cend%7Bbmatrix%7D%3D%20%5Cbegin%7Bbmatrix%7D0%20%26%201%20%5C%5C-%5Cdfrac%7Bk%7D%7Bm%7D%20%26%200%20%5Cend%7Bbmatrix%7D%20%5Cbegin%7Bbmatrix%7Dx_1%5C%5Cx_2%5Cend%7Bbmatrix%7D%20%2B%20%5Cbegin%7Bbmatrix%7D0%20%5C%5C%5Cdfrac%7B1%7D%7Bm%7D%5Cend%7Bbmatrix%7Du_%7B%28t%29%7D)

![y=\begin{bmatrix}1 & 0 \end{bmatrix}x](https://chart.apis.google.com/chart?cht=tx&chl=y%3D%5Cbegin%7Bbmatrix%7D1%20%26%200%20%5Cend%7Bbmatrix%7Dx)  
二つが状態方程式です。

式を見ればわかりますが、入力値のtex:u\_{(t)}\]と出力値（状態）を表す![x](https://chart.apis.google.com/chart?cht=tx&chl=x)の関係性を表しているということがわかります。  
  
  
### 現代制御の例2

以下のような状態について状態方程式を立ててみます。  
![f:id:pythonjacascript:20190218204648j:plain](/images/ppythonjacascript2019021820190218204648.jpg "f:id:pythonjacascript:20190218204648j:plain")

今度は、減衰係数![c](https://chart.apis.google.com/chart?cht=tx&chl=c)のダンパーが加わっています。

このとき、先ほどと同じように運動方程式を解くと、

![m\ddot{x} + c\dot{x} + kx = u_{(t)}](https://chart.apis.google.com/chart?cht=tx&chl=m%5Cddot%7Bx%7D%20%2B%20c%5Cdot%7Bx%7D%20%2B%20kx%20%3D%20u_%7B%28t%29%7D)

となります。![m\ddot{x}](https://chart.apis.google.com/chart?cht=tx&chl=m%5Cddot%7Bx%7D)について変形すると、

![m\ddot{x} = -\dfrac{c}{m}\dot{x} -\dfrac{k}{m}x + \dfrac{1}{m}u_{(t)}](https://chart.apis.google.com/chart?cht=tx&chl=m%5Cddot%7Bx%7D%20%3D%20-%5Cdfrac%7Bc%7D%7Bm%7D%5Cdot%7Bx%7D%20-%5Cdfrac%7Bk%7D%7Bm%7Dx%20%2B%20%5Cdfrac%7B1%7D%7Bm%7Du_%7B%28t%29%7D)

よって、これを状態方程式を使って表すと、  
![ \begin{bmatrix}\dot{x_1}\\\dot{x_2}\end{bmatrix}= \begin{bmatrix}0 & 1 \\-\dfrac{k}{m} & -\dfrac{c}{m}  \end{bmatrix} \begin{bmatrix}x_1\\x_2\end{bmatrix} + \begin{bmatrix}0 \\\dfrac{1}{m}\end{bmatrix}u_{(t)}](https://chart.apis.google.com/chart?cht=tx&chl=%20%5Cbegin%7Bbmatrix%7D%5Cdot%7Bx_1%7D%5C%5C%5Cdot%7Bx_2%7D%5Cend%7Bbmatrix%7D%3D%20%5Cbegin%7Bbmatrix%7D0%20%26%201%20%5C%5C-%5Cdfrac%7Bk%7D%7Bm%7D%20%26%20-%5Cdfrac%7Bc%7D%7Bm%7D%20%20%5Cend%7Bbmatrix%7D%20%5Cbegin%7Bbmatrix%7Dx_1%5C%5Cx_2%5Cend%7Bbmatrix%7D%20%2B%20%5Cbegin%7Bbmatrix%7D0%20%5C%5C%5Cdfrac%7B1%7D%7Bm%7D%5Cend%7Bbmatrix%7Du_%7B%28t%29%7D)

![y=\begin{bmatrix}1 & 0 \end{bmatrix}x](https://chart.apis.google.com/chart?cht=tx&chl=y%3D%5Cbegin%7Bbmatrix%7D1%20%26%200%20%5Cend%7Bbmatrix%7Dx)

となります。  
  
  
### 参考文献

<http://lab.sdm.keio.ac.jp/ogi/shibaura/ac2-1004.pdf>  
<http://www.ecs.shimane-u.ac.jp/~kyoshida/note2000.pdf>