---
title: "【数列】NO.2　等差数列"
emoji: ""
type: ""
topics: undefined
published: false
---

* [等差数列とは？](#等差数列とは)
* [n番目の値を求める](#n番目の値を求める)  
   * [公式を使う](#公式を使う)  
   * [公式の考え方](#公式の考え方)
* [n番目までの和を求める](#n番目までの和を求める)  
   * [公式を使う](#公式を使う-1)  
   * [公式の考え方](#公式の考え方-1)

### 等差数列とは？

等差数列とは、同じ数ずつ増えていく数列のことです。つまり、こういうことです。  
![f:id:pythonjacascript:20180914013534j:plain](/images/ppythonjacascript2018091420180914013534.jpg "f:id:pythonjacascript:20180914013534j:plain")  
上の図から分かるように、数列「1, 3, 5, 7, 9, 11, ...」は、隣り合っている項の「**差**」が「**等しく**」なっています。なので、「1, 3, 5, 7, 9, 11, ...」は「**等差数列**」です。

  
では、等差数列について問題を解いていきます。

### n番目の値を求める

> 例題：等差数列 5, 9, 13, 17, 21 , 25... の一般項（ｎ番目の値）を求めよ。

このような「ｎ番目の値を求めよ」という問題の解き方は「公式を使う」方法と、「考えて解く」方法があります。どちらも長所・短所があるので両方紹介します。  
  
#### 公式を使う

等差数列の公式：  
**![（等差数列のｎ番目の値）＝（初項）+（1 - （項数））×（交差）](https://chart.apis.google.com/chart?cht=tx&chl=%EF%BC%88%E7%AD%89%E5%B7%AE%E6%95%B0%E5%88%97%E3%81%AE%EF%BD%8E%E7%95%AA%E7%9B%AE%E3%81%AE%E5%80%A4%EF%BC%89%EF%BC%9D%EF%BC%88%E5%88%9D%E9%A0%85%EF%BC%89%2B%EF%BC%881%20-%20%EF%BC%88%E9%A0%85%E6%95%B0%EF%BC%89%EF%BC%89%C3%97%EF%BC%88%E4%BA%A4%E5%B7%AE%EF%BC%89)**

数式で表すと、このようになります。  
**![a_n = a + d(n-1)](https://chart.apis.google.com/chart?cht=tx&chl=a_n%20%3D%20a%20%2B%20d%28n-1%29)** 
  
![a_n](https://chart.apis.google.com/chart?cht=tx&chl=a_n)は数列![{a_n}](https://chart.apis.google.com/chart?cht=tx&chl=%7Ba_n%7D)の![n](https://chart.apis.google.com/chart?cht=tx&chl=n)番目の値、![a](https://chart.apis.google.com/chart?cht=tx&chl=a) は初項、 ![ d](https://chart.apis.google.com/chart?cht=tx&chl=%20d) は交差を表しています。 

（「**交差**」とは、「数列で、値が何ずつ増えているか？」です。例えば、上の例題の場合、数列「5, 9, 13, 17, 21 ...」は値が4ずつ増えていっているので、交差は4です。）

この公式を使うと、一発で上の数列のｎ番目の値を求めることができます。

**（解）**  
数式「5, 9, 13, 17, 21 , 25」は、**初項が5、項数が６、交差が4**の等差数列である。  
よって公式に代入すると、

![a_{n}=5+\left( 6-1\right) \times 4 = 25](https://chart.apis.google.com/chart?cht=tx&chl=a_%7Bn%7D%3D5%2B%5Cleft%28%206-1%5Cright%29%20%5Ctimes%204%20%3D%2025)

  
#### 公式の考え方

例えば、数列 ![ {a_n} = {5, 7, 9, ...}](https://chart.apis.google.com/chart?cht=tx&chl=%20%7Ba_n%7D%20%3D%20%7B5%2C%207%2C%209%2C%20...%7D)となっているとします。下の図を見るとわかるように、![n](https://chart.apis.google.com/chart?cht=tx&chl=n) の数が1増えるとこのとき、 ![ a_n](https://chart.apis.google.com/chart?cht=tx&chl=%20a_n) は交差（この場合は2）ずつ増えていることがわかります。  
![f:id:pythonjacascript:20180914015728j:plain](/images/ppythonjacascript2018091420180914015728.jpg "f:id:pythonjacascript:20180914015728j:plain")  
 このため、例えば![ a_5](https://chart.apis.google.com/chart?cht=tx&chl=%20a_5) を求めるには、初項に交差の2を4回足せばいいことになります。  
よって、![a_5 = 5 + \left( 5-1\right) \times 4 = 21](https://chart.apis.google.com/chart?cht=tx&chl=a_5%20%3D%205%20%2B%20%5Cleft%28%205-1%5Cright%29%20%5Ctimes%204%20%3D%2021)となります。   
  
### n番目までの和を求める

#### 公式を使う

#### 公式の考え方