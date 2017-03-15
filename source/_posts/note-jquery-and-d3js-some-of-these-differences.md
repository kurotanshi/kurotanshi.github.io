---
layout: post
title: '[note] jQuery 與 d3.js 的一些不同之處'
date: 2015-04-22 07:19
comments: true
categories: [D3.js]
tags: [jQuery, d3.js]
---
以前跟人家介紹 d3.js 的時候，我都會笑稱 d3.js 其實就是 SVG 界的 jQuery。
但是最近發現不少人對兩者的 select 以及 append 有些疑問，特別把它寫下來紀錄。

先說大家比較熟悉的 jQuery：

``` javascript
  $("body")
    .append("<div></div>")
    .append("<em></em>");
```

上面這段程式碼產生的 HTML 應該會是這樣的

``` html
  <body>
    <div></div>
    <em></em>
  </body>
```

而在 d3.js 的情況：

``` javascript
  d3.select("body")
    .append("div")
      .append("em");
```

所產生的 HTML 結構會是這樣的：

``` html
  <body>
    <div>
      <em></em>
    </div>
  </body>
```


兩者最大的差異點在於 jQuery 的
``` javascript
  $("body").append(...)
```

回傳的會是 $('body') 本身，而 d3.js 的

``` javascript
  d3.select("body").append(...)
```
回傳的會是 append 後的 DOM
(以上面的範例來說，`d3.select("body").append('div')` 回傳的就會是 `<div></div>` )，
所以再次執行 append 時，就會將 DOM 加入在前一個 append 的元素，也就是 `<div></div>` 之內。

那麼 jQuery 是否也可以像 d3.js 這樣 append 在新 DOM 內呢？
先將 em 包在 div 內，最外層再包一層 append 就可以做到了。
像這樣：

``` javascript
  $("body").append( $('<div>').append( $('<em>') ) );
```
