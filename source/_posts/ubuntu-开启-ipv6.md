---
title: ubuntu 使用 ipv6 隧道
date: 2017-11-05 20:12:01
tags: linux
---

因为宿舍中没有方便的 ipv6，即使是使用 xx-net 也觉得力不从心，于是上网找了一下关于 linux 开启 ipv6 的方法。

主要借鉴了下面教程：

[Ubuntu开启IPv6](http://www.linuxidc.com/Linux/2013-03/80479.htm)

[ubuntu 看youtube 的方法](http://blog.sina.com.cn/s/blog_509336720100xf32.html)

[ubuntu16.04使用ipv6](http://blog.csdn.net/scylhy/article/details/72699166)

<!--more-->

1. 安装 miredo 

   ```shell
   sudo apt install miredo
   ```

2. 更改 ipv6 地址配置

   ```shell
   sudo vim /etc/sysctl.d/10-ipv6-privacy.conf
   ```

   将 `net.ipv6.conf.default.use_tempaddr`  后的数字更改为 0。

3. 重启网络设置

   ```shell
   sudo invoke-rc.d networking restart
   ```



在重启系统之后，需要再次启动 miredo：

```shell
sudo service miredo start
```



以上是我自己在 linux 上启用 ipv6 的过程，如果有其他的问题，可以参考给出的三篇文章。

