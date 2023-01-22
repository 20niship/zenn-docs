---
title: "【C++】Visual Studio 2019 に OpenSiv3D を導入する"
emoji: ""
type: ""
topics: []
published: false
---

![f:id:pythonjacascript:20190828213647j:plain](/images/ppythonjacascript2019082820190828213647.jpg "f:id:pythonjacascript:20190828213647j:plain")

* [OpenSiv3Dとは](#OpenSiv3Dとは)
* [開発環境（この記事）](#開発環境この記事)
* [開発環境（必要最低限）](#開発環境必要最低限)
* [Visual StudioにC++の開発環境を整える](#Visual-StudioにCの開発環境を整える)
* [Siv3Dの導入](#Siv3Dの導入)
* [開発環境を整える](#開発環境を整える)
* [サンプルプロジェクトを作成→実行](#サンプルプロジェクトを作成実行)
* [参考文献](#参考文献)

### OpenSiv3Dとは

**OpenSiv3D**とは、C++ で楽しく簡単にゲームやメディアアートを作れるフレームワークです。  
[play-siv3d.hateblo.jp](https://play-siv3d.hateblo.jp/)

  
以前はSiv3Dと呼ばれていましたが、OpenSiv3Dに変わりました。  
Siv3Dとの違いはここにまとめられています。  
[旧 Siv3D との違い - Siv3D](https://scrapbox.io/Siv3D/旧%5FSiv3D%5Fとの違い)

  
Siv3Dを使ったソフトウェア開発のための、開発環境を構築します。  
  
  
### 開発環境（この記事）

・Visual Studio 2019 Community version:16-2-3  
Visual Studio 2019をInstallした様子は、↓で確認できます。  
[Visual Studio Community 2019 のインストール - とある科学の備忘録](https://shizenkarasuzon.hatenablog.com/entry/2019/08/28/212834)  
  
・Windows 10 Home  
・Intel HD Graphics 520  

### 開発環境（必要最低限）

各OSでSiv3Dを動かすのに必要な最低動作環境です.

**Windows**  
・Windows 7 SP1 / 8.1 / 10 (64-bit)  
・Visual Studio 2019 version 16.1  

**macOS**  
・macOS High Sierra v10.13 若しくはそれ以降のOS  
・Xcode 10.1 若しくはそれ以降のバージョン

**Linux**  
[OpenSiv3D/README.md at linux · Siv3D/OpenSiv3D · GitHub](https://github.com/Siv3D/OpenSiv3D/blob/linux/Linux/README.md)を見てください。  
  
  
### Visual StudioにC++の開発環境を整える

OpenSiv3DはC++でコードを書くので、C++の開発環境をVisual Sudioにインストールする必要があります。

まず、**「Visual Studio Installer」**（下のアイコン）を開きます。  
![f:id:pythonjacascript:20190828210550j:plain](/images/ppythonjacascript2019082820190828210550.jpg "f:id:pythonjacascript:20190828210550j:plain")  
  
下のような画面が表示されたら、  
![f:id:pythonjacascript:20190828210907j:plain](/images/ppythonjacascript2019082820190828210907.jpg "f:id:pythonjacascript:20190828210907j:plain")  
「**C++によるデスクトップ開発**」のチェックをONにします。

そして右下の「インストール」ボタンを押すとインストールが始まり、終わると再起動をして完了です。  
  
  
### Siv3Dの導入

次に、OpenSiv3Dを導入します。ここからは、Windowsの開発環境の場合です。

**Visual Studio 2019はインストールされている前提で書いています。**

[Getting started with Siv3D - Siv3D](https://siv3d.github.io/#windows%5F1)  
まず、上のサイトから、「OpenSiv3D Installer for Windows Desktop」を選択し、Downloadします。  
![f:id:pythonjacascript:20190828204921j:plain](/images/ppythonjacascript2019082820190828204921.jpg "f:id:pythonjacascript:20190828204921j:plain")  
すると、このようなインストーラがダウンロードされるので、実行します。

  
![f:id:pythonjacascript:20190828204934j:plain](/images/ppythonjacascript2019082820190828204934.jpg "f:id:pythonjacascript:20190828204934j:plain")  
僕の環境の場合、このようなエラーが出ましたが、説明文の下の「詳細」をクリックすると表示される「実行」ボタンを押して実行しました。

  
![f:id:pythonjacascript:20190828204937j:plain](/images/ppythonjacascript2019082820190828204937.jpg "f:id:pythonjacascript:20190828204937j:plain")  
言語設定です。

  
![f:id:pythonjacascript:20190828204940j:plain](/images/ppythonjacascript2019082820190828204940.jpg "f:id:pythonjacascript:20190828204940j:plain")  
インストールするフォルダを選択します。デフォルトではDocumentsフォルダでしたが、↑の写真のようにProgram Filesに入れても動作しました。

  
![f:id:pythonjacascript:20190828204943j:plain](/images/ppythonjacascript2019082820190828204943.jpg "f:id:pythonjacascript:20190828204943j:plain")  
「インストール」ボタンを押します。

  
![f:id:pythonjacascript:20190828204946j:plain](/images/ppythonjacascript2019082820190828204946.jpg "f:id:pythonjacascript:20190828204946j:plain")  
インストール中....。ほんの１分程度で完了しました

  
![f:id:pythonjacascript:20190828204948j:plain](/images/ppythonjacascript2019082820190828204948.jpg "f:id:pythonjacascript:20190828204948j:plain")  
再起動する必要があります。  
  
  
### 開発環境を整える

以上でSiv3Dのインストールは終わりましたが、Siv3Dのプロジェクトが作成できない（テンプレートが認識されない）ことがあります。そこで、以下のようなコマンドを実行します。  
まず、CD命令でVisual Studioの中の「IDE」というファイルに移動します。  
そして、「**devenv /InstallVSTemplates**」を実行すればOKです。

C:\Users\Owner>cd C:\Program Files (x86)\Microsoft Visual Studio\2019\Community\Common7\IDE

C:\Program Files (x86)\Microsoft Visual Studio\2019\Community\Common7\IDE>devenv /InstallVSTemplates

C:\Program Files (x86)\Microsoft Visual Studio\2019\Community\Common7\IDE>

  
他にもいろいろな方法があるようですが、僕は↑のコマンドを打っただけで動きました。  
[インストール時のトラブルシューティング · Siv3D/Reference-JP Wiki · GitHub](https://github.com/Siv3D/Reference-JP/wiki/インストール時のトラブルシューティング)  
  
  
### サンプルプロジェクトを作成→実行

では、Visual Studioを開いてOpenSiv3Dnoプロジェクトを作成します。  
Visual Studioを開いて、「ファイル」→「新規プロジェクトの作成」をクリックします。

![f:id:pythonjacascript:20190828204952j:plain](/images/ppythonjacascript2019082820190828204952.jpg "f:id:pythonjacascript:20190828204952j:plain")  
うまくいけば、この画像のように「OpenSiv3」のプロジェクトを作成することができるはずです。  
「OpenSiv3」をクリックし、「次へ」を押します。

  
![f:id:pythonjacascript:20190828204956j:plain](/images/ppythonjacascript2019082820190828204956.jpg "f:id:pythonjacascript:20190828204956j:plain")  
3つ目の欄にプロジェクト名を入力します。それ以外は特にいじる必要はありません。

  
![f:id:pythonjacascript:20190828204959j:plain](/images/ppythonjacascript2019082820190828204959.jpg "f:id:pythonjacascript:20190828204959j:plain")  
プロジェクトが作成し終わったら、↑のような画面になります。  
もしソースコードが表示されていない場合は、画面右側の「**Solution Explorer**」の「**Source Files**」をクリックしてください。  
現在のソースコード一覧が表示されるはずです。

ディレクトリは、デフォルトでは「**プロジェクト名→OpenSiv3D(0.3.2)1→Main.cpp**」です。

  
プロジェクトをビルドするには、メニューバーの「ビルド」→「ソルーションのビルド」を実行します。  
すると、画面下の出力タブに

========== ビルド: 1 正常終了、0 失敗、0 更新不要、0 スキップ ==========

のように表示されていたらビルド成功です。

  
その場合、メニューバーの「デバッグ」→「デバッグなしで実行」をクリックして作成したサンプルアプリを起動します。

![f:id:pythonjacascript:20190828205003j:plain](/images/ppythonjacascript2019082820190828205003.jpg "f:id:pythonjacascript:20190828205003j:plain")

  
このようにアプリケーションが実行されます。  
  
  
### 参考文献

[インストールと開発環境 - Siv3D](https://scrapbox.io/Siv3D/インストールと開発環境)  
[OpenSiv3D の機能紹介\[1\] - Qiita](https://qiita.com/Reputeless/items/53c7edccbb5e447f7c26)