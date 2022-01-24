---
title: JavaScript 深拷贝
date: 2020-07-21 00:00:00
updated: 2020-07-21 00:00:00
tags:
  - JavaScript
  - 深拷贝
---

对象的拷贝不像数值或字符串一样，可以用类似 `let newValue = value` 来进行拷贝，此时 `newValue` 的值只是 `value` 的引用，当我们修改 `newValue` 的值时，`value` 的值也会改变，这不是我们所想要的结果。

<!-- more -->

### 使用 JSON.parse()

当我们确定所拷贝的对象不是很复杂的时候，可以使用 `JSON.parse(JSON.stringify(object))` 来拷贝对象，但是：

1. 当被拷贝的对象中有 undefined，function，symbol 等类型时，由于 JSON 不支持，会导致拷贝的对象不完整，比如：

   ```
    let a = {}
    a.a = 1
    a.b = undefined
    a.c = ()=>{}

    JSON.parse(JSON.stringify(a)) // 得到的对象{a:1}
   ```

### deepCopy 函数

大体思路是递归调用"浅拷贝"。

```
　function deepCopy(p, c) {

　　　　var c = c || {};

　　　　for (var i in p) {

　　　　　　if (typeof p[i] === 'object') {

　　　　　　　　c[i] = (p[i].constructor === Array) ? [] : {};

　　　　　　　　deepCopy(p[i], c[i]);

　　　　　　} else {

　　　　　　　　　c[i] = p[i];

　　　　　　}
　　　　}

　　　　return c;
　}
```

上面的函数一般场景也能用，但是复杂的场景如：`File()、Set()、Map()` 等可能就有问题了，我们可以使用现成的工具函数，如[lodash 克隆](https://github.com/lodash/lodash/blob/master/.internal/baseClone.js)

> 引用

- [ES6 时代，你真的会克隆对象吗(二)](https://segmentfault.com/a/1190000013131068)
- [Javascript 面向对象编程（三）：非构造函数的继承](https://www.ruanyifeng.com/blog/2010/05/object-oriented_javascript_inheritance_continued.html)
