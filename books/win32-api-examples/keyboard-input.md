---
title: "【C++ Win32】キーボ―ド入力のすべて"
emoji: ""
type: ""
topics: []
published: false
---

* [サンプルプログラム](#サンプルプログラム)
* [実行結果](#実行結果)
* [解説](#解説)
* [WndProc()を使って処理する方法](#WndProcを使って処理する方法)  
   * [WM\_CHARとWM\_KEYUP（WM\_KEYDOWN）の違い](#WM%5FCHARとWM%5FKEYUPWM%5FKEYDOWNの違い)  
   * [WM\_CHARメッセージ](#WM%5FCHARメッセージ)  
   * [WM\_KEYDOWN（UP）メッセージ](#WM%5FKEYDOWNUPメッセージ)
* [特定のキーが押されたかをチェック](#特定のキーが押されたかをチェック)

### サンプルプログラム

#include <windows.h>
LRESULT CALLBACK WndProc(HWND, UINT, WPARAM, LPARAM);
ATOM InitApp(HINSTANCE);
BOOL InitInstance(HINSTANCE, int);

char szClassName[] = "keyboard";	// ウィンドウクラス
BOOL bStart = FALSE, bSeikai = TRUE;


int WINAPI WinMain(HINSTANCE hCurInst, HINSTANCE hPrevInst,LPSTR lpsCmdLine, int nCmdShow){
	MSG msg;
	BOOL bRet;
	if (!InitApp(hCurInst))return FALSE;
	if (!InitInstance(hCurInst, nCmdShow))return FALSE;
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

//ウィンドウ・クラスの登録

ATOM InitApp(HINSTANCE hInst){
	WNDCLASSEX wc;
	wc.cbSize = sizeof(WNDCLASSEX);
	wc.style = CS_HREDRAW | CS_VREDRAW;
	wc.lpfnWndProc = WndProc;	//プロシージャ名
	wc.cbClsExtra = 0;
	wc.cbWndExtra = 0;
	wc.hInstance = hInst;//インスタンス
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

	hWnd = CreateWindow(szClassName,"KeyBoard", WS_OVERLAPPEDWINDOW,CW_USEDEFAULT,CW_USEDEFAULT,240,180,NULL,NULL, hInst, NULL);
	if (!hWnd) return FALSE;
	ShowWindow(hWnd, nCmdShow);
	UpdateWindow(hWnd);
	return TRUE;
}

LRESULT CALLBACK WndProc(HWND hWnd, UINT msg, WPARAM wp, LPARAM lp){
	HDC hdc;
	PAINTSTRUCT ps;
	MMTIME mm;

	char tcRe[32];
	char *str = "Please Press some key...";
	char *str2 = "--> A key was pressed!!";

	switch (msg) {
	case WM_PAINT:
		hdc = BeginPaint(hWnd, &ps);
		TextOut(hdc, 0, 0, str, (int)strlen(str));
		EndPaint(hWnd, &ps);
		break;

	case WM_CHAR:
		switch(wp){
			case VK_ESCAPE:
				PostQuitMessage(0);
			return 0;

			case 'A': //SHIFT + Aキーが押された時に実行される
				hdc = GetDC(hWnd);
				SetTextColor(hdc, RGB(30, 30, 30));  //文字の色を設定
				SetBkColor(hdc, RGB(255, 255, 0));  //文字の背景色を設定
				TextOut(hdc, 10, 40, str2, lstrlen(str2));
				ReleaseDC(hWnd, hdc);
				break;

			default:
				hdc = GetDC(hWnd);
				wsprintf(tcRe, "%d", (int)wp);
				SetTextColor(hdc, RGB(200, 200, 200));  //文字の色を設定
				SetBkColor(hdc, RGB(0, 0, 0));  //文字の背景色を設定
				TextOut(hdc, 10, 80, tcRe, lstrlen(tcRe));
				ReleaseDC(hWnd, hdc);
		}
		break;

	case WM_KEYUP:
		//何らかのキーが押されたときに発生するイベント
		break;

	case WM_KEYDOWN:
		switch (wp)	{
			//  エスケープキーの場合
		case VK_ESCAPE:
			// ウィンドウの閉じるメッセージ送信
			MessageBox(hWnd, "エスケープキーが押されたよ", "あああああ", MB_OK);
			PostQuitMessage(0);
			return 0;
		}
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

![f:id:pythonjacascript:20200517174925j:plain](/images/ppythonjacascript2020051720200517174925.jpg "f:id:pythonjacascript:20200517174925j:plain")  
こんな感じです。  
Shift+Aを押すと、「--> A key was pressed!!」と表示されます。  
EscapeキーでWindowを終了します  
  
  
### 解説

キーボードに関する処理は、↑のプログラムでは二つの方法があります。

1. WndProc() 関数を使う
2. **GetKeyState()関数**などによって、特定のキーが押されたかどうかを判定する

### WndProc()を使って処理する方法

キーボードが押されたときに発生するメッセ―ジは以下のようなものがあります

| メッセージ          | 説明              |
| -------------- | --------------- |
| WM\_CHAR       | 文字が入力されたときに発生する |
| WM\_KEYDOWN    | 一般キーが押された       |
| WM\_KEYUP      | 一般キーが話された       |
| WM\_SYSKEYDOWN | システムキーが押された     |
| WM\_SYSKEYUP   | システムキーが離された     |

「**システムキー**」とは Alt と F10キーで、「**一般キー**」はシステムキー以外のキーです。

それぞれのメッセージにおいて、wParamには仮想キーコードが格納されています。  
仮想キーコードにはこのサイトにまとめられています。

[【Win32】仮想キーコード 一覧表 | 初心者のWindowsプログラミング日記](https://ameblo.jp/sgl00044/entry-12518292302.html)

  
#### WM\_CHARとWM\_KEYUP（WM\_KEYDOWN）の違い

* **WM\_CHAR**→一文字入力されたときに送られる。その時に**Unicode**で入力された文字が送られてくる
* **WM\_KEYUP**（WM\_KEYDOWN）→キーボードのいずれかのキーが押されたときに送られる。
* 通常、**WM\_KEYDOWN→WM\_CHAR→WM\_KEYUP**の順にメッセージが送られる

例えば、ユーザーが「A」を入力する間、

1. Shiftキーを押す → **WM\_KEYDOWN**メッセージ（Shiftを押したよ）
2. 「a」キーを押す → **WM\_KEYDOWN**メッセージ（aを押したよ）→ **WM\_CHAR**メッセージ（Unicode文字「A」が入力されたよ）
3. 「a」キーを離す → **WM\_KEYUP**メッセージ（aを離したよ）
4. Shiftキーを離す→ **WM\_KEYUP**メッセージ（Shiftを離したよ）

という一連のメッセージのやり取りがあります。  
  
  
#### WM\_CHARメッセージ

WM\_CHARメッセージは文字が入力されると発生します。wParamにはその文字のUnicodeが入っています

LRESULT CALLBACK WndProc(HWND hWnd, UINT msg, WPARAM wp, LPARAM lp){
	HDC hdc;
	switch (msg) {
	case WM_CHAR:
		switch (wp) {
		case 0x08:  // backspace.
		case 0x09:  // tab.
		case 0x0A:  // linefeed.
		case 0x0D:  // carriage return.
		case 0x1B:  // escape.
		case 0x20:  // space.
			break;

			// Next, handle displayable characters by appending them to our string.
		default:
			hdc = GetDC(hWnd);
			
			SetTextColor(hdc, RGB(0, 0, 0));
			SetBkColor(hdc, RGB(255, 255, 255));
			TextOutW(hdc, 0,0, &(wchar_t)wp, 1);

			ReleaseDC(hWnd, hdc);
			break;
		}
(以下省略)

上のプログラムは押されたキーボードの文字を表示するプログラムです。aキーを押すと「a」と表示され、Shift+aキーだと「A」と表示されます。

特定の文字が入力されたのかどうかを調べるには、以下の様に行います

（WndProc関数内）
switch (msg) {
    case WM_CHAR:
	if (wp == 'A') {
              //Aが入力された（Shift+Aが押された）時にここが実行される
	}
  
  
#### WM\_KEYDOWN（UP）メッセージ

一般キー（Altキー、F10キー以外）が押された時、キーボードフォーカスのあるウィンドウに送られます。  
[【Win32】仮想キーコード 一覧表 | 初心者のWindowsプログラミング日記](https://ameblo.jp/sgl00044/entry-12518292302.html)

wParamには仮想キーコード（↓1つ下の節参照）が入っているため、下のように書くことができます

switch(msg){
    case WM_KEYDOWN:
        switch(WPARAM){
        case VK_ENTER:
            // エンターキーが押されたときの処理
            break;
            
        case 0x41: 
            //Aキーが押されたときの処理
            break;
        }
}
  
  
### 特定のキーが押されたかをチェック

**GetKeyState()関数**を使うことで、特定のキーが現在押されているかを調べることができます。

SHORT GetKeyState(int nVirtKey);

引数のnVirtKeyには、押されているかを調べる**仮想キーコード**を入力します  
返り値は、そのキーが押されている時は負の数、押されていない時は0以上です

仮想キーコードは下のようなものがあります

| 仮想キーコード     | 説明         |
| ----------- | ---------- |
| VK\_RETURN  | エンター       |
| VK\_SHIFT   | シフト        |
| VK\_CONTROL | コントロール     |
| VK\_MENU    | Alt        |
| VK\_PAUSE   | Pause      |
| VK\_CAPITAL | Caps Lock  |
| VK\_ESCAPE  | Esc        |
| VK\_SPACE   | スペースキー     |
| VK\_PRIOR   | Pg UP      |
| VK\_NEXT    | Pg Down    |
| VK\_END     | End        |
| VK\_HOME    | Home       |
| VK\_LEFT    | 左方向キー      |
| VK\_UP      | 上方向キー      |
| VK\_RIGHT   | 右方向キー      |
| VK\_DOWN    | 下方向キー      |
| VK\_INSERT  | Insert     |
| VK\_DELETE  | Delete     |
| VK\_HELP    | ヘルプ        |
| VK\_LWIN    | 左Windowsキー |
| VK\_RWIN    | 右Windowsキー |

そして、通常のキーは[asciiのキーコード](https://ichibaphone.com/irresponsible-encyclopedia/standard/code/ascii-code/)をもとにしているようです。

* A～Z：0x41～0x5A
* 数字0～9：0x60～0x59

 \- ファンクションキー（F1～F24）は「VK\_F○○」の形になります（例：F2キー→「VK\_F2」）

使用例：

if (GetKeyState(VK_CONTROL) < 0) {
    // Ctrlキーが押されている
}else {
    // Ctrlキーが押されていない
}