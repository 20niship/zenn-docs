---
title: "Visual Studio で Verlilog HDL がしたい！"
emoji: "🤖"
type: "tech"
topics: []
published: false
---

最近、**Verlilog HDL**を使用するプロジェクトに参加しています。

そこで、「Verlilog HDLを**Visual Studio**で使えないかな...？」と思っていたら、案の定プラグインがあり、導入したので、そのときの様子を紹介します。

* [0.動作環境について](#0動作環境について)
* [1.プラグインのインストール](#1プラグインのインストール)
* [2.使ってみた](#2使ってみた)
* [フォントの色設定](#フォントの色設定)

### 0.動作環境について

使用しているのは、**Visual Studio Community 2017**です。Windows10 64ビットのものです。  
Visual Studio Codeなどのほかの種類では試していません。

また、プラグインをインストールするのでネット環境が必要です。  
  
### 1.プラグインのインストール

Visual Studioを開いて、「**ツール**」→「**拡張機能と更新プログラム**」を開きます。  
![f:id:pythonjacascript:20181223200227j:plain](/images/ppythonjacascript2018122320181223200227.jpg "f:id:pythonjacascript:20181223200227j:plain")

  
すると、次のような画面が出てくるので、「**オンライン**（左側）」をクリックし、右上の検索ボックスで「**HDL**」と検索します。  
![f:id:pythonjacascript:20181223200605j:plain](/images/ppythonjacascript2018122320181223200605.jpg "f:id:pythonjacascript:20181223200605j:plain")

すると、「**V3S VHDL, Verilog, System Verilog 2017**」というプラグインがあるので、「インストール」を押してインストールします。  
![f:id:pythonjacascript:20181223200627j:plain](/images/ppythonjacascript2018122320181223200627.jpg "f:id:pythonjacascript:20181223200627j:plain")

そして、Visual StudioのAppをすべて終了するとインストールが自動的に始まります。  
  
### 2.使ってみた

では、先ほど入れたV3Sプラグインを用いて、Verilog HDLの編集を行ってみます。

「**ファイル**」→「**新規プロジェクトを作成**」  
![f:id:pythonjacascript:20181223202422j:plain](/images/ppythonjacascript2018122320181223202422.jpg "f:id:pythonjacascript:20181223202422j:plain")

このように、V3Sプロジェクトを作成することができればインストールは正常に完了しています。

![f:id:pythonjacascript:20181223202613j:plain](/images/ppythonjacascript2018122320181223202613.jpg "f:id:pythonjacascript:20181223202613j:plain")  
選択肢を見ればわかりますが、AlteraのFPGAボードの開発もできるようです。  
この記事では、「sample project」を作成してみました。

そして、...  
プロジェクトの作成が終わり、いざコードの編集をしてみよう！と思ったのですが...

![f:id:pythonjacascript:20181223202809j:plain](/images/ppythonjacascript2018122320181223202809.jpg "f:id:pythonjacascript:20181223202809j:plain")  
**文字色がおかしい！！**

黒背景なのに、文字色が黒で見えるはずがないですよ....

ということで、

### フォントの色設定

Verilog HDLのコード表示の文字色を変更します。  
「**ツール**」→「**オプション**」を選択。  
![f:id:pythonjacascript:20181223202948j:plain](/images/ppythonjacascript2018122320181223202948.jpg "f:id:pythonjacascript:20181223202948j:plain")

オプション画面が新しく現れたら、「**フォントおよび色**」を選択します。すると、このようなフォント編集画面になります。  
![f:id:pythonjacascript:20181223203043j:plain](/images/ppythonjacascript2018122320181223203043.jpg "f:id:pythonjacascript:20181223203043j:plain")

ここで、「**表示項目**」が「**V3S-○○○○**」となっている部分を探し、そこの文字色を変更していきます。

そうして、ほぼすべてのフォントを変更した結果：  
![f:id:pythonjacascript:20181223203336j:plain](/images/ppythonjacascript2018122320181223203336.jpg "f:id:pythonjacascript:20181223203336j:plain")  
少しどぎついですが、だいぶ見やすくなりました。  
ここら辺は各自のお好み設定で。

  
それではまた。