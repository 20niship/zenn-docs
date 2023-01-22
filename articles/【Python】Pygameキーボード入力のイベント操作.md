---
title: "【Python】Pygame キーボード入力のイベント操作"
emoji: ""
type: ""
topics: []
published: false
---

前回は、マウスイベントについて説明したので、今回はキーボードについてみていきます。  
[shizenkarasuzon.hatenablog.com](https://shizenkarasuzon.hatenablog.com/entry/2019/02/08/183005)

* [サンプルプログラム](#サンプルプログラム)
* [実行結果](#実行結果)
* [解説](#解説)

### サンプルプログラム

from pygame.locals import *
import pygame
import sys

pygame.init()    # Pygameを初期化
screen = pygame.display.set_mode((400, 330))    # 画面を作成
pygame.display.set_caption("keyboard event")    # タイトルを作成


while True:
    screen.fill((0, 0, 0)) 
    for event in pygame.event.get():
        if event.type == QUIT:
            pygame.quit()
            sys.exit()
        if event.type == KEYDOWN:  # キーを押したとき
            # ESCキーならスクリプトを終了
            if event.key == K_ESCAPE:
                pygame.quit()
                sys.exit()
            
            else:
                print("押されたキー = " + pygame.key.name(event.key))
        pygame.display.update()
  
  
### 実行結果

以下のような画面が表示されます。  
![f:id:pythonjacascript:20190208184004j:plain](/images/ppythonjacascript2019020820190208184004.jpg "f:id:pythonjacascript:20190208184004j:plain")

また、画面上で（？）キーボード上のキーを何か押すと（Esc以外）、そのキーの名前が表示されます。

押されたキー = a
押されたキー = s
押されたキー = x
押されたキー = z
押されたキー = left ctrl
押されたキー = left super
  
  
### 解説

**キーボードイベント**とは、キーボードのキーが押されたり離されたりしたときに発生するイベントのことです。

キーボードイベントには以下の2種類があります。

| イベント名   | 説明      |
| ------- | ------- |
| KEYDOWN | キーが押された |
| KEYUP   | キーが離された |

  
また、それぞれのイベントでは、keyというパラメータが存在して、その中には押されたキーの番号が格納されています。

キー番号には、以下のようなものがあります。

| キー番号      | 押されたキー |
| --------- | ------ |
| K\_x      | x      |
| K\_DELETE | Delete |
| K\_SPACE  | スペースキー |
| K\_LEFT   | 左矢印    |

などなどです。

その他、キー番号について知りたい方は、こちらのサイトをご覧ください。  
<http://westplain.sakuraweb.com/translate/pygame/Key.cgi#pygame.key.name>

K\_LEFT等のキー番号を示す定数は**pygame.locals**で定義されてます。