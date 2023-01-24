---
title: "【Python】PythonでWeb上のファイルを操作（ダウンロード）"
emoji: "🤖"
type: "tech"
topics: []
published: false
---

* [1.URLからHTMLや画像や音楽etc...をダウンロードする](#1URLからHTMLや画像や音楽etcをダウンロードする)  
   * [Webサイトの場合](#Webサイトの場合)  
   * [画像の場合](#画像の場合)
* [WebサイトのHTMLを文字データで取得](#WebサイトのHTMLを文字データで取得)
* [サイトの情報を取得](#サイトの情報を取得)

### 1.URLからHTMLや画像や音楽etc...をダウンロードする

**urlretrieve()**関数を使うことでインターネット上のサイトや画像をダウンロードすることができます。  
urlretrieve関数の**第一引数にURL、第二引数に保存する名前（拡張子も）**を代入します。  

#### Webサイトの場合

#-*- coding:utf-8 -*-
import urllib.request

URL = "https://shizenkarasuzon.hatenablog.com/about"
Name = "sample.html"
urllib.request.urlretrieve(URL, Name)

#### 画像の場合

#-*- coding:utf-8 -*-
import urllib.request

URL = "/images/ppythonjacascript2018112620181126232555.jpg"
Name = "C:/Users/Owner/Pictures/sample.jpg"
urllib.request.urlretrieve(URL, Name)

このように保存する場所を指定することもできます。

URL = "/images/ppythonjacascript2018112620181126232555.jpg"
Name = "C:/Users/Owner/Pictures/sample.jpg"
urllib.request.urlretrieve(URL, Name)
  
  
### WebサイトのHTMLを文字データで取得

#-*- coding:utf-8 -*-
import urllib.request

html = urllib.request.urlopen('https://shizenkarasuzon.hatenablog.com/about').read()
print(html)

### サイトの情報を取得

#-*- coding:utf-8 -*-
import urllib.request
URL = "http://python.org/"
conn = urllib.request.urlopen(URL)
Info = conn.info()

print(Info["Date"])
print(Info["Server"])
print(Info["Content-Length"])
print(Info["Age"])
print(Info["Connection"])
print(Info["Vary"])

上のようなプログラムを実行すると、下のように出力されます。（2019/08/16）

Fri, 16 Aug 2019 11:29:54 GMT
nginx
48535
2704
close
Cookie

**info()関数**を使うことで、指定したURLの情報を指定することができます。  
info()関数の戻り値は**辞書オブジェクト**です。

urlretrieve関数の戻り値を使って情報を取得することもできます。

#-*- coding:utf-8 -*-
import urllib.request
URL = "http://python.org/"
fn, h = urllib.request.urlretrieve(URL)
print(h.items())

結果：

[('Server', 'nginx'), ('Content-Type', 'text/html; charset=utf-8'), （略） ('Via', '1.1 varnish'), ('Age', '3489'), ('Connection', 'close')]