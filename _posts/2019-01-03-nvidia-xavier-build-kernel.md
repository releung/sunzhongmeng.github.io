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
{% highlight %}
tar xvf Jetson_Linux_R31.1.0_aarch64.tbz2
{% endhighlight %}


解压后得到：Linux_for_Tegra 目录

* 下载L4T Sources文件到～/根目录下，并重命名文件夹为L4T_Sources:
{% highlight %}
mv ~/L4T\ Sources/ ~/L4T_Sources
{% endhighlight %}

进入L4T_Sources目录，并解压该目录下的文件：
{% highlight %}
cd ~/L4T_Sources
tar -xvf public_sources.tbz2
{% endhighlight %}

解压内核文件
{% highlight %}
cd ~/L4T_Sources/public_sources
tar -xvf kernel_src.tbz2
{% endhighlight %}

建立编译输出目录
{% highlight %}
cd ~/L4T_Sources/public_sources/kernel/kernel-4.9/
mkdir build
{% endhighlight %}


## 生成配置文件
进入内核根目录：
{% highlight %}
cd ~/L4T_Sources/public_sources/kernel/kernel-4.9
make ARCH=arm64 O=~/L4T_Sources/public_sources/kernel/kernel-4.9/build tegra_defconfig
{% endhighlight %}

## 编译内核和设备树
{% highlight %}
cd ~/L4T_Sources/public_sources/kernel/kernel-4.9
make ARCH=arm64 O=~/L4T_Sources/public_sources/kernel/kernel-4.9/build CROSS_COMPILE=~/gcc-linaro-6.4.1-2017.08-x86_64_aarch64-linux-gnu/bin/aarch64-linux-gnu- -j4
{% endhighlight %}


编译时间30分钟左右。

## 安装模块
{% highlight %}
sudo make modules_install ARCH=arm64 O=~/L4T_Sources/public_sources/kernel/kernel-4.9/build CROSS_COMPILE=~/gcc-linaro-6.4.1-2017.08-x86_64_aarch64-linux-gnu/bin/aarch64-linux-gnu- INSTALL_MOD_PATH=~/Linux_for_Tegra/rootfs
{% endhighlight %}

## 备份当前的映像
{% highlight %}
mv ~/Linux_for_Tegra/kernel/Image{,.`date "+%Y%m%d-%H%M%S"`}
mv ~/Linux_for_Tegra/kernel/kernel_supplements.tbz2{,.`date "+%Y%m%d-%H%M%S"`}
mv ~/Linux_for_Tegra/kernel/dtb{,.`date "+%Y%m%d-%H%M%S"`}
{% endhighlight %}
## 复制文件
{% highlight %}
cp ~/L4T_Sources/public_sources/kernel/kernel-4.9/build/arch/arm64/boot/Image ~/Linux_for_Tegra/kernel/
cp ~/L4T_Sources/public_sources/kernel/kernel-4.9/build/arch/arm64/boot/zImage ~/Linux_for_Tegra/kernel/
cp -r ~/L4T_Sources/public_sources/kernel/kernel-4.9/build/arch/arm64/boot/dts ~/Linux_for_Tegra/kernel/dtb
{% endhighlight %}

完成文件复制。

## 重新生成内核模块文件
进入内核编译输出目录
{% highlight %}
cd ~/Linux_for_Tegra/rootfs
tar --owner root --group root -cjf ~/Linux_for_Tegra/kernel/kernel_supplements.tbz2 lib/modules
cd ~/Linux_for_Tegra/
sudo ./apply_binaries.sh
{% endhighlight %}
