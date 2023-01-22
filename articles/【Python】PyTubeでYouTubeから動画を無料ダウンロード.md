---
title: "【Python】PyTubeでYouTubeから動画を無料ダウンロード"
emoji: ""
type: ""
topics: []
published: false
---

さすがPythonです。

YouTube動画をPythonを使ってダウンロードする方法です。

* [インストール](#インストール)
* [サンプルプログラム](#サンプルプログラム)
* [実行結果](#実行結果)
* [エラーについて](#エラーについて)

### インストール

PyTubeをダウンロードします。  
以下の一行をコマンドプロンプトでダウンロードしてください。

pip install pytube
  
  
### サンプルプログラム

以下のプログラムを実行してください。

from pytube import YouTube
yt = YouTube('https://youtu.be/-sUXMzkh-jI')#動画のURLをここにコピー
stream = yt.streams.first()
finished = stream.download()

### 実行結果

上のプログラムを.pyファイルを作って実行すると、  
.pyファイルのディレクトリに「**ITS A LONG WAY TO THE TOP (IF YOU WANNA ROCK N ROLL) - AC DC.mp4**」という動画が保存されます。  
これは、Youtubeの動画<https://youtu.be/-sUXMzkh-jI>からダウンロードしたものです。  
  
  
### エラーについて

上のプログラムを実行していると、

yt = YouTube(’URLをここに入力')

次のようなエラーに遭遇することがあります。  
  
  File "C:\Users\Owner\Anaconda3\Lib\site-packages\pytube\cipher.py", line 250, in get_signature
    tplan = get_transform_plan(js)

  File "Anaconda3\Lib\site-packages\pytube\cipher.py", line 72, in get_transform_plan
    return regex_search(pattern, js, group=1).split(';')

  File "Anaconda3\Lib\site-packages\pytube\helpers.py", line 65, in regex_search
    .format(pattern=pattern),

RegexMatchError: regex pattern (encodeURIComponent=function\(\w\){[a-z=\.\(\"\)]*;(.*);(?:.+)}) had zero matches

このRogexErrorはURLによって発生するものと発生しないものがあるようです。  
ここらへんで議論されているので見てください。  
[github.com](https://github.com/nficano/pytube/issues/312)
  
  
どうしてもエラーが解決しない場合は、「**youtube-dl**」という別のライブラリも存在したので、そちらを使ってみて下さい。  
[shizenkarasuzon.hatenablog.com](https://shizenkarasuzon.hatenablog.com/entry/2019/02/03/123545)

今のところ、youtbe-dlは全くエラーが出ていません。