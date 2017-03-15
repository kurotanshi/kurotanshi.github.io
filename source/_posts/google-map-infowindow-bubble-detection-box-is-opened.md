---
layout: post
title: '偵測 Google Map 的 InfoWindow 訊息框是否被開啟 (v3)'
date: 2014-08-08 08:58
comments: true
categories: [Google Map]
tags: [google map, infoWindow]
---

因為 Google Map 的 InfoWindow 是被新增出來的物件，所以我們可以透過修改 prototype 的方式，替 InfoWindow 增加新的 method。

``` javascript
  google.maps.InfoWindow.prototype.isOpen = function(){
    var map = this.getMap();
    return (map !== null && typeof map !== "undefined");
  }

  var popup = new google.maps.InfoWindow();
  popup.isOpen();  // will return ture or false.

```

參考資料： http://stackoverflow.com/a/12410385
