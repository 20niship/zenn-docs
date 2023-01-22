---
title: "【Processing 】折れ線グラフを書く"
emoji: ""
type: ""
topics: []
published: false
---

Processingで折れ線グラフを書いてみます。

### 実行結果

![f:id:pythonjacascript:20180731220514p:plain](/images/ppythonjacascript2018073120180731220514.png "f:id:pythonjacascript:20180731220514p:plain")  
右端から、赤色でグラフが描画されて、端までくると、自動的に画面がスクロールします。

### プログラム

float a;
int k;

graphMonitor testGraph;

void setup() {
  size(800, 600);
  frameRate(100);
  smooth();
  testGraph = new graphMonitor("Title", 100, 100, 500, 300);
}

void draw() {
  background(250);
  testGraph.graphDraw(a);

  a = sin(radians(k));
  k++;
}

class graphMonitor {
    String TITLE;
    int X_POS, Y_POS;
    int X_LENGTH, Y_LENGTH;
    float [] y1, y2, y3;
    float maxRange;
    graphMonitor(String _TITLE, int _X_POSITION, int _Y_POSITION, int _X_LENGTH, int _Y_LENGTH) {
      TITLE = _TITLE;
      X_POS = _X_POSITION;
      Y_POS = _Y_POSITION;
      X_LENGTH   = _X_LENGTH;
      Y_LENGTH   = _Y_LENGTH;
      y1 = new float[X_LENGTH];
      y2 = new float[X_LENGTH];
      y3 = new float[X_LENGTH];
      for (int i = 0; i < X_LENGTH; i++) {
        y1[i] = 0;
      }
    }

    void graphDraw(float data) {
      y1[X_LENGTH - 1] = data;
      for (int i = 0; i < X_LENGTH - 1; i++) {
        y1[i] = y1[i + 1];
        y2[i] = y2[i + 1];
        y3[i] = y3[i + 1];
      }
      maxRange = 1;
      for (int i = 0; i < X_LENGTH - 1; i++) {
        maxRange = (abs(y1[i]) > maxRange ? abs(y1[i]) : maxRange);
      }

      pushMatrix();

      translate(X_POS, Y_POS);
      fill(240);
      stroke(130);
      strokeWeight(1);
      rect(0, 0, X_LENGTH, Y_LENGTH);
      line(0, Y_LENGTH / 2, X_LENGTH, Y_LENGTH / 2);

      textSize(25);
      fill(60);
      textAlign(LEFT, BOTTOM);
      text(TITLE, 20, -5);
      textSize(22);
      textAlign(RIGHT);
      text(0, -5, Y_LENGTH / 2 + 7);
      text(nf(maxRange, 0, 1), -5, 18);
      text(nf(-1 * maxRange, 0, 1), -5, Y_LENGTH);

      translate(0, Y_LENGTH / 2);
      scale(1, -1);
      strokeWeight(1);
      for (int i = 0; i < X_LENGTH - 1; i++) {
        stroke(255, 0, 0);
        line(i, y1[i] * (Y_LENGTH / 2) / maxRange, i + 1, y1[i + 1] * (Y_LENGTH / 2) / maxRange);
      }
      popMatrix();
    }
}

  
ソース解読は、各自でお願いします。時間があれば記事追加します。  
  
### 参考文献

[Processingでリアルタイムグラフ表示 – 自作のいろいろ](https://garchiving.com/real-time-graph-by-proccesing/)