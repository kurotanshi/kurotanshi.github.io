---
layout: post
title: 從 Vue 來看 CSS 管理方案的發展
date: 2017-07-26 14:48:26
comments: true
categories: [vue.js]
tags: [vue.js, CSS]
---

昨天看到 caesar 大大發表的 [react & CSS 另類新選擇](https://blog.caesarchi.com/2017/07/25/react-css-styled-components/)，講的其實是 styled-components + react 的 CSS 處理方案。 現今的幾個主流前端框架大多也有類似的做法，身為 Vue 的擁護者，這裡就來簡單說明一下 Vue 的處理方式。

先從早期的 CSS 管理方案開始說起吧。

## CSS 預處理器、CSS 命名與架構學

這麼多年來，CSS 的管理一直都是開發者的夢靨，很大的原因在於 CSS 的程式化與 JavaScript 相比其實是相對困難的，
尤其在於 JS 至少還有它的 scope 可以切分，而 CSS 在這點是相對弱勢的，所有的 CSS 樣式都是 global scoped。 如果頁面上的模組過多，管理起來更是難以維護。
嚴格來說，CSS 本身甚至都不能算是個程式語言。


所以，早期在程式面會有所謂的 LESS, SASS, Stylus 等這樣的工具，藉由 preprocessor (預處理器) 來做編譯，可以做到繼承、重用、複寫等功能。
另一方面，除了上述說的透過工具的預先編譯外，也有另一派人馬提倡的是，由 CSS 的命名學 / 架構論 來完成 CSS 模組的管理與複用。

在幾年前 (2014) ，小弟也有針對此類 CSS 方法論在前端社群聚會做分享，有興趣的朋友可以看下面這份投影片：

<iframe src="//www.slideshare.net/slideshow/embed_code/key/MOVEP2qBeaGyEO" width="595" height="485" frameborder="0" marginwidth="0" marginheight="0" scrolling="no" style="border:1px solid #CCC; border-width:1px; margin-bottom:5px; max-width: 100%;" allowfullscreen> </iframe> 


簡單來說，就是透過命名規則，來做到 CSS 架構的管理。 
<del>但大家都知道，訂出了 convention ，要是沒有嚴格檢查是否遵守，其實跟沒有一樣 XD </del>

## CSS Modules

所以後來出現了比較符合人性的 CSS Modules，透過工具來處理先前那些人工命名規範要做的事。
也就是說，由 Webpack 做了 BEM 的事情。 過去 BEM 是人工手動做，而 Webpack 是交給工具自動化做，用類似 Javascript module 的方式來處理 CSS。


講了大半個篇幅，終於要講到目前的主流： CSS in JS / styled-components 了。

## CSS in JS / All in JS

CSS in JS 的概念最早是由 React 社群所提出，支持與不支持的兩派在社群當中也有著激烈的論戰。
我過去也是反對此類做法，但目前保持中立態度看待。 XD

過去我剛入行的時候，前輩大力倡導 HTML / CSS / JavaScript 三者負責的領域要切得越乾淨越好，簡單來說，inline-style 與 inline-script 都是禁止的。
一旦 inline-style 與 inline-script 寫得越多，程式碼就會像義大利麵般攪在一起難以維護。

而自從 component-based 的前端工具出現後，過去的原則漸漸被打破。
像 React ，就有下面這樣的寫法。

``` js
const style = {
  'color': 'red',
  'fontSize': '46px'
};

const clickHandler = () => alert('hi'); 

ReactDOM.render(
  <h1 style={style} onclick={clickHandler}>
     Hello, world!
  </h1>,
  document.getElementById('example')
);
```

將一個網頁元件 (H1) 的**樣式、事件與結構，通通封裝在一份程式碼**當中。

而 Vue 也有類似的做法，像 Vue 提供的 Vue 元件檔：

``` HTML
/* HTML */
<template>
  <h1 @click="clickHandler">{{ msg }}</h1>
</template>

/* script */
<script>
module.exports = {
  data: function() {
    return {
      msg: 'Hello, world!'
    }
  },
  methods:{
    clickHandler(){
      alert('hi');
    }
  }
}
</script>

/* scoped CSS */
<style scoped>
h1 {
  color: red;
  font-size: 46px;
}
</style>
```

在 Vue 元件檔，透過上面這樣的方式提供了一個 template (HTML)、script 以及帶有 scoped 的 style 樣式，也仍然可以保有過去 HTML、CSS 與 JS 分離的開發體驗。
但本質上仍是 all-in-JS 的變種語法糖就是了 XD

<img src="/static/img/vue-file.png">

值得一提的是，當 style 標籤加上 **scoped** 屬性，那麼在 Vue 元件檔經過編譯後，會自動在元件上打上一個隨機的屬性，再透過 CSS Attribute Selector 的特性來做到 CSS scope 的切分，使得即便是在不同元件檔裡的 h1 也能有 CSS 樣式不互相干擾的效果。 當然開發起來，比起 JSX、或是 inline-style 等的方式，這種折衷的作法更合我的胃口 😁

