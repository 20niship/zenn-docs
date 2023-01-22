---
title: "【Processing】メニューバーを作る"
emoji: ""
type: ""
topics: []
published: false
---

**メニューバー**（この写真の左端の青い部分）を Processing で作ります。

![f:id:pythonjacascript:20180813181845p:plain](/images/ppythonjacascript2018081320180813181845.png "f:id:pythonjacascript:20180813181845p:plain")  
（この写真は、筆者が作っているGUIアプリケーションのひとつ）  
  
* [1.メニューバーの機能](#1メニューバーの機能)
* [2.プログラムの概要](#2プログラムの概要)
* [2.メインのプログラム：](#2メインのプログラム)
* [3\. menuクラスのプログラム](#3-menuクラスのプログラム)

### 1.メニューバーの機能

1. 上の写真のように、メニューを左端に表示する。
2. それぞれのメニューをクリックすると、画面がそのメニューに切り替わる
3. 選択されているメニューバーだけ色を変えて、いまのメニューを判別可能に

というものです。詳しくは、下のコードを実行してみてください。  
  
### 2.プログラムの概要

menuクラスを使って実装しています。なので、コードはmenuクラス用と、メイン処理用の2つに分かれています。  
このプログラムを実行すると、「MENU\_0\_TITLE」、「MENU\_1\_TITLE」、「MENU\_2\_TITLE」、「MENU\_3\_TITLE」という3つのメニューが作られます。  
マウスでどれか一つのメニューをクリックすると、それに対応した描画関数（Draw0 \~ Draw3）が実行されます。  
  
### 2.メインのプログラム：

menu Menu;

void setup(){
  size(1400,802);
  textSize(25);

  frameRate(100);
  smooth();
  Menu = new menu(0, 0, 120, 802);  
}

void draw(){
  background(0);

  switch(Menu.menu){
    case 0: Draw1(); break;
    case 1: Draw2(); break;
    case 2: Draw3(); break;
    case 4: Draw4(); break;    
  }
  Menu.DrawMenu();
}

void mousePressed(){
  mouse_X = mouseX;
  mouse_Y = mouseY;  
  
  Menu.setMenu(mouseX, mouseY);
} 

void Draw0(){
  fill(255);
  text("menu = 0", 200, 20);
  //menu = 0の時の描画処理
}

void Draw1(){
  fill(255);
  text("menu = 1", 200, 20);
  //menu = 1の時の描画処理
}

void Draw2(){
  fill(255);
  text("menu = 2", 200, 20);
  //menu = 2の時の描画処理
}

void Draw3(){  
  fill(255);
  text("menu = 3", 200, 20);
    //menu = 3の時の描画処理
}

### 3\. menuクラスのプログラム

class menu {
    int X_POS, Y_POS;
    int X_LENGTH, Y_LENGTH;
    int menu = 0;
    int menu_num = 4;
    
    int COLOR_R1 = 43;
    int COLOR_G1 = 58;
    int COLOR_B1 = 222;
        
    int COLOR_R2 = 17;
    int COLOR_G2 = 29;
    int COLOR_B2 = 155;
    
    int CUURENT_MENU_R = 255;
    int CUURENT_MENU_G = 0;
    int CUURENT_MENU_B = 0;
    
    int height_per_menu = 0;
   
    String[] menu_names = {"MENU_0_TITLE", "MENU_1_TITLE", "MENU_2_TITLE", "MENU_3_TITLE"};
    
    menu(int _X_POSITION, int _Y_POSITION, int _X_LENGTH, int _Y_LENGTH){
      X_POS = _X_POSITION;
      Y_POS = _Y_POSITION;
      X_LENGTH   = _X_LENGTH;
      Y_LENGTH   = _Y_LENGTH;
      height_per_menu = int(Y_LENGTH / menu_num);
    }

    void DrawMenu(){
      int current_y = 0;
      textAlign(CENTER, CENTER);
      for(int i = 0; i< menu_num; i++){
        if(i == menu){
            fill(CUURENT_MENU_R, CUURENT_MENU_G, CUURENT_MENU_B);
        }else{
            if(i%2 == 0) fill(COLOR_R2, COLOR_G2, COLOR_B2);
            else fill(COLOR_R1, COLOR_G1, COLOR_B1);
        }
        
        rect(0, current_y, X_LENGTH, height_per_menu);
        fill(255);
        textSize(15);
        text(menu_names[i], X_LENGTH/2, current_y + height_per_menu/2);
        current_y += height_per_menu;
      }
    }
    
    int setMenu(int mouse_X, int mouse_Y){
      if(mouse_X < X_POS) return 0;
      if(mouse_X > X_POS + X_LENGTH) return 0;
      if(mouse_Y < Y_POS) return -1;
      if(mouse_Y > Y_POS + Y_LENGTH) return -1;
      menu = int( mouse_Y / height_per_menu);
      return menu;
    }
}