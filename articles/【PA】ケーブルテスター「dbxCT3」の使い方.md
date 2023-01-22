---
title: "【PA】ケーブルテスター「dbx CT3」の使い方"
emoji: "🤖"
type: "tech"
topics: []
published: false
---

dbx製の音響用ケーブルのテスター「**CT3**」を使ってみました

[www.soundhouse.co.jp](https://www.soundhouse.co.jp/products/detail/item/229725/)

* [1.外観とか詳細](#1外観とか詳細)
* [2.準備](#2準備)
* [3.使ってみる](#3使ってみる)
* [注意：](#注意)
* [PDF](#PDF)

### 1.外観とか詳細

まず、CT3の外観は下の写真のようになっています。  
![f:id:pythonjacascript:20190722164016j:plain](/images/ppythonjacascript2019072220190722164016.jpg "f:id:pythonjacascript:20190722164016j:plain")  

> 寸法(W×H×D)：129×46×180mm(除突起部)  
> 質量：1.3kg

  
ボックスの左右の側面にケーブルを差し込む端子があります。左右両端にケーブルを差し込むことで、そのケーブルの何番ピンが断線しているのかまで調べることができます。  
![f:id:pythonjacascript:20190722165532j:plain](/images/ppythonjacascript2019072220190722165532.jpg "f:id:pythonjacascript:20190722165532j:plain")  
テストできる端子  
・XLR  
・フォン  
・スピコン  
・バナナ  
・DMX  
・DIN  
・BNC  
・RJ45  
・RJ11
  
  
右半分が送信（SEND）ユニット、左半分が受信（RECEIVE）ユニットとなっています。仕組みは簡単で、SEND側から信号が送られそれがテストするケーブルを伝わってRECEIVE側で受信できたら、そのケーブルは正常である、というものです。ケーブルのそれぞれのピンが**「正常」「断線」「クロス」「ショート」**のどの状態なのかを視覚的に判断できます。

ちなみに、この左右のユニットは下の土台でつながっているだけなので、分割することができます。  
  
  
### 2.準備

CT3は、9V電池2個で駆動します。下の写真のようにそれぞれのユニットに予め電池を入れておきます。  
![f:id:pythonjacascript:20190722164301j:plain](/images/ppythonjacascript2019072220190722164301.jpg "f:id:pythonjacascript:20190722164301j:plain")

  
そして、電源スイッチをONにすると、POWERランプが赤く点灯します。  
![f:id:pythonjacascript:20190722164433j:plain](/images/ppythonjacascript2019072220190722164433.jpg "f:id:pythonjacascript:20190722164433j:plain")  
左側の「TEST TONE」等が書かれたスイッチは「**OFF**（真ん中）」にセットしておいてください。  
  
  
### 3.使ってみる

適当なケーブルの両端を、それぞれのユニットの端子差込口にさしてください

  
ケーブルが断線していない場合は、下の動画のようにランプが点灯するはずです。  
![f:id:pythonjacascript:20190722164523g:plain](/images/ppythonjacascript2019072220190722164523.gif "f:id:pythonjacascript:20190722164523g:plain")  
上の動画は4極のスピコンでテストした時のものです。

  
XLRはピンが3つあるので、それに対応するLEDが点灯します。  
![f:id:pythonjacascript:20190722164513g:plain](/images/ppythonjacascript2019072220190722164513.gif "f:id:pythonjacascript:20190722164513g:plain")

もし、点灯していないLEDがあれば、そのピンが内部断線しているということです。点灯している順番が違っていれば、そのピンの配線が入れ替わっていることがわかります。

  
点灯の周波数（スピード）は、SENDユニット（右側）の「**SPEED**」と書かれた半固定抵抗を回すことで変更できます。  
![f:id:pythonjacascript:20190722164645j:plain](/images/ppythonjacascript2019072220190722164645.jpg "f:id:pythonjacascript:20190722164645j:plain")

  
### 注意：

**ファントム（phantom）電源を付けた状態でケーブルをささないこと！**

  
### PDF

取扱説明書のPDFがネットに上がっています（English）  
<https://3e7777c294b9bcaa5486-bc95634e606bab3d0a267a5a7901c44d.ssl.cf2.rackcdn.com/product%5Fdocuments/documents/3831%5F1461164795/CT-3%5FManual%5FRev%5FA%5Foriginal.pdf>