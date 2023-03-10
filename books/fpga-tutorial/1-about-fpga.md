---
title: "【FPGA】FPGAって何ぞや？"
emoji: "🤖"
type: "tech"
topics: undefined
published: false
---

**FPGA（field-programmable gate array）**は、製造後に購入者や設計者が構成を設定できる集積回路であす。  
論理回路設計を半田付けではなくPCで設計してそれをコンパイル・書き込みという手順を踏んでに回路を作成します。そのため、回路をすぐに修正できるという利点があります。

  
先日、ネットでこのような記事を見つけました。  
[gigazine.net](https://gigazine.net/news/20150731-fulltr-11-super-l/)

こちらの方は、下のように、トランジスタの回路を組んでCPUを自作していました...（すごい）。  
![f:id:pythonjacascript:20181128003339j:plain](/images/ppythonjacascript2018112820181128003339.jpg "f:id:pythonjacascript:20181128003339j:plain")  
（上記リンクより引用）  
このトランジスタによるCPUは、半田付けで回路をつなげて製作しているようです。

しかし、FPGAを使うと、PC上で↑の回路図を設計し[\*1](#f-7ef4bfa5 "正確にはFPGA専用の言語を使う")、そのデータをFPGAという一つのチップに書き込むだけで、上と同じCPUが完成するのです。  
そのうえ、FPGAは多くの半導体が一つのチップの中に集積されているので、↑のように半田付けで製作するよりも圧倒的に速いです。

  
専用回路を自由に構成できるので、使わない機能を搭載する必要はありません。また、リアルタイム性が必要な機能だけを並列化して高速化、他の機能は回路を兼用して小さくするなど、独自の特徴を出すことができます。そのため、CPUを自作する必要があるときなどによく使用されます。  
  
  
### 論理回路とは？

いままで、「論理回路」というワードが何度も出てきました。

> 「論理回路」とは、論理演算を行う電気回路及び電子回路である。真理値の「真」と「偽」、あるいは二進法の「0」と「1」を、電圧の正負や高低、電流の方向や多少、位相の差異、パルスなどの時間の長短、などで表現する回路

つまり、下の図のような物を言います。  
  
  
### FPGAの構造

FPGAは、何度もプログラムを書き換えることのできるプログラムメモリを持っています。また、このメモリは不揮発性（電源を切ってもデータが保持されること）が必要なので、SRAMが使用されています。 

SRAM型FPGAに電源が投入されると、この外部記憶メディアからプログラムファイルがロードされます。ここで呼び出されるプログラムファイルを「ビットストリームデータ」と呼びます。この情報は、FPGA内の1bit列のSRAM型メモリセルで構成されているコンフィグレーションメモリにロードされます。ロードされたビットストリームデータの個々のbitがFPGA上で実現するユーザー回路の情報元となり、FPGAに装備されているリソースをカスタマイズしてユーザー所望の回路を実現するのです。PCなどにおけるCPUのブートプロセスと非常によく似ています。ハードウェアに依存せず、コンフィグレーションデータを外部に持つことで、高い汎用性とフレキシビリティを実現しているのです。

 次に、FPGAの内部構造を説明します。FPGA内は、

I/O部  
内部配線  
ロジックセル  
クロックネットワーク  
JTAGバウンダリ・スキャン回路  
ブロックRAM・乗算器  
の6つの部分に大別できます（図3）。
  
  
一方でFPGAにとって、プログラムとは構成情報です。構成情報とは内部ユニット類（論理演算・DSP・メモリ）の設定・結線・信号線配置を含む情報で、フローと呼ばれます。FPGAにフローを与えると内部ユニットの構成が変化し、信号の流れ（パイプライン）が組まれます。FPGA開発におけるアルゴリズムの実装とは、希望する処理を行なうパイプラインを組むことです。  
  
  
### CPLDとの違い

#### CPLD（1980年代後半～）

もう1つのデバイスである「**CPLD**」（Complex PLD）が登場しています。PLD（programmable logic device）とは、製造後にユーザの手許で内部論理回路を定義・変更できる集積回路の総称で、FPGAはこの一首に入ります。

CPLDは、複数個の小規模なPLDを1個のLSIに集積することで、任意の大規模論理回路を実現したPLDです。小規模PLDと同じように、不揮発でありつつ書き換えが可能という使い勝手の良さが最大の特徴でした。その代わり、設計自由度の高さではFPGAに及びませんでした。  
  
#### SRAMタイプ（1990年代～）

何度でもプログラムを書き込・変更が可能なタイプのFPGAです。これは、見出し通り、プログラムの保存に**SRAM**を使用しているからです。  
  
  
### コンパイラ

#### Synplify

[Synplify Pro](https://www.synopsys.com/ja-jp/implementation-and-signoff/fpga-based-design/synplify-pro.html)  

#### Quartus II

アルテラ社の開発ソフトウェア「Quartus II」

  
Xilinx ISE WebPack  
BASY2用  
[ダウンロード](http://japan.xilinx.com/support/download/index.html/content/xilinx/ja/downloadNav/design-tools.html)
  
  
[ＢＡＳＹＳ２ Ｓｐａｒｔａｎ ３Ｅ １００Ｋ 評価ボード: 半導体 秋月電子通商-電子部品・ネット通販](http://akizukidenshi.com/catalog/g/gM-07737/)
  
  
[\[VHDL\] BASYS2 と開発環境のインストール | オールトの雲](http://ooltcloud.sakura.ne.jp/blog/201510/article%5F24160358.html)  
[FPGAを使い始めるための基礎知識 ―― 開発フローとやるべき作業を理解する｜Tech Village （テックビレッジ） ／ CQ出版株式会社](http://www.kumikomi.net/archives/2009/04/fpga.php?page=2)

[\*1](#fn-7ef4bfa5):正確にはFPGA専用の言語を使う