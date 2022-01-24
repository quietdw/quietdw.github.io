---
title: CSS实现图片加载前占位
date: 2019-04-11 00:00:00
updated: 2019-04-11 00:00:00
tags:
  - CSS
---

在优化个人简历网站的时候，发现图片加载很慢，页面结构异常，故通过提前占位的方式使页面结构能保持正常

<!-- more -->

基本思路是利用图片的比例，给父元素加一个 padding-top，代码如下：

```css
.parent {
  position: relative;
  display: block;
  width: 100%;
  padding-top: 100%; //假设图片比例1:1，则是100%；
}

.parent img {
  position: absolute;
  top: 0;
  left: 0;
  display: block;
  width: 100%;
  height: 100%;
}
```
