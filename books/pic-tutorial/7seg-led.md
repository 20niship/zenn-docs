---
title: "【PIC】複数の7セグメントLED表示法（ダイナミック制御）"
emoji: ""
type: ""
topics: []
published: false
---

PICで下のように7セグメントLEDの表示器を作ってみました。  
![f:id:pythonjacascript:20181202143212j:plain](/images/ppythonjacascript2018120220181202143212.jpg "f:id:pythonjacascript:20181202143212j:plain")

### 7セグメントLEDの構造

**7セグメントLED**は、このように1桁のデジタル数字を表示するためのLED表示器です。  
![f:id:pythonjacascript:20181202143158j:plain](/images/ppythonjacascript2018120220181202143158.jpg "f:id:pythonjacascript:20181202143158j:plain")  
また、これを複数並べて制御することで複数桁の数字を表示することもできます。

ピンは1つの7セグ当たり10ピンで、その入力値によって表示する数値を操作します。  
  
### アノードコモンとカソコードコモン

上で、ひとつの表示器当たり8つのピンがあるといいました。それぞれのピンは、**数字**を表す７つのLED（**a～g**）と、小数点を表す**DP**というLED、そして**電源用のピン**が2つあります。  
そのピンアサインには**アノードコモン**と**カソードコモン**という2種類があります。

![f:id:pythonjacascript:20181202143552j:plain](/images/ppythonjacascript2018120220181202143552.jpg "f:id:pythonjacascript:20181202143552j:plain")

上記の8つのLED（数字用7つと小数点用1つ）は、アノードもしくはカソードのどちらかが共通して接続されています。**アノード（+極）が接続されているものをアノードコモン**、**カソード（GND）が接続されているものをカソードコモン**といいます。

表示する数字の制御の考え方は同じですが、プラスマイナスが逆なので、回路図が変わってきます。  
  
### 1.ダイナミック制御

7セグLEDを1つ使うと、一桁の数字を表示できるといいましたが、1桁では実用性はあまりないでしょう。そこで、7セグLEDを複数使用して、複数桁wと表示するアルゴリズムについて考えてみます。  
このときに、よく使われているアルゴリズムが「**ダイナミック制御**」と呼ばれる制御方法です。

ダイナミック制御とは、「**表示する桁を高速でずらして一桁ずつ表示していき、目の残像によって全ての桁を同時に表示しているかのように錯覚させる制御方法**」です。

例えば、「12345」という5桁の数字を表示する場合、次のように制御します。

![f:id:pythonjacascript:20181202144531j:plain](/images/ppythonjacascript2018120220181202144531.jpg "f:id:pythonjacascript:20181202144531j:plain")  

1. 12345の1桁目（5）を1の位に表示
2. 表示する桁を一つ左に移動
3. 12345の2桁目（4）を10の位に表示
4. 表示する桁を一つ左に移動
5. 12345の3桁目（3）を100の位に表示
6. 表示する桁を一つ左に移動
7. 12345の4桁目（2）を1000の位に表示
8. 表示する桁を一つ左に移動
9. 12345の5桁目（1）を10000の位に表示

という一連の動作を**数十～数百Hz**で繰り返します。

  
そうすると、目の残像現象によって、すべての桁が同時に表示されているように見えて、「12345」と表示しているように見えるのです。  
  
### 3.回路図（複数桁7セグ）

上記の7セグメントLEDの表示器は下の回路図のように回路を組むことで簡単に製作可能です。  
![f:id:pythonjacascript:20181202145119j:plain](/images/ppythonjacascript2018120220181202145119.jpg "f:id:pythonjacascript:20181202145119j:plain")

**注意点**  
・上の回路図では、7セグLEDは**カソードコモン**です。  
・トランジスタは**2SC1815**です。  
・抵抗の値は適当に決めてください。

左側のピンのうち、上から5番目までが表示する数字を指定する信号を入力する端子、1番したの端子がGND、それ以外のピンは表示する桁の指定信号の入力端子です。

  
実際に制作したものです  
![f:id:pythonjacascript:20181202145235j:plain](/images/ppythonjacascript2018120220181202145235.jpg "f:id:pythonjacascript:20181202145235j:plain")

プログラムはこちら  
[shizenkarasuzon.hatenablog.com](https://shizenkarasuzon.hatenablog.com/entry/2018/12/02/153459)

### 参考文献

[プログラミング・メモ－LEDのダイナミック点灯](http://elec-hobbyist.com/MicomMemo/Pgm%5Fmethod%5F1.html)