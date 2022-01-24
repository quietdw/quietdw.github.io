---
title: 巧用 Git 分支
date: 2020-08-19 00:00:00
updated: 2020-08-19 00:00:00
tags:
  - Git
---

写代码不出 bug 这个事情呢，不能强求的，有时候在新分支（b0）开发新版本开发到一半，线上版本（master）忽然冒出一个 bug 或者功能急需变更，这时就要切换到 master，紧急修复，然后再切回 b0 继续开发；

<!-- more -->

具体要怎么操作呢？

我们要建立分支 b0，在上面开发新版本

```javascript
git checkout -b b0

// 上面是下面两句的简写
//git branch b0 新建分支b0
//git checkout b0 切换到分支b0
```

然后线上版本 bug 了，切回线上版本，新建分支 b1 紧急修复

```javascript
git checkout master
git checkout -b b1
```

修复成功后合并 b1，并删除 b1 分支

```javascript
git checkout master
git merge b1
git branch -d b1
```

然后再愉快切回一开始开发的分支 b0 上，此时 b0 分支上是没有 b1 修改的东西，因为创建 b0 分支在 b1 分支前面，可以使用 git merge master 命令将 master 分支合并入 b0 分支，或者你也可以等到 b0 分支完成其使命，再将其合并回 master 分支。

merge 过程中，可能会存在冲突导致合并失败，就要老老实实一个一个手动选择合并了。

至此，一个修复流程就走完毕了。

> 引用

- [ Git 分支 - 分支的新建与合并](https://git-scm.com/book/zh/v2/Git-%E5%88%86%E6%94%AF-%E5%88%86%E6%94%AF%E7%9A%84%E6%96%B0%E5%BB%BA%E4%B8%8E%E5%90%88%E5%B9%B6)
