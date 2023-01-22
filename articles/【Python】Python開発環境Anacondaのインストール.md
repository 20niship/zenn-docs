---
title: "【Python】Python開発環境Anacondaのインストール"
emoji: ""
type: ""
topics: []
published: false
---

Pythonの開発環境である「Anaconda」のダウロード＆インストール方法を説明します。  
  
* [1.Anacondaをダウンロード](#1Anacondaをダウンロード)
* [2.インストール](#2インストール)
* [3.環境変数の設定](#3環境変数の設定)  
   * [STEP1 コントロールパネルを開く](#STEP1-コントロールパネルを開く)  
   * [STEP2 環境変数を設定するウィンドウを開く](#STEP2-環境変数を設定するウィンドウを開く)  
   * [STEP3\. 環境変数の設定](#STEP3-環境変数の設定)
* [4.コマンドプロンプトでのテスト](#4コマンドプロンプトでのテスト)  
   * [STEP1\. コマンドプロンプトの実行](#STEP1-コマンドプロンプトの実行)  
   * [STEP2\. Pythonの実行](#STEP2-Pythonの実行)
* [5.Spyderでのテスト](#5Spyderでのテスト)

### 1.Anacondaをダウンロード

以下のサイトにアクセスしてください。  
[Downloads - Anaconda](https://www.anaconda.com/download/#windows)

インストールしようとしているPCのOSを選択します。  
また、Windowsの場合は、下の画像のように32bitか64bitを選んでダウンロードします。  
![f:id:pythonjacascript:20181029202746j:plain](/images/ppythonjacascript2018102920181029202746.jpg "f:id:pythonjacascript:20181029202746j:plain")

  
### 2.インストール

ダウンロードが完了すると、「Anaconda3-5.3.0-Windows-x86\_64.exe」という名前のファイルができているはずなので、実行します。

![f:id:pythonjacascript:20181029204929j:plain:h200](/images/ppythonjacascript2018102920181029204929.jpg "f:id:pythonjacascript:20181029204929j:plain:h200")  
「**Next**」をクリックします。

  
![f:id:pythonjacascript:20181029204958j:plain:h200](/images/ppythonjacascript2018102920181029204958.jpg "f:id:pythonjacascript:20181029204958j:plain:h200")  
「**I agree**」をクリック。

  
![f:id:pythonjacascript:20181029205058j:plain:h200](/images/ppythonjacascript2018102920181029205058.jpg "f:id:pythonjacascript:20181029205058j:plain:h200")  
どのユーザーにAnacondaをインストールするか選択します。「**All Users**」を選択しておけばすべてのユーザーで使えます。

  
![f:id:pythonjacascript:20181029205247j:plain](/images/ppythonjacascript2018102920181029205247.jpg "f:id:pythonjacascript:20181029205247j:plain")  
インストールするフォルダを指定します。どこでもいいのですが、あとで環境変数の設定をするので、そのときのパスの値がここで選択したフォルダになります。僕の場合は「**ProgramData**」にインストールしています（管理者権限が必要）。

  
![f:id:pythonjacascript:20181029205522j:plain](/images/ppythonjacascript2018102920181029205522.jpg "f:id:pythonjacascript:20181029205522j:plain")  
下のみにチェックを入れてインストール。

  
![f:id:pythonjacascript:20181029210530j:plain](/images/ppythonjacascript2018102920181029210530.jpg "f:id:pythonjacascript:20181029210530j:plain")  
インストールが完了しました。「Next」をクリックして終了します。

  
![f:id:pythonjacascript:20181029210954j:plain](/images/ppythonjacascript2018102920181029210954.jpg "f:id:pythonjacascript:20181029210954j:plain")  
**Visual Studio Code** のインストールを勧められましたが、僕は無視しています。  
Visual Studio Code は無料なので、以下のサイトからいつでもインストールすることができるからです。  
[code.visualstudio.com](https://code.visualstudio.com/)

Visual Studioのインストール方法については、以下のサイトをご覧ください。  
[shizenkarasuzon.hatenablog.com](https://shizenkarasuzon.hatenablog.com/entry/2018/07/01/112646#Visual-Studio-Code)
  
  
### 3.環境変数の設定

ここでは、Anacondaの**環境変数**の設定を行います。  
環境変数とは：

> 環境変数（environment variable）はオペレーティングシステム (OS) が提供するデータ共有機能の一つ。OS上で動作するタスク（プロセス）がデータを共有するための仕組みである。特にタスクに対して外部からデータを与え、タスクの挙動・設定を変更するために用いる。

（Wikipedia）

ここでの意味は、**Anacondaのアプリケーション（pipなど）のパスを登録しておくことで、そのプログラムをOSに認識させる**ことです。そうすることでコマンドプロンプトなどからそれらを実行できるようになります。  
  
#### STEP1 コントロールパネルを開く

**「コントロールパネル」**を開き、「**システムとセキュリティ**」→「**システム**」を開き、「**システムの詳細設定**」をクリックします。  
  
#### STEP2 環境変数を設定するウィンドウを開く

STEP1を行うと、「システムのプロパティ」というウィンドウの「詳細設定」タブが開いていると思います。  
ここで、「環境変数（N)...」のボタンをクリックすると、次のようなウィンドウが開きます。  
![f:id:pythonjacascript:20181029213618j:plain](/images/ppythonjacascript2018102920181029213618.jpg "f:id:pythonjacascript:20181029213618j:plain")

さらに、「**システム環境変数（S）...**」の中の「**Path**（上記写真で選択している）」をダブルクリックすると、新しくこのようなウィンドウが開きます。

![f:id:pythonjacascript:20181029213844j:plain](/images/ppythonjacascript2018102920181029213844.jpg "f:id:pythonjacascript:20181029213844j:plain")

#### STEP3\. 環境変数の設定

上の写真のPath一覧に、以下のようなパスを新規作成してください。

・(Anacondaのディレクトリ)
・(Anacondaのディレクトリ)\Scripts
・(Anacondaのディレクトリ)\Lib\site-packages

ちなみに筆者の場合 Anaconda を 「**C:\\ProgramData\\Anaconda3**」にインストールしたので、このようになります。

・C:\ProgramData\Anaconda3
・C:\ProgramData\Anaconda3\Scripts
・C:\ProgramData\Anaconda3\Lib\site-packages

環境変数を追加すると、このようになります。赤い四角のなかが、今回追加したパスです。  
![f:id:pythonjacascript:20181029215052p:plain](/images/ppythonjacascript2018102920181029215052.png "f:id:pythonjacascript:20181029215052p:plain")
  
  
### 4.コマンドプロンプトでのテスト

コマンドプロンプトを使ってPythonが入っていることを確認してみます。

#### STEP1\. コマンドプロンプトの実行

コマンドプロンプトを開きます。  
![f:id:pythonjacascript:20181029215647p:plain](/images/ppythonjacascript2018102920181029215647.png "f:id:pythonjacascript:20181029215647p:plain")

#### STEP2\. Pythonの実行

「**python**」と入力してください。  
このように表示されたでしょうか。  
![f:id:pythonjacascript:20181029215857p:plain](/images/ppythonjacascript2018102920181029215857.png "f:id:pythonjacascript:20181029215857p:plain")  
上の写真と同じように表示されていれば、正しくインストールできています。Pythonは対話型言語なので、「**＞＞＞**」のあとに

print(2+3)

などと打ってエンターキーを押すと、

5

と表示されるはずです。

もしこれ以外の表示が出ていたら、環境変数の設定が間違っているか、インストールが失敗しているかのどちらかです。  
  
  
### 5.Spyderでのテスト

Anacondaをインストールすると、新しくSpyderというアプリケーションが入っているはずです。  
![f:id:pythonjacascript:20181029212811j:plain](/images/ppythonjacascript2018102920181029212811.jpg "f:id:pythonjacascript:20181029212811j:plain")  
こんなアイコンです。

Spyderを起動すると、このような画面になります。  
![f:id:pythonjacascript:20181029211734p:plain](/images/ppythonjacascript2018102920181029211734.png "f:id:pythonjacascript:20181029211734p:plain")

初期状態では**左側**のウィンドウが**コードエディター**で、**右下**のウィンドウに**プログラムの実行結果**を出力してくれます。

試しに、左側に以下のプログラムを書いてください。

print("Hello, World!")

そして、「**ファイルを実行**」ボタン（右上のこれ→![f:id:pythonjacascript:20181029212108p:plain](/images/ppythonjacascript2018102920181029212108.png "f:id:pythonjacascript:20181029212108p:plain")）をクリックするか、**F5**を押すことで先ほど書いたプログラムが実行されます。

![f:id:pythonjacascript:20181029212313p:plain](/images/ppythonjacascript2018102920181029212313.png "f:id:pythonjacascript:20181029212313p:plain")  
このように、右下の「**IPythonコンソール**」というウィンドウに「**Hello, World!**」と出力されたでしょうか？  
こうなっていればOKです。