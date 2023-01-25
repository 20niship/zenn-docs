---
title: "【C++/OpenGL】NO.2 プログラムの書き方編➀"
emoji: ""
type: ""
topics: []
published: false
---

前回のプログラム（緑色の三角形を表示するやつ）の解説その1です。  
プログラム全文は前回の記事を見てください。  
[shizenkarasuzon.hatenablog.com](https://shizenkarasuzon.hatenablog.com/entry/2020/05/21/020650)  

OpenGLでポリゴンを表示するには以下のような手順が必要です

* [GLFWとGLEW（またはGLAD)の初期設定](#GLFWとGLEWまたはGLADの初期設定)
* [Windowの生成](#Windowの生成)  
   * [大きさを設定](#大きさを設定)  
   * [表示位置を設定](#表示位置を設定)  
   * [タイトルを設定](#タイトルを設定)  
   * [WIndowアイコンを設定](#WIndowアイコンを設定)
* [Windowを終了する](#Windowを終了する)
* [描画方法の設定](#描画方法の設定)
* [シェーダー作成](#シェーダー作成)  
   * [1\. シェーダーオブジェクトを作成](#1-シェーダーオブジェクトを作成)  
   * [2.シェーダーのGLSLプログラムを読み込む](#2シェーダーのGLSLプログラムを読み込む)  
   * [3.シェーダーをコンパイル](#3シェーダーをコンパイル)  
   * [4\. シェーダープログラムを作成](#4-シェーダープログラムを作成)  
   * [5\. シェーダーを登録](#5-シェーダーを登録)  
   * [6\. シェーダープログラムを有効化](#6-シェーダープログラムを有効化)
* [VBO、VAOの設定](#VBOVAOの設定)  
   * [VAOの設定](#VAOの設定)  
   * [VBOの設定](#VBOの設定)  
         * [glGenBuffers ― VBO作成](#glGenBuffers--VBO作成)  
         * [glBindBuffer― VBOをbind](#glBindBuffer-VBOをbind)  
         * [glBufferData ― データを格納](#glBufferData--データを格納)  
         * [glVertexAttribPointer ― VBOを頂点シェーダで利用するための準備](#glVertexAttribPointer--VBOを頂点シェーダで利用するための準備)
* [描画](#描画)  
   * [glfwMakeContextCurrent ― 描画するウィンドウを指定](#glfwMakeContextCurrent--描画するウィンドウを指定)  
   * [glClear― 初期化](#glClear-初期化)  
   * [VAO、テクスチャなどを割り当てる](#VAOテクスチャなどを割り当てる)  
   * [glDrawArrays、glDrawElements ― 描画！](#glDrawArraysglDrawElements--描画)  
   * [補足：ポリゴンだけ表示](#補足ポリゴンだけ表示)  
   * [glfwSwapBuffers ― スワップバッファ](#glfwSwapBuffers--スワップバッファ)
* [イベント処理](#イベント処理)
* [終了処理](#終了処理)
  
  
### GLFWとGLEW（またはGLAD)の初期設定

何よりもまず、OpeｎGLを初期化する必要があります。  
OpenGLのライブラリの組み合わせとして「GLFWとGLAD」または「GLFWとGLEW」等がありますが、以下の様に初期化を行います

// GLFW helper libraryを使ってOpenGLを初期化！
if (!glfwInit()) {
  fprintf(stderr, "ERROR: could not start GLFW3\n");  return 1;
}

glfwWindowHint(GLFW_CONTEXT_VERSION_MAJOR, 2);
glfwWindowHint(GLFW_CONTEXT_VERSION_MINOR, 1);

glfwWindowHint関数では、OoenGLのヴァージョンを指定します。  
自分のPCにどのバージョンのOpenGLが対応しているのかは、[ここ](https://www.intel.co.jp/content/www/jp/ja/support/articles/000005524/graphics.html)を見てください

OpenGLのバージョンとGLSLのバージョンの関係は、以下の表のようになっています

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
  
GLEWの初期化を行います

// GLEWの初期化を行う
glewExperimental = GL_TRUE;
glewInit();

  
GLADの初期化を行います

// GLADを初期化する
gladLoadGLLoader((GLADloadproc)glfwGetProcAddress);
glfwSwapInterval(1);
  
  
### Windowの生成

GLFWを使ってWindowを作成します

GLFWwindow *window = glfwCreateWindow(640, 480, "Hello Triangle", NULL, NULL);
if (!window) {
    fprintf(stderr, "ERROR: could not open window with GLFW3\n");
    glfwTerminate(); //OpenGLを終了する
    return 1;
}

Windowを作成するには、glfwCreateWindow関数を使用します

* 第1引数 = ウィンドウの横幅
* 第2引数 = ウィンドウの高さ
* 第3引数 = ウィンドウ名（ウィンドウの上のバーに表示される
* 第4引数 = フルスクリーンでなければNULL
* 第5引数 = 他のウィンドウとリソース共有、しないならNULL

成功するとWindowのインスタンスハンドルが返されます

フルスクリーンのWindowを作成するには、以下のようなプログラムを組みます

GLFWwindow* window = glfwCreateWindow(640, 480, "My Title", glfwGetPrimaryMonitor(), NULL);

（参考＝[GLFW: Window guide](https://www.glfw.org/docs/latest/window.html#window%5Ffull%5Fscreen)）  
  
  
##### 大きさを設定

Windowの大きさを取得するにはglfwGetFramebufferSize関数またはglfwGetWindowSize関数を使います

int width, height;
glfwGetFramebufferSize(window, &width, &height);
glfwGetWindowSize(window, &width, &height);

作成したWindowの大きさを変更するにはreshape関数を使います

reshape(window, 1920, 1080);
  
  
##### 表示位置を設定

Windowの場所を移動させるにはglfwSetWindowPos関数を使います

glfwSetWindowPos(window, 0, 0); //Windowを画面左上に移動

##### タイトルを設定

ソースファイルがUTF-8でエンコードされていれば、Unicode文字を使うこともできます

glfwSetWindowTitle(window, "エヴァンゲリオン");
  
  
##### WIndowアイコンを設定

GLFWimage images[2];
images[0] = load_icon("my_icon.png");
images[1] = load_icon("my_icon_small.png");
 
glfwSetWindowIcon(window, 2, images);

アイコンファイルの条件として

* 画像データは32bit
* RGBA方式
* 各チャンネル8ビット
* 左上からデータが始まっていること

があるようです。

デフォルトのアイコンに戻すには、

glfwSetWindowIcon(window, 0, NULL);

を実行します  
  
  
#### Windowを終了する

Windowを閉じるには

glfwSetWindowShouldClose(window, GLFW_TRUE);

と書きます。すると、メインループのループ条件である

!glfwWindowShouldClose(window)

がFalse（つまり、glfwWindowShouldClose関数の戻り値がTrue）になって、Windowが終了します

glfwWindowShouldClose関数の戻り値がTrueになるのは、他にもWindow右上のバツ印が押されたときなどがあります  
  
  
### 描画方法の設定

glEnable関数でOpenGLの機能を有効化し、glDisable関数でOpenGLの機能を無効化します

glEnable(GL_DEPTH_TEST); // 深度バッファを有効化する
glDepthFunc(GL_LESS);

glDisable(GL_DEPTH_TEST); // 深度バッファを無効化する

  
深度バッファ以外にも、引数として指定できるのは主に以下のようなものがあります

| 引数の名前            | 説明                                                            |
| ---------------- | ------------------------------------------------------------- |
| GL\_ALPHA\_TEST  | alpha testを行う                                                 |
| GL\_DEPTH\_TEST  | 深度バッファを利用する                                                   |
| GL\_FOG          | fogを利用する（奥にある図形がかすんで見える）                                      |
| GL\_LINE\_SMOOTH | アンチエイジングを行う                                                   |
| GL\_LIGHTING     | GL\_LIGHTINGが有効化され、どの頂点シェーダも有効でない場合、現在のライトパラメータを使用して頂点の色を計算する |
| GL\_TEXTURE\_2D  | 描画に二次元テクスチャを使う                                                |
| GL\_BLEND        | glBlendFunc関数を使って色のblend方法をデフォルトから変更する                        |

  
他にもいろいろありますが、数が多いので省略します。  
全部の機能は→<https://www.khronos.org/registry/OpenGL-Refpages/gl2.1/xhtml/glEnable.xml>
  
  
### シェーダー作成

OpenGLでは、描画の為に頂点シェーダ（Vertex shader, VS)と、フラグメントシェーダー（Fragment shader, FS）の2つが必要です。それぞれについてはここで簡単に説明しています。  
シェーダーを作成するには、以下のような手順で行います

1. glCreateShader関数でシェーダーオブジェクトを作成
2. glShaderSource関数でシェーダーのGLSLプログラムを読み込む
3. glCompileShader関数でシェーダーをコンパイルする

これらは、頂点シェーダとフラグメントシェーダ両方に必要な手順です

そして、作成したシェーダをまとめるシェーダープログラムというものがあります。

1. glCreateProgram関数でシェーダープログラムを作成する
2. glAttachShader関数で、作成したシェーダープログラムに頂点シェーダとフラグメントシェーダを登録する
3. glLinkProgram関数を実行する
4. メインループの描画前に毎度、glUseProgramを呼び出してシェーダープログラムを有効化することで、描画時にそのシェーダーが実行される

#### 1\. シェーダーオブジェクトを作成

シェーダーを作成します

glCreateShader(GL_VERTEX_SHADER);//頂点シェーダを作成
glCreateShader(GL_FRAGMENT_SHADER);//フラグメントシェーダを作成
glCreateShader(GL_GEOMETRY_SHADER);//ジオメトリシェーダを作成

glCreateShaderの返り値は、作成したシェーダオブジェクトのハンドル（識別番号みたいなもの）を返します  
  
#### 2.シェーダーのGLSLプログラムを読み込む

void glShaderSource(GLuint shader, GLsizei count, const GLchar **string, const GLint *length)
//例：glShaderSource(vs, 1, &vertex_shader, NULL);

* 第1引数＝glCreateShaderで作成したシェーダーオブジェクト
* 第2引数＝第3引数に複数のGLSLプログラムを指定した場合、その個数を指定。普通は1
* 第3引数＝シェーダーのGLSLプログラム（の配列）
* 第4引数＝シェーダーのプログラム（GLSL)の文字数、NULLで自動設定？

#### 3.シェーダーをコンパイル

void glCompileShader(GLuint shader)

shader に指定したシェーダオブジェクトをコンパイルします.

コンパイルが成功したかどうかは以下のように確かめます

GLint success = 0;
glGetShaderiv(shader, GL_COMPILE_STATUS, &success);
if(success == GL_FALSE){
  std::cout << "シェーダー作成に失敗！！"
}

#### 4\. シェーダープログラムを作成

glCreateProgram関数でシェーダープログラムを作成します

GLuint shader_programme;
shader_programme = glCreateProgram();

#### 5\. シェーダーを登録

作成したシェーダープログラムに頂点シェーダとフラグメントシェーダを登録します

glAttachShader(shader_programme, fs);
glAttachShader(shader_programme, vs);
glLinkProgram(shader_programme);

#### 6\. シェーダープログラムを有効化

作成したシェーダープログラムを使って描画するには、以下の様にglUseProgram関数を実行します

glUseProgram(shader_programme);

glUseProgram関数はメインループの描画前に毎度呼び出す必要があるようです  
  
  
### VBO、VAOの設定

VBOは描画に必要なデータ（頂点座標、色など）を格納するバッファです（VBO＝Vertex Buffer Object）。一つの種類の情報に対して一つのVBOが必要になります。VBOの例としては、

* 頂点座標のVBO
* 頂点の色のVBO
* テクスチャのUV座標のVBO

等があります  
  
#### VAOの設定

一方、VAO（Vertex Array Object）はVBOをまとめる役割を持ちます。VBOを複数作るのに対してVAOは1つだけ作成します

  
VAOは glGenVertexArrays関数を使って作成します。  
作成したVAOを使用するには, glBindVertexArray関数を実行します。

void glGenVertexArrays(GLsizei n, GLuint *arrays)
/* 例：
GLuint vao;
glGenVertexArrays(1, &vao);
*/

n 個の頂点配列オブジェクトを作成し, そのオブジェクト名 (整数値で表される識別子) を arrays に指定された配列の要素に格納します.  
  
void glBindVertexArray(GLuint array)
//例：glBindVertexArray(vao);

arrays に指定されたオブジェクト名の頂点配列オブジェクトを結合します. 頂点配列オブジェクトは結合されている間使用できます. array が 0 の時は現在結合されている頂点配列オブジェクトの結合を解除します.  
  
  
#### VBOの設定

VBOは以下のような手順で設定を行います

1. glGenBuffers関数でVBO作成→glBindBuffer関数でbind
2. glBufferData関数で作成したVBOにデータを格納
3. glVertexAttribPointer関数でVBOをどのlayoutにするかを設定

##### glGenBuffers ― VBO作成

void glGenBuffers(GLsizei n, GLuint *buffers)
/*例：
GLuint vbo;
glGenBuffers(1, &vbo);
*/

n 個の頂点バッファオブジェクトを作成し, そのオブジェクト名 (整数値で表される識別子) を buffers に指定された配列の要素に格納します.  
  
  
##### glBindBuffer― VBOをbind

void glBindBuffer(GLenum target, GLuint buffer)
//例：glBindBuffer(GL_ARRAY_BUFFER, vbo);

bufferにしていされたVBOをtargetにbindします。  
targetの値は以下のようになります

| 値                               | 説明                                                                          |
| ------------------------------- | --------------------------------------------------------------------------- |
| GL\_ARRAY\_BUFFER               | 普通は大体これにしておけば良い。VBOに頂点情報（座標、色など）が格納されている時に使う。glDrawArrays()関数で描画する時に使用      |
| GL\_ELEMENT\_ARRAY\_BUFFER      | GL\_ARRAY\_BUFFERはglDrawArrays()関数を使う時に使用するのに対し、glDrawlements()関数で描画するときに使用 |
| GL\_TEXTURE\_BUFFER             | テクスチャデータを格納                                                                 |
| GL\_UNIFORM\_BUFFER             | Uniformデータを格納                                                               |
| GL\_COPY\_READ\_BUFFER          | Buffer copy source                                                          |
| GL\_COPY\_WRITE\_BUFFER         | Buffer copy destination                                                     |
| GL\_PIXEL\_PACK\_BUFFER         | ピクセルデータをPBOへ転送                                                              |
| GL\_PIXEL\_UNPACK\_BUFFER       | Pixel read target                                                           |
| GL\_TRANSFORM\_FEEDBACK\_BUFFER | Transform feedback buffer                                                   |

参考＝<https://www.khronos.org/registry/OpenGL-Refpages/gl4/html/glBindBuffer.xhtml>

  
##### glBufferData ― データを格納

次に、glBufferData関数でVBOのバッファにデータを格納します

void glBufferData(GLenum target, GLsizeiptr size, const GLvoid *data, GLenum usage)
//例：glBufferData(GL_ARRAY_BUFFER, sizeof (GLfloat) * 2 * vertices, position, GL_STATIC_DRAW);

* 第1引数=現在結合（bind）されているVBOの種類を指定します（glBindBuffer関数の第一引数と同じ値にします）
* 第2引数=データの大きさをbyteで指定します
* 第3引数=VBOに格納するデータ配列の最初の値へのポインタ
* 第4引数=VBOに格納されたデータの使用方法を指定します。大体の場合はGL\_STATIC\_DRAWを指定しておけばよいです（GL\_STATIC\_DRAW以外の場合は <http://marina.sys.wakayama-u.ac.jp/~tokoi/?date=20120909> がとても分かりやすいです）

##### glVertexAttribPointer ― VBOを頂点シェーダで利用するための準備

最後に、VBOの値を頂点シェーダに渡すためにglEnableVertexAttribArray関数とglVertexAttribPointer関数を実行します

頂点シェーダにVBOのデータを渡す際、locationという概念があります

例えば下の頂点シェーダのプログラムのように、1.頂点座標と2.色と3.uvという3つの情報を送りたいという状況を考えます。

// 頂点シェーダのGLSLプログラム
layout (location = 0) in vec3 position; //頂点座標
layout (location = 1) in vec4 color; //色
layout (location = 2) in vec2 uv; // UV座標

void main() {
  //ここで、position変数、color変数、uv変数が使える。
};

ここで、メインのソースファイルの中で以下の様に設定することで、頂点シェーダのlocation1には頂点座標の情報が転送され、location2には色が転送され、location3にはUV座標が転送されます

glEnableVertexAttribArray(0); //変数のlocation0を使う

// location＝0には頂点座標を転送する
glBindBuffer(GL_ARRAY_BUFFER, position_vbo);
glVertexAttribPointer(0, 3, GL_FLOAT, GL_FALSE, 0, NULL);


glEnableVertexAttribArray(1); //location1を使う
// layout1には色情報を転送する
glBindBuffer(GL_ARRAY_BUFFER, color_vbo);
glVertexAttribPointer(1, 4, GL_FLOAT, GL_FALSE, 0, NULL);


glEnableVertexAttribArray(2); //location2を使う
// location2にはUV座標を転送する
glBindBuffer(GL_ARRAY_BUFFER, uv_vbo);
glVertexAttribPointer(2, 2, GL_FLOAT, GL_FALSE, 0, NULL);

上の2つの関数の引数の説明です。

glVertexAttribPointer(GLuint index, GLint size, GLenum type, GLboolean normalized, GLsizei stride, const GLvoid *pointer);
//例：glVertexAttribPointer(0, 3, GL_FLOAT, GL_FALSE, 0, NULL);

頂点シェーダでlocation= indexの attribute 変数が受け取るデータを指定する

* 第1引数＝頂点シェーダでデータを受け取るlocationを指定する（0以上）（頂点シェーダの値と一致させる）
* 第2引数＝1個のデータの個数を指定する。1, 2, 3, 4 の値が指定できます（例： 二次元 (x, y)データの集合 なら 2）また、頂点シェーダでは個々の値に合わせてvec2 \~ vec4 のようにデータ型を指定します
* 第3引数＝データ型を指定します（GL\_BYTE, GL\_UNSIGNED\_BYTE, GL\_SHORT, GL\_UNSIGNED\_SHORT, GL\_INT, GL\_UNSIGNED\_INT, GL\_FLOAT, GL\_DOUBLE）
* 第4引数＝true→データが固定小数点型 (GL\_BYTE, GL\_UNSIGNED\_BYTE, GL\_SHORT, GL\_UNSIGNED\_SHORT, GL\_INT, GL\_UNSIGNED\_INT) のとき, その値をそのデータ型で表現可能な最大値で正規化。 GL\_FALSE なら正規化しない
* 第5引数＝ーつの配列の, 要素間の間隔を指定する（例えば、float型の2次元データのデータ州法の時は、sizeof(float) \* 2となります）。 0 ならデータは密に並んでいるものとする。
* 第6引数＝attribute 変数が受け取るデータが格納されている場所を指定します. バイト単位のオフセットをポインタにキャストして渡します.

void glEnableVertexAttribArray(GLuint index)

頂点シェーダでlocation= indexの attribute 変数を有効化します。

  
反対に無効化する場合はglDisableVertexAttribArray関数を使います。

void glDisableVertexAttribArray(GLuint index)
  
  
### 描画

描画のメインループの中で実行する関数を上げていくと

1. 【初期設定】描画を行うWindowを指定する（glfwMakeContextCurrent関数）
2. 【初期設定】前回の描画内容をクリアする（glClear関数）
3. 【初期設定】描画に使用するシェーダープログラムを指定（glUseProgram関数）
4. 【初期設定】描画に使用するVAOを指定（glBindVertexArray関数）
5. 【初期設定】描画に使用するテクスチャを指定する（glBindTexture関数、必要な時のみ）
6. 【描画】バックバッファに描画する（glDrawArrays関数またはglDrawElements関数）
7. 【描画】バックバッファに描画された内容をSwapBufferしてウィンドウに表示（glfwSwapBuffers関数）

描画関連の方から説明していきます  
  
  
##### glfwMakeContextCurrent ― 描画するウィンドウを指定

void glfwMakeContextCurrent(GLFWwindow* window);

この関数が呼び出された後は、windowを描画対象、設定対称とみなします。  
複数のウィンドウに対して描画する場合は描画するウィンドウを切り替えるときにglfwMakeContextCurrentを呼び出す必要があります  
  
  
##### glClear― 初期化

void glClear(GLbitfield mask);
//例：glClear(GL_COLOR_BUFFER_BIT | GL_DEPTH_BUFFER_BIT); // 画面を指定色で塗り潰し、深度バッファをクリアする

glfwMakeContextCurrent関数によって現在選択されているウィンドウの、バッファを初期化します。  
初期化するバッファの内容は以下の4つです

| 名称            | 引数として指定する値               | 説明                                                            |
| ------------- | ------------------------ | ------------------------------------------------------------- |
| カラーバッファ       | GL\_COLOR\_BUFFER\_BIT   | 画面の色を特定の色で塗りつぶします。塗りつぶす色はglClearColor関数で設定します                 |
| デプスバッファ       | GL\_DEPTH\_BUFFER\_BIT   | 深度バッファ（デプスバッファ）を初期化します。GL\_DEPTH\_TESTを有効にしている場合は初期化が必要です     |
| ステンシルバッファ     | GL\_STENCIL\_BUFFER\_BIT | ステンシルバッファをクリアします                                              |
| アキュムレーションバッファ | GL\_ACCUM\_BUFFER\_BIT   | アキュムレーションバッファとは、一度レンダリングした結果を格納しておくバッファをさします。モーションブラーなどで使用します |

  
ちなみに、カラーバッファを初期化するとき、何色で初期化されるのかはglClearColor関数で設定できます

void glClearColor(GLclampf R, GLclampf G, GLclampf B, GLclampf A)
// 例：glClearColor(1.0, 0.0, 0.0); //赤色で初期化

##### VAO、テクスチャなどを割り当てる

作成したシェーダープログラムやVAO、テクスチャを使って描画するには、以下の様にglUseProgram関数やglBindVertexArray関数を呼び出します

glUseProgram(shader_programme); //シェーダプログラムを使用
glBindVertexArray(vao);//VAOを使用
glBindTexture(GL_TEXTURE_2D, texture);　// 特定のテクスチャを描画に指定

glBindTexture関数に関しては、別の記事で説明します........  
  
  
##### glDrawArrays、glDrawElements ― 描画！

以上で、描画準備は整ったので、glDrawArrays関数またはglDrawElements関数を用いてポリゴンの描画を行います。

void glDrawArrays(GLenum mode, GLint first, GLsizei count);
//例：glDrawArrays(GL_TRIANGLES, 0, 3);

* 第1引数＝描画モードを選択します（下画像参照）
* 第2引数＝描画するデータの, 格納場所の先頭からの位置を指定する
* 第3引数＝描画するデータの（頂点の）数を指定する.

例えば、

glDrawArrays(GL_TRIANGLES, 0, 3);

と書いた場合のことを考えます。そして、頂点座標を格納したVBOがあるとして、それには2次元座標データ $(x,y)$ が格納されているとします。  
この時、頂点座標のVBOに格納されている配列のうち使われる範囲は、index=0から（3\[頂点\] × 2\[次元データ\]より）6までとなります  
  
void glDrawElements(GLenum mode, GLsizei count, GLenum type, const GLvoid * indices);
//例：glDrawElements(GL_TRIANGLES, 3, GL_UNSIGNED_INT, 0);

DrawElementsとDrawArraysの違いは頂点indexを使うかどうかです。glDrawArrays関数は使いませんがglDrawElements関数は使います。よって、DrawElementsの方が場合によってはデータの数を節約することができます

* 第1引数＝描画モードを選択します（下画像参照）
* 第2引数＝描画する頂点数を指定します
* 第3引数＝頂点indexデータのデータ型を指定します（GL\_UNSIGNED\_BYTE, GL\_UNSIGNED\_SHORT, GL\_UNSIGNED\_INT のどれか）
* 第4引数＝頂点indexデータのポインタを指定します

glDrawArrays関数やDrawElements関数の第1引数に指定する描画モードです。  
![f:id:pythonjacascript:20200521021505j:plain](/images/ppythonjacascript2020052120200521021505.jpg "f:id:pythonjacascript:20200521021505j:plain")  

##### 補足：ポリゴンだけ表示

glPolygonMode(GL_FRONT_AND_BACK, GL_FILL); //通常表示
glPolygonMode(GL_FRONT_AND_BACK,GL_LINE); //図形の外枠だけを表示

  
描画の前（glDrawArrays関数の前）に↑のどちらかを指定することで、描画方法を変更するという方法もあります。  
**glPolygonMode(GL\_FRONT\_AND\_BACK,GL\_LINE);**を呼び出した場合は、例えglDrawArrays()で内部を塗りつぶすような描画モード（GL\_TRIANGLESなど）を指定しても外枠しか描画されません。  
  
  
##### glfwSwapBuffers ― スワップバッファ

これで、バックバッファに図形が描画されました。しかし、ウィンドウ上にはまだ図形は表示されていません。glfwSwapBuffers関数を呼んで、バックバッファとフロントバッファを切り替えて、バックバッファの画像をウィンドウ上に表示する必要があります

void glfwSwapBuffers(GLFWwindow *window);
//例：glfwSwapBuffers(window);

引数にはスワップバッファする（つまり表示する）ウィンドウを指定します

スワップバッファについては[ここ](https://shizenkarasuzon.hatenablog.com/entry/2020/05/21/020650#%E3%82%B9%E3%83%AF%E3%83%83%E3%83%97%E3%83%90%E3%83%83%E3%83%95%E3%82%A1)を見てください。  
  
  
### イベント処理

上の描画に必要な関数たちに加えて、メインループの中で、イベント（キーボード入力やマウス入力）を処理する関数を呼び出す必要があります

イベント処理を行う関数は

* glfwPollEvents()関数
* glfwWaitEvents()関数

の2つがあります

void glfwWaitEvents(void)

文字通り、イベントが発生するまで処理を一時停止します。イベントが発生したら、それに対応するCallback関数を実行します  
  
void glfwPollEvents(void)

もしイベントが発生した場合、それに対応するCallback関数を呼び出します。イベントが発生しない場合は何もせずすぐに処理を返します。  
メインループでは通常glfwPollEvents関数が使われます。

なお、Callback関数については↓の記事を見てください  
[shizenkarasuzon.hatenablog.com](https://shizenkarasuzon.hatenablog.com/entry/2020/05/26/004752)  
  
  
### 終了処理

OpenGLを終了するにはglfwTerminate関数を呼び出します。

glfwTerminate(); //Opengl終了

Windowを閉じる操作などが行われると、glfwWindowShouldClose関数の戻り値がTrueになるので（詳細はここを参照）、glfwWindowShouldClose関数の戻り値をメインループの繰り返し条件に持ってきて、メインループを抜けたところでglfwTerminate関数を実行する、というプログラムの書き方が多いです。

  
なお、glfwTerminate関数で終了処理をする前に、バッファを開放しておく必要があります

void glDeleteBuffers(GLsizei n, const GLuint * buffers);
//例：glDeleteBuffers(1, &vbo);

* 第一引数＝削除するバッファの数を指定します
* 第二引数＝削除するバッファのポインタを指定します