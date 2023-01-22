---
title: "【Python】Pygameで画像を描画"
emoji: ""
type: ""
topics: []
published: false
---

今回は、Pygameを使って画像を描画するプログラムを書いてみました。

* [サンプルプログラム](#サンプルプログラム)
* [実行結果](#実行結果)
* [解説](#解説)  
   * [対応フォーマット（拡張子）](#対応フォーマット拡張子)  
   * [概要](#概要)  
   * [特定の色を透明化する](#特定の色を透明化する)  
   * [画像を拡大・縮小する](#画像を拡大縮小する)

### サンプルプログラム

from pygame.locals import \*import pygameimport sysdef main(): pygame.init() \# Pygameを初期化 screen = pygame.display.set\_mode((400, 330)) \# 画面を作成 pygame.display.set\_caption("Pygame sample app") \# タイトルを作成 running = True #メインループ while running: screen.fill((0,0,0)) #画面を黒で塗りつぶす \# 画像を描画 #