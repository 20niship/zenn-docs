---
title: " 【C++/OpenGL】NO.5 テクスチャの表示（RAWファイルから）"
emoji: ""
type: ""
topics: []
published: false
---

* [プログラム](#プログラム)
* [.rawファイル作成](#rawファイル作成)
* [実行結果](#実行結果)
* [以下、解説](#以下解説)
* [UV座標について](#UV座標について)
* [全体的な流れ](#全体的な流れ)
* [テクスチャの有効化](#テクスチャの有効化)
* [テクスチャの生成](#テクスチャの生成)  
   * [テクスチャの作成](#テクスチャの作成)  
   * [テクスチャに画像を割り当てる](#テクスチャに画像を割り当てる)  
   * [ラッピング](#ラッピング)  
   * [フィルタ](#フィルタ)  
         * [画像を拡大するときに使えるフィルタ](#画像を拡大するときに使えるフィルタ)  
         * [画像を縮小するときに使えるフィルタ](#画像を縮小するときに使えるフィルタ)  
   * [MIPMAP](#MIPMAP)
* [UV座標のVBOの作成](#UV座標のVBOの作成)
* [シェーダにテクスチャデータを渡す](#シェーダにテクスチャデータを渡す)
* [フラグメントシェーダの設定](#フラグメントシェーダの設定)
* [参考文献](#参考文献)

今回は、[第3回](https://shizenkarasuzon.hatenablog.com/entry/2020/05/26/001805)で描画したような四角形にテクスチャ（画像）を張り付けてみます  
  
### プログラム

[texture.cpp · GitHub](https://gist.github.com/tasonco/73a7291769278320c76928b1bd963cba)を大変参考にさせていただきました。ありがとうございます！

#include <GL/glew.h>
#include <GLFW/glfw3.h> 
#include <iostream>
#include <fstream>

GLint makeShader() {
  const char* vertex_shader =
    "#version 400\n"
    "layout(location = 0) in vec3 position;\n"
    "layout(location = 1) in vec2 vuv;\n"
    "out vec2 Flag_uv;\n"
    "void main(void) {\n"
       "Flag_uv = vuv;\n"
       "gl_Position =vec4(position, 1.0f);\n"
    "}\n";

  
  const char* fragment_shader =
    "#version 400\n"
    "in vec2 Flag_uv;"//頂点シェーダで計算された、テクスチャの残高
    "uniform sampler2D Texture;" //追加：テクスチャを入手
    "out vec4 outFragmentColor; \n"
    "void main(void) {\n"
      //texture2D関数→指定されたUV座標（Flag_uv）のテクスチャの色を返す関数
      "outFragmentColor = texture2D(Texture, Flag_uv); \n" 
    "}\n";
  

  GLuint vs, fs;
  GLuint shader_programme;

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


GLuint loadTexture(std::string filename){
  // テクスチャIDの生成
  GLuint texID;
  glGenTextures(1, &texID);

  // ファイルの読み込み
  std::ifstream fstr(filename, std::ios::binary);
  const size_t fileSize = static_cast<size_t>(fstr.seekg(0, fstr.end).tellg());
  fstr.seekg(0, fstr.beg);
  char* textureBuffer = new char[fileSize];
  fstr.read(textureBuffer, fileSize);

  // テクスチャをGPUに転送
  glPixelStorei(GL_UNPACK_ALIGNMENT, 1);
  glBindTexture(GL_TEXTURE_2D, texID);
  glTexImage2D(GL_TEXTURE_2D, 0, GL_RGB, 512, 512, 0, GL_RGB, GL_UNSIGNED_BYTE, textureBuffer);
  //glTexImage2D(GL_TEXTURE_2D, 0, GL_RGB, 256, 256, 0, GL_RGB, GL_UNSIGNED_BYTE, textureBuffer);


  // テクスチャを拡大縮小する時のフィルタリング方法を指定
  glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MAG_FILTER, GL_NEAREST);
  glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MIN_FILTER, GL_NEAREST);

  //ラッピング方法を指定
  glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_WRAP_S, GL_REPEAT);
  glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_WRAP_T, GL_REPEAT);

  // テクスチャのアンバインド
  delete[] textureBuffer;
  glBindTexture(GL_TEXTURE_2D, 0);

  return texID;
}

int main() {
  GLFWwindow *window = NULL;


  GLfloat points[] = { 0.5f, 0.5f, 0.0f,
             -0.5f, 0.5f, 0.0f,
            -0.5f, -0.5f,  0.0f,
            0.5f, -0.5f, 0.0f };


  GLfloat vertex_uv[] = { 1.0f, 0.0f,
              0.0f, 0.0f,
              0.0f, 1.0f,
              1.0f, 1.0f };

  if (!glfwInit()) {
    fprintf(stderr, "ERROR: could not start GLFW3\n");
    return 1;
  }

  glfwWindowHint(GLFW_CONTEXT_VERSION_MAJOR, 2);
  glfwWindowHint(GLFW_CONTEXT_VERSION_MINOR, 1);

  window = glfwCreateWindow(640, 480, "Texture", NULL, NULL);
  if (!window) {
    fprintf(stderr, "ERROR: could not open window with GLFW3\n");
    glfwTerminate();
    return 1;
  }
  glfwMakeContextCurrent(window);

  glewExperimental = GL_TRUE;
  glewInit();
  glEnable(GL_DEPTH_TEST); 
  glDepthFunc(GL_LESS);


  GLint shader = makeShader();

  GLuint vao, vertex_vbo, uv_vbo;
  glGenVertexArrays(1, &vao);
  glBindVertexArray(vao);

  glGenBuffers(1, &vertex_vbo); 
  glBindBuffer(GL_ARRAY_BUFFER, vertex_vbo); 
  glBufferData(GL_ARRAY_BUFFER, sizeof(points), points, GL_STATIC_DRAW);

  //追加：テクスチャのUV座標を格納するためのVBOを作成
  glGenBuffers(1, &uv_vbo); 
  glBindBuffer(GL_ARRAY_BUFFER, uv_vbo); 
  glBufferData(GL_ARRAY_BUFFER, sizeof(vertex_uv), vertex_uv, GL_STATIC_DRAW); 


  //追加：テクスチャ読み込み
  GLuint texID = loadTexture("test.raw");

  // 追加：テクスチャ情報を送るuniform属性を設定する
  int textureLocation = glGetUniformLocation(shader, "texture");

  while (!glfwWindowShouldClose(window)) {
    glClear(GL_COLOR_BUFFER_BIT | GL_DEPTH_BUFFER_BIT);
    glUseProgram(shader);

    glEnableVertexAttribArray(0);
    glBindBuffer(GL_ARRAY_BUFFER, vertex_vbo);
    glVertexAttribPointer(0, 3, GL_FLOAT, GL_FALSE, 0, (GLvoid*)0);

    //追加：作成したVBOを有効化し、描画に使用する
    glEnableVertexAttribArray(1);
    glBindBuffer(GL_ARRAY_BUFFER, uv_vbo);
    glVertexAttribPointer(1, 2, GL_FLOAT, GL_FALSE, 0, (GLvoid*)0);


    //追加：テクスチャのデータを送る
    glUniform1i(textureLocation, 0);
    glBindTexture(GL_TEXTURE_2D, texID);

    // ポリゴン（画像）描画
    glDrawArrays(GL_TRIANGLE_FAN, 0, 4);

    glDisableVertexAttribArray(0);
    glDisableVertexAttribArray(1);

    glfwPollEvents();
    glfwSwapBuffers(window); 
  }

  glfwTerminate();
  return 0;
}
  
  
### .rawファイル作成

上のプログラムを実行する前に、テクスチャファイル「"test.raw"」を作らなければなりません  
たとえば、下の画像を使うとします。  
![f:id:pythonjacascript:20200604164609j:plain](/images/ppythonjacascript2020060420200604164609.jpg "f:id:pythonjacascript:20200604164609j:plain")  
画像サイズが512x512ピクセルになっていれば、↑でなくてもなんでもいいです。

作成（ダウンロード）した画像ファイルをGIMPで開きます（「ファイル」→「開く/インポート」）。  
そして、「ファイル」→「名前を付けてエクスポート」で「test.raw」のように拡張子をrawにしてエクスポートします

![f:id:pythonjacascript:20200604165338j:plain](/images/ppythonjacascript2020060420200604165338.jpg "f:id:pythonjacascript:20200604165338j:plain")  
このような画面が出てきますが、↑のように設定します  
「エクスポート」を押すとraw画像としてエクスポートされます  
  
### 実行結果

上のプログラムをビルドしてできた実行ファイルと同じディレクトリに、先ほど作成したraw画像を置きます  
そして、プログラムを実行すると以下のようになります  
![f:id:pythonjacascript:20200604165610j:plain](/images/ppythonjacascript2020060420200604165610.jpg "f:id:pythonjacascript:20200604165610j:plain")  

### 以下、解説

以下、解説

### UV座標について

![f:id:pythonjacascript:20200616233246j:plain](/images/ppythonjacascript2020061620200616233246.jpg "f:id:pythonjacascript:20200616233246j:plain")  
ポリゴンにテクスチャを張り付けるとき、画像のどの部分をポリゴンに使用するかを表す方法が必要です。  
これを実現するために、OpenGLは**UV座標**という座標を用います。UVの値は上図の通り、テクスチャ上の座標を0.0～1.0で表し、描画するポリゴンの各頂点にUV座標を割り当てることで、テクスチャの張り付ける位置を指定しているのです。  
  
### 全体的な流れ

テクスチャ付きポリゴンを描画するには以下のような手順を踏む必要があります  
白文字はこれまでと同じ工程、黄色太文字が今回新しく必要な工程です

1. OpenGL初期化**（テクスチャ有効化）**
2. 頂点座標のVBO作成
3. **UV座標のVBO作成**
4. **【glGenTexture関数】テクスチャを生成**
5. **【glTexImage2D関数】テクスチャに画像データを格納**
6. **テクスチャのラッピングの設定**
7. （必要ならばMIPMAPの生成）
8. **テクスチャのフィルタの設定**
9. 【glUniform1i関数】フラグメントシェーダでテクスチャデータが使えるようにする。
10. 【glBindTexture関数など】で生成したテクスチャデータをGPUに転送
11. **フラグメントシェーダの記述変更**
  
  
### テクスチャの有効化

テクスチャを生成したり表示したりする前に、

glEnable(GL_TEXTURE_2D);

を実行してテクスチャを有効化する必要があります。  
  
### テクスチャの生成

#### テクスチャの作成

glGenTextures関数でテクスチャオブジェクトを作成することができます。それぞれのテクスチャを識別するIDのようなものがあり、IDを格納する変数へのポインタを第二引数に指定します。  
第一引数は作成するテクスチャの次元を表し、 **GL\_TEXTURE\_1D** または **GL\_TEXTURE\_2D** を指定します

// テクスチャIDの生成
GLuint texID;
glGenTextures(1, &texID);

  
ここで生成したテクスチャIDは、glBindTexture関数でそのIDを指定することで描画の時に使用するテクスチャを指定することができます。  
  
void glPixelStorei(GLenum pname, GLint param)
//例：
glPixelStorei(GL_UNPACK_ALIGNMENT, 1);

画像のメモリ上での格納状態を指定します。厳密にはメモリ内の各ピクセル行の開始位置に必要なアラインメントを指定します。

paramには1,2,4,8のいずれかを指定することができます。

| pname                 | 説明                                |
| --------------------- | --------------------------------- |
| GL\_UNPACK\_ALIGNMENT | クライアントメモリから画像データを読み取る時のアラインメントを指定 |
| GL\_PACK\_ALIGNMENT   | クライアントメモリに画像データを返すときのアラインメントを指定   |

例えば、各色1バイトで一ピクセルRGBAの計4バイトのデータが格納されている時は、paramに4を指定します。  
モノクロ画像（明るさ、透明度）の2つのデータしかない場合は1ピクセル当たり2バイトなのでparamは2です。  
  
  
void glBindTexture(GLenum target , GLuint texture);
//例：
glBindTexture(GL_TEXTURE_2D, texID);

テクスチャをバインドします。

| target  | テクスチャの次元を表す（**GL\_TEXTURE\_1D** または**GL\_TEXTURE\_2D** ） |
| ------- | -------------------------------------------------------- |
| texture | テクスチャIDを指定。0にすると、現在バインドされているテクスチャがアンバインドされる              |
  
  
#### テクスチャに画像を割り当てる

次に、作成したテクスチャオブジェクトに、実際に画像データを格納します。それには**glTexImage2D**関数を使います。

void glTexImage2D(GLenum target, GLint level, GLint internalFormat, GLsizei width, GLsizei height, GLint border, GLenum format, GLenum type, const GLvoid *pixels)
//例：
glTexImage2D(GL_TEXTURE_2D, 0, GL_RGB, 512, 512, 0, GL_RGB, GL_UNSIGNED_BYTE, textureBuffer);

| target         | 画像の次元を表す。**GL\_TEXTURE\_2D**のみ可                                                                                                                 |
| -------------- | ----------------------------------------------------------------------------------------------------------------------------------------------- |
| level          | MIPMAP を行う場合のテクスチャの解像度レベル（MIPMAPを使わない場合は0）                                                                                                      |
| internalFormat | テクスチャをどの形式で保持するか（**GL\_RGB**、 **GL\_RGBA**, **GL\_ALPHA**, **GL\_LUMINANCE**, など）                                                               |
| width          | テクスチャの横幅                                                                                                                                        |
| height         | テクスチャの高さ                                                                                                                                        |
| border         | 境界線の線幅（0で境界線無し）                                                                                                                                 |
| format         | 読み込むデータ（pixcels）のデータ形式（ **GL\_RGB ，GL\_RGBA, GL\_COLOR\_INDEX, GL\_RED, GL\_GREEN, GL\_BLUE, GL\_ALPHA, GL\_LUMINANCE, GL\_LUMINANCE\_ALPHA** ） |
| type           | 読み込む画像配列のデータ型（**GL\_BYTE, GL\_SHORT, GL\_UNSIGNED\_SHORT, GL\_INT, GL\_UNSIGNED\_INT, GL\_FLOAT, GL\_BITMAP**）                                  |
| pixcels        | 画像データへのポインタ                                                                                                                                     |
  
  
#### ラッピング

もし、UV座標のどちらかの値が1.0を超えている、または負の数に設定した場合、それは画像ファイルの外部の点を表すことになります。  
そこで、本来の画像の外側の部分を**ラッピング**して補完することができます。

ラッピング方法はいかの3種類です  
![f:id:pythonjacascript:20200604172349j:plain](/images/ppythonjacascript2020060420200604172349.jpg "f:id:pythonjacascript:20200604172349j:plain")  
（Lena画像をつかうとカオスになりますな....）

| 値                    | 説明                 | 画像サイズ     |
| -------------------- | ------------------ | --------- |
| GL\_REPEAT           | 画像が無限に敷き詰められる      | 2^n x 2^m |
| GL\_CLAMP\_TO\_EDGE  | エッジのピクセルの色が敷き詰められる | 制限なし      |
| GL\_MIRRORED\_REPEAT | 画像が反転しながら敷き詰められる   | 2^n x 2^m |

  
GL\_REPEATまたはGL\_MIRRORED\_REPEATを使う時には画像の幅と高さがどちらも2の累乗になっている必要があります。上のプログラムで「test.raw」の画像サイズが512\*512になっていたのはそういう理由です。

glTexImage2D関数を実行した後、**glTexParameteri**関数でラッピング方法を指定します

//ラッピング方法を指定
glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_WRAP_S, GL_REPEAT);　// 横方向
glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_WRAP_T, GL_REPEAT);    //縦方向

| 第二引数                 | 説明             |
| -------------------- | -------------- |
| GL\_TEXTURE\_WRAP\_S | 横方向のラッピング方法を指定 |
| GL\_TEXTURE\_WRAP\_T | 縦方向のラッピング方法を指定 |

  
実際に↑のプログラムのUV座標を

GLfloat vertex_uv[] = { 5.0f, -5.0f,
			-5.0f, -5.0f,
			-5.0f, 5.0f,
			5.0f, 5.0f };

にして実行してみると、どの様にラッピングされているのかわかると思います。  
  
#### フィルタ

画像を拡大/縮小して表示する時など、テクスチャの各ピクセルとディスプレイ（厳密にはフラグメントカラー？）が1対1に対応しないことがあります。  
例えば**UV値がテクスチャ上の特定のピクセルとその隣のピクセルのちょうど中間座標を示していた場合、そこの色はどのように表現するのか**、を決定するフィルターを指定する必要があります。

OpenGLでは、そのようなフィルタの種類には以下のようなものがあります  
  
##### 画像を拡大するときに使えるフィルタ

| GL\_NEAREST | 最も距離の近いピクセルの色を使用                |
| ----------- | ------------------------------- |
| GL\_LINEAR  | 周囲4ピクセルの色を配合する（配合割合はUV座標をもとに計算） |

  
GL\_NEARESTは拡大率が上がるとドット絵っぽくなるのに対し、GL\_LINEARは拡大するとボケていく、そんなイメージです。  
![f:id:pythonjacascript:20200609121846j:plain](/images/ppythonjacascript2020060920200609121846.jpg "f:id:pythonjacascript:20200609121846j:plain")  

##### 画像を縮小するときに使えるフィルタ

| 定数                           | 説明                                        | MIPMAP使用有無 |
| ---------------------------- | ----------------------------------------- | ---------- |
| GL\_NEAREST                  | 最も距離の近いピクセルの色を使用                          | 使用しない      |
| GL\_LINEAR                   | 周囲4ピクセルの色を配合する（配合割合はUV座標をもとに計算）           | 使用しない      |
| GL\_NEAREST\_MIPMAP\_NEAREST | 最も距離の近いピクセルの色を使用                          | 使用する       |
| GL\_LINEAR\_MIPMAP\_NEAREST  | 周囲4ピクセルの色を配合する                            | 使用する       |
| GL\_NEAREST\_MIPMAP\_LINEAR  | 2つのMIPMAPそれぞれで最も距離の近いピクセルの色を求め、その2色の平均を使用 | 使用する（2個）   |
| GL\_LINEAR\_MIPMAP\_LINEAR   | 2つのMIPMAPそれぞれで周囲4ピクセルの色を配合し、さらにその2色の平均を使用 | 使用する（2個）   |

  
※MIPMAPについては次の節で解説します

これらフィルタをテクスチャに適応するには、glTexImage2D関数を実行した後に**glTexParameteri**関数を使用します。

glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MAG_FILTER, GL_NEAREST); //拡大時に適応されるフィルタ
glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MIN_FILTER, GL_NEAREST); //縮小時に適応されるフィルタ

  
第3引数で↑の表のようなフィルタの種類を指定します。

| 第二引数                     | 説明               |
| ------------------------ | ---------------- |
| GL\_TEXTURE\_MAG\_FILTER | 拡大時に適応されるフィルタを指定 |
| GL\_TEXTURE\_MIN\_FILTER | 縮小時に適応されるフィルタを指定 |

#### MIPMAP

![f:id:pythonjacascript:20200604180607j:plain](/images/ppythonjacascript2020060420200604180607.jpg "f:id:pythonjacascript:20200604180607j:plain")  
テクスチャを縮小して表示するときに、あらかじめ ![1/2^n](https://chart.apis.google.com/chart?cht=tx&chl=1%2F2%5En)の縮小率のテクスチャを作成しておき、描画時に適切な縮小率のテクスチャを選択して描画する、という方法がある。  
このようなテクスチャをMIPMAPといいます。

MIPMAP作成はglGenerateMipmap関数が自動的に行ってくれる

glGenerateMipmap(GL_TEXTURE_2D);

### UV座標のVBOの作成

VBOの作成方法は[ここ](https://shizenkarasuzon.hatenablog.com/entry/2020/05/24/183340#VBO%E3%81%AE%E8%A8%AD%E5%AE%9A)で一応説明しているし、実際の例は[ここ](https://shizenkarasuzon.hatenablog.com/entry/2020/05/26/001805)にあるので省略します。

上のプログラムのうち、UV座標VBOの作成にかかわっている部分は

//追加：テクスチャのUV座標を格納するためのVBOを作成
glGenBuffers(1, &uv_vbo); 
glBindBuffer(GL_ARRAY_BUFFER, uv_vbo); 
glBufferData(GL_ARRAY_BUFFER, sizeof(vertex_uv), vertex_uv, GL_STATIC_DRAW); 

の所です。  
  
### シェーダにテクスチャデータを渡す

ここまででテクスチャを作成できました。あとは、テクスチャデータをフラグメントシェーダに渡して、シェーダがそれをもとに描画色を決定してくれればOKです。

では、フラグメントシェーダにテクスチャデータを送るにはどうするのかというと、こんな風（↓）にします。

// 追加：テクスチャ情報を送るuniform属性を設定する
int textureLocation = glGetUniformLocation(shader, "texture");

while(true){ //
    //追加：テクスチャのデータを送る
    glUniform1i(textureLocation, 0);
    glBindTexture(GL_TEXTURE_2D, texID);//texIDはglGenTextures関数で作成したテクスチャのID

    // ポリゴン（画像）描画
    glDrawArrays(GL_TRIANGLE_FAN, 0, 4);
}

まず、**glGetUniformLocation**関数でテクスチャデータをシェーダに送るためのバッファを作成します。そのバッファのIDがtextureLocation に返り値として格納されます。第二引数の「"texture"」とは、シェーダでテクスチャデータを読み込むときの変数名を指定しているのです。  
  
  
### フラグメントシェーダの設定

上の様にglGetUniformLocation関数を使うことで、フラグメントシェーダの"texture"変数に画像データが格納されていることがわかりました。このテクスチャデータをもとにフラグメントカラー（最終的なその場所のポリゴン色）を決定する必要があります。  
そこで、送られてきたテクスチャの、特定の座標での色をゲットしなければいけません。それには**texture2D**関数を使用します。

//フラグメントシェーダ
#version 400\n
in vec2 Flag_uv;//頂点シェーダで計算された、テクスチャの残高
uniform sampler2D Texture;//追加：テクスチャを入手
out vec4 outFragmentColor; 
void main(void) {
  //texture2D関数→指定されたUV座標（Flag_uv）のテクスチャの色を返す関数
  outFragmentColor = texture2D(Texture, Flag_uv);  
};

texture2D関数の第一引数にテクスチャデータ、第二引数に取得したいUV座標を指定すると、そのぶぶんの色（RGBA)が返ってきます。  
テクスチャ上のRGBデータを取得する場合は、

  
テクスチャ上の特定の色（RGBのうちどれか）を取得することもできます（↓）

texture2D(Texture, Flag_uv).r //Red取得
texture2D(Texture, Flag_uv).g //Green取得
texture2D(Texture, Flag_uv).b //Blue取得
texture2D(Texture, Flag_uv).a //透明度取得
  
  
### 参考文献

[texture.cpp · GitHub](https://gist.github.com/tasonco/73a7291769278320c76928b1bd963cba)

[Tutorial 5 : A Textured Cube](http://www.opengl-tutorial.org/beginners-tutorials/tutorial-5-a-textured-cube/)

[OpenGL - Textures](https://open.gl/textures)

[OpenGL ES2.0 入門 基礎編（テクスチャのパラメータ） - Qiita](https://qiita.com/kazoo/items/27d2c18d14dbb99bebf3)

[床井研究室 - 第２回 テクスチャの割り当て](http://marina.sys.wakayama-u.ac.jp/~tokoi/?date=20040914)

[Tutorial 5 : A Textured Cube](http://www.opengl-tutorial.org/beginners-tutorials/tutorial-5-a-textured-cube/)

[android - what do texture2D().r and texture2D().a mean? - Stack Overflow](https://stackoverflow.com/questions/47671934/what-do-texture2d-r-and-texture2d-a-mean)