---
title: "【はてなブログ】メニューバーの設置"
emoji: "🤖"
type: "tech"
topics: []
published: false
---

このブログで使われているメニューバーを公開します。下のプログラムをコピペするだけで、メニューバーを設置することができます。

![f:id:pythonjacascript:20180902205444p:plain](/images/ppythonjacascript2018090220180902205444.png "f:id:pythonjacascript:20180902205444p:plain")

このように表示されます。  
はてなブログの無料バージョンを使用している方でも使えます。  
  
  
* [HTMLのコード](#HTMLのコード)
* [CSSのコード](#CSSのコード)
* [参考文献](#参考文献)
  
  
### HTMLのコード

以下のコードを**「デザイン」→「カスタマイズ（🔧マーク）」→「ヘッダ」→「タイトル下、HTMLを追加できます」**の部分にコピペします。  
![f:id:pythonjacascript:20180902210119p:plain:h300](/images/ppythonjacascript2018090220180902210119.png "f:id:pythonjacascript:20180902210119p:plain:h300")  

<div id="menu">
<div id="menu-inner">
<div id="btn-content">
<span id="menu-btn"><i class="blogicon-reorder lg"></i>MENU</span></div>
<ul id="menu-content">
<li><a href="url" class="has-child">カテゴリーA</a>
<ul class="second-content">
   <li><a href="url">カテゴリーB</a></li>
   <li><a href="url">カテゴリーB</a></li>
   <li><a href="url">カテゴリーB</a></li>
   <li><a href="url">カテゴリーB</a></li>   
</ul>
</li>

<li>
<a href="url" class="has-child">カテゴリーA</a>
<ul class="second-content">
   <li><a href="url">カテゴリーB</a></li>
   <li><a href="url">カテゴリーB</a></li>
</ul>
</li>

<li>
<a href="url" class="has-child">カテゴリーA</a>
<ul class="second-content">
   <li><a href="url">カテゴリーB</a></li>
   <li><a href="url">カテゴリーB</a></li>
   <li><a href="url">カテゴリーB</a></li>
   <li><a href="url">カテゴリーB</a></li>
</ul>
</li>

</ul>
</div>
</div>
<div style="clear:both"></div>

<script type="text/javascript" src="http://code.jquery.com/jquery-1.9.1.min.js"></script>
<script>
$(function(){
    var menuBtn = $("#menu-btn"),
        menuContent = $("#menu-content");
    menuBtn.click(function(){
        menuContent.slideToggle();
    });

});
</script>

上のプログラム中で「url」と書かれた部分は、そのメニューを選択したときにジャンプさせたいページのURLを指定します。また、「カテゴリー」の部分には、自分のメニューのカテゴリー名を記入します。  
「カテゴリーA」は大きなカテゴリ（ヘッダ部分に並んで表示される）で、「カテゴリーB」は、「カテゴリーA」の中の小さいカテゴリーになります。

例えばこんな感じです。

<li><a href="https://shizenkarasuzon.hatenablog.com/entry/2018/08/26/212542" class="has-child">プログラミング</a>
<ul class="second-content">
   <li><a href="https://shizenkarasuzon.hatenablog.com/entry/2018/08/26/212542#1C%E8%A8%80%E8%AA%9E">C言語</a></li>
   <li><a href="https://shizenkarasuzon.hatenablog.com/entry/2018/08/26/212542#2Python">Python</a></li>
   <li><a href="https://shizenkarasuzon.hatenablog.com/entry/2018/08/26/212542#3Arduino">HTML</a></li>
   <li><a href="https://shizenkarasuzon.hatenablog.com/entry/2018/08/26/212542#4Processing">Processing</a></li>   
</ul>
</li>

### CSSのコード

以下のコードを**「デザイン」→「カスタマイズ（🔧マーク）」→「デザインCSS」**の中に記入してください。

![f:id:pythonjacascript:20180902211121p:plain](/images/ppythonjacascript2018090220180902211121.png "f:id:pythonjacascript:20180902211121p:plain")

/* <system section="theme" selected="6653586347155924442"> */
@import url("https://blog.hatena.ne.jp/-/theme/6653586347155924442.css");
/* </system> */

pre {
　font-size: 130%;
  color: #FFFFFF;
  background-color: #000000;
}

#menu{
    width: 100%;
    background: #000000;
    margin: 10px auto;
}

#menu-inner{
    width: 100%;
    height: 40px;
}

#menu-btn{display: none;}

#menu-content > li{
    position: relative;
    float: left;
    height: 100%;
    text-align: center;
    width: 25%;
}
#menu-content > li{
    position: relative;
    float: left;
    height: 100%;
    text-align: center;
    width: 20%;
}
#menu-content > li > a{
    position: relative;
    display: block;
    height: 100%;
    width: 100%;/*3*/
    line-height: 40px;/*２*/
    background: #444;/*９*/
    color: #fff;/*10*/
    font-size: 90%;
    text-decoration: none;
    z-index: 2;
}
#menu-content > li > a:hover{
    background: #A9E1F6;
    color: #fff;
}
#menu-content > li > a > .blogicon-chevron-down{margin-right: 5px;}
/*２階層目*/

#menu-content > li > ul.second-content{
    visibility: hidden;
    position: absolute;
    top: 0;
    margin: 0;
    padding-left: 0;
    list-style-type: none;
    z-index: -1;
}
#menu-content > li:hover > ul.second-content{
    visibility: visible;
    top: 40px;
    z-index: 1;
    transition: all .3s;
}
#menu-content > li > ul.second-content > li{
    text-align: center;
    width: 180px;
    height: 40px;
}
#menu-content > li > ul.second-content > li > a{
    display: block;
    line-height: 40px;
    background: #000000;
    color: #fff;
    font-size: 100%;
    text-decoration: none;
}
#menu-content > li > ul.second-content > li > a:hover{
    background: #A9E1F6;
    color: #fff;
}

@media screen and (max-width:968px) {
#menu-content > li > a{
    font-size: 60%;
}
#menu-content > li > ul.second-content > li > a{
    font-size: 60%;
}
}

### 参考文献

[はてなブログのメニューバーをカスタマイズ！記事本数が増えてきたらやると見やすい！ - アラフォーミニマリスト・人生をシンプルにしたたかに生き延びる☆](http://simplelifedancer.hatenablog.com/entry/2017/07/16/204219)