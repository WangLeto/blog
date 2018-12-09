---
title: 借助海外服务器+七牛云加速文件下载
date: 2018-05-29 16:42:57
tags: 其他
---

如果遇到国外网站上的资源，大型文件如 Android studio 之类，即使使用了 vpn 也不见得能以较快速度下载，网络条件较差时尤甚。

如果按照以下方法下载，能一定程度上解决这个问题：

- 在海外服务器上下载所需文件
- 传输到七牛云对应的储存区域
- 使用多线程下载工具下载到本地

<!--more-->

## 准备工作

确保你的海外服务器与七牛云的储存区域尽可能一致，比如我使用了 digital ocean 在新加坡的服务器，七牛云对应开启了一个东南亚区域的储存区域。其余可能的组合比如美国的服务器+七牛云北美位置的储存区域。

如果找不到好的位置组合，可以查看你的服务器提供商是否允许更换服务器位置（通常这是被允许的）。



## 安装七牛云命令行客户端 `qshell`

文档见 [命令行工具(qshell)](https://developer.qiniu.com/kodo/tools/1302/qshell)，`qshell` 是七牛云为开发者提供的一个命令行客户端，方便用户在终端下仅文件的上传下载等管理。

按照文档说明，下载 `qshell` 二进制执行文件到本地：

```shell
mkdir qshell	# 创建 qshell 文件夹
cd qshell		# 进入该文件夹
wget http://devtools.qiniu.com/qshell-v2.1.8.zip?ref=developer.qiniu.com
```

linux 下使用 `pwd` （print working directory）命令打印当前目录：

```shell
[root@leto-revive qshell]# pwd
/root/qshll
```

获知当前目录，将 `qshell` 目录添加到环境变量：

```shell
export PATH=$PATH:/root/qshell
```

这样你可以在任何位置执行 `qshell` 的命令。

### 关于执行权限

> 如果在Linux或者Mac系统上遇到`Permission Denied`的错误，请使用命令`chmod +x qshell`来为文件添加可执行权限。这里的`qshell`是上面文件重命名之后的简写。



## 进行 `qshell` 的用户设置

首先登录七牛云的个人中心，在密钥管理页面查看你的公私钥：[查看页面](https://portal.qiniu.com/user/key)。

假设你的公钥（AK）是 `gongyao`，私钥（SK）是 `siyao`，在终端执行命令添加公私钥：

```shell
qshell account gongyao siyao
```

此时即完成了用户配置，可以尝试使用命令查看你的储存区域（bucket）：

```shell
[root@leto-revive down]# qshell buckets
2018/05/29 16:29:04 [I] Load account from /root/.qshell/account.json
bucket1
bucket2
```

### 补充说明

如果你使用命令 `qshell -h` 打印可用的命令，查看 bucket 的命令是 `qshell listbucket`。但是实际上这条命令并不能执行。



## 下载文件到服务器

`wget` 命令是 linux 上非常好用的下载命令。

比如我想要下载这个页面 [kNN/机器学习实战（中文版+英文版+源代码](https://github.com/frankstar007/kNN/tree/master/%E6%9C%BA%E5%99%A8%E5%AD%A6%E4%B9%A0%E5%AE%9E%E6%88%98%EF%BC%88%E4%B8%AD%E6%96%87%E7%89%88%2B%E8%8B%B1%E6%96%87%E7%89%88%2B%E6%BA%90%E4%BB%A3%E7%A0%81%EF%BC%89) 中的 pdf 文件。首先右键复制目标文件的链接，然后在服务器上使用如下命令进行下载：

```shell
wget https://github.com/frankstar007/kNN/blob/master/%E6%9C%BA%E5%99%A8%E5%AD%A6%E4%B9%A0%E5%AE%9E%E6%88%98%EF%BC%88%E4%B8%AD%E6%96%87%E7%89%88%2B%E8%8B%B1%E6%96%87%E7%89%88%2B%E6%BA%90%E4%BB%A3%E7%A0%81%EF%BC%89/%E6%9C%BA%E5%99%A8%E5%AD%A6%E4%B9%A0%E5%AE%9E%E6%88%98.pdf
```

digital ocean 的下载速度非常之快，可以说瞬间完成。



## 上传文件到七牛云

文件下载完成后，即可上传到云端。

使用 `qshell rput` 命令进行推送。参数列表为：

```shell
Usage: qshell rput <Bucket> <Key> <LocalFile> [<Overwrite>] [<MimeType>] [<UpHost>] [<FileType>]
```

必须填写的是前三个参数：

- Bucket：选定的储存区域
- Key: 上传后文件的名称
- LocalFile: 需要上传的文件路径

因此想要上传文件该文件，使用的命令为：

```shell
qshell rput yourbucket name.pdf 机器学习实战.pdf
```

很快就完成了。



## 下载到本机

进行了这么多麻烦的操作，我们最终可以下载文件到本地了。

登录七牛云的[储存空间](https://portal.qiniu.com/bucket/)，打开你上传的目标储存区域，在文件列表中找到刚刚上传的文件，复制文件外链。

然后打开迅雷等多线程下载工具，新建下载任务，粘贴这个外链，即可进行下载。

![](http://qiniu1.letow.top/snipaste%202018.05.29-16.41.jpg)

