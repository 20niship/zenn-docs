---
title: "【Arduino】LED点滅（Lチカ）"
emoji: "🤖"
type: "tech"
topics: []
published: false
---

まずは、Lチカですね。LEDは13ピンとGNDの間に入れました。

* [1\. ハードウェア](#1-ハードウェア)
* [2\. プログラム](#2-プログラム)  
   * [※解説](#解説)
* [3\. 実行結果](#3-実行結果)

### 1\. ハードウェア

Arduinoには、ボード上に一つ、LEDが埋め込まれています。そして、そのLEDは13ピンとつながっているので、何も接続しなくても、Arduinoボード上のLEが  
点滅します。

しかし、ArduinoにLEDを接続したい！、という人は、次のようにしてください。  
![f:id:pythonjacascript:20180724224453j:plain](/images/ppythonjacascript2018072420180724224453.jpg "f:id:pythonjacascript:20180724224453j:plain")

LEDの向きに注意してください。抵抗値は適当でいいですが、筆者は手元にあった1ｋを使っています。LEDのデータシートを見ながら抵抗値を決めるのが安全なので、そうしたほうがいいです。  
![f:id:pythonjacascript:20180724224447p:plain](/images/ppythonjacascript2018072420180724224447.png "f:id:pythonjacascript:20180724224447p:plain")

  
### 2\. プログラム

以下のようなプログラムをArduinoIDEで書いて、「（ファイル名）.ino」で保存して、実行してください。

const int LED = 13;  //LED pin

void setup() {
  pinMode(LED, OUTPUT); //set LED pin as output pin
}

void loop() {
  digitalWrite(LED, HIGH);  //LEDをONにする
  delay(1000);   //ここの値ごとにLEDが明るくなったり暗くなったりする
 digitalWrite(LED, LOW); //LEDをOFFにする
  delay(1000);
}

#### ※解説

delay(1000)は、1000ミリ秒（＝1秒）、Arduinoの動作停止するプログラムです。また、digitalWrite(); は、ピンの出力を変更する関数です。digitalWrite(13, HIGH);とすれば、13ピンの出力がHIGH(＝ON)になり、反対にdigitalWrite(13, LOW);とすれば、13ピンの出力がLOW(＝OFF)になります。

また、digitalWriteを使うには、そのピンを出力に設定しなければなりません。  
（Arduinoはピンを入力用に使ったり、出力用に使ったりできる。また、出力の中でも、デジタル出力とアナログ出力（PWM）があります。）  
ピンのモードを設定するには、pinModeを使います。これは、setup()関数の中に書いてください。  
  
  
### 3\. 実行結果

LEDが一秒ごとに点滅する