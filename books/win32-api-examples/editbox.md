---
title: "【C++ Win32】エディットボックスを作る"
emoji: ""
type: ""
topics: []
published: false
---

エディットコントロールというのは↓のようなものです  
![f:id:pythonjacascript:20200304155339j:plain](/images/ppythonjacascript2020030420200304155339.jpg "f:id:pythonjacascript:20200304155339j:plain")  
今回はこれを作ります。

* [実行結果](#実行結果)
* [解説](#解説)  
   * [1.CreateWindowEx関数でエディットボックスを作る](#1CreateWindowEx関数でエディットボックスを作る)  
   * [WndProc関数を使った制御](#WndProc関数を使った制御)  
   * [テキストを取得](#テキストを取得)  
   * [テキストをセットする](#テキストをセットする)  
   * [スクロールバーを特定の位置にもっていく](#スクロールバーを特定の位置にもっていく)  
   * [入力できる最大文字数を指定する](#入力できる最大文字数を指定する)  
   * [フォントを設定](#フォントを設定)

#include <windows.h>
#include <windowsx.h>

#define ID_EDIT 101

LRESULT CALLBACK WndProc(HWND, UINT, WPARAM, LPARAM);
ATOM InitApp(HINSTANCE);
BOOL InitInstance(HINSTANCE, int);

HINSTANCE hInst;
char szClassName[] = "test02";
HWND hParent;

int WINAPI WinMain(HINSTANCE hCurInst, HINSTANCE hPrevInst,	LPSTR lpsCmdLine, int nCmdShow){
	MSG msg;
	BOOL bRet;
	hInst = hCurInst;
	if (!InitApp(hCurInst)) return FALSE;
	if (!InitInstance(hCurInst, nCmdShow)) return FALSE;

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
	wc.lpszMenuName = NULL;	//メニュー名
	wc.lpszClassName = (LPCSTR)szClassName;
	wc.hIconSm = NULL;
	return (RegisterClassEx(&wc));
}

BOOL InitInstance(HINSTANCE hInst, int nCmdShow){
	HWND hWnd;
	hWnd = CreateWindow(szClassName,"test", WS_OVERLAPPEDWINDOW, //ウィンドウの種類
		CW_USEDEFAULT,CW_USEDEFAULT,CW_USEDEFAULT,CW_USEDEFAULT,
		NULL,NULL,hInst,NULL);
	if (!hWnd)return FALSE;
	ShowWindow(hWnd, nCmdShow);
	UpdateWindow(hWnd);
	hParent = hWnd; //グローバル変数にウィンドウハンドルを格納
	return TRUE;
}

//ウィンドウプロシージャ

LRESULT CALLBACK WndProc(HWND hWnd, UINT msg, WPARAM wp, LPARAM lp){
	static HWND hEdit;
	char szBuf[64];

	switch (msg) {
	case WM_CREATE:
		InitCommonControls();
		hEdit = CreateWindowEx(0,"EDIT", "",
			WS_CHILD | WS_VISIBLE | WS_BORDER | ES_AUTOHSCROLL,
			10, 10, 200, 200, hWnd, (HMENU)ID_EDIT, hInst, NULL);
		
		SendMessage(hEdit, EM_SETLIMITTEXT, (WPARAM)63, 0);

		wchar_t Buffer[128];
		SendMessage(hEdit, WM_CTLCOLORSTATIC,(WPARAM)_countof(Buffer),(LPARAM)Buffer);
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

![f:id:pythonjacascript:20200304155339j:plain](/images/ppythonjacascript2020030420200304155339.jpg "f:id:pythonjacascript:20200304155339j:plain")  
写真の様に、文字を打ち込むことができます。  
  
### 解説

エディットボックスを作るのには、以下の手順が必要です

1. CreateWindowEx()関数で、子ウィンドウとしてエディットボックスを作成
2. WM\_COMMANDメッセージが来た時の処理を実装

#### 1.CreateWindowEx関数でエディットボックスを作る

HWND hEdit; //エディットボックスのWindowハンドル
hEdit = CreateWindowEx(0,"EDIT", "",
	WS_CHILD | WS_VISIBLE | WS_BORDER | ES_AUTOHSCROLL,
	10, 10, 200, 200, hWnd, (HMENU)ID_EDIT, hInst, NULL);

  
CreateWindowEx関数の引数はこんな感じです

HWND CreateWindowEx(
  DWORD     dwExStyle,  //スタイル。通常は0を指定
  LPCSTR    lpClassName,    //ボタンを作るときは"EDIT"またはTEXT("EDIT")
  LPCSTR    lpWindowName,  //エディットボックスの中身の文字
  DWORD     dwStyle,  //後述
  int  X,  int  Y,  //ボタンの左上の座標
  int nWidth, int nHeight, //ボタンの大きさ
  HWND      hWndParent, //ボタンは子ウィンドウとなるので、親のWindowのウィンドウハンドル
  HMENU     hMenu, //ボタンの識別用ID
  HINSTANCE hInstance, 
  LPVOID    lpParam);

  
第4引数のdwStyleについてですが、以下のような指定方法があります。

//通常（スクロールバーなし）
dwStyle = WS_CHILD | WS_VISIBLE;

//スクロールバーあり(記事↑のサンプルプログラム)
dwStyle = WS_CHILD | WS_VISIBLE | WS_BORDER | ES_AUTOHSCROLL;

//複数行の表示を許可する（ES_MULTILINE）（デフォルトでは改行できない）
dwStyle = WS_CHILD | WS_VISIBLE | ES_MULTILINE

//文字を左寄せ（ES_LEFT）
dwStyle = WS_CHILD | WS_VISIBLE | ES_LEFT;
//中央寄せ→ES_CENTER,  右寄せ→ES_RIGHT,   左寄せ→ES_LEFT を指定

//枠線を表示（WS_BORDER ）
dwStyle = WS_CHILD | WS_VISIBLE | WS_BORDER 

//数字だけ入力可
dwStyle = WS_CHILD | WS_VISIBLE | ES_NUMBER;

#### WndProc関数を使った制御

| イベントID名       | 送られてくる時                                                                        |
| ------------- | ------------------------------------------------------------------------------ |
| EN\_CHANGE    | テキストが変更された可能性のある動作がされた EN\_UPDATE通知メッセージとは異なり, この通知メッセージはシステムがスクリーンを更新したあと送られる |
| EN\_ERRSPACE  | 十分な記憶を割り当てられない時                                                                |
| EN\_HSCROLL   | ユーザーが水平スクロール・バーをクリックするとき                                                       |
| EN\_KILLFOCUS | キーボードフォーカスを失った時                                                                |
| EN\_MAXTEXT   | ES\_AUTOHSCROLL または ES\_AUTOVSCROLL スタイル指定がなく, テキストの挿入がスペースを上回った時              |
| EN\_SETFOCUS  | キーボードフォーカスを得た時に送られる                                                            |
| EN\_UPDATE    | テキストの内容が変更された後, テキストを表示しようとしている時                                               |
| EN\_VSCROLL   | ユーザーが垂直スクロール・バーをクリックするとき, スクリーンが更新される前に送られる                                    |

参考→<http://wisdom.sakura.ne.jp/system/winapi/win32/win66.html>

#### テキストを取得

以下のプログラムでテキストを取得できます

HWND hEdit = CreateWindowEx(.......);

//テキスト取得
LPTSTR strText = (LPTSTR)calloc((GetWindowTextLength(hwnd) + 1), sizeof(TCHAR));

#### テキストをセットする

エディとボックス内のテキストを変更する方法は3通りあります。

まず、ウィンドウハンドルを使って設定する方法は↓

HWND hEdit;
SetWindowText(hEdit, "test") ;

 SetWindowText関数の戻り値はbool型で、成功したかどうかを表します

もう一つは、ウィンドウハンドルではなくIDを使ってテキストをセットする方法です↓

HWND hEdit;
//nIDDlgItemはhEditのコントロールID
SetDlgItemText(hEdit, int nIDDlgItem, "test") ;

SetDlgItemText関数も戻り値はbool型で成功したかどうかです。  
  
HWND hEdit;
SendMessage(hEdit, WM_SETTEXT, 0, (LPARAM)"test");

と打つと、エディットボックス上の文字が "test" になります  
  
#### スクロールバーを特定の位置にもっていく

HWND hEdit;
SendMessage(hEdit, WM_VSCROLL, SB_BOTTOM, 0);

このように書くと、エディットボックスの最下部のテキストが表示されます。  
↑のプログラムの「SB\_BOTTOM」を「SB\_TOP」に変えることで最上部を表示することもできます。  
  
#### 入力できる最大文字数を指定する

HWND hEdit;
SendMessage(hEdit, EM_SETLIMITTEXT, (WPARAM)50, 0);

と書くと、最大50文字しか入力できないようになります  
  
#### フォントを設定

エディットボックスの中の文字フォントを設定する方法です。

HWND hEdit = CreateWindowEx(0,"EDIT","test", ......省略);
		
//フォントの作成
HFONT hFont = CreateFont(15, 0, 0, 0, FW_BOLD,
		FALSE, FALSE, FALSE,
		SHIFTJIS_CHARSET, OUT_DEFAULT_PRECIS,
		CLIP_DEFAULT_PRECIS, DEFAULT_QUALITY,
		VARIABLE_PITCH | FF_ROMAN, NULL);

//フォントを適用する
SendMessage(hEdit, WM_SETFONT, (WPARAM)hFont, MAKELPARAM(FALSE, 0));

  
フォントについては下の記事を見てください  
[shizenkarasuzon.hatenablog.com](https://shizenkarasuzon.hatenablog.com/entry/2020/02/25/171829#%E3%83%95%E3%82%A9%E3%83%B3%E3%83%88%E3%81%AE%E8%A8%AD%E5%AE%9A)