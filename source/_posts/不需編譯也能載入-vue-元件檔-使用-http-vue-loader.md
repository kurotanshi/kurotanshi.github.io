---
layout: post
title: '不需編譯也能載入 .vue 元件檔: 使用 http-vue-loader'
date: 2017-07-11 00:16:55
comments: true
categories: [vue.js]
tags: [vue.js, httpVueLoader]
---

上週在 Vue 社群圈有個令人興奮的熱門新聞: CodePen 可以支援 `.vue` 檔案了！

<blockquote class="twitter-tweet" data-lang="zh-tw"><p lang="en" dir="ltr">Check it out - you can use `.vue` files in CodePen Projects easily:<a href="https://t.co/Cd3Qr11xYQ">https://t.co/Cd3Qr11xYQ</a> <a href="https://t.co/QvtIXxKRUk">pic.twitter.com/QvtIXxKRUk</a></p>&mdash; Chris Coyier (@chriscoyier) <a href="https://twitter.com/chriscoyier/status/880859500185616387">2017年6月30日</a></blockquote>
<script async src="//platform.twitter.com/widgets.js" charset="utf-8"></script>

身為使用 Vue 的開發者聽到這樣的消息當然感到相當開心，但同時也不禁感到好奇，CodePen 是如何做到不須透過 webpack 編譯 vue 檔案，就可以將 .vue 的 component 如實顯示到網頁中。

大家都知道，在網頁開發的世界中，前端是沒有秘密的。 打開了開發工具，才知道原來是透過 **[http-vue-loader](https://github.com/FranckFreiburger/http-vue-loader)** 這個工具做到的。

<hr>

**[http-vue-loader](https://github.com/FranckFreiburger/http-vue-loader)** 這套工具可提供開發者直接在網頁環境中載入 `.Vue` File，無需透過 nodeJS 環境編譯，也不需要 Build 的步驟。

用法很簡單，首先在網頁上載入 Vue 與 http-vue-loader，如下

``` html
  <script src="https://unpkg.com/vue"></script>
  <script src="https://unpkg.com/http-vue-loader"></script>
```

接著，假設我們有一個 `my-component.vue` 的檔案：
``` html
<template>
    <div class="hello">Hello {{who}}</div>
</template>

<script>
module.exports = {
    data: function() {
        return {
            who: 'world'
        }
    }
}
</script>

<style>
.hello {
    background-color: #ffe;
}
</style>

```

那麼，我們就可以在 `components` 內透過 `httpVueLoader` 來載入我們的子元件：

```
<div id="my-app">
  <my-component></my-component>
</div>

<script type="text/javascript">
  new Vue({
    el: '#my-app',
    components: {
      'my-component': httpVueLoader('my-component.vue')
    }
  });
</script>
```
<br>

當然，httpVueLoader 也提供了類似 `Vue.component('my-component', { ... })` 的宣告方式:

``` js
  httpVueLoaderRegister(Vue, 'my-component.vue');

  new Vue({
      components: [
          'my-component'
      },
```

或是將 httpVueLoader 當作 Plugin 來使用：

``` js
  Vue.use(httpVueLoader);

  new Vue({
    components: {
        'my-component': 'url:my-component.vue'
    },
    ...
```

甚至是 Array 的形式也可以：

``` js
    new Vue({
        components: [
            'url:my-component.vue'
        },
        ...
```

需要注意的是，httpVueLoader 目前僅支援 Vue 2 以上的版本，而作者也在專案內說明， httpVueLoader 只是提供一個簡便的測試與開發環境，方便開發者不需要透過繁複的編譯過程才能使用 vue file 進行開發。 

若是要發佈到線上的專案，建議還是需要透過工具編譯打包，會有更好的效能以及更佳的支援度喔。
