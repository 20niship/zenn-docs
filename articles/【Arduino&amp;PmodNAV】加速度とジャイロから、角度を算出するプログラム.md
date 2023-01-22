---
title: "【Arduino &amp; PmodNAV】加速度とジャイロから、角度を算出するプログラム"
emoji: ""
type: ""
topics: []
published: false
---

前回の記事で、PmodNAVというセンサーを使用して、加速度やジャイロを測定するプログラムを公開しました。  
[shizenkarasuzon.hatenablog.com](https://shizenkarasuzon.hatenablog.com/entry/2018/07/24/230713)

  
そこで、今回はそれらの加速度やジャイロのデータをもとにして、角度（センサーの傾き）を算出するプログラムを書きます。

  
使用しているセンサーはPmodNAVです。

![f:id:pythonjacascript:20180724230134p:plain](/images/ppythonjacascript2018072420180724230134.png "f:id:pythonjacascript:20180724230134p:plain")

この記事で使用する加速度＆ジャイロセンサ：PmodNAV

[Pmod NAV \[Reference.Digilentinc\]](https://reference.digilentinc.com/reference/pmod/pmodnav/start)
  
  
では、スタート。  
  
* [1.目標](#1目標)  
   * [目標➀センサーの傾きを算出する](#目標センサーの傾きを算出する)  
   * [目標②ピッチとロール軸まわりの傾きをシリアルモニタに出力する。](#目標ピッチとロール軸まわりの傾きをシリアルモニタに出力する)
* [2.ハードウェア](#2ハードウェア)
* [3.ソフトウェア](#3ソフトウェア)
* [参考文献](#参考文献)

### 1.目標

##### 目標➀センサーの傾きを算出する

そのためには、前回の記事の容量で取得した加速度のデータ「ax, ay, az」と、ジャイロセンサのデータ「gx, gy, gz」を使います。

一口に「センサーの傾き」と言っても、三次元での傾きのことなので、値は３つあります。**「ピッチ(pitch)」、「ロール (roll)」、「ヨー (yaw)」**です。

この記事では、このうち、「ピッチ(pitch)」、「ロール (roll)」の傾きを **Kalman filter** を用いて取得します。  
  
##### 目標②ピッチとロール軸まわりの傾きをシリアルモニタに出力する。

目標➀で取得した傾きをシリアルモニタに数値の形でリアルタイム表示します。  
  
  
### 2.ハードウェア

前回の記事と同じです。次のピンをつないでください。

Arduino UNO の場合：  
PmodNAV<---> Arduino  
pin 6 3.3V  
pin 5 GND  
pin 4 A5  
pin 2 A4

Arduino Due の場合  
PmodNAV<---> Arduino  
pin 6 3.3V  
pin 5 GND  
pin 4 21（SCLピン）  
pin 2 20（SDAピン)  
  
  
### 3.ソフトウェア

この記事では、「Kalman filter」と呼ばれるフィルターを用いて傾きを算出しています。なので、「Kalman filter」のライブラリーをダウンロードして、Arduino IDEのライブラリに追加する必要があります。

ライブラリ（Kalman filter) のダウンロード先（GitHub)：  
<https://github.com/TKJElectronics/KalmanFilter>  
このサイトからZIPでダウンロードして、Arduino IDE で、「ライブラリをインクルード」してください。

  
では、プログラムです。

機能：  
  
  
/\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\* \* Description: Pmod\_NAV \* All data (accelerometer, gyroscope, magnetometer) are displayed \* \* Wiring \* Module<