---
title: "【Processing】Arduinoで受信したMPU-6050の測定値の可視化"
emoji: ""
type: ""
topics: []
published: false
---

* [この記事の内容](#この記事の内容)
* [プログラム](#プログラム)
* [実行結果](#実行結果)

### この記事の内容

前回、MPU-6050を使って加速度と角速度のデータを取得する方法を紹介しました。

[shizenkarasuzon.hatenablog.com](https://shizenkarasuzon.hatenablog.com/entry/2019/02/16/162647)

今回は、このようにして取得したデータをProcessingを使って**グラフ化**してみます。  
  
### プログラム

Arduino側のプログラムと配線は、以下の記事を見ながら行ってください。  
[shizenkarasuzon.hatenablog.com](https://shizenkarasuzon.hatenablog.com/entry/2019/02/16/162647)

ここでは、Processing側のプログラムを紹介します。

import processing.serial.*;

static final int GYRO = 1;
static final int ACCEL = 2;
static final int MAG = 3;
static final int ROTATION = 4;

Serial myPort;

float gx, gy, gz;
float ax, ay, az;

graphMonitor GyroGraph;
graphMonitor AccelGraph;
graphMonitor MagGraph;
graphMonitor RotateGraph;

void setup(){
  size(800, 600);

  myPort = new Serial(this, "COM4", 115200); 
 //「COM4」とは、Arduinoが接続されているCOMポート番号をさします。
 // エラーが出る場合は、デバイスマネージャ等からArduinoのCOMポート番号を調べて書き換えてください。

  frameRate(30);
  smooth();
  myPort.bufferUntil('\n');
  GyroGraph   = new graphMonitor("gyro",  100, 50,  500, 150);
  AccelGraph  = new graphMonitor("accel", 100, 280, 500, 150);
}

void draw(){
  background(0);
  text(str(gx),100,100);
  GyroGraph.graphDraw(gx, gy, gz);
  AccelGraph.graphDraw(ax, ay, az);
}

void serialEvent(Serial myPort){
  String myString = myPort.readStringUntil('\n');

  if (myString != null) {
    myString = trim(myString);
  
    float sensors[] = float(split(myString, ','));
    if (sensors.length > 5) {
        ax = sensors[0];
        ay = sensors[1];
        az = sensors[2];
        gx = sensors[3];
        gy = sensors[4];
        gz = sensors[5];
    }  
  }
}

class graphMonitor {
    String TITLE;
    int X_POSITION, Y_POSITION;
    int X_LENGTH, Y_LENGTH;
    float [] y1, y2, y3;
    float maxRange;
    graphMonitor(String _TITLE, int _X_POSITION, int _Y_POSITION, int _X_LENGTH, int _Y_LENGTH) {
      TITLE = _TITLE;
      X_POSITION = _X_POSITION;
      Y_POSITION = _Y_POSITION;
      X_LENGTH   = _X_LENGTH;
      Y_LENGTH   = _Y_LENGTH;
      y1 = new float[X_LENGTH];
      y2 = new float[X_LENGTH];
      y3 = new float[X_LENGTH];
      for (int i = 0; i < X_LENGTH; i++) {
        y1[i] = 0;
        y2[i] = 0;
        y3[i] = 0;
      }
    }

    void graphDraw(float _y1, float _y2, float _y3) {
      y1[X_LENGTH - 1] = _y1;
      y2[X_LENGTH - 1] = _y2;
      y3[X_LENGTH - 1] = _y3;
      for (int i = 0; i < X_LENGTH - 1; i++) {
        y1[i] = y1[i + 1];
        y2[i] = y2[i + 1];
        y3[i] = y3[i + 1];
      }
      maxRange = 1;
      for (int i = 0; i < X_LENGTH - 1; i++) {
        maxRange = (abs(y1[i]) > maxRange ? abs(y1[i]) : maxRange);
        maxRange = (abs(y2[i]) > maxRange ? abs(y2[i]) : maxRange);
        maxRange = (abs(y3[i]) > maxRange ? abs(y3[i]) : maxRange);
      }

      pushMatrix();

      translate(X_POSITION, Y_POSITION);
      fill(240);
      stroke(130);
      strokeWeight(1);
      rect(0, 0, X_LENGTH, Y_LENGTH);
      line(0, Y_LENGTH / 2, X_LENGTH, Y_LENGTH / 2);

      textSize(25);
      fill(255);
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
        stroke(255, 0, 255);
        line(i, y2[i] * (Y_LENGTH / 2) / maxRange, i + 1, y2[i + 1] * (Y_LENGTH / 2) / maxRange);
        stroke(0, 0, 0);
        line(i, y3[i] * (Y_LENGTH / 2) / maxRange, i + 1, y3[i + 1] * (Y_LENGTH / 2) / maxRange);
      }
      popMatrix();
    }
}

上の二つのプログラムを同じ場所に保存してください。

このようになっていればOKです。  
![f:id:pythonjacascript:20190216163555j:plain](/images/ppythonjacascript2019021620190216163555.jpg "f:id:pythonjacascript:20190216163555j:plain")  
私は、一つ目のコードを「processing.pde」として保存し、2つ目のコードを「graph.pde」として保存しています。  
  
### 実行結果

**ArduinoをUSBでPCに接続した後に、**上のプログラムを起動します。

すると、このような画面が表示されるはずです。  
![f:id:pythonjacascript:20190216163906j:plain](/images/ppythonjacascript2019021620190216163906.jpg "f:id:pythonjacascript:20190216163906j:plain")  
上のグラフが三軸加速度のデータを示しています。下のグラフはジャイロセンサーの値です。