---
title: hexo博客多终端同步
date: 2019-12-19 22:43:52
tags:blog
---

## 首先要先建立好基本的单终端博客

### 这个是win10+hexo+github的搭建方法

参考[链接](https://segmentfault.com/a/1190000020382983)

如果出现难以解决的问题：比如博客本地根目录下的_config.yml文件找不到了的时候，就可以直接把博客根目录删除，从头操作

### 另一个（或者之后的其他）终端需要提前配一下环境

参考[链接]([https://oceandlnu.github.io/2017/03/06/GitHub+Hexo%E5%8D%9A%E5%AE%A2%E5%A4%9A%E7%BB%88%E7%AB%AF%E5%90%8C%E6%AD%A5[%E5%87%86%E5%A4%87%E5%B7%A5%E4%BD%9C\]/](https://oceandlnu.github.io/2017/03/06/GitHub+Hexo博客多终端同步[准备工作]/))

主要就是git+node.js+hexo

<!--next-->

## 多终端部署

参考[链接]([https://oceandlnu.github.io/2017/04/05/GitHub+Hexo%E5%8D%9A%E5%AE%A2%E5%A4%9A%E7%BB%88%E7%AB%AF%E5%90%8C%E6%AD%A5/](https://oceandlnu.github.io/2017/04/05/GitHub+Hexo博客多终端同步/))

#### 坑：注意所有的clone或add等与github网址有关的操作，都要用https那个版本的网址（在github上给出的）

编辑文章时最好用`hexo new post "文章名"`，但是写文章时可以直接在`source/_post/`下用md格式编辑即可