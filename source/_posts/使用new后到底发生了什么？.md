---
title: 使用new后到底发生了什么？
tags:
  - new
  - JavaScript
---

当代码 new Foo(...) 执行时，会发生以下事情：

1. 首先新建一个空对象，空对象的 `__proto__` ，指向该构造函数的 `prototype`;
2. 构造函数的内部的 `this` 指向上面的空对象
3. 如果该函数没有返回对象，则返回 `this`

<!-- more -->

用代码表示，就是以下过程

```javascript
function Foo(){
// var temp={}
// this = temp
// this.__proto__ = Foo.prototype
this.xxx=xxx
...
//return this
}
```

模拟一下 new

```javascript
function _new(fn, ...arg) {
  const obj = Object.create(fn.prototype)
  const ret = fn.apply(obj, arg)
  return ret instanceof Object ? ret : obj // 如果该函数没有返回对象，则返回obj
}
```

new 的作用，就是省那么几行代码。（也就是所谓的语法糖）

> 引用

- [JS 的 new 到底是干什么的？](https://zhuanlan.zhihu.com/p/23987456)
- https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/new
