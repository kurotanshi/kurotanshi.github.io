---
layout: post
title: 在 Vue 取得 jsonp 的方式
date: 2017-08-03 14:04:48
comments: true
categories: [vue.js]
tags: [vue.js, jsonp]
---

如果你是從 V1 就開始用 Vue 開發的朋友，一定知道 Vue.js 重要的核心特性就是只關注於 view layout 的呈現與 Components 系統，提供最小化且必要的功能給開發者。
其他的功能都可以自由選用第三方套件來完成，這也是為什麼被稱為「漸進式框架」的原因。

所以，如果我們用 Vue.js 來開發網站，且想使用 ajax 從遠端取得資源的時候，也許有些人會選用 jQuery ($.get / $.ajax ...等) ，也有些人會用 vue-resource 來做搭配。 早期官方推薦 [vue-resource](https://github.com/pagekit/vue-resource)，到了 V2 官方的推薦 lib 改為 [Axios](https://github.com/mzabriskie/axios) 或是直接用[原生 Fetch API](https://developer.mozilla.org/zh-TW/docs/Web/API/Fetch_API)。

不過很可惜，除了<del>包山包海</del>的 jQuery 之外，其他目前都還不支援 jsonp 這樣的做法。 也就是說，如果你的專案上需要用到 jsonp ，而且又不希望掛上一大包的 jQuery，這裡有個簡單的套件可以幫助你。 (順便自己筆記)

<hr>

首先先安裝 jsonp 這個套件。

```
$ npm install jsonp --save
```

或

```
$ yarn add jsonp
```

然後在你的程式碼內這樣使用：

``` js
import jsonp from 'jsonp'

jsonp('http://www.example.com/foo', { /* DATA HERE */ },
  function (err, data) {
    if (err) {
      console.error(err.message);
    } else {
      console.log(data);
    }
  });
```

就可以了。

