---
title: Git 常用操作
date: 2018-10-11 00:00:00
updated: 2022-04-29 00:00:00
tags:
  - git
---

使用 Git 前，需要先建立一个仓库(repository)。我们可以使用一个已经存在的目录作为 Git 仓库或创建一个空目录。
使用当前目录作为 Git 仓库，我们只需使它初始化。

<!-- more -->

# 常用命令

`git init`

运行后会在当前文件夹建立一个`.git`的文件夹，此时仓库是没有其他文件的，我们可以通过`add`命令增加文件。

`git add filename`

现在我们已经添加了这些文件，我们希望它们能够真正被保存在 Git 仓库。为此，我们将它们提交到仓库。

`git commit -m "Adding files"`

可以通过

`git commit -v`

查看所有 diff 信息

当我们修改了很多文件，而不想每一个都 add，想 commit 自动来提交本地修改，我们可以使用-a 标识。

`git commit -a -m "Changed some files"`

`git commit`命令的`-a`选项可将所有被修改或者已删除的且已经被 git 管理的文档提交到仓库中。
千万注意，`-a`不会造成新文件被提交，只能修改。

`git pull`

拉取线上代码，有可能会有冲突，这时需要手动合并冲突，并提交，vscode 有代码合并工具，方便合并代码。

`git push`

提交本地代码到线上

# 处理 bug

当代码出现 bug 时，可以将当前代码先提交，注意一定要先提交成功，然后执行 `git reset --hard commitID`，重置到指定 commit 时的版本。可以通过`git show commitID` 查看提交时的代码。

# 分支

`git branch 分支名` 创建一个分支
`git push origin 本地分支名:远程` 提交本地分支到远程
`git checkout 分支名` 切换到另一个分支

# 通常提交代码流程

1. `git add .` 通常来讲每次的 add 都是一种类型修改提交，不建议一次提交所有
2. `git commit`
3. `git pull` 注意 push 前一定要 pull 代码
4. `git push`

# 更多技巧

1. `git reflog` 查看 reset 之前的时间点，使用 `git reset --hard` 返回之前时间点;
2. `git commit . --amend` 当前代码提交到上一次提交里;
3. `git rebase -i HEAD~3` 对最近的三次 commit 进行合并、修改;
4. `git rm --cached xxx` 用于 xxx 虽然被写入 .gitignore 但依然被提交了的情况;
5. `git add . ; git stash` 有时代码还没提交，但需要 `git pull`; pull 结束后再用 `git stash pop` 将其召唤回来

> 引用

- [git 详解](http://www.ruanyifeng.com/blog/2015/12/git-cheat-sheet.html)

- [Git 操作手册](https://xiedaimala.com/tasks/24e32d28-9aeb-4010-a643-d97904e8101d/text_tutorials/317c56bc-d774-415c-9594-4b11e67a3f5d)
