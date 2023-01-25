---
title: "【PIC】PIC開発に必要なもの, 環境構築、MATPLABXのインストール"
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



---
title: "【PIC】MAPLAB X のインストール"
emoji: ""
type: ""
topics: []
published: false
---

PICは**C言語**や**アセンブリ言語**のようなプログラムを書き込むことで動作します。  
そのため、PIC開発をするには、PIC用のプログラム開発を行うソフトウェア（**統合開発環境、IDE**）が必要です。

PICマイコンでプログラミングを行うための統合開発環境です。マイクロチップテクノロジー社のホームページから無料でダウンロードできます。

  
そのIDEの中で、Microchip社が無償提供しているIDEで、「**MAPLAB X**」ロいうものがあります。  
この記事では、MAPLAB Xのインストール方法を紹介します。

* [1.ダウンロード](#1ダウンロード)
* [インストール](#インストール)
* [試しに起動](#試しに起動)

### 1.ダウンロード

OSがWindowsの場合は、以下のリンクをクリックしてダウンロードを開始して下さい。  
<https://www.microchip.com/mplabx-ide-windows-installer>

Macの場合は、以下のリンクからダウンロードを開始して下さい。  
<https://www.microchip.com/mplabx-ide-osx-installer>

  
ダウンロードすると、「MPLABX-v5.10-windows-installer.exe」というファイルができているはずなので、それを実行してください。  
  
### インストール

ただ画面に沿ってポチポチしていくだけです。  
![f:id:pythonjacascript:20181125124054j:plain:h300](/images/ppythonjacascript2018112520181125124054.jpg "f:id:pythonjacascript:20181125124054j:plain:h300")  
「**NEXT**」をクリック

  
![f:id:pythonjacascript:20181125124058j:plain:h300](/images/ppythonjacascript2018112520181125124058.jpg "f:id:pythonjacascript:20181125124058j:plain:h300")  
「**I accept ...**」を押して「**Next**」をクリック

  
![f:id:pythonjacascript:20181125124101j:plain:h300](/images/ppythonjacascript2018112520181125124101.jpg "f:id:pythonjacascript:20181125124101j:plain:h300")  
インストールするフォルダを指定して「**Next**」をクリック。  
僕の場合は、デフォルトで上の写真のようなっていたのでこのまま進みました。

  
![f:id:pythonjacascript:20181125124107j:plain:h300](/images/ppythonjacascript2018112520181125124107.jpg "f:id:pythonjacascript:20181125124107j:plain:h300")  
上の二つのチェックを入れて「**Next**」をクリック

  
![f:id:pythonjacascript:20181125124110j:plain:h300](/images/ppythonjacascript2018112520181125124110.jpg "f:id:pythonjacascript:20181125124110j:plain:h300")  
「**Next**」をクリック

  
途中、このような確認画面が表示されるかもしれませんが、すべて「**インストール**」を選択してください。  
![f:id:pythonjacascript:20181125125009j:plain](/images/ppythonjacascript2018112520181125125009.jpg "f:id:pythonjacascript:20181125125009j:plain")

  
インストールが完了すると、下のような画面になるので、すべてチェックを外して「Finish」をクリックしてください。  
![f:id:pythonjacascript:20181125125235j:plain:h300](/images/ppythonjacascript2018112520181125125235.jpg "f:id:pythonjacascript:20181125125235j:plain:h300")

  
これで、MAPLAB Xのインストールは完了です。  
デスクトップに以下のアイコンができていることを確認してください。  
![f:id:pythonjacascript:20181125125555j:plain](/images/ppythonjacascript2018112520181125125555.jpg "f:id:pythonjacascript:20181125125555j:plain")

### 試しに起動

デスクトップのアイコン「**MAPLAB X IDE**」をクリックしてみてください。  
このような画面が表示されればOKです。  
![f:id:pythonjacascript:20181125125921j:plain](/images/ppythonjacascript2018112520181125125921.jpg "f:id:pythonjacascript:20181125125921j:plain")




---
title: "【PIC】PIC Programmer4 Beta のインストール"
emoji: ""
type: ""
topics: []
published: false
---

前回の記事で、**MAPLAB X**（PICのIDE）のインストール方法を紹介しました。MAPLAB Xを使うと、PIC用のプログラムの編集ができます。

**※前回の記事：**  
[shizenkarasuzon.hatenablog.com](https://shizenkarasuzon.hatenablog.com/entry/2018/11/25/130238)

ですが、PICを動かすにはそのプログラムをPICに書き込まなければなりません。PICライターに「**PicKit3**（下の写真）」などを使用している場合は、MAPLAB XのGUI操作でPICにプログラムを書き込むことができます。  
![f:id:pythonjacascript:20181125134457j:plain](/images/ppythonjacascript2018112520181125134457.jpg "f:id:pythonjacascript:20181125134457j:plain")

しかし、PICライターに「**AKI－PICプログラマボード**（下の写真）」を使用している場合、MAPLAB XではPICへの書き込みができません。  
![f:id:pythonjacascript:20181125003137j:plain](/images/ppythonjacascript2018112520181125003137.jpg "f:id:pythonjacascript:20181125003137j:plain")

AKI－PICプログラマボードはその名の通り秋月電子が開発しているボードで、MAPLAB（Microchip社が開発）はサポートしていないのです。つまり、AKI－PICプログラマボード専用のPIC書き込みソフトウェアである、**PIC Programmer4 Beta**が必要です。

  
この記事では、**PIC Programmer4 Beta**のインストール方法を紹介します。  
  
* [ダウンロード](#ダウンロード)
* [インストール](#インストール)
* [試しに起動](#試しに起動)

### ダウンロード

Windowsの場合、下記リンクからダウンロードしてください。  
<http://akizukidenshi.com/img/contents/ver4/archives/picpgm6%5FJK%5FP676%5FF676%5Fbeta.zip>

すると「picpgm6\_JK\_P676\_F676\_beta.zip」というファイルができるので、解凍します。  
解凍後、以下のようなファイルができていればOKです。  
![f:id:pythonjacascript:20181125140829j:plain](/images/ppythonjacascript2018112520181125140829.jpg "f:id:pythonjacascript:20181125140829j:plain")  

### インストール

上の写真のファイルのうち、「**setup.exe**」を実行し、画面の指示に従ってセットアップを進めていきます。

![f:id:pythonjacascript:20181125133122j:plain](/images/ppythonjacascript2018112520181125133122.jpg "f:id:pythonjacascript:20181125133122j:plain")  
「**OK**」をクリック

  
![f:id:pythonjacascript:20181125133126j:plain](/images/ppythonjacascript2018112520181125133126.jpg "f:id:pythonjacascript:20181125133126j:plain")  
インストールするフォルダを指定して、パソコンマークをクリック  
僕はこのデフォルト設定で進みました。

  
![f:id:pythonjacascript:20181125133134j:plain](/images/ppythonjacascript2018112520181125133134.jpg "f:id:pythonjacascript:20181125133134j:plain")  
「**継続**」をクリック

  
![f:id:pythonjacascript:20181125133139j:plain](/images/ppythonjacascript2018112520181125133139.jpg "f:id:pythonjacascript:20181125133139j:plain")  
「**OK**」をクリック  
これで、**PIC Programmer4 Beta**のインストールが完了しました。  
  
### 試しに起動

このようなアイコンがスタートメニュー上にできていると思うので、クリックしてください。  
![f:id:pythonjacascript:20181125134829j:plain](/images/ppythonjacascript2018112520181125134829.jpg "f:id:pythonjacascript:20181125134829j:plain")

このような画面が開いたらOKです。  
![f:id:pythonjacascript:20181125135010j:plain](/images/ppythonjacascript2018112520181125135010.jpg "f:id:pythonjacascript:20181125135010j:plain")



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