---
title: "【Arduino 】MPU-6050を使って姿勢角を算出　（MadgwickFilterを使用）"
emoji: "🤖"
type: "tech"
topics: []
published: false
---


# この記事の内容

前回、MPU-6050を使ってピッチ角とロール角の値を求めました。  

[shizenkarasuzon.hatenablog.com](https://shizenkarasuzon.hatenablog.com/entry/2019/02/16/170706)

しかし、これにはある問題があります。

例えば、ドローンに上の補正なしのプログラムを適応した場合、センサーの振動によって姿勢角のデータが下のグラフのように大幅な誤差が発生します。

![f:id:pythonjacascript:20190216173742j:plain](/images/ppythonjacascript2019021620190216173742.jpg)

上のグラフは、スマホのバイブレーション機能機能を利用して、センサーを小気味に振動させた状態で姿勢角測定を行ったときの測定値です。  
ピッチ角とロール角の値をグラフ化しています。横軸が時間、縦軸が姿勢角（度）です。

このグラフを見ると、**最大で約30°の誤差**が発生していることがわかります。

  
ドローンの場合、こんな誤差ばっかりの姿勢角をもとにフィードバック制御を行えば、確実に**制御不能**になります（**実体験**）。

  
そこで、加速度と角速度のデータをもとに適切な（誤差の少ない）姿勢角を求めなければなりません。

...ということで、登場するのが「**Madgwickフィルター**」です。

  
この記事では、「**Madgwickフィルター**」というフィルターを利用して、より正確な姿勢角算出を行います。  
  
  
# ライブラリのインストール

GitHubに、ちょうどMadgwickフィルターのライブラリがあったので、そちらを使いました。  
[github.com](https://github.com/arduino-libraries/MadgwickAHRS)
  
  
ライブラリの追加方法は以下の通り。

➀「Download ZIP」のボタンを押してダウンロードする。  
②Arduino IDEを起動して、「スケッチ」→「ライブラリをインクルード」→「zip形式のライブラリをインクルード」を選択  
[![/images/ppythonjacascript2018090520180905044117.jpg](/images/ppythonjacascript2018090520180905044117.jpg)](/images/ppythonjacascript2018090520180905044117.jpg)

  
詳しくは、こちらのサイトをご覧ください。  
[shizenkarasuzon.hatenablog.com](https://shizenkarasuzon.hatenablog.com/entry/2018/09/05/051335)  
  
  
# Madgwickフィルターの実装

以下の3ステップで実装します。  
  
#### （1）ライブラリを読み込む
```cpp
#include <MadgwickAHRS.h>
Madgwick MadgwickFilter;
```

この2行でライブラリと、その中のMadgwick クラスを読み込みます。

そして、
```cpp
 MadgwickFilter.begin(100); //100Hz
```

でサンプリング周波数を設定します。これらの関数は一回実行すればいいので、setup()関数の中に書きます。  
  
  
## （2）計算処理を実行

以下のプログラムのように、取得した加速度と角速度のデータを入力すると、自動的に姿勢角の値が計算されます。
```cpp
MadgwickFilter.updateIMU(float gx, float gy, float gz, float ax, float ay, float az);
```

> ジャイロ値は\[deg/s\]。加速度値は、フィルターの中で正規化してますので「センサー生値」でも「G\[kg/ｍ・s^2\]」でもどちらでもいいはずです。

（[ArduinoのMadgwickライブラリの使い方 – 自作のいろいろ](https://garchiving.com/how-to-madgwick-library-of-arduino/)より引用）
  
  
磁束密度もセンサーから取得できる場合は、

MadgwickFilter.update(float gx, float gy, float gz, float ax, float ay, float az, float mx, float my, float mz);

のように書くこともできます。  
  
  
## （3）姿勢角の値を取得
```cpp
float pitch = MadgwickFilter.getPitch();
float roll    = MadgwickFilter.getRoll();
float yaw   = MadgwickFilter.getYaw();
```

このようにして、姿勢角の計算結果を取得することができます。**\-180度～180度の度数法**で帰ってきます。  

```cpp
float pitch = MadgwickFilter.getPitchRadians();
float roll    = MadgwickFilter.getRollRadians();
float yaw   = MadgwickFilter.getYawRadians();
```

と書くと、**![-\pi](https://chart.apis.google.com/chart?cht=tx&chl=-%5Cpi)～![\pi](https://chart.apis.google.com/chart?cht=tx&chl=%5Cpi)の弧度法**で姿勢角を取得することもできます。  
  
  
### プログラム全文
```cpp

#include <Wire.h>
#include <MadgwickAHRS.h>
Madgwick MadgwickFilter;

#define MPU6050_PWR_MGMT_1   0x6B
#define MPU_ADDRESS  0x68


void setup() {
  Wire.begin();
  Serial.begin(115200); //115200bps

  Wire.beginTransmission(MPU_ADDRESS);
  Wire.write(MPU6050_PWR_MGMT_1);  //MPU6050_PWR_MGMT_1レジスタの設定
  Wire.write(0x00);
  Wire.endTransmission();

  MadgwickFilter.begin(100); //100Hz
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

  /*
  //c.f. Madgwickフィルターを使わずに、PRY（pitch, roll, yaw）を計算
  double roll  = atan2(acc_y, acc_z) * RAD_TO_DEG;
  double pitch = atan(-acc_x / sqrt(acc_y * acc_y + acc_z * acc_z)) * RAD_TO_DEG;
  */
  
  //Madgwickフィルターを用いて、PRY（pitch, roll, yaw）を計算
  MadgwickFilter.updateIMU(gyro_x, gyro_y, gyro_z, acc_x, acc_y, acc_z);

  //PRYの計算結果を取得する
  float roll  = MadgwickFilter.getRoll();
  float pitch = MadgwickFilter.getPitch();
  float yaw   = MadgwickFilter.getYaw();

  Serial.print(roll);  Serial.print(",");
  Serial.print(pitch);  Serial.println("");
}
  
```
  
### 回路図

以下のように回路を組んで、ArduinoとMPU-6050をつなげてください。  
![f:id:pythonjacascript:20190216153159j:plain](/images/ppythonjacascript2019021620190216153159.jpg)  

Arduino内部で線がつながっているので、このように繋いでもOKです。  
![f:id:pythonjacascript:20190216162859j:plain](/images/ppythonjacascript2019021620190216162859.jpg "f:id:pythonjacascript:20190216162859j:plain")  
  
  
![f:id:pythonjacascript:20190216170934j:plain](/images/ppythonjacascript2019021620190216170934.jpg)  
回路を組んで、ArduinoをUSBでパソコンにつないだ時の様子です。  
  
  
### 結果：

暴投と同じような、振動に対する誤差テストを行います。

まず、以下のスマホアプリを使ってスマホを振動させ、その上にMPU-6050を固定します。  
[play.google.com](https://play.google.com/store/apps/details?id=com.nekobukiya.endlessvibrator)

そして、センサーが振動している状態で姿勢角算出を行います。

結果です。

フィルターなしの場合：  
![f:id:pythonjacascript:20190216173742j:plain](/images/ppythonjacascript2019021620190216173742.jpg)  
誤差：最大で30度

Madgwickフィルターを使った場合：  
![f:id:pythonjacascript:20190216174900j:plain](/images/ppythonjacascript2019021620190216174900.jpg)  
誤差：多くても数度？  
（左側の数値は（度°）で表記しています）

  
グラフを見れば一目瞭然ですが、ノイズに強くなりました！  
  
  
### 参考文献

[ArduinoのMadgwickライブラリの使い方 – 自作のいろいろ](https://garchiving.com/how-to-madgwick-library-of-arduino/)