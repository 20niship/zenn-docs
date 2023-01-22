---
title: "【Arduinoドローン自作】NO.5 ソフトウェア製作編"
emoji: ""
type: ""
topics: []
published: false
---

![f:id:pythonjacascript:20181123173858j:plain](/images/ppythonjacascript2018112320181123173858.jpg "f:id:pythonjacascript:20181123173858j:plain")

**...ん？！**

なんか見覚えのあr....

**はい。**

  
ということで、Arduinoを使ったドローン自作連載、第五回です。

この記事では、ドローンに搭載したArduinoのプログラムの説明を行います。

  
本研究において、ドローンを飛行させるために、主に3つの部分においてプログラミングを行っている。1つ目は、ドローン本体の制御機能、2つは、リモコン側の制御指令の送信機能、3つ目はパソコンのGUIである。この章では、それぞれのプログラミングについて説明を行います。

* [機体側のプログラム](#機体側のプログラム)  
   * [姿勢角算出](#姿勢角算出)  
   * [モーター出力](#モーター出力)  
   * [バッテリー電圧取得](#バッテリー電圧取得)
* [リモコン側のプログラム](#リモコン側のプログラム)

### 機体側のプログラム

![f:id:pythonjacascript:20181123172851j:plain](/images/ppythonjacascript2018112320181123172851.jpg "f:id:pythonjacascript:20181123172851j:plain")  
本体プログラムの一番大きな役割は、もちろん、ドローンの制御です。リモコンから送られてきた操縦指令をもとに、4つのプロペラの回転速度をリアルタイムで制御します。  
そのためには、高速にループを行う制御系が必要となります。

このドローンを制御するにはPID制御というものを用いているのですが、それについては後日別の記事で説明します。  
簡単に言うと、センサーから機体の姿勢角を算出して、その値と目標値（水平状態）の誤差をもとに制御する方法です。

#### 姿勢角算出

ドローンの姿勢角を取得するために、PmodNAVというセンサーを搭載しています。

PmodNAVから機体の加速度と各速度のデータを取得して、姿勢角を算出を算出します。

詳しいプログラムは、こちらを参照ください。  
[shizenkarasuzon.hatenablog.com](https://shizenkarasuzon.hatenablog.com/entry/2018/07/24/230713)

[shizenkarasuzon.hatenablog.com](https://shizenkarasuzon.hatenablog.com/entry/2018/08/21/220609)

算出方法は、以下の数式に基づいています。  
ピッチ角：  
![f:id:pythonjacascript:20181123180343j:plain](/images/ppythonjacascript2018112320181123180343.jpg "f:id:pythonjacascript:20181123180343j:plain")  
ロール角：  
![f:id:pythonjacascript:20181123180346j:plain](/images/ppythonjacascript2018112320181123180346.jpg "f:id:pythonjacascript:20181123180346j:plain")  
ヨー角：  
![f:id:pythonjacascript:20181123180349j:plain](/images/ppythonjacascript2018112320181123180349.jpg "f:id:pythonjacascript:20181123180349j:plain")

加速度から姿勢角が求められるように変更した。式中のax, ay, azはそれぞれｘ、ｙ、ｚ軸のドローンの角速度を表しています。また、Gx,Gy、Gzはそれぞれの軸の磁束密度を表しています。  
（参考文献：[ロボティクスにおける地磁気センサの基礎知識 - MyEnigma](https://myenigma.hatenablog.com/entry/2016/04/10/211919)）  
  
#### モーター出力

![f:id:pythonjacascript:20181123175346j:plain](/images/ppythonjacascript2018112320181123175346.jpg "f:id:pythonjacascript:20181123175346j:plain")  
ArduinoはESCに**PWM（Pulse Width Modulation ）信号**を入力することで，モーターの回転速度を調整しています。  
PWM信号とはパルス幅（入力がONになっている時間、上の図の赤い四角の部分）を変えることで信号を伝達する方式です。このドローンは、信号のON時間を**1000μs～2000μs**に変化させることによってモーターの回転数を変化させることができます。 

ArduinoではPWMを出力する関数

analogWrite(pin, value);

という関数が存在しますが、1000μs～2000μsの間でマイクロ秒単位で出力を調整したいので、

servo.writeMicroseconds(uS)

という関数を使用しました。

詳しいプログラムは、この記事を参考にしてください。  
[shizenkarasuzon.hatenablog.com](https://shizenkarasuzon.hatenablog.com/entry/2018/08/22/015912)

#### バッテリー電圧取得

二次的な機能として、バッテリーの電圧を定期的に監視して、バッテリーが一定電圧以下になると、飛行を中断し下降動作に移るようなプログラムもあります。

普通に

analogRead(pin);

関数を使用して、バッテリーの電圧を0～1023の値に変換し、その値をもとに判断しています。

なお、バッテリーの電圧はそのままでは約12Vなので、抵抗を使って降圧しています。  
（[抵抗によるバッテリーの分圧](https://shizenkarasuzon.hatenablog.com/entry/2018/11/23/161742#%E3%83%90%E3%83%83%E3%83%86%E3%83%AA%E3%83%BC%E9%9B%BB%E5%9C%A7%E7%9B%A3%E8%A6%96%E6%A9%9F%E8%83%BD)）  
  
  
### リモコン側のプログラム

![f:id:pythonjacascript:20181123172856j:plain](/images/ppythonjacascript2018112320181123172856.jpg "f:id:pythonjacascript:20181123172856j:plain")  
リモコンの役割は、ジョイスティックの傾きをもとに、ドローン本体にスロットル、前後、左右、ヨー軸回転速度の4つの操縦データを送信することである。また、ドローンから受信した姿勢角のデータを受信して、PCのディスプレイ上に表示させることもできます。

コントローラは、機体と通信するためのxBeeと、PCの2つのデバイスとシリアル通信を行っているため、SoftwareSerialというライブラリを導入しました。  
[shizenkarasuzon.hatenablog.com](https://shizenkarasuzon.hatenablog.com/entry/2018/09/10/093648)
  
  
**次回：**  
[shizenkarasuzon.hatenablog.com](https://shizenkarasuzon.hatenablog.com/entry/2018/11/23/205051)