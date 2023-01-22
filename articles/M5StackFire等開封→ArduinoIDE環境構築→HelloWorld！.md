---
title: "M5Stack Fire等開封→Arduino IDE環境構築→Hello World！"
emoji: ""
type: ""
topics: []
published: false
---

ひっさしぶりの投稿です！！！！！  
  
* [M5Stackが届いた！](#M5Stackが届いた)  
   * [中身一覧](#中身一覧)  
   * [M5Stack Fire (本体)](#M5Stack-Fire-本体)  
   * [UnitV](#UnitV)  
   * [ToFセンサー](#ToFセンサー)  
   * [光センサ（CdS）](#光センサCdS)  
   * [BALA（モーター＆エンコーダー）](#BALAモーターエンコーダー)  
   * [ボタンスイッチ](#ボタンスイッチ)
* [セットアップ](#セットアップ)  
   * [USBドライバーのインストール](#USBドライバーのインストール)  
   * [Arduino IDEの設定](#Arduino-IDEの設定)  
         * [ボードマネージャの設定](#ボードマネージャの設定)  
         * [ライブラリの追加](#ライブラリの追加)  
         * [PCとM5Stackの接続確認](#PCとM5Stackの接続確認)  
         * [デバイスの設定](#デバイスの設定)
* [プログラムを書く→コンパイル→書き込み](#プログラムを書くコンパイル書き込み)
* [実行結果](#実行結果)
* [M5Stackの基本的な使い方](#M5Stackの基本的な使い方)  
   * [電源](#電源)  
   * [分解する](#分解する)  
   * [初期設定（工場出荷状態）に戻す](#初期設定工場出荷状態に戻す)  
   * [その他注意事項](#その他注意事項)
* [M5BALAの基本的な使い方](#M5BALAの基本的な使い方)  
   * [電源](#電源-1)
* [参考文献](#参考文献)

### M5Stackが届いた！

M5Stack の一種である[M5Stack Fire](https://www.switch-science.com/catalog/3953/)と[Bala](https://www.switch-science.com/catalog/3995/)のセットが届きました。  
（学校から貸与していただいているものなので、[これ](https://www.switch-science.com/catalog/3953/)や[これ](https://www.switch-science.com/catalog/3995/)とは**内容物が異なります！！**）

![f:id:pythonjacascript:20200519024116j:plain](/images/ppythonjacascript2020051920200519024116.jpg "f:id:pythonjacascript:20200519024116j:plain")  
  
という事で、早速開封していきます  
  
  
#### 中身一覧

![f:id:pythonjacascript:20200519024158j:plain](/images/ppythonjacascript2020051920200519024158.jpg "f:id:pythonjacascript:20200519024158j:plain")  
今回、学校から支給していただいた部品一覧です。  
これらを使っていろいろ試していきたいと考えています。

  
ほとんどのパーツがレゴ用の穴が開いていて、レゴとの相性がとてもいいです。

  
【Basics】

* M5Stack FIRE 1(with a charging base)
* USB type-A to type-C cable 1
* BALA module 1 set(motors & motor driver module, 2 wheels)

【Units】

* M5Stack Unit-V AI camera 1
* ToF unit 2
* Pa.HUB unit 1
* Light unit 1
* Dual-button unit 1

#### M5Stack Fire (本体)

本体です！  
![f:id:pythonjacascript:20200519024937p:plain](/images/ppythonjacascript2020051920200519024937.png "f:id:pythonjacascript:20200519024937p:plain")  
  
M5Stack Fireのほかにも、M5Stack basicやGray、M5Goがありますが、M5Stack Fireの方が高性能です

SH200Q or MPU6886（三軸加速度センサ・三軸ジャイロセンサ）と BMM150（三軸デジタル磁力センサ）を搭載しています。M5Stackの種類によって**MPU6050を搭載していたりMPU9250を搭載していたりするので注意が必要です。**
  
  
#### UnitV

ニューラルネットワークプロセッサ（KPU)を搭載したカメラです。  
公式ドキュメントを読むまで全く気付きませんでしたが、側面に2つプッシュスイッチがあります。

![f:id:pythonjacascript:20200519025451j:plain](/images/ppythonjacascript2020051920200519025451.jpg "f:id:pythonjacascript:20200519025451j:plain")   

[UnitV AI Camera - スイッチサイエンス](https://www.switch-science.com/catalog/6212/)  
  
  
#### ToFセンサー

ToF（Time of Flight）センサーはその名の通り、光（レーザー）を照射して、物体に反射してから戻ってくるまでの時間を計測することで、物体との距離を測るセンサーのことです。

![f:id:pythonjacascript:20200519024309j:plain](/images/ppythonjacascript2020051920200519024309.jpg "f:id:pythonjacascript:20200519024309j:plain")  

[M5Stack用ToF測距センサユニット - スイッチサイエンス](https://www.switch-science.com/catalog/5219/)  

[VL53L0X](https://www.st.com/ja/imaging-and-photonics-solutions/vl53l0x.html)を搭載し、I2C通信でM5Stack本体とデータ送受信を行います。測定できる距離はデータシートでは2ｍでした  
データシート→ <https://www.st.com/resource/en/datasheet/vl53l0x.pdf>  
  
  
#### 光センサ（CdS）

![f:id:pythonjacascript:20200519024625j:plain](/images/ppythonjacascript2020051920200519024625.jpg "f:id:pythonjacascript:20200519024625j:plain")  
デジタル出力（半固定抵抗で閾値を設定）とアナログ出力の2端子があります。

[M5Stack用光センサユニット - スイッチサイエンス](https://www.switch-science.com/catalog/4051/)

#### BALA（モーター＆エンコーダー）

2つのモーターとエンコーダーが付いたUnitです。本体のM5Stack Fire とは、pogoピンを通じてI2C通信を行います。  
Type-Cの端子が付いていて、ここから内蔵バッテリーに充電することができます。

![f:id:pythonjacascript:20200519025423j:plain](/images/ppythonjacascript2020051920200519025423.jpg "f:id:pythonjacascript:20200519025423j:plain")  
  
M3のネジが使えます  
![f:id:pythonjacascript:20200519025736j:plain](/images/ppythonjacascript2020051920200519025736.jpg "f:id:pythonjacascript:20200519025736j:plain")

  
#### ボタンスイッチ

普通のプッシュボタンです。特に説明することはないです。  
![f:id:pythonjacascript:20200519025603j:plain](/images/ppythonjacascript2020051920200519025603.jpg "f:id:pythonjacascript:20200519025603j:plain")  
  
  
### セットアップ

次に、C言語（Arduino言語）を用いてM5Stack用の環境を作っていきます

必要なものは

* **Arduino IDE** (プログラムを書くためのソフト、統合開発環境)
* USBドライバ（Arduino IDEで作成したプログラムをM5Stackに書きこむためのドライバ）
* M5Stackのライブラリ（Arduino IDEにこのライブラリを #include して、M5Stack用の実行ファイルを作成できるようにする）

#### USBドライバーのインストール

インストール元＝  
<https://jp.silabs.com/products/development-tools/software/usb-to-uart-bridge-vcp-drivers>

  
Windowsの場合、「**CP210x\_Universal\_Windows\_Driver.zip**」ダウンロード＆解凍するとその中に「CP210xVCPInstaller\_x64.exe」があるので実行します（32bit環境では「CP210xVCPInstaller\_x32.exe」のほうです）  
（64bitなのか32bitなのかの確認方法は、**コントロールパネル**を開いて、「**システムとセキュリティ**」→「**システム**」をクリックし、「**システム**」項目内の「**システムの種類**」を見ると「**64ビット オペレーティングシステム**」などと書いてある）

![f:id:pythonjacascript:20200519030454j:plain](/images/ppythonjacascript2020051920200519030454.jpg "f:id:pythonjacascript:20200519030454j:plain")

一瞬でインストールできました。（速すぎたので大丈夫か不安になるくらい）  
  
  
#### Arduino IDEの設定

まず、Arduino IDEをインストールします。  
[Arduino - Software](https://www.arduino.cc/en/Main/Software)  
ここからインストールすることができます。  
特に複雑な点はないので省略します。インストールが終了し、Arduino IDEを起動すると、下のような画面が表示されるはずです  
![f:id:pythonjacascript:20181024172314j:plain](/images/ppythonjacascript2018102420181024172314.jpg "f:id:pythonjacascript:20181024172314j:plain")
  
  
Arduino IDEはもともとArduinoのプログラミングを行うために開発されたIDE（統合開発環境）なので、M5StackのプログラミングをArduinoIDEで行うには拡張機能を入れる必要があります。

Arduino IDEの「ファイル」⇒「環境設定」を選択し、  
![f:id:pythonjacascript:20200519031040j:plain](/images/ppythonjacascript2020051920200519031040.jpg "f:id:pythonjacascript:20200519031040j:plain")  
  
新しくダイアログが開くので、「追加のボードマネージャURL」のところに  
<https://raw.githubusercontent.com/espressif/arduino-esp32/gh-pages/package%5Fesp32%5Findex.json>  
を入力してOKを押します  
![f:id:pythonjacascript:20200519030926p:plain](/images/ppythonjacascript2020051920200519030926.png "f:id:pythonjacascript:20200519030926p:plain")  
  
  
##### ボードマネージャの設定

次に、「**ツール**」⇒「**ボード**」→「**ボードマネージャ**」を選択し、新しく開いたダイアログで「esp32」の「インストール」ボタンを押してインストールします。  
![f:id:pythonjacascript:20200519031108j:plain](/images/ppythonjacascript2020051920200519031108.jpg "f:id:pythonjacascript:20200519031108j:plain")  
（画面上の検索ボックスに「esp32」と入れると出てきます。）  
  
  
##### ライブラリの追加

「スケッチ」→「ライブラリをインクルード」→「ライブラリを管理…」を選択し、

新しく開いたダイアログで「M5Stack by M5Stack」という名前のライブラリを選択して「インストール」をクリックします。  
![f:id:pythonjacascript:20200519031146j:plain](/images/ppythonjacascript2020051920200519031146.jpg "f:id:pythonjacascript:20200519031146j:plain")  
（↑写真で一つだけ色が違うやつです。「m5stack」で検索しても十数種類あったので間違えんよーにせんとな...）  
  
  
##### PCとM5Stackの接続確認

付属のUSB-TypeCケーブルを用いて、M5StackをPCと接続します。

PCと接続すると自動的に電源が入り、前回電源を入れた時に実行したプログラムが起動します。電源が入るとM5Stack側面が白く光るみたいです。

  
この状態で、PCの**デバイスマネージャー**を開き、「**ポート（COMとLPT)**」の項目で  
![f:id:pythonjacascript:20200521005717j:plain](/images/ppythonjacascript2020052120200521005717.jpg "f:id:pythonjacascript:20200521005717j:plain")  
このようにM5Stackが認識できていればOKです（上の写真ではCOM3にM5Stackを認識しています）  
  
  
##### デバイスの設定

「**ツール**」→「**ボード**」で「**M5Stack-Core-ESP32**」を選択します。  
そして、もう一度「**ツール**」→「**ボード**」をクリックし、以下のように設定してください

| ボード              | M5Stack-Core-ESP32    |
| ---------------- | --------------------- |
| Upload Speed     | 921600                |
| Flash Frequency  | 80MHz                 |
| Flash mode       | QIO                   |
| Partision Scheme | 初期値                   |
| Core Debug Level | なし                    |
| シリアルポート          | （デバイスマネージャーで確認したCOM?） |

下写真の様になればOKです  
![f:id:pythonjacascript:20200519031445j:plain](/images/ppythonjacascript2020051920200519031445.jpg "f:id:pythonjacascript:20200519031445j:plain")  
  
  
### プログラムを書く→コンパイル→書き込み

Arduino IDEから「ファイル」→「新規ファイル」で新しくプロジェクトを作成して、そこに以下のプログラムを書きこみます

#include <M5Stack.h>

//setup()関数はM5Stackがスタートした最初に読み込まれる
void setup(){
  // M5Stack オブジェクトの初期化
  M5.begin();

  //Power chipがgpio21, gpio22, I2Cにつながれたデバイスに接続される。
  //バッテリー動作の場合はこの関数を読んでください（バッテリーの電圧を調べるらしい）
  M5.Power.begin();

  // 色々設定（なくても動作する）
  M5.Lcd.setCursor(10, 10); //文字表示の左上位置を設定
  M5.Lcd.setTextColor(RED); //文字色設定(WHITE, BLACK, RED, GREEN, BLUE, GRAY...)
  M5.Lcd.setTextSize(2);//文字の大きさ(px)設定
  
    
  // LCDディスプレイに文字表示
  M5.Lcd.drawString("こんにちは", 0, 0);
}

// loop()関数はsetup()関数が呼ばれた後に繰り返し呼ばれる。
// イメージとしては
/* void main(){
 *    setup();
 *    while(true){
 *       loop();
 *    }
 *  }
 */
 // みたいな感じ。

void loop() {} //特になし！！

  
次に、**コンパイル**を行います。（コンパイルは上のプログラムを機械語（実行可能なファイル）に変換する作業のことです。）

Arduino IDEの左上の![f:id:pythonjacascript:20200521010338j:plain](/images/ppythonjacascript2020052120200521010338.jpg "f:id:pythonjacascript:20200521010338j:plain")を押すとコンパイルが開始されます。  
コンパイルが成功すると、下の画像の様に「コンパイルが完了しました」と表示されます。

  
最後に、コンパイルしたプログラムをM5Stackに書き込みます。Arduino IDEの左上の2つ目のボタン（右矢印の形）を押します。  
rduino IDEの左上の「**マイコンボードに書き込む**」ボタン![f:id:pythonjacascript:20181024173825j:plain](/images/ppythonjacascript2018102420181024173825.jpg "f:id:pythonjacascript:20181024173825j:plain")を押してください。

  
![f:id:pythonjacascript:20200519030019j:plain](/images/ppythonjacascript2020051920200519030019.jpg "f:id:pythonjacascript:20200519030019j:plain")  
(書き込み中）  
書き込む途中、M5Stackが2回ほど「カッ」という音がして(ﾟДﾟ)ってなりましたが、問題ありませんでした。

書き込みが終了すると、「**ボードへの書き込みが完了しました。**」と表示されます  
  
### 実行結果

プログラムの書き込みが終了すると、自動的にそのプログラムが実行されます。（書き込みが終わったらUSBケーブルを抜いてもOKです）  
以下の画像のようになるはずです  
  
![f:id:pythonjacascript:20200519030052j:plain](/images/ppythonjacascript2020051920200519030052.jpg "f:id:pythonjacascript:20200519030052j:plain")  

画像では文字が黄色っぽく映っていますが、肉眼で見るとちゃんと赤色です。  
  
  
### M5Stackの基本的な使い方

#### 電源

* 電源ON/リセット → 電源ボタンを1回押す。USB-Cを接続すると勝手に起動する。
* 電源OFF → 電源ボタンを2回続けて押す。ただしUSB-Cケーブル接続中はオフできない。

（電源ボタンの位置はこの記事の上の方の「M5Stack Fire」節を参照）  
なお、これは設定で変更できるようです  
  
M5.Power.setPowerBtnEn(true); //電源ボタンの操作を受け付ける
M5.Power.setPowerBtnEn(false); //電源ボタンの操作を受け付けない

M5.Power.setPowerBoostOnOff(true); //長押しで電源ON/OFF
M5.Power.setPowerBoostOnOff(false); //2回連続押しで電源ON/OFF
M5.Power.setPowerBoostSet(true); //短押し一回で電源ON/OFF

M5.Power.setPowerVin(true); //USBからの電力供給が途切れた時、再起動する
M5.Power.setPowerVin(false); //USBからの電力供給が途切れた時、再起動しない

また、充電状態はM5Stack Fireの手前側面についているLEDで確認することができます。

* LEDについて：充電中＝赤点滅、充電完了＝赤点灯
* バッテリー残量が少ないとSDカードスロット近くの赤LEDが点滅する

#### 分解する

M5Stack背面の4隅のネジを外した後（正確にはMicroSDカードの面の2つでいいのですが）、上下にパカっと2つにわけることができます  
中に2ｘ15のピンヘッダがあり、そこでLCD等とマイコン本体が通信をしているので、その部分が折れたりしたらアウトだと思われます...  
![f:id:pythonjacascript:20200717032344j:plain](/images/ppythonjacascript2020071720200717032344.jpg "f:id:pythonjacascript:20200717032344j:plain")  
  
  
#### 初期設定（工場出荷状態）に戻す

初めに電源を入れた時には、下画像のような表示が出て「Wi-fiにつないでみよう！」みたいに促されますが、この表示に戻すには、  
M5Balaの場合は、工場出荷時のプログラムはこれになっていました  
<https://github.com/m5stack/M5Stack/tree/master/examples/Modules/BALA>

  
<https://github.com/m5stack/M5Stack/blob/master/examples/Basics/FactoryTest/>

#### その他注意事項

* 動作温度→0～40℃
* BALAと分離しておく（BALAはcharging baseと同じような機能を持っているが、USBを繋いでいないBALAにM5Stack Fireを載せるとBalaの内蔵バッテリーを消費するらしい）
* LEDについて：充電中＝赤点滅、充電完了＝赤点灯
* バッテリー残量が少ないとSDカードスロット近くの赤LEDが点滅する
* ベースからはずすタイミングによっては赤LEDがつきっぱなしになるが、放っておくと消える。またはFIREを起動してからオフすると消える。
* プログラム書き込み時やシリアル通信時にエラーが出る場合、Type-Cの差し込む向きを逆にすると解決することがある

### M5BALAの基本的な使い方

#### 電源

* 充電時はM5Stack Fireと分離しておく（M5Stack FireがBalaのバッテリーを消費するので、分離したほうが早く充電できる）
* 電池切れ→ボタンを押しても赤LEDがつかなくなる。
* 充電→USB Type-Cケーブルで充電する。赤LEDが点滅
* 充電完了→赤LEDが点灯。
* 付属のUSB-Cケーブル以外のケーブルでは充電できないらしい
  
  
### 参考文献

<https://raspberrypi.mongonta.com/howto-start-m5stack-arduinoide/#%E3%82%BD%E3%83%95%E3%83%88%E3%81%AE%E3%82%A4%E3%83%B3%E3%82%B9%E3%83%88%E3%83%BC%E3%83%AB%EF%BC%88Windows%EF%BC%89>

M5Stackの回路図→<https://github.com/m5stack/M5-Schematic/blob/master/Core/Basic/M5-Core-Schematic%2820171206%29.pdf>