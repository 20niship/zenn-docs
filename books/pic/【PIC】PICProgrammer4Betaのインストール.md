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