---
title: "【Windows】PCのデータを丸ごとバックアップする"
emoji: ""
type: ""
topics: undefined
published: false
---

今回は、OSなどのシステムファイルごと、Windowsパソコンをバックアップしてくれる無料ソフトウェア見つけたので、それの試用レビューです。  
いまから紹介する手順で行うと、外付けHDDを**フォーマットすることなく(以前外付けHDDに保存したデータが消去されることなく）バックアップを取ることが可能**です。(万が一データが消えても自己責任でお願いします）  
  
### ソフトウェア

この記事でバックアップに使用したソフトウェアはこちらです。

![f:id:pythonjacascript:20180909132932j:plain](/images/ppythonjacascript2018090920180909132932.jpg "f:id:pythonjacascript:20180909132932j:plain")

「**AOMEI Backupper Standard**」という名前です。

**ダウンロード**：  
公式サイト：[無料のWindowsバックアップソフトのダウンロード](https://www.backup-utility.com/jp/download.html)  
Vector：[AOMEI Backupper Standardの詳細情報 : Vector ソフトを探す！](https://www.vector.co.jp/soft/winnt/util/se502086.html)  
窓の社：[「AOMEI Backupper」ドライブやパーティションを丸ごとバックアップ - 窓の杜](https://forest.watch.impress.co.jp/library/software/aomeibackup/)

### 使い方

#### 1.起動

上のリンクから **AOMEI Backupper Standard** をダウンロードして、「BackupperFull.exe」というファイルを開きます。  
![f:id:pythonjacascript:20180909133205p:plain](/images/ppythonjacascript2018090920180909133205.png "f:id:pythonjacascript:20180909133205p:plain")

#### インストール

インストールを行います。  
使用する言語は日本語にしました。  
![f:id:pythonjacascript:20180909133306j:plain:h200](/images/ppythonjacascript2018090920180909133306.jpg "f:id:pythonjacascript:20180909133306j:plain:h200")

プログラムをインストールするディレクトリを指定します。僕は、以下のように設定しています。  
![f:id:pythonjacascript:20180909133401j:plain:h350](/images/ppythonjacascript2018090920180909133401.jpg "f:id:pythonjacascript:20180909133401j:plain:h350")

「読み、同意する」にチェックを入れて、インストールを行います。  
![f:id:pythonjacascript:20180909133428j:plain:h350](/images/ppythonjacascript2018090920180909133428.jpg "f:id:pythonjacascript:20180909133428j:plain:h350")
  
  
#### バックアップを取る

「**AOMEI Backupper Standard**」を起動します。

「バックアップ」→「システムバックアップ」を選択します。  
![f:id:pythonjacascript:20180909133615j:plain](/images/ppythonjacascript2018090920180909133615.jpg "f:id:pythonjacascript:20180909133615j:plain")

バックアップしたデータを保存する場所を指定します。僕の場合、外付けHDDを繋げていたので、それにデータを保存しました。その場合、「2」を選択します。  
![f:id:pythonjacascript:20180909134154j:plain](/images/ppythonjacascript2018090920180909134154.jpg "f:id:pythonjacascript:20180909134154j:plain")

外付けHDDにデータを保存するので、「1」を選択します。（PCによって番号は異なる可能性があります）  
![f:id:pythonjacascript:20180910151937j:plain](/images/ppythonjacascript2018091020180910151937.jpg "f:id:pythonjacascript:20180910151937j:plain")

「開始」をクリックします。  
![f:id:pythonjacascript:20180910152432j:plain](/images/ppythonjacascript2018091020180910152432.jpg "f:id:pythonjacascript:20180910152432j:plain")

すると、バックアップが開始されます。  
![f:id:pythonjacascript:20180910152436j:plain](/images/ppythonjacascript2018091020180910152436.jpg "f:id:pythonjacascript:20180910152436j:plain")

  
バックアップが完了すると、外付けHDDに「ディスクバックアップ（1）」というフォルダが作成され、その中にデータが入っています。  
データの復元も「**AOMEI Backupper Standard**」を使用して行います。