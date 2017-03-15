---
layout: post
title: '[筆記] Shapefile to GeoJSON'
date: 2015-05-05 06:45
comments: true
categories: [GeoJSON]
---
因為每次轉檔都要查，索性把步驟記錄下來。

首先要安裝 GDAL (Geospatial Data Abstraction Library) 這個程式，
因為我是用 Mac ，所以在 terminal 輸入 `brew install gdal` 就可以了。

其他作業系統的安裝方式可詳閱 http://www.gdal.org/ 。

安裝好 gdal 之後，就可以透過 ogr2ogr 來執行轉檔，一樣在 terminal 輸入：

`ogr2ogr -f "GeoJSON" output.json source.shp`

以臺北市政府開放資料的**臺北市區界圖** ( http://data.taipei/opendata/datalist/datasetMeta?oid=1601ef3a-c253-4988-b047-943d9e786143 ) 提供的 Shapefile 來說，因為它的坐標系統是 EPSG:3826(TWD97/121分帶)，我們需要把它轉換成 WGS84經緯度(EPSG:4326)，所以透過 `-s_srs` 與 `-t_srs` 來分別指定轉換前與轉換後的座標系統：

`ogr2ogr -f "GeoJSON" -s_srs EPSG:3826 -t_srs EPSG:4326  output.json source.shp`

轉換後的 geojson 直接輸出到 google map 上：
![螢幕快照 2015-05-05 下午3.47.07.png](http://user-image.logdown.io/user/3292/blog/3340/post/263637/UrqBEFSmTuazvdty255S_%E8%9E%A2%E5%B9%95%E5%BF%AB%E7%85%A7%202015-05-05%20%E4%B8%8B%E5%8D%883.47.07.png)

就這樣。

[補充]
關於 GeoJson 送到 Google Map 的部分可以參考我的其他文章：
[透過 Google Maps API 處理 GeoJSON 資料](http://kuro.tw/posts/2015/04/28/through-the-google-maps-api-geojson-data "透過 Google Maps API 處理 GeoJSON 資料")
[利用 Google Map 檢視台北市降雨淹水模擬圖](http://kuro.tw/posts/2015/09/30/use-google-map-view-rainfall-simulation-of-flooding-in-taipei-city-map "利用 Google Map 檢視台北市降雨淹水模擬圖")

又，如果需要轉換成 TopoJSON 的話，也可以參考這篇： [視覺化實戰 － D3.js 地理區塊視覺化](http://blog.infographics.tw/2015/04/visualize-geographics-with-d3js/ "視覺化實戰 － D3.js 地理區塊視覺化")