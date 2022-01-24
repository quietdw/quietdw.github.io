---
title: Hexo 博客搭建
tags:
  - Hexo
---

使用 GitHub Pages + Hexo 实现自动部署的个人技术博客搭建。

<!-- more -->

### 安装 Hexo

    npm install -g hexo-cli

Hexo 安装好了后，执行以下命令初始项目：

```node
$ hexo init 文件夹
$ cd 文件夹
$ npm install
```

运行 `hexo server` 启动服务，默认运行在 http://localhost:4000/。

博客 `.md` 文件在项目的 `source/_posts` 文件夹中，现在就可以在该文件夹中增加。

### 配置浏览器热更新

有个问题，我们编辑在博客的时候，本地的浏览器不能动态更新，需要手动刷新浏览器，这很麻烦。

安装 browsersync 浏览器热更新

    npm i hexo-browsersync -D

然后重启服务

### 配置主题

我使用的是 `Next` 主题，主题风格简约，适合我的口味。有多种下载方式，按需选择

下载好之后，解压到 `/theme/next` 文件夹中，然后在项目的 `/_config.yml` 配置文件中设置 `theme: next` 即可，主注意空格和缩进。

### 部署到 GitHub Pages

1. 新建名称为 `GitHub名称.github.io` 的仓库，然后将本地未编译文件上传到仓库；
2. 新建`gh-pages` 分支，Hexo 有 `hexo generate` 命令将我们的文件打包成 html 等静态文件，文件放在 `/public` 目录切换分支，编译好后上传；
3. 在仓库 `Setting/GitHub Pages/Source` 将分支改成 `gh-pages` 就可以了，然后在 `GitHub名称.github.io` 访问自己的博客了。

### 配置 Action 自动部署

每次都要上传太麻烦了，能不能每次 push 后自动部署呢？答案是肯定的。

1. 新建 `.github/workflows/pages.yml` 配置文件，并增加以下配置;

```yml
name: Pages

on:
  push:
    branches:
      - master # default branch master | main

jobs:
  pages:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - name: Use Node.js 16.x
        uses: actions/setup-node@v2
        with:
          node-version: '16'
      - name: Cache NPM dependencies
        uses: actions/cache@v2
        with:
          path: node_modules
          key: ${{ runner.OS }}-npm-cache
          restore-keys: |
            ${{ runner.OS }}-npm-cache
      - name: Install Dependencies
        run: npm install
      - name: Build
        run: npm run build
      - name: Deploy
        uses: peaceiris/actions-gh-pages@v3
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          publish_dir: ./public
```

2. 一旦部署完毕，就能在 `gh-pages` 分支看到编译好的文件，由于之前我们文件分支也在 `gh-pages`，并且配置好了 `GitHub Pages`，因此提交好后就能自动部署了；

### 碰到的一些问题

1. 在 next 主题配置中，增加 tags 目录，访问会报 404

解决方法 ：在 `/source` 文件夹中新建 `tags/index.md`文件，文件内容

```md
---
title: tags
type: tags
layout: 'tags'
---
```

> 引用

- [Hexo Documentation](https://hexo.io/docs/#Install-Hexo)
- [Hexo 使用 browsersync 浏览器热更新](http://www.leojuly.top/2018/11/26/Hexo-browsersync/)
- [hexo-theme-next](https://github.com/iissnan/hexo-theme-next)
- [GitHub Actions ](https://hexo.io/docs/github-pages)
