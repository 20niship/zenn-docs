---
title: "【Arduinoドローン自作】NO.1 部品集め編"
emoji: ""
type: ""
topics: []
published: false
---

ドローンを**Arduino**を使って自作するために必要な部品を紹介していきます。

「**フライトコントローラー**」というドローン専用の制御装置を用いてより簡単に製作することもできます。

ですが、  
**「どうせ作るんなら0から作ろう！」**

ということで、プログラムも自作することになりました。  
結果、市販のフライトコントローラを使わずに、Arduinoを使用することにしたのです。

必要なもの：

* [フレーム](#フレーム)
* [ブラシレスモーター](#ブラシレスモーター)
* [プロペラ](#プロペラ)
* [ESC](#ESC)
* [Arduino UNO](#Arduino-UNO)
* [xBee](#xBee)
* [バッテリー](#バッテリー)

### フレーム

![f:id:pythonjacascript:20181123130332j:plain](/images/ppythonjacascript2018112320181123130332.jpg "f:id:pythonjacascript:20181123130332j:plain")  
冒頭で「すべて自作！」とあれだけほざいているので、自作しましょう。  
材料は木材かアルミがいいと思います。

材料選択の基準は、  
**・落下時の衝撃にも耐えられる硬さがあること** 
**・軽いこと**  
の二点です。

![f:id:pythonjacascript:20181123130034j:plain](/images/ppythonjacascript2018112320181123130034.jpg "f:id:pythonjacascript:20181123130034j:plain")  
軽量化のためにこのように「**肉抜き**」を行ってもいいと思います。  
  
  
### ブラシレスモーター

![f:id:pythonjacascript:20180821235135j:plain](/images/ppythonjacascript2018082120180821235135.jpg "f:id:pythonjacascript:20180821235135j:plain")  
プロペラを回転させるためのモーターです。  
今回自作するのは4プロペラタイプなので、プロペラも当然4つ必要です。

普通のDCブラシモーターではなく、「**ブラシレスモーター**」が必要なので、購入するときは注意してください。

選ぶポイントは以下の3つ。  
**・プロペラを付けた時の推力（推力という言葉が正しいかはわかりませんが、持ち上げる力です）** 
**・電源電圧** 
**・モーターを回転させるのに必要な電流量**

  
プロペラの推力は、モーターだけでなくプロペラの形状によっても変わってくるので選択が難しいところですが、  
[DX2205](https://www.amazon.co.jp/2%E5%80%8BDX2205-2300KV-RC%E3%83%AC%E3%83%BC%E3%82%B7%E3%83%B3%E3%82%B0%E3%83%89%E3%83%AD%E3%83%BC%E3%83%B3QAV250-280%E7%94%A82-4S-CCW%E3%83%96%E3%83%A9%E3%82%B7%E3%83%AC%E3%82%B9%E3%83%A2%E3%83%BC%E3%82%BF%E3%83%BC/dp/B07KQ179WP/ref=sr%5F1%5F1?s=toys&ie=UTF8&qid=1542946257&sr=1-1&keywords=DX2205)というブラシレスモーターを使うと、モーター一個当たり、**最大500ｇ以上の推力**を得ることができました。  
推力の計測実験はこのように行いました。  
[shizenkarasuzon.hatenablog.com](https://shizenkarasuzon.hatenablog.com/entry/2018/08/22/212046)

ドローン一台につきモーターは4個つけるので、単純計算で約2kgのドローンを持ち上げることができます。  
  
  
### プロペラ

![f:id:pythonjacascript:20180822205729p:plain](/images/ppythonjacascript2018082220180822205729.png "f:id:pythonjacascript:20180822205729p:plain")  
モーターに取り付けるプロペラです。

このブログで制作しているドローンでは[これ](https://www.amazon.co.jp/HOBBYPOWER-5045%E6%8E%A8%E9%80%B2%E6%A9%9F%E3%83%97%E3%83%AD%E3%83%9A%E3%83%A9-PropellerCW-%E9%81%A9%E7%94%A8Mini-QAV250/dp/B016ZQUW1Y/ref=sr%5F1%5F4?s=hobby&ie=UTF8&qid=1542947872&sr=1-4&keywords=5045)を使用しています。

選択基準は  
・十分な推力が得られるか？  
・モーターのシャフト径と、プロペラの内径があっているか？  
です。

自作ドローンの場合、最初はうまく飛ばず、  
**飛行中にドローンをぶつけて、プロペラ数枚を木っ端微塵にしてしまうこと間違いなし**なので、多めに買っておくことをお勧めします。  
  
### ESC

![f:id:pythonjacascript:20180822211343p:plain](/images/ppythonjacascript2018082220180822211343.png "f:id:pythonjacascript:20180822211343p:plain")  
ESCはブラシレスモーターを回すためのドライバーです。  
ブラシレスモーター一個につきESCも1つ必要なので、合計で4つ注文することになります。

選択基準は、  
・モーターを回転させるのに必要な電流・電圧があるか  
です。

今回は[この](https://www.amazon.co.jp/GoolRC-%E3%82%B9%E3%83%94%E3%83%BC%E3%83%89%E3%82%B3%E3%83%B3%E3%83%88%E3%83%AD%E3%83%BC%E3%83%A9-DX2205-RC%E3%83%AC%E3%83%BC%E3%82%B9%E3%83%9E%E3%83%AB%E3%83%81%E3%82%B3%E3%83%97%E3%82%BF%E3%83%BC-%E3%83%89%E3%83%AD%E3%83%BC%E3%83%B3%E3%82%AF%E3%83%AF%E3%83%83%E3%83%89%E3%83%AD%E3%83%BC%E3%82%BF%E3%83%BC%E7%94%A8/dp/B0748GGSFZ/ref=sr%5F1%5F3?ie=UTF8&qid=1542948148&sr=8-3&keywords=DX2205%E3%80%80ESC)ESCを使用しました。ESCとモーターがセットになっています。  
  
  
### Arduino UNO

![f:id:pythonjacascript:20181024164655p:plain](/images/ppythonjacascript2018102420181024164655.png "f:id:pythonjacascript:20181024164655p:plain")  
Arduinoをフライトコントローラ代わりに使用します。  
Arduino UNOでもDUEでも何でも構いません。

リモコンも自作するならば、リモコン用Arduinoも必要です。また、その場合には、ジョイスティックなどの追加部品も購入が必要になるでしょう。  
  
### xBee

![f:id:pythonjacascript:20180724230134p:plain](/images/ppythonjacascript2018072420180724230134.png "f:id:pythonjacascript:20180724230134p:plain")

リモコンとの無線通信用に使用します。  
xBeeを使用すると、有線シリアル通信を行うのと同じように無線でデータのやり取りを行うことができます。  
  
  
### バッテリー

![f:id:pythonjacascript:20181123135304j:plain](/images/ppythonjacascript2018112320181123135304.jpg "f:id:pythonjacascript:20181123135304j:plain")  
ドローン本体用のバッテリーです。

・電圧  
・容量  
・質量  
・大きさ  
の4つをよく考えて購入してください。

私は[これ](https://www.amazon.co.jp/%E9%9B%BB%E5%8B%95%E3%82%AC%E3%83%B3%E7%94%A8%E3%83%AA%E3%83%9D%E3%83%90%E3%83%83%E3%83%86%E3%83%AA%E3%83%BC%E3%80%90-%E3%83%9F%E3%83%8B%E3%82%BF%E3%82%A4%E3%83%97-7-4v-1300mAh-40C/dp/B06WP4GC3C/ref=sr%5F1%5F28?ie=UTF8&qid=1542948664&sr=8-28&keywords=%E3%83%90%E3%83%83%E3%83%86%E3%83%AA%E3%83%BC%E3%80%80%E9%9B%BB%E5%8B%95%E3%82%AC%E3%83%B3)を使用しました。