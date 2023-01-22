---
title: "【Arduino】Arduino と python でシリアル通信"
emoji: ""
type: ""
topics: []
published: false
---

Pythonには、pyserialというシリアル通信専用のライブラリがあります。  
[Welcome to pySerial’s documentation — pySerial 3.0 documentation](https://pythonhosted.org/pyserial/)  
この記事では、そのpyserialというライブラリを使って、Arduino UNO と PC（Pythonを使用）をシリアル通信してみたいと思います。  
  
* [1.pyserialのインストール](#1pyserialのインストール)
* [2.pyserialの使い方](#2pyserialの使い方)  
   * [1.COMポートを開く](#1COMポートを開く)  
   * [2.COMポートを閉じる](#2COMポートを閉じる)
* [2.文字の送信（一文字ずつ）](#2文字の送信一文字ずつ)
* [3.文字列受信](#3文字列受信)
* [4.byte型のデータを送信](#4byte型のデータを送信)
* [5.byte型のデータを受信](#5byte型のデータを受信)

### 1.pyserialのインストール

pip install pyserial

を実行します。  
下のプログラムで

import serial

と書いていますが、**モジュールの名前は「pyserial」**です。  
**「pip install serial」を実行すると別のモジュールがインストールされて、シリアル通信できない**ので注意してください。

pyserial の代わりに serial をインストールすると、こうなるようです。  
[stackoverflow.com](https://stackoverflow.com/questions/33267070/no-module-named-serial)

### 2.pyserialの使い方

#### 1.COMポートを開く

Pythonではこのように書きます。

import serial
ser = serial.Serial('COM3', 115200, timeout=0.1) 

**ArduinoをつなげたCOMポートの名称、ビットレート、タイムアウト時間**の順で指定します。

もし間違ったCOMポートを指定すると、下のようなエラーが出ます。

SerialException: could not open port 'COM5': PermissionError(13, 'アクセスが拒否されました。', None, 5)
  
  
#### 2.COMポートを閉じる

COMポートを閉じるにはこのように書きます。

ser.close()

上のプログラムでserとは、「**1.COMポートを開く**」のプログラムの **ser = serial.Serial(...)** のserです。  
ポートをクローズしないと、次回使う時にポートが開けずにエラーが出てしまうので、必ず **ser.close()** をどこかで実行するようにしてください。

また、Python特有のwithを使って次のように書くこともできます。

with serial.Serial('/dev/ttyS1', 19200, timeout=1) as ser:
    x = ser.read()  

このように書くと、**ser.close()** と書かなくてもwithの中を抜けると、勝手にポートを閉じてくれます。
  
  
### 2.文字の送信（一文字ずつ）

PC（Python）→ Arduino に文字「a」を送信してみます。

**Pythonのプログラム**

import serial

ser = serial.Serial(COM5, 115200, timeout = 0.1)
ser.write("a") 
ser.close()
 (以下省略）

  
**Arduinoのプログラム**

void setup(){
  Serial.begin(115200);
}

void loop(){
  int data= Serial.read();
 (以下省略）
}
  
  
### 3.文字列受信

Arduino→PC（Python）に文字列を送信します。（Python側は、文字列を受信します。）

**Pythonのプログラム**

import serial

ser = serial.Serial(COM5, 115200, timeout = 0.1)
data = ser.read_all() 
ser.close()
 (以下省略）

**Arduinoのプログラム**

void setup(){
  Serial.begin(115200);
}

void loop(){
  Serial.println("hello");
 (以下省略）
}

Pythonのプログラムの中の、ser.read\_all()関数は、その名の通りシリアル通信で受信したデータをすべて読み込む関数です。読み込んだ文字列は、dataに格納されています。
  
  
### 4.byte型のデータを送信

PC（Python）→ Arduinoに**byte型のデータ**を送信します。  
Arduinoの Serial.write() 関数のPython版です。

**Pythonのプログラム**

import serial

ser = serial.Serial(COM5, 115200, timeout = 0.1)
ser.write(bytes([123]))
ser.close()
 (以下省略）

**Arduinoのプログラム**

void setup(){
  Serial.begin(115200);
}

void loop(){
   byte data = (byte)Serial.read();
 (以下省略）
}

### 5.byte型のデータを受信

Arduino→PC（Python）に**byte型のデータ**を送信します。（Python側は、byte型の数値を受信します。）

**Pythonのプログラム**

import serial

ser = serial.Serial(COM5, 115200, timeout = 0.1)
String_data = ser.read()
int_data = int.from_bytes(String_data , 'big')
ser.close()
 (以下省略）

**Arduinoのプログラム**

void setup(){
  Serial.begin(115200);
}

void loop(){
   Serial.write(123);
 (以下省略）
}