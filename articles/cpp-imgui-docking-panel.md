---
title: "【C++ dear ImGui】ドッキングパネルを作る"
emoji: ""
type: ""
topics: undefined
published: false
---

* [プロジェクト作成](#プロジェクト作成)
* [実行結果](#実行結果)
* [参考文献](#参考文献)

C++のGUIフレームワークの一つである **dear ImGUi** を使ってドッキングパネルを作ります。自分用のメモとして書いています。

ImGuiの通常のプロジェクト作成はこちら：  
[shizenkarasuzon.hatenablog.com](https://shizenkarasuzon.hatenablog.com/entry/2020/03/21/000515)  
  
  
### プロジェクト作成

ドッキングパネルの機能は dear ImGui のmasuterには搭載されておらず、docking という branch 上に実装されています。  
<https://github.com/ocornut/imgui>

よって、この記事は docking branchを使ってImGuiのプロジェクトを作成する、という記事です。

  
まず、dockingブランチをcloneします。

git clone -b ブランチ名 https://リポジトリのアドレス

cloneしたフォルダの中の、  
・「imgui/examples/example\_win32\_directx11」の中のファイル全て（「example\_win32\_directx11.vcxproj」など」）  
・「imgui/examples/」の中の「imgui\_impl\_win32.cpp」「imgui\_impl\_win32.h」「imgui\_impl\_dx11.cpp」「imgui\_impl\_dx11.h」  
・「imgui/ 」にあるcppファイルとhファイル  
を下の様に一つのフォルダの中に移動します

(何らかのフォルダの中身)
<imgui>
 ├ imconfig.h
 ├ imgui.cpp
 ├ imgui.h
 ├ imgui.ini
 ├ imgui_demo.cpp
 ├ imgui_draw.cpp
 ├ imgui_impl_dx11.cpp
 ├ imgui_impl_dx11.h
 ├ imgui_impl_win32.cpp
 ├ imgui_impl_win32.h
 ├ imgui_internal.h
 ├ imgui_widgets.cpp
 └ imstb_truetype.h
build_win32.bat
example_win32_directx11.vcxproj
example_win32_directx11.vcxproj.filters
main.cpp

  
この状態でVisualStudioを起動すると、ファイルの相対パスが変わっているのでincludeエラーが出ます。そのエラーを解消するために、  
Visual Studioの「ソルーションエクスプローラ」（下画像）に入っている「main.cpp」以外のファイルを、右クリック→「プロジェクトから除外」します。  
![f:id:pythonjacascript:20200330000402j:plain](/images/ppythonjacascript2020033020200330000402.jpg "f:id:pythonjacascript:20200330000402j:plain")  

そして、ソリューションエクスプローラ上で「imgui」フォルダを右クリックして「追加」→「既存の項目」から、先ほど移動したファイル群を選択します。  
すると、下画像のようになります  
![f:id:pythonjacascript:20200330000659j:plain](/images/ppythonjacascript2020033020200330000659.jpg "f:id:pythonjacascript:20200330000659j:plain")  
  
これで、VisualStudioの環境設定は終了したので、後は、

main.cppの中の

#include "imgui.h"
#include "imgui_impl_win32.h"
#include "imgui_impl_dx11.h"

  
を  

#include "imgui/imgui.h"
#include "imgui/imgui_impl_win32.h"
#include "imgui/imgui_impl_dx11.h"

  
に変更してビルドすると、ビルドが通るはず...です  
  
  
### 実行結果

![f:id:pythonjacascript:20200329234824g:plain](/images/ppythonjacascript2020032920200329234824.gif "f:id:pythonjacascript:20200329234824g:plain")  
  
  
### 参考文献

[参考プログラム – ☆PROJECT ASURA☆](http://project-asura.com/blog/archives/4085)