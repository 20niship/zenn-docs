---
title: "【xBee】ｘBeeを使ってArduino二台を無線シリアル通信"
emoji: "🤖"
type: "tech"
topics: undefined
published: false
---

前回、ｘBeeの設定方法を紹介しました。

  
この記事では、ｘBeeを使ってArduinoのシリアル通信を無線化してみたいと思います。  
（有線のシリアル通信の方法はこちら）  
[shizenkarasuzon.hatenablog.com](https://shizenkarasuzon.hatenablog.com/entry/2018/07/24/225915)

  
### 1.用意するもの

・Arduino 2台  
・ｘBee 2台  
・配線用コード 数本  
  
  
### ハードウェア

下のような回路を2つ組んでください  
![f:id:pythonjacascript:20200524191720j:plain](/images/ppythonjacascript2020052420200524191720.jpg "f:id:pythonjacascript:20200524191720j:plain")  
（Arduinoは電源電圧が5Vで、xBeeは3.3Vで動いていますが、ロジックレベル変換ICを使わなくても一応上の回路で動きました）

くみ上げると、このようになります。  
![f:id:pythonjacascript:20180921020458j:plain:h300](/images/ppythonjacascript2018092120180921020458.jpg "f:id:pythonjacascript:20180921020458j:plain:h300")

### プログラム：

送信側と受信側でプログラムを二つ用意します。ハードウェアは両方同じなので、どっちにどのプログラムを書き込んでも構いません。  
送信側は、13ピンについているLEDのON/OFF命令を送信します。受信機はそのON/OFF命令に従って、受信機側ArduinoのLEDを点灯・消灯させます。  
もし、二つのArduinoのLEDが同時に点滅を繰り返していれば、シリアル通信ができている証拠です。  
  
### 送信側：

int LED = 13;
int count = 0;

void setup(){
  Serial.begin(9600);
  pinMode(LED, OUTPUT);
}

void loop(){
  count++;
  Serial.write(count);
  if((count % 2) == 1){ 
    digitalWrite(LED, HIGH);
  }else{
    digitalWrite(LED, LOW);
  }
  delay(1000);
}

#### 受信側：

int LED = 13;

void setup(){
  Serial.begin(9600);
  pinMode(LED, OUTPUT);
}

void loop(){
  while(Serial.available()){
    byte inChar = (byte)Serial.read();
    if((inChar % 2) == 1){ 
      digitalWrite(LED, HIGH);
    }else{
      digitalWrite(LED, LOW);
    }
    delay(10);
  }
}

### プログラムの注意：

setup関数の中で

Serial.begin(9600)

という一行がありますが、これはシリアル通信のビットレート（速さ）を表しています。  
なので、この値はｘBeeのX-CTUで設定した値と同じ数値に設定してください。ｘBeeのビットレートとArduinoのビットレートが異なると、正常に通信ができません。  
ｘBeeの設定方法は、  
[shizenkarasuzon.hatenablog.com](https://shizenkarasuzon.hatenablog.com/entry/2018/07/03/003320)  
を見てください。  
  
### 3\. 実行結果

二機のArduinoの13ピンのLEDがそれぞれ、同じタイミングで点滅していれば、二つのArduinoが同期していることの証拠になります。  
  
  
### 4\. 書き込みの時の注意

このプログラムをArduinoに書き込むときは、ｘBeeを外してください。PCとArduinoを繋いでいるUSBケーブルの配線は、そのままｘBeeのTX-RXピンにつながっています。なので、PCとArduinoが正常に通信できなくなり、書き込みエラーが出ます。