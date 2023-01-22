---
title: "【Processing】インストールから最初のプログラムまで"
emoji: ""
type: ""
topics: []
published: false
---

* [1.Processingとは](#1Processingとは)  
   * [（1）視覚的なデザインに向いている](#1視覚的なデザインに向いている)  
   * [（2）Processing のプログラミング言語は、僕の印象では Java言語の簡易版のような感じです。](#2Processing-のプログラミング言語は僕の印象では-Java言語の簡易版のような感じです)
* [2.ダウンロード・インストール](#2ダウンロードインストール)
* [3.サンプルプログラムの実行](#3サンプルプログラムの実行)

### 1.Processingとは

#### （1）視覚的なデザインに向いている

Processingとは、オープンソースプロジェクトで、**電子アートとビジュアルデザインのためのプログラミング言語**です。  
Processingを使うと、他の言語より簡単にグラフィック系のアプリケーションやゲームを作ることができます。

例えば、僕はProcessingを使ってドローンの操作用のGUI（Graphical User Interface）を作っていました。  
こんな感じです。

![f:id:pythonjacascript:20180813181845p:plain](/images/ppythonjacascript2018081320180813181845.png "f:id:pythonjacascript:20180813181845p:plain")

Processingを使ったアプリケーションの例

#### （2）Processing のプログラミング言語は、僕の印象では **Java言語**の簡易版のような感じです。

![f:id:pythonjacascript:20180814125706p:plain](/images/ppythonjacascript2018081420180814125706.png "f:id:pythonjacascript:20180814125706p:plain")  
Processing にはsketchbook（スケッチブック）と呼ばれる必要最小限のIDEが含まれていて、上の写真のようなIDEを使ってプログラムを書いていきます。  
  
  
### 2.ダウンロード・インストール

このサイトからダウンロードします。  
[Download \\ Processing.org](https://processing.org/download/)  
MAC用とWindows用がありますが、自分のPCの仕様に合ったものをダウンロードしてください。

また、Windows7でProcessingを動かす場合、「Processing 3.4」は一部の機能が動作しなかったので、上記のサイトを下にスクロールしたところにある、「Processing 2.2.1」を使ってください。

ダウンロード・インストールが完了すると、そのフォルダの中に、「Processing.exe」というファイルがあるので、それを実行してください。  
Processingが起動するはずです。  
![f:id:pythonjacascript:20180814130530p:plain](/images/ppythonjacascript2018081420180814130530.png "f:id:pythonjacascript:20180814130530p:plain")  
↑：Processingの起動画面  
  
### 3.サンプルプログラムの実行

Processingを使ってプログラムを実行していきます。  
Processingを起動し、以下のコードを書いてください。

void setup(){
    size(300, 300) ;
}

void draw(){
    background(0) ;
    text("Hello, world", 15, 50) ;
}

このようになります。  
![f:id:pythonjacascript:20180814133241p:plain](/images/ppythonjacascript2018081420180814133241.png "f:id:pythonjacascript:20180814133241p:plain")

そして、プレビューボタン（![f:id:pythonjacascript:20180814133334p:plain](/images/ppythonjacascript2018081420180814133334.png "f:id:pythonjacascript:20180814133334p:plain")）をクリックしてください。  
そうすると、先ほど書いたプログラムが実行されます。

![f:id:pythonjacascript:20180814133420p:plain](/images/ppythonjacascript2018081420180814133420.png "f:id:pythonjacascript:20180814133420p:plain")  
このような画面が表示されればOKです。