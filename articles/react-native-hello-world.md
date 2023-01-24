---
title: "【Ubuntu】React Nativeの環境構築 →WebとAndroidでHello World!"
emoji: "🤖"
type: "tech"
topics: []
published: false
---

* [Cross Platformなフレームワーク](#Cross-Platformなフレームワーク)  
   * [ionic](#ionic)  
   * [React Native](#React-Native)  
   * [Xamarin](#Xamarin)  
   * [Flutter](#Flutter)  
   * [Unity](#Unity)  
   * [Google Trendによるトレンド](#Google-Trendによるトレンド)
* [React Nativeの環境構築](#React-Nativeの環境構築)  
   * [バージョン確認](#バージョン確認)
* [プロジェクト作成](#プロジェクト作成)
* [デバッグ](#デバッグ)  
   * [Android実機デバッグ](#Android実機デバッグ)  
   * [Webブラウザでデバッグ](#Webブラウザでデバッグ)  
   * [VSCodeでデバッグ](#VSCodeでデバッグ)
* [参考](#参考)

### Cross Platformなフレームワーク

React, Flutter, Electronなどなど、現在は色々なフレームワークがあります。で、まとめるとこうなります。

| フレームワーク      | 使用言語                             | パフォーマンス | コミュニティ(情報量) | 対応OS                                                     |
| ------------ | -------------------------------- | ------- | ----------- | -------------------------------------------------------- |
| ionic        | CSS, HTML, TypeScript + JS       | △       | ○           | Android iOS Windows                                      |
| React Native | CJS + (Java, Swift, Objective-C) | ○       | ◎           | Android, iOS, (Winsows)                                  |
| Xamarin      | C# (.NET環境)                      | ○       | ○           | Android, iOS, Windows                                    |
| Flutter      | Dart                             | ◎       | △           | Android, iOS                                             |
| Electron     | JS, HTML, CSS                    | ?       | ?           | Mac, Windows, Linux                                      |
| Unity        | C#                               | ?       | ◎           | iOS, Android, Web, Windows, Mac, Linux, PlayStation等ほか多数 |
| Monaca       | HTML, JS                         | ?       | △           | iOS, Android, Windows, Mac, Web                          |

#### ionic

* ☓ ゲーム用には適していない
* ☓ セキュリティで困難があるかも

#### React Native

* UIのアーキテクチャに特化しているためレスポンスが良い
* Node.jsを使う
* 独自UIを使用
* Facebookが開発。
* FacebookやInstagramといった大型なアプリでも使われている

#### Xamarin

* Microsoftが提供
* ザマリンと読む
* .NETに慣れている人は良いかも。

#### Flutter

* 比較的新しいフレームワーク。
* OSのネイティブUIを使用
* Googleが提供している開発環境
* ホットリロード機能でリアルタイムに変更が確認できる。

#### Unity

* ここで紹介しているなかでは唯一オープンソースではない
* 3Dグラフィックス/ゲームアプリの開発

[Which is the Best Cross Platform Mobile App Development Framework? - Radixweb](https://radixweb.com/blog/cross-platform-app-development-frameworks)  

#### Google Trendによるトレンド

  
2021年4月現在、単純な検索数だけだと、**Unity > Electron = Flutter > React Native > Xamarin >> Monacal** の順番ですね。

まぁでも用途によって色々違うだろうし、検索トレンドだけで判断するのは、本当に、なんだかなぁという感じです、

  
まぁでも今回は**React Native**を使います。

### React Nativeの環境構築

<https://qiita.com/sengoku/items/5555fcc10980b7780714>

テスト環境は以下の通り：  
![f:id:pythonjacascript:20210409235433p:plain](/images/ppythonjacascript2021040920210409235433.png)

  
以下のコマンドを実行して色々インストールします。

Node.jsのインストール

$ sudo apt update
$ sudo apt install nodejs
$ sudo apt install npm

JDKのインストール

$ sudo apt-get install openjdk-8-jdk
$ sudo update-java-alternatives --set java-1.8.0-openjdk-amd64

Expoのインストール  
Expoとは、  
\> Expo is an open-source platform for making universal native apps for Android, iOS, and the web with JavaScript and React.  
だそうです。（ <https://expo.io/>より）

以下のコマンドでインストールします。

$ sudo npm install --global expo-cli

#### バージョン確認

上のコマンドでインストールした諸々のバージョンは以下の通り：

$ npm --version 
6.14.4

$ node --version             
v10.19.0

$ java -version
openjdk version "1.8.0_282"
OpenJDK Runtime Environment (build 1.8.0_282-8u282-b08-0ubuntu1~20.04-b08)
OpenJDK 64-Bit Server VM (build 25.282-b08, mixed mode)

$ javac -version
javac 1.8.0_282

### プロジェクト作成

適当な空ディレクトリに行って、testプロジェクトを作成します。

$ create-react-native-app test
✔ How would you like to start › Default new app
✔ Downloaded and extracted project files.

Using npm to install packages.

testプロジェクトを作成しました。  
最初プロジェクトの種類を聞かれるので、default new appを選択します。

  
test/App.jsを好きなように編集してみて下さい。  
例：

import * as React from "react";
import { View, Text } from "react-native";

export default function App() {
  return (
    <View style={{flex: 1,justifyContent: "center",alignItems: "center",}} >
      <Text>Universal React with Expo, Hello World!!!</Text>
    </View>
  );
}

### デバッグ

#### Android実機デバッグ

Androidの実機でデバッグするには、[Expo Go](https://play.google.com/store/apps/details?id=host.exp.exponent&hl=ja&gl=US)というアプリをAndroidスマホに入れておく必要があります。また、AndroidスマホとPCは同じネットワークに接続している必要があります。

プロジェクトフォルダに行って、\`npm start\`コマンドを打ってAppを実行します。

$ cd test
$ npm start

以下のようなQRコードが生成されれば成功です。

![f:id:pythonjacascript:20210421003544j:plain](/images/ppythonjacascript2021042120210421003544.jpg)

  
このQRコードをExpo Goで読み取ります。すると、自動的にアプリがコンパイルされ、実行されます。  
![f:id:pythonjacascript:20210421003727j:plain](/images/ppythonjacascript2021042120210421003727.jpg)

  
やったね！！  
  
#### Webブラウザでデバッグ

1. ↑のように「npm start」を実行してQRコードが表示されたら、ターミナル上で「w」を押して下さい。
2. 自動でコンパイルが開始され、ブラウザが開きます。
3. そして、左側のメニューから「Run in web browser」をクリックすると、ブラウザ上でデバッグすることができます。

#### VSCodeでデバッグ

Android SDKやEmulatorを入れると、VSCode上でデバッグできるようです。私はAndroid SDKの容量が大きくストレージを圧迫してしまうので使っていませんが。

React Native Tools↓を使うらしいです：  
![f:id:pythonjacascript:20210421003301p:plain](/images/ppythonjacascript2021042120210421003301.png)  

  
参考：  
[qiita.com](https://qiita.com/t%5Fokkan/items/2b6b94340b837189054c)
  
  
### 参考

[\[Android\] Ubuntuで始めるReact Native - Qiita](https://qiita.com/sengoku/items/5555fcc10980b7780714)

[VisualStudioCodeでReactNativeのデバッグ環境を構築する - Qiita](https://qiita.com/t%5Fokkan/items/2b6b94340b837189054c)