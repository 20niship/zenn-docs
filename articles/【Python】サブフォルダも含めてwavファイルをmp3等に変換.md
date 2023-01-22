---
title: "【Python】サブフォルダも含めてwavファイルをmp3等に変換"
emoji: ""
type: ""
topics: []
published: false
---

* [この記事の内容](#この記事の内容)
* [必要なライブラリ](#必要なライブラリ)
* [スクリプト](#スクリプト)
* [解説](#解説)

### この記事の内容

以前は、音声のフォーマット（拡張子）を一括変換する場合、「[Any Video Converter](https://jp.any-video-converter.com/any-video-converter-free.php)」というアプリを使っていましたが、変換が完了する度に毎度表示される広告がうっとうしくなり、自分でプログラムを書こうと決めました。

  
ということで、今回は**音声ファイルの拡張子を一括で変換**するスクリプトです。  
wav→oggファイル、wav↔mp3、wma↔mp3ファイルなどに変換できます。  
  
  
### 必要なライブラリ

このスクリプトでは[ffmpeg](https://ffmpeg.org/)を使って拡張子変換を行っています。よって、事前にffmpegをインストールしておく必要があります。

  
ffmpegのインストール方法は以下を参考にしてください。  
[shizenkarasuzon.hatenablog.com](https://shizenkarasuzon.hatenablog.com/entry/2019/02/09/085419)

pip環境があれば、以下のコマンドを叩いてもインストールできます。

pip install ffmpeg-python

  
他にも、**glob**というフォルダ管理ライブラリや、**subprocess**というライブラリを使用しますが、こちらは標準でインストールされています。

もし入っていなかったら

pip install glob

のようなコマンドを実行してください。  
  
  
### スクリプト

以下、WAVファイルとMP3ファイルをOGGファイルに変換するプログラムです。

変換後は、前のファイル（WAVとMP3）は削除されます。  
（↓のプログラムで、**os.remove()**関数の部分を削除すれば、元データの削除は行われなくなります）

\# -\*- coding: utf-8 -\*- import globimport osimport subprocess name\_lists2 = glob.glob("./\*\*", recursive=True)print("the number of music files: " \+ str(len(name\_lists2))) cmd1 = "ffmpeg -i \\""cmd2 = "\\" \-vn -ac 2 -ar 44100 -ab 128k -acodec libvorbis -f ogg \\""cmd3 = "\\""root = os.path.abspath("./") + "/" for i in name\_lists2:print(i)if "wav" in i: i2 = root + i path2 = i2.replace("wav", "ogg")print(cmd1 + i2 + cmd2 + path2 + cmd3) subprocess.call(cmd1 + i2 + cmd2 + path2 + cmd3) os.remove(i2)elif "mp3" in i: i2 = root + i path2 = i2.replace("mp3", "ogg")print(cmd1 + i2 + cmd2 + path2 + cmd3) subprocess.call(cmd1 + i2 + cmd2 + path2 + cmd3) os.remove(i2)else:print(" ##### NO FILE ##### -> " \+ i)print("