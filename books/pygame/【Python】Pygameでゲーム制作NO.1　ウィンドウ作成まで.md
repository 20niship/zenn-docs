---
title: "【Python】Pygameでゲーム制作NO.1　ウィンドウ作成まで"
emoji: "🤖"
type: "tech"
topics: []
published: false
---

Pythonでゲーム制作をするのに最も適したライブラリとして、**Pygame**があります。

この記事では、Pygameのインストールからウィンドウ表示までを行ってみます。  
  
* [Pygameとは？](#Pygameとは)
* [インストール](#インストール)
* [Windowの表示](#Windowの表示)
* [解説](#解説)  
   * [（1）インポート](#1インポート)  
   * [（2）初期設定](#2初期設定)  
   * [（3）メインループ](#3メインループ)  
   * [（4）イベント処理](#4イベント処理)
* [作成したAppが（応答なし）になった時](#作成したAppが応答なしになった時)

### Pygameとは？

Pygameとは、Pythonでゲーム開発をするためのライブラリです。  
<https://www.pygame.org/news>

「ゲーム」と言っても、3Dの市場に出回っているレベルのものを製作することは難しく、Pygameでできるのは簡単な**2Dゲーム**です。

では、早速使っていきましょう。  
  
### インストール

pipで以下のコマンドを実行します。

pip install pygame

以上です。

もし、この方法でできなかった場合は、  
<https://www.lfd.uci.edu/~gohlke/pythonlibs/>  
このサイトから、適切なバージョンのPygameのwhlファイルをダウンロードして、それを実行するとインストールできます。  
↑のサイトはPythonのほぼすべてのライブラリを扱っており、Pygame以外でも何かライブラリをダウンロードしたい時によく使います。  
  
### Windowの表示

Pygameを使用して、簡単なウィンドウを作ってみます。  
以下のプログラムを実行してください。

import pygame
import sys

def main():
    pygame.init()    # Pygameを初期化
    screen = pygame.display.set_mode((640, 480))    # 画面を作成
    pygame.display.set_caption("Hello, World")    # タイトルを作成

    running = True
    #メインループ
    while running:
        screen.fill((0,0,0))  #画面を黒で塗りつぶす
        pygame.display.update() #描画処理を実行
        
        for event in pygame.event.get():
            if event.type == QUIT:  # 終了イベント
                running = False
                pygame.quit()  #pygameのウィンドウを閉じる
                sys.exit() #システム終了
                
     
if __name__=="__main__":
    main()

  
このような画面が現れれば成功です。  
![f:id:pythonjacascript:20181218211525j:plain:h350](/images/ppythonjacascript2018121820181218211525.jpg "f:id:pythonjacascript:20181218211525j:plain:h350")  
  
  
### 解説

簡単に上のプログラムの解説を行います。

#### （1）インポート

Pygameとsysをインポートします。sysはpygameを終了するときに使用します。

import pygame
import sys
  
  
#### （2）初期設定

まず、

    pygame.init()    # Pygameを初期化

でPygameを初期化します。Pygameを使ってアプリケーションを作るときには、最初にこの文を書きます。  
  
  
#### （3）メインループ

以下がメインループの構造です。

running = True
while running:
    (ループ処理)
      if event.type == QUIT:  # 終了イベント
           running = False
           pygame.quit()  #pygameのウィンドウを閉じる
           sys.exit() #システム終了

while文の中身をrunningがTrueの間ずっと繰り返します。

screen.fill((0,0,0))  #画面を黒で塗りつぶす
pygame.display.update() #描画処理を実行

この2行でウィンドウの描画処理を行っています。それぞれの関数の意味はコメントに書いてある通りです。  
  
  
#### （4）イベント処理

キーボードから何か入力されたりマウスがクリックされて、その入力に対応した特有の処理が行われることを**イベント**といいます。

pygame.event.get():

この関数の戻り値はそのイベント（のリスト）を示します。  
そして、for文で受信したイベントの内容を1つずつ調べ、if文で判定します。

そして、イベント内容が終了イベントであれば、

event.type == QUIT

がTrueとなり、

running = False
pygame.quit()  #pygameのウィンドウを閉じる
sys.exit() #システム終了

の3行が実行されます。

※終了イベントとは、ウィンドウ右上のバツ印が押されたり、ESCキーが押されたときに発生する、アプリケーションを終了するイベントです。  
  
### 作成したAppが（応答なし）になった時

Pygameで作成したプログラムにミスがあり、それに気づかずに実行してしまうと、（応答なし）と表示されてアプリケーションがフリーズしてしまうことがよくあります。  
こうなると、キーボードやマウスでアプリケーションを終了することができません。

そういう時は、**タスクマネージャ**を開いて強制終了しましょう。

方法は、タスクマネージャを「**Ctrl + Shift + ESC**」で開くと、以下のように「**Python（応答なし）**」と表示されているので、それを選択して、右下の「**タスクの終了**」ボタンを押すだけです。  
![f:id:pythonjacascript:20181218221222j:plain](/images/ppythonjacascript2018121820181218221222.jpg "f:id:pythonjacascript:20181218221222j:plain")