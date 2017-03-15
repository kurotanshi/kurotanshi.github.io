---
layout: post
title: '[snippet] D3.js 甜甜圈圖 (donut chart) 的放大漸變效果'
date: 2015-05-04 10:47
comments: true
categories: [D3.js]
tags: [D3.js, donut chart]
---
做法很簡單，就是做兩個 `d3.svg.arc()` 然後在 `mouseover` & `mouseout` 的時候改變 `d` 屬性即可。

這裏就拿 http://bl.ocks.org/mbostock/3887193 作為範例。

DEMO: (original charts)
http://output.jsbin.com/darexo/1/

DEMO2: (add hover effect)
http://output.jsbin.com/darexo/3/

<iframe class="jsbin-embed" src="http://jsbin.com/darexo/3/embed?output" frameborder="0" height="400"></iframe>

``` javascript
// original
var arc = d3.svg.arc()
    .outerRadius(radius - 10)
    .innerRadius(radius - 70);

// arc with scaling
var arcOver = d3.svg.arc()
    .outerRadius(radius)
    .innerRadius(radius - 70);
```

中間略

``` javascript
// adding mouse events with transition
g.selectAll(".arc > path")
	.on({
		"mouseover": function(d, i){
			d3.select(this).transition().duration(250).attr('d', arcOver);
		},
		"mouseout": function(d, i){
			d3.select(this).transition().duration(250).attr('d', arc);
		}
});
```

補充：感謝小馬提出 hover 在 text 上會無法執行動畫的問題。
只要把 mouse event 改綁在上一層的 `<g>` 上面，或是在 text 的 CSS 下 `pointer-events: none;` 取消它的 pointer 事件即可。

``` javascript
  g.on({
    "mouseover": function(d, i){
      d3.select(this.childNodes[0]).transition().duration(250).attr('d', arcOver);
    },
    "mouseout": function(d, i){
      d3.select(this.childNodes[0]).transition().duration(250).attr('d', arc);
    }
  });
```
