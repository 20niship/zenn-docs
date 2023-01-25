---
title: "【木製CNC自作】自作したCNCの切削テスト！"
emoji: ""
type: ""
topics: []
published: false
---

CNCなどの工作機械を使って、材料（ワーク）を削り出して目的の形を作り上げることを「**切削**」と言います。

今回は、以下の記事のように自作したCNCを使って、切削テストを行ってみました。

[shizenkarasuzon.hatenablog.com](https://shizenkarasuzon.hatenablog.com/entry/2018/11/04/235423#%E6%9C%A8%E8%A3%BDCNC)

  
### テスト1 切削せずに絵を描く

本来ならば、スピンドル部分にはエンドミル（ドリルみたいなもの）を取り付けて、それで材料を削り出します。しかし、鋭利な刃物を、まともに動くか分からないようなCNCに取り付けるのは怖かったので、  
エンドミルの代わりに鉛筆を取り付けて、図形を書けるかを試してみました。  
  
#### STEP1\. JWCADで図形を描く

鉛筆を動かす軌道を命令するため、描きたい図形をまずパソコン上で書きます。  
**ドラえもん**を書いてみました。

![f:id:pythonjacascript:20181114182602j:plain](/images/ppythonjacascript2018111420181114182602.jpg "f:id:pythonjacascript:20181114182602j:plain")  
  
  
#### STEP2\. NCVCでGCODEに変換

JWCADで設計したドラえもんを、**GCODE**というCNC専用[\*1](#f-753f7560 "正確には3Dプリンターなどの工作機械に使われている。この記事では、CNCにおけるGCODEの利用方法について書いている。")のコードに変換します。変換ソフトは**NCVC**を使いました。  
JWCADのデータは直線や円の座標を羅列して図形を表していますが、GCODEはスピンドル部分を動かす方向や速度を指定しているコードです。

GCODEについてはこちらのページに詳しく書かれていました。  
[Make: Japan | CNCを知ろう：G-Codeの読み方](http://makezine.jp/blog/2017/01/get-to-know-your-cnc-how-to-read-g-code.html)

![f:id:pythonjacascript:20181114183047j:plain](/images/ppythonjacascript2018111420181114183047.jpg "f:id:pythonjacascript:20181114183047j:plain")  
  
  
#### STEP3\. CNCで描画！！

STEP2で作成したGCODEをもとにCNCを動かします。GCODEをもとにCNCに動作命令を送るソフトとして、**MACH3**を使用しています。  
![f:id:pythonjacascript:20181114183309j:plain](/images/ppythonjacascript2018111420181114183309.jpg "f:id:pythonjacascript:20181114183309j:plain")  
↑描画中...

完成したときの写真が↓です。  
![f:id:pythonjacascript:20181114183313j:plain](/images/ppythonjacascript2018111420181114183313.jpg "f:id:pythonjacascript:20181114183313j:plain")
  
  
### テスト② 木を円形に削る

テスト➀によって、CNCが正常動作することがわかりました。そこで、実際に切削してみたいと思います。

厚さ3ｍｍのバルサ板から直径31ｍｍの円柱を製作しました。

![f:id:pythonjacascript:20181114183806j:plain](/images/ppythonjacascript2018111420181114183806.jpg "f:id:pythonjacascript:20181114183806j:plain")  
使用したエンドミルです。歯の部分の直径は4ｍｍです。

  
![f:id:pythonjacascript:20181114183808j:plain](/images/ppythonjacascript2018111420181114183808.jpg "f:id:pythonjacascript:20181114183808j:plain")  
切削中...。

切削終了。PCでは直径31ｍｍになるように設計してGCODEを作成しました。  
![f:id:pythonjacascript:20181114183820j:plain](/images/ppythonjacascript2018111420181114183820.jpg "f:id:pythonjacascript:20181114183820j:plain")  
このように、実際のサイズもほぼ31ｍｍです。思ったよりも精度が高い！

因みに、切削の設定は以下のようにしています。

| 主軸回転数      | 約10,000rpm       |
| ---------- | ---------------- |
| X軸、Y軸送り    | 40ｍｍ/min         |
| Z軸送り       | 10ｍｍ/min         |
| R点         | 1ｍｍ              |
| 切込み（一回当たり） | \-0.3ｍｍ          |
| 最終切込み      | \--2.99ｍｍ（深彫を行う） |
  
  
### テスト③ 半球に削る

JWCADの設計図をちょっと工夫すると、このように立体的に削ることもできます。  
その方法は、下の写真のように深さごとに描画レイヤを変更することです。  
![f:id:pythonjacascript:20181114185244j:plain](/images/ppythonjacascript2018111420181114185244.jpg "f:id:pythonjacascript:20181114185244j:plain")  
（『NCVC解説書』 <ftp://s-gikan2.maizuru-ct.ac.jp/pub/NCVC.pdf> より）

そして、レイヤごとに切削の深さを指定することで、このように半球状に穴をあけることができます。  
![f:id:pythonjacascript:20181114185656j:plain](/images/ppythonjacascript2018111420181114185656.jpg "f:id:pythonjacascript:20181114185656j:plain")  

NCVCやJWCADの設定方法については、このページに詳しく載っています。  
[NCVC のページ - NC Viewer and Converter](http://s-gikan2.maizuru-ct.ac.jp/xcl/)  
  
  
### テスト④ いろいろな図形を切り出す。

他にも、様々な図形を切削させてみました。  
![f:id:pythonjacascript:20181114185758j:plain](/images/ppythonjacascript2018111420181114185758.jpg "f:id:pythonjacascript:20181114185758j:plain")  
発泡スチロールにト音記号の模様を削ってみました。

  
![f:id:pythonjacascript:20181114185751j:plain](/images/ppythonjacascript2018111420181114185751.jpg "f:id:pythonjacascript:20181114185751j:plain")  
このように、パソコンで設計した通りの図形を簡単に全自動で作り出すことができるのです。

[\*1](#fn-753f7560):正確には3Dプリンターなどの工作機械に使われている。この記事では、CNCにおけるGCODEの利用方法について書いている。