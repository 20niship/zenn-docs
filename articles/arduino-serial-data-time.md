---
title: "【Arduino】シリアル通信にかかる時間"
emoji: ""
type: ""
topics: []
published: false
---

シリアル通信にかかる時間を計ってみました。

結果です。単位はすべてus(マイクロセカンド）です。

※この実験で使用したArduinoボードは「**Arduino UNO**」です。

### BPS = 115200

| 関数                    | Serial | SofwareSerial |
| --------------------- | ------ | ------------- |
| read()                |        |               |
| write(byte)           |        |               |
| print("Hello, World") |        |               |

### BPS = 9600

| 関数                    | Serial | SofwareSerial |
| --------------------- | ------ | ------------- |
| read()                |        |               |
| write(byte)           |        |               |
| print("Hello, World") |        |               |