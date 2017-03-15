---
layout: post
title: '在 Google Map 加入 D3 圖像'
date: 2015-05-19 16:05
comments: true
categories: [D3.js]
tags: [d3.js, google map]
---
因為昨天 [台北市政府開放平台](http://data.taipei/) 開始提供 [臺北捷運各站進出量統計](http://data.taipei/opendata/datalist/datasetMeta?oid=1d71c478-205f-42c5-8386-35f86d74fdd1) 的統計資料，所以就在思考可以利用這份資料做什麼應用，第一個想到的就是能否透過 D3 與地圖的共同呈現，當然就從我最熟悉的 Google Map 開始。

不過這篇文章只會介紹到如何在 Google Map 加入 D3 (SVG) 圖像。
等我將捷運各站資訊加入後也許會還有下回，哈哈哈。

先看結果 - Demo: http://output.jsbin.com/wexiva/1/
![螢幕快照 2015-05-19 下午11.34.22.png](http://user-image.logdown.io/user/3292/blog/3340/post/276547/NyZaTlSTDuH8v6DRzWAY_%E8%9E%A2%E5%B9%95%E5%BF%AB%E7%85%A7%202015-05-19%20%E4%B8%8B%E5%8D%8811.34.22.png)

整個程式非常簡單，首先在 Google map 加入一個 `google.maps.OverlayView()`，然後透過 `d3.csv` 載入資料。在繪製 marker (圓點) 的時候，透過自訂的 `transform function` 去指定它的座標就完成了。

相關程式如下:

``` javascript

// 捷運各站經緯度資訊: https://dl.dropboxusercontent.com/u/12537630/mrt.csv
// 資料來源: https://github.com/repeat/taipei-metro-stations

d3.csv("https://dl.dropboxusercontent.com/u/12537630/mrt.csv", function(data){
  var overlay = new google.maps.OverlayView();

  // 新增 OverlayView 到 google map
  overlay.onAdd = function() {
    var layer = d3.select(this.getPanes().overlayLayer).append("div")
        .attr("class", "stations");

    overlay.draw = function() {
      var projection = this.getProjection(), padding = 16;

      var marker = layer.selectAll("svg")
          .data(d3.entries(data))
          .each(transform)
          .enter().append("svg:svg")
            .each(transform)
            .attr("class", "marker");

      // 加入圓點
      marker.append("svg:circle")
          .attr("r", 6)
          .attr("cx", padding)
          .attr("cy", padding);

      // 加入標籤
      marker.append("svg:text")
          .attr("x", padding + 7)
          .attr("y", padding)
          .attr("dy", ".31em")
          .text(function(d) { return d.value.name; });

      // transform function. 指定每個點的座標.
      function transform(d) {
        d = new google.maps.LatLng(d.value.lat, d.value.lng);
        d = projection.fromLatLngToDivPixel(d);
        return d3.select(this)
            .style("left", (d.x - padding) + "px")
            .style("top", (d.y - padding) + "px");
      }
    };
  };

  // 將 overlay 加入到 google 地圖
  overlay.setMap(map);
});
```

#### 參考:
- http://bl.ocks.org/mbostock/899711
- https://developers.google.com/maps/documentation/javascript/customoverlays?hl=zh-tw

***

#### 本系列文章列表:
- [在 Google Map 加入 D3 圖像 (1)](http://kuro.tw/posts/2015/05/20/join-the-d3-in-google-map-image)
- [在 Google Map 加入 D3 圖像 (2)](http://kuro.tw/posts/2015/05/20/added-to-the-google-map-images-d3-2)

***