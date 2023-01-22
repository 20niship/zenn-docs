---
title: "【Raspberry Pi 3B】で自宅サーバー構築  Debianのインストール"
emoji: "🤖"
type: "tech"
topics: []
published: false
---

めちゃくちゃ前（2019年）の話ですが、Raspberry Pi Model 3Bを買っていました：  
![f:id:pythonjacascript:20210426103007j:plain](/images/ppythonjacascript2021042620210426103007.jpg)

今回から数回の記事にわたって、**Raspberry Pi Model 3Bを使ってサーバー環境を構築**しようと思っています。

最終形態としては以下の様なものを想定しています

* OSはDebian 10 buster ( [Debian 10 (buster) リリースノート (32 ビット PC 用)](https://www.debian.org/releases/stable/i386/release-notes/index.ja.html) )
* Docker-composeを使って複数のWebアプリ/サイトを一台のラズパイで制御する
* SSHで中身をいじることができる（認証は公開鍵を用いる）

この記事では、Raspberry PiにDebianをインストールして、ローカルの別PCからSSHして中身をいじるところまで行きます。

* [OS（Debian）を入れる](#OSDebianを入れる)  
   * [ダウンロード](#ダウンロード)  
   * [OSをmicro SDに書き込む](#OSをmicro-SDに書き込む)
* [OS動作確認](#OS動作確認)  
   * [ターミナルの文字がおかしい](#ターミナルの文字がおかしい)
* [色々なソフトをインストール](#色々なソフトをインストール)  
   * [Dockerのインストール](#Dockerのインストール)  
   * [Docker-composeのインストール](#Docker-composeのインストール)
* [タイムゾーン設定](#タイムゾーン設定)  
   * [日本語ツールのインストール](#日本語ツールのインストール)  
   * [zshの設定](#zshの設定)  
   * [Powerlevel10kの設定](#Powerlevel10kの設定)
* [ユーザーの追加](#ユーザーの追加)
* [SSHの設定](#SSHの設定)  
   * [公開鍵認証にする](#公開鍵認証にする)  
   * [パスワード認証の無効化](#パスワード認証の無効化)

### OS（Debian）を入れる

#### ダウンロード

下記リンクからOSのイメージデータをダウンロードします。  
[Tested images](https://raspi.debian.net/tested-images/)  
  
この記事では、上記リンク先のリストでModel 3Bの動作確認が済んでいる最新版の「\[[xz-compressed image 376.97 MB, 2021-02-10 14:16:-0600](https://raspi.debian.net/verified/20210210%5Fraspi%5F3%5Fbuster.img.xz)」」をインストールしました。リンクをクリックすると自動的にダウンロードが始まります。

むかし（2018年くらい？）は[ここ](https://wiki.debian.org/RaspberryPi3)にIMGデータのリンクが書いてあったようですが、今は見当たりません。  
  
#### OSをmicro SDに書き込む

RaspberryPiに使用するストレージ (micro SD) に、↑でダウンロードしたOSデータを書き込みます。それには、公式でリリースされている**Raspberry Pi Imager**を用います。

↓のリンクからRaspberry pi imagerをインストールします。  
<https://www.raspberrypi.org/software/>

起動すると、以下のような画面になるので、「Choose OS」を選択→「Use Custom」から、先ほどダウンロードしたOSファイル（hoge.img.xy)を選択します。  
![f:id:pythonjacascript:20210426111804j:plain](/images/ppythonjacascript2021042620210426111804.jpg)  
  
![f:id:pythonjacascript:20210426111843j:plain](/images/ppythonjacascript2021042620210426111843.jpg)  
  
そして、「Choose Storage」ボタンから書き込むmicro SDカードを選択し、「Write」ボタンを押します  
![f:id:pythonjacascript:20210426112038j:plain](/images/ppythonjacascript2021042620210426112038.jpg)  
このような画面が出るので「YES」を押して書き込みを開始します。およそ5分くらいで終わりました。  
  
### OS動作確認

では、OSを入れたmicro SDカードをラズパイに差し込んで、電源を入れます。すると自動的に電源が入り、以下の様に「login : 」と表示されるので、「root」と入力しEnterを押して、root ユーザーでログインします（初期状態ではパスワードは必要ありませんでした）。

![f:id:pythonjacascript:20210426112235j:plain](/images/ppythonjacascript2021042620210426112235.jpg)  
  
上の画像はログインしてDebianが起動した後の画面です。以下の様なコマンドを入力して、ハードウェアを確認します：

# cat /proc/cpu/info
processor : 0
BogoMIPS : 38.40
features : fp asImd evtstrm crc32 cpuid
.......(以下略。4コアCPUであることがわかります)

# cat /proc/meminfo
MemTotal : 939552kB
.......(以下略。1GBですね)

# ip address show
......ローカルIPアドレスなどが色々表示されます。
# ip a
......ローカルIPアドレスなどが色々表示されます。

# cat /etc/debian_info
10.8 # OSのバージョンを知ることができる。

##### ターミナルの文字がおかしい

キーボードの種類？によって？、一部の記号キーが正しく入力されないようです。物理キーボードのキー上に印刷されている文字とは異なる文字が入力されました。僕の場合は以下のキーを入力するために修正が必要でした

* 「:」を入力→Shift + 「れ (;)」
* 「\\」を入力→「む（})」

### 色々なソフトをインストール

Debianが起動したので、色々なソフトウェアを入れていきます。

$ apt update
$ apt upgrade
$ apt install zsh tmux git ufw openssh-server openssh-clients network-manager wget neovim
$ apt-get install sudo zip unzip -y

* zsh : bashに代わるターミナル。Powerlevelkを使ってシンタックスハイライトをしたり色々できる
* tmuxx : ターミナル画面を分割する
* git : バージョン管理システム
* ufw : ファイアウォール
* network-manager : 名前の通りネットワーク管理を行う。\`nmtui\`コマンドを実行するとTUI(Text User Interface)を使ってWiFi接続などができるようになります：↓
* wget : wget \[url\]でネット上のファイルをダウンロードできます
* neovim : ターミナル上で動くエディター。vimよりイイゾ！！
* zip unzip : unzip \[ファイル名\] や zip \[ファイル名\] でZIPファイルを圧縮/解凍することだができる

#### Dockerのインストール

Dockerのインストールは[公式ドキュメント](https://docs.docker.com/engine/install/debian/)を見ながらインストールしていきます

$ apt-get remove docker docker-engine docker.io containerd runc # 古いバージョンを削除

$ sudo apt-get update

$ sudo apt-get install apt-transport-https ca-certificates curl gnupg lsb-release # レポジトリのセットアップ

$ curl -fsSL https://download.docker.com/linux/debian/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg # Dockerの公式GPGキーを追加

$ echo "deb [arch=arm64 signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/debian $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

$ sudo apt-get update;sudo apt-get install docker-ce docker-ce-cli containerd.io # Dockerをインストール

  
\`docker --version\` と入力してバージョンが出力されれば、インストールに成功しています  
  
#### Docker-composeのインストール

**これが大変だった**、、、、、というかまだ解決していない、、、、。

<https://github.com/docker/compose/releases> を見ると、最近のバージョン（この記事を書いている時点では1.29.1が最新）ではx86\_64のアーキテクチャ用のパッケージしか配布されていません。しかし。Raspberry pi 3B+ではアーキテクチャは、

$ uname -m
aarch64

です。なので、インストールには特別な方法が必要です（公式ドキュメントには書いていない）

[このサイト](https://qiita.com/tkyonezu/items/912fb7cfaab2f59276dd)等を見てgithubからクローンしてビルドしようとしたけれどうまくいかなかった、、、。そこでpipを使ってインストールすることにした。

$ sudo apt update
$ sudo apt install -y python python-pip libffi-dev python-backports.ssl-match-hostname
$ sudo pip install docker-compose
$ sudo apt-get -y install python3-pip
$ pip3 install docker-compose

上記のようにするとdocker-composeをインスト―ルはできます。できるんですが、正常に動いてくれません、、、、  
docker-compose up -dと打ってもコンテナが動きません（Docker単体だと動くのですが、composeを使うと無理みたい）

おそらく↓と同じような現象なのですが、まだ解決していない状態です、、、  
[github.com](https://github.com/docker/compose/issues/7051)

  
他にもいくつかの方法があったのですが、上手くいっていません。

* docker-composeのGitHubをクローンして自前でビルドする → コンパイルエラーがでた
* sudo apt install docker-compose → docker-compose upでフリーズする

### タイムゾーン設定

$ apt-get install locales
$ sudo dpkg-reconfigure locales

  
を実行すると以下の様な画面が表示されます。  
![f:id:pythonjacascript:20210502061922j:plain](/images/ppythonjacascript2021050220210502061922.jpg)

  
「ja\_JP.UTF-8」までスクロールしてスペースキーを押すと選択できます（選択されると行頭のかっこが\[ \* \]になる（↑写真））。選択後、エンターを押すと次は以下の画面になる：

![f:id:pythonjacascript:20210502061933j:plain](/images/ppythonjacascript2021050220210502061933.jpg)

「ja\_JP.UTF-8」を選択（上画像の様に赤くなっていればOK）してエンターを押します

  
次に、以下のコマンドを実行します

sudo dpkg-reconfigure tzdata

  
![f:id:pythonjacascript:20210502061959j:plain](/images/ppythonjacascript2021050220210502061959.jpg)  
「アジア」を選択→「東京」を選択します。

これでタイムゾーンの設定が終わりました。dateコマンドで正しい時刻が表示されればOKです。

$ date
2021年  4月 24日 土曜日 16:54:48 JST

#### 日本語ツールのインストール

上記のタイムゾーン設定を行ったのち、以下のコマンドを実行すると日本語ツールがインストールされます。

$ sudo apt install man

terminalの出力文字が日本語になるはずです。  
  
  
#### zshの設定

sudo chsh -s $(which zsh)　# zshをデフォルトのターミナルに設定する

#### Powerlevel10kの設定

[github.com](https://github.com/romkatv/powerlevel10k#meslo-nerd-font-patched-for-powerlevel10k)  

これをするとターミナルの見た目がカラフルできれいになります。  
以下のコマンドを実行します

$ cd ~/適当なフォルダ

# まずはフォントファイル（ttf)をダウンロード
$ sudo wget https://github.com/romkatv/powerlevel10k-media/raw/master/MesloLGS%20NF%20Regular.ttfwget 
$ sudo wget https://github.com/romkatv/powerlevel10k-media/raw/master/MesloLGS%20NF%20Bold.ttf
$ sudo wget https://github.com/romkatv/powerlevel10k-media/raw/master/MesloLGS%20NF%20Italic.ttf
$ sudo wget https://github.com/romkatv/powerlevel10k-media/raw/master/MesloLGS%20NF%20Bold%20Italic.ttf

# フォントをインストール(参考：https://gist.github.com/matthewhartman/7b1661dbe6ff26a231e4)
# 必要なら文頭にsudo付けてね
$ apt-get install xfonts-utils x11-xserver-utils -y
$ apt install fontconfig
$ mv *.ttf /usr/share/fonts/truetype # (フォルダが無ければmkdirも！)
$ cd /usr/share/fonts/truetype
$ mkfontscale
$ mkfontdir
$ fc-cache
$ xset fp rehash

### ユーザーの追加

初期ではrootユーザーしかいないので、SSHする用のユーザーを追加します。以下のサンプルではadmin01という名前のユーザーを作成していますが、admin○○みたいな名前だと総当たり攻撃に弱くなる気もします、、、

$ adduser admin01
$ gpasswd -a admin01 sudo # 管理者権限付与
$ usermod -aG sudo admin01 # 上と同じ意味。上で実行できればそれでよし？

admin01に管理者権限が付与されているかどうかは、admin01でsudo関連のコマンドが実行できればOKです

$ su admin01 #admin01ユーザーに切り替え

### SSHの設定

以下の様な設定にしたいと思います

* SSHのポートを22から任意番号に変更する（↓のサンプルでは10022）
* rootのログインは不可
* パスワード入力でのログインは不可、公開鍵認証のみ  
   * ただし、最初は鍵をSCPで転送するので、最初だけパスワードでログインOKにして置き、鍵をラズパイに転送したのちに、パスワード不可に設定する
* ufwでファイアウォールを設定  
   * 特定のポートのみアクセス許可

ではまず、SSHを止めます

$ systemctl stop ssh # sshを止める
$ systemctl status ssh 

次に、/etc/ssh/sshd\_configを以下の様に編集し、セキュリティを強化します。（システム設定ファイルを編集するのでバックアップを取っておくと良いと思います）

# sshd_config
Port 10022 # 好きな番号で（デフォルトでは22になっている）
PermitRootLogin no # rootユーザーのログインを許可しない

ChallengeResponseAuthentication no # 恐らくこれはそのまま
UsePam yes # おそらくこれもそのまま

# 以下はなくても良いがエラーが出たら試してみるのはありかも？
# AllowUsers admin01

（その他の部分は変更せずに、そのまま保存）

  
その他のセキュリティの設定については↓を見てください

[qiita.com](https://qiita.com/comefigo/items/092137ac40f319cb14fa)  

  
ufwでファイアウォールの設定をします。今もう一度調べてみるとiptablesを使うのが主流みたいですね。

$ ufw allow ssh
$ ufw default deny #明示的に設定していないポートはアクセス不可
$ ufw allow 10022 # sshd_configで設定したSSHのポートをオープンにする
$ ufw enable # ファイアウォールを有効にする

  
そして、SSHを起動します。

$ systemctl restart ssh
$ systemctl status ssh
(以下の様に表示されればOKです)
● ssh.service - OpenBSD Secure Shell server
   Loaded: loaded (/lib/systemd/system/ssh.service; enabled; vendor preset: enabled)
   Active: active (running) since Sat 2021-04-24 05:48:36 UTC; 35min ago
     Docs: man:sshd(8)
           man:sshd_config(5)
   （以下略）

そして、ラズパイのIPアドレスを確認し↓

$ ip a
 (略) 
3: wlan0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
link/ether b8:27:eb:31:ee:ed brd ff:ff:ff:ff:ff:ff
inet 192.168.43.118/24 brd 192.168.43.255 scope global dynamic noprefixroute wlan0
       (略)

↑の場合だと192.168.43.118ですね。そして、同じローカルネットワークの別PCからSSHしてみます

$ ssh admin01@192.168.43.118 -p 1002
Are you sure you want to continue connecting (yes/no)? yes
admin01@192.168.43.118's password: パスワードを入力（表示されませんが入力できています）

admin01@rpi3-20210209:~$ echo hello
hello

admin01でログイン成功しました！！！  
  
  
#### 公開鍵認証にする

まず、クライアント側のPCで\`ssh-keygen\`を実行し、鍵のペアを作成します。

$ ssh-keygen -t rsa
Generating public/private rsa key pair.
Enter file in which to save the key (/home/owner4/.ssh/id_rsa): id_rsa ←鍵の保存先（何も入力しないと括弧内に保存される）
Enter passphrase (empty for no passphrase):  <-- パスフレーズ入力（任意）
Enter same passphrase again:
SHA256:114514hogehoge@aaaaa

↑の様に保存先に\`id\_rsa\`コマンドと入力した場合、現在いる場所に id\_rsa(秘密鍵)とid\_rsa.pub（公開鍵）が作成されます。

そして、クライアントPCで以下のコマンドを実行することでサーバーに公開鍵を転送することができます

$ ssh-copy-id -i ./id_rsa.pub admin01@192.168.43.118
/usr/bin/ssh-copy-id: INFO: Source of key(s) to be installed: "./id_rsa.pub"
/usr/bin/ssh-copy-id: INFO: attempting to log in with the new key(s), to filter out any that are already installed
/usr/bin/ssh-copy-id: INFO: 1 key(s) remain to be installed -- if you are prompted now it is to install the new keys
admin01@192.168.43.118's password: ←パスワード入力
Number of key(s) added: 1
Now try logging into the machine, with:   "ssh -p '10022' 'admin01@192.168.43.118'"
and check to make sure that only the key(s) you wanted were added.

これで公開鍵の転送ができました！！！
  
  
試しに公開鍵を使ってSSHしてみます！

$ ssh admin01@192.168.43.118 -p 1002 -i ./id_rsa
Linux rpi3-20210209 4.19.0-14-arm64 #1 SMP Debian 4.19.171-2 (2021-01-30) aarch64
(略)
Last login: Sat Apr 24 05:49:23 2021 from 192.168.43.240
admin01@rpi3-20210209:~$

できたぁ！！！  
  
#### パスワード認証の無効化

ということで、もうパスワード認証は使わないので無効化します。

/etc/ssh/sshd\_configを以下の様に設定します

PasswordAuthentication no # パスワード認証を無効化
ChallengeResponseAuthentication no # チャレンジ（レスポンス）認証を無効化

SSHを再起動します

$ systemctl restart ssh

  
これで、別PCからパスワードログインをしようとして、以下の様に拒否されればOKです。

$ ssh admin01@192.168.43.118 -p 10022
admin01@192.168.43.118: Permission denied (publickey).
zsh: exit 255   ssh admin01@192.168.43.118 -p 10022

ということで、Raspberry Piでサーバーをたてて、LAN中の他のコンピュータから公開鍵認証でSSHすることに成功しましたとさ。めでたしめでたし！！