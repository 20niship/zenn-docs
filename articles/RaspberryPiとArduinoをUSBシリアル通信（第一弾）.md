---
title: "Raspberry Pi と Arduino をUSBシリアル通信（第一弾）"
emoji: "🤖"
type: "tech"
topics: []
published: false
---

今回は、Raspberry PiとArduinoのコンビです。

  
シリアル通信を使って、相互にデータのやり取りをしてみます。

* [ハードウェア](#ハードウェア)
* [Raspberry Pi→Arduino](#Raspberry-PiArduino)  
   * [プログラム](#プログラム)  
   * [動作結果](#動作結果)
* [Arduino→Raspberry Pi](#ArduinoRaspberry-Pi)  
   * [プログラム](#プログラム-1)  
   * [動作結果](#動作結果-1)
* [解説](#解説)  
   * [ポートを開く](#ポートを開く)  
   * [データを受け取る](#データを受け取る)  
   * [データを送信する](#データを送信する)  
   * [ポートを閉じる](#ポートを閉じる)

### ハードウェア

プログラム実行時には、以下のようにRaspberryPiとArduinoをUSBで接続します。  
RaspberryPiにはUSB端子が4つありますが、どれでも構いません。  
![f:id:pythonjacascript:20190304003926j:plain](/images/ppythonjacascript2019030420190304003926.jpg "f:id:pythonjacascript:20190304003926j:plain")  

実際に繋いでみました。  
![f:id:pythonjacascript:20190304003450j:plain](/images/ppythonjacascript2019030420190304003450.jpg "f:id:pythonjacascript:20190304003450j:plain")  
  
  
### Raspberry Pi→Arduino

#### プログラム

以下のようなプログラムです。

**Raspberry Pi側：**

import serial
import time
 
ser = serial.Serial('/dev/ttyACM0', 9600)

for i in range(10):
        ser.write('a')
        print("LED ON")
        time.sleep(0.5)
        ser.write('0')
        print("LED OFF")
        time.sleep(0.5)

ser.close()
print("bbb")

**Arduino側：**

byte val=0;
#define LED_PIN 13

void setup() { 
   pinMode(LED_PIN, OUTPUT); 
   Serial.begin(9600);
}

void loop() {
   if(Serial.available() > 0){ 
      val = Serial.read();
   }
   if(val == 'a') digitalWrite(LED_PIN, HIGH);
   else if(val == '0') digitalWrite(LED_PIN, LOW);
}
  
  
#### 動作結果

Raspberry PiとArduinoをUSB接続して、↑のプログラムを実行します。

すると、Arduino基盤に実装されたチップLEDが1秒間隔で10回、点滅するはずです。  
また、Raspberry Piのディスプレイにも、ArduinoのLEDのON/OFFと同時に「LED ON」や「LED OFF」といった文字が表示されるはずです。  
  
### Arduino→Raspberry Pi

今度は、逆にArduinoからRaspberry Piにデータを送ってみます。

#### プログラム

以下のようなプログラムです。

**Raspberry Pi側：**

import serial
ser = serial.Serial('/dev/ttyACM0', 115200)
while True:
    String_data = ser.read()
    print(String_data)
ser.close()

**Arduino側：**

void setup(){
  Serial.begin(115200);
  pinMode(13, OUTPUT);
}

void loop(){
  Serial.println("LED ON");
  digitalWrite(13, HIGH);
  delay(1000);
  Serial.println("LED OFF");
  digitalWrite(13, LOW);
  delay(1000); 
}

#### 動作結果

Arduino側のLEDが点滅するのに合わせて、Raspberry Pi側のディスプレイに「LED ON」や「LED OFF」と表示されます。

Raspberry Pi側のプログラムを中断するには、Ctrl+C を押して下さい。  
  
  
### 解説

この記事はRaspberry Piがメインの記事なので、主にRaspberry piのプログラムについて解説していきます。

Arduino側については、こちらの記事をご覧ください。  
[shizenkarasuzon.hatenablog.com](https://shizenkarasuzon.hatenablog.com/entry/2018/09/10/150209)  
  
  
#### ポートを開く

特定のUSBポートを開く場合は、以下のように設定してください。

ser = serial.Serial('/dev/ttyACM0', 9600)

尚、USBポートではなくGPIO上のTxD/RxDピンで通信する場合は、以下のようになります。

ser = serial.Serial('/dev/serial0', 9600)

この場合は「pi-config」の設定等が必要になるので、また別の記事で紹介します。

  
ser.Serial()の第一引数には「'/dev/ttyACM0'」を指定し、第二引数にはビットレートを指定します。  
ビットレートは一秒間に送るバイト数のことで、「9600」や「115200」などが一般的です。

また、このように書いてタイムアウト時間を指定することもできます。引数の単位は\[秒\]です。

ser = serial.Serial('/dev/ttyACM0', 9600, timeout = 0.3)

  
#### データを受け取る

Raspberry Piでも、シリアル通信でデータを受け取ると特定のバッファにその値が保存されます。  
プログラムでできることはそのバッファを読み取ることです。

  
下のように書くと、シリアル通信で送られてきた文字列を古い順に一つずつ読み取ります。  
読み取った文字はcharに格納されます。

#一文字受け取る
char = ser.read()

ある特定の文字（文字列）まで全て読み込む場合は、read\_until()関数を利用します。  
例えば、下のように書くと「moji」という単語が送られてくるまでの全ての文字列をchar2に格納します。

char2 = ser.read_until("moji")

  
下のように書くと、現在バッファにたまっているものをすべて読み込み、バッファを空にします。

char2 = ser.read_all()
  
  
尚、バッファに何もデータがなくそのままタイムアウトした場合、上のような読み込み関数は「**None**」を返すようです。  
  
  
#### データを送信する

下のように書くと、文字列を送信することができます。

ser.write('a')
  
  
#### ポートを閉じる

シリアルポートの使用後は、以下のプログラムを実行してポートを閉じます。

ser.close()