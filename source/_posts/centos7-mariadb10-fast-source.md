---
title: centos7 配置 MariaDB 10 软件源
date: 2017-12-30 03:38:10
tags: [linux]
---


使用阿里云服务器上的 centos7 安装 MariaDB 10 的时候虐心了……今天抱着试试看的想法找了一下，发现真的有国内快速源。（我好菜）

**Ps**: 顺便安利这篇文章，分分钟教你~~怎么做人(误)~~怎么在 centOS 环境上搭建 Joomla 网站——[Install Joomla! on CentOS 7](http://srnqdh83.biz/index.php/1/68-captcha)。

![](http://qiniu1.letow.top/centos7-mariadb10-fast.jpg)

## 啊，Joomla 是什么？

>Joomla是一个获奖的文章管理系统（CMS），它可以使用户建立网站和功能强大的在线应用。 在许多方面，包括其易于使用和可扩展性，使Joomla成为了最流行的Web建站软件。

参见 [Joomla 官网](https://docs.joomla.org/Joomla_info_page/zh-CN)。

<!--more-->

言归正传…

## 配置 centOS 7 的 MariaDB 10 源

编辑文件：

```shell
vim /etc/yum.repos.d/MariaDB10.repo
```

文件内容：

```
[mariadb]
name = MariaDB
baseurl = https://mirrors.tuna.tsinghua.edu.cn/mariadb/yum/10.1/centos7-amd64
gpgkey = https://mirrors.tuna.tsinghua.edu.cn/mariadb/yum//RPM-GPG-KEY-MariaDB
gpgcheck=1
```

注意自己的 mariadb 版本，我的是 10.1，如果需要 10.2 的源，请使用

```
[mariadb]
name = MariaDB
baseurl = http://mirrors.ustc.edu.cn/mariadb/yum/10.2/centos7-amd64/
gpgkey=http://mirrors.ustc.edu.cn/mariadb/yum/RPM-GPG-KEY-MariaDB
gpgcheck=1
```

<iframe frameborder="no" border="0" marginwidth="0" marginheight="0" width=330 height=86 src="//music.163.com/outchain/player?type=2&id=108257&auto=1&height=66"></iframe>

