---
layout : article
title : Xavier 内核编译
category : tech 
tags : Xavier
key : 内核编译
---
本文主要基于JetPack源码进行内核编译，也可以下载制定版本的内核源码进行编译，具体方法参照[“Specific Version Source”](https://developer.ridgerun.com/wiki/index.php?title=Xavier/JetPack_4.1/Compiling_Code/Kernel)部分.
* 在Nvidia 下载中心下载与内核版本对应的工具链（[GCC 6.4.1 Tool Chain for 64-bit Kernel](https://developer.nvidia.com/embedded/dlc/kernel-gcc-6-4-tool-chain)）,下载后解压到~/nVIDIA/toolchain 目录下。
* 设置环境变量

export CROSS_COMPILE=~/nVIDIA/toolchain/bin/aarch64-linux-gnu- 
{:.info}

* 获取源码，在~/nVIDIA/xavier/JetPack/Xavier/Linux_for_Tegra目录下运行./source_sync.sh
下载源码到~/nVIDIA/xavier/JetPack/Xavier/Linux_for_Tegra/sources,在该目录下会出现kernel文件夹，便是下载的内核代码。
[参考文章1](https://blog.csdn.net/chenjiehua123456789/article/details/77979575),[参考文章2](https://blog.csdn.net/mantis_1984/article/details/62882042)

## Kernel编译
### 添加环境变量

export KERNEL_OUT=~/nVIDIA/xavier/JetPack/Xavier/Linux_for_Tegra/sources/kernel/kernel-4.9/build
{:.info}

export KERNEL_MODULES_OUT=~/nVIDIA/xavier/JetPack/Xavier/Linux_for_Tegra/rootfs
{:.info}

[参考1](https://devtalk.nvidia.com/default/topic/1025464/jetson-tx2/what-are-the-minimum-lines-of-commands-to-update-device-tree-from-a-host-for-tx2-/)

### 编译配置文件(Create the .config file)
在~/nVIDIA/xavier/JetPack/Xavier/Linux_for_Tegra/sources/kernel/kernel-4.9目录下输入：

make ARCH=arm64 O=$KERNEL_OUT tegra_defconfig
{:.info}

执行结果如图所示:

![make tegra_defconfig](/resource/2018-12-27-nvidia-xavier-build-kernel/make-tegra_defconfig.png)

### 编译内核和设备树(Build the kernel and DTBs)
在当前目录下继续执行：

make ARCH=arm64 O=$KERNEL_OUT CROSS_COMPILE=${CROSS_COMPILE} -j4
{:.info}

开始编译，编译时间与主机的配置有关，不同机器编译时间也不一样，以我的i3笔记本为例4G内存，Ubuntu16.04系统，编译大概30分钟左右。

### 加载模块(Install modules in temporal location)
在当前目录下继续执行:

sudo make modules_install ARCH=arm64 O=$KERNEL_OUT CROSS_COMPIL
E=${CROSS_COMPILE} INSTALL_MOD_PATH=$KERNEL_MODULES_OUT
{:.info}

### 备份当前镜像(Make a backup of the current image)
把当前的镜像备份成：文件名+日期时间的格式.

mv ~/nVIDIA/xavier/JetPack/Xavier/Linux_for_Tegra/kernel/Image{,.`date "+%Y_%m_%d_%H_%M_%S"`}
{:.info}

 mv ~/nVIDIA/xavier/JetPack/Xavier/Linux_for_Tegra/kernel/zImage{,.`date "+%Y_    %m_%d_%H_%M_%S"`}
{:.info}

mv ~/nVIDIA/xavier/JetPack/Xavier/Linux_for_Tegra/kernel/kernel_supplements.tbz2{,.`date "+%Y_%m_%d_%H_%M_%S"`}
{:.info}

mv ~/nVIDIA/xavier/JetPack/Xavier/Linux_for_Tegra/kernel/dtb{,.`date "+%Y_%m_%d_%H_%M_%S"`}
{:.info}

但是实际我的目录下并没有zImage这个文件。

### 部署二进制文件(Deploy binarier in their required locations)
进入到编译输出目录：

cd $KERNEL_OUT
{:.info}

进入到~/nVIDIA/xavier/JetPack/Xavier/Linux_for_Tegra/sources/kernel/kernel-4.9/build$ 目录下。

执行复制命令，拷贝二进制文件。

cp ./arch/arm64/boot/Image ~/nVIDIA/xavier/JetPack/Xavier/Linux_for_Tegra/kernel/
{:.info}

cp ./arch/arm64/boot/zImage ~/nVIDIA/xavier/JetPack/Xavier/Linux_for_Tegra/kernel/
{:.info}

cp -r ./arch/arm64/boot/dts ~/nVIDIA/xavier/JetPack/Xavier/Linux_for_Tegra/kernel/
{:.info}

### 重新生成内核模块追加文件(Regenerate kernel modules supplement file)
切换到~/nVIDIA/xavier/JetPack/Xavier/Linux_for_Tegra/rootfs目录

cd $KERNEL_MODULES_OUT
{:.info}

执行如下命令：

tar --owner root --group root -cjf ~/nVIDIA/xavier/JetPack/Xavier/Linux_for_Tegro/kernel/kernel_supplements.tbz2 lib/modules
{:.info}

### 生成JetPack映像(Generate JetPack image)
切换到~/nVIDIA/xavier/JetPack/Xavier/Linux_for_Tegra/目录下，执行如下命令：

cd ~/nVIDIA/xavier/JetPack/Xavier/Linux_for_Tegra
{:.info}

sudo ./apply_binaries.sh
{:.info}



