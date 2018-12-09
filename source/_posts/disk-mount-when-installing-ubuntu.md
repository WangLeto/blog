---
title: 安装 Ubuntu 时的分区挂载问题
date: 2017-11-05 20:59:26
tags: linux
---

最近频繁安装 Ubuntu，有一个步骤经常困扰我——就是如何进行分区的挂载问题。

![](http://qiniu1.letow.top/ubuntu-install-2017-11-06-2.30.jpg)

网络上有种流行的方式：给 boot 分配 100~200M 的一个区域，给 swap 分配 1~2G 的区域，然后剩余的交给 / 分区。

这种分区方式，说实话，给我造成了明显的问题。

<!--more-->

由于某次错误选择软件源的问题，造成了启用 Ubuntu 开发者选项中“提前释出的更新”。至于之后出现了网络驱动废掉的问题并在一系列脑残操作之后不得不再次重装系统的平淡无奇的故事没什么好讲的，某天我突然发现 boot 分区容量告急，查看发现安装了 3 或更多个系统内核，boot 分区的 200M 空间已经消耗殆尽。

很明显，当我们看到网上教程中提到物理内存时用“1到2G”“大内存”时就该警惕起来：这是一篇10年或更早之前的教程，里面的内容早就落后于电脑硬件和操作系统太多了。

再次安装 Ubuntu 时，我没有再次将 boot 单独分区，而是只进行了 swap 和 / 挂载分区。不过，就网上流传的讨论来看，对于**作为服务器的 linux 系统，保留 boot 分区似乎是有必要**的，这一点请自行斟酌。

关于 swap 分区，linux 和 windows 下的虚拟内存有不尽相同。详细请参考：

[在物理内存充足的情况下，是否需要禁用虚拟内存？](https://www.zhihu.com/question/20728404)

[[操作系统]虚拟内存和swap分区](http://blog.csdn.net/lady_lili/article/details/52624242)

[Should my swap size be zero or am I misunderstanding the output?](https://askubuntu.com/questions/646370/should-my-swap-size-be-zero-or-am-i-misunderstanding-the-output)

原来，在 Linux 上，只有内存用尽时才会去使用交换空间，以便尽可能发挥内存作用。而在 Windows 上，交换空间从来没有空闲的时候。这样看来，如果对自己的内存大小有足够信心，即使不设置 swap 分区也是可以的。
