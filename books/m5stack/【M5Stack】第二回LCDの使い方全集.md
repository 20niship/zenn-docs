---
title: "【M5Stack】第二回 LCDの使い方 全集"
emoji: ""
type: ""
topics: []
published: false
---

* [LCDについて](#LCDについて)
* [サンプルプログラム](#サンプルプログラム)
* [実行結果](#実行結果)
* [解説](#解説)  
   * [LCDについて](#LCDについて-1)  
   * [事前準備](#事前準備)  
   * [テキスト表示](#テキスト表示)  
   * [日本語表示](#日本語表示)  
   * [図形を表示](#図形を表示)  
         * [指定の色でLCDを塗りつぶす](#指定の色でLCDを塗りつぶす)  
         * [ピクセル操作](#ピクセル操作)  
         * [四角形](#四角形)  
         * [三角形](#三角形)  
         * [円](#円)  
         * [線](#線)  
   * [QRコード作成](#QRコード作成)  
   * [プログレスバー作成](#プログレスバー作成)  
   * [色について](#色について)
* [参考文献](#参考文献)

### LCDについて

M5Stackには表面に320ｘ240のTFTカラーLCDがついています。  
![f:id:pythonjacascript:20200519030052j:plain](/images/ppythonjacascript2020051920200519030052.jpg "f:id:pythonjacascript:20200519030052j:plain")

ここに文字や図形を表示することができるので、その方法を紹介します  
  
### サンプルプログラム

#include <M5Stack.h>
//reference = https://github.com/m5stack/m5-docs/blob/master/docs/ja/api/lcd.md

//setup()関数はM5Stackがスタートした最初に読み込まれる
void setup(){
  // M5Stack オブジェクトの初期化
  M5.begin();

  //Power chipがgpio21, gpio22, I2Cにつながれたデバイスに接続される。
  //バッテリー動作の場合はこの関数を読んでください（バッテリーの電圧を調べるらしい）
  M5.Power.begin();

  M5.Lcd.setBrightness(200); //バックライトの明るさを0（消灯）～255（点灯）で制御
  //M5.Lcd.loadFont("filename", SD); // フォント読み込み
}


void loop() {
 M5.Lcd.fillScreen(WHITE);

  
 // 文字描画
  M5.Lcd.setCursor(10, 10); //文字表示の左上位置を設定
  M5.Lcd.setTextColor(RED); //文字色設定(背景は透明)(WHITE, BLACK, RED, GREEN, BLUE, YELLOW...)
  M5.Lcd.setTextSize(2);//文字の大きさを設定（1～7）
  M5.Lcd.print("Hey Guys! \n\n We have a gift for you!");

  M5.Lcd.setTextColor(RED, BLACK); //文字色設定と背景色設定(WHITE, BLACK, RED, GREEN, BLUE, YELLOW...)
  M5.Lcd.setCursor(10, 100); //文字表示の左上位置を設定
  M5.Lcd.print("Hey Guys! \n\n We have a gift for you!");


  delay(1000); // 1000ms待つ
  M5.Lcd.fillScreen(WHITE);
  

  // ディスプレイの大きさ取得（僕の環境では320 x 240だった）
  int display_width = M5.Lcd.width();
  int display_height = M5.Lcd.height();
  M5.Lcd.setCursor(10, 10); //文字表示の左上位置を設定
  M5.Lcd.printf("w=%d, h=%d",display_width, display_height);


  delay(1000); // 1000ms待つ
  M5.Lcd.fillScreen(WHITE);

  
  // 特定の色で全体を塗りつぶす
  uint16_t colors[] = {WHITE, RED, GREEN, BLUE, BLACK, YELLOW };
  for(int j = 0; j<6; j++){
    M5.Lcd.fillScreen(colors[j]);
    delay(300);
  }

   uint16_t colorvalue;
   colorvalue= M5.Lcd.color565(123,30,50); //RGB値（0～255）を指定することもできる
   M5.Lcd.fillScreen(colorvalue);


  delay(1000);// 1000ms待つ
  M5.Lcd.fillScreen(WHITE);

  // 図形を書く（長方形、円、三角形）
  M5.Lcd.drawRect(10, 10, 80, 50, BLUE); //枠だけ left, top, witdh, height
  M5.Lcd.fillRect(15, 15, 70, 40, RED); // 塗りつぶし left, top, witdh, height

  M5.Lcd.drawRoundRect(1000, 10, 80, 50, 10, GREEN); // 隅が丸くなった長方形(x,y,width,height,radius,col)
  
  M5.Lcd.drawCircle(250, 50, 40, BLUE); //枠だけ center-x, center-y, radius
  M5.Lcd.fillCircle(250, 50, 30, RED); //塗りつぶし center-x, center-y, radius
  
  M5.Lcd.drawTriangle(50, 130, 100, 130, 80,  200, BLUE);//枠だけ (x1, y1), (x2,y2), (x3, y3)
  M5.Lcd.fillTriangle(90, 130, 130, 150, 110, 200, RED);//塗りつぶし (x1, y1), (x2,y2), (x3, y3)

  M5.Lcd.drawPixel(300, 40, RED);//（x, y)のピクセルを指定の色で塗る
  M5.Lcd.drawPixel(300, 45, RED);//（x, y)のピクセルを指定の色で塗る
  M5.Lcd.drawPixel(300, 50, RED);//（x, y)のピクセルを指定の色で塗る
  M5.Lcd.drawPixel(300, 55, RED);//（x, y)のピクセルを指定の色で塗る
  
  M5.Lcd.drawLine(0, 0, 120, 120, BLACK);//（x1, y1) (x2, y2)
  M5.Lcd.drawFastVLine(150, 110, 100, RED);//(x,y,height,col) (x,y)からheightだけ垂直（Vertical）な線を書く
  M5.Lcd.drawFastHLine(150, 110, 150, GREEN);//(x,y,width,col) (x,y)からwidthだけ水平（Horizontal）な線を書く


  delay(1000);// 1000ms待つ
  M5.Lcd.fillScreen(WHITE);


  // 高度な描画
  for(uint8_t i=0;i<10;i++){
    M5.Lcd.progressBar(0,0,240,20,i*10);//(x,y, width, height, percent)
    delay(100);
  }
  M5.Lcd.qrcode("http://www.m5stack.com",10,10,230,7); //(URL,x,y,size, version)


  delay(1000);// 1000ms待つ
}

### 実行結果

![f:id:pythonjacascript:20200519112615j:plain](/images/ppythonjacascript2020051920200519112615.jpg "f:id:pythonjacascript:20200519112615j:plain")  
上の写真たちのようになります。

delay(1000);

は1000ms（一秒）プログラムの動作を停止させる関数なので、一秒ずつ画面の状態が変化するようになっています。  
  
  
### 解説

では、↑のプログラムを簡単に説明していきます。  
  
#### LCDについて

M5StackについているLCDのスペックについて簡単に書くと、以下の表のようになります

| スペック     | 説明                 |
| -------- | ------------------ |
| 種類       | TFTカラーディスプレイ       |
| 解像度      | 320x240            |
| サイズ（実測値） | w40.8 x h31 \[mm\] |

GPIOのピンなどはこのURLから見ることができます

<https://github.com/m5stack/M5-Schematic/blob/master/Core/Basic/M5-Core-Schematic%2820171206%29.pdf>

  
 LCDディスプレイの解像度を取得するのは下のプログラムで可能です。  
 （M5.begin関数とM5.Power.begin();の後で呼び出してください）

int display_width = M5.Lcd.width();
int display_height = M5.Lcd.height();
M5.Lcd.setCursor(10, 10); //文字表示の左上位置を設定
M5.Lcd.printf("w=%d, h=%d",display_width, display_height);

#### 事前準備

Lcdを使うには以下の関数を実行する必要があります。

// M5Stack オブジェクトの初期化
M5.begin();

M5.begin関数は以下のように書くこともできる。

M5.begin(true, false, true, true);

* 第1引数 = LCDを初期化するかどうか
* 第2引数 = SDカードを使用するかどうか
* 第3引数 = Serial通信をするかどうか
* 第4引数 = I2C通信をするかどうか
  
  
M5.Power.begin()を実行することで、Power chipがgpio21, gpio22, I2Cにつながれたデバイスに接続されます。  
バッテリー動作の場合はこの関数を読んでください（バッテリーの電圧を調べるらしい）

M5.Power.begin();

LCDの明るさの設定は M5.Lcd.setBrightness関数 で行います。

M5.Lcd.setBrightness(200); //バックライトの明るさを0（消灯）～255（点灯）で制御

尚、Sleepさせるときはバックライトも消しておいた方が消費電力が減るので、

M5.Lcd.sleep();
M5.Lcd.setBrightness(0);

のようにしてください（LCDのバックライトは別系統で制御している）。  
  
  
#### テキスト表示

M5.Lcd.loadFont("filename", SD); // フォント読み込み
M5.Lcd.setTextSize(2);//文字の大きさを設定（1(最小)～7(最大)）
M5.Lcd.setCursor(10, 10); //文字表示の左上位置を設定

M5.Lcd.setTextColor(RED, BLACK); //文字色設定と背景色設定
M5.Lcd.setTextColor(RED); //文字色を設定（文字背景色は透明）

  
文字を表示するにはprint()関数などを使います

int hoge = 100;
M5.Lcd.printf("HOGE = %d" hoge);

M5.Lcd.print("Hey Guys! \n\n We have a gift for you!"); //普通に出力（最後に改行はない）

M5.Lcd.println("Hey Guys! \n\n We have a gift for you!"); //最後に改行を付ける

ちなみに、print()関数等が実行されるとカーソルの位置が変化するので、毎度 setCursor関数を呼ぶ必要はありません。

  
あまりサンプルコードでは見ませんが、drawString関数を使って文字を書くこともできます。

M5.Lcd.drawString("Hello 　World", 0, 0);

構文：

drawString(const char *string, int32_t poX, int32_t poY, uint8_t font);
drawString(const char *string, int32_t poX, int32_t poY);
drawString(const String& string, int32_t poX, int32_t poY, uint8_t font);
drawString(const String& string, int32_t poX, int32_t poY);

(posX, posY) は描画する文字列の左上の座標、stringは勿論描画する文字列。font=1のときは loadFontを使用します

print関数との違いは、LCDの端に来た時に改行をするかどうかだと思われます。print関数だとLCDの端で文字が改行されるのに対し、drawString関数は下の写真の様にはみ出してもそのまま描画されます。  
![f:id:pythonjacascript:20200519112847j:plain](/images/ppythonjacascript2020051920200519112847.jpg "f:id:pythonjacascript:20200519112847j:plain")  
（DrawString使用時）

![f:id:pythonjacascript:20200519030052j:plain](/images/ppythonjacascript2020051920200519030052.jpg "f:id:pythonjacascript:20200519030052j:plain")   
（print関数使用時）  
  
  
#### 日本語表示

通常のprint関数などではアルファベットと普通の記号以外は表示できません。なので、日本語等を表示する時には特別なプログラムが必要になります...  
[M5Stackで日本語表示 - Qiita](https://qiita.com/taront/items/7900c88b9e9782c33b08)  
  
  
#### 図形を表示

**fill○○関数**と**draw○○関数**がありますが、fill○○関数は図形を塗りつぶす関数で、draw○○関数は枠線だけを描く関数です。  
  
##### 指定の色でLCDを塗りつぶす

 M5.Lcd.fillScreen(WHITE);
 // 構文： M5.Lcd.fillScreen(uint16_t color);

LCD全体を塗りつぶします。  
  
##### ピクセル操作

M5.Lcd.drawPixel(300, 40, RED);//（x, y)のピクセルを指定の色で塗る
 // 構文： M5.Lcd.drawPixel(int16_t x, int16_t y, [uint16_t color]);

1つ1つのピクセルの色をセットすることができます。  
  
##### 四角形

M5.Lcd.drawRect(10, 10, 80, 50, BLUE); //枠だけ left, top, witdh, height
M5.Lcd.fillRect(15, 15, 70, 40, RED); // 塗りつぶし left, top, witdh, height
// 構文 : drawRect(int16_t x, int16_t y, int16_t w, int16_t h, [uint16_t color]);

引数は左上の座標(x,y)、サイズ(w,h)の順で指定します。

長方形の4つの角が丸くなったdrawRoundRect関数も存在します

M5.Lcd.drawRoundRect(1000, 10, 80, 50, 10, GREEN); // 隅が丸くなった長方形(x,y,width,height,radius,col)

第5引数に角のR半径( > 0)を指定します。  
  
  
##### 三角形

M5.Lcd.drawTriangle(50, 130, 100, 130, 80,  200, BLUE);//枠だけ (x1, y1), (x2,y2), (x3, y3)
M5.Lcd.fillTriangle(90, 130, 130, 150, 110, 200, RED);//塗りつぶし (x1, y1), (x2,y2), (x3, y3)

// 構文：drawTriangle(int16_t x0, int16_t y0, int16_t x1, int16_t y1, int16_t x2, int16_t y2, [uint16_t color]);

三角形の3つの座標を順番に指定します。  
  
  
##### 円

M5.Lcd.drawCircle(250, 50, 40, BLUE); //枠だけ center-x, center-y, radius
M5.Lcd.fillCircle(250, 50, 30, RED); //塗りつぶし center-x, center-y, radius 
// 構文：drawCircle(int16_t x, int16_t y, int16_t r, [uint16_t color]);||<

<https://github.com/m5stack/m5-docs/blob/master/docs/ja/api/lcd.md> のdockには載っていませんでしたが、この関数も動いたので書いておきます。  
第1、第2引数で円の中心座標を指定して、第三引数で円の半径を指定します。  
  
  
##### 線

M5.Lcd.drawLine(0, 0, 120, 120, BLACK);//（x1, y1) (x2, y2)
M5.Lcd.drawFastVLine(150, 110, 100, RED);//(x,y,height,col) (x,y)からheightだけ垂直（Vertical）な線を書く
M5.Lcd.drawFastHLine(150, 110, 150, GREEN);//(x,y,width,col) (x,y)からwidthだけ水平（Horizontal）な線を書くM5.Lcd.fillScreen(uint16_t color);

* drawLine関数は普通に線を引く関数、
* drawFastVLine関数は垂直方向（始点から下側）に線を引く関数、
* drawFastHLine関数は水平方向（始点から右側）に線を引く関数

#### QRコード作成

M5.Lcd.qrcode("http://www.m5stack.com",10,10,230,7); //(URL,x,y,size, version)
//構文：qrcode(const String &string, uint16_t x, uint16_t y, uint8_t width, uint8_t version);

versionのことは、<https://lang-ship.com/blog/work/m5stickc-qrcode/> のサイトがまとめてくださっています。  
  
#### プログレスバー作成

uint8_t percent = 80;
M5.Lcd.progressBar(0,0,240,20,percent);//(x,y, width, height, percent)
//構文：progressBar(int x, int y, int w, int h, uint8_t val);

  
プログレスバーを作成します。モノクロです。  
  
  
#### 色について

setTextColor関数やfillScreen関数で指定できる色には以下のようなものがあります。

| 色定義              | 中身     | 説明    | R   | G   | B   |
| ---------------- | ------ | ----- | --- | --- | --- |
| TFT\_BLACK       | 0x0000 | 黒     | 0   | 0   | 0   |
| TFT\_NAVY        | 0x000F | ネイビー  | 0   | 0   | 128 |
| TFT\_DARKGREEN   | 0x03E0 | 濃い緑   | 0   | 128 | 0   |
| TFT\_MAROON      | 0x7800 | マロン   | 128 | 0   | 0   |
| TFT\_PURPLE      | 0x780F | パープル  | 128 | 0   | 128 |
| TFT\_OLIVE       | 0x7BE0 | オリーブ  | 128 | 128 | 0   |
| TFT\_LIGHTGREY   | 0xC618 | 薄い灰色  | 192 | 192 | 192 |
| TFT\_DARKGREY    | 0x7BEF | 濃い灰色  | 128 | 128 | 128 |
| TFT\_BLUE        | 0x001F | 青     | 0   | 0   | 255 |
| TFT\_GREENYELLOW | 0xB7E0 | 黄緑    | 180 | 255 | 0   |
| TFT\_GREEN       | 0x07E0 | 緑     | 0   | 255 | 0   |
| TFT\_YELLOW      | 0xFFE0 | 黄色    | 255 | 255 | 0   |
| TFT\_ORANGE      | 0xFDA0 | オレンジ  | 255 | 180 | 0   |
| TFT\_PINK        | 0xFC9F | ピンク   | 255 | 255 | 16  |
| TFT\_CYAN        | 0x07FF | シアン   | 0   | 255 | 255 |
| TFT\_DARKCYAN    | 0x03EF | 濃いシアン | 0   | 128 | 128 |
| TFT\_RED         | 0xF800 | 赤     | 255 | 0   | 0   |
| TFT\_MAGENTA     | 0xF81F | マゼンダ  | 255 | 0   | 255 |
| TFT\_WHITE       | 0xFFFF | 白     | 255 | 255 | 255 |

（<https://raw.githubusercontent.com/m5stack/m5-docs/master/docs/ja/api/lcd.md> より引用）

  
自分でRGBの色を設定したい場合は

uint16_t myColor;
myColor= M5.Lcd.color565(123,30,50); //RGB値（0～255）を指定
// myColor = (uint16_t)0xFC9F;//でもOK
M5.Lcd.setTextColor(myColor);
M5.Lcd.fillScreen(myColor);

のようにすることもできます  
  
  
### 参考文献

<https://github.com/m5stack/m5-docs/blob/master/docs/ja/api/lcd.md>