---
title: "【M5Stack】第7回 WiFiを使ってPC→M5Stackにデータ送信"
emoji: ""
type: ""
topics: []
published: false
---

今度は、[前回の記事](https://shizenkarasuzon.hatenablog.com/entry/2020/06/09/020243)の逆に、PCからM5Stackへデータを送ってみます。

* [プログラム（M5Stack）](#プログラムM5Stack)
* [プログラム（PC側、C++）](#プログラムPC側C)
* [プログラム（PC側、Python）](#プログラムPC側Python)
* [実行結果](#実行結果)
* [解説](#解説)

### プログラム（M5Stack）

#include <WiFi.h>
#include <WiFiUDP.h>
#include <M5Stack.h>

// arp -aでIPアドレスをすべて取得
// ipconfigで自身のIPアドレスを取得

const char ssid[] = "hogehoge"; //WifiのSSID
const char pass[] = "0123456"; //Wifiの password

WiFiUDP wifiUdp; 
const char *pc_addr = "192.168.0.0";  //PCのIPアドレスを指定
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
  int num_packet = wifiUdp.parsePacket();
  char packetBuffer[100];
  if (num_packet){
    int len = wifiUdp.read(packetBuffer, num_packet);
    if (len > 0){ packetBuffer[len] = '\0'; }
    M5.Lcd.println(packetBuffer);
  }
}
  
  
### プログラム（PC側、C++）

#include <stdio.h>
#include <iostream>
#include <string>
#include <WinSock2.h> //windows
#include <Windows.h>

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
  /* タイムアウト時間の設定 */
  tv.tv_sec = 0;
  tv.tv_usec = 1000;

  WSACleanup();
  WSAStartup(MAKEWORD(2, 0), &wsaData);   //MAKEWORD(2, 0)はwinsockのバージョン2.0ってこと
  sock = socket(AF_INET, SOCK_DGRAM, 0);  //AF_INETはIPv4、SOCK_DGRAMはUDP通信、0は？


  //bindのタイムアウト設定
  if (setsockopt(sock, SOL_SOCKET, SO_RCVTIMEO, (char*)&tv, sizeof(tv)) < 0) { perror("Error"); }


  sock = socket(AF_INET, SOCK_DGRAM, 0);
  /* 待ち受けポート番号を12345にするためにbind()を行う */
  addr.sin_family = AF_INET;
  addr.sin_port = htons(50008);//M5Stackのmy_portと一致させる
  addr.sin_addr.S_un.S_addr = inet_addr("255.255.255.255");// 送信アドレスを設定

  while(1) {
    /* AF_INET+SOCK_DGRAMなので、IPv4のUDPソケット */
    char data[] = "test";
  
    //データ送信
    sendto(sock, data, sizeof(data), 0, (struct sockaddr *)&addr, sizeof(addr));
    printf("send data!\n");
    Sleep(1000);
  }

  closesocket(sock);
  WSACleanup();

  return 0;
}
  
  
### プログラム（PC側、Python）

# -*-coding:utf-8-*-

from socket import *
import time

addr = ("255.255.255.255", 50008)  # M5StackのIPアドレスとポート番号（プログラム中のmy_port）を指定
UDPSock = socket(AF_INET, SOCK_DGRAM)
UDPSock.settimeout(0.0001)
print("setup finished")

while True:
    msg = "test"
    UDPSock.sendto(msg.encode('utf-8'), addr)
    time.sleep(2)
    print("send message!!")

Pythonってコードが短くていいよなぁ  
  
  
### 実行結果

プログラムには数か所、M5StackやPCのIPアドレスを入力するところがありますが、M5StackやPCのIPアドレスを調べる方法は[ここ](https://shizenkarasuzon.hatenablog.com/entry/2020/06/09/020243#%E6%BA%96%E5%82%99)を見てください

  
そして、プログラムを実行すると以下の写真のようになります。  
![f:id:pythonjacascript:20200609030420j:plain](/images/ppythonjacascript2020060920200609030420.jpg "f:id:pythonjacascript:20200609030420j:plain")  
（Python版）

![f:id:pythonjacascript:20200609030436j:plain](/images/ppythonjacascript2020060920200609030436.jpg "f:id:pythonjacascript:20200609030436j:plain")  
（C++版）  
PCで「test」の文字を送信すると、それをM5Stackで受信していることがわかります。  
  
  
### 解説

M5StackをWiFiにつなげるプログラムや、UD通信のセットアップ方法などは↓の記事を見てください  
[shizenkarasuzon.hatenablog.com](https://shizenkarasuzon.hatenablog.com/entry/2020/06/09/020243)  

ここではデータの受信方法についてみていきます。  
上のサンプルプログラムで言うと↓の部分です

int num_packet = wifiUdp.parsePacket();
char packetBuffer[100];
if (num_packet){
  int len = wifiUdp.read(packetBuffer, num_packet);
  if (len > 0){ packetBuffer[len] = '\0'; }
  M5.Lcd.println(packetBuffer);
}

M5Stackでは、UDP通信でデータを受け取るにはread関数を使用しますが、その前にparsePacket関数を呼ぶ必要があります。

**wifiUdp.parsePacket**関数はUDPの受信パケットがあるかどうかを判定し、そのパケットサイズをint型で返します。

そして、**wifiUdp.read**関数でそのパケットサイズの分だけデータを読み込みます

WiFiUDP.read(char *buffer, int len); 

受信データをsizeだけ読み取って、bufferに格納します。

つまり、↑のプログラムではpacketBufferに受信データが格納されています。これをprintln関数で表示しているのですが、文字列の終端を表すために「**終端文字￥0(プログラムでは '\\0')**」を加えています。