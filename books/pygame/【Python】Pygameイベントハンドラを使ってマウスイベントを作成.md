---
title: "【Python】Pygame イベントハンドラを使ってマウスイベントを作成"
emoji: "🤖"
type: "tech"
topics: []
published: false
---

Pygameで作ったアプリケーションに、マウス入力を加えてみます。

マウスでクリックした場所を取得し、それをprintするプログラムです。

* [1.サンプルプログラム](#1サンプルプログラム)
* [2.実行結果](#2実行結果)
* [解説](#解説)  
   * [マウスイベントの種類](#マウスイベントの種類)  
   * [マウスのボタン番号](#マウスのボタン番号)
* [参考文献：](#参考文献)

### 1.サンプルプログラム

# -*- coding: utf-8 -*-

from pygame.locals import *
import pygame
import sys

def main():
    pygame.init()    # Pygameを初期化
    screen = pygame.display.set_mode((400, 330))    # 画面を作成
    pygame.display.set_caption("mouse event")    # タイトルを作成
    
    while True:        
        for event in pygame.event.get():
            if event.type == QUIT:
                pygame.quit()
                sys.exit()

            # マウスクリック時の動作
            if event.type == MOUSEBUTTONDOWN:
                x, y = event.pos
                print("mouse clicked -> (" + str(x) + ", " + str(y) + ")")
                
            # マウスポインタが移動したときの動作
            if event.type == MOUSEMOTION:
                x, y = event.pos
                #print("mouse moved   -> (" + str(x) + ", " + str(y) + ")")

        pygame.display.update()
               
if __name__=="__main__":
    main()
  
  
### 2.実行結果

上のプログラムを実行すると、真っ黒な画面が出てきます。  
![f:id:pythonjacascript:20190208180848j:plain](/images/ppythonjacascript2019020820190208180848.jpg "f:id:pythonjacascript:20190208180848j:plain")

  
また、その画面上のどこか特定の場所をクリックすると、下のような文字列が表示されるはずです。

mouse clicked -> (187, 121)
mouse clicked -> (6, 9)
mouse clicked -> (51, 55)
mouse clicked -> (45, 127)

### 解説

Pygameには、**イベントハンドラ**というものがあります。イベントハンドラとは、**イベントが発生したときに呼び出される処理**を表したものです。

今までは、イベントハンドラは QUIT（アプリケーション終了）のイベントしか使っていませんでしたが、他にもマウスイベントをはじめ、多くの種類のイベントがあります。

今回はマウスイベントを使っています。  
  
#### マウスイベントの種類

マウスイベントとは、その名の通り、ユーザーがマウスを動かしたりクリックしたりした時に発生するイベントです。

  
マウスイベントには、以下のようなものがあります。

| イベント名           | 説明               |
| --------------- | ---------------- |
| MOUSEBUTTONDOWN | マウスが押されたときに発生    |
| MOUSEBUTTONUP   | マウスボタンが離されたときに発生 |
| MOUSEMOTION     | マウスポインタが動いたとき    |

それぞれのイベントが発生すると、**eventオブジェクトのtype**に、イベントの種類が格納されます。  
ですから、

for event in pygame.event.get():       
      if event.type == MOUSEBUTTONDOWN:
           (マウスがクリックされたときの処理)

      if event.type == MOUSEMOTION:
           (マウスが動いたときの処理)

のようにfor文を使って書くことができます。  
  
  
#### マウスのボタン番号

また、MOUSEBUTTONDOWN等のイベントが発生したときには、どのボタンが押されたかを示す「ボタン番号」の情報も入っています。

| ボタン番号 | ボタンの位置       |
| ----- | ------------ |
| 1     | 左ボタン         |
| 2     | 中ボタン         |
| 3     | 左ボタン         |
| 4     | ホイールを上スクロール  |
| 5     | ホイールを下にスクロール |

マウスのボタン番号を確かめるには、以下のプログラムを実行して実際にクリックしてみて下さい。

from pygame.locals import *
import pygame
import sys

def main():
    pygame.init()    # Pygameを初期化
    screen = pygame.display.set_mode((400, 330))    # 画面を作成
    pygame.display.set_caption("mouse event")    # タイトルを作成
    
    while True:        
        for event in pygame.event.get():
            if event.type == QUIT:
                pygame.quit()
                sys.exit()
                
            # マウスクリック時の動作
            if event.type == MOUSEBUTTONDOWN:
                print("Button Number = " + str(event.button))
        pygame.display.update()

               
if __name__=="__main__":
    main()

  
ボタン番号をプログラム中で使用したい時には、

from pygame.locals import *
import pygame
import sys

pygame.init()    # Pygameを初期化
screen = pygame.display.set_mode((400, 330))    # 画面を作成
pygame.display.set_caption("mouse event")    # タイトルを作成

while True:        
    for event in pygame.event.get():
        if event.type == QUIT:
            pygame.quit()
            sys.exit()
            
        # マウスクリック時の動作
        if event.type == MOUSEBUTTONDOWN and event.button == 1:
            print("左ボタンが押された")
            
        if event.type == MOUSEBUTTONDOWN and event.button == 3:
            print("右ボタンが押された")
    pygame.display.update()

のように、**eventオブジェクトのbuttonから**値を取得します。  
  
### 参考文献：

<http://westplain.sakuraweb.com/translate/pygame/Mouse.cgi>