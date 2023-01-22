---
title: "【C言語】NO.1 VisualStudio のインストール"
emoji: "🤖"
type: "tech"
topics: []
published: false
---

* [VisualStudio とは](#VisualStudio-とは)  
   * [Visual Studio Community](#Visual-Studio-Community)  
   * [Visual Studio Professional](#Visual-Studio-Professional)  
   * [Visual Studio Test Professional](#Visual-Studio-Test-Professional)  
   * [Visual Studio Code](#Visual-Studio-Code)  
   * [Visual Studio Enterprise](#Visual-Studio-Enterprise)
* [公式ホームページによる VisualStudio の説明](#公式ホームページによる-VisualStudio-の説明)
* [VisualStudioをインストール](#VisualStudioをインストール)  
   * [1.ダウンロード](#1ダウンロード)  
   * [2.パッケージの選択](#2パッケージの選択)

### VisualStudio とは

VisualStudio2017（略称：VS）は、**無料**の開発環境で、Cのほかにも多くのプログラミング言語に対応しています。

MicrosoftのVisualStudioサイト：

[Visual Studio IDE、コード エディター、Team Services、Mobile Center](https://visualstudio.microsoft.com/ja/)

 ですが、一言でVisualStudioと言っても、多くの種類があります。

* > Visual Studio Community
* > Visual Studio Professional
* > Visual Studio Test Professional
* > Visual Studio Enterprise
* > Visual Studio Code

などです。このうち、どれを使えばいいのかを見てみます。

#### Visual Studio Community

Community はいくつかの利用制限はありますが、**無料**で利用することのできます！「利用制限」と言っても、個人的なプロジェクトにおいては全く気になりません。なので、「ちょっとC言語をやってみたい、VisualStudio触ってみたい」という人には、**Community がベストな選択**だと思います

#### Visual Studio Professional

Visual Studio Professional はざっくりいうと統合開発環境 (IDE) です。単体テストとかコード分析とか、結構高度な支援機能が搭載され、あらゆるテスティングフレームワークを分け隔てなく実行/結果の把握ができます。有料で年間＄539です。

#### Visual Studio Test Professional

「統合テスト環境」です。

#### Visual Studio Code

通常のVisual Studioとは別の統合開発環境で、[Windows](https://ja.wikipedia.org/wiki/Microsoft%5FWindows "Microsoft Windows")、[macOS](https://ja.wikipedia.org/wiki/MacOS "MacOS")、[Linux](https://ja.wikipedia.org/wiki/Linux "Linux")に対応しています。コードエディターが主要機能だが、リファクタリングやデバッガも対応しています。**無償提供。**

#### Visual Studio Enterprise

Visual Studio Professional のさらに上のようです。有料で、年間＄2999です。

### 公式ホームページによる VisualStudio の説明

[visualstudio.microsoft.com](https://visualstudio.microsoft.com/ja/)

[visualstudio.microsoft.com](https://visualstudio.microsoft.com/ja/vs/compare/)

[visualstudio.microsoft.com](https://visualstudio.microsoft.com/ja/vs/pricing/)

### VisualStudioをインストール

PCにインストールしていない方は、インストールをしてください。

 ここでは、例としてVisualStudioCommunityを入れます。

#### 1.ダウンロード

[無料の IDE と開発者ツール | Visual Studio Community](https://visualstudio.microsoft.com/ja/vs/community/)

のサイトから、「Visual Studio Community 2017をダウンロード」ボタンを押します。

すると、「vs\_community ○○○.exe」というファイルがダウンロードされるので、実行します。

#### 2.パッケージの選択

その後、「はい」とか「同意する」とか「OK」とかを押していくと、「Visual Studio installer」という画面が出てきます。

![f:id:pythonjacascript:20180902092916p:plain](/images/ppythonjacascript2018090220180902092916.png "f:id:pythonjacascript:20180902092916p:plain")

ここで、「Visual Studio にインストールするパッケージを選択します。

僕は「Windows (3) 」の３つを選択しました。

![f:id:pythonjacascript:20180902093105p:plain](/images/ppythonjacascript2018090220180902093105.png "f:id:pythonjacascript:20180902093105p:plain")

必要なワークロード (複数可) を選択したら、**\[インストール\]** をクリックします。

これで終了です。