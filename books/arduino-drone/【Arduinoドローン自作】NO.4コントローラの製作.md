---
title: "【Arduinoドローン自作】NO.4 コントローラの製作"
emoji: ""
type: ""
topics: []
published: false
---

ドローン自作連載第4回です。  
ドローンの**コントローラ**を自作します。**プロポ**とも呼ばれています。  
  
### コントローラの役割

ドローンのコントローラーは、専用のものがいくつも市販されています。  
![f:id:pythonjacascript:20181019050950j:plain](/images/ppythonjacascript2018101920181019050950.jpg "f:id:pythonjacascript:20181019050950j:plain")

そして、それらを使ってドローン側のArduinoを制御することもできます。  
[shizenkarasuzon.hatenablog.com](https://shizenkarasuzon.hatenablog.com/entry/2018/10/19/053742)

  
しかし、今回はあえてコントローラーも製作しました  
![f:id:pythonjacascript:20181123162326j:plain](/images/ppythonjacascript2018112320181123162326.jpg "f:id:pythonjacascript:20181123162326j:plain")

コントローラーの主な役割は、左右2つのジョイスティックの傾きをもとに、ドローンに操縦指令を送信することです。ジョイスティックの左右、上下野かは向きはそれぞれ特定の役割を持っており、リアルタイムで制御することができます。

  
![f:id:pythonjacascript:20181123153601j:plain](/images/ppythonjacascript2018112320181123153601.jpg "f:id:pythonjacascript:20181123153601j:plain")  
コントローラの主な動作内容は、

1. ジョイスティックの傾きを読み取る
2. 傾きを0～255の値に変換
3. その値をｘBeeを通じてドローン本体に送信

というものです。

もう一つ、二次的な機能として、ドローンから受け取ったドローンの現在状況（姿勢角、どこかに異常がないか、etc.）をPCに送信して画面に表示させる、というものもあります。  
  
### コントローラの製作

以下のようにコントローラを製作しました。

#### ジョイスティックからデータ取得

まず、ジョイスティックの傾きを読み取る方法です。  
[shizenkarasuzon.hatenablog.com](https://shizenkarasuzon.hatenablog.com/entry/2018/07/24/225532)  
このページを参考にしてください。

簡単に言うと、Arduinoの関数、

analogRead(pin); //ピンの入力電圧をアナログでゲットする関数

を使用しました。  
  
#### ドローン本体との通信

ドローン本体とコントローラは 「**xBee S2C** 」という無線通信のデバイスを使用しました。  
![f:id:pythonjacascript:20180921020458j:plain:h300](/images/ppythonjacascript2018092120180921020458.jpg "f:id:pythonjacascript:20180921020458j:plain:h300")  
（↑通信テスト中）  
これを使うと、通常のシリアル通信を行うように無線通信ができます。

だがしかし！

ただ単に

byte data = ○○;
Serial.write(data);

と送信するだけだと、ドローンは正常に動きません。

なぜなら、送信しなければならない**データの数が4個**（左右ジョイスティックの値で、ジョイスティック一つにつき左右の傾きと前後の傾きの2つ、つまり合計4個）あるからです！

そして、それらを一つ一つ区別してデータ送受信を行わなければなりません。

そこで、下のような独自の送受信アルゴリズムを使用しました。  
[shizenkarasuzon.hatenablog.com](https://shizenkarasuzon.hatenablog.com/entry/2018/09/21/024721)
  
  
#### PCとの通信

今回製作したドローンは、状態表示機能がついており、ドローンの姿勢角や速度などの飛行情報をリアルタイムでPCのディスプレイ上で確認できるようにしました。

そのために、以下のようなアルゴリズムを追加つました。

1. ドローンがコントローラに飛行情報を送信
2. コントローラがその情報を受信して、PCに送信
3. PCにおいて、自作GUIがその情報を受信してディスプレイに表示

ここでも、1つ問題があります。  
Arduinoはシリアル通信用のピンが1セット（0ピンと1ピンのこと、RXとTXと書かれている）しか用意されていません。そして、そのピンたちは、PCとシリアル接続するUSBとも電気的につながっているのです。  
そのため、もしPCとUSB接続してしまうと、ドローンとの通信に使用するxBeeとのシリアル通信に使えるピンが無くなってしまうのです。（本来、xBeeとの通信は0ピンと1ピンで行う予定だった）

このアルゴリズムを実装するためにArduinoに必要な技術が「**Software Serial**」と呼ばれるものです。

Software Serialを使うと、通常のI/Oピンを疑似的にシリアル通信用のピンとして使用できます。  
[shizenkarasuzon.hatenablog.com](https://shizenkarasuzon.hatenablog.com/entry/2018/09/10/093648)

この機能を利用して2,3ピンをxBeeとのシリアル通信用のピンに設定し、無事プログラムを動作させることができました。

  
ちなみに、コントローラ裏面の様子（配線途中）です。  
![f:id:pythonjacascript:20181223215659j:plain](/images/ppythonjacascript2018122320181223215659.jpg "f:id:pythonjacascript:20181223215659j:plain")