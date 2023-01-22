---
title: "【DesignSpark】パターン作成について（物体を等間隔に複製する）"
emoji: ""
type: ""
topics: []
published: false
---

* [この記事の内容](#この記事の内容)
* [実際にやってみよう！](#実際にやってみよう)
* [パターン作成（応用編）](#パターン作成応用編)

### この記事の内容

同じ作業を繰り返すのは苦痛ですよね...

例えば、直方体を等間隔で作成したい場合。

直方体を一個作って、それをコピー＆ペーストすればいいのですが、ペーストしたい数が大量だった場合、どうしますか？  
以下のように大量の直方体が必要な場合、**いちいちコピペ作業をやってられません**....。  
![f:id:pythonjacascript:20190103210416j:plain](/images/ppythonjacascript2019010320190103210416.jpg "f:id:pythonjacascript:20190103210416j:plain")

**そんなあなたに！**  
DesignSparkには「**パターン作成機能**」があります。  
  
### 実際にやってみよう！

> これは完全に**蛇足**ですが、最近見出しがこのように砕けた表現になっているのは理由があります。  
> それは「[30日でできる! OS自作入門](https://www.amazon.co.jp/30%E6%97%A5%E3%81%A7%E3%81%A7%E3%81%8D%E3%82%8B-OS%E8%87%AA%E4%BD%9C%E5%85%A5%E9%96%80-%E5%B7%9D%E5%90%88-%E7%A7%80%E5%AE%9F/dp/4839919844)」という本の影響です。この本には30日でHariboteOSというOSを作成しよう！って本なのですが、見出しのつけ方が結構雑です( ´∀｀ )  
> 内容は丁寧に書かれていて面白い本なのでぜひ読んでみてください！！

  
どうでもいい話は置いといて、パターン作成の便利さを紹介しましょう。

**STEP1**  
直方体を一つ作ります。  
![f:id:pythonjacascript:20190103211030j:plain](/images/ppythonjacascript2019010320190103211030.jpg "f:id:pythonjacascript:20190103211030j:plain")

  
**STEP2**  
**「移動」アイコン**をクリックします。  
![f:id:pythonjacascript:20190103211239j:plain](/images/ppythonjacascript2019010320190103211239.jpg "f:id:pythonjacascript:20190103211239j:plain")  
「移動」と書いてありますが、今回はどちらかというと「複製」が、このアイコンで操作を行います。

次に移動（複製）する物体を選択します。  
今回は直方体を複製したいので、直方体を選択（左のトリプルクリックで物体を選択できます）。

すると、下の写真のようになるはずです。  
![f:id:pythonjacascript:20190103211932j:plain](/images/ppythonjacascript2019010320190103211932.jpg "f:id:pythonjacascript:20190103211932j:plain")

  
**STEP3**  
ここで、画面左下に「**パターン作成**」と書かれたチェックボックスがあるので、ここにチェックを入れます。  
ここにチェックを入れなかったら物体はただ移動するだけになります。

  
**STEP4**  
複製したい方向に矢印をドラッグします。  
![f:id:pythonjacascript:20190103212400j:plain](/images/ppythonjacascript2019010320190103212400.jpg "f:id:pythonjacascript:20190103212400j:plain")

  
**STEP5**  
マウスを離すと、下のように「数」と書かれたボックスがあるので、ここに複製する数を入れてください。  
![f:id:pythonjacascript:20190103212631j:plain](/images/ppythonjacascript2019010320190103212631.jpg "f:id:pythonjacascript:20190103212631j:plain")  
今回は「5」と入力しました。  
すると...

**STEP6**  
等間隔で直方体を5個複製することができました。  
![f:id:pythonjacascript:20190103212715j:plain](/images/ppythonjacascript2019010320190103212715.jpg "f:id:pythonjacascript:20190103212715j:plain")  
  
  
### パターン作成（応用編）

パターン作成ができるのは、上のように平行移動の時だけではありません。  
回転移動させるときにもパターン作成を行うことができます。

例えば、下のように円と台形があります。  
![f:id:pythonjacascript:20190103213809j:plain](/images/ppythonjacascript2019010320190103213809.jpg "f:id:pythonjacascript:20190103213809j:plain")

  
この台形を選択して、円の中心を軸に回転させてパターンを作成します。  
![f:id:pythonjacascript:20190103213902j:plain](/images/ppythonjacascript2019010320190103213902.jpg "f:id:pythonjacascript:20190103213902j:plain")

  
その結果がこちら  
![f:id:pythonjacascript:20190103213916j:plain](/images/ppythonjacascript2019010320190103213916.jpg "f:id:pythonjacascript:20190103213916j:plain")

  
この全体をプルすると、このように歯車のような物体が完成します！！  
![f:id:pythonjacascript:20190103213957j:plain](/images/ppythonjacascript2019010320190103213957.jpg "f:id:pythonjacascript:20190103213957j:plain")

歯車の歯を一つずつ書くのはあまりに大変すぎるので、このような効率UP系のコマンドも覚えておくべきでしょう。

**※この歯車作成方法は微妙に間違っています！！！本当の歯車の作り方というのを今後上げるつもりです...。**