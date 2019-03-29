---
title: Small Tricks
date: 2019-02-06 14:52:09
tags: 其他
---

有时候收获了很多不常用的“骚操作”，因为很少用到，所以会忘；用到时又是一顿好找。那么干脆在这里记录一下，省得总忘。

目录：

- chrome 选择超链接文字
- chrome 删除 url 建议
- chrome 跨设备同步标签页
- chrome 跨设备同步打开的标签页
- chrome 快速定位到地址栏
- Windows 上与 Linux 等效的命令
- 快速固定窗口到最前显示
- 微信等内嵌浏览器网页调试
- wifi adb 调试
- Intel 核显右键集成菜单删除与找回
- 谁唤醒了我的电脑
- 移除删除不掉的语言包

<!--more-->

## chrome 选择超链接文字

按住 `ALT` 键。



## chrome 删除 url 建议

chrome 自动记录 url 并适时补全，但是有时候网站地址变了，不希望输入同一个关键字继续出现旧的 url，这个技巧就有用了。

- 输入关键字
- 方向键找到过时的 url
- `shift + delete` 删除之

因为相关的 url 可能会很多，所以要多来几次才能删除干净。



## chrome 跨设备同步标签页

科学上网、Google 框架是必须的，然后就可以在电脑上查看用手机打开了的标签页。

历史记录侧边栏：从其他设备打开的标签页。

实际上需要一点时间同步，没那么快。



## chrome 跨设备同步打开的标签页

谷歌框架、账号登陆、自动同步是前提条件。所以这里只是介绍入口。

**PC 查看手机端打开的标签页**

历史记录左侧 - 从其他设备打开的标签页。

**手机查看 PC 端打开的标签页**

菜单 - 最近打开的标签页。



## chrome 快速定位到地址栏

某个版本之前我一直用的是 `F6`，按一下直接定位到地址栏。但是后来发现 `F6` 会先定位到标签页。

后来查了一下，发现 `Alt + D` 也有同样的功能。（`Ctrl + L` 感觉有点麻烦，一直不用）



## Windows 上与 Linux 等效的命令

| linux   | windows | 作用             |
| ------- | ------- | ---------------- |
| cat     | type    | 打印文件内容     |
| whereis | where   | 搜索二进制文件等 |



## 快速固定窗口到最前显示

原来 `nircmd` 还有这功能，这玩意真的啥都能干啊。那我还要 DeskPins 作甚？

管理员身份运行：

```powershell
nircmd win settopmost title "Sticky Notes" 1   #应该是用窗口名称查找进程
```



## 微信等内嵌浏览器网页调试

来源：[使用Chrome开发者工具调试Android端内网页(微信，QQ，UC，App内嵌页等)](https://juejin.im/post/5b558be7e51d4519984139e6#comment)。

以 X5 内核为例，关键是手机访问这个网址：[debugx5.qq.com](debugx5.qq.com)。



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



## 移除删除不掉的语言包

来源：[Fix: Cannot remove a Language from Windows 10](https://www.thewindowsclub.com/cannot-remove-language-windows-10)

使用 powershell，首先查看安装的语言包信息：

```powershell
Get-WinUserLanguageList
```

输出类似于：

```
LanguageTag     : zh-Hans-CN
Autonym         : 中文(中华人民共和国)
EnglishName     : Chinese
balabala....
```

记下目标语言包的 LanguageTag，然后运行：

```powershell
$LangList = Get-WinUserLanguageList
$MarkedLang = $LangList | where LanguageTag -eq "所要删除的语言包 tag"
$LangList.Remove($MarkedLang)
Set-WinUserLanguageList $LangList -Force
```

好了。



*to be continue...*