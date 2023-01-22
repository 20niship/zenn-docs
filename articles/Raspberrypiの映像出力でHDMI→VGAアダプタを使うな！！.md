---
title: "Raspberry piの映像出力でHDMI→VGAアダプタを使うな！！"
emoji: "🤖"
type: "tech"
topics: []
published: false
---

* [プロローグ](#プロローグ)
* [原因](#原因)
* [解決策](#解決策)  
   * [Gert's VGA 666 adapter](#Gerts-VGA-666-adapter)
* [参考文献](#参考文献)

### プロローグ

**Raspberry pi zero W**を先日購入しました。  
![f:id:pythonjacascript:20190125080302j:plain](/images/ppythonjacascript2019012520190125080302.jpg "f:id:pythonjacascript:20190125080302j:plain")

Raspberry piの映像はmini-HDMI端子から出ているので、以下のようなHDMI→VGAアダプタを使って映像出力を行っていました。  
[shizenkarasuzon.hatenablog.com](https://shizenkarasuzon.hatenablog.com/entry/2019/01/19/232741)

  
最初の数日は順調に起動して動作確認も終了しました。

  
ですが、ある日。いつも通りRaspberry piを起動してみました。

（一瞬チップLEDが緑に点灯）  
...

約0.1秒後：**（LED消灯）**

**...んん？？**

全く何も起きなくなりました。画面に何も表示されません。

どうやら、**microUSBの電源ケーブルを差し込んだ直後に死亡された模様...。**

  
調べてみると、Raspberry piのHDMI端子付近から煙が出てしまうこともあるのだとか。

> 業務連絡。Raspberry Pi 燃えた。HDMI 端子にHDMI-DVI 変換のケーブルでディスプレイに接続したら、端子横の抵抗から煙が。慌てて電源抜いたけど、HDMI 死亡。ググったらいくつもの先例があった。ぐやじいいいい アナログ端子からの映像はおっけ、無事起動。
> 
> — Yuji Ogihara (@rochefort\_68) [February 25, 2013](https://twitter.com/rochefort%5F68/status/305968363342020608?ref%5Fsrc=twsrc%5Etfw)
  
  
このような失敗談をしたので、その原因をまとめてみました。  
  
  
### 原因

結論から言います。  
**Raspberry piは、ほとんどのHDMI→VGAアダプタに対応していません。**

HDMIからVGAに映像処理を行うアダプタの場合、典型的には映像変換時に電力を使用します。

そして、その電気をRaspberry piから得ようと設計されているものが多いのです。

そのため、Raspberry pi側のHDMI電源が問題を引き起こしてしまいます。

  
Raspberry Piの新しいモデル（Pi 2、A+、またはB+）は、 HDMI 5Vの出力電力機能が改善されているため、問題ありません 。  
しかし、古いAまたはBモデルは、アダプタ内のHDMI→VGAコンバーターが正常に動作するための十分な電力を供給できないことがあります。Raspberry pi zero WHも、この問題は改善されていないようです。  
  
> If your monitor only has a VGA Connector you need to buy a cheap HDMI to VGA adapter. It is not possible to buy a cheap HDMI->VGA Cable. If you see a cable that is VGA on one end and HDMI on the other it will not work.
> 
> （中略）
> 
> If you have one of the newer models of the Pi (Pi 2, A+ or B+) which have improved HDMI 5V output power capabilities, you should have no further problems. If you are using the older A or B models, though, please note that some cheaper converters attempt to be powered by the Pi: the older models may not be able to provide sufficient power for these converters to work successfully. 

### 解決策

よって、**素直にHDMI端子を持ったディスプレイを使用しましょう。**

  
というのは、一つの解決策でしょう。  
しかし、Raspberry piのために安くても10,000円くらいするディスプレイを購入するのは、**「＃本当に、なんだかなぁだよね」**という方のために、もう一つの解決策を紹介しましょう。  
  
  
#### Gert's VGA 666 adapter

「**Gert's VGA 666 adapter**」というものがあります。  
[Gert VGA 666 - Hardware VGA for Raspberry Pi – Pi Supply](https://uk.pi-supply.com/products/gert-vga-666-hardware-vga-raspberry-pi?lang=ja)

[![https://cdn.shopify.com/s/files/1/2187/3161/products/9e0e0e8cb8136f5ff02c3b584e75283a_1024x.png?v=1520443348](https://cdn.shopify.com/s/files/1/2187/3161/products/9e0e0e8cb8136f5ff02c3b584e75283a_1024x.png?v=1520443348)](https://cdn.shopify.com/s/files/1/2187/3161/products/9e0e0e8cb8136f5ff02c3b584e75283a%5F1024x.png?v=1520443348)

これは、Raspberry piに搭載された40ピンのGPIOピンを利用して、VGAビデオ出力を生成するアダプターです。  
**Raspberry pi 2、B+、zeroに対応**しています。

「**Gert's VGA 666 adapter**」は40ピンすべてのGPIOを使用するため、他のインターフェースとの接続が制限されます。  
また、Gert's VGA 666 adapterを使ってVGA出力をする場合、特別なドライバーが必要になります（Raspbianの新しいバージョンに実装予定）  
  
### 参考文献

[STICKY: Pictorial Buying Guide for the Raspberry Pi - Raspberry Pi Forums](https://www.raspberrypi.org/forums/viewtopic.php?f=91&t=83446)