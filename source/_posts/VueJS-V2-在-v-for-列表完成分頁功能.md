---
title: '[VueJS-V2] 在 v-for 列表完成分頁功能 (從 v1 至 v2)'
date: 2017-05-30 17:00:00
comments: true
categories: [vue.js]
tags: [vue.js]
---

Vue 2 都發行半年多了，直到最近有網友留言這才想起一直沒更新裡面的內容，囧。

延續上回 [[VueJS] 在 v-for 列表中透過 filter 完成搜尋與分頁的功能](https://kuro.tw/posts/2016/05/30/vuejs-in-v-for-through-the-filter-in-the-list-complete-search-and-page-functions/) 這篇的說明，
這次我們來看看，自從 <span style="color: red">VueJS 更新到 V2 拿掉了內建的 filterBy、limitBy 等好用的 filter 後</span>，要如何修改才能做到分頁的功能。

首先看到原本 V1 的範例，是這樣的，這裡透過 `limitBy countOfPage pageStart` 來完成分頁，如下：

``` html
  <tr v-for="r in rows 
    | filterBy filter_name in 'name' 
    | recordLength 'filteredRowCount' 
    | limitBy countOfPage pageStart ">
    <td>{{ (currPage-1) * countOfPage + $index + 1 }}</td>
    <td>{{ r.name }}</td>
    <td>{{ r.age }}</td>
    <td>{{ r.gender }}</td>
  </tr>
```

因為從 V2 開始，`filterBy` 、 `limitBy` 、 `orderBy` 都沒有了，所以我們直接移除掉：

``` html
  <tr v-for="r in rows">
    <td>{{ (currPage-1) * countOfPage + $index + 1 }}</td>
    <td>{{ r.name }}</td>
    <td>{{ r.age }}</td>
    <td>{{ r.gender }}</td>
  </tr>
```

這時應該會出現錯誤，因為 `$index` 的寫法也更新了，所以改一下 `v-for` 的內容：

``` html
  <tr v-for="(r, index) in rows">
    <td>{{ (currPage-1) * countOfPage + index + 1 }}</td>
    <td>{{ r.name }}</td>
    <td>{{ r.age }}</td>
    <td>{{ r.gender }}</td>
  </tr>
```

到目前為止，至少可以看到程式正常運作，但相對地<span style="color: red">分頁與搜尋的功能也沒了</span>，所以接下來先來處理**搜尋**的部份。

<hr>

首先，在 `computed` 屬性，我們新增一個叫做 `filteredRows` 的屬性，用來處理與 `filter_name` 比對後的結果：

``` javascript
  filteredRows: function(){
    // 因為 JavaScript 的 filter 有分大小寫，
    // 所以這裡將 filter_name 與 rows[n].name 通通轉小寫方便比對。
    var filter_name = this.filter_name.toLowerCase();
    
    // 如果 filter_name 有內容，回傳過濾後的資料，否則將原本的 rows 回傳。
    return ( this.filter_name.trim() !== '' ) ? 
        this.rows.filter(function(d){ return d.name.toLowerCase().indexOf(filter_name) > -1; }) : 
        this.rows;
  },
```

然後將 html 內的 `rows` 換成 `filteredRows`

``` html
  <tr v-for="(r, index) in filteredRows">
    <td>{{ (currPage-1) * countOfPage + index + 1 }}</td>
    <td>{{ r.name }}</td>
    <td>{{ r.age }}</td>
    <td>{{ r.gender }}</td>
  </tr>
```

name 搜尋的部份到這裡就算完成了，接著回頭來處理**分頁**。

<hr>

處理分頁之前，先看到底下的分頁按鈕， `n in range` 在 V2 的寫法也有不同，原本的索引會由 0 開始，但從 V2 開始會由 1 開始，所以需要把原本的：


``` html
<div class="pagination">
    <ul>
    <li v-bind:class="{'disabled': (currPage === 1)}" 
        @click.prevent="setPage(currPage-1)"><a href="#">Prev</a></li>
    <li v-for="n in totalPage"
        v-bind:class="{'active': (currPage === (n+1))}" 
        @click.prevent="setPage(n+1)"><a href="#">{{n+1}}</a></li>
    <li v-bind:class="{'disabled': (currPage === totalPage)}" 
        @click.prevent="setPage(currPage+1)"><a href="#">Next</a></li>
    </ul>
</div>
```


改寫成：

``` html
<div class="pagination">
    <ul>
    <li v-bind:class="{'disabled': (currPage === 1)}" 
        @click.prevent="setPage(currPage-1)"><a href="#">Prev</a></li>
    <li v-for="n in totalPage"
        v-bind:class="{'active': (currPage === (n))}" 
        @click.prevent="setPage(n)"><a href="#">{{n}}</a></li>
    <li v-bind:class="{'disabled': (currPage === totalPage)}" 
        @click.prevent="setPage(currPage+1)"><a href="#">Next</a></li>
    </ul>
</div>
```

顯示就會是正常的從 1 開始了。


再來處理顯示筆數時，需要的會有 `pageStart` 、 `countOfPage` 與 `totalPage` 這三個部分，其中 `totalPage` 會跟先前提到的 **搜尋** 比較有關係，因為總頁數不對，算出來的分頁也會有問題。

原本的 `totalPage` 長這樣：

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

因為資料在先前都交給 `filteredRows` 處理了，所以可以改寫成
``` javascript
  totalPage: function(){
    return Math.ceil(this.filteredRows.length / this.countOfPage);
  }
```

實際執行會發現最底下的分頁按鈕已經會隨著 Filter 的結果筆數而有所不同：

<img src="/static/img/v2-filter-1.png">
<hr>
<img src="/static/img/v2-filter-2.png">

最後，再回到處理<span style="color: red">每頁顯示資料量</span>的部分。

在沒有了 `limitBy` 之後，顯示筆數的部份我們可以透過 JavaScript 的 `slice` 來處理，當然也可以寫在 `computed` 屬性。
但這個範例只是單純在顯示作切換，所以可以直接寫在 view 上，像這樣將 `filteredRows` 改寫成 `filteredRows.slice(pageStart, pageStart + countOfPage)`：

對 `slice` 不熟的朋友可參考： MDN: [Array.prototype.slice()](https://developer.mozilla.org/zh-TW/docs/Web/JavaScript/Reference/Global_Objects/Array/slice)

``` html
  <tr v-for="(r, index) in filteredRows.slice(pageStart, pageStart + countOfPage)">
    <td>{{ (currPage-1) * countOfPage + index + 1 }}</td>
    <td>{{ r.name }}</td>
    <td>{{ r.age }}</td>
    <td>{{ r.gender }}</td>
  </tr>
```

就完成了從 V1 更新到 V2 的過程了。

完整更新的範例可在此參考：<br>
<a class="jsbin-embed" href="http://jsbin.com/kusafiqaka/embed?html,js,output">JS Bin on jsbin.com</a>
