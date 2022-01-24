---
title: VSCode 配置跳转定义
date: 2021-03-18 00:00:00
updated: 2021-03-18 00:00:00
tags:
  - VSCode
---

使用 VSCode 的时候，有时想 F12 查看引用组件的内容，不会自动跳转，很不爽。今天鼓捣了一下，总算是可以跳转引用了。

<!-- more -->

在项目根目录增加该文件，并且在使用'@'别名的路径时，也会自动提示。

```
{
  "compilerOptions": {
      "target": "ES6",
      "module": "commonjs",
      "allowSyntheticDefaultImports": true,
      "baseUrl": "./",
      "paths": {
        "@/*": ["src/*"]
      }
  },
  "exclude": [
      "node_modules"
  ]
}
```

> [vs code 中路径别名（@）提示和转定义等问题 ](https://www.jianshu.com/p/1798d57ecdab)
