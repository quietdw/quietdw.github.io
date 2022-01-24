---
title: 堆叠上下文
date: 2018-11-04 00:00:00
updated: 2018-11-04 00:00:00
tags:
  - 堆叠上下文
  - z-index
  - CSS
---

### 什么是堆叠上下文

层叠上下文，英文称作 ”stacking context” 。是 HTML 中的一个三维的概念。如果一个元素含有层叠上下文，我们可以理解为这个元素（包括其中的子元素）在 z 轴上就“高人一等”。

<!-- more -->

![堆叠顺序](https://jiangnana.fun/test/%E5%A0%86%E5%8F%A0%E9%A1%BA%E5%BA%8F.jpg)

### 什么时候出现堆叠上下文？

堆叠上下文可以理解为堆叠作用域，以下属性会出发堆叠上下文。

- 根元素(HTML)
- z-index 值不为 auto 的，相对绝对定位
- z-index 值不为 auto，flex 项目，即父元素 display:flex/inline-flex
- opacity 的值小于 1 的元素，rgba 不算，这是背景
- transform 属性不为 none
- mix-blend-mode 属性不为 normal 的元素
- filter 的值不为 none 的元素
- perspective 的值不为 none 的元素
- isolation 属性被设置为 isolate 的元素
- position: fixed
- 在 will-charge 中指定了任意 css 属性
- webkit-overflow-scrolling 属性被设置 touch 的元素

> 引用

- https://www.zhangxinxu.com/wordpress/2016/01/understand-css-stacking-context-order-z-index/
