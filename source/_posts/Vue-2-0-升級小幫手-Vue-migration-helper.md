---
title: 'VueJS 2.0 升級小幫手: Vue migration helper'
date: 2016-09-30 22:38:01
comments: true
categories: [vue.js]
tags: [vue.js]
---

Vue JS 自今年四月 (2016/04) 發佈 Vue 2.0 preview 版本至今也有五個月了，在新舊版本的交替之中，開發者最關心的一定是「**我的專案能不能升到 Vue 2.0**」、「**升上去會不會爆**」、「**專案該用那個版本來開發**」，<del>「聽說隔壁那個 ng 升級幾乎等於砍掉重練」</del> 之類的問題。

不過幸好，Vue 1.0 與 Vue 2.0 有 90% 的 API 是相同的，過去在 Vue 1.x 的核心概念到 Vue 2.0 一樣可以沿用，而且又多了些新特性。

這裡有一份官方的[升級建議](http://rc.vuejs.org/guide/migration.html "Migration from Vue 1.x")，有點長，如果沒有耐心讀的話，沒關係，這裡介紹你好物: <br> **<span style="color: #f00;">Vue migration helper</span>**。

傳說中的升級小幫手 Vue migration helper: https://github.com/vuejs/vue-migration-helper

使用方式很簡單，打開 terminal 透過 npm 安裝後，在你的專案目錄下執行 vue-migration-helper，小幫手就會幫你掃描整份專案，然後給你修改的建議了。

完整的安裝與使用方式：
```
# install
$ npm install --global git://github.com/vuejs/vue-migration-helper.git

# navigate to a Vue 1.x project directory
$ cd path/to/my-vue-project

# scan all files in the current directory
$ vue-migration-helper
```

拿之前的某份範例試試，就像這樣：

<del>花惹發</del>，滿坑滿谷的升級建議
![migration](/static/img/vue-migration-demo.png)

依照 migration helper 給的建議一一修正後，再次執行專案，檢查一下 console，是否有噴錯，如果沒有錯誤，那麼專案的升級也就差不多了。

不過要注意，目前 Vue migration helper 還在 beta 階段，如果未來要再更新至新版的話，直接再次執行 `npm install --global git://github.com/vuejs/vue-migration-helper.git` 重新安裝一次就好。

最後，鄉親啊，如果你正要開啟新專案，建議現在就可以直接從 Vue 2.0 進入喔！
