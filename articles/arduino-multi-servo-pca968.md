---
title: "【Arduino】PCA9685でサーボモーターを動かす"
emoji: "🤖"
type: "tech"
topics: []
published: false
---

PCA9685を使うと、多くのサーボモーターを同時制御することができます。  
これが、PCA9685です。

![f:id:pythonjacascript:20180727010830j:plain](/images/ppythonjacascript2018072720180727010830.jpg "f:id:pythonjacascript:20180727010830j:plain")

  
### 1.ハードウェア

PCA9685と、ArduinoをI2Cという規格で通信して、サーボモータを動かしてみます。  
![f:id:pythonjacascript:20180727011614p:plain](/images/ppythonjacascript2018072720180727011614.png "f:id:pythonjacascript:20180727011614p:plain")

実際に組み立てると、このようになります。  
![f:id:pythonjacascript:20180727011501p:plain](/images/ppythonjacascript2018072720180727011501.png "f:id:pythonjacascript:20180727011501p:plain")

  
### 2.ソフトウェア

#include <PCA9685.h>     //PCA9685用ヘッダーファイル
PCA9685 pwm = PCA9685(0x41);    //PCA9685のアドレス指定（アドレスジャンパ未接続時）

#define SERVOMIN 150            //最小パルス幅 (GWS Micro2BBMG)
#define SERVOMAX 500            //最大パルス幅 (GWS Micro2BBMG)
#define SERVO_CH 0

void setup() {
 pwm.begin();                   //初期設定 (アドレス0x40用)
 pwm.setPWMFreq(50);            //PWM周期を50Hzに設定 (アドレス0x40用)
}

int n=0;

void loop() {
  servo_write(SERVO_CH, n);
  n=n+10;
  if(n>=180){
    n=0;
  }
  delay(500);  
}

void servo_write(int ch, int ang){ //動かすサーボチャンネルと角度を指定
  ang = map(ang, 0, 180, SERVOMIN, SERVOMAX); //角度（0～180）をPWMのパルス幅（150～500）に変換
  pwm.setPWM(ch, 0, ang);
}

### プログラム解説

#### ライブラリのインストール

このプログラムは、事前にPCA9685用のライブラリをインストールする必要があります。  
プログラム一行目の

PCA9685.h

というライブラリ（ヘッダーファイル）です。  
筆者は秋月電子通商のページからダウンロードしました。ライブラリの追加方法は各自で調べてください。

PCA9685のライブラリ：  
<http://akizukidenshi.com/download/ds/akizuki/PCA9685.zip>
  
  
#### アドレス設定について

PCA9685 pwm = PCA9685(0x41);  

の「0ｘ41」は、PCA9685のアドレスです。アドレスは、次の図の「Slave address」を指定します  
![f:id:pythonjacascript:20180727012039p:plain](/images/ppythonjacascript2018072720180727012039.png "f:id:pythonjacascript:20180727012039p:plain")  
A6＝1は固定ですが、 A5～A0 の値は基盤表側のジャンパワイヤを半田付けして接続することで自由に設定できます（何もしない場合は「0」、半田付けをして接続した場合は「1」になります。）

そして、「Slave address」はA6～A0の値を指定します。  
この記事の場合、A0を接続したので、アドレスは「b0100001（A6とA0のみ1）」＝「0x41」です。なにも接続しなかった場合、「b0100000（A6のみ1）」＝「0x40」になります。  
<http://wiki.sunfounder.cc/index.php?title=PCA9685%5F16%5FChannel%5F12%5FBit%5FPWM%5FServo%5FDriver>  
  
  
### 3.実行結果

サーボモータが回転する。  
筆者の環境では、サーボモータの電源（V+）は、Panasonicの充電式電池を３本させたものです。動作確認は、「TG9e」と「MG996R（正規品）」の二つのサーボモータで行いましたが、両方とも正常に動作しました。  
  
  
### 参考文献

PCA9685のデータシート  
<https://www.nxp.com/docs/en/data-sheet/PCA9685.pdf>

[Dovewill PCA9685 16チャネルPWMサーボモータドライバをArduinoで使ってみた - 極力ローコスト ロボット製作 ブログ](http://blog.robotakao.jp/blog-entry-148.html)