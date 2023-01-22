---
title: "【C++】UDP通信を実装する（Windows/Linux環境）"
emoji: ""
type: ""
topics: []
published: false
---

* [UDP通信について](#UDP通信について)
* [この記事ですること](#この記事ですること)
* [プログラム（送信側）](#プログラム送信側)
* [プログラム（受信側）](#プログラム受信側)
* [実行結果](#実行結果)
* [解説（送信側）](#解説送信側)  
   * [初期化](#初期化)  
         * [【補足】ポート番号について](#補足ポート番号について)  
   * [データ送信](#データ送信)  
   * [タイムアウト時間設定](#タイムアウト時間設定)
* [解説（受信側）](#解説受信側)  
   * [初期化](#初期化-1)  
   * [データ受信](#データ受信)  
         * [recv関数](#recv関数)  
         * [recvfrom関数](#recvfrom関数)  
   * [ノンブロッキング処理（recv関数の受信待ちをしない）](#ノンブロッキング処理recv関数の受信待ちをしない)

### UDP通信について

インターネットの通信は階層化されたプロトコル（通信する際の約束事）によって成り立っています。  
例えば、Webサーバーとクライアント（サイトを見るブラウザ）の通信について考えてみます。

この時、ブラウザとサーバーは**HTTP**というプロトコルで通信を行っています。  
例えば、指定のURLのページを見たい時には、ブラウザから「**GET /index.html HTTP/1.1 Host: localhost:8080**」のような命令がサーバーに送られます。そして、サーバーがその要求を解釈して「HTTP/1.1 200 OK .......」のようにデータを返す、という感じです。

そして、このようなメッセージのやり取りを行うためのプロトコルも必要になります。それが**TCP**や**UDP**というものです。  
TCPとUDPの違いは、

| 名称  | 相違点                                               | 使用例         |
| --- | ------------------------------------------------- | ----------- |
| TCP | 受信したデータが破損したかどうかチェックして、破損していた場合は再送要求をする           | 通常のウェブサイトなど |
| UDP | 受信データが破損しているかどうかチェックしない。リアルタイム性が要求されるときに採用することが多い | ライブ映像配信など   |

というものです。

で、今回はUDP通信を実装してみます。

UDP通信の送受信までの一連の流れは以下のようになっています。  
![f:id:pythonjacascript:20200625140234j:plain](/images/ppythonjacascript2020062520200625140234.jpg "f:id:pythonjacascript:20200625140234j:plain")  

### この記事ですること

上の図ではクライアントとサーバーが相互にデータをやり取りしていますが、この記事のプログラムでは一方向の送信にとどめます。  
  
### プログラム（送信側）

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


int main(int argc, char *argv[]){
  int sock;
  struct sockaddr_in addr;
  WSAData wsaData;
  struct timeval tv;

  WSACleanup();
  WSAStartup(MAKEWORD(2, 0), &wsaData);   //MAKEWORD(2, 0)はwinsockのバージョン2.0ってこと
  sock = socket(AF_INET, SOCK_DGRAM, 0);  //AF_INETはIPv4、SOCK_DGRAMはUDP通信、0は？

  //タイムアウト設定
  //tv.tv_sec = 0;
  //tv.tv_usec = 1000;
  //if (setsockopt(sock, SOL_SOCKET, SO_RCVTIMEO, (char*)&tv, sizeof(tv)) < 0) { perror("Error"); }

  sock = socket(AF_INET, SOCK_DGRAM, 0);
  
  addr.sin_family = AF_INET;
  addr.sin_port = htons(50008);// 待ち受けポート番号を50008にする
  addr.sin_addr.S_un.S_addr = inet_addr("127.0.0.1");// 送信アドレスを設定

  for (int i = 0; i < 100; i++) {
    /* AF_INET+SOCK_DGRAMなので、IPv4のUDPソケット */

    char data[] = "test";
  
    //データ送信
    sendto(sock, data, sizeof(data), 0, (struct sockaddr *)&addr, sizeof(addr));//addrに文字列送信
    printf("send data!\n");
    Sleep(1000); //1000ms待つ（win32 API)
  }

  closesocket(sock);
  WSACleanup();

  return 0;
}

### プログラム（受信側）

#include <stdio.h>
#include <string.h>
#include <WinSock2.h> //windows
#include <Windows.h>
#include <windowsx.h>

#pragma comment(lib, "ws2_32.lib")
#pragma warning(disable:4996) // inet_addr()関数で警告が出る場合は以下で警告を無効化する。


int main() {
  WSAData wsaData;
  WSAStartup(MAKEWORD(2, 0), &wsaData);   //MAKEWORD(2, 0)はwinsockのバージョン2.0ってこと
  int sock = socket(AF_INET, SOCK_DGRAM, 0);  //AF_INETはIPv4、SOCK_DGRAMはUDP通信、0は？

  sock = socket(AF_INET, SOCK_DGRAM, 0);  //AF_INETはIPv4、SOCK_DGRAMはUDP通信、0は？

  // アドレス等格納
  struct sockaddr_in addr;
  addr.sin_family = AF_INET;  //IPv4
  addr.sin_port = htons(50008);   //通信ポート番号設定
  addr.sin_addr.S_un.S_addr = INADDR_ANY; // INADDR_ANYはすべてのアドレスからのパケットを受信する
  bind(sock, (struct sockaddr *)&addr, sizeof(addr));

  // ここで、ノンブロッキングに設定しています。
  // u_long val = 1;
  // ioctlsocket(sock, FIONBIO, &val);

  // タイムアウト時間の設定
  //struct timeval tv;
  //tv.tv_sec = 0;
  //tv.tv_usec = 10;
  //if (setsockopt(sock, SOL_SOCKET, SO_RCVTIMEO, (char*)&tv, sizeof(tv)) < 0) {
  //  perror("Error");
  //}

  char buf[100];
  
  for (int i = 0; i < 100; i++) {
    /*std::string rdata = udp0.recieve();
    printf("recv:%s\n", rdata.c_str());*/
    memset(buf, 0, 100);
    recv(sock, buf, 100, 0);
    printf("recv : %s \n", buf);

    /*
    if (recv(sock, &buf, 1, 0) >= 0)
      printf("recv : %c \n", buf);*/
  }
  
  closesocket(sock); 
  WSACleanup();
  return 0;
}
  
  
### 実行結果

送信側のプログラムを起動して、その次に受信側のプログラムを起動します。  
下の写真の様になれば成功です。  
![f:id:pythonjacascript:20200625141904j:plain](/images/ppythonjacascript2020062520200625141904.jpg "f:id:pythonjacascript:20200625141904j:plain")  
  
  
### 解説（送信側）

#### 初期化

上のプログラムから自分的にメモっておきたいところを抜粋して説明します。

int sock = socket(AF_INET, SOCK_DGRAM, 0);

socket()関数で通信に使うsocketオブジェクトを作成します。  
もし、UDPではなくTCP通信を行うならば、第二引数が**SOCK\_DGRAM**から**SOCK\_STREAM**に変更します。  
  
addr.sin_family = AF_INET;
addr.sin_port = htons(50008);// 待ち受けポート番号を50008にする
addr.sin_addr.S_un.S_addr = inet_addr("127.0.0.1");// 送信アドレスを設定

つぎに、送信先のアドレスを設定します。

* **addr.sin\_addr.S\_un.S\_addr**は送信先のIPアドレスを格納しています。
* **addr.sin\_port**は送受信に使用するポート番号です。受信側のプログラムでbindするポート番号と同じ値にします。

##### 【補足】ポート番号について

インターネットでは、さまざまな種類の通信が行なわれていますが、それをポート番号で管理しています。まず、ホストからホストに通信を行なう場合は、宛先の IPアドレスの他に、データの種類に対応した宛先のポート番号を指定します。受信したホストは、そのポート番号に適切なプログラムにその内容を渡すことで、データの区別を行っています。  
代表的なポート番号の例は以下のようなものがあります。

| ポート番号 | 名称    | 説明                   |
| ----- | ----- | -------------------- |
| 22    | SSH   | 暗号化されたコマンドシェル        |
| 25    | SMTP  | メール送受信               |
| 80    | HTTP  | WWWでの通信              |
| 53    | DNS   | ホスト名とIPアドレスの変換データベース |
| 443   | HTTPS | WWWでの通信（暗号化）         |
| 123   | NTP   | ネットワーク時刻同期           |

なので、自身の作ったプログラムのポート番号はこれら以外の値である必要があります。  
  
  
#### データ送信

↑で設定したアドレスにデータを送信するには、

char data[] = "test";
sendto(sock, data, sizeof(data), 0, (struct sockaddr *)&addr, sizeof(addr));//addrに文字列送信

のように**sendto関数**を使用します。

ssize_t sendto(
    int socket, //通信を行うソケット
    const void *buffer, //送信データ（の配列の最初へのポインタ）
    size_t length, //送信データのバイト長
    int flags,  //MSG_OOB（ソケット上のアウト・オブ・バンドのデータを送信し）やMSG_DONTROUTEを指定できる。普通は0にしておけばよい
    const struct sockaddr *address, //送信先のアドレス
    size_t address_len //sizeof(address)
);

  
引き数 sockfd は、データを送信するパケットのファイルディスクリプターである。

※TCP通信などの接続型のソケット (SOCK\_STREAM, SOCK\_SEQPACKET) で データを送信する場合、引き数 addressと address\_lenは無視されます (**address=NULL, address\_len=0** にする)。  
  
#### タイムアウト時間設定

// タイムアウト時間の設定
struct timeval tv;
tv.tv_sec = 0; //タイムアウト時間（秒）
tv.tv_usec = 10;//タイムアウト時間（マイクロ秒）
if (setsockopt(sock, SOL_SOCKET, SO_RCVTIMEO, (char*)&tv, sizeof(tv)) < 0) {
  perror("Error");
}

上のようにすることでソケットのタイムアウト時間を設定することができます。  
[c - Socket with recv-timeout: What is wrong with this code? - Stack Overflow](https://stackoverflow.com/questions/393276/socket-with-recv-timeout-what-is-wrong-with-this-code)  
  
  
### 解説（受信側）

#### 初期化

送信側とほとんど同じですが、違うところはbindが必要なところです。

sock = socket(AF_INET, SOCK_DGRAM, 0); 

// アドレス等格納
struct sockaddr_in addr;
addr.sin_family = AF_INET;  //IPv4
addr.sin_port = htons(50008);   //通信ポート番号設定
addr.sin_addr.S_un.S_addr = INADDR_ANY; // INADDR_ANYはすべてのアドレスからのパケットを受信する

bind(sock, (struct sockaddr *)&addr, sizeof(addr));

  
上の様にsocketをaddrにバインドすることで、全てのIPアドレスから50008ポートに送られてきたデータを受信できるようになります。  
  
  
#### データ受信

データを受信するには**recv関数**と**recvfrom関数**があります。（recvfrom関数を使うと、データ送信側のIPアドレスがわかる）  
どちらも、通常モード（ブロックモード）では、データを受信するまで処理を返さずに受信するのを待ち続けます。これをなくすにはノンブロック処理（↓の節）を設定する必要があります。  
  
##### recv関数

// 実装例：
 char buf[100];   //受信データをここに格納する
memset(buf, 0, 100);  //bufを全て0で初期化
recv(sock, buf, 100, 0);  //100バイト受信する

int recv(
  int socket,
  char *buffer, //受信データを格納するバッファのポインタ
  int length, //何バイト受信するか
  int flags //0にしておけばいい
);

  
返り値については、↓の「ノンブロッキング処理」を見てください。  
  
##### recvfrom関数

int recvfrom(int socket, char *buffer, 
             int length, int flags,
             struct sockaddr *address, 
             int *address_length);
  
  
#### ノンブロッキング処理（recv関数の受信待ちをしない）

通常モード(**ブロックモード**)では、recv関数/recvfrom関数は何らかのデータを受信するまで処理を返しません。  
しかし、**ノンブロッキングモード**にすると、データ受信の如何に関わらず処理を即座に戻します。

実際にデータを受信したのかどうかはrecv関数/recvfrom関数の返り値で判断します。

**【recv関数の場合の返り値】**

* データ受信した場合→受信したデータのバイト数を返す
* ソケットがclosedの時→0を返す
* データ受信に失敗した時→-1（SOCKET\_ERROR）を返す

**【recvfrom関数の場合の返り値】**

* データ受信した場合→受信したデータのバイト数を返す
* データが受信せず、socket がブロック以外のモードになっている場合→**\-1（SOCKET\_ERROR）**を返す

  
ノンブロッキングに設定するには、bindした後で、

// ここで、ノンブロッキングに設定しています。
u_long val = 1;
ioctlsocket(sock, FIONBIO, &val);

  
を加えます。

ノンブロッキングなので、データ受信の部分の関数はこのように書きます

int data_size = recv(sock, &buf, 1, 0);
if(data_size >= 0){
  printf("recv : %c \n", buf);
}