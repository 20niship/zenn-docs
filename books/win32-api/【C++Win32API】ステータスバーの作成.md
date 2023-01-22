---
title: "【C++/Win32 API】ステータスバーの作成"
emoji: ""
type: ""
topics: []
published: false
---

* [サンプルプログラム](#サンプルプログラム)
* [実行結果](#実行結果)
* [ステータスバーを作成](#ステータスバーを作成)
* [ステータスバーの設定](#ステータスバーの設定)
* [ステータスバーの表示文字列を変える](#ステータスバーの表示文字列を変える)

### サンプルプログラム

ステータスバーを作成し、そこに表示する文字を変更するプログラムです。  
右クリックすると「right button click!」、左クリックで「left button click!」と表示されます。

#include <windows.h>
#include <commctrl.h>

#define MY_STATUS 100

char szClassName[] = "sample02";
HWND CreateStatusbar(HWND hWnd);
HINSTANCE hInst; //インスタンスハンドル


LRESULT CALLBACK WndProc(HWND hWnd, UINT msg, WPARAM wp, LPARAM lp) {
  static HWND hStatus;
  //INITCOMMONCONTROLSEX ic;

  switch (msg) {
  case WM_CREATE:
    /*
    ic.dwICC = ICC_BAR_CLASSES;
    ic.dwSize = sizeof(INITCOMMONCONTROLSEX);
    InitCommonControlsEx(&ic);
    */
    hStatus = CreateWindowEx(
      0,    //拡張スタイル
      STATUSCLASSNAME,    //ウィンドウクラス
      NULL,               //タイトル
      WS_CHILD | CCS_BOTTOM | WS_VISIBLE, //ウィンドウスタイル 
      0, 0,    //座標（x,y)
      0, 0,    //大きさ(width, height)
      hWnd,    //親ウィンドウ
      (HMENU)MY_STATUS,    //ステータスバーのＩＤ
      hInst,    //インスタンスハンドル
      NULL);
    SendMessage(hStatus, SB_SIMPLE, 0, 0);
    SendMessage(hStatus, SB_SETTEXT, 0 | 0, (LPARAM)"Status bar");
    break;

  case WM_LBUTTONDOWN:
    SendMessage(hStatus, SB_SETTEXT, SB_SIMPLEID | SBT_NOBORDERS, (LPARAM)"left button clicked!!");
    break;

  case WM_RBUTTONDOWN:
    SendMessage(hStatus, SB_SETTEXT, SB_SIMPLEID | SBT_NOBORDERS, (LPARAM)"right button clicked!!");
    break;


  case WM_SIZE:
    SendMessage(hStatus, msg, wp, lp);
    break;

  case WM_DESTROY:
    DestroyWindow(hStatus);
    PostQuitMessage(0);
    break;

  default:
    return (DefWindowProc(hWnd, msg, wp, lp));
  }
  return 0;
}


int WINAPI WinMain(HINSTANCE hCurInst, HINSTANCE hPrevInst,
  LPSTR lpsCmdLine, int nCmdShow){
  MSG msg;
  BOOL bRet;
  WNDCLASSEX wc;
  HWND hWnd;
  ATOM atom;

  wc.cbSize = sizeof(WNDCLASSEX);
  wc.style = CS_HREDRAW | CS_VREDRAW;
  wc.lpfnWndProc = WndProc;  //プロシージャ名
  wc.cbClsExtra = 0;
  wc.cbWndExtra = 0;
  wc.hInstance = hCurInst;//インスタンス
  wc.hIcon = NULL;
  wc.hCursor = (HCURSOR)LoadImage(NULL,MAKEINTRESOURCE(IDC_ARROW),IMAGE_CURSOR,0,0,LR_DEFAULTSIZE | LR_SHARED);
  wc.hbrBackground = (HBRUSH)GetStockObject(WHITE_BRUSH);
  wc.lpszMenuName = NULL;  //メニュー名
  wc.lpszClassName = (LPCSTR)szClassName;
  wc.hIconSm = NULL;

  if ((atom = RegisterClassEx(&wc)) == 0)
    return FALSE;

  hWnd = CreateWindow(MAKEINTATOM(atom)/*szClassName*/,
    "ステータスバー", //タイトルバーにこの名前が表示されます
    WS_OVERLAPPEDWINDOW, //ウィンドウの種類
    CW_USEDEFAULT,  //Ｘ座標
    CW_USEDEFAULT,  //Ｙ座標
    CW_USEDEFAULT,  //幅
    CW_USEDEFAULT,  //高さ
    NULL, //親ウィンドウのハンドル、親を作るときはNULL
    NULL, //メニューハンドル、クラスメニューを使うときはNULL
    hCurInst, //インスタンスハンドル
    NULL);
  if (!hWnd)
    return FALSE;

  hInst = hCurInst;

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

  
### 実行結果

右クリックすると「right button click!」、左クリックで「left button click!」と表示されます。  
![f:id:pythonjacascript:20200616123544j:plain](/images/ppythonjacascript2020061620200616123544.jpg "f:id:pythonjacascript:20200616123544j:plain")  
  
  
### ステータスバーを作成

ステータスバーも、ボタンやエディとボックスと同じで、**子ウィンドウ**として扱われます。よって、ステータスバーを作成する時も**CreateWindow**関数を使用します

hwndStatus = CreateWindowEx(
    0,                       // 拡張スタイル（0＝拡張しない）
    STATUSCLASSNAME,         // ステータスバー作成時はSTATUSCLASSNAME
    (PCTSTR) NULL,           // 初期のテキスト
    SBARS_SIZEGRIP |         // includes a sizing grip
    WS_CHILD | WS_VISIBLE,   // creates a visible child window
      0, 0,    //座標（x,y)（0にすると自動割り当て）
      0, 0,    //大きさ(width, height)（0にすると自動割り当て）
    hwndParent,              //親ウィンドウ
    (HMENU) MY_STATUS,     //ステータスバーのＩＤ
    hinst,                        //インスタンスハンドル
    NULL);                     // no window creation data
  
  
### ステータスバーの設定

ステータスバーを縦方向に分割することもできます。

SendMessage(hStatus, SB_SETPARTS, 3, -1);
//SendMessage(hStatus, SB_SETPARTS, nParts, nWidth);

CreateWindowEx関数でステータスバーを作成したのち、↑のようにSendMessage関数を呼び出すことでステータスバーを分割することができます。

分割せずにシンプルに表示する場合は、以下の文をCreateWindowEx関数の後ろに追加します

SendMessage(hStatus, SB_SIMPLE, 0, 0);
  
  
### ステータスバーの表示文字列を変える

SendMessage(hStatus, SB_SETTEXT, 0 | 0, (LPARAM)"Status bar");

このようにSendMessage関数でステータスバーの文字を変えることができます。