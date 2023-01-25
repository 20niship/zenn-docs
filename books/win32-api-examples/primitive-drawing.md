---
title: "【C++ Win32】図形の描画（Win32 APIシリーズ第3回）"
emoji: ""
type: ""
topics: []
published: false
---

* [目標](#目標)
* [プログラム](#プログラム)
* [実行結果](#実行結果)
* [解説](#解説)  
   * [概要](#概要)
* [Step1 前処理](#Step1-前処理)
* [Step2 HPEN、HBRUSHの作成](#Step2-HPENHBRUSHの作成)
* [Step3\. 図形を描画](#Step3-図形を描画)  
   * [長方形](#長方形)
* [直線](#直線)
* [円弧](#円弧)
* [Step5 後処理](#Step5-後処理)

### 目標

Window上に図形を描く。  
完成形↓  
![f:id:pythonjacascript:20200226001120j:plain](/images/ppythonjacascript2020022620200226001120.jpg "f:id:pythonjacascript:20200226001120j:plain")  

### プログラム

// draw_figures.cpp
#include <windows.h>

LRESULT CALLBACK WndProc(HWND, UINT, WPARAM, LPARAM);
char szClassName[] = "sample02";	//ウィンドウクラス

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
	wc.hIcon = NULL;
	wc.hCursor = (HCURSOR)LoadImage(NULL,MAKEINTRESOURCE(IDC_ARROW),IMAGE_CURSOR,0,0,LR_DEFAULTSIZE | LR_SHARED);
	wc.hbrBackground = (HBRUSH)GetStockObject(BLACK_BRUSH); //背景色設定
	wc.lpszMenuName = NULL;	//メニュー名
	wc.lpszClassName = (LPCSTR)szClassName;
	wc.hIconSm =NULL;

	if ((atom = RegisterClassEx(&wc)) == 0)
		return FALSE;

	hWnd = CreateWindow(MAKEINTATOM(atom), "Window Title", WS_OVERLAPPEDWINDOW, 
		CW_USEDEFAULT,CW_USEDEFAULT,CW_USEDEFAULT,CW_USEDEFAULT,
		NULL,NULL,hCurInst,	NULL);

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
	case WM_DESTROY:
		PostQuitMessage(0);
		break;

	case WM_PAINT:
		MyPaint(hWnd);
		break;

	default:
		return (DefWindowProc(hWnd, msg, wp, lp));
	}
	return 0;
}

void MyPaint(HWND hWnd) {
	HDC hdc;
	PAINTSTRUCT ps;

	HPEN hPen;
	HBRUSH hBrush;

	hdc = BeginPaint(hWnd, &ps);
	
	//点を描く
	for(int i=0;i<10;i++)
		SetPixelV(hdc, 5+5*i, 5, RGB(255, 255, 255));

	//図形の枠線の色を設定
	hPen = CreatePen(PS_SOLID, 0, RGB(255, 0, 255)); 
	SelectObject(hdc, hPen);

	//図形内部の塗りつぶし方と色を設定
	hBrush = CreateSolidBrush(RGB(60, 60, 60));  
	SelectObject(hdc, hBrush);  

	//長方形を描く
	Rectangle(hdc, 10, 10, 60, 60);  
	DeleteObject(hBrush);

	//NULLにすると描かれない（BrushをNULLにすると枠線のみ、PenをNULLにすると枠線なし）
	hBrush = (HBRUSH)GetStockObject(NULL_BRUSH);  
	SelectObject(hdc, hBrush);
	Rectangle(hdc, 20, 50, 70, 100);

	//hPenを使って線を描く
	MoveToEx(hdc, 100, 50, NULL);  
	LineTo(hdc, 150, 65);

	//描画ブラシの変更
	DeleteObject(hBrush);
	hBrush = (HBRUSH)CreateSolidBrush(RGB(255, 255, 0));
	SelectObject(hdc, hBrush);

	//描画ペンの変更
	DeleteObject(hPen);
	hPen = (HPEN)CreatePen(PS_SOLID, 3, RGB(0, 255, 0));
	SelectObject(hdc, hPen);


	//楕円を描く
	Ellipse(hdc, 120, 100, 160, 160);
    
	//扇形を描く
	Pie(hdc, 120, 200, 200, 250, 200, 225, 123, 210);

	//作成したhPenとhBrushは削除する。
	DeleteObject(hPen); 
	DeleteObject(hBrush);
	EndPaint(hWnd, &ps);
}

### 実行結果

上のプログラムを実行すると  
![f:id:pythonjacascript:20200226001120j:plain](/images/ppythonjacascript2020022620200226001120.jpg "f:id:pythonjacascript:20200226001120j:plain")  
このWindowが出てきます。  
  
### 解説

第一回のひな形のプログラムと変化した部分を抜き出すと、↓の部分が該当します。

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

	HPEN hPen;
	HBRUSH hBrush;

	hdc = BeginPaint(hWnd, &ps);
	
	//点を描く
	for(int i=0;i<10;i++)
		SetPixelV(hdc, 5+5*i, 5, RGB(255, 255, 255));

	//図形の枠線の色を設定
	hPen = CreatePen(PS_SOLID, 0, RGB(255, 0, 255)); 
	SelectObject(hdc, hPen);

	//図形内部の塗りつぶし方と色を設定
	hBrush = CreateSolidBrush(RGB(60, 60, 60));  
	SelectObject(hdc, hBrush);  

	//長方形を描く
	Rectangle(hdc, 10, 10, 60, 60);  
	DeleteObject(hBrush);

	//NULLにすると描かれない（BrushをNULLにすると枠線のみ、PenをNULLにすると枠線なし）
	hBrush = (HBRUSH)GetStockObject(NULL_BRUSH);  
	SelectObject(hdc, hBrush);
	Rectangle(hdc, 20, 50, 70, 100);

	//hPenを使って線を描く
	MoveToEx(hdc, 100, 50, NULL);  
	LineTo(hdc, 150, 65);

	//描画ブラシの変更
	DeleteObject(hBrush);
	hBrush = (HBRUSH)CreateSolidBrush(RGB(255, 255, 0));
	SelectObject(hdc, hBrush);

	//描画ペンの変更
	DeleteObject(hPen);
	hPen = (HPEN)CreatePen(PS_SOLID, 3, RGB(0, 255, 0));
	SelectObject(hdc, hPen);


	//楕円を描く
	Ellipse(hdc, 120, 100, 160, 160);
    
	//扇形を描く
	Pie(hdc, 120, 200, 200, 250, 200, 225, 123, 210);

	//作成したhPenとhBrushは削除する。
	DeleteObject(hPen); 
	DeleteObject(hBrush);
	EndPaint(hWnd, &ps);
}
  
  
##### 概要

この部分で↑の画像のような図形を描いているのですが、以下の手順を踏まなければなりません。

1. Windowのデバイスコンテキストハンドル HDC を取得【BeginPaint関数】
2. **HPEN**オブジェクトと**HBRUSH**オブジェクトを作成【CreatePen関数、CreateSolidBrush関数、CreateHatchBrush関数】
3. 2.で作ったHPENとHBRUSHをデバイスコンテキストに関連付ける【SelectObject】
4. LineTo関数やRectangle関数を使って図形を描く
5. 2.で作ったHPENとHBRUSHオブジェクトを破棄する【DeleteObject関数】
6. 描画がすべて完了したら、【EndPaint関数】を呼ぶ

> ただし、BeginPaint関数、EndPaint関数を使う代わりに、GetDC関数、ReleaseDC関数を使うこともあります

それぞれのステップについてみていきます。

#### Step1 前処理

ウィンドウ上に図形を書くには、そのウィンドウの**デバイスコンテキストハンドル**（Device Context Handle -> HDC型）を取得する必要があります。デバイスコンテキストには「デバイス（ディスプレイ、プリンタなど）の描画属性に関する情報」が格納されており、デバイスコンテキスト「ハンドル」とは、そのデバイスコンテキストへのポインタのようなものです。

デバイスコンテキストハンドルを取得するにはBeginPaint関数を使用します

HDC BeginPaint(HWND hWnd, LPPAINTSTRUCT lpPS)

* 第1引数→Windowハンドル
* 第2引数→PAINTSTRUCT型のオブジェクトのポインタ。直前にPAINTSTRUCT ps; と書いて、&psを渡せばよい
* 返り値→成功すればデバイスコンテキストハンドル、失敗すればFALSE

#### Step2 HPEN、HBRUSHの作成

図形の枠線の形状や色、線幅の情報を格納したものが**HPEN**型のオブジェクトです。一方、図形の塗りつぶしの色、塗りつぶしパターンの情報を格納したものが**HbRUSH**型オブジェクトです。  
HPENオブジェクトは図形の枠を描く時に使われ、HBRUSHオブジェクトは図形内部を塗りつぶすときに使われます。

  
それぞれ、CreatePen関数、CreateSolidBrush関数（塗りつぶしパターンがあるときはCreateHatchBrush関数）で作成します

HPENオブジェクトを作成

HPEN CreatePen(int iStyle, int cWidth, COLORREF color);
//例↓
HPEN  hPen = CreatePen(PS_SOLID, 1, RGB(255,255,0)); // 1pxの線幅の黄色のHPENを作成

* 第1引数→ペンの描画スタイル（↓の表参照）
* 第2引数→線幅（px）
* 第3引数→線の色

第一引数にしていできる値は以下の表のようなものがあります

PS\_SOLID

実線。（普通はこれを使う）

PS\_DASH

破線