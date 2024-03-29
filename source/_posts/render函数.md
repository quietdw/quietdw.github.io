---
title: render函数
date: 2019-04-16 00:00:00
updated: 2019-04-16 00:00:00
tags:
  - Vue
  - render
---

字符串模板的代替方案，允许你发挥 JavaScript 最大的编程能力。该渲染函数接收一个 createElement 方法作为第一个参数用来创建 VNode。

<!-- more -->

# 基本用法

template

```html
<template id="hdom">
  <div>
    <h1 v-if="level==1">
      <slot></slot>
    </h1>
    <h2 v-if="level==2">
      <slot></slot>
    </h2>
    <h3 v-if="level==3">
      <slot></slot>
    </h3>
  </div>
</template>
```

使用 vue 组件定义

```javascript
Vue.component('child', {
  props: ['level'],
  template: '#hdom'
})
```

使用 render 函数进行定义组件

```javascript
Vue.component('child', {
  render: function (createElement) {
    return createElement('h' + this.level, this.$slots.default)
  },
  props: ['level']
})
```

# render 函数的参数

1. 第一个参数必须是 createElement,该参数的类型是 function

# createElement 函数的参数

## 第一个参数（必选）

有三种数据类型

1. String 类型：HTML 标签，例如`h1`
2. Object 类型：含有数据的对象，如`{template: "<h1></h1>"}`
3. Function 类型：返回一个数据对象，例如：

```javascript
Vue.component('child', {
  render(createElement) {
    let dom = () => {
      return
      {
        template: '<h1>13</h1>'
      }
    }
    return createElement(dom(), this.$slots.default)
  }
})
```

## 第二个参数（可选）

数据对象，只能是 Object

常用的选项有：

1. class

```javascript
class:{
  xxx:true
}
```

2. style

```javascript
style: {
  color: 'red'
}
```

3. attrs （正常的 html 特性）

```
attrs:{
  id:'xxx'
}
```

4. domprops （原生 Dom 特性）

```
domProps:{
  innerHTML:'<strong>我是加粗的字</strong>'
}
```

5. on 添加事件监听

## 第三个参数（可选）

可以是 String 或 Array 类型，代表子节点

1. String 类型：
2. Array 类型：

```javascript
render(createElement) {
				let xxx = () => {
					return {
						template: "<h1></h1>"
					}
				}
				return createElement(
					'div', {

						attrs: {
							id: 'xxx'
						},
						style: {
							color: 'red'
						},
						class: {
							x1xxx: true
						}
					},[
						createElement('div','div内容1'),
						createElement('div','div内容2')
					]
				);
			}
```

# slot 在 render 中应用

```Javascript
<div id="app">
  <child>
    <div>111</div>
    <div>222</div>
    <div slot='a'>333</div>
    <div slot='b'>444</div>
  </child>
</div>
<script>
  Vue.component('child', {
    render(createElement) {
      let header = this.$slots.a // 返回含有 VNODE 的数组
      let main = this.$slots.default
      let footer = this.$slots.b
      return createElement(
        'div',[
          createElement('header',header),// 虚拟节点，因为 vue 的双向绑定，每次更新了虚拟节点的数据，页面就会更新变更的那一部分
          createElement('main',main),
          createElement('footer',footer)
        ]
      );
    }
  })
  let vm = new Vue({
    el: '#app',
    data: {}
  })
</script>
```

# 作用域插槽在 render 中的应用

```javascript
<div id="app">
  <child>
    <template scope = 'my'>
      {{my.xxx}}
      {{my.yyy}}
    </template>
  </child>
</div>
<script>
  Vue.component('child', {
    render(createElement) {
      return createElement(
        'div',this.$scopedSlots.default({
          xxx:'111',
          yyy:'222'
        })
      );
    }
  })
  let vm = new Vue({
    el: '#app',
    data: {}
  })
</script>
```

# 函数化组件

设置 `functional:true` ,则该组件无状态，无实例。

```
<div id="app">
  <child value="hi">

  </child>
</div>
<script>
  Vue.component('child', {
    functional:true,
    render(createElement,context) {

      return createElement(
        'div',[
          createElement('button',{
            on:{
              click:function(){
                console.log(context.parent) // 父组件
                console.log(context.props.value) // hi
                console.log(context.children) // 子组件
                console.log(this) // Window
              }
            }
          },'点击')
        ]
      );
    }
  })
  let vm = new Vue({
    el: '#app',
    data: {}
  })
</script>
```

> 引用

- [渲染函数 & JSX](https://cn.vuejs.org/v2/guide/render-function.html)
