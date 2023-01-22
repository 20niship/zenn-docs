---
title: "【HTML】メモ帳とgoogleを使ってHTML編集"
emoji: ""
type: ""
topics: []
published: false
---

前回、Visual Studio を使ってHTMLを編集する記事を上げました。

前回の記事はこちら：  
[shizenkarasuzon.hatenablog.com](https://shizenkarasuzon.hatenablog.com/entry/2018/09/02/101702)

  
ですが、 Visual Studioをインストールして開発環境を整えるのが**面倒**、という場合、

**メモ帳**を使ってHTMLファイルを作ることができます。その方法を紹介します。  
  
### 1、メモ帳を開く

普通にメモ帳アプリを起動します。テキストエディタでも可能です。  
![f:id:pythonjacascript:20180904005544p:plain](/images/ppythonjacascript2018090420180904005544.png "f:id:pythonjacascript:20180904005544p:plain")
  
  
### 2、HTMLのプログラムを書く

メモ帳に文章を書くようにして、普通にHTMLのプログラムを書きます。  
![f:id:pythonjacascript:20180904005559p:plain](/images/ppythonjacascript2018090420180904005559.png "f:id:pythonjacascript:20180904005559p:plain")

  
動作確認だけしたい方は、以下のサンプルプログラムをコピペしてください。  
サンプルプログラム：

<html lang="en" xmlns="http://www.w3.org/1999/xhtml">
<head>
    <meta charset="utf-8" />
    <title>Title Here</title>
</head>
<body>
    <p>Hello, World!</p>
</body>
</html>

### 3、HTMLファイルとして保存する

「ファイル」→「名前をつけて保存」を押すと、次のような画面になります。  
![f:id:pythonjacascript:20180904005614p:plain](/images/ppythonjacascript2018090420180904005614.png "f:id:pythonjacascript:20180904005614p:plain")

そこで、通常の保存方法と違うところが2つあります。  
**・「ファイル名」の拡張子を「.html」にする** 
**・「ファイルの種類」を「テキスト文書（\*.txt)」から「すべてのファイル（\*.）」に変更する。**

このように保存されていればOKです。もし拡張子が「.txt」になっていると、HTMLファイルとして認識されないためブラウザで正常に開くことができません。  
![f:id:pythonjacascript:20180904010149p:plain](/images/ppythonjacascript2018090420180904010149.png "f:id:pythonjacascript:20180904010149p:plain")

### 4、ブラウザを使って開く

保存したHTMLファイルを好きなブラウザで開いてください。きちんと表示されているはずです。  
![f:id:pythonjacascript:20180904010143p:plain](/images/ppythonjacascript2018090420180904010143.png "f:id:pythonjacascript:20180904010143p:plain")