---
title:  使用 apt-fast 让 Ubuntu 软件安装飞起
date: 2018-11-10 15:51:00
tags: linux
---

刚刚被100k/s的 apt 下载速度惊到了，于是试着搜索了一下 “apt multi threads”，没想到真的让我找到了一个叫做 “apt-fast” 的软件。

```shell
$ sudo add-apt-repository ppa:saiarcot895/myppa

$ sudo apt update
$ sudo apt install apt-fast
```

然后出现安装的配置界面，选择 “apt” →  “5（默认）” →  “No”，确认完成。（注：虽然没有确凿证据，但是如果你习惯使用 `apt-get` 命令而不是直接使用 `apt` 命令的话[事实上 apt 命令更先进]，建议在第一项配置中选择 “apt-get”）

然后我继续使用 `apt upgrade`，此时正在使用手机 4G 热点，速度飙升到了 6M/s。
