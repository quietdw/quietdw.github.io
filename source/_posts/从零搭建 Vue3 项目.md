---
title: 从零搭建 Vue3 项目
date: 2022-12-14 00:00:00
updated: 2022-12-14 00:00:00
tags:
  - Vue3
  - Webpack
---

之前项目都是基于 `Vue CLI` 搭建的，对其脚手架搭建的方式、用了哪些包也只一知半解，现通过从零开始搭建一个 Vue 项目，加深一下印象。

<!-- more -->

# 配置 Webpack 环境

先抛开 `Vue` ，先使用 `Webpack` 搭建一个最初始的项目。

```
// 创建文件夹
mkdir vue3-project && cd vue3-project
// 初始化项目
yarn init
```

此时你将会等到只有 `package.json` 的项目。

然后运行以下命令安装 `Webpack` 。

```
yarn add webpack webpack-cli
```

接下来在根目录新建 `src` 文件夹，并增加 `main.js` 文件，并在根目录下创建 `index.html` 和 `webpack.config.js` 文件

```
mkdir src && touch ./src/main.js
touch index.html webpack.config.js
```

并在 `webpack.config.js` 里添加以下内容

```javascript
const path = require("path");

module.exports = {
  mode: 'development', // 环境模式
  entry: path.resolve(__dirname, './src/main.js'), // 打包入口
  output: {
    filename: 'js/[name].js', // 打包完的静态资源文件名
    path: path.resolve(__dirname, "dist"), // 打包出口
  },
};
```

> *注

1. `__dirname` 返回当前模块文件解析过后所在的文件夹(目录)的绝对路径；
2. `path` 是 `Node.js` 提供的用于处理文件路径的小工具，`path.resolve` 方法将传进来的路径处理成一个绝对路径，`path.resolve(__dirname, "dist")` 相当于当前目录下的 `dist` 文件夹。

修改 `package.json` 的 `scripts` 属性，使用 `webpack.config.js` 打包代码：

```json
"scripts": {
  "dev": "webpack --config ./webpack.config.js"
}
```

运行打包指令 yarn dev , 如下所示代表成功：

```
$ webpack --config ./webpack.config.js
asset js/main.js 1.18 KiB [emitted] (name: main)
./src/main.js 1 bytes [built] [code generated]
webpack 5.75.0 compiled successfully in 71 ms
```

# 配置 html 模板

打包没有 `html` 文件，`html-webpack-plugin` 插件可以在打包的时候以 `index.html` 为模板，输出到 `dist` 文件夹

运行安装 `html-webpack-plugin`

```
yarn add html-webpack-plugin -D
```

> *注

1. `-D` 命令表示只是在开发环境使用，依赖写入到 `devDependencies`

安装完成在 `webpack.config.js` 下引入并使用

```javascript
const path = require("path");
const HtmlWebpackPlugin = require("html-webpack-plugin");

module.exports = {
  mode: "development", // 环境模式
  entry: path.resolve(__dirname, "./src/main.js"), // 打包入口
  output: {
    filename: "js/[name].js", // 打包完的静态资源文件名
    path: path.resolve(__dirname, "dist"), // 打包出口
  },
  plugins: [
    new HtmlWebpackPlugin({
      template: path.resolve(__dirname, "./index.html"), // 我们要使用的 html 模板地址
      filename: "index.html", // 打包后输出的文件名
      title: "从零搭建 Vue3 项目", // index.html 模板内，通过 <%= htmlWebpackPlugin.options.title %> 拿到的变量
    }),
  ],
};
```

给 index.html 增加一下内容

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title><%= htmlWebpackPlugin.options.title %></title>
</head>
<body>
  <div id="app"></div>
</body>
</html>
```

给 main.js 增加一下内容

再次运行 `yarn dev` ，`dist` 文件夹中多了 `index.html`，直接访问页面是有文字的，页面的 `title` 也是设置的 `从零搭建 Vue3 项目`

# 配置热更新

每次写完代码都要重新打包才能看到效果，我们需要插件实现一个实时更新最新代码的能力。

话不多说，安装！

```
yarn add webpack-dev-server -D
```

在 webpack.config.js 下添加如下配置：

```js
  devServer: {
    port: 8080,
  },
```

重新运行一下 `yarn dev`， 如下所示代表成功，访问 `http://localhost:8080/`，页面内容和之前访问的一致，并且改动了代码，页面也能正常更新。

```
$ webpack serve --progress --config ./webpack.config.js
<i> [webpack-dev-server] Project is running at:
<i> [webpack-dev-server] Loopback: http://localhost:8080/
<i> [webpack-dev-server] On Your Network (IPv4): http://192.168.158.146:8080/
<i> [webpack-dev-server] Content not from webpack is served from 'E:\study\vue3-project\public' directory
asset js/main.js 243 KiB [emitted] (name: main)
asset index.html 305 bytes [emitted]
```

# 配置 Vue

安装 `Vue`

```
yarn add vue
```

在 `./src` 目录下新建 `App.vue`，并增加以下内容

```vue
<script setup>
import { ref } from "vue";

const count = ref(0);
</script>

<template>
  <div class="app">
    <button @click="count++">+1</button>
    {{ count }}
  </div>
</template>

<style scoped>
.app {
  color: red;
}
</style>

```

