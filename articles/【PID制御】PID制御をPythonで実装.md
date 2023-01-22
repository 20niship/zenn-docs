---
title: "【PID制御】PID制御をPythonで実装"
emoji: ""
type: ""
topics: []
published: false
---

フィードバックの一種で、PID制御と言うものがあります。

この記事では、PID制御をPythonで２通りのやり方で実装してみます。

* [方法1 matpllotlib のライブラリを使用](#方法1-matpllotlib-のライブラリを使用)  
   * [実行結果](#実行結果)
* [方法2 ライブラリを使わずに自分で書く](#方法2-ライブラリを使わずに自分で書く)  
   * [実行結果](#実行結果-1)  
   * [解説](#解説)
* [参考文献](#参考文献)

### 方法1 matpllotlib のライブラリを使用

プログラム：


from control.matlab import *
from matplotlib import pyplot as plt

# PID制御器のパラメータ
Kp = 0.6  # 比例
Ki = 0.03 # 積分
Kd = 0.03 # 微分
num = [Kd, Kp, Ki]
den = [1, 0]
K = tf(num, den)

# 制御対象
Kt = 1
J = 0.01
C = 0.1
num = [Kt]
den = [J, C, 0]
G = tf(num, den)

# フィードバックループ
sys = feedback(K*G, 1)
t = np.linspace(0, 3, 1000)
y, T = step(sys, t)
plt.plot(T, y)
plt.grid()
plt.axhline(1, color="b", linestyle="--")
plt.xlim(0, 3)

（ほとんどこのサイトからのコピペです。  
[【python-control】PID制御のシミュレーション | アルゴリズム雑記](https://algorithm.joho.info/seigyoriron/python-control-pid/)）  
  
#### 実行結果

![f:id:pythonjacascript:20180827001327p:plain](/images/ppythonjacascript2018082720180827001327.png "f:id:pythonjacascript:20180827001327p:plain")

  
### 方法2 ライブラリを使わずに自分で書く

import matplotlib.pyplot as plt
import numpy as np

import time

class PID:
    def __init__(self, P=0.2, I=0.0, D=0.0):
        self.Kp = P
        self.Ki = I
        self.Kd = D
        self.targetPos=0.
        self.clear()

    def clear(self):
        self.SetPoint = 0.0
        self.PTerm = 0.0
        self.ITerm = 0.0
        self.DTerm = 0.0
        self.last_error = 0.0
        self.delta_time = 0.1
        # Windup Guard
        self.int_error = 0.0
        self.windup_guard = 20.0
        self.output = 0.0

    def update(self, feedback_value):
        error = self.targetPos - feedback_value
        delta_error = error - self.last_error  
        self.PTerm = self.Kp * error  #PTermを計算
        self.ITerm += error * self.delta_time  #ITermを計算

        if (self.ITerm > self.windup_guard):  #ITermが大きくなりすぎたとき様
            self.ITerm = self.windup_guard
        if(self.ITerm < -self.windup_guard):
           self.ITerm = -self.windup_guard
           
        self.DTerm = delta_error / self.delta_time  #DTermを計算
        self.last_error = error
        self.output = self.PTerm + (self.Ki * self.ITerm) + (self.Kd * self.DTerm)
        
    def setTargetPosition(self, targetPos):
        self.targetPos = targetPos
        

if __name__ == "__main__":
    pid = PID(0.3, 0.3, 0.002)

    RepeatNum = 100
    feedback = 0
    target_position = np.ones(RepeatNum)
    
    feedback_list = []

    for i in range(1, RepeatNum):
        pid.update(feedback)
        feedback += pid.output
        pid.setTargetPosition(target_position[i])
        feedback_list.append(feedback)
        
    plt.title('PID control in python')
    plt.xlabel('time (s)')
    plt.ylabel('PID (PV)')
    plt.plot(feedback_list, label = 'target')
    plt.plot(target_position, label = 'feedback')
    plt.ylim(min(target_position) * -0.2, max(target_position) * 1.2)
    plt.legend(loc='lower right')
    plt.show()

（参考文献：<https://github.com/ivmech/ivPID>）  
  
#### 実行結果

![f:id:pythonjacascript:20180827001418p:plain](/images/ppythonjacascript2018082720180827001418.png "f:id:pythonjacascript:20180827001418p:plain")

#### 解説

それぞれの変数の説明です。

| RepeatNum         | PID制御を実行する回数                 |
| ----------------- | ---------------------------- |
| target\_position  | 目標値の配列                       |
| PID.SetPoint      | 現在時間の目標値                     |
| PID.Kp            | p制御の強さ                       |
| PID.Ki            | i制御の強さ                       |
| PID.Kd            | d制御の強さ                       |
| PID.PTerm         | p制御の制御量                      |
| PID.ITerm         | i制御の制御量                      |
| PID.DTerm         | d制御の制御量                      |
| PID.windup\_guard | ITermが大きく（小さく）なりすぎたことを検知する閾値 |
| PID.output        | PID制御の出力                     |

        if (self.ITerm > self.windup_guard):  #ITermが大きくなりすぎたとき様
            self.ITerm = self.windup_guard
        if(self.ITerm < -self.windup_guard):
           self.ITerm = -self.windup_guard

の部分は、ITermが大きくなりすぎることを防ぐためのものです。  
この文のおかげで、ITermは、常に **windup\_guard <= ITerm <= windup\_guard**という条件を満たしています。  
  
### 参考文献

[【python-control】PID制御のシミュレーション | アルゴリズム雑記](https://algorithm.joho.info/seigyoriron/python-control-pid/)  
[GitHub - ivmech/ivPID: Python PID Controller](https://github.com/ivmech/ivPID)