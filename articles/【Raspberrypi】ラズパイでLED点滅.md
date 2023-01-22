---
title: "【Raspberry pi】ラズパイでLED点滅"
emoji: ""
type: ""
topics: []
published: false
---

前回、ラズベリーパイの起動試験を行いました。

[shizenkarasuzon.hatenablog.com](https://shizenkarasuzon.hatenablog.com/entry/2019/01/25/093759)

今回は、**Raspberry pi 3 Model B**を使って、LEDの点滅（通称、**Lチカ**）を行ってみたいと思います。

* [ライブラリをインストール](#ライブラリをインストール)
* [配線](#配線)
* [環境作成](#環境作成)
* [プログラム](#プログラム)
* [実行](#実行)
* [解説](#解説)  
   * [GPIOピンの使い方](#GPIOピンの使い方)  
   * [下準備](#下準備)  
   * [メイン処理（点滅プログラム）](#メイン処理点滅プログラム)  
   * [メイン処理後](#メイン処理後)

### ライブラリをインストール

ラズベリーパイには約50本のピンがあります。  
それぞれが固有の役割を持っており、LEDやスイッチ、センサーを適切な位置につなぐことで様々なことができます。

そのように、I/O（GPIIO）ピンを使うために必要なライブラリが「**RPi.GPIO**」です。  
他にも「**wiringpi**」など多くの種類のライブラリがありますが、「RPi.GPIO」は初めからインストールされているので（Raspbianの場合）、「RPi.GPIO」を採用しました。

NOOBS等のOSを使っており、「RPi.GPIO」がインストールされていなかった場合は、下のコマンドを実行してインストールしてください。  
  
sudo pip install rpi.gpio

もしくは、

sudo apt-get install python-rpi.gpio

を試してください。「**sudo**」というのは管理者権限で実行する、という意味です。
  
  
因みに、実行方法ですが、デスクトップ画面左上の![f:id:pythonjacascript:20190225225046j:plain](/images/ppythonjacascript2019022520190225225046.jpg "f:id:pythonjacascript:20190225225046j:plain")アイコンをクリックしてください。  
すると、下の写真のようにコマンドプロンプトのようなウィンドウが開くので、ここに↑の命令を書き込んでください。  
![f:id:pythonjacascript:20190225224921j:plain](/images/ppythonjacascript2019022520190225224921.jpg "f:id:pythonjacascript:20190225224921j:plain")  
  
  
### 配線

RPIのGPIOは以下のようなピンアサインになっています。  
![f:id:pythonjacascript:20190225224029j:plain](/images/ppythonjacascript2019022520190225224029.jpg "f:id:pythonjacascript:20190225224029j:plain")  

  
今回は、以下のような配線にしてください。  
![f:id:pythonjacascript:20190225225900j:plain](/images/ppythonjacascript2019022520190225225900.jpg "f:id:pythonjacascript:20190225225900j:plain")  

  
抵抗値は計算して適当な値を選んでください。  
![f:id:pythonjacascript:20190225230952j:plain](/images/ppythonjacascript2019022520190225230952.jpg "f:id:pythonjacascript:20190225230952j:plain")  
  
  
### 環境作成

LED点滅用プログラムを実行する環境を作ります。

先ほどと同じように、デスクトップ画面左上の![f:id:pythonjacascript:20190225225046j:plain](/images/ppythonjacascript2019022520190225225046.jpg "f:id:pythonjacascript:20190225225046j:plain")アイコンをクリックします。  
![f:id:pythonjacascript:20190225224921j:plain](/images/ppythonjacascript2019022520190225224921.jpg "f:id:pythonjacascript:20190225224921j:plain")

そして、以下の命令を実行すると、下のような画面になるはずです。

sudo idle &

  
![f:id:pythonjacascript:20190225230302j:plain](/images/ppythonjacascript2019022520190225230302.jpg "f:id:pythonjacascript:20190225230302j:plain")  
新しく「**IDLE**」というプログラムが起動しました。

  
ここで、**「idle」のメニューバーの「File」→「New File」**からpyファイルを新規作成しましょう。  
  
  
### プログラム

以下のプログラムを入力してください。

import RPi.GPIO as GPIO
from time import sleep

GPIO.setmode(GPIO.BCM)
GPIO.setup(25, GPIO.OUT)

for i in range(10):
    GPIO.output(25, GPIO.HIGH)
    sleep(0.5)
    GPIO.output(25, GPIO.LOW)
    sleep(0.5)

GPIO.cleanup()

  
そして、名前を付けて保存しましょう。  
「File」→「Save As」で名前を付けて保存できます。  
![f:id:pythonjacascript:20190225230443j:plain](/images/ppythonjacascript2019022520190225230443.jpg "f:id:pythonjacascript:20190225230443j:plain")  
  
  
### 実行

では、先ほど書いたプログラムのエディタ上で、「**F5キー**」を押してみてください。もしくは、メニューバーの**「Run」→「Run Module」**をクリックしてください。

...LEDが点滅したでしょうか？

このようになれば成功です。  
![f:id:pythonjacascript:20190225231006j:plain](/images/ppythonjacascript2019022520190225231006.jpg "f:id:pythonjacascript:20190225231006j:plain")  
  
  
### 解説

LEDの点滅プログラムを簡単に解説していきます。

#### GPIOピンの使い方

GPIOピンを使うには、上にも書いたように、RPi.GPIOやwiringpiのようなライブラリが必要です。今回はRPi.GPIOを使用したのでそれについて書いていきます。

  
次の一文でRPi.GPIOがインポートされ、プログラム内でRPi.GPIOライブラリを使用することができます。

import RPi.GPIO as GPIO

#### 下準備

まず、使用するGPIOピンを設定します。

GPIO.setmode(GPIO.BCM)
GPIO.setup(25, GPIO.OUT)

二番目の文は「GPIO25ピンを出力’（GPIO.OUT)に設定する」という意味です。

ここで、GPIOピンの指定方法は「**GPIO.BOARD**」と「**GPIO.BCM**」の二通りがあります。

| GPIO.BOARD | ピン番号で指定   |
| ---------- | --------- |
| GPIO.BCM   | GPIO番号で指定 |

「GPIO.BCM」は下の図の「GPIO X」のXの数字を使ってピンを指定する方法で、「GPIO.BOARD」は下の図で上から順番に1、2、...とナンバリングされている、その数値を使ってピンを指定する方法です。  
![f:id:pythonjacascript:20190225224029j:plain](/images/ppythonjacascript2019022520190225224029.jpg "f:id:pythonjacascript:20190225224029j:plain")

  
つまり、「GPIO.BCM」での「25番目」が指し示すピンは、「GPIO.BOARD」での「22番目」のピンと同じことになります。

ここまで書けばわかると思いますが、

GPIO.setmode(GPIO.BCM)

は「GPIO.BCM設定でピン番号を指定する」ということを表します。  
  
#### メイン処理（点滅プログラム）

点滅処理について書くことは時にありません。

GPIO.output(25, GPIO.HIGH)

と書けば、25ピンがON（電圧HIGH)になります。

反対に、

GPIO.output(25, GPIO.LOW)

と書けば、25ピンがOFF（電圧LOW）になります。

因みに、

sleep(0.5)

はプログラムの動作を一時停止する関数です。Arduinoのdelay()関数にあたります。  
引数には動作を中止する時間を指定します。注意してほしいのは、**単位がミリ秒ではなく秒**ということです。  
上のプログラムでは、「0.5秒（500ms）動作停止」になります。  
  
#### メイン処理後

一度使ったピンはクリーンアップして再度使用できるようにリセットする必要があります。

リセットしなければ、

This channel is already in use

というエラーが出てしまいます。これを防ぐために、

GPIO.cleanup()

と書く必要があります。