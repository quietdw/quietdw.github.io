---
title: CSS布局
tags:
  - CSS
  - 布局
---

### 左右布局

1. 绝对定位布局
   - 在父元素下设置两个宽度 50%容器，设置绝对定位。但有时候会出错。
   - 父容器不得不必须要设置高度，但设置高度会出 bug（内容改变的时候），更不要说绝对定位的元素脱离了文档流。

<!-- more -->

2. Table 布局
   使用`display: table`使父亲元素变成一个 table，然后使用`display: table-cell`使子元素变成里面的元素。

3. 浮动布局
   使用 float 布局，可以解决绝对定位带来问题。此外父元素要清除浮动，以避免浮动带来高度塌陷的问题。

4. Flex 布局
   - flex 布局非常棒，但是它仅限用于 ie10 以上的浏览器。
   - 首先把父容器设置`display:flex`，为了使子元素都一样，子元素设置`flex:1`，不用设置宽高，完美。

> 以上参考：

- https://css-tricks.com/left-and-right/
- https://segmentfault.com/a/1190000011358507

### 左中右布局

1. Table 布局
   和左右布局类似，再多加一列，只是不能优先显示中间的一列。

2. 双飞翼布局

   - 在 HTML 布局中，center 放在最前面，其次 left，最后是 right，因此优先显示 center；
   - center，left，right 都设置`float:left`;
   - center 设置宽度 100%，此时 center 占满一行，left 和 right 都被挤到第二行；
   - 此时给 left 设置`margin-left:100%`,left 就被拉倒最左边，同理 right 设置`margin-lef: 300px`（right 的宽度），拉到最右侧；
   - 此时 center 的左右两边的内容被覆盖，给 center 加一个子元素 div，设置 margin，左右值分别为 left 和 right 的宽度； -浮动布局要给父元素清除浮动，或者添加属性`over-flow:hidden`

3. flex 布局
   - 给 container 设置`display: flex`；
   - center 设置宽度`width:100%`，left 和 right 设置；
   - 给 left 和 right 设置`flex-shrink: 0`，使之不会收缩；
   - 使用 order 属性给三个 div 重新排序，left 设置`order:1`，center 设置`order:2`，right 设置`order:3`。

> 以上参考：

- https://www.cnblogs.com/imwtr/p/4441741.html
- https://www.jianshu.com/p/3046eb050664

### 水平居中

1. 行内元素
   对于`<span>、<a>、<label>、<em>、<img>`这种内联元素，直接构建一个样式是`text-align:center`的容器。如果是块级元素，里面的文本会居中。

2. 块级元素

   - 定宽

     - 添加样式`margin:0 auto`实现容器居中，`text-align:center`实现里面的文本居中;
     - 设置绝对定位 50%，`margin:left：宽度的一半`；

   - 不定宽
     - CSS3 的 flex 实现水平居中，父元素设置：`display：flex`，子元素设置`margin:auto`；
     - 绝对定位 + transform，`transform: translateX(-50%)`可以移动本身元素的 50%；

> 以上参考

- https://blog.csdn.net/dengdongxia/article/details/80297116
- https://blog.csdn.net/MrZZhou/article/details/70344947

### 垂直居中

1. 先试试`padding:20px 0;`再说

2. 单行文本

   - 若元素是单行文本, 则可设置 line-height 等于父元素高度

3. 行内块级元素

   - 基本思想是使用 display: inline-block，vertical-align: middle 和一个伪元素让内容块处于容器中央。

   ```css
   .parent::after,
   .son {
     display: inline-block;
     vertical-align: middle;
   }
   .parent::after {
     content: '';
     height: 100%;
   }
   ```

4. 元素高度不定
   使用 flex，父元素做如下设置即可保证子元素垂直居中:

   ```css
   .parent {
     display: flex;
     align-items: center;
   }
   ```

> 以上参考

- https://blog.csdn.net/MrZZhou/article/details/70344947
