---
title: git快速上手
date: 2019-12-21 22:13:59
tags:tools
---

### 1.命令基础

git常用命令速查表如下：

![git常用命令.png](https://i.loli.net/2019/12/21/qmNHon9ipjKcelt.png)



### 2.图解git

参考国外大神的[精美图解](http://marklodato.github.io/visual-git-guide/index-zh-cn.html)

git结构三大模块之间的关系如图：

![img](http://marklodato.github.io/visual-git-guide/basic-usage.svg)

上面的四条命令在工作目录、暂存目录(也叫做索引)和仓库之间复制文件。

- `git add *files*` 把当前文件放入暂存区域。
- `git commit` 给暂存区域生成快照并提交。
- `git reset -- *files*` 用来撤销最后一次`git add *files*`，你也可以用`git reset` 撤销所有暂存区域文件。
- `git checkout -- *files*` 把文件从暂存区域复制到工作目录，用来丢弃本地修改。

你可以用 `git reset -p`, `git checkout -p`, or `git add -p`进入交互模式。

也可以跳过暂存区域直接从仓库取出文件或者直接提交代码。也可以跳过暂存区域直接从仓库取出文件或者直接提交代码。

![img](http://marklodato.github.io/visual-git-guide/basic-usage-2.svg)

- `git commit -a `相当于运行 `git add` 把所有当前目录下的文件加入暂存区域再运行。`git commit`.
- `git commit *files*` 进行一次包含最后一次提交加上工作目录中文件快照的提交。并且文件被添加到暂存区域。
- `git checkout HEAD -- *files*` 回滚到复制最后一次提交。

#### 约定

后文中以下面的形式使用图片。

![img](http://marklodato.github.io/visual-git-guide/conventions.svg)

绿色的5位字符表示提交的ID，分别指向父节点。分支用橘色显示，分别指向特定的提交。当前分支由附在其上的*HEAD*标识。 这张图片里显示最后5次提交，*ed489*是最新提交。 *master*分支指向此次提交，另一个*maint*分支指向祖父提交节点。

