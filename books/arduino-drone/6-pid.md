---
title: "【Arduinoドローン自作】NO.6 PID制御実装編"
emoji: ""
type: ""
topics: []
published: false
---

今回こそ、まさに...  
![f:id:pythonjacascript:20181123173858j:plain](/images/ppythonjacascript2018112320181123173858.jpg "f:id:pythonjacascript:20181123173858j:plain")  

* [1.ドローンの制御方法](#1ドローンの制御方法)
* [2.PID制御とは](#2PID制御とは)
* [3.PID制御の実装](#3PID制御の実装)  
   * [メインループの処理部分（一部抜粋）](#メインループの処理部分一部抜粋)  
   * [2.PIDの計算処理（一部抜粋）](#2PIDの計算処理一部抜粋)  
   * [3.ESCにモーター出力を送信（一部抜粋）](#3ESCにモーター出力を送信一部抜粋)

### 1.ドローンの制御方法

ドローンを制御するには、**フィードバック制御**が必要不可欠です。

フィードバック制御とは、機体（制御するもの）の状態を常に監視し、それが目標状態からずれたら、その誤差をリアルタイムで修正する制御方法です。

![f:id:pythonjacascript:20181123182442j:plain](/images/ppythonjacascript2018112320181123182442.jpg "f:id:pythonjacascript:20181123182442j:plain")  
もう少し厳密にいうと、出力 (制御量) を入力 (目標値) 側を比較し，その差を最小化する制御です。つまり、センサーで得たドローンの姿勢角の値と、目標の姿勢角の値の差を計算し、その値が0に近づくように制御することともいうことができます。

フィードバック制御は、予期しない外乱 (風など) が加わっても有効に修正動作が行うことができるという利点があります。  
  
### 2.PID制御とは

今回のドローンは、そのようなフィードバック制御の一つである、「**PID制御**」という制御アルゴリズムを実装しました。

PID制御を採用した理由は、以下の3つ。  
**・パラメーターがKp, Kd, Ki の3つなので、実装が簡単** 
**・計算処理量が少ないので、Arduinoのような貧弱なマイコンでもリアルタイムの制御できる。** 
**・動作の機敏さ、安定性などの飛行特性をパラメーターで変更できる。**

  
PID制御は、**P制御**（比例制御）、**I制御**（積分制御）、**D制御**（微分制御）の3つのフィードバック制御を合わせた制御方法です。

PID制御を式で表すと、次のようになります。  
![f:id:pythonjacascript:20181123193040j:plain](/images/ppythonjacascript2018112320181123193040.jpg "f:id:pythonjacascript:20181123193040j:plain")  
（Wikipediaより引用）  
上の式の右辺のそれぞれの項は、P制御、I制御、D制御を表している。ここでu(t)は制御量を、e(t)は目標値xd(t)と現在値x(t)の差を表し、Kp, Kd、 Ki はそれぞれP制御、D制御、I制御のゲイン（パラメータ）を表しています。  
パラメーターの値を変えることで、P、I、D制御のそれぞれの影響力の強さを指定できます。よって、パラメーターの値を適切に決定することによって最適な制御量u(t)が求められるのです。

![f:id:pythonjacascript:20181123204123j:plain](/images/ppythonjacascript2018112320181123204123.jpg "f:id:pythonjacascript:20181123204123j:plain")

  
ドローンは、ピッチ角、ロール角、ヨー角の3つの姿勢角を持っているので、それぞれに対してPID制御を行いました。それぞれの角の運動がほかの2つの角の運動に影響を与えないため、それぞれを個別に制御することができます。  
  
### 3.PID制御の実装

上のようなPID制御のアルゴリズムをArduinoで動かします。

専用プログラムを書いたのですが、全文を乗せると大変なことになる野江、一部のみを載せておきます。

#### メインループの処理部分（一部抜粋）

 IMU.Update();   //IMUは、PmodNAVからドローンの姿勢角を取得するクラス
  roll = last_roll * (1 - GAMMA) + (IMU.getRoll() - initial_roll) * GAMMA;
  pitch = last_pitch * (1 - GAMMA) + (IMU.getPitch() - initial_pitch) * GAMMA;

  last_roll = roll;
  last_pitch = pitch;

  roll = max(roll, -90);
  roll = min(roll, 90);
  double roll2 = Roll_PID.Update(roll);  //ここでPIDの計算を実行している

  pitch = max(pitch, -90);
  pitch = min(pitch, 90);
  double pitch2 = Pitch_PID.Update(pitch);

  if ((roll2 - last_roll2) < -W_Dot_Max) roll2 = last_roll2 - W_Dot_Max;
  if ((roll2 - last_roll2) > W_Dot_Max)  roll2 = last_roll2 + W_Dot_Max;
  if ((pitch2 - last_pitch2) < -W_Dot_Max) pitch2 = last_pitch2 - W_Dot_Max;
  if ((pitch2 - last_pitch2) > W_Dot_Max)  pitch2 = last_pitch2 + W_Dot_Max;

  unsigned int m1 = min(throttle - roll2 + yaw_error + pitch2, 1500);
  unsigned int m2 = min(throttle + roll2 + yaw_error - pitch2, 1500);
  unsigned int m3 = min(throttle - roll2 - yaw_error - pitch2, 1500);
  unsigned int m4 = min(throttle + roll2 - yaw_error + pitch2, 1500);

  Motors.setPWMData(m1, m2, m3, m4);　　//モーターに出力
  Motors.PWM_Motors_out();
  last_roll2 = roll2;
  last_pitch2 = pitch2;

#### 2.PIDの計算処理（一部抜粋）

double PID::Update(double feedback){
  double error = setPoint - feedback;
  double delta_error = error - last_error;
  P_term = Kp * error;
  I_term += error * delta_time;
  if(I_term >  WINDUP_GUARD) I_term = WINDUP_GUARD;
  if(I_term < - WINDUP_GUARD) I_term = -WINDUP_GUARD;

  D_term = delta_error / delta_time;
  last_error = error;
  return P_term + Ki * I_term + Kd * D_term;
} 

#### 3.ESCにモーター出力を送信（一部抜粋）

#define MOTORS_ESC_PWM_MAX 2000
#define MOTORS_ESC_PWM_MIN 1000

void motors::PWM_Motors_out(){ // set new motor output
    motor0i = max(motor0i, MOTORS_ESC_PWM_MIN);
    motor1i = max(motor1i, MOTORS_ESC_PWM_MIN);
    motor2i = max(motor2i, MOTORS_ESC_PWM_MIN);
    motor3i = max(motor3i, MOTORS_ESC_PWM_MIN);
    motor0i = min(motor0i, MOTORS_ESC_PWM_MAX);
    motor1i = min(motor1i, MOTORS_ESC_PWM_MAX);
    motor2i = min(motor2i, MOTORS_ESC_PWM_MAX);
    motor3i = min(motor3i, MOTORS_ESC_PWM_MAX);
    BrushlessMotor0.writeMicroseconds(motor0i);
    BrushlessMotor1.writeMicroseconds(motor1i);
    BrushlessMotor2.writeMicroseconds(motor2i);
    BrushlessMotor3.writeMicroseconds(motor3i);
}

void motors::setPWMData(int m1, int m2, int m3, int m4){
    motor0i = m1;
    motor1i = m2;
    motor2i = m3;
    motor3i = m4;
}