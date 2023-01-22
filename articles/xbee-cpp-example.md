---
title: "【ZigBee】NO.1　ZigBeeを使ってみる。"
emoji: ""
type: ""
topics: []
published: false
---

先日、ドローン用の電子部品一式を注文しました...。もう後戻りはできない。やってやろうじゃないか！  
ということで、今回は、例の無線モジュール、**XBee**を使っていきます！！

* [1.用意するもの](#1用意するもの)  
   * [➀無線モジュール、ZigBee ２個](#無線モジュールZigBee２個)  
   * [②XBee-USBシリアル変換基盤、ケーブル](#XBee-USBシリアル変換基盤ケーブル)  
   * [③PCおよびUSBドライバソフト](#PCおよびUSBドライバソフト)  
   * [④2ｍｍ-2.54ｍｍのピッチ変換基盤](#2ｍｍ-254ｍｍのピッチ変換基盤)  
   * [⑤X-CTU](#X-CTU)
* [2.XBeeの簡単な設定](#2XBeeの簡単な設定)  
   * [（1）XBee-USB変換基盤に、XBeeを付ける。](#1XBee-USB変換基盤にXBeeを付ける)  
   * [（2）XBee-USB変換基盤をPCに接続](#2XBee-USB変換基盤をPCに接続)  
   * [（3）X-CTUを起動](#3X-CTUを起動)  
   * [（4）X-CTUにXBeeを認識させる](#4X-CTUにXBeeを認識させる)
* [3.xBeeの動作確認](#3xBeeの動作確認)  
   * [（1）以下のような回路図を作ります。](#1以下のような回路図を作ります)  
   * [（2）上の方法で、PCにｘBeeを認識させます。](#2上の方法でPCにｘBeeを認識させます)  
   * [（3）テスト開始!!](#3テスト開始)

### 1.用意するもの

以下のものが絶対必要

#### ➀無線モジュール、ZigBee ２個

XBeeはパーツショップでも売られていて、安価に購入できる、無線の定番モジュール。ZigBee を２個用意するだけで、簡単な簡単に、すぐに無線技術が体験できる  
![f:id:pythonjacascript:20180703003118j:plain:h300](/images/ppythonjacascript2018070320180703003118.jpg "f:id:pythonjacascript:20180703003118j:plain:h300")

（Fig.1 今回の実験で使用するXBee PRO シリーズ2）

#### ②XBee-USBシリアル変換基盤、ケーブル

XBeeとPCをつなぐための基盤、ケーブル。（XBeeはPCで設定する必要があります）  
![f:id:pythonjacascript:20180703003249j:plain:h300](/images/ppythonjacascript2018070320180703003249.jpg "f:id:pythonjacascript:20180703003249j:plain:h300")

#### ③PCおよびUSBドライバソフト

②のUSB変換基盤のドライバーが必要です。今回私は、「CP2104」というUSB-シリアル変換ICを内蔵した基盤を使っているので、そのICにあったドライバをインストールしておきます。  
ここからダウンロードしました。  
[www.silabs.com](https://www.silabs.com/products/development-tools/software/usb-to-uart-bridge-vcp-drivers)  
ダウンロード、インストール方法はこちら：  
[shizenkarasuzon.hatenablog.com](https://shizenkarasuzon.hatenablog.com/entry/2018/07/03/011800)

#### ④2ｍｍ-2.54ｍｍのピッチ変換基盤

XBeeのピンのピッチは2ｍｍなので、それをブレッドボードにさせる2.54ｍｍピッチに変更する必要があります。そのために、変換基盤をブレッドボードとXBeeの間に取り付け、ピッチを変更します。  
![f:id:pythonjacascript:20180703003305j:plain:h300](/images/ppythonjacascript2018070320180703003305.jpg "f:id:pythonjacascript:20180703003305j:plain:h300")

#### ⑤X-CTU

X-CTUはXBeeの動作を設定するソフトウェアです。XBeeは買った状態では使用できず、初期設定を行う必要があります。  
ここからダウンロードします。  
[Product Support: XCTU Software | Digi International](https://www.digi.com/support/productdetail?pid=3352)  
インストール／ダウンロード方法はこちら  
[shizenkarasuzon.hatenablog.com](https://shizenkarasuzon.hatenablog.com/entry/2018/07/03/011800)

### 2.XBeeの簡単な設定

X-CTUにｘBeeを認識させます。簡単なテストを行っていきます。  
この記事の最終目標は、「PC上でX-CTUからｘBeeのピンの出力を変更できるようにする」です！！

では、早速作業を開始していきます。

#### （1）XBee-USB変換基盤に、XBeeを付ける。

見出しの通りです。変換基盤にｘBeeを取り付けますが、向きに注意してください。基盤上に白くXBeeの外形がプリントされているので、それに合わせてください。  
  
#### （2）XBee-USB変換基盤をPCに接続

上の作業（1）で作ったモジュールをPCにUSB接続します。  
  
#### （3）X-CTUを起動

私の場合は、デスクトップ上にアイコンができていたので、そこから起動しました。  
![f:id:pythonjacascript:20180703005932j:plain:h300](/images/ppythonjacascript2018070320180703005932.jpg "f:id:pythonjacascript:20180703005932j:plain:h300")

#### （4）X-CTUにXBeeを認識させる

以下のような手順で行いました。

1. 「Discover Devices」をクリック
2. 適切なCOMポートを選択し、「NEXT」をクリック
3. 「Set Port Parameter」の画面が出てきますが、そのまま「Finish」をクリック

![f:id:pythonjacascript:20180703225106j:plain:h300](/images/ppythonjacascript2018070320180703225106.jpg "f:id:pythonjacascript:20180703225106j:plain:h300")  
（写真：COMポートを選択中）

![f:id:pythonjacascript:20180703223620j:plain:h300](/images/ppythonjacascript2018070320180703223620.jpg "f:id:pythonjacascript:20180703223620j:plain:h300")  
（写真：xBeeを発見（discoverd device）した）

そうすると、この写真のように、XBeeをPCが認識してくれます。  
「**add selected devices**」をクリックすると、下の写真ような画面が表示されたでしょうか？  
![f:id:pythonjacascript:20180703223658j:plain:h300](/images/ppythonjacascript2018070320180703223658.jpg "f:id:pythonjacascript:20180703223658j:plain:h300")

  
### 3.xBeeの動作確認

X-CTUにｘBeeを認識していることを確かめるために、簡単なテストを行っていきます。  
この記事の最終目標は、「**PC上でX-CTUからｘBeeのピンの出力を変更できるようにする**」です！！

#### （1）以下のような回路図を作ります。

ｘBee の「D1O1」ピンと、「GND」ピンの間にLEDと抵抗を置きます。もちろん、D101ピン側がLEDのアノードです。  
いまから、PCを使ってこのLEDを点滅させます。もし点滅したら、PCとｘBeeの通信がうまくいっている、かつ、ｘBee  
が正常に動作している、ということです。  
![f:id:pythonjacascript:20180703224425j:plain:h300](/images/ppythonjacascript2018070320180703224425.jpg "f:id:pythonjacascript:20180703224425j:plain:h300")

#### （2）上の方法で、PCにｘBeeを認識させます。

このとき、（1）でｘBeeに接続したLEDが少し光っているかもしれませんが、問題ありません。  
  
#### （3）テスト開始!!

では、下の写真の「I/O Settings」の「D1」のところが、「Disable」になっていると思うので、これを、「Digital Out High」に変えてみてください。どうですか？LEDが点灯しましたか？  
![f:id:pythonjacascript:20180703223634j:plain](/images/ppythonjacascript2018070320180703223634.jpg "f:id:pythonjacascript:20180703223634j:plain")  
![f:id:pythonjacascript:20180703224424j:plain:h300](/images/ppythonjacascript2018070320180703224424.jpg "f:id:pythonjacascript:20180703224424j:plain:h300")

反対に「Digital Out Low」にすると、消灯したと思います。  
![f:id:pythonjacascript:20180703224200j:plain](/images/ppythonjacascript2018070320180703224200.jpg "f:id:pythonjacascript:20180703224200j:plain")  
![f:id:pythonjacascript:20180703224425j:plain:h300](/images/ppythonjacascript2018070320180703224425.jpg "f:id:pythonjacascript:20180703224425j:plain:h300")

これで、テストは完了です。次回は、実際にarduinoを使って通信させたいと思います。