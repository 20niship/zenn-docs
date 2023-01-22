---
title: "【電子工作】1.5V→5Vの昇圧回路を作成"
emoji: "🤖"
type: "tech"
topics: []
published: false
---

今回は、このような小物を製作します。

![f:id:pythonjacascript:20190119110006j:plain](/images/ppythonjacascript2019011920190119110006.jpg "f:id:pythonjacascript:20190119110006j:plain")

1.5Vを5Vに昇圧する回路です。

* [使用部品一覧](#使用部品一覧)
* [1.回路図](#1回路図)
* [配線図](#配線図)
* [作成](#作成)
* [実験](#実験)

### 使用部品一覧

部品点数も少ないためすぐに製作できると思います。

| 部品名            | 個数 | 説明                    | 購入サイト                                          |
| -------------- | -- | --------------------- | ---------------------------------------------- |
| HT7750A        | 1  | PFMステップ・アップDC/DCコンバータ | <http://akizukidenshi.com/catalog/g/gI-02800/> |
| 電解コンデンサ100uF前後 | 2  | 電圧平滑用                 | <http://akizukidenshi.com/catalog/g/gP-02724/> |
| ダイオード1N5817等   | 1  | ショットキーバリアダイオード        | <https://www.marutsu.co.jp/pc/i/69376/>        |
| マイクロインダクタ 47uH | 1  | 47\~100uHのインダクタ       | <http://akizukidenshi.com/catalog/g/gP-04924/> |
  
  
### 1.回路図

![f:id:pythonjacascript:20190119110058j:plain](/images/ppythonjacascript2019011920190119110058.jpg "f:id:pythonjacascript:20190119110058j:plain")  
このように作成します。HT7750というICを用いて昇圧します。

データシートはこちらに上がっていました：  
<http://www.kyohritsu.jp/eclib/OTHER/DATASHEET/ht77xxa.pdf>

### 配線図

表面から見た配線図：  
![f:id:pythonjacascript:20190119114328j:plain](/images/ppythonjacascript2019011920190119114328.jpg "f:id:pythonjacascript:20190119114328j:plain")  
C1、C2はコンデンサ、D1はダイオードを表しています。  
Vinが入力側+電源、Voutが+5V出力端子です。

裏面から見た配線図（左右反転）：  
![f:id:pythonjacascript:20190119114400j:plain](/images/ppythonjacascript2019011920190119114400.jpg "f:id:pythonjacascript:20190119114400j:plain")

### 作成

作成しました。  
表面：  
![f:id:pythonjacascript:20190119112514j:plain](/images/ppythonjacascript2019011920190119112514.jpg "f:id:pythonjacascript:20190119112514j:plain")  
左側の2本のコードが出力用、右側の2本のコードが入力用です。  
少ない基盤スペースに部品を詰め込んでいます。

裏面：  
![f:id:pythonjacascript:20190119112506j:plain](/images/ppythonjacascript2019011920190119112506.jpg "f:id:pythonjacascript:20190119112506j:plain")  
  
  
### 実験

1.4Vの乾電池を入力につなぎます。  
![f:id:pythonjacascript:20190119112643j:plain](/images/ppythonjacascript2019011920190119112643.jpg "f:id:pythonjacascript:20190119112643j:plain")

  
すると...  
![f:id:pythonjacascript:20190119112720j:plain](/images/ppythonjacascript2019011920190119112720.jpg "f:id:pythonjacascript:20190119112720j:plain")  
きちんと5Vが出力されていました。

LEDを光らせたり、スマホを充電したりという簡単なことはできます。