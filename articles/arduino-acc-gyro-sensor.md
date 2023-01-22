---
title: "【Arduino】MPU6050とI2C通信して、加速度とジャイロのデータを取得"
emoji: "🤖"
type: "tech"
topics: []
published: false
---

題名の通り、今回から6軸センサーの「**MPU-6050**」を使用していきます。

![f:id:pythonjacascript:20190216151131j:plain](/images/ppythonjacascript2019021620190216151131.jpg "f:id:pythonjacascript:20190216151131j:plain")

この記事では、MPU6050から加速度とジャイロの計測データを取得して、それをシリアルモニターに表示するところまでを行います。  
それ以降の内容（角度算出etc.）は、別の記事に書いていくつもりです。

* [1.下準備（反田付け）](#1下準備反田付け)
* [2.回路図](#2回路図)
* [3.サンプルプログラム](#3サンプルプログラム)
* [4.実行](#4実行)
* [5.簡単に解説](#5簡単に解説)  
   * [（1）MPU-6050のセットアップ](#1MPU-6050のセットアップ)  
   * [(2) 通信相手の確認](#2-通信相手の確認)  
   * [(3) 動作モードの読み出し](#3-動作モードの読み出し)  
   * [(4) 値を取得](#4-値を取得)  
   * [(5) 値を補正](#5-値を補正)  
   * [(6) 送信](#6-送信)
* [参考文献](#参考文献)

### 1.下準備（反田付け）

私は、[ここ](https://www.amazon.co.jp/ps61003-MPU-6050-%E4%BD%BF%E7%94%A8-%EF%BC%93%E8%BB%B8%E3%82%B8%E3%83%A3%E3%82%A4%E3%83%AD%E3%82%B9%E3%82%B3%E3%83%BC%E3%83%97%E3%83%BB%EF%BC%93%E8%BB%B8%E5%8A%A0%E9%80%9F%E5%BA%A6%E3%82%BB%E3%83%B3%E3%82%B5%E3%83%BC-%E3%83%A2%E3%82%B8%E3%83%A5%E3%83%BC%E3%83%AB/dp/B008BOPN40)からMPU-6050のモジュールを購入しました。Amaozonさんです。

  
購入すると、このようにピンヘッダが2種類付属していたので、曲がっている方をとりあえず半田付けします。  
![f:id:pythonjacascript:20190216151456j:plain](/images/ppythonjacascript2019021620190216151456.jpg "f:id:pythonjacascript:20190216151456j:plain")

  
半田付け後、裏面の様子です。  
![f:id:pythonjacascript:20190216151537j:plain](/images/ppythonjacascript2019021620190216151537.jpg "f:id:pythonjacascript:20190216151537j:plain")

### 2.回路図

以下のような回路を組んでください。  
![f:id:pythonjacascript:20190216153145j:plain](/images/ppythonjacascript2019021620190216153145.jpg "f:id:pythonjacascript:20190216153145j:plain")

  
Arduino内部で線がつながっているので、このように繋いでもOKです。  
![f:id:pythonjacascript:20190216162859j:plain](/images/ppythonjacascript2019021620190216162859.jpg "f:id:pythonjacascript:20190216162859j:plain")

  
本当は、Arduinoの入出力ピンが5V、MPU-6050の入出力が3.3Vなので、電圧変換モジュールをはさんだ方がよいと思います。  
上のように組んでも一応動きます。  
  
### 3.サンプルプログラム

以下のプログラムをArduinoに書き込んでください。

#include <Wire.h>

// MPU-6050のアドレス、レジスタ設定値
#define MPU6050_WHO_AM_I     0x75  // Read Only
#define MPU6050_PWR_MGMT_1   0x6B  // Read and Write
#define MPU_ADDRESS  0x68


// デバイス初期化時に実行される
void setup() {
  Wire.begin();

  // PCとの通信を開始
  Serial.begin(115200); //115200bps
 
  // 初回の読み出し
  Wire.beginTransmission(MPU_ADDRESS);
  Wire.write(MPU6050_WHO_AM_I);  //MPU6050_PWR_MGMT_1
  Wire.write(0x00);
  Wire.endTransmission();

  // 動作モードの読み出し
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
  float gyro_x = gxRaw / 131.0;//FS_SEL_0 131 LSB / (°/s)
  float gyro_y = gyRaw / 131.0;
  float gyro_z = gzRaw / 131.0;

  Serial.print(acc_x);  Serial.print(",");
  Serial.print(acc_y);  Serial.print(",");
  Serial.print(acc_z);  Serial.print(",");
  Serial.print(gyro_x); Serial.print(",");
  Serial.print(gyro_y); Serial.print(",");
  Serial.print(gyro_z); Serial.println("");
}
  
  
### 4.実行

プログラムを書き込んだら、ArduinoをUSBでPCに接続し、シリアルモニターを開きます。  
通信速度は115200bpsに設定しておいてください。

![f:id:pythonjacascript:20190216170934j:plain](/images/ppythonjacascript2019021620190216170934.jpg "f:id:pythonjacascript:20190216170934j:plain")  
電源が供給されると、MPU-6050の基盤に実装されたチップLEDが赤く光ります。

  
下のようにデータが送られてきたら成功です。  
![f:id:pythonjacascript:20190216153627j:plain](/images/ppythonjacascript2019021620190216153627.jpg "f:id:pythonjacascript:20190216153627j:plain")  
  
  
### 5.簡単に解説

まず、MPU-6050とArduinoの通信には、I2Cという通信規格が用いられています。  
I2C通信を行うには、「**Wire.h**」というライブラリを使用する必要があります。

では、このプログラムの動作を考えていきます。  
  
#### （1）MPU-6050のセットアップ

まず、MPU-6050の初期設定を行います。

Wire.begin();

の命令で、MPU-6050とのI2C通信を開始します。
  
  
次に、MPU6050のデバイス確認と設定を行います。

#### (2) 通信相手の確認

  // 初回の読み出し
  Wire.beginTransmission(MPU_ADDRESS);
  Wire.write(MPU6050_WHO_AM_I);
  Wire.write(0x00);
  Wire.endTransmission();

の4行は、MPU-6050の通信アドレスを確認しています。

  
正確には、MPU-6050の中の**「WHO\_AM\_I」レジスタ**を読み込んでいます。  
「WHO\_AM\_I」レジスタのメモリ番地は0x75なので、

#define MPU6050_WHO_AM_I     0x75  // R

と書いているのです。

このレジスタはデバイスの識別情報を確認するために使用されます。 WHO\_AM\_Iの内容は、MPU-60X0の7ビットI2Cアドレスの上位6ビットであり、デフォルト値は0x68です。

  
WHO\_AM\_Iレジスタの中身です。（**MPU-6050 Register Map**より）  
![f:id:pythonjacascript:20190216155542j:plain](/images/ppythonjacascript2019021620190216155542.jpg "f:id:pythonjacascript:20190216155542j:plain")

  
この4行は通信相手の動作確認のために書いたので、削除しても動作します。  
  
  
#### (3) 動作モードの読み出し

#define MPU6050_PWR_MGMT_1   0x6B  // Read and Write
（中略）
// 動作モードの読み出し
Wire.beginTransmission(MPU_ADDRESS);
Wire.write(MPU6050_PWR_MGMT_1);  //PWR_MGMT_1レジスタの設定
Wire.write(0x00);
Wire.endTransmission();

この4行で、MPU-6050の動作モードを読み込みます。

0x6B とは、MPU-6050の「**PWR\_MGMT\_1レジスタ」**の番地を表しています。

PWR\_MGMT\_1レジスタは、電力モードとクロックソースの設定を行うためのものです。

また、デバイス全体をリセットするためのビットと、温度センサーを無効にするためのビットもあります。

PWR\_MGMT\_1レジスタの中身です。  
![f:id:pythonjacascript:20190216155526j:plain](/images/ppythonjacascript2019021620190216155526.jpg "f:id:pythonjacascript:20190216155526j:plain")

  
次の表に従ってクロックソースを選択できます。  
![f:id:pythonjacascript:20190216160430j:plain](/images/ppythonjacascript2019021620190216160430.jpg "f:id:pythonjacascript:20190216160430j:plain")  

この4行は、MPU-6050の重要な設定が含まれているので、削除すると動かなくなります。  
  
#### (4) 値を取得

以下の部分で値を取得します。

  Wire.beginTransmission(0x68);
  Wire.write(0x3B);
  Wire.endTransmission(false);
  Wire.requestFrom(0x68, 14, true);
  while (Wire.available() < 14);
  int16_t axRaw, ayRaw, azRaw, gxRaw, gyRaw, gzRaw, Temperature;

  axRaw = Wire.read() << 8 | Wire.read();
  ayRaw = Wire.read() << 8 | Wire.read();
(以下も同じ形式なので略）

**int16\_t** とは、符号付き整数を格納する16ビットの変数型です。  
ここでは使用しませんが、**uint16\_t** にすると、正の整数の16ビットになります。  
  
  Wire.beginTransmission(0x68);
  Wire.write(0x3B);
  Wire.endTransmission(false);
  Wire.requestFrom(0x68, 14, true);

は、「今からデータを送って下さい！！」という命令を送信しています。  
因みに、0ｘ3Bは、ACCEL\_XOUT\_H（x軸加速度データの上位バイト）が格納されているレジスタの番地です。

「ACCEL\_XOUT\_Hレジスタから14ビット分連続でレジスタの値を送信してください」という命令です。

ちなみに、ACCEL\_XOUT\_Hレジスタ以降の値は以下の表のとおりです。  
![f:id:pythonjacascript:20190216161555j:plain](/images/ppythonjacascript2019021620190216161555.jpg "f:id:pythonjacascript:20190216161555j:plain")

加速度のほかにも、角速度、温度のデータを送っていることがわかります。

  
MPU-6050から送られてくる加速度、角速度のデータは16ビットですが、I2C通信で一回で送ることのできる値は8ビットです。  
そこで、一つのデータ（数値）あたり2回のI2C読み込み処理を行って、特定の変数に格納する作業が

  axRaw = Wire.read() << 8 | Wire.read();

の部分で行われています。  
  
  
#### (5) 値を補正

送られてきた値を補正します。

というのも、PWR\_MGMT\_1でセンサー感度を設定したとき、それと同時に測定値の数値のScalability（数値の示す大きさ）を変更したことになります。そこで、下のように数値を乗除して正しい値にしているのです。

  // 加速度値を分解能で割って加速度(G)に変換する
  float acc_x = axRaw / 16384.0;  //FS_SEL_0 16,384 LSB / g
  float acc_y = ayRaw / 16384.0;
  float acc_z = azRaw / 16384.0;

  // 角速度値を分解能で割って角速度(degrees per sec)に変換する
  float gyro_x = gxRaw / 131.0;
  float gyro_y = gyRaw / 131.0;
  float gyro_z = gzRaw / 131.0;

これらの値は、データシートに基づいています。  
（加速度の設定）  
![f:id:pythonjacascript:20190216162202j:plain](/images/ppythonjacascript2019021620190216162202.jpg "f:id:pythonjacascript:20190216162202j:plain")  
上のプログラムの場合、「**AFS-SEL**」は0に設定されているため、16384で割っています。

（ジャイロの設定）  
![f:id:pythonjacascript:20190216162205j:plain](/images/ppythonjacascript2019021620190216162205.jpg "f:id:pythonjacascript:20190216162205j:plain")  
上のプログラムの場合、「**FS-SEL**」は0に設定されているため、131で割っています。  
  
  
#### (6) 送信

あとは、補正した値をシリアル通信で送信するだけです。

  Serial.print(acc_x);  Serial.print(",");
  Serial.print(acc_y);  Serial.print(",");
  Serial.print(acc_z);  Serial.print(",");
  Serial.print(gyro_x); Serial.print(",");
  Serial.print(gyro_y); Serial.print(",");
  Serial.print(gyro_z); Serial.println("");

これらの命令達をループ関数内で繰り返し行うことで、連続して値を取得することができます。  
  
  
### 参考文献

MPU6050のデータシート：  
<https://www.invensense.com/wp-content/uploads/2015/02/MPU-6000-Datasheet1.pdf>

  
MPU6050のレジスタ一覧：  
<https://www.invensense.com/wp-content/uploads/2015/02/MPU-6000-Register-Map1.pdf>

[Raspberry Pi 2でI2C (MPU-6050の制御) 動作確認編 - wide\_snow’s blog](http://www.widesnow.com/entry/2015/09/06/211055)

[Arduinoから MPU6050の値を取得してみる – 自作のいろいろ](https://garchiving.com/how-to-use-mpu6050-in-arduino/)

[初めての電子工作: Arduinoで6軸センサを動かしてみる - Qiita](https://qiita.com/key/items/8c6b684e6308cc5f451f)

[Arduino Playground - MPU-6050](https://playground.arduino.cc/Main/MPU-6050)