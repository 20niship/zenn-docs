---
title: "【C++/OpenGL】NO.4  イベント処理（Callback関数）一覧"
emoji: ""
type: ""
topics: []
published: false
---

* [プログラム](#プログラム)
* [実行結果](#実行結果)
* [解説](#解説)

### プログラム

サンプルプログラムです。[このプログラム](https://shizenkarasuzon.hatenablog.com/entry/2020/05/21/020650#%E3%83%97%E3%83%AD%E3%82%B0%E3%83%A9%E3%83%A0)にイベント処理の内容を付け加えただけです

#include <GL/glew.h>
#include <GLFW/glfw3.h>
#include <Windows.h>
#include <stdio.h>
#include <iostream>

GLint makeShader() {
	const char* vertex_shader =
		"#version 400\n"
		"in vec3 vp;"
		"void main() {"
		"  gl_Position = vec4(vp, 1.0);"
		"}";


	const char* fragment_shader =
		"#version 400\n"
		"out vec4 frag_colour;"
		"void main() {"
		"  frag_colour = vec4(0.5, 1.0, 0.5, 1.0);"
		"}";

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


///////////////   イベントのCallback関数一覧  //////////////////


// マウスボタンが押されたときの処理 
void MouseButtonCallback(GLFWwindow* window, int button, int action, int mods) {
	std::cout << "mouse clicked! " << button << ", " << action << "\n";
}


// マウスカーソルの位置が変化したときの処理
void CursorposCallback(GLFWwindow *window, double x, double y) {
	std::cout << "mouse pos changed! " << x << ", " << y << "\n";
}


// マウスホイール操作時の処理 
void ScrollCallback(GLFWwindow *const window, double x, double y) {
	std::cout << "scroll callback! " << x << ", " << y << "\n";
}

//キーボード入力があった時の処理
void KeyboardCallback(GLFWwindow *const window, int key, int scancode, int action, int mods) {
	std::cout << "keyboard callback! " << key << ", " << mods << "\n";
}


// マウスホイール操作時の処理 
void CharCallback(GLFWwindow *const window, unsigned int c) {
	std::cout << "char callback! " << c << " = " << (PTSTR)&c << "\n";
}

//OpgnGL内部の処理でエラーが発生したときの処理
void ErrorCallback(int error, const char* description) {
	std::cout << "Error : " << description << "\n";
}


//Windowがリサイズされようとしている時の処理
void WindowResizeCallback(GLFWwindow *window, int width, int height) {
	printf("windowSizeCB %d %d\n", width, height);
}

//ファイルがWindow内にドラッグアンドドロップされたときの処理
void DropCallback(GLFWwindow *window, int path_count, const char* paths[]) {
	std::cout << "object dropped! " << paths[0] << "^n";
}

////////////////////////////////////////////////////////////////

int main() {
	GLFWwindow *window = NULL;
	GLuint vao;
	GLuint vbo;
	/* geometry to use. these are 3 xyz points (9 floats total) to make a
		 triangle */
	GLfloat points[] = { 0.5f, 0.5f, 0.3f, 0.5f, -0.5f, 0.0f, -0.5f, -0.5f, 0.0f };


	/* start GL context and O/S window using the GLFW helper library */
	if (!glfwInit()) {
		fprintf(stderr, "ERROR: could not start GLFW3\n");
		return 1;
	}

	glfwWindowHint(GLFW_CONTEXT_VERSION_MAJOR, 2);
	glfwWindowHint(GLFW_CONTEXT_VERSION_MINOR, 1);

	window = glfwCreateWindow(640, 480, "Hello Triangle", NULL, NULL);
	if (!window) {
		fprintf(stderr, "ERROR: could not open window with GLFW3\n");
		glfwTerminate();
		return 1;
	}

	glfwSetMouseButtonCallback(window, MouseButtonCallback);
	glfwSetScrollCallback(window, ScrollCallback);// マウスホイール操作時   
	glfwSetKeyCallback(window, KeyboardCallback);// キーボード操作時
	glfwSetCharCallback(window, CharCallback);// キーボード操作時
	glfwSetErrorCallback(ErrorCallback); //エラーが発生した時
	glfwSetWindowSizeCallback(window, WindowResizeCallback); // ウィンドウのサイズ変更時に呼び出す処理の登録   
	glfwSetCursorPosCallback(window, CursorposCallback);


	//nugetではglfwSetDropCallbackでエラーが出たので、公式サイトからGLFWとGLEWのパッケージを入れなおしたら動きました。
	glfwSetDropCallback(window, DropCallback);
	 
	glfwMakeContextCurrent(window);
	glewExperimental = GL_TRUE;
	glewInit();

	glGenBuffers(1, &vbo);
	glBindBuffer(GL_ARRAY_BUFFER, vbo);
	glBufferData(GL_ARRAY_BUFFER, 9 * sizeof(GLfloat), points, GL_STATIC_DRAW);

	glGenVertexArrays(1, &vao);
	glBindVertexArray(vao);
	glEnableVertexAttribArray(0);
	glBindBuffer(GL_ARRAY_BUFFER, vbo);
	glVertexAttribPointer(0, 3, GL_FLOAT, GL_FALSE, 0, NULL);

	GLint shader = makeShader();
	while (!glfwWindowShouldClose(window)) {
		glClear(GL_COLOR_BUFFER_BIT | GL_DEPTH_BUFFER_BIT);
		glUseProgram(shader);
		glBindVertexArray(vao);

		glDrawArrays(GL_TRIANGLES, 0, 3);

		glfwPollEvents();
		glfwSwapBuffers(window);
	}

	glfwTerminate();
	return 0;
}
  
  
### 実行結果

上のプログラムを実行すると、コマンドプロンプトが起動して、下の画像の様にマウスを動かしたりキーボードで文字を入力したりするとイベント情報が表示されます  
![f:id:pythonjacascript:20200526003126j:plain](/images/ppythonjacascript2020052620200526003126.jpg "f:id:pythonjacascript:20200526003126j:plain")  

### 解説

GLFWでのイベント処理を行うには、以下の3つが必要です

* イベントが発生したときに呼び出されるコールバック関数を定義
* glfwSet○○Callback関数で定義したコールバック関数を関連付け
* メインループの中でglfwPollEvents関数を呼び出し、イベントが起こったかどうかを調べる

glfwPollEvents関数については[ここ](https://shizenkarasuzon.hatenablog.com/entry/2020/05/24/183340#%E3%82%A4%E3%83%99%E3%83%B3%E3%83%88%E5%87%A6%E7%90%86)をみてください

  
コールバック関数を実装する前に、主なOpenGLのイベントの種類は以下のようなものがあります

| 名称                       | イベントが発生するタイミング             | 関連付けを行う関数                  |
| ------------------------ | -------------------------- | -------------------------- |
| mouse button callback    | マウスの左右ボタンと中央ボタンが押された時      | glfwSetMouseButtonCallback |
| scroll callback          | マウス中央のホイールを回転させたとき         | glfwSetScrollCallback      |
| key calllback            | キーボード上のキーを押したとき            | glfwSetKeyCallback         |
| char callbadk            | キーボードを使って文字が一文字入力されたとき     | glfwSetCharCallback        |
| error callback           | OpenGL内でエラーが発生したとき         | glfwSetErrorCallback       |
| window size callback     | ウィンドウのサイズをユーザーが変更しようとしたとき  | glfwSetWindowSizeCallback  |
| cursor position callback | マウスカーソルがGLFWwindow上で動いたとき  | glfwSetCursorPosCallback   |
| drop callback            | ファイルがD&D（ドラッグアンドドロップ）されたとき | glfwSetDropCallback        |

※OpenGLのライブラリを入れる方法として、VisualStudioを使っている場合はnugetというライブラリ管理システムがありますが、nugetを使ってGLFWを入れた時にはglfwSetDropCallback関数だけなぜか定義されていませんでした。公式サイトからGLFWとGLEWのパッケージを入れなおしたら動きました。

  
という事で、↑のサンプルプログラムの中からイベント処理に関連する部分だけを取り出すと下のようになります

#include <GL/glew.h>
#include <GLFW/glfw3.h>
#include <Windows.h>
#include <stdio.h>
#include <iostream>


// マウスボタンが押されたときの処理 
void MouseButtonCallback(GLFWwindow* window, int button, int action, int mods) {
	std::cout << "mouse clicked! " << button << ", " << action << "\n";
}


// マウスカーソルの位置が変化したときの処理
void CursorposCallback(GLFWwindow *window, double x, double y) {
	std::cout << "mouse pos changed! " << x << ", " << y << "\n";
}


// マウスホイール操作時の処理 
void ScrollCallback(GLFWwindow *const window, double x, double y) {
	std::cout << "scroll callback! " << x << ", " << y << "\n";
}

//キーボード入力があった時の処理
void KeyboardCallback(GLFWwindow *const window, int key, int scancode, int action, int mods) {
	std::cout << "keyboard callback! " << key << ", " << mods << "\n";
}


// マウスホイール操作時の処理 
void CharCallback(GLFWwindow *const window, unsigned int c) {
	std::cout << "char callback! " << c << " = " << (PTSTR)&c << "\n";
}

//OpgnGL内部の処理でエラーが発生したときの処理
void ErrorCallback(int error, const char* description) {
	std::cout << "Error : " << description << "\n";
}


//Windowがリサイズされようとしている時の処理
void WindowResizeCallback(GLFWwindow *window, int width, int height) {
	printf("windowSizeCB %d %d\n", width, height);
}

//ファイルがWindow内にドラッグアンドドロップされたときの処理
void DropCallback(GLFWwindow *window, int path_count, const char* paths[]) {
	std::cout << "object dropped! " << paths[0] << "^n";
}

////////////////////////////////////////////////////////////////


int main() {
	(省略) 
	glfwSetMouseButtonCallback(window, MouseButtonCallback);
	glfwSetScrollCallback(window, ScrollCallback);// マウスホイール操作時   
	glfwSetKeyCallback(window, KeyboardCallback);// キーボード操作時
	glfwSetCharCallback(window, CharCallback);// キーボード操作時
	glfwSetErrorCallback(ErrorCallback); //エラーが発生した時
	glfwSetWindowSizeCallback(window, WindowResizeCallback); // ウィンドウのサイズ変更時に呼び出す処理の登録   
	glfwSetCursorPosCallback(window, CursorposCallback);
	(省略) 

	while (!glfwWindowShouldClose(window)) {
		(省略) 
		glfwPollEvents();
	}

	glfwTerminate();
	return 0;
}