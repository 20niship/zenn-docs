---
title: "【C++】MinGW (g++)とCMakeでOpenCVとopencv-contributeをビルドする（Windows）"
emoji: "🤖"
type: "tech"
topics: []
published: false
---

* [バージョン](#バージョン)
* [はじめに](#はじめに)
* [ビルド方法](#ビルド方法)
* [HelloWorld](#HelloWorld)
* [HelloWorld実行](#HelloWorld実行)

### バージョン

$ cmake --version
cmake version 3.17.2

$ make -v
GNU Make 3.81

$ mingw32-make -v
GNU Make 4.2.1

$ g++ -v 
gcc version 8.1.0 

### はじめに

タイトルの通りです。開発環境がVisual Studioを使っているならば、[ココ](https://opencv.org/releases/)の「Windows」ボタンからビルド済みのものをインストールできますが、g++, gccでビルドされたものは見つかりませんでした。  
\[追記\]...と思っていたら、ありました！！  
<https://github.com/huihut/OpenCV-MinGW-Build>  
まぁでも最新バージョンではないっぽいし、自分でビルドすることにしました。

  
ということで、OpenCVの適当なバージョンをダウンロードして、cmakeで普通にやろうとしたのですが、

$ cmake [directry]

ってしても、抑もConfigureが通りません。そして、そのままmakeをしても、

gcc: error: long: No such file or directory
C:\PROGRA~1\MINGW-~1\X86_64~1.0-P\mingw64\bin\windres.exe: preprocessing failed.
make[2]: *** [modules/core/CMakeFiles/opencv_core.dir/vs_version.rc.obj] エラー 1
make[1]: *** [modules/core/CMakeFiles/opencv_core.dir/all] エラー 2
make: *** [all] エラー 2

  
もちろんですがエラーが出てしまいました。

ということで、CMakeのGUIを使って設定する必要があります。  
  
  
### ビルド方法

**1\. opencv-3.4 とcontributeをダウンロードする**  
ここ↓から、最新バージョンのOpenCVをgit cloneしてきます。  
[github.com](https://github.com/opencv/opencv)  
そして、ここ↓から最新バージョンのOpencv-contributeをgit cloneしてきます。  
[github.com](https://github.com/opencv/opencv%5Fcontrib)

> Contributeとは：OpenCvの追加物みたいなもので、顔認識やセグメンテーションのような機能を追加できます（機能一覧については[ここ](https://www.atmarkit.co.jp/ait/articles/1709/12/news135.html)を見てください）。十分にテストされていないAPIなので、OpenCV本体と一緒にするべきではない、という考えのようです。

**2\. opencv-3.4の中にbuildフォルダを作る（名前は何でもOK）**

> opencv-3.4  
> ↳build\_mingw (←作成)  
> opencv\_contrib-3.4

みたいな感じです。

  
**3\. Cmake GUIを開く**  
CMakeGUIを開いて、一番上にある「Where is the source code」と「Where to build the binaries」を設定します。  
ソースコードの位置は hoge/opencv-3.4、ビルド先にはhoge/opencv-3.4/build\_mingw みたいな感じで先ほど作ったフォルダを指定します。  
そして、Configureボタンを押します。

**4\. コンパイラをMinGWに指定する**  
Configureボタンを押すと、以下の様なコンパイラーを選択する画面が表示されます。  
![f:id:pythonjacascript:20201113010159j:plain](/images/ppythonjacascript2020111320201113010159.jpg "f:id:pythonjacascript:20201113010159j:plain")  
この写真のように設定してFinishを押します 

**7\. 以下の様な設定にします。**  
Configureが終わると、未解決の項目が赤くなって大量に表示されているはずです。ここからビルド設定を行います。ここは個人の環境によって変わるかもしれませんが、僕の環境では以下の設定でビルドが通りました。

* BUILD\_JAVAのチェックを外す
* BUIKD\_PYTHONのチェックを外す
* BUILD\_OPENEXRのチェックを外す
* WITH\_IPPのチェックを外す
* ENABLE\_PRECOMPILED\_HEADERS のチェックを外す
* WITH\_OPENGL のチェックを付ける
* OPENCV\_ENABLE\_NONFREEのチェックを付ける
* OPENCV\_EXTRA\_MODULES\_PATH のパスを「hoge/opencv\_contrib-3.4/modules」に設定する
* BUILD\_opencv\_worldのチェックを付ける（これをチェックすると、libファイルやdllファイルが「opencv\_world\_\[version\].dll」の様な名前で一つにまとめてくれる）

  
**7\. DOPENCV\_ALLOCATOR\_STATS\_COUNTER\_TYPEを追加**  
CMake GUIの右上に「Add Entry」というボタンがあるので、そこを押します。  
そして、  
![f:id:pythonjacascript:20201113010950j:plain](/images/ppythonjacascript2020111320201113010950.jpg "f:id:pythonjacascript:20201113010950j:plain")

* Name = DOPENCV\_ALLOCATOR\_STATS\_COUNTER\_TYPE
* Type = STRING
* Value = int64\_t（上写真ではDescriptionの所に書き込んでいますがミスですm(\_\_)m。Valueが正解）

となるように設定します。

![f:id:pythonjacascript:20201113011135j:plain](/images/ppythonjacascript2020111320201113011135.jpg "f:id:pythonjacascript:20201113011135j:plain")  
このように表示されればOKです。

  
**10\. 「Configure」→「Generate」**  
これで、一通りの設定が終わったので、もう一度Configure をしてみましょう！  
エラーが出ずに最後までConfigureできたら成功です。Generateもします。

  
**11\. コマンドプロンプトで「make」!**  
ここまで順調に来ていれば、mingw\_buildフォルダ(2.で作成したフォルダ)にMakeFileがあるはずなので、

$ cd mingw_make  #(2.で作成したフォルダ)
$ make

でmakeします。

上手くいけば、以下のファイルが生成されているはずです

* mingw\_make/bin/libopencv\_world3412.dll
* mingw\_make/lib/libopencv\_world3412.dll.a

上の二つがあればビルドはできています！！(\*'ω'\*)  
  
  
### HelloWorld

適当にプログラムを作ります。HelloWorldという文字を出力するプログラムです。

//main.cpp
#include <opencv2/opencv.hpp>
int main() {
  cv::Mat image(200, 800, CV_8UC3);
  
  cv::String text = "Hello, world";
  cv::putText(image, text, cv::Point(100,100), cv::FONT_HERSHEY_SIMPLEX, 3, cv::Scalar(255,255,0), 5, cv::LINE_AA);// テキストを描画

  // 画像を表示
  cv::imshow("hello_world", image);
  cv::waitKey(0);
  return 0;
}
  
  
CmakeListsは以下のようになります。

# CmakeLists.txt
cmake_minimum_required(VERSION 2.8)

set(PROJECT_NAME Test)
project(${PROJECT_NAME} CXX)

set(OpenCV_DIR "hogw/mingw_build") #2.で作成したフォルダ
find_package(OpenCV REQUIRED)
if(OpenCV_FOUND)
    include_directories(${OpenCV_INCLUDE_DIRS})
    link_libraries(${OpenCV_LIBS})
endif()

add_executable(${PROJECT_NAME} main.cpp main.hpp)
target_link_libraries(${PROJECT_NAME} ${OpenCV_LIBRARIES})

  
コマンドプロンプトで、

$ cd (上で書いた.cppとCMakeFilesがあるディレクトリ)
$ mkdir build 
$ cmake .. -G"MinGW Makefiles"  # コンパイラをMinGWに指定する
$ make

これでTest.exeが生成されればOKです！！

動的ライブラリファイル「(2.で作ったフォルダ)/bin/libopencv\_world3412.dll」をTest.extと同じ階層にコピーしてきます。  
これで、OpenCVのプログラムを実行できるはずです、、、！

### HelloWorld実行

実行できたぁ～～  
![f:id:pythonjacascript:20201113012407j:plain](/images/ppythonjacascript2020111320201113012407.jpg "f:id:pythonjacascript:20201113012407j:plain")