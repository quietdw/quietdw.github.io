---
title: CSS深入浅出
date: 2018-11-19 00:00:00
updated: 2018-11-19 00:00:00
tags:
  - CSS
---

以前在用 CSS 时，总想系统学习一下 CSS 知识。因为以前写 CSS 时，总是哪里不会 baidu 哪里，没有系统性，对一些“效果”（bug）也是浅尝辄止，属于解决完就忘了的那种。现系统学习一番后，将学习总结、体会汇成一篇博客。

<!-- more -->

# CSS 不正交

CSS 易学，只要记住定义元素的属性和值就可以了，但因其不正交的特点，又使得它又非常难学。何为不正交？举个例子：当我们在页面中增加`u>li`标签，其默认样式是有小圆点的，但是当我们给`li`加上`display:block`时，小圆点非常奇怪的消失了，这使得我们在使用 CSS 非常小心，因为你也不知道什么时候会踩坑。除此之外，还有以下几个坑：

- margin 与 border

  1. 上下两个 div，margin 都为 10px 上 div 底部与下 div 顶部的 margin 会重合，有以下解决方法：

     - 在两个 div 之间加个 border 为 0.1px 的 div
     - 给两个 div 之间加`display：table`的 div
     - 给两个 div 之间加`display：flex`的 div
     - 给两个 div 之间加`overflow: hidden;`的 div

  2. 两个父子 div，子元素有 margin 值，子元素的 margin 值会超出父元素，有以下解决方法：
     - 给父元素加 border
     - 给父元素加 padding
     - 给父元素加`overflow: hidden;`
     - 给父元素加`display：flex`，子元素宽度会有变化

- `display:inline`、`display:inline-block`的元素，加了`position:absolute`，display 会变成 block;

- `display:fix`与 transform 混用，会影响 fix 的定位位置

- float 与 inline 元素，inline 元素会环绕 float 元素，而不会在 float 元素下方，但是 block 元素会在下方

# 元素的宽高

记住一点，能不用定死宽高的，就不要定死宽高，采用添加 padding 的方式撑高元素

- 字与字通过基线对齐，元素高度相同，基线不同，并排的话，可能会导致撑开的高度大于元素本身。内联元素 margin 和 padding 会只会影响宽，不会影响高，其高度由 line-height 决定
- `font-size`的大小不一定真是字体大小，不同字体有默认行高（设计师设计时设置），使得不同字体的撑开的高度可能不一致
- 内联元素之间如果有空格，或有回车，在网页则会显示两个内联元素之间有一个莫名其妙的空格
- 设置了浮动，父元素要清除浮动，防止浮动造成高度塌陷
- 设置两端对齐
  ```css
  .paernt {
    text-align: justify;
  }
  .parent::after {
    content: '';
    display: inline-block;
    width: 100%;
  }
  ```
