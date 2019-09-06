---
title: 阿里云安装Arch Linux
date: 2019-09-06 20:54:42
tags:
---

# 1. 前记
一直在想入手一台国内服务器,做流量中继
9月3日,阿里云活动好像有漏洞,免费拿到了200元的免门槛代金券,果断入手一台
<!-- more -->
# 2. 闲聊
1. 作为一个Archer怎么能忍受Linux发行版不是Arch呢?以及不想后台有阿里系的毒瘤监控,想要一个干干静静的系统,开始了我的折腾
2. 使用著名的[vps2arch](),[digitalocean-debian-to-arch]()项目都不行,脚本根本跑不起来,不知道阿里做了啥操作
3. 常规操作 阅读[凤凰酱的文章](https://blog.phoenixlzx.com/2018/02/01/build-archlinux-image-template-for-aliyun/),按照阿里要求制作启动镜像,在用oss上传,啊 ,感觉步骤真繁琐,而且阿里肯定要在系统里塞私货 
4. 然后我在[archlinux-cn](https://t.me/archlinuxcn_group)group提问,非常感谢[Whyme Lyu]的回答,提供一种全新思路,本文基于谈话和[文章](https://www.jianshu.com/p/b98412b5fe69)补充整理而成
5. 大致流程: 
            1. debian 改 grub.cfg 加 arch linux iso
            2. 启动arch iso载入到内存中
            3. 使用内存中的系统格式化物理磁盘
            4. 安装Arch linux    

# 3. 开始操作
`    默认系统: debain 9  `

## 1. 准备操作
1. 阿里云的网络名称为专有网络,实际只是在路由器上做了公私网映射而已,方便他们抓取流量分析,美名其曰方便软件控制,而且不给公网ipv6地址 黑脸,jpg (发工单也没用
2. okok,不说了,我们需要做的就是搞定内网网络就OK了. 使用 ` ip addr ` 查看内网地址和子网掩码,使用 ` ip route ` 查看网关地址 ### 敲黑板,这些信息之后需要使用,需要记录

## 2. 开始
1. 下载Arch iso到根目录
```
cd /
wget https://mirrors.tuna.tsinghua.edu.cn/archlinux/iso/latest/archlinux-2019.09.01-x86_64.iso  #下载最新Arch操作系统
```

2. 硬核,直接改 ` /boot/grub/grub.cfg `
```
set timeout=60
menuentry 'ArchISO' --class iso {
  set isofile=/archlinux-2019.09.01-x86_64.iso
  loopback loop0 $isofile
  #archisolabel设置archiso文件驻留的文件系统标签。
  #img_dev指明archiso文件驻留的设备
  #img_loop是archiso文件在img_dev里的绝对位置
  linux (loop0)/arch/boot/x86_64/vmlinuz archisolabel=ARCH20181201 img_dev=/dev/vda1 img_loop=$isofile
  initrd (loop0)/arch/boot/x86_64/archiso.img
}
```
这个时候Arch iso就直接添加到启动项了
现在控制台vnc连接服务器,现在已经启动arch iso了

3. 格盘,装Arch
` df -h ` 看下挂载情况
你会发现原来的物理磁盘已经挂载到了 ` /run/archiso/img_dev `
但是现在这个是只读的,需要使用 ` mount -o remount,rw /run/archiso/img_dev ` 重新可读挂载下,现在操作系统在内存上,所以可以放心的使用 ` rm -rf /run/archiso/img_dev/* `了,等执行完成进去 `ls -al`看看,完全干净,nice!
现在 常规操作 ` pacstrap /run/archiso/img_dev base base-devel `
后续 arch-chroot .....不在详细说明!
另外阿里虚拟机系统是BIOS方式引导,grub如何安装,自行参照arch wiki
多提一句 arch-chroot 进去之后最好装上 ` rng-tools `并` systemctl enable rngd --now ` 增加熵值,不然你会发现重启系统后直接 ` ssh  `连接不上,原因就是随机数池耗尽,更多细节参加 [Random number generation](https://wiki.archlinux.org/index.php/Random_number_generation#Alternatives)

4. 重启后,Arch linux真香,现在修复网络,还记得前面的重点吗?
使用netctl配置网络,就是添加内网,OK之后,重启netctl服务,OK,现在使用ssh直接连接,你就得到了一个干净的Arch!



# 转载文章
[替换云服务器操作系统为Archlinux](https://www.jianshu.com/p/b98412b5fe69)