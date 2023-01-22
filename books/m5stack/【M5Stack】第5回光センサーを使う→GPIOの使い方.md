---
title: "【M5Stack】第5回 光センサーを使う→GPIOの使い方"
emoji: ""
type: ""
topics: []
published: false
---

* [M5Stackのライトセンサーユニットについて](#M5Stackのライトセンサーユニットについて)
* [サンプルプログラム](#サンプルプログラム)
* [実行結果](#実行結果)
* [解説](#解説)  
   * [GPIOの絶対最大定格などを調べる](#GPIOの絶対最大定格などを調べる)  
   * [初期設定](#初期設定)  
   * [デジタル出力](#デジタル出力)  
   * [アナログ出力](#アナログ出力)  
   * [デジタル入力](#デジタル入力)  
   * [アナログ入力](#アナログ入力)
  
  
### M5Stackのライトセンサーユニットについて

今回使うのは、これです↓  
![f:id:pythonjacascript:20200519024625j:plain](/images/ppythonjacascript2020051920200519024625.jpg "f:id:pythonjacascript:20200519024625j:plain")  
**CdS**（明るさによって抵抗値が変化する電子部品）を使っています。  
デジタル出力（10kΩの半固定抵抗で閾値を設定）とアナログ出力の2端子があります。

[M5Stack用光センサユニット - スイッチサイエンス](https://www.switch-science.com/catalog/4051/)  

![f:id:pythonjacascript:20200524101122p:plain](/images/ppythonjacascript2020052420200524101122.png "f:id:pythonjacascript:20200524101122p:plain")  
（[M5Stack Docs-The reference docs for M5Stack products.](https://docs.m5stack.com/#/en/unit/light) より引用）  
  
  
### サンプルプログラム

// reference = https://github.com/m5stack/M5Stack/blob/master/examples/Unit/LIGHT/LIGHT.ino
// reference2= https://docs.m5stack.com/#/en/unit/light

#include <M5Stack.h>

void setup() {
  M5.begin();
  M5.Power.begin();

  dacWrite(25, 0);  //disable the speak noise

  M5.Lcd.setTextColor(WHITE, BLACK);
  M5.Lcd.setTextSize(2);
  M5.Lcd.fillScreen(BLACK);

  pinMode(26, INPUT); //GPIOピンの26をデジタル入力に設定
  // アナログ入力はpinMode関数を呼ぶは必要ない
}

uint16_t analogRead_value = 0;
uint16_t digitalRead_value = 0;
void loop() {
  analogRead_value = analogRead(36); //0～1023までの整数でピンの電圧測定
  digitalRead_value = digitalRead(26); //デジタル入力
  
  M5.Lcd.setCursor(10, 10);
  M5.Lcd.printf("analog = %d   ", analogRead_value);
  M5.Lcd.setCursor(10, 70);
  M5.Lcd.printf("digital = %d  ", digitalRead_value);
  delay(10);
}

### 実行結果

ライトセンサを**PORT-C（I/O用ポート）**に接続します

下写真のようになります。  
![f:id:pythonjacascript:20200519125311j:plain](/images/ppythonjacascript2020051920200519125311.jpg "f:id:pythonjacascript:20200519125311j:plain")

一行目は明るさを0～4096の数値で表しており、2行目は明るかったら0、暗かったら1を出力しています。  
明るい/暗いの判定閾値は半固定抵抗のつまみを回して調整することができます  
  
  
### 解説

光センサについて解説というか、GPIOの使い方について説明します。  
  
#### GPIOの絶対最大定格などを調べる

* 3.3V駆動系

![f:id:pythonjacascript:20200524104127j:plain](/images/ppythonjacascript2020052420200524104127.jpg "f:id:pythonjacascript:20200524104127j:plain")  
引用元：[ESP32のデータシート](https://www.espressif.com/sites/default/files/documentation/esp32%5Fdatasheet%5Fen.pdf)   
  
  
#### 初期設定

他のプログラムと同じようにbegin()関数を実行します。

  M5.begin();
  M5.Power.begin();

その後、使用するGPIOの設定を行います

//GPIO26ピンをデジタル入力に設定
pinMode(26, INPUT); 

//GPIO26ピンをデジタル出力用に設定（デジタル/アナログ両方）
pinMode(26, OUTPUT); 

//GPIO26ピンをプルアップしてデジタル入力に設定
pinMode(26, INPUT_PULLUP); 

  
アナログ入力/アナログ出力として利用するときはpinMode()関数は必要ありません。

ピン番号は[この回路図](https://github.com/m5stack/M5-Schematic/blob/master/Core/Basic/M5-Core-Schematic%2820171206%29.pdf)または[このサイト](https://trac.switch-science.com/wiki/esp32%5Ftips)から調べておきます。  
  
  
#### デジタル出力

以下のように書きます

// デジタル出力用に設定した26ピンをLOW(0V)出力
digitalWrite(26, LOW); 

// デジタル出力用に設定した26ピンをHIGH(3.3V)出力
digitalWrite(26, HIGH); 

#### アナログ出力

Arduinoの場合、アナログ出力はPWMで行われていますが、ESP32（M5Stackに搭載されたマイコン）の場合はアナログ電圧が直接出てくる8bitのDACが搭載されています  
使用可能なピンは**25（DAC1)**と**26（DAC2）**ピンです

// アナログ出力用に設定した30/255の電圧で出力
dacWrite(26, 30);

// アナログ出力用に設定した26ピンを最大電圧で出力
analogWrite(26, 255);
  
  
#### デジタル入力

以下のように書きます

// 26ピンの電圧を0or1で取得
int hoge;
hoge = digitalRead(26);

  
#### アナログ入力

ESP32は最大12bitのADC（Analog Digital Converter ）を搭載しています。  
なので、ピン電圧を**0～4096**の値で測定可能です

// 26ピンの電圧を0～4096の値で取得
int hoge;
hoge = analogRead(26);

  
なお、DACの分解能は9～12bitに変更することができます（デフォルトは12bit）

/*
 * Sets the sample bits
 * Default is 12bit (0 - 4095)
 * Range is 9 - 12
 * */
// ADCのハードウエア解像度を設定。
void analogSetWidth(uint8_t bits)

bitsにはADCのハードウェア解像度を9～12の値でしていします。  
 (<https://github.com/espressif/arduino-esp32/blob/3cbc405edf2448cf1d77b0a30a5e62ddab806a85/cores/esp32/esp32-hal-adc.h#L43>)  
  

// analogRead関数の戻り値の解像度を設定。
analogReadResolution(uint8_t bits);

引数bitsには解像度（1～9）をしていします。9～12を指定したは設定されているハードウエア解像度と等しくなり、それ以外の場合には値をシフトして返却します。

| 定数  | GPIOピン |
| --- | ------ |
| A0  | 36     |
| A3  | 39     |
| A4  | 32     |
| A5  | 33     |
| A6  | 34     |
| A7  | 35     |
| A10 | 4      |
| A11 | 0      |
| A12 | 2      |
| A13 | 15     |
| A14 | 13     |
| A15 | 12     |
| A16 | 14     |
| A17 | 27     |
| A18 | 25     |
| A19 | 26     |

  
（参考＝[esp32\_tips – スイッチサイエンス](https://trac.switch-science.com/wiki/esp32%5Ftips#ADC%E3%82%A2%E3%83%8A%E3%83%AD%E3%82%B0%E5%85%A5%E5%8A%9B)）

> ただし、Wi-Fiなどの無線を利用する場合にはADC2が利用できなくなります。  
> 一度でも無線を利用すると、再起動するか内部レジスタを書き換えない限りADC2は利用できないので、注意してください。

([ESP32のGPIO研究 – Lang-ship](https://lang-ship.com/blog/work/esp32-gpio/#%E3%82%A2%E3%83%8A%E3%83%AD%E3%82%B0%E5%85%A5%E5%8A%9B))