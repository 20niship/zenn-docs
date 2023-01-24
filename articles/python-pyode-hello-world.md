---
title: "【Python】PyODEをインストールしてウィンドウ表示"
emoji: "🤖"
type: "tech"
topics: []
published: false
---

**PyODE**は、Pythonの**3Dシミュレーション**用のライブラリです。  
[PyODE](http://pyode.sourceforge.net/)

  
今回は、そんなPyODEをインストールしてウィンドウ表示までを行いたいと思います。  
  
### 1.インストール

**※Pythonとpipはインストールされている前提で説明します。**

以下のサイトからインストールします。  
[Python Extension Packages for Windows - Christoph Gohlke](https://www.lfd.uci.edu/~gohlke/pythonlibs/)

上のサイトにアクセスして、「PyODE」で検索し、適切なバージョンの**whlファイル**をダウンロードしてください。  
私の場合、32bitのPython3.6を使用しているので、「**pyodeint-0.10.1-cp36-cp36m-win32.whl**」をダウンロードしました。

そして、**コマンドプロンプト**を開き、whlファイルをダウンロードしたディレクトリに**cd命令**で移動します。  
![f:id:pythonjacascript:20181223205728j:plain](/images/ppythonjacascript2018122320181223205728.jpg "f:id:pythonjacascript:20181223205728j:plain")

そして、「**pip install (先ほどインストールしたwhlファイル名)**」と打ち込み、PyODEをインストールします。  
私の環境の場合は、「pip install pyodeint-0.10.1-cp36-cp36m-win32.whl」でした。

「**Suscessfully installed!**」のように表示されればOKです。  
  
### サンプルプログラム

以下のサンプルプログラムを実行してください。

import ode

# Create a world object
world = ode.World()
world.setGravity( (0,-9.81,0) )

# Create a body inside the world
body = ode.Body(world)
M = ode.Mass()
M.setSphere(2500.0, 0.05)
M.mass = 1.0
body.setMass(M)

body.setPosition( (0,2,0) )
body.addForce( (0,200,0) )

# Do the simulation...
total_time = 0.0
dt = 0.04
while total_time<2.0:
    x,y,z = body.getPosition()
    u,v,w = body.getLinearVel()
    print ("Hello, World")
    world.step(dt)
    total_time+=dt

### 実行結果

このような画面が表示されればOKです。  
  
### 解説