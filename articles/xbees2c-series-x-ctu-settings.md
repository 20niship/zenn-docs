---
title: "【xBee】xBee S2Cシリーズの X-CTU の設定"
emoji: ""
type: ""
topics: []
published: false
---

少し前の話ですが、ｘBeeの新しいシリーズが発売されました。

それが、**xBee S2C** シリーズです。

![f:id:pythonjacascript:20180825032333j:plain:h300](/images/ppythonjacascript2018082520180825032333.jpg "f:id:pythonjacascript:20180825032333j:plain:h300")

性能は、

| S2      | S2B Pro | **S2C** |           |
| ------- | ------- | ------- | --------- |
| 最大室内レンジ | 40m     | 60m     | **60m**   |
| 最大屋外レンジ | 120m    | 1500m   | **1200m** |

の通り、前のバージョンのS2よりも大幅に性能アップしています。xBee S2B proと同じくらいの距離です。

このS2Cモデルを使って通信をしてみたので、その方法を書きます。  
  
* [1\. この記事に書いてあること](#1この記事に書いてあること)
* [2.用意するもの](#2用意するもの)
* [3.X-CTUを用いた設定方法](#3X-CTUを用いた設定方法)  
   * [(1）パソコンにxBeeを接続](#1パソコンにxBeeを接続)  
   * [（2）X-CTU を起動 →ｘBeeを認識させる。](#2X-CTU-を起動ｘBeeを認識させる)  
   * [（3）X-CTU を使った基本的な設定](#3X-CTU-を使った基本的な設定)  
   * [（4）通信モードの設定](#4通信モードの設定)  
   * [（5）ATモードか、APIモードかを選択](#5ATモードかAPIモードかを選択)
  
  
### 1\. この記事に書いてあること

「なんでこの記事を書いたの？ｘBeeの設定とかいつもと同じやないん？」と思っているかもしれませんが、  
xBee はS2Cシリーズから、**「コーディネーター」や「ルーター」などのモード設定がなくなりました！**

なので、今まであった「Firmware」の設定がなくなったのです！

ということで、今回は、

**X-CTUを使ってｘBeeを設定**する方法を書いてみました。  
  
  
### 2.用意するもの

![f:id:pythonjacascript:20180825034228j:plain:h300](/images/ppythonjacascript2018082520180825034228.jpg "f:id:pythonjacascript:20180825034228j:plain:h300")

1. X-CTU の入ったパソコン
2. xBee S2C 必要な台数
3. xBee USB シリアル変換基盤
4. USBケーブル（片方はPC用につながるUSB、もう片方は**ミニB**と呼ばれるタイプのUSB）
5. 気合

### 3.X-CTUを用いた設定方法

#### (1）パソコンにxBeeを接続

下の写真のように接続してください。xBeeをUSB変換基盤に差し込むときは、**向きに注意**してください！基盤に xBee の外形が白線で書かれているので、それの向きに従ってください。  
![f:id:pythonjacascript:20180825034448j:plain:h300](/images/ppythonjacascript2018082520180825034448.jpg "f:id:pythonjacascript:20180825034448j:plain:h300")

  
#### （2）X-CTU を起動 →ｘBeeを認識させる。

![f:id:pythonjacascript:20180825034620p:plain](/images/ppythonjacascript2018082520180825034620.png "f:id:pythonjacascript:20180825034620p:plain")  
ここも、今までのバージョンと同じなので、他の記事を参考にしてください。  
  
  
#### （3）X-CTU を使った基本的な設定

以下の内容を書き込みます。この設定も、他のｘBeeをと同じです。

➀「**PAN ID**」：適当な数字を打ち込みます。２台で通信を行う場合は、その二台のPAN IDを同じ値に設定します。  
②「**DH**」：通信相手の xBee の上位アドレス（全ての xBee の共通で「13A200」）  
③「**DL**」：通信相手の xBee の上位アドレス（個々の xBee によって値が違う）  
  
  
#### （4）通信モードの設定

本題です。xBee二台で通信を行うには、一台を「コーディネーター」、もう一台を「ルーター」として動かします。

**パターンA：「コーディネータ」に設定する。**「Coordinator Enable」を「Enabled」にします。これで、「コーディネータモード」と同じように機能します。  
![f:id:pythonjacascript:20180825035343p:plain](/images/ppythonjacascript2018082520180825035343.png "f:id:pythonjacascript:20180825035343p:plain")  
  
 **パターンB：「ルーター」に設定する**「Coordinator Enable」を「Enabled」にします。これで、「ルーターモード」と同じように機能します。  
![f:id:pythonjacascript:20180825035527p:plain](/images/ppythonjacascript2018082520180825035527.png "f:id:pythonjacascript:20180825035527p:plain")

  
設定を変更した後に、![f:id:pythonjacascript:20180825035559p:plain:h20](/images/ppythonjacascript2018082520180825035559.png "f:id:pythonjacascript:20180825035559p:plain:h20")を使って、ｘBeeに変更した設定を書き込むこと。  
  
  
#### （5）ATモードか、APIモードかを選択

**パターンA：ATモードで使用**「API Enable」を「Transparent mode\[0\]」に設定する。  
![f:id:pythonjacascript:20180825040000p:plain](/images/ppythonjacascript2018082520180825040000.png "f:id:pythonjacascript:20180825040000p:plain")

  
**パターンB：APIモードで使用**「API Enable」を「API Enabled\[1\]」に設定する。  
![f:id:pythonjacascript:20180825040110p:plain](/images/ppythonjacascript2018082520180825040110.png "f:id:pythonjacascript:20180825040110p:plain")