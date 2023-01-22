---
title: "【Python】フライトレーダーのデータをPythonで読み取りExcelに出力"
emoji: ""
type: ""
topics: []
published: false
---

突然ですが、このサイトをご存知でしょうか？

<https://www.flightradar24.com/35.25,134.92/7>

このサイト、「**フライトレーダー24**」は、**リアルタイムで全世界の飛行中の飛行機の位置情報や目的地到着予定時刻、出発時刻etc.**の情報を見ることができるサイトです。

![f:id:pythonjacascript:20181226204844j:plain](/images/ppythonjacascript2018122620181226204844.jpg "f:id:pythonjacascript:20181226204844j:plain")

  
今回は、このサイトから**航空機の情報を入手**して、**Excelファイルに表の形でまとめて**みました。

* [前準備](#前準備)
* [プログラム](#プログラム)
* [実行](#実行)

### 前準備

今回、フライトレーダーからデータを読み取るために、以下のコードを使用させていただきました。  
[github.com](https://github.com/mkorkmaz/flightradar24)

上のサイトから、「[Download ZIP](https://github.com/mkorkmaz/flightradar24/archive/master.zip)」を行って、上のレポジトリを.zipファイルでダウンロードします。

解凍すると、下のようなファイル構造になるので、下の画像と同じようなディレクトリに「**test.py**」を新規作成します。  
![f:id:pythonjacascript:20181226205759j:plain](/images/ppythonjacascript2018122620181226205759.jpg "f:id:pythonjacascript:20181226205759j:plain")  
この画像は、解凍したファイルである「**flightradar24-master**」の中身です。

**使用したライブラリのreadmeには、pip3を使ってインストールするようになっていますが、今回はPython2を使用してコードを走らせたかったので、このような方法をとりました。**

  
### プログラム

上の工程で作成した「test.py」の中身を次のように書いてください。

#! /usr/bin/env python

import matplotlib.pyplot as plt
import flightradar24
import openpyxl as px

def MakeExcel(df, file_name = "sample.xlsx"):
    wb = px.Workbook()
    ws = wb.active
    
    ws.title = "Sheet1" #Sheetの名前を設定
    
    for i in range(len(df)):
        line_data = df[i]
        j = 1
        try:
            for key in line_data.keys():
                ws.cell(row = i + 1, column = j, value = line_data[key])
                j += 1
        except AttributeError:
            for j in range(len(line_data)):
                ws.cell(row = i + 1, column = j+1, value = line_data[j])
    
    wb.save(file_name)   # 保存

def GetAirports():
    """
    日本国内の空港名と、その緯度＆経度のデータを取得
    """
    fr = flightradar24.Api()
    airports = fr.get_airports() #全ての空港の情報を取得
    
    airports_japan = []
    
    for airport in airports['rows']:
        if airport["country"] == "Japan":
            airports_japan.append(airport) #国内の空港の情報を取得
    
    MakeExcel(airports_japan, "Airports.xlsx") #Excelデータ作成


def GetAirlines():
    """
    空港会社の一覧を取得
    """
    fr = flightradar24.Api()   
    airlines = fr.get_airlines()["rows"]
    MakeExcel(airlines, "Airlines.xlsx")#Excelデータ作成


def GetFlights():
    """
    JALが現在飛ばしている飛行機のデータをすべて取得
    """
    fr = flightradar24.Api()  
    airline = 'JAL' # Japan Airline
    flights = fr.get_flights(airline)
    flights_list = []
    for flight in flights.values():
        try:
            if(len(flight)==19):
                flights_list.append(flight)
        except TypeError:
            pass
    MakeExcel(flights_list, "Flight.xlsx")


if __name__ is "__main__":
    GetAirports()
    GetAirlines()
    GetFlights()

本当は内包表記を使ったほうがいいのでしょうが、for文を多用しています...。  
  
  
### 実行

「test.py」を実行します。  
すると、「**Airlines.xlsx**」「**Flights.xlsx**」「**Airports.xlsx**」の3つのExcelファイルが生成されます。

これらのファイルを開くと、フライトレーダーから取得したデータが保存されていることがわかると思います。  
![f:id:pythonjacascript:20181226211744j:plain](/images/ppythonjacascript2018122620181226211744.jpg "f:id:pythonjacascript:20181226211744j:plain")  
（Flights.xlsxの例）