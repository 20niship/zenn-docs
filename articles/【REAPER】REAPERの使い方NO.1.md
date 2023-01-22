---
title: "【REAPER】REAPER の使い方 NO.1"
emoji: ""
type: ""
topics: []
published: false
---

REAPERの使い方連載、第一回です。

REAPERで音楽編集をするに必要な基礎を忘れない内に書いておきます。  
基本的に他のソフトと同じなので、なんとなくで使い方がわかる人は、この記事は全く参考にならないと思います。

* [画面の外観](#画面の外観)
* [プロジェクト](#プロジェクト)  
   * [プロジェクトの作成](#プロジェクトの作成)  
   * [プロジェクトの保存](#プロジェクトの保存)
* [編集](#編集)  
   * [楽曲の取り込み](#楽曲の取り込み)  
   * [フェードアウト・フェードイン](#フェードアウトフェードイン)  
   * [トラックの合成](#トラックの合成)
* [書き出し（Rendering）](#書き出しRendering)

### 画面の外観

REAPERのメイン画面です。  
![f:id:pythonjacascript:20190731231509j:plain](/images/ppythonjacascript2019073120190731231509.jpg "f:id:pythonjacascript:20190731231509j:plain")

それぞれのトラックには右側に色々調整するパネルがあります。  
![f:id:pythonjacascript:20190731231513j:plain:h300](/images/ppythonjacascript2019073120190731231513.jpg "f:id:pythonjacascript:20190731231513j:plain:h300")

  
また、下のミキサーパネルのUIはこんな感じ。  
![f:id:pythonjacascript:20190731231447j:plain:h300](/images/ppythonjacascript2019073120190731231447.jpg "f:id:pythonjacascript:20190731231447j:plain:h300")

↑の「Clipping」とは、音量がピークを越えたとき、機器が壊れるのを防ぐため許容値以上の音を削除することをさします。よって音質も悪くなり、音割れします。  
REAPERではトラックの音がピークを越えると、そのトラックの「Clipping」部分が赤くなります。音編するときはピークを越えないように注意してください。

赤くなった部分を左クリックすると元の状態に戻ります。  
  
  
### プロジェクト

REAPERにも「プロジェクト」という概念があります。プロジェクトごとに設定を変更したりすることもできるので便利です。

#### プロジェクトの作成

編集プロジェクトを作成すると、その楽曲に関するすべての編集データが保存されます。  
**「File」→「New Project」**で作成できます。REAPERのプロジェクトの拡張子は「**.rpp**」です。  
  
#### プロジェクトの保存

**「ファイル」→「プロジェクトの保存」**で保存することができます。  
![f:id:pythonjacascript:20180906002857j:plain:h300](/images/ppythonjacascript2018090620180906002857.jpg "f:id:pythonjacascript:20180906002857j:plain:h300")

プロジェクトとして保存した場合、そのプロジェクト内で使用されている楽曲は動かさないほうが良いと思います。本来読み込まれるべき音楽ファイルの場所が違っていると、エラーになり再生できません。  
![f:id:pythonjacascript:20180906002850j:plain:h300](/images/ppythonjacascript2018090620180906002850.jpg "f:id:pythonjacascript:20180906002850j:plain:h300")  
  
  
### 編集

#### 楽曲の取り込み

REAPERの編集画面に楽曲を取り込む方法は、<span style="color: #d32f2f">**楽曲ファイルをREAPERの画面にドラッグ**するだけです。  
![f:id:pythonjacascript:20180906002027j:plain](/images/ppythonjacascript2018090620180906002027.jpg "f:id:pythonjacascript:20180906002027j:plain")

ドラッグでマウスから指を話した位置で、取り込んだ楽曲の開始位置が決定されます。  
取り込める音楽ファイルは、mp3 や wav 等の基本的ファイルは普通に読み込めます。  
  
  
#### フェードアウト・フェードイン

REAPERではフェードアウトとフェードインを簡単に行うことができます。  
下のように音声波形の右上（もしくは左上）端にマウスを持っていくと、カーソルの形が変化します。  
![f:id:pythonjacascript:20190731233646g:plain](/images/ppythonjacascript2019073120190731233646.gif "f:id:pythonjacascript:20190731233646g:plain")  

フェードアウトしたのち、その場で右クリックするとフェードアウトの音量カーブの種類が選べます。  
![f:id:pythonjacascript:20190731234054j:plain](/images/ppythonjacascript2019073120190731234054.jpg "f:id:pythonjacascript:20190731234054j:plain")  
  
  
#### トラックの合成

上下にある二つのトラックの音声をMergeします。  
下の方にあるトラックをドラッグすると、自動的にフェードイン・フェードアウトが作成されます。  
![f:id:pythonjacascript:20190731234244g:plain](/images/ppythonjacascript2019073120190731234244.gif "f:id:pythonjacascript:20190731234244g:plain")  
こちらも通常と同様に、右クリックでカーブを選択できます。
  
  
### 書き出し（Rendering）

編集が完了した音声をmp3 や wav などの音声ファイル形式で保存するには、**「ファイル」→「レンダリング」**を選択してください。

このような画面が表示されると思います。  
![f:id:pythonjacascript:20180909115236j:plain:h350](/images/ppythonjacascript2018090920180909115236.jpg "f:id:pythonjacascript:20180909115236j:plain:h350")

以下のような項目を設定します。  
・「Directory」＝ レンダリングした音声の出力先のフォルダを選択します。  
・「File Name」 = 出力する音声ファイル名を入力します。  
・「OutputFormat」＝出力するフォーマット（拡張子）を選択します。「wav」や「mp3」「OGG」などたくさんあります。