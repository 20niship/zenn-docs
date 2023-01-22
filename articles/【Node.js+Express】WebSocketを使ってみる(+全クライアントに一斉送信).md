---
title: "【Node.js + Express】WebSocketを使ってみる( + 全クライアントに一斉送信)"
emoji: "🤖"
type: "tech"
topics: []
published: false
---

解説が面倒なのでサンプルだけ。

### サンプル1

* WebSocketを使ったNode.jsのサンプルです。
* 動作は  
   1. 送信ボタンを押すと、WebSocketを使ってサーバーに「Hello」文字列が送信されます。  
   2. サーバーのコンソールにWebSocketで受信した文字列（Hello）が表示され、クライアントにWebSocketを使って文字列を送り返します。
* という感じです。
* F12などで開発者ウィンドウを開いて、console.log() で出力された内容が見られるようにしておく必要があります。

const express = require('express');
var fs = require('fs');

const app = express();
const WebSocket = require('ws');
const wss = new WebSocket.Server({ port: 8000 });

wss.on('connection', function connection(ws) {
  ws.on('message', function incoming(message) {
    console.log('received: %s', message);
    ws.send('something');
  });
  ws.send('something');
});


app.get('/', function(req, res) {
    fs.readFile("./index.html", function (err, data) {
        res.writeHead(200, { "Content-Type": "text/html" });
        res.write(data);
        res.end();
    });
});

app.listen(8080);

  
以下の内容で「index.html」を作成します。

<!DOCTYPE html>
<html>
<head><meta charset="UTF-8" /><title>Hello</title></head>

<script type="text/javascript">
    console.log("送信ボタンを押して下さい");
    var sock = new WebSocket('ws://127.0.0.1:8000');

    sock.addEventListener('open',function(e){// 接続
        console.log('Socket 接続成功');
    });

    sock.addEventListener('message',function(e){// サーバーからデータを受け取る
        console.log(e.data);
    });

    document.addEventListener('DOMContentLoaded',function(e){
        document.getElementById('sample').addEventListener('click',function(e){
            console.log("send Hello!!");
            sock.send('hello');// WebSocketでサーバーに文字列を送信
        });
    });
</script>
</head>

<body>
    <h1>Hello Node.js!</h1>
    <input type="button" id="sample" value="送信">
</body>
</html>
  
  
##### 実行

$npm install express
$npm install 

  
**_localhost:8080/_** にアクセスし、送信ボタンを押すとWebSocketが動いていることが解ると思います、、、  
![f:id:pythonjacascript:20210420223013p:plain](/images/ppythonjacascript2021042020210420223013.png)

### サンプル2 同時に全クライアントに送信する

以下のように、CLIENT配列を作ることで、現在接続されているすべてのクライアントにWebSocketで送信された文字列を共有することができます。

const express = require('express');
var fs = require('fs');
const app = express();
const WebSocket = require('ws');
const wss = new WebSocket.Server({ port: 8000 });

var CLIENTS=[]; // クライアントのリスト
var id;

wss.on('connection', function(ws) {
    id = Math.floor(Math.random() * 999999999);
    console.log('新しいクライアント： ' + id);
    CLIENTS.push(ws); //クライアントを登録

    ws.on('message', function(message) {
        console.log('received: %s', message);
        ws.send("self message : " + message);  // 自分自身にメッセージを返す

        for (var j=0; j < CLIENTS.length; j++) {
          //他の接続しているクライアントにメッセージを一斉送信
            if(ws !== CLIENTS[j]) {CLIENTS[j].send("send from " + id + " : " + message);} 
        }
    });

    ws.on('close', function() {
        console.log('ユーザー：' + id + ' がブラウザを閉じました');
        delete CLIENTS[id];
    });
});

app.get('/', function(req, res) {
    fs.readFile("./index.html", function (err, data) {
        res.writeHead(200, { "Content-Type": "text/html" });
        res.write(data);
        res.end();
    });
});

app.listen(8080);
  
  
##### 実行結果

こんな感じで、複数ブラウザで同時にログインし、送信ボタンを押すと、すべてのブラウザに「hello」メッセージが共有されます。

![f:id:pythonjacascript:20210420223638p:plain](/images/ppythonjacascript2021042020210420223638.png)