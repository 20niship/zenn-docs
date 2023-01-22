---
title: "【Python】無料でジオコーディング(住所→緯度経度etc.)"
emoji: ""
type: ""
topics: []
published: false
---

住所⇔緯度＆経度の変換をしたい、目的地までのルート検索や所要時間を調べたい、と思い良いものがないかなぁと思っていました。  
すると、↓の記事で**Google Maps API**というものがあることを知り、  
[qiita.com](https://qiita.com/Spooky%5FMaskman/items/9f4c487ed884d803641b)

使ってみよ～～、と思ったのですが、なんと**有料でした！**

  
一学生として有料は厳しい、、、、。で調べていくと、[OSM(Open Street Map)](https://www.openstreetmap.org/)が提供しているデータを使えばジオコーディングが無料でできるらしい。おお～～(≧▽≦)

  
**geopy**というものを使うと、住所→緯度＆軽度の取得ができます

* Python 3.7.3 on win32
* geographiclib-1.50 geopy-2.0.0

$ pip install geopy

でgeopyを入れます。  
  
### プログラム

# -*- coding: utf-8 -*-
from geopy.geocoders import Nominatim

def main():
  geolocator = Nominatim(user_agent="test-dayo")
  location = geolocator.geocode("日本 北九州")
  print("Lat, long = ",location.latitude, location.longitude)
  print("full address = ", location.address)

  # 辞書として読み取る
  loc_dict = dict(location.raw)
  print("Lat, long = ", loc_dict["lat"], loc_dict["lon"])
  print("full address = ", loc_dict["display_name"])
  print("class and type = ", loc_dict["class"], loc_dict["type"])

  url = f"https://www.google.com/maps/search/?api=1&query={location.latitude, location.longitude}"
  
  import webbrowser
  webbrowser.open(url)

main()

上のプログラムを実行すると、

Lat, long =  35.675886399999996 139.74505141191034
full address =  国会議事堂, 1, 国道246号, 永田町1, 永田町, 千代田区, 100-0014, 日本 (Japan)
Lat, long =  35.675886399999996 139.74505141191034
full address =  国会議事堂, 1, 国道246号, 永田町1, 永田町, 千代田区, 100-0014, 日本 (Japan)
class and type =  office government

  
と表示され、ブラウザで国会議事堂のGoogleMapが開くと思います。  
![f:id:pythonjacascript:20201024111219j:plain](/images/ppythonjacascript2020102420201024111219.jpg "f:id:pythonjacascript:20201024111219j:plain")

  
### 参考文献

* [GitHub - geopy/geopy: Geocoding library for Python.](https://github.com/geopy/geopy)
* [【Python】Geopyで経度と緯度を取得する方法 | yamagablog](https://ymgsapo.com/2019/07/05/how-to-use-geopy)
  
  
### ほかのライブラリ

他にも数種類GeoCodingをするPythonライブラリがあるみたいです。

#### GeoCoder

$ pip install geocoder

でgeocoderを入れます。

import geocoder

# 下のコードはGoogleAPIの登録が必須になってから動かなくなっている
# g = geocoder.google('Mountain View, CA')
# print(g.latlng)

location = '日本 国会議事堂'
ret = geocoder.osm(location, timeout=5.0)
print(ret.latlng)

# 出力結果：[35.675886399999996, 139.74505141191034]

[medium.com](https://medium.com/shimakaze-soft-techblog/python%E3%81%A7%E5%9C%B0%E5%90%8D%E3%81%8B%E3%82%89%E4%BD%8F%E6%89%80%E3%81%A8%E5%BA%A7%E6%A8%99%E3%82%92%E5%89%B2%E3%82%8A%E5%87%BA%E3%81%99%E3%82%B8%E3%82%AA%E3%82%B3%E3%83%BC%E3%83%87%E3%82%A3%E3%83%B3%E3%82%B0%E3%82%92%E8%A9%A6%E3%81%97%E3%81%A6%E3%81%BF%E3%82%8B-61fcaeb5a093)  