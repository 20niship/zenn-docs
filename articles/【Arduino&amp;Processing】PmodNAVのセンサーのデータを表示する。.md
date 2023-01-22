---
title: "【Arduino &amp; Processing】PmodNAVのセンサーのデータを表示する。"
emoji: ""
type: ""
topics: []
published: false
---

前回は、PmodNAVとArduinoを接続して、センサーからもらった加速度などのデータをArduino IDEに付属の「シリアルモニタ」に表示するというところまで行いました。

前回の記事は、こちらです。  
[shizenkarasuzon.hatenablog.com](https://shizenkarasuzon.hatenablog.com/entry/2018/07/24/230713)

ちなみに、PmodNAVとは、

> 加速度、ジャイロ、方向（magnetic field）、気圧センサーが一つになっているモジュールです。

  
しかし、シリアルモニタでは、数字データが文字として表示されるだけなので見にくいという欠点があります、  
この記事では、受信したデータ（加速度、ジャイロ、センサーの傾き）をグラフ化して表示したいと思います。  
  
* [プログラム：](#プログラム)  
   * [Arduino側](#Arduino側)  
   * [Processing側](#Processing側)
* [実行方法](#実行方法)

目標はこのように表示されることです。  
![f:id:pythonjacascript:20180805161442p:plain](/images/ppythonjacascript2018080520180805161442.png "f:id:pythonjacascript:20180805161442p:plain")

このようなグラフなどを表示するGUI（Graphical User Interface）を作るために、Processingを使いました。  
  
  
### プログラム：

#### Arduino側

/\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\* \* Description: Pmod\_NAV \* All data (accelerometer, gyroscope, magnetometer) are displayed \* \* Wiring \* Module<