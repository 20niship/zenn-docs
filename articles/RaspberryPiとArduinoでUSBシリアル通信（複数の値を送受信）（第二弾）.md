---
title: "Raspberry Pi と Arduino でUSBシリアル通信（複数の値を送受信）（第二弾）"
emoji: ""
type: ""
topics: undefined
published: false
---

前回、このような記事を書きました。  
[shizenkarasuzon.hatenablog.com](https://shizenkarasuzon.hatenablog.com/entry/2019/03/04/004048)

Raspberry PiとArduinoをUSBで接続し、シリアル通信でデータの送受信を行う、という記事です。

ですが、↑の記事では一つのデータしか同時に送ることができません。

今回の記事は、**複数の異なった数値データを同時に送受信**しよう、というプログラムです。

この記事では、ArduinoからRaspberry Piにデータを送っています。  
  
* [ハードウェア](#ハードウェア)
* [プログラム](#プログラム)  
   * [動作結果](#動作結果)
* [解説](#解説)  
   * [送信システム概要](#送信システム概要)  
   * [受信システム概要](#受信システム概要)

### ハードウェア

プログラム実行時には、以下のようにRaspberryPiとArduinoをUSBで接続します。  
RaspberryPiにはUSB端子が4つありますが、どれでも構いません。  
![f:id:pythonjacascript:20190304003926j:plain](/images/ppythonjacascript2019030420190304003926.jpg "f:id:pythonjacascript:20190304003926j:plain")  

実際に繋いでみました。  
![f:id:pythonjacascript:20190304003450j:plain](/images/ppythonjacascript2019030420190304003450.jpg "f:id:pythonjacascript:20190304003450j:plain")  
  
  
### プログラム

以下のようなプログラムです。

**Raspberry Pi側：**

class Serial_communication:
    def __init__(self):
        self.ser = serial.Serial('/dev/ttyACM0', 115200, timeout = 0.01)
        print("setting serial communication...")
        time.sleep(2)
        self.data = [0,0,0,0]

    def main(self):
        while True:
            for i in range(50):
                self.read_one()
                print(ser.data)
            self.clear_serial_buffer()
                
    def read_one(self):
        received = self.ser.read_until("k")
        str_value = re.sub(r'\D',  '', received)
        if str_value is not "":
            data_kind = 3
            data_kind_str = received[0]
            if (data_kind_str == "a" ): data_kind = 0
            elif (data_kind_str == "b" ): data_kind = 1
            elif (data_kind_str == "c" ): data_kind = 2
            else: print("dataprotocol error")
            self.data[data_kind] = int(str_value)
            

    def clear_serial_buffer(self):
        self.ser.read_all()

if __name__ == '__main__':
    ser = Serial_communication()
    ser.main()

**Arduino側：**

#define LED_PIN 13

void setup() { 
   pinMode(LED_PIN, OUTPUT); 
   Serial.begin(115200);
}

void loop() {
  for(int i=0; i<100; i++){
    sendData(0, i*10);
    sendData(1, i*10+3);
    sendData(2, i*10+6);
    sendData(3, i*10+9);
    delay(100);
  }
}

void sendData(unsigned int data_kind, unsigned int data){
  Serial.write(97+data_kind); //97= "a"のASCII表記での数値
  Serial.print(data);
  Serial.print("k");  //データ送信終了
}
  
  
#### 動作結果

ArduinoからRaspberryPiに連続でデータが送信されます。  
そして、RaspberryPiは受け取ったデータを配列に変換して下のように表示します。

![f:id:pythonjacascript:20190309164412j:plain](/images/ppythonjacascript2019030920190309164412.jpg "f:id:pythonjacascript:20190309164412j:plain")   
  
  
### 解説

ArduinoとRaspberryPiの基本的な接続方法については、こちらの記事を見てください。

[shizenkarasuzon.hatenablog.com](https://shizenkarasuzon.hatenablog.com/entry/2019/03/04/004048)  
  
ここでは、どのようにして複数の値を同時に同期させているのかを説明します。  
  
  
#### 送信システム概要

複数の種類のデータを送る場合、データの受け手が受け取ったデータが何を表しているのかを知る必要があります。

今回は、「a」、「b」、「c」の3種類のデータをやり取りすると仮定しました。

その時、a～ｃそれぞれを区別するために、それぞれのデータ（数値）を送る前にデータの種類（a\~c）を送信しています。

1. データの種類を送信（a\~c）
2. データ本体（数値）を送信
3. 送信終了の合図を送信（「k」)

の3ステップです。

その部分のプログラムは以下のようになります。

void sendData(unsigned int data_kind, unsigned int data){
  Serial.write(97+data_kind); //97= "a"のASCII表記での数値
  Serial.print(data);   // データ本体（数値）を送信
  Serial.print("k");  //データ送信終了
}

  
このプログラムで少し工夫を加えているのが、「Serial.write(97+data\_kind); 」の部分です。  
この一行だけ「Serial.print()」ではなく「Serial.write()」になっているのには理由があります。

Serial.write()は「引数の数値をそのまま**送信バイトの2進数の数値として**送信」します。  
一方、Serial.print()は「引数の数値や文字列を**ASCIIテキストとして**送信」します。  
  
例えば、「a」という文字列を送信する方法は、以下の2通りがあります。

Serial.print("a");  //方法1
Serial.write(97);  //方法2

ASCIIテキストの「a」は10進数表記すると97にあたるからです。

  
このようにして、データの種類とデータ本体を送信しています。

これらの処理が0.3秒ごとに繰り返され、実際には下のような文字列の羅列が送られることになります。

（送信されるデータ↓）

a0kb3kc6kd9kb13kc16kd19ka20kb23kc26kd29ka30k......
  
  
#### 受信システム概要

上の文字列からデータを読み取り、配列にデータを格納するシステムをPythonで書きます。

そのメイン処理の部分が、↓です。

received = self.ser.read_until("k")
str_value = re.sub(r'\D',  '', received)
if str_value is not "":
    data_kind = 3
    data_kind_str = received[0]
    if (data_kind_str == "a" ): data_kind = 0
    elif (data_kind_str == "b" ): data_kind = 1
    elif (data_kind_str == "c" ): data_kind = 2
    else: print("dataprotocol error")
    self.data[data_kind] = int(str_value)

まず、received = read\_until("k")の文で「k」までの文字列を読み取ります。

例えば、「a0kb3kc6k.......」という文字列を受信していた場合、「a0k」がreceived に格納されます。

次に、

str_value = re.sub(r'\D',  '', received)

を使って先ほどの「a0k」から数字の部分を抽出します。上の例では、「0」がstr\_value に代入されます。

  
さらに、received の一文字目はデータの種類（↑の例では「a」）を表しているので、

data_kind_str = received[0]
if (data_kind_str == "a" ): data_kind = 0
elif (data_kind_str == "b" ): data_kind = 1
elif (data_kind_str == "c" ): data_kind = 2

と書いて、データの種類を判別します。
  
  
最後に、値を特定の配列に代入して受信完了です。

self.data[data_kind] = int(str_value)