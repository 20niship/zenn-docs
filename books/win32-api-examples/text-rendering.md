---
title: "【C++ Win32】文字の描画とフォントの作成（Win32 APIシリーズ第2回）"
emoji: ""
type: ""
topics: []
published: false
---

* [目標](#目標)
* [プログラム①](#プログラム)
* [実行結果](#実行結果)
* [解説](#解説)
* [TextOut関数](#TextOut関数)
* [デバイスコンテキストとは](#デバイスコンテキストとは)  
   * [デバイスコンテキストの利点](#デバイスコンテキストの利点)
* [背景色と文字色の設定](#背景色と文字色の設定)
* [フォントの設定](#フォントの設定)  
   * [nWeightについて](#nWeightについて)  
   * [nCharSetについて](#nCharSetについて)  
   * [nEscapementとnOrientationについて](#nEscapementとnOrientationについて)  
   * [nQualityについて](#nQualityについて)  
   * [nPitchAndFamilyについて](#nPitchAndFamilyについて)  
   * [lpszFacenameについて](#lpszFacenameについて)
* [参考文献](#参考文献)

### 目標

Win32 APIを使ってWindowsアプリケーションを作るシリーズ、第二回です。  
今回は、画面上に文字を表示してみようと思います。  
といっても、文字を表示する方法はいくつかあり、  
・CreateWindow("STATIC", .......);を使う方法  
・TextOut()関数を使う方法...  
等がありますが、今回紹介するのは後者です。  
  
### プログラム①

全体のプログラムです。

#include <windows.h>

LRESULT CALLBACK WndProc(HWND, UINT, WPARAM, LPARAM);
char szClassName[] = "text_test";	//ウィンドウクラス
void MyPaint(HWND);  //WM_PAINTが呼ばれたときの描画動作 

int WINAPI WinMain(HINSTANCE hCurInst, HINSTANCE hPrevInst,LPSTR lpsCmdLine, int nCmdShow){
	MSG msg;
	BOOL bRet;
	WNDCLASSEX wc;
	HWND hWnd;
	ATOM atom;

	wc.cbSize = sizeof(WNDCLASSEX);
	wc.style = CS_HREDRAW | CS_VREDRAW;
	wc.lpfnWndProc = WndProc;	//プロシージャ名
	wc.cbClsExtra = 0;
	wc.cbWndExtra = 0;
	wc.hInstance = hCurInst;//インスタンス
	wc.hIcon = (HICON)LoadImage(NULL,MAKEINTRESOURCE(IDI_APPLICATION),IMAGE_ICON,0,0,LR_DEFAULTSIZE | LR_SHARED);
        wc.hCursor = (HCURSOR)LoadImage(NULL,MAKEINTRESOURCE(IDC_ARROW),IMAGE_CURSOR,0,0,LR_DEFAULTSIZE | LR_SHARED);
	wc.hbrBackground = (HBRUSH)GetStockObject(WHITE_BRUSH);
	wc.lpszMenuName = NULL;
	wc.lpszClassName = (LPCSTR)szClassName;
	wc.hIconSm = (HICON)LoadImage(NULL,MAKEINTRESOURCE(IDI_APPLICATION),IMAGE_ICON,	0,0,LR_DEFAULTSIZE | LR_SHARED);
        
        if ((atom = RegisterClassEx(&wc)) == 0)return FALSE;

	hWnd = CreateWindow(MAKEINTATOM(atom), "Window Title", WS_OVERLAPPEDWINDOW, CW_USEDEFAULT,CW_USEDEFAULT,200,100,NULL,NULL, hCurInst, NULL);
	if (!hWnd)return FALSE;

	ShowWindow(hWnd, nCmdShow);
	UpdateWindow(hWnd);
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

LRESULT CALLBACK WndProc(HWND hWnd, UINT msg, WPARAM wp, LPARAM lp){
	switch (msg) {
	case WM_PAINT:
		MyPaint(hWnd);
		break;
	case WM_DESTROY:
		PostQuitMessage(0);
		break;
	default:
		return (DefWindowProc(hWnd, msg, wp, lp));
	}
	return 0;
}

void MyPaint(HWND hWnd) {
	HDC hdc;
	PAINTSTRUCT ps;

	char *szStr = "Hello, World!!";
	hdc = BeginPaint(hWnd, &ps);

	SetBkColor(hdc, RGB(10, 10, 10));  //文字の背景色を設定
	SetTextColor(hdc, RGB(200,200,200));  //文字の色を設定
	TextOut(hdc, 10, 10, (LPCTSTR)szStr, (int)strlen(szStr));//文字を書く
	EndPaint(hWnd, &ps);
}

### 実行結果

以下のようなWindowが表示されます  
![f:id:pythonjacascript:20200225172147j:plain](/images/ppythonjacascript2020022520200225172147.jpg "f:id:pythonjacascript:20200225172147j:plain")  

### 解説

前回と変わった部分だけ抜き出すと、

void MyPaint(HWND);  //WM_PAINTが呼ばれたときの描画動作 

LRESULT CALLBACK WndProc(HWND hWnd, UINT msg, WPARAM wp, LPARAM lp){
	switch (msg) {
	case WM_PAINT:
		MyPaint(hWnd);
		break;
        //（そのほか色んな処理）
	}
}

void MyPaint(HWND hWnd) {
	HDC hdc;
	PAINTSTRUCT ps;

	char *szStr = "Hello, World!!";
	hdc = BeginPaint(hWnd, &ps);

	SetBkColor(hdc, RGB(10, 10, 10));  //文字の背景色を設定
	SetTextColor(hdc, RGB(200,200,200));  //文字の色を設定
	TextOut(hdc, 10, 10, (LPCTSTR)szStr, (int)strlen(szStr));//文字を書く
	EndPaint(hWnd, &ps);
}

  
です。このプログラムでは、MyPaint関数で文字を描画しています。  
具体的にどのようにして文字が表示されるのかを見ていきます。

まず、**WM\_PAINT**メッセージは、**再描写が必要**なときに送られてくるメッセージです。

WM\_PAINTが送られるタイミングは、

> ➀Windowの起動時（正確には、UpdateWindow()が実行されたとき）  
> ②ウィンドウが変更された時（リサイズ、最小化、最大化など）  
> ③InvalidRect()が呼ばれたとき

などです。

WinMain関数内にUpdateWindow();と書いてあるので、WM\_PAINTが呼ばれます。そして、WndProc関数を経由してMyPaint関数が呼び出されます。  
  
### TextOut関数

TextOut関数を使うことで文字列をWindow上に表示できます

BOOL TextOut(
  HDC hdc,  //描画するデバイスコンテキスト
  int nXStart, //文字列の左上のｘ座標
  int nYStart, //文字列の左上のｙ座標
  LPCTSTR lpString, //文字列
  int cbString  //文字数（ (int)strlen(lpString)と書くことが圧倒的に多い）
);

  
重要なのは第一引数の「**デバイスコンテキスト**」です。  
  
### デバイスコンテキストとは

**デバイスコンテキスト**は、ディスプレイやプリンターなどのデバイスの描画属性に関する情報を含む Windows データ構造です。出力デバイスはそれぞれデバイスコンテキストを持っており、それらは「**デバイスコンテキストハンドル**」という値を使って識別します。

 **GetDC()**関数を使うことでその出力デバイスのデバイスコンテキストハンドルを得ることができます。  
 ウィンドウ全体（クライアント領域と非クライアント領域の両方）または画面全体のデバイスコンテキストハンドルを取得する場合は、**GetWindowDC**を使用します。 プリンターの場合 **CreateDC**を使用します。 

HDC GetDC(HWND hWnd);  
//hWnd   ->  ウィンドウハンドル。
//返り値　->  hWndのデバイスコンテキストハンドル

デバイスコンテキストには、図形の枠を描くためのペン、塗りつぶし用のブラシ、ビットマップ、色のセットを定義するパレット...などの描画に必要なものを設定することができます。  
![f:id:pythonjacascript:20200225222958j:plain](/images/ppythonjacascript2020022520200225222958.jpg "f:id:pythonjacascript:20200225222958j:plain")  
そして、TextOut関数やFillRect関数を使って、Win32 APIからデバイスコンテキストを通してWindowに描画します。  
  
#### デバイスコンテキストの利点

デバイスコンテキストを使うことで、異なる出力デバイスを同様のプログラムで制御することができます。プリンタで文字を印刷するにも、モニター上に文字を表示するにもTextOut()関数を使えばよいのです。  
  
### 背景色と文字色の設定

文字の表示の話に戻ります。文字色と背景色の設定は以下の様に行います。

HDC hdc;  //Windowのデバイスコンテキストハンドル
SetBkColor(hdc, RGB(10, 10, 10));  //文字の背景色を設定
//または、
SetBkMode(hdc ,TRANSPARENT);  // TRANSPARENT→背景を透明にする。OPAQUE→デフォルト

SetTextColor(hdc, RGB(200,200,200));  //文字の色を設定

  
RGB(int r, int g, int b)の引数は、色のRGB値（0～225）の値です。  
  
### フォントの設定

フォントを設定するには、

1. フォントを作成する【CreateFont関数】
2. フォントを適応する【SelectObject関数】
3. 文字を書き終わったら（または、Windowを破棄する時に）、フォントオブジェクトを削除する【DeleteObject関数】

の3つの変更が必要になります。  
3のDeleteObject()を行わないと、while()ループの中で一回のループにつき1つずつHFONTが作られるようになっているので、いずれはメモリが足りなくなってフリーズします。  
DeleteObjectは（個人的に）忘れがちなのですが大事です。

これらをすべて実装すると、↑のプログラムのMyPaint関数は下のようになります。

void MyPaint(HWND hWnd) {
	HDC hdc;
	PAINTSTRUCT ps;

	char *szStr = "Hello, World!!";
	hdc = BeginPaint(hWnd, &ps);
	SetBkColor(hdc, RGB(10, 10, 10));  //文字の背景色を設定
	SetTextColor(hdc, RGB(200, 200, 200));  //文字の色を設定

	//Step1  フォント作成
	HFONT hFont1 = CreateFont(30, 0, //高さ, 幅
		0, 0, FW_BOLD,  //傾き(反時計回り), 文字の向き, 太字(0にするとデフォルト)
		TRUE, TRUE, FALSE,   //Italic,  下線,  打消し線
		SHIFTJIS_CHARSET, OUT_DEFAULT_PRECIS, //キャラクタセット, 物理フォントの検索方法
		CLIP_DEFAULT_PRECIS, DEFAULT_QUALITY,  //はみ出した場合の処理,  属性
		VARIABLE_PITCH | FF_ROMAN, NULL);    //ピッチ、ファミリ,  タイプフェイスのポインタ

	SelectObject(hdc, hFont1);  //Step2  フォントを適応
	TextOut(hdc, 10, 10, (LPCTSTR)szStr, (int)strlen(szStr)); //Step3  文字を描画
	DeleteObject(hFont1);   //Step4. HFONTオブジェクトを破棄
	EndPaint(hWnd, &ps);
}

このようになります。実行結果は以下の通り。  
![f:id:pythonjacascript:20200225224341j:plain](/images/ppythonjacascript2020022520200225224341.jpg "f:id:pythonjacascript:20200225224341j:plain")  

では、CreateFont関数の引数を説明していきます。

HFONT CreateFont(
    int nHeight,  //1文字の高さ（px）
    int nWidth,   //1文字あたりの横幅（px）。高さに合わせて自動で設定するには0にする
    int nEscapement,   //水平方向からの文字列の角度（反時計回り）
    int nOrientation,   //各文字の向きを水平方向から反時計回りに回転
    int nWeight,   //フォントの太さ。0, FALSE →デフォルト、FW_BOLD→太字
    BYTE bItalic,   //TRUE→イタリック体（斜め文字）になる（通常は0 or FALSE）
    BYTE bUnderline,   //TRUE→アンダーラインが引かれる（通常は0 or FALSE）
    BYTE cStrikeOut,   //TRUE→アンダーラインが引かれる（通常は0 or FALSE）
    BYTE nCharSet,   //キャラクタセットの設定
    BYTE nOutPrecision,   //出力精度　通常は OUT_DEFAULT_PRECIS
    BYTE nClipPrecision,   //はみ出た場合の処理 通常はCLIP_DEFAULT_PRECIS
    BYTE nQuality,   //属性 
    BYTE nPitchAndFamily,   //ピッチ、ファミリ
    LPCTSTR lpszFacename);   //フォント名

#### nWeightについて

FW\_BOLD以外にも、色々あります。

fnWeightの例→FW_DONTCARE（デフォルト）
FW_THIN	,  FW_EXTRALIGHT, FW_ULTRALIGHT, W_EXTRALIGHT,  FW_LIGHT, FW_NORMAL,  FW_REGULAR,  FW_NORMAL, FW_MEDIUM,
FW_SEMIBOLD, FW_DEMIBOLD, FW_SEMIBOLD, FW_BOLD, FW_EXTRABOLD, FW_ULTRABOLD,  FW_EXTRABOLD, FW_BLACK, FW_HEAVY

  
上の順番でだんだん文字が太くなっていきます。  
  
  
#### nCharSetについて

| 設定値               | 使用用途                               |
| ----------------- | ---------------------------------- |
| ANSI\_CHARSET     | ANSIフォントを使用。アルファベットと一部記号のみで日本語非対応。 |
| DEFAULT\_CHARSET  | デフォルト                              |
| SHIFTJIS\_CHARSET | SHIFT JISを使う場合。日本語OK               |

#### nEscapementとnOrientationについて

1/10°単位で指定できるので、45°回転させるには、450を代入します。  
![f:id:pythonjacascript:20200225230849j:plain](/images/ppythonjacascript2020022520200225230849.jpg "f:id:pythonjacascript:20200225230849j:plain")  

#### nQualityについて

出力品質を設定します。論理フォントと実際の物理フォントの属性をどの程度一定させるかを設定します。

| DEFAULT\_QUALITY     | 通常             |
| -------------------- | -------------- |
| ANTIALIASED\_QUALITY | フォントのふちが滑らかになる |

![f:id:pythonjacascript:20200225232530j:plain](/images/ppythonjacascript2020022520200225232530.jpg "f:id:pythonjacascript:20200225232530j:plain")  
うーん...違いが分からん...  
  
#### nPitchAndFamilyについて

以下の設定値をOR(｜)でつないだものを指定します。

VARIABLE_PITCH | FF_ROMAN  //例

  
ピッチ＝文字の間隔のこと

| 設定値             | 用途    |
| --------------- | ----- |
| DEFAULT\_PITCH  | デフォルト |
| FIXED\_PITCH    | 固定幅   |
| VARIABLE\_PITCH | 可変幅   |

![f:id:pythonjacascript:20200225233021j:plain](/images/ppythonjacascript2020022520200225233021.jpg "f:id:pythonjacascript:20200225233021j:plain")  

ファミリ

| 設定値            | 使用用途                                            |
| -------------- | ----------------------------------------------- |
| FF\_DECORATIVE | 装飾付きフォント。例：Old English                          |
| FF\_DONTCARE   | デフォルト                                           |
| FF\_MODERN     | 一定のストローク幅を持つフォント。例：Pica, Elite, and Courier New |
| FF\_ROMAN      | 可変ストローク幅を持ち、セリフ付き。例： MS Serif                   |
| FF\_SCRIPT     | 手書きのようなフォント。 Script and Cursive are examples.   |
| FF\_SWISS      | 可変ストローク幅、セリフなし。 例：MS?Sans Serif is an example.  |

  
![f:id:pythonjacascript:20200225234447j:plain](/images/ppythonjacascript2020022520200225234447.jpg "f:id:pythonjacascript:20200225234447j:plain")  
....？？

  
次に行きます。

#### lpszFacenameについて

フォント名を指定します。

HFONT hFont1 = CreateFont(30, 0, 0, 0, 0, FALSE, FALSE, FALSE, SHIFTJIS_CHARSET, OUT_DEFAULT_PRECIS, CLIP_DEFAULT_PRECIS, DEFAULT_QUALITY,
        VARIABLE_PITCH | FF_DONTCARE, "HG行書体");

  
というふうに書き換えて実行すると、  
![f:id:pythonjacascript:20200225235955j:plain](/images/ppythonjacascript2020022520200225235955.jpg "f:id:pythonjacascript:20200225235955j:plain")  
こうなります。

  
利用可能なフォントの一覧を取得するには、一例としてPythonをあげると、

import pygame
 print(pygame.font.get_fonts())

を実行してください。  
  
### 参考文献

[WindowsプログラムとWin32 API](http://bkhouse.web.fc2.com/Pro4.htm)  
<https://flylib.com/books/en/4.460.1.25/1/>  
[フォント](http://wisdom.sakura.ne.jp/system/winapi/win32/win131.html)  