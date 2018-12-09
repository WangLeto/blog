---
title: 使用批处理进行 adb 截图
date: 2018-01-22 18:32:57
tags: [Android, 脚本]
---

因为需要获取一些手机截图，并且直接在电脑上进行编辑，所以想到了使用了 adb 工具进行截图。但是手敲两行命令实在太长，因此找资料写了这个批处理命令。

参考：

[使用adb命令对手机进行截屏保存到电脑，SDCard](http://blog.csdn.net/yyongchao/article/details/51658009)

[Choose Highest Numbered File - Batch File](https://stackoverflow.com/questions/11645226/choose-highest-numbered-file-batch-file)

[DOS批处理BAT变量赋值及用户输入交互式赋值](http://www.ecdoer.com/post/dos-bat-set-variable.html)

<!--more-->

使用：双击后，在批处理文件所在的文件夹下创建 “screenshot-n.png” 屏幕截图，n从0开始计数。

```batch
%echo off
setlocal enabledelayedexpansion
set max=0
for %%x in (screenshot-*.png) do (
  set "FN=%%~nx"
  set "FN=!FN:screenshot-=!"
  if !FN! GTR !max! set max=!FN!
)
set /a max+=1
adb shell /system/bin/screencap -p /sdcard/screenshot.png
adb pull /sdcard/screenshot.png ./screenshot-%max%.png
```



最后晒一下手机桌面……嘻嘻（total launcher 启动器）

![](http://qiniu1.letow.top/batch-adb-screenshot-2018-1-22-19.12.jpg)
