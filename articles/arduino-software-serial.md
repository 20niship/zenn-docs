---
title: "【Arduino】SoftwareSerial を使って、複数のデバイスとシリアル通信を行う"
emoji: ""
type: ""
topics: []
published: false
---

* [SoftwareSerial とは？](#SoftwareSerial-とは)
* [プログラムの書き方](#プログラムの書き方)  
   * [（1）ライブラリのインクルード](#1ライブラリのインクルード)  
   * [（2）通信用ピンの設定](#2通信用ピンの設定)  
   * [（3）シリアル通信のポートを切り替える](#3シリアル通信のポートを切り替える)
* [2.サンプルプログラム](#2サンプルプログラム)  
   * [配線](#配線)  
   * [プログラム](#プログラム)  
         * [送信機](#送信機)
* [実行結果](#実行結果)

### SoftwareSerial とは？

通常、シリアル通信に使われるピンは、**TXピンが1ピン、RXピンが0ピン**です。また、それらのピンは、Arduinoについている、パソコンと通信するためのUSBにも繋がっています。

しかし、複数の電子機器とシリアル通信をするには、0ピンと１ピン以外のピンを使わなければいけません。ですが、Arduinoには通常のシリアル通信ポート（TXピンとRXピン）が1対しか用意されていません。  
そこで**任意のピンをプログラムで疑似的にRX-TXピンとして使う**ためのライブラリである、「SoftwareSerial」というライブラリです。  
  
  
### プログラムの書き方

SoftwareSerial の使い方は、通常のシリアル通信とほとんど同じです。

ですが、いくつか違う部分もあるので、そこだけ解説しておきます。  
  
#### （1）ライブラリのインクルード

SoftwareSerialを使う時は、次のような一文を書いてください。

#include <SoftwareSerial.h>

#### （2）通信用ピンの設定

通常の Serial ライブラリは、シリアル通信用のピン（TX、RX）があらかじめ決められていました。  
しかし、SoftwareSerial では、シリアル通信に使いたいピンを自由に選択することができます。

SoftwareSerial softSerial(2,3);

意味：RXピンを2ピン、TXピンを３ピンに設定する  
  
  
#### （3）シリアル通信のポートを切り替える

SoftwareSerialを使用するということは、複数のシリアルポートを同時に使うことが多いと思います。そのとき、どちらのシリアルポートを使うかを切り替える必要があります。  
その切り替えを高速で繰り返すことによって、複数のポートで同時にシリアル通信ができているようなスケッチを書きます。

シリアル通信ポートの切り替えには、「**listen()**」関数を使います。

SoftwareSerial mySerial(2, 3); // RX, TX

...（途中省略）

Serial.println("TX pin = 1,  RX pin = 0");  //通常のシリアル通信を使用
mySerial.listen();    //SoftwareSerialに切り替え
mySerial.write(count);     //SoftwareSerialを使用

### 2.サンプルプログラム

シリアル通信を２台のArduinoで行います。一方が送信器でもう一方が受信器です。そして、受信機側をPCとUSBで接続し、PCにデータを送信します。  
![f:id:pythonjacascript:20180910073910j:plain](/images/ppythonjacascript2018091020180910073910.jpg "f:id:pythonjacascript:20180910073910j:plain")

#### 配線

配線は以下のようにします。  
![f:id:pythonjacascript:20190305183108j:plain](/images/ppythonjacascript2019030520190305183108.jpg "f:id:pythonjacascript:20190305183108j:plain")

#### プログラム

##### 送信機

#include <SoftwareSerial.h>
SoftwareSerial mySerial(2, 3); // RX, TX

int LED = 13;
int count = 0;

void setup(){
  Serial.begin(115200);  
  mySerial.begin(115200); // ソフトウェアシリアルの初期化

  pinMode(LED, OUTPUT);
}

void loop(){
  count++;
  Serial.print("counter = ");
  Serial.println(count);
  
  mySerial.listen();
  mySerial.write(count);
  
  if((count % 2) == 1){ 
    digitalWrite(LED, HIGH);
  }else{
    digitalWrite(LED, LOW);
  }
  delay(1000);
}

受信機

int LED = 13;

void setup(){
  Serial.begin(115200);
  pinMode(LED, OUTPUT);
}

void loop(){
  while(Serial.available()){
    byte inChar = (byte)Serial.read();
    if((inChar % 2) == 1){ 
      digitalWrite(LED, HIGH);
    }else{
      digitalWrite(LED, LOW);
    }
    delay(10);
  }
}

### 実行結果

受信機側のArduinoをUSBでPCに接続し、シリアルモニターを開きます。  
![f:id:pythonjacascript:20180910073914j:plain:h300](/images/ppythonjacascript2018091020180910073914.jpg "f:id:pythonjacascript:20180910073914j:plain:h300")  
このように表示されていれば、通常のシリアル通信は動作しています。

また、二つのArduinoの13ピンについているLEDが同時に点滅していれば、SoftwareSerialも正常に動作していることになります。