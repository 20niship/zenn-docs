---
title: "【Arduino】PmodNAVをつかって加速度などを読み取る"
emoji: "🤖"
type: "tech"
topics: []
published: false
---

この章では、PmodNAVというセンサーを使います。それがこちらです。

![f:id:pythonjacascript:20180724230134p:plain](/images/ppythonjacascript2018072420180724230134.png "f:id:pythonjacascript:20180724230134p:plain")

このPmodNavは、加速度、ジャイロ、方向（magnetic field）、気圧センサーが一つになっているモジュールです。  
ArduinoとはSPI通信を行い、それぞれのセンサーのデータを送ります。そして、そのデータをPCにシリアル通信で送り、シリアルモニター表示する、ということをやってみます。

* [ハードウェア](#ハードウェア)  
   * [Arduino UNO の場合：](#Arduino-UNO-の場合)  
   * [Arduino Due の場合](#Arduino-Due-の場合)
* [ソフトウェア](#ソフトウェア)
* [実行結果](#実行結果)
* [ソースコード全文](#ソースコード全文)
* [参考文献：](#参考文献)

### ハードウェア

次のピンをつないでください。

##### Arduino UNO の場合：

 PmodNAV<---> Arduino  
pin 6 3.3V  
pin 5 GND  
pin 4 A5  
pin 2 A4

##### Arduino Due の場合

 PmodNAV<---> Arduino  
pin 6 3.3V  
pin 5 GND  
pin 4 21（SCLピン）  
pin 2 20（SDAピン)  
  
  
### ソフトウェア

全て載せると長くなるので、重要な部分だけ載せます。  
全文は、このページの一番下で確認を。

#include <Wire.h>
#include <SparkFunLSM9DS1.h>

// Déclaration des adresses du module
#define LSM9DS1_M 0x1E
#define LSM9DS1_AG 0x6B

LSM9DS1 imu; // Creation of the object imu
#define PRINT_SPEED 250
static unsigned long lastPrint = 0;
#define DECLINATION -0.74 // déclinaison (en degrés) pour Japan.

void setup(void){
  Serial.begin(115200); // initialization of serial communication
  imu.settings.device.commInterface = IMU_MODE_I2C; // initialization of the module
  imu.settings.device.mAddress = LSM9DS1_M;
  imu.settings.device.agAddress = LSM9DS1_AG;
  if (!imu.begin())
  {
    Serial.println("Probleme de communication avec le LSM9DS1.");
    while (1);
  }
}

void loop(){
  if (imu.gyroAvailable())
    imu.readGyro(); // acquisition des données du gyroscope
  
  if (imu.accelAvailable())
    imu.readAccel(); //Acquisition of accelerometer data
  
  if (imu.magAvailable())
    imu.readMag(); // Acquisition of the magnetometer

  if ((lastPrint + PRINT_SPEED) < millis()){
    printGyro(); // Print "G: gx, gy, gz"
    printAccel(); // Print "A: ax, ay, az"
    printMag(); // Print "M: mx, my, mz"
    printAttitude(imu.ax, imu.ay, imu.az,-imu.my, -imu.mx, imu.mz);
    Serial.println();
    lastPrint = millis();
 }
}

### 実行結果

シリアルモニタに、センサーの値が表示される。  
![f:id:pythonjacascript:20180724230515p:plain](/images/ppythonjacascript2018072420180724230515.png "f:id:pythonjacascript:20180724230515p:plain")

  
### ソースコード全文

/\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\* \* Description: Pmod\_NAV \* All data (accelerometer, gyroscope, magnetometer) are displayed \* \* Wiring \* Module<