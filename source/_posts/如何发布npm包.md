---
title: 如何发布npm包
date: 2019-03-27 00:00:00
updated: 2019-03-27 00:00:00
tags:
  - npm
---

最近在造轮子的过程中，需要将本地的代码发布到 npm 上，方便他人使用。写一篇博客方便以后查阅。

<!-- more -->

# 发布

1. 在根目录建立 index.js 的文件，需与 package.json 中 main 的值一致。

2. 将写好的组件导入到 index.js 中，形如：

```JavaScript
import Component from "dist"
```

3. 如报错提示不支持 import 的语法， 可在使用 parcel
   `mpx parcel build index.js --no-minify --no-cache` 将文件转移成兼容版本。

4. 上传

- 第一次发布前需要使用 `npm adduser` 添加在 npm 上注册的用户名；
- `npm publish` 发布。

# 测试包

在包发布以后，每次包升级，需要测试。如果每次上传，都本地安装测试，会比较麻烦，而且不利于用户使用。

1. 在项目处使用 `npm link`；

2. 测试目录使用 `npm link 包的名字`。
