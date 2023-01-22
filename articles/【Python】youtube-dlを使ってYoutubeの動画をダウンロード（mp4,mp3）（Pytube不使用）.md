---
title: "【Python】youtube-dlを使ってYoutubeの動画をダウンロード（mp4, mp3）（Pytube不使用）"
emoji: ""
type: ""
topics: []
published: false
---

* [前書き](#前書き)
* [この記事の内容](#この記事の内容)
* [1.インストール](#1インストール)
* [2.cmdからダウンロード（mp4の動画）](#2cmdからダウンロードmp4の動画)
* [3.cmdからダウンロード（mp3の音声）](#3cmdからダウンロードmp3の音声)
* [4.Pythonスクリプトでダウンロード(動画）](#4Pythonスクリプトでダウンロード動画)
* [4.Pythonスクリプトでダウンロード(音声）](#4Pythonスクリプトでダウンロード音声)
* [追記（問題と解決策）](#追記問題と解決策)
* [参考文献](#参考文献)

### 前書き

前回、PyTubeを使ってYouTube動画をダウンロードするコードを紹介しました。  
[shizenkarasuzon.hatenablog.com](https://shizenkarasuzon.hatenablog.com/entry/2019/02/03/114236)

しかし、このコードは「**RegexMatchError**」というエラーが多発し、使い物になりませんでした。

そこで、他にYoutubeの動画をダウンロードするためのライブラリがないかな～？と探していたところ、

  
ありました！！  
[github.com](https://github.com/rg3/youtube-dl/blob/master/README.md)  
では、早速使っていきます！  
  
  
### この記事の内容

「DA BUMP」の「USA」をyoutube-dlを使ってダウンロードします。  
[YouTube](https://www.youtube.com/watch?v=sr--GVIoluU)

コマンドプロンプトを使う方法と、Pythonを使う方法の2通りを解説します。  
また、↑の動画をそのまま動画としてmp4で保存する方法と、音声のみ抽出してmp3で保存する方法を紹介します。  
  
  
### 1.インストール

以下のpipコマンドをプロンプトで実行

pip install youtube-dl
  
  
### 2.cmdからダウンロード（mp4の動画）

まず、手始めにコマンドプロンプトを使ってYouTube動画を保存してみます。

以下のコマンドを実行してください。

youtube-dl https://www.youtube.com/watch?v=sr--GVIoluU

ちょっと待っていると、このように表示されて、

[youtube] sr--GVIoluU: Downloading webpage
[youtube] sr--GVIoluU: Downloading video info webpage
[download] Destination: DA PUMP _ U.S.A.-sr--GVIoluU.mp4
[download] 100% of 18.45MiB in 00:09

現在のディレクトリに「**DA PUMP \_ U.S.A.-sr--GVIoluU.mp4**」という名前で、YouTube動画が**mp4形式でダウンロード**されます！

  
このように、mp4でダウンロードするときは、

youtube-dl 動画のURL

だけでダウンロード完了です！  
  
### 3.cmdからダウンロード（mp3の音声）

では、音声のみダウンロードしてmp3形式で保存するにはどうしたらいいのか。

以下のコマンドを実行してください。

youtube-dl --extract-audio https://www.youtube.com/watch?v=sr--GVIoluU

mp3形式で音声のみダウンロードしたい場合は、「youtube-dl」とURLの間に「 **\--audio-format mp3** 」と入力してください。

なお、mp3ダウンロードには「ffmpeg」が必要になります。  
ffmpegをインストールしていないと、以下のようなエラーが出るので、

ERROR: ffprobe/avprobe and ffmpeg/avconv not found. Please install one.

上のエラーが出た場合は、ffmpegをインストールしてください。  
[shizenkarasuzon.hatenablog.com](https://shizenkarasuzon.hatenablog.com/entry/2019/02/09/085419)  
  
  
### 4.Pythonスクリプトでダウンロード(動画）

上の2つは、コマンドプロンプトからのダウンロードでしたが、もちろん.pyファイルを使ってダウンロードすることもできます。

以下のPythonプログラムを実行してください。

ydl = youtube_dl.YoutubeDL({'outtmpl': '%(id)s%(ext)s','format':'137'})

with ydl:
    result = ydl.extract_info(
        'https://www.youtube.com/watch?v=sr--GVIoluU',
        download=True # We just want to extract the info
    )

  
実行すると、

[youtube] sr--GVIoluU: Downloading webpage
[youtube] sr--GVIoluU: Downloading video info webpage
[download] Destination: sr--GVIoluUmp4
[download] 100% of 90.16MiB in 00:29 

このように表示されて、上の.pyスクリプトファイルと同じディレクトリに「**sr--GVIoluU.mp4**」というファイルが作成されているはずです。

※一部の環境ではファイル名が「**sr--GVIoluUmp4**」となって拡張子がなくなっていることもありましたが、ファイル名の変更で「**sr--GVIoluU.mp4**」に変えてあげると、無事に再生できました。  
  
  
### 4.Pythonスクリプトでダウンロード(音声）

以下のプログラムを実行してください

import youtube_dl

ydl_opts = {
    'format': 'bestaudio/best',
    'outtmpl':  "sample_music" + '.%(ext)s',
    'postprocessors': [
        {'key': 'FFmpegExtractAudio',
        'preferredcodec': 'mp3',
         'preferredquality': '192'},
        {'key': 'FFmpegMetadata'},
    ],
}

ydl = youtube_dl.YoutubeDL(ydl_opts)
info_dict = ydl.extract_info("https://www.youtube.com/watch?v=sr--GVIoluU", download=True)

YouTubeの動画の音声のみがmp3ファイルで保存されます。

mp3で保存するときには**ffmpeg**という別のライブラリが必要になります。

「ffmpegがないよ！！」という趣旨のエラーが出るときは、こちらを参考にインストールをお願いします。  
[shizenkarasuzon.hatenablog.com](https://shizenkarasuzon.hatenablog.com/entry/2019/02/09/085419)  
  
  
### 追記（問題と解決策）

・youtube-dlを使っていると、

WARNING: Unable to extract video title
ERROR: This video is unavailable.

のようなエラーが出ることがあります。これは、古いバージョンのyoutube-dlがGoogle側の更新によって使えなくなったことが原因で、

pip upgrade youtube-dl

をすることで解決します。  
現在（2020/02/08）、youtube-dlの version 2020.01.24 は問題なく動作しています。

（youtube-dl のバージョン確認は「**youtube-dl --version**」で行いました）  
  
  
### 参考文献

[stackoverflow.com](https://stackoverflow.com/questions/44870658/how-to-extract-all-mp4-with-youtube-dl-in-python)