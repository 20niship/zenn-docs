---
title: "【C++/OpenGL】NO.4 行列を使って、図形を移動・回転させ、ピクセル座標→同軸座標の変換も行う！"
emoji: ""
type: ""
topics: []
published: false
---

タイトルが長ぇよ

* [はじめに](#はじめに)
* [プログラム1（回転）](#プログラム1回転)
* [実行結果](#実行結果)
* [解説](#解説)
* [行列の考え方](#行列の考え方)  
   * [そのまま](#そのまま)  
   * [拡大縮小](#拡大縮小)  
   * [移動](#移動)  
   * [反転](#反転)  
   * [回転](#回転)  
   * [座標変換（左上原点ピクセル座標→中央原点同軸座標）](#座標変換左上原点ピクセル座標中央原点同軸座標)
* [プログラム2（ピクセル座標→同軸座標）](#プログラム2ピクセル座標同軸座標)
* [実行結果](#実行結果-1)

### はじめに

行列を使うことで、図形を回転させたり移動させたりできます。

例えば、![ A = (x, y, z, 1.0)](https://chart.apis.google.com/chart?cht=tx&chl=%20A%20%3D%20%28x%2C%20y%2C%20z%2C%201.0%29) という位置に点があるとします。

> 補足：  
> 通常、3次元で位置を表すには![(x,y,z)](https://chart.apis.google.com/chart?cht=tx&chl=%28x%2Cy%2Cz%29)という3実数を使いますが、OpenGlでは4つ目のスカラー量![w](https://chart.apis.google.com/chart?cht=tx&chl=w)が加わります。これは、![w \not=0](https://chart.apis.google.com/chart?cht=tx&chl=w%20%5Cnot%3D0)のとき、プログラム上の![(x,y,z,w)](https://chart.apis.google.com/chart?cht=tx&chl=%28x%2Cy%2Cz%2Cw%29)は、実際には![ (\dfrac{x}{w}, \dfrac{y}{w}, \dfrac{z}{w})](https://chart.apis.google.com/chart?cht=tx&chl=%20%28%5Cdfrac%7Bx%7D%7Bw%7D%2C%20%5Cdfrac%7By%7D%7Bw%7D%2C%20%5Cdfrac%7Bz%7D%7Bw%7D%29)を意味しています。このような4つのスカラーで表される座標を**同次座標**といいます
> 
>  
> ![w=0](https://chart.apis.google.com/chart?cht=tx&chl=w%3D0)の時は、プログラム上で![(x,y,z,w)](https://chart.apis.google.com/chart?cht=tx&chl=%28x%2Cy%2Cz%2Cw%29)と書くと、これは位置ではなく方向ベクトルを表すという意味で使うことがあります。![w=0](https://chart.apis.google.com/chart?cht=tx&chl=w%3D0)ということは、![ \dfrac{x}{w}, \dfrac{y}{w}, \dfrac{z}{w})](https://chart.apis.google.com/chart?cht=tx&chl=%20%5Cdfrac%7Bx%7D%7Bw%7D%2C%20%5Cdfrac%7By%7D%7Bw%7D%2C%20%5Cdfrac%7Bz%7D%7Bw%7D%29)が無限遠点を表現しているので、例えば、光源方向などを表すのに使います

これに、

![R = \left( \begin{array}{cccc}
      1.0 & 0 & 0 & 123 \\
      0 & 1.0 & 0 & 456 \\
     0 & 0 & 1.0 & 789 \\
      0 & 0 & 0 & 1.0 \\
    \end{array} \right)](https://chart.apis.google.com/chart?cht=tx&chl=R%20%3D%20%5Cleft%28%20%5Cbegin%7Barray%7D%7Bcccc%7D%0A%20%20%20%20%20%201.0%20%26%200%20%26%200%20%26%20123%20%5C%5C%0A%20%20%20%20%20%200%20%26%201.0%20%26%200%20%26%20456%20%5C%5C%0A%20%20%20%20%200%20%26%200%20%26%201.0%20%26%20789%20%5C%5C%0A%20%20%20%20%20%200%20%26%200%20%26%200%20%26%201.0%20%5C%5C%0A%20%20%20%20%5Cend%7Barray%7D%20%5Cright%29)

という4ｘ4の行列をかけることで、点Aを移動させることができます。移動後の点の位置ベクトルを![B](https://chart.apis.google.com/chart?cht=tx&chl=B)とすると、  
![B=RA](https://chart.apis.google.com/chart?cht=tx&chl=B%3DRA)  
となり、  
![B= (x+123, y+456, z+789, 1.0)](https://chart.apis.google.com/chart?cht=tx&chl=B%3D%20%28x%2B123%2C%20y%2B456%2C%20z%2B789%2C%201.0%29) の位置に描画されます。つまり、点Aは![ (123, 456, 789)](https://chart.apis.google.com/chart?cht=tx&chl=%20%28123%2C%20456%2C%20789%29)だけ平行移動します  
ポリゴン上のすべての頂点についてこのような変換を使うことで、ポリゴンを移動させたり回転させたりできます。

  
頂点シェーダで、上のような回転行列をかける処理を行うことで、描画する頂点座標全てを（つまりポリゴンを）一度に移動したり回転させたりするとができます  
  
  
### プログラム1（回転）

前回の四角形を描画するプログラムを紹介しました。  
[shizenkarasuzon.hatenablog.com](https://shizenkarasuzon.hatenablog.com/entry/2020/05/26/001805#%E3%83%97%E3%83%AD%E3%82%B0%E3%83%A9%E3%83%A0)  
ここでは、この四角形を上のような回転行列を使ってｚ軸中心に45°回転させてみます

#include <GL/glew.h>
#include <GLFW/glfw3.h>
#include <stdio.h>

int window_width = 640;
int window_heignt = 400;


GLint makeShader() {
  const char* vertex_shader =
    "#version 400\n"
    "layout(location = 0) in vec2 position;\n"
    "layout(location = 1) in vec3 color;\n"
    "uniform mat4 projectionMatrix;"
    "out vec3 outColor;\n"
    "void main(void) {\n"
      "outColor = color;\n"
      // ここで変換行列を適応する
      "gl_Position = projectionMatrix * vec4(position, 0.0f, 1.0f);\n"
    "}\n";


  const char* fragment_shader =
    "#version 400\n"
    "in vec3 outColor; \n"
    "out vec4 outFragmentColor; \n"
    "void main(void) {\n"
      "outFragmentColor = vec4(outColor, 1.0f); \n"
    "}\n";


  GLuint vs, fs, shader_programme;

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

  //GLfloat points[] = { 250.0f, 250.0f,
  //           250.0f,  0.0f,
  //           0.1f,  250.0f,
  //           0.1f,  0.0f};

  GLfloat points[] = { 0.6, 0.6,
            0.6,  0.0f,
            0.0f,  0.6f,
            0.0f,  0.0f};


  GLfloat colors[] = { 1.0f, 0.0f, 0.3f,
             0.0f, 1.0f, 0.0f,
             0.0f, 0.0f, 1.0f,
             1.0f, 0.8f, 1.0f};


  if (!glfwInit()) {
    fprintf(stderr, "ERROR: could not start GLFW3\n");
    return 1;
  }

  glfwWindowHint(GLFW_CONTEXT_VERSION_MAJOR, 2);
  glfwWindowHint(GLFW_CONTEXT_VERSION_MINOR, 1);

  window = glfwCreateWindow(window_width, window_heignt, "Hello Triangle", NULL, NULL);
  if (!window) {
    fprintf(stderr, "ERROR: could not open window with GLFW3\n");
    glfwTerminate();
    return 1;
  }
  glfwMakeContextCurrent(window);

  glewExperimental = GL_TRUE;
  glewInit();

  /*
  GLEWではなくGLADを使っているの場合
  gladLoadGLLoader((GLADloadproc)glfwGetProcAddress);
  glfwSwapInterval(1);
  */

  glEnable(GL_DEPTH_TEST);
  glDepthFunc(GL_LESS);

  GLint shader = makeShader();
  GLuint vao, vertex_vbo, color_vbo;

  glGenVertexArrays(1, &vao);
  glBindVertexArray(vao);

  glGenBuffers(1, &vertex_vbo); 
  glBindBuffer(GL_ARRAY_BUFFER, vertex_vbo); 
  glBufferData(GL_ARRAY_BUFFER, sizeof(points), points, GL_STATIC_DRAW);
  glGenBuffers(1, &color_vbo); 
  glBindBuffer(GL_ARRAY_BUFFER, color_vbo);
  glBufferData(GL_ARRAY_BUFFER, sizeof(colors), colors, GL_STATIC_DRAW); 

  
  //Z軸で45度回転
  // 0.7071 = cos(4/3.14) = sin(4/3.14)
const float projectionMatrix[4][4] = {
    { 0.7071,  -0.7071, 0.0f,  0.0f },
    { 0.7071,  0.7071, 0.0f,  0.0f },
    { 0.0f,    0.0f,   1.0f,  0.0f },
    { 0.0f,    0.0f,   0.0f,  1.0f }
  };

  //頂点シェーダへのバッファを作成。
  // バッファ上のデータは、シェーダではprojectionMatrixという変数名に格納される
  GLint projectionMatLocation = glGetUniformLocation(shader, "projectionMatrix");

  glUseProgram(shader);
  
  while (!glfwWindowShouldClose(window)) {
    glClear(GL_COLOR_BUFFER_BIT | GL_DEPTH_BUFFER_BIT);

    glEnableVertexAttribArray(0);
    glBindBuffer(GL_ARRAY_BUFFER, vertex_vbo);
    glVertexAttribPointer(0, 2, GL_FLOAT, GL_FALSE, 0, (GLvoid*)0);

    glEnableVertexAttribArray(1);
    glBindBuffer(GL_ARRAY_BUFFER, color_vbo);
    glVertexAttribPointer(1, 3, GL_FLOAT, GL_FALSE, 0, (GLvoid*)0);

    //追加：glGetUniformLocation関数で作成したバッファにprojectionMatrixを割り当てる。
    glUniformMatrix4fv(projectionMatLocation, 1, GL_FALSE, &projectionMatrix[0][0]);

    glDrawArrays(GL_TRIANGLE_STRIP, 0, 4);
    glDisableVertexAttribArray(0);
    glDisableVertexAttribArray(1);

    glfwPollEvents();
    glfwSwapBuffers(window); 
  }

  glfwTerminate();
  return 0;
}
  
  
### 実行結果

実行すると以下の画像のようになります。  
![f:id:pythonjacascript:20200603000542j:plain](/images/ppythonjacascript2020060320200603000542.jpg "f:id:pythonjacascript:20200603000542j:plain")  
  
  
### 解説

[前回のプログラム](https://shizenkarasuzon.hatenablog.com/entry/2020/05/26/001805)と比べて追加した部分だけ解説を行います

まず、4×4の行列を作ります。GL\_float型です。

//Z軸で45度回転
// 0.7071 = cos(4/3.14) = sin(4/3.14)
const float projectionMatrix[4][4] = {
    { 0.7071,  -0.7071, 0.0f,  0.0f },
    { 0.7071,  0.7071, 0.0f,  0.0f },
    { 0.0f,    0.0f,   1.0f,  0.0f },
    { 0.0f,    0.0f,   0.0f,  1.0f }
  };

今回適応する行列は上のような形にします。この行列の説明は後でします

  
つぎに、この行列の値を頂点シェーダに転送しなければなりません。GPU（シェーダを実行する部分）にデータを転送するためのバッファを作る必要があります。

今までは、シェーダーに渡す値はVBOを作ってシェーダープログラムでは「"layout(location = 0) in vec2 position;\\n"」みたいに書いていましたが、  
これは、各頂点で送られてくる値が異なるときに使います。しかし、この行列の値は全頂点で同じです。なので、VBOは使わずに**Uniform型**（テクスチャの時もuniformを使います）という特殊なデータ型の変数によって、シェーダにデータを転送します。

  
そのために必要な関数はglGetUniformLocationとglUniformMatrix4fvです

  
シェーダーにデータを渡すには、まず**glGetUniformLocation**関数で、GPUに値を送信するためのバッファを用意します。

GLint projectionMatLocation = glGetUniformLocation(shader, "projectionMatrix");

第二引数の「"projectionMatrix"」はシェーダープログラムで使う変数名を指定します。返り値（**projectionMatLocation** ）はバッファの識別用の番号（？）みたいなのを表します。

  
上の関数で作ったバッファに実際のデータを送るにはglUniformMatrix4fv関数を使います。第三引数でデータへのポインタを指定することで、バッファにデータを送信することができます、

glUniformMatrix4fv(projectionMatLocation, 1, GL_FALSE, &projectionMatrix[0][0]);

  
glUniformMatrix4fv関数は描画の度に呼ばなければなりません。  
  
  
### 行列の考え方

OpenGLでは、座標は4次元ベクトルで指定します。  
![(x, y, z, w)](https://chart.apis.google.com/chart?cht=tx&chl=%28x%2C%20y%2C%20z%2C%20w%29) のように書いた場合、実際に描画される点は![(x/w,  y/w,  z/w)](https://chart.apis.google.com/chart?cht=tx&chl=%28x%2Fw%2C%20%20y%2Fw%2C%20%20z%2Fw%29)です。

#### そのまま

![R = \left( \begin{array}{cccc}
      1.0 & 0 & 0 & 0\\
      0 & 1.0 & 0 & 0\\
     0 & 0 & 1.0 & 0\\
      0 & 0 & 0 & 1.0 \\
    \end{array} \right)](https://chart.apis.google.com/chart?cht=tx&chl=R%20%3D%20%5Cleft%28%20%5Cbegin%7Barray%7D%7Bcccc%7D%0A%20%20%20%20%20%201.0%20%26%200%20%26%200%20%26%200%5C%5C%0A%20%20%20%20%20%200%20%26%201.0%20%26%200%20%26%200%5C%5C%0A%20%20%20%20%200%20%26%200%20%26%201.0%20%26%200%5C%5C%0A%20%20%20%20%20%200%20%26%200%20%26%200%20%26%201.0%20%5C%5C%0A%20%20%20%20%5Cend%7Barray%7D%20%5Cright%29)

![E_4](https://chart.apis.google.com/chart?cht=tx&chl=E_4)ですね。  
  
#### 拡大縮小

![R = \left( \begin{array}{cccc}
      z_x & 0 & 0 & 0\\
      0 & z_y& 0 & 0\\
     0 & 0 & z_z & 0\\
      0 & 0 & 0 & 1.0 \\
    \end{array} \right)](https://chart.apis.google.com/chart?cht=tx&chl=R%20%3D%20%5Cleft%28%20%5Cbegin%7Barray%7D%7Bcccc%7D%0A%20%20%20%20%20%20z_x%20%26%200%20%26%200%20%26%200%5C%5C%0A%20%20%20%20%20%200%20%26%20z_y%26%200%20%26%200%5C%5C%0A%20%20%20%20%200%20%26%200%20%26%20z_z%20%26%200%5C%5C%0A%20%20%20%20%20%200%20%26%200%20%26%200%20%26%201.0%20%5C%5C%0A%20%20%20%20%5Cend%7Barray%7D%20%5Cright%29)  
で、原点を中心に![(z_x z_y, z_z)](https://chart.apis.google.com/chart?cht=tx&chl=%28z_x%20z_y%2C%20z_z%29) だけ拡大します。![ z_x = z_y = z_z = 1.0](https://chart.apis.google.com/chart?cht=tx&chl=%20z_x%20%3D%20z_y%20%3D%20z_z%20%3D%201.0) の時は拡大率1.0なのでそのままです  
  
#### 移動

![R = \left( \begin{array}{cccc}
      1.0& 0 & 0 & t_x\\
      0 & 1.0& 0 & t_y\\
     0 & 0 & 1.0 & t_z\\
      0 & 0 & 0 & 1.0 \\
    \end{array} \right)](https://chart.apis.google.com/chart?cht=tx&chl=R%20%3D%20%5Cleft%28%20%5Cbegin%7Barray%7D%7Bcccc%7D%0A%20%20%20%20%20%201.0%26%200%20%26%200%20%26%20t_x%5C%5C%0A%20%20%20%20%20%200%20%26%201.0%26%200%20%26%20t_y%5C%5C%0A%20%20%20%20%200%20%26%200%20%26%201.0%20%26%20t_z%5C%5C%0A%20%20%20%20%20%200%20%26%200%20%26%200%20%26%201.0%20%5C%5C%0A%20%20%20%20%5Cend%7Barray%7D%20%5Cright%29)  
で、![(t_xt_y, t_z)](https://chart.apis.google.com/chart?cht=tx&chl=%28t_xt_y%2C%20t_z%29) だけ平行移動します。  
  
#### 反転

![x](https://chart.apis.google.com/chart?cht=tx&chl=x) 軸反転をする場合は  
![R = \left( \begin{array}{cccc}
      -1.0& 0 & 0 & 0\\
      0 & 1.0& 0 & 0\\
     0 & 0 & 1.0 & 0\\
      0 & 0 & 0 & 1.0 \\
    \end{array} \right)](https://chart.apis.google.com/chart?cht=tx&chl=R%20%3D%20%5Cleft%28%20%5Cbegin%7Barray%7D%7Bcccc%7D%0A%20%20%20%20%20%20-1.0%26%200%20%26%200%20%26%200%5C%5C%0A%20%20%20%20%20%200%20%26%201.0%26%200%20%26%200%5C%5C%0A%20%20%20%20%200%20%26%200%20%26%201.0%20%26%200%5C%5C%0A%20%20%20%20%20%200%20%26%200%20%26%200%20%26%201.0%20%5C%5C%0A%20%20%20%20%5Cend%7Barray%7D%20%5Cright%29)

![y](https://chart.apis.google.com/chart?cht=tx&chl=y) 軸反転をする場合は  
![R = \left( \begin{array}{cccc}
      1.0& 0 & 0 & 0\\
      0 & -1.0& 0 & 0\\
     0 & 0 & 1.0 & 0\\
      0 & 0 & 0 & 1.0 \\
    \end{array} \right)](https://chart.apis.google.com/chart?cht=tx&chl=R%20%3D%20%5Cleft%28%20%5Cbegin%7Barray%7D%7Bcccc%7D%0A%20%20%20%20%20%201.0%26%200%20%26%200%20%26%200%5C%5C%0A%20%20%20%20%20%200%20%26%20-1.0%26%200%20%26%200%5C%5C%0A%20%20%20%20%200%20%26%200%20%26%201.0%20%26%200%5C%5C%0A%20%20%20%20%20%200%20%26%200%20%26%200%20%26%201.0%20%5C%5C%0A%20%20%20%20%5Cend%7Barray%7D%20%5Cright%29)

![z](https://chart.apis.google.com/chart?cht=tx&chl=z) 軸反転をする場合は  
![R = \left( \begin{array}{cccc}
      1.0& 0 & 0 & 0\\
      0 & 1.0& 0 & 0\\
     0 & 0 & -1.0 & 0\\
      0 & 0 & 0 & 1.0 \\
    \end{array} \right)](https://chart.apis.google.com/chart?cht=tx&chl=R%20%3D%20%5Cleft%28%20%5Cbegin%7Barray%7D%7Bcccc%7D%0A%20%20%20%20%20%201.0%26%200%20%26%200%20%26%200%5C%5C%0A%20%20%20%20%20%200%20%26%201.0%26%200%20%26%200%5C%5C%0A%20%20%20%20%200%20%26%200%20%26%20-1.0%20%26%200%5C%5C%0A%20%20%20%20%20%200%20%26%200%20%26%200%20%26%201.0%20%5C%5C%0A%20%20%20%20%5Cend%7Barray%7D%20%5Cright%29)  
です  
  
#### 回転

![x](https://chart.apis.google.com/chart?cht=tx&chl=x) 軸を軸として![ \theta](https://chart.apis.google.com/chart?cht=tx&chl=%20%5Ctheta)だけ回転する場合は  
![R = \left( \begin{array}{cccc}
      1.0& 0 & 0 & 0\\
      0 & cos\theta& -sin\theta& 0\\
     0 & sin\theta & cos\theta & 0\\
      0 & 0 & 0 & 1.0 \\
    \end{array} \right)](https://chart.apis.google.com/chart?cht=tx&chl=R%20%3D%20%5Cleft%28%20%5Cbegin%7Barray%7D%7Bcccc%7D%0A%20%20%20%20%20%201.0%26%200%20%26%200%20%26%200%5C%5C%0A%20%20%20%20%20%200%20%26%20cos%5Ctheta%26%20-sin%5Ctheta%26%200%5C%5C%0A%20%20%20%20%200%20%26%20sin%5Ctheta%20%26%20cos%5Ctheta%20%26%200%5C%5C%0A%20%20%20%20%20%200%20%26%200%20%26%200%20%26%201.0%20%5C%5C%0A%20%20%20%20%5Cend%7Barray%7D%20%5Cright%29)

  
![y](https://chart.apis.google.com/chart?cht=tx&chl=y) 軸を軸として![ \theta](https://chart.apis.google.com/chart?cht=tx&chl=%20%5Ctheta)だけ回転する場合は  
![R = \left( \begin{array}{cccc}
      cos\theta& 0 & sin\theta & 0\\
      0 & 1 & 0 & 0\\
     -sin\theta & 0 & cos\theta & 0\\
      0 & 0 & 0 & 1.0 \\
    \end{array} \right)](https://chart.apis.google.com/chart?cht=tx&chl=R%20%3D%20%5Cleft%28%20%5Cbegin%7Barray%7D%7Bcccc%7D%0A%20%20%20%20%20%20cos%5Ctheta%26%200%20%26%20sin%5Ctheta%20%26%200%5C%5C%0A%20%20%20%20%20%200%20%26%201%20%26%200%20%26%200%5C%5C%0A%20%20%20%20%20-sin%5Ctheta%20%26%200%20%26%20cos%5Ctheta%20%26%200%5C%5C%0A%20%20%20%20%20%200%20%26%200%20%26%200%20%26%201.0%20%5C%5C%0A%20%20%20%20%5Cend%7Barray%7D%20%5Cright%29)

  
![z](https://chart.apis.google.com/chart?cht=tx&chl=z) 軸を軸として![ \theta](https://chart.apis.google.com/chart?cht=tx&chl=%20%5Ctheta)だけ回転する場合は  
![R = \left( \begin{array}{cccc}
      cos\theta & -sin\theta & 0 & 0\\
      sin\theta & cos\theta & 0 & 0\\
     0 & 0 & 1.0 & 0\\
      0 & 0 & 0 & 1.0 \\
    \end{array} \right)](https://chart.apis.google.com/chart?cht=tx&chl=R%20%3D%20%5Cleft%28%20%5Cbegin%7Barray%7D%7Bcccc%7D%0A%20%20%20%20%20%20cos%5Ctheta%20%26%20-sin%5Ctheta%20%26%200%20%26%200%5C%5C%0A%20%20%20%20%20%20sin%5Ctheta%20%26%20cos%5Ctheta%20%26%200%20%26%200%5C%5C%0A%20%20%20%20%200%20%26%200%20%26%201.0%20%26%200%5C%5C%0A%20%20%20%20%20%200%20%26%200%20%26%200%20%26%201.0%20%5C%5C%0A%20%20%20%20%5Cend%7Barray%7D%20%5Cright%29)

  
#### 座標変換（左上原点ピクセル座標→中央原点同軸座標）

「OprnGLの画面中央原点の-1.0～1.0で表す独特の座標」から「ピクセル指定で画面左上原点の」座標に変換するような4×4の行列を考えます。

例を挙げてみます。

横幅![w](https://chart.apis.google.com/chart?cht=tx&chl=w)\[px\]、高さ![h](https://chart.apis.google.com/chart?cht=tx&chl=h)\[px\]のウィンドウと仮定すると、今作ろうとしている行列の写像の例は  
（ピクセル→OpenGL座標系）

* ![(0,0)](https://chart.apis.google.com/chart?cht=tx&chl=%280%2C0%29)→![(-1, 1)](https://chart.apis.google.com/chart?cht=tx&chl=%28-1%2C%201%29)の変換
* ![(w,h)](https://chart.apis.google.com/chart?cht=tx&chl=%28w%2Ch%29)→![(1, -1)](https://chart.apis.google.com/chart?cht=tx&chl=%281%2C%20-1%29)の変換
* ![(\dfrac{w}{2}, \dfrac{h}{2})](https://chart.apis.google.com/chart?cht=tx&chl=%28%5Cdfrac%7Bw%7D%7B2%7D%2C%20%5Cdfrac%7Bh%7D%7B2%7D%29)→![(0, 0)](https://chart.apis.google.com/chart?cht=tx&chl=%280%2C%200%29)の変換

等と考えられます。

これを満たすような行列を連立方程式で解くと、下のようになります  
![R = \left( \begin{array}{cccc}
      \dfrac{2}{w} & 0 & 0 & -1\\
      0 & - \dfrac{2}{h}  & 0 & 1\\
     0 & 0 & 1 & 0\\
      0 & 0 & 0 & 1.0 \\
    \end{array} \right)](https://chart.apis.google.com/chart?cht=tx&chl=R%20%3D%20%5Cleft%28%20%5Cbegin%7Barray%7D%7Bcccc%7D%0A%20%20%20%20%20%20%5Cdfrac%7B2%7D%7Bw%7D%20%26%200%20%26%200%20%26%20-1%5C%5C%0A%20%20%20%20%20%200%20%26%20-%20%5Cdfrac%7B2%7D%7Bh%7D%20%20%26%200%20%26%201%5C%5C%0A%20%20%20%20%200%20%26%200%20%26%201%20%26%200%5C%5C%0A%20%20%20%20%20%200%20%26%200%20%26%200%20%26%201.0%20%5C%5C%0A%20%20%20%20%5Cend%7Barray%7D%20%5Cright%29)

  
プログラム上で書く時の回転行列は

int window_width = 640;
int window_heignt = 400;

const float projectionMatrix[4][4] = {
	{ 2.0f / float(window_width), 0.0f, 0.0f, 0.0f },
	{ 0.0f, -2.0f / float(window_heignt), 0.0f, 0.0f },
	{ 0.0f, 0.0f, 1.0f, 0.0f },
	{ -1.0f, 1.0f, 0.0f, 1.0f }
};

のようになります  
  
  
### プログラム2（ピクセル座標→同軸座標）

上の変換行列を使って、ピクセル指定で画面左上原点座標系→「OprnGLの画面中央原点の-1.0～1.0で表す座標系」に変換する行列を適応してみました。  
これによって、**ポリゴンの描画位置をピクセルで指定**することができるようになります。  
  
#include <GL/glew.h>
#include <GLFW/glfw3.h>
#include <stdio.h>

int window_width = 640;
int window_heignt = 400;


GLint makeShader() {
  const char* vertex_shader =
    "#version 400\n"
    "layout(location = 0) in vec2 position;\n"
    "layout(location = 1) in vec3 color;\n"
    "uniform mat4 projectionMatrix;"
    "out vec3 outColor;\n"
    "void main(void) {\n"
    "outColor = color;\n"
    "gl_Position = projectionMatrix * vec4(position, 0.0f, 1.0f);\n"
    "}\n";


  const char* fragment_shader =
    "#version 400\n"
    "in vec3 outColor; \n"
    "out vec4 outFragmentColor; \n"
    "void main(void) {\n"
    "outFragmentColor = vec4(outColor, 1.0f); \n"
    "}\n";


  GLuint vs, fs, shader_programme;

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

  //ポリゴンの描画位置をピクセルで指定
  GLfloat points[] = { 250.0f, 250.0f,
         250.0f,  0.0f,
         0.1f,  250.0f,
         0.1f,  0.0f };


  GLfloat colors[] = { 1.0f, 0.0f, 0.3f,
         0.0f, 1.0f, 0.0f,
         0.0f, 0.0f, 1.0f,
         1.0f, 0.8f, 1.0f };


  if (!glfwInit()) {
    fprintf(stderr, "ERROR: could not start GLFW3\n");
    return 1;
  }

  glfwWindowHint(GLFW_CONTEXT_VERSION_MAJOR, 2);
  glfwWindowHint(GLFW_CONTEXT_VERSION_MINOR, 1);

  window = glfwCreateWindow(window_width, window_heignt, "Hello Triangle", NULL, NULL);
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
  GLuint vao, vertex_vbo, color_vbo;

  glGenVertexArrays(1, &vao);
  glBindVertexArray(vao);

  glGenBuffers(1, &vertex_vbo);
  glBindBuffer(GL_ARRAY_BUFFER, vertex_vbo);
  glBufferData(GL_ARRAY_BUFFER, sizeof(points), points, GL_STATIC_DRAW);
  glGenBuffers(1, &color_vbo);
  glBindBuffer(GL_ARRAY_BUFFER, color_vbo);
  glBufferData(GL_ARRAY_BUFFER, sizeof(colors), colors, GL_STATIC_DRAW);

  //座標系の変換行列（上の節参照）
  const float projectionMatrix[4][4] = {
    { 2.0f / float(window_width), 0.0f, 0.0f, 0.0f },
    { 0.0f, -2.0f / float(window_heignt), 0.0f, 0.0f },
    { 0.0f, 0.0f, 1.0f, 0.0f },
    { -1.0f, 1.0f, 0.0f, 1.0f }
  };

  //変換行列をシェーダに渡すためのバッファ作成
  GLint projectionMatLocation = glGetUniformLocation(shader, "projectionMatrix");

  glUseProgram(shader);

  while (!glfwWindowShouldClose(window)) {
    glClear(GL_COLOR_BUFFER_BIT | GL_DEPTH_BUFFER_BIT);

    glEnableVertexAttribArray(0);
    glBindBuffer(GL_ARRAY_BUFFER, vertex_vbo);
    glVertexAttribPointer(0, 2, GL_FLOAT, GL_FALSE, 0, (GLvoid*)0);

    glEnableVertexAttribArray(1);
    glBindBuffer(GL_ARRAY_BUFFER, color_vbo);
    glVertexAttribPointer(1, 3, GL_FLOAT, GL_FALSE, 0, (GLvoid*)0);

    //バッファにデータを送信
    glUniformMatrix4fv(projectionMatLocation, 1, GL_FALSE, &projectionMatrix[0][0]); 

    glDrawArrays(GL_TRIANGLE_STRIP, 0, 4);
    glDisableVertexAttribArray(0);
    glDisableVertexAttribArray(1);

    glfwPollEvents();
    glfwSwapBuffers(window);
  }
  glfwTerminate();
  return 0;
}
  
  
### 実行結果

実行すると以下の画像のようになります。  
![f:id:pythonjacascript:20200517170831j:plain](/images/ppythonjacascript2020051720200517170831.jpg "f:id:pythonjacascript:20200517170831j:plain")  
  
プログラムでは頂点座標を

  //ポリゴンの描画位置をピクセルで指定
  GLfloat points[] = { 250.0f, 250.0f,
         250.0f,  0.0f,
         0.1f,  250.0f,
         0.1f,  0.0f };

のようにピクセル座標で書いていますが、きちんと表示されています。