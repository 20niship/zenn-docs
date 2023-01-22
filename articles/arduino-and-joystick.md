---
title: "【Arduino】ジョイスティックを使う"
emoji: "🤖"
type: "tech"
topics: []
published: false
---

Arduinoで2軸ジョイスティックのバーの傾きを検出してみました。

* [1\. ハードウェア](#1-ハードウェア)
* [2\. ソフトウェア](#2-ソフトウェア)
* [3\. 実行結果：](#3-実行結果)

### 1\. ハードウェア

ジョイスティックとは、こんな部品です。  
![f:id:pythonjacascript:20180724225255j:plain](/images/ppythonjacascript2018072420180724225255.jpg "f:id:pythonjacascript:20180724225255j:plain")

ゲームのリモコンなどに使われているあれです。この章では、ジョイスティックをArduinoに接続し、ジョイスティックの角度を読み取っていきます。つなぎ方は、以下の通り。  
![f:id:pythonjacascript:20180724225318p:plain](/images/ppythonjacascript2018072420180724225318.png "f:id:pythonjacascript:20180724225318p:plain")

  
ジョイスティックのピン – Arduinoのピン  
VDD(二か所) – 5V  
ADC OH 0 – A0  
ADC OH 1 – A1  
GND（二か所） - GND

![f:id:pythonjacascript:20180724225435p:plain](/images/ppythonjacascript2018072420180724225435.png "f:id:pythonjacascript:20180724225435p:plain")

  
ジョイスティックは、秋月電子で購入したものを使用しています。データシートを見ながら製作してください。  
<http://akizukidenshi.com/download/ds/parallax/27800.pdf>

  
### 2\. ソフトウェア

int StickXaxis = 14; //A0 pin
int StickYaxis = 15; //A1 pin

void setup() {
  Serial.begin(9600);
  pinMode(StickXaxis,INPUT);
  pinMode(StickYaxis,INPUT);
}

void loop() {
  Serial.println(analogRead(StickXaxis));
  Serial.println(analogRead(StickYaxis));
  Serial.println("************************");
  delay(1000);
}

### 3\. 実行結果：

プログラムを実行しる状態でPCと接続して、シリアルモニターを表示させてください。  
ジョイスティックのバーの傾きが、一秒ごとにArduino IDEのシリアルモニタに表示される