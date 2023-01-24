---
title: "【M5Stack】第7回 ToFセンサを使って物体との距離[mm]を計測"
emoji: ""
type: ""
topics: []
published: false
---

### ToFセンサーとは

> TOFとは「Time Of Flight」の頭文字で、センサからパルス投光されたレーザがセンサ内の受光素子に戻ってくるまでの時間を計測し、その時間を距離に換算する測定方式です。

(<https://www.optex-fa.jp/tech%5Fguide/tof%5Fspecial/> より)  

  
この記事で使うのは、M5Stack公式のToFセンサーです。  
![f:id:pythonjacascript:20200519024309j:plain](/images/ppythonjacascript2020051920200519024309.jpg "f:id:pythonjacascript:20200519024309j:plain")

[M5Stack用ToF測距センサユニット - スイッチサイエンス](https://www.switch-science.com/catalog/5219/)  
  
[VL53L0X](https://www.st.com/ja/imaging-and-photonics-solutions/vl53l0x.html)を搭載し、I2C通信でM5Stack本体とデータ送受信を行います。測定できる距離はデータシートでは2ｍでした  
データシート→ <https://www.st.com/resource/en/datasheet/vl53l0x.pdf>  
  
  
### サンプルプログラム

#include <M5Stack.h>
#include <Wire.h>
#include "VL53L0X.h"

//library   = https://github.com/pololu/vl53l0x-arduino

VL53L0X sensor;
void setup() {
  M5.begin();

  //Power chipがgpio21, gpio22, I2Cにつながれたデバイスに接続される。
  //バッテリー動作の場合はこの関数を読んでください（バッテリーの電圧を調べるらしい）
  M5.Power.begin();
  Wire.begin();// I2C通信を開始する
  
  sensor.init();
  sensor.setTimeout(500);
  M5.Lcd.fillScreen(BLACK);
  M5.Lcd.setCursor(10, 10);
  M5.Lcd.setTextColor(WHITE, BLACK);
  M5.Lcd.setTextSize(4);
}
void loop() {
  int distance = sensor.readRangeSingleMillimeters();
  M5.Lcd.setCursor(0, 0);
  M5.Lcd.print(distance);
  M5.Lcd.print("[mm]");
    
  if (sensor.timeoutOccurred()) {
    M5.Lcd.setCursor(0, 50);
    M5.Lcd.println("TIMEOUT");
  }else{
    M5.Lcd.setCursor(0, 50);
    M5.Lcd.println("        ");  
  }
}

  
### 実行

インクルードファイルの「#include "VL53L0X.h"」は <https://github.com/pololu/vl53l0x-arduino> のものです。プロジェクトフォルダ（.inoファイルが入っているフォルダ) に上記サイトのVL53L0X.h"とVL53L0X.cpp"をコピーして持ってきます

その状態でコンパイルしてM5Stackに書き込み実行します

尚、この記事で使っているToFセンサユニットはI2C通信でM5Stackとやり取りしているので、下写真の様にType-Cケーブルの隣のポートにセンサーを接続します。

M5Stackには同じ形のポートが3つありますが（PORT-A ～ PORT-C）I2C通信を行うことができるのはPORT-Aです  
![f:id:pythonjacascript:20200519024937p:plain](/images/ppythonjacascript2020051920200519024937.png "f:id:pythonjacascript:20200519024937p:plain")  

  
そして電源を入れると、距離がmm単位で表示されます  
（下画像では「TIMEOUT」と表示されていますが、実際には表示されないはずです）  
![f:id:pythonjacascript:20200519121418j:plain](/images/ppythonjacascript2020051920200519121418.jpg "f:id:pythonjacascript:20200519121418j:plain")  

因みに、「65535mｍ」と表示されていたら、そもそもセンサーが認識できていない、またはきちんとI2C通信が行われていません。  
0～1500mmくらいまでは計測できます。けれど、それ以上距離が離れると「8190mm」と表示されます

タイムアウトが起これば「TIMEOUT」と2行目に表示されます  
![f:id:pythonjacascript:20200519121401j:plain](/images/ppythonjacascript2020051920200519121401.jpg "f:id:pythonjacascript:20200519121401j:plain")  
  
  
### 参考サイト

[VL53L0XをM5Stackで使う - Qiita](https://qiita.com/nnn112358/items/1d71c0b3269ee977e781) のサイトを参考にさせてもらいました。

1分くらいすると固まってしまうという症状もあるようです（<https://twitter.com/tokuhira/status/1094103814347472923?s=20> ）  
電源周りを改良したらうまくいったという報告も↑のツイッターに載っていました

  
また、この記事のプログラムは既存ライブラリを使っていましたが、ライブラリを使わずに書いてあるコードも見つけました  
→（<https://github.com/m5stack/M5Stack/blob/master/examples/Unit/ToF%5FVL53L0X/ToF%5FVL53L0X.ino> ）