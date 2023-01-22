---
title: "XAMPPPの使い方"
emoji: ""
type: ""
topics: undefined
published: false
---

### ダウンロード＆インストール

<https://www.apachefriends.org/jp/index.html>  
からexeをダウンロードしてインストールします。アンチウイルスソフトが動いていると警告が出るけど、必要な機能は問題なく動きます。  
  
### Hello, World!

XAMPPを実行します。（exeファイルは「（インストール先のフォルダ）/xampp-control.exe」）

下のような画面が出てくるので、「Apache」をSutartします

  
この状態で、ブラウザで

http://localhost/dashboard/

にアクセスすると、下のような画面が表示されるはずです。

  
これでインストールが成功していることがわかります。  
  
### 作成したHTMLファイルを表示する

XAMPPでは（インストール先のフォルダ）の中の htdocs フォルダの中身を表示することができます。

例えば、「（インストール先のフォルダ)/htdocs/sampe/test.html」をブラウザで表示するには、  
上の画像の様にXAMPPのコントロールパネルで「Apache」をスタートさせて

http://localhost/sample/text.html

にアクセスします。  
  
<html>
  <head>
    <meta charset="utf-8">
    <title>これはテストです</title>
  </head>
<body>
  <p>本文ーー</p>
</body>
</html>

### SSIを使う

<!--#include file="header.html"-->

のようなSSI（server-side includes）の構文を使うにはXAMPPの設定を変更する必要があります  
「（インストール先のフォルダ)/apache/conf/httpd.conf」の中の

> 『417行目あたりを  
> AddType text/html .shtml  
> AddOutputFilter INCLUDES .shtml  
> ↓  
> AddType text/html .shtml .html  
> AddOutputFilter INCLUDES .shtml .html  
> に変更する  
> そしてapacheを再起動すると大丈夫です。』

引用元：<https://qiita.com/rico/items/d923e9c5a4b671fc4902>
  
  
### 文字化けする

#### 原因1：HTML側

以下、HTMLファイルの文字コードを「UTF-8」で書いている体で話を進めます

「メモ帳」で「名前を付けて保存」を行うと文字コードを選択できます。ここではUTF-8を選択しています。

  
そして、HTMLファイルのタグ内に 

<head>

</head>

を追加します。  
  
  
#### 原因2：XAMPPP側

僕の環境では↑の方法だけで解決しましたが、この設定もいるのかも。

<https://qiita.com/guzuri/items/643c1311224e8d482278>