---
layout: article
title: Nvidia TX2 CAN测试
category: tech
tags: tx2
key: can
---
TX2设置信息：
* IP:192.168.8.115
* SSH PORT:22
* User:nvidia
* Pwr:nvidia
[ 参考文章](https://blog.csdn.net/xuezhunzhen9743/article/details/81877757)

运行脚本：
./can.sh

设置波特率：
sudo ip link set can1 type can bitrate 500000

使能can1：
sudo ip link set up can1
[发送数据参考文章](https://sgframework.readthedocs.io/en/latest/cantutorial.html)

发送数据：
cansend can1 001#11223344aabbccdd

[参考文章1](https://en.wikipedia.org/wiki/SocketCAN)

[参考文章2](https://blog.csdn.net/jirryzhang/article/details/79417986)

