---
title: "【Raspberry pi】C言語でもブラシレスモーターが回ってモーター"
emoji: ""
type: ""
topics: undefined
published: false
---

**...タイトルからふざけてますな。**（もう三回目......）

**はい。**

  
ということで、Raspberry Piを使ってブラシレスモーター「**BR1806**（↓写真）」を制御してみました。  
![f:id:pythonjacascript:20190303221630j:plain](/images/ppythonjacascript2019030320190303221630.jpg "f:id:pythonjacascript:20190303221630j:plain")  
  
今回も、WiringPiというライブラリを使用するので、初めての人は[このように](https://shizenkarasuzon.hatenablog.com/entry/2019/03/04/002041#%E7%92%B0%E5%A2%83%E4%BD%9C%E6%88%90)ダウンロードしてください。  
  
* [プログラム](#プログラム)
* [配線](#配線)  
   * [補足：ESCについて](#補足ESCについて)  
   * [半田付け](#半田付け)
* [実行方法](#実行方法)
* [実行結果](#実行結果)
* [解説](#解説)  
   * [pigpioを使ったPWMパルス出力方法](#pigpioを使ったPWMパルス出力方法)  
   * [ESCのセットアップ](#ESCのセットアップ)  
   * [メイン動作部分](#メイン動作部分)
* [モーターが回らない・MAXで回って制御できない場合](#モーターが回らないMAXで回って制御できない場合)

  
### プログラム

import time
import pigpio
motor_pin = 4

pi = pigpio.pi()

pi.set_servo_pulsewidth(motor_pin, 2000) 
print("max")
time.sleep(1)
pi.set_servo_pulsewidth(motor_pin, 1000) 
print("min")

time.sleep(3)

for i in range(3):
        pi.set_servo_pulsewidth(motor_pin, 1080) 
        time.sleep(1)
        pi.set_servo_pulsewidth(motor_pin, 1030) 
        print("1200")
        time.sleep(1)
        
pi.set_servo_pulsewidth(motor_pin, 0) 
pi.stop()

### 配線

以下のようにブラシレスモーターとRaspberryPiを繋いでください。  
![f:id:pythonjacascript:20190309170657j:plain](/images/ppythonjacascript2019030920190309170657.jpg "f:id:pythonjacascript:20190309170657j:plain")

電源系の配線のほかに、RaspberryPiのGPIO18ピンと、ESCの信号入力線（白コード）を繋ぎます。

ラシレスモーターとは、その名の通りブラシがないモーターです。そのため、電池に繋げば回るという単純なものではなく、きちんとした制御回路を組む必要があります。そして、その制御回路がひとまとまりになったものがESCです。

#### 補足：ESCについて

ESCとは、「lectronic Speed Controller 」の略で、ブラシレスモーターをコントロールするのに使用されます。  
出力はブラシレスモータのそれぞれの端子、入力はブラシレスモーターの出力を設定するためのPWM信号と、電源です。

今回は、このESCを使用しました。  
![f:id:pythonjacascript:20180822011900p:plain:h400](/images/ppythonjacascript2018082220180822011900.png "f:id:pythonjacascript:20180822011900p:plain:h400")

スペック：

| 最大電流 | 25A  |
| ---- | ---- |
| 電圧   | 2-4S |

購入元：  
<https://www.amazon.co.jp/GoolRC-スピードコントローラ-DX2205-RCレースマルチコプター-ドローンクワッドローター用/dp/B0748GGSFZ/ref=sr%5F1%5F6?ie=UTF8&qid=1534866536&sr=8-6&keywords=DX2205:h400>

#### 半田付け

ESCとブラシレスモーターを半田付けします。  
![f:id:pythonjacascript:20180822012457j:plain](/images/ppythonjacascript2018082220180822012457.jpg "f:id:pythonjacascript:20180822012457j:plain")  
（半田付けの様子。半田付けが汚いのは気にしないでください～。）

ESC 側の３か所の端子と、ブラシレスモーターの３本の線を半田付けします。ブラシレスモーターに極性はないので、どの端子に度のコードを取り付けても回ります。  
ただし、回転方向が逆の場合は、３本のコードのうち、どれか2つのコードを逆の端子に半田付けしなおせば、逆方向に回転するはずです。  
そして、半田付け部分は、金属に触れてショートしないように、絶縁体で覆っておきましょう。（僕の場合は、熱収縮チューブがないのでセロハンテープですが。）  
  
  
### 実行方法

まず、↑のプログラムを適切な場所に保存します。（拡張子は「.c」）  
そして、

gcc -Wall -o servo servo.c -lwiringPi

のようなコマンドを実行してコンパイルを行い、（↑の「servo.c」は各自で自分のファイル名に変えてください）

sudo ./servo

でプログラムが動き始めます。  
  
### 実行結果

以下の手順で実行してください。

1. ESCにバッテリーを接続
2. 「sudo ./○○」でRaspberryPiのプログラムを実行
3. しばらくすると「max」と表示される
4. 1秒後、「min」が表示され、モーターからBeep音が鳴る
5. モーターが回り始める

※ 「Ctrl + C」で強制終了することができます。

うまくいけば、モーターが回り始めるはずです。  
![f:id:pythonjacascript:20190303234215j:plain](/images/ppythonjacascript2019030320190303234215.jpg "f:id:pythonjacascript:20190303234215j:plain")  
  
  
### 解説

ブラシレスモーターも、サーボモーターと同様に、PWM制御を使って動かすことができます。[\*1](#f-fe1fb813 "正確には、ESCでブラシレスモーターを制御する時、ESCにはPWM信号を送信するという意味")

ですが、今回はPWMのパルスのON時間を指定したいので、前回使用したRPi.GPIOライブラリではなく、「**pigpio**」を使用します。

#### pigpioを使ったPWMパルス出力方法

pigpioを使うには、まず

import pigpio

でインポートした後に、

pi = pigpio.pi()

と書いて、オブジェクトを読み込みます。

そして、特定のピンにパルスを出力するには、

pi.set_servo_pulsewidth(4, 1500)

のように書きます。  
上は、GPIO 4ピン（**ピン番号ではなく、GPIOの番号です！**）に1500msのパルスを出力した例です。  
この関数は、パルス幅500ms～2000msの間で出力可能みたいです。  
  
#### ESCのセットアップ

ESCにはいろいろな種類がありますが、今回使ったESCには以下のようなセットアップが電源をONにした時毎回必要なようです。

1. ESCの電源ON
2. PWMのパルスの最大値を送る
3. 一定時間待つ**（待ち時間はESCによって異なる）**
4. PWMのパルスの最小値を送る
5. 一定時間待つ**（待ち時間はESCによって異なる）**
6. 運転モードに入る

このように、入力パルスの最大値と最小値を予め設定しておくことで、より正確な制御をかのうにしているのですな...。凄い凄い。

...と言いたいところですが、最大パルスを出力してから最小パルスを出力するまでの待ち時間が問題。この時間がESCによって異なるのです。  
Beep音が鳴るのでそれに合わせて設定すればいいのですが、結構大変です。設定を間違えると制御不能になります。

僕が使っているESCは、以下のようなセットアッププログラムで動作しました。

pi.set_servo_pulsewidth(motor_pin, 2000) 
print("max")
time.sleep(1)
pi.set_servo_pulsewidth(motor_pin, 1000) 
print("min")
time.sleep(3)
  
  
#### メイン動作部分

for i in range(3):
        pi.set_servo_pulsewidth(motor_pin, 1080) 
        time.sleep(1)
        pi.set_servo_pulsewidth(motor_pin, 1030) 
        print("1200")
        time.sleep(1)
        
pi.set_servo_pulsewidth(motor_pin, 0) 
pi.stop()

8％出力と3％出力を3回繰り返しています。

最後には、

pi.stop()

と書いて、パルス出力を終了して下さい。  
  
  
### モーターが回らない・MAXで回って制御できない場合

ESCには様々なファームウェアがあり、それぞれセットアップ方法が異なります。「セットアップ」とはPWMの最大パルス幅と最小パルス幅を設定する作業のことで、電源を入れた直後に行われます。  
このセットアップのタイミングがずれると、ESCがPWM信号を正しく認識してくれない場合があります。

このような時に、Beep音が鳴らなかったり、いきなり出力MAXで高速回転して制御不能になったりする事態が起こります。

  
もしそうなった場合は、各自でESCのファームウェアを調べて、セットアップ部分（PWMのMAX出力とMIN出力を送っているところ）のプログラムを書き直してください。

[\*1](#fn-fe1fb813):正確には、ESCでブラシレスモーターを制御する時、ESCにはPWM信号を送信するという意味