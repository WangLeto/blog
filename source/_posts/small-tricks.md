---
title: Small Tricks
date: 2019-02-06 14:52:09
tags: 其他
---

有时候收获了很多不常用的“骚操作”，因为很少用到，所以会忘；用到时又是一顿好找。那么干脆在这里记录一下，省得总忘。

目录：

- chrome 选择超链接文字
- chrome 删除 url 自动建议
- chrome 跨设备同步标签页
- wifi adb 调试
- Intel 核显右键集成菜单删除与找回
- 谁唤醒了我的电脑

<!--more-->

## chrome 选择超链接文字

按住 `ALT` 键。



## chrome 删除 url 自动建议

chrome 自动记录 url 并适时补全，但是有时候网站地址变了，不希望输入同一个关键字继续出现旧的 url，这个技巧就有用了。

- 输入关键字
- 方向键找到过时的 url
- `shift + delete` 删除之

因为相关的 url 可能会很多，所以要多来几次才能删除干净。



## chrome 跨设备同步标签页

科学上网、Google 框架是必须的，然后就可以在电脑上查看用手机打开了的标签页。

历史记录侧边栏：从其他设备打开的标签页。

实际上需要一点时间同步，没那么快。



## wifi adb 调试

USB 的充电功率低的不行，使用远程调试保持不掉电算你走运，想要充电呢基本不能。那就通过 wifi 直接局域网调试吧。当然先得插上 USB，除非 root 过：

```powershell
adb devices
adb tcpip 3456
adb connect host-ip:3456
```

拔掉 USB 就可以（充电）了。



## Intel 核显右键集成菜单删除与找回

多少年了也没有变化，该说英特尔是守旧呢还是兼容呢（喵喵喵？）

管理员权限运行：

```powershell
# 删除右键集成
regsvr32 /u igfxdtcm.dll

# 找回右键集成菜单
regsvr32 igfxdtcm.dll
```



## 谁唤醒了我的电脑

查看上一次唤醒原因：

```powershell
powercfg –lastwake
```

查看可以唤醒电脑的设备：

```powershell
powercfg /devicequery wake_armed
```

禁用某个列出的设备（管理员权限）：

```powershell
powercfg /devicedisablewake "设备名"
```

注意，有时候鼠标会注册两个设备，一个 mouse ，一个 keyboard，需要同时禁用两个…

或者：在系统日志里搜索“Power-Troubleshooter”。

如果唤醒源是unknown的话，有可能是计划任务。查找可以唤醒电脑的计划任务然后处理：

```powershell
Get-ScheduledTask | where {$_.settings.waketorun}
```

来源：[如何禁止鼠标唤醒Win10？ - Kenny的回答 - 知乎](https://www.zhihu.com/question/48154015/answer/162508741)。



*to be continue...*