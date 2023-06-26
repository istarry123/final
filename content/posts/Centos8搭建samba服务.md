---
layout: posts
title: Centos8搭建samba服务
date: 2023-06-12 16:20:34
tags:
- Linux
- samba
description: '在centos8上搭建samba服务器'
---
## samba服务
 简单的来讲 Samba 是一个基于 SMB协议（Server Messages Block，一种局域网文件共享传输协议）的文件共享服务，它可以实现 Linux 系统与 Windows 系统之间的文件共享，当然也可以用于 Linux 系统之间的文件共享，但是 Linux 系统之间的文件共享首选肯定是NFS。
  Samba 有两个主要的进程 smbd 和 nmbd。smbd 进程提供了文件和打印机共享，而 nmbd 则提供了 NetBIOS 名称服务和浏览支持，帮助 SMB 客户定位服务器，处理所有基于 UDP 的协议，使主机能浏览 Linux服务器。smbd 进程使用的是 TCP 端口139 和 445，nmbd 进程使用的是 UDP 端口138和139。

## samba服务搭建

### 安装
```
yum install samba -y
```

### 配置文件
```
vim /etc/samba/smb.conf
```
增加以下内容
```
[share] #共享名称，在原有文档下面增加自定义的内容，格式要一致
    comment = samba share #描述
    path = /samba/share #本地共享目录
    browsealbe = yes
    valid users = zhangsan,lisi #允许访问的用户
    write list = lisi #允许编辑的用户
```

### 关闭核心防火墙服务
```
setenforce 0
systemctl stop firewalld
```
`vim /etc/sysconfig/selinux` 把里边的一行改为 `SELINUX=disabled`关闭防火墙

### 根目录创建所要共享的文件（要与smb,conf设置的共享路径要一致），且放开该目录的权限，增加zhangsan和lisi两个用户
```
cd /samba
mkdir share
chmod 777 share
cd ~
useradd zhangsan
useradd lisi
smbpasswd -a zhangsan
smbpasswd -a lisi
```
还有一种创建系统用户方法
```
pdbedit -a zhangsan
pdbedit -a lisi
```

### 开启samba服务
```
systemctl start smb.service
systemctl start nmb.service
```

### 查看服务器地址
```
ifconfig
```

### 启用samba功能
打开windows功能
![1.png](https://s2.loli.net/2023/06/12/QmRM6XTsxOqShHL.png)

### 启动
win + r 打开命令行输入：\\本机的IP  即可
