---
title: "【C++ Win32API】マウスの処理の全て"
emoji: ""
type: ""
topics: []
published: false
---

* [サンプルプログラム](#サンプルプログラム)
* [実行結果](#実行結果)
* [解説](#解説)
* [マウスを操作したときに発生するイベント](#マウスを操作したときに発生するイベント)
* [マウスの現在位置（ウィンドウ内の相対座標）を取得する](#マウスの現在位置ウィンドウ内の相対座標を取得する)  
   * [方法1](#方法1)  
   * [方法2.](#方法2)
* [マウスの現在位置（モニター左上からの絶対座標）を取得する](#マウスの現在位置モニター左上からの絶対座標を取得する)
* [マウスの位置をプログラムで変更する](#マウスの位置をプログラムで変更する)
* [マウスの特定のボタンが押されているかをチェックする](#マウスの特定のボタンが押されているかをチェックする)

### サンプルプログラム

#include <windows.h>
LRESULT CALLBACK WndProc(HWND, UINT, WPARAM, LPARAM);
ATOM InitApp(HINSTANCE);
BOOL InitInstance(HINSTANCE, int);

char szClassName[] = "mouse";	// ウィンドウクラス
BOOL bStart = FALSE, bSeikai = TRUE;

RECT rec;
HPEN hPen;
HBRUSH hBrush;


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
	wc.lpfnWndProc = WndProc;
	wc.cbClsExtra = 0;
	wc.cbWndExtra = 0;
	wc.hInstance = hInst;
	wc.hIcon = NULL;
	wc.hCursor = (HCURSOR)LoadImage(NULL,MAKEINTRESOURCE(IDC_ARROW),IMAGE_CURSOR,0,0,LR_DEFAULTSIZE | LR_SHARED);
	wc.hbrBackground = (HBRUSH)GetStockObject(BLACK_BRUSH);
	wc.lpszMenuName = NULL;
	wc.lpszClassName = (LPCSTR)szClassName;
	wc.hIconSm = NULL;
	return (RegisterClassEx(&wc));
}

BOOL InitInstance(HINSTANCE hInst, int nCmdShow){
	HWND hWnd;
	hWnd = CreateWindow(szClassName,"mouse", WS_OVERLAPPEDWINDOW,CW_USEDEFAULT,CW_USEDEFAULT,CW_USEDEFAULT,CW_USEDEFAULT,NULL, NULL, hInst,NULL);
	if (!hWnd) return FALSE;
	ShowWindow(hWnd, nCmdShow);
	UpdateWindow(hWnd);
	return TRUE;
}


LRESULT CALLBACK WndProc(HWND hWnd, UINT msg, WPARAM wp, LPARAM lp){
	HDC hdc;
	PAINTSTRUCT ps;

	char *mouse_move  = "mouse is moving...";
	char *mouse_left  = "Left Click!!          ";
	char *mouse_right = "Right Click!!         ";
	char mouse_pos[50];

	switch (msg) {
	case WM_PAINT:
		hdc = BeginPaint(hWnd, &ps);
		//TODO : 描画処理
		EndPaint(hWnd, &ps);
		break;

	case WM_LBUTTONDOWN:
		hdc = GetDC(hWnd);
		TextOut(hdc, 0, 0, mouse_left, (int)strlen(mouse_left));
		ReleaseDC(hWnd, hdc);

		break;
		

	case WM_MOUSEMOVE:
		hdc = GetDC(hWnd);

		GetClientRect(hWnd, &rec);  //ウインドウのサイズを取得
		hBrush = (HBRUSH)CreateSolidBrush(RGB(0, 0, 0));
		SelectObject(hdc, hBrush);
		PatBlt(hdc, 0, 0, rec.right, rec.bottom, PATCOPY);


		POINTS mouse_p = MAKEPOINTS(lp);
		wsprintf(mouse_pos, "x座標 =  %d ,  y座標= %d ", mouse_p.x, mouse_p.y);
		TextOut(hdc, 0, 30, mouse_pos, (int)strlen(mouse_pos));
		TextOut(hdc, 0, 0, mouse_move, (int)strlen(mouse_move));

		hPen = CreatePen(PS_SOLID, 0, RGB(255, 0, 255)); //図形の枠線の色を設定
		SelectObject(hdc, hPen);
		hBrush = (HBRUSH)CreateSolidBrush(RGB(255, 255, 0));
		SelectObject(hdc, hBrush);		//図形内部の塗りつぶし方と色を設定

		Ellipse(hdc, mouse_p.x-10, mouse_p.y-10, mouse_p.x+10, mouse_p.y+10);		//楕円を描く

		ReleaseDC(hWnd, hdc);
		break;
		

	case WM_RBUTTONDOWN:
		hdc = GetDC(hWnd);
		TextOut(hdc, 0, 0, mouse_right, (int)strlen(mouse_right));
		ReleaseDC(hWnd, hdc);
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

window上でマウスを動かすと、マウスの位置が表示されます。右クリック、左クリックすると、それに対応した文字が表示されます。  
![f:id:pythonjacascript:20200321002342j:plain](/images/ppythonjacascript2020032120200321002342.jpg "f:id:pythonjacascript:20200321002342j:plain")
  
  
### 解説

以下、上のプログラムの解説に入ります。  
マウス関連の処理は主に、①WndProc関数でイベントとして処理する  
②諸関数（例：）を用いて状態を取得・設定する  
です。上のプログラムでは、ＷＭ\_LBUTTONDOWNなどと書かれている部分が①、  
と書かれている部分が②にあたります  
  
  
### マウスを操作したときに発生するイベント

ユーザーがマウスをクリックしたり、ドラッグしたりすると、以下のようなイベントが発生し、プロシージャが呼び出されます。

以下がそのイベントを使った例です

| 値                 | 説明               |
| ----------------- | ---------------- |
| WM\_LBUTTONDOWN   | 左ボタンを押したとき       |
| WM\_LBUTTONDBLCLK | 左ボタンをダブルクリックしたとき |
| WM\_LBUTTONUP     | 左ボタンを離した時        |
| WM\_RBUTTONDOWN   | 右ボタンを押したとき       |
| WM\_RBUTTONDBLCLK | 右ボタンをダブルクリック     |
| WM\_RBUTTONUP     | 右ボタンを離した時        |
| WM\_MBUTTONDOWN   | 中央ボタンを押したとき      |
| WM\_MBUTTONUP     | 中央ボタンを離した時       |
| WM\_MOUSEMOVE     | マウスカーソルが動いたとき    |
| WM\_MOUSELEAVE    | マウスがウィンドウ領域から出た  |

こんな感じで使います

//ウィンドウプロシージャ
LRESULT CALLBACK WndProc(HWND hWnd, UINT msg, WPARAM wp, LPARAM lp){
	switch (msg) {
	case WM_MOUSEMOVE:
		//マウスが動いたときの処理
		break;
		

	case WM_LBUTTONDOWN:
		//左クリックされたときの処理
		break;

	default:
		return (DefWindowProc(hWnd, msg, wp, lp));
	}
	return 0;
}

これらのイベントでは、wpには仮想キー（Ctrl、Shift）が押されたかどうかの情報が格納されています。  
  
### マウスの現在位置（ウィンドウ内の相対座標）を取得する

ＷＭ\_LBUTTONDOWN等のマウスイベント発生時の現在位置は、WndProc関数の第３引数lParamで確認することができます

#### 方法1

[WM\_LBUTTONDOWN message (Winuser.h) - Win32 apps | Microsoft Docs](https://docs.microsoft.com/en-us/windows/win32/inputdev/wm-lbuttondown)

↑の公式のドキュメントには以下のような方法が載っています  
**サンプル**

#include <windowsx.h>

(省略)

LRESULT CALLBACK WndProc(HWND hWnd, UINT msg, WPARAM wp, LPARAM lp){
	(省略)
	short xPos, yPos;

	switch (msg) {
	case WM_LBUTTONDOWN:
	case WM_MOUSEMOVE:
		hdc = GetDC(hWnd);
		xPos = GET_X_LPARAM(lp);
		yPos = GET_Y_LPARAM(lp);
		(省略......)
  
  
#### 方法2.

サンプルプログラムで紹介したような方法もあります

LRESULT CALLBACK WndProc(HWND hWnd, UINT msg, WPARAM wp, LPARAM lp){
	PAINTSTRUCT ps;
	(省略)
	char mouse_pos[50];

	switch (msg) {
	case WM_MOUSEMOVE:
		POINTS mouse_p = MAKEPOINTS(lp);
		wsprintf(mouse_pos, "x座標 =  %d ,  y座標= %d ", mouse_p.x, mouse_p.y);
		(省略......)

  
MAKEPOINTSマクロを使ってPOINTS構造体にマウスの現在位置を格納します。POINTS構造体にはSHORT型のx, yの2つの変数があります。

typedef struct tagPOINTS {
    SHORT x;    //x座標
    SHORT y;    //ｙ座標
} POINTS, *POINTS;

  
LONG型のPOINT構造体もあります

typedef struct tagPOINTS {
    LONG x;    //x座標
    LONG y;    //ｙ座標
} POINT, *POINT;

  
**※注意**  
lParamからマウスのxy座標を取得するのに**LOWORDやHIWORDを使用しないで下さい**。マルチモニタを使っているとき、xy座標はマイナスになる場合がありますが、LOWORDとHIWORDはそれに対応していません  
  
  
### マウスの現在位置（モニター左上からの絶対座標）を取得する

**GetCursorPos関数**を使います

POINT mouse_p;
GetCursorPos(&mouse_p);
wsprintf(mouse_pos, "x座標 =  %d ,  y座標= %d ", mouse_p.x, mouse_p.y);
  
  
### マウスの位置をプログラムで変更する

プログラムでマウスカーソルを移動させるには、**SetCursorPos関数**を使います  
ただし、SetCursorPos関数は**モニターの左上からの絶対座標**です（ウィンドウ内の座標ではありません）。

BOOL SetCursorPos(int X, int Y);

  
ウィンドウ内の座標に移動させるには、以下の様にウィンドウの座標を取得して、動かしたい相対座標を指定する必要があります。

ウィンドウの現在位置を取得するには**GetWindowInfo関数**を使います。

BOOL GetWindowInfo(HWND hwnd, PWINDOWINFO pwi);

  
第二引数の**WINDOWINFO構造体**にウィンドウ情報が格納されます

typedef struct tagWINDOWINFO {
  DWORD cbSize;
  RECT  rcWindow;
  RECT  rcClient;
  DWORD dwStyle;
  DWORD dwExStyle;
  DWORD dwWindowStatus;
  UINT  cxWindowBorders;
  UINT  cyWindowBorders;
  ATOM  atomWindowType;
  WORD  wCreatorVersion;
} WINDOWINFO, *PWINDOWINFO, *LPWINDOWINFO;

サンプルプログラム  
以下のプログラムは、マウスをウィンドウ内の相対座標で見て(xPos, yPos)に移動させます

int xPos_absolute, yPos_absolute;

int xPos = 0;  //移動させたいｘ座標（ウィンドウ内の相対座標）
int yPos = 100; //移動させたいｙ座標（ウィンドウ内の相対座標）
WINDOWINFO windowInfo;

//ウィンドウの位置を取得
windowInfo.cbSize = sizeof(WINDOWINFO);
GetWindowInfo(hWnd, &windowInfo);

//マウスの移動先の絶対座標（モニター左上からの座標）
xPos_absolute = xPos + windowInfo.rcWindow.left;
yPos_absolute = yPos + windowInfo.rcWindow.top + 35; //ウィンドウのタイトルバーの分（35px）をプラス

SetCursorPos(xPos_absolute, yPos_absolute);

### マウスの特定のボタンが押されているかをチェックする

**GetAsyncKeyState関数**を使うことでマウスの左右中央のボタンが押されているかを確認できます。

SHORT GetAsyncKeyState(int vKey);

  
[GetAsyncKeyState function (winuser.h) - Win32 apps | Microsoft Docs](https://docs.microsoft.com/en-us/windows/win32/api/winuser/nf-winuser-getasynckeystate)  

サンプル↓

#include <windows.h>
if (GetAsyncKeyState(VK_LBUTTON)) {
	//左ボタンが押されている
}

if (GetAsyncKeyState(VK_RBUTTON)) {
	//右ボタンが押されている
}

if (GetAsyncKeyState(VK_MBUTTON)) {
	//中央ボタンが押されている
}