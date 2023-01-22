---
title: "【PIC】PIC開発に必要なもの"
emoji: ""
type: ""
topics: []
published: false
---

PICというマイコンがあります。  
[shizenkarasuzon.hatenablog.com](https://shizenkarasuzon.hatenablog.com/entry/2018/11/25/010712)

PICは、比較的簡単に誰でも開発を行うことができるマイコンです。

この記事では、  
**PICを使うには何が必要なのか？**  
について書きます。

  
必要なものは以下の通り！

* [PIC本体](#PIC本体)
* [PICライター](#PICライター)
* [・シリアル・USB変換ケーブル](#シリアルUSB変換ケーブル)
* [PICライター用電源](#PICライター用電源)
* [パソコン](#パソコン)
* [MPLAB X](#MPLAB-X)
* [XCファミリー](#XCファミリー)
* [PICプログラマー4 Beta](#PICプログラマー4-Beta)
* [周辺回路用電子部品](#周辺回路用電子部品)

### PIC本体

![f:id:pythonjacascript:20181124235242j:plain](/images/ppythonjacascript2018112420181124235242.jpg "f:id:pythonjacascript:20181124235242j:plain")  
何といっても絶対にPICそのものがなければ始まりません。  
下のリンクから購入ページを見てもらえるとわかりますが、PIC自体は**安価**です！安いものだと**50円以下**（税別）で売られています。

主な入手先として**秋月電子通商**や**マルツパーツ**があります。  
秋月電子通商：[マイコン関連/PIC 秋月電子通商-電子部品・ネット通販](http://akizukidenshi.com/catalog/c/cpicr/)  
マルツパーツ：[定格検索結果 PIC/マイコン](https://www.marutsu.co.jp/RatedList.jsp?goodsClassCode1=19&goodsClassCode2=0024&goodsClassCode3=0008)

Amazonでスターターキットを買うのもありかもしれません。  
  
  
### PICライター

![f:id:pythonjacascript:20181125003137j:plain](/images/ppythonjacascript2018112520181125003137.jpg "f:id:pythonjacascript:20181125003137j:plain")  
（秋月電子通称から購入、 **AKI-PICプログラマーVer.4**）  
PICはプログラムを書き込んで初めて動作します。なので、プログラムを書き込むための専用ライターが必要です。

ライターを購入せずに、下のサイトのように自作することもできますが、あまりお勧めはできません。  
[自作PICライター「PICerFT」の作例。 桐井研究室](http://kirylabo.blog.fc2.com/blog-entry-88.html)  
自作したライターで書き込めるPICの種類が少ない上に、もしプログラムが動かなかった場合ライターが悪いのか、PICが悪いのか、プログラムが悪いのかがわからないからです。（市販のPICライターは、PICが死んでいないかを確かめる機能がある）

  
PICライターには、主なもので次の2種類があります。  
・**AKI-PICプログラマーVer.4**（秋月電子製）  
・**PicKit3**（PICの製造会社のMicroChip製）  
  
  
### ・シリアル・USB変換ケーブル

**AKI-PICプログラマーを利用している場合のみ必要です。**  
**PICKitを利用している場合は不用**

**シリアル（オス）**と**ＵＳＢ**を変換するケーブルです。  
のものです。

シリアルポートとは、このようなDサブコネクタの9ピンのものをいいます。  
![f:id:pythonjacascript:20181126213924j:plain](/images/ppythonjacascript2018112620181126213924.jpg "f:id:pythonjacascript:20181126213924j:plain")  
（Wikipediaより）

参考までに。このようなものです↓  
[ＵＳＢ−シリアル変換ケーブル スケルトン: 半導体 秋月電子通商-電子部品・ネット通販](http://akizukidenshi.com/catalog/g/gM-00720/)
  
  
### PICライター用電源

**AKI-PICプログラマーを利用している場合のみ必要です。**  
**PICKitを利用している場合は不用**

**15V200ｍA以上**で、端子が**2.1mmDCジャック**のものでなければいけません。  
センタープラスでもセンターマイナスでも両方OKです。

このようなACアダプターならOKです。  
[超小型スイッチングＡＣアダプター１５Ｖ０．８Ａ １００〜２４０Ｖ ＧＦ１２−ＵＳ１５０８: 電源一般 秋月電子通商-電子部品・ネット通販](http://akizukidenshi.com/catalog/g/gM-01805/)

  
僕は、このように **AKI-PICプログラマーVer.4**と専用のACアダプターを使っています。  
![f:id:pythonjacascript:20181126195710j:plain:h300](/images/ppythonjacascript2018112620181126195710.jpg "f:id:pythonjacascript:20181126195710j:plain:h300")  
  
  
### パソコン

![f:id:pythonjacascript:20181126212254j:plain:h300](/images/ppythonjacascript2018112620181126212254.jpg "f:id:pythonjacascript:20181126212254j:plain:h300")  
Windows製でもMac製でも構いませんが、  
**・インターネットに接続できる** 
**・USBポートがある**  
の2つが条件です。

  
以下は、PCで動かすための**ソフトウェア**です

### MPLAB X

![f:id:pythonjacascript:20181125151914j:plain](/images/ppythonjacascript2018112520181125151914.jpg "f:id:pythonjacascript:20181125151914j:plain")  
PIC開発専用のIDE（統合開発環境）です。  
PICのプログラムの編集作業などを行います。Microchip社のホームページから無料でダウンロードできます。  
[MPLAB- X IDE | Microchip Technology](https://www.microchip.com/mplab/mplab-x-ide)

ダウンロード・インストール方法はこちら  
[shizenkarasuzon.hatenablog.com](https://shizenkarasuzon.hatenablog.com/entry/2018/11/25/130238)

基本的な使い方はこちら  
[shizenkarasuzon.hatenablog.com](https://shizenkarasuzon.hatenablog.com/entry/2018/11/25/155431)  
  
  
### XCファミリー

PIC用のCコンパイラです。  
無償版と有償版がありますが、最適化を行ってくれるかどうかの違いなので、無償版でもPIC開発は可能です。  
PICの命令ビット長に分けて、**XC8**（8、12ビット用）、**XC16**（16ビット用）、**XC32**（32ビット用）の三種類があります。

こちらもMicrochip社のホームページからダウンロードできます。  
[MPLAB- XC Compilers | Microchip Technology](https://www.microchip.com/mplab/compilers)

  
ダウンロード・インストール方法はこちら  
[shizenkarasuzon.hatenablog.com](https://shizenkarasuzon.hatenablog.com/entry/2018/11/25/164722)
  
  
### PICプログラマー4 Beta

![f:id:pythonjacascript:20181126191639j:plain](/images/ppythonjacascript2018112620181126191639.jpg "f:id:pythonjacascript:20181126191639j:plain")  
このソフトウェアは、PICライターに「AKI-PICプログラマー4 Beta」を使用している人のみ必要です。  
PICKitを使用している場合は、このソフトウェアの役割を MAPLAB Xが行ってくれるので必要ありません。

**PICプログラマー4 Beta** は、MPLABで作成したプログラムを、PICライターを通してPICに書き込むためのソフトウェアです。

秋月電子通称のホームページから無償でダウンロードできます。  
[akizukidenshi.com](http://akizukidenshi.com/catalog/contents2/ver4support.aspx)

  
ダウンロード・インストール方法はこちら  
[shizenkarasuzon.hatenablog.com](https://shizenkarasuzon.hatenablog.com/entry/2018/11/25/132146)

  
### 周辺回路用電子部品

PICの周りの回路に使用する電子部品たちとしては、このようなものがあります。

| 電源              | PICの電源電圧は3～5V。乾電池でOK |
| --------------- | -------------------- |
| 積層セラコン（0.1μF程度） | PICへの電源平滑用           |
| 抵抗（1ｋΩ程度）       | MCLRのリセット電流入力用       |
| ブレッドボード         | あると超便利               |