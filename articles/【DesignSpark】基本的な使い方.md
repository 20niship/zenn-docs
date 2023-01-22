---
title: "【DesignSpark】基本的な使い方"
emoji: ""
type: ""
topics: []
published: false
---

![f:id:pythonjacascript:20181208181457j:plain](/images/ppythonjacascript2018120820181208181457.jpg "f:id:pythonjacascript:20181208181457j:plain")

この記事では、無料3DCADソフト、**Design Spark Mechanical** の基本的な使い方について紹介します。

* [物体について](#物体について)  
   * [サーフェス（面）VS ソリッド（物体）](#サーフェス面VS-ソリッド物体)
* [プレーン](#プレーン)
* [物体の選択方法](#物体の選択方法)  
   * [通常選択](#通常選択)  
   * [エッジループの選択](#エッジループの選択)  
   * [ソリッドの選択](#ソリッドの選択)  
   * [ボックス選択](#ボックス選択)  
   * [投げ縄選択](#投げ縄選択)  
   * [全選択・全解除](#全選択全解除)
* [ビューの操作](#ビューの操作)  
   * [スピン](#スピン)  
   * [パン](#パン)  
   * [ズーム](#ズーム)  
   * [ホームビュー](#ホームビュー)  
   * [平面ビュー](#平面ビュー)
* [座標のやつを使った移動](#座標のやつを使った移動)
* [参考文献：](#参考文献)

### 物体について

Design Sparkで製作した設計図には、**面（サーフェス）**・**ソリッド**・**プレート**・**プレート**という4つの構成要素で成り立っています。それぞれ説明してます。  
  
#### サーフェス（面）VS ソリッド（物体）

![f:id:pythonjacascript:20181216004322j:plain](/images/ppythonjacascript2018121620181216004322.jpg "f:id:pythonjacascript:20181216004322j:plain")  
まず、**サーフェス**と**ソリッド**についてです。どちらも、デザインしているものを表します。

違いは簡単で、**「サーフェス」が面（2D）**、**「ソリッド」が物体（3D）**です。「サーフェス」を**プル**するとソリッドを作成することができます。

それぞれ、「面」や「辺」という概念があり、それらをマウスで選択することができます。  
![f:id:pythonjacascript:20181216005117j:plain](/images/ppythonjacascript2018121620181216005117.jpg "f:id:pythonjacascript:20181216005117j:plain")  

### プレーン

寸法などを記載する面（プレート）です。同じプレーン上に複数の寸法を書き込むこと話できますが、寸法の図る向きが異なる場合、新しくプレーンを用意しなければなりません。  
![f:id:pythonjacascript:20181216005654j:plain:h300](/images/ppythonjacascript2018121620181216005654.jpg "f:id:pythonjacascript:20181216005654j:plain:h300")  
プレーンは↑の図のように、薄い青色で描画されます。

「**アノテーションプレーン**」とも書かれることもあります。  
  
  
### 物体の選択方法

物体の辺・面・頂点等を選択する方法についてです。  
  
#### 通常選択

オブジェクトの選択は、**左クリック**します。大抵の選択はこれで事足ります。

**Ctrlキー**を押しながら複数オブジェクトをクリックすると、複数のオブジェクトを選択することもできます。  
  
#### エッジループの選択

**方法：ダブルクリック**  
![f:id:pythonjacascript:20181216011522j:plain:h300](/images/ppythonjacascript2018121620181216011522.jpg "f:id:pythonjacascript:20181216011522j:plain:h300")  
左ダブルクリックをすると、このように**エッジループ**の選択が可能です。  
  
#### ソリッドの選択

**方法：トリプルクリック**

左クリックを高速で三回行うことで、そのソリッド全体を選択することができます。  
![f:id:pythonjacascript:20181216010139j:plain:h300](/images/ppythonjacascript2018121620181216010139.jpg "f:id:pythonjacascript:20181216010139j:plain:h300")

#### ボックス選択

![f:id:pythonjacascript:20181216010614j:plain](/images/ppythonjacascript2018121620181216010614.jpg "f:id:pythonjacascript:20181216010614j:plain")  
ドラッグすることで、ドラッグした範囲（選択ボックス）の中のオブジェクトを選択します。  
左上から右下へドラッグすると、選択ボックス内に**完全に含まれるオブジェクトのみ**が選択されます。この時、選択ボックスの概形は**実線**で描かれます。

反対に、右下から左上にドラッグすると、選択ボックスに**少しでも入るオブジェクトがすべて選択**されます。この時、選択ボックスの概形は**点線**で描かれます。  
  
#### 投げ縄選択

「**選択**」→「**投げ縄ボックス**」を選択します。  
![f:id:pythonjacascript:20181216011152j:plain:h150](/images/ppythonjacascript2018121620181216011152.jpg "f:id:pythonjacascript:20181216011152j:plain:h150")

そして、マウスをドラッグすると、その跡が選択ボックスの形となります。選択ボックスに完全に含まれるオブジェクトが選択されます。  
![f:id:pythonjacascript:20181216011316j:plain:h300](/images/ppythonjacascript2018121620181216011316.jpg "f:id:pythonjacascript:20181216011316j:plain:h300")

#### 全選択・全解除

「**選択**」→「**全選択**」をクリックすると、すべてのオブジェクトを選択することができます。  
![f:id:pythonjacascript:20181216011152j:plain:h200](/images/ppythonjacascript2018121620181216011152.jpg "f:id:pythonjacascript:20181216011152j:plain:h200")
  
  
### ビューの操作

3Dビューの操作方法を説明します。  
  
#### スピン

**方法：マウスホイールを押してドラッグ**  
スピン操作では、オブジェクトを画面上で自由に回転させて見る方向を変更することができます。  
![f:id:pythonjacascript:20181216000328g:plain](/images/ppythonjacascript2018121620181216000328.gif "f:id:pythonjacascript:20181216000328g:plain")

**マウスホイール**（マウス中心のころころ）をクリック＆ドラッグすると、デザイン全体を回転させることができます。このとき、**はじめにホイールをクリックする場所によって、回転の中心が変化します。**この動作は慣れるととても使いやすいです。  
  
#### パン

**方法：Shift + マウスホイールを押してドラッグ**  
  
オブジェクトの位置を上下左右に移動することができます。スピンと違って回転はせず、水平移動です。  
![f:id:pythonjacascript:20181216001539g:plain](/images/ppythonjacascript2018121620181216001539.gif "f:id:pythonjacascript:20181216001539g:plain")

  
#### ズーム

**方法：Ctrl + マウスホイールを押してドラッグ**  
  
デザイン全体を拡大・縮小表示します。  
![f:id:pythonjacascript:20181216001804g:plain](/images/ppythonjacascript2018121620181216001804.gif "f:id:pythonjacascript:20181216001804g:plain")

特定の部品を中心に拡大/縮小するには、**最初にその部品周辺をホイールでクリックし、そのままドラッグ**することで、その部品を中心に拡大/縮小ができます。  
  
  
#### ホームビュー

**方法：Hキー**  
  
デザインには、「**ホームビュー**」という決まった視点があります。「Hキー」を押すことで、その決まった視点にスムーズに移動します。  
新規デザインを作成したときの初めの視点が「ホームビュー」になります。  
![f:id:pythonjacascript:20181216002623g:plain](/images/ppythonjacascript2018121620181216002623.gif "f:id:pythonjacascript:20181216002623g:plain")

この機能は、ズームしすぎたりスピンしすぎてビュー画面がおかしくなった時にとても有効です。Hキーを押すだけで全体像を確認することができます。
  
  
#### 平面ビュー

**方法：面選択→Vキー**  
  
オブジェクトの特定の面を真上から見た視点に移動します。  
![f:id:pythonjacascript:20181216002636g:plain](/images/ppythonjacascript2018121620181216002636.gif "f:id:pythonjacascript:20181216002636g:plain")

視点を合わせたい面を**左クリック**で選択したのちに、Vキーを押します。  
  
### 座標のやつを使った移動

**方法：左下の座標の矢印を左クリック**  
  
「Z軸上に視点を置き、そこから物体をX-Y平面上に眺めたい」という時に使用します。  
![f:id:pythonjacascript:20181216012940g:plain](/images/ppythonjacascript2018121620181216012940.gif "f:id:pythonjacascript:20181216012940g:plain")

3Dビュー画面の左下には、小さな立方体とX軸、Y軸、Z軸の矢印が描画されています。  
![f:id:pythonjacascript:20181216013503j:plain:h150](/images/ppythonjacascript2018121620181216013503.jpg "f:id:pythonjacascript:20181216013503j:plain:h150")  
このそれぞれの**矢印を左クリック**すると、それぞれの軸上から眺めた視点に移動します。

  
また、その座標軸表示機の近くにマウスを持って行ったときに、下のように青い丸矢印が2つ、現れることがあります。  
![f:id:pythonjacascript:20181216013227j:plain:h150](/images/ppythonjacascript2018121620181216013227.jpg "f:id:pythonjacascript:20181216013227j:plain:h150")

これらをクリックすると、**視点を90度回転**することができます。  
  
  
### 参考文献：

[designspark.zendesk.com](https://designspark.zendesk.com/hc/ja/articles/214147205-DesignSpark-Mechanical-%E5%9F%BA%E6%9C%AC%E6%93%8D%E4%BD%9C%E5%85%A5%E9%96%80%E7%B7%A8)  
[designspark.zendesk.com](https://designspark.zendesk.com/hc/ja/articles/213603389-DesignSpark-Mechanical-%E5%9F%BA%E6%9C%AC%E3%82%AA%E3%83%96%E3%82%B8%E3%82%A7%E3%82%AF%E3%83%88%E3%81%A8%E5%9F%BA%E6%9C%AC%E3%83%84%E3%83%BC%E3%83%AB%E3%81%AB%E3%81%A4%E3%81%84%E3%81%A6)