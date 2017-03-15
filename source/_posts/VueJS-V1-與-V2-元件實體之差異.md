---
title: '[VueJS] V1 與 V2 元件實體之差異'
date: 2016-10-03 18:20:01
comments: true
categories: [vue.js]
tags: [vue.js]
---

狂賀！ [Vue 2.0](https://github.com/vuejs/vue/releases/tag/v2.0.0 "Vue 2.0") 終於正式發佈！

關於 Vue 2.0 的新特性，作者也在官方 Blog - [Vue 2.0 is Here!](https://medium.com/the-vue-point/vue-2-0-is-here-ef1f26acf4b8#.n0m3yjfmp "Vue 2.0 is Here!") ([中文翻譯](http://jiongks.name/blog/vue-2-is-here/ "Vue 2.0 来了！")) 一文中敘述地相當詳細，這裡就不多說。

如果你也與我一樣是從 V1 就開始接觸的開發者，一定都知道 VueJS 最核心的一部分是 Component，而 Component 是由實體 (Vue Instance) 來實作。
在這篇文章，我們就來談談 Vue 1.x 與 2.x 元件實體的差異。

<hr>

### Vue 2.0 元件實體註冊

<img alt="tree of components" src="/static/img/Vue-instance.png">

像上面這樣的網站，我們可以將它抽象化為一棵「元件樹」，而每個元件樹都會有個根節點，或稱為根實體 (root Vue instance)。

那麼，每個 Vue 元件樹的根實體其實是透過 `Vue` 這個建構函式所產生：

``` javascript
var vm = new Vue({
  // options
});
```

將 Vue 元件與實體 DOM 結合的方式有兩種，一種是直接寫在 `el` option 內：

``` javascript
var vm = new Vue({
  el: '#app'
});
```

而另一種方式則是透過 `$mount` 來指定節點：
``` javascript
var vm = new Vue({
  // options without 'el'
});

vm.$mount('#app');
```

這部分跟 Vue 1.x 的註冊是完全一樣的，但是需要注意的是，在 vue 1.x 允許開發者以 `<body>` 或 `<html>` 作為根實體的掛載點，
但<span style="color: #f00">到了 VueJS 2.0 後，只能透過 **獨立的節點掛載** </span>，如: div 等。 否則會產生錯誤，警告訊息如下：

```
“Do not mount Vue to <html> or <body> - mount to normal elements instead.“
```

換成用獨立的 DOM 節點，如 `<div id="app"></div>`，就可以正常運作了。

<hr>

### Vue 2.0 元件實體的生命週期

<img alt="Vue 2.0 Lifecycle Diagram" title="Vue 2.0 Lifecycle Diagram" src="/static/img/vue2-lifecycle.png" height="400">
<span style="font-size: 12px;">圖片來源： [Vue 2.0 Guide: Instance Lifecycle Hooks](http://vuejs.org/guide/instance.html#Instance-Lifecycle-Hooks)</span>

基本上 Vue 2.0 實體生命週期中，大部分的階段都與 Vue 1.x 是一樣的，最大的不同在於 lifecycle hook 名稱的改變，以及在元件被掛載 `mounted` 之後，還新增了 `beforeUpdate` 以及 `updated` 這兩組偵測更新的 hook。

vue 1.x 的 `init` 變成 `beforeCreate` ， `beforeCompiled` 改為 `beforeMount`。 而原本的 `complied` 與 `ready` 則是統一收斂成 `mounted`。

另外需要注意的是，<span style="color: #f00">**若元件本身是透過 server-side rendering 的話，除了 `beforeCreate` 以及 `created` 以外的所有 hook 都不會被呼叫**</span>  [(參考資料)](https://vuejs.org/api/#Options-Lifecycle-Hooks)。

有關元件 V-DOM 的重新渲染與更新後面再提，其他部分則與 Vue 1.x 大同小異。

<hr>
### Vue 2.0 元件與模板的編譯 - Render Function

在大部分情況下，透過元件的 `template` 屬性，或是直接寫在 HTML 中就已經足夠操作你的元件了。
不過若是你想完全透過 JavaScript 來操作你的元件，那麼可以使用 render 這個 function 直接來寫底層的 virtual-DOM 來取代 `template` 屬性。
VueJS 2.0 的 virtual DOM 機制，是採用 [snabbdom](https://github.com/snabbdom/snabbdom "snabbdom") 這個 virtual DOM 的 library 來實作的。

可以使用 `createElement` 這個 function 來建立你的元件內容：
``` javascript
// @returns {VNode}
createElement(
  // {String | Object | Function}
  // An HTML tag name, component options, or function
  // returning one of these. Required.
  'div',
  // {Object}
  // A data object corresponding to the attributes
  // you would use in a template. Optional.
  {
    // (see details in the next section below)
  },
  // {String | Array}
  // Children VNodes. Optional.
  [
    createElement('h1', 'hello world')
    createElement(MyComponent, {
      props: {
        someProp: 'foo'
      }
    }),
    'bar'
  ]
)
```

官方也提供了一個完整的 render function 範例：
``` javascript
var getChildrenTextContent = function (children) {
  return children.map(function (node) {
    return node.children
      ? getChildrenTextContent(node.children)
      : node.text
  }).join('')
}
Vue.component('anchored-heading', {
  render: function (createElement) {
    // create kebabCase id
    var headingId = getChildrenTextContent(this.$slots.default)
      .toLowerCase()
      .replace(/\W+/g, '-')
      .replace(/(^\-|\-$)/g, '')

    return createElement(
      'h' + this.level,
      [
        createElement('a', {
          attrs: {
            name: headingId,
            href: '#' + headingId
          }
        }, this.$slots.default)
      ]
    )
  },
  props: {
    level: {
      type: Number,
      required: true
    }
  }
})
```

當然，你可能跟我一樣覺得一層又一層的 `createElement` 看了總是讓人厭煩，你也可以透過這個 Plugin: [babel-plugin-transform-vue-jsx](https://github.com/vuejs/babel-plugin-transform-vue-jsx)，來做 JSX 語法的轉換，如果你曾是 react 應用程式的開發者，應該對 JSX 語法不陌生。 寫起來會像這樣：

``` javascript
import AnchoredHeading from './AnchoredHeading.vue'

new Vue({
  el: '#demo',
  render (h) {
    return (
      <AnchoredHeading level={1}>
        <span>Hello</span> world!
      </AnchoredHeading>
    )
  }
})
```
在預設情況中，VueJS 2.0 會將 `template` 內的 HTML 透過 parse 轉換成 AST，再自動轉換優化成 render function 去建立 virtual DOM。 在建立 virtual DOM 之後，透過 observe 機制與資料進行綁定，再 compile 成實體的 DOM 並渲染至網頁上：

<img src="/static/img/vue2-rendering-flow.png">
<span style="font-size: 12px;">參考資料與圖片來源： [Next Vue.js 2.0 by kazupon](https://speakerdeck.com/kazupon/next-vue-dot-js-2-dot-0)</span>


前面說過，VueJS 2.0 會將 `template` 內的 HTML 自動編譯成 render function，下面這是官方文件以 `Vue.compile` 提供的 demo：

``` html
<!-- template -->
<div>
  <h1>I'm a template!</h1>
  <p v-if="message">
    {{ message }}
  </p>
  <p v-else>
    No message.
  </p>
</div>
```

``` javascript
// render:
function anonymous() {
  with(this){return _h('div',[_m(0),(message)?_h('p',[_s(message)]):_h('p',["No message."])])}
}
```

``` javascript
// staticRenderFns:
_m(0): function anonymous() {
  with(this){return _h('h1',["I'm a template!"])}
}
```


<hr>
### Vue 2.0 元件的追蹤變化

最後，我們來看看元件內狀態的追蹤變化。有寫過 VueJS 1.x 的朋友應該知道，元件實體內有個 option 叫 `data`，
這個 `data` 物件就是用來存放元件內狀態/資料的地方。

與 Vue 1.x 相同的地方是，`data` 物件透過 `Object.defineProperty()` 來為元件內各屬性設定 「getter」與「setter」。
就在 data 屬性被存取修改時，會透過 getter/setter 來通知物件內屬性的變化，當先前設定好的 setter 被呼叫的時候，會去觸發 watcher 重新計算，也就會導致 DOM 的更新與重新渲染。


與 Vue 1.x 不同的是，Vue 1.x 是透過 directive 來重新渲染 DOM 內容：

<img src="/static/img/vue1-watcher.png">

而 Vue 2.0 在通知 watcher 更新時，會去呼叫前面介紹的 「render function」與更新後的 data 去做更新後再次渲染，概念與 1.x 大致相同。
但更新 DOM 的手法不同，減少了不必要的比對，也因此大幅度提升了效能。

<img src="/static/img/vue2-watcher.png">
