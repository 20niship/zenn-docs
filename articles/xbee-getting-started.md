---
title: "【ZigBee】NO.2　ソフトウェアのインストール"
emoji: "🤖"
type: "tech"
topics: []
published: false
---

ZigBeeを使うにはソフトウェアを二つインストールしないといけません。➀ＰＣとXBeeを通信するためのソフトウェアと、②XBeeの設定を行うためのソフトウェアです。そこで、今回はその二つのソフトのダウンロード・インストールの方法を解説します。

* [0.実行環境](#0実行環境)
* [1.USB to UART Drivers](#1USB-to-UART-Drivers)
* [2.X-CTU](#2X-CTU)

### 0.実行環境

私は、次のような環境で、このブログの記事の実験を行っています。特にUSB-シリアル変換のソフトなどは機種によってダウンろーおするものが変わってくるので、実行するときは自己責任でお願いします。  
PC : Windows 10 Home  
 64 bit Operationg System  
XBee : XBee PRO S2B  
USB－シリアル変換基盤：CQ出版の本の付属[\*1](#f-5ea84f5c "「超お手軽　無線モジュールXBee』")のもの  
（この基盤はシリアル-USB変換ICにCP2104を使用しているため、それにあったドライバを入れました。）  
  
### 1.USB to UART Drivers

PCとXBeeを通信するためのソフトウェアです。

**ダウンロード・インストール方法：**

（1）下記サイトにアクセスします。  
![f:id:pythonjacascript:20180703005911j:plain](/images/ppythonjacascript2018070320180703005911.jpg "f:id:pythonjacascript:20180703005911j:plain")[www.silabs.com](https://www.silabs.com/products/development-tools/software/usb-to-uart-bridge-vcp-drivers)

（2）「**Download for Windows 10 Universal (v10.1.3)**」のところから、「**Download VCP (2.3 MB)** 」を、ダウンロード  
![f:id:pythonjacascript:20180703010148j:plain](/images/ppythonjacascript2018070320180703010148.jpg "f:id:pythonjacascript:20180703010148j:plain")

（3）あとは、適当にOKとかAgreeとか押してれば何とかなる  
  
### 2.X-CTU

X-CTUはXBeeを設定するためのソフトウェアです

**ダウンロード・インストール方法：**

（1）下記サイトにアクセスします  
[XCTU Software Product Detail - Digi International](https://www.digi.com/support/productdetail?pid=3352)

  
（2）「Diagnostics, Utilities and MIBs」の「DOWNLOAD XCTU」の「XCTU v. 6.4.0 windows x86/64」をダウンロード  
![f:id:pythonjacascript:20180703005917j:plain](/images/ppythonjacascript2018070320180703005917.jpg "f:id:pythonjacascript:20180703005917j:plain")

（3）インストールする  
![f:id:pythonjacascript:20180703005925j:plain](/images/ppythonjacascript2018070320180703005925.jpg "f:id:pythonjacascript:20180703005925j:plain")

![f:id:pythonjacascript:20180703005928j:plain](/images/ppythonjacascript2018070320180703005928.jpg "f:id:pythonjacascript:20180703005928j:plain")  
「”Digi International Inc."からのソフトウェアを常に信頼しますか？」のチェックボックスをONにして、「インストール」をクリック

（4）完了  
![f:id:pythonjacascript:20180703005932j:plain](/images/ppythonjacascript2018070320180703005932.jpg "f:id:pythonjacascript:20180703005932j:plain")
  
  
以上です、お疲れさまでした。

[\*1](#fn-5ea84f5c):「超お手軽 無線モジュールXBee』