---
title: "【最適化シリーズ NO.1】Pythonによる線形計画法の最適化"
emoji: "🤖"
type: "tech"
topics: []
published: false
---

* [1.最適化するもの](#1最適化するもの)
* [2.Pythonを使わずに数学で解く。](#2Pythonを使わずに数学で解く)
* [3.Python（pulp）を使って解く](#3Pythonpulpを使って解く)  
   * [プログラム](#プログラム)  
   * [解説](#解説)  
         * [STEP1\. 数理モデルの作成](#STEP1-数理モデルの作成)  
         * [STEP2\. 変数の作成](#STEP2-変数の作成)  
         * [STEP3\. 目的関数の作成](#STEP3-目的関数の作成)  
         * [STEP4\. 制約条件の作成](#STEP4-制約条件の作成)  
         * [STEP5\. 最適化の実行](#STEP5-最適化の実行)
* [4.Python（cvxpy）を使って解く](#4Pythoncvxpyを使って解く)  
   * [プログラム](#プログラム-1)  
   * [解説](#解説-1)  
         * [STEP1\. 変数の作成](#STEP1-変数の作成)  
         * [STEP2\. 目的関数の設定](#STEP2-目的関数の設定)  
         * [STEP3\. 制約条件の作成](#STEP3-制約条件の作成)  
         * [STEP4\. 最適化の実行](#STEP4-最適化の実行)
* [参考文献](#参考文献)

### 1.最適化するもの

Pythonを使って次のような問題を解いてみます。

問題：  
![x≥0,y≥0 , x+y≤3,x+2y≤4](https://chart.apis.google.com/chart?cht=tx&chl=x%E2%89%A50%2Cy%E2%89%A50%20%2C%20x%2By%E2%89%A43%2Cx%2B2y%E2%89%A44) で表される領域内で関数 ![f(x,y)=4x+5y](https://chart.apis.google.com/chart?cht=tx&chl=f%28x%2Cy%29%3D4x%2B5y) を最大にする点とその最大値を求めよ。  
  
  
### 2.Pythonを使わずに数学で解く。

![x≥0,y≥0 , x+y≤3,x+2y≤4](https://chart.apis.google.com/chart?cht=tx&chl=x%E2%89%A50%2Cy%E2%89%A50%20%2C%20x%2By%E2%89%A43%2Cx%2B2y%E2%89%A44) で表される領域は、斜線部分（境界線を含む）のようになる。  
![f:id:pythonjacascript:20180826124655p:plain:h350](/images/ppythonjacascript2018082620180826124655.png "f:id:pythonjacascript:20180826124655p:plain:h350")

![4x+5y = k](https://chart.apis.google.com/chart?cht=tx&chl=4x%2B5y%20%3D%20k)とすると、![y = -\dfrac{4}{5} x + \dfrac{1}{5} k](https://chart.apis.google.com/chart?cht=tx&chl=y%20%3D%20-%5Cdfrac%7B4%7D%7B5%7D%20x%20%2B%20%5Cdfrac%7B1%7D%7B5%7D%20k)となり、これは、傾き，傾き![ -\dfrac{4}{5} ](https://chart.apis.google.com/chart?cht=tx&chl=%20-%5Cdfrac%7B4%7D%7B5%7D%20)で![y](https://chart.apis.google.com/chart?cht=tx&chl=y)切片が ![\dfrac{1}{5}k](https://chart.apis.google.com/chart?cht=tx&chl=%5Cdfrac%7B1%7D%7B5%7Dk) の直線である。よって、この直線が![(2, 1)](https://chart.apis.google.com/chart?cht=tx&chl=%282%2C%201%29)を通るときに、![f(x, y)](https://chart.apis.google.com/chart?cht=tx&chl=f%28x%2C%20y%29)は最大になる。  
![f:id:pythonjacascript:20180826125521p:plain:h350](/images/ppythonjacascript2018082620180826125521.png "f:id:pythonjacascript:20180826125521p:plain:h350")

その時の![k](https://chart.apis.google.com/chart?cht=tx&chl=k)の値は、![2 = -\dfrac{4}{5}  + \dfrac{1}{5} k](https://chart.apis.google.com/chart?cht=tx&chl=2%20%3D%20-%5Cdfrac%7B4%7D%7B5%7D%20%20%2B%20%5Cdfrac%7B1%7D%7B5%7D%20k)を解いて![k = 13](https://chart.apis.google.com/chart?cht=tx&chl=k%20%3D%2013)となり、  
点![(2, 1)](https://chart.apis.google.com/chart?cht=tx&chl=%282%2C%201%29)において最大値は13をとる。
  
  
という解き方です。高校数学でくわしく習います。  
  
  
### 3.Python（pulp）を使って解く

数理最適化用の「PuLP」というライブラリを見つけたので、使ってみます。

#### プログラム

from pulp import *

m = LpProblem(sense=LpMaximize) #1. 数理モデルを作成（最大化）

x = LpVariable('x') # 2.変数の作成
y = LpVariable('y') 

m += 4 * x + 5 * y  # 3.目的関数の設定

m += x >= 0      #4.変数の制約条件の作成
m += y >=0
m += x + y <= 3
m += x + 2*y <= 4

m.solve()   #最適化を実行
Max_x = value(x)
Max_y = value(y)
MaxValue = 4 * value(x) + 5 * value(y)

print("x = " + str(Max_x))
print("y = " + str(Max_y))
print("のとき、最大値" + str(MaxValue))

  
実行結果：

x = 2.0
y = 1.0
のとき、最大値13.0

#### 解説

上記の問題は、目的関数 ![f(x,y)=4x+5y](https://chart.apis.google.com/chart?cht=tx&chl=f%28x%2Cy%29%3D4x%2B5y)を最大化する![x, y](https://chart.apis.google.com/chart?cht=tx&chl=x%2C%20y)の値を、制約条件を満たしながら求める、というものです。なので、最適化を行うためには、

| 目的関数 | ![f(x,y)=4x+5y](https://chart.apis.google.com/chart?cht=tx&chl=f%28x%2Cy%29%3D4x%2B5y)                                                         |
| ---- | ---------------------------------------------------------------------------------------------------------------------------------------------- |
| 変数   | ![x](https://chart.apis.google.com/chart?cht=tx&chl=x)と![y](https://chart.apis.google.com/chart?cht=tx&chl=y)                                  |
| 制約条件 | ![x≥0,y≥0 , x+y≤3,x+2y≤4](https://chart.apis.google.com/chart?cht=tx&chl=x%E2%89%A50%2Cy%E2%89%A50%20%2C%20x%2By%E2%89%A43%2Cx%2B2y%E2%89%A44) |

の３つをプログラムとして記述する必要があります。  
では、実際のコードを見ていきます。  
  
##### STEP1\. 数理モデルの作成

m = LpProblem(sense=LpMaximize) #1. 数理モデルを作成（最大化）

目的関数を最大化するための数理モデルを作製します。

目的関数を最小化したい時はこのように書きます。

m = LpProblem() #1. 数理モデルを作成（最小化）

##### STEP2\. 変数の作成

x = LpVariable('x') # 2.変数の作成
y = LpVariable('y') 

LpVariable で変数を作成します。

元の構文はこのようになっています。

LpVariable(name, lowBound=None, upBound=None, cat='Continuous', e=None)

| name     | 変数名                  |  |
| -------- | -------------------- |  |
| lowbound | 変数の最小値。デフォルトは-inf    |  |
| upBound  | 変数の最大値。デフォルトは +inf   |  |
| cat      | 変数の種類（整数、小数（←デフォルト）等 |  |

使い方の例

LpVariable('y', lowbound = 0)  #正の数
LpVariable('y', lowerbound = 0, upbound = 100, cat = 'Integer')  #0~100の整数

##### STEP3\. 目的関数の作成

目的関数を設定するには、次のように書きます。

m += 4 * x + 5 * y  # 3.目的関数の設定

##### STEP4\. 制約条件の作成

制約条件を設定するには、次のように書きます。  
今回は4つの条件があるので、それらを順番に記述していきます。

m += x >= 0      #4.変数の制約条件の作成
m += y >=0
m += x + y <= 3
m += x + 2*y <= 4

##### STEP5\. 最適化の実行

solve()で最適化を実行します。

m.solve()   #最適化を実行

### 4.Python（cvxpy）を使って解く

cvxpyは、Anacondaにデフォルトでインストールされていました。

#### プログラム

x = cvxpy.Variable()
y = cvxpy.Variable()

objective = cvxpy.Maximize(4 * x + 5 * y)

constraints = [0 <= x]
constraints += [0 <= y]
constraints += [x + y <= 3]
constraints += [x + 2*y <= 4]
prob = cvxpy.Problem(objective, constraints)

result = prob.solve()

print("x = ", x.value)
print("y = ", y.value)
print("のとき、最大値 = ", prob.value)

  
実行結果：

x =  2.000000025730404
y =  0.9999999678267295
のとき、最大値 =  12.999999942055263

#### 解説

##### STEP1\. 変数の作成

変数を作成します。

x = cvxpy.Variable()
y = cvxpy.Variable()

##### STEP2\. 目的関数の設定

目的関数を最大化するための数理モデルを作製します。

objective = cvxpy.Maximize(4 * x + 5 * y)

##### STEP3\. 制約条件の作成

制約条件を設定するには、次のように書きます。  
今回は4つの条件があるので、それらを順番に記述していきます。

constraints = [0 <= x]
constraints += [0 <= y]
constraints += [x + y <= 3]
constraints += [x + 2*y <= 4]

##### STEP4\. 最適化の実行

solve()で最適化を実行します。

result = prob.solve()

### 参考文献

[領域における最大・最小問題（線形計画法） | 高校数学の美しい物語](https://mathtrain.jp/linearprogramming)  
[pulp: Pulp classes — PuLP v1.4.6 documentation](https://www.coin-or.org/PuLP/pulp.html)  
[PuLPを使って線形計画問題を解く - DISTRICT 37](http://dragstar.hatenablog.com/entry/2015/03/23/114335)  
[Python製凸最適化モデリングツールCVXPYの使い方 - MyEnigma](https://myenigma.hatenablog.com/entry/2016/11/06/114603)