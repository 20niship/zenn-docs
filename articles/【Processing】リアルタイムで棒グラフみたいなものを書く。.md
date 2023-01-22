---
title: "【Processing】リアルタイムで棒グラフみたいなものを書く。"
emoji: ""
type: ""
topics: []
published: false
---

こんなものをProcessingで表現してみました。

![f:id:pythonjacascript:20180731221107p:plain](/images/ppythonjacascript2018073120180731221107.png "f:id:pythonjacascript:20180731221107p:plain")

0～100までの値を入力すると、それに合わせて、水色の部分の高さと、中央の数字が変化します。

  
プログラム

float Rotation_1;
int k = 0;

class barMonitor {
    String TITLE;
    int X_POS, Y_POS;
    int X_LENGTH, Y_LENGTH;
    float VALUE_MAX = 100;
    
    int EMPTY_COLOR_R = 100;
    int EMPTY_COLOR_G = 100;
    int EMPTY_COLOR_B = 100;
    
    int FILLED_COLOR_R = 0;
    int FILLED_COLOR_G = 255;
    int FILLED_COLOR_B = 255;
    
    int LINE_COLOR_R = 100;
    int LINE_COLOR_G = 100;
    int LINE_COLOR_B = 100;
    
    barMonitor(String _TITLE, int _X_POSITION, int _Y_POSITION, int _X_LENGTH, int _Y_LENGTH){
      TITLE = _TITLE;
      X_POS = _X_POSITION;
      Y_POS = _Y_POSITION;
      X_LENGTH   = _X_LENGTH;
      Y_LENGTH   = _Y_LENGTH;
    }

  void barDraw(float value) {
    strokeWeight(3);
    colorMode(RGB,256);
    stroke(LINE_COLOR_R, LINE_COLOR_G, LINE_COLOR_B); // line color config
    fill(EMPTY_COLOR_R, EMPTY_COLOR_G, EMPTY_COLOR_B);  //inter color config
    rect(X_POS, Y_POS, X_LENGTH, Y_LENGTH);  //retangle draw

    strokeWeight(2);
    fill(FILLED_COLOR_R, FILLED_COLOR_G, FILLED_COLOR_B);
    if(value > VALUE_MAX){
      value = VALUE_MAX;
    }
    
    int velue_l = int(Y_LENGTH * value / VALUE_MAX);
    rect(X_POS, Y_POS + Y_LENGTH - velue_l, X_LENGTH, velue_l);
    
    
    textSize(25);
    fill(60);
    textAlign(CENTER, BOTTOM);
    text(TITLE, X_POS, Y_POS);
    
    textSize(22);
    fill(0);
    textAlign(CENTER);
    text(int(value), X_POS + X_LENGTH / 2, Y_POS + Y_LENGTH / 2);
  }
}


barMonitor bar_r1;

void setup(){
  size(500,300);
  frameRate(100);
  smooth();
  bar_r1 = new barMonitor("Title", 100, 30,  35, 120);
}

void draw(){
  background(200,100,200);  
  bar_r1.barDraw(Rotation_1);
  Rotation_1 = (sin(radians(k)) + 1)* 50;
  k++;
}