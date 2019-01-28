title: px4 matlab package
date: 2015-12-07 11:52:45
tags: pixhawk
---
[Pixhawk PX4 Support from Embedded Coder](http://cn.mathworks.com/hardware-support/pixhawk.html)
这个安装包貌似只支持matlab 2014b 或者 2015a 64位

* 先安装matlab 2014b 或者 2015a 64位

* 下载px4 toolchain 

> 因为这个package会对toolchain做一些修改。所以在安装这个matlab前要先装好toolchain

* 安装这个包

<!--more-->
然后你的simulink里会出现![](http://7xk7fp.com1.z0.glb.clouddn.com/simulink.png)

详细教程下载[官方文档](http://www.mathworks.com/hardware-support/files/dds-blockset-support-package-user-guide.pdf)

使用之前应该要有源码。并且应该被初次编译过。

可以从git上直接clone：[Firmware](https://github.com/PX4)

也可以直接从PX4 Toolchain里有一个选项：PX4 Software Download

点击这个后他会自动帮你从Github上下载Firmware Bootloader libopencm3三个文件夹到你安装PX4 Toolchain(比如D:/PX4)的路径里。

下载完成后要用PX4 Console进行编译

```
make archives
make all
```
all 目标在我上一篇的makefile解析里有。可以看出分别都是那些任务
第一次编译要久一点。

