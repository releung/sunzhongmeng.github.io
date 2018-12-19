---
layout: article
title: JetsonXavier NFS
category: tech
tags: Xavier
key: nfs
---
# 安装NFS软件包
在Xavier上执行以下命令：

* sudo apt-get install nfs-kernel-server #安装 NFS 服务器端
* sudo apt-get install nfs-common #安装 NFS 客户端

# 添加NFS共享目录
安装完 NFS 服务器等相关软件后，需要指定用于共享的 NFS 目录，其方法是在“/etc/exports”文件里面设置对应的目录及相应的访问权限，每一行对应一个设置。下面介绍如何添加 NFS 共享目录。在终端输入以下命令：

* sudo vim /etc/exports

在文件末尾添加以下内容：

/nfsroot *(rw,sync,no_root_squash)

修改完成后，保存并退出“/etc/exports”文件。然后新建“/nfsroot”目录，并为该目录
设置最宽松的权限：

* sudo mkdir /nfsroot
* sudo chmod -R 777 /nfsroot
* sudo chown –R nobody /nfsroot

# 启动NFS服务
执行如下命令：
* sudo /etc/init.d/nfs-kernel-server start

# 测试NFS服务器
NFS 服务启动后，可以在 Linux 主机上进行自测。测试的基本方法为：将已经设定好的
NFS 共享目录 mount（挂载）到另外一个目录下，看能否成功
假定 Linux 主机 IP 为 192.168.12.123，NFS 共享目录为/nfsroot 可使用如下命令进行测
试：
* sudo mount -t nfs 192.168.12.123:/nfsroot /mnt -o nolock

如果指令运行没有出错，则 NFS 挂载成功，在/mnt 目录下应该可以看到/nfsroot 目录下
的内容.

# Windows下挂载Xavier主机的NFS
## 启动Windows NFS 客户端服务
* 打开控制面板->程序->打开或关闭windows功能->NFS客户端，勾选NFS客户端,即开启windows NFS客户端服务.
* win+R->cmd
* mount 192.168.12.123:/nfsroot G:

成功挂载，打开我的电脑，可以看到G盘。
