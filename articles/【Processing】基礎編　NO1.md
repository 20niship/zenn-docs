---
title: "【Processing】基礎編　NO1"
emoji: ""
type: ""
topics: []
published: false
---

Processingの基本事項についてまとめます。  
この記事は、他のプログラミング言語についてすこし見たことがある人向けです。

* [1.setup関数とdraw関数](#1setup関数とdraw関数)
* [2.ピクセルについて](#2ピクセルについて)
* [3.setup()の中に書く関数 ～図形・文字編～](#3setupの中に書く関数図形文字編)  
   * [（1）size()関数](#1size関数)  
         * [構文➀：size(width, height) ;](#構文sizewidth-height-)  
         * [構文②：size(width, height, mode) ;](#構文sizewidth-height-mode-)  
   * [（2）frameRate関数](#2frameRate関数)  
   * [（3）smooth関数](#3smooth関数)
* [3.draw()の中に関数](#3drawの中に関数)  
   * [（1）line関数](#1line関数)  
   * [（2）ellipse関数](#2ellipse関数)  
   * [（3）rect関数](#3rect関数)  
   * [（4）rectMode関数](#4rectMode関数)  
   * [（5）text関数](#5text関数)  
   * [（6）textAlign関数](#6textAlign関数)  
   * [（7）background関数](#7background関数)  
   * [（8）point関数](#8point関数)
* [4.setup()の中に書く関数 ～色編～](#4setupの中に書く関数色編)  
   * [（1）colorMode関数](#1colorMode関数)  
   * [（2）stroke関数](#2stroke関数)  
   * [（2）fill関数](#2fill関数)  
   * [（3）noStroke関数](#3noStroke関数)  
   * [（4）strokeWeight関数](#4strokeWeight関数)  
   * [（5）noFill関数](#5noFill関数)

### 1.setup関数とdraw関数

Processingには、必ずsetup関数とdraw関数が必要です。  
**setup()で初期化**し、**draw()を繰り返し実行することでアニメーション**などが出来ます。  
setup()は一度だけしか使えません。例えば、前回の記事で使った次のようなプログラムの場合：

void setup(){
    size(300, 300) ;
}

void draw(){
    background(0) ;
    text("Hello, world", 15, 50) ;
}

このプログラムが実行されるとき、まずsetup関数が実行されます。そこで、size()関数が実行され、画面の初期設定が行われます。  
次にdraw()関数が繰り返し（１秒回に何十回も）実行され、その関数の中の描画処理（background()関数と、text()関数）が実行されます。

  
余談ですが、上のプログラムの形をみて、Arduino IDE の書き方に似ていると思った方もいると思います。Arduino IDEにもsetup関数とloop関数があります。実は、Arduino IDEは、Processing IDEをもとにして作られたIDEです。

### 2.ピクセルについて

世の中には、メートルやインチなど、多くの長さの単位がありますが、パソコンのディスプレイ上の長さを表すのに使う単位として、**ピクセル**というものがあります。

パソコンのディスプレイは、たくさんのドットでできています。例えば、花の画像をPCのディスプレイ上に表示するとき、その画像をこのように細かく区切って、それぞれの区画（ドット）ごとにある特定の色を表示します。（下図）  
![f:id:pythonjacascript:20180814140052g:plain](/images/ppythonjacascript2018081420180814140052.gif "f:id:pythonjacascript:20180814140052g:plain")  
そして、その区画（ドット）一つ分の長さが、1ピクセルとなるのです。

なので、「1920 × 1080ピクセルのディスプレイ」というのは、ドットが横に1920個、縦に1080個並んでいる、という意味です。

そして、**Processingで使われる長さの単位はすべて、ピクセル**です。

また、Processingで実行したアプリケーション画面の左上の座標が（0,0）です。  
  
### 3.setup()の中に書く関数 ～図形・文字編～

画面の初期設定に必要な関数を並べていきます。

#### （1）size()関数

##### 構文➀：size(width, height) ;

 \+ 画面の大きさを指定する（横＝width、縦＝height)  
 \+ もちろん、width, heightは小数点は不可（int型のみ）

##### 構文②：size(width, height, mode) ;

 \+ 画面の大きさを指定する（上と同じ）  
 \+ 描画モードを指定する（modeには、 P2D、P3D、JAVA2D、OPENGL ）がある。  
 \+ JAVA2D： デフォルト。精密な2Dレンダラ。   
 \+ P2D：高速な2Dレンダラ。  
 \+ P3D ： 高速な3Dレンダラ。Webでの使用に適している   
 \+ OPENGL ：高速3Dレンダラ。OpenGL対応のハードウエアを使用  
  
  
#### （2）frameRate関数

**構文：frameRate(fps)**

1. フレームレート（1秒間に画面が更新される回数）を変更する（fpsにフレームレートを代入)
2. 書かなくてもよい（デフォルトは60fpsです。）

#### （3）smooth関数

**構文：smooth();**

1. 図形のエッジを滑らかにする
2. 書かなくてもよい

### 3.draw()の中に関数

#### （1）line関数

 **構文：line(x1, y1, x2, y2)**

1. (x1, y1) から（x2, y2)まで線を引く

#### （2）ellipse関数

**構文：ellispe(x, y, width, height);**

1. 円や楕円を書く
2. 円の中心座標＝(x, y)
3. width = 楕円の横幅（直径）
4. height = 楕円の縦幅（直径）

#### （3）rect関数

**構文：rect(x1, x2, a, b);**

1. 長方形を書く。
2. x１、x２、a, bで長方形の表示位置と大きさを指定するが、その方法はrectMode()によって異なる。
3. rectMode()を書かない（デフォルト）の場合：

 長方形の左上を（x1, y1)として、横の長さがa、縦の長さがbの長方形を書く。

1. rectModeを指定した場合のrect()関数の書き方は、rectMode関数の所を参照。

#### （4）rectMode関数

構文：**rectMode(mode);** 
\+ 長方形を書く時の座標の指定方法を変更する。

1. modeには、「CORNER」、「CORNERS」、「CENTER」「RADIUS」の4種類がある
2. CORNERモード ：rect(左上頂点のx座標, 左上頂点のy座標, 幅, 高さ) ※デフォルト
3. CORNERSモード : rect(左上頂点のx座標, 左上頂点のy座標, 右下頂点のx座標, 右下頂点のy座標)
4. CENTERモード ：rect(中心のx座標, 中心のy座標, 幅, 高さ)
5. RADIUSモード ：rect(中心のx座標, 中心のy座標, 幅の半分, 高さの半分)

#### （5）text関数

構文：**text("文字列",x, y);** 
\+ ””で囲まれた文字列を（ｘ, y)に表示する。（x、ｙ）の指定の方法は、textAlign()関数で変わってくる。  
  
  
#### （6）textAlign関数

構文：**textAlign(mode1, mode2);** 
\+ text("文字列",x, y)関数の（x、ｙ）の指定の方法を変更する。

1. mode1でx方向のモードを指定して、mode2でy方向のモードを指定する
2. mode1は、「LEFT」（左揃え）、「CENTER」(中央ぞろえ）、「RIGHT」（右揃え）の３種類
3. mode2は、「BASELINE」（ベースライン）、「TOP」（上）、「CENTER」（中央）、「BOTTOM」（下）の4種類
4. 例えば、

textAlign( LEFT,  BOTTOM);
text("Hello, World", 200, 100);

と書くと、「hello, World」という文字列が、左下の座標が（200, 100）になるように表示される。  
  
  
#### （7）background関数

1. 画面全体を特定の色で塗りつぶす

**構文➀：background(gray)** 
**構文②：background(gray, alpha)** 
**構文③：background(R, G, B)** 
**構文④：background(R, G, B, alpha)**など、多くある。  
  
  
#### （8）point関数

構文：**point(x, y);**\+ 座標（x, y)に点を描画する  
  
  
### 4.setup()の中に書く関数 ～色編～

#### （1）colorMode関数

1. 色の表現の仕方は、RGBモードとHSBモードがある。そして、図形に色を付けるときに、どちらのモードで色を指定するかを設定する

構文：  
**colorMode(RGB,256); //RGBモード（各256段階）で指定** 
**colorMode(HSB,360,100,100); //HSBモード（H：360段階, S,B：100段階）で指定** 
**colorMode(HSB,360,100,100,100); //HSBモード（H:360段階, S,B,Alpha:各100段階）で指定**

  
#### （2）stroke関数

構文：**stroke(a, b, c);** 
\+ 線の色を（a, b, c）に変更する

1. （a, b, c）がRGBモードかHSBモード化かは、colorMode関数で決定する。

#### （2）fill関数

1. 図形内部を塗りつぶす色を変更する

構文➀：**fill(gray)**; //塗りつぶす色をgray色に変更  
構文②：**fill(a, b, c);**  //塗りつぶす色を（a, b, c）に変更  
  
  
#### （3）noStroke関数

構文：**noStroke();**\+ 縁線のない円を描く  
  
  
#### （4）strokeWeight関数

構文：**strokeWeight(a)** 

1. 図形の枠線の太さを a に変更

#### （5）noFill関数

構文：**noFill() ;**\+ 図形の塗りつぶしをしないように設定

  
参考文献：  
<http://www.musashinodenpa.com/p5/>  
<http://www.d-improvement.jp/learning/processing/2011-a/02.html>  
[Language Reference (API) \\ Processing 3+](https://processing.org/reference/)