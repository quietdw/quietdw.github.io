---
title: vw、rem 混合适配移动端与PC端
date: 2023-03-11 00:00:00
updated: 2023-03-11 00:00:00
tags:
  - 移动端
  - vw
  - rem
---

目前移动端有两种适配方案，一种是传统的 rem 布局，需要在页面中加入一段js代码，用于计算根元素的 `font-size`，另外一种则是使用 vw 单位，不用使用js。

<!-- more -->

两种方案，主流是vw，因为不用额外在页面中加入js，但是该方案在 PC 端就非常不友好，因为vw是取决于页面的宽度，加上PC端是横屏的，就会导致页面内容被放大，对用户非常不友好。

如果是纯粹移动端访问的，那问题不大，直接找个可以 px 转 vw 插件 `postcss-px-to-viewport-8-plugin`，自动转单位就好了。

如果想移动端（页面宽度小于750px）自动适配，大于750px就固定大小的，可以使用 `媒体查询 + rem + vw` 进行适配；

考虑到根元素 `font-size` 有最小值，而设计稿也有 375px 宽度的，计算后的 `font-size` 单位太小，在低版本浏览器有显示问题，需要放大根元素的 `font-size`；

话不多说，直接上代码。

```css
@media (max-width: 750px) {
  html {
    font-size: 4vw; 
  }
}

/* 大于750px就居中显示 */
@media (min-width: 750px) {
  html {
    font-size: 24px;
  }

  body {
    width: 750px !important;
    height: 100%;
    margin: 0 auto;
  }
}

```

```js
module.exports = {
  plugins: [
    require('postcss-px-to-viewport-8-plugin')({
      unitToConvert: 'px',
      viewportWidth: 375*4, // 设计稿 放大4倍，css那也要放大4倍。
      unitPrecision: 5,
      propList: ['*'],
      viewportUnit: 'rem',
      fontViewportUnit: 'rem',
      selectorBlackList: ['.ignore', '.hairlines'],
      minPixelValue: 1,
      mediaQuery: false,
      replace: true,
      landscape: false,
      landscapeUnit: 'rem',
      landscapeWidth: 667,
    }),
  ],
}
```

