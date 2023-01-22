---
title: "【Arduino】ブラシレスモーターが回ってモーター..."
emoji: "🤖"
type: "tech"
topics: ["arduino"]
published: false
---

**タイトルからふざけてますね。はい。**

  
とりあえず、**ブラシレスモーター**「DX2205」を**Arduino**を使って制御することに成功したので、その方法を書いていきます。

![f:id:pythonjacascript:20180822011251j:plain](/images/ppythonjacascript2018082220180822011251.jpg "f:id:pythonjacascript:20180822011251j:plain")

* [1.目標](#1目標)
* [2.部品説明](#2部品説明)  
   * [（1）ブラシレスモーター「DX2205」](#1ブラシレスモーターDX2205)  
   * [（2）ESC](#2ESC)
* [3.ハードウェア](#3ハードウェア)  
   * [（1）半田付け](#1半田付け)  
   * [（2）モーターの固定](#2モーターの固定)  
   * [（3）配線](#3配線)
* [4.ソフトウェア](#4ソフトウェア)
* [5モーターを回す！！](#5モーターを回す)
* [6プログラム](#6プログラム)

### 1.目標

・ブラシレスモーター「DX2205」を**Arduino UNO** と **ESC** のみを使って制御  
・シリアルモニターを使って、ブラシレスモーターの回転数を設定できるようにする。  
  
  
### 2.部品説明

#### （1）ブラシレスモーター「DX2205」

![f:id:pythonjacascript:20180821235135j:plain](/images/ppythonjacascript2018082120180821235135.jpg "f:id:pythonjacascript:20180821235135j:plain")

ブラシレスモータ（DX2205)の外観

この記事で使用しているブラシレスモーター「DX2205」のスペック：

| KV値    | 2300KV |
| ------ | ------ |
| 高さ     | 31.5mm |
| モーター直径 | 27.9mm |
| シャフト直径 | M5     |
| 質量     | 28.8g  |
| 電圧     | 2-4S   |
| 最大電流   | 27.6A  |
| 最大電力   | 408W   |

> ※「電圧」の項目ですが、「2-4S」というのは、リポバッテリーの直列で繋ぐパックの数を表しています。リポバッテリーは１パック当たりで3.7Vなので、2-4Sは、2～4パックの間の電圧（7.4V～14.8V）の間で使用してくださいという意味です。

購入元：  
<https://www.amazon.co.jp/GoolRC-スピードコントローラ-DX2205-RCレースマルチコプター-ドローンクワッドローター用/dp/B0748GGSFZ/ref=sr%5F1%5F6?ie=UTF8&qid=1534866536&sr=8-6&keywords=DX2205:h400>

  
ブラシレスモーターとは、その名の通りブラシがないモーターです。そのため、電池に繋げば回るという単純なものではなく、きちんとした制御回路を組む必要があります。そして、その制御回路がひとまとまりになったものがESCです。  
  
#### （2）ESC

ESCとは、「lectronic Speed Controller 」の略で、ブラシレスモーターをコントロールするのに使用されます。  
出力はブラシレスモータのそれぞれの端子、入力はブラシレスモーターの出力を設定するためのPWM信号と、電源です。

今回は、このESCを使用しました。  
![f:id:pythonjacascript:20180822011900p:plain:h400](/images/ppythonjacascript2018082220180822011900.png "f:id:pythonjacascript:20180822011900p:plain:h400")

  
スペック：

| 最大電流 | 25A  |
| ---- | ---- |
| 電圧   | 2-4S |

  
購入元：  
<https://www.amazon.co.jp/GoolRC-スピードコントローラ-DX2205-RCレースマルチコプター-ドローンクワッドローター用/dp/B0748GGSFZ/ref=sr%5F1%5F6?ie=UTF8&qid=1534866536&sr=8-6&keywords=DX2205:h400>
  
  
### 3.ハードウェア

では、実際に制作してみます。

#### （1）半田付け

ESCとブラシレスモーターを半田付けします。  
![f:id:pythonjacascript:20180822012457j:plain](/images/ppythonjacascript2018082220180822012457.jpg "f:id:pythonjacascript:20180822012457j:plain")  
（半田付けの様子。半田付けが汚いのは気にしないでください～。）

ESC 側の３か所の端子と、ブラシレスモーターの３本の線を半田付けします。ブラシレスモーターに極性はないので、どの端子に度のコードを取り付けても回ります。  
ただし、回転方向が逆の場合は、３本のコードのうち、どれか2つのコードを逆の端子に半田付けしなおせば、逆方向に回転するはずです。  
そして、半田付け部分は、金属に触れてショートしないように、絶縁体で覆っておきましょう。（僕の場合は、熱収縮チューブがないのでセロハンテープですが。）  
  
#### （2）モーターの固定

ブラシレスモーターを高速回転させることになるので、モーターの固定はしっかり行ったほうがいいです。「DX2205」の裏側には、モーター固定用のネジを取り付ける穴が４か所あったので、そのうちに箇所を使って固定しています。

![f:id:pythonjacascript:20180822012249j:plain:h400](/images/ppythonjacascript2018082220180822012249.jpg "f:id:pythonjacascript:20180822012249j:plain:h400")  
（1ｍｍのヒノキ板を使って、裏からネジで固定。中央にもシャフト用の穴が必要。）  
  
#### （3）配線

以下のように配線します。

➀Arduino 9ピン <----> ESCから出ているコード（ピンソケット側）の白色  
②Arduino GNDピン <----> ESCから出ているコード（ピンソケット側）の黒色  
③モーター電源（マイナス） <----> ESCの黒いコード  
④モーター電源（プラス） <----> ESCの赤いコード

  
全体像です。  
![f:id:pythonjacascript:20180822011251j:plain](/images/ppythonjacascript2018082220180822011251.jpg "f:id:pythonjacascript:20180822011251j:plain")  
「➀Arduino 9ピン <----> ESCから出ているコード（ピンソケット側）の白色」の間になんとなく1ｋの抵抗器をはさんでいますが、なくても十分動作すると思います。

私は、この実験では、モーター用電源はデスクトップのATX電源を使用しました。電源電圧は12Vがほしいので、ATX電源から出ている黄色のコード（12V）をプラス、黒のコード（GND）をマイナスに使用しています。  
  
### 4.ソフトウェア

ESCを使ってブラシレスモータを扱う時は、PWM制御を使います。PWMの出力をESCに入力すると、ESCが、その duty比をもとにモーターを動かしてくれます。

ですが、ここで一つ重要なことがあります。ESCを使うには、ESCに電源投入後に、毎回PWMの設定を行わなければなりません。具体的には、duty比（正確には、パルスの長さ）の最小値と最大値を設定するのです。そして、初期設定のあとモーターを制御するためにPWM信号を送信するときには、パルス幅がその間でなければなりません。

プログラムは、この記事の一番下に置いてあります。

  
メインのPWM信号の送信方法は、

#include <Servo.h> //servoライブラリを使います。

//↓どこかの関数内：
 Servo motor;  
 motor.attach(9);  //9ピンからPWM信号を出力
 motor.writeMicroseconds(value);  //value [us]の間だけONにするようなPWM信号を出力

という感じです。  
  
  
### 5モーターを回す！！

いよいよです。次の順序で行ってください。

1. ArduinoにUSBをさし、この記事の一番下のプログラムを書き込む
2. シリアルモニターを開く
3. モーター電源をONにする → ESCからビープ音
4. シリアルモニタで何か文字を送信 → ESCからビープ音
5 .ビープ音が鳴っている間は、ESCが初期設定を行っているのでそっとしておく
6. ビープ音がやんだあと、VAL_MIN（下のプログラム参照）～VAL_MINの間で、整数をシリアルモニタで送信
7. モーターの回転速度が更新される

6 ～ 7 の繰り返し

終了時：
8. 徐々にモーターの回転数を下げるようシリアルモニタで命令し、最終的に回転を止める
9. モーター電源をOFFにする
10. ArduinoからUSBをはずす

ESCの設定手順の所が複雑ですが、この手順通りにすればうまくいくはずです。ブラシレスモーターの回転数を急に変えると、いろいろ危ない予感がします（実際に試したことがないので何とも言えませんが）。

万が一焦げ臭く感じた時などは、電源をすぐに切ってください。大電流を扱っているので大変危険です。  
  
  
### 6プログラム

/* ブラシレスモータ用。
 * モータの電源は12Vで行います。
* motor : DX2205
* ESC BLheli_s
 */

#include <Servo.h> 

Servo motor;  // create servo object to control a servo 

#define VAL_MIN 1000
#define VAL_MAX 2000

int val;    // variable to read the value from the analog pin 

void setup(){ 
  Serial.begin(9600);
  motor.attach(9);  // attaches the servo on pin 9 to the servo object 
  val = 1000;
  
  // Wait for input
  while (!Serial.available());
  Serial.read();
  Serial.println("Writing maximum output...");
  Serial.println("Turn on power source, then wait 2 seconds and press any key.");
  motor.writeMicroseconds(VAL_MAX);

  delay(3000);
  
  // Send min output
  Serial.println("Sending minimum output");
  motor.writeMicroseconds(VAL_MIN);
} 
 
void loop(){
  delay(100);
  motor.writeMicroseconds(val);
  
  if (Serial.available() > 0){
    delay(20);
    byte data_size = Serial.available();
    byte buf[data_size];
 
    Serial.print("data size:");
    Serial.println(data_size);
 
    for (byte i = 0 ; i < data_size ; i++){
      buf[i] = Serial.read() - '0';
    }
    Serial.println();

    int dub = 1;
    val = 0;
    for(byte i = 0; i < data_size; i++){
      val += buf[data_size - 1 - i] * dub;
      dub *= 10;
    }
    Serial.print(val);
  }

  val = min(VAL_MAX, val);
  val = max(VAL_MIN, val);
}
  
  
**次回**：  
このモーターにプロペラを固定して回します！！  
[shizenkarasuzon.hatenablog.com](https://shizenkarasuzon.hatenablog.com/entry/2018/08/22/212046)