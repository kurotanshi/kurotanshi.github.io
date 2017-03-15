---
title: VueJS 各種資料綁定 (data binding) 的方式
date: 2016-10-05 09:04:34
comments: true
categories: [vue.js]
tags: [vue.js]
---

大部分資料是從這裡來的: [Vue 1.0.0 binding syntax cheatsheet](https://github.com/vuejs/vue/wiki/1.0.0-binding-syntax-cheatsheet)，再加上對 Vue 2.0 補充的部分。

``` html
<!-- normal directive -->
<div v-if="ok"></div>

<!-- directive with argument -->
<button v-on:click="onClick"></button>

<!-- v-on with argument + key modifier -->
<input v-on:keyup.enter="handleEnter">

<!-- literal modifier: pass literal string to directive for Vue 1.x, -->
<!-- but deprecated in 2.0 -->
<a v-link.literal="/a/b/c"></a>

<!-- binding an attribute with v-bind -->
<img v-bind:src="imgSrc">
<a v-bind:href="baseURL + '/path'">

<!-- shorthand: colon for v-bind -->
<img :src="imgSrc">
<a :href="baseURL + '/path'">

<!-- shorthand: @ for v-on -->
<button @click="onClick"></button>

<!-- key modifier -->
<input @keyup.enter="handleEnter">

<!-- stop/prevent modifier -->
<button @click.stop="onClick"></button>
<form @submit.prevent></form>

<!-- props for Vue 1.x -->
<!-- .once and .sync are deprecated in 2.0 -->
<my-comp
  prop="literal string"
  v-bind:prop="defaultOneWay"
  v-bind:prop.sync="twoWay"
  v-bind:prop.once="oneTime">
</my-comp>

<!-- props with one time binding for Vue 2.0 -->
<my-comp
  prop="literal string"
  v-bind:prop="defaultOneWay"
  v-once>
</my-comp>

<!-- component with props and custom events, in shorthand -->
<item-list
  :items="items"
  :open.sync="isListOpen"
  @ready="onItemsReady"
  @update="onItemsUpdate">
</item-list>

<!-- v-el and v-ref now just use the argument -->

<!-- registers vm.$refs.child for Vue 1.x -->
<comp v-ref:child></comp>

<!-- registers vm.$refs.child for Vue 2.0 -->
<comp ref:child></comp>

<!-- registers vm.$els.node for Vue 1.x, but deprecated in 2.0 -->
<div v-el:node></div>
```