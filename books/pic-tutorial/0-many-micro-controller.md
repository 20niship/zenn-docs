---
title: "【マイコン】どのマイコンを選んだらいいのか？"
emoji: "🤖"
type: "tech"
topics: undefined
published: false
---

マイコンは**プログラムで好きなように動きをコントロール**できるICです。

...と一言で言っても、マイコンには多くの種類があります。  
今回は、どのマイコンがいいのか？を考えてみました。

今回紹介するマイコンは、  
**Arduino、Raspberry pi、PIC、STM32**の4つ！  
  
* [それぞれの主な使用](#それぞれの主な使用)
* [Arduino](#Arduino)
* [Raspberry pi](#Raspberry-pi)
* [PIC](#PIC)
* [STM32](#STM32)

### それぞれの主な使用

| マイコンの種類                             | Arduino                                       | Raspberry pi                | PIC               | STM32                       |
| ----------------------------------- | --------------------------------------------- | --------------------------- | ----------------- | --------------------------- |
| プログラミング                             | Arduino言語（C言語）                                | Python                      | C、アセンブラ           | C言語                         |
| プログラミング難易度（初期設定等）                   | とても楽                                          | まあまあ                        | ちょっと面倒            | ちょっと面倒                      |
| 開発環境                                | Arduino IDE                                   | Raspberry pi本体              | MPLABなど           | Sytem Workbench for STM32など |
| 開発環境（ハードウェア）                        | USBケーブルのみ                                     | デスクトップのパソコンとして使うので、それ用の機材のみ | PICライターが必要（￥5000） | USBケーブル                     |
| ボードの値段                              | ￥3000\~7000                                   | ￥500～5000                   | ￥100～600          | ￥1000～3000                  |
| 周波数                                 | 16MHz、84MHz[\*1](#f-ff9404f8 "Arduino DUEのみ") | 1.4GHｚなど                    | ～40MHｚ            | ～180MHｚ                     |
| 動作電圧[\*2](#f-921f104d "機種によって違います") | 5V、3.3V[\*3](#f-045f9cfd "Arduino DUEのみ")     | 5V                          | 3.3V              | 5Vなど                        |

詳しく見ていきましょう...。  
  
  
### Arduino

### Raspberry pi

### PIC

### STM32

得意分野はロボット等の機械制御、パソコンとの通信、簡単な回路テスト。

USBに繋げば電源が入りパソコンにArduinoIDEというプログラム開発環境をインストールすることでいきなりプログラミングを開始することが出来ます。

また、書き込んだマイコンを取り外し、自前で回路を組んでAVRマイコンとして利用することも出来ます。（Arduinoに載っているAVRマイコンには予めArduinoとして動くようブートローダーというプログラムが書き込んであります）

動作速度は基本的に16MHz固定。
  
  
＜画像クリックで詳細ページ＞

初期設定が非常に簡単、USBのみで機能して省エネ。  
プログラムもサンプルソースが多数あるので複雑なことをしなければすごく簡単。  
サイズ違いなど種類も豊富でハードに一貫性があるのでプログラムの使い回しも容易。  
アナログ入力が可能。  
電源も入出力も５Vで扱いやすい。  
入出力端子に番号や記号が書いてある親切な設計。  
「シールド」と呼ばれている基盤が豊富でを追加することで回路を考えなくてもイロイロなセンサーを組み立てることが可能。  
プログラム言語はArduinoオリジナルの「Arduino言語」と呼ばれている「C++言語」を扱い易くした言語が用いられている。  

【RaspberryPi】  
得意分野はサーバー構築、単体コンピュータ、家電IOT機器化。

SDカードにOS（基本的にRaspbianというDebian（Linuxの種類の１つ）をベースにRaspberryPi用にカスタムしてあるOS）をインストールする必用がありますが映像出力、サウンド出力、USB、LANといった装備を備えており電源さえ入れればコンピュータとして機能するようになっています。（単体で動かすならHDMIモニター、USBキーボドが必用）

USB機器が使えるのでカメラ等のパソコン用機材の接続も容易（ドライバは必用）

マイコンを取り外しての単体利用は出来ません。

プログラム言語はLinuxパソコンで扱えるものほぼ全般。「アセンブラ」「C言語」「Java」「Ruby」「Python」の他、ゲーム感覚でプログラムが組める「Scratch」もOS（Raspbian）に標準インストールされているのも特徴。

動作速度はマイコンとしては高速の約１GHzで駆動（比較としてArduinoは16MHz駆動なので62.5倍！）するがOS上でプログラムを走らせているのでナノ秒単位の「厳密」な動作は不得意（赤外線リモコン通信なんかではタイミング合わせに苦労する）。そういう場合はUSBにArduinoを繋いじゃう！なんて回りくどい手も使えます。
  
  
＜画像クリックで詳細ページ＞

OSをインストールして周辺機器さえ繋げばLinuxコンピュータとして機能する。（OS無しのベアメタル駆動させるマニアもいる）  
プログラム用のパソコン要らずでRaspberryPi単体でプログラム環境を構築することが出来る。  
消費電力は他のマイコンに比べると大きいもののLINUXマシンとしては省エネ。  
アナログ入力不可。  
外部機器は3.3V入出力のものを選ぶ必用がある。（電源は５Vなので要注意）  
RaspberryPi用センサーモジュールが豊富。  

【PIC】【ArduinoのAVRマイコン単体】  
得意分野は回路の最小構成や基盤の自由な部品配置設計。

マイコン単体ですのでそのままでは何も出来ません。電源も自分で準備しないといけません。電源を入れるだけでも電源ノイズ対策のコンデンサを自分でマイコン周囲に設置する必用があります。

基本定には電源以外に、発振器（クリスタルやセラロックというMHz単位のクロックを刻む部品）、リセットスイッチが必用ですが、それさえプログラム内で端折ってしまえる種類もあります（マイコンの内部に発振回路が含まれている）。

PICやAVRはマイコン単体のことですので一番何にでもなり得る、最も説明が難しいものです。

（AVRはArduino用のブートローダーを書き込むことでArduinoとして機能しますが、PICもPICSYSフレームワークというPICをArduino化するというものが存在します。）

１つの価格が安い（数百円程度）ので回路自体を安く仕上げられ、設計次第で超低消費電力機器にもなります。

また単品でプログラムは不可能ですから必ず「ライター」と呼ばれる書き込み機が必用で、ライターも書き込むマイコンの種類に応じた物を使う必用があります。

プログラム言語は「アセンブラ」「C言語」が主流です。

動作速度は任意に設定（内部発振の4MHz〜外部発振40MHzまで幅広く設定）出来るが、基本的に早くなるほど電力消費は多くなります。

何度もプログラムを書き直せるEEPROM式のものが現在は主流ですが、その昔は紫外線を当ててプログラムをリセットするものが主流でした。

大量生産品で書き直す予定が無い物は格安のワンタイム品という一度きりの書き込みタイプがあり製造コストを削減することが出来ます。

一般製品に型番を表示せずに搭載していることもあるようですのである意味プロ仕様ともいえます。
  
  
＜画像クリックで詳細ページ＞

単体では電源を繋いでも何も出来ないただのICチップ。  
必用な入出力数に応じてマイコンを選別する。  
プログラムを書き込むためには書き込み機が必用、消費電力は回路設計、プログラミング次第。  
プログラムはマイコンの種類別に設定項目や機能が違うのでプログラムの使い回しは互換性のある種類以外は困難。  
アナログ入力対応機もある。  
センサー類は自前で回路を設計するので機器の最小構成が出来る。  

現時点では上記３つのうち、一番始めに「電子工作」として初心者が手軽に使えるのは間違いなくArduinoと言えるでしょう。何せシールド（拡張ボード）の豊富さは他のマイコンと桁が違います。
  
  
そして、Arduinoは用途に応じて種類も豊富な上、さらに亜種ともいえるマイコンが多種存在し、亜種もArduinoIDEでArduino言語を用いてプログラミング出来るのもすごいところです。

例えば、ESP32開発ボードはUSB接続端子搭載で小型の低価格ボードなうえ、WiFi／Bluetooth搭載で最大160MHz駆動というバケモノマイコンです。↓

[\*1](#fn-ff9404f8):Arduino DUEのみ

[\*2](#fn-921f104d):機種によって違います

[\*3](#fn-045f9cfd):Arduino DUEのみ