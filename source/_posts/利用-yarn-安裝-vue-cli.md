---
layout: post
title: 利用 Yarn 安裝 Vue-cli
date: 2016-10-13 11:49:07
comments: true
categories: [vue.js]
tags: [vue.js, yarn]
---

這兩天 JavaScript 圈子的最大新聞應該就是 Facebook 發布了一套新的 JavaScript 套件管理工具 Yarn，感覺來勢洶洶勢不可擋。
實際花了一點時間試用，表現的確也比過去 npm 好很多，尤其過去 `npm install` 速度過慢，套件相依衝突的問題，目前在 Yarn 還沒遇到。 即使是剛開始 `yarn run scripts` 無法帶入參數的問題，在發布的第二天也快速地更新 (v0.15.1) 解決了。 👍

<img src="/static/img/yarn-logo.png">

有關 Yarn 介紹的部分，已經有其他前輩分享了，推薦可以看看這篇 [取代 npm 的新利器 Yarn](https://medium.com/@jackypan1989/%E5%8F%96%E4%BB%A3-npm-%E7%9A%84%E6%96%B0%E5%88%A9%E5%99%A8-yarn-7d97f2f409b9#.pu0jrdcc4)。

既然 Yarn 號稱可以用來取代 npm，那麼身為 Vue 的愛好者，也想馬上來試試 [Vue-cli](https://github.com/vuejs/vue-cli) 這套 Vue 的樣板工具包是否也可以透過 yarn 來單獨執行。

<del>既然要取代 npm，那麼第一件事情就是把 npm 整包幹掉</del>，關於刪掉 npm modules 的過程我是參考 stackoverflow 的這篇:
[Command to remove all npm modules globally?](http://stackoverflow.com/questions/9283472/command-to-remove-all-npm-modules-globally)，但實際上 Yarn 與 npm 是可以和平共存的，不一定要把 npm 幹掉才能跑 Yarn ，這裡只是想簡單做個試驗，啾咪 ^.<

然後安裝 Yarn。 執行 `curl -o- -L https://yarnpkg.com/install.sh | bash` 即可。

<hr>

### 第一步：安裝 vue-cli

很簡單，把原本的  `npm install -g vue-cli` 改成 `yarn global add vue-cli` 就可以了。執行的時候像這樣：
<img src="/static/img/yarn-vue-1.png">

而安裝完畢後，執行 `yarn vue` 應該可以看到這樣的畫面。
<img src="/static/img/yarn-vue-2.png">

每執行完一個指令還會告訴你它跑了多久喔<del>，非常囂張 (誤)</del>。


### 第二步：初始化 Vue 專案

接著，就來初始化我們的 Vue 專案。 Vue-cli 這個 scaffolding 工具官方目前提供了幾種 template 讓開發者自行選擇，
像是大家常見的 webpack、browserify 都有。當然也可以挑選什麼都沒有的 simple: 就是最基本的 Vue 專案這樣。
如果想更詳細了解各種 template 細節的話，可以到 vue-templates 的 [Repo](https://github.com/vuejs-templates) 去看。

這裡我就選擇拿「[webpack-simple](https://github.com/vuejs-templates/webpack-simple)」當範例。

執行 `yarn vue init webpack-simple my-project`
<img src="/static/img/yarn-vue-3.png">

因為 VueJS 目前已經發佈至 2.0 版了，所以你會看到 Vue-cli 很貼心地提醒你現在安裝的是<span style="color: red;"> 2.0 的版本</span>。
若你想安裝的是 1.x 版本的話，可以改成 <br> `yarn vue init webpack-simple#1.0 my-project` 即可。

然後，Vue-cli 會幫我們建立一個新目錄 `my-project`。

透過 `cd my-project` 切換到新專案目錄後，我們將原本的 `npm install` 改成直接執行 `yarn` 即可。
<img src="/static/img/yarn-vue-4.png">


### 第三步：啟動並執行 vue 專案

最後，直接執行 `yarn run dev` 應該會看到像這樣的畫面。
<img src="/static/img/yarn-vue-5.png">

打開你的瀏覽器連到 `http://localhost:8080/` 看到 `Hello Vue!` 的字樣就代表成功執行囉！
<img src="/static/img/yarn-vue-6.png">

