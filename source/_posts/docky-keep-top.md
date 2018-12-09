---
title: 设置 docky 置顶并不产生空白 
date: 2017-12-08 20:19:33
tags: [linux]
---

docky 是一款在 Ubuntu 上广受欢迎的启动器软件，模仿了 macOS 的启动器风格，感觉很漂亮。但是在默认的设置中，我们无法使得 docky 始终显示并保持在所有应用的最上方。

如何才能在使用全屏应用的情况下，让 docky 仍然可以显示，并且不产生难看的空白？

![](http://qiniu1.letow.top/docky-keep-top-2017-12-08-20-00-49.png)

参考教程：[How to keep Docky above all windows? ](https://askubuntu.com/questions/56260/how-to-keep-docky-above-all-windows)

<!--more-->

## 做法

### 设置 docky 

将 docky 的隐藏选项设置为 "窗口闪避" 和 淡出式隐藏：

![](http://qiniu1.letow.top/docky-keep-top-2017-12-08-20-05-14.png)

### 安装 gconf-editor 

如果没有安装过，需要首先安装它：

```shell
sudo apt install gconf-editor
```

### 完成配置

打开刚刚安装的 gconf-editor，中文名称是 “配置编辑器”，找到路径：`/apps/docky-2/Docky/Interface/DockPreferences/Dock1`，将 "FadeOpacity" 一项的数值改成 1。

![](http://qiniu1.letow.top/docky-keep-top-2017-12-08-20-11-45.png)

### 重启 docky

重启后，就实现了最上方图示的效果～


