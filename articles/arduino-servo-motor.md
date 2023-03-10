---
title: "【Arduino】Arduinoでサーボモーターを制御する"
emoji: "🤖"
type: "tech"
topics: []
published: false
---

Arduinoを使ってDCサーボモータを動かしてみます。

* [1.サーボモーターとは](#1サーボモーターとは)
* [2.回路図](#2回路図)
* [3.サンプルプログラム](#3サンプルプログラム)
* [4.実行結果](#4実行結果)
* [5.解説](#5解説)  
   * [STEP1.初期設定](#STEP1初期設定)  
   * [STEP2.回転](#STEP2回転)

### 1.サーボモーターとは

※「サーボモータ」には「ACサーボモータ」と「DCサーボモータ」がありますが、  
一般的に電子工作や個人的な作品において「サーボモータ」という場合、「**DCサーボモータ**」をさします。

![f:id:pythonjacascript:20190127071103j:plain](/images/ppythonjacascript2019012720190127071103.jpg "f:id:pythonjacascript:20190127071103j:plain")  
サーボモータは、このような外観をしています。

  
**サーボモーター**とは、「**サーボ機構**」を用いて位置や回転速度を制御することができるモーターです。  
通常の「DCブラシモーター」などは電圧で回転数やトルクを変更することができますが、「制御」と言えるほど精密なものではありません。

一方、サーボモータには回転角を読み取る検出器（センサー）がついており、その検出された値をフィードバックすることで、回転角や回転速度を正確に制御できます。

そのため、通常のサーボモーターは180度程度しか回転できません。（

因みに、位置や回転速度を制御することができるモーターには「ステッピングモーター」もあります。しかし、ステッピングモーターには検出器がついていないため、**脱調し**た場合角度の特定ができなくなります。

ステッピングモーターについてはこちらの記事をご覧ください。  
[shizenkarasuzon.hatenablog.com](https://shizenkarasuzon.hatenablog.com/entry/2018/11/04/234350)  
  
  
### 2.回路図

下のような回路を組んでください。  
![f:id:pythonjacascript:20190127074507j:plain](/images/ppythonjacascript2019012720190127074507.jpg "f:id:pythonjacascript:20190127074507j:plain")  
（あまり電流を必要としないサーボの場合）

  
私は、Tg9eという小さいサーボモーターを使用しました。  
[Turnigy TG9e Servo Specifications and Reviews](https://servodatabase.com/servo/turnigy/tg9e)

そのため、USBから電源を供給しても、なんとか回ってくれました。

しかし、MG996等の大きめのサーボモータになると、大電流が必要になるため、サーボ側の電源をもう少し強いものにした方がよいかもしれません。

![f:id:pythonjacascript:20190127074922j:plain](/images/ppythonjacascript2019012720190127074922.jpg "f:id:pythonjacascript:20190127074922j:plain")  
（サーボ駆動に大電流を使う場合）

  
このようなATX電源等を使っておけば最強です。  
[shizenkarasuzon.hatenablog.com](https://shizenkarasuzon.hatenablog.com/entry/2019/01/19/101143)

サーボに流れる電流は各自データシートを読んで調べたほうが良いです。

  
サーボモータからは、電源－、電源＋、信号線の3種類のコードが出ています。  
多くのサーボは、それぞれのコードに対して色が決まっており、  
![f:id:pythonjacascript:20190127080149j:plain](/images/ppythonjacascript2019012720190127080149.jpg "f:id:pythonjacascript:20190127080149j:plain")

| コードの色   | 用途       |
| ------- | -------- |
| 黒・茶色    | 電源－（GND) |
| 赤色      | 電源＋      |
| 黄色、オレンジ | 信号線      |

という仕様が多いです。  
ですが、念のためデータシートでの確認をお願いします。  
  
### 3.サンプルプログラム

#include <Servo.h>
Servo myservo;

void setup() {
  //9ピンからサーボモーターの回転信号をPWM出力
  myservo.attach(9); 
}

void loop() {
  myservo.write(0); //0度に回転
  delay(1000);      //1000㎳待つ
  
  myservo.write(90);
  delay(1000);
  
  myservo.write(180);
  delay(1000);
  
  myservo.write(90);
  delay(1000);
}

以下のプログラムをArduinoに書き込んで実行してください。  
  
### 4.実行結果

電源を入れると、  
サーボモータが0度に回転  
→1秒後、90度に回転  
→さらに1秒後、180度に回転  
→さらに1秒後、90度に戻る  
→さらに1秒後、0度に戻る  
→1秒後、90度に回転

...(以下無限ループ）

を繰り返すはずです。  
  
  
### 5.解説

以下は、↑のコードの簡単な解説です。

#### STEP1.初期設定

Arduinoを使ってサーボモータを回転させるには、「Servo.h」というファイル（ライブラリ）をインクルードする必要があります。

#include <Servo.h>
Servo myservo;

PWM機能を使って独自でライブラリを開発することもできますが、それはまた別の機会にします。

Servoライブラリをインクルードすると、新しくmyservoというクラスを作っておきます。

そして、回転命令を出力するピンを指定します。

myservo.attach(9); 

と書くと、9ピンから信号が出力されます。

また、attach()関数を実行すると、設定したピンはI/Oピンとして使えなくなります。  
  
#### STEP2.回転

  myservo.write(degree);

でdegre度に回転させることができます。

指定した角度に到達するまで時間がかかるので、↑のプログラムでは回転命令を出すごとに1秒間待つようにしています。

  
上記以外にも、サーボの現在角度を読み取ったり、サーボのピン設定を解除したりする関数があるので、  
詳しく知りたい方は以下のサイトを見てください。  
[Arduino - Servo](https://www.arduino.cc/en/reference/servo)