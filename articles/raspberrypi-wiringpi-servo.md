---
title: "【Raspberry Pi】WiringPiを使ってC言語でサーボモーター制御"
emoji: ""
type: ""
topics: undefined
published: false
---

下の記事のC言語編です。  
[shizenkarasuzon.hatenablog.com](https://shizenkarasuzon.hatenablog.com/entry/2019/03/04/002150)

PWM出力をC言語を使って行い、サーボモータを制御します。  
  
### 配線

![f:id:pythonjacascript:20190309165632j:plain](/images/ppythonjacascript2019030920190309165632.jpg "f:id:pythonjacascript:20190309165632j:plain")

上のように配線してください。  
サーボモータからは3本線が出ていますが、各線の役割は以下の通りです。

| コード色     | 役割      |
| -------- | ------- |
| 黒線       | GND（0V） |
| 赤線       | 電源+（5V） |
| オレンジ（黄色） | 信号線     |

よって上のような配線になるのですが、上の配線はあくまでサーボモータを回すのに必要な電流量がRaspberryPiが供給できる電流量を上回らない時だけのものです。  
今回はマイクロサーボを使っておりラズパイ側の電源でも十分電気を供給できますが、サーボは大きくなると数A食うものもあり、データシートを見て絶対最大定格を確認することをお勧めします。  
  
  
### プログラム

#include <stdio.h>
#include <stdint.h>
#include <wiringPi.h>

#define SERVO_PIN 18

int main(){
  if (wiringPiSetupGpio() == -1) {
     printf("cannot setup gpio.");
    return 1;
  }

  pinMode(SERVO_PIN, PWM_OUTPUT);
  pwmSetMode(PWM_MODE_MS);
  pwmSetClock(400);
  pwmSetRange(1024);
   
   int i = 0;
   while(i<10){
     pwmWrite(SERVO_PIN, i*10);
      delay(500);
      i++;
}
  return 0;
}
  
  
### 実行結果

上のプログラムを実行すると、このようにサーボモータが回転します。  
![f:id:pythonjacascript:20190225234425g:plain](/images/ppythonjacascript2019022520190225234425.gif "f:id:pythonjacascript:20190225234425g:plain")  
（↑の動画はイメージです。実際には[このプログラム](https://shizenkarasuzon.hatenablog.com/entry/2019/03/04/002150)で動かしています。） 