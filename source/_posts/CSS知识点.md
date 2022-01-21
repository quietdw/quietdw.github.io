---
title: CSS知识点
tags:
  - CSS
---

### CSS 介绍

层叠样式表 (Cascading Style Sheets，缩写为 CSS），是一种 样式表语言，用来描述 HTML 或 XML（包括如 SVG、MathML、XHTML 之类的 XML 分支语言）文档的呈现。CSS 描述了在屏幕、纸质、音频等其它媒体上的元素应该如何被渲染的问题。

<!-- more -->

CSS 是开放网络的核心语言之一，由 W3C 规范 实现跨浏览器的标准化。CSS 被分为不同等级：CSS1 现已废弃， CSS2.1 是推荐标准（修改了 CSS2 中部分错误）， CSS3 分成多个小模块且正在标准化中。后续不会有 CSS4，各个分支独立升级。

### CSS 知识点

- 引用 CSS 的 4 种方式
  - 内联样式。
  - style 标签。
  - 外联样式`<link rel="stylesheet" href="" >`。
  - 在 css 中引用样式（不推荐）`@import"mystyle.css"。 `
- 调试时给调试标签加一像素的 border。
- 使元素排列在一行：给子元素添加浮动，父元素添加 clearfix,不建议使用 overflow:hidden。
  ```css
  content: '';
  display: block;
  clear: both;
  ```
- `<a>`有默认样式，在全局改变字体颜色，其字体颜色不会改变，添加样式`color: inhert`使 a 可以继承全局设置的颜色。
- 标签的高度由其内部文档流元素的高度总和决定.
  > 文档流：内联元素按基线从左往右，块级元素从上往下流动。
- `position:relative`不会使元素脱离文档流
- 内联元素的 margin 和 padding 会影响宽，不会影响高，高度取决于 line-height
- 少用`display：inline`，有 bug，使用`float`。
- 行高建议比字体大，不同字体的建议行高不一样。
- 字和字通过基线对齐，不是通过中线对齐。
- `position:fix`会使宽度缩进去，使用`width:100%`，但有 padding 时会出 bug，其宽度超过浏览器宽度，可通过在下一级添加一个 div，把左右 padding 加在这个 div 上解决。
- `background-size：cover`，`background-position：center center`:解决背景图片居中、改变背景大小的问题。
- 用 padding 撑起宽高，尽量少设置宽高，可用 max-width。
- span 里不要有 div。
- `boxsizing:content-box`:标准盒模型，实际宽高为设置的宽高+内外边距+border。
- `boxsizing:border-box`:怪异盒模型，实际宽高就是设置的宽高。
- 伪元素不能选中。
- [设置动画旋转](https://developer.mozilla.org/zh-CN/docs/Web/CSS/CSS_Animations/Using_CSS_animations)
  ```css
  @keyframes rotate{
      from{animation： rotate(0deg);}
      to{animation： rotate(360deg);}
  }
  # demo{
      animation: rotate 2s infinite;
      animation-timing-function:linear;
  }
  ```
- 下载按钮要用`<a>`标签做，因为有下载请求。
- `:nth-child(an+b)`这个 CSS 伪类首先找到所有当前元素的兄弟元素，然后按照位置先后顺序从 1 开始排序，选择的结果为第（an+b）个元素的集合（n=0，1，2，3...）。
- 使用`display:inline-block`会有空隙，要同时使用`vertical-align:top`。
- 横线做结构，不做类名。
- nbsp 是 no break space 的缩写
- 在伪类上加`padding-top:100%`，可以生成个正方形
