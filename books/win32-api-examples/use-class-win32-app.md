---
title: "【C++ Win32】WndProc関数含め、WIN32APIアプリケーションのクラス化"
emoji: ""
type: ""
topics: []
published: false
---

今回は、タイトルの通り、Win32 APIを使ったアプリケーションをクラスを使って書こう、という記事です。

問題点が、「**static**」なのです！！  
イベントを処理する関数を作っても、lpfnWndProc に代入して関連付けできるのはstaticな関数だけなのです。

そして、静的メンバ関数はstaticな変数しか処理できません。( ﾟДﾟ)

...みたいな感じで苦戦していたのですが（全てglobal変数にしようかと考えたけどやめた）、そんな中[このサイト](https://suzulang.com/win32apiでウィンドウをカプセル化/)や[このサイト](https://elcharolin.wordpress.com/2015/01/24/wndproc-as-a-class-member-win32/)に出会い、無事WndProc関数のクラス関数化に成功しました！！

以下が完成したプログラムです

#include <windows.h>

class Window_template {
public:
	bool setup(HINSTANCE, HINSTANCE, LPSTR, int);
	bool MainLoop();
	void setWindowName(LPCSTR window_name);
	void setAppName(LPCSTR app_name);
	void setWindowSize(int, int);
	void setWindowPos(int, int);
	LRESULT CALLBACK WndProc(HWND, UINT, WPARAM, LPARAM);

private:
	static LRESULT CALLBACK MessageRouter(HWND, UINT, WPARAM, LPARAM);

	HINSTANCE _hCurInst;
	HINSTANCE _hPrevInst;
	LPSTR _lpsCmdLine;
	int _nCmdShow;
	WNDCLASSEX _wc;
	HWND _hWnd;

	LPCSTR _window_name = "window_name";
	LPCSTR _app_name = "_app_name";
	int _wm_width = CW_USEDEFAULT;
	int _wm_height = CW_USEDEFAULT;
	int _wm_pos_x = CW_USEDEFAULT;
	int _wm_pos_y = CW_USEDEFAULT;
};


bool Window_template::setup(HINSTANCE hCurInst, HINSTANCE hPrevInst, LPSTR lpsCmdLine, int nCmdShow) {
	ATOM atom;

	_hCurInst = hCurInst;
	_hPrevInst = hPrevInst;
	_lpsCmdLine = lpsCmdLine;
	_nCmdShow = nCmdShow;

	_wc.cbSize = sizeof(WNDCLASSEX);
	_wc.style = CS_HREDRAW | CS_VREDRAW;
	_wc.lpfnWndProc = MessageRouter;	//プロシージャ名、ここではstaticしか関連付けできない
	_wc.cbClsExtra = 0;
	_wc.cbWndExtra = 0;
	_wc.hInstance = hCurInst;
	_wc.hIcon = NULL;
	_wc.hCursor = (HCURSOR)LoadImage(NULL, MAKEINTRESOURCE(IDC_ARROW), IMAGE_CURSOR, 0, 0, LR_DEFAULTSIZE | LR_SHARED);
	_wc.hbrBackground = (HBRUSH)GetStockObject(BLACK_BRUSH);
	_wc.lpszMenuName = NULL;
	_wc.lpszClassName = (LPCSTR)_app_name;
	_wc.hIconSm = NULL;

	if ((atom = RegisterClassEx(&_wc)) == 0) {
		MessageBox(NULL, "Failed to Create Window", "ERROR1-1", MB_OK);
		return false;
	}

	_hWnd = CreateWindowEx(NULL, MAKEINTATOM(atom), _window_name, WS_OVERLAPPEDWINDOW, _wm_pos_x, _wm_pos_y,  _wm_width, _wm_height,NULL,NULL, hCurInst, this);


	if (!_hWnd) {
		MessageBox(NULL, "Failed to Create Window", "ERROR1-2", MB_OK);
		return false;
	}

	ShowWindow(_hWnd, _nCmdShow);
	UpdateWindow(_hWnd);
	return true;
}

LRESULT Window_template::WndProc(HWND hWnd, UINT msg, WPARAM wp, LPARAM lp) {
	switch (msg) {
	case(WM_DESTROY):
		::PostQuitMessage(0);
		break;
	default:
		return ::DefWindowProc(hWnd, msg, wp, lp);
	}
	return 0;
}


LRESULT CALLBACK Window_template::MessageRouter(HWND hWnd, UINT msg, WPARAM wParam, LPARAM lParam) {
        //参考 → https://suzulang.com/win32apiでウィンドウをカプセル化/
	Window_template* app;
	if (msg == WM_CREATE) {
		app = (Window_template*)(((LPCREATESTRUCT)lParam)->lpCreateParams);
		SetWindowLongPtr(hWnd, GWLP_USERDATA, (LONG_PTR)app);

	}
	else {
		app = (Window_template*)GetWindowLongPtr(hWnd, GWLP_USERDATA);
	}
	return app->WndProc(hWnd, msg, wParam, lParam);
}

bool Window_template::MainLoop() {
	MSG msg;
	if (GetMessage(&msg, NULL, 0, 0)) {
		TranslateMessage(&msg);
		DispatchMessage(&msg);
		return true;
	}
	return false;
}

void Window_template::setWindowSize(int w, int h) { _wm_width = w; _wm_height = h; }
void Window_template::setWindowPos(int x, int y) { _wm_pos_x = x; _wm_pos_y = y; }
void Window_template::setWindowName(LPCSTR window_name) { _window_name = window_name; }
void Window_template::setAppName(LPCSTR app_name) { _app_name = app_name; }


int WINAPI WinMain(HINSTANCE hCurInst, HINSTANCE hPrevInst,
	LPSTR lpsCmdLine, int nCmdShow) {
	Window_template window1;

	bool loop1 = window1.setup(hCurInst, hPrevInst, lpsCmdLine, nCmdShow);

	while (loop1) {
		loop1 = window1.MainLoop();
	}
}