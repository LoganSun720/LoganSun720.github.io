---
title: hexo博客多终端同步
date: 2019-12-19 22:43:52
tags: blog
---

## 一、首先要先建立好基本的单终端博客系统

### 1.这个是win10+hexo+github的搭建方法

​		参考[链接](https://segmentfault.com/a/1190000020382983)

​		基本操作比较简单，本文也不再赘述。

​		如果出现难以解决的问题：比如博客本地根目录下的_config.yml文件找不到了的时候，就可以直接把博客根目录删除，从头操作

**坑1：**注意想用自定义theme最好不要从github上直接克隆下来，而是以压缩包形式下载到对应theme文件夹后再解压。这对之后的多终端同步有好处

<!--more-->

### 2.另一个（或者之后的其他）终端需要提前配一下环境

​		参考[链接]([https://oceandlnu.github.io/2017/03/06/GitHub+Hexo%E5%8D%9A%E5%AE%A2%E5%A4%9A%E7%BB%88%E7%AB%AF%E5%90%8C%E6%AD%A5[%E5%87%86%E5%A4%87%E5%B7%A5%E4%BD%9C\]/](https://oceandlnu.github.io/2017/03/06/GitHub+Hexo博客多终端同步[准备工作]/))

​		主要就是git+node.js+hexo

## 二、多终端部署

​		参考[链接]([https://oceandlnu.github.io/2017/04/05/GitHub+Hexo%E5%8D%9A%E5%AE%A2%E5%A4%9A%E7%BB%88%E7%AB%AF%E5%90%8C%E6%AD%A5/](https://oceandlnu.github.io/2017/04/05/GitHub+Hexo博客多终端同步/))

**坑2：**注意所有的clone或add等与github网址有关的操作，都要用https那个版本的网址（在github上给出的）

### 0.基础部署

​		首先是本文中的一、2.中的环境要部署好，接下来就是关键的对接操作：

```powershell
git init  #在blog根目录下初始化本地仓库
git add .  #将目录下的所有文件添加到本地仓库
git commit -m "Blog Source Hexo" #提示本次commit主要目的
git branch hexo  #新建hexo分支
git checkout hexo  #切换到hexo分支上
git remote add origin https://github.com/LoganSun720/LoganSun720.github.io.git #将本地与Github项目对接
git push origin hexo  #push到Github项目的hexo分支上
```

​		这样github就会多出一个名为hexo的分支

### 1.git clone/pull

​		第一次在新终端上操作时需要将hexo上的东西都clone下来

```powershell
git clone -b hexo https://github.com/LoganSun720/LoganSun720.github.io.git
cd oceandlnu.github.io  #切换到刚刚clone的文件夹内
npm install  #注意，这里一定要切换到刚刚clone的文件夹内执行，安装必要的所需组件，不用再hexo init
```

**注：之后再用这台终端写文章时就用`git pull`就好**

​		每次写之前都要先在blog根文件夹下的hexo分支进行pull操作，将远端hexo分支下的内容更新至本地。不然新一次更改上传的时候会把之前在别的终端上进行上传的更改覆盖。

**坑：**可能会有如下报错：

> gitThere is no tracking information for the current branch.
>
> Please specify which branch you want to merge with.
>
> See git-pull(1) for details
>
> ```powershell
> git pull <remote> <branch>
> ```
>
> If you wish to set tracking information for this branch you can do so with:
>
> ```powershell
> git branch --set-upstream-to=origin/<branch> hexo
> ```

**解决：** 根据提示输入：

git branch --set-upstream-to=origin/远程分支的名字(我的是hexo) 本地分支的名字(我的是hexo) ·

### 2.hexo new post

​		编辑文章时最好用`hexo new post "文章名"`，但是写文章时可以直接在blog根文件夹下的`source/_post/`下用md格式编辑即可

### 3.上传

```powershell
git add source  #经测试每次只要更新source中的文件到Github中即可，因为只是新建了一篇新博客
git commit -m "XX"  #提醒之后的自己这次更改是什么
git push origin hexo  #推送到远程仓库，更新hexo分支
```

#### 附：当然为了使博客生效 每次可以先用hexo三连来预览，并用`hexo d`来部署，最后在用(二)中的后三步来更新到github