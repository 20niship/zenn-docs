---
title: "Python+OpenCVで画像処理 勉強会第一回（物体認識、背景削除）"
emoji: "🤖"
type: "tech"
topics: []
published: false
---

にゃんぱすー！  
[大学の某サークル](http://fastpass.tech/)の画像処理関連の勉強会の内容です。

  
この記事では、以下の内容について語っていきます

* Python + OpenCVの環境構築
* OpenCVの基本的な使い方  
   * 画像の読み込み・表示  
   * カメラ画像の読み込み・表示  
   * 各種エフェクト  
         * 二値化  
         * グレースケール変換  
         * エッジ抽出  
         * ブラー（ぼかし）  
         * 色空間変換（RGB↔HSV等）
* カメラの背景画像を削除する
* 物体検知（機械学習を使わずに）

プログラム全体はGitHubの↓レポジトリに上がっているよ  
[github.com](https://github.com/20niship/python%5Fopencv%5Flecture)  

ではいこう！  

* [環境構築](#環境構築)  
   * [Pythonのインストール](#Pythonのインストール)  
   * [pipで必要モジュールのインストール](#pipで必要モジュールのインストール)  
   * [VSCodeの設定](#VSCodeの設定)
* [1\. 画像を表示してみる](#1-画像を表示してみる)
* [2\. 各種エフェクト](#2-各種エフェクト)
* [3\. カメラ画像を取得](#3-カメラ画像を取得)
* [4\. 背景削除](#4-背景削除)
* [5\. 物体認識(画像編)](#5-物体認識画像編)
* [6\. 物体認識（カメラ編）](#6-物体認識カメラ編)
  
  
### 環境構築

基本的にWindowsの前提で話します。Macの人は頑張って下さい（ネット上に記事が大量にあるはず）  
  
#### Pythonのインストール

自分のPCでPythonプログラムが実行できるような環境を整えます

[www.python.org](https://www.python.org/downloads/)

上記リンクから、最新のPythonをダウンロードします。2021/6/5時点では、[Python3.9.5](https://www.python.org/downloads/release/python-395/)が最新なので、それをクリックします。  
そして、Files節の自分のOSに合ったインストーラーをダウンロードします。

* Windows→「Windows installer (64-bit)」
* intel製Mac→「macOS 64-bit Intel installer」
* M1 Mac→「macOS 64-bit universal2 installer」

かなぁ。（間違ってたらごめんなさい）

インストール中に、「**Add Python 3.x to PATH（Pythonを環境変数に追加する）**」のような表示が出たらチェックすることを忘れずに！！

インストールが正常に終わっていると、ターミナル（Windowsの人はデフォルトではコマンドプロンプト。でも[Windows Terminal](https://www.microsoft.com/ja-jp/p/windows-terminal/9n0dx20hk701)を入れておくことをおすすめします）で以下のコマンドを実行してみて下さい。

$ python --version
Python 3.8.5

$ pip --version
pip 20.0.2 from /usr/lib/python3/dist-packages/pip (python 3.8)

※「＄」マークは「これはコマンドだよ」というのを表す記号なので、入力しないで下さい。  
※上のようにしてエラーが出る場合は**python→python3**, **pip→pip3**に置き換えて見て下さい

上のように、バージョンが表示されればOKです

ちなみに、[pip](https://pypi.org/project/pip/)はPythonの標準パッケージインストーラーです。  
  
#### pipで必要モジュールのインストール

では、そのpipを使って画像処理用ライブラリ「**OpenCV**」と行列計算ライブラリ「**numpy**」をインストールします。

$ pip install numpy
$ pip install opencv-python
$ pip install opencv-contrib-python
  
  
#### VSCodeの設定

Pythonの拡張機能を入れておくと便利です。補完機能や、デバッグの効率化、インデント色付け、ボタン一つでPythonプログラムを実行など色々あります。  
VSCodeの左枠の拡張機能アイコン（![f:id:pythonjacascript:20210605071017p:plain](/images/ppythonjacascript2021060520210605071017.png)）をクリックすると拡張機能一覧が表示されるので、「Python」とかで検索していい感じのものを入れましょう
  
  
ここからが本題です。プログラミングを始めます。この記事の通りにプログラムを書いて実行していっても良いですが、[このレポジトリ](https://github.com/20niship/python%5Fopencv%5Flecture)をzipダウンロード→解凍して使ったほうが楽かも。  
  
### 1\. 画像を表示してみる

以下のプログラムを実行して下さい。

# https://github.com/20niship/python_opencv_lecture/blob/master/1_1_show_img%20copy.py

# OpenCVライブラリを読み込む
import cv2

# 画像を読み込んでimg変数に格納
# print(img)をするとわかるが、imgはw * h * 3の大きさのnumpy行列
img = cv2.imread("cat.jpg")

while True:
    # testウィンドウにimgを表示
    cv2.imshow("test", img)

    # 10ms待つ。その間になにかキーボードが押されたらその数値を返してkeyに代入
    key = cv2.waitKey(10)
    # print(key)

    # ESCキーやQキーが押された場合は終了する
    if key == 27 or key == 113: break

プログラムの実行の仕方は、

1. 「hogehoge.py」みたいに「.py」拡張子で名前を付けて保存して下さい。
2. pyファイルと同じ位置にcat.jpgという名前で適当な画像をおいて下さい。（この記事では[これ](https://raw.githubusercontent.com/20niship/python%5Fopencv%5Flecture/master/cat.jpg)を使います）
3. コマンドプロンプトで「_python hogehoge.py_」のように「_python \[スクリプトファイル名\]_」と打って、プログラムを実行

  
以下のように「test」Windowに猫の画像が表示されたでしょうか？

![f:id:pythonjacascript:20210605083336j:plain](/images/ppythonjacascript2021060520210605083336.jpg)  

説明はスクリプトに書いてあるので読んでね。それでもわからない部分はググってね  
  
### 2\. 各種エフェクト

imread関数で読み込んだ画像オブジェクトに、ぼかしエフェクトを加えてみます。  
以下の一文をimread関数とimshow関数の間に追加して下さい。

img = cv2.blur(img,(51,51))

「(51, 51)のサイズでimgにブラー（ぼかし）を加えて、結果をimgに代入する」、という動作

以下の画像のようになったでしょうか？  
![f:id:pythonjacascript:20210605090544p:plain](/images/ppythonjacascript2021060520210605090544.png)  

blur関数の第二引数を色々変えてみると、ブラーの強さが変化します  
blur関数が何をやっているのかは[公式ドキュメント](http://labs.eecs.tottori-u.ac.jp/sd/Member/oyamada/OpenCV/html/py%5Ftutorials/py%5Fimgproc/py%5Ffiltering/py%5Ffiltering.html)を見てね（行列をかけてる）

  
今度は、キーボードの入力で適応されるエフェクトが変化するスクリプトを作ってみます。  
こーんな感じ。

[python\_opencv\_lecture/1\_2\_effects.py at master · 20niship/python\_opencv\_lecture · GitHub](https://github.com/20niship/python%5Fopencv%5Flecture/blob/master/1%5F2%5Feffects.py)  

import cv2
import numpy as np # 行列計算ライブラリ、Numpyをインポート
key = 0

while True:
    img = cv2.imread("cat.jpg") # image read

    # 画像にエフェクトをかけていく
    if key is ord('a'): # Aが押された時
        img = cv2.blur(img,(15,31)) # 画像をぼかす（15, 35)はX,Y方向のぼかす大きさ
    elif key is ord('g'):
        img = cv2.cvtColor(img, cv2.COLOR_BGR2GRAY)  # グレースケールにする
    elif key is ord('e'):
        img = cv2.Canny(img,60,200) # 輪郭抽出
    elif key is ord('x'):
        # 油絵エフェクト
        # 第二引数はsize, 第三引数はdynRatio
        img = cv2.xphoto.oilPainting(img, 7, 2, cv2.COLOR_BGR2Lab)    
    elif key is ord('h'):
        # 色空間の変換(RGB -> HSV)
        img = cv2.cvtColor(img, cv2.COLOR_BGR2HSV)
    elif key is ord('b'):
        gray = cv2.cvtColor(img, cv2.COLOR_BGR2GRAY)  # グレースケールにする
        ret3,img = cv2.threshold(gray,0,255,cv2.THRESH_BINARY+cv2.THRESH_OTSU) # 大津の二値化
    
    # 上のようなエフェクト組み合わせて色々作っていくよ

    # 例1：グリーンバック
    elif key is ord('w'):
        hsv = cv2.cvtColor(img, cv2.COLOR_BGR2HSV) # 色空間の変換(RGB -> HSV)
        height, width, channel = img.shape
        print(hsv[10, 10])
        for x in range(width):
            for y in range(height):
                h, s, v= img[y, x]
                if 30 < h < 70 and s > 50: # 緑色の部分は
                    img[y, x]=[255, 0, 0] # 赤色にする
    # 例2：自力で輪郭抽出
    elif key is ord('v'):
        gray = cv2.cvtColor(img, cv2.COLOR_BGR2GRAY)  # グレースケール化
        ret3,img_binary = cv2.threshold(gray,0,255,cv2.THRESH_BINARY+cv2.THRESH_OTSU) # 二値化
        img_binary = cv2.bitwise_not(img_binary) # ネガポジ反転（findContours関数は白い部分を検出するため）
        contours, hierarchy = cv2.findContours(img_binary, cv2.RETR_EXTERNAL, cv2.CHAIN_APPROX_NONE) # 輪郭検出
        img = cv2.drawContours(img, contours, -1, (0, 255, 0), 5) # 輪郭描画

    cv2.imshow("test", img)
    key_tmp = cv2.waitKey(10)
    if key_tmp > 0: key = key_tmp

    if key == 27 or key == 113: break

プログラムを実行して、

| 押したキー | 内容                            | 出力画像                                                                                                |
| ----- | ----------------------------- | --------------------------------------------------------------------------------------------------- |
| A     | ブラー（平滑化）                      | ![f:id:pythonjacascript:20210605084216j:plain](/images/ppythonjacascript2021060520210605084216.jpg) |
| G     | グレースケール（白黒化）                  | ![f:id:pythonjacascript:20210605084750j:plain](/images/ppythonjacascript2021060520210605084750.jpg) |
| E     | エッジ抽出                         | ![f:id:pythonjacascript:20210605084707j:plain](/images/ppythonjacascript2021060520210605084707.jpg) |
| X     | 油絵エフェクト（こんなのもある。面白いなぁ）        | ![f:id:pythonjacascript:20210605084240j:plain](/images/ppythonjacascript2021060520210605084240.jpg) |
| H     | 色空間をRGBからHSVにする               | ![f:id:pythonjacascript:20210605084619j:plain](/images/ppythonjacascript2021060520210605084619.jpg) |
| B     | 大津の二値化（白黒二色にする）               | ![f:id:pythonjacascript:20210605084229j:plain](/images/ppythonjacascript2021060520210605084229.jpg) |
| W     | 緑っぽい部分を青色（RGB(0, 0, 255)) にする | ![f:id:pythonjacascript:20210605084508j:plain](/images/ppythonjacascript2021060520210605084508.jpg) |
| V     | 輪郭を描画(自作スクリプト)                | ![f:id:pythonjacascript:20210605084408j:plain](/images/ppythonjacascript2021060520210605084408.jpg) |

という処理を実行します。  
  
  
### 3\. カメラ画像を取得

PCにカメラがあれば（もしくはUSBカメラ等が繋がっていれば）、そのカメラ映像を表示することもできます。

import cv2
import numpy as np # 行列計算ライブラリ、Numpyをインポート

# VideoCapture オブジェクトを取得
cap = cv2.VideoCapture(0)
 
if cap.isOpened(): 
    # get vcap property 
    width  = cap.get(cv2.CAP_PROP_FRAME_WIDTH)   # float `width`
    height = cap.get(cv2.CAP_PROP_FRAME_HEIGHT)  # float `height`
    # 以下の方法でも可
    # width  = cap.get(3)  # float `width`
    # height = cap.get(4)  # float `height`

    fps = cap.get(cv2.CAP_PROP_FPS)

    print("camera found!")
    print(f"width={width}, height={height}, fps={fps}")
else:
    print("カメラが取得できない！")

while(True):
    ret, img = cap.read() # 現在のカメラ画像を取得

    # ここにエフェクトを追加していく

    cv2.imshow('camera',img)

    key = cv2.waitKey(10)
    if key == 27 or key == 113: break
  
  
### 4\. 背景削除

ここからは問題形式。

![f:id:pythonjacascript:20210605090013j:plain](/images/ppythonjacascript2021060520210605090013.jpg)

> * カメラ画像から背景を削除する
> * Zoomのバーチャル背景みたいな機能を実装する
> * 人がいないときのカメラ映像は持っているものとする

  
▼ 解決策（例）  

  
考え方： 
* 背景を削除したいということは、以下のアルゴリズムが必要  
   * 1\. 背景と前景を区別する  
   * 2\. 背景と認定された部分を塗りつぶす  
   * 3\. 背景を塗りつぶした画像を表示

1.について：

* 背景画像と現在画像の色の差が一定以上担った部分を「前景」とすれば良い  
   * [absdiff()関数](http://opencv.jp/opencv-2svn/cpp/operations%5Fon%5Farrays.html)使えばなんとかなりそう(2つの画像の各ピクセルの差の絶対値を求める）  
   * 差分が閾値以下かどうかの判別にはthreshould関数を使おう
* でもこれだけだと、ノイズに弱いよね  
   * 例えば、偶然前景のある部分の色が 背景と同じ色だった場合、そこも背景になってしまう  
   * 領域を膨張すればよいのでは？  
   * [Dilation関数](http://labs.eecs.tottori-u.ac.jp/sd/Member/oyamada/OpenCV/html/py%5Ftutorials/py%5Fimgproc/py%5Fmorphological%5Fops/py%5Fmorphological%5Fops.html#dilation)を使おう
* まてよ、、  
   * 逆に、背景がすこし動いたり光の当たり方が変わって、その数ピクセルだけが誤って前景と認識されることもある？  
   * この場合は[領域収縮のerode関数](http://labs.eecs.tottori-u.ac.jp/sd/Member/oyamada/OpenCV/html/py%5Ftutorials/py%5Fimgproc/py%5Fmorphological%5Fops/py%5Fmorphological%5Fops.html#erosion)だな  
   * ブラーをかけるのもノイズ対策としては有効かも？

2.について

* Pythonのfor文でピクセルごとに処理すれば自分でプログラムを書くことはできるが、多分めっちゃ遅くなる。
* いいのないかなぁ、、、と探していたら、[np.where()](https://note.nkmk.me/python-numpy-where/)というのがあるのを知った。超便利！

3.について

* imshowでOk

import cv2
import numpy as np # 行列計算ライブラリ、Numpyをインポート

# VideoCapture オブジェクトを取得
cap = cv2.VideoCapture(0)

width = 10
height = 10
if cap.isOpened(): 
    # get vcap property 
    width  = int(cap.get(cv2.CAP_PROP_FRAME_WIDTH))  # float `width`
    height = int(cap.get(cv2.CAP_PROP_FRAME_HEIGHT))  # float `height`
    # 以下の方法でも可
    # width  = cap.get(3)  # float `width`
    # height = cap.get(4)  # float `height`

    fps = cap.get(cv2.CAP_PROP_FPS)

    print("camera found!")
    print(f"width={width}, height={height}, fps={fps}")
else:
    print("カメラが取得できない！")
    import os
    os.exit()

# ここでは自分は映らない
# 背景画像を取得
ret, background = cap.read()
background_gray = cv2.cvtColor(background, cv2.COLOR_BGR2GRAY)  # グレースケールにする


#青色ブランク画像
blue_img = np.zeros((int(height), int(width), 3))
blue_img += [0,0,255][::-1] #RGBで青指定

while(True):
    ret, img = cap.read()

    #グレースケール化して、backgroundとの絶対値（背景差分）を求める
    img_gray = cv2.cvtColor(img, cv2.COLOR_BGR2GRAY)  # グレースケールにする
    diff_img = cv2.absdiff(background_gray, img_gray)

    #二値化処理(どちらか良さそうな方を使って)
    # img_th = cv2.threshold(diff_img, 10, 255,cv2.THRESH_BINARY)[1]
    ret3,img_th = cv2.threshold(diff_img,0,255,cv2.THRESH_BINARY+cv2.THRESH_OTSU) # 大津の二値化

    # 膨張処理
    kernel = np.ones((5,5), np.uint8)
    mask = cv2.erode( img_th,kernel,iterations = 1) #縮小処理
    mask = cv2.dilate(mask,kernel,iterations = 1) # 拡大処理

    # #マスク画像を使って対象を切り出す
    result = np.copy(img)
    result[:] = np.where(mask[:height, :width, np.newaxis] == 0, blue_img, img)

    cv2.imshow('camera',img_th)
    cv2.imshow('diff',diff_img)
    cv2.imshow('result', result)

    key = cv2.waitKey(1)
    if key == 27 or key == 113: break
  
  
### 5\. 物体認識(画像編)

問題設定

> [flower.jpg](https://raw.githubusercontent.com/20niship/python%5Fopencv%5Flecture/master/flower.jpg)がある  
> ここから赤のチューリップを認識して四角の枠で囲もう

例：  
![f:id:pythonjacascript:20210605085053j:plain](/images/ppythonjacascript2021060520210605085053.jpg)  
  
  
▼ 解決策（例）  

  
考え方： 
* 画像全体を眺めると、背景部分は緑とか黒っぽい色が多いのに対して、花の部分は赤いな
* HSV変換して、色相が赤っぽいところを抽出すれば良さそう
* その後、[findCounters関数](http://labs.eecs.tottori-u.ac.jp/sd/Member/oyamada/OpenCV/html/py%5Ftutorials/py%5Fimgproc/py%5Fcontours/py%5Fcontours%5Fbegin/py%5Fcontours%5Fbegin.html)でバウンディングボックスを作成すればOKじゃん！
import cv2import numpy as np img = cv2.imread('flower.jpg') \# 画像読み込み \# HSV色空間に変換hsv\_img = cv2.cvtColor(img, cv2.COLOR\_BGR2HSV)\# 画像をぼかす（15, 35)はX,Y方向のぼかす大きさ \# hsv\_img = cv2.blur(hsv\_img,(5,5)) \# hsv\_img = cv2.GaussianBlur(hsv\_img, (9, 9), 3)h\_img = hsv\_img\[:, :, 0\] s\_img = hsv\_img\[:, :, 1\] v\_img = hsv\_img\[:, :, 2\] lower\_th = (0, 100, 100) higher\_th = (10, 255, 255) img\_th = cv2.inRange(hsv\_img, lower\_th, higher\_th)\# ret, img\_th= cv2.threshold(h\_img, 60, 255, cv2.THRESH\_BINARY)img\_th = cv2.bitwise\_not(img\_th) \# ネガポジ反転（findContours関数は白い部分を検出するため） \# 収縮・膨張処理kernel = np.ones((3,3), np.uint8) mask = cv2.dilate(img\_th, kernel, iterations = 1) #縮小処理（ノイズ除去） \# mask = cv2.dilate(mask, kernel, iterations = 1) # 拡大処理（凹んだ部分を埋める）result = np.copy(img) contours, hierarchy = cv2.findContours(mask,cv2.RETR\_TREE,cv2.CHAIN\_APPROX\_SIMPLE)\# labels, contours, hierarchy = cv2.findContours(mask, cv2.RETR\_LIST, cv2.CHAIN\_APPROX\_NONE) print("