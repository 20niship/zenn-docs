---
title: "Windows10のキャッシュを徹底的に削除する"
emoji: "🤖"
type: "tech"
topics: undefined
published: false
---

![f:id:pythonjacascript:20210705034013j:plain](/images/ppythonjacascript2021070520210705034013.jpg)

* [Windows10全般の話](#Windows10全般の話)  
   * [Windowsの「設定」から削除](#Windowsの設定から削除)  
   * [ディスクのクリーンアップ](#ディスクのクリーンアップ)  
   * [FileSumを使う](#FileSumを使う)
* [各アプリのキャッシュを削除](#各アプリのキャッシュを削除)  
   * [FireFox](#FireFox)  
   * [AviUtl](#AviUtl)  
   * [Reaper](#Reaper)  
   * [Davinch Resolve](#Davinch-Resolve)  
   * [Solidworks](#Solidworks)  
   * [Adobe製品](#Adobe製品)  
   * [Slack](#Slack)

Windows10のストレージが一杯になって空き容量が15GBとかになったので、キャッシュや一時ファイルなどを徹底的に削除してやりました。  
これはその備忘録です。

### Windows10全般の話

Windows Explorerで以下のフォルダを削除

* C:\\WINDOWS\\Temp
* C:\\Users\\ユーザ名\\AppData\\Local\\Temp
* ごみ箱  
   * Explorerのディレクトリ入力場所に「ごみ箱」と入力すると、ゴミ箱が表示される
* C:/Windows.old  
   * これを消すとWindowsを前のバージョンに戻すことができなくなる
* C:\\Windows\\Prefetch  
   * Ctrl + Rを押して、prefetchと入力すると表示される。

C:\\Users\\ユーザ名\\AppData\\AppData/Localの中の見た感じ不要な削除する**（自己責任で！！）**  
今回削除したフォルダは以下：

* LINE/Cache (20MB)
* LINE/bin/sharedフォルダの中に数字が並んでいるので、その中で数字が一番大きいもの以外 (1GB)  
   * Qtのdllファイルが大量にあった  
   * フォルダ名はバージョン番号？
* LINE/bin/old
* Google/Chrome/User Data/Crashpad
* pip/cache (1GB)
* slack/フォルダの「app-\[バージョン番号\]」でバージョン番号が最新のやつ以外のもの（200MB）
* CrashDumps (ちょっと）

#### Windowsの「設定」から削除

Windowsの設定アプリを開く

1. 「システム」→「記憶域（ストレージ）」→「一時ファイル」を選択
2. 適切なチェックボックスにチェックを入れて「ファイルの削除」を実行

（Windows Update関連のファイル約4GBを削除できたので優秀）  
  
  
#### ディスクのクリーンアップ

上記のWindowsの「設定」アプリからの同様のことができますが、それだけだと数十MBファイルがまだ残っていたので、↓のやり方も効果はありそう

1. 「Windows + R」（ファイル名を指定して実行）から「cleanmgr.exe」を実行する（ディスクのクリーンアップ）
2. 「システムファイルのクリーンアップ」ボタンを押す
3. 必要ないファイルを選択して削除。

#### FileSumを使う

FileSumを使うと、フォルダの容量を一覧表示させることができる。  
[www.vector.co.jp](https://www.vector.co.jp/soft/winnt/util/se513257.html)  

![f:id:pythonjacascript:20210705034323p:plain](/images/ppythonjacascript2021070520210705034323.png)  
Linuxでいうncdu コマンドみたいなもの  
容量の大きいファイルから削除していくことができるので効率的。  
  
### 各アプリのキャッシュを削除

##### FireFox

* C:\\Users\\ユーザー名\\AppData\\Local\\Mozilla\\Firefox\\Profiles\\ を探して削除  
   * FireFoxでURLを入力するところに、「about:cache」と打つと、キャッシュファイルの場所を表示してくれる。
* FireFox上でCtrl + Bをして履歴を表示→削除

とか色々やっていたが geekでアプリ容量を見ると3GBくらいだったので、アンインストール＆再インストールした  
再インストールするとアプリ容量が430MBくらいになった。アンインストールしてもクッキーデータやブックマークは残っているらしく、再インストール後も復元されたので、定期的にやると良さそう  
  
##### AviUtl

* _**(hogehoge).lwi**_ファイルをExplorerのファイル検索削除

##### Reaper

* (hoge).reapeaksファイルを削除
* REAPER設定→一般→パスの「Alternative waveform peak cache path」（一番下）が設定されている場合は、そのディレクトリ内にreapeaksがまとまっているので、そこを削除

##### Davinch Resolve

* メニューバーの「再生」→「レンダーキャッシュの削除」→「全て」
* メニューバーの「ファイル」→「プロジェクトの設定」→「マスター設定」→「作業フォルダ」の中の「ギャラリースチルの場所」にキャッシュファイルがある
* が、そこまで容量が大きくなかったので、あまり意味はないかも。

##### Solidworks

* Solidworks Downloadフォルダを削除した（自己責任で！）
* 僕の環境の場合はC/Users/ユーザー名/Documents/SolidWorks Download
* 削除後もアセンブリを開いたり編集したりはできた。それ以外のことは知らん。

##### Adobe製品

* C:\\Users\\ユーザー名\\AppData\\Roaming\\Adobe\\Common

にキャッシュがあるので削除

##### Slack

左上の横棒三本→「ヘルプ」→「トラブルシューティング」→「キャッシュを削除して再起動」  
![f:id:pythonjacascript:20210705033851p:plain](/images/ppythonjacascript2021070520210705033851.png)

  
**30GBくらい空き容量増えたぜ！！やったぁ！**