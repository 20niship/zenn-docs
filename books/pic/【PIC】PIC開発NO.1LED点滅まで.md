---
title: "【PIC】PIC開発NO.1 LED点滅まで"
emoji: ""
type: ""
topics: []
published: false
---

今回は、**PIC16F688**を使って**LEDの点滅**を行います。

![f:id:pythonjacascript:20181126211642j:plain](/images/ppythonjacascript2018112620181126211642.jpg "f:id:pythonjacascript:20181126211642j:plain")  
必要なソフトウェアや機材は準備完了している前提で書いています。  
PIC開発に必要なものがそろってない場合は、こちらから準備をお願いします。  
[shizenkarasuzon.hatenablog.com](https://shizenkarasuzon.hatenablog.com/entry/2018/11/26/214907)  
  
  
* [用意するもの](#用意するもの)  
   * [PIC開発共通](#PIC開発共通)  
   * [この実験で必要なもの](#この実験で必要なもの)
* [1.プログラムを書く](#1プログラムを書く)
* [2.ビルドとHEXファイルの作成](#2ビルドとHEXファイルの作成)
* [3.PICプログラマーを起動](#3PICプログラマーを起動)  
   * [DCジャック電源の極性について！](#DCジャック電源の極性について)
* [4.ライターをPCに接続](#4ライターをPCに接続)
* [5.PICプログラマにライターを認識させる](#5PICプログラマにライターを認識させる)  
   * [補足](#補足)
* [6.PIC選択](#6PIC選択)
* [7.HEXファイル読み込み](#7HEXファイル読み込み)
* [8.PICをライターにセット](#8PICをライターにセット)
* [9.書き込み！](#9書き込み)
* [10.回路製作](#10回路製作)
* [11.動作テストォォ！](#11動作テストォォ)

### 用意するもの

必要なもの一覧です。

#### PIC開発共通

| PICライター        | この記事は、AKI-PICプログラマー4で行っていますが、PICKitでもOK |
| -------------- | --------------------------------------- |
| シリアル・USB変換ケーブル | AKI-PICプログラマー4とPCの接続に使用                 |
| PICライター用電源     | 15V200ｍA以上の2.1mmDCジャック付き                |

![f:id:pythonjacascript:20181126195710j:plain:h300](/images/ppythonjacascript2018112620181126195710.jpg "f:id:pythonjacascript:20181126195710j:plain:h300")

  
また、以下のソフトウェアがインストールされ、USB端子のあるパソコンが必要です。

| MPLAB X IDE     | PICのプログラム開設に必要               |
| --------------- | ---------------------------- |
| PICプログラマー4 Beta | AKI-PICプログラー専用のPICライターソフトウェア |

![f:id:pythonjacascript:20181125134829j:plain](/images/ppythonjacascript2018112520181125134829.jpg "f:id:pythonjacascript:20181125134829j:plain") ![f:id:pythonjacascript:20181126195826j:plain](/images/ppythonjacascript2018112620181126195826.jpg "f:id:pythonjacascript:20181126195826j:plain")

PICプログラマー4 Beta と MPLAB X IDE のアイコン  
  
#### この実験で必要なもの

| PIC16F688（1個） | 秋月電子等で購入可能            |
| ------------- | --------------------- |
| LED（1個）       | 適当                    |
| LED用抵抗（1個）    | LEDの順方向電流調整用          |
| MCLR用抵抗（1個）   | 1ｋΩあたり                |
| 0.1μF積セラ      | 積層セラミックコンデンサの略。電源平滑化用 |

このほかにもブレッドボードなどがあると便利です。  
  
### 1.プログラムを書く

この記事に沿って、「MAPLAB X」を使って以下のプログラムをビルドします。  
[shizenkarasuzon.hatenablog.com](https://shizenkarasuzon.hatenablog.com/entry/2018/11/25/155431)

プロジェクトを新規作成する必要があります。  
PICを選択する画面があれば、PIC16F688を選択てください。

プロジェクトの新規作成が終わったら、そのプロジェクトに新しくソースファイルを作成し、そこに以下のプログラムを書き込みます。

#pragma config FOSC = INTOSCIO  // Oscillator Selection bits (INTOSCIO oscillator: I/O function on RA4/OSC2/CLKOUT pin, I/O function on RA5/OSC1/CLKIN)
#pragma config WDTE = OFF       // Watchdog Timer Enable bit (WDT disabled)
#pragma config PWRTE = ON       // Power-up Timer Enable bit (PWRT enabled)
#pragma config MCLRE = ON       // MCLR Pin Function Select bit (MCLR pin function is MCLR)
#pragma config CP = OFF         // Code Protection bit (Program memory code protection is disabled)
#pragma config CPD = OFF        // Data Code Protection bit (Data memory code protection is disabled)
#pragma config BOREN = ON       // Brown Out Detect (BOR enabled)
#pragma config IESO = OFF       // Internal External Switchover bit (Internal External Switchover mode is disabled)
#pragma config FCMEN = OFF      // Fail-Safe Clock Monitor Enabled bit (Fail-Safe Clock Monitor is disabled)

#include <xc.h>
#include <htc.h>
#define _XTAL_FREQ 31000

void main(void) {
    OSCCON = 0;
    PORTC = PORTA = 0;
    
    while(1){
        TRISA = 0x1F;
        TRISC = 0x0F;
        RA5 ^= 1;
        __delay_ms(500);
    }
    return;
}

現時点では、MAPLAB Xの画面はこのようになっています。  
![f:id:pythonjacascript:20181125151914j:plain](/images/ppythonjacascript2018112520181125151914.jpg "f:id:pythonjacascript:20181125151914j:plain")  
今から、上のプログラムをPIC16F688に書き込む工程です。  
  
  
### 2.ビルドとHEXファイルの作成

**ビルド**すると、右下の画面に以下のような文字が出てくると思います。

make -f nbproject/Makefile-default.mk SUBPROJECTS= .build-conf

(途中省略）

Memory Summary:
    Program space        used    22h (    34) of  1000h words   (  0.8%)
    Data space           used     4h (     4) of   100h bytes   (  1.6%)
    EEPROM space         used     0h (     0) of   100h bytes   (  0.0%)
    Data stack space     used     0h (     0) of    50h bytes   (  0.0%)
    Configuration bits   used     1h (     1) of     1h word    (100.0%)
    ID Location space    used     0h (     0) of     4h bytes   (  0.0%)

make[2]: Leaving directory 'C:/Users/（ユーザー名）/MPLABXProjects/LED_BLINK.X'
make[1]: Leaving directory 'C:/Users/（ユーザー名）/MPLABXProjects/LED_BLINK.X'

BUILD SUCCESSFUL (total time: 5s)
Loading code from C:/Users/Owner/MPLABXProjects/LED_BLINK.X/dist/default/production/LED_BLINK.X.production.hex...
Loading completed

  
このうち、下から約6行目にある、

make[2]: Leaving directory 'C:/Users/（ユーザー名）/MPLABXProjects/LED_BLINK.X'

の部分に注目してください。ここに**HEX**ファイルという、PICへ書き込むのに必要なファイルが作成されています。

僕の開発環境の場合、「C:\\Users\\（ユーザー名）\\MPLABXProjects\\LED\_BLINK.X\\dist\\default\\production」というフォルダの中に、  
「**LED\_BLINK.X.production.hex**」という名前でHEXファイルが保存されていました。  
HEXファイルなので拡張子は「**.hex**」です。

今から、このHEXファイルをPICライターを使ってPICに書き込みます。  
  
### 3.PICプログラマーを起動

AKI-PICプログラマーに電源を入れます。

DCジャックを差し込むのですが、ここでひとつ注意！

#### DCジャック電源の極性について！

DCジャックには**センター+**タイプと**センターマイナス**のタイプがあります。

センタープラスタイプは中央がプラス極、外側にむき出しの金属部分がマイナス極ですが、センターマイナスのDCジャックは極性が逆です！  
そのため、事前にボードのジャンパーピンを変更しておく必要があります。  
![f:id:pythonjacascript:20181126201125j:plain](/images/ppythonjacascript2018112620181126201125.jpg "f:id:pythonjacascript:20181126201125j:plain")

センタープラスの電源を使う場合は、1ピンと3ピンを接続、また2ピンと4ピンを接続してください。  
センターマイナスの場合は、1ピンと2ピン、3ピンと4ピンを接続してください。

間違って逆向きにジャンパーピンを指すと、プラスマイナスを逆に電源を供給していることになるので、ライターが**即昇天**します！

ライターの電源を入れると、**PowerLED（赤色）**が点灯し、**D1LED（赤色、小さい基板上のチップLED）**が2回点灯します。  
  
  
### 4.ライターをPCに接続

USBシリアル変換ケーブルでPICライターとPCを接続します。この時、接続したCOMポートを確認しておく必要があります。

COMポートの確認は、デバイスマネージャから行うことができます。  
![f:id:pythonjacascript:20181126180159j:plain](/images/ppythonjacascript2018112620181126180159.jpg "f:id:pythonjacascript:20181126180159j:plain")

上の画像の場合、PICライターは、「**Prolific USB-to-Serial Comm Port**」という名前で**COM3**に認識されています。  
  
  
### 5.PICプログラマにライターを認識させる

AKI-PICプログラマー専用ソフト、「**PICプログラマー4 Beta**」を起動します。  
こんなアイコンのソフトウェアです。  
![f:id:pythonjacascript:20181125134829j:plain](/images/ppythonjacascript2018112520181125134829.jpg "f:id:pythonjacascript:20181125134829j:plain")

まだPICプログラマー4 Beta をインストールしていない場合は、以下のサイトを参考にインストールしてください。  
[shizenkarasuzon.hatenablog.com](https://shizenkarasuzon.hatenablog.com/entry/2018/11/25/132146)

  
このソフトを起動するお、このような画面が表示されます。  
![f:id:pythonjacascript:20181125135010j:plain](/images/ppythonjacascript2018112520181125135010.jpg "f:id:pythonjacascript:20181125135010j:plain")

ここで、COMポートを選択するウィンドウで、PICライターを認識したCOMポートを選択します。  
今回の場合、デバイスマネージャで確認するとCOM3になっていたので、「**COM3**」を選択します。  
![f:id:pythonjacascript:20181126180851p:plain](/images/ppythonjacascript2018112620181126180851.png "f:id:pythonjacascript:20181126180851p:plain")

もし、上の写真のようなCOMポート選択画面が表示されない場合は、下の「通信」ボタンを押してください。  
![f:id:pythonjacascript:20181126180859p:plain](/images/ppythonjacascript2018112620181126180859.png "f:id:pythonjacascript:20181126180859p:plain")

  
COMポートを選択した後、右下の「**メッセージ**」欄に「**COM3を選択しました**」のように表示されていれば、正常にPICライターが認識されています。  
  
#### 補足

これ以降、**PICプログラマー4 Beta**を動かしているときに、以下のようなメッセージが表示されることがありますが、「**VPP-FIRST（推奨）**」を選択してください。  
![f:id:pythonjacascript:20181126181200j:plain](/images/ppythonjacascript2018112620181126181200.jpg "f:id:pythonjacascript:20181126181200j:plain")

  
### 6.PIC選択

画面左上の「**デバイス選択**」から、書き込むPICを選択します。  
今回は「PIC16F688」を使用するので、下の写真のように設定します。  
![f:id:pythonjacascript:20181126181647j:plain](/images/ppythonjacascript2018112620181126181647.jpg "f:id:pythonjacascript:20181126181647j:plain")
  
  
### 7.HEXファイル読み込み

STEP2で作成したHEXファイルを**PICプログラマー4 Beta**に読み込みます。  
「**HEXロード**」ボタンを押して、MPLABXでビルドしたときに作成したHEXファイルを選択します。  
![f:id:pythonjacascript:20181126181411j:plain](/images/ppythonjacascript2018112620181126181411.jpg "f:id:pythonjacascript:20181126181411j:plain")

右下の「メッセージ」欄に「**ファイルロード完了**」と表示されればHEXファイルが正しく読み込まれています。  
コンフィグレーションビットの設定がソースファイルと同じになっているかを確かめるのも一つの確認方法です。  
  
  
### 8.PICをライターにセット

PICマイコンをライターにセットします。  
PICをセットする向きや位置などは、「PICマイコン情報（下写真）」というウィンドウの通りにしてください。  
![f:id:pythonjacascript:20181126182139j:plain](/images/ppythonjacascript2018112620181126182139.jpg "f:id:pythonjacascript:20181126182139j:plain")

上の図の場合、このようになります。  
確認する部分は、①**ジャンパピンの位置**と、②**PICの差し込み位置**と、③**PICのセットする向き**の3つです。  
③については、すべてのICには通称「切り欠き」と呼ばれるかけた部分があるので、それを目印にして向きを揃えます。  
![f:id:pythonjacascript:20181126191208j:plain](/images/ppythonjacascript2018112620181126191208.jpg "f:id:pythonjacascript:20181126191208j:plain")

![f:id:pythonjacascript:20181126191212j:plain](/images/ppythonjacascript2018112620181126191212.jpg "f:id:pythonjacascript:20181126191212j:plain")

  
### 9.書き込み！

いよいよPICにプログラムを書き込みます！  
右上の「プログラム」ボタンを押します。  
![f:id:pythonjacascript:20181126191550j:plain](/images/ppythonjacascript2018112620181126191550.jpg "f:id:pythonjacascript:20181126191550j:plain")

下のような画面が出てくる場合もありますが、「**はい**」を選択します。  
![f:id:pythonjacascript:20181126191604j:plain](/images/ppythonjacascript2018112620181126191604.jpg "f:id:pythonjacascript:20181126191604j:plain")

すると、書き込みが始まります....  
![f:id:pythonjacascript:20181126191639j:plain](/images/ppythonjacascript2018112620181126191639.jpg "f:id:pythonjacascript:20181126191639j:plain")

右下の「メッセージ」欄に

プログラミング成功。 31sec  終了時刻：（省略）Pass = 1  Fail = 0

と書かれて入れば、正常に書き込みが完了しています！！

### 10.回路製作

PICマイコンにプログラムが書き込まれたので、あとは回路を組んで動かすだけです。  
以下のような回路を作成します。  
PIC16F688のデータシートを見ながら回路作成を行います。  
<http://ww1.microchip.com/downloads/en/DeviceDoc/41203B.pdf>

16F688のピンアサインは次のようになっており、今回使用するのは黄色でマーカーをつけたピンです。  
![f:id:pythonjacascript:20181126205200j:plain](/images/ppythonjacascript2018112620181126205200.jpg "f:id:pythonjacascript:20181126205200j:plain")

回路図です。  
![f:id:pythonjacascript:20181126211422j:plain](/images/ppythonjacascript2018112620181126211422.jpg "f:id:pythonjacascript:20181126211422j:plain")

ブレッドボード上で実験します。  
![f:id:pythonjacascript:20181126211635j:plain](/images/ppythonjacascript2018112620181126211635.jpg "f:id:pythonjacascript:20181126211635j:plain")  
上の回路図をよく見て製作してください。   
  
### 11.動作テストォォ！

では、電源を入れます。  
![f:id:pythonjacascript:20181126211642j:plain](/images/ppythonjacascript2018112620181126211642.jpg "f:id:pythonjacascript:20181126211642j:plain")

LEDが0.5秒ごとに点滅したでしょうか？