---
title: "【PIC】7セグメントLEDの複数桁表示器の製作"
emoji: ""
type: ""
topics: undefined
published: false
---

下のような5桁7セグLEDの表示器の製作記です。  
  
* [1.コンセプト](#1コンセプト)
* [2.使用部品](#2使用部品)
* [ハードウェアの設計](#ハードウェアの設計)
* [サンプルプログラム](#サンプルプログラム)  
   * [関数](#関数)  
   * [変数・定数](#変数定数)

### 1.コンセプト

PIC16F883を使用して、以下の機能を持った7セグ表示器を製作します。  
・5桁の表示ができる  
・シリアル通信によって表示する数値の設定ができる  
この記事では、表示部分の配線、コード作成を行う工程を書きます。

### 2.使用部品

・PIC16F883（7セグ表示用）  
・前回製作した7セグ表示器  
![f:id:pythonjacascript:20181202145235j:plain](/images/ppythonjacascript2018120220181202145235.jpg "f:id:pythonjacascript:20181202145235j:plain")

前回の記事はこちら  
[shizenkarasuzon.hatenablog.com](https://shizenkarasuzon.hatenablog.com/entry/2018/12/02/145836)

### ハードウェアの設計

以下のように接続してください。

| PIC16F883側の端子名 | 7セグ表示器（前回の記事）側の端子名                         |
| -------------- | ------------------------------------------ |
| PORTA0～7ピン     | 7セグ表示器の表示する数値を指定する8つのピン（7セグのa\~g、DPに順番に接続） |
| PORTC0～4ピン     | 7セグ表示器の桁指定用の5つのピン（1の位、10の位、...の順番に接続）      |
| MCLR端子         | プルアップする                                    |
| GND            | GND                                        |

筆者はブレッドボードで組みました。  
![f:id:pythonjacascript:20181202143212j:plain](/images/ppythonjacascript2018120220181202143212.jpg "f:id:pythonjacascript:20181202143212j:plain")  
  
  
### サンプルプログラム

PIC16F883のサンプルプロプログラムです。

簡単に説明します。

#### 関数

| ioport()        | 入出力用のピン入力設定を行う。          |
| --------------- | ------------------------ |
| main()          | メイン関数。12345を描画し続ける       |
| display(number) | 引数であるnumberの値を一度だけ表示する関数 |

display関数は一度だけ数字を描画する関数なので、値を表示し続けるにはwhile文か何かで繰り返し呼び出す必要があります。

#### 変数・定数

| freq          | 表示する数。デフォルトでは0～99999の値を表示可能                             |
| ------------- | ------------------------------------------------------- |
| KETA\_MAX     | 表示する桁数。7セグの数と合わせること                                     |
| KETA\_PORT    | ダイナミック制御で、現在表示する桁をしていする出力端子。デフォルトでPORTC                 |
| DISPLAY\_PORT | ダイナミック制御で、現在特定の一つのけたに表示している数値を表す。デフォルトではPORTA           |
| pin\_data     | PORTAの値配列。表示する値（0～9の数値）↔DISPLAY\_PORTの値（0ｘ00～0XFF）の変換用。 |

以下がそのコードです。

/******************************************
    5-digits 7セグ表示器　ソース
     8M Hz     PIC16F883
    ※動作未確認
*******************************************/
#include<htc.h>

__CONFIG(LVP_OFF & FCMEN_OFF & IESO_OFF & BOREN_ON & CPD_OFF & CP_OFF
         & MCLRE_ON & PWRTE_ON & WDTE_OFF & FOSC_INTRC_CLKOUT);

__CONFIG(WRT_OFF & BOR4V_BOR40V);

#define _XTAL_FREQ 8000000

/********************************
           関数
***************************/
ioport();
display(unsigned int);
test();

/***************************
           変数
*********************************/
unsigned int freq;     //表示する数

const unsigned int pin_data[]={
   0xFC,  //0
   0x60,  //1
   0xDA, //2
   0xF2,  //3
   0x66,  //4
   0xB6,  //5
   0xBE,  //6
   0xE4,  //7
   0xFE,  //8
   0xF6,  //9
};

#define KETA_MAX 5
#define JIKAN 2     //表示する数を変える間の待ち時間
#define KETA_PORT PORTC
#define DISPLAY_PORT PORTA


/*********************************
          Program
**********************************/
main(){
  OSCCON=0x75;     //01110101
  ioport();
  PORTC=0x00;  
  freq=12345;
  while(1){
    display(freq);
  }
}

ioport(){
  PORTA=PORTC=0;
  TRISA=0x00;
  TRISC=0x00;
}

display(unsigned int n){
  PORTC=0X01;
  for(unsigned int i=0; i<KETA_MAX; i++){
    PORTA=pin_data[n%10];  //display 1digit
    __delay_ms(JIKAN);
    DISPLAY_PORT=0x00;
    KETA_PORT<<=1;   //Shift the digit
    n=n/10;
  }
  PORTC=0x00;
}

**動作確認を行っていないので、動かない可能性もあります。**