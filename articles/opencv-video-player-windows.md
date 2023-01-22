---
title: "【C++/OpenCV】動画のプレイヤーを作成する"
emoji: "🤖"
type: "tech"
topics: undefined
published: false
---

ん？  
cv::VideoCapture()を使えばいいじゃないかって？  
そうなんだけど、そうなんですけど......。とりあえず読んでください。  
  
* [サンプルプログラム](#サンプルプログラム)
* [改良（音声再生）](#改良音声再生)  
   * [pszSound](#pszSound)  
   * [hmod](#hmod)  
   * [fdwSound](#fdwSound)
* [問題1（スピード調整）](#問題1スピード調整)
* [問題2（処理性能の問題）](#問題2処理性能の問題)
* [cap >> frame と cap.read()の違い](#cap--frame-と-capreadの違い)
* [結論（動画を音声付きで再生するプログラム最終版）](#結論動画を音声付きで再生するプログラム最終版)  
   * [A) cap >> imgを使う場合](#A-cap--imgを使う場合)  
   * [A) cap.read(img)を使う場合](#A-capreadimgを使う場合)

### サンプルプログラム

とりあえず、OpenCVの基礎の基礎のみを利用して作ったサンプルプログラムがこちら。

# include <opencv2/opencv.hpp>
# include <opencv2/highgui.hpp>
# include <opencv2/videoio.hpp>

#define WINDOW_NAME "Video"

void main() {
	cv::Mat img;
	cv::namedWindow(WINDOW_NAME ,cv::WINDOW_NORMAL);
	cv::VideoCapture cap("C:/sample.mp4"); //このように、動画ファイルのパスを指定する
	cv::moveWindow(WINDOW_NAME , 0, 0);
	cv::setWindowProperty(WINDOW_NAME , cv::WND_PROP_FULLSCREEN, cv::WINDOW_FULLSCREEN);  //フルスクリーンにする

	int v_w = cap.get(cv::CAP_PROP_FRAME_WIDTH); //縦の大きさ
	int v_h = cap.get(cv::CAP_PROP_FRAME_HEIGHT); //横の大きさ
	int max_frame = cap.get(cv::CAP_PROP_FRAME_COUNT); //フレーム数
	int fps = cap.get(cv::CAP_PROP_FPS); //フレームレート
	//cap.set(CAP_PROP_POS_FRAMES, 100); //100フレーム目から再生

	for (int CFN = 0; CFN < max_frame; CFN++) {
		cap >> img; //1フレーム分取り出してimgに保持させる
		cv::imshow("Video", img);

		int key = cv::waitKey(33); // 表示のために最低1ms待つ
		if(key == 27){ break; }// esc or enterキーで終了
	}
}

  
このプログラムでも、普通に動画を流すことはできます。  
では、ここから色々改良していきます。  
  
### 改良（音声再生）

OpenCVは画像編集ライブラリなので、音声関連には全く対応していません。  
なので、動画の音声を再生するには使う必要があります。  
このブログでは一番簡単な**PlaySound()関数**を使ってみます。

//事前準備
#include <windows.h>
#pragma comment(lib,"Winmm.lib")//この行の代わりに「追加の依存ファイル」に追加しても良い
#include <mmsystem.h>

//音源の再生（main関数内）
PlaySound("C:/sample.wav", NULL, SND_FILENAME | SND_ASYNC);  //このように、音声ファイルのパスを指定する

//音源の停止（main関数内の最後の行）
PlaySound(NULL, NULL, 0);

  
これらの行を上のコードの適切な位置に追加すると（最終的なコードはこの記事の一番下に乗っています）、動画と一緒に音声も流れ出します。

PlaySound関数について解説すると、

BOOL PlaySound(
   LPCTSTR pszSound,
   HMODULE hmod,
   DWORD   fdwSound
);

#### pszSound

再生する音声ファイルのパスを設定します。最大256文字です。NULLにすると、現在再生している音声が停止されます（一時停止ではなく停止です。もう一度PlaySound関数を呼ぶと最初から再生されます）

#### hmod

**SND\_RESOURCE**がfdwSoundで指定されていない限り、hmodはNULLでなければなりません。  
ロードするリソースを含む実行可能ファイルのハンドルを表します。

#### fdwSound

再生モードを設定します。

| 設定値           | 役割                                                                                                |
| ------------- | ------------------------------------------------------------------------------------------------- |
| SND\_ASYNC    | 非同期で再生され、PlaySound関数はサウンドの開始直後にreturnされます。再生された音声を終了するには、PlaySound(NULL, NULL, 0);を実行します          |
| SND\_SYNC     | 音声は同期的に再生され、再生し終わったときにPlaySound関数がreturnされます。デフォルトの再生モード                                          |
| SND\_FILENAME | pszSound（第一引数）がファイル名を表すときに使用。音声ファイルが見つからなかった場合、PlaySoud関数はSND\_NODEFAULTがセットされていなければデフォルトのサウンドを再生 |
| SND\_LOOP     | pszSoundがNULLでPlaySound関数が呼び出されるまで、音源をリピートします。SND\_LOOPをセットするには、SND\_ASYNC もセットしなければなりません         |

このくらいしか使わない気がするけど、他にもいろいろあって↓

| SND\_MEMORY    | pszSound がメモリー上に読み込まれた音声ファイルをさしているときに使用します。                                                                                                                                                   |
| -------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| SND\_NODEFAULT | 音声ファイルが見つからなかっとき、SND\_NODEFAULTがセットされている場合は何も再生せずにreturnします。SND\_NODEFAULTがセットされていない場合、デフォルトの音源が再生されます                                                                                        |
| SND\_NOSTOP    | 指定されたサウンドイベントは、同じプロセスで既に再生されている別のサウンドイベントに譲ります。サウンドの生成に必要なリソースが別のサウンドの再生でビジーであるためにサウンドを再生できない場合、関数は要求されたサウンドを再生せずにすぐに「FALSE」を返します。                                                            |
| SND\_RESOURCE  | pszSoundパラメータはリソース識別子です。 hmod-リソースを含むインスタンスを識別する必要があります。                                                                                                                                      |
| SND\_SENTRY    | SND\_SENTRYがセットされている時、PlaySounｄ関数が呼ばれるとSoundSentryイベント（音声のvisual cueを表示するためのもの）が起こります。                                                                                                        |
| SND\_SYSTEM    | SND\_SYSTEMが設定されている場合、音声はシステム通知サウンドに割り当てられます。システム音量制御プログラム（SndVol）は、システム通知音を制御する音量スライダーを表示します。このフラグを設定すると、音量スライダーの制御下にサウンドが配置されます。SND\_SYSTEMが設定されていない場合は、音声はアプリケーションシステムのデフォルトサウンドに割り当てられます。 |

  
音声が再生されている途中にOpenCVで動画表示を行うため、PlaySound関数は音源再生を始めたらすぐにreturnする必要があります。よって、

PlaySound("C:/sample.wav", NULL, SND_FILENAME | SND_ASYNC);  

  
のように、**SND\_ASYNC**をセットしてください。  
これで、音声を再生できるようになりました！！v(￣Д￣)v ｲｴｲ  
  
  
### 問題1（スピード調整）

しかし、問題が発生します。

> 音声と動画のフレームが一致していない！！

という問題です。  
原因は勿論、動画を1フレームだけ表示するのにかかる時間が一定ではないため、動画の再生スピードがぐちゃぐちゃになっているのです。

これを解決するために、**waitKey()の待ち時間の値を動的に調整する**という対策を取ります。

まず、➀clock()関数を使って再生開始からどれくらい時間がたったのかをミリ秒単位で取得します。  
次に、②動画の現在の再生フレームとFPSから、動画再生開始からの理想の経過時間を算出します。  
そして、➀－②の値をwaitkey()の引数に入れ込むことで、きちんとしたスピードで動画が再生されるようになります。

これを実装すると、こうなります。

# include <opencv2/opencv.hpp>
# include <opencv2/highgui.hpp>
# include <opencv2/videoio.hpp>

#include <windows.h>
#include <time.h>     // for clock()

#define ESCAPE_KEY 27
#define PLAY_SPEED 1.0

void main() {
	cv::Mat img;
	cv::namedWindow("Video",cv::WINDOW_NORMAL);
	cv::VideoCapture cap("C:/sample.mp4");

	int max_frame = cap.get(cv::CAP_PROP_FRAME_COUNT); //フレーム数
	clock_t start = clock();    // 動画再生時刻

	for (int CFN = 0; CFN < max_frame; CFN++) {
		cap >> img; //1フレーム分取り出してimgに保持させる
		cv::imshow("Video", img);

		clock_t end = clock(); //現在時刻を取得
		int wait_time = int((double)CFN / (30.0 * PLAY_SPEED) * 1000.0 - (double)(end - start));
		int wait_time2 = (MIN(MAX(wait_time, 1), 50));

		cv::waitKey(wait_time2); // 表示のために最低1ms待つ
	}
}

  
waitkey()の引数をwaitkey(33)からwaitkey(変数)にすることで解決しています。

これで、音声と動画フレームの再生がずれなくなったはずです！！v(￣Д￣)v ｲｴｲ  
  
  
### 問題2（処理性能の問題）

だがしかし！！まだ問題があります。  
もしこれにエフェクトなどを追加していくと、メインの処理は下の様に簡略化して書くことができます。

for (int CFN = 0; CFN < max_frame; CFN++) {
	cap >> img; //1フレーム分取り出してimgに保持させる
	cv::Mat img2;
	
	img2 = myEffect(img);//ここで画像にいろんなエフェクトをかける

	cv::imshow("Video", img2);

	clock_t end = clock(); //現在時刻を取得
	int wait_time = int((double)CFN / (30.0 * PLAY_SPEED) * 1000.0 - (double)(end - start));
	int wait_time2 = (MIN(MAX(wait_time, 1), 50));

	cv::waitKey(wait_time2); // 表示のために最低1ms待つ
}

  
問題というのは、ここでmyEffect関数がとてつもなく重たい処理をする場合に起こります。  
音声は再生されているから動画もリアルタイムで表示する必要があります。しかし、↑のプログラムだと、必ずすべてのフレームを読み込むので、いつか処理が追い付かなり、動画の再生速度が遅くなってくる...ということが起こります。

よって、動画の1フレーム表示にかかる時間が増えた場合に、**強引に表示FPSを落として**、なんとしてもリアルタイムで表示する。という処理が必要です。

以下がそのためのプログラムです。

for (int CFN = 0; CFN < max_frame; CFN++) {
	cap >> img; //1フレーム分取り出してimgに保持させる

	Sleep(20); //画像処理で何か重たい処理をする場合, ここでは20ms画像処理に時間がかかるとする
	cv::imshow("screen", img); //画像を表示

	clock_t end = clock();
	int wait_time = int((double)CFN / (30.0 * PLAY_SPEED) * 1000.0 - (double)(end - start));
		
	//追加部分
	if (wait_time < -40) {
		int loop_num = -wait_time / 33.3;  //送れているコマ数を計算（FPS=30の動画の場合、一コマ当たり33.3ms）
		for (int i = 0; i < loop_num; i++) {
			cap >> img;  //現在の表示フレームが遅れている場合、その分だけフレームナンバーを進める
			CFN++;
		}
		std::cout << loop_num << "\n";
	}

	int key = cv::waitKey(int(min(max(wait_time, 1), 50))); // 表示のために1ms待つ
		
	if(key == ESCAPE_KEY){
		// esc or enterキーで終了
		cv::destroyAllWindows();
		return;
	}
}

  
変更したのは↑のfor文の中の、「**//追加部分**」の所です。  
wait\_timeは、設定した速度で動画を再生するためにwaitkey()で何ms待てばよいかを表す値ですが、動画の表示や画像処理が追い付かなくなると、この値がマイナスになります。  
その時に、遅れている分だけ余計に動画のフレームを進めようっていうことです。うまく説明できないのでコード見てください。  
  
  
### cap >> frame と cap.read()の違い

cv::VideoCapture cap("sample.mp4");
cv::Mat img;
cap >> img;  //一フレーム読み込む

の代わりに

cap.read(img);  //一フレーム読み込む

を使うことができます。  
前者の場合、動画を最終フレームまで読み込んでしまって、もう次のフレームがない状態で

cap >> img;

をすると、その時点でフリーズするのに対し、後者では

cap.read(img); //1フレーム分取り出してimgに保持させる
if (img.empty()) { //読み込んだ画像がempty、つまり最終フレームに達したとき
	cap.set(cv::CAP_PROP_POS_FRAMES, 0);  //また最初から再生し直す
	cap.read(img);
}

と書くことができます。  
因みに、cap >> img と cap.read(img) で体感的な速度の差はありません。  
  
  
### 結論（動画を音声付きで再生するプログラム最終版）

というわけで、これまでの問題点と解決策をすべてまとめた結果が下のプログラムになります。

#### A) cap >> imgを使う場合

# include <opencv2/opencv.hpp>
# include <opencv2/highgui.hpp>
# include <opencv2/videoio.hpp>
# include <iostream>

#include <windows.h>
#include <time.h>     // for clock()

#pragma comment(lib,"Winmm.lib")//この行の代わりに「追加の依存ファイル」に追加しても良い
#include <mmsystem.h>

#define PLAY_SPEED 1.0
#define ESCAPE_KEY 27

void main() {
	cv::Mat img;
	cv::VideoCapture cap("sample.mp4"); //動画読み込み（pathを設定してください）
	int max_frame = cap.get(cv::CAP_PROP_FRAME_COUNT); //フレーム数

	clock_t start = clock();    // スタート時間
	PlaySound("sample.wav", NULL, SND_FILENAME | SND_ASYNC); //音声読み込み(pathを設定してください)

	for (int CFN = 0; CFN < max_frame; CFN++) {
		cap >> img; //1フレーム分取り出してimgに保持させる

		//Sleep(20);    画像処理で何か重たい処理をする場合, ここでは20ms画像処理に時間がかかるとする
		//img = myEffect(img);  のように、ここで表示する動画にリアルタイムでエフェクトをかける
		cv::imshow("screen", img); //画像を表示

		clock_t end = clock();
		int wait_time = int((double)CFN / (30.0 * PLAY_SPEED) * 1000.0 - (double)(end - start));
		
		//追加部分
		if (wait_time < -40) {
			int loop_num = -wait_time / 33.3;  //送れているコマ数を計算（FPS=30の動画の場合、一コマ当たり33.3ms）
			for (int i = 0; i < loop_num; i++) {
				cap >> img;  //現在の表示フレームが遅れている場合、その分だけフレームナンバーを進める
				CFN++;
			}
			std::cout << loop_num << "\n";
		}

		int key = cv::waitKey(int(min(max(wait_time, 1), 50))); // 表示のために1ms待つ
		
		if(key == ESCAPE_KEY){
			// esc or enterキーで終了
			cv::destroyAllWindows();
			return;
		}
	}
	PlaySound(NULL, NULL, 0);
	cv::destroyWindow("screen");
}

#### A) cap.read(img)を使う場合

# include <opencv2/opencv.hpp>
# include <opencv2/highgui.hpp>
# include <opencv2/videoio.hpp>
# include <iostream>

#include <windows.h>
#include <time.h>     // for clock()

#pragma comment(lib,"Winmm.lib")//この行の代わりに「追加の依存ファイル」に追加しても良い
#include <mmsystem.h>


#define PLAY_SPEED 1.0
#define ESCAPE_KEY 27

void main() {
	cv::Mat img;
	cv::VideoCapture cap("sample.mp4"); 
	int max_frame = cap.get(cv::CAP_PROP_FRAME_COUNT); //フレーム数
	clock_t start = clock();    // スタート時間

	PlaySound("sample.wav", NULL, SND_FILENAME | SND_ASYNC);
	int CFN = 0;

	while(1){
		cap.read(img); //1フレーム分取り出してimgに保持させる
		CFN++;

		if (img.empty()) {  //最終フレームに達したら、
			cap.set(cv::CAP_PROP_POS_FRAMES, 0); //もう一度最初から再生しなおす
			cap.read(img);
		}

		cv::imshow("screen", img); //画像を表示

		clock_t end = clock();
		int wait_time = int((double)CFN / (30.0 * PLAY_SPEED) * 1000.0 - (double)(end - start));
		
		//追加部分
		if (wait_time < -40) {
			int loop_num = -wait_time / 33.3;  //送れているコマ数を計算（FPS=30の動画の場合、一コマ当たり33.3ms）
			for (int i = 0; i < loop_num; i++) {
				cap >> img;  //現在の表示フレームが遅れている場合、その分だけフレームナンバーを進める
				CFN++;
			}
			std::cout << loop_num << "\n";
		}

		int key = cv::waitKey(int(min(max(wait_time, 1), 50))); // 表示のために1ms待つ
		
		if(key == ESCAPE_KEY){
			// esc or enterキーで終了
			cv::destroyAllWindows();
			return;
		}
	}
	PlaySound(NULL, NULL, 0);
	cv::destroyWindow("screen");
}