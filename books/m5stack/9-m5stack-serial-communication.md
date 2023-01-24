---
title: "【M5Stack】第8回 シリアル通信　全集"
emoji: ""
type: ""
topics: []
published: false
---

PCとM5StackをUSBで接続して、シリアル通信を行ってみます

* [サンプルプログラム](#サンプルプログラム)
* [実行方法](#実行方法)
* [実行結果](#実行結果)
* [解説](#解説)  
   * [シリアル通信について](#シリアル通信について)  
   * [初期化](#初期化)  
   * [送信（M5Stack→PC）](#送信M5StackPC)  
         * [文字列送信](#文字列送信)  
         * [数値送信（n進数）](#数値送信n進数)  
         * [数値送信（桁数指定）](#数値送信桁数指定)  
         * [uint8\_t型のデータを1バイト送信](#uint8%5Ft型のデータを1バイト送信)  
         * [uint8\_t型のデータをｎバイト送信](#uint8%5Ft型のデータをｎバイト送信)  
   * [受信（PC→M5Stack）](#受信PCM5Stack)  
         * [何バイト受信したか調べる](#何バイト受信したか調べる)  
         * [データを1バイト読み込む](#データを1バイト読み込む)  
         * [データをnバイト読み込む](#データをnバイト読み込む)  
         * [ある文字を受信するまで読み込む](#ある文字を受信するまで読み込む)  
         * [タイムアウト時間を設定](#タイムアウト時間を設定)

### サンプルプログラム

#include <M5Stack.h>

void setup() {
  M5.begin(true, false, true);
  M5.Power.begin();

  Serial.begin(115200);//ビットレート設定 

  M5.Lcd.fillScreen(BLACK);
  M5.Lcd.setTextColor(WHITE, BLACK);
  M5.Lcd.setTextSize(2);
}

void repeat_string(){
  if ( Serial.available() > 0 ) {
    String str = Serial.readStringUntil('\n');
    Serial.print("recieved -> ");
    Serial.println(str);
  }
}

void SendTest(){
  Serial.print("BIN->"); 
  Serial.print(10, BIN); // 2進数の10である"1010"が文字列として送信される
  Serial.print("\nOCT->"); 
  Serial.print(10, OCT); // 8進数の10である"12"が文字列として送信される
  Serial.print("\nDEC->"); 
  Serial.print(10, DEC); // 10進数の10である"10"が文字列として送信される
  Serial.print("\nHEX->"); 
  Serial.print(10, HEX); // 16進数の10である"A"が文字列として送信される
  Serial.print("\n"); 
  delay(1000);
}


void repeat_byte(){
  int recieved_byte_size = Serial.available();
  if(recieved_byte_size <= 0) return;
  
  Serial.print("byte size ->"); 
  Serial.println(recieved_byte_size, DEC);
  for(int i=0;i<recieved_byte_size;i++){
    char received_data = Serial.read();
    Serial.print("received data ->"); 
    Serial.print(received_data, DEC);
    Serial.print("\n"); 
  }
}

void display_recieved_data(){
  #define MAX_SIZE 100
  int recieved_byte_size = Serial.available();
  char recieved_data[100];
  
  for(int i=0;i<recieved_byte_size;i++){
    recieved_data[i] = (char)Serial.read();
  }
  recieved_data[recieved_byte_size] = 0; //終端文字
  
  //描画！
  M5.Lcd.setCursor(0,100);
  M5.Lcd.printf("%s", recieved_data);
}

int serial_status = 0;

void loop() {
  M5.update();// update button state

  M5.Lcd.setCursor(0,0);
  if (M5.BtnA.wasPressed()) {
    serial_status = 0;
    M5.Lcd.print("button A \n-> repeat_string() ");
  } 
  if (M5.BtnB.wasPressed()) {
    serial_status = 1;
    M5.Lcd.print("button B \n-> SendTest()      ");
  }

  if (M5.BtnC.wasPressed()) {
    serial_status = 2;
    M5.Lcd.print("button C \n-> repeat_byte()    ");
  }

  switch(serial_status){
  case 0:
    repeat_string();
    break;
  case 1:
    SendTest();
    break;
  case 2:
    repeat_byte();
    //display_recieved_data();
    break;
  }
}

### 実行方法

まず、↑のプログラムを書きこんだM5StackをPCと接続します。  
![f:id:pythonjacascript:20200519030019j:plain](/images/ppythonjacascript2020051920200519030019.jpg "f:id:pythonjacascript:20200519030019j:plain")  
  
するとM5Stackの電源が自動的に入ります。

そして、PCのArduino IDEを立ち上げ、右上の**虫眼鏡マーク（シリアルモニタ）**をクリックします。  
すると、このようなシリアルモニタが表示されるはずです。

![f:id:pythonjacascript:20200623120008j:plain](/images/ppythonjacascript2020062320200623120008.jpg "f:id:pythonjacascript:20200623120008j:plain")  
この右下の「115200bps」と書いてある部分で、PC側のシリアル通信のビットレートを設定します。M5Stackのプログラムで指定した値（Serial.begin関数の引数）と同じ値にしてください。  
PCからM5Stackに文字列を送信するには、画面上のエディトボックスに送信したい文字列を入力して、右上ボタンの「送信」を押せばOKです。  
  
### 実行結果

M5StackのボタンA～ボタンCを押して、各動作を確かめてください。

※ボタンA～ボタンCとはM5StackのLCD下部のボタンのこと：  
![f:id:pythonjacascript:20200519113814j:plain](/images/ppythonjacascript2020051920200519113814.jpg "f:id:pythonjacascript:20200519113814j:plain")  
  
次のような動作をするはずです

* ボタンA→文字列をPCからM5Stackに送信すると、その文字列がM5Stackから送られてきてシリアルモニタに表示される
* ボタンB→10が二進数、8進数、10進数、16進数で表示される
* ボタンC→文字列をPCからM5Stackに送信すると、その文字列のバイト列がM5Stackから送られてきてシリアルモニタに表示される

### 解説

#### シリアル通信について

シリアル通信とは、一度に1ビットずつ逐次的にデータを送るデータ通信方法です。パラレル通信の逆ですね。

そして、シリアル通信を行うのに必要な集積回路として、**UART (Universal Asynchronous Receiver/Transmitter）**や**USART（Universal Synchronous Asynchronous Receiver Transmitter ）**があります。  
UARTとUSARTの違いは同期式シリアル通信に対応しているかしていないかの違いで、対応しているのがUSART、対応していないのがUARTです。USARTのSがシンクロのSってことかぁ。

  
M5Stack（ESP32）では、以下のようなUARTを利用することができます。

* Serial : USB接続した場合にPCと通信するシリアル
* Serial1 : 2系統目のシリアル
* Serial2 : 3系統目のシリアル
* BluetoothSerial : BlueToothを使ってワイヤレスなシリアル通信を行う

一つ目のSerialは、M5StackのUSB-TypeC端子とPCを繋ぐことで通信できます。  
なので、今回使うのは一つ目のSerialです。シリアル通信用のライブラリSerialがあるのでそれを使って実装しています。  
  
  
#### 初期化

シリアル通信を使うには、setup()関数内で以下の様にシリアル通信の設定を行う必要があります。

 // M5Stack オブジェクトの初期化
M5.begin(true, false, true); //M5.begin();でも良い

//Power chipがgpio21, gpio22, I2Cにつながれたデバイスに接続される。
//バッテリー動作の場合はこの関数を読んでください（バッテリーの電圧を調べるらしい）
M5.Power.begin(); 

//ビットレート設定 
Serial.begin(115200);

  
最初の二つは[第一回で既出](https://shizenkarasuzon.hatenablog.com/entry/2020/05/21/012143)なので、3つ目の**Serial.begin**関数について説明すると、

Serial.begin(int speed)

シリアル通信の通信速度を指定します。単位は**bps(ビット/秒)**です。  
**300, 1200, 2400, 4800, 9600, 14400, 19200, 28800, 38400, 57600, 115200**  
のいずれかを指定してください。

  
通信速度を速くすれば短時間に多くのデータを送ることができますが、その分エラーの確率が増えるので、スピード重視でない時は9600などでも十分だと思います（9600のサンプルコードが結構あったため）  
  
  
#### 送信（M5Stack→PC）

まずは、M5StackからPCにデータを送信する関数から紹介します

##### 文字列送信

文字列を1文字ずつASCII文字に変換して送信します。  
文字列の最後にはNULL文字が加えられます。

Serial.print("Hello, World!");
Serial.println("Hello, World!");

##### 数値送信（n進数）

Serial.print(data, format)

  
と書くことで、数値をASCII文字列に変換して送信することができます。  
formatには、何進数かを指定します。

  Serial.print(10, BIN); // 2進数の10である"1010"が文字列として送信される
  Serial.print(10, OCT); // 8進数の10である"12"が文字列として送信される
  Serial.print(10, DEC); // 10進数の10である"10"が文字列として送信される
  Serial.print(10, HEX); // 16進数の10である"A"が文字列として送信される

  
この関数で送られるのは文字列の「1010」や「A」であって、通信データの中身のバイトが b0000 1010 であったり 0x0A であったりするわけではないです。  
  
##### 数値送信（桁数指定）

float型のデータを送信する場合、第二引数に有効桁数を指定することができます。

Serial.println(1.23456, 0) - "1"が出力されます。
Serial.println(1.23456, 2) - "1.23"が出力されます。
Serial.println(1.23456, 4) - "1.2346"が出力されます。

##### uint8\_t型のデータを1バイト送信

Serial.write(45); // send a byte with the value 45

  
write()関数を使うことによって、バイナリデータを出力することができます。

"4"と"5"はそれぞれASCII文字列では 34, 35 なので、

Serial.print(45);

と

Serial.write(34); 
Serial.write(35);

は、同じような意味を持ちます。  
  
##### uint8\_t型のデータをｎバイト送信

uint8_t data[] = {10, 20, 30, 40};
Serial.write(data, 4); 

  
のように書くこともできます。  
  
  
#### 受信（PC→M5Stack）

##### 何バイト受信したか調べる

int size_int_bytes = Serial.available();

シリアルポートに何バイトのデータが到着しているかを返します。バッファは64バイトまで保持できます。  
後述するSerial.read関数でデータを読み込むと、その分だけSerial.availableの帰ってくる値は小さくなります。

何らかのデータを受け取った時に○○という処理を行う、という場面で、下のようなスケッチをよく見かけます。

if (Serial.available() > 0) { // 受信したデータが存在する
    incomingByte = Serial.read(); // 受信データを読み込む
    Serial.print("I received: "); 
    //色々処理
}

##### データを1バイト読み込む

int recieved_data = Serial.read(); // 受信データを1バイト読み込む

  
受信データのストックから1バイト読み込みます。

* 受信データがすでにある場合はその値を0～255で返します。
* もし受信データがない場合（→Serial.available() == 0の場合）は一定時間（setTimeout関数で待ち時間の設定可。詳細はこの記事の最下部）待ち、それでも何も受信しない場合は-1を返します

##### データをnバイト読み込む

上の節のSerial.read関数とfor文を使って次のように書けます

#define MAX_SIZE 100
int recieved_byte_size = Serial.available();
char recieved_data[100];

for(int i=0;i<recieved_byte_size;i++){
  recieved_data[i] = (char)Serial.read();
}
recieved_data[recieved_byte_size] = 0; //終端文字

//描画！
M5.Lcd.printf("%s", recieved_data);

##### ある文字を受信するまで読み込む

String str = Serial.readStringUntil('\n');
// 構文：String Serial.readStringUntil(char terminator);

  
上のプログラムでは"\\n"、すなわち改行に値する文字を受信するまでの文字列一覧をstrに格納します。  
もし、一定時間待っても"\\n"文字が現れない場合はタイムアウトしてreturnします。

タイムアウトする時間は、setTimeout関数で指定することができます。  
  
##### タイムアウト時間を設定

Serial.setTimeout(10); //10ms待ってもデータが来ない場合はメイン処理に返すように設定

シリアルデータ受信の最大待ち時間（ミリ秒）を設定する。  
デフォルト値は1000ミリ秒