---
title: "【Brushless motor】DX2205 プロペラを付けて揚力テスト"
emoji: ""
type: ""
topics: []
published: false
---

前回の記事で、ブラシレスモーターDX2205をArduinoを使って制御する方法を紹介しました。

前回の記事：  
[shizenkarasuzon.hatenablog.com](https://shizenkarasuzon.hatenablog.com/entry/2018/08/22/015912)

  
今回は、その記事の続きで、ブラシレスモーターにプロペラを付けて推力を図ってみたいと思います。  
![f:id:pythonjacascript:20180822205953j:plain](/images/ppythonjacascript2018082220180822205953.jpg "f:id:pythonjacascript:20180822205953j:plain")

* [0.実験環境](#0実験環境)  
   * [（1）ブラシレスモーター](#1ブラシレスモーター)  
   * [（2）ESC](#2ESC)  
   * [(3)プロペラ](#3プロペラ)
* [1\. 方法](#1-方法)  
   * [（1）前回の記事に従って、ブラシレスモーターを制御する回路＆プログラムを作成する](#1前回の記事に従ってブラシレスモーターを制御する回路プログラムを作成する)  
   * [（2）プロペラを固定](#2プロペラを固定)  
   * [（3）はかりの上に固定。](#3はかりの上に固定)
* [2.測定する。](#2測定する)
* [3.結果：](#3結果)

### 0.実験環境

以下の3つを使って実験を行いました。参考までに。

#### （1）ブラシレスモーター

![f:id:pythonjacascript:20180821235135j:plain:h300](/images/ppythonjacascript2018082120180821235135.jpg "f:id:pythonjacascript:20180821235135j:plain:h300")

DX2205

| 品名     | DX2205 |
| ------ | ------ |
| KV値    | 2300KV |
| 高さ     | 31.5mm |
| モーター直径 | 27.9mm |
| シャフト直径 | M5     |
| 質量     | 28.8g  |
| 電圧     | 2-4S   |
| 最大電流   | 27.6A  |
| 最大電力   | 408W   |

購入元：<https://www.amazon.co.jp/GoolRC-スピードコントローラ-DX2205-RCレースマルチコプター-ドローンクワッドローター用/dp/B0748GGSFZ/ref=sr%5F1%5F6?ie=UTF8&qid=1534866536&sr=8-6&keywords=DX2205>

#### （2）ESC

![f:id:pythonjacascript:20180822211343p:plain:h300](/images/ppythonjacascript2018082220180822211343.png "f:id:pythonjacascript:20180822211343p:plain:h300")

| 電圧      | 2-4S      |
| ------- | --------- |
| 最大電流    | 25A       |
| ファームウェア | BLheli\_s |

購入元：<https://www.amazon.co.jp/GoolRC-スピードコントローラ-DX2205-RCレースマルチコプター-ドローンクワッドローター用/dp/B0748GGSFZ/ref=sr%5F1%5F6?ie=UTF8&qid=1534866536&sr=8-6&keywords=DX2205:>

  
#### (3)プロペラ

HOBBYPOWER 4ペア 5045推進機プロペラ PropellerCW CCW 5045R 適用Mini QAV250 TL250H TL250C TL280H TH280C  
![f:id:pythonjacascript:20180822205729p:plain:h300](/images/ppythonjacascript2018082220180822205729.png "f:id:pythonjacascript:20180822205729p:plain:h300")

購入元：<https://www.amazon.co.jp/HOBBYPOWER-5045%E6%8E%A8%E9%80%B2%E6%A9%9F%E3%83%97%E3%83%AD%E3%83%9A%E3%83%A9-PropellerCW-%E9%81%A9%E7%94%A8Mini-QAV250/dp/B016ZQUW1Y/ref=sr%5F1%5F18?ie=UTF8&qid=1534939004&sr=8-18&keywords=%E3%83%97%E3%83%AD%E3%83%9A%E3%83%A9%E3%80%805045>
  
  
### 1\. 方法

#### （1）前回の記事に従って、ブラシレスモーターを制御する回路＆プログラムを作成する

モーターを回転させるには、制御装置であるArduinoに専用プログラムを書き込む必要があります。  
その方法は、以下のページを参考にしてください。  
[shizenkarasuzon.hatenablog.com](https://shizenkarasuzon.hatenablog.com/entry/2018/08/22/015912)  

#### （2）プロペラを固定

まず、プロペラをブラシレスモーターの上部に固定します。回転させながらねじ込む感じです。

プロペラの設置が完了したら、その上からナットで固定してください。  
高速で回転するので、しめ方が甘くて回転中にプロペラが飛んで行った、なんてことは起こらないようにしたほうがいいです。

固定すると、このようになります。  
![f:id:pythonjacascript:20180822210450j:plain:h400](/images/ppythonjacascript2018082220180822210450.jpg "f:id:pythonjacascript:20180822210450j:plain:h400")

  
#### （3）はかりの上に固定。

完成系はこんな感じです。  
![f:id:pythonjacascript:20180822210509j:plain:h400](/images/ppythonjacascript2018082220180822210509.jpg "f:id:pythonjacascript:20180822210509j:plain:h400")

  
### 2.測定する。

前回の記事をもとに測定してください。  
  
  
### 3.結果：

図のようになりました。  
![f:id:pythonjacascript:20180822210707j:plain](/images/ppythonjacascript2018082220180822210707.jpg "f:id:pythonjacascript:20180822210707j:plain")

室内ですると、とてつもない迫力です。

なので、ビビッて40％までしか出力できていません...

いくら、**電源にデスクトップ用ATX電源の12V**を使っているといっても、これはすごい、と思います。

ですが、**40％の時点で、300ｇ以上の推力**があります。  
このままいけば、最大出力は、600ｇを超えそうです。  
![f:id:pythonjacascript:20180822212020j:plain:h400](/images/ppythonjacascript2018082220180822212020.jpg "f:id:pythonjacascript:20180822212020j:plain:h400")

また、この出力が異常に高いモーターでスロットル100％を試す勇気が出たら、その時に追記します。