---
title: "【M5Stack】第4回 M5Stack Fire内蔵のIMU（MPU6886）を使って姿勢角算出"
emoji: ""
type: ""
topics: []
published: false
---

このブログでは姿勢角算出プログラムは4記事目です。

[【Arduino】MPU-6050を使って姿勢角を算出 （フィルターなし） - とある科学の備忘録](https://shizenkarasuzon.hatenablog.com/entry/2019/02/16/170706)

[【Raspberry Pi】MPU-6050を使って姿勢角算出（C言語編） - とある科学の備忘録](https://shizenkarasuzon.hatenablog.com/entry/2019/03/06/171256)  
  
[【Raspberry Pi】MPU-6050を使って姿勢角算出（Python編） - とある科学の備忘録](https://shizenkarasuzon.hatenablog.com/entry/2019/03/06/163906)  
  
全部MPU6050使ってますが、今回使用するのはMPU6886です。  
  
* [IMUとは](#IMUとは)
* [プログラム（ライブラリ使用）](#プログラムライブラリ使用)
* [実行結果](#実行結果)
* [解説1](#解説1)
* [プログラム（自作ライブラリ）](#プログラム自作ライブラリ)  
   * [解説2](#解説2)  
   * [その他](#その他)  
   * [参考文献](#参考文献)

#### IMUとは

**IMU**（Inertial Measurement Unit、日本語では**慣性計測装置**）とは、3軸のジャイロと3方向の加速度を計測する装置のことです。ジャイロと加速度を計測できるIMUを**6軸IMU**ということもあります。  
同様にして、加速度センサとジャイロセンサに加えて3軸磁気センサを加えたものを**9軸IMU**といいます。

加速度とジャイロセンサがあればピッチ角とロール角を求めることはできるのですが、ヨー角を正確に求めるには地磁気センサを利用する必要があるそうです。

今回使用するMPU6886は加速度とジャイロのみの6軸センサです  
  
  
#### プログラム（ライブラリ使用）

// <M5Stack.h>をincludeする前に、IMUモジュールを#defineしておく
// この記事で使用するのは、M5Stack FireでIMUはMPU6886を使っているのでMPU6886を#defineしておく
#define M5STACK_MPU6886 
//#define M5STACK_MPU9250 
//#define M5STACK_MPU6050
// #define M5STACK_200Q

#include <M5Stack.h>

#define RAD_TO_DEG 57.324

float accX, accY, accZ;
float gyroX, gyroY, gyroZ;
float pitch, roll, yaw;
float my_pitch, my_roll, my_yaw;
float Temp;


void setup(){
  M5.begin();
  M5.Power.begin();
    
  M5.IMU.Init();

  M5.Lcd.fillScreen(BLACK);
  M5.Lcd.setTextColor(GREEN , BLACK);
  M5.Lcd.setTextSize(2);
}

void loop() {
  M5.IMU.getGyroData(&gyroX,&gyroY,&gyroZ);
  M5.Lcd.setCursor(0, 0);
  M5.Lcd.printf("gyro=(%5.1f, %5.1f, %5.1f)", gyroX, gyroY, gyroZ);
  
  M5.IMU.getAccelData(&accX,&accY,&accZ);
  M5.Lcd.setCursor(0, 50);
  M5.Lcd.printf("acc=(%5.1f, %5.1f, %5.1f)", accX, accY, accZ);
  
  M5.IMU.getAhrsData(&pitch,&roll,&yaw);
  M5.Lcd.setCursor(0, 100);
  M5.Lcd.printf("PRY=(%5.1f, %5.1f, %5.1f)", pitch, roll, yaw);

  //reference = https://myenigma.hatenablog.com/entry/2015/11/09/183738
  float my_roll = atan(accY / accZ) * RAD_TO_DEG;
  float my_pitch = atan(-accX / sqrtf(accY*accY + accZ*accZ)) * RAD_TO_DEG;
  M5.Lcd.setCursor(0, 150);
  M5.Lcd.printf("pitch = %5.1f, roll = %5.1f", my_pitch, my_roll);


  M5.IMU.getTempData(&Temp);
  M5.Lcd.setCursor(0, 200);
  M5.Lcd.printf("Temperature=%.2f C", Temp);
  
  delay(1);
}
  
  
#### 実行結果

IMUはM5Stackの中に内蔵されているので、配線などは何もいりません。めっちゃ楽です。

以下の画像のようになるはずです。  
![f:id:pythonjacascript:20200519120820j:plain](/images/ppythonjacascript2020051920200519120820.jpg "f:id:pythonjacascript:20200519120820j:plain")  
（温度の値がバグっています）  

PRYはgetAhrsData（M5Stack標準関数）で生成した姿勢角（![ pitch,  roll,  yaw](https://chart.apis.google.com/chart?cht=tx&chl=%20pitch%2C%20%20roll%2C%20%20yaw) ）の値を表示しています。

4行目のピッチ角とロール角は<https://myenigma.hatenablog.com/entry/2015/11/09/183738>のサイトに基づいて計算したpitchとrollの値を表示しています。

  
ロール角![\phi](https://chart.apis.google.com/chart?cht=tx&chl=%5Cphi)について  
![\phi = atan(\dfrac{a_y}{a_z})](https://chart.apis.google.com/chart?cht=tx&chl=%5Cphi%20%3D%20atan%28%5Cdfrac%7Ba_y%7D%7Ba_z%7D%29)

ピッチ角![\psi](https://chart.apis.google.com/chart?cht=tx&chl=%5Cpsi)について  
![\psi = atan(\dfrac{-a_x}{a_y sin(\phi) + a_z cos(\phi)})](https://chart.apis.google.com/chart?cht=tx&chl=%5Cpsi%20%3D%20atan%28%5Cdfrac%7B-a_x%7D%7Ba_y%20sin%28%5Cphi%29%20%2B%20a_z%20cos%28%5Cphi%29%7D%29)

![a_x](https://chart.apis.google.com/chart?cht=tx&chl=a_x)、![a_x](https://chart.apis.google.com/chart?cht=tx&chl=a_x)、![a_x](https://chart.apis.google.com/chart?cht=tx&chl=a_x)はそれぞれ、![x](https://chart.apis.google.com/chart?cht=tx&chl=x)軸、![y](https://chart.apis.google.com/chart?cht=tx&chl=y)軸、![z](https://chart.apis.google.com/chart?cht=tx&chl=z)軸の加速度を表します。

この部分をArduinoのプログラムに実装すると、このようになります。

double roll  = atan2(acc_y, acc_z) * RAD_TO_DEG;
double pitch = atan(-acc_x / sqrt(acc_y * acc_y + acc_z * acc_z)) * RAD_TO_DEG;

**RAD\_TO\_DEG**というのは、![\dfrac{360}{2\pi}](https://chart.apis.google.com/chart?cht=tx&chl=%5Cdfrac%7B360%7D%7B2%5Cpi%7D)を表す定数で、弧度法（rad）の数値を度数法（°）に変更にするのに使用しています。  
  
  
#### 解説1

解説と言えるほどのことは書いていないですが.......

まず、

M5.begin();
M5.Power.begin();
M5.IMU.Init();

で初期化します。

んであとは、  
float pitch, roll, yaw;  
  
float gyroX, gyroY, gyroZ;
M5.IMU.getGyroData(&gyroX,&gyroY,&gyroZ);

getGyroData関数でジャイロセンサーのデータを取得したり、  
  
float accX, accY, accZ;
M5.IMU.getAccelData(&accX,&accY,&accZ);

getAccelData関数で加速度センサのデータを取得したり、  
  
float my_pitch, my_roll, my_yaw;
M5.IMU.getAhrsData(&pitch,&roll,&yaw);

getAhrsData関数で姿勢角を取得したり、  
  
float Temp;
M5.IMU.getTempData(&Temp);

getTempDataで温度（℃）を取得したりしています。

ちなみに、M5Stackの動作保証温度は 0℃ \~ 40℃です。  
  
  
### プログラム（自作ライブラリ）

上のプログラムはM5Stackの標準ライブラリを使っていますが、ライブラリを自作すると以下のようになります。  
inoファイルと同じディレクトリに「myIMU.h」のような名前で保存してください

まずは、ヘッダーファイルから

// myIMU.h #include <math.h> #include <M5Stack.h> #include <Wire.h> #define MPU6886\_ADDRESS 0x68 //The slave address of the MPU-6886 is b110100X which is 7 bits long. #define MPU6886\_WHOAMI 0x75 // 設定用 #define MPU6886\_SMPLRT\_DIV 0x19 #define MPU6886\_CONFIG 0x1A #define MPU6886\_GYRO\_CONFIG 0x1B #define MPU6886\_ACCEL\_CONFIG 0x1C #define MPU6886\_ACCEL\_CONFIG2 0x1D #define MPU6886\_INT\_PIN\_CFG 0x37 #define MPU6886\_INT\_ENABLE 0x38 #define MPU6886\_ACCEL\_INTEL\_CTRL 0x69 #define MPU6886\_PWR\_MGMT\_1 0x6B #define MPU6886\_PWR\_MGMT\_2 0x6C // データ取得用 #define MPU6886\_TEMP\_DATA\_START 0x41 #define MPU6886\_ACCEL\_DATA\_START 0x3B #define MPU6886\_GYRO\_DATA\_START 0x43 class myIMU{private:void write\_byte(uint8\_t addr, uint8\_t data){ Wire.beginTransmission(MPU6886\_ADDRESS); Wire.write(addr); Wire.write(data); Wire.endTransmission(); }//Gyro Full Scale Select: 00 = ±250 dps. 01= ±500 dps. 10 = ±1000 dps. 11 = ±2000 dps. //ACCEL\_FS\_SEL\[1:0\] Accel Full Scale Select: ±2g (00), ±4g (01), ±8g (10), ±16g (11) /\*  ジャイロセンサの精度は、±250dps. ±500dps. ±1000dps. ±2000dps の4種類で選択できる。  変更方法はsetGyroScale関数を実行（0～3の値を指定）  gScaleはI2Cで得られた16bitのデータをfloat型に変換するためのもの。  例えば、±2000dpsに設定している場合、16bitのデータがb000 0000 0000 0000 → float=-2000に変換し、b1111 1111 1111 1111 → float=+2000に変換する  \*/ /\*  加速度センサの精度は、±2g, ±4g, ±8g, ±16g の4種類で選択できる。  変更方法はsetAccelScale関数を実行（0～3の値を指定）  accScaleはI2Cで得られた16bitのデータをfloat型に変換するためのもの。  例えば、±16gに設定している場合、16bitのデータがb000 0000 0000 0000 → float=-16.0に変換し、b1111 1111 1111 1111 → float=+16.0に変換する  \*/ float gScale, accScale;public:/\*  本当は、モジュール内部のデバイスとI2C通信する時にはWireではなくWire1を使うようなのだが、Wireでも動いたのでこのままにしてある  \*/ void init(){ Wire.begin();// 400 kHz Fast Mode I2C for communicating with all registers //I2Cのクロックの設定。無くても動いた // Wire.setClock(400000); //