---
title: "【Raspberry pi】サーボモータをPWM制御する"
emoji: "🤖"
type: "tech"
topics: []
published: false
---

**Raspberry pi 3 model B**を使って、マイクロサーボモータ「**SG90**」を制御します。

### 配線

![f:id:pythonjacascript:20190225233845j:plain](/images/ppythonjacascript2019022520190225233845.jpg "f:id:pythonjacascript:20190225233845j:plain")  
上のように配線してください。  
サーボモータからは3本線が出ていますが、各線の役割は以下の通りです。

| コード色     | 役割      |
| -------- | ------- |
| 黒線       | GND（0V） |
| 赤線       | 電源+（5V） |
| オレンジ（黄色） | 信号線     |

よって上のような配線になるのですが、上の配線はあくまでサーボモータを回すのに必要な電流量がRaspberryPiが供給できる電流量を上回らない時だけのものです。  
今回はマイクロサーボを使っておりラズパイ側の電源でも十分電気を供給できますが、サーボは大きくなると数A食うものもあり、データシートを見て絶対最大定格を確認することをお勧めします。  
  
  
### プログラム

import RPi.GPIO as GPIO
import time

GPIO.setmode(GPIO.BCM)

gp_out = 4
GPIO.setup(gp_out, GPIO.OUT)

servo = GPIO.PWM(gp_out, 50)
servo.start(0)

for i in range(3):
    servo.ChangeDutyCycle(2.5)
    time.sleep(0.5)

    servo.ChangeDutyCycle(7.25)
    time.sleep(0.5)

    servo.ChangeDutyCycle(12)
    time.sleep(0.5)

    servo.ChangeDutyCycle(7.25)
    time.sleep(0.5)

servo.stop()
GPIO.cleanup()
  
  
### 実行結果

上のプログラムを実行すると、このようにサーボモータが回転します。  
![f:id:pythonjacascript:20190225234425g:plain](/images/ppythonjacascript2019022520190225234425.gif "f:id:pythonjacascript:20190225234425g:plain")  
  
  
#### 解説

PWM出力のプログラムの書き方については、こちら（↓）の記事で結構書いたので、

今からはサーボモータにも関係あることについて書いていきます。

まず、サーボのPWM制御の規格は、このように図示するができます。  
![f:id:pythonjacascript:20190225235416j:plain](/images/ppythonjacascript2019022520190225235416.jpg "f:id:pythonjacascript:20190225235416j:plain")

そしてPWM制御において「**デューティ比**」という考え方があります。「デューティ比」とは、PWMパルスの中でのONの時間とOFFの時間の長さの比です。ONの時間の割合が多いほど平均的な電圧は高くなり、OFFの時間が長いと平均電圧は低くなります。

サーボモータは、この「デューティ比」を使って回転角を制御しています。

SG-90のデータシート（<http://www.ee.ic.ac.uk/pcheung/teaching/de1%5Fee/stores/sg90%5Fdatasheet.pdf>）を読むと、

1. PWMの周期は50Hzが良い。
2. パルスが1.5msの時、サーボは中央に回転
3. パルスが2msの時、サーボは中央から右回転90°の位置に回転
4. パルスが1msの時、サーボは中央から左回転90°に回転

と書かれていました。

  
これをもとに、デューティー比の数値を決めると、↑のプログラムのようになります。

PWMの出力方法については、こちらの記事をご覧ください。  
[shizenkarasuzon.hatenablog.com](https://shizenkarasuzon.hatenablog.com/entry/2019/03/04/002116)