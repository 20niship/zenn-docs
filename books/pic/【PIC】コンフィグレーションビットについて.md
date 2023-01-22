---
title: "【PIC】コンフィグレーションビットについて"
emoji: ""
type: ""
topics: []
published: false
---

PICは、「コンフィグレーションビット」というレジスタの内容を変更することで、発信回路、リセット、コード・プロテクトなどの設定を変更することができます。  
今回は、このコンフィグレーションビットの設定方法を書きます。

* [コンフィグレーションビット](#コンフィグレーションビット)
* [FCMENビット](#FCMENビット)  
   * [Fail Safe Clock Monitorとは](#Fail-Safe-Clock-Monitorとは)
* [IESOビット](#IESOビット)  
   * [2 Speed Startup Modeとは](#2-Speed-Startup-Modeとは)
* [BORENビット](#BORENビット)  
   * [Brown Out Resetとは](#Brown-Out-Resetとは)
* [CPDビット](#CPDビット)  
   * [Code Protect（EEPROM）とは](#Code-ProtectEEPROMとは)
* [CPビット](#CPビット)  
   * [Code Protect（プログラムメモリ）とは](#Code-Protectプログラムメモリとは)
* [MCLRビット](#MCLRビット)  
   * [外部リセット入力とは](#外部リセット入力とは)  
   * [注意！](#注意)
* [PWRTEビット](#PWRTEビット)  
   * [Power Up Timerとは](#Power-Up-Timerとは)
* [WDTEビット](#WDTEビット)  
   * [Watch Dog Timerとは](#Watch-Dog-Timerとは)
* [FOSCビット](#FOSCビット)  
   * [発振回路とは](#発振回路とは)
* [コンフィグレーションビットの設定方法（MPLAB）](#コンフィグレーションビットの設定方法MPLAB)

### コンフィグレーションビット

コンフィグレーションには、多くの設定項目があります。それらをひとつひとつ解説していきます。

例えば、PIC16F688の場合、コンフィグレーションビットは次のような14ビットになっています。

| ビット | 13 | 12 | 11    | 10   | 9      | 8      | 7   | 6  | 5     | 4     | 3    | 2     | 1     | 0     |
| --- | -- | -- | ----- | ---- | ------ | ------ | --- | -- | ----- | ----- | ---- | ----- | ----- | ----- |
| 役割  | なし | なし | FCMEN | IESO | BOREN1 | BOREN0 | CPD | CP | MCLRE | PWRTE | WDTE | FOSC2 | FOSC1 | FOSC0 |

このコンフィグレーションビットの役割は、どのPICでもだいたい同じです。

では、それぞれのビットの役割について説明していきます。  
  
  
### FCMENビット

**フェイル・セーフ・クロック・モニター（Fail Safe Clock Monitor）**を使うかどうかを選択します。

**Fail Safe Clock Monitor**を使用する場合は、1（**FCMEN**）に設定します。  
**Fail Safe Clock Monitor**を使用しない場合は、0（**FCMDIS**）に設定します。

#### Fail Safe Clock Monitorとは

PICは、外部の発振器で生成されたクロックをもとに動作することができる。その時、Fail Safe Clock MonitorをONにしていると、外部のシステムクロック供給が万が一止まってしまっても、内部発振器でシステムクロックを作り、動作をつづけることができる。  
また、この時には割込みが発生する。  
  
  
### IESOビット

**2スピード・スタートアップ・モード（2 Speed Startup Mode）**を使うかどうか選択します。

**2 Speed Startup Mode**を使用する場合は1（**IESOEN**）に設定します。  
**2 Speed Startup Mode**を使用しない場合は0（**IESODIS**）に設定します。

#### 2 Speed Startup Modeとは

オシレータ・スタートアップ・タイマーが起動している間、内部発振器で作ったシステムクロックでプログラムを実行する機能のこと。  
オシレータ・スタートアップ・タイマーとは、パワーアップ・タイマーがタイムアウトした後に起動するタイマーで、1024クロック分だけPICをリセットする機能です。  
  
  
### BORENビット

**ブラウンアウト・リセット（Brown Out Reset）**の設定を行います。

BOREN1, BOREN0の順で、  
11 = **Brown Out Reset**を使用  
10 = Sleep Modeに入っていないときだけ、**Brown Out Reset**を使用  
01 = PCONレジスタのSBORENビットで**Brown Out Reset**を使用するかどうかを選択  
00 = **Brown Out Reset**を使用しない

#### Brown Out Resetとは

電源電圧がブラウンアウト・リセット電圧よりも低い場合に、PICをリセットする機能です。  
ブラウンアウト・リセット電圧は、通常約2Vに設定されています。  
  
  
### CPDビット

データ**EEPROMメモリをコード・プロテクト（Code Protect）**するかどうかを選択します。  
EEPROMをCode Protectする場合は、1（**CPDEN**）に設定します。  
EEPROMをCode Protectしない場合は、0（**CPDDIS**）に設定します。

#### Code Protect（EEPROM）とは

Code Protectをプログラムメモリに行うと、PICライターを使ってもEEPROMからEEPROMに保存されているデータ を読みだせなくなる。  
Code ProtectをOFFにしておくと、PICライターを使って現在PICに書き込まれているEEPROMの中身を読み取ることができる。  
  
  
### CPビット

プログラムメモリをコード・プロテクト（Code Protect）するかどうかを選択します。

プログラムをCode Protectする場合は、1（**CPEN**）に設定します。  
プログラムをCode Protectしない場合は、0（**CPDIS**）に設定します。

#### Code Protect（プログラムメモリ）とは

Code Protectをプログラムメモリに行うと、PICライターを使ってもプログラムメモリからプログラムを読みだせなくなる。  
Code ProtectをOFFにしておくと、PICライターを使って現在PICに書き込まれているプログラムを読み込むことができる。  
  
  
### MCLRビット

**外部リセット入力**を利用するかどうかを選択します。  
外部リセット入力を利用するときは1（**MCLREN**）に設定します。  
外部リセット入力を利用するときは0（**MCLRDIS**）に設定します。

#### 外部リセット入力とは

外部リセット入力をONにすると、MCLR端子がリセット入力用の端子として使われる。そのとき、通常時はMCLR端子をプルアップしておけばPICは正常動作し、MCLRの電圧がLOWになればリセットがかかる。  
外部リセット入力をOFFにしている場合は、MCLR端子をI/O入力用の端子として使用できる。  
  
#### 注意！

外部リセット入力機能をOFFにすると、**次回から正常にプログラムを書き込めなくなる場合があります**。そのため、外部リセット入力機能は**ON**にしておくことをお勧めする。  
  
  
### PWRTEビット

**パワーアップ・タイマー（Power Up Timer**）を利用するかどうかを選択します。

**Power Up Timer**を使用する場合は、1（PWREN）に設定します。  
**Power Up Timer**を使用しない場合は、0（PWRDIS）に設定します。

#### Power Up Timerとは

パワーアップ・タイマーを使用すると、PICに電源が供給されてから64msの間、PICマイコンをリセットする。こうすることで、電源が安定した状態になるまで待つことができ、不安定な動作を防ぐことができる。  
  
  
### WDTEビット

**ウォッチドックタイマー（Watch Dog Timer）**を使用するかどうかを選択します。

**Watch Dog Timer**を使用しない場合は、0（WDTDIS）を選択します。  
**Watch Dog Timer**を使用する場合は、1（WDTEN）を選択します。

#### Watch Dog Timerとは

WatchDogTimerとは、WDTとも省略されますが、タイムアウトするとPICをリセットさせるタイマーです。タイムアウトまでの時間を設定できるので、一定時間たてば自動的にPICをリセットさせたいときに使用します。  
  
### FOSCビット

ここで、どの**発振回路**を使うかを設定します。

FOSC2、FOSC1、FOSC0の順で、  
111 = **RCCLK**モード  
110 = **RCIO**モード  
101 = **INTCLK**モード  
100 = **INTIO**モード  
011 = **EC**モード  
010 = **HS**モード  
001 = **XT**モード  
000 = **LP**モード

#### 発振回路とは

システムクロックを発生させるための回路です。  
PICには内蔵発振回路があり、それを使うこともできますが、新たに部品を付け加えて、外部でクロックを生成してそのクロックをシステムクロックとして使用することもできます。  
  
  
### コンフィグレーションビットの設定方法（MPLAB）

[ここ](https://shizenkarasuzon.hatenablog.com/entry/2018/11/25/155431#4CONFIG%E3%81%AE%E8%A8%AD%E5%AE%9A)を参考にしてください。

  
かつては以下のように書く方法もありましたが、今ではこの書き方はエラーになるようです。

__CONFIG(FCMDIS & IESODIS & BOREN & UNPROTECT & MCLREN & PWRTEN & WDTDIS & INTIO);

  
代わりに、**コンフィグレーションビットのコードを自動生成**してくれる機能が追加されました。

右上に検索（Search）項目があるので、ここから「**Set Configuration Bits**」というActionを検索して実行してください。  
![f:id:pythonjacascript:20181125152522j:plain](/images/ppythonjacascript2018112520181125152522.jpg "f:id:pythonjacascript:20181125152522j:plain")

すると、右下にこのような画面が出てきます。ここで、Configuration Bits の設定を行います。  
設定例として写真で上げておきます。  
![f:id:pythonjacascript:20181125154125j:plain](/images/ppythonjacascript2018112520181125154125.jpg "f:id:pythonjacascript:20181125154125j:plain")

このように設定して、「**Generate Source Code to Output**」をクリックすると、このようなソースコードが自動生成されます。

  
自動生成されたコードの例：

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

[shizenkarasuzon.hatenablog.com](https://shizenkarasuzon.hatenablog.com/entry/2018/11/25/155431)