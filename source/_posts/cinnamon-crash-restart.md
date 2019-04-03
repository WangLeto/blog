---
title: cinnamon 崩溃后安全重启
date: 2019-04-04 02:45:52
tags: [Linux]
---

最近安装了 Linux mint 19 做毕设，时隔 1 年多再次用上 Linux（新版），意外地感觉好用。虽然用户界面的稳定性和优化还是不如 Windows 10，但是明显感觉出来有很多进步。不过这篇文字不是夸 cinnamon（mint 的桌面环境）的。

在 cinnamon 奔溃卡住以后，如果强行重启电脑，势必会丢失未保存的工作，也可能造成硬件损坏。搜索了一下，发现有个很不错的方法：

1. `ctrl` + `alt` + `1` 进入 tty 界面

2. 重启 cinnamon：

   ```shell
   $ pkill -HUP -f "cinnamon --replace"
   ```

3. `ctrl` + `alt` + `7` 回到 cinnamon 桌面环境

据解释 `cinnamon --replace` 命令的效果是用新的 cinnamon 进程代替原来被卡住的那个。