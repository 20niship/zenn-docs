---
title: "JavaScriptでMarkdownパーサーを作る"
emoji: ""
type: ""
topics: []
published: false
---

このレポジトリについての記事です

[github.com](https://github.com/20niship/simple-markdown-parser)

  
タイトルにはMarkdownパーサーと書いていますが、正確にはMarkdown→HTMLの変換を行うものです。  
  
* [概要](#概要)
* [各モジュールの変換](#各モジュールの変換)  
   * [表](#表)  
   * [リスト](#リスト)  
   * [見出し](#見出し)  
   * [引用](#引用)
* [画像、リンク等の細かい記法の変換](#画像リンク等の細かい記法の変換)

### 概要

[このparse.js](https://github.com/20niship/simple-markdown-parser/blob/master/parser.js)のparse()関数は、Markdownを渡すとそれをHTMLに変換した文字列を返します。この記事はその実装の内容備忘録です

* parse.jsが呼ばれると、

### 各モジュールの変換

#### 表

#### リスト

#### 見出し

#### 引用

### 画像、リンク等の細かい記法の変換