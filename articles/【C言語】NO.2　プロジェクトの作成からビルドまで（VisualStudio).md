---
title: "【C言語】NO.2　プロジェクトの作成からビルドまで（VisualStudio)"
emoji: ""
type: ""
topics: []
published: false
---

C言語の基礎を連載していきます。この記事が第一回目です。

* [C言語とは？](#C言語とは)
* [VisualStudio とは](#VisualStudio-とは)
* [VisualStudioのインストール](#VisualStudioのインストール)
* [プロジェクトの作成](#プロジェクトの作成)
* [プログラムを書く](#プログラムを書く)
* [2.ビルド](#2ビルド)
* [3.プログラムの実行](#3プログラムの実行)

### C言語とは？

C言語とは、プログラミング言語のひとつです。ほかの言語には、RubyとかPythonとかいろいろありますが、

Cには、人が使う言葉に近い表現でプログラムを記述するプログラミング言語であるが、ハードウェアに密着した処理も容易に記述できる！

という特徴があります。なので、多くのソフトウェアやアプリがCで書かれています。

### VisualStudio とは

C言語でプロジェクトを作成・実行するには、専用のアプリケーションが必要です。この記事では、そのようなアプリケーションのうち、最も有名な VisualStudio を使ってC言語の開発環境を整えます。

VisualStudio2017（略称：VS）は、無料の開発環境で、Cのほかにも多くのプログラミング言語に対応しています。

MicrosoftのVisualStudioサイト：  
<https://visualstudio.microsoft.com/ja/?rr=https%3A%2F%2Fwww.google.com%2F>  

僕は現在、Cのソースファイルは、VisualStudio2017で書いています。

![f:id:pythonjacascript:20180701111939j:plain:h350](/images/ppythonjacascript2018070120180701111939.jpg "f:id:pythonjacascript:20180701111939j:plain:h350")

VisualStudio の画面

### VisualStudioのインストール

VisualStudioをPCにインストールしていない方は、インストールをしてください。

### プロジェクトの作成

VisualStudio2017を起動します。  
「ファイル」→「新規作成」→「プロジェクト」を選択  
「Windowsコンソールアプリケーション」を選択して、プロジェクト名を入力（下図）→「OK」を選択

![f:id:pythonjacascript:20180701113429j:plain](/images/ppythonjacascript2018070120180701113429.jpg "f:id:pythonjacascript:20180701113429j:plain")

プロジェクトの作成

1～3を行うと、下のような画面が出てきたと思います。これで、準備は完了です。

![f:id:pythonjacascript:20180701113814j:plain](/images/ppythonjacascript2018070120180701113814.jpg "f:id:pythonjacascript:20180701113814j:plain")

プロジェクト画面

### プログラムを書く

左上の一番大きい部分に、以下のプログラムを記入してください。

#include "stdafx.h"

int main()
{
    printf("Hello, World!");
    return 0;
}

**「ファイル」→「○○○（プロジェクト名）の保存」**をクリックして、変更内容を保存することを忘れずに。  
  
### 2.ビルド

では、いよいよ、初プログラム実行に移ります。  
そのためには、今書いた（コピペした）プログラムを実行可能なファイルに変換する必要があります。その作業を「ビルド」と言います。  
ビルドする方法は、  
**「ビルド（B)」→「ソルーションのビルド」をクリック**  
だけです！  
すると、VSの下の画面に、  
![f:id:pythonjacascript:20180701120310j:plain](/images/ppythonjacascript2018070120180701120310.jpg "f:id:pythonjacascript:20180701120310j:plain")  
（Fig.５ ビルド終了時のVSの画面）

Fig.5のような画面が出ているでしょうか？  
「========== ビルド: 1 正常終了、0 失敗、0 更新不要、0 スキップ ==========」と出ていればOKです！  
  
### 3.プログラムの実行

では、ビルドしたプログラムを実行します！  
**「デバック」→「デバックなしで開始」**をクリックしてください。

下のような画面が出れば成功です！  
![f:id:pythonjacascript:20180701120550j:plain](/images/ppythonjacascript2018070120180701120550.jpg "f:id:pythonjacascript:20180701120550j:plain")  
分かりにくいですが、左上に「Hello, World!」と表示されています。

お疲れさまでした！！次回からは、本格的にプログラムを作っていきます！