---
title: "Raspberry Pi と Arduino をUSBシリアル通信（C言語版・その1）"
emoji: "🤖"
type: "tech"
topics: []
published: false
---

今回は、Raspberry PiとArduinoのコンビです。

シリアル通信を使って、相互にデータのやり取りをしてみます。

今回の例はArduinoからRaspberryPiの向きでデータを一方向送信します。

* [ハードウェア](#ハードウェア)
* [プログラム](#プログラム)
* [実行方法](#実行方法)
* [動作結果](#動作結果)
* [解説](#解説)  
   * [ポートを開く](#ポートを開く)  
   * [データを受け取る](#データを受け取る)  
   * [データを送信する](#データを送信する)

### ハードウェア

プログラム実行時には、以下のようにRaspberryPiとArduinoをUSBで接続します。  
RaspberryPiにはUSB端子が4つありますが、どれでも構いません。  
![f:id:pythonjacascript:20190304003926j:plain](/images/ppythonjacascript2019030420190304003926.jpg "f:id:pythonjacascript:20190304003926j:plain")  

実際に繋いでみました。  
![f:id:pythonjacascript:20190304003450j:plain](/images/ppythonjacascript2019030420190304003450.jpg "f:id:pythonjacascript:20190304003450j:plain")  
  
### プログラム

以下のようなプログラムです。

**Raspberry Pi側：**

#include <stdio.h>
#include <stdint.h>
#include <wiringPi.h>
#include <wiringSerial.h>

int main(){
    /* シリアルポートオープン */
    int fd = serialOpen("/dev/ttyACM0",115200);    
    
    wiringPiSetup();
    fflush(stdout);
    
    if(fd<0){
        printf("can not open serialport");
    }

    while(1){
    /* 受信処理 */
    while(serialDataAvail(fd)){
        int get_char =  serialGetchar(fd);
        if(get_char !=  -1){
            printf("recive : %d\n",get_char);
        }else{
            printf("no data\n");    
        }
    }
    }
    return 0;
}

**Arduino側：**

void setup(){
  // シリアルポートを115200bps[ビット/秒]で初期化
  Serial.begin(115200);
}

void loop(){
  // 文字列を送信
  for(int i = 0; i<100; i++){
     Serial.write(i);
     // 30ms待機
     delay(30);
  }
}

  
### 実行方法

まず、Arduino側に↑のプログラムを書き込みます。

RaspberryPiは上のプログラムを特定の名前で保存します。（**拡張子は「.c」**です。）  
そして、ターミナルを開いて、

gcc -Wall -o serial serial.c -lwiringPi

を実行してください。勿論、「serial.c」の部分は各自で保存した.Cファイル名に書き換えてください。

そして、ArduinoをUSB接続した後で

sudo ./serial

を実行すると、上のプログラムが走り出します。  
  
### 動作結果

Raspberry PiとArduinoをUSB接続して、↑のプログラムを実行します。  
すると、下の画像のようにArduinoから送られてきた数値がRaspberryPiのウィンドウ上に表示されるはずです。

![f:id:pythonjacascript:20190309163254j:plain](/images/ppythonjacascript2019030920190309163254.jpg "f:id:pythonjacascript:20190309163254j:plain")  
  
  
### 解説

この記事はRaspberry Piがメインの記事なので、主にRaspberry piのプログラムについて解説していきます。

Arduino側については、こちらの記事をご覧ください。  
[shizenkarasuzon.hatenablog.com](https://shizenkarasuzon.hatenablog.com/entry/2018/09/10/150209)  
  
  
#### ポートを開く

特定のUSBポートを開く場合は、以下のように設定してください。

int fd = serialOpen("/dev/ttyACM0",115200);    

尚、USBポートではなくGPIO上のTxD/RxDピンで通信する場合は、以下のようになります。

int fd = serialOpen("/dev/serial0",115200);    

この場合は「pi-config」の設定等が必要になるので、また別の記事で紹介します。

  
serialOpen()関数の第一引数には「'/dev/ttyACM0'」を指定し、第二引数にはビットレートを指定します。  
ビットレートは一秒間に送るバイト数のことで、「9600」や「115200」などが一般的です。

返り値のfdはint型で、シリアルポートを通じて値を送受信するときに使用します。

#### データを受け取る

Raspberry Piでも、シリアル通信でデータを受け取ると特定のバッファにその値が保存されます。  
プログラムでできることはそのバッファを読み取ることです。

  
下のように書くと、シリアル通信で送られてきた数値を受け取ります。

Arduinoとは異なり、ASCII形式には変換しません。  
シリアル通信の生データ（0～255の8ビットの値）を返します。

//一バイト受け取る
int get_char =  serialGetchar(fd);

#### データを送信する

下のように書くと、文字列を送信することができます。  
書き方は以下の三通りがあります。

//一文字だけ送信
serialPutchar (int fd, ”a”) ;

//文字列を送信（最後の文字はNULL）
serialPuts (int fd,　"hello, World\n") ;


//文字列を送信
serialPrintf (int fd, "Hello, World\n") ;