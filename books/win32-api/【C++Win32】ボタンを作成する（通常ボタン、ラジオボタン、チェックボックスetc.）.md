---
title: "【C++ Win32】ボタンを作成する（通常ボタン、ラジオボタン、チェックボックス etc.）"
emoji: ""
type: ""
topics: []
published: false
---

* [最もシンプルなプログラム](#最もシンプルなプログラム)
* [実行結果](#実行結果)
* [解説](#解説)  
   * [1.ボタンの作成](#1ボタンの作成)  
   * [2.ボタンの有効化/無効化](#2ボタンの有効化無効化)  
   * [3\. ボタンの文字のフォントを設定](#3-ボタンの文字のフォントを設定)  
   * [4\. ボタンの文字の色/文字の背景色を変更](#4-ボタンの文字の色文字の背景色を変更)  
   * [5.ボタンの状態を取得する](#5ボタンの状態を取得する)  
   * [6.ボタンの状態を変更](#6ボタンの状態を変更)

### 最もシンプルなプログラム

ボタンをクリックしたら「ボタンが押された！」とメッセージが表示されるだけの、簡単なプログラムです。

#include <windows.h>
#include <windowsx.h>

#define ID_BUTTON 100   //追加

LRESULT CALLBACK WndProc(HWND, UINT, WPARAM, LPARAM);
ATOM InitApp(HINSTANCE);
BOOL InitInstance(HINSTANCE, int);

HINSTANCE hInst;  
HWND hBtn;//追加

char szClassName[] = "menu02";
HWND hParent;

int WINAPI WinMain(HINSTANCE hCurInst, HINSTANCE hPrevInst,	LPSTR lpsCmdLine, int nCmdShow){
	MSG msg;
	BOOL bRet;
	if (!InitApp(hCurInst))	return FALSE;
	if (!InitInstance(hCurInst, nCmdShow))	return FALSE;

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
	wc.lpfnWndProc = WndProc;
	wc.cbClsExtra = 0;
	wc.cbWndExtra = 0;
	wc.hInstance = hInst;
	wc.hIcon = NULL;
	wc.hCursor = (HCURSOR)LoadImage(NULL, MAKEINTRESOURCE(IDC_ARROW), IMAGE_CURSOR, 0, 0, LR_DEFAULTSIZE | LR_SHARED);
	wc.hbrBackground = (HBRUSH)GetStockObject(DKGRAY_BRUSH);
	wc.lpszMenuName = NULL; //MAKEINTRESOURCE(MYMENU);	//メニュー名
	wc.lpszClassName = (LPCSTR)szClassName;
	wc.hIconSm = NULL;
	return (RegisterClassEx(&wc));
}

BOOL InitInstance(HINSTANCE hInst, int nCmdShow){
	HWND hWnd;
	hWnd = CreateWindow(szClassName,"test",WS_OVERLAPPEDWINDOW,
		CW_USEDEFAULT,CW_USEDEFAULT,CW_USEDEFAULT,CW_USEDEFAULT,
		NULL, NULL, hInst,NULL);
	if (!hWnd) return FALSE;
	ShowWindow(hWnd, nCmdShow);
	UpdateWindow(hWnd);
	hParent = hWnd; //グローバル変数にウィンドウハンドルを格納
	return TRUE;
}

LRESULT CALLBACK WndProc(HWND hWnd, UINT msg, WPARAM wp, LPARAM lp){
	int id;
	char szBuf[64];
	switch (msg) {
	case WM_CREATE:
		//ボタンの作成
		hBtn = CreateWindowEx(0, "BUTTON", "Push!!",
			WS_CHILD | WS_VISIBLE | BS_FLAT,
			10, 100, 50, 30,
			hWnd, (HMENU)ID_BUTTON, hInst, NULL);
		break;

	case WM_COMMAND:
		switch (LOWORD(wp)) {
		case ID_BUTTON:
			//ボタンが押されたときの処理
			MessageBox(hWnd, "ボタンが押された！",	"Hey guys!", MB_OK);
			break;
		}
		break;

	case WM_CLOSE:
		DestroyWindow(hBtn);
		DestroyWindow(hWnd);
		break;
	case WM_DESTROY:
		PostQuitMessage(0);
		break;
	default:
		return (DefWindowProc(hWnd, msg, wp, lp));
	}
	return 0;
}

### 実行結果

![f:id:pythonjacascript:20200303235921j:plain](/images/ppythonjacascript2020030320200303235921.jpg "f:id:pythonjacascript:20200303235921j:plain")  
  
ボタンを押すと、↑の画像の様に「ボタンが押された！」と表示されます。

### 解説

今回、ボタンを表示するために変更した部分は、以下の3箇所です

1. ボタンのIDの設定等の準備
2. CreateWindowEx()関数でボタンを作る
3. ボタンが押されたときの実行内容を、WM\_COMMANDの中に書く

具体的には、

#include <windows.h>

#define ID_BUTTON 100   //追加
HWND hBtn;  //追加

LRESULT CALLBACK WndProc(HWND hWnd, UINT msg, WPARAM wp, LPARAM lp){
	switch (msg) {
	case WM_CREATE:
		//ボタンの作成
		hBtn = CreateWindowEx(0, "BUTTON", "Push!!",
			WS_CHILD | WS_VISIBLE | BS_FLAT,
			10, 100, 50, 30,
			hWnd, (HMENU)ID_BUTTON, hInst, NULL);
		break;

	case WM_COMMAND:
		switch (LOWORD(wp)) {
		case ID_BUTTON:
			//ボタンが押されたときの処理
			MessageBox(hWnd, "ボタンが押された！",	"Hey guys!", MB_OK);
			break;
		}
		break;
	}
	return 0;
}

  
の部分です。

#### 1.ボタンの作成

CreateWindowEx関数の引数の中身によっていろんな種類のボタンを作ることができます。

HWND CreateWindowEx(
  DWORD     dwExStyle,  //
  LPCSTR    lpClassName,    //ボタンを作るときは"BUTTON"またはTEXT("BUTON")
  LPCSTR    lpWindowName,  //個々の文字がボタンに表示される
  DWORD     dwStyle,  //後述
  int  X,  int  Y,  //ボタンの左上の座標
  int nWidth, int nHeight, //ボタンの大きさ
  HWND      hWndParent, //ボタンは子ウィンドウとなるので、親のWindowのウィンドウハンドル
  HMENU     hMenu, //ボタンの識別用ID
  HINSTANCE hInstance, 
  LPVOID    lpParam);

[Button Styles (Winuser.h) - Win32 apps | Microsoft Docs](https://docs.microsoft.com/en-us/windows/win32/controls/button-styles)  
色々なボタンを作ることができます。

| 設定値                 | 説明                                                                                  |  |
| ------------------- | ----------------------------------------------------------------------------------- |  |
| BS\_3STATE          | チェックボックスと同じボタンを作成する。ただし、異なる点はgrayの状態（ONでもOFFでもない状態）が存在すること                          |  |
| BS\_AUTO3STATE      | BS\_3STATEのボタンに、クリックされると同時に状態が変化する機能が付いたボタン。状態変化は、ON→未決定（gray状態）→OFFの順              |  |
| BS\_AUTOCHECKBOX    | チェックボックスを作る。クリックすると自動的にON/OFFが切り替わる                                                 |  |
| BS\_AUTORADIOBUTTON | ラジオボタンを作る。クリックすると自動的にON/OFFが切り替わる                                                   |  |
| BS\_BITMAP          | ボタンにBITMAPを描画する                                                                     |  |
| BS\_BOTTOM          | ボタンの長方形の下部にテキストを描画                                                                  |  |
| BS\_CENTER          | ボタンの長方形の中央にテキストを描画                                                                  |  |
| BS\_CHECKBOX        | チェックボックスを作る。テキストを表示するには「BS\_LEFTTEXT（左側にテキスト表示）」か、「BS\_RIGHTTEXT（右側にテキスト表示）」を指定すること |  |
| BS\_ICON            | ボタンにICONを描画する                                                                       |  |
| BS\_FLAT            | デフォルトの3Dのような影を作らず、2Dっぽいボタンを作る                                                       |  |
| BS\_LEFT            | ボタンの長方形の左側に文字を詰める                                                                   |  |
| BS\_LEFTTEXT        | ラジオボタンかチェックボックスにおいて、テキストをチェックする四角の左に配置                                              |  |
| BS\_MULTILINE       | ボタンの中にテキストを複数行描画することを許可する                                                           |  |
| BS\_NOTIFY          | BN\_KILLFOCUSおよびBN\_SETFOCUSメッセージを親ウィンドウに送信する                                       |  |
| BS\_PUSHLIKE        | ボタンを3D化する（OFFで浮き上がって見え、ONで沈んで見える）                                                   |  |

例としては、

//通常
hBtn  = CreateWindowEx(0, "BUTTON", "Push!!", WS_CHILD | WS_VISIBLE, 0, 0, 50, 20,hWnd, (HMENU)ID_BUTTON, hInst, NULL);

//ラジオボタン
hBtn2 = CreateWindowEx(0, "BUTTON", "Push!!", WS_CHILD | WS_VISIBLE | BS_RADIOBUTTON, 0, 25, 50, 20, hWnd, (HMENU)0, hInst, NULL);

//ラジオボタン(クリックと同時にON/OFF切り替え)
hBtn2 = CreateWindowEx(0, "BUTTON", "Push!!", WS_CHILD | WS_VISIBLE | BS_AUTORADIOBUTTON, 0, 50, 50, 20, hWnd, (HMENU)0, hInst, NULL);

//チェックボックス
hBtn3 = CreateWindowEx(0, "BUTTON", "Push!!", WS_CHILD | WS_VISIBLE | BS_CHECKBOX, 0, 75, 50, 20, hWnd, (HMENU)0, hInst, NULL);

//チェックボックス(クリックと同時にON/OFF切り替え0
hBtn4 = CreateWindowEx(0, "BUTTON", "Push!!", WS_CHILD | WS_VISIBLE | BS_AUTOCHECKBOX, 60,0, 50, 20, hWnd, (HMENU)0, hInst, NULL);

//ボタンに画像を付ける
hBtn5 = CreateWindowEx(0, "BUTTON", "Push!!", WS_CHILD | WS_VISIBLE | BS_BITMAP, 60, 25, 50, 20, hWnd, (HMENU)0, hInst, NULL);
hBmp = (HBITMAP)LoadImage(NULL, "button.bmp", IMAGE_BITMAP, 0, 0, LR_LOADFROMFILE | LR_CREATEDIBSECTION);
SendMessage(hBtn5, BM_SETIMAGE, IMAGE_BITMAP, (LPARAM)hBmp);

  
等があります。最後の「ボタンに画像を付ける」のプログラムを実行するときは、LoadImage関数の"button.bmp"をボタンに表示したいbitmapの名前にしてください。

"button.bmp"に下の画像を使うと、  
![f:id:pythonjacascript:20200304005356j:plain](/images/ppythonjacascript2020030420200304005356.jpg "f:id:pythonjacascript:20200304005356j:plain")

このような結果になります。  
![f:id:pythonjacascript:20200304005256j:plain](/images/ppythonjacascript2020030420200304005256.jpg "f:id:pythonjacascript:20200304005256j:plain")  

#### 2.ボタンの有効化/無効化

ボタンを有効化/無効化するには**EnableWindow関数**を使います。

HWND hBtn  CreateWindowEx(.......);

//ボタンを有効化
EnableWindow(hBtn, TRUE);

//ボタンを無効化
EnableWindow(hBtn, FALSE);

  
ボタンが無効化されると、色が灰色になってクリックできない状態になります。

ボタンが有効化されているか/無効化されているかを調べるには、**IsWindowEnabled関数**を使います  
有効化されていれば0以外、無効化されていれば0を返します。

HWND hBtn  CreateWindowEx(.......);

if(IsWindowEnabled(hBtn) != 0){
      //ボタンは有効化されている
}else{
      //ボタンは無効化されている
}

#### 3\. ボタンの文字のフォントを設定

HFONT hFont1;
hFont1 = CreateFont(20, 0, 0, 0, 0, FALSE, FALSE, FALSE, SHIFTJIS_CHARSET, OUT_DEFAULT_PRECIS, CLIP_DEFAULT_PRECIS, DEFAULT_QUALITY,
			VARIABLE_PITCH | FF_DONTCARE, "HG行書体");
SendMessage(hBtn, WM_SETFONT, (LPARAM)hFont1, true);

  
を追加してください。WM\_CREATEの所でOKです。  
  
#### 4\. ボタンの文字の色/文字の背景色を変更

下のプログラムをWndProc関数のswitch文のなかに追加してください。

case WM_CTLCOLORBTN: //通常ボタンの設定はこれが必要
case WM_CTLCOLORSTATIC:  //RADIOボタン、PUSHボタンなどはこのメッセージが呼び出される
	HDC hdcStatic;
	hdcStatic = (HDC)wp;
	SetTextColor(hdcStatic, RGB(255, 0, 0)); //文字色設定
	SetBkColor(hdcStatic, RGB(0, 0, 255)); //文字の背景色

	//フォントを設定
	hFont1 = CreateFont(20, 0, 0, 0, 0, FALSE, FALSE, FALSE, SHIFTJIS_CHARSET, OUT_DEFAULT_PRECIS, CLIP_DEFAULT_PRECIS, DEFAULT_QUALITY,
		VARIABLE_PITCH | FF_DONTCARE, "HG行書体");
	SelectObject(hdcStatic, hFont1);
		
	//ボタンそのものの背景色を変更
	return (long)CreateSolidBrush(RGB(0,0,0));

  
WM\_PAINTメッセージなどでボタンが再描画されるとき、WM\_CTLCOLORBTNメッセージやWM\_CTLCOLORSTATICメッセージが送られてきます。これらは、ボタンの背景色を設定するためのメッセージで、返り値は背景を塗るHBRUSHオブジェクトとなります。

上のプログラムを実行すると、このようになります。  
![f:id:pythonjacascript:20200304151443j:plain](/images/ppythonjacascript2020030420200304151443.jpg "f:id:pythonjacascript:20200304151443j:plain")  

#### 5.ボタンの状態を取得する

ラジオボタン、PUSHボタンが押されているのかいないのか、チェックがついているのかいないのかを調べます

int result;
result = SendMessage(hBtn4, BM_GETCHECK, NULL, NULL);
if (result == BST_CHECKED) {   //if (result == 1)でもOK
	MessageBox(hWnd, "ボタンはチェックされている！", "Hey guys!", MB_OK);
}else {
	MessageBox(hWnd, "ボタンはチェックされていない！", "Hey guys!", MB_OK);
}

  
SendMessage関数を使ってボタンの状態を取得します。返り値は、

| 返り値                | 数値 | 説明                  |
| ------------------ | -- | ------------------- |
| BST\_CHECKED       | 1  | ボタンはON              |
| BST\_CHECKED       | 0  | ボタンはON              |
| BST\_INDETERMINATE | 2  | gray状態（ONでもOFFでもない） |

BST\_INDETERMINATE が存在するのはCreateWindowExでボタンを作った時に、BS\_3STATE や BS\_AUTO3STATE の設定で作った時のみです。  
  
#### 6.ボタンの状態を変更

ボタンをプログラムからON/OFFするのもSendMessage関数を使います。

HWND hBtn4 = CreateWindowEx(........);

//ボタンをONする
SendMessage(hBtn4, BM_SETCHECK, BST_CHECKED, 0);

//ボタンをOFFする
SendMessage(hBtn4, BM_SETCHECK, BST_UNCHECKED, 0);

  
ラジオボタンでもチェックボタンでもこのプログラムで動きます。