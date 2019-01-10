---
layout : article 
title : Xavier指定版本内核编译
category : tech 
tags : kernel 
key : xavier
---
# Xavier指定版本内核编译流程
## 编译器前准备工作：
* 下载L4T Jetson AGX Xavier Driver Package包文件：Jetson_Linux_R31.1.0_aarch64.tbz2，到～/ 目录下，并解压。
{% hightlight %}
tar xvf Jetson_Linux_R31.1.0_aarch64.tbz2
{% endhightlight %}

{:.info}

解压后得到：Linux_for_Tegra 目录

* 下载L4T Sources文件到～/根目录下，并重命名文件夹为L4T_Sources:
mv ~/L4T\ Sources/ ~/L4T_Sources
{:.info}

进入L4T_Sources目录，并解压该目录下的文件：
cd ~/L4T_Sources
tar -xvf public_sources.tbz2

解压内核文件
cd ~/L4T_Sources/public_sources

tar -xvf kernel_src.tbz2

建立编译输出目录
cd ~/L4T_Sources/public_sources/kernel/kernel-4.9/

mkdir build


## 生成配置文件
进入内核根目录：
cd ~/L4T_Sources/public_sources/kernel/kernel-4.9
{:.info}

make ARCH=arm64 O=~/L4T_Sources/public_sources/kernel/kernel-4.9/build tegra_defconfig
{:.info}

## 编译内核和设备树
make ARCH=arm64 O=~/L4T_Sources/public_sources/kernel/kernel-4.9/build CROSS_COMPILE=~/gcc-linaro-6.4.1-2017.08-x86_64_aarch64-linux-gnu/bin/aarch64-linux-gnu- -j4

{:.info}

编译时间30分钟左右。

## 安装模块
sudo make modules_install ARCH=arm64 O=~/L4T_Sources/public_sources/kernel/kernel-4.9/build CROSS_COMPILE=~/gcc-linaro-6.4.1-2017.08-x86_64_aarch64-linux-gnu/bin/aarch64-linux-gnu- INSTALL_MOD_PATH=~/Linux_for_Tegra/rootfs

{:.info}

## 备份当前的映像
mv ~/Linux_for_Tegra/kernel/Image{,.`date "+%Y%m%d-%H%M%S"`}

 mv ~/Linux_for_Tegra/kernel/kernel_supplements.tbz2{,.`date "+%Y%m%d-%H%M%S"`}


 mv ~/Linux_for_Tegra/kernel/dtb{,.`date "+%Y%m%d-%H%M%S"`}
## 复制文件

cp ~/L4T_Sources/public_sources/kernel/kernel-4.9/build/arch/arm64/boot/Image ~/Linux_for_Tegra/kernel/

cp ~/L4T_Sources/public_sources/kernel/kernel-4.9/build/arch/arm64/boot/zImage ~/Linux_for_Tegra/kernel/

cp -r ~/L4T_Sources/public_sources/kernel/kernel-4.9/build/arch/arm64/boot/dts ~/Linux_for_Tegra/kernel/dtb



完成文件复制。

## 重新生成内核模块文件
进入内核编译输出目录

cd ~/Linux_for_Tegra/rootfs

{:.info}

tar --owner root --group root -cjf ~/Linux_for_Tegra/kernel/kernel_supplements.tbz2 lib/modules

{:.info}

## 生成JetPack映像文件
cd ~/Linux_for_Tegra/

sudo ./apply_binaries.sh
