---
title: "【Raspberry pi】PWM出力でLEDの明るさを制御する"
emoji: ""
type: ""
topics: []
published: false
---

[前回の記事](https://shizenkarasuzon.hatenablog.com/entry/2019/03/04/002041#解説)はLEDの点滅課題（通称「Lチカ」）を行ったので、今回は少しレベルを上げてアナログ制御に挑戦してみます。

尚、ラズベリーパイのセットアップが終わっていなかったり、Pythonファイルの実行方法が分からない、という人はこちらの記事から読まれることをお勧めします。

* [配線](#配線)
* [プログラム](#プログラム)
* [実行結果](#実行結果)
* [解説](#解説)  
   * [メイン処理（明るさ制御プログラム）](#メイン処理明るさ制御プログラム)  
   * [メイン処理後](#メイン処理後)
  
  
### 配線

以下のような配線です。  
![f:id:pythonjacascript:20190225231520j:plain](/images/ppythonjacascript2019022520190225231520.jpg "f:id:pythonjacascript:20190225231520j:plain")  
  
  
### プログラム

import RPi.GPIO as GPIO
import time

GPIO.setmode(GPIO.BCM)

led_pin = 4
GPIO.setup(led_pin, GPIO.OUT)

#GPIO.setwarnings(False) 

led1 = GPIO.PWM(led_pin, 50) #50Hz
led1.start(0)

for loop in range(10):
    for i in range(0,100,20):
        led1.ChangeDutyCycle(i)
        time.sleep(0.05)

    for i in range(100,0,-20):
        led1.ChangeDutyCycle(i)
        time.sleep(0.05)
        
led1.stop()
GPIO.cleanup()

### 実行結果

このようにLEDが点滅する動作を10回繰り返すはずです。  
![f:id:pythonjacascript:20190225232401g:plain](/images/ppythonjacascript2019022520190225232401.gif "f:id:pythonjacascript:20190225232401g:plain")  
  
  
### 解説

前回、LEDが点滅するプログラムを書きました。

前回との違いは、LEDが単なるON/OFFだけでなく、その間の状態を表現していることです。

まず、使用するGPIOピンを設定します。

GPIO.setmode(GPIO.BCM)
GPIO.setup(25, GPIO.OUT)
led1 = GPIO.PWM(4, 50) #50Hz

  
今回新しく出てきたのは、三行目のプログラムでしょう。上の2行については[前回の記事](https://shizenkarasuzon.hatenablog.com/entry/2019/03/04/002041#解説)を見て下さい。  
ここでPWMの初期設定を行っています。

led = GPIO.PWM(ピン番号, 周波数[Hz])

と書くことで、特定のピンのPWMオブジェクトを作成します。第二引数は、PWMのパルスの周波数のことです。

そして、

led.start(0)

と書くことで、PWM出力が開始されます。引数には**デューティー比**（0～100の％表記）を指定します。  
  
#### メイン処理（明るさ制御プログラム）

LEDの明るさを変えるには、PWM出力のデューティー比を変える必要があります。

led.ChangeDutyCycle(30)

のように書くことで、デューティー比を変更することができます。引数にデューティー比を0～100の間で指定します。上の例ではデューティー比30％（ONの時間：OFFの時間＝3：7）です。

  
因みに、PWMのパルス周波数を変更するには、

led.ChangeFrequency(100) #100Hz

と書きます。  
  
  
#### メイン処理後

一度使ったピンはクリーンアップして再度使用できるようにリセットする必要があります。

リセットしなければ、

This channel is already in use

というエラーが出てしまいます。これを防ぐために、

led.stop()
GPIO.cleanup()

と書く必要があります。