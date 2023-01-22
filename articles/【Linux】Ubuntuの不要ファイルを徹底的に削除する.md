---
title: "【Linux】Ubuntuの不要ファイルを徹底的に削除する"
emoji: "🤖"
type: "tech"
topics: []
published: false
---

Ubuntuで色々やっていると、だんだんSSDの容量を圧迫してきました。  
512GBしかないSSDをWindows用とよく使う動画データ用とUbuntu用で3つにパーティション分割している上に、WindowsパーティションにSolidworksやらFusionやらInventerやらUnityやら重たいソフトばっかり入っているので、どーしてもUbuntuに割くことのできる容量が少なくなってしまい、すぐ容量いっぱいになってしまうんです。

  
ちなみにですが、使用しているUbuntuはこちら：  
![f:id:pythonjacascript:20210409235433p:plain](/images/ppythonjacascript2021040920210409235433.png)

では本題。  
  
* [まずは使用容量と空き容量の確認](#まずは使用容量と空き容量の確認)
* [不要なアプリケーションを削除](#不要なアプリケーションを削除)
* [ndcuで巨大ファイルを削除](#ndcuで巨大ファイルを削除)
* [不要なDockerイメージを削除](#不要なDockerイメージを削除)
* [MySQLのデータベース削除](#MySQLのデータベース削除)
* [各種ログ削除](#各種ログ削除)  
   * [Slack](#Slack)  
   * [MySQL](#MySQL)
* [snap関連を削除](#snap関連を削除)
* [結果](#結果)

### まずは使用容量と空き容量の確認

$ sudo du / -h -s  # 現在使用している容量を取得
75G

$ df / -h 
Filesystem     Type  Size  Used Avail Use% Mounted on
/dev/sda5      ext4   82G   75G    7G  95% /

パーティションサイズが82GBでそのうち75GBも使ってました。ひえー  
  
  
### 不要なアプリケーションを削除

これは簡単。以下のコマンドを実行するだけ

$ sudo apt autoremove # 更新に伴い必要なくなったパッケージを削除
$ sudo apt clean    # キャッシュされている全てのdebファイルを削除
  
  
### ndcuで巨大ファイルを削除

sudo apt-get install ncdu -y 

でインストールします。

cd /; ncdu

を実行すると、以下のように表示されます。

![f:id:pythonjacascript:20210409235725p:plain](/images/ppythonjacascript2021040920210409235725.png)

\`du\`コマンドなどと違って、ファイルの大きさがグラフで表示されており、しかもサイズが大きい順にソートされているのでわかりやすいです。

* 上下矢印：フォルダ or ファイル選択
* 左矢印：上の階層に移動
* Enter：選択中のフォルダに入る
* d : フォルダ or ファイルの削除
* q : ncduを終了

のようなコマンドを駆使して、大容量の不要ファイルを見つけて削除していきます。  
?を入力するとヘルプ画面が表示されます  
  
### 不要なDockerイメージを削除

\`dockrer ps -a\` とか \`docker images\`とかを打ちながら、もう使っていないDockerコンテナ/イメージを削除します。

$ docker ps -a  #コンテナ一覧
$ docker images # イメージ一覧

$ docker rm [container] # コンテナ削除（その前にdocker stop [container]でコンテナ停止が必要）
$ docker rmi [image] # イメージ削除

上記のようなコマンドを一括で行うには

$ docker system prune -a

を実行します。これを実行すると以下の内容が削除されます：

> * all stopped containers
> * all networks not used by at least one container
> * all images without at least one container associated to them
> * all build cache

Android emulatorのDocker Imageが11GBくらいあったので、思い切って削除しました。  
  
### MySQLのデータベース削除

$ mysql --version
mysql  Ver 8.0.23-0ubuntu0.20.04.1 for Linux on x86_64 ((Ubuntu))


$ sudo mysql -u root -p;

mysql> show databases;
mysql> drop database [hoge];
......
mysql> quit

みたいな感じでMySQLの不要なテーブルやデータベースを削除していきます。

最終的に残ったデータベースはこれだけになりました。↓（おそらくデフォルト？）

+