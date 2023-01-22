---
title: "【Raspberry Pi】MPU-6050から加速度とジャイロと気温データを取得（C言語編）"
emoji: "🤖"
type: "tech"
topics: undefined
published: false
---

[shizenkarasuzon.hatenablog.com](https://shizenkarasuzon.hatenablog.com/entry/2019/02/16/162647)

↑の記事のRaspberry Piバージョンです。

* [ハードウェア](#ハードウェア)
* [下準備（raspy-config）](#下準備raspy-config)  
   * [I2Cデバイスの設定](#I2Cデバイスの設定)
* [I2Cの動作確認](#I2Cの動作確認)  
   * [WiringPiのインストール](#WiringPiのインストール)
* [プログラム](#プログラム)
* [実行方法](#実行方法)
* [実行結果](#実行結果)
* [解説](#解説)  
   * [MPU-6050について](#MPU-6050について)  
   * [I2Cデバイスの使い方](#I2Cデバイスの使い方)  
   * [下準備](#下準備)  
   * [接続](#接続)  
   * [データの受信](#データの受信)  
   * [データを送信](#データを送信)
* [参考文献](#参考文献)

この記事では、MPU6050から加速度とジャイロの計測データを取得して、それをディスプレイに表示するところまでを行います。  
それ以降の内容（角度算出etc.）は、別の記事に書いていくつもりです。  
  
### ハードウェア

以下の四本を接続してください。  
![f:id:pythonjacascript:20190304005009j:plain](/images/ppythonjacascript2019030420190304005009.jpg "f:id:pythonjacascript:20190304005009j:plain")  

| ラズパイピン番号 | MPU6050 |
| -------- | ------- |
| 1pin     | VCC     |
| 3pin     | SDA     |
| 5pin     | SCL     |
| 6pin     | GND     |

上のようにつなぐと、MPU6050に電力が供給され、MPU6050上の赤色LEDが光ります。  
![f:id:pythonjacascript:20190304005137j:plain](/images/ppythonjacascript2019030420190304005137.jpg "f:id:pythonjacascript:20190304005137j:plain")  

### 下準備（raspy-config）

プログラムを実行する前に、いくつか環境構築を行います。

#### I2Cデバイスの設定

MPU-6050とRaspberry Piの通信には、「I2C」という通信規格を使用します。

そのためには、「**raspy-config**」を使った設定が必要です。

まず、プロンプトで「**sudo raspi-config**」と打ち込みます。  
![f:id:pythonjacascript:20190304005651j:plain](/images/ppythonjacascript2019030420190304005651.jpg "f:id:pythonjacascript:20190304005651j:plain")

以下のような画面に変わるので**「Interface Options」→「I2C」**の順に選択します。  
![f:id:pythonjacascript:20190304005737j:plain](/images/ppythonjacascript2019030420190304005737.jpg "f:id:pythonjacascript:20190304005737j:plain")  

![f:id:pythonjacascript:20190304005752j:plain](/images/ppythonjacascript2019030420190304005752.jpg "f:id:pythonjacascript:20190304005752j:plain")  

そして「I2Cを有効化しますか？」と聞かれるので（下画像）「＜はい＞」→「＜了解＞」を選択します。  
![f:id:pythonjacascript:20190304005954j:plain](/images/ppythonjacascript2019030420190304005954.jpg "f:id:pythonjacascript:20190304005954j:plain")  
  
![f:id:pythonjacascript:20190304010003j:plain](/images/ppythonjacascript2019030420190304010003.jpg "f:id:pythonjacascript:20190304010003j:plain")  
  
![f:id:pythonjacascript:20190304010008j:plain](/images/ppythonjacascript2019030420190304010008.jpg "f:id:pythonjacascript:20190304010008j:plain")  

この後、**再起動**すればI2Cが有効になっています。  
  
### I2Cの動作確認

上の方法でI2Cが有効化されているのかを確かめるには、

gpio readall

を実行してください。

上のコマンドを実行すると、全てのGPIOピンの状態を取得できます。  
![f:id:pythonjacascript:20190306114533j:plain](/images/ppythonjacascript2019030620190306114533.jpg "f:id:pythonjacascript:20190306114533j:plain")  
I2C通信に使われるピンは3ピン（SDA)と5ピン（SCL）ですが、I2CがOFFの時はどちらも「GPIO」として機能しています。

  
しかし、I2CがONになるとそれぞれ「SDA」や「SCL」というI2C通信用のピンとして設定されるので、ピンの状態を見ることでI2Cが有効になっているかがわかるのです。

  
以下のコマンドを実行すると、現在I2Cを使って接続されているデバイスのアドレスが一覧表示されます。

sudo i2cdetect -y 1

![f:id:pythonjacascript:20190306114456j:plain](/images/ppythonjacascript2019030620190306114456.jpg "f:id:pythonjacascript:20190306114456j:plain")  
  
MPU-6050のアドレスは「0x68」なので、正常に認識されています。  
  
#### WiringPiのインストール

C言語でGPIO操作をするために、WiringPiというライブラリを用います。WiringPiはデフォルトでインストールされていないので、インターネットから持ってくる必要があります。

インストール方法はこちらの記事を参考にしてください。  
[shizenkarasuzon.hatenablog.com](https://shizenkarasuzon.hatenablog.com/entry/2019/03/06/123608)  
  
  
### プログラム

以下のプログラムを実行してください。

#include <stdio.h>
#include <stdint.h>
#include <wiringPi.h>
#include <wiringPiI2C.h>

#define ACCEL_X_OUT 0x3b
#define ACCEL_Y_OUT 0x3d
#define ACCEL_Z_OUT 0x3f
#define TEMP_OUT 0x41
#define GYRO_X_OUT 0x43 
#define GYRO_Y_OUT 0x45
#define GYRO_Z_OUT 0x47

#define PWR_MGMT_1 0x6B  //PWR_MGMT_1
#define PWR_MGMT_2 0x6c  //PWR_MGMT_2
#define DEV_ADDR 0x68    // I2C

float get2data(int fd, unsigned int reg){
  unsigned int h_value = wiringPiI2CReadReg8(fd, reg);
  unsigned int l_value = wiringPiI2CReadReg8(fd, reg+1);
  float value = (h_value << 8) + l_value;
  if(value>=32768) return value-65534;  //32768=0x8000, 65534 = 0xFFFF 
  else return value;
}


void mainloop(int fd){
  float gx,gy,gz,ax,ay,az;
  while(1){
    gx = get2data(fd, GYRO_X_OUT)/131.0;
    gy = get2data(fd, GYRO_Y_OUT)/131.0;
    gz = get2data(fd, GYRO_Z_OUT)/131.0;

    ax = get2data(fd, ACCEL_X_OUT)/16384.0;
    ay = get2data(fd, ACCEL_Y_OUT)/16384.0;
    az = get2data(fd, ACCEL_Z_OUT)/16384.0;
    printf("gx=%.2f, gy=%.2f, gz=%.2f, ax=%.2f, ay=%.2f, az=%.2f\n", gx,gy,gz,ax,ay,az);
  }
}

int main(){
  int fd = wiringPiI2CSetup(DEV_ADDR);
  if (fd == -1){
    printf("ERROR : No device!!");
	 return 0;
  }
  wiringPiI2CReadReg8 (fd, PWR_MGMT_1);
  wiringPiI2CWriteReg16(fd, PWR_MGMT_1, 0);

  mainloop(fd);
}
  
  
### 実行方法

まず、↑のプログラムを適当な名前で保存します。**拡張子は「.c」**です。

ここでは、デスクトップ（**/home/pi/デスクトップ/**）に「6050\_1.c」という名前で保存しているていで話を進めます。

次に、cd命令でプログラムを保存したディレクトリに移動します。

cd /home/pi/デスクトップ/

![f:id:pythonjacascript:20190306123112j:plain](/images/ppythonjacascript2019030620190306123112.jpg "f:id:pythonjacascript:20190306123112j:plain")  

次に、以下のコマンドを実行してプログラムをコンパイルします。

gcc -Wall -o 6050_1 6050_1.c -lwiringPi

↑の「6050\_1.c」の部分は保存したプログラムファイル名に書き換えてください。  
  
何もエラーが出なければコンパイル成功です。Cファイルと同じディレクトリに「6050\_1」という紙飛行機アイコンの実行ファイルができているはずです。

  
最後に、

sudo ./6050_1

と打つと、プログラムが開始されます。  
  
### 実行結果

ディスプレイに加速度とジャイロの値がリアルタイムで表示されるはずです。  
![f:id:pythonjacascript:20190306114021j:plain](/images/ppythonjacascript2019030620190306114021.jpg "f:id:pythonjacascript:20190306114021j:plain")   
  
### 解説

#### MPU-6050について

こちらをご覧ください。  
[shizenkarasuzon.hatenablog.com](https://shizenkarasuzon.hatenablog.com/entry/2019/02/16/162647)  
  
  
#### I2Cデバイスの使い方

そもそも、「**I2C**」とは通信規格の一種をさします。「**SPI**」とか「**USART**」とかと同じようなものです。

  
Raspberry PiでI2Cデバイスを使うプログラムについて簡単に紹介します。

I2C通信を行うには、「**smbus**（System Management Bus）」というライブラリを使って[このように](https://shizenkarasuzon.hatenablog.com/entry/2019/03/06/114248)通信することもできますが、今回は敢えてWiringPiを使いました。  
  
#### 下準備

WiringPiのインストール方法は[こちら](https://shizenkarasuzon.hatenablog.com/entry/2019/03/06/123608)に書いています。  
これを見ながらダウンロード＆インストールを行ってください。  
  
#### 接続

//ライブラリを読み込む
#include <wiringPi.h>
#include <wiringPiI2C.h>

//セットアップ
int fd = wiringPiI2CSetup(DEV_ADDR)

**#include**構文を使ってWiringPiのライブラリを読み込みます。こう書くことで、これ以降でWiringPiの中で定義された関数などを使うことができます。

また、プログラムをコンパイルするときにも「**\-lwiringPi** 」を指定する必要があります。

そして、 wiringPiI2CSetup(DEV\_ADDR)でI2C通信のセットアップを行います。引数にはデバイスのアドレスを指定します。  
  
  
#### データの受信

int data = wiringPiI2CRead (int fd)

デバイスからデータを一バイト読み込み、int型で返します。  
  
int data = wiringPiI2CReadReg8(fd, reg);
int data =  wiringPiI2CReadReg16 (int fd, int reg) ;//16ビットの場合

トランザクションデータを送信します。

「トランザクションデータ」についてですが、[このサイト](https://teratail.com/questions/133723)に以下のように書いてありました。

> i2c自体はアドレス指定してデータ送りつける/受け取るだけで、コマンド（reg）とかありません  
> 多くのデバイスの仕様で1バイトのコマンドに続けてデータを送るようになっているためそれが用意されてるのかと

  
上のプログラムでは、第二引数の**reg**には、MPU-6050から受信したいデータのレジスタアドレスを指定しています。  
  
  
#### データを送信

int wiringPiI2CWrite (int fd, int data) ;

デバイスにdata（一バイト）を送信します。いくつかのデバイスはこのように内部レジスタの番地を指定せずに値の書き込みを行うことができます。  
  
  
int wiringPiI2CWriteReg8 (int fd, int reg, int data) ;
int wiringPiI2CWriteReg16 (int fd, int reg, int data) ;　//16ビットの場合

トランザクションデータを送信します。

「トランザクションデータ」についてですが、このサイトに以下のように書いてありました。

> i2c自体はアドレス指定してデータ送りつける/受け取るだけで、コマンド（cmd）とかありません  
> 多くのデバイスの仕様で1バイトのコマンドに続けてデータを送るようになっているためそれが用意されてるのかと
  
  
### 参考文献

[I2C Library | Wiring Pi](http://wiringpi.com/reference/i2c-library/)  
<https://teratail.com/questions/133723>