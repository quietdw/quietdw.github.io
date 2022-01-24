---
title: 使用 Vue directives 封装 clipboard
date: 2022-01-22 00:00:00
updated: 2022-01-22 00:00:00
tags:
  - Vue
  - directives
  - clipboard
---

最近在使用 clipboard 实现复制到剪切板的功能，发现在 Vue 中使用不是那么丝滑，想着怎么封装一下。

<!-- more -->

查看 clipboard 的文档，发现该插件是通过操作 DOM，来实现复制剪切板的功能。而 Vue 也贴心的提供了 directives 方便我们对普通 DOM 元素进行底层操作。

### 初步使用

<iframe height="300" style="width: 100%;" scrolling="no" title="directives 初步使用" src="https://codepen.io/quietdw/embed/zYEQMVr?default-tab=js%2Cresult" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href="https://codepen.io/quietdw/pen/zYEQMVr">
  directives 初步使用</a> by jiang (<a href="https://codepen.io/quietdw">@quietdw</a>)
  on <a href="https://codepen.io">CodePen</a>.
</iframe>

当我们点击复制按钮的的时候，控制台打印 `复制成功: Welcome to Vue!`。但是当我们更改 input 里面的内容时，点击复制按钮，控制台还是打印 `复制成功: Welcome to Vue!`，并没有按照我们希望的方式更新剪切板的内容。

### 进阶使用

我们希望在 input 的值更新时，点击复制按钮，剪切板的值也能更新。directives 有 update 钩子函数，可以更新值。我们把 new 出来的 `cbd` 保存到 `el` 上，这样我们在 update 钩子函数中也能通过 `cbd` 更新剪切板的内容。不使用了的时候，需要在 unbind 钩子中摧毁实例。

<iframe height="300" style="width: 100%;" scrolling="no" title="directives 进阶使用" src="https://codepen.io/quietdw/embed/MWEdZEy?default-tab=js%2Cresult" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href="https://codepen.io/quietdw/pen/MWEdZEy">
  directives 进阶使用</a> by jiang (<a href="https://codepen.io/quietdw">@quietdw</a>)
  on <a href="https://codepen.io">CodePen</a>.
</iframe>

### 再加一点点

有时复制失败，或者复制成功后想要做一些操作，clipboard 有这两种回调函数，我们需要在 directives 中保留一下。通过给指令传参数，我们可以绑定多个值。

<iframe height="300" style="width: 100%;" scrolling="no" title="directives 进阶使用" src="https://codepen.io/quietdw/embed/LYzoMXm?default-tab=js%2Cresult" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href="https://codepen.io/quietdw/pen/LYzoMXm">
  directives 进阶使用</a> by jiang (<a href="https://codepen.io/quietdw">@quietdw</a>)
  on <a href="https://codepen.io">CodePen</a>.
</iframe>

> 引用

- [clipboard.js](https://clipboardjs.com/#example-target)
- [自定义指令](https://cn.vuejs.org/v2/guide/custom-directive.html)
