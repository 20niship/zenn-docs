---
title: "【PIC】MPLAB Xの使い方（プロジェクト作成～ビルド）"
emoji: ""
type: ""
topics: []
published: false
---

PIC用のIDE（統合開発環境）として**MPLAB X** という無償のソフトウェアがあります。

今回は、MPLABX を使って初めてのプロジェクト作成を行ってみます。

* [1.MPLABのインストール](#1MPLABのインストール)
* [2.新規プロジェクトの作成](#2新規プロジェクトの作成)
* [3.ソースファイルの作成](#3ソースファイルの作成)
* [4.CONFIGの設定](#4CONFIGの設定)
* [5.ビルド](#5ビルド)

### 1.MPLABのインストール

まだ、MPLABXをインストールしていない場合は、この記事を参考にインストールしてください。  
[shizenkarasuzon.hatenablog.com](https://shizenkarasuzon.hatenablog.com/entry/2018/11/25/130238)

インストールが終わったら、MPLABXを起動してください。  
  
### 2.新規プロジェクトの作成

「**File**」→「**New Project**」を選択します。（**Ctrl + Shift + N**でも可）

すると、このような画面が開きます。  
![f:id:pythonjacascript:20181125145402j:plain:h350](/images/ppythonjacascript2018112520181125145402.jpg "f:id:pythonjacascript:20181125145402j:plain:h350")  
「Standalone Project」を選択し、「Next」をクリックします。

  
![f:id:pythonjacascript:20181125145541j:plain:h350](/images/ppythonjacascript2018112520181125145541.jpg "f:id:pythonjacascript:20181125145541j:plain:h350")  
書き込むPICを選択します。各自で使用するPICを選択してください。ほぼ全てのPICがそろっています。  
ここでは、例ととして「8-bit MCU」の「PIC16F688」を選択して進めます。

  
![f:id:pythonjacascript:20181125145544j:plain:h350](/images/ppythonjacascript2018112520181125145544.jpg "f:id:pythonjacascript:20181125145544j:plain:h350")  
何もせずに「Next」をクリックします。

  
![f:id:pythonjacascript:20181125145546j:plain:h350](/images/ppythonjacascript2018112520181125145546.jpg "f:id:pythonjacascript:20181125145546j:plain:h350")  
PICライターを選択します。PICKit3、4はMicrochip社が開発しているPICライターです。ライター名の左の丸印が緑色になていればそのライターは利用可能です。  
秋月電子の**AKI－PICプログラマボード**を使用している場合は、**PICKit3**を選択してください。

  
![f:id:pythonjacascript:20181125150816j:plain:h350](/images/ppythonjacascript2018112520181125150816.jpg "f:id:pythonjacascript:20181125150816j:plain:h350")  
**コンパイラー**を指定します。コンパイラーとは、プログラマーが書いたC言語またはアセンブリ言語のプログラムを機械語に翻訳する作業（**コンパイル**という）を行ってくれるソフトウェアのことです。

アセンブリ言語でプログラムを書く場合は、「**mpasm**」を選択し、C言語でプログラムを書く場合は、「**XC8**」を選択します。

※デフォルトではXC8はダウンロードされていないため、下のように「XC8」の選択肢がないことがあります。  
![f:id:pythonjacascript:20181125145549j:plain:h300](/images/ppythonjacascript2018112520181125145549.jpg "f:id:pythonjacascript:20181125145549j:plain:h300")

この場合、XC8のダウンロードが必要になります。  
インストール方法は[こちら](https://shizenkarasuzon.hatenablog.com/entry/2018/11/25/164722#%E8%A3%9C%E8%B6%B3MAPLAB%E3%81%8B%E3%82%89%E3%83%80%E3%82%A6%E3%83%B3%E3%83%AD%E3%83%BC%E3%83%89)です。  

  
![f:id:pythonjacascript:20181125150935j:plain:h350](/images/ppythonjacascript2018112520181125150935.jpg "f:id:pythonjacascript:20181125150935j:plain:h350")  
作成するプロジェクト名と、そのプロジェクトを保存するフォルダを設定します。ここでは、プロジェクト名を「LED\_BLINK」としましたが、何でも構いません。  
それ以外の場所は変更せずに「Finish」をクリックします。

すると、新規プロジェクト作成画面が消えて、メイン画面が下のようになります。  
![f:id:pythonjacascript:20181125151225j:plain](/images/ppythonjacascript2018112520181125151225.jpg "f:id:pythonjacascript:20181125151225j:plain")  
  
  
### 3.ソースファイルの作成

PICのプログラムを編集するプロジェクトを作成することができました。次に、プログラムを書くファイルを作成します。

左上のウィンドウから「Source Files」というフォルダを右クリックして、「**New**」→「**main.c**」を選択してください。  
![f:id:pythonjacascript:20181125151451j:plain:h300](/images/ppythonjacascript2018112520181125151451.jpg "f:id:pythonjacascript:20181125151451j:plain:h300")  
こうすることで、新しくC言語のファイルを作成し、それを先ほど作ったプロジェクトと関連づけさせることができます。  
ただし、C言語ではなくアセンブリ言語でプログラムを書く場合は、「**New**」→「**main.asm**」を選択してください。

  
![f:id:pythonjacascript:20181125151708j:plain:h300](/images/ppythonjacascript2018112520181125151708.jpg "f:id:pythonjacascript:20181125151708j:plain:h300")  
ソースファイルのファイル名などを指定します。私は「**main.c**」としました。  
それ以外の設定は特にいじる必要はありません。  
ファイル名を指定した後、「Finish」を押します。

  
すると、このようなメイン画面が下のようになるはずです。  
![f:id:pythonjacascript:20181125151914j:plain:h300](/images/ppythonjacascript2018112520181125151914.jpg "f:id:pythonjacascript:20181125151914j:plain:h300")

これで、ソースファイルの作成が完了しました。あとは、このファイルにプログラムを書いて、**ビルド**を行えば、MAPLABでの作業は完了です！  
  
### 4.CONFIGの設定

PICには「**Configuration Bits（コンフィグレーション・ビット）**」というものがあります。パソコンでいうBIOSのようなもので、PICの基本的な動作設定を行います。ここでWDT（ウォッチドックタイマー）の設定や、CP（Code Protection）の設定などを行います。

かつては以下のように書く方法もありましたが、今ではこの書き方はエラーになるようです。

__CONFIG(FCMDIS & IESODIS & BOREN & UNPROTECT & MCLREN & PWRTEN & WDTDIS & INTIO);

代わりに、以下のような方法で、**CONFIGのコードを自動生成**してくれる機能が追加されました。

右上に検索（Search）項目があるので、ここから「**Set Configuration Bits**」というActionを検索して実行してください。  
![f:id:pythonjacascript:20181125152522j:plain](/images/ppythonjacascript2018112520181125152522.jpg "f:id:pythonjacascript:20181125152522j:plain")

  
すると、右下にこのような画面が出てきます。ここで、Configuration Bits の設定を行います。  
設定例として写真で上げておきます。  
![f:id:pythonjacascript:20181125154125j:plain](/images/ppythonjacascript2018112520181125154125.jpg "f:id:pythonjacascript:20181125154125j:plain")

このように設定して、「**Generate Source Code to Output**」をクリックすると、このようなソースコードが自動生成されます。  
![f:id:pythonjacascript:20181125154442j:plain:h300](/images/ppythonjacascript2018112520181125154442.jpg "f:id:pythonjacascript:20181125154442j:plain:h300")

これを、作成したソースファイル（**main.c**)の先頭部分に貼り付けてください。  
![f:id:pythonjacascript:20181125154549j:plain:h300](/images/ppythonjacascript2018112520181125154549.jpg "f:id:pythonjacascript:20181125154549j:plain:h300")

### 5.ビルド

下のコードをコピペしてください。

// PIC16F688 Configuration Bit Settings
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

  
そして、**ビルド**（Build）ボタン（\[![f:id:pythonjacascript:20181125154724j:plain:h15](/images/ppythonjacascript2018112520181125154724.jpg "f:id:pythonjacascript:20181125154724j:plain:h15")）を押します。

右下のウィンドウに以下のように表示されればビルド完了です。

Memory Summary:
    Program space        used    22h (    34) of  1000h words   (  0.8%)
    Data space           used     4h (     4) of   100h bytes   (  1.6%)
    EEPROM space         used     0h (     0) of   100h bytes   (  0.0%)
    Data stack space     used     0h (     0) of    50h bytes   (  0.0%)
    Configuration bits   used     1h (     1) of     1h word    (100.0%)
    ID Location space    used     0h (     0) of     4h bytes   (  0.0%)

make[2]: Leaving directory 'C:/Users/Owner/MPLABXProjects/LED_BLINK.X'
make[1]: Leaving directory 'C:/Users/Owner/MPLABXProjects/LED_BLINK.X'

BUILD SUCCESSFUL (total time: 9s)
Loading code from C:/Users/Owner/MPLABXProjects/LED_BLINK.X/dist/default/production/LED_BLINK.X.production.hex...
Loading completed

**BUILD SUCCESSFUL**と書かれていればビルド成功ですが、**BUILD FAILED** と書かれていれば、何らかの理由でビルドが失敗しています。ソースコードを修正してください。
  
  