---
title: OVS交换机连接多控制器
date: 2019-12-20 15:09:46
tags: SDN
---

为网桥端口增加ip:

```shell
ovs-vsctl add-port br0 p1
ip addr add 192.168.3.123/24 dev p1
```

<!--more-->

为网桥添加多个控制器：

```shell
ovs-vsctl set-controller b1(网桥名) tcp:192.168.1.110:6653 tcp:192.168.1.110:6654
```



