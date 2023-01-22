---
title: "【Python】numpyで音声をフーリエ変換し、結果を画像として表示"
emoji: ""
type: ""
topics: []
published: false
---

* [この記事の内容](#この記事の内容)
* [サンプルプログラム](#サンプルプログラム)
* [実行結果](#実行結果)
* [解説：](#解説)  
   * [窓関数とは](#窓関数とは)  
   * [窓関数をかける](#窓関数をかける)  
   * [窓の位置をスライド](#窓の位置をスライド)
* [参考、上画像の音声データについて](#参考上画像の音声データについて)

### この記事の内容

前回、.wavファイルをフーリエ変換するプログラムをアップしました。

[shizenkarasuzon.hatenablog.com](https://shizenkarasuzon.hatenablog.com/entry/2018/12/31/150340)

上のサイトでは、.wavファイル全体を一つの音声データの集合とみて、それをフーリエ変換しています。

ですが、**今回は音声ファイルを約23msごとに分割し、その一つ一つについてフーリエ変換をしてみます。**

また、**その結果をmatplotlibを使って可視化**してみました。  
  
### サンプルプログラム

こちらの方のプログラムを大変参考にさせて頂きました。  
[numpyでスペクトログラムによる音楽信号の可視化 - Qiita](https://qiita.com/namaozi/items/dec1575cd455c746f597)  

#coding:utf-8
import numpy as np
import matplotlib.pyplot as plt
import wave

def ReadWavFile(FileName = "sample.wav"):
    """
    wavファイルを読み込み、配列として返す
    """
    try:
        wr = wave.open(FileName, "r")
    except FileNotFoundError: #ファイルが存在しなかった場合
        print("[Error 404] No such file or directory: " + FileName)
        return 0
    data = wr.readframes(wr.getnframes())
    wr.close()
    x = np.frombuffer(data, dtype="int16") / float(2**15)
    return x


sampling_rate = 44100 #サンプリングレート
data = np.array(ReadWavFile("sample.wav")) #wavデータを配列で保存

NFFT = 1024 # フレームの大きさ
OVERLAP = NFFT / 2 # 窓をずらした時のフレームの重なり具合. half shiftが一般的らしい
frame_length = data.shape[0] #全フレーム数
split_number = len(np.arange((NFFT / 2), frame_length, (NFFT - OVERLAP))) #楽曲の分割数

window = np.hamming(NFFT)  #窓関数

spec = np.zeros([split_number, NFFT // 4]) #転置状態で定義初期化
#NFFT // 4はPython3用です。int(NFET / 4)と同じ意味です。

pos = 0

for fft_index in range(split_number):
    frame = data[int(pos):int(pos+NFFT)]
    if len(frame) == NFFT:
        windowed = window * frame  #窓関数をかける
        fft_result = np.fft.rfft(windowed)
         # real()関数で虚部を削除して、さらに高周波をカット（複素共役による鏡像のため不要）
        fft_data2 = np.real(fft_result[:int(len(fft_result)/2)]) 
        fft_data2 = np.log(fft_data2** 2)  # グラフで見やすくするために対数をとります

        for i in range(len(spec[fft_index])):
            spec[fft_index][-i-1] = fft_data2[i]

        pos += (NFFT - OVERLAP) #窓をずらす

# プロット
plt.imshow(spec.T, extent=[0, frame_length, 0, sampling_rate/2], aspect="auto")
plt.xlabel("time[s]")
plt.ylabel("frequency[Hz]")
plt.colorbar()
plt.show()

以下のプログラムを実行する前に、「**sample.wav**」というファイルを作成して、.pyファイルと同じディレクトリに入れてください。  
  
  
### 実行結果

このような画像が表示されれば正常にフーリエ変換ができています。  
![f:id:pythonjacascript:20190101015402j:plain:h300](/images/ppythonjacascript2019010120190101015402.jpg "f:id:pythonjacascript:20190101015402j:plain:h300")  
  
  
### 解説：

全体をフーリエ変換する方法は、  
[shizenkarasuzon.hatenablog.com](https://shizenkarasuzon.hatenablog.com/entry/2018/12/31/150340)  
で説明しました。

ですが、**音声を細かく分割してそれぞれをフーリエ変換**する場合、変換前にもう1STEP必要になります。  
それが「窓関数をかける」というものです。  
  
#### 窓関数とは

**窓関数（ハミング窓）**とは、以下の式で表されます。  
![f:id:pythonjacascript:20190101021022j:plain](/images/ppythonjacascript2019010120190101021022.jpg "f:id:pythonjacascript:20190101021022j:plain")（Wikipediaより）

グラフで書くと、こんな感じです。  
![f:id:pythonjacascript:20190101020708j:plain](/images/ppythonjacascript2019010120190101020708.jpg "f:id:pythonjacascript:20190101020708j:plain")

  
窓関数描画のプログラム

import numpy as np
import matplotlib.pyplot as plt
window = np.hamming(100)
plt.plot(window)
plt.show()

もともとあった数列に上のグラフをかけることで、数列の特定の部分だけの数値を取り出し、区間外の数値を0にすることができます。  
このように、**特定の数値を取り出す**ことができる「窓」があるので「**窓関数**」と呼ばれています。  
  
  
#### 窓関数をかける

音声のサンプリングデータの配列に、この窓関数を書けることによって、特定の一部の音声データを取り出すことができます。  
また、窓関数をｘ軸方向に平行移動することで、取り出す音声データの場所も変更することができます。

このようにして、一部の音声をフーリエ変換しているのです。

その部分のプログラムは以下になります。

window = np.hamming(NFFT)  #窓関数
(中略）
for fft_index in range(split_number):
　　（中略）
        windowed = window * frame  #窓関数をかける
  
  
#### 窓の位置をスライド

上で書いたように、取り出す音声データの箇所を変更するには、窓関数を移動させる必要があります。  
それを、今回のプログラムでは、反対に音声データの窓関数で取り出される部分のデータのみを取り出して（したプログラム）

for fft_index in range(split_number):
    frame = data[int(pos):int(pos+NFFT)]
    (中略）
    pos += (NFFT - OVERLAP) #窓をずらす（正確には窓を当てる部分の配列の先頭位置をずらす）

その一部分に窓関数をかける、という実装をしています。

  windowed = window * frame  #窓関数をかける

最後に、**windowed**をフーリエ変換して完成です。  
  
  
### 参考、上画像の音声データについて

上の画像を生成した音声データ「sample.wav」ですが、かの有名な「**エンダァーーーイヤァーーー**」を使用しています。**Whitney Houstonの『 I Will Always Love You』**のさびです。

  
その部分だけAviUtlで切り取って**「ファイル」→「WAV出力」**でsample.wavを作成しています。  
![f:id:pythonjacascript:20190101015942j:plain](/images/ppythonjacascript2019010120190101015942.jpg "f:id:pythonjacascript:20190101015942j:plain")

  
なるべく単純な音声を使って実験したほうが結果がわかりやすくなります。  
電子音の和音とか、肉声とかが良いと思います。

いろんな楽器が入った音声ファイルを使うと、多くの周波数成分を読み取ってしまうため、グラフがそれっぽくなりません。