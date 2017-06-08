---
layout: post
title: 如何在 Vue-CLI 建立的開發環境呼叫跨域遠端 RESTful APIs
date: 2017-06-07 18:14:00
comments: true
categories: [vue.js]
tags: [vue.js]
---

前幾天在 VueTW 活動結束後，有朋友來問：Vue 的開發環境能不能在 local 端建立 proxy 服務來解決跨域讀取後端的 API，
<del>隔壁棚的 Angular CLI 好像有這功能</del>，[Vue-CLI](https://github.com/vuejs/vue-cli) 是不是也能做到。

(使用 Angular 開發專案的朋友，這裏推薦參考 Will 保哥這篇文章：[如何在 Angular CLI 建立的 Angular 2 開發環境呼叫遠端 RESTful APIs](http://blog.miniasp.com/post/2017/02/05/Setup-proxy-to-backend-in-Angular-CLI.aspx))。 

使用 Vue 的朋友，別著急莫緊張，Vue-CLI 也有提供類似功能喔。

> 注意： 本文撰寫時，使用 Node v8.0.0 / NPM 5.0.0 與 yarn v0.24.6，Vue-CLI 為 2.8.2。 
> 實際執行情況可能因執行環境的版本會有所差異。

首先，第一步當然是先安裝 Vue-CLI: 
- NPM:  
``` shell
$ npm install -g vue-cli
```

- YARN: 
``` shell
$ yarn global add vue-cli
```

<hr>

安裝好了之後，接著我們透過 Vue-CLI 來建立新專案，樣板的部分我們選用 <span style="color: red;">webpack</span>，然後執行：
 
``` shell
 $ vue init webpack test
```

上面指令中的 `test` 是專案名稱，這裏請自行取名替換成你要的名稱。 為了節省時間，範例裡只安裝必要的套件，其他選項可依你的需要選用安裝。
<img src="/static/img/vue-cli.png">

接著切換至新增好的目錄後，安裝相關套件。
- NPM:
``` shell
$ cd test
$ npm i
$ npm run dev
```

- YARN:
``` shell
$ cd test
$ yarn
$ yarn dev
```

安裝完成並執行 dev-server 之後，應該會自動開啟瀏覽器 `http://localhost:8080/` 並看到畫面：
<img src="/static/img/hello-vue.png">

到目前為止已經建立起一個基本的 Vue 專案了。

<hr>

接著，我們以 [Data.Taipei](http://data.taipei/) 的 [Youbike臺北市公共自行車即時資訊](http://data.taipei/opendata/datalist/datasetMeta/preview?id=8ef1626a-892a-4218-8344-f7ac46e1aa48&rid=9c6a96d6-353c-41c0-84cc-d181988304f2) 作為本次的範例。
<img src="/static/img/data-taipei-Youbike.png">

VueJS 的核心並沒有提供 Ajax 這樣的功能，所以在發送遠端請求的時候，我們要先安裝 [axios](https://github.com/mzabriskie/axios) 這樣的套件來協助我們，當然你想要用 jQuery AJAX 或是 Fetch API 也是 ok 的。

安裝方式：
- NPM:
``` shell
$ npm install --save axios vue-axios
```

- YARN:
``` shell
$ yarn add axios vue-axios
```

安裝完畢後，我們打開 App.vue 檔，並加上 `import` 以及 `Vue.use` 來載入外部的套件：

``` javascript
import Vue from 'vue'
import axios from 'axios'
import VueAxios from 'vue-axios'

Vue.use(VueAxios, axios)
```

接著，我們在 Vue 實體的 `create` hook 裡面來送出 GET 請求試試：

``` javascript
  created(){
    Vue.axios.get('http://data.taipei/youbike').then((response) => {
      console.log(response.data)
    })
  },
```

像這樣：
<img src="/static/img/vue-axios-code.png">

存檔後再次開啟 dev-server，毫無意外地，你應該會看到像這樣的錯誤：
<img src="/static/img/ajax-error.png">


因為遠端的 API 並沒有開啟 CORS 協定，所以我們無法跨域存取這個資源。 不過還好，Vue-CLI 的 dev server 整合了 [http-proxy-middleware](https://github.com/chimurai/http-proxy-middleware)。 這個工具替我們在 local 端建立一個代理層，讓我們在開發時期就可以很方便的呼叫遠端的 HTTP API。

套件的部分，Vue-CLI 都幫我們準備好了，接著打開專案下 `config/index.js` 這個檔案，然後編輯 ` proxyTable: {} ` 的部分。
<img src="/static/img/proxyTable.png">

修改 `config/index.js` 如下：
``` javascript
  proxyTable: {
    '/data': {                        // 自訂 local 端的位置
      target: 'http://data.taipei/',  // 遠端 URL Domain
      changeOrigin: true,
      pathRewrite: {
        '^/data': ''
      }
    }
  },
```

經過這樣設定後，`http://data.taipei/` 網域內的資源，都會在 local 端以 `/data` 的形式被代理，也就是說，像 `http://data.taipei/youbike` 這樣的遠端資源，我們就可以在 local 端用 `/data/youbike` 來取得。

所以，再回到 `App.vue`，這裏將原本的 `http://data.taipei/youbike` 改成 `/data/youbike`，像這樣：

``` javascript
  created(){
    Vue.axios.get('/data/youbike').then((response) => {
      console.log(response.data)
    })
  },
```

接著，記得要重開 dev-server，執行 `$ npm run dev` 或 `$ yarn dev` 。

最後，在網頁讀取完成後，打開 console 就可以看到，遠端的 youbike 資訊已經可以正確取得囉。
<img src="/static/img/vue-axios-ok.png">


詳細的 http-proxy-middleware 設定可以參考這裡：
- https://github.com/chimurai/http-proxy-middleware#http-proxy-middleware-options
- https://vuejs-templates.github.io/webpack/proxy.html
