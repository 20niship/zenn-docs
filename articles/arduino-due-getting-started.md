---
title: "【Arduino】Arduino Due を使ってみた。"
emoji: ""
type: ""
topics: ["arduino"]
published: false
---

Arduinoには多くの種類がありますが、その中でも最も使用されているのは、「Arduino UNO」だと思います。  
ですが、この記事では少しマイナーな Arduino Due の使用までの流れを書いてみました。  
最後にはLED点滅までやってみます。

Aarduino Due は、こんなボードです。  
![f:id:pythonjacascript:20180731213839j:plain](/images/ppythonjacascript2018073120180731213839.jpg "f:id:pythonjacascript:20180731213839j:plain")  
Arduino UNO よりも、すこし大きめのサイズです。

* [1.準備](#1準備)  
   * [1\. 必要なもの：](#1-必要なもの)  
   * [2\. Arduino IDEの設定](#2-Arduino-IDEの設定)
* [２.プログラムを書く](#２プログラムを書く)
* [3.書き込む](#3書き込む)

### 1.準備

#### 1\. 必要なもの：

* Aarduino Due
* **インターネットにつながっている**PC（Arduino IDE はPCに入っている前提で説明を始めます。）
* Micro USB ケーブル

この３つをそろえてください。

パソコンにArduino IDEを入れる方法は、検索するとたくさん出てくると思うので、割愛します。

Arduino UNOはUSB type-C でパソコンつないでいましたが、Arduino Due は、マイクロUSBでパソコンと接続します。Androidのスマホの充電器のケーブルと同じ形です。  
  
#### 2\. Arduino IDEの設定

Arduino IDEを起動して、「ツール」→「ボード」をクリックしてください。ここで、書き込むArduinoの種類を選択するのですが、「Arduino Due」は見つかりません。デフォルトの状態では、ライブラリがインストールされていないのです。

  
ライブラリーのインストール方法

1. Arduino IDEで、「ツール」→「ボード」→「ボードマネージャ」をクリック

![f:id:pythonjacascript:20180731213431p:plain](/images/ppythonjacascript2018073120180731213431.png "f:id:pythonjacascript:20180731213431p:plain")  
（Fig.1 ボードマネージャ）

1. 上のような画面が表示されたら、検索場所するところに「due」と入力。
2. 「Arduino SAM Boards (32-bits ARM Cortex-M3)」を選択。
3. 右下の「インストール」をクリック

![f:id:pythonjacascript:20180731213726p:plain](/images/ppythonjacascript2018073120180731213726.png "f:id:pythonjacascript:20180731213726p:plain")
  
  
### ２.プログラムを書く

以下のコードをArduino IDEでコンパイルしてください。  
コンパイルするとき、ボードをArduino Dueに設定してください。  
設定方法は、「ツール」→「ボード」→「Arduino Due(Programming Port）」をクリックすれば完了です。

Arduino Due もArduino UNO と同じように、13ピンにLEDが最初から接続されています。  
このLED、Arduinoが正常に動作するかをテストするのにとても便利です！  
この記事でも、そのLEDを１秒ごとに点滅するプログラムを実行します。

const int LED = 13;  //LED pin

void setup() {
  pinMode(LED, OUTPUT); //set LED pin as output pin
}

void loop() {
  digitalWrite(LED, HIGH);
  delay(1000);
  digitalWrite(LED, LOW);
  delay(1000);
}

### 3.書き込む

プログラムを書き込むときに、一つだけ注意点があります。それは、  
**「Arduino Dueは、ふたつUSBポートがあり、それぞれ役割が違う！」**ということです。

![f:id:pythonjacascript:20180731214744j:plain](/images/ppythonjacascript2018073120180731214744.jpg "f:id:pythonjacascript:20180731214744j:plain")

リセットスイッチに遠いほうが、プログラムを書き込むのに使うUSBポートです。  
リセットスイッチに近いUSBポートは、SAM3X（MCU）に直接続されています。プログラムを書いたり、パソコンとシリアル通信をするとき[\*1](#f-93402629 "[https://www.arduino.cc/en/Guide/ArduinoDue:title]")は、リセットスイッチに遠い、「Programming Port Serial」を使ってください。

  
後は、特にArduino UNO と変更はありません。

1. Arduinoをパソコンと接続して
2. 「ツール」→「シリアルポート」から、Arduinoを接続したシリアルポートを選択して、
3. 書き込みボタン（「⇒」の形のボタン）を押せば完了です。

  
4.動作確認  
Arduino Due ボード上の「L」と書かれた隣にあるチップLEDが一秒ごとに点滅していれば、成功です！  
もし、エラーが出たり、動作しなかった場合は、もう一度上記の手順を確かめてみてください。

[\*1](#fn-93402629):[Arduino - ArduinoDue](https://www.arduino.cc/en/Guide/ArduinoDue)