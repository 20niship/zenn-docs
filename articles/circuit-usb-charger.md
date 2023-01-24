---
title: "【電子工作】USB充電器の製作"
emoji: "🤖"
type: "tech"
topics: undefined
published: false
---

スマホとモバイルバッテリー用のUSB充電器を作ってみました。

* [製作](#製作)
* [運用結果：](#運用結果)

  
![f:id:pythonjacascript:20190119120008j:plain](/images/ppythonjacascript2019011920190119120008.jpg "f:id:pythonjacascript:20190119120008j:plain")

製作したのは、写真上部分のGoogleモバイルバッテリーではなく、それを充電している下の基盤の方です。

前回作成した5V昇圧回路と組み合わせて使用し、モバイルバッテリーを充電しています。  
[shizenkarasuzon.hatenablog.com](https://shizenkarasuzon.hatenablog.com/entry/2019/01/19/112922)

  
### 製作

とても簡単です。  
必要な部品は以下の2種類。

| 部品名   | 個数 | 説明        | 購入先URL                                         |
| ----- | -- | --------- | ---------------------------------------------- |
| USB端子 | 1  | メスです      | <http://akizukidenshi.com/catalog/g/gC-11551/> |
| 抵抗    | 2  | 筆者は1KΩを使用 | <http://akizukidenshi.com/catalog/g/gR-25102/> |

  
では、早速制作に取り掛かります。

まず、USBは以下のようなピンアサインになっています。  
![f:id:pythonjacascript:20190119121536j:plain](/images/ppythonjacascript2019011920190119121536.jpg "f:id:pythonjacascript:20190119121536j:plain")  
このように、中央の2本は信号線なので今回は使用しません。抵抗でプルアップしておきました。

1ピンを5Vに接続し、4ピンを0Vに接続します。それだけです。  
![f:id:pythonjacascript:20190119122835j:plain](/images/ppythonjacascript2019011920190119122835.jpg "f:id:pythonjacascript:20190119122835j:plain")

表面：  
![f:id:pythonjacascript:20190119122004j:plain](/images/ppythonjacascript2019011920190119122004.jpg "f:id:pythonjacascript:20190119122004j:plain")

  
裏面：  
![f:id:pythonjacascript:20190119122022j:plain](/images/ppythonjacascript2019011920190119122022.jpg "f:id:pythonjacascript:20190119122022j:plain")

  
### 運用結果：

正常に動いています。  
![f:id:pythonjacascript:20190119120008j:plain](/images/ppythonjacascript2019011920190119120008.jpg "f:id:pythonjacascript:20190119120008j:plain")  
分かりにくいですが、Googleモバイルバッテリーの電源ボタン上の充電ランプが点灯しています。