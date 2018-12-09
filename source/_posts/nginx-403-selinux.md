---
title: SELinux 引起的 Nginx 403 forbidden
date: 2018-04-04 01:56:48
tags: [Nginx, website, linux]
---

今天（昨晚）想要把博客从阿里云迁移到 Digital Ocean 上，但是 Nginx 死活出错：403 forbidden。

改文件夹用户组、改 Nginx 运行用户、777 授权都试了一遍，全都没有用。

后来发现是一个叫做 SELinux 的进程在保护我的系统安全……

<!--more-->

查看 selinux 状态：

```shell
/user/sbin/sestatus
```

打印出的第一行：

```
SELinux status:                  enabled
```

进行修改：`vim /etc/selinux/config`

```
SELINUX=disabled
```

`reboot` 重启系统。



参考：

[Nginx 403 forbidden for all files](https://stackoverflow.com/a/26228202/7526989 )

[解决Nginx出现403 forbidden (13: Permission denied)报错的三种办法](http://www.shuchengxian.com/article/658.html)

![](http://qiniu1.letow.top/sorrow-in-smile.jpg)
