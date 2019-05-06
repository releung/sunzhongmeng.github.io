---
layout: article
title: Xavier上的第一个I2C驱动 
tags: xavier,i2c,driver
key: i2c,driver
---
按照《Linux设备驱动开发详解-基于最新的Linux 4.0内核》15.6小节的AT24xx驱动，在Xavier上实现一个简单的I2C驱动模型 
# 设备驱动
驱动端程序
``` c
#include <linux/kernel.h>
#include <linux/module.h>
#include <linux/platform_device.h>
#include <linux/i2c.h>
#include <linux/err.h>
#include <linux/regmap.h>
#include <linux/slab.h>

static const struct i2c_device_id ds90ub96x_id_table[] = {
		{ "ds90ub96x", 0x30},
			{}
};
MODULE_DEVICE_TABLE(i2c,ds90ub96x_id_table);	


static int ds90ub96x_probe(struct i2c_client *client,
						const struct i2c_device_id *id)
{
		printk("%s %s %d\n", __FILE__, __FUNCTION__, __LINE__);
			return 0;
}

static int ds90ub96x_remove(struct i2c_client *client)
{
		printk("%s %s %d\n", __FILE__, __FUNCTION__, __LINE__);
			return 0;
}


/* 1.分配/设置 i2c_driver */
static struct i2c_driver ds90ub96x_driver = {
	.driver	= {
			.name	= "ds90ub96x",
			.owner	= THIS_MODULE,
		},
		.probe	= ds90ub96x_probe,
		.remove	= ds90ub96x_remove,
		.id_table= ds90ub96x_id_table,
};

static int __init ds90ub96x_drv_init(void)
{
		return i2c_add_driver(&ds90ub96x_driver);
}
module_init(ds90ub96x_drv_init);

static void __exit ds90ub96x_drv_exit(void)
{
		i2c_del_driver(&ds90ub96x_driver);
}
module_exit(ds90ub96x_drv_exit);
MODULE_LICENSE("GPL");

```

# 驱动端代码
``` c
nclude <linux/kernel.h>
#include <linux/module.h>
#include <linux/platform_device.h>
#include <linux/i2c.h>
#include <linux/err.h>
#include <linux/regmap.h>
#include <linux/slab.h>

static struct i2c_client *ds90ub96x_client;

static const unsigned short addr_list[] = { 0x30, I2C_CLIENT_END };
static int ds90ub96x_dev_init(void)
{
	struct i2c_adapter *i2c_adap;
	struct i2c_board_info ds90ub96x_info;

	memset(&ds90ub96x_info, 0, sizeof(struct i2c_board_info));	
		strlcpy(ds90ub96x_info.type, "ds90ub96x", I2C_NAME_SIZE);

	i2c_adap = i2c_get_adapter(2);/*ds90ub964使用Xavier上的I2C3(所有索引号为2)*/
	ds90ub96x_client = i2c_new_probed_device(i2c_adap, &ds90ub96x_info, addr_list, NULL);
	i2c_put_adapter(i2c_adap);

	if (ds90ub96x_client)
		return 0;
	else
		return -ENODEV;
}

```

# makefile代码
```

kernel_modules:
        make -C /usr/src/linux-headers-4.9.140-tegra-ubuntu18.04_aarch64/kernel-4.9/  M=$(CURDIR) modules

clean:
        make -C /usr/src/linux-headers-4.9.140-tegra-ubuntu18.04_aarch64/kernel-4.9/ M=$(CURDIR) clean
        rm -rf modules.order
obj-m += ds90ub96x_dev.o
obj-m += ds90ub96x_drv.o


```
# 编译 
进入nVIDIA agx-Xavier 开发板，将驱动代码和amkefile上传到开发板上，并在终端执行：` make `,产生模块文件。 

# 加载模块
在终端执行： 
```
sudo insmod ds90ub96x_drv.ko 
sudo insmod ds90ub96x_dev.ko

```
然后执行：` lsmod `,可以看到` ds90ub96x `驱动已经加载，在终端执行：` dmesg |grep ds90ub96x_* ` ,可以看到模块加载的时候打印的信息。 
在终端执行：` sudo rmmod ds90ub96x_dev ` 卸载模块，在终端执行` dmesg |grep ds90ub96x_* `,可以看到模块卸载时打印的信息。 

# 注意事项
Makefile文件参考的宋宝华老师《Linux设备驱动开发详解-基于最新的Linux 4.0内核》4.9小节的Makefile文件，但模块的路径无法使用` KVERS `指定，这个目前还不知道是什么原因导致的，可能与Xavier的系统镜像有关系，在编译Xavier的kernel和module的时候设置的` build `与书中的有所不同，这个问题有待深究。 
# 参考链接
* [本文的代码](https://github.com/sunzhongmeng/nvidia-agx-xavier-training/tree/master/1.first-i2c-driver)
