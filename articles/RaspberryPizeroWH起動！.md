---
title: "Raspberry Pi zero WH 起動！"
emoji: ""
type: ""
topics: []
published: false
---

先日、**Raspberry pi zero W** が届きました！  
[shizenkarasuzon.hatenablog.com](https://shizenkarasuzon.hatenablog.com/entry/2019/01/25/080522)

今回は、Raspberry pi zero Wの起動を行ってみます！

* [1.必要な部品](#1必要な部品)
* [2.OSダウンロード](#2OSダウンロード)  
   * [SDカードをフォーマット](#SDカードをフォーマット)  
   * [OSダウンロード](#OSダウンロード)
* [初めての起動](#初めての起動)

### 1.必要な部品

* Raspberry pi zero
* micro SDカード
* mini HDMI出力に対応したディスプレイまたは変換ケーブルetc.
* キーボード
* マウス
* 2.0Aのmicro-USB電源
* micro USB - USB変換アダプタ
* USBハブ

一部の部品は開発環境によっては不要なものもあります。

あと、Raspberry pi はケースに入れた方が良いです。  
基盤がむき出しになっていると、いつショートして昇天してしまうか分かりません。  
  
### 2.OSダウンロード

RPIはコンピュータなのでOSが必要です。  
そのため、OSをmicro-SDカードにダウンロードして入れます。

...とその前に、micro-SDカードをフォーマットしておくことをお勧めします。

#### SDカードをフォーマット

[SDメモリカードフォーマッター - SD Association](https://www.sdcard.org/jp/downloads/formatter%5F4/)  
まず、上記サイトから「**SDメモリカードフォーマッター**」をダウンロードして、SDカードをフォーマットします。

  
![f:id:pythonjacascript:20190125083128j:plain](/images/ppythonjacascript2019012520190125083128.jpg "f:id:pythonjacascript:20190125083128j:plain")  
ここの青ボタンを押すとダウンロードが開始されます。

ソフトを起動すると、このような画面になるので、  
![f:id:pythonjacascript:20190125083228j:plain](/images/ppythonjacascript2019012520190125083228.jpg "f:id:pythonjacascript:20190125083228j:plain")  
➀一番上の「**カードの選択**」でmicro-SDのドライブを選択  
②右下「フォーマット」ボタンを押す  
でフォーマットが完了します。

このような画面が表示されればOKです。  
![f:id:pythonjacascript:20190125083401j:plain](/images/ppythonjacascript2019012520190125083401.jpg "f:id:pythonjacascript:20190125083401j:plain")

  
#### OSダウンロード

次に、**NOOBS lite**というOSを以下のサイトからダウンロードます、  
[Download NOOBS for Raspberry Pi](https://www.raspberrypi.org/downloads/noobs/)

上記ページの「**NOOBS lite**」の「**Download ZIP**」を選択してください。  
![f:id:pythonjacascript:20190125083700j:plain](/images/ppythonjacascript2019012520190125083700.jpg "f:id:pythonjacascript:20190125083700j:plain")

すると、「**NOOBS\_lite\_v3\_0.zip**」というファイルができるので、**それを解凍して**microSDカードに保存します。

![f:id:pythonjacascript:20190125084218j:plain](/images/ppythonjacascript2019012520190125084218.jpg "f:id:pythonjacascript:20190125084218j:plain")  
SDカードの中がこのようになっていればOKです。  
  
### 初めての起動

OSをダウンロードしたmicro-USBを差し込みます。  
![f:id:pythonjacascript:20190125084328j:plain](/images/ppythonjacascript2019012520190125084328.jpg "f:id:pythonjacascript:20190125084328j:plain")  
「カチッ」って引っかかるタイプではなく、ただ差し込むだけです。

  
そして、ケーブル類を接続して、  
![f:id:pythonjacascript:20190125084350j:plain](/images/ppythonjacascript2019012520190125084350.jpg "f:id:pythonjacascript:20190125084350j:plain")  
![f:id:pythonjacascript:20190125084403j:plain](/images/ppythonjacascript2019012520190125084403.jpg "f:id:pythonjacascript:20190125084403j:plain")

**※2つのmicro-USB端子のうち、mini-HDMI端子に近いほうがキーボード＆マウスetc.用、HDMI端子から遠いほうが電源用です。**

電源用のmicro-USB端子に電源を供給すれば...

起動します！！  
![f:id:pythonjacascript:20190125093721j:plain](/images/ppythonjacascript2019012520190125093721.jpg "f:id:pythonjacascript:20190125093721j:plain")

![f:id:pythonjacascript:20190125093742j:plain](/images/ppythonjacascript2019012520190125093742.jpg "f:id:pythonjacascript:20190125093742j:plain")