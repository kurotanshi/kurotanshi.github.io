---
title: '[筆記] Google 街景 API 加入圖標與預設角度計算'
date: 2017-04-04 15:10:00
comments: true
categories: [Google Map]
tags: [google map, street map api]
---

相信大家都知道 Google Map 在多年以前就開放了「街景檢視」這樣的服務，當然 Google Map API 也提供了給開發者使用相關的 API 服務：「<a target="_blank" href="https://developers.google.com/maps/documentation/javascript/streetview">Street View Service</a>」來開發地圖的應用。


除了原本在 Google Map 上面就有的街景檢視之外，透過 Street View API 開發者可以自由地在街景地圖上加入標示項，作法就跟平時在地圖上加入 marker 一樣：

``` javascript
  var marker = new google.maps.Marker({
    map: map,
    icon: '//chart.googleapis.com/chart?chst=d_map_pin_icon&chld=train|ffff00',
    position: {lat: 25.051269, lng: 121.512386}
  });

```

如下圖黃色 icon：
<img src="/static/img/map-marker.png">


這個時候我們切換成街景檢視的時候，你會發現還需要自己手動調整角度才能找到 icon 的位置：
<br>(如下地圖，可按右下角按鈕切換街景)
<iframe style="border: 1px solid #aaa; width: 720px; height: 300px;" src="https://kuro.tw/demo-pages/maps/map-add-marker.html"></iframe>

需要將視角往左邊移動後才能找到 icon 的位置。

<hr>

那麼，是否有更好的方式可以讓 Google 街景可以自動判斷視角方向呢？

有的，可以利用 Google Map 所提供的 <a target="_blank" href="https://developers.google.com/maps/documentation/javascript/geometry">Geometry Library</a> 來協助我們計算兩點之間的角度。

在 Google Maps API 內，「方向」是以與正北的角度來定義，也就是從正北 (0 度) 的順時針方向計算的角度，可以使用 `computeHeading()` 方法，將兩個 `from` 和 `to` 的 LatLng 物件傳遞給它，來計算兩個位置之間的方向。

如下：

``` javascript
var heading = google.maps.geometry.spherical.computeHeading(from_latLng, target_latlng);

```

`google.maps.geometry.spherical.computeHeading( )` 就會回傳兩點之間的角度，接著，我們就可以透過
`panorama.setPov({ heading: heading, pitch: 0 });` 來指定街景的預設角度了。


