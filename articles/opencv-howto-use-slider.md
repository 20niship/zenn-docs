---
title: "【C++】OpenCV標準のスライダーを使用する"
emoji: ""
type: ""
topics: []
published: false
---

* [環境構築](#環境構築)
* [サンプルプログラム](#サンプルプログラム)
* [実行結果](#実行結果)

### 環境構築

[shizenkarasuzon.hatenablog.com](https://shizenkarasuzon.hatenablog.com/entry/2020/03/21/000258)  
  
  
### サンプルプログラム

# include <opencv2/opencv.hpp>
# include <opencv2/highgui.hpp>
# include <iostream>

#define ESCAPE_KEY 27

void do_nothing(int size, void*){}

void main() {
	cv::Mat img = cv::imread("C:/Users/Owner/Desktop/AUL_project/Effects/sample_images/sample.jpg");
	int slider_value = 0;
	int slider_value2 = 0;

	while(1){
		cv::imshow("test", img);
		// http://opencv.jp/opencv-2.1/cpp/user_interface.html
		// cv::createTrackbar([スライダーの名前], [表示されるWindowの名前],[整数型変数へのぽいんた（スライダーの値を格納）] , 
		//                    [スライダー最大値（最小値は0）], [スライダの値が変更されると呼び出される関数のポインタ])
		cv::createTrackbar("slider1", "test", &slider_value, 100, do_nothing);
		cv::createTrackbar("slider2", "test", &slider_value2, 100, do_nothing);

		int key = cv::waitKey(10);
		
		std::cout << slider_value << "\n";

		if(key == ESCAPE_KEY){
			// esc or enterキーで終了
			break;
		}
	}
	std::cout << "program finished";
}
  
  
### 実行結果

![f:id:pythonjacascript:20200403154500j:plain](/images/ppythonjacascript2020040320200403154500.jpg "f:id:pythonjacascript:20200403154500j:plain")