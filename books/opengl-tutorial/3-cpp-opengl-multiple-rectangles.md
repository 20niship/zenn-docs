---
title: "【C++/OpenGL】NO.3 　複数の四角形表示"
emoji: ""
type: ""
topics: []
published: false
---

* [このページの内容](#このページの内容)
* [プログラム](#プログラム)
* [実行結果](#実行結果)
* [解説](#解説)
* [複数のポリゴンを描画する](#複数のポリゴンを描画する)  
   * [変更点1（GLfloat points\[\]）](#変更点1GLfloat-points)  
   * [変更点2（GLfloat colors\[\]）](#変更点2GLfloat-colors)  
   * [変更点3（VBOのサイズ変更）](#変更点3VBOのサイズ変更)  
   * [変更点4（glDrawArrays関数）](#変更点4glDrawArrays関数)
* [実行結果](#実行結果-1)

### このページの内容

前回はもっとも簡単なプログラムで三角形を表示しました。  
この記事では、前回のプログラムの解説をしながら、ちょっと複雑にして色がついた長方形を表示してみたいと思います。  
  
  
### プログラム

カラフルな長方形を表示するプログラムです。

#include <GL/glew.h>
#include <GLFW/glfw3.h>
#include <stdio.h>

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

	GLfloat points[] = { 0.5f, 0.5f, 0.0f, 
				 0.5f, -0.5f, 0.0f, 
				-0.5f, 0.5f,  0.0f,
				-0.5f, -0.5f, 0.0f};

	GLfloat colors[] = { 0.5f, 0.0f, 0.3f,
				 0.5f, 0.8f, 0.0f,
				 1.0f, 0.0f, 1.0f,
				 1.0f, 0.8f, 1.0f};


	if (!glfwInit()) {
		fprintf(stderr, "ERROR: could not start GLFW3\n");
		return 1;
	}

	glfwWindowHint(GLFW_CONTEXT_VERSION_MAJOR, 2);
	glfwWindowHint(GLFW_CONTEXT_VERSION_MINOR, 1);

	window = glfwCreateWindow(640, 480, "Rectangle", NULL, NULL);
	if (!window) {
		fprintf(stderr, "ERROR: could not open window with GLFW3\n");
		glfwTerminate();
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


	renderer = glGetString(GL_RENDERER); 
	version = glGetString(GL_VERSION);
	printf("Renderer: %s\n", renderer);
	printf("OpenGL version supported %s\n", version);

	glEnable(GL_DEPTH_TEST);
	glDepthFunc(GL_LESS);


	GLint shader = makeShader();
	GLuint vao;
	GLuint vertex_vbo, color_vbo;

	glGenVertexArrays(1, &vao);
	glBindVertexArray(vao);

	// 頂点座標のVBOを作成	
	glGenBuffers(1, &vertex_vbo); //バッファを作成
	glBindBuffer(GL_ARRAY_BUFFER, vertex_vbo); //以下よりvertex_vboでバインドされているバッファが処理される
	glBufferData(GL_ARRAY_BUFFER, sizeof(points), points, GL_STATIC_DRAW); //実データを格納

	// 色のVBOを作成
	glGenBuffers(1, &color_vbo); //バッファを作成
	glBindBuffer(GL_ARRAY_BUFFER, color_vbo); //以下よりcolor_vboでバインドされているバッファが処理される
	glBufferData(GL_ARRAY_BUFFER, sizeof(colors), colors, GL_STATIC_DRAW); //実データを格納

	
	while (!glfwWindowShouldClose(window)) {
		glClear(GL_COLOR_BUFFER_BIT | GL_DEPTH_BUFFER_BIT);
		glUseProgram(shader);

		glEnableVertexAttribArray(0);
		glBindBuffer(GL_ARRAY_BUFFER, vertex_vbo);
		glVertexAttribPointer(0, 3, GL_FLOAT, GL_FALSE, 0, (GLvoid*)0);

		glEnableVertexAttribArray(1);
		glBindBuffer(GL_ARRAY_BUFFER, color_vbo); 
		glVertexAttribPointer(1, 3, GL_FLOAT, GL_FALSE, 0, (GLvoid*)0);

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

![f:id:pythonjacascript:20200517153335j:plain](/images/ppythonjacascript2020051720200517153335.jpg "f:id:pythonjacascript:20200517153335j:plain")  
四角形が表示されました～～。pointsやcolorsの値をいじってみると色々変化があると思います。

また、↑のサンプルの頂点シェーダープログラムを下の様にいじると頂点の色や座標を一括で簡単に変えることができます

const char* vertex_shader =
   "#version 400\n"
   "layout(location = 0) in vec3 position;\n"
   "layout(location = 1) in vec3 color;\n"
   "out vec3 outColor;\n"
   "void main(void) {\n"
        "outColor = color + vec3(0.2, 0.1, 0.0);\n" // 赤と緑をちょっと明るくする
        "gl_Position = vec4(position, 1.0f) + vec4(0.5, 0.0, 0.0, 0.0);\n" //ｘ軸性の方向に移動
   "}\n";

### 解説

では、前回のプログラムと今回のプログラムを解説していきます。  
第一回の3角形表示プログラムとの変更点は、以下の二か所です

* **変更点1\.** 頂点数が3→4になったため、VBOのバッファ容量やデータ配列のサイズ、glDrawArrays()の引数が変わった
* **変更点2\.** 頂点の色を緑一色→カラフルに変更したため、頂点色を格納したVBOを作成した

まず、 **変更点1\.** を実際のプログラムで見てみると、下のようになります

GLfloat points[] = { 0.5f, 0.5f, 0.0f, 
               0.5f, -0.5f, 0.0f, 
            -0.5f, 0.5f,  0.0f,
            -0.5f, -0.5f, 0.0f};

glDrawArrays(GL_TRIANGLE_STRIP, 0, 4);

この2箇所だけです。GLfloat型の配列points\[\]は、頂点座標を![(x_1,y_1,z_1), (x_2, y_2, z_2), ....](https://chart.apis.google.com/chart?cht=tx&chl=%28x_1%2Cy_1%2Cz_1%29%2C%20%28x_2%2C%20y_2%2C%20z_2%29%2C%20....)のような形で格納しています。なので一頂点当たり3個のデータで、四角形を描画する場合4頂点あるので12個の数値が並んでいます

二つ目のglDrawArrays関数についての説明は、[ここ](https://shizenkarasuzon.hatenablog.com/entry/2020/05/24/183340#glDrawArraysglDrawElements--%E6%8F%8F%E7%94%BB)を見てください。  
0番目の頂点からはじまり、4頂点分のデータを描画するので、第2引数が0、第3引数が4になっています

  
**変更点2\.** は変更というか追加ですが、以下の部分が付け加えられています

glGenBuffers(1, &color_vbo); //バッファを作成
glBindBuffer(GL_ARRAY_BUFFER, color_vbo); //以下よりcolor_vboでバインドされているバッファが処理される
glBufferData(GL_ARRAY_BUFFER, sizeof(colors), colors, GL_STATIC_DRAW); //実データを格納	

↑の3行のプログラムで頂点色を格納する[VBO](https://shizenkarasuzon.hatenablog.com/entry/2020/05/21/020650#VBO%E3%81%A8VAO)を作っています。諸関数やVBOの作り方については[ここ](https://shizenkarasuzon.hatenablog.com/entry/2020/05/24/183340#VBO%E3%81%AE%E8%A8%AD%E5%AE%9A)を見てください  
  
// while文のメインループの中
glEnableVertexAttribArray(1);
glBindBuffer(GL_ARRAY_BUFFER, color_vbo); 
glVertexAttribPointer(1, 3, GL_FLOAT, GL_FALSE, 0, (GLvoid*)0);

このようにして、頂点色VBOに格納されたデータを頂点シェーダに転送しています。[ここ](https://shizenkarasuzon.hatenablog.com/entry/2020/05/24/183340#VBO%E3%81%AE%E8%A8%AD%E5%AE%9A)などを参考にしてください

  
そして、頂点シェーダとフラグメントシェーダもマイナーチェンジしています。

//頂点シェーダ
(省略）
layout(location = 1) in vec3 color;
out vec3 outColor;
void main(void) {
    outColor = color;
    (省略）
}

//フラグメントシェーダ
 (省略）
in vec3 outColor; 
out vec4 outFragmentColor; 
void main(void) {
    outFragmentColor = vec4(outColor, 1.0f); 
}

流れを説明すると、

1. 【c++ファイル内】glVertexAttribPointer関数によって、シェーダーのプログラムのlocation=1に頂点色VBOのデータが割り当てられる
2. 【頂点シェーダ内】location=1にVBOのデータがvec3型（3つの要素のある配列のようなデータ型）として送られてきて、color変数にその値が格納される
3. 【頂点シェーダ内】変数outColorを定義（vec3型で、「out」がついているので頂点シェーダ→フラグメントシェーダにデータが送られる）
4. 【頂点シェーダのmain関数内】outColorにcolorの値を代入
5. 【フラグメントシェーダ内】頂点シェーダで「out」修飾子を付けた変数はフラグメントシェーダ内で参照できるので、outColorの値がそのままフラグメントシェーダに伝えられる
6. 【フラグメントシェーダのmain関数内】フラグメントの描画色を表すoutFragmentColor にoutColorの値を代入

のようになっています  
  
### 複数のポリゴンを描画する

以上のことから、複数のポリゴンを表示するには以下の様にすればいいとわかると思います（？）

例として、二つの四角形を描画することを考えます。上のプログラムから変更が必要な部分は、

1. 頂点座標の情報（GLfloat points\[\]）を2つの四角形分準備する（3\[x,y,zの個/頂点\] x 4\[頂点/四角形\] x 2\[個の四角形\]で計24個の値が必要）
2. 各頂点の色情報（GLfloat colors\[\]）を2つの四角形分準備する
3. 頂点座標と色情報のVBOの確保するサイズを2倍にする
4. glDrawArrays()を2回呼ぶ

この3つの変更でOKです。

変更後のプログラムを一部載せると、  
  
##### 変更点1（GLfloat points\[\]）

GLfloat points[] = { 0.5f, 0.5f, 0.0f, //四角形一つ目
			 0.5f, -0.5f, 0.0f,
			-0.5f, 0.5f,  0.0f,
			-0.5f, -0.5f, 0.0f,

			0.3f, 0.8f, 0.0f,//四角形2つ目
			0.5f, -0.3f, 0.0f,
			-0.7f, 0.5f, 0.0f,
			-0.2f, -0.2f, 0.0f};

  
上の様に2つ目の四角形の頂点座標データを付け加えます。  
  
  
##### 変更点2（GLfloat colors\[\]）

GLfloat colors[] = { 0.5f, 0.0f, 0.3f,//四角形一つ目
			 0.5f, 0.8f, 0.0f,
			 1.0f, 0.0f, 1.0f,
			 1.0f, 0.8f, 1.0f,

			 0.5f, 0.0f, 1.0f,//四角形2つ目
			 0.5f, 0.3f, 0.5f,
			 1.0f, 0.0f, 1.0f,
			 0.2f, 0.1f, 1.0f };

  
上の様に2つ目の四角形の色データを付け加えます。  
  
  
##### 変更点3（VBOのサイズ変更）

VBOのサイズは**glBufferData**関数の第二引数で決めていますが、上のサンプルプログラムではsizeof(○○)を使っているのでとくに変更は ありません。

もし定数で書くなら、

glBufferData(GL_ARRAY_BUFFER, 12*sizeof(GLfloat), points, GL_STATIC_DRAW); //四角形が一つの時は12個のfloatデータが入っている

を

glBufferData(GL_ARRAY_BUFFER, 24*sizeof(GLfloat), points, GL_STATIC_DRAW); ///四角形が一つの時は24個のfloatデータが入っている

に変えればOKです。

##### 変更点4（glDrawArrays関数）

glDrawArrays(GL_TRIANGLE_STRIP, 4, 4); //一つ目の頂点を描画（VBOに格納されたデータの4～(4+4)番目を使う）
glDrawArrays(GL_TRIANGLE_STRIP, 0, 4); //一つ目の頂点を描画（VBOに格納されたデータの0～4番目を使う）
  
  
#### 実行結果

![f:id:pythonjacascript:20200517154636j:plain](/images/ppythonjacascript2020051720200517154636.jpg "f:id:pythonjacascript:20200517154636j:plain")  
このように2つの長方形が描画されたはずです。  
ここでは深度バッファを設定していないため、後で描画されたものが上に表示されます。ｚ軸の値に合わせてきちんと描画するには、

glEnable(GL_DEPTH_TEST);

で深度バッファをONにしてください。