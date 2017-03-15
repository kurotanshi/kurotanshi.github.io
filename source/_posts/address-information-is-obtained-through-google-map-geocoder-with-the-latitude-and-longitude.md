---
layout: post
title: '透過 Google map Geocoder API 以經緯度轉換地址資訊'
date: 2015-04-27 14:34
comments: true
categories: [Google Map]
tags: [google map, Geocoder]
---
前陣子因為需求的關係，需要以經緯度來轉換地址，幸好 Google map API 有提供 Geocoder 可以轉換大略地址的服務。使用方式非常簡單，我們這裡以立法院的經緯度 (25.0439892, 121.5212213) 為例：

``` javascript
var geocoder = new google.maps.Geocoder();

// google.maps.LatLng 物件
var coord = new google.maps.LatLng(25.0439892, 121.5212213);

// 傳入 latLng 資訊至 geocoder.geocode
geocoder.geocode({'latLng': coord }, function(results, status) {
  if (status === google.maps.GeocoderStatus.OK) {
    // 如果有資料就會回傳
    if (results) {
      console.log(results[0]);
    }
  }
  // 經緯度資訊錯誤
  else {
    alert("Reverse Geocoding failed because: " + status);
  }
});
```

我們傳入經緯度資訊 google.maps.LatLng 物件，透過 `geocoder.geocode` 轉換出來的結果會是這樣的：

![螢幕快照 2015-04-27 下午10.42.56.png](http://user-image.logdown.io/user/3292/blog/3340/post/262289/0bIqy71dT8Sb3YwERlEs_%E8%9E%A2%E5%B9%95%E5%BF%AB%E7%85%A7%202015-04-27%20%E4%B8%8B%E5%8D%8810.42.56.png)

如上圖，Google map Geocoder API 會回傳一連串的地址資訊，如果你需要的是已經整合好格式的地址，那麼將上面範例中的`results[0]` 改為 `results[0].formatted_address` 就可以取得「**100台灣台北市中正區青島東路1號**」這樣的地址資訊了。

下面提供一個完整的範例供各位參考：
``` javascript
var map, geocoder, popup;

function initMap() {
    geocoder = new google.maps.Geocoder();
    popup = new google.maps.InfoWindow();

    // 地圖初始設定
    var mapOptions = {
        center: new google.maps.LatLng(25.04674, 121.52168),
        zoom: 16,
        mapTypeId: google.maps.MapTypeId.ROADMAP
    };

    var mapElement = document.getElementById("mapDiv");

    // Google 地圖初始化
    map = new google.maps.Map(mapElement, mapOptions);

    // 設定 Google map 繪圖控制項
    var drawingManager = new google.maps.drawing.DrawingManager({
        drawingMode: null,
        drawingControl: true,
        drawingControlOptions: {
            position: google.maps.ControlPosition.TOP_CENTER,
            drawingModes: [google.maps.drawing.OverlayType.MARKER]
        }
    });

    // 開啟 Google map 繪圖控制項
    drawingManager.setMap(map);

    // 在地圖中加入 marker
    google.maps.event.addListener(drawingManager, 'markercomplete', function(marker) {
        // 取得 marker 的經緯座標
        var markerPosition = marker.getPosition();

        // 將經緯度透過 Google map Geocoder API 反查地址
        geocoder.geocode({
          'latLng': markerPosition
        }, function(results, status) {
            if (status === google.maps.GeocoderStatus.OK) {
                if (results) {
                    // 將取得的資訊傳入 marker 訊息泡泡
                    showAddress(results[0], marker);
                }
            } else {
                alert("Reverse Geocoding failed because: " + status);
            }
        });
    });

    // 設定 marker 的訊息泡泡
    function showAddress(result, marker) {
        map.setCenter(marker.getPosition());
        // 顯示傳入的地址資訊
        var popupContent = '<b>地址: </b> ' + result.formatted_address;
        popup.setContent(popupContent);
        popup.open(map, marker);
    }
}
```

![螢幕快照 2015-04-27 下午10.52.20.png](http://user-image.logdown.io/user/3292/blog/3340/post/262289/boE1unBmQsCitdOxHG01_%E8%9E%A2%E5%B9%95%E5%BF%AB%E7%85%A7%202015-04-27%20%E4%B8%8B%E5%8D%8810.52.20.png)

Demo: http://jsbin.com/penuqofabe/1/

參考網址: [Google Map 地理編碼服務](https://developers.google.com/maps/documentation/javascript/geocoding?hl=zh-tw)
