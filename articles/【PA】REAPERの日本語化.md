---
title: "【PA】REAPERの日本語化"
emoji: "🤖"
type: "tech"
topics: []
published: false
---

今回は、DAWのREAPERを日本語化してみます。  
![f:id:pythonjacascript:20190731222544j:plain](/images/ppythonjacascript2019073120190731222544.jpg "f:id:pythonjacascript:20190731222544j:plain")  

REAPERのダウンロード先はこちら：  
[REAPER | User Guide](https://www.reaper.fm/userguide.php)  

* [1.日本語化パッチのインストール](#1日本語化パッチのインストール)
* [2.日本語化](#2日本語化)
* [元の言語に戻す方法](#元の言語に戻す方法)

### 1.日本語化パッチのインストール

まずは、いかのURLから**「REAPER日本語化パッチ.exe」**をダウンロードします。

[Home · chiepomme/REAPERJapanesePatcher Wiki · GitHub](https://github.com/chiepomme/REAPERJapanesePatcher/wiki)

  
ダウンロードして解凍すると、**「REAPER日本語化パッチ.exe」**が作成されるので、実行します。  
EXEを実行する前に、REAPERを使用している場合は、一旦保存して**REAPERを終了してください。**

### 2.日本語化

「REAPER日本語化パッチ.exe」を実行すると、以下のような画面が出てくるので、指示に沿って操作していきます。

![f:id:pythonjacascript:20190731223142j:plain](/images/ppythonjacascript2019073120190731223142.jpg "f:id:pythonjacascript:20190731223142j:plain")  
まず、REAPER本体のexeファイルの場所を指定します。デフォルトでは上のように「Program Files→REAPER(x64)→reaper.exe」にあります。  
筆者は↑のような設定で実行しましたが、現在何の問題もなく使えています。

  
![f:id:pythonjacascript:20190731223204j:plain](/images/ppythonjacascript2019073120190731223204.jpg "f:id:pythonjacascript:20190731223204j:plain")  
↑の画面から「実行」ボタンを押すと、このようなエラーメッセージが出ることがあります。この場合は、管理者権限でもう一度実行して下さい。  
それでもエラーが出る場合は「続行」で何とかなります。（筆者も管理者権限で実行しても再起動してもエラーが出たので、「続行」を押して強引に動かしたら日本語化できました。）

  
![f:id:pythonjacascript:20190731223207j:plain](/images/ppythonjacascript2019073120190731223207.jpg "f:id:pythonjacascript:20190731223207j:plain")  
インストール中......

![f:id:pythonjacascript:20190731223212j:plain](/images/ppythonjacascript2019073120190731223212.jpg "f:id:pythonjacascript:20190731223212j:plain")  
「OK」を押すと日本語化パックがインストールされます。「Cancel」を押すとインストールがキャンセルされます。  
当然「OK」を選択します。

  
![f:id:pythonjacascript:20190731223216j:plain](/images/ppythonjacascript2019073120190731223216.jpg "f:id:pythonjacascript:20190731223216j:plain")

> 日本語インストールが完了しました。新しい言語に変更するには、REAPERを再起動してください

ということなので、REAPERを起動すると、無事日本語になっていました。

![f:id:pythonjacascript:20190731223228j:plain](/images/ppythonjacascript2019073120190731223228.jpg "f:id:pythonjacascript:20190731223228j:plain")  
メニューバーや設定の中なども日本語化されています。すごい！  
  
  
### 元の言語に戻す方法

日本語パッチを入れた後でも、英語やその他の言語に変更することができます。REAPERを開き、メニューバーの「**Option（オプション）**」から「**preferences（設定）**」を選択し、出てきたWindowの「**General（一般）**」のタブをクリックします。  
![f:id:pythonjacascript:20190731224952j:plain](/images/ppythonjacascript2019073120190731224952.jpg "f:id:pythonjacascript:20190731224952j:plain")

すると、Languageの部分に英語か日本語か選択するところがあるので、ここで表示言語を切り替えることができます。