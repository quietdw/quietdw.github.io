---
title: 插槽slot
date: 2019-04-16 00:00:00
updated: 2019-04-16 00:00:00
tags:
  - Vue
  - slot
---

slot 将父组件的内容传给子组件，弥补视图缺失

<!-- more -->

# 插槽的用法

```html
<div id="app">
  <child>
    <p>父组件的内容</p>
  </child>
</div>
<script src="https://cdn.jsdelivr.net/npm/vue"></script>
<script type="text/javascript">
  Vue.component('child', {
    template: `
    <div>
      <slot>如果父组件没有插入内容，就默认出现这个</slot>
    </div>
    `
  })

  let vm = new Vue({
    el: '#app',
    data: {}
  })
</script>
```

# 具名插槽

倘若我们需要多个插槽，我们可以给插槽一个 name 属性，一个不带 name 的 `<slot>` 出口会带有隐含的名字“default”。

```html
<div class="container">
  <header>
    <slot name="header"></slot>
  </header>
  <main>
    <slot></slot>
  </main>
  <footer>
    <slot name="footer"></slot>
  </footer>
</div>
```

在向具名插槽提供内容的时候，我们可以在一个 <template> 元素上使用 v-slot 指令，并以 v-slot 的参数的形式提供其名称：

```html
<base-layout>
  <template v-slot:header>
    <h1>Here might be a page title</h1>
  </template>

  <p>A paragraph for the main content.</p>
  <p>And another one.</p>

  <template v-slot:footer>
    <p>Here's some contact info</p>
  </template>
</base-layout>
```

# 作用域插槽

让插槽内容能够访问子组件中才有的数据。

```html
<div id="app">
  <child>
    <template scope="xxx"> {{xxx.text}} </template>
  </child>
</div>
<script src="https://cdn.jsdelivr.net/npm/vue"></script>
<script type="text/javascript">
  Vue.component('child', {
    render(createElement) {
      return createElement(
        'div',
        this.$scopedSlots.default({
          text: 'xxx'
        })
      )
    }
  })

  let vm = new Vue({
    el: '#app',
    data: {}
  })
</script>
```

# 访问 slot 里的内容

通过 this.$slots 访问静态插槽的内容，得到的是一个 VNodes 数组

# 动态组件

```html
<div id="dynamic-component-demo" class="demo">
  <button
    v-for="tab in tabs"
    v-bind:key="tab"
    v-bind:class="['tab-button', { active: currentTab === tab }]"
    v-on:click="currentTab = tab"
  >
    {{ tab }}
  </button>

  <component v-bind:is="currentTabComponent" class="tab"></component>
</div>
<script>
  Vue.component('tab-home', {
    template: '<div>Home component</div>'
  })
  Vue.component('tab-posts', {
    template: '<div>Posts component</div>'
  })
  Vue.component('tab-archive', {
    template: '<div>Archive component</div>'
  })

  new Vue({
    el: '#dynamic-component-demo',
    data: {
      currentTab: 'Home',
      tabs: ['Home', 'Posts', 'Archive']
    },
    computed: {
      currentTabComponent: function () {
        return 'tab-' + this.currentTab.toLowerCase()
      }
    }
  })
</script>
```
