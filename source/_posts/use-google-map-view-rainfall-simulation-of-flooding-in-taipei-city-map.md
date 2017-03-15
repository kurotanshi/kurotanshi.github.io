---
layout: post
title: '利用 Google Map 檢視台北市降雨淹水模擬圖'
date: 2015-09-30 04:00
comments: true
categories: [OpenData]
tags: [google map, opendata]
---
看到前兩天的新聞: [柯文哲想公開易淹水地區 北市府已上網](https://tw.news.yahoo.com/%E6%9F%AF%E6%96%87%E5%93%B2%E6%83%B3%E5%85%AC%E9%96%8B%E6%98%93%E6%B7%B9%E6%B0%B4%E5%9C%B0%E5%8D%80-%E5%8C%97%E5%B8%82%E5%BA%9C%E5%B7%B2%E4%B8%8A%E7%B6%B2-115016390.html "柯文哲想公開易淹水地區 北市府已上網")，又剛好有前輩寫了一篇 [利用QGIS檢視台北市降雨淹水模擬圖](http://gis.rchss.sinica.edu.tw/qgis/?p=3221 "利用QGIS檢視台北市降雨淹水模擬圖")，<del>雖然身處房仲業，但身為有良心的開發者不能只想著房價</del>，心想應也可透過 Google Map 來呈現，於是試了一下，順便寫篇記錄。

首先從[臺北市政府資料開放平台](http://data.taipei/opendata/datalist/datasetMeta?oid=fa1e8012-ebb4-473b-888e-97f9a9ce365e "臺北市政府資料開放平台")將所需的資料一一下載下來，格式是 kmz (其實就是 zip 壓縮)。在解壓縮後可以得到 doc.kml 檔案。 接著，雖然 Google maps API 有提供 [KML Layers](https://developers.google.com/maps/documentation/javascript/examples/layer-kml "Google maps API KML Layers") 的圖層嵌套，但轉出來的 kml 檔似乎要稍作修改後才能透過 KML Layers 套用在 Google map 上，這裏我選擇另一種做法: 將 kml 轉為 geoJSON 後使用。

前面說到要將 kml 檔案轉為 geoJSON，那麼該如何轉換格式呢？
幸好 mapbox 提供了 [togeojson](https://github.com/mapbox/togeojson) 這套工具，透過它提供的 `toGeoJSON.kml(doc)` 就可以輕鬆地將它轉為 geoJSON 的格式了。

有關 GeoJSON 如何輸出至 Google Map 可以參考小弟之前的 post：[透過 Google Maps API 處理 GeoJSON 資料](http://kuro.tw/posts/2015/04/28/through-the-google-maps-api-geojson-data)，這裏就不再贅述。

值得一提的是，透過 Google Maps Javascript API 輸出的 GeoJSON 預設的樣式都是黑色粗線，想要修改樣式的話可以透過 `setStyle` 來做處理，像這樣可以針對 name 是 0.3 的時候，我們輸出樣式為藍色的線，如果是 0.3~1.0 的話，則是輸出為綠色的線段，當然也可以針對填色與透明度等等做設定。

``` jsvascript
dataMap.setStyle(function(feature){
  if( feature.getProperty('name') === '0.3'){
    return { fillOpacity: 0.35, fillColor: '#0070FF', strokeWeight: 2, strokeColor: '#0070FF', strokeOpacity: 1 };
  }
  if( feature.getProperty('name') === '0.3~1.0'){
    return { fillOpacity: 0.35, fillColor: '#54FF00', strokeWeight: 2, strokeColor: '#54FF00', strokeOpacity: 1 };
  }
});
```

結果呈現像這樣：
![image.png](http://user-image.logdown.io/user/3292/blog/3340/post/302535/lNUGoyd7RB2MOsx8i1cw_image.png)

這裏也有 Online Demo: [http://kurotanshi.github.io/TPEDisasterSummary/rain/rain_tp_map.html](http://kurotanshi.github.io/TPEDisasterSummary/rain/rain_tp_map.html)