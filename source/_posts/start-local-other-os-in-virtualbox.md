---
title: Linux下 VirtualBox 启动本地磁盘上的其它系统
date: 2021-03-16 22:30:00
tags: 虚拟化，VirtualBox
categories: 虚拟机，Linux
show: hide
---
---
# 使用 VirtualBox 启动本地磁盘上的其它系统
<!-- more -->
## Virtualbox直接运行物理分区的系统
1. 创建虚拟硬盘
首先查看下分区情况，我的如下
```
/dev/nvme0n1p1      2048   2099199   2097152    1G EFI 系统
/dev/nvme0n1p2   2099200   3147775   1048576  512M Microsoft 保留
/dev/nvme0n1p3   3147776   4196351   1048576  512M Windows 恢复环境
/dev/nvme0n1p4   4196352 318769151 314572800  150G Microsoft 基本数据
```

``` bash
#将当前用户加入到vboxusers,disk用户组
sudo usermod -G vboxusers,disk -a `whoami`
VBoxManage internalcommands createrawvmdk -filename windows.vmdk -rawdisk /dev/nvme0n1 -partitions 1,2,3,4 -relative
```
现在你得到了 ` windows.vmdk ` 和 ` windows-pt.vmdk ` 的虚拟磁盘
2. 加入虚拟磁盘到Virtualbox
移动 虚拟磁盘文件到 ` /opt/virtualbox/Windows ` 下，在Virtualbox中注册 管理--->虚拟介质管理---> 注册 windwos.vmdk
3. 启动虚拟机
注意下如果是EFI引导，记得虚拟机选项勾选，就ok了，同理windows下的D盘，E盘也可按照相同方法添加，不过多叙述！

参考：

https://superuser.com/questions/495025/use-physical-harddisk-in-virtual-box
