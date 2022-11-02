---
title: fuck ie , ie 兼容相关
date: 2022-11-02 00:00:00
updated: 2022-11-02 00:00:00
tags:
  - IE
  - 兼容
---

IE 始终都在，无法逃避。不过辛亏有各种 Babel 帮我们处理 IE 兼容问题。

目前只处理兼容 ie10 及以上。

<!-- more -->

### Echarts 兼容

新版本的 Echarts5.0 对 ie 兼容有问题，降版本处理，如果使用 vue-echarts 也需要降版本。注意参数、方法变更，比如 5.0 版本 options 字段改成了 option。

```
"echarts": "4.1.0",
"vue-echarts": "4.0.2"
```

### Vue cli 兼容 

cli 版本 5.0

.browserslistrc

```
> 1%
last 2 versions
not ie <= 10
```

vue cli 有 babel 插件 `@vue/cli-plugin-babel`，需要配置一下：

babel.config.js

```
 presets: [
    [
      '@vue/app',
      {
        polyfills: ['es.promise', 'web.url', 'web.url-search-params'],
      },
    ],
  ],
```

以上，后续有再补充。
