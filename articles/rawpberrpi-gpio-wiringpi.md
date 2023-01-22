---
title: "【Raspberry pi】WiringPiを使ってC言語でGPIO操作"
emoji: "🤖"
type: "tech"
topics: undefined
published: false
---

少し前に、Python言語でRaspberry piのGPIO操作をする方法を紹介しました。  
[shizenkarasuzon.hatenablog.com](https://shizenkarasuzon.hatenablog.com/entry/2019/03/04/002041)

しかし今回は**C言語**を使ってLチカを行います！！  
  
* [GPIO用ライブラリ](#GPIO用ライブラリ)  
   * [RPi.GPIO](#RPiGPIO)  
   * [WiringPi](#WiringPi)  
   * [pigpio](#pigpio)
* [WiringPiのインストール](#WiringPiのインストール)  
   * [A.pipを使う](#Apipを使う)  
   * [B. apt-getを使う](#B-apt-getを使う)
* [配線](#配線)
* [サンプルプログラム](#サンプルプログラム)
* [実行方法](#実行方法)
* [実行結果](#実行結果)
* [解説](#解説)  
   * [初期設定](#初期設定)  
   * [GPIO制御](#GPIO制御)  
   * [delay()関数](#delay関数)
  
  
### GPIO用ライブラリ

Raspberry piには、GPIOを制御するためのライブラリが以下の3種類あります。

| ライブラリ名   | 使用言語     | 高性能PWM | 割り込み | リモート制御      |
| -------- | -------- | ------ | ---- | ----------- |
| RPi.GPIO | Pythonのみ | ×      | 〇    | ×           |
| WiringPi | PythonとC | 〇      | ×    | 〇（+WebIOPi） |
| pigpio   | PythonとC | 〇      | 〇    | 〇           |

#### RPi.GPIO

RPi.GPIOはPythonを使って簡単にGPIO制御をできることが利点です。  
また、OSにRaspbianを採用している場合、デフォルトでインストールされています。  
[shizenkarasuzon.hatenablog.com](https://shizenkarasuzon.hatenablog.com/entry/2019/03/04/002041)  
  
  
#### WiringPi

個人的意見ですが、ネットなどでC言語を使ってGPIO操作をしている場合、WiringPiを使っているのが大多数のような気がします。

WiringPiは初期インストールされていないので、RaspberryPiをインターネットにつなげてインストールする必要があります。  
  
  
#### pigpio

pigpioを使用するときには、事前に以下のコマンドで「pigpiod」というデーモンを立ち上げる必要があります。

sudo pigpiod

「**デーモン**」とはバックグラウンドで動くソフトウェア（常駐ソフト）のようなものをさします。

こうしてデーモンで起動させることによって、GPIOを制御するプログラムをsudo（管理者権限）なしで実行できるのです。またWebIOPi等を使ってリモートコントロールすることもできるようになりました。

pigpioもRaspbianにはデフォルトでインストールされています。  
  
  
### WiringPiのインストール

WiringPiは初期インストールされていないので、RaspberryPiをインターネットにつなげてインストールする必要があります。

#### A.pipを使う

pipを使う場合は、以下の一行を実行するだけで十分です。

pip install wiringpi

#### B. apt-getを使う

数個のコマンドを実行する必要があります。順番に行きましょう....

sudo apt-get install libi2c-dev

![f:id:pythonjacascript:20190306120526j:plain](/images/ppythonjacascript2019030620190306120526.jpg "f:id:pythonjacascript:20190306120526j:plain")  
  
次に、Gitをインストールします。既にインストール済みの場合は必要ありません。

sudo apt-get install git-core

![f:id:pythonjacascript:20190306120634j:plain](/images/ppythonjacascript2019030620190306120634.jpg "f:id:pythonjacascript:20190306120634j:plain")  

ホームに移動します。git cloneでWiringPiをダウンロードします。

cd ~

git clone git://git.drogon.net/wiringPi

  
WiringPiのディレクトリに移動して、ビルドを行います。

cd wiringPi

./build

以上でWiringPiのインストールは終了です。  
  
### 配線

以下のように配線します。  
![f:id:pythonjacascript:20190306121323j:plain](/images/ppythonjacascript2019030620190306121323.jpg "f:id:pythonjacascript:20190306121323j:plain")  
抵抗値は適当なものを選んでください。  
  
### サンプルプログラム

  wiringPiSetup() ;
  pinMode(0, OUTPUT) ;
  for (;;){
    digitalWrite (0, HIGH);
	 delay (500) ;
    digitalWrite (0,  LOW);
 	 delay (500) ;
  }
  return 0;
}

### 実行方法

まず、↑のプログラムを適当な名前で保存します。**拡張子は「.c」**です。

ここでは、デスクトップ（**/home/pi/デスクトップ/**）に「blink.c」という名前で保存しているていで話を進めます。

次に、cd命令でプログラムを保存したディレクトリに移動します。

cd /home/pi/デスクトップ/

![f:id:pythonjacascript:20190306123112j:plain](/images/ppythonjacascript2019030620190306123112.jpg "f:id:pythonjacascript:20190306123112j:plain")  

次に、以下のコマンドを実行してプログラムをコンパイルします。

gcc -Wall -o blink blink.c -lwiringPi

↑の「blink.c」の部分は保存したプログラムファイル名に書き換えてください。  
![f:id:pythonjacascript:20190306123237j:plain](/images/ppythonjacascript2019030620190306123237.jpg "f:id:pythonjacascript:20190306123237j:plain")  
  
上の画像のように何もエラーが出なければコンパイル成功です。Cファイルと同じディレクトリに「blink」という紙飛行機アイコンの実行ファイルができているはずです。

  
最後に、

sudo ./blink

と打つと、プログラムが開始されます。  
  
  
### 実行結果

このように、取り付けたLEDが点滅します。  
![f:id:pythonjacascript:20190306122618j:plain](/images/ppythonjacascript2019030620190306122618.jpg "f:id:pythonjacascript:20190306122618j:plain")  
  
![f:id:pythonjacascript:20190306122623j:plain](/images/ppythonjacascript2019030620190306122623.jpg "f:id:pythonjacascript:20190306122623j:plain")  
  
プログラムを終了するには、ターミナル上で何かキーを押します。  
  
  
### 解説

上のプログラムを見るとわかりますが、WiringPiは**Arduino言語風**のCライブラリです。

delay()関数などはArduinoそのまんまです。

なのでArduinoを扱ったことがある人は、ある程度のことはプログラムを読めばわかるはずです。  
  
  
#### 初期設定

初めに、WiringPiのセットアップを行います。  
セットアップを行うための関数は以下の３種類があり、度の関数を実行するかでその後のピン指定の番号が変わってきます。

//wiringPi独自のピン番号を利用:
wiringPiSetup()

//GPIO番号を使用
wiringPiSetupGpio()

///sys/class/gpioインターフェースを使い初期化
wiringPiSetupSys()

  
今回はwiringPiSetup()を使いたいと思います。

  
次に、それぞれのピンの役割設定を行います。  
あるピンを出力に設定するには以下のように書きます。

pinMode(0, OUTPUT) ;

  
入力の場合はこうです：

 pinMode(0, INPUT) ;

  
第一引数にはピン番号を指定します。ピン番号の定義は上のセットアップ関数によって変わってきます。   
  
  
#### GPIO制御

pin番目のピンからHIGH（3.3V）を出力します。

digitalWrite(pin, HIGH);

pin番目のピンからLOW（0V）を出力します。

digitalWrite(pin, LOW);

#### delay()関数

delay(114514);

と書くと、**114.514秒**プログラムが動作を停止します。

Pythonの**time.sleep(**)関数は引数の単位は秒\[s\]でしたが、delay()関数の場合は**ミリ秒\[ms\]**です。間違えないように。