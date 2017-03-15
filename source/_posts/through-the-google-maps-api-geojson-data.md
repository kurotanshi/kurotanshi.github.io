---
layout: post
title: '透過 Google Maps API 處理 GeoJSON 資料'
date: 2015-04-28 12:10
comments: true
categories: [Google Map]
tags: [google map, GeoJSON]
---
在說明 Google Map 如何存取 GeoJSON 前，先來簡單介紹 GeoJSON。

[GeoJSON](http://geojson.org/ "GeoJSON") 是一種專門處理地理資訊 (GIS) 結構的 JSON 標準格式。 一個 GeoJSON 物件可以用來代表**點** (Point)，**線** (LineString)，**多邊形** (Polygon) 等等的幾何結構，以及**特徵** (Feature) 的集合，或是**一系列的特徵** (FeatureCollection)。

也因為 GeoJSON 是一種基於 JSON 的公開標準，其結構簡單且容易讀取的特性也廣受開發者的歡迎，有不少開發程式庫開始支援 GeoJSON 的處理，也有許多政府開放資料(官方或非官方)開始提供 GeoJSON 作為其資料格式。

一個簡單的 GeoJSON 會長得像這個樣子：
``` javascript
{
    "type": "FeatureCollection",
    "features": [{
        "type": "Feature",
        "geometry": {
            "type": "Point",
            "coordinates": [102.0, 0.5]
        },
        "properties": {
            "prop0": "value0"
        }
    }, {
        "type": "Feature",
        "geometry": {
            "type": "LineString",
            "coordinates": [
                [102.0, 0.0],
                [103.0, 1.0],
                [104.0, 0.0],
                [105.0, 1.0]
            ]
        },
        "properties": {
            "prop0": "value0",
            "prop1": 0.0
        }
    }, {
        "type": "Feature",
        "geometry": {
            "type": "Polygon",
            "coordinates": [
                [
                    [100.0, 0.0],
                    [101.0, 0.0],
                    [101.0, 1.0],
                    [100.0, 1.0],
                    [100.0, 0.0]
                ]
            ]
        },
        "properties": {
            "prop0": "value0",
            "prop1": {
                "this": "that"
            }
        }
    }]
}
```

如上所示，每一筆資料都會是一個「**Feature**」物件，地理位置相關資訊會存放在 geometry 物件內，其中分別有「**type**」以及「**coordinates**」屬性，type 用來表示資料類別，可以是點，線，甚至是多邊形等；而 coordinates 用來存放經緯度座標。而其他的相關資訊則會放在「**properties**」內，以 **key: value** 方式呈現。

簡單介紹就到此，有興趣的朋友可以參考 http://geojson.org/
註：這裏有中國網友翻譯的 GeoJSON 規格 http://www.oschina.net/translate/geojson-spec

回到主題。
Google Map 匯入 GeoJSON 的方式非常簡單，如果已經有一個完整的 GeoJSON 檔案的話，那麼透過

`map.data.loadGeoJson(FILE-URL);`

利用這一行程式碼就可以載入 GeoJSON 至 Google Map 的 Data Layer 了。

簡單範例：
``` javascript
var map;
function initMap() {
    // 地圖初始設定
    var mapOptions = {
        center: new google.maps.LatLng(25.04674, 121.54168),
        zoom: 13,
        mapTypeId: google.maps.MapTypeId.ROADMAP
    };

    // GeoJSON file
    var url = 'https://dl.dropboxusercontent.com/u/12537630/geo.json';

    var mapElement = document.getElementById("mapDiv");

    // Google 地圖初始化
    map = new google.maps.Map(mapElement, mapOptions);

    // 載入 GeoJSON 資料
    map.data.loadGeoJson(url);
}
```
DEMO: http://jsbin.com/totuzisobo/1/
![螢幕快照 2015-04-28 下午7.15.40.png](http://user-image.logdown.io/user/3292/blog/3340/post/262352/sSKDGsS7RUqG2TluFYJC_%E8%9E%A2%E5%B9%95%E5%BF%AB%E7%85%A7%202015-04-28%20%E4%B8%8B%E5%8D%887.15.40.png)


如果希望載入的資料可以有自訂的樣式的話，可以透過 `map.data.setStyle` 在載入前指定好希望的 style，這裏以 marker 的圖示為例：

圖示資訊我放在 GeoJSON 的 `properties.icon` 內，如下 (其中一筆 point 的內容)：

``` javascript
  "type": "Feature",
  "geometry": {
	  "type": "Point",
	  "coordinates": [ 121.51771545410156, 25.028294990979614 ]
  },
  "properties": {
  	"icon": "http://google-maps-icons.googlecode.com/files/vegetarian.png"
  }
```

於是我們可以透過 `feature.getProperty('icon')` 去指定每一筆 point 的圖示：

``` javascript
  // 自訂樣式
  map.data.setStyle(function(feature) {
    return { 'icon': feature.getProperty('icon') };
  });

  // 載入 GeoJSON 資料
  map.data.loadGeoJson(url);
```
DEMO: http://jsbin.com/xikaqe/1/
![螢幕快照 2015-04-28 下午7.15.46.png](http://user-image.logdown.io/user/3292/blog/3340/post/262352/u5lfzGXOQVaVY7zkw57q_%E8%9E%A2%E5%B9%95%E5%BF%AB%E7%85%A7%202015-04-28%20%E4%B8%8B%E5%8D%887.15.46.png)

如果希望透過 ajax 方式載入 GeoJSON 的話也非常簡單，在取得 ajax 回傳資料時，用 `map.data.addGeoJson(res);` 這行就可以載入資料了。

以 jQuery 為例，如:
``` javascript
$.get(FILE-URL, function(res) { map.data.addGeoJson(res); });
```

有個整理好的 GeoJSON，只需要一行就可以很輕鬆地在 Google Map 產生想要的圖標了。
如果想在圖表上處理點擊等等的事件，還是得另外處理喔。

參考資料:
https://developers.google.com/maps/documentation/javascript/examples/layer-data-simple
https://developers.google.com/maps/documentation/javascript/examples/layer-data-quakes
