---
layout: post
title: '[筆記] JavaScript 變數宣告與作用域'
date: 2015-07-08 10:11
comments: true
categories: [javascript]
tags: [javascript]
---
大家都知道，JavaScript 的變數有其作用域的範圍，若使用前未經 var 宣告，就會自動變成全域變數 (global variable)，而在其 code block 內宣告的變數也只有該 code block 內可以使用。

這次的問題，其實很久以前在 tonyQ 的聚會上就聽他說過了，<del>只是沒想到還真的會遇到 XDDDD</del>

``` javascript
var a = 1;

(function(){
  console.log(a);		// 1
})();
```
上面的 code 很簡單，就是宣告一個全域變數 a，然後值為 1 ，因為是全域變數，所以在之後的匿名函式內可以使用它。


``` javascript
var a = 1;

(function(){
  var a = 100;
  console.log(a);		// 100
})();
```
這次，在匿名函式內，我們另外宣告了一個 a，值為 100，因為其作用域的關係，所以 console 的結果會是 100。


接著，問題來了，如果我們在 `var a = 100;` 之前去取值，會發生什麼事呢？
``` javascript
var a = 1;

(function(){
  console.log(a);    //  ?
  var a = 100;
  console.log(a);    // 100
})();
```

答案是，第一次的 console.log(a) 會印出 **undefined**，而第二次會出現 100。

因為在匿名函數獨立的 scope 內，不管 var 是放在最前面，或是最後一行，他的變數實體在該 code block 一開始就是新的了，也就是說，剛剛的 code 其實等同下面這段：
``` javascript
var a = 1;

(function(){
  var a;
  console.log(a);    // undefined
  a = 100;
  console.log(a);    // 100
})();
```

所以第一次會印出 undefined 。

要怎麼排除這樣的問題呢，很簡單，要嘛一開始就不要取相同名稱，要嘛就透過參數的方式代入原本的變數，像這樣：
``` javascript
var a = 1;

(function(_a){
  console.log(_a);    // 1
  var a = 100;
  console.log(a);    // 100
})(a);
```

