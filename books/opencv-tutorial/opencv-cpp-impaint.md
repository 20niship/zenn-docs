---
title: "【OpenCV/C++】画像の一部分を修復する（cv::inpaint)"
emoji: "🤖"
type: "tech"
topics: []
published: false
---

### 今回すること

OpenCVを用いて画像の一部を修復します。例えば、下の画像の様に、電線だけ消す、というようなことができます。（下の方は失敗していますが、一番上4本の電線はかなりきれいに消えていると思います）  
![f:id:pythonjacascript:20200903020610j:plain](/images/ppythonjacascript2020090320200903020610.jpg "f:id:pythonjacascript:20200903020610j:plain")  

このような画像修復を行うために、cv::inpaintを用います。

// inpaintMaskのピクセル値が0でないピクセルを修復します。
void inpaint(const Mat& src, const Mat& inpaintMask, Mat& dst, double inpaintRadius, int flags)

  
引数：

| src           | 入力画像。8ビット，1あるいは3チャンネル                                                                                   |
| ------------- | ------------------------------------------------------------------------------------------------------- |
| inpaintMask   | 8ビット，1チャンネル。修復領域を指定する                                                                                   |
| dst           | src と同じサイズ，同じタイプの画像。修復画像が出力される                                                                          |
| inpaintRadius | 修復される各点を中心とする近傍円形領域の半径。この領域がアルゴリズムに考慮される                                                                |
| flags         | 修復手法。cv::INPAINT\_NS (ナビエ・ストークス（Navier-Stokes）ベースの手法)または、cv::INPAINT\_TELEA（ Alexandru Telea による手法）の2通り |

  
[qiita.com](https://qiita.com/satsukiya/items/0c490b73add0c7e5c28f)  
このブログでとても詳しく説明されているので、今回は適当にコード書いて終わります。  
  
  
### プログラム

#include <opencv2/opencv.hpp>
#include <opencv2/imgcodecs.hpp>
#include <opencv2/highgui.hpp>
#include <opencv2/imgproc.hpp>
#include <iostream>

using namespace cv;
using namespace std;

bool LbtnPushed, need_update;
cv::Mat src, mask, result_img;


static void on_mouse(int event, int x, int y, int flags, void* param){
  const int radius = 2;

  switch (event){
  case EVENT_LBUTTONDOWN: 
    LbtnPushed = true;
    break;
  
  case EVENT_LBUTTONUP:
    LbtnPushed = false;
    break;

  case EVENT_MOUSEMOVE:
    if (LbtnPushed){
      circle(mask, cv::Point(x, y), radius, 255, -1);// 消去する部分のmaskの画素値を1以上にする（←のプログラムだと255）
      need_update = true;
    }
    break;
  }
}


bool loop() {
  if (need_update) {
    Mat binMask;
    binMask = mask & 1; //binMaskは、maskがGC_FGD(=1)とGC_PR_FGD(=3)のピクセルが1になり、それ以外は0になる
    result_img = cv::Mat(src.size(), CV_8UC3, cv::Scalar(0, 255, 0)); //全面緑の画像作成
    src.copyTo(result_img, binMask); //binMaskが1の部分だけをresult_imgにコピーする
    cv::inpaint(src, mask, result_img, 2, cv::INPAINT_TELEA);

    need_update = false;

  }
  cv::imshow("result", result_img);
  cv::imshow("mask", mask);
  return !(cv::waitKey(1) == 27);
}

int main(){
  namedWindow("result", WINDOW_AUTOSIZE);
  setMouseCallback("result", on_mouse, 0);
  need_update = true;

  src = cv::imread("test.jpg");

  mask.create(src.size(), CV_8UC1);
  mask.setTo(Scalar::all(0));
  //mask = cv::imread("mask.png", 0);
  while(loop()){}

  return 0;
}

### 実行結果

上のプログラムを実行して、src画像の上でマウスを左クリック→ドラッグすると、その部分が修復領域に指定され、修復された画像が再描画されます。  
手作業で頑張って修復させると下画像のようになります。  
![f:id:pythonjacascript:20200903020610j:plain](/images/ppythonjacascript2020090320200903020610.jpg "f:id:pythonjacascript:20200903020610j:plain")  
  
  
### 参考

[qiita.com](https://qiita.com/satsukiya/items/0c490b73add0c7e5c28f)

  
cv::xphoto::inpaintというのもあるようです：  
[taktak.jp](https://taktak.jp/2017/05/16/2291)