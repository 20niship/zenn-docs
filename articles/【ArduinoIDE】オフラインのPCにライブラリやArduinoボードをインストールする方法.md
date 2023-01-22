---
title: "【Arduino IDE】オフラインのPCにライブラリやArduinoボードをインストールする方法"
emoji: ""
type: ""
topics: undefined
published: false
---

* [インターネットにつながっている場合](#インターネットにつながっている場合)  
   * [ライブラリの追加/削除方法](#ライブラリの追加削除方法)  
   * [新しくボードを追加/削除する方法](#新しくボードを追加削除する方法)
* [インターネットにつながっていない場合](#インターネットにつながっていない場合)  
   * [ライブラリの追加/削除](#ライブラリの追加削除)  
         * [STEP 1 ライブラリをGitHubで探す](#STEP-1-ライブラリをGitHubで探す)  
         * [STEP 2 ライブラリをZIPでダウンロード](#STEP-2-ライブラリをZIPでダウンロード)  
         * [STEP 3 ZIPファイルをコピー](#STEP-3-ZIPファイルをコピー)  
         * [STEP 4 ライブラリをインクルード](#STEP-4-ライブラリをインクルード)  
         * [STEP 5 完了](#STEP-5-完了)  
   * [Arduino Boardの追加/削除](#Arduino-Boardの追加削除)  
         * [STEP1 ボードをダウンロード](#STEP1-ボードをダウンロード)  
         * [STEP2 データをコピー](#STEP2-データをコピー)  
         * [STEP3 データを張り付け](#STEP3-データを張り付け)  
         * [STEP4 Arduino IDE に認識させる](#STEP4-Arduino-IDE-に認識させる)  
         * [STEP5 完了！](#STEP5-完了)
* [最後に...（愚痴）](#最後に愚痴)

  
arduinoのプログラミングを行うための総合開発環境の一つに「arduino IDE」があります。

この記事では、arduino IDEを使ってオフラインでプログラミングを行なうときに、様々なライブラリーをインストールする方法や、標準搭載以外のarduino board（Arduino DUE等）にプログラミングをする方法を紹介します。  
  
  
### インターネットにつながっている場合

#### ライブラリの追加/削除方法

**「スケッチ」→「ライブラリをインクルード」→「ライブラリを管理**」で、ライブラリの追加、削除を行うことができます。  
![f:id:pythonjacascript:20180905042252j:plain](/images/ppythonjacascript2018090520180905042252.jpg "f:id:pythonjacascript:20180905042252j:plain")  
インストールしたいライブラリと、そのバージョンを選択して「インストールボタンを押すだけです。  
  
#### 新しくボードを追加/削除する方法

「ツール」→「ボード：○○〇」→「ボードマネージャ」で IDE で書き込むことのできる Arduino ボードの追加、削除を行うことができます。  
![f:id:pythonjacascript:20180905042611j:plain](/images/ppythonjacascript2018090520180905042611.jpg "f:id:pythonjacascript:20180905042611j:plain")
  
  
インターネットにつながっている場合は、ざっとこんな感じです。

**だがしかし！！**

  
もし、Arduino IDE を動かしているPCがインターネットにつながらないものだったとしたら...？  
上記の方法は、オフラインでは通用しません。

そこで、色々試した結果、これが最善です。

> ※今から紹介する方法は、**インターネットにつながっているPCが一台必要**です。そのPCを使ってネットからデータを拾ってきて、そのデータをオフラインのPCにコピーし、Arduino IDE に認識させる、という方法になります。

### インターネットにつながっていない場合

#### ライブラリの追加/削除

「ライブラリの管理」が使えないので、以下の手順でライブラリをインストールします。

##### STEP 1 ライブラリをGitHubで探す

GitHubに大抵のArduino用ライブラリは上がっています。なので、GitHubで自分がほしいライブラリを探してみてください。  
[github.com](https://github.com/)  

##### STEP 2 ライブラリをZIPでダウンロード

STEP1 で選択したライブラリをZIPファイルとしてダウンロードします。  
方法は、右上の緑の「Clone or Download」と書かれたボタンをクリック → 「Download ZIP」を選択  
![f:id:pythonjacascript:20180905043718j:plain](/images/ppythonjacascript2018090520180905043718.jpg "f:id:pythonjacascript:20180905043718j:plain")

ダウンロードした場所を覚えておいてください。  
  
##### STEP 3 ZIPファイルをコピー

ZIPファイルのまま、Arduino IDEのあるPCにライブラリのデータをコピーします。  
コピーした後の場所はどこでもOKです。  
  
##### STEP 4 ライブラリをインクルード

Arduino IDEを起動して、「スケッチ」→「ライブラリをインクルード」→「zip形式のライブラリをインクルード」を選択します。  
![f:id:pythonjacascript:20180905044117j:plain](/images/ppythonjacascript2018090520180905044117.jpg "f:id:pythonjacascript:20180905044117j:plain")

あとは、STEP4でコピーしたZIPファイルを選択するだけです。  
  
##### STEP 5 完了

「ライブラリがインストールされました」とArduino IDE のホーム画面の緑の帯の部分に表示されれば完了です。  
  
  
#### Arduino Boardの追加/削除

##### STEP1 ボードをダウンロード

インターネットにつながっているPCでArduino IDEを起動します。そして、ボードマネージャーから、上記の方法で Arduino Boardを追加します。  
![f:id:pythonjacascript:20180905042611j:plain](/images/ppythonjacascript2018090520180905042611.jpg "f:id:pythonjacascript:20180905042611j:plain")

  
##### STEP2 データをコピー

そうすると、「**C:\\Users\\$USERNAME\\AppData\\Local\\Arduino15**」のフォルダに、先ほどインストールしたボードのコンパイル情報（？）が入っていると思います。  
![f:id:pythonjacascript:20180905045601j:plain](/images/ppythonjacascript2018090520180905045601.jpg "f:id:pythonjacascript:20180905045601j:plain")

「packages」と「staging」という２つのフォルダがあるようですが、どちらも更新されているので、それら両方をそのままコピーします。  
  
##### STEP3 データを張り付け

STEP2でコピーしたデータをインターネットにつながっていないPCに張り付けます。場所は「**C:\\Users\\$USERNAME\\AppData\\Local\\Arduino15**」です。  
  
##### STEP4 Arduino IDE に認識させる

先ほど張り付けたフォルダの**「\\packages\\arduino\\hardware\\＄パッケージ名\\$バージョン\\post\_install.bat」**を実行します。  
![f:id:pythonjacascript:20180905045601j:plain](/images/ppythonjacascript2018090520180905045601.jpg "f:id:pythonjacascript:20180905045601j:plain")

##### STEP5 完了！

インストールされたことの確認のために、「ツール」→「ボード：○○〇」を選択します。

![f:id:pythonjacascript:20180905051011j:plain](/images/ppythonjacascript2018090520180905051011.jpg "f:id:pythonjacascript:20180905051011j:plain")  
このように表示されていれば、新しいArduinoBoardがインストールされています。  
(上の写真は、試しにこの方法で「Arduino DUE」をインストールしてみたものです。）  
  
  
### 最後に...（愚痴）

なんでオフラインのインストールにこだわるのかって？  
**部活で使っているPCが全てオフラインなんだよぉ～**