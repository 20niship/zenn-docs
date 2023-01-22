---
title: "【Python】Pyaudioで.wavファイルを読み込みグラフに表示（FFTもやってみた）"
emoji: ""
type: ""
topics: []
published: false
---

今回は、Pythonを使って.wavファイルの波形表示をしてみます。

### サンプルプログラム

import numpy as npimport waveimport pyaudioimport matplotlib.pyplot as pltdef ReadWavFile(FileName = "sample.wav"):try: wr = wave.open(FileName, "r")except FileNotFoundError: #ファイルが存在しなかった場合 print("\[Error 404\] No such file or directory: " \+ FileName)return 0 data = wr.readframes(wr.getnframes()) wr.close() x = np.frombuffer(data, dtype="int16") / float((2^15))#音声波形表示 plt.figure(figsize=(15,3)) plt.plot(x) plt.show()#