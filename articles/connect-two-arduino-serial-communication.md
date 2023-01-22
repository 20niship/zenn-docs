---
title: "【Arduino】２台のAruduinoをシリアル通信させる"
emoji: "🤖"
type: "tech"
topics: ["arduino","cpp"]
published: false
---

タイトルの通りです。Arduino同士をシリアル通信します。

* [1\. ハードウェア](#1-ハードウェア)
* [2\. ソフトウェア](#2-ソフトウェア)
* [送信側：](#送信側)  
   * [受信側：](#受信側)
* [3\. 実行結果](#3-実行結果)
* [4\. 書き込みの時の注意](#4-書き込みの時の注意)

### 1\. ハードウェア

次の図のような回路を組みます。下の図の2本の配線はシリアル通信用なので、Arduinoが電源につながっていない時は、別途に電源用の配線が必要になります。  
![f:id:pythonjacascript:20180724225759p:plain](/images/ppythonjacascript2018072420180724225759.png "f:id:pythonjacascript:20180724225759p:plain")

### 2\. ソフトウェア

送信側と受信側でプログラムを二つ用意します。ハードウェアは両方同じなので、どっちにどのプログラムを書き込んでも構いません。

### 送信側：

```cpp
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
```

#### 受信側：

```cpp
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
```

### 3\. 実行結果

二機のArduinoの13ピンのLEDがそれぞれ、同じタイミングで点滅していれば、二つのArduinoが同期していることの証拠になります。  
  
  
### 4\. 書き込みの時の注意

このプログラムをArduinoに書き込むときは、Arduino同士を繋いでいるケーブルを外してください。PCとArduinoを繋いでいるUSBケーブルの配線は、そのままTX-RXピンにつながっているため、PCとArduinoが正常に通信できなくなり、書き込みエラーが出ます。