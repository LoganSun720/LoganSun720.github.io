---
title: 基于zookeeper的Ryu多控制器实现过程全纪录
date: 2019-12-19
tags: SDN
---

## 基础分布式锁

通过zookeeper的分布式锁来实现一个交换机只会被一个控制器所控制，参考康奈尔大学的一个[项目](https://github.com/coscin/coscin-app-ryu.git)

<!--more-->

**问题：**没有连接交换机的时候，控制器就会有交换机的提示，显示连接了三个交换机，且地址是本地(127.0.0.1)

经过验证，自带的`ryu-manager --verbose`也出现这个问题，发现是Mininet进程在后台运行(即使是电脑重启它也会跟随系统启动)。

**解决：**用`sudo mn -c`命令即可将后台的Mininet进程clean and exit

#### 在服务器上开启zookeeper服务器

实验阶段用常开的台式机中的Ubuntu虚拟机部署`standalone`模式下的服务器：(zookeeper安装教程不再赘述)

```bash
zkServer.sh start-foreground ./conf/zoo.cfg
```

`start-foreground`是将启动日志在屏幕上打出来，在`./conf/zoo.cfg`(下载安装zookeeper的文件夹下的配置文件)中将最重要的`dataDir`、`clientPort`设置好(如果要用quorum模式，则需要在最下面添加server地址和端口)

*注意：*如果出现地址端口已经被占用问题可以用一下命令修改

```bash
sudo netstat -ap | grep 2181
```

此命令检查出正在使用2181端口的进程，进而终止

```bash
sudo kill -9 PID_num
```

#### 控制器启动：

```bash
ryu-manager --verbose -ofp-tcp-listen-port 6653 ./coscin_app.py
```

**问题1：**出现错误提示：`ImportError：Import by filename is not supported.`但是用pycharm来debug却没有问题。

*原因：*只能在ryu内部添加`coscin_app.py`等一系列脚本并运行添加后的整个ryu才行，因为在`coscin_app.py`程序中的`import`格式是相对于ryu内部的格式。  

**解决：**只需要更改`coscin_app.py`内部`import`方式即可。

**问题2：**系统路径下的`ryu-manager`总是用python2.7

**解决：**通过`ls /usr/bin/python*`来查看系统可用的python版本

```bash
sudo update-alternatives --install /usr/bin/python python /usr/bin/python2 1
sudo update-alternatives --install /usr/bin/python python /usr/bin/python3 2
```

此时再输入`python --version`检验可知版本应该已经切换到python3了，如果想要重新换回python则用：

```bash
sudo update-alternatives --config python
```

#### openflow1.3中对于多控制器的描述

​		交换机可以与单个控制器建立通信，或者可以与多个控制器建立通信。 拥有多个控制器可以提高可靠性，因为如果一个控制器或控制器连接失败，交换机可以继续在OpenFlow模式下运行。 控制器之间的切换完全由控制器本身管理，从而可以从故障中快速恢复，并实现控制器负载平衡。 控制器之间通过本说明书范围之外的机制来协调交换机之间的管理，并且多控制器功能的目的仅仅是帮助同步由控制器执行的控制器切换。 多控制器功能仅解决控制器故障转移和负载平衡问题，而不能解决无法在OpenFlow交换协议之外完成的虚拟化问题。

​		启动OpenFlow操作时，交换机必须连接到配置了它的所有控制器，并尝试同时保持与所有控制器的连接。许多控制器可以将controllerto-switch命令发送到交换机，与这些命令相关的答复或错误消息必须仅在与该命令关联的控制器连接上发送。可能需要将异步消息发送到多个控制器，对于每个合格的OpenFlow通道，该消息将被复制，并且在相应的控制器连接允许时，将发送每个消息。

​		控制器的默认角色是OFPCR_ROLE_EQUAL。在此角色中，控制器具有对交换机的完全访问权限，并且与处于同一角色的其他控制器相同。默认情况下，控制器会接收所有交换机异步消息（例如，输入数据包，删除流量）。控制器可以发送控制器到交换机的命令来修改交换机的状态。交换机在控制器之间不进行任何仲裁或资源共享。

​		控制器可以请求将其角色更改为OFPCR_ROLE_SLAVE。在此角色下，控制器对交换机具有只读访问权限。默认情况下，除了端口状态消息外，控制器不接收交换机异步消息。控制器无法执行所有发送数据包或修改交换机状态的控制器到交换机命令。例如，必须拒绝OFPT_PACKET_OUT，OFPT_FLOW_MOD，OFPT_GROUP_MOD，OFPT_PORT_MOD，OFPT_TABLE_MOD请求以及具有非空主体的OFPMP_TABLE_FEATURES的多部分请求。

​		当控制器有发出上述消息时，交换机必须使用OFPT_BAD_REQUEST的类型字段（OFPBRC_IS_SLAVE的代码字段）答复OFPT_ERROR消息。 其他仅查询数据的控制器到交换机的消息，例如OFPT_ROLE_REQUEST，OFPT_SET_ASYNC和OFPT_MULTIPART_REQUEST应该正常处理。

​		控制器可以请求将其角色更改为OFPCR_ROLE_MASTER。该角色类似于OFPCR_ROLE_EQUAL，并且具有对交换机的完全访问权限，不同之处在于，该交换机确保它是该角色中的唯一控制器。当控制器将角色更改为OFPCR_ROLE_MASTER时，交换机会将角色为OFPCR_ROLE_MASTER的当前控制器更改为角色OFPCR_ROLE_SLAVE，但不影响角色为OFPCR_ROLE_EQUAL的控制器。当交换机执行此类角色更改时，不会向更改其角色的控制器生成任何消息（在大多数情况下，该控制器不再可访问）。每个控制器可以发送OFPT_ROLE_REQUEST消息以将其角色传达给交换机（请参阅7.3.9），并且交换机必须记住每个控制器连接的角色。如果消息中的generation_id是最新的，则控制器可以随时更改角色（请参见下文）。角色请求消息提供了一种轻量级的机制来帮助控制器主选举过程，控制器配置其角色，并且通常仍需要在它们之间进行协调。交换机无法自行更改控制器的状态，始终会根据其中一个控制器的请求更改控制器的状态。任何从控制器或相等控制器都可以选举自己为主。交换机可以同时连接到处于相等状态的多个控制器，处于从属状态的多个控制器以及处于主状态的一个控制器。处于“主”状态（如果有）的控制器和处于“相等”状态的所有控制器都可以完全更改交换机状态，没有任何机制可以在这些控制器之间强制进行交换机分区。如果具有“主”角色的控制器必须是唯一能够在交换机上进行更改的控制器，则任何控制器都不应处于“相等”状态，而所有其他控制器都应处于“从属”状态。控制器还可以控制通过其OpenFlow通道发送哪些类型的交换机异步消息，并更改上述默认值。这是通过“异步配置”消息（请参见6.1.1）完成的，列出了需要为特定OpenFlow通道启用或过滤（参见7.3.10）的每种消息类型的所有原因。使用此功能，不同的控制器可以接收不同的通知，处于主模式的控制器可以有选择地禁用它不关心的通知，而处于从模式的控制器可以启用它想要监视的通知。



#### 将多控制器锁模块与信息备份模块结合

*需要同步的信息：拓扑信息，包含交换机、主控制器、链路以及备份控制器等信息*

***1.将有变动的拓扑同步至ryu控制器***

**问题1：**topology下面的数据出现变化时没有反应

原因：没有通过kazoo监控修改节点事件

解决：`@DataWatch(client=zk_client.zkc, path=zk_path)`中不要设置返回函数，并且在watcher函数中监听节点和节点值后面加上不断循环

**问题2：**在主模块中不能只是单纯调用子模块来实现目标

原因：如果子模块中有循环获取最新状态的操作，则不会回到主模块中。实际上，我们需要的是运行状态时子模块的实例，而`import`无法做到这一点。

解决：利用Ryu中的模块调用思路

***2.***

