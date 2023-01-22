---
title: "【C++/OpenGL】NO.5 透明度付きテクスチャの表示（GLbyte配列から生成）"
emoji: ""
type: ""
topics: []
published: false
---

* [この記事の内容](#この記事の内容)
* [プログラム](#プログラム)
* [実行結果](#実行結果)
* [解説](#解説)  
   * [アルファテクスチャを描画する時の設定](#アルファテクスチャを描画する時の設定)

  
### この記事の内容

前回の記事では、画像ファイル（rawファイル）を読み込んでテクスチャとして表示してみました。

前回の記事↓  
[shizenkarasuzon.hatenablog.com](https://shizenkarasuzon.hatenablog.com/entry/2020/06/05/120502)  

今回は、raw画像などを使わずに、GLbyteの二次元配列をテクスチャとして読み込み表示することを試してみます。  
ついでに、アルファ（透明度）の情報も入ったテクスチャを表示します。  
  
### プログラム

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


// (´・ω・)のテクスチャ作成
GLuint loadTexture(){
  // テクスチャIDの生成
  GLuint texID;
  glGenTextures(1, &texID);

  static GLubyte smiley[] = /* 16x16 smiley face */
  {
    0x03, 0xc0,  /*       ****       */
    0x0f, 0xf0, /*     ********     */
    0x1f, 0xf8, /*    **********    */
    0x3f, 0xfc, /*   ************   */
    0x73, 0xce, /*  ***  ****  ***  */
    0x73, 0xce, /*  ***  ****  ***  */
    0xff, 0xff, /* **************** */
    0xff, 0xff, /* **************** */
    0xff, 0xff, /* **************** */
    0xff, 0xff, /* **************** */
    0x6f, 0xf6, /*  ** ******** **  */
    0x77, 0xee, /*  *** ****** ***  */
    0x39, 0x9c, /*   ***  **  ***   */
    0x1e, 0x78, /*    ****  ****    */
    0x0f, 0xf0, /*     ********     */
    0x03, 0xc0, /*       ****       */
  };

  float index[] = { 0.0, 1.0 };

  glPixelStorei(GL_UNPACK_ALIGNMENT, 1);

  glPixelMapfv(GL_PIXEL_MAP_I_TO_R, 2, index);
  glPixelMapfv(GL_PIXEL_MAP_I_TO_G, 2, index);
  glPixelMapfv(GL_PIXEL_MAP_I_TO_B, 2, index);
  glPixelMapfv(GL_PIXEL_MAP_I_TO_A, 2, index);

  // テクスチャをGPUに転送
  glBindTexture(GL_TEXTURE_2D, texID);
  glTexImage2D(GL_TEXTURE_2D, 0, GL_RGBA, 16, 16, 0, GL_COLOR_INDEX, GL_BITMAP, smiley);

  // テクスチャを拡大縮小する時のフィルタリング方法を指定
  glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MAG_FILTER, GL_NEAREST);
  glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MIN_FILTER, GL_NEAREST);
  

  //ラッピング方法を指定
  glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_WRAP_S, GL_REPEAT);
  glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_WRAP_T, GL_REPEAT);

  // テクスチャのアンバインド
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

  window = glfwCreateWindow(480, 320, "Texture", NULL, NULL);
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


  //Alpha要素のあるテクスチャを描画する
  glEnable(GL_BLEND);
  glBlendFunc(GL_SRC_ALPHA, GL_ONE_MINUS_SRC_ALPHA);


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
  GLuint texID = loadTexture();

  // 追加：テクスチャ情報を送るuniform属性を設定する
  int textureLocation = glGetUniformLocation(shader, "texture");

  while (!glfwWindowShouldClose(window)) {
    glClearColor(1.0, 0.0, 0.0, 1.0);
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
  
  
### 実行結果

実行すると↓の写真のようになります。  
![f:id:pythonjacascript:20200609115905j:plain](/images/ppythonjacascript2020060920200609115905.jpg "f:id:pythonjacascript:20200609115905j:plain")  
  
  
### 解説

[前回のプログラム](https://shizenkarasuzon.hatenablog.com/entry/2020/06/05/120502)と比べて変わった部分はloadTexture関数の中身です。  
上のサンプルプログラムからloadTexture関数を抜き出してみると、

// (´・ω・)のテクスチャ作成
GLuint loadTexture(){
  // テクスチャIDの生成
  GLuint texID;
  glGenTextures(1, &texID);

  static GLubyte smiley[] = /* 16x16 smiley face */
  {
    0x03, 0xc0,  /*       ****       */
    0x0f, 0xf0, /*     ********     */
    0x1f, 0xf8, /*    **********    */
    0x3f, 0xfc, /*   ************   */
    0x73, 0xce, /*  ***  ****  ***  */
    0x73, 0xce, /*  ***  ****  ***  */
    0xff, 0xff, /* **************** */
    0xff, 0xff, /* **************** */
    0xff, 0xff, /* **************** */
    0xff, 0xff, /* **************** */
    0x6f, 0xf6, /*  ** ******** **  */
    0x77, 0xee, /*  *** ****** ***  */
    0x39, 0x9c, /*   ***  **  ***   */
    0x1e, 0x78, /*    ****  ****    */
    0x0f, 0xf0, /*     ********     */
    0x03, 0xc0, /*       ****       */
  };

  float index[] = { 0.0, 1.0 };

  glPixelStorei(GL_UNPACK_ALIGNMENT, 1);

  glPixelMapfv(GL_PIXEL_MAP_I_TO_R, 2, index);
  glPixelMapfv(GL_PIXEL_MAP_I_TO_G, 2, index);
  glPixelMapfv(GL_PIXEL_MAP_I_TO_B, 2, index);
  glPixelMapfv(GL_PIXEL_MAP_I_TO_A, 2, index);

  // テクスチャをGPUに転送
  glBindTexture(GL_TEXTURE_2D, texID);
  glTexImage2D(GL_TEXTURE_2D, 0, GL_RGBA, 16, 16, 0, GL_COLOR_INDEX, GL_BITMAP, smiley);

  // テクスチャを拡大縮小する時のフィルタリング方法を指定
  glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MAG_FILTER, GL_NEAREST);
  glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MIN_FILTER, GL_NEAREST);
  
  //ラッピング方法を指定
  glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_WRAP_S, GL_REPEAT);
  glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_WRAP_T, GL_REPEAT);

  // テクスチャのアンバインド
  glBindTexture(GL_TEXTURE_2D, 0);

  return texID;
}

  
となります。smileyの配列をテクスチャデータとして読み込んでいます。

テクスチャがドット絵の様に各ピクセルの境界がはっきりしたほうがかっこいい（？）と思ったので、拡大縮小の方法を**GL\_NEAREST**に設定しています。（詳しくは[ここ](https://shizenkarasuzon.hatenablog.com/entry/2020/06/05/120502#%E3%83%95%E3%82%A3%E3%83%AB%E3%82%BF)を見てください）

テクスチャに透明度の情報を与えるために、glTexImage2D関数の第三引数をGL\_RGBAに設定しています。（詳しくは[前回の記事](https://shizenkarasuzon.hatenablog.com/entry/2020/06/05/120502#%E3%83%86%E3%82%AF%E3%82%B9%E3%83%81%E3%83%A3%E3%81%AB%E7%94%BB%E5%83%8F%E3%82%92%E5%89%B2%E3%82%8A%E5%BD%93%E3%81%A6%E3%82%8B)を見てください。）  
ちなみに、glTexImage2D関数の第3引数と第4引数はテクスチャデータの解像度（縦×横）を表しています。

  
それ以外は前回のプログラムと全く同じです。

  
ちなみに、背景を赤く塗る処理は、メインループの中の

glClearColor(1.0, 0.0, 0.0, 1.0);
glClear(GL_COLOR_BUFFER_BIT | GL_DEPTH_BUFFER_BIT);

で行っています。  
**glClearColor**関数で背景色（R, G, B, Aの順に0.0～1.0で指定)を設定します。  
  
  
#### アルファテクスチャを描画する時の設定

**変更点1.**  
テクスチャに透明度の情報を与えるには、glTexImage2D関数の第三引数を**GL\_RGBA**に設定します。（詳しくは[前回の記事](https://shizenkarasuzon.hatenablog.com/entry/2020/06/05/120502#%E3%83%86%E3%82%AF%E3%82%B9%E3%83%81%E3%83%A3%E3%81%AB%E7%94%BB%E5%83%8F%E3%82%92%E5%89%B2%E3%82%8A%E5%BD%93%E3%81%A6%E3%82%8B)を見てください。）

//例：
glTexImage2D(GL_TEXTURE_2D, 0, GL_RGBA, 512, 512, 0, GL_RGB, GL_UNSIGNED_BYTE, textureBuffer);

**変更点2.**  
描画を開始する前に

  //Alpha要素のあるテクスチャを描画する
  glEnable(GL_BLEND);
  glBlendFunc(GL_SRC_ALPHA, GL_ONE_MINUS_SRC_ALPHA);

を呼び出してください。  
そうすることで、テクスチャに格納されているアルファ情報が有効化され、穴が開いたテクスチャや、テクスチャを張り付けたポリゴンとは違う形状のテクスチャを表現することができます。