---
title: "【Python】PygameでBGMの再生"
emoji: ""
type: ""
topics: []
published: false
---

Pygameを使うと、簡単に音楽ファイルを再生することができます。

* [プログラム1](#プログラム1)
* [実行結果](#実行結果)
* [解説1](#解説1)  
   * [再生：](#再生)  
   * [一時停止→再開](#一時停止再開)  
   * [再生終了](#再生終了)  
   * [フェードアウト](#フェードアウト)  
   * [音量設定](#音量設定)
* [プログラム2](#プログラム2)
* [実行結果](#実行結果-1)
* [参考文献](#参考文献)

### プログラム1

音声を再生するだけのプログラムです。

import pygame

def alarm():
    pygame.mixer.init(frequency = 44100)    # 初期設定
    pygame.mixer.music.load("sample.wav")     # 音楽ファイルの読み込み
    pygame.mixer.music.play(1)              # 音楽の再生回数(1回)
    while(1):
        a = input("Finish? --->")
        if(a is 'y'): break
    pygame.mixer.music.stop()               # 再生の終了
    return 0
    
if __name__ is "__main__":
    alarm()

### 実行結果

上のプログラムの

pygame.mixer.music.load("sample.wav")     # 音楽ファイルの読み込み

の「sample.wav」を適切なファイル名に書き換えて、↑のファイルを実行してください。

「sample.wav」が流れ始めると思います。

また、音声再生中にコマンドプロンプトに「y」を入力してください。  
すると、「sample.wav」の再生が終了します。  
  
### 解説1

**pygame.mixer**は、音声を読み込んで再生するためのpygameモジュールです。

pygame.mixerを使って音声を再生・停止etcする方法を紹介します。  
  
#### 再生：

以下のプログラムで音楽が最初から再生されます。

pygame.mixer.music.load("sample.wav")     # 音楽ファイルの読み込み
pygame.mixer.music.play()

まず、load()関数で音声ファイルを読み込み、play()関数で再生、という感じです。  
load()関数で読み込める音声データは、OGGファイルや非圧縮形式のWAVファイル、mp3ファイル等です。（**pygame-1.9.3**で確認済み）  
  
pygame.mixer.music.play(3)

のように書くと、3回同じ楽曲が繰り返し再生されます。デフォルトでは0が設定されているので繰り返し再生はありません。  
play()の引数に-1を指定すると、永遠に繰り返し再生されます。  
  
  
#### 一時停止→再開

pygame.mixer.music.unpause()

一時停止した音声を再び再生し始めます。再生開始ポイントは一時停止した個所からです。  
  
#### 再生終了

pygame.mixer.music.stop()

全ての音声の再生を終了します。  
  
#### フェードアウト

pygame.mixer.music.fadeout(ms)

音声をフェードアウトします。フェードアウトとは、だんだん音量を小さくしていった上で再生を終了することです。  
引数にはフェードアウトにかける時間（ミリ秒\[ms\]）を指定します。  
  
#### 音量設定

pygame.mixer.music.set_volume(voluime)

音量をvolumeに設定変更します。設定後に再生する全ての音声にこの音量が適用されます。引数volumeには0.0から1.0の範囲内の値を設定します。  
  
  
### プログラム2

上の音声再生・停止の方法と下の記事の内容を組み合わせて、簡単な音声再生アプリを作成しました。

# -*- coding: utf-8 -*-
from pygame.locals import *
import pygame
import sys

def main():
    pygame.init()    # Pygameを初期化
    screen = pygame.display.set_mode((300, 100))    # 画面を作成
    pygame.display.set_caption("Pygame audio app")    # タイトルを作成
    
    StartBtn = pygame.Rect(30, 30, 70, 50)
    pausBtn  = pygame.Rect(115, 30, 70, 50)
    StopBtn  = pygame.Rect(200, 30, 70, 50)
    
    #ボタン用フォントの用意  
    font = pygame.font.SysFont(None, 25)
    
    #ボタン用テキストの設定
    text1 = font.render("START", True, (0,0,0))
    text2 = font.render("PAUSE", True, (0,0,0))
    text3 = font.render("STOP",  True, (0,0,0))

    #Mixerを初期化（サンプリング周波数を設定）
    pygame.mixer.init(frequency = 44100)
    
    paused = False
    
    running = True
    #メインループ
    while running:
        screen.fill((0,0,0))  #画面を黒で塗りつぶす
        
        pygame.draw.rect(screen, (255, 0, 0), StartBtn)
        pygame.draw.rect(screen, (0, 255, 0), pausBtn)
        pygame.draw.rect(screen, (255, 0, 255), StopBtn)

        screen.blit(text1, (40, 45))
        screen.blit(text2, (125,45))
        screen.blit(text3, (205,45))

        pygame.display.update() #描画処理を実行
        for event in pygame.event.get():
            if event.type == QUIT:  # 終了イベント
                running = False
                pygame.quit()  #pygameのウィンドウを閉じる
                sys.exit() #システム終了
                
            if event.type == pygame.MOUSEBUTTONDOWN:
                if StartBtn.collidepoint(event.pos):
                    if paused:
                        pygame.mixer.music.unpause()
                    else:
                        pygame.mixer.music.load("sample.wav")     # 音楽ファイルの読み込み
                        pygame.mixer.music.play()
                    print("start")
                    
                if pausBtn.collidepoint(event.pos):
                    pygame.mixer.music.pause()
                    paused = True
                    print("pause")
                
                if StopBtn.collidepoint(event.pos):
                    paused = True
                    pygame.mixer.music.stop()
                    print("stop")

if __name__=="__main__":
    main()
  
  
### 実行結果

上のプログラムを実行すると、以下のような画面ができるはずです。  
![f:id:pythonjacascript:20190224090411j:plain](/images/ppythonjacascript2019022420190224090411.jpg "f:id:pythonjacascript:20190224090411j:plain")

そして、各ボタンを押すと、それぞれ以下のような動作をします。

* STARTボタン（赤）：再生する、一時停止から再生を再開する
* PAUSEボタン（緑）：再生を一時停止
* STOPボタン（ピンク）：再生を完全に終了

ボタンのクリックは左クリックでも右クリックでもOKです。  
  
### 参考文献

[mixer - Pygameドキュメント 日本語訳](http://westplain.sakuraweb.com/translate/pygame/Mixer.cgi)