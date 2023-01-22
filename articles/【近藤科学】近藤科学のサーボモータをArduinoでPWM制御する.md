---
title: "【近藤科学】近藤科学のサーボモータを Arduino でPWM制御する"
emoji: ""
type: ""
topics: []
published: false
---

訳あって、近藤科学のサーボモータ「KRS-2552RHV ICS」を Arduino からPWM制御をしてみたくなりました。  
近藤科学のサーボモータは、初期設定では、ICSという近藤科学独自の通信方法を使って制御されているので、それをPWM制御に変える必要がありました。

* [1.目標](#1目標)
* [2.必要なもの](#2必要なもの)  
   * [➀サーボモータ「KRS-2552RHV ICS」](#サーボモータKRS-2552RHV-ICS)  
   * [②Arduino](#Arduino)  
   * [③Arduino にプログラムを書き込むようのPC、USBケーブル等](#Arduino-にプログラムを書き込むようのPCUSBケーブル等)  
   * [④サーボモータ用電源（筆者は9.9Vのバッテリーを使用）](#サーボモータ用電源筆者は99Vのバッテリーを使用)  
   * [⑤ICS3.5Manager（ソフトウェア）](#ICS35Managerソフトウェア)  
   * [⑥Dual USBアダプターHS](#Dual-USBアダプターHS)  
   * [⑦KO-Driver](#KO-Driver)
* [3.STEP1：サーボモータの設定を行う](#3STEP1サーボモータの設定を行う)  
   * [（1）Dual USBアダプターHS のUSBを「ICSモード」に変更します。](#1Dual-USBアダプターHS-のUSBをICSモードに変更します)  
   * [（2）アダプターをPCのUSBポートに接続します。](#2アダプターをPCのUSBポートに接続します)  
   * [（3）ドライバーの更新](#3ドライバーの更新)  
   * [（4）「ICS3.5Manager」を起動する](#4ICS35Managerを起動する)  
   * [（5）サーボモータの設定を変更する](#5サーボモータの設定を変更する)
* [4.STEP2：Aarduino側の準備](#4STEP2Aarduino側の準備)
* [5.動作確認](#5動作確認)
* [6.参考サイト](#6参考サイト)

### 1.目標

この記事で扱う近藤科学のサーボモータ「KRS-2552RHV」は、こんな見た目です。  
![f:id:pythonjacascript:20180808191000j:plain](/images/ppythonjacascript2018080820180808191000.jpg "f:id:pythonjacascript:20180808191000j:plain")  
（引用源：<https://kondo-robot.com/product/krs-2552rhv-ics>）  
これを、Arduinoと接続し、通常のアナログサーボと同じように**PWM制御**するのがこの記事の目標です。  
  
  
### 2.必要なもの

#### ➀サーボモータ「KRS-2552RHV ICS」

#### ②Arduino

筆者はArduino UNOを使いました。

#### ③Arduino にプログラムを書き込むようのPC、USBケーブル等

#### ④サーボモータ用電源（筆者は9.9Vのバッテリーを使用）

#### ⑤ICS3.5Manager（ソフトウェア）

サーボモータの設定を行うのに使用します。ここから無料でダウンロードできます。  
<https://kondo-robot.com/w/wp-content/uploads/Ics35Manager1003.zip>  
ダウンロードして解凍したら、ICS3.5Manager.exe というファイルがあるので、それを実行してください。  
![f:id:pythonjacascript:20180808191907p:plain](/images/ppythonjacascript2018080820180808191907.png "f:id:pythonjacascript:20180808191907p:plain")  
このような画面が現れていたら成功です。ここで、サーボモータの設定を行います。

#### ⑥Dual USBアダプターHS

これを使ってサーボモータとパソコンを接続し、サーボモータの設定を行います。  
![f:id:pythonjacascript:20180808192445j:plain](/images/ppythonjacascript2018080820180808192445.jpg "f:id:pythonjacascript:20180808192445j:plain")  
ここから購入が可能です。  
[Dual USBアダプターHS | 近藤科学](https://kondo-robot.com/product/02116)

#### ⑦KO-Driver

上の⑥のDual USBアダプターHS用のドライバーです。  
ここからダウンロードできます。  
<https://kondo-robot.com/w/wp-content/uploads/KO-Driver2015.zip>
  
  
### 3.STEP1：サーボモータの設定を行う

初期設定では、近藤科学のサーボモータは、ICSという近藤科学独自の通信方法を使っています。それを、PWMという制御方式に変更する必要があります。  
そのために必要なのが、上の「必要なもの」の⑤～⑦です。

#### （1）Dual USBアダプターHS のUSBを「ICSモード」に変更します。

![f:id:pythonjacascript:20180808193338p:plain](/images/ppythonjacascript2018080820180808193338.png "f:id:pythonjacascript:20180808193338p:plain")  
USBアダプターの側面のスイッチを上図のようにセットしてください。

#### （2）アダプターをPCのUSBポートに接続します。

![f:id:pythonjacascript:20180808193535p:plain](/images/ppythonjacascript2018080820180808193535.png "f:id:pythonjacascript:20180808193535p:plain")  
モード表示ランプが**赤色**に点灯します。  
  
#### （3）ドライバーの更新

デバイスマネージャを開いてください。「DUAL USB ADAPTER HS」というデバイスが認識されるので、それをクリックして、「ドライバーの更新」を実行してください。ドライバーは、Dual USBアダプターHS用の「KO-Driver」です。  
  
#### （4）「ICS3.5Manager」を起動する

サーボモータと、バッテリーを次のように刺した状態で、ICS3.5Manager.exeを実行してください。  
![f:id:pythonjacascript:20180809031014p:plain](/images/ppythonjacascript2018080920180809031014.png "f:id:pythonjacascript:20180809031014p:plain")  
このような画面が表示されたら、成功です。

#### （5）サーボモータの設定を変更する

COMポートを選択して、「接続」ボタンを押した後、「シリアル専用」のチェックを外してください。  
![f:id:pythonjacascript:20180809031323p:plain](/images/ppythonjacascript2018080920180809031323.png "f:id:pythonjacascript:20180809031323p:plain")  
そうすることで、サーボモータのデータ通信規格が、ICSからPWMに変化します。そして、「書き込みボタン（↓）を押します。  
![f:id:pythonjacascript:20180809031316p:plain](/images/ppythonjacascript2018080920180809031316.png "f:id:pythonjacascript:20180809031316p:plain")

これで、サーボモータの設定は完了です。  
上の記述だけでは不十分だと思うので、  
[KRSサーボをR/Cプロポで使用できますか？ | 近藤科学](https://kondo-robot.com/faq/krs%5Frcpropo)  
を見ながら行ってください。

<https://kondo-robot.com/w/wp-content/uploads/Dual%5FUSB%5FadapterHS2015.zip>  
の中の、「KO\_Driver2015\_InstallManual.pdf」  
  
  
### 4.STEP2：Aarduino側の準備

PWMでサーボモータを制限するので、あとは簡単です。ですが、一つ注意しておこうことがあります。  
![f:id:pythonjacascript:20180809031657p:plain](/images/ppythonjacascript2018080920180809031657.png "f:id:pythonjacascript:20180809031657p:plain")  
それは、サーボモータの電源をONにした直後500ｍｓはサーボに信号を入力しない、ということです。  
また、パルスの長さは700usから、2300usの間にしてください。

それに注意して、プログラムを書いてください。

  
Arduinoとサーボモータの接続は、通常のアナログサーボと同じです。サーボモータのピン配置については、  
[製品マニュアル | 近藤科学](https://kondo-robot.com/archives/faq%5Fcategory/products-manual)  
のサイトから調べることができます。  
  
### 5.動作確認

上記の方法で動作しました。もうすぐ、サンプルプログラムを上げる予定です。  
![f:id:pythonjacascript:20180809032239j:plain](/images/ppythonjacascript2018080920180809032239.jpg "f:id:pythonjacascript:20180809032239j:plain")

### 6.参考サイト

<https://kondo-robot.com/w/wp-content/uploads/KO-Driver2015.zip>  
<https://kondo-robot.com/w/wp-content/uploads/Dual%5FUSB%5FadapterHS2015.zip>  
<https://kondo-robot.com/product/krs-2552rhv-ics>  
<https://kondo-robot.com/faq/krs%5Frcpropo>