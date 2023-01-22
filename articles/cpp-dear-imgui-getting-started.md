---
title: "【C++】Dear ImGui を導入してみた（Visual Studio 2015)"
emoji: "🤖"
type: "tech"
topics: undefined
published: false
---

* [Dear ImGuiとは](#Dear-ImGuiとは)
* [開発環境](#開発環境)
* [導入方法](#導入方法)  
   * [Step 1.](#Step-1)  
   * [Step 2.](#Step-2)  
   * [Step 3.](#Step-3)  
   * [Step 4.](#Step-4)
* [完了](#完了)
  
  
### Dear ImGuiとは

**Dear ImGui**とはGUIのFrameworkの一種で、DirectXやOpenglをもとに動いています。ImGUI以外にもGUIのフレームワークは色々あるのですが（[このサイト](https://nantonaku-shiawase.hatenablog.com/entry/2014/01/09/220636)や[このサイト](https://qiita.com/shimacpyon/items/e5d2a4e2019273345c37)にとても詳しくまとまっていました。）その中でも見た目がよさそうだったDear ImGuiを試してみることにしました。

どうやら、Dear ImGui と[imgui](https://github.com/AdrienHerubel/imgui)の2種類があり、それぞれ全く別物の様です。今回導入したのは前者の方です。

これ以降、Dear ImGui を略してImGuiと書いていきます。

（ImGuiのGitHubページ）  
[github.com](https://github.com/ocornut/imgui)  

### 開発環境

・Visual Studio 2015  
・ImGuiは2020/02/13の時点で最新のものを使っています。  
  
  
### 導入方法

##### Step 1.

まず、下のGitHubからImGuiのレポジトリをZIPダウンロードして、適当な場所に解凍します。  
（ZIPダウンロードは緑のボタンの「Download ZIP」から可能）  
[github.com](https://github.com/ocornut/imgui)  
  
  
##### Step 2.

次に、回答したフォルダの中の「**imgui-master/examples/example\_win32\_directx11/example\_win32\_directx11.vcxproj**\]をVisual Studioで開きます。そして、ソリューションのビルド→デバッグ（実行）します。（↑のソルーションには5つのプロジェクトが含まれていますが、僕はDirectx11のプロジェクトを使っています。ほかのプロジェクトは「プロジェクトのアンロード」でもしておいてください。）

この時点でビルドが成功しなかった場合は開発環境に問題があると思います。

ビルドが成功すれば、プログラムを実行すると以下の画面が出てくるはずです。  
![f:id:pythonjacascript:20200213003713j:plain](/images/ppythonjacascript2020021320200213003713.jpg "f:id:pythonjacascript:20200213003713j:plain")  

  
では、次のステップに進みます。  
  
##### Step 3.

ここからは、プロジェクトを特定のフォルダに移していきます。

とりあえず、上のプロジェクトのフォルダごと好きな場所に移します。

しかし、ただコピー＆ペーストしただけだと、プロジェクトのpathが変わっているため、main.cpp以外に必要なファイルを読み込もうとすると**#include**文でエラーが出ます。

必要なファイルは、以下の通り。

* imconfig.h
* imgui.cpp
* imgui.h
* imgui.ini
* imgui\_demo.cpp
* imgui\_draw.cpp
* imgui\_impl\_dx11.cpp
* imgui\_impl\_dx11.h
* imgui\_impl\_win32.cpp
* imgui\_impl\_win32.h
* imgui\_internal.h
* imgui\_widgets.cpp

これらを**main.cppと同じディレクトリにコピー＆ペースト**するか、  
プロジェクトを選択した状態で、メニューバーの**「プロジェクト」→「プロパティ」→「C/C++」→「追加のインクルードディレクトリ」**で上に列挙したファイル達のpathを登録する、のどちらか2通りで解決してください。  
![f:id:pythonjacascript:20200213004918j:plain](/images/ppythonjacascript2020021320200213004918.jpg "f:id:pythonjacascript:20200213004918j:plain")  

  
因みに、僕は以下の様に include するファイルを「imgui」のフォルダに入れて、その中のコードを全て上の画像の方法で追加しました。

<.vs>
 ├ <Hello_World1>
 │  └ <v14>
 │  　  └ .suo
 └ <example_win32_directx11>
 　  └ <v14>
 　  　  └ .suo
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
Hello_World1.sln
build_win32.bat
example_win32_directx11.vcxproj
example_win32_directx11.vcxproj.filters
example_win32_directx11.vcxproj.user
imgui.ini
main.cpp
  
  
##### Step 4.

新しくincludeファイルを登録するのと同時に、プロジェクトフォルダ移動前の依存ファイルをプロジェクトから除いておく必要があります。

そのためには、「**ソリューションエクスプローラー**（デフォルトだとVisual Studioの画面右側にある）」にある「imgui」の中と「sources」のmain.cpp以外のファイルを選択して「**プロジェクトから除外**」を選択します。  
![f:id:pythonjacascript:20200213005428j:plain](/images/ppythonjacascript2020021320200213005428.jpg "f:id:pythonjacascript:20200213005428j:plain")

#### 完了

Step2～4を行うと、プロジェクトフォルダを移動してもエラーが出ずにビルドできるはず...  
![f:id:pythonjacascript:20200213003713j:plain](/images/ppythonjacascript2020021320200213003713.jpg "f:id:pythonjacascript:20200213003713j:plain")  