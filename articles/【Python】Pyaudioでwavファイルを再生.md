---
title: "【Python】Pyaudioでwavファイルを再生"
emoji: ""
type: ""
topics: []
published: false
---

**Pyaudio**と**wave**という音声関係のライブラリを使って、.wavファイルを再生してみます。  
  
### サンプルプログラム

import numpy as np
import wave 
import pyaudio
import matplotlib.pyplot as plt

def PlayWavFie(Filename = "sample.wav"):
    try:
        wf = wave.open(Filename, "r")
    except FileNotFoundError: #ファイルが存在しなかった場合
        print("[Error 404] No such file or directory: " + Filename)
        return 0
        
    # ストリームを開く
    p = pyaudio.PyAudio()
    stream = p.open(format=p.get_format_from_width(wf.getsampwidth()),
                    channels=wf.getnchannels(),
                    rate=wf.getframerate(),
                    output=True)

    # 音声を再生
    chunk = 1024
    data = wf.readframes(chunk)
    while data != '':
        stream.write(data)
        data = wf.readframes(chunk)
    stream.close()
    p.terminate()


if __name__ is "__main__":
    PlayWavFie("sample.wav") 
  
  
### 実行

上のプログラムを書き込んだ.pyファイルと同じ場所に、「**sample.wav**」というWAVファイルを作成してください。

なにの音楽を持ってたり、下の記事のプログラムを使うと、Pythonを使って録音して.wav出力することもできます。  
[shizenkarasuzon.hatenablog.com](https://shizenkarasuzon.hatenablog.com/entry/2018/12/30/122610)

  
そのうえで、プログラムを実行してください。  
「sample.wav」の音声が再生されます。