`main.js` 内容如下

```javascript
import { createApp } from "vue";
import App from "./App.vue";
const app = createApp(App);
app.mount("#app");
```

重新运行 `yarn dev` 命令，报错了~

```
ERROR in ./src/App.vue 1:0
Module parse failed: Unexpected token (1:0)
You may need an appropriate loader to handle this file type, currently no loaders are configured to process this file. See https://webpack.js.org/concepts#loaders
> <template>
|   <div class="app">Vue 可以使用了</div>
| </template>
 @ ./src/main
 .js 2:0-28 3:22-25
```

报错内容告诉我们没有 `Vue` 相关的 `loader`；

理解了，安装！根据官网提示，要安装 2 个 `loader`

```
yarn add vue-loader vue-template-compiler babel-loader vue-style-loader css-loader -D
```

然后运行 `yarn dev`

```
&hot=true&live-reload=true
Module build failed (from ./node_modules/babel-loader/lib/index.js):
Error: Cannot find module '@babel/core'
```

报错了！提示我们安装 `@babel/core`

```
yarn add @babel/core -D
```

理解了，安装！

然后运行 `yarn dev`，没有报错了，成功了。

至此，简单的 `Vue` 项目，就完成了。

# 使用 vite 搭建

先把上面项目的 `webpack.config.js` 配置文件删除，因为我们要使用更快的 `vite`

首先安装 `vite` 

```
yarn add vite @vitejs/plugin-vue -D
```

`@vitejs/plugin-vue` 插件提供了对 vue3 的支持

然后新建 `vite.config.js` 文件，增加如下代码

```js
import { defineConfig } from "vite";
import vue from "@vitejs/plugin-vue";

export default defineConfig({
  plugins: [vue()],
});
```

`package.json` 同样修改 

```
  "scripts": {
    "dev": "vite",
    "build": "vite build",
    "preview": "vite preview"
  },
```

然后运行 `yarn dev`

```
  VITE v4.0.1  ready in 468 ms

  ➜  Local:   http://127.0.0.1:5173/
  ➜  Network: use --host to expose
  ➜  press h to show help
```

提示运行成功了，很疑惑，这么容易就好了吗，太香了把。。。

打开浏览器访问，发现访问页面空白的，似乎页面并没有编译

发现页面 `title` 是 `<%= htmlWebpackPlugin.options.title %>`，还是之前 `webpack` 用的模板，需要修改一下。

直接官网抄过来

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <link rel="icon" type="image/svg+xml" href="/vite.svg" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Vite + Vue</title>
  </head>
  <body>
    <div id="app"></div>
    <script type="module" src="/src/main.js"></script>
  </body>
</html>
```

都不用重启，页面就好了！

至此，使用 vite 搭建就完成了

# 自动引入

`Vue3` 有组合式 `API` 的语法糖，但是比如需要使用 `ref` 的时候，都需要在页面引入，使用起来十分不舒服。能不能在使用的时候自动引入呢？

话不多说，安装！

```
yarn add -D unplugin-auto-import
```

按照官方要求增加配置

```js
import { defineConfig } from "vite";
import vue from "@vitejs/plugin-vue";
import AutoImport from "unplugin-auto-import/vite";

export default defineConfig({
  plugins: [
    vue(),
    AutoImport({
     
    }),
  ],
});
```

删除 ref 引用，页面报错了，ref 并没有引用

```
Uncaught ReferenceError: ref is not defined
```

继续看文档，发现少了配置……

```
{
      include: [
        /\.[tj]sx?$/, // .ts, .tsx, .js, .jsx
        /\.vue$/,
        /\.vue\?vue/, // .vue
        /\.md$/, // .md
      ],
      // global imports to register
      imports: [
        "vue",
      ],
    }
```

> *注

1. `include` 表示符合这些结尾的文件，都可以自动引用；
2. `imports` 表示这些的包提供的方法，能全局使用。

真香！现在组件也不想手动引入了，可以吗？

话不多说，安装！


```
yarn add -D unplugin-vue-components/vite
```

在 `vite.config.ts` 添加配置，想都不用想，肯定也要加配置的，文档接着往下翻，然后复制过来

```
 Components({ 
    dirs: ['src/components'],
  }),
```

> *注

1. `dirs` 表示这个目录下的文件可以被自动引入

我们 `src` 下新建组件，然后在 `App` 组件里直接使用，页面直接更新了！


[代码地址](https://github.com/quietdw/vue3-test1/tree/vite)





#



> 引用

- [Getting Started](https://webpack.js.org/guides/getting-started/#basic-setup)
- [Vue 3 和 Webpack 5 来了，手动搭建的知识该更新了](https://juejin.cn/post/6921161482663100423)
- [webpack-dev-server](https://github.com/webpack/webpack-dev-server)
- [Vue Loader](https://vue-loader.vuejs.org/zh/guide/)
- [index.html 与项目根目录](https://cn.vitejs.dev/guide/#index-html-and-project-root)
- [vite-vue-starter](https://stackblitz.com/edit/vitejs-vite-xm5kcu?file=package.json&terminal=dev)
- [unplugin-auto-import](https://github.com/antfu/unplugin-auto-import)
- [unplugin-vue-components](https://github.com/antfu/unplugin-vue-components)




