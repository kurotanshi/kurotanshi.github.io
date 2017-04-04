---
layout: post
title: '淺談 Google map Heat map API '
date: 2015-04-29 08:01
comments: true
categories: [Google Map]
tags: [google map, Heatmap]
---
前陣子因為工作的需要，稍微研究了一下 Google Map 提供的 HeatMap (熱點圖/熱圖) API，實作方面也滿容易的，在這裡就簡單做個紀錄。

**HeatMap** (以下稱熱圖) 是用來表示**資料數值強度與位置的可視化**，Google 熱圖 API 在預設情況下，高強度的數值會以紅色表示，低強度的數值則是以綠色來表示。

![螢幕快照 2015-04-29 下午3.37.46.png](http://user-image.logdown.io/user/3292/blog/3340/post/262458/gg6DtVUASmSRqxaw8gpk_%E8%9E%A2%E5%B9%95%E5%BF%AB%E7%85%A7%202015-04-29%20%E4%B8%8B%E5%8D%883.37.46.png)
圖片來源: https://developers.google.com/maps/documentation/javascript/examples/layer-heatmap

有了簡單的認知後，那麼就來說明如何透過 Google Map API 在 Google Map 載入熱圖。

首先是在載入 Google Maps API 時，必須加入 **visualization** 這個 library。在後面加入 `libraries=visualization` 即可，要是想再加入其他 library 可用 `,` 逗號隔開。如果沒有載入 visualization 這個 library，熱圖會無法顯示。

``` html
<script type="text/javascript"
  src="https://maps.googleapis.com/maps/api/js?libraries=visualization">
</script>
```

再來，因爲熱圖其實是一連串 **座標點** 與 **數值** 的集合，所以我們要先準備好一個陣列來存放 LatLng 物件的集合：

``` javascript
// 存放 google.maps.LatLng 物件的陣列
var heatmapData = [
  new google.maps.LatLng(37.782, -122.447),
  new google.maps.LatLng(37.782, -122.445),
	// 以下略 ...
];

// 產生一個 Heatmap Layer
var heatmap = new google.maps.visualization.HeatmapLayer({
  data: heatmapData
});

// 將 heatmap 圖層加入至 map
heatmap.setMap(map);
```

如果想要改變熱圖樣式的話也很簡單，有兩種方式，第一種是在 `new google.maps.visualization.HeatmapLayer` 時加入樣式的設定，如：

``` javascript
var heatmap = new google.maps.visualization.HeatmapLayer({
  data: heatmapData,
  gradient: ['transparent', '#f00', '#0f0', '#00f'],	// 指定顏色範圍 ex:透明, 紅, 綠, 藍
  radius: 10,				// 每個點的半徑 (單位 px)
  opacity: 0.5			// 熱圖圖層透明度 (0 ~ 1)
});
```

或是想改變已載入熱圖的樣式，可以透過 `heatmap.set()` :
``` javascript
// 改變範圍顏色
heatmap.set('gradient', ['transparent', '#f00', '#0f0', '#00f']);

// 改變透明度
heatmap.set('opacity', 0.5);
```

完整範例可以參考 Google Heat Map 文件：
https://developers.google.com/maps/documentation/javascript/examples/layer-heatmap

接著，同場加映。

如果今天我們有一份完整的 GeoJSON (關於 GeoJSON 可參考[前篇 GeoJSON 介紹](http://kuro.tw/posts/2015/04/28/through-the-google-maps-api-geojson-data))文件，是否可以直接生成熱圖？ 當然沒問題。

我們只要將取得的 GeoJSON 解析出來，並存入陣列內就可以輕鬆地生成熱圖。

這是 GeoJSON 範例的格式，可以看出來這是一個 Point，而且有個屬性 valueCount，值為 5。
``` javascript
{
  "type": "Feature",
  "geometry": {
      "type": "Point",
      "coordinates": [121.52803907522677, 25.036051507818932]
  },
  "properties": { "valueCount": 5 }
}
```

``` javascript
// 畫熱圖
function draw_heatmap(results) {
  var heatmapData = [];

  for (var i = 0; i < results.features.length; i++) {
    // 取得座標
    var coords = results.features[i].geometry.coordinates;
    // 要注意： LatLng 物件的經緯度順序與 GeoJSON 的座標順序相反
    var latLng = new google.maps.LatLng(coords[1], coords[0]);

    var weightedLoc = {
      // 位置
      location: latLng,
      // 單位強度，這裏由 GeoJSON 內的 valueCount 屬性取得
      weight: results.features[i].properties.valueCount
    };

    heatmapData.push(weightedLoc);
  }

	// 生成熱圖圖層
  var heatmap = new google.maps.visualization.HeatmapLayer({
    data: heatmapData,
    dissipating: true,
    map: map,
    radius: 40,
    gradient: ['transparent', '#f00', '#0f0', '#00f']
  });
}
```

![螢幕快照 2015-04-29 下午3.50.00.png](http://user-image.logdown.io/user/3292/blog/3340/post/262458/jE7EkvSumXUFaRKArSMw_%E8%9E%A2%E5%B9%95%E5%BF%AB%E7%85%A7%202015-04-29%20%E4%B8%8B%E5%8D%883.50.00.png)
DEMO: http://jsbin.com/xafagi/1/


如果想要 debug 確認生成的熱圖是否正確，因為我們是利用 GeoJSON 產生熱圖，所以我們可以透過 `map.data.addGeoJson( geoJson );` 來加入 marker 供我們確認。
![螢幕快照 2015-04-29 下午3.59.13.png](http://user-image.logdown.io/user/3292/blog/3340/post/262458/iU5KQsFgRQKTwgEjNraG_%E8%9E%A2%E5%B9%95%E5%BF%AB%E7%85%A7%202015-04-29%20%E4%B8%8B%E5%8D%883.59.13.png)
DEMO: http://jsbin.com/xafagi/2/


參考資料：
https://developers.google.com/maps/documentation/javascript/examples/layer-heatmap
https://developers.google.com/maps/documentation/javascript/heatmaplayer