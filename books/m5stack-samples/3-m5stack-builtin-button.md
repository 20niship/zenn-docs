---
title: "【M5Stack】第3回  LCD下部のボタンの状態を取得する"
emoji: ""
type: ""
topics: []
published: false
---

* [この記事の内容](#この記事の内容)
* [サンプルプログラム](#サンプルプログラム)
* [実行結果](#実行結果)
* [解説](#解説)  
   * [ボタンの配線](#ボタンの配線)

### この記事の内容

M5StackにはLCDの下に3つのボタンがあります。それぞれ左からBtnA、BtnB、BtnCという風にプログラムでは使います。  
![f:id:pythonjacascript:20200519113814j:plain](/images/ppythonjacascript2020051920200519113814.jpg "f:id:pythonjacascript:20200519113814j:plain")  
この記事では、この3つのボタンの状態を取得するプログラムを紹介します。  
  
### サンプルプログラム

#include <M5Stack.h>

//reference = https://lang-ship.com/reference/unofficial/M5StickC/Class/Button/

void setup() {
  // init lcd, serial, but don't init sd card
  M5.begin(true, false, true);

   //Power chipがgpio21, gpio22, I2Cにつながれたデバイスに接続される。
  //バッテリー動作の場合はこの関数を読んでください（バッテリーの電圧を調べるらしい）
  M5.Power.begin();

  M5.Lcd.fillScreen(BLACK);
  M5.Lcd.setTextColor(WHITE, BLACK);
  M5.Lcd.setTextSize(3);
}


void loop() {
  M5.update();// update button state
   
  M5.Lcd.setCursor(0,0);
  if (M5.BtnA.wasPressed()) {
    M5.Lcd.printf("BtnA pressed!");
  } else if (M5.BtnA.wasReleased()) {
    M5.Lcd.print("BtnA Released!");
  }else{
    M5.Lcd.print("BtnA None     ");
  }
  

  M5.Lcd.setCursor(0,60);
  if (M5.BtnB.isPressed()) {
    M5.Lcd.printf("BtnB pressed!");
  } else if (M5.BtnB.isReleased()) {
    M5.Lcd.print("BtnB Released!");
  }else{
    M5.Lcd.print("BtnB None     ");
  }
  
   
  M5.Lcd.setCursor(0, 120);
  if (M5.BtnC.pressedFor(1000)) {
    M5.Lcd.print("BtnC pressed for 1sec  ");
  }else if(M5.BtnC.releasedFor(1000)){
    M5.Lcd.print("BtnC released for 1sec ");
  }else{
    M5.Lcd.print("BtnC None              ");
  }
}

### 実行結果

![f:id:pythonjacascript:20200519114002j:plain](/images/ppythonjacascript2020051920200519114002.jpg "f:id:pythonjacascript:20200519114002j:plain")  
以下の様にLCD今のボタンの状態が表示されます。BtnA、BtnB、BtnCでは微妙にプログラムが異なっていて。

* BtnA = 押された瞬間（話された瞬間）のみ「pressed」「released」の表示が出る
* BtnB = 押されている間はずっと「pressed」、離されている間はずっと「released」
* BtnC = 1秒長押しされた場合は「pressed」、ボタンが離されてから1秒以上たったら「released」になる

という動きをするようになっています  
  
  
### 解説

M5StackにはLCDディスプレイの下に3つのボタンがあり、左から順にBtnA、BtnB、BtnCとプロ御グラム名では表記しています。

それぞれの状態を取得する関数は以下のようなものがあります

| 関数名                      | 説明                                             |
| ------------------------ | ---------------------------------------------- |
| isPressed()              | 現在ボタンが押されていれば1、そうでなければ0を返す                     |
| isReleased()             | 現在ボタンが押されていなければ1、そうでなければ0を返す                   |
| wasPressed()             | 前回wasPressed()が呼ばれた後にボタンが押されていれば1、そうでなければ0     |
| wasReleased()            | 前回wasReleased()が呼ばれた後にボタンが離されていれば1、そうでなければ0    |
| pressedFor(uint32\_t ms) | \[ms\]ミリ秒以上ボタンが長押しされていれば1を返す。長押しされていなければ0      |
| pressedFor(uint32\_t ms) | ボタンが話されてから\[ms\]ミリ秒以上経過していれば1を返す。それ以外の状態では0を返す |

返す方は全て**uin8\_t** 型です。

これらの関数は、

  if (M5.BtnB.isPressed()) {　//以下略.....

のようにして、M5.BtnX.\[関数名\]の形で使用します。

尚、これらの関数を呼ぶ前にupdate()関数を実行してボタンの状態を更新する必要があります。

M5.update(); // ボタンの状態更新
  
  
#### ボタンの配線

BtnA～Cは3.3V電源にプルアップされた状態でつながっています。  
ESP32（マイコン）にはGPIO37～39でつながっています  
![f:id:pythonjacascript:20200519115213j:plain](/images/ppythonjacascript2020051920200519115213.jpg "f:id:pythonjacascript:20200519115213j:plain")  
  
参考＝<https://github.com/m5stack/M5-Schematic/blob/master/Core/Basic/M5-Core-Schematic%2820171206%29.pdf>  