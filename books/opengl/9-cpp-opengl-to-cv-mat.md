---
title: "【C++/OpenGL】NO.7 OpenCVのcv::MatをOpenGLのテクスチャに変換する"
emoji: ""
type: ""
topics: []
published: false
---

* [内容](#内容)
* [結論](#結論)
* [サンプルプログラム](#サンプルプログラム)
* [実行結果](#実行結果)

### 内容

タイトルの通りです。  
OpenCV（画像処理ライブラリ）のcv::Mat型の画像データをOpenGLのテクスチャに変換します。  
  
### 結論

cv::Mat型において、実際の画像データが格納されている場所は、**img.ptr()**で知ることができます。

つまり、こうなります。

#include <GL/glew.h>
#include <GLFW/glfw3.h>
#include <opencv2/opencv.hpp>
#include <opencv2/core.hpp>


GLuint loadTextureFromCVmat(cv::Mat *image){
  assert((*image).empty());
  
  GLuint texID;
  //glTexEnvi(GL_TEXTURE_ENV, GL_TEXTURE_ENV_MODE, GL_MODULATE);
  //glTexEnvi(GL_TEXTURE_ENV, GL_TEXTURE_ENV_MODE, GL_REPLACE);
  glGenTextures(1, &texID);
  glBindTexture(GL_TEXTURE_2D, texID);

  //テクスチャを拡大縮小した表示するときの処理方法の指定
  glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MIN_FILTER, GL_NEAREST); //縮小
  glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MAG_FILTER, GL_LINEAR); //拡大

  //もとのテクスチャ画像からはみ出した所を描画するときの処理
  glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_WRAP_S, GL_CLAMP);
  glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_WRAP_T, GL_CLAMP);

  cv::cvtColor((*image), (*image), cv::COLOR_RGB2BGR);

  glTexImage2D(GL_TEXTURE_2D, 0, GL_RGB,  (*image).cols, (*image).rows, 0, GL_RGB, GL_UNSIGNED_BYTE, (*image).ptr()); 
  
  return texID;
} 

OpenGLのテクスチャについてこれ以上の解説は↓を見てください。  
[shizenkarasuzon.hatenablog.com](https://shizenkarasuzon.hatenablog.com/entry/2020/06/05/120502)  
  
  
### サンプルプログラム

OpenCVのcv::VideoCaptureを使ってカメラ映像を読み取り、リアルタイムでGLFWwindowに描画するプログラムです。

#include <GL/glew.h>
#include <GLFW/glfw3.h> 
#include <iostream>
#include <fstream>
#include <opencv2/opencv.hpp>
#include <opencv2/core.hpp>

GLint makeShader() {
  const char* vertex_shader =
    "#version 400\n"
    "layout(location = 0) in vec2 position;\n"
    "layout(location = 1) in vec2 vuv;\n"
    "out vec2 Flag_uv;\n"
    "void main(void) {\n"
    "Flag_uv = vuv;\n"
    "gl_Position =vec4(position, 0.0, 1.0);\n"
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


void BindCVMatTexure(cv::Mat *image, GLuint texID) {
  assert(img.empty());

  glBindTexture(GL_TEXTURE_2D, texID);

  //テクスチャを拡大縮小した表示するときの処理方法の指定
  glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MIN_FILTER, GL_NEAREST); //縮小
  glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MAG_FILTER, GL_LINEAR); //拡大

  //もとのテクスチャ画像からはみ出した所を描画するときの処理
  glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_WRAP_S, GL_CLAMP);
  glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_WRAP_T, GL_CLAMP);

  cv::cvtColor((*image), (*image), cv::COLOR_RGB2BGR);
  glTexImage2D(GL_TEXTURE_2D,0,GL_RGB,image.cols,image.rows, 0, GL_RGB,GL_UNSIGNED_BYTE, image.ptr()); 
}


int main() {
  GLFWwindow *window = NULL;

  
  GLfloat points[] = { 0.8,  0.8,
            -0.8,  0.8,
            -0.8, -0.8f,
             0.8, -0.8, };

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

  window = glfwCreateWindow(1280, 720, "Texture", NULL, NULL);
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
  cv::VideoCapture cap(0);
  GLuint texID;
  cv::Mat img;
  
  //追加：テクスチャオブジェクトを作成
  glTexEnvi(GL_TEXTURE_ENV, GL_TEXTURE_ENV_MODE, GL_REPLACE);
  glGenTextures(1, &texID);

  //テクスチャ情報を送るuniform属性を設定する
  int textureLocation = glGetUniformLocation(shader, "texture");

  while (!glfwWindowShouldClose(window)) {
    glClear(GL_COLOR_BUFFER_BIT | GL_DEPTH_BUFFER_BIT);

    //現在のカメラ映像をimgに格納
    cap >> img;

    //imgをtexIDテクスチャにBindする（データを格納）
    BindCVMatTexure(&img, texID);

    glUseProgram(shader);

    glEnableVertexAttribArray(0);
    glBindBuffer(GL_ARRAY_BUFFER, vertex_vbo);
    glVertexAttribPointer(0, 2, GL_FLOAT, GL_FALSE, 0, (GLvoid*)0);

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

  glDeleteTextures(1, &texID);
  glfwTerminate();
  return 0;
}
  
  
### 実行結果

こんな感じで、カメラ映像がリアルタイムで表示されます。  
![f:id:pythonjacascript:20200612234611j:plain](/images/ppythonjacascript2020061220200612234611.jpg "f:id:pythonjacascript:20200612234611j:plain")