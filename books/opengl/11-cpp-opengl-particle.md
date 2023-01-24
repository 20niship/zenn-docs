---
title: "【C++/OpenGL】インスタンス化を用いてパーティクル出力"
emoji: ""
type: ""
topics: []
published: false
---

~~にゃんぱすー。~~お久しぶりです。期末テストが終わって夏休みに入ったのでブログを再開しようかなぁと考えているところです。  
  
* [プログラム](#プログラム)
* [実行結果](#実行結果)
* [解説](#解説)  
   * [インスタンス化](#インスタンス化)  
   * [データ共有によるメモリ削減（glVertexAttribDivisor）](#データ共有によるメモリ削減glVertexAttribDivisor)  
   * [パーティクルのシミュレーション](#パーティクルのシミュレーション)

  
今回作るのはこちら！  
![f:id:pythonjacascript:20200808134507g:plain](/images/ppythonjacascript2020080820200808134507.gif "f:id:pythonjacascript:20200808134507g:plain")  

では、早速プログラムから。

### プログラム

#include <GL/glew.h>
#include <GLFW/glfw3.h>
#include <stdio.h>
#include <vector>
#include <algorithm>
#include <iostream>

#define MAX_PARTICLE_N 5000
#define PARTICLE_LIFE_SECONDS 6.0
#define DEFAULT_POS_X -0.5
#define DEFAULT_POS_Y -0.5
#define DEFAULT_POS_Z 0.1
#define DEFAULT_VEL_X 0.2
#define DEFAULT_VEL_Y 0.15
#define DEFAULT_VEL_Z 0.2
#define SPREAD_RATE   0.1
#define DEFAULT_SIZE  1.0


GLint makeShader() {
  const char* vertex_shader =
    "#version 400\n"
    "layout(location = 0) in vec3 billbords;\n"
    "layout(location = 1) in vec4 position;\n"
    "layout(location = 2) in vec4 color;\n"
    "out vec4 outColor;\n"
    "void main(void) {\n"
    "outColor = color;\n"
    "gl_Position = vec4(position.xyz + billbords*position.w, 1.0f);\n"
    "}\n";

  const char* fragment_shader =
    "#version 400\n"
    "in vec4 outColor; \n"
    "out vec4 outFragmentColor; \n"
    "void main(void) {\n"
    "outFragmentColor = outColor; \n"
    "}\n";

  GLuint vs, fs, shader_programme;

  vs = glCreateShader(GL_VERTEX_SHADER);
  glShaderSource(vs, 1, &vertex_shader, NULL);
  glCompileShader(vs);
  //GLint success = 0;
  //glGetShaderiv(vs, GL_COMPILE_STATUS, &success);
  //if (success == GL_FALSE) std::cout << "頂点シェーダー作成に失敗！！";

  fs = glCreateShader(GL_FRAGMENT_SHADER);
  glShaderSource(fs, 1, &fragment_shader, NULL);
  glCompileShader(fs);
  //GLint success = 0;
  //glGetShaderiv(fs, GL_COMPILE_STATUS, &success);
  //if (success == GL_FALSE) std::cout << "フラグメントシェーダー作成に失敗！！";

  shader_programme = glCreateProgram();
  glAttachShader(shader_programme, fs);
  glAttachShader(shader_programme, vs);
  glLinkProgram(shader_programme);

  return shader_programme;
}


// CPUでパーティクルを作成する。それぞれの粒子一つ一つがこの値を持っている。
struct Particle {
  float pos[3];
  float speed[3];
  float color[4];
  float size;
  float life; // 粒子の寿命。精製された瞬間はPARTICLE_LIFE_SECONDSが代入され、時間がたつと徐々に減少して、負の数になっている場合は描画されない。
  float cameradistance; // 中心座標からの距離の二乗。これでソートする

  bool operator<(const Particle& that) const {
    // Sort in reverse order : far particles drawn first.
    return this->cameradistance > that.cameradistance;
  }
};


Particle ParticlesContainer[MAX_PARTICLE_N];
int LastUsedParticle = 0;

// life < 0.となっている（つまり描画されていない）パーティクルを探す。
// この後そのパーティクルを初期化して復活させるため
int FindUnusedParticle() {
  for (int i = LastUsedParticle; i < MAX_PARTICLE_N; i++) {
    if (ParticlesContainer[i].life < 0) {
      LastUsedParticle = i;
      return i;
    }
  }
  for (int i = 0; i < LastUsedParticle; i++) {
    if (ParticlesContainer[i].life < 0) {
      LastUsedParticle = i;
      return i;
    }
  }

  return 0; // All particles are taken, override the first one
}


void SortParticles() {
  std::sort(&ParticlesContainer[0], &ParticlesContainer[MAX_PARTICLE_N]);
}


int main() {
  GLFWwindow *window = NULL;
  const GLubyte *renderer;
  const GLubyte *version;
  
  if (!glfwInit()) {
    fprintf(stderr, "ERROR: could not start GLFW3\n");
    return 1;
  }

  glfwWindowHint(GLFW_CONTEXT_VERSION_MAJOR, 2);
  glfwWindowHint(GLFW_CONTEXT_VERSION_MINOR, 1);

  window = glfwCreateWindow(640, 480, "Particle", NULL, NULL);
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


  static GLfloat* g_particule_position = new GLfloat[MAX_PARTICLE_N * 4];
  static GLfloat* g_particule_color = new GLfloat[MAX_PARTICLE_N * 4];


  for (int i = 0; i < MAX_PARTICLE_N; i++) {
    ParticlesContainer[i].life = -1.0f;
    ParticlesContainer[i].cameradistance = -1.0f;
  }

  // それぞれのパーティクルの形状を表している。
  // インスタンス化のお陰で、パーティクル全ての形状をこの12変数だけで表すことができる
  // (インスタンス化をしない場合は 12 x 粒子数 だけデータ必要になる)
  static const GLfloat g_vertex_buffer_data[] = {
     -0.01f, -0.01f, 0.0f,
      0.01f, -0.01f, 0.0f,
     -0.01f,  0.01f, 0.0f,
      0.01f,  0.01f, 0.0f,
  };


  GLint shader = makeShader();
  GLuint vao;
  GLuint vertex_vbo, color_vbo, shape_vbo;

  glGenVertexArrays(1, &vao);
  glBindVertexArray(vao);

  // 粒子形状のVBOを作成
  glGenBuffers(1, &shape_vbo);
  glBindBuffer(GL_ARRAY_BUFFER, shape_vbo);
  glBufferData(GL_ARRAY_BUFFER, sizeof(g_vertex_buffer_data), g_vertex_buffer_data, GL_STATIC_DRAW);


  // 頂点座標のVBOを作成  
  glGenBuffers(1, &vertex_vbo); //バッファを作成
  glBindBuffer(GL_ARRAY_BUFFER, vertex_vbo); //以下よりvertex_vboでバインドされているバッファが処理される
  glBufferData(GL_ARRAY_BUFFER, sizeof(g_particule_position), g_particule_position, GL_STATIC_DRAW); //実データを格納

  // 色のVBOを作成
  glGenBuffers(1, &color_vbo); //バッファを作成
  glBindBuffer(GL_ARRAY_BUFFER, color_vbo); //以下よりcolor_vboでバインドされているバッファが処理される
  glBufferData(GL_ARRAY_BUFFER, sizeof(g_particule_color), g_particule_color, GL_STATIC_DRAW); //実データを格納


  double lastTime = glfwGetTime();

  while (!glfwWindowShouldClose(window)) {
    glClear(GL_COLOR_BUFFER_BIT | GL_DEPTH_BUFFER_BIT);

    double currentTime = glfwGetTime();
    double delta = currentTime - lastTime;
    lastTime = currentTime;

    //1msあたり10個の粒子を新しく作る
    int newparticles = (int)(delta*10000.0);
    if (newparticles > (int)(0.016f*10000.0)) //新しく作る粒子の数が巨大になりすぎないように制限
      newparticles = (int)(0.016f*10000.0);

    for (int i = 0; i < newparticles; i++) {
      //まだ作られていない（またはlife < 0で死亡した）パーティクルを探して初期化する

      int particleIndex = FindUnusedParticle(); 
      ParticlesContainer[particleIndex].life = PARTICLE_LIFE_SECONDS;
      ParticlesContainer[particleIndex].pos[0] = DEFAULT_POS_X;
      ParticlesContainer[particleIndex].pos[1] = DEFAULT_POS_Y;
      ParticlesContainer[particleIndex].pos[2] = DEFAULT_POS_Z;
      ParticlesContainer[particleIndex].pos[3] = DEFAULT_SIZE;

      ParticlesContainer[particleIndex].speed[0] = DEFAULT_VEL_X + (rand() % 2000 - 1000.0f)* SPREAD_RATE / 1000.0f;
      ParticlesContainer[particleIndex].speed[1] = DEFAULT_VEL_Y + (rand() % 2000 - 1000.0f)* SPREAD_RATE / 1000.0f;
      ParticlesContainer[particleIndex].speed[2] = DEFAULT_VEL_Z + (rand() % 2000 - 1000.0f)* SPREAD_RATE / 1000.0f;

      ParticlesContainer[particleIndex].color[0] = rand() % 10000 / 10000.0;
      ParticlesContainer[particleIndex].color[1] = rand() % 10000 / 10000.0;
      ParticlesContainer[particleIndex].color[2] = rand() % 10000 / 10000.0;
      ParticlesContainer[particleIndex].color[3] = 1.0; //rand() % 10000 / 10000.0;

      ParticlesContainer[particleIndex].size = (rand() % 1000) / 20000.0f + DEFAULT_SIZE;
    }


    //パーティクルをCPUでシミュレーションする
    int ParticlesCount = 0;
    for (int i = 0; i < MAX_PARTICLE_N; i++) {
      Particle& p = ParticlesContainer[i];

      p.life -= delta;
      if (p.life > 0.0f) {
        //等速直線運動
        p.pos[0] += p.speed[0] * (float)delta;
        p.pos[1] += p.speed[1] * (float)delta;
        p.pos[2] += p.speed[2] * (float)delta;

        p.cameradistance = (p.pos[0])*(p.pos[0]) + (p.pos[1])*(p.pos[1]) + (p.pos[2])*(p.pos[2]);

        // GPUに転送するバッファに値をコピーする
        g_particule_position[4 * ParticlesCount + 0] = p.pos[0];
        g_particule_position[4 * ParticlesCount + 1] = p.pos[1];
        g_particule_position[4 * ParticlesCount + 2] = p.pos[2];
        g_particule_position[4 * ParticlesCount + 3] = p.size;

        g_particule_color[4 * ParticlesCount + 0] = p.color[0];
        g_particule_color[4 * ParticlesCount + 1] = p.color[1];
        g_particule_color[4 * ParticlesCount + 2] = p.color[2];
        g_particule_color[4 * ParticlesCount + 3] = p.color[3];
      }else {
        //描画されないパーティクルはSortParticles()で最後のほうに並ぶようにする
        p.cameradistance = -1.0f;
      }

      ParticlesCount++;
    }

    SortParticles();


    printf("%d ",ParticlesCount);


    glBindBuffer(GL_ARRAY_BUFFER, vertex_vbo);
    glBufferData(GL_ARRAY_BUFFER, MAX_PARTICLE_N * 4 * sizeof(GLfloat), NULL, GL_STREAM_DRAW); // Buffer orphaning, a common way to improve streaming perf. See above link for details.
    glBufferSubData(GL_ARRAY_BUFFER, 0, ParticlesCount * sizeof(GLfloat) * 4, g_particule_position);

    glBindBuffer(GL_ARRAY_BUFFER, color_vbo);
    glBufferData(GL_ARRAY_BUFFER, MAX_PARTICLE_N * 4 * sizeof(GLfloat), NULL, GL_STREAM_DRAW); // Buffer orphaning, a common way to improve streaming perf. See above link for details.
    glBufferSubData(GL_ARRAY_BUFFER, 0, ParticlesCount * sizeof(GLfloat) * 4, g_particule_color);


    glEnable(GL_BLEND);
    glBlendFunc(GL_SRC_ALPHA, GL_ONE_MINUS_SRC_ALPHA);

    glUseProgram(shader);


    // 各種バッファにデータ転送
    glEnableVertexAttribArray(0);
    glBindBuffer(GL_ARRAY_BUFFER, shape_vbo);
    glVertexAttribPointer(0, 3, GL_FLOAT, GL_FALSE, 0, (void*)0);

    glEnableVertexAttribArray(1);
    glBindBuffer(GL_ARRAY_BUFFER, vertex_vbo);
    glVertexAttribPointer(1, 4, GL_FLOAT, GL_FALSE, 0, (GLvoid*)0);

    glEnableVertexAttribArray(2);
    glBindBuffer(GL_ARRAY_BUFFER, color_vbo);
    glVertexAttribPointer(2, 4, GL_FLOAT, GL_FALSE, 0, (GLvoid*)0);

    // https://qiita.com/y_UM4/items/585212422fea72faa889
    glVertexAttribDivisor(0, 0); // 頂点情報はすべての粒子に同じ値を使用し、分割しない → 第二引数は0
    glVertexAttribDivisor(1, 1); // 粒子の位置はすべての粒子で違うので4つごとにデータを区切る→ 第二引数は1
    glVertexAttribDivisor(2, 1); // 粒子の色はすべての粒子で違うので4つごとにデータを区切る→ 第二引数は1                               -> 1

    //描画！！
    //インスタンス化を使わない場合は for(i in ParticlesCount) : glDrawArrays(GL_TRIANGLE_STRIP, 0, 4), と書くこともできるが、
    // ドローコールが何回も呼び出されることで動作が遅くなるため、NG
    glDrawArraysInstanced(GL_TRIANGLE_STRIP, 0, 4, ParticlesCount);

    glDisableVertexAttribArray(0);
    glDisableVertexAttribArray(1);
    glDisableVertexAttribArray(2);


    glfwPollEvents();
    glfwSwapBuffers(window);// Windowに描画内容を表示
  }

  glfwTerminate();//終了処理
  return 0;
}
  
  
### 実行結果

下のプログラムを実行すると以下の画像のようになります。  
![f:id:pythonjacascript:20200808134440j:plain](/images/ppythonjacascript2020080820200808134440.jpg "f:id:pythonjacascript:20200808134440j:plain")  
  
  
### 解説

上のプログラムの重要な部分の説明です。

#### インスタンス化

上のGifアニメーションの様に同じ形状の大量のオブジェクトを描画したいという場合には「**インスタンス化**」を使うと描画を効率化することができます。

  
今までのプログラムでは、描画の時にglDrawArrays()関数を使っていましたが、これでは大量のオブジェクトを描画するには、

//ParticlesCountは描画したい粒子の数
for(int i=0; i< ParticlesCount; i++){
  glDrawArrays(GL_TRIANGLE_STRIP, 0, 4);
}

のように書かなくてはいけません。glDrawArraysのようなDrawコールを行うとそのたびにCPUやGPUが色々な計算をするらしく（？）動作がもっさりしてしまうため、処理速度を上げようと思ったら、描画に必要なデータを一度にGPUに渡して、できるだけ少ない数のDrawコールで済むようにプログラムするのはいいみたいです。

  
ということで、インスタンス化の出番です。glDrawArraysの代わりに**glDrawArraysInstanced**関数を使うと、↑のプログラムは

//ParticlesCountは描画したい粒子の数
glDrawArraysInstanced(GL_TRIANGLE_STRIP, 0, 4,ParticlesCount);

とドローコール一回で済むという事です。  
  
  
#### データ共有によるメモリ削減（glVertexAttribDivisor）

上のプログラムは最大で5000個の粒子を描画します。それらの粒子の形状は全て同じなのに、前回までのプログラムの様にそれぞれの粒子について形状データを配列化して登録していたらメモリが無駄になってしまいます。

  
そこで、**glVertexAttribDivisor**関数を使うことで、全ての粒子に同じ値を適応することができます。

void glVertexAttribDivisor(	GLuint index,　GLuint divisor);
// index = VBOのindex (glVertexAttribPointerの第一引数に指定した値)
// divisor == 0 --> index番目のVBOデータは、分割されずにすべての図形に共有される
// divisor == 1 --> index番目のVBOデータは、glVertexAttribPointerの第2引数で指定している頂点数で分割する。

以下の記事を参考にさせて頂きましたm(\_\_)m   
[qiita.com](https://qiita.com/y%5FUM4/items/585212422fea72faa889)

  
上のサンプルプログラムでは以下の部分が相当します、

glEnableVertexAttribArray(0);
glBindBuffer(GL_ARRAY_BUFFER, shape_vbo);
glVertexAttribPointer(0, 3, GL_FLOAT, GL_FALSE, 0, (void*)0);

glEnableVertexAttribArray(1);
glBindBuffer(GL_ARRAY_BUFFER, vertex_vbo);
glVertexAttribPointer(1, 4, GL_FLOAT, GL_FALSE, 0, (GLvoid*)0);

glEnableVertexAttribArray(2);
glBindBuffer(GL_ARRAY_BUFFER, color_vbo);
glVertexAttribPointer(2, 4, GL_FLOAT, GL_FALSE, 0, (GLvoid*)0);

glVertexAttribDivisor(0, 0); // 頂点情報はすべての粒子に同じ値を使用し、分割しない → 第二引数は0
glVertexAttribDivisor(1, 1); // 粒子の位置はすべての粒子で違うので4つごとにデータを区切る→ 第二引数は1
glVertexAttribDivisor(2, 1); // 粒子の色はすべての粒子で違うので4つごとにデータを区切る→ 第二引数は1                 

//描画！！
glDrawArraysInstanced(GL_TRIANGLE_STRIP, 0, 4, ParticlesCount);
  
  
#### パーティクルのシミュレーション

それぞれの粒子には下の様な構造体を割り当てています。

struct Particle {
	float pos[3];
	float speed[3];
	float color[4];
	float size;
	float life; // 粒子の寿命。精製された瞬間はPARTICLE_LIFE_SECONDSが代入され、時間がたつと徐々に減少して、負の数になっている場合は描画されない。
	float cameradistance; // 中心座標からの距離の二乗。これでソートする

	bool operator<(const Particle& that) const {
		// Sort in reverse order : far particles drawn first.
		return this->cameradistance > that.cameradistance;
	}
};

  
そして、これらの値は以下の様にメインループの中でいじられています。

| 内容     | 説明                                                                                                                                |
| ------ | --------------------------------------------------------------------------------------------------------------------------------- |
| 粒子の消滅  | Particle.life変数が時間とともに徐々に減っていき、life < 0になったらcameradistance = -1.0にする。cameradistanceをもとにソートすると（SortParticles関数）後ろの方に追いやられ、描画されなくなる |
| 粒子の生成  | life < 0の粒子のうちいくつか（個数は経過時間によって決まる）を初期化する                                                                                          |
| 粒子の初期化 | lifeをPARTICLE\_LIFE\_SECONDSに設定し、頂点座標と粒子の色と移動速度をランダムの値と定数を用いて初期化する                                                                |
| 粒子の移動  | 等速直線運動を行う（position = speed \* dt）。運動後にcameradistanceを再計算する                                                                        |
| 粒子の描画  | life > 0の粒子の位置と色をGPUに転送するためのバッファにコピーして描画                                                                                          |