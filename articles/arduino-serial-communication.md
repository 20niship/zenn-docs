---
title: "【Arduino】シリアル通信の方法"
emoji: ""
type: ""
topics: []
published: false
---

Arduino には、Serial というシリアル通信用のライブラリが入っています。そのライブラリを使ってPC - Arduino や、Arduino - Arduino 間でシリアル通信を使ってデータを送受信することができます。

* [1.それぞれの関数](#1それぞれの関数)  
   * [begin()](#begin)  
   * [print()](#print)  
   * [println()](#println)  
   * [write()](#write)  
   * [abailable()](#abailable)  
   * [readStringUntil()](#readStringUntil)  
   * [setTimeout()](#setTimeout)
* [プログラム達](#プログラム達)  
   * [一秒ごとに自然数をひとつずつ送信するプログラム](#一秒ごとに自然数をひとつずつ送信するプログラム)  
   * [受信した文字列をそのまま送信する](#受信した文字列をそのまま送信する)  
   * [受信した文字の種類でLEDを点灯・消灯させる](#受信した文字の種類でLEDを点灯消灯させる)
* [2.Arduinoのシリアル通信に関する記事](#2Arduinoのシリアル通信に関する記事)
* [3.参考文献](#3参考文献)

### 1.それぞれの関数

#### begin()

 **構文：Serial.begin(bitrate);**

シリアル通信を開始します。bitrate には、**シリアル通信の速度（bit/second）**を指定します。Arduino 同士で接続する場合は、ビットレートを任意の値（整数）に変えることができます。PCとArduinoで通信をする場合は、ビットレートは**300, 1200, 2400, 4800, 9600, 14400, 19200, 28800, 38400, 57600, 115200** の中から選んでください。  
勿論、送信側と受信側のビットレートは同じにします。  
  
  
#### print()

**構文1：Serial.print(文字列);**  
**ASCII形式**でデータをシリアルポートへ出力します。  
<http://www3.nit.ac.jp/~tamura/ex2/ascii.html>

数値は1桁ずつASCII文字に変換されます。浮動小数点数の場合は、小数点以下第2位まで出力するのがデフォルトの動作です。バイト型のデータは1文字として送信されます。文字列はそのまま送信されます。 

Serial.print(78)  // "78"が出力
Serial.print("Hello world.") // "Hello world."

  
**構文2：Serial.print(文字列, format);**  
formatで送信する文字列の種類を指定します。**「BIN」→2進数、「OCT」→8進数、DEC→「10進数」、「HEX」→16進数**

Serial.print(78, BIN) - "1001110"が出力されます。 
Serial.print(78, OCT) - "116"が出力されます。 
Serial.print(78, DEC) - "78"が出力されます。 
Serial.print(78, HEX) - "4E"が出力されます。 

しかし、format を変更しても、送信される文字列はあくまで**ASCIIコードに基づいています。**
  
  
#### println()

**ASCII形式**でデータをシリアルポートへ出力します。**データの最後に改行（ASCIIでは、「CR（0x0d）」と「LF（0x0a）」**が付けられます。

数値は1桁ずつASCII文字に変換されます。浮動小数点数の場合は、小数点以下第2位まで出力するのがデフォルトの動作です。バイト型のデータは1文字として送信されます。文字列はそのまま送信されます。 

Serial.print("Hello")
Serial.println(" world.") 
Serial.println("Yeah!!") 

と書くと、

Hello world.
Yeah!!

と表示されます。「Serial.ptint()関数は改行はないので「Hello」と「world」は同じ行に表示されています。  
  
  
#### write()

**構文：Serial.write(data);**

**1バイトの数値データ（構文中でのdata）を送信**します。  
print()関数などとの違いは、**ASCII文字としてではなく、数値として送られる**ことです。  
例えば

Serial.write(3); 

と書けば、ASCII文字列の「”3”」つまり（**ｂ110011 1010 1010**）ではなく、二進数の3（**b0000 0011**）と送られます。  
  
  
#### abailable()

**構文：int data\_num = Serial.available()**

シリアル通信で**受信したデータのバイト数を取得**します。シリアル通信を行うことができないときは -1 が返されます。

例えば、

while (Serial.available() <= 0)
Serial.println("data received");  

と書くことで、データが送信されたら「data received」と送信するプログラムを書くことができます。  
  
  
#### readStringUntil()

ある一定の文字まで受信データを読み込みます。

例えば、

String data = Serial.readStringUntil("\n");

というプログラムの場合、「\\n」が受信されるまでデータを受信し続けてその結果をdataに格納します。  
また、タイムアウト機能もあり、一定時間「\\n」が受信されなければタイムアウトとしてデータの受信を中断し、次の処理に進みます。タイムアウト時間はる setTimeout 関数で決定できます。  
  
#### setTimeout()

**構文：Serial.setTimeout(ms);**  
readStringUntil関数のタイムアウト時間を指定します。単位はｍｓで、初期値は1000msとなっています。  
  
  
### プログラム達

#### 一秒ごとに自然数をひとつずつ送信するプログラム

int i = 0;
void setup() {
  // ビットレート9600でシリアル通信を開始
  Serial.begin(9600);
}

void loop() {
  i++;
  
  //値を送信
  Serial.println(i);
  
  //一秒待つ
  delay(1000);
}

  
実行すると、

1
2
3

...と表示されます。  
  
  
#### 受信した文字列をそのまま送信する

void setup() {
  // ビットレート9600でシリアル通信を開始
  Serial.begin(9600);
  pinMode(LED_PIN, OUTPUT);
}

void loop() {
  int data;
  while (Serial.available() <= 0)
  data = Serial.read();
  Serial.print("received data ->"); 
  Serial.println(data);
}

 Arduinoに何か文字列を送信すると、そのまま文字列が返ってきます。  
  
  
#### 受信した文字の種類でLEDを点灯・消灯させる

#define LED_PIN 13

void setup() {
  // ビットレート9600でシリアル通信を開始
  Serial.begin(9600);
  pinMode(LED_PIN, OUTPUT);
}

void loop() {
  while (Serial.available() <= 0)
  
  switch(Serial.read()){
    case 'y':
      // 「ｙ」を受信
      digitalWrite(LED_PIN, HIGH);
      break;
    case 'n':  
      // 「ｎ」を受信
      digitalWrite(LED_PIN, LOW);
      break;
  }
}

Arduinoに「y」を送信すると13ピンのLEDが点灯し、「ｎ」を送信するとLEDが消灯するプログラムです。  
  
  
### 2.Arduinoのシリアル通信に関する記事

[【Arduino】PC と Arduino でシリアル通信 - とある科学の備忘録](https://shizenkarasuzon.hatenablog.com/entry/2018/07/24/225048)

[【Arduino】２台のAruduinoをシリアル通信させる - とある科学の備忘録](https://shizenkarasuzon.hatenablog.com/entry/2018/07/24/225915)

[【Arduino】シリアル通信で複数のデータをやり取りする - とある科学の備忘録](https://shizenkarasuzon.hatenablog.com/entry/2018/08/13/133406)

[【Arduino】SoftwareSerial を使って、複数のデバイスとシリアル通信を行う - とある科学の備忘録](https://shizenkarasuzon.hatenablog.com/entry/2018/09/10/093648)

  
### 3.参考文献

[Arduino 日本語リファレンス](http://www.musashinodenpa.com/arduino/ref/)