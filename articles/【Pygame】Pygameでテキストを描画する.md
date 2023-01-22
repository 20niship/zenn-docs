---
title: "【Pygame】Pygameでテキストを描画する"
emoji: ""
type: ""
topics: []
published: false
---

前回、Pygameをインストールして初のウィンドウを作成しました。  
[shizenkarasuzon.hatenablog.com](https://shizenkarasuzon.hatenablog.com/entry/2018/12/18/221334)

[![/images/ppythonjacascript2018121820181218211525.jpg](/images/ppythonjacascript2018121820181218211525.jpg)](/images/ppythonjacascript2018121820181218211525.jpg)

  
今回は、この真っ黒な画面に文字を書いていきます。  
  
### 1.サンプルプログラム

以下のスクリプトを実行してください。

import pygame
import sys

def main():
    pygame.init()    # Pygameを初期化
    screen = pygame.display.set_mode((400, 300))    # 画面を作成
    pygame.display.set_caption("S.C.MAGI")    # タイトルを作成

    #STEP1.フォントの用意  
    font1 = pygame.font.SysFont("hg正楷書体pro", 150)
    font2 = pygame.font.SysFont(None, 50)
    
    #STEP2.テキストの設定
    text1 = font1.render("警告", True, (255,0,0))
    text2 = font2.render("EMERGENCY", True, (255,0,0))
    
    running = True
    #メインループ
    while running:
        screen.fill((0,0,0))  #画面を黒で塗りつぶす
    
        # STEP3.テキストを描画
        screen.blit(text1, (40,30))
        screen.blit(text2, (80,180))
        
        pygame.display.update() #描画処理を実行
        
        for event in pygame.event.get():
            if event.type == QUIT:  # 終了イベント
                running = False
                pygame.quit()  #pygameのウィンドウを閉じる
                sys.exit() #システム終了
                
     
if __name__=="__main__":
    main()
  
  
### 実行結果

![f:id:pythonjacascript:20181229192049j:plain:h300](/images/ppythonjacascript2018122920181229192049.jpg "f:id:pythonjacascript:20181229192049j:plain:h300")  
このように表示されればOKです。  
  
### 簡単に解説

文字を描画するには、3STEP必要です。

#### STEP1.フォントの用意

font1 = pygame.font.SysFont("hg正楷書体pro", 150)
font2 = pygame.font.SysFont(None, 50)

**pygame.font.SysFont()**でフォントを読み込みます。  
引数は**フォント名**と**サイズ**の2つです。  
  
##### フォント名について：

フォント名に**None**を与えるとデフォルトフォント（freesansbold.ttf）を使います。このフォントは日本語は使えません（□□←こんな感じで文字化けします）  
そのため、日本語を描画するときは別のフォントを使わなければなりません。上のサンプルプログラムでは、「**hg正楷書体pro**」を使用しています。

使用可能なフォント一覧は、いかのプログラムを実行すると得ることができます。

import pygame
 print(pygame.font.get_fonts())

ちなみに、僕の環境（Windows10、Python3.6）では、上のプログラムを実行した結果下のようになりました。

利用可能なフォント一覧：

['', 'symeteoiv25', 'syastroiv50', 'symap', 'gdtiv25', 'isoct3iv25', 'italict', 'gothici', 'gothicg', 'nirmalauisemilight', 'greekciv50', 'segoeuisemibold', 'segoescript', 'hg教科書体hgp教科書体hgs教科書体', 'mistral','constantia', 'bradleyhanditc', （中略） 'comicsansms', 'stylus', 'isocp2', 'symusic', 'swiss721condensed', 'microsofttaile']
  
  
#### STEP2.テキストの設定

text1 = font1.render("警告", True, (255,0,0))
text2 = font2.render("EMERGENCY", True, (255,0,0))

STEP1.で作成したフォントをもとに、テキストオブジェクトを作成します。作成方法は、

（テキストオブジェクト）＝ （フォントオブジェクト）.render("文字列", （滑らかにするか？T/F）, (文字色RGB))

です。render()関数の3つの引数は以下の通り。

| 引数   | 説明            | 例                        |
| ---- | ------------- | ------------------------ |
| 第1引数 | 表示するテキスト      | "EMERGENCY"              |
| 第2引数 | 文字の境界を滑らかにするか | True（滑らかにする）、False（かくかく） |
| 第3引数 | 文字色のRGB値      | （255,255,255）#←白色を表す     |
  
  
#### STEP3\. テキストを描画

screen.blit(text1, (40,30))
screen.blit(text2, (80,180))

blit()関数を使って、screenにテキストを描画します。  
それぞれの引数は以下の通り。

| 引数   | 説明                   | 例          |
| ---- | -------------------- | ---------- |
| 第1引数 | テキストオブジェクト（STEP2で作成） | text1      |
| 第2引数 | 表示位置（左上からのピクセル値）     | (100, 100) |

  