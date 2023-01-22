---
title: "【Python】waveで.wavファイルを読み込もうとするとエラーが出る"
emoji: ""
type: ""
topics: []
published: false
---

waveを使うと、たいていの.wavファイルは読み込むことができます。  
WAVファイル読み込みのプログラムはこちらです。

wr = wave.open(FileName, "r")

しかし、極稀に

Error: file does not start with RIFF id

というエラーが出て、ファイルが読み込めないことがあります。  
エラー内容を直訳すると、

エラー：.wavファイルがRIFF idから始まっていません。

となります。

この原因は、プログラムではなく、WAVファイル側にあります。

WAVファイルには大きく分けて2つのバージョンがあり、**64-bit RIFF**と**32-bit RIFF**があります。  
そして、**waveライブラリは、64bit-RIFFに対応していないのです！**

[stackoverflow.com](https://stackoverflow.com/questions/25672289/failed-to-open-file-file-wav-as-a-wav-due-to-file-does-not-start-with-riff-id)

上記エラーの原因は、64bit-RIFFのWAVファイルを読み込もうとしたために起こったのです。  
因みに、64bit-RIFFのWAVファイルは**拡張子が.w64**となっている場合もあります。

  
解決は以下の二つ。  
**・WAVのフォーマットを変更する** 
**・64bit-RIFFのWAVファイル対応のPythonライブラリを使用する。**

  
### 1.WAVファイルをフォーマットし直す

今回は、**AviUtl**を使った方法を紹介します。

**REAPERやAudacityなどの音声編集ソフトも試したのですが、64bit-RIFFのWAVは読み込めない**ようです。  
**...さすがAviUtl！！**

新規プロジェクトを作成し、「音声ファイル」をぶち込みます。  
音声ファイルを読み込むときは、**エクスプローラからドラッグするのではなく**、**「メディアオブジェクトの追加」→「音声ファイルの追加」→「ファイル参照」**で行ってください（下写真）  
![f:id:pythonjacascript:20190101124224j:plain](/images/ppythonjacascript2019010120190101124224.jpg "f:id:pythonjacascript:20190101124224j:plain")

そして、音声ファイルを読み込んだ後は、**「ファイル」→「WAV出力」でWAVファイルを出力**してください。  
![f:id:pythonjacascript:20190101124510j:plain](/images/ppythonjacascript2019010120190101124510.jpg "f:id:pythonjacascript:20190101124510j:plain")  
**「オーディオ圧縮」は「PCM 44.1ｋHz」**を選択してください。

これで、32bit-RIFFを作成することができます。  
作成したファイルをPythonで読み込んでみてください。うまくいくはずです。

雑な説明ですみません。  
AviUtlの扱いに慣れてない方は、次の2つ目の解決策をお勧めします。  
  
### 1-2.wavのフォーマットを変更する（専用アプリを使う）

もう一つ、解決策を見つけました。  
それは、**FFmpeg**を使用する方法です。  
[FFmpeg](https://www.ffmpeg.org/)

FFmpegとは、動画と音声を記録・変換・再生するためのフリーソフトウェアで広く使われています。

インストールの方法は、  
[Download FFmpeg](https://www.ffmpeg.org/download.html)からインストールするか、

apt-get install ffmpeg

の2種類だと思います。

インストールが完了したら、

ffmpeg -i （変換したいWAVファイル） out_test7.wav

で32bitWAVに変換、という感じです。

参考：  
<https://blog.csdn.net/appleyuchi/article/details/78486179>
  
  
### 2.64bit-RIFFのWAVファイル対応のPythonライブラリを使用

現状（2019.01.01）、そのようなライブラリはなさそうです。

随時更新していく予定です。