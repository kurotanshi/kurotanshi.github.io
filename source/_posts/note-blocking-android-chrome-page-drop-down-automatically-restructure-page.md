---
layout: post
title: '[筆記] 阻擋 android chrome 網頁下拉自動重整頁面'
date: 2015-05-11 15:00
comments: true
categories: [android]
tags: [android, chrome, mobile]
---
早上開會時，同事提到了 Android 升級後，Chrome for Android 會出現網頁畫面在瀏覽器頂端時，往下拉會重整頁面的問題(<del>其實應該說是 Feature XD</del>)，因為這個新功能會造成網頁使用上的一些困擾，於是試了幾種方式，發現可以阻擋網頁重整的動作，趁還有印象就把它記錄下來。

![2015-05-11 15.12.02.jpg](http://user-image.logdown.io/user/3292/blog/3340/post/275711/HX5wrVYQ8WW8gkKhK8rQ_2015-05-11%2015.12.02.jpg)
(就是這個小圈圈，看到它就代表網頁要重整了)

原理其實很簡單，就是在當 chrome 已經到達網頁頂端時，而且判斷 touchmove 是往下拉時，把事件阻擋掉，就可以了。

可以用 Android 試試看。
Demo: http://jsbin.com/conuga/1/

聽說 iOS 也有類似問題，不過我手邊沒有 iPhone，所以無法測試。 orz


``` javascript
window.addEventListener('load', function() {

    var isWindowTop = false;
    var lastTouchY = 0;

    var touchStartHandler = function(e) {
        if (e.touches.length !== 1) return;
        lastTouchY = e.touches[0].clientY;
        isWindowTop = (window.pageYOffset === 0);
    };

    var touchMoveHandler = function(e) {
        var touchY = e.touches[0].clientY;
        var touchYmove = touchY - lastTouchY;
        lastTouchY = touchY;

        if (isWindowTop) {
            isWindowTop = false;
            // 阻擋移動事件
            if (touchYmove > 0) {
                e.preventDefault();
                return;
            }
        }

    };

    document.addEventListener('touchstart', touchStartHandler, false);
    document.addEventListener('touchmove', touchMoveHandler, false);

});
```