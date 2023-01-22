---
title: "【Arduino】シリアル通信で複数のデータを送受信する　第2段！（精度向上！？）"
emoji: ""
type: ""
topics: []
published: false
---

前回の記事で、このような内容を書きました。

[shizenkarasuzon.hatenablog.com](https://shizenkarasuzon.hatenablog.com/entry/2018/08/13/133406)

この記事では、二台のArduino間で複数のデータをやり取りするプログラムをアップしましたが、こっちの方が精度が良さそうなので、再アップです。  
  
* [1.プログラムの内容](#1プログラムの内容)
* [2.実験してみる](#2実験してみる)  
   * [1.実験内容](#1実験内容)  
   * [2.ハードウェア](#2ハードウェア)  
   * [プログラム](#プログラム)  
         * [送信機用プログラム](#送信機用プログラム)  
         * [受信機用プログラム](#受信機用プログラム)

### 1.プログラムの内容

二台のArduino間で複数のbyteデータをやり取りすることができます。データは **byte data\[_data number_\]** の配列に格納され、この配列の値を読み込んだり、書き換えて送信命令を送るだけで、データの送受信が可能です。

データの送受信方法はこのようになっています。

送信するデータの種類と、データの値そのものを二回に分けて **Serial.write()** で送信しています。  
![f:id:pythonjacascript:20180921014441j:plain](/images/ppythonjacascript2018092120180921014441.jpg "f:id:pythonjacascript:20180921014441j:plain")

前回の記事では、上位バイト（黄色の部分）と下位バイト（青い部分）を見分ける方法がなかったため、万が一、下位バイトから受信してしまった場合、上位バイトを下位バイトと勘違いして、誤作動が起こる可能性がありました。ですが、この記事のプログラムは、送信されるそれぞれのデータの最上位ビットに、上位バイト（黄色）か下位バイト（青色）かを区別するための識別値を設定しました。上位バイトの場合は、最上位ビットが0になり、下位バイトの場合は、最上位ビットが1になります。

こうすることで、シリアル通信におけるデータの破損などを防ぐことができます。  
  
### 2.実験してみる

#### 1.実験内容

このプログラムは、二台のAruduino間での通信プログラムなので、最低2台のArduinoが必要です。今回は、このような実験をしてみました。

**・Arduinoを2台準備して、xBee S2C を使って2台をシリアル通信ができるようにする** 
**・Arduinoにそれぞれ、送信機用と受信機用プログラムを書き込む（受信機は、Softwarerialを使ってxBee通信）**  
SoftwareSerialについては、ここをご覧ください。  
[shizenkarasuzon.hatenablog.com](https://shizenkarasuzon.hatenablog.com/entry/2018/09/10/093648)

**・受信機用プログラムを書き込んだArduinoをPCにつなぎ、シリアルモニタを表示すると、受信したデータが表示される**  
![f:id:pythonjacascript:20180921024559j:plain](/images/ppythonjacascript2018092120180921024559.jpg "f:id:pythonjacascript:20180921024559j:plain")

  
このようにデータを送受信してみます。  
![f:id:pythonjacascript:20180921022546j:plain](/images/ppythonjacascript2018092120180921022546.jpg "f:id:pythonjacascript:20180921022546j:plain")  
緑の吹き出しで書いてある部分が、今回メイン処理として記述する部分です。基本的な仕組みは、**byte型の data という配列を作って、その中身を随時更新/同期していくことで、複数のデータの送受信を実現している、というものです。** 

**単にfor文を使って一定時間ごとに全データを送受信してもいいのですが、データの数が多くなってくると、➀データの送受信に時間がかかる、②値が変わっていない部分まで送信するのは効率が悪い、③シリアル通信が正しく行われなかったとき、すべてのデータが受信できないことになるので、データが破損するリスクが高い、などの欠点が発生して、正しくデータの送受信が行われませんでした。**

  
**そのため、すべてのデータを送受信するのではなく、data配列の中でも特定の（値の更新された）一つを送信するプログラムを書きました。詳しくはCommunication.cppを見てください。** 
**あとのdata配列を同期する関数などは、別のソースファイルにクラスの形で書いています。** 
  
#### 2.ハードウェア

このように組んでください。  
![f:id:pythonjacascript:20180910093613j:plain](/images/ppythonjacascript2018091020180910093613.jpg "f:id:pythonjacascript:20180910093613j:plain")

組み立てると、こんな感じです。

![f:id:pythonjacascript:20180921020458j:plain](/images/ppythonjacascript2018092120180921020458.jpg "f:id:pythonjacascript:20180921020458j:plain")  
写真では、ｘBeeを使って無線でシリアル通信を行っていますが、同じような動作をします。  
ｘBeeの使い方については、この記事をご覧ください。  
  
  
#### プログラム

送信機、受信機ともにプログラムは、メインループのプログラム（.ino）、シリアル通信用のクラスを実装したソースファイル（.cpp と .h）の3つで構成されています。  
![f:id:pythonjacascript:20180921020959j:plain](/images/ppythonjacascript2018092120180921020959.jpg "f:id:pythonjacascript:20180921020959j:plain")

そのうち、ここには .ino のみを載せます。残りのソースとヘッダファイルGitHubのリンクを貼っておくので、そちらからお願いします。ダウンロードしたファイルは、inoファイルと同じフォルダ内に入れてください。  
  
  
##### 送信機用プログラム

#include "Comunication.h"

Communication Com = Communication();

void setup(){
  Com.Com_setup();
}

void loop(){
  Com.RecieveManyData();
  delay(100);
  Com.data[3] = Com.data[0];
  Com.data[4] = 200;
  Com.data[5] = Com.data[2] + 151;
  Com.SendOneData(3);
  Com.SendOneData(4);
  Com.SendOneData(5); 
}

その他にCommunivcation.cpp と Communication.hをここからダウンロードしてください。  
[MissionF/Communication.cpp at master · IndyAnMD/MissionF · GitHub](https://github.com/IndyAnMD/MissionF/blob/master/Drone/version2-4/SerialTest/version-2/SerialTest/Communication.cpp)  
[MissionF/Comunication.h at master · IndyAnMD/MissionF · GitHub](https://github.com/IndyAnMD/MissionF/blob/master/Drone/version2-4/SerialTest/version-2/SerialTest/Comunication.h)

  
##### 受信機用プログラム

#include "Comunication.h"

Communication Com = Communication();

void setup() {
  Serial.begin(38400);
  Com.Com_setup();
}

void loop() {
  Com.data[0] = 10;
  Com.data[1] = 54;
  Com.data[2] = 49;
  
  Com.SendOneData(0);
  Com.SendOneData(1);
  Com.SendOneData(2);
  delay(500);

  Com.RecieveManyData();

  int a = 0;
    Serial.print(Com.data[3]);
    Serial.print(", ");
    Serial.print(Com.data[4]);
    Serial.print(", ");
    Serial.println(Com.data[5]);
}

その他にCommunivcation.cpp と Communication.hをここからダウンロードしてください。  
[MissionF/Communication.cpp at master · IndyAnMD/MissionF · GitHub](https://github.com/IndyAnMD/MissionF/blob/master/Drone/version2-4/SerialTest/version-2/SerialTest/controller/Communication.cpp)  
[MissionF/Comunication.h at master · IndyAnMD/MissionF · GitHub](https://github.com/IndyAnMD/MissionF/blob/master/Drone/version2-4/SerialTest/version-2/SerialTest/controller/Comunication.h)