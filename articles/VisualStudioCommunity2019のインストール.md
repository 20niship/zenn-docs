---
title: "Visual Studio Community 2019 のインストール"
emoji: "🤖"
type: "tech"
topics: undefined
published: false
---

* [VisualStudio とは](#VisualStudio-とは)  
   * [Visual Studio Community](#Visual-Studio-Community)  
   * [Visual Studio Professional](#Visual-Studio-Professional)  
   * [Visual Studio Test Professional](#Visual-Studio-Test-Professional)  
   * [Visual Studio Code](#Visual-Studio-Code)  
   * [Visual Studio Enterprise](#Visual-Studio-Enterprise)
* [Visual Studioをインストール](#Visual-Studioをインストール)
* [2.パッケージの選択](#2パッケージの選択)

### VisualStudio とは

VisualStudio2017（略称：VS）は、最も使われている開発環境の一つで、C、Python、Java....など、多くのプログラミング言語に対応しています。

MicrosoftのVisualStudioサイト：  
[visualstudio.microsoft.com](https://visualstudio.microsoft.com/)

 ですが、一言でVisualStudioと言っても、多くの種類があります。  
Visual Studio Community  
Visual Studio Professional  
Visual Studio Test Professional  
Visual Studio Enterprise  
Visual Studio Code

#### Visual Studio Community

Community はいくつかの利用制限はありますが、無料で利用することのできます！「利用制限」と言っても、個人的なプロジェクトにおいては全く気になりません。なので、「ちょっとC言語をやってみたい、VisualStudio触ってみたい」という人には、Community がベストな選択だと思います  
  
#### Visual Studio Professional

Visual Studio Professional はざっくりいうと統合開発環境 (IDE) です。単体テストとかコード分析とか、結構高度な支援機能が搭載され、あらゆるテスティングフレームワークを分け隔てなく実行/結果の把握ができます。有料で年間＄539です。  
  
#### Visual Studio Test Professional

「統合テスト環境」です。  
  
#### Visual Studio Code

通常のVisual Studioとは別の統合開発環境で、Windows、macOS、Linuxに対応しています。コードエディターが主要機能だが、リファクタリングやデバッガも対応しています。無償提供。  
  
#### Visual Studio Enterprise

Visual Studio Professional のさらに上のようです。有料で、年間＄2999です。

### Visual Studioをインストール

PCにインストールしていない方は、インストールをしてください。

 ここでは、例としてVisual Studio 2019 Community を入れます。

[Visual Studio 2019 | Visual Studio](https://visualstudio.microsoft.com/ja/vs/)

↑のサイトから、「Visual Studio Community 2019をダウンロード」ボタンを押します。  
インストーラーのEXEファイルは[こちら](https://visualstudio.microsoft.com/thank-you-downloading-visual-studio/?sku=Community&rel=16)です。  
![f:id:pythonjacascript:20190828211711j:plain](/images/ppythonjacascript2019082820190828211711.jpg "f:id:pythonjacascript:20190828211711j:plain")

すると、「**vs\_community ○○○.exe**」というファイルがダウンロードされるので、実行します。  

### 2.パッケージの選択

![f:id:pythonjacascript:20190828211804j:plain](/images/ppythonjacascript2019082820190828211804.jpg "f:id:pythonjacascript:20190828211804j:plain")  
「続行」をクリック

  
![f:id:pythonjacascript:20190828211809j:plain](/images/ppythonjacascript2019082820190828211809.jpg "f:id:pythonjacascript:20190828211809j:plain")  
ほんの数分でVisual Studio Installerがインストールされました

  
![f:id:pythonjacascript:20190828211814j:plain](/images/ppythonjacascript2019082820190828211814.jpg "f:id:pythonjacascript:20190828211814j:plain")  
ここで、「Visual Studio にインストールするパッケージを選択します。僕は「C++によるデスクトップ開発 」を選択しました。  
この設定は、Visual Studio Installerを実行することで、いつでも変更することができます。

  
![f:id:pythonjacascript:20190828211818j:plain](/images/ppythonjacascript2019082820190828211818.jpg "f:id:pythonjacascript:20190828211818j:plain")  
右下の「インストール」ボタンを押すと、インストールが始まります。10Gほどあるので時間かかりました。

  
![f:id:pythonjacascript:20190828211821j:plain](/images/ppythonjacascript2019082820190828211821.jpg "f:id:pythonjacascript:20190828211821j:plain")  
サインインしてもよいですが、「後で」を選択してもVsisual Studioの機能を使うことができます。
  
  
![f:id:pythonjacascript:20190828211823j:plain](/images/ppythonjacascript2019082820190828211823.jpg "f:id:pythonjacascript:20190828211823j:plain")  
テーマ設定....

  
![f:id:pythonjacascript:20190828211827j:plain](/images/ppythonjacascript2019082820190828211827.jpg "f:id:pythonjacascript:20190828211827j:plain")  
VisualStudio2019から、↑のような選択画面が表示されるようです。従来の画面（下の写真）に移行するには、一番下の「**コードなしで実行**」をクリックします。

  
![f:id:pythonjacascript:20190828211830j:plain](/images/ppythonjacascript2019082820190828211830.jpg "f:id:pythonjacascript:20190828211830j:plain")  
これがデフォルトの画面です。