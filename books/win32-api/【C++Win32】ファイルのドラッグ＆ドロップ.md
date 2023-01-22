---
title: "【C++ Win32】ファイルのドラッグ＆ドロップ"
emoji: ""
type: ""
topics: []
published: false
---

ファイル（複数の場合もある）を Drag & Drop することで、そのファイルを取り込む機能（正確には Drag & Drop されたファイルのpathを取得する機能）を作ります

* [サンプルプログラム](#サンプルプログラム)
* [実行結果](#実行結果)

### サンプルプログラム

ファイルが Drag & Drop されると、**その場所に円を描画し、 Drag & Drop されたファイル名を表示**するプログラムです

#include <windows.h>

LRESULT CALLBACK WndProc(HWND, UINT, WPARAM, LPARAM);


RECT rec;

char szStr[200] = "please drag file";
POINT dropped_point;


char szClassName[] = "sample02";


int WINAPI WinMain(HINSTANCE hCurInst, HINSTANCE hPrevInst,
	LPSTR lpsCmdLine, int nCmdShow){
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
	wc.hIcon = (HICON)LoadImage(NULL,
		MAKEINTRESOURCE(IDI_APPLICATION),
		IMAGE_ICON,
		0,
		0,
		LR_DEFAULTSIZE | LR_SHARED);
	wc.hCursor = NULL;
	wc.hbrBackground = (HBRUSH)GetStockObject(WHITE_BRUSH);
	wc.lpszMenuName = NULL;	
	wc.lpszClassName = (LPCSTR)szClassName;
	wc.hIconSm = NULL;

	if ((atom = RegisterClassEx(&wc)) == 0)	return FALSE;

	hWnd = CreateWindow(MAKEINTATOM(atom),"DragDrop", WS_OVERLAPPEDWINDOW, CW_USEDEFAULT, CW_USEDEFAULT, CW_USEDEFAULT, CW_USEDEFAULT,NULL, NULL, hCurInst, NULL);
	if (!hWnd) return FALSE;

	DragAcceptFiles(hWnd, TRUE);

	ShowWindow(hWnd, nCmdShow);
	UpdateWindow(hWnd);
	while ((bRet = GetMessage(&msg, NULL, 0, 0)) != 0) {
		if (bRet == -1) {
			break;
		}
		else {
			TranslateMessage(&msg);
			DispatchMessage(&msg);
		}
	}
	return (int)msg.wParam;
}


LRESULT CALLBACK WndProc(HWND hWnd, UINT msg, WPARAM wp, LPARAM lp){
	int num, i;
	HDROP hdrop;
	TCHAR filename[100];
	HBRUSH hBrush;
	HPEN hPen;
	char mouse_pos[50];
	WINDOWINFO windowinfo;


	switch (msg) {
	case WM_PAINT:
		HDC hdc;
		PAINTSTRUCT ps;
		hdc = BeginPaint(hWnd, &ps);

		GetClientRect(hWnd, &rec);  //ウインドウのサイズを取得
		hBrush = (HBRUSH)CreateSolidBrush(RGB(0, 0, 0));
		SelectObject(hdc, hBrush);
		PatBlt(hdc, 0, 0, rec.right, rec.bottom, PATCOPY);

		wsprintf(mouse_pos, "x座標 =  %d ,  y座標= %d ", dropped_point.x, dropped_point.y);
		TextOut(hdc, 0, 60, mouse_pos, (int)strlen(mouse_pos));
		DeleteObject(hBrush);
		hPen = CreatePen(PS_SOLID, 0, RGB(255, 0, 255)); //図形の枠線の色を設定
		SelectObject(hdc, hPen);
		hBrush = (HBRUSH)CreateSolidBrush(RGB(255, 255, 0));
		SelectObject(hdc, hBrush);		//図形内部の塗りつぶし方と色を設定
		Ellipse(hdc, dropped_point.x - 10, dropped_point.y - 10, dropped_point.x + 10, dropped_point.y + 10);		//楕円を描く

		SetBkColor(hdc, RGB(10, 10, 10));  //文字の背景色を設定
		SetTextColor(hdc, RGB(200, 200, 200));  //文字の色を設定
		TextOut(hdc, 10, 10, (LPCTSTR)szStr, (int)strlen(szStr));
		EndPaint(hWnd, &ps);
		break;


	case WM_DROPFILES:
		GetCursorPos(&dropped_point);
		windowinfo.cbSize = sizeof(WINDOWINFO);
		GetWindowInfo(hWnd, &windowinfo);

		dropped_point.x -= windowinfo.rcWindow.left + 3;
		dropped_point.y -= windowinfo.rcWindow.top + 22;


		hdrop = (HDROP)wp;
		//ドロップされたFile数
		num = DragQueryFile(hdrop, -1, NULL, 0);

		for (i = 0; i < num; i++) {
			DragQueryFile(hdrop, i, filename, sizeof(filename)/sizeof(TCHAR));
			//TODO: filenameにDrag DropしたファイルのフルPathが入っているので、処理をする
		}
		DragFinish(hdrop);
		wsprintf(szStr, "number = %d ,  name = %s", num, filename);
		InvalidateRect(hWnd, NULL, FALSE);
		break;


	case WM_DESTROY:
		DeleteObject(hPen);
		DeleteObject(hBrush);
		PostQuitMessage(0);
		break;

	default:
		return (DefWindowProc(hWnd, msg, wp, lp));
	}
	return 0;
}

### 実行結果

何かファイルをドラッグ＆ドロップしてみてください。下のようにドラッグアンドドロップされた場所と、ファイル名が表示されるはずです  
![f:id:pythonjacascript:20200320231454j:plain](/images/ppythonjacascript2020032020200320231454.jpg "f:id:pythonjacascript:20200320231454j:plain")