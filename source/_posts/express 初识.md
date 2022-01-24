---
title: express 初识
date: 2019-05-01 00:00:00
updated: 2019-05-01 00:00:00
tags:
  - express
---

最近在使用 express 做在线备忘贴的小项目，一边摸索着使用，一边整理一下使用过程中的笔记。

<!-- more -->

# EXPRESS 的安装使用

1. 安装 `npm i express`，一般使用第二种脚手架安装
2. 安装 express 的脚手架 express-generator ：`npm install express-generator`，运行，`./node_modules/express-generator/bin/express-cli.js -f -e`
3. 运行 `npm i`，安装脚手架提供的包
4. 运行 `PORT = 4000 node ./bin/www` 可以在端口 4000 预览 demo
5. `"start": "cross-env PORT=8080 node ./bin/www"`，可以设置脚本，默认在 8080 端口打开页面，cross-env 是为了解决 windows 下报错的问题。

# EXPRESS 工作流程

目录结构

```
├── app.js # 应用的主入口
├── bin  # 启动脚本
├── node_modules # 依赖的模块
├── package.json # node模块的配置文件
├── public # 静态资源，如css、js等存放的目录
├── routes # 路由规则存放的目录
└── views # 模板文件存放的目录
```

首先通过入口 `app.js`， 得到路由，路由一般会被拆分到 `routes/` 目录下，根据不同路由 render 数据到页面模板中。

# EXPRESS 三个重要的概念

## 路由

顾名思义，负责寻址的，根据用户请求的定位资源，图片、样式等文件的路由走 static

## 中间件

进行数据处理，把请求交给中间键处理，得到的结果再给下一级

```javascript
var app = express()

app.use(function (req, res, next) {
  // 可以得到请求的相关信息
  console.log('Time:', Date.now())
  next() // 交给下一层的中间件，或者直接send
})
```

## 模板引擎

模板引擎类似 `Vue` 中的 template ，express 可选的模板引擎有 jade ，ejs 等

> 引用

- [Express 使用手记：核心入门](https://www.cnblogs.com/chyingp/p/express-intro.html)
