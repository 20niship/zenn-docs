---
title: "【Raspberry Pi】MPU-6050を使って姿勢角算出（Python編）"
emoji: ""
type: ""
topics: undefined
published: false
---

* [この記事の内容](#この記事の内容)
* [ハードウェア](#ハードウェア)
* [プログラム](#プログラム)
* [実行結果](#実行結果)
* [姿勢角の算出方法](#姿勢角の算出方法)

### この記事の内容

前回、MPU-6050を使って加速度とジャイロの計測データを取得して、それをディスプレイに表示しました。  
[shizenkarasuzon.hatenablog.com](https://shizenkarasuzon.hatenablog.com/entry/2019/03/06/114248)

今回は、そうして得られた値から姿勢角（ピッチ角とロール角）を算出してみます。  
  
  
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

### プログラム

# -*- coding: utf-8 -*-

import smbus
import math
from time import sleep

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

# Sensor data read
def read_word_sensor(adr):
    val = read_word(adr)
    if (val >= 0x8000):         # minus
        return -((65535 - val) + 1)
    else:                       # plus
        return val


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

    #print ('{:4.3f},{:4.3f}, {:4.3f}, {:4.3f},{:4.3f}, {:4.3f},' .format(gx, gy, gz, ax, ay, az))
    roll = math.atan(ay/az) * 57.324
    pitch = math.atan(-ax / math.sqrt( ay* ay+ az*az ) ) * 57.324

    #pitch = math.atan(-ax / (ay*math.sin(roll) + az*math.cos(roll)))

    # ↓のprint()分が間違っていたため修正いたしました。申し訳ありません
    print('{:4.3f}, {:4.3f},' .format(pitch, roll))
  
  
### 実行結果

プログラムの実行方法や、その他いろいろ事前準備が必要なので、そちらについては[前回の記事](https://shizenkarasuzon.hatenablog.com/entry/2019/03/06/114248)を見てください。

正しく実行されると、以下のようにピッチ角とロール角が表示されるはずです。  
![f:id:pythonjacascript:20190306163602j:plain](/images/ppythonjacascript2019030620190306163602.jpg)  

### 姿勢角の算出方法

姿勢角は以下の方法で算出しています。

  
姿勢角には、**Pitch（ピッチ）、Roll（ロール）、Yaw（ヨー）**の3種類があります。

この記事では、**ピッチ角とロール角**を求めてみます。

それぞれ、以下の数式で表すことができます。

ロール角![\phi](https://chart.apis.google.com/chart?cht=tx&chl=%5Cphi)について  
![\phi = atan(\dfrac{a_y}{a_z})](https://chart.apis.google.com/chart?cht=tx&chl=%5Cphi%20%3D%20atan%28%5Cdfrac%7Ba_y%7D%7Ba_z%7D%29)

ピッチ角![\psi](https://chart.apis.google.com/chart?cht=tx&chl=%5Cpsi)について  
![\psi = atan(\dfrac{-a_x}{a_y sin(\phi) + a_z cos(\phi)})](https://chart.apis.google.com/chart?cht=tx&chl=%5Cpsi%20%3D%20atan%28%5Cdfrac%7B-a_x%7D%7Ba_y%20sin%28%5Cphi%29%20%2B%20a_z%20cos%28%5Cphi%29%7D%29)

![a_x](https://chart.apis.google.com/chart?cht=tx&chl=a_x)、![a_x](https://chart.apis.google.com/chart?cht=tx&chl=a_x)、![a_x](https://chart.apis.google.com/chart?cht=tx&chl=a_x)はそれぞれ、![x](https://chart.apis.google.com/chart?cht=tx&chl=x)軸、![y](https://chart.apis.google.com/chart?cht=tx&chl=y)軸、![z](https://chart.apis.google.com/chart?cht=tx&chl=z)軸の加速度を表します。

この部分をArduinoのプログラムに実装すると、このようになります。

roll = math.atan(ay/az) * 57.324
pitch = math.atan(-ax / math.sqrt( ay* ay+ az*az ) ) * 57.324

**57.324**というのは、![\dfrac{360}{2\pi}](https://chart.apis.google.com/chart?cht=tx&chl=%5Cdfrac%7B360%7D%7B2%5Cpi%7D)で、弧度法（rad）の数値を度数法（°）に変更にするのに使用しています。