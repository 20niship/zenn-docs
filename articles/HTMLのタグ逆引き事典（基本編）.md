---
title: "HTMLのタグ逆引き事典（基本編）"
emoji: ""
type: ""
topics: undefined
published: false
---

にゃんぱｓ (ry

  
HTML初心者用に、覚えておくべきタグとその使い方（サンプル）を紹介します！

各タグにおいて以下のことを説明します

* タグ名
* 使い方（サンプル）
* よく使うCSSプロパティなどの紹介
* [＜html＞ - HTML文書であることを指定](#html---HTML文書であることを指定)
* [＜title＞ - タイトル（タブに表示される文字列）を指定](#title---タイトルタブに表示される文字列を指定)
* [＜meta> - meta情報を付与](#meta---meta情報を付与)  
   * [文字エンコーディングを設定](#文字エンコーディングを設定)  
   * [著書](#著書)  
   * [概要](#概要)  
   * [キーワード](#キーワード)  
   * [OGPの設定（SNSでシェアしたときに表示されるタイトルやトップ画像を設定）](#OGPの設定SNSでシェアしたときに表示されるタイトルやトップ画像を設定)
* [link - 外部CSSファイルの読み込み](#link---外部CSSファイルの読み込み)
* [style - CSSの指定（HTMLファイル中に埋め込み）](#style---CSSの指定HTMLファイル中に埋め込み)
* [script - JavaScript読み込み](#script---JavaScript読み込み)
* [p - 段落（paragraph)](#p---段落paragraph)
* [文字修飾関連](#文字修飾関連)  
   * [b](#b)  
   * [i](#i)  
   * [em](#em)  
   * [strong](#strong)  
   * [del](#del)
* [a - リンク（anchor)](#a---リンクanchor)
* [h1 - h6 - 見出し（header)](#h1---h6---見出しheader)
* [img - 画像](#img---画像)
* [ul - li -](#ul---li--)
* [ol - li -](#ol---li--)
* [div](#div)
* [pre](#pre)
* [code](#code)
* [table,](#table)

  
ではスタート！！  
  
### ＜html＞ - HTML文書であることを指定

HTMLファイルの一番トップの階層で使用します。HTMLタグでファイル全体を囲み、その中に＜HEAD>タグと＜BODY>タグを一つづつ含む構成が一般的です。

<!DOCTYPE html>
<html>
<head>
  <meta charset="utf-8">
  <title>タイトル</title>
</head>

<body>本文</body>
</html>
  
  
### ＜title＞ - タイトル（タブに表示される文字列）を指定

下の用に、**htmlタグの中のheadタグの中に書いて下さい**

<html>
<head>
  <title>たいとるだよん</title>
</head>

<body> ほげほげ</body>
</html>

### ＜meta> - meta情報を付与

こちらも、titleタグと同様に、の中に書いて下さい 
* metaタグを追加しても、ブラウザで表示した時の見た目に変化はありません。
* しかし、文書のキーワードやタイトルを適切に設定することで、SEO対策になると言われています。

#### 文字エンコーディングを設定

文字エンコーディングを下記の様にしていします。

* HTML文書の先頭 1024バイト以内に記述
* ＜title＞タグが非ASCII文字（日本語等。詳しくはググって）を使用している場合は、＜title＞ よりも前に記述
* 日本語の場合は大体UTF-8（下のサンプル一行目）を書けばOk

<meta charset="UTF-8">
<meta charset="Shift_JIS">
<meta charset="euc-jp">
<meta charset="iso-2022-jp">

#### 著書

<meta name="author" content="Tohoho">

#### 概要

<meta name="description" content="HTMLリファレンス">

#### キーワード

* カンマで区切って指定
* SEO対策になる

<meta name="keywords" content="HTML,CSS,JavaScript">

#### OGPの設定（SNSでシェアしたときに表示されるタイトルやトップ画像を設定）

こんな感じで行います。

<head prefix="og: http://ogp.me/ns# fb: http://ogp.me/ns/ fb# prefix属性: http://ogp.me/ns/ prefix属性#">
<meta property="og:url" content=" ページの URL" />
<meta property="og:type" content=" ページの種類" />
<meta property="og:title" content=" ページの タイトル" />
<meta property="og:description" content=" ページのディスクリプション" />
<meta property="og:site_name" content="サイト名" />
<meta property="og:image" content=" サムネイル画像の URL" />

### link - 外部CSSファイルの読み込み
  
  
### style - CSSの指定（HTMLファイル中に埋め込み）

* 場所はどこに書いても動きますが、headタグの中に書かれることが多い

<style type="text/css">
  p {color:blue; line-height:1.5;}  /*例：文字を青色にするCSS*/
  /* その他CSSの設定文字列 */
</style>

  
### script - JavaScript読み込み

（「JavaScript読み込み」て書いたけれど、べつにJavaScript以外にもよみこめる。だが、ここではJSを例に紹介します）

* どこに書いても良いわけではない！！

 \- HTMLは上から下へ順番に読み込まれて解析される  
 \- つまり、bodyタグの上（headタグの中など）に書かれたJSが読み込まれる時、bodyはレンダリングされていない  
 \- そのような場合にJSでbodyタグの中のものを取得しようとしてもエラーになる！  
 \- 詳しくは[ここ](https://www.sejuku.net/blog/19754#JavaScript)など

以下の2つの方法があります

**1\. htmlファイル中に埋め込む**

<script type="text/javascript">
    alert("Hello JavaScript:D"); //ダイアログボックス表示
    // Javascriptファイル
</script>

  
**2\. 外部から読み込む**

<script src="myscripts.js"></script>
  
  
### p - 段落（paragraph)

### 文字修飾関連

一気に行きます！

#### b

#### i

#### em

#### strong

#### del

### a - リンク（anchor)

### h1 - h6 - 見出し（header)

### img - 画像

### ul - li -

### ol - li -

### div

### pre

### code

### table,
  
  
つかれたーー！！おやすみ