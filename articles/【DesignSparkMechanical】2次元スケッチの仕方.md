---
title: "【DesignSparkMechanical】2次元スケッチの仕方"
emoji: ""
type: ""
topics: []
published: false
---

無料の3DCADの中で有名なものとして、**DesignSparkMechanical（以下、略してDSM）**があります。  
[shizenkarasuzon.hatenablog.com](https://shizenkarasuzon.hatenablog.com/entry/2018/12/10/023130)

この記事で、DSMでの設計において重要な操作は

1. 二次元スケッチの設計
2. プル命令で二次元を三次元に

の２つであると書きました。

今回は、そのうちの1つ目、「**2次元スケッチ**」の方法について書いてみました。  
  
* [1.二次元スケッチとは？](#1二次元スケッチとは)
* [2.実際にやってみよ～！（プロジェクト作成）](#2実際にやってみよプロジェクト作成)
* [3.2Dスケッチの基本操作](#32Dスケッチの基本操作)  
   * [3Dモードに戻る](#3Dモードに戻る)  
   * [平面ビュー](#平面ビュー)  
   * [新規スケッチ面を選択](#新規スケッチ面を選択)  
   * [スケッチ面を移動](#スケッチ面を移動)
* [3.スケッチする](#3スケッチする)  
   * [直線を書く](#直線を書く)  
   * [四角形を書く](#四角形を書く)  
   * [円を書く](#円を書く)  
   * [面取り＆フィレット](#面取りフィレット)  
   * [コーナーを作成](#コーナーを作成)

### 1.二次元スケッチとは？

その名の通り、**二次元（平面上）で設計図を描くこと**です。後ほど、この設計図をもとに3次元の物体を作るので、**断面図**といってもいいでしょう。

![f:id:pythonjacascript:20181210020018j:plain](/images/ppythonjacascript2018121020181210020018.jpg "f:id:pythonjacascript:20181210020018j:plain")  
（2次元スケッチの画面）

逆に言えば、この2次元スケッチができなければ、3Dの物体を作ることは無理である！ということになります。  
  
### 2.実際にやってみよ～！（プロジェクト作成）

...ということで、今から2Dスケッチを実際にやるのですが、その前にプロジェクトを作成する必要があります。

プロジェクトの作成方法は、  
[shizenkarasuzon.hatenablog.com](https://shizenkarasuzon.hatenablog.com/entry/2018/12/10/023130)  
で解説していますが、

**「ファイル」→「新規作成」→「デザイン」**をクリックすると、プロジェクトを新規作成できます。  
[![/images/ppythonjacascript2018121020181210015941.jpg](/images/ppythonjacascript2018121020181210015941.jpg)](/images/ppythonjacascript2018121020181210015941.jpg)  
  
すると、このように網目状の平面が描画されているはずです。  
[![/images/ppythonjacascript2018121020181210020018.jpg](/images/ppythonjacascript2018121020181210020018.jpg)](/images/ppythonjacascript2018121020181210020018.jpg)

これが、2Dスケッチの初期画面です。  
  
  
### 3.2Dスケッチの基本操作

では、網目状の平面（**スケッチグリッド、スケッチ面**といいます）の上にスケッチをしていきます。

...のですが、いくつか基本的知識として知って欲しいことがあります。  
それは、右下のこの4つのアイコンです。  
![f:id:pythonjacascript:20190103174259j:plain](/images/ppythonjacascript2019010320190103174259.jpg "f:id:pythonjacascript:20190103174259j:plain")

それぞれ、次のような意味があります。押してみたらわかると思います。

#### 3Dモードに戻る

![f:id:pythonjacascript:20190103175318j:plain](/images/ppythonjacascript2019010320190103175318.jpg "f:id:pythonjacascript:20190103175318j:plain")  
このボタンを押すと、2Dスケッチを終了して3Dモードに戻ります。  
モードについてはこのサイトをご覧ください。  
  
#### 平面ビュー

![f:id:pythonjacascript:20190103174747j:plain](/images/ppythonjacascript2019010320190103174747.jpg "f:id:pythonjacascript:20190103174747j:plain")  
このアイコンを押すと、スケッチ面が画面正面になるように回転して表示します。  
押してみたらわかると思います。  
  
#### 新規スケッチ面を選択

![f:id:pythonjacascript:20190103174559j:plain](/images/ppythonjacascript2019010320190103174559.jpg "f:id:pythonjacascript:20190103174559j:plain")  
このアイコンを押すと、スケッチ面の位置を変更することができます。  
物体がすでにあった場合、このアイコンを押してその**面を選択（左クリック）すると、選択した面がスケッチ面になります**。  
  
#### スケッチ面を移動

![f:id:pythonjacascript:20190103174930j:plain](/images/ppythonjacascript2019010320190103174930.jpg "f:id:pythonjacascript:20190103174930j:plain")  
このアイコンを押すと、スケッチ面を**XYZ方向**に移動させることができます。  
アイコンを押すと、下の写真のようにスケッチ面上に3つの赤緑青の矢印が表示されます。  
![f:id:pythonjacascript:20190103175106j:plain](/images/ppythonjacascript2019010320190103175106.jpg "f:id:pythonjacascript:20190103175106j:plain")  
その矢印の上にマウスを置いて、そのまま矢印をドラッグすると、矢印の方向にスケッチ面を平行移動させることができます。

  
基本知識は以上です！では、実際にスケッチをしていこう～！

### 3.スケッチする

基本的にスケッチは、上のスケッチタブにある図形アイコンを押して行います。  
こいつらです。  
![f:id:pythonjacascript:20190103180212j:plain](/images/ppythonjacascript2019010320190103180212.jpg "f:id:pythonjacascript:20190103180212j:plain")

では、まずは簡単なただ図形を書くだけのものから。  
  
#### 直線を書く

2Dスケッチモードになっていることを確認しといてね。  
まず、下の画像のように「直線」アイコンをクリックします。  
![f:id:pythonjacascript:20190103184327j:plain](/images/ppythonjacascript2019010320190103184327.jpg "f:id:pythonjacascript:20190103184327j:plain")

そして、マウスをスケッチ面上において、ドラッグさせます。  
![f:id:pythonjacascript:20190103184330j:plain](/images/ppythonjacascript2019010320190103184330.jpg "f:id:pythonjacascript:20190103184330j:plain")  
すると、ドラッグの開始点が直線の一端になり、ドラッグの終点が直線のもう一端になります。  
繰り返しクリックすることで直線を続けて書くこともできます。  
![f:id:pythonjacascript:20190103184916j:plain](/images/ppythonjacascript2019010320190103184916.jpg "f:id:pythonjacascript:20190103184916j:plain")  
直線描画を終了するには**Escキー**を押します。  
  
  
#### 四角形を書く

![f:id:pythonjacascript:20190103180604j:plain](/images/ppythonjacascript2019010320190103180604.jpg "f:id:pythonjacascript:20190103180604j:plain")  
まず、上の画像のように**「矩形」アイコン**をクリックします。これは長方形を描画するアイコンです。

![f:id:pythonjacascript:20190103180616j:plain](/images/ppythonjacascript2019010320190103180616.jpg "f:id:pythonjacascript:20190103180616j:plain")  
そして、マウスをスケッチ面上において、ドラッグさせます。  
すると、長方形が書けます。そして、同時に寸法線も表示されるので、**サイズ指定も行うことができます。**  
寸法線の数字が表示されている部分をクリックして、数値を打ち込むだけです。  
  
  
#### 円を書く

![f:id:pythonjacascript:20190103181511j:plain](/images/ppythonjacascript2019010320190103181511.jpg "f:id:pythonjacascript:20190103181511j:plain")  
まず、上の写真のように「円アイコン」をクリックします。

そして、マウスをスケッチ面上でドラッグします。  
![f:id:pythonjacascript:20190103181543j:plain](/images/ppythonjacascript2019010320190103181543.jpg "f:id:pythonjacascript:20190103181543j:plain")  
すると、ドラッグし始めた点が円の中心になり、ドラッグを終えたところが円周上の点になります。  
一旦ドラッグをした後で、半径を入力して調整することができます。  
  
  
#### 面取り＆フィレット

> 面取りとは、面取りとは、角部を削り角面や丸面などの形状に加工する工法である

（Wikipediaより）  
とりあえず、実際にやってみたらわかります。

まず、下の「面取り」アイコンをクリックします。  
![f:id:pythonjacascript:20190103182744j:plain](/images/ppythonjacascript2019010320190103182744.jpg "f:id:pythonjacascript:20190103182744j:plain")

次に、下の画像のように**2本の直線を順に選択**します。  
![f:id:pythonjacascript:20190103182534j:plain](/images/ppythonjacascript2019010320190103182534.jpg "f:id:pythonjacascript:20190103182534j:plain")  
すると、それらの直線の交点が丸くなって**R**（丸み）が付きます。

Rの半径は、2本目の直線をクリックするときに、そのクリック位置で決定されます。  
もちろん選択後に数値入力で変更することもできますが。  
  
  
#### コーナーを作成

見出しが長くなってしまいましたが、**要は2直線の交点を作成**する、ってことです。  
まず、「コーナーを作成」アイコンをクリックします。  
![f:id:pythonjacascript:20190103183748j:plain](/images/ppythonjacascript2019010320190103183748.jpg "f:id:pythonjacascript:20190103183748j:plain")

そして、つながっていない2直線を順にクリックします。  
![f:id:pythonjacascript:20190103183524j:plain](/images/ppythonjacascript2019010320190103183524.jpg "f:id:pythonjacascript:20190103183524j:plain")  
すると、自動的に交点が作成されます。

尚、当たり前ですが2直線が平行であれば交点はできないのでエラーになります。

  
よく使うのはこれくらいです。