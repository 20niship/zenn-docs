---
title: "【Arduino】MPU-6050を使って姿勢角を算出　（フィルターなし）"
emoji: ""
type: ""
topics: []
published: false
---

前回、MPU-6050を使って角速度と加速度のデータを取得するプログラムを書きました。  
[shizenkarasuzon.hatenablog.com](https://shizenkarasuzon.hatenablog.com/entry/2019/02/16/162647)

今回は、それを使って姿勢角を求めたいと思います。

* [1.方法](#1方法)
* [実行環境](#実行環境)
* [実行結果](#実行結果)
* [プログラム（全文）](#プログラム全文)

### 1.方法

姿勢角には、**Pitch（ピッチ）、Roll（ロール）、Yaw（ヨー）**の3種類があります。

この記事では、**ピッチ角とロール角**を求めてみます。

それぞれ、以下の数式で表すことができます。

ロール角![\phi](https://chart.apis.google.com/chart?cht=tx&chl=%5Cphi)について  
![\phi = atan(\dfrac{a_y}{a_z})](https://chart.apis.google.com/chart?cht=tx&chl=%5Cphi%20%3D%20atan%28%5Cdfrac%7Ba_y%7D%7Ba_z%7D%29)

ピッチ角![\psi](https://chart.apis.google.com/chart?cht=tx&chl=%5Cpsi)について  
![\psi = atan(\dfrac{-a_x}{a_y sin(\phi) + a_z cos(\phi)})](https://chart.apis.google.com/chart?cht=tx&chl=%5Cpsi%20%3D%20atan%28%5Cdfrac%7B-a_x%7D%7Ba_y%20sin%28%5Cphi%29%20%2B%20a_z%20cos%28%5Cphi%29%7D%29)

![a_x](https://chart.apis.google.com/chart?cht=tx&chl=a_x)、![a_x](https://chart.apis.google.com/chart?cht=tx&chl=a_x)、![a_x](https://chart.apis.google.com/chart?cht=tx&chl=a_x)はそれぞれ、![x](https://chart.apis.google.com/chart?cht=tx&chl=x)軸、![y](https://chart.apis.google.com/chart?cht=tx&chl=y)軸、![z](https://chart.apis.google.com/chart?cht=tx&chl=z)軸の加速度を表します。

この部分をArduinoのプログラムに実装すると、このようになります。

double roll  = atan2(acc_y, acc_z) * RAD_TO_DEG;
double pitch = atan(-acc_x / sqrt(acc_y * acc_y + acc_z * acc_z)) * RAD_TO_DEG;

**RAD\_TO\_DEG**というのは、![\dfrac{360}{2\pi}](https://chart.apis.google.com/chart?cht=tx&chl=%5Cdfrac%7B360%7D%7B2%5Cpi%7D)を表す定数で、弧度法（rad）の数値を度数法（°）に変更にするのに使用しています。

これを実際に埋め込んだプログラム全文は、この記事の一番下に書いています。  
  
### 実行環境

回路図等は、以下のサイトをご覧ください。  
[shizenkarasuzon.hatenablog.com](https://shizenkarasuzon.hatenablog.com/entry/2019/02/16/162647)

プログラム全文は、この記事の一番下に載せています。  
  
  
### 実行結果

![f:id:pythonjacascript:20190216170934j:plain](/images/ppythonjacascript2019021620190216170934.jpg "f:id:pythonjacascript:20190216170934j:plain")  
このように、シリアルモニタを通して姿勢角のデータを算出することに成功しました。  
![f:id:pythonjacascript:20190216165438j:plain](/images/ppythonjacascript2019021620190216165438.jpg "f:id:pythonjacascript:20190216165438j:plain")

Processingで可視化した結果です。  
![f:id:pythonjacascript:20190216165429j:plain](/images/ppythonjacascript2019021620190216165429.jpg "f:id:pythonjacascript:20190216165429j:plain")

Processingのプログラムは[ここ](https://shizenkarasuzon.hatenablog.com/entry/2019/02/16/163954)を見ながら作ってください。  
  
### プログラム（全文）

#include <Wire.h>
#define MPU6050_WHO_AM_I     0x75  // R
#define MPU6050_PWR_MGMT_1   0x6B  // R/W
#define MPU_ADDRESS  0x68


// デバイス初期化時に実行される
void setup() {
  Wire.begin();
  Serial.begin(115200); //115200bps

  Wire.beginTransmission(MPU_ADDRESS);
  Wire.write(MPU6050_PWR_MGMT_1);  //MPU6050_PWR_MGMT_1レジスタの設定
  Wire.write(0x00);
  Wire.endTransmission();
}


void loop() {
  Wire.beginTransmission(0x68);
  Wire.write(0x3B);
  Wire.endTransmission(false);
  Wire.requestFrom(0x68, 14, true);
  while (Wire.available() < 14);
  int16_t axRaw, ayRaw, azRaw, gxRaw, gyRaw, gzRaw, Temperature;

  axRaw = Wire.read() << 8 | Wire.read();
  ayRaw = Wire.read() << 8 | Wire.read();
  azRaw = Wire.read() << 8 | Wire.read();
  Temperature = Wire.read() << 8 | Wire.read();
  gxRaw = Wire.read() << 8 | Wire.read();
  gyRaw = Wire.read() << 8 | Wire.read();
  gzRaw = Wire.read() << 8 | Wire.read();

  // 加速度値を分解能で割って加速度(G)に変換する
  float acc_x = axRaw / 16384.0;  //FS_SEL_0 16,384 LSB / g
  float acc_y = ayRaw / 16384.0;
  float acc_z = azRaw / 16384.0;

  // 角速度値を分解能で割って角速度(degrees per sec)に変換する
  float gyro_x = gxRaw / 131.0;  // (度/s)
  float gyro_y = gyRaw / 131.0;
  float gyro_z = gzRaw / 131.0;

  double roll  = atan2(acc_y, acc_z) * RAD_TO_DEG;
  double pitch = atan(-acc_x / sqrt(acc_y * acc_y + acc_z * acc_z)) * RAD_TO_DEG;
  
  Serial.print(roll);  Serial.print(",");
  Serial.print(pitch);  Serial.println("");
}