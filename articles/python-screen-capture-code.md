---
title: "【Python】スクリーンキャプチャ（mp4, avi, gif出力）"
emoji: "🤖"
type: "idea"
topics: []
published: false
---

### 説明

タイトルの通りです。選択した範囲をスクリーンキャプチャして、動画ファイル（mp4, avi, gif）として出力します。  
プログラムを起動すると、まず現在の画面をキャプチャしたWindowが現れるので、マウスをドラッグして録画領域を選択します。  
ドラッグが終わるとそのまま録画が開始されます。

録画を中断するには、コマンドプロンプト（？）上で何かキーを押してください。

ではまた。  
  
### プログラム

```py
# coding: utf-8

#  *************************
#  https://stackoverflow.com/questions/49268120/why-cannot-imagegrab-grab-capture-the-whole-screen
# to capture whole screen:
# -> Goto Python installed directory and Right-click on python.exe
# -> Properties -> Compatibility tab -> check 'Disable display scaling on high DPI settings'.
#  *************************

import cv2
import numpy as np
from PIL import ImageGrab
from PIL import Image
import time
import msvcrt

isSetArea = False
isMouseDown = False

pos_1 = [0,0]
pos_2 = [50,50]
mouse_x = 0
mouse_y = 0

# ImageGrab.grab()で全画面をキャプチャするためのもの（高DPI設定を無効化）
from ctypes import windll
user32 = windll.user32
user32.SetProcessDPIAware()


def callback(event, x, y, flags, param):
    global pos_1, pos_2, isSetArea, isMouseDown, mouse_x, mouse_y
    if event == cv2.EVENT_LBUTTONDOWN:
        isMouseDown = True
        pos_1[0] = int(x)
        pos_1[1]  = int(y)

    if event == cv2.EVENT_LBUTTONUP:
        pos_2[0] = int(x)
        pos_2[1] = int(y)

        isSetArea = True

    if event == cv2.EVENT_MOUSEMOVE:
        mouse_x, mouse_y = x, y


def get_record_area():
    global pos_1, pos_2,isMouseDown, mouse_x, mouse_y

    cv2.namedWindow("test", cv2.WINDOW_NORMAL)
    cv2.setMouseCallback("test", callback)
    img_cv = np.asarray(ImageGrab.grab())

    while isSetArea is False:
        img = cv2.cvtColor(img_cv, cv2.COLOR_BGR2RGB)
        img2 = cv2.resize(img, (int(np.shape(img)[1] * 0.5), int(np.shape(img)[0] * 0.5)))
        img3 = img2

        if isMouseDown:
            img3 = cv2.rectangle(img3, tuple(pos_1), (mouse_x, mouse_y), (255, 255, 0), thickness=2)
        else:
            img3 = cv2.putText(img3, "please select record area", (0, 50), cv2.FONT_HERSHEY_PLAIN, 1.5, (255, 255, 255), 5, cv2.LINE_AA)
        cv2.imshow("test", img3)
        cv2.waitKey(1)

    pos_1[0], pos_1[1] = pos_1[0]*2, pos_1[1]*2
    pos_2[0], pos_2[1] = pos_2[0]*2, pos_2[1]*2    
    cv2.destroyWindow("test")
    cv2.destroyAllWindows()


def save_mp4(images, capSize, fps):
    # fourcc = cv2.VideoWriter_fourcc(*"H264")
    fourcc = cv2.VideoWriter_fourcc(*"MP4V")
    writer = cv2.VideoWriter("sample.mp4", fourcc, fps, capSize)
    for img in images:
        writer.write(img)
    writer.release()


def save_avi(images, capSize, fps):
    print("FPS: {}".format(fps))
    print("Capsize : ", capSize)
    print("video saving...")

    fourcc = cv2.VideoWriter_fourcc(*"XVID")
    writer = cv2.VideoWriter("sample.avi", fourcc, fps, capSize)
    for img in images:
        writer.write(img)
    writer.release()


def save_gif(images, capSize, fps):
    print("FPS: {}".format(fps))
    print("Capsize : ", capSize)
    print("video saving...")
    images_pillow = []
    duration = int(1000. / fps)
    for image in images:
        images_pillow.append(Image.fromarray(cv2.cvtColor(image, cv2.COLOR_RGB2BGR)))

    images_pillow[0].save('sample.gif', save_all=True, append_images=images_pillow[1:], optimize=False, duration=duration, loop=0)


def record():
    count = 0
    get_record_area()
    render_img_list = []
    print("recording start....")
    print("録画を停止するには何かキーを押してください")
    print(f"position = ({pos_1}, {pos_2})")
    
    img_cv = np.asarray(ImageGrab.grab())
    print(np.shape(img_cv))

    start_time = time.time()

    while True:
        img_cv = np.asarray(ImageGrab.grab())
        img = cv2.cvtColor(img_cv, cv2.COLOR_BGR2RGB)
        img_area = img[pos_1[0]:pos_2[1], pos_1[0]:pos_2[0]]
        # img_area = img[0:100,0:100]
        render_img_list.append(img_area)
        count += 1.

        if msvcrt.kbhit():
            break   # 何か文字が押されたら終了
    width, height = pos_2[0]-pos_1[0], pos_2[1]-pos_1[1]
    fps = count / (time.time() - start_time)
    save_gif(render_img_list, (width, height), fps)
    # mp4出力の時：save_mp4(render_img_list, (width, height), fps)
    # avi出力の時：save_avi(render_img_list, (width, height), fps)

record()
input("何かキーを押してください.....")
```
  
### 参考文献

[python - Why cannot ImageGrab.grab capture the whole screen? - Stack Overflow](https://stackoverflow.com/questions/49268120/why-cannot-imagegrab-grab-capture-the-whole-screen)  
[【Python】画像の切り出し方法【OpenCV】 - Qiita](https://qiita.com/ikanamazu/items/d752225a0a9834ce0d41)  