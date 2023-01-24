---
title: "【C++/OpenGL】NO.6 Win32 APIを利用して文字の表示（日本語OK！）"
emoji: ""
type: ""
topics: []
published: false
---

* [前書き](#前書き)
* [プログラム](#プログラム)
* [実行結果](#実行結果)
* [解説](#解説)
* [参考文献](#参考文献)

### 前書き

freeglutを使って文字の表示をすることもできますが、freeglutを使うには勿論freeGLUTを#includeする必要が出てきます。ですが、そうすると、同時にGLFWをインクルードできないので、メインループやイベントコールバックを自前で書いたり複数ウィンドウを表示したり、ということができなくなります。

という事で、freeGLUTを使わずに文字（勿論日本語対応）を表示する方法を紹介します。

簡単な動作解説をすると、

1. Win32 APIでビットマップを作る
2. Win32 APIでビットマップ上に文字を書く
3. そのビットマップをOpenGLのテクスチャデータに変換
4. テクスチャとして表示

という感じです。  
  
### プログラム

#include <GL/glew.h>
#include <GLFW/glfw3.h> 
#include <iostream>
#include <Windows.h>


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
    "in vec2 Flag_uv;"
    "uniform sampler2D Texture;"
    "out vec4 outFragmentColor; \n"
    "void main(void) {\n"
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


// 文字のフォントを作成
GLuint CreateStringMonoBits( HFONT hFont, const TCHAR* pszString) {
  HDC hMemDC = CreateCompatibleDC(NULL);
  
  SelectObject(hMemDC, hFont);
  SIZE sizeText;
  GetTextExtentPoint32(hMemDC, pszString, strlen(pszString), &sizeText);// テキストの幅と高さを計算します。
  sizeText.cx = ((sizeText.cx + 31) & (~31));  // 32の倍数にする

  // GetTextExtentPoint32関数で計算したサイズのビットマップを用意
  BITMAP  bitmap;
  bitmap.bmWidth = sizeText.cx;
  bitmap.bmHeight = sizeText.cy;
  bitmap.bmWidthBytes = ((sizeText.cx + 7) / 8 + 1) & (~1);
  bitmap.bmPlanes = 1;
  bitmap.bmBitsPixel = 1;
  bitmap.bmBits = malloc(bitmap.bmWidthBytes * sizeText.cy);

  HBITMAP hBitmap = CreateBitmapIndirect(&bitmap);

  if (!hBitmap) { std::cout << "ERROR!"; return 0; }
  if (NULL == &bitmap) { std::cout << "ERROR"; return 0; }// BITMAPの後処理


  // MemDCのビットマップ、フォント、テキスト色、背景色、背景モードの指定
  SelectObject(hMemDC, hBitmap);
  SelectObject(hMemDC, hFont);
  SetTextColor(hMemDC, RGB(255, 255, 255));
  SetBkColor(hMemDC, RGB(0, 0, 0));
  SetBkMode(hMemDC, OPAQUE);

  // MemDC上にテキストを描画
  SelectObject(hMemDC, (HBRUSH)CreateSolidBrush(RGB(0,0,0)));
  SelectObject(hMemDC, (HPEN)CreatePen(PS_SOLID, 1, RGB(0,0,0)));
  Rectangle(hMemDC, 0, 0, sizeText.cx, sizeText.cy);
  TextOut(hMemDC, 0, 0, pszString, lstrlen(pszString));

  int uiBitsSize = sizeText.cy * sizeText.cx / 8;
  GLubyte* bitmap_buffer = (GLubyte*)malloc(uiBitsSize);
  FillMemory(bitmap_buffer, uiBitsSize, 0);

  // ビットマップをビット列にコピー
  BITMAPINFO Bitmapinfo;

  Bitmapinfo.bmiHeader.biSize = sizeof(Bitmapinfo.bmiHeader);
  Bitmapinfo.bmiHeader.biWidth = sizeText.cx;
  Bitmapinfo.bmiHeader.biHeight = - sizeText.cy;
  Bitmapinfo.bmiHeader.biPlanes = 1;
  Bitmapinfo.bmiHeader.biBitCount = 1;// 1ピクセルあたりの1ビット＝２色ビットマップ
  Bitmapinfo.bmiHeader.biCompression = BI_RGB;
  Bitmapinfo.bmiHeader.biSizeImage = uiBitsSize;
  Bitmapinfo.bmiHeader.biXPelsPerMeter = 1;
  Bitmapinfo.bmiHeader.biYPelsPerMeter = 1;
  Bitmapinfo.bmiHeader.biClrUsed = 0;
  Bitmapinfo.bmiHeader.biClrImportant = 0;

  // 文字が書かれたデバイスコンテキストhMemDCの内容をbitmap_bufferにコピー
  auto result = GetDIBits(hMemDC, hBitmap, 0, sizeText.cy, bitmap_buffer,&Bitmapinfo, DIB_RGB_COLORS);

  GLuint texture_name;
  glGenTextures(1, &texture_name); // テクスチャオブジェクトを作成。texture_nameには作成したテクスチャオブジェクトのIDが入っている
  glPixelStorei(GL_UNPACK_ALIGNMENT, 1); // テクスチャデータは各ピクセル1bitですきまなくならんでいる

  float index[] = { 0.0, 1.0 };
  glPixelMapfv(GL_PIXEL_MAP_I_TO_R, 2, index);
  glPixelMapfv(GL_PIXEL_MAP_I_TO_G, 2, index);
  glPixelMapfv(GL_PIXEL_MAP_I_TO_B, 2, index);
  glPixelMapfv(GL_PIXEL_MAP_I_TO_A, 2, index);

  //テクスチャオブジェクト作成
  glBindTexture(GL_TEXTURE_2D, texture_name);

  //テクスチャオブジェクトにbitmap_bufferの画像データを格納する
  glTexImage2D(GL_TEXTURE_2D, 0, GL_RGBA, sizeText.cx, sizeText.cy, 0, GL_COLOR_INDEX, GL_BITMAP, bitmap_buffer);

  //画像を拡大縮小して表示する時の設定
  glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MAG_FILTER, GL_LINEAR);
  glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MIN_FILTER, GL_LINEAR);

  glBindTexture(GL_TEXTURE_2D, 0);

  free(bitmap_buffer);
  DeleteDC(hMemDC);
  DeleteObject(hBitmap);

  return texture_name;
}


int main() {
  GLFWwindow *window = NULL;
  const GLubyte *renderer;
  const GLubyte *version;

  GLfloat points[] = { 0.5f, 0.5f, 0.0f,
             -0.5f, 0.5f, 0.0f,
            -0.5f, -0.5f,  0.0f,
            0.5f, -0.5f, 0.0f };


  GLfloat vertex_uv[] = { 1.0f, 0.0f,
              0.0f, 0.0f,
              0.0f, 1.0f,
              1.0f, 1.0f };

  /* start GL context and O/S window using the GLFW helper library */
  if (!glfwInit()) {
    fprintf(stderr, "ERROR: could not start GLFW3\n");
    return 1;
  }

  glfwWindowHint(GLFW_CONTEXT_VERSION_MAJOR, 2);
  glfwWindowHint(GLFW_CONTEXT_VERSION_MINOR, 1);

  window = glfwCreateWindow(900, 300, "Hello Triangle", NULL, NULL);
  if (!window) {
    fprintf(stderr, "ERROR: could not open window with GLFW3\n");
    glfwTerminate();
    return 1;
  }
  glfwMakeContextCurrent(window);
  // start GLEW extension handler
  glewExperimental = GL_TRUE;
  glewInit();

  /* get version info */
  renderer = glGetString(GL_RENDERER); /* get renderer string */
  version = glGetString(GL_VERSION);   /* version as a string */
  printf("Renderer: %s\n", renderer);
  printf("OpenGL version supported %s\n", version);

  glEnable(GL_DEPTH_TEST);
  glDepthFunc(GL_LESS);

  //文字背景は透明にしたいので、テクスチャのアルファ値を適応する
  glEnable(GL_BLEND);
  glBlendFunc(GL_SRC_ALPHA, GL_ONE_MINUS_SRC_ALPHA);

  GLint shader = makeShader();


  GLuint vao;
  GLuint vertex_vbo, uv_vbo;

  glGenVertexArrays(1, &vao);
  glBindVertexArray(vao);

  // Position attribute  
  glGenBuffers(1, &vertex_vbo); //バッファを作成
  glBindBuffer(GL_ARRAY_BUFFER, vertex_vbo); //以下よりvertex_vboでバインドされているバッファが処理される
  glBufferData(GL_ARRAY_BUFFER, sizeof(points), points, GL_STATIC_DRAW); //実データを格納

  // UV coordinate attribute
  glGenBuffers(1, &uv_vbo); //バッファを作成
  glBindBuffer(GL_ARRAY_BUFFER, uv_vbo); //以下よりcolor_vboでバインドされているバッファが処理される
  glBufferData(GL_ARRAY_BUFFER, sizeof(vertex_uv), vertex_uv, GL_STATIC_DRAW); //実データを格納


  //[NEW] 文字フォント作成
  HDC hdc = CreateCompatibleDC(NULL);
  HFONT hFont = CreateFont(100, 0, //高さ, 幅
    0, 0, FW_BOLD,  //傾き(反時計回り), 文字の向き, 太字(0にするとデフォルト)
    FALSE, FALSE, FALSE,   //Italic,  下線,  打消し線
    SHIFTJIS_CHARSET, OUT_DEFAULT_PRECIS, //キャラクタセット, 物理フォントの検索方法
    CLIP_DEFAULT_PRECIS, DEFAULT_QUALITY,  //はみ出した場合の処理,  属性
    VARIABLE_PITCH | FF_DONTCARE, "メイリオ");    //ピッチ、ファミリ,  タイプフェイスのポインタ

  //文字が書かれたテクスチャ作成
  GLuint texID = CreateStringMonoBits(hFont, "こんにちは、世界");

  while (!glfwWindowShouldClose(window)) {
    glClear(GL_COLOR_BUFFER_BIT | GL_DEPTH_BUFFER_BIT);
    glClearColor(1.0, 0.0, 0.0,1.0);
    glUseProgram(shader);


    glEnableVertexAttribArray(0);
    glBindBuffer(GL_ARRAY_BUFFER, vertex_vbo);
    glVertexAttribPointer(0, 3, GL_FLOAT, GL_FALSE, 0, (GLvoid*)0);

    glEnableVertexAttribArray(1);
    glBindBuffer(GL_ARRAY_BUFFER, uv_vbo);
    glVertexAttribPointer(1, 2, GL_FLOAT, GL_FALSE, 0, (GLvoid*)0);

    int textureLocation = glGetUniformLocation(shader, "texture");
    glUniform1i(textureLocation, 0);

    glBindTexture(GL_TEXTURE_2D, texID);
    glDrawArrays(GL_TRIANGLE_FAN, 0, 4);//表示！

    glDisableVertexAttribArray(0);
    glDisableVertexAttribArray(1);

    glfwPollEvents();
    glfwSwapBuffers(window);
  }

  glfwTerminate();
  return 0;
}
  
  
### 実行結果

![f:id:pythonjacascript:20200606105213j:plain](/images/ppythonjacascript2020060620200606105213.jpg "f:id:pythonjacascript:20200606105213j:plain")  
このように文字が表示されます。  
  
  
### 解説

上のプログラムを作るうえで、↓サイトをとても参考にさせていただきました。分かりやすかったです！ありがとうございますm(\_\_)m  
[www.hiramine.com](https://www.hiramine.com/programming/opengl/jtext.html)  

OpenGLのテクスチャについて  
[shizenkarasuzon.hatenablog.com](https://shizenkarasuzon.hatenablog.com/entry/2020/06/05/120502)  

Win32 APIのフォントや文字描画について  
[shizenkarasuzon.hatenablog.com](https://shizenkarasuzon.hatenablog.com/entry/2020/02/25/171829)  
  
  
### 参考文献

[日本語文字列を描画するには](https://www.hiramine.com/programming/opengl/jtext.html)  
  
[c++ - OpenGL and monochrome texture - Stack Overflow](https://stackoverflow.com/questions/327642/opengl-and-monochrome-texture)  