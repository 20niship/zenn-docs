---
title: "【AviUtl】AviUtlでGIFアニメーションでエンコードする方法"
emoji: "🤖"
type: "tech"
topics: []
published: false
---

ブログに動画を載せたいと思い、GIFアニメーションをAviUtlで出力できないかな？と調べたら案の定プラグインがあったので、その使い方について書きます。

**AviUtl**を使ってGIFアニメーションを作成する方法です。

* [1.プラグインのダウンロード](#1プラグインのダウンロード)
* [2.プラグインのインストール](#2プラグインのインストール)
* [3.エンコードの設定](#3エンコードの設定)  
   * [フレームレートの設定](#フレームレートの設定)
* [4.出力先フォルダの設定](#4出力先フォルダの設定)
* [サンプル](#サンプル)
* [参考文献](#参考文献)

### 1.プラグインのダウンロード

以下のサイトからプラグインをインストールします。  
[OneDrive](https://onedrive.live.com/?authkey=%21AHg31ARXSfIHQUc&id=26840CDF9818EB8E%21391&cid=26840CDF9818EB8E)

上のサイトに飛ぶと、「**DGE2.zip**」という圧縮ファイルがあるので、そちらをダウンロード＆解凍します。

  
※「DGE2.zip」ではなく「AGifExportPack.7z」をダウンロードするように書いてあるサイトもありましたが、Lhaplusを使った解凍中にエラーが出たため、「DGE2.zip」を利用させていただきました。

  
### 2.プラグインのインストール

解凍が完了すると、下の写真のように5つのファイルができていると思います。  
![f:id:pythonjacascript:20181215234515j:plain:h150](/images/ppythonjacascript2018121520181215234515.jpg "f:id:pythonjacascript:20181215234515j:plain:h150")

そのうち、readme.txtを除く3つのファイルをAviUtlのフォルダにコピーします。  
  
  
### 3.エンコードの設定

新規プロジェクトを作成し、何かしらの動画の書き出しを行ってみます。

エンコードを行う際、「**ファイル**」→「**プラグイン出力**」→「**Direct Animated GIF export 2**」を選択します。もし、「Direct Animated GIF export 2」が表示されない場合は、AviUtlをいったん閉じて、もう一度↑のSTEP2を行って下さい。

![f:id:pythonjacascript:20181215234924j:plain:h350](/images/ppythonjacascript2018121520181215234924.jpg "f:id:pythonjacascript:20181215234924j:plain:h350")

  
「Direct Animated GIF export 2」を選択すると、次のような画面が現れるので、「**ビデオ圧縮**」ボタンをクリックします。  
![f:id:pythonjacascript:20181215235254j:plain:h350](/images/ppythonjacascript2018121520181215235254.jpg "f:id:pythonjacascript:20181215235254j:plain:h350")

  
すると、下のような画面が現れます。ここで、ビデオ圧縮の設定を行います。  
![f:id:pythonjacascript:20181215235313j:plain:h350](/images/ppythonjacascript2018121520181215235313.jpg "f:id:pythonjacascript:20181215235313j:plain:h350")

  
その前に、初回エンコード時は「**Gifscale's path**」と書かれた部分に、STEP2でコピーした「**gifsicle\_x64.exe**」の**絶対パス**を打ち込んでください。相対パスだとうまく動きません！

私の環境の場合、「**C:\\Program Files\\aviutl100\\gifsicle\_x64.exe**」と入力すれば正常動作しました。  
  
  
#### フレームレートの設定

良くいじる部分は「**3\. Target Framerate:**」の部分です。ここでフレームレート（FPS）を設定します。  
上の写真では「5/1」となっているので、一秒間に5フレームです。

  
その他にも「**Color Limit**」や「**Dither Level**」、「**Speed**」など多くの設定項目がありますが、とくに変更は不要です。

  
そして、すべての設定が終わったら、「**OK**」ボタンを押します。  
  
  
### 4.出力先フォルダの設定

すると、また下の画面に戻るので、ここで動画の出力先フォルダの設定を行います。  
![f:id:pythonjacascript:20181215235254j:plain:h350](/images/ppythonjacascript2018121520181215235254.jpg "f:id:pythonjacascript:20181215235254j:plain:h350")

  
拡張子は「.gif」です。

そして、「**保存（ｓ）**」ボタンを押すと、エンコードが始まります。  
  
### サンプル

以上の方法を使って製作したGIFアニメーションのサンプルです。  
![f:id:pythonjacascript:20181216000328g:plain](/images/ppythonjacascript2018121620181216000328.gif "f:id:pythonjacascript:20181216000328g:plain")

### 参考文献

[aviutl.info](http://aviutl.info/gif-puraguinn/)