---
title: "【Raspberry Pi】MPU-6050を使って姿勢角算出（C言語編）"
emoji: ""
type: ""
topics: undefined
published: false
---

* [この記事の内容](#この記事の内容)
* [ハードウェア](#ハードウェア)
* [プログラム](#プログラム)
* [実行方法](#実行方法)
* [実行結果](#実行結果)
* [姿勢角の算出方法](#姿勢角の算出方法)

### この記事の内容

前回、MPU-6050を使って加速度とジャイロの計測データを取得して、それをディスプレイに表示しました。  
[shizenkarasuzon.hatenablog.com](https://shizenkarasuzon.hatenablog.com/entry/2019/03/06/114248)

今回は、そうして得られた値から姿勢角（ピッチ角とロール角）を算出してみます。  
  
  
### ハードウェア

以下の四本を接続してください。  
![f:id:pythonjacascript:20190304005009j:plain](/images/ppythonjacascript2019030420190304005009.jpg "f:id:pythonjacascript:20190304005009j:plain")  

| ラズパイピン番号 | MPU6050 |
| -------- | ------- |
| 1pin     | VCC     |
| 3pin     | SDA     |
| 5pin     | SCL     |
| 6pin     | GND     |

上のようにつなぐと、MPU6050に電力が供給され、MPU6050上の赤色LEDが光ります。  
![f:id:pythonjacascript:20190304005137j:plain](/images/ppythonjacascript2019030420190304005137.jpg "f:id:pythonjacascript:20190304005137j:plain")  

### プログラム

#include <stdio.h>
#include <stdint.h>
#include <wiringPi.h>
#include <wiringPiI2C.h>
#include <math.h>

#define ACCEL_X_OUT 0x3b
#define ACCEL_Y_OUT 0x3d
#define ACCEL_Z_OUT 0x3f
#define TEMP_OUT 0x41
#define GYRO_X_OUT 0x43 
#define GYRO_Y_OUT 0x45
#define GYRO_Z_OUT 0x47

#define PWR_MGMT_1 0x6B  //PWR_MGMT_1
#define PWR_MGMT_2 0x6c  //PWR_MGMT_2
#define DEV_ADDR 0x68    // I2C

#define RAD_TO_DEG 57.324

float get2data(int fd, unsigned int reg){
  unsigned int h_value = wiringPiI2CReadReg8(fd, reg);
  unsigned int l_value = wiringPiI2CReadReg8(fd, reg+1);
  float value = (h_value << 8) + l_value;
  if(value>=32768) return value-65534;  //32768=0x8000, 65534 = 0xFFFF 
  else return value;
}


void mainloop(int fd){
  float pitch, roll;
  float ax,ay,az;

  while(1){
    ax = get2data(fd, ACCEL_X_OUT)/16384.0;
    ay = get2data(fd, ACCEL_Y_OUT)/16384.0;
    az = get2data(fd, ACCEL_Z_OUT)/16384.0;
    //printf("gx=%.2f, gy=%.2f, gz=%.2f, ax=%.2f, ay=%.2f, az=%.2f\n", gx,gy,gz,ax,ay,az);

　 pitch = atan(ay / az) * RAD_TO_DEG;
    roll = atan(-ax / sqrtf(ay*ay + az*az)) * RAD_TO_DEG;
    printf("pitch=%.2f, roll=%.2f\n", pitch,roll);
  }
}


int main(){
  int fd = wiringPiI2CSetup(DEV_ADDR);
  if (fd == -1){
    printf("ERROR : No device!!");
	 return 0;
  }
  wiringPiI2CReadReg8 (fd, PWR_MGMT_1);
  wiringPiI2CWriteReg16(fd, PWR_MGMT_1, 0);

  mainloop(fd);
}
  
  
### 実行方法

まず、↑のプログラムを適当な名前で保存します。**拡張子は「.c」**です。

ここでは、デスクトップ（**/home/pi/デスクトップ/**）に「6050\_2.c」という名前で保存しているていで話を進めます。

次に、cd命令でプログラムを保存したディレクトリに移動します。

cd /home/pi/デスクトップ/

![f:id:pythonjacascript:20190306123112j:plain](/images/ppythonjacascript2019030620190306123112.jpg "f:id:pythonjacascript:20190306123112j:plain")  

次に、以下のコマンドを実行してプログラムをコンパイルします。

gcc -Wall -o 6050_2 6050_2.c -lwiringPi -lm

↑の「6050\_2.c」の部分は保存したプログラムファイル名に書き換えてください。  
前回とは異なって、新しく「**\-lm**」という3文字が増えましたが、これは今回の場合、**math.h**を使用するというオプションを意味します。

gccでは，math.hが巨大なため、オプションなしでコンパイルすると数学ライブラリをリンクせず、

#include <math.h>

でエラーが出てしまうのです。

  
何もエラーが出なければコンパイル成功です。Cファイルと同じディレクトリに「6050\_2」という紙飛行機アイコンの実行ファイルができているはずです。

  
最後に、

sudo ./6050_2

と打つと、プログラムが開始されます。  
  
  
### 実行結果

正しく実行されると、以下のようにピッチ角とロール角が表示されるはずです。  
![f:id:pythonjacascript:20190306164445j:plain](/images/ppythonjacascript2019030620190306164445.jpg "f:id:pythonjacascript:20190306164445j:plain")  
  
  
### 姿勢角の算出方法

姿勢角は以下の方法で算出しています。

  
姿勢角には、**Pitch（ピッチ）、Roll（ロール）、Yaw（ヨー）**の3種類があります。

この記事では、**ピッチ角とロール角**を求めてみます。

それぞれ、以下の数式で表すことができます。

ロール角![\phi](https://chart.apis.google.com/chart?cht=tx&chl=%5Cphi)について  
![\phi = atan(\dfrac{a_y}{a_z})](https://chart.apis.google.com/chart?cht=tx&chl=%5Cphi%20%3D%20atan%28%5Cdfrac%7Ba_y%7D%7Ba_z%7D%29)

ピッチ角![\psi](https://chart.apis.google.com/chart?cht=tx&chl=%5Cpsi)について  
![\psi = atan(\dfrac{-a_x}{a_y sin(\phi) + a_z cos(\phi)})](https://chart.apis.google.com/chart?cht=tx&chl=%5Cpsi%20%3D%20atan%28%5Cdfrac%7B-a_x%7D%7Ba_y%20sin%28%5Cphi%29%20%2B%20a_z%20cos%28%5Cphi%29%7D%29)

![a_x](https://chart.apis.google.com/chart?cht=tx&chl=a_x)、![a_x](https://chart.apis.google.com/chart?cht=tx&chl=a_x)、![a_x](https://chart.apis.google.com/chart?cht=tx&chl=a_x)はそれぞれ、![x](https://chart.apis.google.com/chart?cht=tx&chl=x)軸、![y](https://chart.apis.google.com/chart?cht=tx&chl=y)軸、![z](https://chart.apis.google.com/chart?cht=tx&chl=z)軸の加速度を表します。

この部分を実装すると、このようになります。

#include <math.h>
#define RAD_TO_DEG 57.324
//（中略）
pitch = atan(ay / az) * RAD_TO_DEG;
roll = atan(-ax / sqrtf(ay*ay + az*az)) * RAD_TO_DEG;

**57.324**というのは、![\dfrac{360}{2\pi}](https://chart.apis.google.com/chart?cht=tx&chl=%5Cdfrac%7B360%7D%7B2%5Cpi%7D)で、弧度法（rad）の数値を度数法（°）に変更にするのに使用しています。