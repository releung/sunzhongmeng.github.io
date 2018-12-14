---
layout: article
title: Nvidia TX2 CAN测试
category: tech
tags: tx2
key: can
---
TX2 IP:192.168.8.115 SSH PORT:22
User:nvidia
Pwr:nvidia

https://blog.csdn.net/xuezhunzhen9743/article/details/81877757

运行脚本：can.sh
{% highlight c %}
sudo ip link set can1 type can bitrate 500000

sudo ip link set up can1
{% highlight %}
发送数据：https://sgframework.readthedocs.io/en/latest/cantutorial.html

cansend can1 001#11223344aabbccdd


https://en.wikipedia.org/wiki/SocketCAN


https://blog.csdn.net/jirryzhang/article/details/79417986