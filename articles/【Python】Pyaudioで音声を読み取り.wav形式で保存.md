---
title: "【Python】Pyaudioで音声を読み取り.wav形式で保存"
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
* [参考文献：](#参考文献)

### 1.ライブラリのインストール

今回は、録音用にpyaudioを使用し、音声書き出し＆保存用にwaveというライブラリを使います。  
それぞれ下のコマンドをプロンプトで実行するとインストールができます。

pip install pyaudio

pip install wave

その後、Pythonの以下のスクリプトでエラーが出なければインストールは完了しています。

import pyaudio
import wave
  
  
### 2.音声を録音

以下のプログラムで音声を録音し、「sample.wav」として保存することができます。

import numpy
import wave     #wavファイルを扱うためのライブラリ
 
WAVE_OUTPUT_FILENAME = "sample.wav" #音声を保存するファイル名
iDeviceIndex = 0 #録音デバイスのインデックス番号
 
def MakeWavFile(FileName = "sample.wav", Record_Seconds = 2)
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
    wavFile = wave.open(WAVE_OUTPUT_FILENAME, 'wb')
    wavFile.setnchannels(CHANNELS)
    wavFile.setsampwidth(p.get_sample_size(FORMAT))
    wavFile.setframerate(RATE)
    wavFile.writeframes(b''.join(all)) #Python2 用
    #wavFile.writeframes(b"".join(all)) #Python3用
    
    wavFile.close()


if __name__ is "__main__":
    #WAVファイル作成, 引数は（ファイル名, 録音する秒数）
    MakeWavFile("sample.wav", Record_Seconds = 2) 
  
  
### 3.実行結果

上の.pyファイルと同じディレクトリに**「sample.wav」**ができていればOKです。  
試しに再生してみてください。音声は正常に録音できているでしょうか...？

もし何か変だな...という場合は、↓のトラブルシューティングをご覧ください。

コードの中身については、コメントを見ながら感じ取ってください。  
  
  
### トラブルシューティング

このプログラムを実行しても、エラーが出る、録音されない等の対処法です。  
こちらをご覧ください：  
[shizenkarasuzon.hatenablog.com](https://shizenkarasuzon.hatenablog.com/entry/2018/12/30/120922)  
  
  
### 参考文献：

[python - TypeError: sequence item 0: expected str instance, bytes found - Stack Overflow](https://stackoverflow.com/questions/32071536/typeerror-sequence-item-0-expected-str-instance-bytes-found)  
[PyAudio Documentation — PyAudio 0.2.11 documentation](https://people.csail.mit.edu/hubert/pyaudio/docs/)  
[Python3で録音してwavファイルに書き出すプログラム | 全人類がわかる統計学](https://to-kei.net/python/voice-record/)