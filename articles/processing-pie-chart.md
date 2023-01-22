---
title: "【Processing】円グラフを書く"
emoji: "🤖"
type: "tech"
topics: ["processing"]
published: false
---

Processingで下のような円グラフを書く方法です。  
リアルタイムに値が変化します。  
![f:id:pythonjacascript:20180731223420p:plain](/images/ppythonjacascript2018073120180731223420.png "f:id:pythonjacascript:20180731223420p:plain")

数値を入力すると、その値に合わせて、水色の円弧のなす角（？）と、グラフの中の数字がリアルタイムで変化します。

  
プログラムです。

float Rotation_1;
int k = 0;

class circleMonitor {
    String TITLE;
    int X_POS, Y_POS;
    int X_LENGTH, Y_LENGTH;
    float diameter;
    
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
    
    circleMonitor(String _TITLE, int _X_POSITION, int _Y_POSITION, int Diameter){
      TITLE = _TITLE;
      X_POS = _X_POSITION;
      Y_POS = _Y_POSITION;
      diameter = Diameter;
   }

  void circleDraw(float value){
      pieChart(value);
      textSize(25);
      fill(60);
      text(TITLE, X_POS, Y_POS-diameter/2 - 15);
      
      textSize(22);
      fill(0);
      text(int(value), X_POS, Y_POS + diameter / 4);
  }

  void pieChart(float data) {
    strokeWeight(3);
    stroke(LINE_COLOR_R, LINE_COLOR_G, LINE_COLOR_B); // line color config
    fill(EMPTY_COLOR_R, EMPTY_COLOR_G, EMPTY_COLOR_B);
    
    ellipse(X_POS, Y_POS, diameter, diameter);
    
    fill(FILLED_COLOR_R, FILLED_COLOR_G, FILLED_COLOR_B);
    arc(X_POS, Y_POS, diameter, diameter, 0, radians(data *360 / VALUE_MAX)); 
  }
}


circleMonitor r1;

void setup(){
  size(500,300);
  frameRate(100);
  smooth();
  r1 = new circleMonitor("Title", 150, 150, 120);
}


void draw(){
  background(200,100,200);  
  r1.circleDraw(Rotation_1);
  Rotation_1 = (sin(radians(k)) + 1)* 50;
  k++;
}