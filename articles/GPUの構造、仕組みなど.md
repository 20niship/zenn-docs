---
title: "GPUの構造、仕組みなど"
emoji: ""
type: ""
topics: undefined
published: false
---

* [GPUとは](#GPUとは)
* [搭載形態](#搭載形態)  
   * [オンボードグラフィック](#オンボードグラフィック)  
   * [ビデオカード](#ビデオカード)  
   * [USB端子接続型](#USB端子接続型)
* [GPUの計算の特徴](#GPUの計算の特徴)
* [参考文献](#参考文献)

### GPUとは

**GPU**とは、「**Graphics Processing Unit**」の略で、パソコンに搭載されている計算装置で、主に映像関連の計算処理を行うのに特化しています。

![f:id:pythonjacascript:20181124132307j:plain:h300](/images/ppythonjacascript2018112420181124132307.jpg "f:id:pythonjacascript:20181124132307j:plain:h300")  
（GTX1080）

CPUも計算処理を行う部品なのですが、CPUとの大きな違いは構造にあります。  
CPUはコア（一種の計算機）がせいぜい4個～十数個なのに対し、GPUは**約数千ものコア**が搭載されています！！  
[![https://www.ibm.com/developerworks/jp/cognitive/library/cc-machine-learning-deep-learning-architectures/figure01.png](https://www.ibm.com/developerworks/jp/cognitive/library/cc-machine-learning-deep-learning-architectures/figure01.png)](https://www.ibm.com/developerworks/jp/cognitive/library/cc-machine-learning-deep-learning-architectures/figure01.png)  
（[深層学習のアーキテクチャー](https://www.ibm.com/developerworks/jp/cognitive/library/cc-machine-learning-deep-learning-architectures/)より引用）

  
そのため、GPUは多くの計算を複数のコアで並列して行うことができます。そのため、画像処理やレンダリング作業の「**多くの計算を、より高速で**」というニーズにこたえることができるのです。

  
**つまり、こういうことです......。**

[youtu.be](https://youtu.be/-P28LKWTzrI)  
（Youtube [Mythbusters Demo GPU versus CPU - YouTube](https://youtu.be/-P28LKWTzrI)より）

GPUはやばいな...ということです。ハイ。  
  
### 搭載形態

このようなGPUをパソコンに搭載するとき、大きく2つの選択肢があります。「**オンボードグラフィック**」方式と、「**ビデオカード**」を増設する方法です。

また、GPUを搭載した専用の外部計算機をUSBなどを通して接続する方法があります。  
  
#### オンボードグラフィック

![f:id:pythonjacascript:20181210011716j:plain:h300](/images/ppythonjacascript2018121020181210011716.jpg "f:id:pythonjacascript:20181210011716j:plain:h300")  
「**オンボードグラフィック**」とは、パーソナルコンピュータのマザーボード上に搭載されている GPU のことです。

実装形態としては、マザーボードに1つのチップとして直接実装しているものと、チップセット[\*1](#f-1fa548fc "メモリや周辺機器接続回路、CPUとの通信回路などの、複数の機能を1～2個のチップに集積したもの。マザーボードに実装されている。")に搭載しているものがあります。

  
オフィス製品を使ったり、ブラウジングのみを行う時など、強力な描画性能を必ずしも必要としない時は、オンボードのグラフィックでも十分です。  
**低価格・省電力・軽量化**が求められるパソコンには大抵オンボードグラフィックが採用されます。  
  
#### ビデオカード

しかし、3DゲームやAviUtlやAfter Effectsなどによる動画編集をする場合、オンボードのGPUでは計算処理が追い付かなくなる可能性があります。そこで、導入されるのが「**ビデオカード**」と呼ばれるものです。

![f:id:pythonjacascript:20181124132307j:plain:h300](/images/ppythonjacascript2018112420181124132307.jpg "f:id:pythonjacascript:20181124132307j:plain:h300")  
冒頭のこの写真も、ビデオカードの一種で、「GTX1080」です。

ビデオカードの中には、**GPU**とそれ専用のメモリ（**ビデオメモリ**という）、補助電源装置、出力端子、基盤が搭載されています。オンボードのGPUとは比べ物にならないほどの性能を発揮します。  
![f:id:pythonjacascript:20181210014009j:plain:h300](/images/ppythonjacascript2018121020181210014009.jpg "f:id:pythonjacascript:20181210014009j:plain:h300")

#### USB端子接続型

Apple社が開発している製品で、「**eGPU**」というものがあります。  
![f:id:pythonjacascript:20181210013708j:plain:h300](/images/ppythonjacascript2018121020181210013708.jpg "f:id:pythonjacascript:20181210013708j:plain:h300")

  
これは、いわば「**外付けGPU**」で、**Thunderbolt 3ポート**でパソコンにつなぐと、eGPU内部のGPUで計算処理を行ってくれる、というものです。

[www.apple.com](https://www.apple.com/jp/shop/product/HM8Y2J/A/blackmagic-egpu?fnode=302ca1336a67a0d80c17a42ada60e49d3ce5b954f782e716280a6322467911a2ecbdc82b76c9e181cbccefdcdf5d94b6b8f6c62d23ed0f618c422bcf16d244926a8890b526836219203ded4b5bdec930b5a012ecd2909f7d7e51bfe06b2be670)  
  
  
### GPUの計算の特徴

この項目は、下のページをまとめたものです。  
[Graphics Processing Unit - Wikipedia](https://ja.wikipedia.org/wiki/Graphics%5FProcessing%5FUnit#NVIDIA%5FFermi%E3%82%A2%E3%83%BC%E3%82%AD%E3%83%86%E3%82%AF%E3%83%81%E3%83%A3%E3%81%AE%E4%BE%8B)

ここでは、**NVIDIA Fermiアーキテクチャ**のGPUの構造を見ていきます。  
  
  
### 参考文献

[ビデオカード - Wikipedia](https://ja.wikipedia.org/wiki/%E3%83%93%E3%83%87%E3%82%AA%E3%82%AB%E3%83%BC%E3%83%89#%E3%83%93%E3%83%87%E3%82%AA%E3%82%AB%E3%83%BC%E3%83%89%E3%81%AE%E6%A7%8B%E6%88%90)

[グラフィックカードの構成～単体GPU、VRAM、出力端子、補助電源端子](http://www.pasonisan.com/pc-gpu/02grabo-kousei.html)  
<https://www.softek.co.jp/SPG/Pgi/TIPS/public/accel/gpu-accel2.html>

[\*1](#fn-1fa548fc):メモリや周辺機器接続回路、CPUとの通信回路などの、複数の機能を1～2個のチップに集積したもの。マザーボードに実装されている。