---
title: "【C++ Win32】Windowsアプリケーションを作る！（Win32 APIシリーズ第一回）"
emoji: ""
type: ""
topics: []
published: false
---

* [1.CUIとGUI](#1CUIとGUI)
* [開発環境](#開発環境)
* [実際に作ってみる](#実際に作ってみる)  
   * [プロジェクトの作成](#プロジェクトの作成)  
   * [プログラム](#プログラム)
* [実行結果](#実行結果)
* [解説](#解説)  
   * [Windows Applicationの基本動作](#Windows-Applicationの基本動作)  
   * [Windowクラスの登録（RegisterClassEx関数とWNDCLASSEX構造体）](#Windowクラスの登録RegisterClassEx関数とWNDCLASSEX構造体)  
         * [hInstance](#hInstance)  
         * [style](#style)  
         * [hbrBackground](#hbrBackground)  
         * [hIconとhIconSm](#hIconとhIconSm)  
   * [Windowの作成（CreateWindow関数）](#Windowの作成CreateWindow関数)  
         * [dwStyleについて](#dwStyleについて)  
   * [メッセージの処理（WndProc関数）](#メッセージの処理WndProc関数)

### 1.CUIとGUI

Windowsには、CUI(Character User Interface)とGUI(Graphical User Interface)という二種類のアプリケーションの種類があります。その字の通り、CUIは文字を使ってユーザーとのやり取りを行うアプリケーションで、GUIはボタンや画像などのGraphic要素を使ってユーザーとやり取りを行います。「コンソールアプリケーション」CUIですが、このシリーズで作っていくアプリケーションはWindows Application、「GUI」です。  
![f:id:pythonjacascript:20200225154559j:plain](/images/ppythonjacascript2020022520200225154559.jpg "f:id:pythonjacascript:20200225154559j:plain")  
WindowsでGUIを作るには、QtやImGUIなどのフレームワークを使うなど、色々な方法がありますが、今回は一番基本的（？）な「Windows API（Win32 API）」を使ったGUI作成を行ってみました。  
  
### 開発環境

Visual Studio Community 2015  
Windows 10 Home   
  
### 実際に作ってみる

#### プロジェクトの作成

①Visual Studio を起動します。  
②メニューバーから「File」→「新規作成」→「新しいプロジェクト」をクリック。  
![f:id:pythonjacascript:20200225154616j:plain](/images/ppythonjacascript2020022520200225154616.jpg "f:id:pythonjacascript:20200225154616j:plain")  

③「Win32プロジェクト」を選択して、プロジェクトを作成します。  
![f:id:pythonjacascript:20200225154627j:plain](/images/ppythonjacascript2020022520200225154627.jpg "f:id:pythonjacascript:20200225154627j:plain")  

④「空のプロジェクト」にチェックを入れて、「完了」を押します。  
![f:id:pythonjacascript:20200225154716j:plain](/images/ppythonjacascript2020022520200225154716.jpg "f:id:pythonjacascript:20200225154716j:plain")  

⑤右側の「Solution Explorer」の「ソースファイル」を右クリックして、新しく「main.cpp」を追加します。  
![f:id:pythonjacascript:20200225154727j:plain](/images/ppythonjacascript2020022520200225154727.jpg "f:id:pythonjacascript:20200225154727j:plain")  
「main.cpp」には、下の「プログラム」をコピー＆ペーストしてください。
  
  
このままビルドしてしまうと、

warning C4302: '型キャスト': 'LPWSTR' から 'WORD' へ切り詰めます。
error C2440: '=': 'LPCSTR' から 'LPCWSTR' に変換できません。
'char [9]' から 'LPCWSTR' へ変換できません。

のような文字関連のエラーが出まくります。だから↓

  
⑥プロジェクトの「Property」ウインドウを開きます。  
![f:id:pythonjacascript:20200225154828j:plain](/images/ppythonjacascript2020022520200225154828.jpg "f:id:pythonjacascript:20200225154828j:plain")

・「全般」→「文字セット」を、「マルチバイト文字セットを使用する」にする  
・「C/C++」→「全般」の「SDLチェック」を「いいえ」にする  
![f:id:pythonjacascript:20200225154956j:plain](/images/ppythonjacascript2020022520200225154956.jpg "f:id:pythonjacascript:20200225154956j:plain")![f:id:pythonjacascript:20200225155004j:plain](/images/ppythonjacascript2020022520200225155004.jpg "f:id:pythonjacascript:20200225155004j:plain")  
この2つの作業が必要です。  
  
  
#### プログラム

とりあえず、以下のコードをコピー&ペーストして実行してください。

// main.cpp
#include <windows.h>

LRESULT CALLBACK WndProc(HWND, UINT, WPARAM, LPARAM);
ATOM InitApp(HINSTANCE);
BOOL InitInstance(HINSTANCE, int);

char szClassName[] = "sample02";	//ウィンドウクラス

int WINAPI WinMain(HINSTANCE hCurInst, HINSTANCE hPrevInst,LPSTR lpsCmdLine, int nCmdShow){
	MSG msg;
	BOOL bRet;

	if (!InitApp(hCurInst))
		return FALSE;
	if (!InitInstance(hCurInst, nCmdShow))
		return FALSE;

	while ((bRet = GetMessage(&msg, NULL, 0, 0)) != 0) {
		if (bRet == -1) {
			break;
		}else {
			TranslateMessage(&msg);
			DispatchMessage(&msg);
		}
	}
	return (int)msg.wParam;
}

ATOM InitApp(HINSTANCE hInst){
	WNDCLASSEX wc;
	wc.cbSize = sizeof(WNDCLASSEX);
	wc.style = CS_HREDRAW | CS_VREDRAW;
	wc.lpfnWndProc = WndProc;	//プロシージャ名
	wc.cbClsExtra = 0;
	wc.cbWndExtra = 0;
	wc.hInstance = hInst;//インスタンス
	wc.hIcon = NULL; //アプリのアイコン。.icoファイルをリソースファイルに読み込みここに記入
	wc.hCursor = (HCURSOR)LoadImage(NULL,MAKEINTRESOURCE(IDC_ARROW),IMAGE_CURSOR,0,	0,LR_DEFAULTSIZE | LR_SHARED);
	wc.hbrBackground = (HBRUSH)GetStockObject(BLACK_BRUSH);//Windowの背景色を指定。
	wc.lpszMenuName = NULL;	 // メニュー名。リソースファイルで設定した値を記入
	wc.lpszClassName = (LPCSTR)szClassName;
	wc.hIconSm = NULL; //アプリのアイコンの小さい版。タスクバーに表示されるもの

	return (RegisterClassEx(&wc));
}


//ウィンドウの生成
BOOL InitInstance(HINSTANCE hInst, int nCmdShow){
	HWND hWnd;

	hWnd = CreateWindow(szClassName,
		"Sample Application 1", //Windowのタイトル
		WS_OVERLAPPEDWINDOW, //ウィンドウの種類
		CW_USEDEFAULT,	//Ｘ座標 (指定なしはCW_USEDEFAULT)
		CW_USEDEFAULT,	//Ｙ座標(指定なしはCW_USEDEFAULT)
		CW_USEDEFAULT,	//幅(指定なしはCW_USEDEFAULT)
		CW_USEDEFAULT,	//高さ(指定なしはCW_USEDEFAULT)
		NULL, //親ウィンドウのハンドル、親を作るときはNULL
		NULL, //メニューハンドル、クラスメニューを使うときはNULL
		hInst, //インスタンスハンドル
		NULL);

	if (!hWnd)
		return FALSE;
	ShowWindow(hWnd, nCmdShow);
	UpdateWindow(hWnd);
	return TRUE;
}


//ウィンドウプロシージャ
//ユーザーがWindowを操作したり、Windowが作られたりした場合、この関数が呼び出されて処理を行う。
LRESULT CALLBACK WndProc(HWND hWnd, UINT msg, WPARAM wp, LPARAM lp){
	switch (msg) {
	case WM_CREATE:
		//CreateWindow()でWindowを作成したときに呼び出される。ここでウィジェットを作成する。
		break;

	case WM_DESTROY:
		//ユーザーがWindow右上の×ボタンを押すとここが実行される
		PostQuitMessage(0); //終了メッセージ
		break;

	case WM_PAINT:
		//画面に図形などを描く処理を実装
		break;

	default:
		return (DefWindowProc(hWnd, msg, wp, lp));
	}
	return 0;
}

  
このプログラムはWindows applicationの雛形のようなもので、今後もこのコードを変更似ていくことで機能を追加していきます。  
  
### 実行結果

以下のような画面が出てきたら成功です。  
![f:id:pythonjacascript:20200225155225j:plain](/images/ppythonjacascript2020022520200225155225.jpg "f:id:pythonjacascript:20200225155225j:plain")  
  
  
### 解説

#### Windows Applicationの基本動作

コンソールアプリケーションをC++言語で作るときは、main関数を定義します。これは、ユーザーがApplicationを実行する時に（つまり、〇〇.exeを起動する時に）最初に呼び出されるのがmain()関数だからです。

しかし、windows applicationになると、最初に呼び出されるのは**WinMain関数**です。  
そして、次のような手順でプログラムが実行されます。

> ①ユーザーが実行ファイル（〇〇.exe）をクリックする。  
> ②Windows OS がWinMain 関数を呼び出す  
> ③ウインドウ・クラスを登録する 【RegisterClassEx関数】  
> ④Windowを作成する 【CreateWindow 関数】(ここでアプリの画面ができる)  
> ⑤メッセージが送られてくると、それに対応した処理が行われる【winProc関数】  
> while文で⑤を無限ループ  
> ⑦Application終了メッセージ【WM\_DETORY】を受信すると、Windowを閉じて、⑤の無限ループから抜ける。

というものです。②〜④は初期設定みたいなもので、主な処理は⑤に書きます。

⑤の「メッセージ（Message）」というのは、「ボタンが押された！」or「スペースキーが押された」or「マウスが動いた！」など、ユーザーがApplication上で操作した内容や、Windowsシステムがプログラムに対して送るメッセージを指します。

**WndProc関数**は、このMessageの内容を受け取って、それぞれのメッセージに基づいた処理を行っています。それがWndProc関数のswitch文の中身です。  
![f:id:pythonjacascript:20200225183709j:plain](/images/ppythonjacascript2020022520200225183709.jpg "f:id:pythonjacascript:20200225183709j:plain")  

windowsアプリケーションの動きの概略は上で説明した感じです。ここからは、一つ一つについて詳しく見ていきます。

#### Windowクラスの登録（RegisterClassEx関数とWNDCLASSEX構造体）

ここでは、**WNDCLASSEX**構造体にWindowの特性などを記述して、それを**RegisterClassEx**関数で登録する、という処理を行っています。

typedef struct tagWNDCLASSEXA {
  UINT      cbSize;
  UINT      style;
  WNDPROC   lpfnWndProc;
  int       cbClsExtra;
  int       cbWndExtra;
  HINSTANCE hInstance;
  HICON     hIcon;
  HCURSOR   hCursor;
  HBRUSH    hbrBackground;
  LPCSTR    lpszMenuName;
  LPCSTR    lpszClassName;
  HICON     hIconSm;
} WNDCLASSEXA;

(参考→<https://docs.microsoft.com/en-us/windows/win32/api/winuser/ns-winuser-wndclassexa>)

各変数の役割はこんな感じ

| 名前            | 役割                          | 実装例                                                         |                     |
| ------------- | --------------------------- | ----------------------------------------------------------- | ------------------- |
| cbSize        | WNDCLASSEX構造体のサイズ           | sizeof(WNDCLASSEX)                                          |                     |
| style         | Windowのスタイル                 | CS\_HREDRAW｜｜CS\_VREDRAW                                    | （編集の都合上全角の｜を使っています） |
| lpfnWndProc   | メッセージを処理する関数を指定             | WndProc                                                     |                     |
| cbClsExtra    | 0                           |                                                             |                     |
| cbWndExtra    | 0                           |                                                             |                     |
| hInstance     | Windowのインスタンスハンドル           | hInst (WinMain関数の第一引数)                                      |                     |
| hIcon         | Window左上や.exeファイルで表示されるアイコン | .icoをリソースファイルで読み取って表示。もしくはWindowsデフォルトのものを使う。もしくはNULL       |                     |
| hCursor       | カーソルの形                      | LoadCursor( NULL, IDC\_ARROW )など。LoadImageを使って独自の形にすることもできる |                     |
| hbrBackground | Windowの背景を塗るHBRUSHを指定       | HBRUSH)GetStockObject(WHITE\_BRUSH)だと白の背景になる                |                     |
| lpszMenuName  | メニュー名                       | リソースファイルで設定したメニューをここで読み込む                                   |                     |
| lpszClassName | クラス名                        |                                                             |                     |
| hIconSm       | タスクバーに表示される16px\*16pxのアイコン  | .icoファイルを読み込ませる。デフォルトはNULLにしておけばOK                          |                     |

**補足説明**  

##### hInstance

**インスタンス（Instance）**とは、「メモリ上のプログラム」のことです。Windows OSでは、同じアプリケーションを複数個起動することができます。そして、複数個起動されたアプリをそれぞれ識別するために、各自「**インスタンスハンドル**」という値を持っています。上のプログラムでは、hCurInstが現在(current)のインスタンスハンドルを格納しています。  

##### style

| CS\_BYTEALIGNCLIENT | ウィンドウのクライアント領域をバイト境界（x方向）に揃える                              |
| ------------------- | ---------------------------------------------------------- |
| CS\_BYTEALIGNWINDOW | ウィンドウをバイト境界に（x方向に）位置を合わせる                                  |
| CS\_CLASSDC         | すべてのWindowの間でデバイスコンテキストを共有                                 |
| CS\_DBLCLKS         | Windowの中でダブルクリックが発生したことを通知                                 |
| CS\_DROPSHADOW      | Windowに影を付ける。 SPI\_SETDROPSHADOWによってこのエフェクトはON/OFFが可能      |
| CS\_GLOBALCLASS     | ウィンドウクラスがアプリケーショングローバルクラスであることを示す                          |
| CS\_HREDRAW         | 横サイズが変わったとき，ウインドウ全体を再描画                                    |
| CS\_NOCLOSE         | 「閉じる」ボタンを無効化                                               |
| CS\_OWNDC           | Windowごとにデバイスコンテキストを１つずつ割り当てる                              |
| CS\_VREDRAW         | 縦サイズが変わったとき，ウインドウ全体を再描画                                    |
| CS\_PARENTDC        | 子ウィンドウが親windowに描画できるようにする。（子ウィンドウのクリッピング長方形を親ウィンドウの長方形に設定） |

（参考→[Window Class Styles (Winuser.h) - Win32 apps | Microsoft Docs](https://docs.microsoft.com/ja-jp/windows/win32/winmsg/window-class-styles)）  

##### hbrBackground

以下のような書き方ができます。

hbrBackground = (HBRUSH)GetStockObject(BLACK_BRUSH);
//BLACK_BRUSHのほかにも、
//WHITE_BRUSH（白）,  GRAY_BRUSH（グレー）, LTGRAY_BRUSH（明るいグレー）, DKGRAY_BRUSH（暗いグレー）
//が存在する

//好きな色にしたい場合は、
hbrBackground = CreateSolidBrush(RGB(255,255,0)); //黄色の背景
//のようにしてRGBを255段階で指定できる

##### hIconとhIconSm

これの説明は、「Win32 APIシリーズ第4回の『画像を描画』」で行います。  
  
  
#### Windowの作成（CreateWindow関数）

Windowを作成するには、**CreateWindow**関数、または**CreateWindowEx**関数を使います。

void CreateWindowA(
   lpClassName,//クラス名（WNDCLASSEXのlpszClassNameと同じにする）
   lpWindowName,　//タイトルバーに表示される文字列
   dwStyle, //スタイル（後述）
   x, y, //Windowの初期位置、CW_USEDEFAULTにすると自動的設定される
   nWidth,  nHeight,  //Windowの初期の大きさ、CW_USEDEFAULTにすると自動的設定される
   hWndParent, //親Windowのハンドル。子Windowを作るときに必要になるが、親を作るときはNULL)
   hMenu,  //メニュー（リソースファイルで作ったものをここで取り込む）
   hInstance,  //インスタンス
   lpParam  //だいたいNULLにしておけばOK
);

##### dwStyleについて

[ウィンドウスタイル一覧 - AutoHotkeyJp](https://sites.google.com/site/autohotkeyjp/reference/misc/Styles)  
の様に大量にあるので、主によく使うものを上げておきます。

・通常パターン（特にこだわらない場合、こうしておけばOK）

style = WS_OVERLAPPEDWINDOW;

  
・スクロールバー付き

style = WS_VSCROLL; //縦スクロール
style = HS_VSCROLL; //横スクロール
style = WS_VSCROLL || HS_VSCROLL; //縦横両方スクロール

  
・画面最大化

style = WS_POPUP | WS_VISIBLE;
//最大化をするには、サイズ指定も必要になります。

WS\_VISIBLEは初期状態でWindowを表示するため、WS\_POPUPはタイトルバーなどを非表示にするためです。  
この設定にすると、下の様に表示されます（黄色い部分がWindowです）  
![f:id:pythonjacascript:20200225170516j:plain](/images/ppythonjacascript2020022520200225170516.jpg "f:id:pythonjacascript:20200225170516j:plain")  
（WS\_POPUPにするときは、Windowのwidthとheightを数値で設定する必要があります（CW\_USEDEFAULTだと表示されなかった。）  
 また、初期状態のWindowの位置はCW\_USEDEFAULTにすると(0,0)です）  
  
  
#### メッセージの処理（WndProc関数）

ここでは、ユーザーやWindows OSから送られてきたメッセージをもとに処理を行います。  
例えば…  
・Windowが作成されると、**WM\_CREATE**メッセージが送られてきます。すると、switch文のcase WM\_CREATE:野中が実行されます。ここでは、Windowの中に設置するボタンを作るなどの初期設定を行います。  
・Windowの上に別のWindowが表示されて、画面の再描画が必要な時、**WM\_PAINT**メッセージが送られてきます。すると、case WM\_PAINTの中が実行され、画像や図形の再描画が行われます。  
….など。

Windows Applicationは、何かメッセージが来た時に処理をする、という「メッセージ駆動型」で動いています。  
ここで、WndProcに送られてくるメッセージを全て説明することはできないので、随時必要になった時に紹介したいと思います。