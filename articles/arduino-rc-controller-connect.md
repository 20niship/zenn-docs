---
title: "【Arduino】 Arduino とラジコンのコントローラー（プロポ）を接続する"
emoji: "🤖"
type: "tech"
topics: []
published: false
---

この記事では、Arduino とラジコンコントローラー（プロポともいう）を接続する方法を紹介します。

* [1.この記事の内容](#1この記事の内容)
* [2.配線](#2配線)
* [3.スケッチ](#3スケッチ)
* [4.実行](#4実行)  
   * [STEP1\. Arduinoを電源ON](#STEP1-Arduinoを電源ON)  
   * [STEP2\. プロポ（送信機）を電源ON](#STEP2-プロポ送信機を電源ON)  
   * [STEP3\. シリアルモニタで確認](#STEP3-シリアルモニタで確認)
* [5.参考文献](#5参考文献)

### 1.この記事の内容

この記事で使っているプロポはこちらです。  
**10J （10ch-2.4GHz T-FHSS AIRモデル）**  
![f:id:pythonjacascript:20181019050412p:plain:h300](/images/ppythonjacascript2018101920181019050412.png "f:id:pythonjacascript:20181019050412p:plain:h300")  
（引用元サイト：[双葉電子工業 ホビーサイト ホームページ | プロポセット(空用) | 10J](https://www.rc.futaba.co.jp/propo/air/10j.html)）

このプロポとArduinoを無線通信を行い、ジョイスティックのデータを取得します。

プロポのレシーバーには **R3008SB** を使用します。  
![f:id:pythonjacascript:20181019050718j:plain](/images/ppythonjacascript2018101920181019050718.jpg "f:id:pythonjacascript:20181019050718j:plain")  
（説明書：<https://rc.futaba.co.jp/dl%5Fmanual/data/reciever/r3008sb.pdf>）
  
  
全体像としてはこうなります。  
![f:id:pythonjacascript:20181019050950j:plain](/images/ppythonjacascript2018101920181019050950.jpg "f:id:pythonjacascript:20181019050950j:plain")
  
  
### 2.配線

今からは、1チャンネル（右側のジョイスティックの左右の傾き）を受信してみます。そのためには、次のように配線してください。  
![f:id:pythonjacascript:20181019051444j:plain](/images/ppythonjacascript2018101920181019051444.jpg "f:id:pythonjacascript:20181019051444j:plain")

![f:id:pythonjacascript:20181019051619j:plain](/images/ppythonjacascript2018101920181019051619.jpg "f:id:pythonjacascript:20181019051619j:plain")![f:id:pythonjacascript:20181019051626j:plain](/images/ppythonjacascript2018101920181019051626.jpg "f:id:pythonjacascript:20181019051626j:plain")

  
プロポ受信機（R3008SB）の電源は、「7/8」と書かれたピンから供給します。また、「1」～「6」には、それぞれ次のようなジョイスティックの値が送信されています。

| チャンネル名 | 送信内容     |
| ------ | -------- |
| 1ch    | 右スティック左右 |
| 2ch    | 左スティック上下 |
| 3ch    | 右スティック上下 |
| 4ch    | 左スティック左右 |

  
(5、6チャンネルはよくわかりませんでした...進展があれば追記します）  
  
  
### 3.スケッチ

以下のコードをコピペしてください。1チャンネルの値を受信して、シリアルモニタに表示するプログラムです。

#define RECV_PULSE_LENGTH_MIN  1000
#define RECV_PULSE_LENGTH_MAX 2000

#define CH1_PIN A0

int CH1_value = 0;

void setup() {
  pinMode(CH1_PIN, INPUT);
  Serial.begin(38400); 
}

void loop() {
    CH1_value = pulseIn(CH1_PIN,HIGH);
    
    Serial.print("CH1 value = ");
    Serial.print(CH1_value);
    int Ch1_percent = map(CH1_value, RECV_PULSE_LENGTH_MIN, RECV_PULSE_LENGTH_MAX, 0, 100);
    Serial.print(" ( ");
    Serial.print(Ch1_percent);
    Serial.println(" %)"); 
    delay(100); 
}

### 4.実行

Arduinoに上のスケッチを書き込んだら、早速実験してみてください。

#### STEP1\. Arduinoを電源ON

上のプログラムの場合、ArduinoはPCとシリアル通信をするので、ArduinoはPCと接続して、電源はUSBから供給してください。  
この時、受信機（R3008SB）のLEDが**赤色**に点灯していればOKです。  
  
#### STEP2\. プロポ（送信機）を電源ON

中央にある横長のスイッチがプロポの電源スイッチです。  
プロポの電源をONにしてから2～3秒後に、受信機のLEDが赤から**緑の点灯**に変わったら無線通信が行われています。  
  
#### STEP3\. シリアルモニタで確認

シリアルモニタを開いて、右側のジョイスティックを左右に傾けてみてください。  
その傾きに応じてシリアルモニタに表示される数字がリアルタイムで変化するはずです。  
![f:id:pythonjacascript:20181019053002j:plain](/images/ppythonjacascript2018101920181019053002.jpg "f:id:pythonjacascript:20181019053002j:plain")

### 5.参考文献

[Arduinoでラジコン受信機を読み取る Techメカ工房・工作室](http://robokosaku.blog115.fc2.com/blog-entry-1093.html)

<https://rc.futaba.co.jp/dl%5Fmanual/data/reciever/r3008sb.pdf>

[飛行船の制御 その1 – プロポからの信号をArduinoを使って読み取ってみる。 | icchi's blog](https://blog.icchi.me/transmitter-arduino-1/)