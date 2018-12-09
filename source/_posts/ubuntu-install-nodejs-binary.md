---
title: ubuntu 使用二进制文件安装 nodejs 8
date: 2018-08-12 11:57:48
tags: [Linux]
---


比起一年前的小白，现在重新上手 Ubuntu，各种操作到是逻辑清晰了不少。

## 下载解压

本来按照官网给出的 [Installing Node.js via package manager](https://nodejs.org/en/download/package-manager/#debian-and-ubuntu-based-linux-distributions) 方法来做，但是总出错，因此索性使用编译好的二进制文件。比起包管理工具安装似乎还要快些。

[下载列表](https://nodejs.org/en/download/)，我选择了 `Linux Binaries (x86/x64)` 64 位版本。

下载完成后，使用归档管理器解压出来，发现里面的结构倒也简单，我们需要的可执行文件 `node`、`npm` 都在其中的 `bin` 文件夹下。

如果需要，可以将文件夹移动到其他路径，我将其放置于 `/usr/local/lib/` 路径下（python 也在这里）。

```bash
$ sudo mv node-v8.11.3-linux-x64 /usr/local/lib/nodejs
```

这样，我将文件夹名称也改为了 `nodejs`。

<!--more-->

## 导出环境变量

导出环境变量的意义和在 Windows 上一样，都是为了终端能在任何路径下直接使用程序名称来执行。

导出的语句如下：

```bash
$ export PATH=$PATH:/usr/local/lib/nodejs/bin
```

但是如果直接这样执行一次，环境变量的有效期只能维持到本次对话结束，也就是终端再次打开时就会失效。

为了方便使用，我们需要让其更加持久～

在 `/home/用户名` 路径下，编辑 `.profile` 文件：

```bash
$ vim ~/.profile

export PATH=$PATH:/usr/local/lib/nodejs/bin
```

输入 `:wq` 保存退出。

因为该文件是在启动后加载，因此运行 `init 6` 重启。

## 最后

再次开机时，在终端运行 `node -v`，此时可以看到所安装的 node 版本了。

```bash
$ node -v
v8.11.3
```

如果喜欢使用 `nodejs` 命令而不是 `node` 命令，可以直接建立软连接：

```bash
$ sudo ln -s /usr/local/lib/nodejs/bin/node /usr/local/lib/nodejs/bin/nodejs
```

这样输入 `nodejs` 也可以运行。



上述方法可以用于任何使用二进制文件安装的程序，比如 jre 等。
