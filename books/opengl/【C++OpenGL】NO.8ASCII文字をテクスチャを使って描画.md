---
title: "【C++/OpenGL】NO.8 ASCII文字をテクスチャを使って描画"
emoji: ""
type: ""
topics: []
published: false
---

前回の記事で、配列からテクスチャを生成し(≧▽≦)マークを描画する、という事をしました。  
[shizenkarasuzon.hatenablog.com](https://shizenkarasuzon.hatenablog.com/entry/2020/06/09/121330)  
  
今回は、これを使って文字列を描画してみます。

* [準備（配列作成）](#準備配列作成)
* [サンプルプログラム](#サンプルプログラム)
* [実行結果](#実行結果)
* [解説](#解説)

### 準備（配列作成）

文字をテクスチャとして描画するには、前回のプログラムのように、文字列のテクスチャを配列化する必要があります。  
（[このよう](https://shizenkarasuzon.hatenablog.com/entry/2020/06/05/120502)に直接画像ファイルから読み込む方法もありますが、pngファイルの透明度読み込みに苦戦しておりまだ解決していないので、とりあえず配列を作成する方法でいきます）

今回テクスチャは↓の画像を使います  
![f:id:pythonjacascript:20200617010942j:plain](/images/ppythonjacascript2020061720200617010942.jpg "f:id:pythonjacascript:20200617010942j:plain")  
（サイズは512\*512）  
この画像データを配列に変換する必要があります

で、以下のようなPythonプログラムを実行します

\# -\*-coding:utf-8-\*- import cv2import numpy as npdef CreateFont():\# ビットマップフォントの画像ファイルを読み込む img = cv2.imread("font.bmp", 0)print(" 