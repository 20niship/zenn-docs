---
title: "VertualBoxを使って仮想環境を作成（Windos10）"
emoji: "🤖"
type: "tech"
topics: undefined
published: false
---

* [1.Vertual Box とは？](#1Vertual-Box-とは)
* [2\. ダウンロード＆インストール](#2-ダウンロードインストール)
* [3.仮想マシンの作成](#3仮想マシンの作成)
* [4.ディスクイメージの作成](#4ディスクイメージの作成)
* [4.実行](#4実行)
* [仮想OSの終了](#仮想OSの終了)

### 1.Vertual Box とは？

**Vertual Box** とは、**仮想マシン**の一種です。

「仮想マシン」とは、PC上で**仮想的なPCを作成**して、別のOSをその仮想PC上で実行することを指します。仮想機械によって、1つのコンピュータ上で複数のコンピュータやOSやプログラムを動作させることができます。

その中でも代表的な仮想マシンとして、「**VirtualBox**」や「**VMware**」などがありますが、今回はVirtualBoxを扱います。

下の写真が、実際にVertualBoxを使用したときの様子です。  
[![https://upload.wikimedia.org/wikipedia/commons/thumb/7/7c/VirtualBox_screenshot.png/300px-VirtualBox_screenshot.png](https://upload.wikimedia.org/wikipedia/commons/thumb/7/7c/VirtualBox_screenshot.png/300px-VirtualBox_screenshot.png)](https://upload.wikimedia.org/wikipedia/commons/thumb/7/7c/VirtualBox%5Fscreenshot.png/300px-VirtualBox%5Fscreenshot.png)  
（Wikipediaより）  
  
### 2\. ダウンロード＆インストール

では、VertualBoxをダウンロードします。

以下のサイトにアクセスし、Windowsユーザーは、「**windows hosts**」をクリックします。   
[Downloads – Oracle VM VirtualBox](https://www.virtualbox.org/wiki/Downloads)

すると、「**VirtualBox-5.2.22-126460-Win.exe**」というファイルがダウンロードされるので、実行します。
  
  
![f:id:pythonjacascript:20181218194533j:plain](/images/ppythonjacascript2018121820181218194533.jpg "f:id:pythonjacascript:20181218194533j:plain")  
「**Next**」をクリック

  
![f:id:pythonjacascript:20181218194537j:plain](/images/ppythonjacascript2018121820181218194537.jpg "f:id:pythonjacascript:20181218194537j:plain")  
インストールする内容を選択します。  
僕は何もせずに「Next」をクリックして、全部インストールしました。

  
![f:id:pythonjacascript:20181218194539j:plain](/images/ppythonjacascript2018121820181218194539.jpg "f:id:pythonjacascript:20181218194539j:plain")  
デスクトップ上にアイコンを設定するかなどを選べます。僕は上の画面のように設定しました。

  
![f:id:pythonjacascript:20181218194542j:plain](/images/ppythonjacascript2018121820181218194542.jpg "f:id:pythonjacascript:20181218194542j:plain")  
**VertualBoxのインターネット機能をインストールするには、一時的にインターネットの接続を切断する必要があるよ**、という警告です。  
「Yes」をクリックします。

![f:id:pythonjacascript:20181218194546j:plain](/images/ppythonjacascript2018121820181218194546.jpg "f:id:pythonjacascript:20181218194546j:plain")  
「**Install**\]を押すとインストールが始まります。

![f:id:pythonjacascript:20181218194550j:plain](/images/ppythonjacascript2018121820181218194550.jpg "f:id:pythonjacascript:20181218194550j:plain")  
インストール中です....

  
![f:id:pythonjacascript:20181218194554j:plain](/images/ppythonjacascript2018121820181218194554.jpg "f:id:pythonjacascript:20181218194554j:plain")  
インストールが完了しました。  
ここにチェックを入れると、「**Finish**」を押した後にVertualBoxが起動します。  
インストール画面を閉じるには「**Finish**」を押します。

  
![f:id:pythonjacascript:20181218194558j:plain](/images/ppythonjacascript2018121820181218194558.jpg "f:id:pythonjacascript:20181218194558j:plain")  
この画面がVertualBoxの初期画面です。  
この画面が開いたらインストール成功です。  
  
### 3.仮想マシンの作成

左上の「新規」ボタンから、新しく仮想マシンを作成します。  
![f:id:pythonjacascript:20181218213913j:plain](/images/ppythonjacascript2018121820181218213913.jpg "f:id:pythonjacascript:20181218213913j:plain")  
ここでは、設定はこのようにしました。

![f:id:pythonjacascript:20181218214001j:plain](/images/ppythonjacascript2018121820181218214001.jpg "f:id:pythonjacascript:20181218214001j:plain")  
HariboteOSに割り当てるメモリ量を設定します。

![f:id:pythonjacascript:20181218214012j:plain](/images/ppythonjacascript2018121820181218214012.jpg "f:id:pythonjacascript:20181218214012j:plain")  
ここでは、「**仮想ハードディスクは作成しない**」を選択します。後からフロッピーディスクを追加してそこからディスクイメージをインポートします。

![f:id:pythonjacascript:20181218214146j:plain](/images/ppythonjacascript2018121820181218214146.jpg "f:id:pythonjacascript:20181218214146j:plain")  
警告が出ますが、「続ける」を押して仮想マシン作成を続行します。

![f:id:pythonjacascript:20181218214301j:plain](/images/ppythonjacascript2018121820181218214301.jpg "f:id:pythonjacascript:20181218214301j:plain")  
このように、「HariboteOS」が作成されていればOKです。  
  
### 4.ディスクイメージの作成

上の画面の上のボタン「**設定**」→「**ストレージ**」をクリックします。  
そして、下の写真のように、「**フロッピーディスクを追加**」します。  
![f:id:pythonjacascript:20181218214512j:plain](/images/ppythonjacascript2018121820181218214512.jpg "f:id:pythonjacascript:20181218214512j:plain")

  
![f:id:pythonjacascript:20181218214553j:plain](/images/ppythonjacascript2018121820181218214553.jpg "f:id:pythonjacascript:20181218214553j:plain")  
「**ディスクを選択**」を押すと、エクスプローラが開くので、「イメージファイル（ここでは**helloos1.img**)」を選択します。

すると、下のようになり、「helloos.img」が読み込まれたことがわかります。  
![f:id:pythonjacascript:20181218214745j:plain](/images/ppythonjacascript2018121820181218214745.jpg "f:id:pythonjacascript:20181218214745j:plain")  
以上で設定は終了です。  
設定ウィンドウを閉じてください。  
  
### 4.実行

「**起動→**」ボタンを押します。  
すると、このように「Hello,World！」と表示されます。  
![f:id:pythonjacascript:20181218214756j:plain](/images/ppythonjacascript2018121820181218214756.jpg "f:id:pythonjacascript:20181218214756j:plain")

### 仮想OSの終了

仮想マシンを実行中のウィンドウの、右上のバツ印を押すと下のような選択画面が表示されます。  
![f:id:pythonjacascript:20181218215107j:plain](/images/ppythonjacascript2018121820181218215107.jpg "f:id:pythonjacascript:20181218215107j:plain")  
「電源を切る」を押すと、仮想マシン用に占領していたメモリやストレージが解放されるようです。