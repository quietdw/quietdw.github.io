---
title: 初识jQuery
date: 2018-12-11 00:00:00
updated: 2018-12-11 00:00:00
tags:
  - jQuery
---

jQuery 的思路：

1. 接收一个选择器
2. 将选择的元素封装成一个伪数组
3. 对伪数组封装了几个 API
4. 返回这个伪数组

<!-- more -->

版本一

```javascript
<div>内容</div>
<div>内容</div>
<div>内容</div>
<div>内容</div>
<div>内容</div>

<script>
window.jQuery = function(node){
  var nodes = document.querySelectorAll(node)//nodes是个伪数组，用户以为操作的是node，其实是伪数组nodes

  return {//返回addClass、setText两个方法
    addClass: function(cls){
      nodes.forEach(function(value,key){
       nodes[key].classList.add(cls)
      })//nodes和函数形成了一个闭包
    },
    setText: function(text){
    nodes.forEach(function(value,key){
       nodes[key].textContent = text
      })
    }
  }
}
window.$ = jQuery //全局可以使用$符号

var $div = $('div')//$div就有addClass、setText两个方法
$div.addClass('red') // 可将所有 div 的 class 添加一个 red
$div.setText('hi') // 可将所有 div 的 textContent 变为 hi

</script>
```

版本二

将方法绑定在 nodes 上，返回一个 nodes

```javascript
window.jQuery = function (node) {
  var nodes = document.querySelectorAll(node)

  nodes.addClass = function (cls) {
    nodes.forEach(function (value, key) {
      nodes[key].classList.add(cls)
    })
  }

  nodes.setText = function (text) {
    nodes.forEach(function (value, key) {
      nodes[key].textContent = text
    })
  }

  return nodes
}
```

> 引用

[JS 中的闭包是什么？](https://zhuanlan.zhihu.com/p/22486908)
