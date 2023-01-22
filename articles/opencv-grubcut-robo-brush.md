---
title: "【OpenCV/C++】GrabCutで対話的な前景抽出を行う（AEのロトブラシの画像版）"
emoji: ""
type: ""
topics: []
published: false
---

この記事では、  
![f:id:pythonjacascript:20200830131125j:plain](/images/ppythonjacascript2020083020200830131125.jpg "f:id:pythonjacascript:20200830131125j:plain")  
↑画像を、  
![f:id:pythonjacascript:20200830131140j:plain](/images/ppythonjacascript2020083020200830131140.jpg "f:id:pythonjacascript:20200830131140j:plain")  
↑こんな風にするエフェクトをC++で作っていきます。

Rotoscoping、AEで言うところのRotobrush、日本語だと前景抽出とかセグメンテーションとかそういう類の技術です。  
  
  
### 実装方法

細かいことを言うと、セグメンテーションのアルゴリズムにはGMM（Gaussian mixture model、ガウス混合モデル）でminCutアルゴリズムを使って前景と背景を分けて...という理屈はあるのですが、幸いにも、それらのアルゴリズムは[GrabCut](http://labs.eecs.tottori-u.ac.jp/sd/Member/oyamada/OpenCV/html/py%5Ftutorials/py%5Fimgproc/py%5Fgrabcut/py%5Fgrabcut.html)としてOpenCV内に実装があるので、それを使います。

//セグメンテーションの初期化
cv::grabCut(src, // 入力画像
  mask,   // セグメンテーションされたマスク
  myrect,// 入力画像の前景の位置を示した矩形
  bgModel, fgModel, // GMMモデル
  1, // 繰り返しの回数
  cv::GC_INIT_WITH_RECT); // use myrectを使ってmaskを初期化する

のようにすることで、srcの前景と思われる部分を判断し、前景/背景のセグメンテーションの結果をmaskに格納します。

第5引数は、以下の3通りがあります。  
矩形領域内の情報を基に初期値を決めるか，前景/背景を指定する線を基に初期値を決めるかを

| cv::GC\_INIT\_WITH\_RECT | 矩形領域内の情報を基に初期化                    |
| ------------------------ | --------------------------------- |
| cv::GC\_INIT\_WITH\_MASK | mask(第二引数)内の前景/背景を指定する線（後述）を基に初期化 |
| 第5引数なし                   | 初期化せずにマスクを更新する                    |

  
第二引数のmaskは8UC1（8bitで1Colorのcv::Mat）であり、その各画素の値は、

| GC\_BGD     | 0 | 背景である |
| ----------- | - | ----- |
| GC\_FGD     | 1 | 前景である |
| GC\_PR\_BGD | 2 | 背景らしい |
| GC\_PR\_FGD | 3 | 前景らしい |

に分類されます。GrabCut関数は、GC\_PR\_BGDとGC\_PR\_FGD のピクセルを更新し、GC\_BGDとGC\_FGDの部分は更新されません。

つまり、画像の前景だけを表示したい場合は、maskの画素値がGC\_FGDとGC\_PR\_FGD の部分だけを表示します。

この記事の下のプログラムは、GC\_BGDとGC\_FGDはユーザーがマウスで指定し、そのユーザー入力をもとにGrabCutが前景らしい部分と背景らしい部分（GC\_PR\_BGDとGC\_PR\_FGD ）を計算するものです。  
  
  
### プログラム

#include <opencv2/opencv.hpp>
#include <opencv2/imgcodecs.hpp>
#include <opencv2/highgui.hpp>
#include <opencv2/imgproc.hpp>
#include <iostream>


using namespace cv;
using namespace std;

bool LbtnPushed;
bool RbtnPushed;
bool need_update;
bool use_mask;

cv::Mat src;
cv::Mat mask, result_img;
cv::Mat bgModel, fgModel; // the models (internally used)


const string winName = "image";
cv::Rect myrect;


static void getBinMask(const Mat& comMask, Mat& binMask)
{
  if (comMask.empty() || comMask.type() != CV_8UC1)
    CV_Error(Error::StsBadArg, "comMask is empty or has incorrect type (not CV_8UC1)");
  if (binMask.empty() || binMask.rows != comMask.rows || binMask.cols != comMask.cols)
    binMask.create(comMask.size(), CV_8UC1);
  binMask = comMask & 1;
}


static void on_mouse(int event, int x, int y, int flags, void* param){
  const int radius = 2;

  switch (event){
  case EVENT_LBUTTONDOWN: 
    LbtnPushed = true;
    break;

  case EVENT_RBUTTONDOWN:
    RbtnPushed = true;
    break;
  
  case EVENT_LBUTTONUP:
    LbtnPushed = false;
    break;

  case EVENT_RBUTTONUP:
    RbtnPushed = false;
    break;

  case EVENT_MOUSEMOVE:
    if (LbtnPushed){
      circle(mask, cv::Point(x, y), radius, GC_FGD, -1);// 前景ラベル（GC_FGD）をセットする
      std::cout << "new foredround point = (" << x << ", " << y << ")\n";

    }else if (RbtnPushed) {
      circle(mask, cv::Point(x, y), radius, GC_BGD, -1); // 背景ラベル（GC_BGD）をセットする
      std::cout << "new background point = (" << x << ", " << y << ")\n";
    }
    break;
  }
}


bool loop() {
  if (need_update) {
    if (use_mask) {
      //セグメンテーションの更新
      cv::grabCut(src, // 入力画像
        mask,   // セグメンテーションされたマスク
        myrect,// 入力画像の前景の位置を示した矩形
        bgModel, fgModel, // GMMモデル
        1); // 繰り返しの回数

      std::cout << "mask updated using mask!!\n";
    }else {
      //セグメンテーションの初期化
      cv::grabCut(src, // 入力画像
        mask,   // セグメンテーションされたマスク
        myrect,// 入力画像の前景の位置を示した矩形
        bgModel, fgModel, // GMMモデル
        1, // 繰り返しの回数
        cv::GC_INIT_WITH_RECT); // use myrectを使ってmaskを初期化する
      use_mask = true;
      std::cout << "mask updated using rect!!\n";
    }

    Mat binMask;
    binMask = mask & 1; //binMaskは、maskがGC_FGD(=1)とGC_PR_FGD(=3)のピクセルが1になり、それ以外は0になる
    result_img = cv::Mat(src.size(), CV_8UC3, cv::Scalar(0, 255, 0)); //全面緑の画像作成
    src.copyTo(result_img, binMask); //binMaskが1の部分だけをresult_imgにコピーする
    
    need_update = false;

  }
  cv::imshow(winName, result_img); //画像表示

  int key = cv::waitKey(1);
  if (key == 110) need_update = true; 
  return !(key == 27);
}


int main(){
  namedWindow(winName, WINDOW_AUTOSIZE);
  setMouseCallback(winName, on_mouse, 0);
  need_update = true;
  use_mask = false;

  src = cv::imread("test.jpg");
  mask.create(src.size(), CV_8UC1);
  mask.setTo(Scalar::all(GC_PR_FGD));
  myrect = cv::Rect(1, 1, src.cols - 2, src.rows - 2); //前景がすべて治まるように設定します。
  (mask(myrect)).setTo(Scalar(GC_PR_FGD));

  while(loop()){}

  return 0;
}

### 実行結果

コンパイルで作成された実行ファイルと同じフォルダにtest.jpgを入れて実行します。  
（test.jpgの例）  
![f:id:pythonjacascript:20200830131125j:plain](/images/ppythonjacascript2020083020200830131125.jpg "f:id:pythonjacascript:20200830131125j:plain")  
  
初期状態は↓画像の様にセグメンテーションがあいまいな状態で表示されます。  
![f:id:pythonjacascript:20200830133613j:plain](/images/ppythonjacascript2020083020200830133613.jpg "f:id:pythonjacascript:20200830133613j:plain")  

そこで、そのWindowの中で、マウスを左クリックしながら動かすと、その部分が前景となります。逆に右クリックしながら動かすと、その部分が背景になります。また、キーボードのｎキーを押すと、GrabCutがもう一度実行されて、より正確なセグメンテーション結果になるはずです。

そのような地道な作業を繰り返していくことで、下の画像のようになります。  
![f:id:pythonjacascript:20200830131140j:plain](/images/ppythonjacascript2020083020200830131140.jpg "f:id:pythonjacascript:20200830131140j:plain")  
  
  
### （参考）ロトブラシ（動画に対する前景抽出）の実装方法

AEのロトブラシ（動画に対して前景抽出を行う）アルゴリズムは下のページ内にあるPDFで解説されています。  
[Video SnapCut](http://www.juew.org/projects/SnapCut/snapcut.htm)

このアルゴリズムを実装してくれているのがこのレポジトリの様です。  
[github.com](https://github.com/bcheng1996/rotobrush)

簡単にいえば、動画のあるフレームに対し前景抽出を行い、前景の物体の輪郭に沿ってWindowを作成します。そして、輪郭のフレーム間の動きを推定することで、それぞれのWindowを輪郭に沿って動かし、そのWindow内でセグメンテーションを行う、という手法の様です。輪郭の動きに沿ってWindowを移動させる際、誤ってWindow同士の間に隙間ができないよう、敢えて大きめ（かぶりが生じるよう）にWindowのサイズを調整します。