---
title: VueJS 元件載入模板 (template) 的幾種方式
date: 2017-09-21 12:36:12
categories: [vue.js]
tags: [vue.js]
---

<img src="/static/img/vue-template.png">

平常已經在使用 VueJS 開發專案的朋友，相信對 Vue Components 的用法已經不陌生，
而 Component 有個相當棒的特性，就是將 HTML 封裝起來，掛載在網頁上的時候，只需要透過自定義的 tag 如 `<components></components>` 就可以掛載至網頁上。

<hr>

如果你是透過 CDN 載入 VueJS 做開發的朋友，相信最熟悉的方式應該是 in-HTML Templates 的方式：

``` html
<h1>{{ hello }}</h1>

<script type="text/javascript">
var vm = new Vue({
  data: {
    hello: 'Hello, World!'
  }
})
</script>
```

<hr>

當我們開始切分子元件 (Child Components) 之後，通常會使用到 `template` 這個 option，然後裡面可以填入 HTML 的模板字串：

``` html
<greeter name="World"></greeter>
```

``` javascript
Vue.component('greeter', {
  template: '<div> Hello, {{ name }}!</div>',
  props: ['name'],
});
```

<hr>

然而，隨著專案規模的擴增，我們的 HTML 模板結構可能會變得越來越大，光是用 `template` 直接掛上 HTML 字串時，可能你的程式架構就會變得不是那麼好閱讀、管理，這時候，我們可以把整個 HTML 模板區塊透過 `<script id="xxx" type="text/x-template"> </script>` 這樣的 `<script>` 標籤來封裝我們的 HTML 模板，這種方式通常被稱為「X-Templates」：

``` html
<script type="text/x-template" id="my-component">
  <div class="component">A custom component of Vue!</div>
</script>
```

然後在 template 的 option 可以直接帶入對應的 CSS Selector，像這樣：

``` html
Vue.component('my-component', {
  template: '#my-component'
});
```

<hr>

再來，如果你有聽過 Vue Components 的編譯作用域 (Compilation Scope) 的話，你應該會知道在子元件中帶入的任何 tag 是沒有意義的：

``` html
<child-component>
  {{ message }}
</child-component>
```

像上面這樣，`<child-component>` 在經過編譯之後，會直接把 {{ "{{ message " }}}} 忽略掉。

如果這種時候，你又非得要在 `<child-component>` 安插 HTML 模板不可時，你就可以透過 `inline-template` 這個屬性來幫忙：

``` html
<my-component inline-template>
  <div>
    <p>These are compiled as the component's own template.</p>
    <p>Not parent's transclusion content.</p>
  </div>
</my-component>
```

但要小心，加入了 `inline-template` 之後，不要跟 `<slot>` 的有效範圍搞混了，`inline-template` 的內容是由子元件提供，而 `<slot>` 的內容是由父層所提供。

<hr>

有寫過 AngularJS 1 的朋友可能會問，過去寫 AngularJS 的時候，directive 裡面有個選項叫做 `templateUrl`，可以讓我們將模板儲存至另外一個 HTML 檔案，再透過這個 HTML 載入，那麼在 Vue 裡面是否也有類似用法呢？

在講這個之前，我們先來介紹 Vue-loader。
Vue-loader 最大的功能就是他可以將 Vue 的元件封裝成單獨的 .vue 檔案，這個檔案同時包含了三個部分：`<template>` 、 `<script>` 以及 `<style>`。
如果你有用過 Vue CLI 建立專案 (webpack、webpack-simple) 的話，應該對封裝 .vue 檔案的步驟不陌生，這個功能就是 Vue-loader 在背後替我們處理的。

相信大家都知道，既然已經封裝成 .vue 檔案，那麼 HTML 字串模板的部分就可以通通往 `<template>` 裡面放就好。

回到主題。那麼，如果要透過外部 HTML 檔案載入的話，跟這個有什麼關係？
可能很多人不知道，.vue 檔案的各區塊，其實是可以用 `src` 屬性來載入外部檔案的。
像這樣，你就可以透過外部的靜態檔案來做為你元件的內容來源：

``` html
<template src="./template.html"></template>
<script src="./script.js"></script>
<style src="./style.css"></style>
```

這部分可以参照 Vue-loader 文件的說明： https://vue-loader.vuejs.org/en/start/spec.html#src-imports

另外，如果你用的是 laravel-elixir 以及 laravel mix.browserify 已經幫你包裝好的方法的話，更可以直接這樣寫：

``` javascript
module.export = {
  template: require('./templates/template.html'),
  data: function(){
      return {
      text: "Hello World!"
      };
  }
};
```

template.html 像這樣：

``` html
<h1>{{ text }}<h1>
```

<hr>

上面就是在 VueJS 開發專案上面常見的模版掛載方式，提供給大家參考。

