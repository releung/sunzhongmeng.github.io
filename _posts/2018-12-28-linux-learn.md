---
layout : article
title : Linux学习记录
category : tech
tags : linux 
key : linux
---
# Linux下解压缩
Linux下常见的压缩格式有5种：zip,tar.gz,tar.bz2,tar.xz,tar.Z。
* fileneme.zip的解压：

unzip filename.zip
{:.info}
* filename.tar.gz的解压：

tar -zxvf filename.tar.gz
{:.info}
其中zxvf含义如下：

z:gzip    压缩格式

x:extract 解压

v:verbose 详细信息

f:file(file=archieve) 文件
* filename.tar.bz2的解压：

tar -jxvf filename.tar.bz2
{:.info}
j:bzip2 压缩格式
* filename.tar.xz的解压：

tar -Jxvf filename.tar.xz
{:.info}
* filename.tar.Z的解压：

tar -Zxvf filename.tar.Z

* 关于tar的详细命令可以使用以下命令查看

tar --help
{:.info}
从1.15版本开始tar就可以自动识别压缩的格式，因此不需要区分压缩格式就能够正确解压：

tar -xvf filename.tar.gz
{:.info}
[本文来源](https://www.cnblogs.com/cursorhu/p/5891699.html)
