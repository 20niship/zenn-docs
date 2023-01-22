---
title: "【OpenCV/C++】OpenCVでトーンカーブっぽいものを実装する"
emoji: "🤖"
type: "tech"
topics: []
published: false
---

タイトルの通りです。今回作ってみるのはこれです。  
![f:id:pythonjacascript:20200828125840g:plain](/images/ppythonjacascript2020082820200828125840.gif "f:id:pythonjacascript:20200828125840g:plain")  
右側の画面の2つの黄色の円を動かすことで、画像の色調補正を行うことができるプログラムです。  
右側の画面の白色の曲線が補正の値を表していて、横軸が入力値（RGBそれぞれ）、縦軸が出力値となります。

  
で、プログラムです。

### プログラム

以下がプログラム全文です。

# include <opencv2/opencv.hpp>
# include <opencv2/highgui.hpp>
# include <opencv2/imgproc/imgproc_c.h> //CV_AA用
#include <iostream>

#define ESCAPE_KEY 27
#define SQUARE(x) ((x)*(x))

float mX1 = 0.25f;
float mX2 = 0.75f;
float mY1 = 0.25f;
float mY2 = 0.75f;

float Bezier_fA(float aA1, float aA2) { return 1.0f - 3.0f * aA2 + 3.0f * aA1; }
float Bezier_fB(float aA1, float aA2) { return 3.0f * aA2 - 6.0 * aA1; }
float Bezier_fC(float aA1) { return 3.0 * aA1; }

float CalcBezier(float aT, float aA1, float aA2) { return  ((Bezier_fA(aA1, aA2)*aT + Bezier_fB(aA1, aA2))*aT + Bezier_fC(aA1))*aT; }
float GetSlope(float aT, float aA1, float aA2) { return 3.0 * Bezier_fA(aA1, aA2)*aT*aT + 2.0 * Bezier_fB(aA1, aA2) * aT + Bezier_fC(aA1); }

float getForX(float aX) {
  float currentSlope;
  float aGuessT = aX;
  for (__int8 i = 0; i < 4; i++) {
    currentSlope = GetSlope(aGuessT, mX1, mX2);
    if (currentSlope == 0.0) return aGuessT;
    float currentX = CalcBezier(aGuessT, mX1, mX2) - aX;
    aGuessT -= currentX / currentSlope;
  }
  return aGuessT;
}


float getBezier(float aX) {
  if (mX1 == mY1 && mX2 == mY2) return aX; // linear
  return CalcBezier(getForX(aX), mY1, mY2);
}

#define WINDOW_SIZE 500.0f
#define WINDOW_MARGIN 25

cv::Mat img = cv::Mat::zeros(WINDOW_SIZE + WINDOW_MARGIN * 2, WINDOW_SIZE + WINDOW_MARGIN * 2, CV_64FC3);
bool isBtnDown = false;

void mouse_callback(int event, int x, int y, int flags, void *userdata) {
  if (event == cv::EVENT_LBUTTONDOWN) {
    isBtnDown = true;
    return;
  }
  else if (event == cv::EVENT_LBUTTONUP) {
    isBtnDown = false;
    return;
  }

  float posX = float(x - WINDOW_MARGIN) / WINDOW_SIZE;
  float posY = 1.0f - float(y - WINDOW_MARGIN) / WINDOW_SIZE;

  float dis_1 = SQUARE(posX - mX1) + SQUARE(posY - mY1);
  float dis_2 = SQUARE(posX - mX2) + SQUARE(posY - mY2);

  if (event == cv::EVENT_MOUSEMOVE && isBtnDown == true) {
    if (dis_1 < dis_2 && dis_1 < 10) {
      mX1 = posX;
      mY1 = posY;
    }
    if (dis_1 > dis_2 && dis_2 < 10) {
      mX2 = posX;
      mY2 = posY;
    }
    return;
  }
}

