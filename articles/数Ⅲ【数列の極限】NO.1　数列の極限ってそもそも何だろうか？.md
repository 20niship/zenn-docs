---
title: "数Ⅲ【数列の極限】NO.1　数列の極限ってそもそも何だろうか？"
emoji: ""
type: ""
topics: undefined
published: false
---

授業がとうとう数Ⅲの「数列の極限」に突入したので、そのメモ用に書きます。

まだ習いたてなので、間違っているところがあるかもしれませんが、ご了承ください。  
![f:id:pythonjacascript:20180809033332j:plain](/images/ppythonjacascript2018080920180809033332.jpg "f:id:pythonjacascript:20180809033332j:plain")  
（引用源：<https://togetter.com/li/1112702>）  
（余談ですが、筆者もこのゲームをやろうとしたことはありましたが、当時数Ⅱしかやっていなかったので訳が分からなくなり、結論として  
[www.wolframalpha.com](https://www.wolframalpha.com/)  
を使ってしまいました。Wolfram最高！！）  
  
  
### 1.「数列の極限」とは？

#### (1) こんな問題が解けてほしい！

そもそも「数列の極限」とは何ぞや？この単元では、どんな問題が出るのか？ですが、僕のイメージはこんな感じです。

下のような数列があるとします。  
![ \a = {frac{1}{2}, frac{1}{4}, frac{1}{6}, frac{1}{8}, frac{1}{10} ...} ](https://chart.apis.google.com/chart?cht=tx&chl=%20%5Ca%20%3D%20%7Bfrac%7B1%7D%7B2%7D%2C%20frac%7B1%7D%7B4%7D%2C%20frac%7B1%7D%7B6%7D%2C%20frac%7B1%7D%7B8%7D%2C%20frac%7B1%7D%7B10%7D%20...%7D%20)  
数Bでは、

1. 「この数列のn番目の値（![\a_n](https://chart.apis.google.com/chart?cht=tx&chl=%5Ca_n)）は何なのか？」
2. 「この数列の最初からn番目までの合計は何になるのか？」

を考えてきました。

しかし、これが数Ⅲになると、  
「n番目」が、「無限番目（？）」になります。つまり、

1. 問題A「上の数列の無限番目の値は何なのか？」
2. 問題B「上の数列な値を全て（最初から無限番目まで）足したら[\*1](#f-1086afa4 "「足す」という言葉は、足す数に限りがある場合のみ使う言葉らしいので、数学的には間違っているそうですが、ここではわかりやすくするために、「足す」を使っています")何になるのか？」

を考えます。

この記事では、問題Aの解き方を解説します。問題Bの解き方は、次の記事を見てください。
  
  
2.問題を数式化して考える  
それを考えるために、問題Aを数式を使って表して見ます。

> 問題A：「数列aの無限番目の値は何なのか」

数Bの知識を用いて、数列のn番目の値は![\a_n](https://chart.apis.google.com/chart?cht=tx&chl=%5Ca_n)と表すことができます。問題Aで求めたい値は、このnを無限にした値と考えることができます。それを数式で表すにはどうしたらよいか…？

正解は「Limを使って、nを無限大に飛ばす」です。  
つまり、上の問題は、  
「![ \lim_{t \to \infty}a_n](https://chart.apis.google.com/chart?cht=tx&chl=%20%5Clim_%7Bt%20%5Cto%20%5Cinfty%7Da_n)を求めよ」  
という問題に書き換えられます。
  
  
2.解く手順  
問題Aの解き方を考えます。

問題A：「数列aの無限番目の値は何なのか」の解き方

上で説明したように、この問題は「「![ \lim_{t \to \infty}a_n](https://chart.apis.google.com/chart?cht=tx&chl=%20%5Clim_%7Bt%20%5Cto%20%5Cinfty%7Da_n)を求めよ」と同じなので、この問題を解くプロセスは以下のようになります。

① ![\a_n](https://chart.apis.google.com/chart?cht=tx&chl=%5Ca_n) をnを使った数式で表す。  
これは数Bの知識です。例えば、数列aが、  
![ \a = {frac{1}{2}, frac{1}{4}, frac{1}{6}, frac{1}{8}, frac{1}{10} ...} ](https://chart.apis.google.com/chart?cht=tx&chl=%20%5Ca%20%3D%20%7Bfrac%7B1%7D%7B2%7D%2C%20frac%7B1%7D%7B4%7D%2C%20frac%7B1%7D%7B6%7D%2C%20frac%7B1%7D%7B8%7D%2C%20frac%7B1%7D%7B10%7D%20...%7D%20)  
で表される場合、![\a_n](https://chart.apis.google.com/chart?cht=tx&chl=%5Ca_n) は、  
![\a_n =  \frac{1}{2n}](https://chart.apis.google.com/chart?cht=tx&chl=%5Ca_n%20%3D%20%20%5Cfrac%7B1%7D%7B2n%7D)  
ですね。

② ①で求めた値のLimをする。  
これは、よく行う計算なので、別の記事で解説します。Limの計算はパターンを守って機械的に行えば、誰でもできるようになります。  
（答え） ![\ = [tex: \lim_{t \to \infty}\frac{1}{2n](https://chart.apis.google.com/chart?cht=tx&chl=%5C%20%3D%20%5Btex%3A%20%5Clim_%7Bt%20%5Cto%20%5Cinfty%7D%5Cfrac%7B1%7D%7B2n)  
この2ステップで、問題Aを解くことができます。整理したら思ったより単純…かな？
  
  
では、例題を解いてみます。

例題

> 数列  
> があるとき、これの極限を求めよ。

この単元の重要語句ですが、  
「数列aの極限」とは、「数列aの無限番目の値」のことです。

  
では、例題を解いていきましょう。

ステップ① a\_n をnを使った数式で表す
  
  
ステップ② ①で求めた値のLimをする。
  
  
4.まとめ  
ざっとこんな感じです。まだ、この記事は基礎の基礎という感じです。この次の記事では、  
問題B：「上の数列な値を全て（最初から無限番目まで）足したら何になるのか？」

を考えます。
  
  
では、また。

[\*1](#fn-1086afa4):「足す」という言葉は、足す数に限りがある場合のみ使う言葉らしいので、数学的には間違っているそうですが、ここではわかりやすくするために、「足す」を使っています