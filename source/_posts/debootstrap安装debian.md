---
title: debootstrap安装debian stretch
date: 2017-05-03 20:36:42
tags: [debootstrap,debian,linux]
---
# 通过debootstrap安装debian stretch
主要介绍如何通过debootstrap安装debian。
通过debootstrap安装debian首先得有一个liveCD，我用的是ubuntu 16.04的livecd。本文大部分内容来自debian安装手册。
debian stretch安装手册链接：
debian stretch安装手册中文版：https://d-i.debian.org/manual/zh_CN.amd64/
debian stretch安装手册英文版：https://d-i.debian.org/manual/en.amd64/
如果有能力的话，我建议看英文版比较好，中文版翻译比较落后。
## 1、准备开始
首先在分区上创建文件系统，我用的是ext4文件系统。
```
mkfs.ext4 /dev/sda10
mkfs.ext4 /dev/sda8
```
初始化并激活交换分区
```
mkswap /dev/sda9
sync
swapon 
```
建立一个目录用来挂载根文件系统
```
mkdir /mnt/debian
mount /de/sda10 /mnt/debian
```
然后建立boot文件夹用来挂载boot分区
``` 
mkdir /mnt/debian/boot
mount /dev/sda8 /mnt/debian/boot
```

## 2、安装debootstrap
``` 
apt install debootstrap
```

## 3、运行 debootstrap
``` 
debootstrap --arch amd64 stretch /mnt/debian http://mirrors.ustc.edu.cn/debian
```

## 4、基本系统的配置
``` 
chroot /mnt/debian /bin/bash
```

## 5、创建设备文件
此时，/dev/ 只含有非常基本的设备文件。安装的后续步骤可能还需要更多的设备文件，所以我们安装 makedev 软件包，并创建默认的静态设备文件，使用(chroot 以后)
``` 
apt install makedev
mount none /proc -t proc
cd /dev
MAKEDEV generic
```

## 6、分区的挂载
首先新建 /etc/fstab
``` 
editor /etc/fstab
```
然后根据模板修改
``` 
# /etc/fstab: static file system information.
#
# file system    mount point   type    options                  dump pass
/dev/XXX         /             ext4    defaults                 0    1
/dev/XXX         /boot         ext4    rw,nosuid,nodev          0    2

/dev/XXX         none          swap    sw                       0    0
proc             /proc         proc    defaults                 0    0

#/dev/fd0         /media/floppy auto    noauto,rw,sync,user,exec 0    0
#/dev/cdrom       /media/cdrom  iso9660 noauto,ro,user,exec      0    0

#/dev/XXX         /tmp          ext4    rw,nosuid,nodev          0    2
#/dev/XXX         /var          ext4    rw,nosuid,nodev          0    2
#/dev/XXX         /usr          ext4    rw,nodev                 0    2
#/dev/XXX         /home         ext4    rw,nosuid,nodev          0    2
```

然后通过 mount -a 来挂载 /etc/fstab 中所标明的所有文件系统，当然也可以逐个分别地挂载这些文件系统。
``` 
mount -a
```
然后挂载 proc 和sysfs 文件系统
``` 
mount -t proc proc /proc
mount -t sysfs sysfs /sys
```
## 7、设置时区
    editor /etc/adjtime
写入以下内容
``` 
0.0 0 0.0
0
LOCAL
```
一般情况下用LOCAL，当然也可以用UTC代替LOCAL。<br>
然后设定和选择时区
``` 
dpkg-reconfigure tzdata
```
## 8、网络的配置
编辑/etc/network/interfaces，下面是模板：
``` 
######################################################################
# /etc/network/interfaces -- configuration file for ifup(8), ifdown(8)
# See the interfaces(5) manpage for information on what options are
# available.
######################################################################

# We always want the loopback interface.
#
auto lo
iface lo inet loopback

# To use dhcp:
#
# auto eth0
# iface eth0 inet dhcp

# An example static IP setup: (broadcast and gateway are optional)
#
# auto eth0
# iface eth0 inet static
#     address 192.168.0.42
#     network 192.168.0.0
#     netmask 255.255.255.0
#     broadcast 192.168.0.255
#     gateway 192.168.0.1
```

然后设定host name
``` 
echo DebianHostName > /etc/hostname
```
之后编辑hosts，并开启ipv6支持，加入以下内容：
``` 
127.0.0.1 localhost
127.0.1.1 DebianHostName

# The following lines are desirable for IPv6 capable hosts
::1     ip6-localhost ip6-loopback
fe00::0 ip6-localnet
ff00::0 ip6-mcastprefix
ff02::1 ip6-allnodes
ff02::2 ip6-allrouters
ff02::3 ip6-allhosts
```
## 9、配置 Apt
编辑/etc/apt/sources.list 文件用于安装额外的软件包，我用的是中科大的开源镜像站。
``` 
deb http://mirrors.ustc.edu.cn/debian/ stretch main contrib non-free
deb-src http://mirrors.ustc.edu.cn/debian/ stretch main contrib non-free

deb http://mirrors.ustc.edu.cn/debian/ stretch-updates main contrib non-free
deb-src http://mirrors.ustc.edu.cn/debian/ stretch-updates main contrib non-free

deb http://mirrors.ustc.edu.cn/debian-security/ stretch/updates main contrib non-free
deb-src http://mirrors.ustc.edu.cn/debian-security/ stretch/updates main contrib non-free
```
然后执行，刷新软件仓库，并安装中文字体和aptitude：
``` 
apt update
apt install fonts-noto-cjk aptitude
```
## 10、设置区域和键盘

设定区域
``` 
apt install locales
dpkg-reconfigure locales
```
配制键盘：
``` 
apt install console-setup
dpkg-reconfigure keyboard-configuration 
```

## 11、安装内核
```
apt search linux-image
apt install linux-image-arch-etc
```
我习惯上把内核头文件也装上，比如我的就是：

    apt install linux-image-4.9.0-2-amd64 linux-headers-4.9.0-2-amd64
## 12、安装引导程序
``` 
apt install grub-pc
grub-install /dev/sd8
update-grub
```
## 13、创建登录用户并设定密码
创建用户
``` 
adduser wang
```
设定root密码
``` 
passwd root
```
## 14、安装标准系统和桌面
``` 
tasksel install standard
apt install gnome-core
```
## 15、清理安装过程中下载的软件包
``` 
aptitude clean
```
