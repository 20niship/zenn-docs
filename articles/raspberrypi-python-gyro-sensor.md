---
title: "【Raspberry Pi】MPU-6050から加速度とジャイロと気温データを取得（Python編）"
emoji: ""
type: ""
topics: undefined
published: false
---

[shizenkarasuzon.hatenablog.com](https://shizenkarasuzon.hatenablog.com/entry/2019/02/16/162647)

↑の記事のRaspberry Piバージョンです。

* [ハードウェア](#ハードウェア)
* [下準備（raspy-config）](#下準備raspy-config)
* [I2Cの動作確認](#I2Cの動作確認)
* [プログラム](#プログラム)
* [実行結果](#実行結果)
* [解説](#解説)  
   * [MPU-6050について](#MPU-6050について)  
   * [I2Cデバイスの使い方](#I2Cデバイスの使い方)  
   * [下準備](#下準備)  
   * [接続](#接続)  
   * [データの受信](#データの受信)  
   * [データを送信](#データを送信)

この記事では、MPU6050から加速度とジャイロの計測データを取得して、それをディスプレイに表示するところまでを行います。  
それ以降の内容（角度算出etc.）は、別の記事に書いていくつもりです。  
  
### ハードウェア

以下の四本を接続してください。  
![f:id:pythonjacascript:20190304005009j:plain](/images/ppythonjacascript2019030420190304005009.jpg)  

| ラズパイピン番号 | MPU6050 |
| -------- | ------- |
| 1pin     | VCC     |
| 3pin     | SDA     |
| 5pin     | SCL     |
| 6pin     | GND     |

上のようにつなぐと、MPU6050に電力が供給され、MPU6050上の赤色LEDが光ります。  
![f:id:pythonjacascript:20190304005137j:plain](/images/ppythonjacascript2019030420190304005137.jpg)  

### 下準備（raspy-config）

MPU-6050とRaspberry Piの通信には、「I2C」という通信規格を使用します。

そのためには、「**raspy-config**」を使った設定が必要です。

まず、プロンプトで「**sudo raspi-config**」と打ち込みます。  
![f:id:pythonjacascript:20190304005651j:plain](/images/ppythonjacascript2019030420190304005651.jpg)

以下のような画面に変わるので**「Interface Options」→「I2C」**の順に選択します。  
![f:id:pythonjacascript:20190304005737j:plain](/images/ppythonjacascript2019030420190304005737.jpg)  

![f:id:pythonjacascript:20190304005752j:plain](/images/ppythonjacascript2019030420190304005752.jpg)  

そして「I2Cを有効化しますか？」と聞かれるので（下画像）「＜はい＞」→「＜了解＞」を選択します。  
![f:id:pythonjacascript:20190304005954j:plain](/images/ppythonjacascript2019030420190304005954.jpg)  
  
![f:id:pythonjacascript:20190304010003j:plain](/images/ppythonjacascript2019030420190304010003.jpg)  
  
![f:id:pythonjacascript:20190304010008j:plain](/images/ppythonjacascript2019030420190304010008.jpg)  

この後、**再起動**すればI2Cが有効になっています。  
  
### I2Cの動作確認

上の方法でI2Cが有効化されているのかを確かめるには、

gpio readall

を実行してください。

上のコマンドを実行すると、全てのGPIOピンの状態を取得できます。  
![f:id:pythonjacascript:20190306114533j:plain](/images/ppythonjacascript2019030620190306114533.jpg)  
I2C通信に使われるピンは3ピン（SDA)と5ピン（SCL）ですが、I2CがOFFの時はどちらも「GPIO」として機能しています。

  
しかし、I2CがONになるとそれぞれ「SDA」や「SCL」というI2C通信用のピンとして設定されるので、ピンの状態を見ることでI2Cが有効になっているかがわかるのです。

  
以下のコマンドを実行すると、現在I2Cを使って接続されているデバイスのアドレスが一覧表示されます。

sudo i2cdetect -y 1

![f:id:pythonjacascript:20190306114456j:plain](/images/ppythonjacascript2019030620190306114456.jpg)  
  
MPU-6050のアドレスは「0x68」なので、正常に認識されています。  
  
  
### プログラム

以下のプログラムを実行してください。

# -*- coding: utf-8 -*-

import smbus
import math
from time import sleep
import time

DEV_ADDR = 0x68

ACCEL_XOUT = 0x3b
ACCEL_YOUT = 0x3d
ACCEL_ZOUT = 0x3f
TEMP_OUT = 0x41
GYRO_XOUT = 0x43
GYRO_YOUT = 0x45
GYRO_ZOUT = 0x47

PWR_MGMT_1 = 0x6b
PWR_MGMT_2 = 0x6c   

bus = smbus.SMBus(1)
bus.write_byte_data(DEV_ADDR, PWR_MGMT_1, 0)

def read_word(adr):
    high = bus.read_byte_data(DEV_ADDR, adr)
    low = bus.read_byte_data(DEV_ADDR, adr+1)
    val = (high << 8) + low
    return val

def read_word_sensor(adr):
    val = read_word(adr)
    if (val >= 0x8000):  return -((65535 - val) + 1)
    else:  return val

def get_temp():
    temp = read_word_sensor(TEMP_OUT)
    x = temp / 340 + 36.53      # data sheet(register map)記載の計算式.
    return x

def getGyro():
    x = read_word_sensor(GYRO_XOUT)/ 131.0
    y = read_word_sensor(GYRO_YOUT)/ 131.0
    z = read_word_sensor(GYRO_ZOUT)/ 131.0
    return [x, y, z]


def getAccel():
    x = read_word_sensor(ACCEL_XOUT)/ 16384.0
    y= read_word_sensor(ACCEL_YOUT)/ 16384.0
    z= read_word_sensor(ACCEL_ZOUT)/ 16384.0
    return [x, y, z]


while 1:
    ax, ay, az = getAccel()
    gx, gy, gz = getGyro()
    # 下の文が間違っており、全てgxが出力されていたので修正いたしました。
    print ('{:4.3f},{:4.3f},{:4.3f},{:4.3f},{:4.3f},{:4.3f},' .format(gx, gy, gz, ax, ay, az))
  
  
### 実行結果

ディスプレイ上に、センサーから取得した3軸加速度の値と3軸角速度の値がリアルタイムで表示されるはずです。  
![f:id:pythonjacascript:20190306171530j:plain](/images/ppythonjacascript2019030620190306171530.jpg)  
  
  
### 解説

#### MPU-6050について

こちらをご覧ください。  
[shizenkarasuzon.hatenablog.com](https://shizenkarasuzon.hatenablog.com/entry/2019/02/16/162647)  
  
  
#### I2Cデバイスの使い方

そもそも、「**I2C**」とは通信規格の一種をさします。「**SPI**」とか「**USART**」とかと同じようなものです。

  
Raspberry PiでI2Cデバイスを使うプログラムについて簡単に紹介します。

I2C通信を行うには、「**smbus**（System Management Bus）」というライブラリが必要です。  
<https://raspberry-projects.com/pi/programming-in-python/i2c-programming-in-python/using-the-i2c-interface-2>

まだインストールしていない場合は、

sudo apt-get install python-smbus

を行ってください。  
  
#### 下準備

プログラムを書く前に、「sudo pi-config」でI2Cを有効化します。  
また、「gpio readall」を実行し、目的のI2Cデバイスのアドレスを確認しておきます。  
  
#### 接続

#ライブラリを読み込む
import smbus

#SMBusオブジェクトを作成
bus = smbus.SMBus(1)    # 0 = /dev/i2c-0 (port I2C0), 1 = /dev/i2c-1 (port I2C1)

上のプログラムの smbus.SMBus(1) でI2C通信を開始します。

Raspberry Piには、2つのI2Cポートがあり、I2C0を使う場合は引数に「0」を指定し、I2C1を使う場合は引数に「1」を指定します。  
  
#### データの受信

data = read_byte(int addr)

アドレスがaddrのデバイスの読み込むレジスタを指定せずに、一バイト読み込みます。  
返り値はlong型です。

data =read_byte_data(int addr, char cmd)

トランザクションデータを送信します。

「トランザクションデータ」についてですが、このサイトに以下のように書いてありました。

> i2c自体はアドレス指定してデータ送りつける/受け取るだけで、コマンド（cmd）とかありません  
> 多くのデバイスの仕様で1バイトのコマンドに続けてデータを送るようになっているためそれが用意されてるのかと
  
  
#### データを送信

write_byte(int addr, char val)

アドレスがaddrのデバイスにval（一バイト）を送信します。  
  
  
 write_byte_data(int addr,char cmd,char val)

トランザクションデータを送信します。

「トランザクションデータ」についてですが、このサイトに以下のように書いてありました。

> i2c自体はアドレス指定してデータ送りつける/受け取るだけで、コマンド（cmd）とかありません  
> 多くのデバイスの仕様で1バイトのコマンドに続けてデータを送るようになっているためそれが用意されてるのかと