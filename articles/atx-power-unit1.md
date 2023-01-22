---
title: "ATX電源（デスクトップPCの電源ユニット）を電子工作用の電源モジュールにする"
emoji: ""
type: ""
topics: undefined
published: false
---

* [ATX電源とは](#ATX電源とは)
* [電源モジュールに改造](#電源モジュールに改造)
* [運用結果](#運用結果)
* [コードの色別電圧一覧](#コードの色別電圧一覧)

### ATX電源とは

デスクトップ型のPCには、「ATX電源（電源ユニット）」と呼ばれる部品が存在します。

こんなごつい外見をしています。  
![f:id:pythonjacascript:20190119084634j:plain](/images/ppythonjacascript2019011920190119084634.jpg "f:id:pythonjacascript:20190119084634j:plain")

Wikipediaには、

> ATX電源（エーティーエックスでんげん、英: ATX Power Supply）は、ATXコンピュータ用の電源回路を収めたユニットの標準規格、およびその規格に準じた電源ユニットを指す。

と書かれています。

ATX電源は、元々デスクトップPCの電源用に作られました。ですが、今回はこれを電子工作等の電源モジュールに流用します。

  
ATX電源を使う利点は以下の3つ。  
**・3.3V、5V、12V等、よく使う電圧が得られる** 
**・大電流が流せるため、直流DCモーターの電源ユニット等、大電力が必要な場合も活躍可能！** 
**・過電流・短絡保護回路が搭載されており、安全対策が施されている**

  
では、実際に作っていきましょう。  
  
### 電源モジュールに改造

今回の実験では、AYX電源は[これ](https://www.amazon.co.jp/KEIAN-%E9%9D%99%E3%81%8B-ATX420W%E9%9B%BB%E6%BA%90-KT-420BKV-SLI/dp/B000K7JYCS)を使用しています。  
[KEIAN KEIAN 静か ATX420W電源 KT-420BKV SLI](https://www.keian.co.jp/products/kt-420rs/)

ダメもとで中古品を買ったので1000円かからずに購入できました。

  
では、ATX電源のコンセントをさして、電源ON！！  
っとしても動きません。

  
あるワンポイント改造が必要になります。

  
![f:id:pythonjacascript:20190119091822j:plain](/images/ppythonjacascript2019011920190119091822.jpg "f:id:pythonjacascript:20190119091822j:plain")  
このように、**緑の端子とその隣の黒の端子を短絡**させてください。

![f:id:pythonjacascript:20190119095319j:plain](/images/ppythonjacascript2019011920190119095319.jpg "f:id:pythonjacascript:20190119095319j:plain")  
上の図で赤線で接続されている2つのピンです。

これが電源スイッチのようなものになります。

この部分にはあまり電流は流れないので、そこそこのコードや金属線でいいと思います。  
因みに筆者はクリップをU字型に変形させて差し込んでいます（写真上）。

  
では、今度こそ電源をONしてみてください。  
ファンが回っているとATX電源が動いている証拠です。

**もし焦げ臭いにおいがしたり煙が出始めた時はすぐにコンセントを抜いてください。**

  
### 運用結果

使ってみた感じです。  
**・とても使いやすい！！** 
**・長時間の運用にも耐えられる！**  
ということを感じました。

CNCのスピンドルモーターとして、RS-540を5時間近く連続運用していたのですが、全然耐えてくれます。  
RS-540は最大50A必要になるため（ストール時）、電源側にとてつもない負荷がかかるモーターなのですが、電源は熱くなることもなく平然と動き続けました。

因みに、そのCNCとはこちらです。  
[shizenkarasuzon.hatenablog.com](https://shizenkarasuzon.hatenablog.com/entry/2018/11/14/190047)  

とりあえず、電源ユニットとしては問題なく使用できます！！  
  
### コードの色別電圧一覧

コードの色と電圧の関係は以下の通りになっています。  
![f:id:pythonjacascript:20190119095319j:plain](/images/ppythonjacascript2019011920190119095319.jpg "f:id:pythonjacascript:20190119095319j:plain")

| コードの色 | 役割（電圧）  |
| ----- | ------- |
| 黒     | GND（0V） |
| 黄     | +12V    |
| 赤     | +5V     |
| オレンジ  | +3.3V   |
| 青     | \-12V   |
| 白     | 使用しない   |

基本的には「**黒（GND)－その他の色（+？V）**」という接続で使用します。

色があっていれば度のコードに接続しても基本的に動きます。

電圧測定をしたので結果を示しておきます。
  
  
![f:id:pythonjacascript:20190119095716j:plain](/images/ppythonjacascript2019011920190119095716.jpg "f:id:pythonjacascript:20190119095716j:plain")  
黄色に接続（理想：+12V）
  
  
![f:id:pythonjacascript:20190119095722j:plain](/images/ppythonjacascript2019011920190119095722.jpg "f:id:pythonjacascript:20190119095722j:plain")  
青色に接続（理想：-12V)
  
  
![f:id:pythonjacascript:20190119095725j:plain](/images/ppythonjacascript2019011920190119095725.jpg "f:id:pythonjacascript:20190119095725j:plain")  
オレンジに接続（理想：+3.3V)
  
  
![f:id:pythonjacascript:20190119095728j:plain](/images/ppythonjacascript2019011920190119095728.jpg "f:id:pythonjacascript:20190119095728j:plain")  
赤に接続（理想：+5V)
  
  
![f:id:pythonjacascript:20190119095719j:plain](/images/ppythonjacascript2019011920190119095719.jpg "f:id:pythonjacascript:20190119095719j:plain")  
青色に接続（理想：+5V)

  
![f:id:pythonjacascript:20190119095707j:plain](/images/ppythonjacascript2019011920190119095707.jpg "f:id:pythonjacascript:20190119095707j:plain")  
白コードも試してみました。5Vが出ていましたが、5V電源として使用する際は赤コードを使用したほうがいいと思います。
  
  
「**５Ｖに負荷が無いと１２Ｖ側の電圧が上がらない。５Ｖに負荷があると１２Ｖの電圧が上がる。**」と[ここ](http://www.geocities.co.jp/Playtown/9373/make/atx-power/index.html)に書かれていましたが、今のところ問題なく12Vを出力できています。