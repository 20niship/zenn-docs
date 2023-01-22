---
title: "【C++】OpenGLで描画したものをOpenCVのMatに変換"
emoji: "🤖"
type: "tech"
topics: []
published: false
---

* [すること](#すること)
* [サンプルプログラム](#サンプルプログラム)
* [実行結果](#実行結果)
* [解説](#解説)  
   * [オフスクリーンレンダリング](#オフスクリーンレンダリング)  
   * [レンダリングしたデータを取得](#レンダリングしたデータを取得)

  
### すること

[shizenkarasuzon.hatenablog.com](https://shizenkarasuzon.hatenablog.com/entry/2020/05/26/001805)  
少し前に、OpenGLを使って四角形を表示するプログラムを書きました。この記事ではOpenGLでレンダリングを行って、OpenGL（正確にはGLFWwindow)のウィンドウで画像表示を行いましたが、今回はレンダリングはOpenGに行わせて、レンダリング結果の画像データをOpenCVのMatに読み込ませ、それをOpenCVを使って表示してみます。  
  
### サンプルプログラム

[【C++/OpenGL】NO.3 複数の四角形表示 - とある科学の備忘録](https://shizenkarasuzon.hatenablog.com/entry/2020/05/26/001805)  
↑のプログラムを少しいじってOpenCVの要素をたしただけですが、一応全文を載せます

#include <GL/glew.h>
#include <GLFW/glfw3.h>
#include <stdio.h>
#include <opencv2/core.hpp>
#include <opencv2/opencv.hpp>


GLint makeShader() {
  const char* vertex_shader =
    "#version 400\n"
    "layout(location = 0) in vec3 position;\n"
    "layout(location = 1) in vec3 color;\n"
    "out vec3 outColor;\n"
    "void main(void) {\n"
    "outColor = color;\n"
    "gl_Position = vec4(position, 1.0f);\n"
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
  const GLubyte *renderer;
  const GLubyte *version;

  
  GLfloat points[] = { 0.5f, 0.5f, 0.0f, //�l�p�`���
             0.5f, -0.5f, 0.0f,
            -0.5f, 0.5f,  0.0f,
            -0.5f, -0.5f, 0.0f,

            0.3f, 0.8f, 0.0f,//�l�p�`2��
            0.5f, -0.3f, 0.0f,
            -0.7f, 0.5f, 0.0f,
            -0.2f, -0.2f, 0.0f };

  GLfloat colors[] = { 0.5f, 0.0f, 0.3f,//�l�p�`���
             0.5f, 0.8f, 0.0f,
             1.0f, 0.0f, 1.0f,
             1.0f, 0.8f, 1.0f,

             0.0f, 1.0f, 1.0f,//�l�p�`2��
             0.0f, 0.3f, 0.5f,
             1.0f, 0.0f, 1.0f,
             0.2f, 0.1f, 1.0f };


  if (!glfwInit()) { fprintf(stderr, "ERROR: could not start GLFW3\n"); return 1; }

  glfwWindowHint(GLFW_CONTEXT_VERSION_MAJOR, 2);
  glfwWindowHint(GLFW_CONTEXT_VERSION_MINOR, 1);

  // 追加：オフスクリーンレンダリングを使用
  glfwWindowHint(GLFW_VISIBLE, 0);


  window = glfwCreateWindow(640, 480, "Rectangle", NULL, NULL);
  if (!window) {
    fprintf(stderr, "ERROR: could not open window with GLFW3\n");
    glfwTerminate();
    return 1;
  }

  glfwMakeContextCurrent(window);
  glewExperimental = GL_TRUE;
  glewInit();

  renderer = glGetString(GL_RENDERER);
  version = glGetString(GL_VERSION);
  printf("Renderer: %s\n", renderer);
  printf("OpenGL version supported %s\n", version);

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


  glClear(GL_COLOR_BUFFER_BIT | GL_DEPTH_BUFFER_BIT);
  glUseProgram(shader);

  glEnableVertexAttribArray(0);
  glBindBuffer(GL_ARRAY_BUFFER, vertex_vbo);
  glVertexAttribPointer(0, 3, GL_FLOAT, GL_FALSE, 0, (GLvoid*)0);

  glEnableVertexAttribArray(1);
  glBindBuffer(GL_ARRAY_BUFFER, color_vbo);
  glVertexAttribPointer(1, 3, GL_FLOAT, GL_FALSE, 0, (GLvoid*)0);

  glDrawArrays(GL_TRIANGLE_STRIP, 0, 4);
  glDrawArrays(GL_TRIANGLE_STRIP, 4, 4);

  glDisableVertexAttribArray(0);
  glDisableVertexAttribArray(1);

  glfwPollEvents();
  glfwSwapBuffers(window);

  //追加！
  glReadBuffer(GL_FRONT);
  cv::Mat img = cv::Mat(cv::Size(640, 480), CV_8UC4); //RGBAの四チャンネルの640x480の画像データ作成
  glReadPixels(0, 0, 640, 480, GL_BGRA_EXT, GL_UNSIGNED_BYTE, img.data);
  cv::flip(img, img, 0); // 水平軸で反転（垂直反転） cf. 1: 水平反転、 -1 : 両方の軸で反転
 
  cv::imshow("cvWindow", img);
  cv::waitKey(0);
  glfwTerminate();
  return 0;
}
  
  
### 実行結果

上のプログラムを実行すると↓のように表示されます。  
ウィンドウの名前が「cvWindow」になっているので、きちんとOpenCVで描画されていることがわかります。  
![f:id:pythonjacascript:20200625020110j:plain](/images/ppythonjacascript2020062520200625020110.jpg "f:id:pythonjacascript:20200625020110j:plain")  
  
  
### 解説

#### オフスクリーンレンダリング

通常、OpenGLでレンダリング処理（描画処理）を行った場合、その結果として生成された画像はGPUのVRAM（Video RAM）という場所に書き込まれてモニター上に表示されます。  
しかし、それを表示させないのが**オフスクリーンレンダリング**です。文字通りです。

OpenGLはGLFWを使うとオフスクリーンレンダリングができるようになります。  
方法は簡単で、glfwCreateWindow関数の前に一分追加するだけです。

  // 追加：オフスクリーンレンダリングを使用
  glfwWindowHint(GLFW_VISIBLE, 0);

  window = glfwCreateWindow(640, 480, "Rectangle", NULL, NULL);
  
  
#### レンダリングしたデータを取得

いつも通り、glDrawArrays関数で描画し、glfwSwapBuffers関数でスワップバッファ（表示用フロントバッファと描画用バックバッファのデータを入れ替える）を行います。

その後、レンダリングしたデータをOpenCVに格納するには以下のようにします。

glReadBuffer(GL_FRONT);
cv::Mat img = cv::Mat(cv::Size(640, 480), CV_8UC4); //RGBAの四チャンネルの640x480の画像データ作成
glReadPixels(0, 0, 640, 480, GL_BGRA_EXT, GL_UNSIGNED_BYTE, img.data);
cv::flip(img, img, 0); // 水平軸で反転（垂直反転） cf. 1: 水平反転、 -1 : 両方の軸で反転

一つずつ見ていきます。

glReadBuffer(GL_FRONT);

  
[glReadBuffer](https://www.khronos.org/registry/OpenGL-Refpages/gl2.1/xhtml/glReadBuffer.xml)  
glReadPixels関数で画像データを読み込むときに、どのバッファから画像を読み込むかを指定します。

glfwSwapBuffers関数でスワップバッファしない場合は「glReadBuffer(GL\_BACK);」と書くべきなんでしょうか。

  
で、次にglReadPixels関数で画像データを読み込みます。

glReadPixels(0, 0, 640, 480, GL_BGRA_EXT, GL_UNSIGNED_BYTE, img.data);

//構文：
void glReadPixels( 
   GLint x, GLint y,  //フレームバッファの読み込む場所の左上座標
   GLsizei width, GLsizei height, //フレームバッファから読み込む画像の大きさ
   GLenum format, //書き込み先のデータフォーマット。
   GLenum type, //書き込み先のデータ型
   void * data //フレームバッファから読み込んだデータの格納先
);

  
formatやtypeに何が入るのかは↓を見てください...  
[glReadPixels](https://www.khronos.org/registry/OpenGL-Refpages/gl2.1/xhtml/glReadPixels.xml)