---
title: "【C++ OpenCV】VisualStudio 2015で、C++ &amp; OpenCVの環境構築"
emoji: "🤖"
type: "tech"
topics: undefined
published: false
---

* [1\. OpenCVをダウンロードする](#1-OpenCVをダウンロードする)
* [2\. 新規プロジェクトの作成](#2-新規プロジェクトの作成)
* [3\. OpenCV用の設定](#3-OpenCV用の設定)
* [4\. サンプルプログラム](#4-サンプルプログラム)
* [4\. 実行](#4-実行)
* [5\. エラーとその対処法](#5-エラーとその対処法)

### 1\. OpenCVをダウンロードする

以下のサイトから好きなフォルダにOpenCVをダウンロードします。  
<https://opencv.org/releases/>

ダウンロード先のフォルダはどこでも構いません。（後でpathを設定するので）

若しくは、SourceForgeからOpencv○○.exeをダウロード＆実行します  
![f:id:pythonjacascript:20200320234241j:plain](/images/ppythonjacascript2020032020200320234241.jpg "f:id:pythonjacascript:20200320234241j:plain")  
インストール後はこんな感じのファイル構成になっています  
  
  
### 2\. 新規プロジェクトの作成

Visual Studio Community 2015を起動します。

「ファイル」→「新規作成」→「プロジェクト」で、プロジェクトを作成します  
![f:id:pythonjacascript:20200320233937j:plain](/images/ppythonjacascript2020032020200320233937.jpg "f:id:pythonjacascript:20200320233937j:plain")

プロジェクトの設定は次のようにしました。  
コンソールアプリケーションでもWindowsアプリケーションでもOKです。  
![f:id:pythonjacascript:20200320233957j:plain](/images/ppythonjacascript2020032020200320233957.jpg "f:id:pythonjacascript:20200320233957j:plain")  
  
  
### 3\. OpenCV用の設定

まず、VisualStudioの「ソリューションエクスプローラ」で、現在のソリューションを右クリックして、「プロパティ」を開きます。  
![f:id:pythonjacascript:20200320234629j:plain](/images/ppythonjacascript2020032020200320234629.jpg "f:id:pythonjacascript:20200320234629j:plain")

  
「構成プロパティ」→「VC++ディレクトリ」→「インクルードディレクトリ」に  
 **（OpenCVインストール先のディレクトリ）\\opencv\\build\\include** を追加  
  
「構成プロパティ」→「VC++ディレクトリ」→「ライブラリディレクトリ」に  
**（OpenCVインストール先のディレクトリ）\\opencv\\build\\x64\\vc15\\lib**を追加  
![f:id:pythonjacascript:20200320234847j:plain](/images/ppythonjacascript2020032020200320234847.jpg "f:id:pythonjacascript:20200320234847j:plain")  

「C/C++」→「全般」→「追加のインクルードディレクトリ」に、  
 **（OpenCVインストール先のディレクトリ）\\opencv\\build\\include** を追加  
![f:id:pythonjacascript:20200320234829j:plain](/images/ppythonjacascript2020032020200320234829.jpg "f:id:pythonjacascript:20200320234829j:plain")
  
  
「構成プロパティ」→「リンカ」→「入力」→「追加の依存ファイル」に  
**opencv\_world411d.lib**を追加  
![f:id:pythonjacascript:20200320235112j:plain](/images/ppythonjacascript2020032020200320235112.jpg "f:id:pythonjacascript:20200320235112j:plain")  
  
  
環境編集のPathに  
**C:\\Users\\Owner\\Library\\Opencv\\opencv\\build\\x64\\vc15\\lib**  
と  
**C:\\Users\\Owner\\Library\\Opencv\\opencv\\build\\x64\\vc15\\bin**  
を追加

以上の作業を行います  
  
### 4\. サンプルプログラム

# include <opencv2/opencv.hpp>
# include <opencv2/highgui.hpp>
#include <opencv2/imgproc/imgproc_c.h> //CV_AA用

void main() {
	cv::namedWindow("test",cv::WINDOW_NORMAL);

	cv::Mat img = cv::Mat::zeros(500, 500, CV_8UC3); //500×500ピクセルの黒色のMat

	rectangle(img, cv::Point(25, 180), cv::Point(450, 300), cv::Scalar(0, 255, 0), CV_FILLED, 8, 0);
	putText(img, "TEST OPENCV", cv::Point(20, 250), cv::FONT_HERSHEY_SIMPLEX, 2, cv::Scalar(0, 0, 0), 5, CV_AA);

	imshow("test", img);
	cv::waitKey(0);
	return;
}
  
  
### 4\. 実行

上のプログラムをビルドすると、実行ファイル（○○.exe）が作成されます。  
そこで、実行ファイルと同じディレクトリに「**opencv\_world411d.lib**」をコピー＆ペーストします  
（opencv\_world411d.libはダウンロードしたOpenCVのフォルダから探して持ってきます）

  
上のプログラムを実行して以下のように表示されればOKです  
![f:id:pythonjacascript:20200320233917j:plain](/images/ppythonjacascript2020032020200320233917.jpg "f:id:pythonjacascript:20200320233917j:plain")  
  
  
### 5\. エラーとその対処法

Debugモードでビルドすると、

LNK2019	未解決の外部シンボル "void __cdecl cv::resize(...)が関数...で参照されました。
LNK2019	未解決の外部シンボル "void __cdecl cv::rectangle(......) が関数 .......で参照されました。

  
......などのエラーが出ることがありますが、Releaseビルドを行うとこのエラーは解消されました。