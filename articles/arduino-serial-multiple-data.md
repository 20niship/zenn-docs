---
title: "【Arduino】シリアル通信で複数のデータをやり取りする"
emoji: ""
type: ""
topics: []
published: false
---

Arduino同士、もしくはArduinoとPCの間で、複数のデータを送受信する方法（自己流）を紹介します。参考にしていただければ幸いです。  
  
* [1.方法](#1方法)
* [2.サンプルプログラム](#2サンプルプログラム)
* [シリアル通信で複数のデータを送受信する第二段](#シリアル通信で複数のデータを送受信する第二段)

### 1.方法

データの種類が一つの場合

Serial.write(data);  //送信側
data = Serial.read()  //受信側

のように書けばデータを送受信することができます。しかし、データの種類が複数になると、送られてきたデータが何を表しているのかが分からなければなりません。例えば、加速度センサーの値とジャイロセンサーの値という二つのデータをやり取りするとき、加速度センサーのデータを受信しても、それをジャイロセンサーの値と誤解してしまい、間違った処理をしてしまう、ということも考えられます。

そこで、データそのものを送る前に、データの種類（今から送られてくるデータが、加速度を表すのかジャイロを表すのか）を送信すればいいのではないか？と考えました。  
  
  
### 2.サンプルプログラム

この記事の一番下に載せています。このプログラムは、送信と受信両方できるプログラムです。  
送信用関数は「Send\_Data()」、受信用の関数は「serialEvent()」です。

データ送受信の形式は、

> **（データの種類) , (データ本体）\\n**

です。

serialEvent() 内の 

myString = readStringUntil("\n")

は、送られてきたデータを "\\n"（改行）まで受信し続ける関数です。そして、戻り値は受信したデータなので、myString には

> **（データの種類) , (データ本体）\\n**

の形でデータが代入されます。

そして、それをsplit関数で、データの種類とデータ本体に分け、適切な変数にデータを格納することで、データ受信完了、というアルゴリズムです。

split関数は、文字列を特定の文字（以下の場合は”, "）で区切る関数ですが、Arduino IDE にはsplit関数がないので、以下のサイトを参考にしてsplit関数を作成しました。  
[【Arduino】文字列を区切り文字で分割(split関数) | アルゴリズム雑記](https://algorithm.joho.info/arduino/string-split-delimiter/)

  
#define GYRO_X 1
#define GYRO_Y 2
#define GYRO_Z 3

// #define データの種類 定数　という形で、データの種類を定義していきます。

float cmds[2];// 分割された文字列を格納する配列 

float Gyro_X = 0;  //受信したデータはここに格納されます。
float Gyro_Y = 0;
float Gyro_Z = 0;

void setup(){
  Serial.begin(115200);
}

void loop(){
  //このように、SendData(データの種類(定数）, データ本体）で、データを送信
  //送信できるデータは、一回につき1バイトです。
  SendData(GYRO_Y, Gyro_Y);   
  delay(50);
}

void SendData(int kind, float data){  
  //データ一つを送信する関数です
  Serial.print(kind);
  Serial.print(","); 
  Serial.print(int(data));
  Serial.println("\n");
}

int split(String data, char delimiter, float *dst){
    int index = 0;
    int arraySize = (sizeof(data)/sizeof((data)[0]));  
    int datalength = data.length();
    for (int i = 0; i < datalength; i++) {
        char tmp = data.charAt(i);
        if ( tmp == delimiter ) {
            index++;
            if ( index > (arraySize - 1)) return -1;
        }
        else dst[index] += float(tmp);
    }
    return (index + 1);
}

void serialEvent(){
  //データを1つ受信する関数（割り込み処理）です。
  String myString = Serial.readStringUntil('\n');
  int index = split(myString, ',', cmds);
  if(Serial.available() >= 2){ 
     switch(int(cmds[0])){
       case GYRO_X:
       Gyro_X = float(cmds[1]);
       break;

       case GYRO_Y:
         Gyro_Y = float(cmds[1]);
       break;
             
       case GYRO_Z:
         Gyro_Z = float(cmds[1]);
       break;
           
     }
  }
}
  
  
### シリアル通信で複数のデータを送受信する第二段

[shizenkarasuzon.hatenablog.com](https://shizenkarasuzon.hatenablog.com/entry/2018/09/21/024721)