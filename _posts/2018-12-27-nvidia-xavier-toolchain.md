---
layout : article
title : Xavier Tool Chain
category : tech 
tags : Xavier
key : ToolChain
---

* 在Nvidia 下载中心下载与内核版本对应的工具链（[GCC 6.4.1 Tool Chain for 64-bit Kernel](https://developer.nvidia.com/embedded/dlc/kernel-gcc-6-4-tool-chain)）,下载后解压到~/Documents/Nvidia 目录下。
* 设置环境变量

export CROSS_COMPILE=~/Documents/Nvidia/gcc-linaro-6.4.1-2017.08-x86_64_aarch64-linux-gnu/bin/aarch64-linux-gnu- 
{:.info}

* 获取源码，在~/Documents/Nvidia/Linux_for_Tegra目录下运行./source_sync.sh[参考文章1](https://blog.csdn.net/chenjiehua123456789/article/details/77979575),[参考文章2](https://blog.csdn.net/mantis_1984/article/details/62882042)

## Kernel编译
* 添加环境变量

export KERNEL_OUT=~/Documents/Nvidia/Linux_for_Tegra/kernel/linux-headers-4.9.108-tegra/kernel-4.9/build
{:.info}

export KERNEL_MODULES_OUT=~/Documents/Nvidia/Linux_for_Tegra/rootfs
{:.info}



