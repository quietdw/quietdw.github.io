---
title: Vue 基础知识
date: 2019-04-11 00:00:00
updated: 2019-04-11 00:00:00
tags:
  - Vue
---

梳理一下 Vue 的基础知识。

<!-- more -->

# 生命周期

1. created：Vue 实例创建完成，还未挂载
2. mounted：数据刚刚挂载到 Dom 上
3. beforedestroy：一般用于处理事件解绑，定时器清除

# 属性与计算属性

1. 访问 Vue 实例中的属性，可使用类似`vm.$el`的方式访问，访问 data 中的属性，直接使用`vm.xxx`的形式访问
2. 文本插值`{{}}`，里面能进行简单的运算，不支持多行语句，复杂的数据使用计算属性，文本插值里可使用过滤器，`{{data | filterFunc}}`
3. 计算属性有缓存，其变更依赖其依赖属性的变更，计算属性可以计算多个 Vue 实例的数据
4. 计算属性默认用的是 get 函数
5. methods 里的方法，没有缓存
6. class 的绑定，能同时使用对象和数组绑定，`:class="[{xxx:true},yyy]"`

# 内置指令

1. v-cloak：解决初始化慢导致页面闪动的问题，一般和`display:none`一起使用
2. v-for：有一点，如果是遍历对象的多个属性，则用`value in obj`，如还要遍历 key 和 value 的值，则`(value,key,index) in obj` ,里面的顺序不能改变
3. 数组的 push，pop，unshift，shift，splice，sort 和 reverse 等操作会改变数组，引发页面重新渲染，改变数组长度或者指定项不会。使用 Vue.set(items,index,newval) 和 items.splice(index,1,newval)，改变数组也会引发渲染
4. 方法有参数，却不加括号，默认传的是原生 JavaScript 的事件对象
5. 单个单选框，radio， 使用 v-model 不生效
   6 checkbox：最好使用数组当做绑定值，如果不是，则会被转换成布尔值，使得 checkbox 全选或者全不选
6. select：只能把 v-model 放在 select
7. 如果想 v-model 动态绑定值，使用 v-bind 绑定 value，v-model 的值是 value 的值

> 引用

- [Vue](https://cn.vuejs.org/v2/guide/)
