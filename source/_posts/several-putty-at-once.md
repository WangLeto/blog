---
title: 一次性打开多个 putty 连接
date: 2019-03-13 01:01:34
tags: 其他
---

最近忙毕设，经常要打开多个 putty 窗口进行远程 ssh 连接，一个个打开实在有点烦。

![](http://qiniu1.letow.top/snipaste-2019.03.13-01.01_0.jpg)

我需要一次性打开阿里云、2 个办公室连接、2 个蛋壳连接，找了一下，发现可以通过批处理脚本进行：

```powershell
# 使用 start 可以做到并行运行命令
start putty -load 蛋壳
start putty -load 蛋壳
start putty -load 办公室
start putty -load 办公室
start putty -load 阿里云
```

至于怎么样保存 ssh 连接文件，无需输入密码进行登陆，这里先挖坑，以后有空填。