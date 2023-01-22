---
title: "【Design Spark Mechanical】NO.1 使い方編"
emoji: ""
type: ""
topics: []
published: false
---

無料の3DCADとして、**Design Spark Mechanical**（以降、頭文字をとって**DSM**と略します）というソフトウェアがあります。  
![f:id:pythonjacascript:20181208181457j:plain](/images/ppythonjacascript2018120820181208181457.jpg "f:id:pythonjacascript:20181208181457j:plain")

今回は、このソフトウェアの使い方の基礎の基礎の基礎の基s.....を紹介します。

失礼しました。午前3時にこの記事を書いており、深夜テンションの為~~たまに~~よく文章がおかしくなっています。

気を取り直して、この記事ではDesign Spark Mechanicalの基礎について紹介します！

このソフトを入れてないという方は、こちらのサイトをもとにインストールを！  
[shizenkarasuzon.hatenablog.com](https://shizenkarasuzon.hatenablog.com/entry/2018/12/09/142624)  

* [この記事の目標](#この記事の目標)
* [ファイルの作成・保存](#ファイルの作成保存)
* [作業の進めかた](#作業の進めかた)
* [STEP1\. 2Dスケッチ](#STEP1-2Dスケッチ)
* [STEP2\. プルして厚みを持たせる](#STEP2-プルして厚みを持たせる)
* [STEP3\. 面取りなど](#STEP3-面取りなど)
* [まとめ](#まとめ)

### この記事の目標

![f:id:pythonjacascript:20181210022750j:plain](/images/ppythonjacascript2018121020181210022750.jpg "f:id:pythonjacascript:20181210022750j:plain")  
こんな直方体を作成します。  
  
### ファイルの作成・保存

まず、DSMを起動します。すると、このような画面が現れます。  
![f:id:pythonjacascript:20181209142227j:plain:h300](/images/ppythonjacascript2018120920181209142227.jpg "f:id:pythonjacascript:20181209142227j:plain:h300")

では、作業開始です。

まず、「**新規デザイン**」を作成します。今から、ここで作成するデザインファイルの中で物体を作成・編集することになります。

  
**「ファイル」→「新規作成」→「デザイン」**をクリックします。  
![f:id:pythonjacascript:20181210015941j:plain:h300](/images/ppythonjacascript2018121020181210015941.jpg "f:id:pythonjacascript:20181210015941j:plain:h300")

すると、このような画面に変化します。  
![f:id:pythonjacascript:20181210020018j:plain](/images/ppythonjacascript2018121020181210020018.jpg "f:id:pythonjacascript:20181210020018j:plain")  
これが作業画面です。このウィンドウ上に3D図形を書いていきます。

そして、作業が終わったら**「ファイル」→「名前を付けて保存」**をクリックして、適切な場所にデータを保存します。

  
保存したファイルの拡張子は「**.rsdoc**」です。  
  
### 作業の進めかた

今から**3D物体**を**バンバン作成**していくのですが、それには一定の手順があります。

なので、最初だけはこのページに沿って物体を作ってください。

その手順とは、以下の通り。 
**1. 2Dスケッチ**
**2. プルして厚みを持たせる**
**3. 面取りなど**

**この手順、あたりまえだけど、個人的には結構重要な気がします。**

**では、「STEP1 2Dスケッチ」から行います。** 
  
### STEP1\. 2Dスケッチ

この工程では、作りたい3Dオブジェクトの**断面図**を**2D**で書きます。  
「3DCADなのになんで2Dなんやねん？」ってなるかもしれませんが、そこは焦らずにやりましょう。

今回は直方体を製作するので、**長方形**を描きます。

下の写真のように「**デザイン**」タブの**四角形のアイコン**をクリックします。  
![f:id:pythonjacascript:20181210020743j:plain:h100](/images/ppythonjacascript2018121020181210020743.jpg "f:id:pythonjacascript:20181210020743j:plain:h100")

そして、そのままマウスを中央の網目状の部分に持っていき、**ドラッグ**させると...  
![f:id:pythonjacascript:20181210020754j:plain:h300](/images/ppythonjacascript2018121020181210020754.jpg "f:id:pythonjacascript:20181210020754j:plain:h300")  
このように、四角形を書くことができます。  
  
### STEP2\. プルして厚みを持たせる

STEP2では、「**プル**」という処理を行い、STEP1で作成した**2D図形を3Dに**します！

ここが3DCADの最も楽しい作業です。

今回は、STEP1で作成した長方形を直方体にします。

下の写真のように「**デザイン**」タブの**「プル」**をクリックします。  
![f:id:pythonjacascript:20181210021542j:plain:h100](/images/ppythonjacascript2018121020181210021542.jpg "f:id:pythonjacascript:20181210021542j:plain:h100")

その状態で、先ほど作成した直方形の面上にマウスを置くと、長方形の色が**黄色に変化**します。  
![f:id:pythonjacascript:20181210021705j:plain:h300](/images/ppythonjacascript2018121020181210021705.jpg "f:id:pythonjacascript:20181210021705j:plain:h300")

そして、**面の色が黄色の状態の時に**、マウスを左クリックし、そのまま**上にドラッグ**します。

すると、このように長方形が直方体に変化します！！  
![f:id:pythonjacascript:20181210021843j:plain](/images/ppythonjacascript2018121020181210021843.jpg "f:id:pythonjacascript:20181210021843j:plain")

正確には、長方形が通った**軌跡（領域）**を物体として生成しています。

このようにして、直方体を作成することができました。  
  
### STEP3\. 面取りなど

最後に、もし必要ならば「**面取り**」作業を行うことができます。

> 面取りとは、角部を削り角面や丸面などの形状に加工する工法である

(Wikipediaより）

  
面取りを行うには、まず「**デザイン**」タブの**「プル」**をクリックします。

そして、製作した直方体の**辺上にマウス**を置きます。

![f:id:pythonjacascript:20181210022208j:plain:h300](/images/ppythonjacascript2018121020181210022208.jpg "f:id:pythonjacascript:20181210022208j:plain:h300")  
すると、↑の写真のように**辺の色が黄色に変化**します。

この状態で、マウスを**左クリック**して、**物体中心方向（上の写真の場合は左下）にドラッグ**してみてください。  
![f:id:pythonjacascript:20181210022622j:plain](/images/ppythonjacascript2018121020181210022622.jpg "f:id:pythonjacascript:20181210022622j:plain")

このように、面取りができ、辺に丸みがでたと思います。

この面取り作業を上部の4つの辺に行うと、このようになります。  
![f:id:pythonjacascript:20181210022750j:plain](/images/ppythonjacascript2018121020181210022750.jpg "f:id:pythonjacascript:20181210022750j:plain")

**完成です！！**

上書き保存をお忘れなく。  
  
### まとめ

DSMでは、

1. 2Dスケッチを描く
2. 「プル」で2Dスケッチを3Dの物体に
3. 面取りをして物体を美しく（？）

の**3STEP**で3Dの物体を作成できます。

物体の形状が複雑になればそれらの作業は複雑化しますが、やっていることはどれも同じです。

お疲れさまでした。