---
layout: post
title: '[VueJS] 在 v-for 列表中透過 filter 完成搜尋與分頁的功能'
date: 2016-05-30 14:30
comments: true
categories: [vue.js]
tags: [vue.js]
---
最近 [Vue.js](https://vuejs.org/) 正夯，所以手上幾個東西打算用這個來改寫，關於 Vue.js 的基本介紹可以參考小弟的投影片，這裡就不再贅述。

<iframe src="//www.slideshare.net/slideshow/embed_code/key/DLQDvLrRSNunsY" width="476" height="400" frameborder="0" marginwidth="0" marginheight="0" scrolling="no" style="border:1px solid #CCC; border-width:1px; margin-bottom:10px; max-width: 100%;" allowfullscreen> </iframe>

有用過 Vue.js 開發的朋友一定知道它提供的 filter 功能十分強大，在 `v-for` 列表中使用 `filterBy` 可以在一行內完成列表搜尋的功能：

``` html
  <div id="filter-by-example">
    <input v-model="n">
    <ul>
      <!-- 透過 input 欄位的 v-model n 與 user.name 做模糊比對 -->
      <li v-for="user in users | filterBy n in 'name'">
        {{ user.name }}
      </li>
    </ul>
  </div>
```

若是要限制顯示的筆數也能用 `limitBy` 做到，進而完成分頁的功能。

``` html
  <!-- 只顯示前 10 個元素 -->
  <div v-for="item in items | limitBy 10"></div>

  <!-- 顯示第 5 到 15 筆元素-->
  <div v-for="item in items | limitBy 10 5"></div>
```

這次遇到的問題是這樣的，如果我們想要同時完成「搜尋」與「分頁」的需求，光靠 `filterBy` 與 `limitBy` 就不是那麼容易做到，還好 Vue.js 提供了[自訂 filter](http://vuejs.org/guide/custom-filter.html) 的功能：先用 filterBy 過濾，再透過自訂 recordLength 記錄過濾後的資料數量，最後再用 limitBy 搭配頁籤切換頁面。
小心 filter 的順序，filter 會依序執行，然後再繼續下個 filter。


``` html
  <tr v-for=" r in rows
               | filterBy filter_name in 'name'
               | recordLength 'filteredRowCount'
               | limitBy countOfPage pageStart ">
      <td>......</td>
  </tr>
```

然後是自定的 filter `recordLength`：
result 代表傳入的資料， key 則是從 view 帶入的參數，這個範例是 `filteredRowCount`。

這裡透過 `vm.$set` 來將過濾後的數量指定至 vue 實體，以便可以直接在 Vue 實體使用。

``` javascript
  Vue.filter('recordLength', function (result, key) {
    this.$set(key, result.length);
    return result;
  });
```

最後在頁籤的部分，我們就可以簡單透過 `filter_name` 欄位是否空白來切換是否透過 `filteredRowCount` 計算總頁數:

``` javascript
  totalPage: function(){
    if( this.filter_name.trim() === '' ) {
      return Math.ceil(this.rows.length / this.countOfPage);
    }
    else{
      return Math.ceil(this.filteredRowCount / this.countOfPage);
    }
  }
```


完整的 [demo](http://jsbin.com/bimawidora/1/edit?html,js,output) 如下:

<a class="jsbin-embed" href="http://jsbin.com/bimawidora/embed?output">JS Bin on jsbin.com</a><script src="http://static.jsbin.com/js/embed.min.js?3.35.12"></script>
