---
title: zookeeper快速上手
date: 2019-12-22 22:52:42
tags: tools
---

#### C/S模式建立

client端

```bash
zkCli.sh -server localhost:2181
```

开启客户端后可以进行之后操作：

```bash
ls -R /
```

增加、删除、改变节点

```bash
create [路径]/delete [路径]/set [路径] [值]
```



#### 四字命令

```bash
echo ruok | nc localhost 2181
```

![四字命令合集](https://cdn.nlark.com/yuque/0/2019/png/514710/1575941721925-49c49ed2-964b-407c-8b04-9cb2448d607c.png)

