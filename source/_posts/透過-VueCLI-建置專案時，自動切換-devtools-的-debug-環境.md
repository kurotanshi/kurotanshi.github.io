---
layout: post
title: 透過 Vue-CLI 建置專案時，自動切換 devtools 的 debug 環境
date: 2016-12-08 11:40:00
comments: true
categories: [vue.js]
tags: [vue.js, webpack, devtools]
---

Vue 提供了相當好用的 debug 工具 (Chrome 套件)， [Vue.js devtools](https://chrome.google.com/webstore/detail/vuejs-devtools/nhdogjmejiglipccpnnnanhbledajbpd)，在安裝之後當你用 Chrome 開啟 Vue 專案時，Chrome 的開發者工具會多出一個 Vue 的 Tab，然後把 Vue Instance 裡的屬性通通列出來：

<img src="/static/img/vue-tool-screenshot.png">

在預設開啟 debug mode 時，按下 Vue devtools 的 icon 你會看到這樣畫面：
<img src="/static/img/vue-tool-on.png">

這時候打開 Chrome 開發者工具， vue 專案中所有 Instance 的屬性資料通通一覽無遺。

<hr>

但是當專案完成要上線的時候，就應該要將 debug mode 關閉，尤其在屬性可能會存有較為機敏性資料的時候。
關閉的方式也很簡單，加上這兩行就可以了：

``` javascript
Vue.config.debug = false;
Vue.config.devtools = false;
```
關閉後，再開啟 Vue 專案，雖然 icon 偵測到 Vue.js 會亮起來，但是你會看到這樣訊息：
<img src="/static/img/vue-tool-disabled.png">

這時候 Chrome 開發者工具的 Vue tab 就不會出現了。

<hr>

但是，在建置專案的時候，常常會忘記手動調整 debug mode，導致上線的專案會是開啟 debug mode 的版本，
是否可以在 webpack 啟動的時候自動判斷目前環境呢？

以 Vue-CLI 提供的 package.json 為例，他提供了兩個預設的 scripts， dev 用在開發專案時使用，而 build 則用於建置 production 使用：
<img src="/static/img/vue-package-json.png">

圖中可以看到，在 build 模式下多了 `NODE_ENV=production` 這個參數。

這時候我們就可以利用 `NODE_ENV` 這個參數來判斷使否開啟 debug mode：

``` javascript
import Vue from 'vue'
import App from './App.vue'

const isDebug_mode = process.env.NODE_ENV !== 'production';

Vue.config.debug = isDebug_mode;
Vue.config.devtools = isDebug_mode;
```

這樣 `Vue.config.debug` 的版本就會自動依照執行階段的不同而有不同的設定了。


