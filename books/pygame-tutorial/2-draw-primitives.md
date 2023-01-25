---
title: "【Python】Pygameで図形を描画する"
emoji: "🤖"
type: "tech"
topics: []
published: false
---

前回、Pygameを使って画面上に文字を描画するプログラムを作成しました。

[shizenkarasuzon.hatenablog.com](https://shizenkarasuzon.hatenablog.com/entry/2018/12/29/203344)  
![f:id:pythonjacascript:20181229192049j:plain:h300](/images/ppythonjacascript2018122920181229192049.jpg "f:id:pythonjacascript:20181229192049j:plain:h300")

今回は、このプログラムに図形を描画するプログラムを追加します。

* [サンプルプログラム：](#サンプルプログラム)
* [実行結果：](#実行結果)
* [簡単に解説](#簡単に解説)
* [四角形（矩形）を描画](#四角形矩形を描画)
* [四角形（矩形）を描画（枠線なし&高速）](#四角形矩形を描画枠線なし高速)
* [円を描画](#円を描画)
* [楕円を描画](#楕円を描画)
* [多角形を描画](#多角形を描画)

### サンプルプログラム：

以下のプログラムを実行してください。

from pygame.locals import \*import pygameimport sysdef main(): pygame.init() \# Pygameを初期化 screen = pygame.display.set\_mode((400, 330)) \# 画面を作成 pygame.display.set\_caption("S.C.MAGI") \# タイトルを作成 #テキストの設定については前回の記事をご覧ください。 font1 = pygame.font.SysFont("hg正楷書体pro", 150) font2 = pygame.font.SysFont(None, 50) text1 = font1.render("警告", True, (255,0,0)) text2 = font2.render("EMERGENCY", True, (255,0,0)) running = True #メインループ while running: screen.fill((0,0,0)) #画面を黒で塗りつぶす \# 図形を描画 #