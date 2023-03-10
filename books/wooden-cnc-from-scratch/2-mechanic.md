---
title: "【木製CNC】機械部分の製作"
emoji: ""
type: ""
topics: []
published: false
---

木製CNC作成シリーズです。今回は、ハードウェアの製作について書いていきます。

* [機械部分の構造](#機械部分の構造)
* [1.設計](#1設計)
* [2.スピンドル部分の製作](#2スピンドル部分の製作)
* [3.スピンドルモーター用の電源](#3スピンドルモーター用の電源)
* [4.X軸、Y軸、Z軸の可動部の製作](#4X軸Y軸Z軸の可動部の製作)
* [調整](#調整)

### 機械部分の構造

CNCの機械的な構造はこのようになっています。

![f:id:pythonjacascript:20181107214919j:plain](/images/ppythonjacascript2018110720181107214919.jpg "f:id:pythonjacascript:20181107214919j:plain")

CNCは、**X軸方向、Y軸方向、Z軸方向に水平移動する3つの可動部**と、エンドミルを回転させるための**スピンドル部**の4つの部品で構成されています。  
3 つの可動部がそれぞれ、x軸方向、y軸方向、z軸方向に一定量移動することで、ワークとエンドミルの位置を制御しながら、DCモーターでエンドミルを回転させ、ワークを切削し、特定の形を作り出すのです。

各可動部には**ステッピングモーター**（Stepper Motor）がついていて、それがそれぞれの可動部を動かしています。例えば、Z軸のステッピングモーターはＺ軸の可動部（上の写真の赤い部分）を上下に動かします。

実際に組み立てると、このようになります。  
[![/images/ppythonjacascript2018081320180813190535.png](/images/ppythonjacascript2018081320180813190535.png)](/images/ppythonjacascript2018081320180813190535.png)

この写真は、自作したCNCを正面から撮ったものです。
  
  
それぞれの可動部は下のような形をしています。  
[![/images/ppythonjacascript2018081320180813190413.png](/images/ppythonjacascript2018081320180813190413.png)](/images/ppythonjacascript2018081320180813190413.png)  
それぞれの可動部は左右2本のアルミ棒によって固定されており、アルミ棒に沿って一方向にのみスライドするようになっています。  
ステッピングモーターによって全ねじが回されると、可動部が動きます。このような可動部が、Ｘ軸、Ｙ軸、Ｚ軸の3つあるため、3次元で立体図形を作ることができるのです。  
  
  
### 1.設計

JWCADという2DのCADソフトウェアで設計を行いました。  
（ダウンロード元：[Jw\_cadのページ](http://www.jwcad.net/)）

![f:id:pythonjacascript:20181107213059j:plain](/images/ppythonjacascript2018110720181107213059.jpg "f:id:pythonjacascript:20181107213059j:plain")  
設計は全くの初心者です...。

このように、まず全体像を設計して、その後、一つ一つの部品の設計に移りました。  
![f:id:pythonjacascript:20181107213109j:plain](/images/ppythonjacascript2018110720181107213109.jpg "f:id:pythonjacascript:20181107213109j:plain")  
ほぼすべての部品をネジで固定する予定なので、ネジ同士が干渉しないようにネジを配置するのが大変でした。

そして、それらの部品の設計図を1/1で印刷して木材に張り付け、その輪郭通りに切るという方法で部品を切り出しました。  
  
  
### 2.スピンドル部分の製作

「**スピンドル**」とは、工具（エンドミル）をつけて材料を削るための回転する軸のことです。「主軸」と書かれていることもあるようです。

スピンドルの製作において求められることは、何よりも精度です。スピンドル部は高速で回転する部分であり、センターをきっちり出して回転軸がゆがまないようにしないといけません。今回は、このようにベアリングを２個使って固定しています。因みにですが、ベアリングは内径8ｍｍ、外形22ｍｍのやつを使用しています。  
[![/images/ppythonjacascript2018081320180813191029.jpg](/images/ppythonjacascript2018081320180813191029.jpg)](/images/ppythonjacascript2018081320180813191029.jpg)

実際にベアリングを挿入したときの写真です。  
![f:id:pythonjacascript:20181107213134j:plain](/images/ppythonjacascript2018110720181107213134.jpg "f:id:pythonjacascript:20181107213134j:plain")

  
エンドミルを回すモーターは**RS-540**を使っています。もともとはラジコン用のモーターのため、長時間使用を目的として作られていないので、これは後から分かったことですが、30分でも動かし続けると、触れないくらい熱くなります。一度モーターを固定している木に焦げ目がついたことがあるレベルです。

適正電圧（？）の7.2Ｖで駆動させたら、すぐ寿命が尽きそうなので、少し電圧を下げて約5Ｖで動作させています。

[![/images/ppythonjacascript2018081320180813191155.png](/images/ppythonjacascript2018081320180813191155.png)](/images/ppythonjacascript2018081320180813191155.png)

  
スピンドル部分は完成すると、このような形になりました。  
![f:id:pythonjacascript:20181107213402j:plain:h350](/images/ppythonjacascript2018110720181107213402.jpg "f:id:pythonjacascript:20181107213402j:plain:h350")
  
  
### 3.スピンドルモーター用の電源

スピンドル用の電源はデスクトップパソコンの電源として使われる、「ATX電源ユニット」を使用しています。  
  
  
### 4.X軸、Y軸、Z軸の可動部の製作

ほとんどの部分は木製でできていて、接着剤をほとんど使わずに、ねじで固定しながら組み立てていきます。  
穴あけは電気ドリルやボール盤で、木材の切断はのこぎりや糸鋸を使っています。

特に説明することがないので、写真だけ何枚か載せておきます。

Z軸の可動部です。  
![f:id:pythonjacascript:20181107215822j:plain](/images/ppythonjacascript2018110720181107215822.jpg "f:id:pythonjacascript:20181107215822j:plain")

X軸の可動部の製作中の写真です。  
![f:id:pythonjacascript:20181107220019j:plain](/images/ppythonjacascript2018110720181107220019.jpg "f:id:pythonjacascript:20181107220019j:plain")  
写真中央の正方形の板が左右（X軸方向）に移動します。この上にワークを載せて切削させるつもりです。

可動部のみ仮組してみました。それぞれの軸の可動部がスムーズに動くことを確認します。  
![f:id:pythonjacascript:20181107220309j:plain](/images/ppythonjacascript2018110720181107220309.jpg "f:id:pythonjacascript:20181107220309j:plain")  
  
  
### 調整

このように組み立てが完成したら、それぞれの軸が直交するように調整します。  
この調整を行うか行わないかで、**切削したものの精度が全く異なってきます。**
  
  
**次回**  
[shizenkarasuzon.hatenablog.com](https://shizenkarasuzon.hatenablog.com/entry/2018/11/09/003811)