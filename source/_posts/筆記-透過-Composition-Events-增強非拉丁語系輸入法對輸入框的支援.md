---
layout: post
title: '[筆記] 透過 Composition Events 增強非拉丁語系輸入法對輸入框的支援'
date: 2016-10-11 12:57:30
comments: true
categories: [HTML]
tags: [html, javascript, events]
---

最近在爬 Vue 的原始碼的時候，意外發現兩個沒看過的 event：`compositionstart` 與 `compositionend`。 查了一下 MDN 才發現這些叫做「[Composition Events](https://developer.mozilla.org/en-US/docs/Web/API/CompositionEvent "Composition Events")」的 event 是從 DOM Level 3 之後才新增的。

介紹 Composition Events 之前先來談談 DOM API 過去對輸入框偵測變化的幾個方式。

一般來說，常見的表單輸入框如: `<input type="text">` 如果要動態監聽輸入框的文字變化時，
大多會透過監聽 `keydown`、`keypress`、`keyup` 等鍵盤事件來判斷 value 是否變動，但如果是透過複製貼上之類的操作，就無法透過鍵盤事件來判斷。

而即使是 `change` 事件則是要在使用者改變內容，且<span style="color: red;">焦點離開輸入框的前一刻才會被觸發</span>。
所以後來有了 `input` 事件， input 事件會在<span style="color: red;">輸入框的內容被改變時即時觸發</span>，確實也解決了過去在 onChange 以及鍵盤相關事件帶來的不少問題。

新的問題來了！

通常像這樣的搜尋框，我們會用類似 autocomplete 的方式給使用者搜尋建議 (以 google 為例)：
<img src="/static/img/google-autocompleted.png">

如上圖，在輸入中文的時候，通常會需要透過注音之類的輸入法來做拼字，但是在大部分的情況下，自動給「注音符號」或是「拼音文字」搜尋建議是沒有太大意義的。

回到主題。這個時候就需要透過 **Composition Events** 來為輸入框做增強。
透過 Composition Events 我們可以觀察使用者在輸入框內開啟輸入法 (Input Method Editor, IME) 時，組字或選字的狀態。


Composition Events 提供三個事件給開發者監聽：分別是 `compositionstart` 、 `compositionend` ，以及 `compositionupdate`。

* `compositionstart`: 輸入框內開啟輸入法，且正在拼字時觸發。
* `compositionupdate`:輸入框內開啟輸入法，且正在拼字或選字時更改了內容時觸發。
* `compositionend`: 輸入框內開啟輸入法，拼字或選字完成，正要送出至輸入框時觸發。

<p></p>
執行的時候像這樣：
<img src="/static/img/composition-demo.png">

可以看到，如果要確認使用者輸入完成並送出文字時，可以透過 `compositionend` 來做最後確認。<br>
附上 JSBin Demo: http://jsbin.com/mofepimiqo/1/edit?js,console,output

最後則是大家都很關心的瀏覽器支援度。以目前來說，mobile 平台上可能還不太 ok，但是 Desktop 平台上表現看起來相當不錯，可以大膽地使用囉。
<img src="/static/img/compositionEvent-compatibility.png">

<p class="no-space" style="margin-bottom:0">參考資料：</p>
* [用 Composition Event 改進 CodeMirror 對輸入法的支援](http://blog.zhusee.in/post/146553/enhance-ime-support-of-codemirror-with-composition-events)
* [MDN: Composition Events](https://developer.mozilla.org/en-US/docs/Web/API/CompositionEvent "Composition Events")