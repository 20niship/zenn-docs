---
title: "【Python】Pyaudioまとめ編  音声の録音 &amp; 保存 &amp; 再生 &amp; 波形表示 &amp; フーリエ変換"
emoji: ""
type: ""
topics: []
published: false
---

**pyaudio**というライブラリを使うと、**音声を録音・再生・保存**etc.することができます。

この記事では、音声の録音に挑戦してみます。また、録音した音声をmatplotlibを使ってグラフに表示してみます。

* [1.ライブラリのインストール](#1ライブラリのインストール)
* [使い方](#使い方)
* [サンプルプログラム](#サンプルプログラム)

### 1.ライブラリのインストール

以下のコマンドをプロンプトで実行します。

pip install pyaudio

その後、Pythonの以下のスクリプトでエラーが出なければインストールは完了しています。

import pyaudio

### 使い方

以下のサイトをご覧ください。

録音する：  
[shizenkarasuzon.hatenablog.com](https://shizenkarasuzon.hatenablog.com/entry/2018/12/30/120922)

音声を録音＆保存する：  
[shizenkarasuzon.hatenablog.com](https://shizenkarasuzon.hatenablog.com/entry/2018/12/30/122610)

再生：  
[shizenkarasuzon.hatenablog.com](https://shizenkarasuzon.hatenablog.com/entry/2018/12/31/145510)

波形表示＆高速フーリエ変換  
[shizenkarasuzon.hatenablog.com](https://shizenkarasuzon.hatenablog.com/entry/2018/12/31/150340)  
  
  
### サンプルプログラム

上の4つをまとめるとこのようになります。

import numpy as np
import wave     #wavファイルを扱うためのライブラリ
import pyaudio
import matplotlib.pyplot as plt

 
def MakeWavFile(FileName = "sample.wav", Record_Seconds = 2, save = True):
    """
    録音して、波形表示
    """
    chunk = 1024
    FORMAT = pyaudio.paInt16
    
    CHANNELS = 1 #モノラル
    RATE = 44100 #サンプルレート（録音の音質）
    
    p = pyaudio.PyAudio()
    
    stream = p.open(format = FORMAT,
                    channels = CHANNELS,
                    rate = RATE,
                    input = True,
                    frames_per_buffer = chunk)
    
    #レコード開始
    print("Now Recording...")
    all = []
    for i in range(0, int(RATE / chunk * Record_Seconds)):
        data = stream.read(chunk) #音声を読み取って、
        all.append(data) #データを追加
    
    #レコード終了
    print("Finished Recording.")
    
    stream.close()
    p.terminate()
    
    #録音したデータを配列に変換
    #data = ''.join(all) #Python2用
    data = b"".join(all) #Python3用
    result = np.frombuffer(data,dtype="int16") / float(2**15)
    plt.plot(result)
    plt.show()
    
    if(save): #保存するか？
        wavFile = wave.open(FileName, 'wb')
        wavFile.setnchannels(CHANNELS)
        wavFile.setsampwidth(p.get_sample_size(FORMAT))
        wavFile.setframerate(RATE)
        wavFile.writeframes(b''.join(all)) #Python2 用
        #wavFile.writeframes(b"".join(all)) #Python3用
        wavFile.close()

    
def ReadWavFile(FileName = "sample.wav"):
    """
    wavファイルを読み込み、波形表示＆高速降りえ変換（FFT）
    """
    try:
        wr = wave.open(FileName, "r")
    except FileNotFoundError: #ファイルが存在しなかった場合
        print("[Error 404] No such file or directory: " + FileName)
        return 0
    data = wr.readframes(wr.getnframes())
    wr.close()
    x = np.frombuffer(data, dtype="int16") / float(2**15)

    plt.figure(figsize=(15,3))
    plt.plot(x)
    plt.show()
    
    x = np.fft.fft(np.frombuffer(data, dtype="int16"))
    plt.figure(figsize=(15,3))
    plt.plot(x.real[:int(len(x)/2)])
    plt.show()
    
    
def PlayWavFie(Filename = "sample.wav"):
    """
    wavファイルを再生
    """
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

    # チャンク単位でストリームに出力し音声を再生
    chunk = 1024
    data = wf.readframes(chunk)
    while data != '':
        stream.write(data)
        data = wf.readframes(chunk)
    stream.close()
    p.terminate()
    

if __name__ is "__main__":
    #WAVファイル作成, 引数は（ファイル名, 録音する秒数）
    MakeWavFile("sample.wav", Record_Seconds = 2)
    ReadWavFile("sample.wav") 