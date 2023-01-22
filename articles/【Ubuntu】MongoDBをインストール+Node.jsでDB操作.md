---
title: "【Ubuntu】MongoDBをインストール + Node.jsでDB操作"
emoji: ""
type: ""
topics: undefined
published: false
---

MongoDBはNoSQLと呼ばれるデータベースの一種です。  
RDBMSのようにレコードをテーブルに格納するのではなく、「ドキュメント」と呼ばれる構造的データをJSONライクな形式で表現し、そのドキュメントの集合を「コレクション」として管理します。

  
[www.mongodb.org](https://www.mongodb.org/)

### インストール

公式ドキュメント（下リンク）に書かれている通りに実行します。  
[Install MongoDB Community Edition on Ubuntu — MongoDB Manual](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-ubuntu/)

$ wget -qO - https://www.mongodb.org/static/pgp/server-4.4.asc | sudo apt-key add -
$ echo "deb [ arch=amd64,arm64 ] https://repo.mongodb.org/apt/ubuntu focal/mongodb-org/4.4 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-4.4.list
$ sudo apt-get update
$ sudo apt-get install -y mongodb-org

これでインストールは完了です。

mongoDBを起動させるには、以下のどちらかのコマンドを実行します。

# 以下のどちらか一方を実行
$ sudo service mongod start 
$ sudo systemctl start mongod

### サンプルプログラム

mongoDBにアクセスしusersコレクションを編集するNode.jsのプログラムです。

function sleep(ms) {
  return new Promise(resolve => { setTimeout(resolve, timeout=ms); })
}


console.log ("*** 開始 ***")

const MongoClient = require('mongodb').MongoClient;
const assert = require('assert');

const dbName = 'myproject';
const client = new MongoClient('mongodb://localhost:27017');

//サーバーに接続する 
client.connect(function(err) {
  assert.strictEqual(null, err);
  console.log("Connected successfully to server");
  const db = client.db(dbName);

   // usersコレクションを作成し、そこにアクセスする。
   const users = db.collection('users');
   users.deleteMany({}); //usersのすべてのデータを削除

    // ====================
    // データ追加
    // ====================
    
    // userを追加
    users.insertOne({name: 'name1', age:1, sex:"male"});
    users.insertOne({name: 'name2', age:2, sex:"male"});
    users.insertOne({name: 'name3', age:3, sex:"male"});
    users.insertOne({name: 'name4', age:4, sex:"female"});
    users.insertOne({name: 'name5', age:5, sex:"female"});
    users.insertOne({name: 'name6', age:6, sex:"female"});

    // このように配列を引数に取ることで、複数同時に追加することもできる
    users.insertMany([
        {name: 'test1', id:'id7', age:7, sex:"female"},
        {name: 'test2', id:'id8', age:8, sex:"male"}
    ]);

    // ====================
    // 検索
    // ====================
    // 男性を出力するプログラム
	users.find({sex:"male"}).each(function(err, doc) {
	  if(doc !== null){ console.log(doc.name); }
    });
		
    // 全データ出力
    // users.find().each(function(err, doc) {
    //     console.log(doc);
    // });

    // 出力を制御する（↓のプログラムの場合は_idを出力せず、nameを出力する）
    users.find({}, { projection: {_id: 0, name: 1 } }).toArray(function(err, result) {
    if (err) throw err;
        console.log(result);
    });
    

    // ====================
    // データ削除 (Node.jsが非同期処理なので、async-awaitで書かないと、その下のcount()までに削除処理が間に合わない可能性がある。)
    // ====================
    users.deleteOne({"age":0});//ageが0のデータを一つ削除
    users.deleteMany({"age":{"$lte": 4}}) //ageが4以下のデータを削除
     users.deleteMany({}) // 全データ削除


    // ====================
    // データ個数を調べる 
    // ====================
    users.count({}).then((result) => { // find(条件).count()という書き方もある
      console.log("最終的なデータの数 = " + String(result));
    })


    client.close();
    console.log ("*** 終了 ***")
});

### 実行

上のプログラムを実行するには以下のコマンドを実行して下さい。

$ npm instal mongodb
$ npm index.js # ↑のJavaScriptファイル名

以下のように出力されるはずです。

*** 開始 ***

*** 終了 ***
name1
name2
name3
test2
[ { name: 'name1' },
  { name: 'name2' },
  { name: 'name3' },
  { name: 'name5' },
  { name: 'name4' },
  { name: 'test1' },
  { name: 'test2' },
  { name: 'name6' } ]
最終的なデータの数 = 8

### SocketExeptionで繋がらなくなった

Ubuntuのシステムをアップデートしたら、mongoコマンドを実行しても以下のようなエラーが出てデータベースにつながらなくなりました。

connecting to: mongodb://127.0.0.1:27017/?compressors=disabled&gssapiServiceName=mongodb
2020-10-23T16:57:38.500+0000 E  QUERY    [js] Error: couldn't connect to server 127.0.0.1:27017, connection attempt failed: SocketException: Error connecting to 127.0.0.1:27017 :: caused by :: Connection refused :
connect@src/mongo/shell/mongo.js:353:17
@(connect):2:6
2020-10-23T16:57:38.501+0000 F  -        [main] exception: connect failed
2020-10-23T16:57:38.501+0000 E  -        [main] exiting with code 1

  
調べていくと↓の記事に出会い、無事解決しました。ありがたい〜〜  
[qiita.com](https://qiita.com/sirius1000/items/e4dc31bb3ce186cfe9d8)  

### 解説

気が向いたら書く......

//TODO  