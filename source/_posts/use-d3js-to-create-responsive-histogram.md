---
layout: post
title: '利用 d3.js 製作 responsive 的長條圖'
date: 2015-12-20 06:02
comments: true
categories: [D3.js]
tags: [d3.js, rwd]
---
利用 d3.js 我們可以很輕易地產生我們想要的圖表，以最常見的長條圖為例，只要透過 scale (比例尺) 與 axis (軸線)，再加上一點 SVG 的基礎知識，像這樣的長條圖一下子就可以生成。

![螢幕快照 2015-12-20 下午12.21.25.png](http://user-image.logdown.io/user/3292/blog/3340/post/378297/2V8wHgLUTEawU5iJstYS_%E8%9E%A2%E5%B9%95%E5%BF%AB%E7%85%A7%202015-12-20%20%E4%B8%8B%E5%8D%8812.21.25.png)
[[Code](http://kurotanshi.github.io/d3js-samples/rwd/rwd.html "長條圖範例")]

但是，像這樣尺寸的圖表，往往都會因為太大而不適合在手機螢幕上呈現。還好 SVG 有著向量圖形的特性，可以自由縮放，這篇就來簡單介紹 d3.js 的長條圖如何也能做出 rwd 的效果。

在上面的程式碼內，我們可以看到，原先設定的寬高是寫死的 960 與 500 (未扣除邊界)
``` javascript
  var margin = 40,
      width = 960 - margin*2,
      height = 500 - margin*2;
```

所以第一步，我們先將外層的 `.content` 元素設定成寬高 100%，然後把原先寫死的寬高改成由程式去抓取實際的寬高
``` css
.content{
  display: block;
  width: 100%;
  height: 100%;
  min-width: 300px;
  max-width: 960px;
  max-height: 500px;
  overflow: hidden;
}
```

``` javascript
 // 將尺寸改成即時取得的寬高
 width = parseInt(d3.select(".content").style("width"), 10) - margin*2;
 height = parseInt(d3.select(".content").style("height"), 10) - margin*2;
```
然後我們將瀏覽器縮小之後重整，結果像這樣：
![螢幕快照 2015-12-20 下午1.23.36.png](http://user-image.logdown.io/user/3292/blog/3340/post/378297/Jrbqo6EaTJswhfBQUF6g_%E8%9E%A2%E5%B9%95%E5%BF%AB%E7%85%A7%202015-12-20%20%E4%B8%8B%E5%8D%881.23.36.png)
[[Code](http://kurotanshi.github.io/d3js-samples/rwd/rwd2.html "長條圖範例2")]

可以看到，這個時候因為畫面的寬高已經不是寫死的了，所以會依「繪製圖形當下」的寬高去做比例的修正。這時我們已經完成了長條圖 RWD 的第一步了。

這時要是將行動裝置螢幕橫擺後，比例並不會依照橫擺之後有所不同，如果要使用者不斷地重整頁面，這就太不友善了。所以，我們要在瀏覽器上加上 `resize` 事件，並將繪製圖形的動作通通封裝至 rendering 這個 function 內。

這個時候的程式架構會像這樣：
``` javascript
  var svg = d3.select('.svg');

  // 將繪製動作包裝至 function 內
  function rendering() {
     // 將繪製的程式碼通通搬到裡面
     // 內略
  }

  // 將 window 綁定 resize 事件，並重新繪製圖型
  d3.select(window).on('resize', rendering);

  // 首次繪製
  rendering();
```
所以，這個時候，我們可以任意拉放瀏覽器的尺寸
像這樣
![螢幕快照 2015-12-20 下午1.39.54.png](http://user-image.logdown.io/user/3292/blog/3340/post/378297/eTI8r7EXSQWaH80HZuKH_%E8%9E%A2%E5%B9%95%E5%BF%AB%E7%85%A7%202015-12-20%20%E4%B8%8B%E5%8D%881.39.54.png)

或是這樣
![螢幕快照 2015-12-20 下午1.40.01.png](http://user-image.logdown.io/user/3292/blog/3340/post/378297/NMpkG3LeTOWaYaNf9pYC_%E8%9E%A2%E5%B9%95%E5%BF%AB%E7%85%A7%202015-12-20%20%E4%B8%8B%E5%8D%881.40.01.png)

[[Code](http://kurotanshi.github.io/d3js-samples/rwd/rwd3.html "長條圖範例3")]
都是沒有問題的。

這份長條圖到目前為止已經可以說是好棒棒了。
可是不曉得有沒有人發現，在最後那張橫擺的 y 軸刻度實在太過擁擠，其實是不容易閱讀的:
![螢幕快照 2015-12-20 下午1.42.43.png](http://user-image.logdown.io/user/3292/blog/3340/post/378297/VA6ir3TRyutauy6ALqdH_%E8%9E%A2%E5%B9%95%E5%BF%AB%E7%85%A7%202015-12-20%20%E4%B8%8B%E5%8D%881.42.43.png)

我們可以怎麼樣更優化呢？

這時候就要利用 d3.js 提供的 `tick()` 功能，來為我們調整 y 軸上的刻度。
只要在 y 軸上加上 tick ，像這樣:

``` javascript
  // y 軸加上 ticks
  var yAxis = d3.svg.axis()
        .scale(yScale2)
        .orient("left")
        .ticks(Math.max(height/50, 2));
```

`Math.max` 會回傳兩個指定數字中較大的一個，而 `ticks()` 則是設定軸線上刻度的數量。

所以經過剛剛的設定，當圖形的高度大於 100px 的時候，圖表每 50px 會有一個刻度，而圖形高度小於或等於 100px 時，則至少會有兩個刻度，像這樣：

![螢幕快照 2015-12-20 下午1.54.29.png](http://user-image.logdown.io/user/3292/blog/3340/post/378297/Mz3vq9BKRZ2dkSd5SP6W_%E8%9E%A2%E5%B9%95%E5%BF%AB%E7%85%A7%202015-12-20%20%E4%B8%8B%E5%8D%881.54.29.png)

![螢幕快照 2015-12-20 下午1.55.08.png](http://user-image.logdown.io/user/3292/blog/3340/post/378297/swZcbhnQPmhiWQBwTbDG_%E8%9E%A2%E5%B9%95%E5%BF%AB%E7%85%A7%202015-12-20%20%E4%B8%8B%E5%8D%881.55.08.png)

[[Code](http://kurotanshi.github.io/d3js-samples/rwd/rwd4.html "長條圖範例4")]

實際用手機試試：

![2015-12-20 03.33.30.png](http://user-image.logdown.io/user/3292/blog/3340/post/378297/mM3agzeTGGGdM2p4YjZq_2015-12-20%2003.33.30.png)
![2015-12-20 03.33.43.png](http://user-image.logdown.io/user/3292/blog/3340/post/378297/j4wccnAOTZWhLzubTUDw_2015-12-20%2003.33.43.png)

透過這樣的修正，就可以讓圖表變得更好閱讀了。

以上，我們只要做簡單的小調整，就可以讓現有的 d3 長條圖做到有 RWD 的效果。

但是要注意的是，不是所有圖表都合適在手機螢幕上呈現，在設計時也需要把這些考慮進去，是要為手機版本另外做一個新的圖表，或是做 RWD 的設計，就看使用的情境以及想表達的意義來決定囉。
