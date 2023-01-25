---
title: "【Python】Pygameでボタン作成"
emoji: "🤖"
type: "tech"
topics: []
published: false
---

Pygameには、ボタンオブジェクトがありません。  
なので、長方形を描画するときに使う**Rect**オブジェクトを利用して、ボタンもどきを作成してみました。  
  
### サンプルプログラム

from pygame.locals import *
import pygame
import sys

def main():
    pygame.init()    # Pygameを初期化
    screen = pygame.display.set_mode((200, 100))    # 画面を作成
    pygame.display.set_caption("Pygame sample app")    # タイトルを作成
    
    button = pygame.Rect(30, 30, 50, 50)  # creates a rect object
    button2 = pygame.Rect(100, 30, 70, 50)  # creates a rect object

    #STEP1.フォントの用意  
    font = pygame.font.SysFont(None, 25)
    
    #STEP2.テキストの設定
    text1 = font.render("RED", True, (0,0,0))
    text2 = font.render("GREEN", True, (0,0,0))
    
    
    running = True
    #メインループ
    while running:
        screen.fill((0,0,0))  #画面を黒で塗りつぶす
        
        pygame.draw.rect(screen, (255, 0, 0), button)
        pygame.draw.rect(screen, (0, 255, 0), button2)

        screen.blit(text1, (40, 45))
        screen.blit(text2, (105,45))

        pygame.display.update() #描画処理を実行
        for event in pygame.event.get():
            if event.type == QUIT:  # 終了イベント
                running = False
                pygame.quit()  #pygameのウィンドウを閉じる
                sys.exit() #システム終了
                
            if event.type == pygame.MOUSEBUTTONDOWN:
                if button.collidepoint(event.pos):
                    print("red button was pressed")
                if button2.collidepoint(event.pos):
                    print("green button was pressed")
                    
                    
if __name__=="__main__":
    main()
  
  
### 実行結果

上のプログラムを実行すると、このような画面が表示されます。  
![f:id:pythonjacascript:20190222235156j:plain](/images/ppythonjacascript2019022220190222235156.jpg "f:id:pythonjacascript:20190222235156j:plain")

  
また、赤いボタンを押すと、

red button was pressed

と表示され、緑のボタンを押すと、

green button was pressed

とコンソール上に表示されます。  
  
### 解説

Pygameにボタンオブジェクトのようなものは存在しないため、Rectを使って描画します。

button = pygame.Rect(30, 30, 50, 50)  # creates a rect object

**Rect**とは長方形のオブジェクトで、このように書くことで作成することができます。

引数は順番に長方形の**左上の頂点ｘ座標、左上ｙ座標、横幅（px）、高さ（px）**の順で指定します。
  
  
また、Rectオブジェクトの描画には、**pygame.draw.rect()**関数を使います。

pygame.draw.rect(screen, (255, 0, 0), button)

第二引数は、描画するときにの色、第三引数にRectオブジェクトを入れます。
  
  
また、ボタンのテキストの表示ついては、  
[shizenkarasuzon.hatenablog.com](https://shizenkarasuzon.hatenablog.com/entry/2018/12/29/203344)  
をご覧ください。  