---
layout: post
title: '在 Google Map 加入 D3 圖像 - 2'
date: 2015-05-20 15:49
comments: true
categories: [D3.js]
tags: [google map, d3.js]
---
[上一篇](http://kuro.tw/posts/2015/05/20/join-the-d3-in-google-map-image "在 Google Map 加入 D3 圖像 ")提到了如何在 Google Map 裡面加入 D3 的圖像，這次我們實際將資料套進去吧。

資料來源是上篇提到的[臺北捷運各站進出量統計](http://data.taipei/opendata/datalist/datasetMeta?oid=1d71c478-205f-42c5-8386-35f86d74fdd1)的統計資料，因為台北市政府開放平台並沒有提供 CORS (跨來源資源共享)的服務，沒關係，我們直接將資料下載存成 json 檔案即可。 (範例為 2015/4 進出站人數)

按照慣例，先看結果 - Demo: http://jsbin.com/wexiva/3/
![螢幕快照 2015-05-20 下午11.54.46.png](http://user-image.logdown.io/user/3292/blog/3340/post/276658/4DhTbWRwSvujvsxZC2Z5_%E8%9E%A2%E5%B9%95%E5%BF%AB%E7%85%A7%202015-05-20%20%E4%B8%8B%E5%8D%8811.54.46.png)

藍色的是進站人數，橘色的是出站人數。可以看出各站在 4/1 的進出站人數相當平均。

因為這次抓取的資料比較多，所以程式也稍微複雜一點，不過沒關係，概念還是很簡單的。
不囉唆直接看 code.


``` javascript
var map;
var overlay = new google.maps.OverlayView();
var sta_in = [], sta_out = [], mrtData;

// 讀取資料, sta_in = 2015 年四月進站人數, sta_out = 2015 年四月出站人數.
// 資料來源: http://data.taipei/opendata/datalist/datasetMeta?oid=1d71c478-205f-42c5-8386-35f86d74fdd1
d3.csv("https://dl.dropboxusercontent.com/u/12537630/mrt.csv",
function(data){
  mrtData = data;

  // 進站
  d3.json("https://dl.dropboxusercontent.com/u/12537630/mrt-in-april.json",
  function(error, json) {
    sta_in = json.result.results;
    if( sta_in.length > 0 && sta_out.length > 0 ) { drawMap(); }
  });

  // 出站
  d3.json("https://dl.dropboxusercontent.com/u/12537630/mrt-out-april.json",
  function(error, json) {
    sta_out = json.result.results;
    if( sta_in.length > 0 && sta_out.length > 0 ) { drawMap(); }
  });
});

function drawMap() {

    // 設定圓餅圖長寬, 半徑
    var width = 35, height = 40, radius = Math.min(width, height) / 2;

    // 透過 d3.scale.category10() 生成顏色
    var color = d3.scale.category10();

		// d3.layout.pie()
		var pie = d3.layout.pie();

    // 設定圓餅內外層半徑, 這裏內層設 0.
    var arc = d3.svg.arc()
        .innerRadius(0)
        .outerRadius(radius);

    overlay.onAdd = function() {
        var layer = d3.select(this.getPanes().overlayLayer).append("div")
            .attr("class", "stations");

        overlay.draw = function() {
            var projection = this.getProjection(),
                padding = 16;

            // 針對每一筆捷運站增加 marker
            var marker = layer.selectAll("svg")
                .data(d3.entries(mrtData))
                .each(transform)
                .enter().append("svg")
                .each(transform)
                .attr({
                    'class': "marker",
                    "width": width,
                    "height": height,
                    "transform": "translate(" + width / 2 + "," + height / 2 + ")",
                });

            // 將取得的進出站資料透過 .data( pie([ 進站人數, 出站人數 ]) ) 指定到圓餅圖中。
            // sta_in[0] 代表 4/1 進站人數, sta_out[0] 代表 4/1 出站人數.
            var g = marker.selectAll("g")
                .data(function(d, i) {
                    return pie([parseInt(sta_in[0][d.value.name].replace(',', ''), 10), parseInt(sta_out[0][d.value.name].replace(',', ''), 10)]);
                })
                .enter()
                .append("g");

            // 著色
            g.append("path")
                .attr({
                  "fill": function(d, i) { return color(i); },
                  "d", arc,
                  "transform": "translate(" + width / 2 + "," + height / 2 + ")"
                });

            // 加入標籤
            marker.append("text")
                .attr("x", padding + 7)
                .attr("y", padding)
                .attr("dy", ".31em")
                .text(function(d) {
                    return d.value.name;
                });

            function transform(d) {
                d = new google.maps.LatLng(d.value.lat, d.value.lng);
                d = projection.fromLatLngToDivPixel(d);
                return d3.select(this)
                    .style("left", (d.x - padding) + "px")
                    .style("top", (d.y - padding) + "px");
            }
        };

    };
    overlay.setMap(map);
}

```


#### 本系列文章列表:
- [在 Google Map 加入 D3 圖像 (1)](http://kuro.tw/posts/2015/05/20/join-the-d3-in-google-map-image)
- [在 Google Map 加入 D3 圖像 (2)](http://kuro.tw/posts/2015/05/20/added-to-the-google-map-images-d3-2)

***