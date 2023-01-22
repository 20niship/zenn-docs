---
title: "【Python】Pyaudioで音声を録音してグラフに表示"
emoji: ""
type: ""
topics: []
published: false
---

**pyaudio**というライブラリを使うと、**音声を録音・再生・保存**etc.することができます。

この記事では、音声の録音に挑戦してみます。また、録音した音声をmatplotlibを使ってグラフに表示してみます。

* [1.ライブラリのインストール](#1ライブラリのインストール)
* [2.音声を録音](#2音声を録音)  
   * [3.実行結果](#3実行結果)
* [トラブルシューティング](#トラブルシューティング)  
   * [プログラムエラー](#プログラムエラー)  
   * [波形が変](#波形が変)  
   * [波形が変②](#波形が変-1)
* [参考文献：](#参考文献)

### 1.ライブラリのインストール

以下のコマンドをプロンプトで実行します。

pip install pyaudio

その後、Pythonの以下のスクリプトでエラーが出なければインストールは完了しています。

import pyaudio
  
  
### 2.音声を録音

以下のプログラムで音声を録音し、matplotlibで音声波形を表示することができます。

import pyaudio
import numpy
import matplotlib.pyplot as plt

chunk = 1024
FORMAT = pyaudio.paInt16

CHANNELS = 1 #モノラル（2にするとステレオ）
RATE = 44100 #サンプルレート（録音の音質）
RECORD_SECONDS = 3 #録音時間

p = pyaudio.PyAudio()

stream = p.open(format = FORMAT,
                channels = CHANNELS,
                rate = RATE,
                input = True,
                frames_per_buffer = chunk)

#レコード開始
print("Now Recording...")
all = []
for i in range(0, int(RATE / chunk * RECORD_SECONDS)):
    data = stream.read(chunk) #音声を読み取って、
    all.append(data) #データを追加

#レコード終了
print("Finished Recording.")

stream.close()
p.terminate()

data = ''.join(all) #Python2用
#data = b"".join(all) #Python3用

#録音したデータを配列に変換
result = numpy.frombuffer(data,dtype="int16") / float(2**15)

plt.plot(result)
plt.show()

#### 3.実行結果

以下のようなグラフが作成されればOKです。

![f:id:pythonjacascript:20181230115046j:plain](/images/ppythonjacascript2018123020181230115046.jpg "f:id:pythonjacascript:20181230115046j:plain")  
録音中に、マイクの前で数回手を叩いてみました。

コードの中身については、コメントを見ながら感じ取ってください。  
  
### トラブルシューティング

いか、私がこのプログラムを実行するときに躓いた点です。  
  
#### プログラムエラー

上のプログラムを実行すると、このようなエラーが出ることがあります。

 data = ''.join(all)
TypeError: sequence item 0: expected str instance, bytes found

これは、Python3系で動作したときに発生するエラーのようです。

[stackoverflow.com](https://stackoverflow.com/questions/32071536/typeerror-sequence-item-0-expected-str-instance-bytes-found)

これを解決するには、

 data = ''.join(all)

の部分を

data = b"".join(all) #Python3用

に変更してください。  
  
  
#### 波形が変

プログラムはエラーなく動くのですが、波形が下のようになって全く録音できない、という場合は、パソコン側のマイク設定が間違っていることが考えられます。  
![f:id:pythonjacascript:20181230115426j:plain](/images/ppythonjacascript2018123020181230115426.jpg "f:id:pythonjacascript:20181230115426j:plain")

  
まず、**「設定」→「システム」→「サウンド」の「入力」項目**を開きます。

そして、「**マイクのテスト**」を行ってください。と言っても、マイクの前で何かしゃべって下の画像のように青いバーが動くかどうかを確認するだけです。  
![f:id:pythonjacascript:20181230115752j:plain](/images/ppythonjacascript2018123020181230115752.jpg "f:id:pythonjacascript:20181230115752j:plain")

これでマイクから音声信号が入力されているかがわかります。もし何も反応がなければマイクが認識されていません。「入力デバイスの選択」などからマイクの設定を行ってください。  
  
#### 波形が変②

マイクが認識されていても、録音されない場合があります。

それは、マイクの入力ゲインが「－∞」になっている場合です。つまり、マイクから入力された音声のボリュームが0に設定されているのです。

この設定を変更するには、**「設定」→「システム」→「サウンド」の「入力」項目**を開き、「**デバイスのプロパティ**」をクリックします。

すると、**「（マイク名）のプロパティ」画面**が現れるので、「レベル」タブを選択してください（下画像)）。  
![f:id:pythonjacascript:20181230120331j:plain](/images/ppythonjacascript2018123020181230120331.jpg "f:id:pythonjacascript:20181230120331j:plain")  
そして、「マイク」項目のスライドバーを左右に動かすことで、入力ゲインの調整ができます。  
  
  
### 参考文献：

[python - TypeError: sequence item 0: expected str instance, bytes found - Stack Overflow](https://stackoverflow.com/questions/32071536/typeerror-sequence-item-0-expected-str-instance-bytes-found)  
[PyAudio Documentation — PyAudio 0.2.11 documentation](https://people.csail.mit.edu/hubert/pyaudio/docs/)  
[Pythonで音を録音して可視化する - Qiita](https://qiita.com/mix%5Fdvd/items/adce7636e2ab33b25208)