---
title: 二分法定位 Bug
date: 2022-08-05 00:00:00
updated: 2022-08-05 00:00:00
tags:
  - debug
---

二分法是 debug 中最常用也是最好用的方式。我们可以一点点注释掉程序逻辑，不断地进行排错，把可能出现的问题范围缩小，然后找出罪魁祸首。再用常规手段调试。

<!-- more -->

大概方法，举个例子：

```javascript
function abc() {
    a()
    b()
    c()
    d()
}

function a() {
    console.log('a');
}
function b() {
    console.log('b');
}
function c() {
    console.log('cc');
}
function d() {
    console.log('d');
}
```

比如函数 `abcd` ，期望依次打印出 abcd，结果打印出 abccd ，使用二分法思路：

1. 先把函数 abcd 中，函数 c d 先注释，此时输出 ab，结果符合预期；
2. 只注释函数 d ，此时输出 abcc，结果不符合预期；
3. 发现函数c输出了 cc ，找到错误源头。

上面例子很简单，但当项目复杂度上升，按照例子思路就可以一块一块确定代码块，快速定位 bug。

