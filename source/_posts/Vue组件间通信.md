---
title: Vue组件间通信
date: 2019-02-24 00:00:00
updated: 2019-02-24 00:00:00
tags:
  - Vue
  - 通信
---

列举了 4 种组件间通信的形式

<!-- more -->

# 父子通信

## 利用 props

使用 props，父组件可以使用 props 向子组件传递数据。子组件在 props 上注册该属性，就可用像用 data 一样使用 props。

## 利用$children

注意 $children 并不保证顺序，也不是响应式的。如果你发现自己正在尝试使用 $children 来进行数据绑定，考虑使用一个数组配合 v-for 来生成子组件，并且使用 Array 作为真正的来源。

# 子组件向父组件通信

## 使用 Vue 事件

    发布订阅模式

    1. 父组件自定义事件并监听
    2. 自组件通过点击等事件触发该事件（$emit）
    3. 父组件监听到这个事件，并获得传来的数据

## 使用$parent

    父实例，如果当前实例有的话。

# 爷孙通信

    依旧是使用发布订阅模式。通过子父，父爷层层向上通知事件。

# 非父子组件、兄弟组件之间的数据传递

使用 eventBus，利用 Vue 实例提供的 $emit 和 $on 方法

```html
<div id="app">
  <component-a></component-a>
  <component-b></component-b>
</div>

<script>
  let eventHub = new Vue()
  Vue.prototype.$eventHub = eventHub
  //compontent 是 Vue 的一个实例 this.__proto===Vue.prototype
  Vue.component('component-a', {
    template: `
        <div>a<button @click=notify>点击</button></div>
    `,
    methods: {
      notify() {
        this.$eventHub.$emit('xxx', 'hi')
      }
    }
  })

  Vue.component('component-b', {
    template: `
        <div>b<div ref=output></div></div>
    `,
    created() {
      this.$eventHub.$on('xxx', data => {
        this.$refs.output.textContent = data
      })
    }
  })
  let app = new Vue({
    el: '#app'
  })
</script>
```

# 更复杂的单页应用数据管理

[请使用 Vuex](https://jiangnana.fun/2019/04/02/Vuex-%E5%B0%8F%E8%AE%B0/)

> 引用

- [渐进式
  JavaScript 框架](https://cn.vuejs.org/)
