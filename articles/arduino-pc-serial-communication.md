---
title: "【Arduino】PC と Arduino でシリアル通信"
emoji: "🤖"
type: "tech"
topics: []
published: false
---

パソコンとArduinoを通信させます。通信方法は「シリアル通信」といって、RXとTXの２ピンを使ってデータのやり取りを行います。仕組みはとても複雑なのですが、それを理解していなくても、専用の「Serial」ライブラリを使うと、簡単にシリアル通信ができます。

* [1\. ハードウェア](#1-ハードウェア)
* [2\. プログラム](#2-プログラム)  
   * [※解説](#解説)
* [3\. 実行](#3-実行)
* [4.実行結果](#4実行結果)

### 1\. ハードウェア

回路を組む必要はありません。ただ、プログラムを実行するとき、PCとArduinoをUSBで接続してください。Arduinoは、USBから電源をもらうので、電源回路も必要ありません。  
  
### 2\. プログラム

void setup() {
  Serial.begin(9600);
}
void loop() {
  for(unsigned int i = 0; i<100; i++){
    Serial.println(i * 100);
    Serial.println("Pop Team Epic");
    delay(100);
  }
}

#### ※解説

シリアル通信を行うには、新たに2つの関数を使う必要があります。それがSerial.println();とserial.begin()である。まず、setup()のなかで、Serial.begin()をおこない、シリアル通信をセットアップする。かっこ()の中の引数は、シリアル通信の速度を表していて、「 Serial.begin(9600);」だと、「毎秒9600bitでデータを送信します」という意味です。  
  
### 3\. 実行

➀ArduinoをUSBで接続して、「ツール」→「シリアルモニタ」を開いてください。  
②ビットレートをSerial.bigin()で設定した値と同じにしてください。  
  
### 4.実行結果

シリアルモニターに次のように表示されます。  
![f:id:pythonjacascript:20180724225044p:plain](/images/ppythonjacascript2018072420180724225044.png "f:id:pythonjacascript:20180724225044p:plain") 