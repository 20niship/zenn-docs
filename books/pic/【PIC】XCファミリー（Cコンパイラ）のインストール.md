---
title: "【PIC】XCファミリー（Cコンパイラ）のインストール"
emoji: ""
type: ""
topics: undefined
published: false
---

PIC用のCコンパイラの1つに、**MPLAB XC8**、**MPLAB XC16**、**MPLAB XC32**というものがあります。  
このコンパイラをインストールすると、MAPLB X（PIC開発用のIDE）でC言語でのPIC開発が可能になります。

今回は、このXCファミリーをインストールする方法を書きます。

* [1.XCファミリーの種類](#1XCファミリーの種類)  
   * [MPLAB XC8](#MPLAB-XC8)  
   * [MPLAB XC16](#MPLAB-XC16)  
   * [MPLAB XC32](#MPLAB-XC32)  
   * [Free版](#Free版)  
   * [Standard版](#Standard版)  
   * [PRO版](#PRO版)
* [2.ダウンロード](#2ダウンロード)
* [補足（MAPLABからダウンロード）](#補足MAPLABからダウンロード)
* [インストール](#インストール)
* [参考文献](#参考文献)

### 1.XCファミリーの種類

XCファミリには下記の3種類があります。  
[MPLAB- XC Compilers | Microchip Technology](https://www.microchip.com/mplab/compilers)  

#### MPLAB XC8

**8ビット**PIC用のCコンパイラです。**PIC10/12/16/18**にプログラムを書き込むことができます。  
ダウンロードリンク：  
<https://www.microchip.com/mplabxc8windows>

#### MPLAB XC16

**16ビット**PIC用のCコンパイラです。**PIC24/dsPIC**にプログラムを書き込むことができます。  
ダウンロードリンク：  
<https://www.microchip.com/mplabxc16windows>

#### MPLAB XC32

**32ビット**PIC用のCコンパイラです。**PIC32**にプログラムを書き込むことができます。  
ダウンロードリンク：  
<https://www.microchip.com/mplabxc32windows>
  
  
またそれぞれに最適化レベルで下記の3種類の分かれています。

#### Free版

最適化機能なしのフリーバージョンで機能はすべて使えます。ビルドやシミュレーションもできるので、ちょっと扱ってみようという場合は、Free版で十分です。  
インストール後60日間はPRO版として動作します。

#### Standard版

最適化レベルが中程度のバージョンで有償です。  
  
#### PRO版

最適化レベルが最高レベルとなっています。  
有償で、＄995です。  
[SW006021-2 - MPLAB XC8 PRO Compiler (Workstation License)](https://www.microchip.com/Developmenttools/ProductDetails/SW006021-2#additional-summary)

  
詳しくは、以下ホームページを確認してください。  
[MPLAB- XC Compilers | Microchip Technology](https://www.microchip.com/mplab/compilers#utm%5Fmedium=Press-Release&utm%5Fterm=XC-Compiler-Subscriptions%5FPR%5F12-1-15&utm%5Fcontent=Tools&utm%5Fcampaign=Compilers)

  
### 2.ダウンロード

上の三種類のXCシリーズから適当なものを選んで、上のダウンロードリンクからダウンロードしてください。  
「**xc8-v2.00-full-install-windows-installer.exe**」のようなファイルがダウンロードされるので、それをクリックして実行します。  
  
  
### 補足（MAPLABからダウンロード）

もし、MAPLBXで新規プロジェクトを作成するときにこのような画面が表示され、XCファミリーが選択できなければ、  
![f:id:pythonjacascript:20181125145549j:plain:h300](/images/ppythonjacascript2018112520181125145549.jpg "f:id:pythonjacascript:20181125145549j:plain:h300")  
青文字の「**Download Latest**」をクリックしてください。  
ダウンロード場所は、デフォルト「**C:\\Users\\○○○○\\Downloads**」のままにします。

その後は、自動でダウンロードが開始されて、[下の手順](https://shizenkarasuzon.hatenablog.com/entry/2018/11/25/164722#%E3%82%A4%E3%83%B3%E3%82%B9%E3%83%88%E3%83%BC%E3%83%AB)通りにインストールを行います。

インストールが完了すると、このようなダイアログメッセージが表示されるので、  
![f:id:pythonjacascript:20181125164256j:plain](/images/ppythonjacascript2018112520181125164256.jpg "f:id:pythonjacascript:20181125164256j:plain")  
「**OK**」を押すと、

![f:id:pythonjacascript:20181125150816j:plain](/images/ppythonjacascript2018112520181125150816.jpg "f:id:pythonjacascript:20181125150816j:plain")  
このように、XCコンパイラーを選択できるようになります。  
  
  
### インストール

![f:id:pythonjacascript:20181125163342j:plain](/images/ppythonjacascript2018112520181125163342.jpg "f:id:pythonjacascript:20181125163342j:plain")  
「**Next**」をクリック。

  
![f:id:pythonjacascript:20181125163345j:plain](/images/ppythonjacascript2018112520181125163345.jpg "f:id:pythonjacascript:20181125163345j:plain")  
「**I accept..**.」を選択し、「**Next**」をクリック

  
![f:id:pythonjacascript:20181125163347j:plain](/images/ppythonjacascript2018112520181125163347.jpg "f:id:pythonjacascript:20181125163347j:plain")  
私はFree版を使用しているので、このまま**Next**をクリックしました。

  
![f:id:pythonjacascript:20181125163351j:plain](/images/ppythonjacascript2018112520181125163351.jpg "f:id:pythonjacascript:20181125163351j:plain")  
インストールするディレクティブを選択します。特に変更する必要はありません。

  
![f:id:pythonjacascript:20181125163353j:plain](/images/ppythonjacascript2018112520181125163353.jpg "f:id:pythonjacascript:20181125163353j:plain")  
両方にチェックを入れて、「**Next**」をクリック

  
![f:id:pythonjacascript:20181125163357j:plain](/images/ppythonjacascript2018112520181125163357.jpg "f:id:pythonjacascript:20181125163357j:plain")  
「**Next**」をクリック

  
![f:id:pythonjacascript:20181125163403j:plain](/images/ppythonjacascript2018112520181125163403.jpg "f:id:pythonjacascript:20181125163403j:plain")  
Free版を使用しているので何もせずに「Next」をクリックしました。

  
![f:id:pythonjacascript:20181125163406j:plain](/images/ppythonjacascript2018112520181125163406.jpg "f:id:pythonjacascript:20181125163406j:plain")  
これでインストールは完了です。「**Finish**」をクリックします。  
  
  
### 参考文献

[MPLAB X IDE](http://www.picfun.com/xcframe.html)