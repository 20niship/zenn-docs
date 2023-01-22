---
title: "【C++/MinGW】FreeTypeを使ってTTFファイルから文字レンダリング"
emoji: ""
type: ""
topics: []
published: false
---

* [FreeTypeとは？](#FreeTypeとは)
* [FreeTypeのインストール](#FreeTypeのインストール)
* [サンプルコード](#サンプルコード)
* [実行結果](#実行結果)
* [FreeTypeの使い方](#FreeTypeの使い方)  
   * [初期化](#初期化)  
   * [太字or斜体に設定する](#太字or斜体に設定する)  
   * [レンダリング（方法1）](#レンダリング方法1)  
   * [レンダリング（方法2）](#レンダリング方法2)

### FreeTypeとは？

FreeTypeはフォントエンジンを実装したライブラリです。つまり、std::stringとかcharとかの文字列を画像に変換することができます。この時、フォントファイル（ttf等)を指定したり、太字/斜体でレンダリングすることできます。TrueType、Type1フォント、OpenTypeなどのフォント形式をサポートしているようです。  
  
  
### FreeTypeのインストール

まず、下のURKから、最新版のFreeTyoeをダウンロードしてきます。  
[FreeType Downloads](https://www.freetype.org/download.html)

僕がダウンロードしたのはバージョン2.10.4です。

解凍して中身を見ると、CMakeListsがあったので、CMakeで入れることにします。

$ cd [ダウンロードしたFreeTypeライブラリのフォルダ]
$ mkdir build
$ cd build
$ cmake .. -G "MinGW Makefiles" 
$ make

下から二行目の"MinGW Makefiles" はコンパイラを指定しているだけで、通常は付ける必要はありません。筆者の環境ではVisualStudioのコンパイラとg++が混在しているので一応指定しておいただけです。

makeコマンドがうまくいくと、作成したbuildフォルダの中に「libfreetype.a」ができるので、これをリンクすればOKです。  
  
  
### サンプルコード

#include <opencv2/opencv.hpp>

#include <stdio.h>
#include <math.h>
#include <codecvt>
#include <string>
#include <iostream>
#include <locale>

#include <ft2build.h>
#include FT_FREETYPE_H

#define WIDTH   500
#define HEIGHT  300


class TextRender{
private:
  FT_Library library;   /* handle to library     */
  FT_Face face;      /* handle to face object */
  unsigned char *image;
  void draw_bitmap(int x, int y);
  FT_GlyphSlot slot;  // グリフへのショートカット
  std::u32string  u32str;

public:
  TextRender();
  ~TextRender();
  bool init();
  bool setFont(const char *font_file_name, int char_width, int char_height);
  void drawString(const char text[]);
  unsigned char *getImage();
};

TextRender::TextRender():image(nullptr){ init();}


// 1. libraryを初期化
bool TextRender::init(){
  if(image != nullptr) free(image);
  image = nullptr;
  auto error = FT_Init_FreeType( &library ) ;
  if (error) { std::cerr << "[ERROR] Failed to init FreeType library!" << std::endl; }
  return !error;
}

//フォントファイルを読み込む
bool TextRender::setFont(const char *font_file_name, int char_width, int char_height){
  // 2. faceを作成
  auto error = FT_New_Face( library, font_file_name, 0, &face);
  if ( error == FT_Err_Unknown_File_Format ){
    std::cerr << "[ERROR] Font file format is not supported!! " << std::endl; return false;
  }else if ( error ){
    std::cerr << "[ERROR] Font file not found or it is broken! " << std::endl; return false;
  }
  
  // 3. 文字サイズを設定
  error = FT_Set_Char_Size(face, 0,
                  char_width*char_height, // 幅と高さ
                  300, 300);  // 水平、垂直解像度*/
  
  slot = face->glyph;  // グリフへのショートカット
  return !error;
}


void TextRender::drawString(const char text[]){
  image = new unsigned char[WIDTH*HEIGHT];
  
  std::u32string  u32str  = std::wstring_convert<std::codecvt_utf8<char32_t>, char32_t>().from_bytes(text);

  int curPosX = 0;
  int curPosY = 60; //現在のカーソル位置
  int last_height = 0; //最後に文字を書いたときの文字の大きさ
  
  for (int n = 0; n < u32str.size(); n++ ){
    if(u32str[n] == '\n'){
      curPosX = 0;
      curPosY += last_height + 20;
    }else{
      if (FT_Load_Char( face, u32str[n], FT_LOAD_RENDER )) continue; //一文字レンダリング
      // int yMax = face->bbox.yMax;
      // int yMin = face->bbox.yMin;
      // int baseline = bitmap->rows * yMax / (yMax - yMin);
      draw_bitmap(curPosX, curPosY - slot->bitmap_top); //imageにslot->bitmapの中身をコピーする
    }
    last_height = (slot->bitmap).rows;

    curPosX += slot->advance.x >> 6;
    curPosY += slot->advance.y >> 6;
   }
};

//生成された位置も自分の画像データをimageにコピーする
void TextRender::draw_bitmap(int x, int y){
  int  i, j, p, q;
  const int  x_max = x + (slot->bitmap).width;
  const int  y_max = y + (slot->bitmap).rows;

  for ( i = x, p = 0; i < x_max; i++, p++ ){
    for ( j = y, q = 0; j < y_max; j++, q++ ){
      if ( i < 0      || j < 0       || i >= WIDTH || j >= HEIGHT) continue;
      image[j*WIDTH + i] |= (slot->bitmap).buffer[q *(slot->bitmap).width + p];
    }
  }
}


unsigned char *TextRender::getImage(){ return image; }

TextRender::~TextRender(){
  if(image != nullptr) free(image);
  FT_Done_Face    ( face );
  FT_Done_FreeType( library );
}


int main(){
  TextRender TR;
  TR.setFont("Meiryo.ttf", 16, 34);


  //レンダリング
  unsigned char *image;
  const char text[] = "こんにちは、世界！\nHello, World!!";
  TR.drawString(text);
  image =  TR.getImage();


  //画像を表示
  cv::Mat image_cv(HEIGHT, WIDTH, CV_8UC1,image);   
  cv::imshow("output", image_cv);
  cv::waitKey(0);

  return 0;
}

  
上のコードをコンパイルする為のCmakeListtsも載せておきます。  
CmakeListsでしていることは

* OpenCVを見つけていれる
* \[ダウンロードしたFreeTypeライブラリ\]/includeフォルダを#includeでの読み込み対象にする
* libfreetype.aをリンクする
* main.cpp(上のプログラムのこと)から実行ファイルを生成

という感じです  
  
cmake_minimum_required( VERSION 3.5 )
project(test CXX)

set(OpenCV_DIR "C:/Users/Owner/Library/Opencv/opencv-3.4/build_mingw")
find_package(OpenCV REQUIRED)
if(OpenCV_FOUND)
    include_directories(${OpenCV_INCLUDE_DIRS})
    link_libraries(${OpenCV_LIBS})
endif()

set(FREETYPE_DIR "C:/Users/Owner/Library/freetype-2.10.4")
include_directories(${FREETYPE_DIR}/include)

add_executable( test main.cpp)
target_link_libraries( test ${FREETYPE_DIR}/build/libfreetype.a ${OpenCV_LIBRARIES})

### 実行結果

下の画像の様な結果になります  
Meiryo.ttfが読み込まれて日本語もきちんと表示できているのがわかると思います  
![f:id:pythonjacascript:20201030181324j:plain](/images/ppythonjacascript2020103020201030181324.jpg "f:id:pythonjacascript:20201030181324j:plain")  
  
  
### FreeTypeの使い方

#### 初期化

まず、こんな感じでincludeします

#include <ft2build.h>
#include FT_FREETYPE_H

次に、色々初期化します。

FT_Library library;
FT_Face face; 
  
// 1. libraryを初期化
auto error = FT_Init_FreeType( &library ) ;
if (error) {... }

// 2. faceを作成
error = FT_New_Face( library, "hoge.ttf", 0, &face);
if (error) {... }

  
  // 3. 文字サイズを設定
  error = FT_Set_Char_Size(face, 0,
                  16*54, // 幅と高さ
                  300, 300);  // 水平、垂直解像度*/

ここでいじるのはフォントファイル名と文字サイズくらいだと思います  
  
#### 太字or斜体に設定する

太字や車体などの設定は、以下の関数を使ってグリフに適応させます

FT_GlyphSlot_Oblique(face->glyph );  //斜体にする
FT_GlyphSlot_Embolden(face->glyph );//太字にする

これらを使うにはグリフを読み込む必要があり、TextRender::drawString関数のfor文の中身を以下の様に変更する必要があります。

  for (int n = 0; n < u32str.size(); n++ ){
    if(u32str[n] == '\n'){
      curPosX = 0;
      curPosY += last_height + 20;
    }else{
     // 追加！今からレンダリングする式のグリフを取得
      FT_UInt glyph_index = FT_Get_Char_Index( face, u32str[n] );

     // 追加！そのグリフの設定を読み取る（ここで1bitビットマップに設定したりカラー画像にしたり色々変えられる）
      auto error = FT_Load_Glyph( face, glyph_index, FT_LOAD_DEFAULT );
      if ( error )continue;  /* ignore errors */
    
     // 追加！
      FT_GlyphSlot_Oblique(face->glyph );  //斜体にする
      FT_GlyphSlot_Embolden(face->glyph );//太字にする

      //追加！1文字レンダリング！
      error = FT_Render_Glyph(slot, FT_RENDER_MODE_NORMAL );
      if ( error ) continue;

      draw_bitmap(curPosX, curPosY - slot->bitmap_top); //imageにslot->bitmapの中身をコピーする
    }
    last_height = (slot->bitmap).rows;

    curPosX += slot->advance.x >> 6;
    curPosY += slot->advance.y >> 6;
   }

  
上のプログラムの様に、各文字のグリフを取得して、それ経由でレンダリングすることもできます。その時に、太字は斜体などの設定をグリフに適応させる、という事でしょう。  
ちなみに、最初のプログラムで使っていた FT\_Load\_Char関数とごちゃ混ぜにするとフリーズするので気を付けてください。

また、FT\_GlyphSlot\_Oblique関数とFT\_GlyphSlot\_Embolden関数は **freetype/ftsynth.h** で宣言されているので、冒頭のinclude文を

#include <ft2build.h>
#include <freetype/ftsynth.h>
#include FT_FREETYPE_H

に書き直してください

参考サイト→[FreeType2での太字、斜体 - HYPERSPACE UNIVERSE NEWS](https://blog.goo.ne.jp/saitosatoru/e/c7c6dce99512c0e1d2f22de8dd9808a7)  

実行するとこうなります：  
![f:id:pythonjacascript:20201030190940j:plain](/images/ppythonjacascript2020103020201030190940.jpg "f:id:pythonjacascript:20201030190940j:plain")  
  
  
#### レンダリング（方法1）

FT_Load_Char( face, ’a’, FT_LOAD_RENDER)

このように書くことで、'a'という一文字がレンダリングされて、face->glyph.bitmapにビットマップとして格納されます。（上のプログラムでは、slot = face->glyph;というショートカットを作成しているので、slot->bitmapと書いています）

このビットマップのサイズ等は以下の様に取得できます

| 値         | 計算                                   |
| --------- | ------------------------------------ |
| 横幅        | slot->bitmap.width                   |
| 高さ        | slot->bitmap.rows                    |
| yMax      | face->bbox.yMax;                     |
| yMin      | face->bbox.yMin                      |
| 文字のベースライン | bitmap->rows \* yMax / (yMax - yMin) |

yMinとyMaxが何を意味するのか分かりませんが、文字のベースラインを計算するにはこの値を使うみたいです。  
  
#### レンダリング（方法2）

「太字or斜体にする」の節で使用した、グリフに基づくレンダリング方法です

// 文字'a'のグリフを取得
FT_UInt glyph_index = FT_Get_Char_Index( face, 'a');
auto error = FT_Load_Glyph( face, glyph_index, FT_LOAD_DEFAULT );// そのグリフの設定を読み取る
FT_Render_Glyph(slot, FT_RENDER_MODE_NORMAL );//1文字レンダリング！

行数が上の方法1の三倍になっていますが、やっていることは同じで「a」をface->glyph.bitmapにビットマップとして格納します