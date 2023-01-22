---
title: "【C++/OpenCV】スクリーンキャプチャした映像を表示 or 動画ファイルとして保存"
emoji: ""
type: ""
topics: []
published: false
---

タイトルの通りです。  
C++を使ってスクリーンキャプチャを行います。

* [プログラム1（キャプチャ映像表示）](#プログラム1キャプチャ映像表示)
* [実行結果1](#実行結果1)
* [プログラム2（キャプチャした映像をmp4で保存）](#プログラム2キャプチャした映像をmp4で保存)
* [実行結果2](#実行結果2)
* [参考文献](#参考文献)

ちなみに、Python版は↓です。  
[shizenkarasuzon.hatenablog.com](https://shizenkarasuzon.hatenablog.com/entry/2020/03/23/005440)  
  
ではまず、ただ単にモニター映像をリアルタイムで表示するプログラムから（録画機能は無し）

### プログラム1（キャプチャ映像表示）

# include <opencv2/opencv.hpp>
# include <opencv2/highgui.hpp>
# include <opencv2/videoio.hpp>
#include <Windows.h>

int main(int argc, char * const argv[]){
  // モニターサイズ取得
  HWND desktop = GetDesktopWindow();
  RECT rect;
  GetWindowRect(desktop, &rect);


  // DIBの情報を設定する
  BITMAPINFO bmpInfo;
  bmpInfo.bmiHeader.biSize = sizeof(BITMAPINFOHEADER);
  bmpInfo.bmiHeader.biWidth = rect.right;
  bmpInfo.bmiHeader.biHeight = -rect.bottom;
  bmpInfo.bmiHeader.biPlanes = 1;
  bmpInfo.bmiHeader.biBitCount = 24; //cv::Matの画像をアルファチャンネル有りにする場合はは32;
  bmpInfo.bmiHeader.biCompression = BI_RGB;


  LPDWORD lpPixel;
  HDC hDC = GetDC(desktop);
  HBITMAP hBitmap = CreateDIBSection(hDC, &bmpInfo, DIB_RGB_COLORS, (void**)&lpPixel, NULL, 0);
  HDC hMemDC = CreateCompatibleDC(hDC);
  SelectObject(hMemDC, hBitmap);

  cv::Mat monitor_img;
  monitor_img.create(rect.bottom, rect.right, CV_8UC3); //RGBのみ。アルファちゃんねるを加えるにはCV_8UN4
  
  // 画像表示用のWindowを作成（ウィンドウの大きさを640x480に固定する）
  cv::namedWindow("Screenshot", cv::WINDOW_NORMAL);
  cv::resizeWindow("Screenshot", 640, 480);

  while (true){
    //hDCの画像（スクリーンショット）をhMemDCにコピーする
    BitBlt(hMemDC, 0, 0, rect.right, rect.bottom, hDC, 0, 0, SRCCOPY);
    // hMemDCの内容をcv::Matの画像（monitor_img）にコピー
    GetDIBits(hMemDC, hBitmap, 0, rect.bottom, monitor_img.data, (BITMAPINFO *)&bmpInfo, DIB_RGB_COLORS);  //copy from hwindowCompatibleDC to hbwindow

    cv::imshow("Screenshot", monitor_img); // スクリーンショットの表示    
    if (cv::waitKey(1) == 27) break; //Escキーで終了
  }

  //メモリ開放
  ReleaseDC(desktop, hDC);
  DeleteDC(hMemDC);
  DeleteObject(hBitmap);

  return 0;
}
  
  
### 実行結果1

プログラムを実行すると、下の画像の様にスクリーンの映像がリアルタイムで表示されます  
![f:id:pythonjacascript:20200625013755j:plain](/images/ppythonjacascript2020062520200625013755.jpg "f:id:pythonjacascript:20200625013755j:plain")  
終了するにはEscキーを押してください。  
  
  
### プログラム2（キャプチャした映像をmp4で保存）

上のプログラム1を少し改良して、スクリーンキャプチャした映像をmp4形式で録画してみようと思います。

\# include <opencv2/opencv.hpp> \# include <opencv2/highgui.hpp> \# include <opencv2/videoio.hpp> #include <Windows.h> #include <string> #include <vector> #include <time.h> #include <conio.h> void main() {/\* デスクトップのサイズ \*/ HWND desktop = GetDesktopWindow(); RECT rect; GetWindowRect(desktop, &rect);int width = rect.right;int height = rect.bottom;// DIBの情報を設定する BITMAPINFO bmpInfo; bmpInfo.bmiHeader.biSize = sizeof(BITMAPINFOHEADER); bmpInfo.bmiHeader.biWidth = width; bmpInfo.bmiHeader.biHeight = -height; bmpInfo.bmiHeader.biPlanes = 1; bmpInfo.bmiHeader.biBitCount = 24; //cv::Matの画像をアルファチャンネル有りにする場合はは32; bmpInfo.bmiHeader.biCompression = BI\_RGB; LPDWORD lpPixel; HDC hDC = GetDC(desktop); HBITMAP hBitmap = CreateDIBSection(hDC, &bmpInfo, DIB\_RGB\_COLORS, (void\*\*)&lpPixel, NULL, 0); HDC hMemDC = CreateCompatibleDC(hDC); SelectObject(hMemDC, hBitmap); cv::Mat monitor\_img; monitor\_img.create(height, width, CV\_8UC3); //RGBのみ。アルファちゃんねるを加えるにはCV\_8UN4 //スクリーンショットの映像一覧 std::vector<cv::Mat> screen\_shot\_list; std::cout << "Start Recording !!" << std::endl;clock\_t start = clock(); cv::Mat cloned\_img;while (\_kbhit() == 0) { //何か入力があるまで録画し続ける //hDCの画像（スクリーンショット）をhMemDCにコピーする BitBlt(hMemDC, 0, 0, width, height, hDC, 0, 0, SRCCOPY);// hMemDCの内容をcv::Matの画像（monitor\_img）にコピー GetDIBits(hMemDC, hBitmap, 0, height, monitor\_img.data, (BITMAPINFO \*)&bmpInfo, DIB\_RGB\_COLORS); //copy from hwindowCompatibleDC to hbwindow cloned\_img = monitor\_img.clone(); // cv::Matはポインターのようなやつなので、cloneして実体を作らないと正しくpush\_backできない screen\_shot\_list.push\_back(cloned\_img); }//cv::String fname("SC.mp4"); clock\_t end = clock();double duration = static\_cast<double\>(end - start) / CLOCKS\_PER\_SEC \* 1000.0;int fps = int(float(screen\_shot\_list.size() \* 1000)/ duration); std::cout << "