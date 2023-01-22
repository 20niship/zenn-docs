---
title: "【DesignSpark】プルの仕方"
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

前回の記事で、1つ目の**2次元スケッチ**の方法について書いてみました。  
[shizenkarasuzon.hatenablog.com](https://shizenkarasuzon.hatenablog.com/entry/2019/01/03/184957)

今回は、STEP2の「**プル**」について書きます。  
  
* [プルとは？](#プルとは)
* [2Dスケッチを平行移動して3D物体を生成](#2Dスケッチを平行移動して3D物体を生成)
* [２Dスケッチを回転移動して3D物体を生成](#２Dスケッチを回転移動して3D物体を生成)  
   * [回転によるプル（応用編）](#回転によるプル応用編)
* [物体の一部をプルして変形する（面取り編）](#物体の一部をプルして変形する面取り編)
* [物体の一部をプルして変形する（穴あけ編）](#物体の一部をプルして変形する穴あけ編)

### プルとは？

前回作成したのは、2Dスケッチです。しかし、DesignSparkは3DCAD。  
どうやって、2Dの図形を3Dにするのでしょうか？  
祖実はその作業が**プル**なのです。

プルとは、「**2Dスケッチに厚みを持たせて3Dの物体を作成する**」ことを言います。  
今から、その方法を紹介していきます。

今から行う「プル」という作業なのですが、主に3種類に分けることができます。  
1. 2Dスケッチを平行移動して3D物体を生成
2. ２Dスケッチを回転移動して3D物体を生成
3. すでにある物体の一部をプルして物体の形を変える

今回は、この３本柱でプルの説明を行います。では、スタート！  
  
  
### 2Dスケッチを平行移動して3D物体を生成

これが一番簡単なプル操作です。

まず、↓のサイトをもとに2Dスケッチを行い、**面を作成**します。  
[shizenkarasuzon.hatenablog.com](https://shizenkarasuzon.hatenablog.com/entry/2019/01/03/184957)

試しに正方形を書いてみました。こんな感じ。  
![f:id:pythonjacascript:20190103195907j:plain](/images/ppythonjacascript2019010320190103195907.jpg "f:id:pythonjacascript:20190103195907j:plain")

次に、「**デザイン**」タブの**「プル」**をクリックします。  
![f:id:pythonjacascript:20181210021542j:plain:h100](/images/ppythonjacascript2018121020181210021542.jpg "f:id:pythonjacascript:20181210021542j:plain:h100")

  
その状態で、先ほど作成した直方形の面上にマウスを置くと、長方形の色が**黄色に変化**します。  
![f:id:pythonjacascript:20181210021705j:plain:h300](/images/ppythonjacascript2018121020181210021705.jpg "f:id:pythonjacascript:20181210021705j:plain:h300")

  
そして、**面の色が黄色の状態の時に**、マウスを左クリックし、そのまま**上にドラッグ**します。

すると、このように長方形が直方体に変化します！！  
![f:id:pythonjacascript:20181210021843j:plain](/images/ppythonjacascript2018121020181210021843.jpg "f:id:pythonjacascript:20181210021843j:plain")

  
正確には、長方形が通った**軌跡（領域）**を物体として生成しています。  
2Dの長方形を平行移動して3D物体を作成することができました。  
  
  
### ２Dスケッチを回転移動して3D物体を生成

1の平行移動のプルを少しだけ難しくしたのが回転移動で作成するプルです。  
ここでは、直方体ではなく**円柱を作成**します。

  
最初は上の1と同じです。長方形を作成し、「**プル**」アイコンを押します。  
すると、下の写真のように画面左上に数個のアイコンが現れます。  
![f:id:pythonjacascript:20190103201111j:plain](/images/ppythonjacascript2019010320190103201111.jpg "f:id:pythonjacascript:20190103201111j:plain")

  
ここで、![f:id:pythonjacascript:20190103201224j:plain:h50](/images/ppythonjacascript2019010320190103201224.jpg "f:id:pythonjacascript:20190103201224j:plain:h50")**アイコン（上写真の赤矢印）をクリック**します。

  
すると、アイコンが大きく表示されるので、次に**長方形の辺を一つ選択**して下さい。  
これが、**プルするときの回転軸**となります。

辺クリックすると、下の画像のようにその辺が青くなります。  
![f:id:pythonjacascript:20190103201510j:plain](/images/ppythonjacascript2019010320190103201510.jpg "f:id:pythonjacascript:20190103201510j:plain")

  
この状態で長方形の面を選択し、ドラッグしてみてください。  
![f:id:pythonjacascript:20190103201556j:plain](/images/ppythonjacascript2019010320190103201556.jpg "f:id:pythonjacascript:20190103201556j:plain")  
先ほど選択した辺を中心として直方体が回転し、その領域が物体になっています。  
これが、回転によるプルです。

ドラッグしていると下のようにボックスが表示されるので（下写真の赤矢印）  
![f:id:pythonjacascript:20190103201855j:plain](/images/ppythonjacascript2019010320190103201855.jpg "f:id:pythonjacascript:20190103201855j:plain")  
そこに数値を入力すると、**回転角を指定**することができます。

「360」を入力すると長方形が1回転するので円柱形を作成することができます。  
![f:id:pythonjacascript:20190103202201j:plain](/images/ppythonjacascript2019010320190103202201.jpg "f:id:pythonjacascript:20190103202201j:plain")  
  
#### 回転によるプル（応用編）

この回転によるプルは応用範囲が広く、少し工夫するとこのような図形も作ることができます。  
![f:id:pythonjacascript:20190103202634j:plain](/images/ppythonjacascript2019010320190103202634.jpg "f:id:pythonjacascript:20190103202634j:plain")

円錐形の穴が開いたものに、先のとがった部分が削れた形です。

  
上の図形の作り方は、こうです。  
まず、長方形と、斜めの直線を書きます。  
![f:id:pythonjacascript:20190103202746j:plain](/images/ppythonjacascript2019010320190103202746.jpg "f:id:pythonjacascript:20190103202746j:plain")

  
そして、上で説明したように「プル」→「![f:id:pythonjacascript:20190103201224j:plain:h50](/images/ppythonjacascript2019010320190103201224.jpg "f:id:pythonjacascript:20190103201224j:plain:h50")**アイコン」をクリック**します。  
次に、回転軸を選択するのですが、長方形の辺ではなく、もう一つの直線を選択します。  
![f:id:pythonjacascript:20190103202926j:plain](/images/ppythonjacascript2019010320190103202926.jpg "f:id:pythonjacascript:20190103202926j:plain")

  
最後に、長方形を選択してドラッグします。  
![f:id:pythonjacascript:20190103203029j:plain](/images/ppythonjacascript2019010320190103203029.jpg "f:id:pythonjacascript:20190103203029j:plain")  
回転軸が長方形に対して斜めになっているので、長方形も斜めにプルされます。

  
その結果、このような図形ができるのです。  
![f:id:pythonjacascript:20190103202634j:plain](/images/ppythonjacascript2019010320190103202634.jpg "f:id:pythonjacascript:20190103202634j:plain")  
  
  
### 物体の一部をプルして変形する（面取り編）

例えば、直方体があるとします。  
![f:id:pythonjacascript:20190103203753j:plain](/images/ppythonjacascript2019010320190103203753.jpg "f:id:pythonjacascript:20190103203753j:plain")

この直方体の一つの辺を「**面取り**」してみましょう。

> 面取りとは、角部を削り角面や丸面などの形状に加工する工法である

(Wikipediaより）

  
まず「**デザイン**」タブの**「プル」**をクリックします。

そして、製作した直方体の**辺上にマウス**を置きます。

![f:id:pythonjacascript:20181210022208j:plain:h300](/images/ppythonjacascript2018121020181210022208.jpg "f:id:pythonjacascript:20181210022208j:plain:h300")  
すると、↑の写真のように**辺の色が黄色に変化**します。

この状態で、マウスを**左クリック**して、**物体中心方向（上の写真の場合は左下）にドラッグ**してみてください。  
![f:id:pythonjacascript:20181210022622j:plain](/images/ppythonjacascript2018121020181210022622.jpg "f:id:pythonjacascript:20181210022622j:plain")

このように、面取りができ、辺に丸みがでたと思います。

この面取り作業を上部の4つの辺に行うと、このようになります。  
![f:id:pythonjacascript:20181210022750j:plain](/images/ppythonjacascript2018121020181210022750.jpg "f:id:pythonjacascript:20181210022750j:plain")

**完成です！！**

### 物体の一部をプルして変形する（穴あけ編）

今度は、直方体に穴をあけてみましょう。

直方体があります。  
![f:id:pythonjacascript:20190103203753j:plain](/images/ppythonjacascript2019010320190103203753.jpg "f:id:pythonjacascript:20190103203753j:plain")

ここで、一度**穴の形を設計する**ために**2Dスケッチに戻ります。**  
今回は、円形の穴を作成してみます。

なので、「スケッチタブ」の「円」アイコンをクリックしましょう。  
![f:id:pythonjacascript:20190103181511j:plain](/images/ppythonjacascript2019010320190103181511.jpg "f:id:pythonjacascript:20190103181511j:plain")

  
このまま、マウスを直方体の上に移動させると**（カーソルを動かすだけでクリックはしないよ！！）**...  
![f:id:pythonjacascript:20190103204125j:plain](/images/ppythonjacascript2019010320190103204125.jpg "f:id:pythonjacascript:20190103204125j:plain")  
このように、**カーソルを置いた面が黄色く**なって、その面に沿って**スケッチ面が表示**されているでしょうか？

カーソルをほかの面に動かすと、カーソルの動きに追従して黄色くなる面も変化するはずです。  
どれか一つ面を選んで、面上で左クリックしましょう。面の色が黄色からもとの緑色に戻ります。

そこで、円を書いてみてください。**スケッチ面上で適当にドラッグ**すればOKです。  
![f:id:pythonjacascript:20190103204639j:plain](/images/ppythonjacascript2019010320190103204639.jpg "f:id:pythonjacascript:20190103204639j:plain")  
円の描き方について詳しく知りたい場合は、[こちら](https://shizenkarasuzon.hatenablog.com/entry/2019/01/03/184957#%E5%86%86%E3%82%92%E6%9B%B8%E3%81%8F)をどうぞ。

では、ただいまより**プル作業に移ります。**  
プルアイコンをクリックします。  
![f:id:pythonjacascript:20190103204843j:plain](/images/ppythonjacascript2019010320190103204843.jpg "f:id:pythonjacascript:20190103204843j:plain")

  
そして、先ほど作成した**円の中をクリック**すると、**円がオレンジ色に変化**します。  
![f:id:pythonjacascript:20190103205206j:plain](/images/ppythonjacascript2019010320190103205206.jpg "f:id:pythonjacascript:20190103205206j:plain")

  
そして、そのまま選択した円を下方向にドラッグすると...  
![f:id:pythonjacascript:20190103205255j:plain](/images/ppythonjacascript2019010320190103205255.jpg "f:id:pythonjacascript:20190103205255j:plain")  
**なんということでしょう。**  
2Dスケッチの円がプルされて、直方体が円の形に削れていくのです。

この操作を複数の円に行うと、このような図形を作成できます。  
![f:id:pythonjacascript:20190103205402j:plain](/images/ppythonjacascript2019010320190103205402.jpg "f:id:pythonjacascript:20190103205402j:plain")