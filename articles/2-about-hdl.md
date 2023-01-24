---
title: "【HDL】ハードウェア記述言語とは？"
emoji: "🤖"
type: "tech"
topics: []
published: false
---

* [VHLについて](#VHLについて)
* [VHLの種類](#VHLの種類)  
   * [VHDLとVerilog HDLの違い](#VHDLとVerilog-HDLの違い)  
   * [VHDLの記述例](#VHDLの記述例)
* [Verilog-HDLの記述例](#Verilog-HDLの記述例)
* [参考文献](#参考文献)

### VHLについて

VHLとは、「**Hardware Description Language**」、つまり「**ハードウェア記述言語**」のことです。  
ハードウェアの動作仕様を記述するのに使います。

通常、論理回路を組むときは半田付けなどで物理的に回路を作成しますが、VHLを使うと、**プログラミングを行うように回路の作成を行う**ことができます。**FPGA**の回路設計を行う時にも、このVHLを使用します。  
  
  
### VHLの種類

VHLには、主に**VHDL**と**Verilog HDL**の2種類があります。

どちらも、IEEEによって標準化されており、両方ともよく使われます。  
例えば、FPGAの開発ソフトウェアである、**QuartusII**は、VHDL と Verilog HDL 両方の回路シミュレーションソフトウェアが搭載されています。

#### VHDLとVerilog HDLの違い

おもに、以下の表のような違いがあります。

| 項目      | VHDL | Verilog HDL |
| ------- | ---- | ----------- |
| 習得しやすさ  | ×    | 〇           |
| 文法的な曖昧さ | 〇    | ×           |
| 記述の容易さ  | ×    | 〇           |
| 符号OK    | 〇    | ×           |

ということで、初心者の私は書きやすいVerilog HDLについて学んでいきます。

以下が、コード記述の例です。

#### VHDLの記述例

library IEEE;
use IEEE.std_logic_1164.all;
entity HA is
   port (
       A, B : in std_logic;
       S, C : out std_logic
    );
end HA;

architecture RTL of HA is
signal x, y : std_logic;
begin
     x <= A or B;
     y <= A nand B;
     S <= x and y;
     C <= not y;
end RTL;

このようになります。  
  
### Verilog-HDLの記述例

続いて、Verilog-HDLを使用して上記と同じ回路を作成するためのプログラムです。

module HA (S, C, A, B);
    input A, B;
    output S, C;
    wire x, y;
    assign x = A | B,
               y = ~(A & B),
               S = x & y,
               C = ~y;
endmodule

Verilog-HDLには、「**モジュール**」という考え方があり、そのモジュールの中に回路を書き込みます。  
そして、モジュールを入れ込構造にしたり複数配置することで、回路を作成していきます。

両者を比較するとわかるように、**Verilog-HDLのほうが記述量が少なくなります**。

  
ということで、初心者の私はVerilog-HDLを学ぼうと思いました。

### 参考文献

<http://www.cqpub.co.jp/dwm/contents/0007/dwm000701080.pdf>  
<http://www.cqpub.co.jp/hanbai/books/33/33631/33631%5F018-019.pdf>