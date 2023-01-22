---
title: "【M5Stack】第6回 WiFiを使ってM5Stack→PCにデータ送信"
emoji: ""
type: ""
topics: []
published: false
---

* [すること](#すること)
* [プログラム（M5Stack）](#プログラムM5Stack)
* [プログラム（PC側、C++）](#プログラムPC側C)
* [プログラム（PC側、Python）](#プログラムPC側Python)
* [準備](#準備)  
   * [自身のPCのIPアドレスを知る](#自身のPCのIPアドレスを知る)  
   * [M5StackのIPアドレスを知る](#M5StackのIPアドレスを知る)
* [実行結果](#実行結果)
* [解説](#解説)  
   * [WiFiに接続](#WiFiに接続)  
   * [UDP通信の設定](#UDP通信の設定)  
   * [データ送信](#データ送信)

### すること

タイトルの通りです。M5StackはWiFiモジュールが内蔵されており、WiFiに接続することで、PCとデータをやり取りすることができます。  
UDP通信についてはほかの記事を見たりググったりしてください。  
  
  
### プログラム（M5Stack）

#include <WiFi.h>
#include <WiFiUDP.h>
#include <M5Stack.h>

// arp -aでIPアドレスをすべて取得
// ipconfigで自身のIPアドレスを取得

const char ssid[] = "hogehoge"; //WiFIのSSIDを入力
const char pass[] = "01234567"; // WiFiのパスワードを入力

WiFiUDP wifiUdp; 
const char *pc_addr = "192.168.0.0";  //"192.168.0.6";
const int pc_port = 50007; //送信先のポート
const int my_port = 50008;  //自身のポート


void setup() {
  M5.begin();
  M5.Power.begin();
  M5.Lcd.setTextSize(2);

  WiFi.begin(ssid, pass);
  while( WiFi.status() != WL_CONNECTED) {
    delay(500); 
    M5.Lcd.print("."); 
  }  
  M5.Lcd.println("WiFi connected");
  M5.Lcd.print("IP address = ");
  M5.Lcd.println(WiFi.localIP());
  
  wifiUdp.begin(my_port);
}

void loop(){
  for(int i=0;i<100;i++){
  // データ送信
  wifiUdp.beginPacket(pc_addr, pc_port);
  wifiUdp.write('t');
  wifiUdp.write('e');
  wifiUdp.write('s');
  wifiUdp.write('t');
  wifiUdp.endPacket();
  M5.Lcd.print(".");


  delay(2000); //2秒待つ
  }

  //終了コマンド送信
  wifiUdp.beginPacket(pc_addr, pc_port);
  wifiUdp.write('&');
  wifiUdp.endPacket();
  delay(2000);
}

### プログラム（PC側、C++）

#include <stdio.h>
#include <iostream>
#include <string>
#include <WinSock2.h> //windows
// Windows.hをインクルードしたい場合は、Windows.ｈよりもWinsock2.hを先にインクルードすること
// なんならWindows.ｈだけでも、Winsock2.hだけでも動く。

#pragma comment(lib, "ws2_32.lib")

// inet_addr()関数で警告が出る場合は以下で警告を無効化する。
#pragma warning(disable:4996) 


int sock;
struct sockaddr_in addr;
WSAData wsaData;
struct timeval tv;


int main(int argc, char *argv[]){
  WSACleanup();
  WSAStartup(MAKEWORD(2, 0), &wsaData);   //MAKEWORD(2, 0)はwinsockのバージョン2.0ってこと
  sock = socket(AF_INET, SOCK_DGRAM, 0);  //AF_INETはIPv4、SOCK_DGRAMはUDP通信、0は？


  //タイムアウト時間の設定 
  tv.tv_sec = 0;　//秒
  tv.tv_usec = 1000; //マイクロセカンド
  if (setsockopt(sock, SOL_SOCKET, SO_RCVTIMEO, (char*)&tv, sizeof(tv)) < 0) { perror("Error"); }


  /* 待ち受けポート番号を12345にするためにbind()を行う */
  addr.sin_family = AF_INET;
  addr.sin_port = htons(50007);//M5Stackのプロ御グラムのポート番号と一致させる
  addr.sin_addr.s_addr = INADDR_ANY; //全てのアドレスからの入力を受け取る
  bind(sock, (struct sockaddr *)&addr, sizeof(addr));


  /* ノンブロッキングに設定する（recv関数がデータ受信を待たずにすぐにreturnする）
  u_long val = 1;
  ioctlsocket(sock, FIONBIO, &val);*/


  while(1) {
    char data[5];//受信データ格納

    int addrlen = sizeof(addr);
    memset(data, 0, sizeof(data));

    recv(sock, data, sizeof(data), 0);//データ受信
    //recvfrom関数を使うことでデータ送信元のアドレスを知ることもできる
    //recvfrom(sock, data, sizeof(data), 0, (struct sockaddr *)&addr, &addrlen);

    //if (ntohs(addr.sin_addr.S_un.S_addr) != 0) {
      printf("recv data: %s, address=%d\n", data, ntohs(addr.sin_addr.S_un.S_addr));
      if (data[0] == '&') { break; }
    //}


    ////受信データをuint8_t型としてゲットするには、
    //  char data_char = 0;
    //  recv(sock, &data_char, 1, 0);
    //  uint8_t data_uint8 = (uint8_t)data_char; // charがあ他は-127-127、uint8_tは非負整数の0～127なのでキャスト
  }


  closesocket(sock);
  WSACleanup();

  return 0;
}
  
  
### プログラム（PC側、Python）

# -*-coding:utf-8-*-

import sys
from socket import *
import time

"""
IPアドレスの確認方法
コマンドプロンプトで「ipconfig」と入力し
と入力し、
［IPv4 アドレス］または［IPアドレス］の値が、使用しているパソコンのIPアドレスです。
"""

print("start network")
addr = ("", 50007)  # 192.168.0.9

print("network setup started")
UDPSock = socket(AF_INET, SOCK_DGRAM)
UDPSock.settimeout(0.0001)
print("Connected !!")
print("Network info -->" + str(addr))
UDPSock.bind(addr) 

print("setup finished")

while True:
    try:
        (data, addr) = UDPSock.recvfrom(1024)
    except timeout:
        continue

    if addr !=0:
        str_data = data.decode('utf-8')
        print(f"get message from {addr} --> {str_data}")

print("end!")
  
  
### 準備

上のプログラムを実行する前に、M5StackとPCが同じWiFiに接続されていることを確認してください。  
そして、それぞれのUDP通信においてデータを送信するには**送信先のIPアドレスを知っておく必要があります。**（当たり前ですが）

ということで、PCのIPアドレスを知る方法（Windows版）です

#### 自身のPCのIPアドレスを知る

コマンドプロンプトを開いて、「**ipconfig**」と入力してください  
![f:id:pythonjacascript:20200609025630j:plain](/images/ppythonjacascript2020060920200609025630.jpg "f:id:pythonjacascript:20200609025630j:plain")  
「IPv4アドレス」の部分がそのデバイス自身のIPアドレスです。  
因みに、「**192.168.**○○.○○」というのは**プライベートIPアドレス、ローカルIPアドレス**を表し、自宅内や社内などのローカルなネットワークの中だけのアドレスです。  
  
  
#### M5StackのIPアドレスを知る

反対に、M5Stackのアドレスを知る方法はあるのでしょうか。  
上のプログラムではM5StackのLCD上に自身のIPアドレスを表示しているのでそれを読み取るというのも手ですが、Windows PCにはネットワークに接続されている機器のIPアドレス一覧を表示するコマンドがあります。それが「**arp -a**」です。  
![f:id:pythonjacascript:20200609030021j:plain](/images/ppythonjacascript2020060920200609030021.jpg "f:id:pythonjacascript:20200609030021j:plain")  
  
こんな感じで表示された色々なデバイスのうち、どれかがM5Stackに割り当てられたIPアドレスになっています。  
  
### 実行結果

下の画像の様に「test」の4文字をM5StackからPCに転送しています。  
PC側はC++を実行してもPythonを実行してもどちらでもよいのですが、どちらでも動作することを確認しました。

![f:id:pythonjacascript:20200609020024j:plain](/images/ppythonjacascript2020060920200609020024.jpg "f:id:pythonjacascript:20200609020024j:plain")  
(C++版)

![f:id:pythonjacascript:20200609020047j:plain](/images/ppythonjacascript2020060920200609020047.jpg "f:id:pythonjacascript:20200609020047j:plain")  
（Python版）  
  
  
### 解説

#### WiFiに接続

M5StackがWiFiに接続するには以下のようなプログラムが必要です。

const char ssid[] = "hogehoge"; // SSID
const char pass[] = "hogahoga"; //password

WiFi.begin(ssid, pass);
while( WiFi.status() != WL_CONNECTED) {
  delay(500); 
  M5.Lcd.print("."); 
}  

//WiFiの情報表示
M5.Lcd.println("WiFi connected");
M5.Lcd.print("IP address = ");
M5.Lcd.println(WiFi.localIP());

まず、**WiFi.begin**関数でWiFiに接続を試みます。

接続がうまくいくと**WiFi.status()関数**の返り値は **WL\_CONNECTED**となりますが、接続に失敗した場合以下のような値が返ってきます。

WiFi.status()関数の返り値：

| 値                    | 説明                |
| -------------------- | ----------------- |
| WL\_CONNECTED        | 正常にWiFiに接続された状態   |
| WL\_NO\_SHIELD       | WiFiのシールドが無い      |
| WL\_NO\_SSID\_AVAIL  | SSIDが有効でない        |
| WL\_CONNECT\_FAILED  | すべての試みにおいて接続に失敗した |
| WL\_DISCONNECTED     | WiFiの接続が切れた       |
| WL\_CONNECTION\_LOST | WiFiのコネクションがロストした |
  
  
#### UDP通信の設定

WiFiUDP.begin(int port)

WiFiを使ったUDP通信の初期化を行います。引数portには自分自身のポートを指定することが多いです。(別のデバイスでこのM5Stackに送信するにはここで指定したポート番号を使用します)  
接続成功すると1、失敗すると0を返します。  
  
  
#### データ送信

下の例が文字列を送信するプログラムです。

wifiUdp.beginPacket(pc_addr, pc_port);
wifiUdp.write('t');
wifiUdp.write('e');
wifiUdp.write('s');
wifiUdp.write('t');
wifiUdp.endPacket();

見ればわかるように、データを送信する前と後にbeginPacket関数とendPacket関数を呼びます。

  
データの送信はwifiUdp.write関数で行います。

WiFiUDP.write(byte);
WiFiUDP.write(buffer, size); 

なので、↑のプログラムは

wifiUdp.beginPacket(pc_addr, pc_port);
uint8_t data_test[] = "test";
wifiUdp.write(data_test, sizeof(data_test));
wifiUdp.endPacket();

と同じことを意味します。