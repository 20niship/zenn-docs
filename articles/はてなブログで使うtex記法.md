---
title: "はてなブログで使うtex記法"
emoji: ""
type: ""
topics: undefined
published: false
---

**数式の見た目をきれいにしたい！**

  
そんな時に使うのが**Tex記法**です。

例えば、

u(t) = Kp \* e(t) + Ki ∫0^t e(tau) \* d tau + Kd \* de(t) / dt

より、

![u(t)=K_p e(t)+K_i\int _{{0}}^{{t}}e(\tau )\,d\tau +K_d\dfrac{de(t)}{dt}](https://chart.apis.google.com/chart?cht=tx&chl=u%28t%29%3DK_p%20e%28t%29%2BK_i%5Cint%20_%7B%7B0%7D%7D%5E%7B%7Bt%7D%7De%28%5Ctau%20%29%5C%2Cd%5Ctau%20%2BK_d%5Cdfrac%7Bde%28t%29%7D%7Bdt%7D)

のほうが百倍分かりやすいと思います。

後者の数式は、

[tex:u(t)=K_p e(t)+K_i\int _{{0}}^{{t}}e(\tau )\,d\tau +K_d\dfrac{de(t)}{dt}]

と書いて表現しています。  
このように、数式の見た目を美しくするのには、**Texコマンド**を使います。。

* [普通の書き方：普通に](#普通の書き方普通に)
* [関数：普通に](#関数普通に)
* [右下に小さい文字を置く：「A\_B」](#右下に小さい文字を置くA%5FB)
* [分数（サイズ大）：\\dfrac{a}{b}](#分数サイズ大dfracab)
* [分数（サイズ小）：\\frac{a}{b}](#分数サイズ小fracab)
* [積分：「\\inf\_a^b」](#積分inf%5Fab)
* [ルート：\\sqrt{a}](#ルートsqrta)
* [連立方程式：「中括弧{}を使用」](#連立方程式中括弧を使用)
* [右上、右下に小さく書く](#右上右下に小さく書く)
* [行列](#行列)
* [ベクトル](#ベクトル)
* [おすすめの方法：手書き入力](#おすすめの方法手書き入力)
* [おすすめの方法2：HostMath](#おすすめの方法2HostMath)
* [参考文献：](#参考文献)

### 普通の書き方：普通に

![ x + 2y = 5](https://chart.apis.google.com/chart?cht=tx&chl=%20x%20%2B%202y%20%3D%205)  
書き方：

[tex: x + 2y = 5]

### 関数：普通に

![ f(x), f(x, y)](https://chart.apis.google.com/chart?cht=tx&chl=%20f%28x%29%2C%20f%28x%2C%20y%29)  
書き方：

[tex: f(x), f(x, y)]

### 右下に小さい文字を置く：「A\_B」

![ x = v_0 t + \dfrac{1}{2}gt^2](https://chart.apis.google.com/chart?cht=tx&chl=%20x%20%3D%20v_0%20t%20%2B%20%5Cdfrac%7B1%7D%7B2%7Dgt%5E2)  
書き方：

[tex: x = v_0 t + \dfrac{1}{2}gt^2]

・「v\_0」の部分。アンダーバー（＿）を書く  
・右下の添え字を書き終わったら、半角スペースを書いたほうがいい。  
  
### 分数（サイズ大）：\\dfrac{a}{b}

![ a = \dfrac{1.4142}{1.23456}](https://chart.apis.google.com/chart?cht=tx&chl=%20a%20%3D%20%5Cdfrac%7B1.4142%7D%7B1.23456%7D)  
書き方：

[tex: a = \dfrac{1.4142}{1.23456}]

・\\dfrac{a}{b}とかく。a = 分子 b = 分子  
・分母、分子の文字サイズが周りの文字と同じ大きさ  
  
### 分数（サイズ小）：\\frac{a}{b}

![ a = \frac{1.4142}{1.23456}](https://chart.apis.google.com/chart?cht=tx&chl=%20a%20%3D%20%5Cfrac%7B1.4142%7D%7B1.23456%7D)  
書き方：

[tex: a = \frac{1.4142}{1.23456}]

・\\frac{a}{b}と書く。a = 分子 b = 分子  
・分母、分子の文字を小さくして、一行分の高さで分数を表現。  
  
  
### 積分：「\\inf\_a^b」

![\int_1^t f(x)](https://chart.apis.google.com/chart?cht=tx&chl=%5Cint_1%5Et%20f%28x%29)  
書き方：

[tex:\int_1^t f(x)]

### ルート：\\sqrt{a}

![\sqrt{2} = \sqrt{a}](https://chart.apis.google.com/chart?cht=tx&chl=%5Csqrt%7B2%7D%20%3D%20%5Csqrt%7Ba%7D)  
書き方：

[tex:\sqrt{2} = \sqrt{a}]

### 連立方程式：「中括弧{}を使用」

![{\displaystyle 
\begin{eqnarray}
  \left\{
    \begin{array}{l}
      x + y = 10 \\
     2x + 4y = 32
    \end{array}
  \right.
\end{eqnarray}
}](https://chart.apis.google.com/chart?cht=tx&chl=%7B%5Cdisplaystyle%20%0A%5Cbegin%7Beqnarray%7D%0A%20%20%5Cleft%5C%7B%0A%20%20%20%20%5Cbegin%7Barray%7D%7Bl%7D%0A%20%20%20%20%20%20x%20%2B%20y%20%3D%2010%20%5C%5C%0A%20%20%20%20%202x%20%2B%204y%20%3D%2032%0A%20%20%20%20%5Cend%7Barray%7D%0A%20%20%5Cright.%0A%5Cend%7Beqnarray%7D%0A%7D)

書き方：

[tex:{\displaystyle 
\begin{eqnarray}
  \left\{
    \begin{array}{l}
      x + y = 10 \\
     2x + 4y = 32
    \end{array}
  \right.
\end{eqnarray}
}]
  
  
### 右上、右下に小さく書く

このように書くには、「＾」や「＿」を使います。  
小さく書きたい文字が複数ある場合は、｛中カッコ｝でくくってその中に書きます。

![y = x^2](https://chart.apis.google.com/chart?cht=tx&chl=y%20%3D%20x%5E2)

![a_{n+1} = 3a_n + 5](https://chart.apis.google.com/chart?cht=tx&chl=a_%7Bn%2B1%7D%20%3D%203a_n%20%2B%205)

書き方

[tex:y = x^2]

[tex:a_{n+1} = 3a_n + 5]

  
### 行列

![\begin{bmatrix}a & b \\c & d \end{bmatrix}](https://chart.apis.google.com/chart?cht=tx&chl=%5Cbegin%7Bbmatrix%7Da%20%26%20b%20%5C%5Cc%20%26%20d%20%5Cend%7Bbmatrix%7D)  
このよう名行列表記を使うには、{bmatrix}を使います。

  
書き方：

[tex:\begin{bmatrix}a & b \\c & d \end{bmatrix}]

  
実際に使うとこんな感じです。  
![A = \begin{bmatrix}a & b \\c & d \end{bmatrix} + \begin{bmatrix}e & f \\g & h \end{bmatrix}](https://chart.apis.google.com/chart?cht=tx&chl=A%20%3D%20%5Cbegin%7Bbmatrix%7Da%20%26%20b%20%5C%5Cc%20%26%20d%20%5Cend%7Bbmatrix%7D%20%2B%20%5Cbegin%7Bbmatrix%7De%20%26%20f%20%5C%5Cg%20%26%20h%20%5Cend%7Bbmatrix%7D)

  
書き方：

[tex:A = \begin{bmatrix}a & b \\c & d \end{bmatrix} + \begin{bmatrix}e & f \\g & h \end{bmatrix}
  
  
### ベクトル

![\vec{a} = (a_1, a_2, \cdots, a_n)](https://chart.apis.google.com/chart?cht=tx&chl=%5Cvec%7Ba%7D%20%3D%20%28a_1%2C%20a_2%2C%20%5Ccdots%2C%20a_n%29)

ベクトルを表記するには、矢印を書きたい文字の前に**\\vec**を付けます。

書き方：

[tex:\vec{a} = (a_1, a_2, \cdots, a_n)]

### おすすめの方法：手書き入力

以下のサイトを使うと、手書きの数式をtex記法に自動変換してくれます。  
<https://webdemo.myscript.com/views/main/math.html>

![f:id:pythonjacascript:20180922173643j:plain:h300](/images/ppythonjacascript2018092220180922173643.jpg "f:id:pythonjacascript:20180922173643j:plain:h300")

  
右側の「**LaTex**」と書かれたタブを選択して、あとは「**Write Here**」と薄く書かれたウィンドウにマウスやペンタブで数式を書くだけです。  
認識精度も高く、微積の記号、ギリシャ文字、行列、etc. 数式のほとんどがサポートされている感じです。

書き終わったら、「LaTex」と書かれたタブの下の文字列をコピペするだけです。

はてなブログにこのサイトで作成したTexコマンドを入力するときは、

> **\[tex：（ここに上記サイトからコピペしたTexコマンドを入力\]」**

としてください。  
  
### おすすめの方法2：HostMath

**HostMath**とは、オンラインのTexエディターです。  
[www.hostmath.com](http://www.hostmath.com/)

このサイトを使えば、数式のサンプルパーツを選択しながら、リアルタイムでTex数式を作成することができます。  
![f:id:pythonjacascript:20190218185322j:plain](/images/ppythonjacascript2019021820190218185322.jpg "f:id:pythonjacascript:20190218185322j:plain")  
また、書いてみたTex数式がきちんと表示されるかを確かめる確認用Viewerとしても活躍できます。  
  
### 参考文献：

[【はてなブログ】数式を綺麗に表示してみよう【Tex】 - Milkのメモ帳](https://www.milkmemo.com/entry/hatena%5Ftex)