- [一行字，超出省略号](https://jiangnana.fun/test/%E7%9C%81%E7%95%A5%E5%8F%B7.html)
- [多行字超出省略号（管 IE 干嘛 →_→）](https://jiangnana.fun/test/%E5%A4%9A%E8%A1%8C%E8%B6%85%E5%87%BA%E7%9C%81%E7%95%A5%E5%8F%B7.html)

# 堆叠上下文

[开启传送门·另一篇博客](https://jiangnana.fun/2018/11/04/CSS%E5%A0%86%E5%8F%A0%E4%B8%8A%E4%B8%8B%E6%96%87/)

# icon 的做法

现在使用较多的是通过 SVG 导入在线图标，也有通过背景图的（sprites 生成器），或者直接用 CSS 干的比如[太极图](https://jiangnana.fun/test/taichi.html)

# 移动端页面

移动端的页面和 PC 端的没区别，但要注意以下几点

- 手机端要加个`<meta name="viewport" content="width=device-width, user-scalable=no, initial-scale=1.0, maximum-scale=1.0, minimum-scale=1.0">`禁止用户缩放
- 媒体查询，需要注意优先级问题，后面优先级强
  ```css
  @media (max-width: 980px) {
    body {
      background: red;
    }
  }
  @media (max-width: 375px) {
    body {
      background: yellow;
    }
  }
  ```
- `<link rel='stylesheet' href='xxx.css' media='max-width:375px'>`设备小于 320px 才引用 style.css
- 要设计图，没设计图不做
- 学会隐藏元素
- 手机端有 touch 事件，没有 hover 事件
- 手机端不能改变屏幕大小（resize），也没有滚动条

# flex 布局

flex 适用于简单的线性布局，更复杂的用 grid

主要记住 flex container 以及 flex item 的六个属性

flex container

| 属性名          | 功能                                                                      |
| --------------- | ------------------------------------------------------------------------- |
| flex-direction  | 定义内部元素的排序方向，常用的有 row，row-reverse，column，column-reverse |
| flex-wrap       | 是否换行                                                                  |
| flex-flow       | 上面两个的简写                                                            |
| justify-content | 主轴方向的对齐方向                                                        |
| align-items     | 侧轴方向的对齐方向                                                        |
| align-content   | 多行、多列内容的对齐方向，center，space-around，space-bteween 等          |

flex item

| 属性名      | 功能                                    |
| ----------- | --------------------------------------- |
| flex-grow   | 空间有空余时，增长比例                  |
| flex-shrink | 空间不够时，收缩比例                    |
| flex-basis  | 设置初始大小                            |
| flex        | 上面三个的缩写                          |
| order       | 元素排列顺序                            |
| align-self  | 自身的对齐方式，重写 align-items 的属性 |

# BFC（格式化上下文）

什么是 BFC 说不清楚

但可以说明某例子是否是 BFC

一个块格式化上下文（block formatting context） 是 Web 页面的可视化 CSS 渲染出的一部分。它是块级盒布局出现的区域，也是浮动层元素进行交互的区域。

一个块格式化上下文由以下之一创建：

- 根元素或其它包含它的元素
- 浮动元素 (元素的 float 不是 none)
- 绝对定位元素 (元素具有 position 为 absolute 或 fixed)
- 内联块 (元素具有 display: inline-block)
- 表格单元格 (元素具有 display: table-cell，HTML 表格单元格默认属性)
- 表格标题 (元素具有 display: table-caption, HTML 表格标题默认属性)
- 具有 overflow 且值不是 visible 的块元素，
- display: flow-root
- column-span: all 应当总是会创建一个新的格式化上下文，即便具有 - - column-span: all 的元素并不被包裹在一个多列容器中

一个块格式化上下文包括创建它的元素内部所有内容，除了被包含于创建新的块级格式化上下文的后代元素内的元素。

BFC 有什么用？ - 使父元素能包住子元素 - 兄弟之间划清界限

# 动态 rem

em 是自己的 font-size 的值的大小，大概就是一个汉字的高度，或者一个字母 M 的高度

rem 是 root em ，指的是根元素（html）的字体大小

- 可以通过 js 动态调整 rem 大小

```javascript
<meta name="viewport" content="width=device-width, user-scalable=no, initial-scale=1.0, maximum-scale=1.0, minimum-scale=1.0">
 <script>
     var pageWidth = window.innerWidth
     document.write('<style>html{font-size:'+pageWidth+'px;}</style>')
 </script>
```

- 在 SCSS 里使用 PX2REM
  在 vscode 里可以下载 sass 插件

```scss
@function px($px) {
  @return $px/$designWidth * 10 + rem;
}

$designWidth: 640; // 640 是设计稿的宽度，你要根据设计稿的宽度填写。如果设计师的设计稿宽度不统一，就杀死设计师，换个新的。

.child {
  width: px(320);
  height: px(160);
  margin: px(40) px(40);
  border: 1px solid red;
  float: left;
  font-size: 1.2em;
}
```

# bootstrap

2 兼容 IE6,3 不兼容 IE6

栅栏布局：

    - .row>div.col-lg-12
    - .row>div.col-md-12
    - .row>div.col-xs-12
    - .row>div.col-sn-12

建议不要更改 bootstrap 上类的值，如需修改，建议套一个父级 div，在父级 div 上更改

其余的抄官方文档就完事了。。。

# IFC

BFC 的兄弟，指 inline formatting context

`font-size：100px;`到底指的是什么呢？em-square，要去了解国外的活字印刷，指的是印刷这个字的模具大小。

line-height：实际占地高度

`vertical-align`可以用来解决 img 元素在 div 中，底部有空隙的 bug，其余没什么用。
