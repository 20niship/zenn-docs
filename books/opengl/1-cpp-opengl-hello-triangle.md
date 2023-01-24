---
title: "【C++/OpenGL】NO.1 OpenGLの仕組みと三角形表示"
emoji: ""
type: ""
topics: []
published: false
---

* [OpenGLとは](#OpenGLとは)
* [OpenGLの仕組み（適当に解説）](#OpenGLの仕組み適当に解説)  
   * [VBOとVAO](#VBOとVAO)  
   * [頂点シェーダ（Vertex Shader)](#頂点シェーダVertex-Shader)  
   * [フラグメントシェーダ（Fragment Shader)](#フラグメントシェーダFragment-Shader)  
   * [ラスタライズ](#ラスタライズ)  
   * [スワップバッファ](#スワップバッファ)  
   * [深度バッファ](#深度バッファ)  
   * [OpenGLの座標の考え方](#OpenGLの座標の考え方)  
   * [ViewPortとシザー矩形](#ViewPortとシザー矩形)  
   * [GLSLとは](#GLSLとは)
* [プログラム](#プログラム)
* [実行結果](#実行結果)
  
  
### OpenGLとは

> クロノス・グループ (英: Khronos Group) が策定している、グラフィックスハードウェア向けの2次元/3次元コンピュータグラフィックスライブラリである  
> (<https://ja.wikipedia.org/wiki/OpenGL>より）

  
「3次元コンピュータグラフィックスライブラリ」としては、OpenGLのほかにも、**DirectX**や**Vulkan**などもありますが、このシリーズではOpenGLの使い方的なものを書いていきます。

DirectXやVulkanもチャレンジしてみようと思ったのですが、覚えることもコード量もとても多くてOpenGLを使うことにしました。  
ちょっと触った感覚だと、同じ動作をさせるのに必要なコード量は**OpenGL＜DirectX＜Vulkan**という感じです。

DirectX（以降DX）はDX9とかDX11とかバージョンがあるのですが、DX12で

* コマンドリスト、コマンドキュー
* ディスクリプタヒープ
* ルートシグネチャ
* ディスクリプタ、ディスクリプタヒープ
* フェンス/バリア

......など多くの機能が追加され、「うわぁぁぁぁぁ！！」ってなりました。紛らわしいものも多いし、三角形一つを表示するのも大変そうだったのでDirectX系はあきらめました。  
  
  
### OpenGLの仕組み（適当に解説）

では、OpenGLはどのように動作しているのか、ってのを簡潔に書いていきます。

とりあえず全体をまとめるとこんな感じです

  
![f:id:pythonjacascript:20200521015912j:plain](/images/ppythonjacascript2020052120200521015912.jpg "f:id:pythonjacascript:20200521015912j:plain")

画像や図形の頂点データや色データ、テクスチャデータなどをOpenGLにぶっこむと、色々あって描画してくれるよ、ってことなのですが、中身がどうなってんの？って話です。順番に見ていきます。  
  
  
#### VBOとVAO

**頂点バッファオブジェクト（VBO）**は、頂点配列データをVRAM（GPUのメモリ）に格納するためのバッファです。1種類のデータに対して1つのVBOを作成します。  
例えば、三角形を描画するには頂点座標を格納したVBOと頂点の色情報を格納したVBOの2つが必要です。他にも、頂点のIndex情報（頂点同士のつながり方）を表したVBOやテクスチャのUV座標を表したVBOがあります。

  
**Vertex Array Object (VAO)** とは、VBOをまとめる**ディスクリプタ―（Descriptor）**です。「ディスクリプタ―」とは、「GPUに送ったデータをシェーダーで使用するための仕様書」のようなものです。（使用するだけに仕様書...）  
  
  
#### 頂点シェーダ（Vertex Shader)

GPUが描画データを受け取った後、最初に実行されるのが**頂点シェーダ**です。頂点シェーダ―はC++言語などではなく、OpenGL専用のGLSLという言語で書きます。

  
頂点シェーダの役割は、各頂点の座標や色、テクスチャのUV座標を計算することです。つまり、頂点シェーダは頂点の数だけ呼び出され、それぞれの頂点に対して同じ計算を行います。

  
例えば、

out vec4 gl_Position; //最終的に描画される座標. 
in vec2 position; //VBOに入力した座標　　"in vec2"は二次元(x, y)の入力変数であることを表す
uniform mat4 RotationMat; //回転行列
void main(void){
  gl_Position = RotationMat * vec4(position, 0.0, 1.0); //回転行列をかけて物体を移動させる
}

のように書くと、RotationMat（4x4の回転行列）をうまく設定することで物体を移動させたり変形させたりできます。  
  
  
#### フラグメントシェーダ（Fragment Shader)

一方、**フラグメントシェーダ**はスクリーン（正確にはViewport）の各画素について実行され、それぞれのピクセルと何の色で塗るかを計算します。入力値として与えられるのは、テクスチャ画像とそのUV座標、または頂点シェーダで設定した図形の色などです。

例えば、

#version 120
out vec4 gl_FragColor;
void main(void){
  gl_FragColor = vec4(1.0, 0.0, 0.0, 1.0);//(Red=1.0, Green=0.0, Blue=0.0, alpha=1.0(不透明）)
}

と書くと、全ての頂点を赤色で塗るシェーダーができあがります。  
  
  
#### ラスタライズ

頂点シェーダとフラグメントシェーダの間に実行される処理として「**ラスタライズ**」というものがあります。

「ラスタライズ」とは、頂点シェーダから渡された頂点データをもとに、それぞれの物体をスクリーン上のどのピクセルに描画すればいいのかを計算する工程のことです。  
  
  
#### スワップバッファ

VRAM（GPUのメモリ）には、現在スクリーンに描画されている画像データ（へのポインタ）が格納されています。そして、何かをレンダリングする時には現在のスクリーンの画像データに直接描画することで、画面の更新ができます。

...しかし、このように直接画面の更新を行うと画面の「ちらつき」が起こる場合があります。若しくは「tearing」（モニタは画面を上から下に再描画していくので、画面の上半分は新しい画像、画面の下半分は古い画像というように画面が2つに分割されること）がおこります。

  
これを回避するため、OpenGLには「**スワップバッファー（SwapBuffer）**」（DirectXではSwapCharin）という機能があります。  
「SwapBuffer」を実現するには、「**フロントバッファ**」と「バックバッファ」の二つのバッファが必要になります。  
「フロントバッファ」には現在モニターに表示されている画像が格納されており、「バックバッファ」には描画用の画像が格納されています。

SwapBufferとは、モニタに直接レンダリングするのではなく、「バックバッファー」と呼ばれる別の画像データに一旦描画し、描画が終わったら「バックバッファ」と「フロントバッファ」を入れ替える（Swap）するという手法です。  
  
  
#### 深度バッファ

深度バッファ（Depth buffer)はポリゴンの前後関係（ｚ座標）のデータを格納しています。

深度バッファを使わない場合、OpenGLは後に描画されたものの方が手前にある様に表示されます。

![f:id:pythonjacascript:20200517150436j:plain](/images/ppythonjacascript2020051720200517150436.jpg "f:id:pythonjacascript:20200517150436j:plain")
  
  
しかし、深度バッファを使った場合、「赤ポリゴンよりも青ポリゴンの方が手前にある」という事を認識して、前後関係が正しく描画されます。  
![f:id:pythonjacascript:20200517150450j:plain](/images/ppythonjacascript2020051720200517150450.jpg "f:id:pythonjacascript:20200517150450j:plain")
  
  
深度バッファを有効化/無効化するには

glEnable(GL_DEPTH_TEST); // 有効化
glDisable(GL_DEPTH_TEST);// 無効化

で行います。

また、深度バッファを毎度の描画ループでクリアする必要があります

glClear(GL_DEPTH_BUFFER_BIT);

#### OpenGLの座標の考え方

OpenGL（というか3Dグラフィック系のライブラリ）の画面の座標の考え方はWin32 APIなどの通常のものとは別で、下の画像のように考えます。  
![f:id:pythonjacascript:20200517150554j:plain](/images/ppythonjacascript2020051720200517150554.jpg "f:id:pythonjacascript:20200517150554j:plain")
  
  
ウィンドウの中央が原点で、Windowの端が-1.0または1.0となります。  
なので、ポリゴンの位置は下の様にfloat（GLfloat）で指定します。

GLfloat points[] = { 0.5f, 0.5f, 0.0f,
			0.5f, -0.5f, 0.0f,
			-0.5f, -0.5f, 0.0f };

この独特のOpenGL座標から通常のピクセル座標に変換するには、頂点シェーダで行列計算を行うことで解決できます。  
  
  
#### ViewPortとシザー矩形

ビューポートとシザー矩形はどちらもウィンドウ上のどの部分に描画するかを設定します。

例えば、幅がwidth\[px\]、高さがheight\[px\]のウィンドウを考えます。このとき、

// ビューポート（描画する矩形領域）の設定
// ウィンドウの左下ｘ,y座標、右上x,y座標 の順番で指定
// OpenGLでは左下が原点にある
glViewPort(0, 0, width/2, height/2); 

と書くと、画面を4分割した内の左下の部分にポリゴンが描画されます。  
![f:id:pythonjacascript:20200521015438j:plain](/images/ppythonjacascript2020052120200521015438.jpg "f:id:pythonjacascript:20200521015438j:plain")  
  
  
// シザー矩形（描画しない領域）の設定
// ウィンドウの左下ｘ,y座標、右上x,y座標 の順番で指定
// OpenGLでは左下が原点にある
glScissor(10, 10, 100, 100); 

と書くと、ViewPort全体に描画は行われますが、そのあと左下頂点が（10,10）、右上頂点が（100, 100) の長方形の部分だけクリッピングされてそれ以外の描画領域は無効になります  
![f:id:pythonjacascript:20200521015424j:plain](/images/ppythonjacascript2020052120200521015424.jpg "f:id:pythonjacascript:20200521015424j:plain")  
  
シザー矩形を使うには、

glEnable(GL_SCISSOR_TEST);

を描画前に実行してください

因みに、先ほどOpenGLの座標系は「-1.0～1.0の小数点で表す」と言いましたが、glViewPort()関数とglScissor()関数での座標の考え方は左下原点のピクセル座標です、  
![f:id:pythonjacascript:20200517150607j:plain](/images/ppythonjacascript2020051720200517150607.jpg "f:id:pythonjacascript:20200517150607j:plain")
  
  
#### GLSLとは

OpenGLのシェーダープログラム言語です。頂点シェーダ―もフラグメントシェーダ―もGLSLで書きます。  
OpenGLとのバージョン対応表は以下のようになっています。

尚、自分のPCがどのOpenGLのバージョンに対応しているかは[ここ](https://www.intel.co.jp/content/www/jp/ja/support/articles/000005524/graphics-drivers.html)から確認できます。

| OpenGL version | GLSL version | GLSL string                   |
| -------------- | ------------ | ----------------------------- |
| 2.0            | 110          | "#version 110"                |
| 2.1            | 120          | "#version 120"                |
| 3.0            | 130          | "#version 130"                |
| 3.1            | 140          | "#version 140"                |
| 3.2            | 150          | "#version 150"                |
| 3.3            | 330          | "#version 330 core"           |
| 4.0            | 400          | "#version 400 core"           |
| 4.1            | 410          | "#version 410 core"           |
| 4.2            | 420          | "#version 410 core"           |
| 4.3            | 430          | "#version 430 core"           |
| ES 2.0         | 100          | "#version 100" = WebGL 1.0    |
| ES 3.0         | 300          | "#version 300 es" = WebGL 2.0 |

[imgui/imgui\_impl\_opengl3.cpp at master · ocornut/imgui · GitHub](https://github.com/ocornut/imgui/blob/master/examples/imgui%5Fimpl%5Fopengl3.cpp)より  
  
  
### プログラム

簡単な、ただ三角形を表示するだけのプログラムです

// hello_world.cpp

#include <GL/glew.h> //今回はglewを使います。gladでも動くはず...？（TODO:動作確認）
#include <GLFW/glfw3.h> 
//#include <GLFW/glfw3.h>  //glfw3でも動くよ～～
#include <stdio.h>


GLint makeShader() {
	// 頂点シェーダ作成（vertexshader.shaderみたいに別ファイルを作って読み込む方法もある)
	// シェーダーの言語はGLSLというらしい
	const char* vertex_shader =
		"#version 400\n" //GLSLのバージョン
		"in vec3 vp;" //入力
		"void main() {" //拡張店ごとにmain()関数が実行される
		"  gl_Position = vec4(vp, 1.0);" //gl_Positionは4変数ベクトルで、（x,y,z,w）となる（wはとりあえず1にしておけばいい。それ以外の場合は、x = x/wのようになる）
		"}";
	
	//フラグメントシェーダ作成
	const char* fragment_shader =
		"#version 400\n"
		"out vec4 frag_colour;" //出力値はその場所の色（R,G,B,A)を表す。それぞれ　0.0（透明or色なし）～1.0（不透明or色あり）の小数点で設定する
		"void main() {"
		"  frag_colour = vec4(0.5, 1.0, 0.5, 1.0);" //
		"}";

	GLuint vs, fs;
	GLuint shader_programme;


	//上のシェーダープログラムをコンパイルする。
	vs = glCreateShader(GL_VERTEX_SHADER);
	glShaderSource(vs, 1, &vertex_shader, NULL);
	glCompileShader(vs);
	fs = glCreateShader(GL_FRAGMENT_SHADER);
	glShaderSource(fs, 1, &fragment_shader, NULL);
	glCompileShader(fs);
	shader_programme = glCreateProgram();
	glAttachShader(shader_programme, fs);
	glAttachShader(shader_programme, vs);
	glLinkProgram(shader_programme);

	return shader_programme;
}

int main() {
	GLFWwindow *window = NULL;
	GLuint vao;
	GLuint vbo;
	

	GLfloat points[] = { 0.5f, 0.5f, 0.3f, 0.5f, -0.5f, 0.0f, -0.5f, -0.5f, 0.0f };


	// GLFW helper libraryを使ってOpenGLを初期化！
	if (!glfwInit()) {
		fprintf(stderr, "ERROR: could not start GLFW3\n");
		return 1;
	}

	glfwWindowHint(GLFW_CONTEXT_VERSION_MAJOR, 2);
	glfwWindowHint(GLFW_CONTEXT_VERSION_MINOR, 1);

	// windowを作成 glfwCreateWindowの第一、第二引数はWindowの最初の大きさ
	window = glfwCreateWindow(640, 480, "Hello Triangle", NULL, NULL);
	if (!window) {
		fprintf(stderr, "ERROR: could not open window with GLFW3\n");
		glfwTerminate(); //OpenGLを終了する
		return 1;
	}
	
	// 今からこのウィンドウについての設定を行うよ～みたいな意味
	glfwMakeContextCurrent(window);

	//GLEWまたはGLADを初期化する（Glewを使うかGladを使うかでの変更点はここだけ）

	// GLEWの初期化を行う（GLADを使う場合は下の二行はコメントアウト）
	glewExperimental = GL_TRUE;
	glewInit();

	// GLADを使う場合は下の二行をコメントアウトしてね
	// gladLoadGLLoader((GLADloadproc)glfwGetProcAddress);
	// glfwSwapInterval(1);

	// 今使っているPCのOpenGLのレンダラーとかバージョンを得る。ここは別になくても動く。
	const GLubyte *renderer;
	const GLubyte *version;
	renderer = glGetString(GL_RENDERER);
	version = glGetString(GL_VERSION);
	printf("Renderer: %s\n", renderer);
	printf("OpenGL version supported %s\n", version);


	glEnable(GL_DEPTH_TEST); // Viewerに近い部分を表示する（描画の順番ではなくZ座標を考慮する）
	glDepthFunc(GL_LESS);


	// 頂点バッファオブジェクト（Vertex Buffer Object）作成
	// VBOとは頂点データや色データ、indexデータなどをGPUに伝えるための道？みたいなもの（？）
	glGenBuffers(1, &vbo);
	glBindBuffer(GL_ARRAY_BUFFER, vbo);
	glBufferData(GL_ARRAY_BUFFER, 9 * sizeof(GLfloat), points, GL_STATIC_DRAW); //配列pointsの値をGPUに伝える


	//  頂点配列オブジェクト（Vertex Array Object）作成
	// VAOは、VBOから頂点シェーダへの入力変数としてどのデータを使用するかを定義するDescriptorです。
	glGenVertexArrays(1, &vao);
	glBindVertexArray(vao);
	glEnableVertexAttribArray(0);
	glBindBuffer(GL_ARRAY_BUFFER, vbo);
	glVertexAttribPointer(0, 3, GL_FLOAT, GL_FALSE, 0, NULL);


	GLint shader = makeShader();
	while (!glfwWindowShouldClose(window)) {
		glClear(GL_COLOR_BUFFER_BIT | GL_DEPTH_BUFFER_BIT); // Depth bufferをクリアする
		glUseProgram(shader);
		glBindVertexArray(vao);

		// 現在選択されているシェーダーで、VAO上のオブジェクトを描画する
		// VBOに格納されたpointsデータの0番目から描画し、3頂点分だけ描画する
		glDrawArrays(GL_TRIANGLES, 0, 3);

		glfwPollEvents(); //キーボード入力などのイベント情報をupdateする
		glfwSwapBuffers(window); //glDrawArraysで書いたものをWindowに表示
	}

	glfwTerminate(); //Opengl終了
	return 0;
}

### 実行結果

↑のプログラムを実行すると、下のような三角形が表示されるはずです。  
![f:id:pythonjacascript:20200517150926j:plain](/images/ppythonjacascript2020051720200517150926.jpg "f:id:pythonjacascript:20200517150926j:plain")