void main() {
  cv::Mat lookUpTable(1, 256, CV_8U);
  uchar* p = lookUpTable.data;
  cv::Mat sample_img = cv::imread("test.jpg");
  cv::Mat lut_applied_img = sample_img.clone();

  cv::namedWindow("test", cv::WINDOW_FREERATIO);
  cv::setMouseCallback("test", mouse_callback);

  while (1) {
    cv::rectangle(img, cv::Point(0, 0), cv::Point(WINDOW_SIZE + WINDOW_MARGIN * 2, WINDOW_SIZE + WINDOW_MARGIN * 2), cv::Scalar(0, 0, 0), -1, 8, 0);
    cv::rectangle(img, cv::Point(WINDOW_MARGIN, WINDOW_MARGIN), cv::Point(WINDOW_SIZE + WINDOW_MARGIN, WINDOW_SIZE + WINDOW_MARGIN), cv::Scalar(225, 225, 225), 1, 8, 0);

    cv::Point point_1 = cv::Point(WINDOW_SIZE * mX1 + WINDOW_MARGIN, WINDOW_SIZE * (1.0f - mY1) + WINDOW_MARGIN);
    cv::Point point_2 = cv::Point(WINDOW_SIZE * mX2+ WINDOW_MARGIN, WINDOW_SIZE *  (1.0f - mY2) + WINDOW_MARGIN);

    // ベジエ曲線の描画
    int posX_old = 0;
    int posY_old = 500.0f * (1.0f - getBezier(0));

    for (int i = 0; i < 256; i++) {
      float value = getBezier(float(i) / 256.0f);
      int posX = WINDOW_SIZE * float(i) / 256.0f;
      int posY = 500.0f * (1.0f - value);
      cv::line(img, cv::Point(posX_old + WINDOW_MARGIN, posY_old + WINDOW_MARGIN), cv::Point(posX + WINDOW_MARGIN, posY + WINDOW_MARGIN), cv::Scalar(225, 225, 225), 2, CV_AA);
      posX_old = posX; posY_old = posY;

      p[i] = (uchar)(value * 256.0f); //LUT作成
    }

    //トーンカーブエディターを描画
    cv::line(img, cv::Point(WINDOW_MARGIN, WINDOW_SIZE + WINDOW_MARGIN), point_1, cv::Scalar(200, 150, 0), 2, CV_AA);
    cv::line(img, cv::Point(WINDOW_SIZE + WINDOW_MARGIN, WINDOW_MARGIN), point_2, cv::Scalar(200, 150, 0), 2, CV_AA);
    cv::circle(img, point_1, 7, cv::Scalar(0, 200, 200), 3, 4);
    cv::circle(img, point_2, 7, cv::Scalar(0, 200, 200), 3, 4);

    std::string text = "(" + std::to_string(mX1) + ", " + std::to_string(mY1) + "), (" + std::to_string(mX2) + ", " + std::to_string(mY2) + ")";
    cv::putText(img, text, cv::Point(0, 100), cv::FONT_HERSHEY_SIMPLEX, 0.5, cv::Scalar(100, 100, 100));
    cv::imshow("test", img);

    cv::LUT(sample_img, lookUpTable, lut_applied_img); //1DLUTとしてトーンカーブを当てる
    cv::imshow("output", lut_applied_img);

    int key = cv::waitKey(1);
    std::cout << "(" << mX1 << ", " << mY1 << "), (" << mX2 << ", " << mY2 << ")\n";

    if (key == ESCAPE_KEY) break; // esc or enterキーで終了
  }
  std::cout << "program finished";
}
  
  
### 説明

上のプログラムの構成は、➀トーンカーブの曲線（ベジエ曲線）作成、②トーンカーブをもとに1DLUT（cv::LUTを作成）、③LUTをあてた画像を表示、という3部分に分かれています。

#### ベジエ曲線について

以下のサイトを参考にさせていただきましたm(\_\_)m。  
[greweb.me](http://greweb.me/2012/02/bezier-curve-based-easing-functions-from-concept-to-implementation/)

import matplotlib.pyplot as plt

mX1 = 0.25
mY1 = 0.1
mX2 = 0.25
mY2 = 1.0

def A(aA1, aA2):
    return 1.0 - 3.0 * aA2 + 3.0 * aA1

def B(aA1, aA2):
    return 3.0 * aA2 - 6.0 * aA1

def C(aA1):
    return 3.0 * aA1

def CalcBezier(aT, aA1, aA2):
    return ((A(aA1, aA2)*aT + B(aA1, aA2))*aT + C(aA1))*aT

def GetSlope(aT, aA1, aA2):
    return 3.0 * A(aA1, aA2)*aT*aT + 2.0 * B(aA1, aA2) * aT + C(aA1)

def GetTForX(aX):
    # Newton raphson iteration
    aGuessT = aX
    for i in range(4):
        currentSlope = GetSlope(aGuessT, mX1, mX2)
    if currentSlope == 0.0:
        return aGuessT
    currentX = CalcBezier(aGuessT, mX1, mX2) - aX
    aGuessT -= currentX / currentSlope
    return aGuessT

def get(aX):
    if mX1 == mY1 and mX2 == mY2:
        return aX  # linear
    return CalcBezier(GetTForX(aX), mY1, mY2)

x_list = []
y_list = []
for i in range(100):
    x_list.append(float(i)/100.0)
    y_list.append(get(float(i)/100.0))

print(y_list)
plt.scatter(x=x_list, y=y_list)
plt.show()

上のプログラムを実行すると、したのような画像が表示されるはずです。  
![f:id:pythonjacascript:20200828123806j:plain](/images/ppythonjacascript2020082820200828123806.jpg "f:id:pythonjacascript:20200828123806j:plain")

上のプログラムは、mX1, mY1, mX2, mY2の値をもとに、ベジエ曲線を作成しています。なので、自由な形状のトーンカーブが作れるように、OpenCVのmouseCallbackを使ってこれらの値操作できるようにします。  
  
  
#### LUT（LookUpTable）

OpenCVには3DLUTの機能がないので、独自に実装する必要があります：  
[shizenkarasuzon.hatenablog.com](https://shizenkarasuzon.hatenablog.com/entry/2020/08/13/185223)  

しかし、1DのLUTはすでに機能があるのでそれを使わない手はありません。  
試しに、明るさを0.5倍にするようなLUTを当てるプログラムを書いてみます

# include <opencv2/opencv.hpp>
# include <opencv2/highgui.hpp>
# include <opencv2/imgproc/imgproc_c.h>

int main(){
    cv::Mat lookUpTable(1, 256, CV_8U);
    uchar* p = lookUpTable.data;
    for( int i = 0; i < 256; ++i){
        p[i] = i*0.5;
    }
    cv::Mat img = cv::imread("test.jpg");
    cv::LUT(img, lookUpTable, img);
    cv::imshow("test", img);
    cv::waitKey(0);
    return 0;
}

  
実行すると、明るさが半減したtest.jpgが表示されます。  
![f:id:pythonjacascript:20200828125048j:plain](/images/ppythonjacascript2020082820200828125048.jpg "f:id:pythonjacascript:20200828125048j:plain")  

これら2つを応用したのがこの記事の一番上のプログラムです。